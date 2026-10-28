
import javax.swing.*;
import javax.swing.border.EmptyBorder;
import javax.swing.plaf.basic.BasicComboBoxUI;
import java.awt.*;
import java.text.NumberFormat;
import java.util.Locale;

public class Main extends JFrame {

    private final Color BG = new Color(12, 18, 32);
    private final Color PANEL = new Color(22, 31, 49);
    private final Color INPUT = new Color(30, 42, 63);
    private final Color TEXT = new Color(237, 243, 255);
    private final Color MUTED = new Color(151, 166, 190);
    private final Color BLUE = new Color(92, 133, 255);
    private final Color GREEN = new Color(63, 210, 158);
    private final Color BORDER = new Color(46, 61, 85);

    private final NumberFormat money =
            NumberFormat.getCurrencyInstance(new Locale("en", "IN"));

    private JTextField amountField, rateField, monthsField;
    private JComboBox<String> typeBox, frequencyBox;

    private JLabel maturityValue, investedValue, interestValue, returnValue;
    private JLabel statusLabel;

    public Main() {
        setTitle("DepositIQ - FD & RD Interest Compounding Engine");
        setSize(1050, 720);
        setMinimumSize(new Dimension(850, 650));
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        setLocationRelativeTo(null);
        getContentPane().setBackground(BG);

        JPanel root = new JPanel(new BorderLayout(20, 20));
        root.setBackground(BG);
        root.setBorder(new EmptyBorder(25, 28, 25, 28));

        root.add(createHeader(), BorderLayout.NORTH);
        root.add(createBody(), BorderLayout.CENTER);

        add(root);
        updateResults(0, 0, 0, 0);
    }

    private JPanel createHeader() {
        JPanel header = new JPanel();
        header.setOpaque(false);
        header.setLayout(new BoxLayout(header, BoxLayout.Y_AXIS));

        JLabel brand = new JLabel("◈  DepositIQ");
        brand.setFont(new Font("SansSerif", Font.BOLD, 17));
        brand.setForeground(BLUE);

        JLabel title = new JLabel(
                "FD & RD Interest Compounding Engine");
        title.setFont(new Font("SansSerif", Font.BOLD, 27));
        title.setForeground(TEXT);

        JLabel subtitle = new JLabel(
                "Enter your deposit details and calculate your estimated returns.");
        subtitle.setFont(new Font("SansSerif", Font.PLAIN, 13));
        subtitle.setForeground(MUTED);

        header.add(brand);
        header.add(Box.createVerticalStrut(12));
        header.add(title);
        header.add(Box.createVerticalStrut(7));
        header.add(subtitle);

        return header;
    }

    private JPanel createBody() {
        JPanel body = new JPanel(new BorderLayout(18, 18));
        body.setOpaque(false);

        body.add(createInputPanel(), BorderLayout.WEST);
        body.add(createResultsPanel(), BorderLayout.CENTER);

        return body;
    }

    private JPanel createInputPanel() {
        JPanel panel = new JPanel();
        panel.setBackground(PANEL);
        panel.setPreferredSize(new Dimension(350, 450));
        panel.setBorder(BorderFactory.createCompoundBorder(
                BorderFactory.createLineBorder(BORDER),
                new EmptyBorder(20, 20, 20, 20)));

        panel.setLayout(new BoxLayout(panel, BoxLayout.Y_AXIS));

        panel.add(label("Enter Your Details", 20, TEXT));
        panel.add(Box.createVerticalStrut(20));

        panel.add(fieldLabel("Deposit Type"));

        typeBox = new JComboBox<>(new String[]{
                "Fixed Deposit (FD)",
                "Recurring Deposit (RD)"
        });
        styleCombo(typeBox);
        panel.add(typeBox);
        panel.add(Box.createVerticalStrut(15));

        amountField = addInput(
                panel, "Deposit Amount (₹)", "100000");
        rateField = addInput(
                panel, "Annual Interest Rate (%)", "7");
        monthsField = addInput(
                panel, "Tenure (Months)", "12");

        panel.add(fieldLabel("Compounding Frequency"));

        frequencyBox = new JComboBox<>(new String[]{
                "Quarterly", "Monthly",
                "Half-yearly", "Yearly"
        });
        styleCombo(frequencyBox);
        panel.add(frequencyBox);

        panel.add(Box.createVerticalStrut(22));

        JButton calculate = new JButton("Calculate Returns");
        calculate.setFont(new Font("SansSerif", Font.BOLD, 14));
        calculate.setForeground(Color.WHITE);
        calculate.setBackground(BLUE);
        calculate.setFocusPainted(false);
        calculate.setBorderPainted(false);
        calculate.setAlignmentX(Component.LEFT_ALIGNMENT);
        calculate.setMaximumSize(
                new Dimension(Integer.MAX_VALUE, 44));
        calculate.setCursor(
                new Cursor(Cursor.HAND_CURSOR));
        calculate.addActionListener(e -> calculate());

        panel.add(calculate);
        panel.add(Box.createVerticalStrut(10));

        JButton reset = new JButton("Clear / Reset");
        reset.setFont(new Font("SansSerif", Font.BOLD, 13));
        reset.setForeground(TEXT);
        reset.setBackground(INPUT);
        reset.setFocusPainted(false);
        reset.setBorder(BorderFactory.createLineBorder(BORDER));
        reset.setAlignmentX(Component.LEFT_ALIGNMENT);
        reset.setMaximumSize(
                new Dimension(Integer.MAX_VALUE, 40));
        reset.setCursor(new Cursor(Cursor.HAND_CURSOR));
        reset.addActionListener(e -> resetFields());

        panel.add(reset);
        panel.add(Box.createVerticalStrut(12));

        statusLabel = label("Ready for your input.", 11, MUTED);
        statusLabel.setAlignmentX(Component.LEFT_ALIGNMENT);
        panel.add(statusLabel);

        return panel;
    }

    private JPanel createResultsPanel() {
        JPanel panel = new JPanel();
        panel.setOpaque(false);
        panel.setLayout(new BoxLayout(panel, BoxLayout.Y_AXIS));

        panel.add(label("Your Investment Summary", 21, TEXT));
        panel.add(Box.createVerticalStrut(6));

        panel.add(label(
                "Results update when you click Calculate Returns.",
                12, MUTED));
        panel.add(Box.createVerticalStrut(20));

        maturityValue = new JLabel("₹0.00");
        investedValue = new JLabel("₹0.00");
        interestValue = new JLabel("₹0.00");
        returnValue = new JLabel("0.00%");

        panel.add(resultCard(
                "ESTIMATED MATURITY VALUE", maturityValue, BLUE));
        panel.add(Box.createVerticalStrut(14));

        panel.add(resultCard(
                "TOTAL AMOUNT INVESTED", investedValue, TEXT));
        panel.add(Box.createVerticalStrut(14));

        panel.add(resultCard(
                "TOTAL INTEREST EARNED", interestValue, GREEN));
        panel.add(Box.createVerticalStrut(14));

        panel.add(resultCard(
                "RETURN ON TOTAL INVESTMENT", returnValue, GREEN));
        panel.add(Box.createVerticalStrut(20));

        JPanel info = new JPanel();
        info.setLayout(new BoxLayout(info, BoxLayout.Y_AXIS));
        info.setBackground(PANEL);
        info.setBorder(new EmptyBorder(16, 16, 16, 16));
        info.setAlignmentX(Component.LEFT_ALIGNMENT);
        info.setMaximumSize(new Dimension(Integer.MAX_VALUE, 120));

        info.add(label("HOW IT WORKS", 12, BLUE));
        info.add(Box.createVerticalStrut(9));
        info.add(label(
                "FD: Invest a lump sum and earn compound interest.",
                12, TEXT));
        info.add(Box.createVerticalStrut(7));
        info.add(label(
                "RD: Invest a fixed amount every month.",
                12, TEXT));
        info.add(Box.createVerticalStrut(7));
        info.add(label(
                "Results are estimates, not guaranteed bank quotes.",
                11, MUTED));

        panel.add(info);

        return panel;
    }

    private JPanel resultCard(
            String heading, JLabel value, Color color) {

        JPanel card = new JPanel();
        card.setBackground(PANEL);
        card.setLayout(new BoxLayout(card, BoxLayout.Y_AXIS));
        card.setBorder(BorderFactory.createCompoundBorder(
                BorderFactory.createLineBorder(BORDER),
                new EmptyBorder(15, 17, 15, 17)));
        card.setAlignmentX(Component.LEFT_ALIGNMENT);
        card.setMaximumSize(new Dimension(Integer.MAX_VALUE, 95));

        JLabel caption = label(heading, 11, MUTED);
        value.setFont(new Font("SansSerif", Font.BOLD, 23));
        value.setForeground(color);

        card.add(caption);
        card.add(Box.createVerticalStrut(10));
        card.add(value);

        return card;
    }

    private JTextField addInput(
            JPanel panel, String caption, String defaultValue) {

        panel.add(fieldLabel(caption));

        JTextField field = new JTextField(defaultValue);
        field.setFont(new Font("SansSerif", Font.PLAIN, 14));
        field.setForeground(TEXT);
        field.setCaretColor(TEXT);
        field.setBackground(INPUT);
        field.setSelectionColor(BLUE);
        field.setSelectedTextColor(Color.WHITE);
        field.setBorder(BorderFactory.createCompoundBorder(
                BorderFactory.createLineBorder(BORDER),
                new EmptyBorder(9, 10, 9, 10)));
        field.setMaximumSize(new Dimension(Integer.MAX_VALUE, 40));
        field.setAlignmentX(Component.LEFT_ALIGNMENT);

        panel.add(field);
        panel.add(Box.createVerticalStrut(14));

        return field;
    }

    private JLabel fieldLabel(String text) {
        JLabel label = label(text, 12, MUTED);
        label.setAlignmentX(Component.LEFT_ALIGNMENT);
        label.setBorder(new EmptyBorder(0, 0, 7, 0));
        return label;
    }

    private JLabel label(String text, int size, Color color) {
        JLabel label = new JLabel(text);
        label.setFont(new Font("SansSerif", Font.PLAIN, size));
        label.setForeground(color);
        return label;
    }

    // FIXED: Dark background for both dropdowns and their popup lists.
    private void styleCombo(JComboBox<String> combo) {
        combo.setFont(new Font("SansSerif", Font.PLAIN, 13));
        combo.setForeground(TEXT);
        combo.setBackground(INPUT);
        combo.setOpaque(false);
        combo.setFocusable(true);
        combo.setBorder(BorderFactory.createLineBorder(BORDER));

        // Use a UI delegate that respects our custom colors.
        combo.setUI(new BasicComboBoxUI() {
            @Override
            protected JButton createArrowButton() {
                JButton arrow = new JButton("▾");
                arrow.setFont(new Font("SansSerif", Font.PLAIN, 12));
                arrow.setForeground(TEXT);
                arrow.setBackground(INPUT);
                arrow.setOpaque(false);
                arrow.setBorderPainted(false);
                arrow.setFocusPainted(false);
                arrow.setContentAreaFilled(true);
                return arrow;
            }
        });

        combo.setRenderer(new DefaultListCellRenderer() {
            @Override
            public Component getListCellRendererComponent(
                    JList<?> list,
                    Object value,
                    int index,
                    boolean isSelected,
                    boolean cellHasFocus) {

                JLabel item = (JLabel)
                        super.getListCellRendererComponent(
                                list, value, index,
                                isSelected, cellHasFocus);

                item.setOpaque(true);
                item.setFont(new Font(
                        "SansSerif", Font.PLAIN, 13));
                item.setBackground(isSelected ? BLUE : INPUT);
                item.setForeground(TEXT);
                item.setBorder(
                        new EmptyBorder(6, 8, 6, 8));

                return item;
            }
        });

        combo.setMaximumSize(
                new Dimension(Integer.MAX_VALUE, 40));
        combo.setAlignmentX(Component.LEFT_ALIGNMENT);

        // Match the popup menu to the dark theme.
        JPopupMenu popup = new JPopupMenu();
        popup.setBackground(INPUT);
        popup.setBorder(BorderFactory.createLineBorder(BORDER));
    }

    private int getFrequency() {
        switch ((String) frequencyBox.getSelectedItem()) {
            case "Monthly":
                return 12;
            case "Quarterly":
                return 4;
            case "Half-yearly":
                return 2;
            default:
                return 1;
        }
    }

    private double getNumber(
            JTextField field, String name) {

        try {
            double value = Double.parseDouble(
                    field.getText().trim());

            if (!Double.isFinite(value) || value <= 0) {
                throw new IllegalArgumentException(
                        name + " must be greater than zero.");
            }

            return value;

        } catch (NumberFormatException ex) {
            throw new IllegalArgumentException(
                    "Please enter a valid number for " + name + ".");
        }
    }

    private int getTenure() {
        double value = getNumber(monthsField, "tenure");

        if (value != Math.floor(value) || value > 600) {
            throw new IllegalArgumentException(
                    "Tenure must be a whole number from 1 to 600 months.");
        }

        return (int) value;
    }

    private void calculate() {
        try {
            double amount = getNumber(
                    amountField, "deposit amount");

            // Allow zero interest, but reject invalid values.
            double rate;
            try {
                rate = Double.parseDouble(
                        rateField.getText().trim());
            } catch (NumberFormatException ex) {
                throw new IllegalArgumentException(
                        "Please enter a valid interest rate.");
            }

            if (!Double.isFinite(rate) || rate < 0) {
                throw new IllegalArgumentException(
                        "Interest rate cannot be negative.");
            }

            if (rate > 100) {
                throw new IllegalArgumentException(
                        "Interest rate cannot exceed 100%.");
            }

            int months = getTenure();

            if (amount > 1e12) {
                throw new IllegalArgumentException(
                        "Deposit amount is too large.");
            }

            int frequency = getFrequency();
            double invested;
            double maturity;

            if (typeBox.getSelectedIndex() == 0) {
                // Fixed Deposit: one-time investment.
                invested = amount;

                maturity = amount * Math.pow(
                        1 + rate / (100.0 * frequency),
                        frequency * months / 12.0);

            } else {
                // Recurring Deposit: monthly installments.
                if (amount > 1e10) {
                    throw new IllegalArgumentException(
                            "Monthly installment is too large.");
                }

                invested = amount * months;

                double monthlyRate = Math.pow(
                        1 + rate / (100.0 * frequency),
                        frequency / 12.0) - 1;

                if (Math.abs(monthlyRate) < 1e-12) {
                    maturity = invested;
                } else {
                    maturity = amount
                            * (Math.pow(
                                    1 + monthlyRate, months) - 1)
                            / monthlyRate;
                }
            }

            if (!Double.isFinite(maturity)) {
                throw new IllegalArgumentException(
                        "Calculated amount is too large.");
            }

            double interest = maturity - invested;
            double returnPercent = invested == 0
                    ? 0 : interest / invested * 100;

            updateResults(
                    invested, interest, maturity, returnPercent);

            statusLabel.setForeground(GREEN);
            statusLabel.setText(
                    "Calculation completed successfully.");

        } catch (IllegalArgumentException ex) {
            statusLabel.setForeground(
                    new Color(255, 115, 115));
            statusLabel.setText(
                    "Please correct your entries.");

            JOptionPane.showMessageDialog(
                    this,
                    ex.getMessage(),
                    "Invalid Input",
                    JOptionPane.WARNING_MESSAGE);
        }
    }

    private void updateResults(
            double invested,
            double interest,
            double maturity,
            double returnPercent) {

        investedValue.setText(money.format(invested));
        interestValue.setText(money.format(interest));
        maturityValue.setText(money.format(maturity));

        returnValue.setText(String.format(
                Locale.US, "%.2f%%", returnPercent));
    }

    private void resetFields() {
        amountField.setText("100000");
        rateField.setText("7");
        monthsField.setText("12");

        typeBox.setSelectedIndex(0);
        frequencyBox.setSelectedItem("Quarterly");

        updateResults(0, 0, 0, 0);

        statusLabel.setForeground(MUTED);
        statusLabel.setText("Ready for your input.");
    }

    public static void main(String[] args) {
        SwingUtilities.invokeLater(() -> {
            try {
                UIManager.setLookAndFeel(
                        UIManager.getSystemLookAndFeelClassName());
            } catch (Exception ignored) {
                // Keep the default look and feel.
            }

            new Main().setVisible(true);
        });
    }
}
