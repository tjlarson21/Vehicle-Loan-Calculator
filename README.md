# Vehicle-Loan-Calculator
using System;
using System.Collections.Generic;
using System.ComponentModel;
using System.Data;
using System.Drawing;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Windows.Forms;

/* Project 1 - Amortization App
 * TJ Larson
 */

namespace MajorProject1
{
    public partial class Form1 : Form
    {
        public Form1()
        {
            InitializeComponent();
        }

        //declare global variables totalLoans, totalInterest. Set equal to 0 to accumulate.
        double totalLoans = 0, totalInterest = 0;

        private void radioGroupBox_Enter(object sender, EventArgs e)
        {

        }

        private void exitButton_Click(object sender, EventArgs e)
        {
            //Open a dialog message box when exit is performed, if yes close form
            DialogResult dialog = MessageBox.Show("Are you sure you want to exit?", "Exit Confirmation",
                MessageBoxButtons.YesNo, MessageBoxIcon.Warning);
            if (dialog == DialogResult.Yes)
                this.Close();
        }

        private void amountTextBox_TextChanged(object sender, EventArgs e)
        {

        }

        private void amountTextBox_KeyPress(object sender, KeyPressEventArgs e)
        {
            //Only allow digit, control characters, negative symbol and backspace
            if (!char.IsControl(e.KeyChar)          //If key is NOT (!) a control char
                && !char.IsDigit(e.KeyChar)         //and also NOT a digit
                && e.KeyChar != '-'                 //and also NOT a dash
                && e.KeyChar != '.')                 //and also NOT a period

            {
                e.Handled = true;

                return;
            }
        }

        private void checkBox1_CheckedChanged(object sender, EventArgs e)
        {

        }

        private void rebateCheckBox_CheckedChanged(object sender, EventArgs e)
        {
            //disable text box until rebate check box is checked
            if (rebateCheckBox.Checked == false)
                rebateTextBox.Enabled = false;
            if (rebateCheckBox.Checked)
                rebateTextBox.Enabled = true;
        }
        
        private void rebateTextBox_TextChanged(object sender, EventArgs e)
        {
          
        }

        private void calcButton_Click(object sender, EventArgs e)
        {
            //declare variables used in calculation
            double loan, rate, rebate, pmt, interest,
                    principalPayment;
            int months = 0;
            

            //extract and store variables
            double.TryParse(amountTextBox.Text, out loan);
            double.TryParse(rebateTextBox.Text, out rebate);
            loan = loan - rebate;
            rate = Convert.ToDouble(aprComboBox.Text);

            //clear listbox
            loanListBox.Items.Clear();

            //Check for zero values in loan and APR input boxes
            if (loan == 0)
            {
                MessageBox.Show("Please add a non-zero number.", "Error Message",
                    MessageBoxButtons.OK, MessageBoxIcon.Error);
                amountTextBox.Focus();
                amountTextBox.SelectAll();
            }

            if (rate == 0)
            {
                MessageBox.Show("Please add a non-zero number.", "Error Message",
                    MessageBoxButtons.OK, MessageBoxIcon.Error);
                aprComboBox.Focus();
                aprComboBox.SelectAll();
            }
            
            //Check each radio button for selection, store as variable "months"
            if (sixRadioButton.Checked)
                months = 6;
            else if (twelveRadioButton.Checked)
                months = 12;
            else if (eighteenRadioButton.Checked)
                months = 18;
            else if (twentyfourRadioButton.Checked)
                months = 24;

            //convert rate to decimal
            rate = rate / 100;

            //calculate principal payment
            principalPayment = rate / 12 * (loan + 0 * Math.Pow(1 + rate / 12, months)) /
                ((Math.Pow(1 + rate / 12, months) - 1) * (1 + rate / 12 * 0));
            
            //calculate interest
            interest = loan * rate / 12;

            //Format columns in list box
            string formatString = ("{0,5}{1,13}{2,14}{3,14}{4,14}");

            //use for loop to calculate and display amortization schedule
            int monthsCounter = 0;
            double totalPmt = 0;

            for (monthsCounter = 1; monthsCounter <= months; monthsCounter = monthsCounter + 1)
            {
                pmt = principalPayment + interest;
                loan = loan - principalPayment;
                loanListBox.Items.Add(string.Format(formatString, monthsCounter, interest.ToString("C2"), 
                    principalPayment.ToString("C2"), pmt.ToString("C2"), loan.ToString("C2")));
                totalInterest += interest;
                totalLoans += principalPayment;
                interest = loan * rate / 12;
                principalPayment = pmt - interest;
                totalPmt += pmt;       //works correctly     
                totalInterestLabel.Text = totalInterest.ToString("C2");
                totalLoansLabel.Text = totalLoans.ToString("C2");
                
            }

            //add a blank line to list box
            loanListBox.Items.Add("");

            //add the totals line to the list box
            loanListBox.Items.Add(string.Format("{0,5}{1,13}{2,14}{3,14}", "Total", 
                totalInterest.ToString("C2"), totalLoans.ToString("C2"), totalPmt.ToString("C2")));

        }

        private void Form1_Load(object sender, EventArgs e)
        {
            //load common APR rates in the combo box upon form load
            for (double rate = 1.0; rate <= 5.0; rate += 0.25)
                aprComboBox.Items.Add(rate.ToString("N2"));

        }

        private void acceptButton_Click(object sender, EventArgs e)
        {

            //clear listbox
            loanListBox.Items.Clear();

            //reset all textboxes and comboboxes
            amountTextBox.Text = null;
            aprComboBox.Text = null;

            //disable rebate checkbox and clear text box
            rebateCheckBox.Checked = false;
            rebateTextBox.Text = null;

            //set month 6 radio button to checked
            sixRadioButton.Checked = true;     

            //set focus on loan text box
            amountTextBox.Focus();

        }

        private void amountTextBox_Click(object sender, EventArgs e)
        {
            //automatically select all text in the text box when selected
            amountTextBox.SelectAll();
        }

        private void aprComboBox_Click(object sender, EventArgs e)
        {
            //automatically select any text in the combo box when selected
            aprComboBox.SelectAll();
        }

        private void rebateTextBox_Click(object sender, EventArgs e)
        {
            rebateTextBox.SelectAll();
        }

        private void displayButton_Click(object sender, EventArgs e)
        {
            //display totalLoans and totalInterest in their respective labels
            totalLoansLabel.Text = totalLoans.ToString("C2");
            totalInterestLabel.Text = totalInterest.ToString("C2");
        }
    }
}
