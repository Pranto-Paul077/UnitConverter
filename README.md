# UnitConverter
It is a javaFx Code . We interchange unit to unit using JavaFx Gui.
package com.example.unit;

import javafx.application.Application;
import javafx.collections.FXCollections;
import javafx.collections.ObservableList;
import javafx.geometry.Insets;
import javafx.scene.Node;
import javafx.scene.Scene;
import javafx.scene.control.*;
import javafx.scene.layout.GridPane;
import javafx.stage.Stage;

public class UnitConverter extends Application {

    private static final ObservableList<String> UNIT_TYPES = FXCollections.observableArrayList(
            "Temperature", "Currency", "Distance"
    );

    private static final double[] CURRENCY_RATES = {0.85, 100, 110};

    private final ObservableList<String> currencyUnits = FXCollections.observableArrayList("USD", "EUR", "BDT");

    private ComboBox<String> unitComboBox;

    @Override
    public void start(Stage primaryStage) {

        TabPane tabPane = new TabPane();

        Tab unitConverterTab = createUnitConverterTab();
        tabPane.getTabs().add(unitConverterTab);

        Tab bmiCalculatorTab = createBMICalculatorTab();
        tabPane.getTabs().add(bmiCalculatorTab);

        Scene scene = new Scene(tabPane, 500, 400);
        primaryStage.setTitle("Unit Converter & BMI Calculator");
        primaryStage.setScene(scene);
        primaryStage.show();
    }

    private Tab createUnitConverterTab() {
        Tab tab = new Tab("Unit Converter");
        tab.setClosable(false);

        GridPane grid = new GridPane();
        grid.setHgap(10);
        grid.setVgap(10);
        grid.setPadding(new Insets(10));

        Label unitLabel = new Label("Unit:");
        unitComboBox = new ComboBox<>(UNIT_TYPES);
        grid.add(unitLabel, 0, 0);
        grid.add(unitComboBox, 1, 0, 2, 1);

        Label valueLabel = new Label("Value:");
        TextField valueField = new TextField();
        grid.add(valueLabel, 0, 1);
        grid.add(valueField, 1, 1);

        Label fromUnitLabel = new Label("From:");
        ComboBox<String> fromUnitComboBox = new ComboBox<>();
        grid.add(fromUnitLabel, 0, 2);
        grid.add(fromUnitComboBox, 1, 2);

        Label toUnitLabel = new Label("To:");
        ComboBox<String> toUnitComboBox = new ComboBox<>();
        grid.add(toUnitLabel, 0, 3);
        grid.add(toUnitComboBox, 1, 3);

        Button convertButton = new Button("Convert");
        grid.add(convertButton, 2, 3);

        convertButton.setOnAction(e -> {
            try {
                double value = Double.parseDouble(valueField.getText());
                String fromUnit = fromUnitComboBox.getValue();
                String toUnit = toUnitComboBox.getValue();

                double convertedValue = convert(value, fromUnit, toUnit);
                String result = String.format("%.2f %s = %.2f %s", value, fromUnit, convertedValue, toUnit);
                showAlert(result);
            } catch (NumberFormatException ex) {
                showAlert("Invalid input. Please enter a valid number.");
            } catch (Exception ex) {
                showAlert("Conversion error: " + ex.getMessage());
            }
        });

        unitComboBox.getSelectionModel().selectedItemProperty().addListener((observable, oldValue, newValue) -> {
            updateUnitOptions(fromUnitComboBox, toUnitComboBox, newValue);
        });

        ObservableList<String> temperatureUnits = FXCollections.observableArrayList("Celsius","Fahrenheit", "Kelvin");
        ObservableList<String> distanceUnits = FXCollections.observableArrayList("Meters", "Feet", "Inches", "Yards", "Miles");

        fromUnitComboBox.setItems(temperatureUnits);
        toUnitComboBox.setItems(temperatureUnits);

        tab.setContent(grid);
        return tab;
    }

    private double convert(double value, String fromUnit, String toUnit) {
        if ("Temperature".equals(unitComboBox.getValue())) {
            if ("Celsius".equals(fromUnit) && "Fahrenheit".equals(toUnit)) {
                return (value * 9 / 5) + 32;
            } else if ("Fahrenheit".equals(fromUnit) && "Celsius".equals(toUnit)) {
                return (value - 32) * 5 / 9;
            } else if ("Celsius".equals(fromUnit) && "Kelvin".equals(toUnit)) {
                return value + 273.15;
            } else if ("Kelvin".equals(fromUnit) && "Celsius".equals(toUnit)) {
                return value - 273.15;
            }
        } else if ("Currency".equals(unitComboBox.getValue())) {
            int fromIndex = currencyUnits.indexOf(fromUnit);
            int toIndex = currencyUnits.indexOf(toUnit);
            return value * CURRENCY_RATES[toIndex] / CURRENCY_RATES[fromIndex];
        } else if ("Distance".equals(unitComboBox.getValue())) {
            if ("Meters".equals(fromUnit) && "Feet".equals(toUnit)) {
                return value * 3.28084;
            } else if ("Feet".equals(fromUnit) && "Meters".equals(toUnit)) {
                return value * 0.3048;
            }
        }
        return value;
    }

    private void updateUnitOptions(ComboBox<String> fromUnitComboBox, ComboBox<String> toUnitComboBox, String newValue) {
        if ("Temperature".equals(newValue)) {
            fromUnitComboBox.setItems(FXCollections.observableArrayList("Celsius", "Fahrenheit", "Kelvin"));
            toUnitComboBox.setItems(FXCollections.observableArrayList("Celsius", "Fahrenheit", "Kelvin"));
        } else if ("Currency".equals(newValue)) {
            fromUnitComboBox.setItems(currencyUnits);
            toUnitComboBox.setItems(currencyUnits);
        } else if ("Distance".equals(newValue)) {
            fromUnitComboBox.setItems(FXCollections.observableArrayList("Meters", "Feet", "Inches", "Yards", "Miles"));
            toUnitComboBox.setItems(FXCollections.observableArrayList("Meters", "Feet", "Inches", "Yards", "Miles"));
        }
    }

    private Tab createBMICalculatorTab() {
        Tab tab = new Tab("BMI Calculator");
        tab.setClosable(false);

        GridPane grid = new GridPane();
        grid.setHgap(10);
        grid.setVgap(10);
        grid.setPadding(new Insets(10));

        Label weightLabel = new Label("Weight (kg):");
        TextField weightField = new TextField();
        grid.add(weightLabel, 0, 0);
        grid.add(weightField, 1, 0);

        Label heightFeetLabel = new Label("Height (feet):");
        TextField heightFeetField = new TextField();
        grid.add(heightFeetLabel, 0, 1);
        grid.add(heightFeetField, 1, 1);

        Label heightInchesLabel = new Label("Height (inches):");
        TextField heightInchesField = new TextField();
        grid.add(heightInchesLabel, 0, 2);
        grid.add(heightInchesField, 1, 2);

        Button calculateButton = new Button("Calculate BMI");
        grid.add(calculateButton, 1, 3);

        Label bmiLabel = new Label("BMI:");
        grid.add(bmiLabel, 0, 4);

        Label resultLabel = new Label();
        grid.add(resultLabel, 1, 4);

        calculateButton.setOnAction(e -> {
            try {
                double weight = Double.parseDouble(weightField.getText());
                double heightFeet = Double.parseDouble(heightFeetField.getText());
                double heightInches = Double.parseDouble(heightInchesField.getText());

                double heightMeters = (heightFeet * 12 + heightInches) * 0.0254;

                double bmi = calculateBMI(weight, heightMeters);
                resultLabel.setText(String.format("%.2f", bmi));
            } catch (NumberFormatException ex) {
                resultLabel.setText("Invalid input");
            }
        });

        tab.setContent(grid);
        return tab;
    }

    private double calculateBMI(double weight, double height) {
        return weight / (height * height);
    }

    private void showAlert(String message) {
        Alert alert = new Alert(Alert.AlertType.INFORMATION);
        alert.setContentText(message);
        alert.showAndWait();
    }

    public static void main(String[] args) {
        launch(args);
    }
}
