# Shamir's Secret Sharing Implementation

This project implements Shamir's Secret Sharing algorithm to reconstruct a secret from a set of shares using Lagrange interpolation. The code is written in C++ and can parse a JSON-like input structure to decode the values in various bases.


## Overview
Shamir's Secret Sharing is a cryptographic method that allows a secret to be divided into multiple parts (shares), which can be distributed among participants. A specified number of shares (threshold) is required to reconstruct the original secret.

## Features
- Parses JSON-like input for key-value pairs.
- Decodes values from different numerical bases (e.g., binary, hexadecimal).
- Computes the secret using Lagrange interpolation.
- Simple command-line interface for displaying results.
## Steps to Implement in Dev C++

Follow these steps to set up and run the Shamir's Secret Sharing implementation in Dev C++:

1. **Download and Install Dev C++:**
   - If you haven't already, download Dev C++ from [Bloodshed Dev C++](https://bloodshed-dev-cpp.en.softonic.com/) or a similar site.
   - Follow the installation instructions to install the software on your computer.

2. **Create a New Project:**
   - Open Dev C++.
   - Click on **File** in the menu bar.
   - Select **New** > **Project**.
   - Choose **Console Application** and click **OK**.
   - Name your project (e.g., `ShamirSecretSharing`) and select a location to save it.

3. **Set Up the Main Source File:**
   - In the project window, right-click on the **Sources** folder.
   - Select **New File**.
   - Copy the provided C++ code into this new file.
   - Save the file with a `.cpp` extension (e.g., `secret_sharing.cpp`).

4. **Modify the Code (if necessary):**
   - Ensure the `parseJsonFile` function has your desired test cases included, or modify it to read from a JSON file as needed.

5. **Compile the Code:**
   - Click on the **Execute** menu in the menu bar.
   - Select **Compile & Run** (or press `F9`).
   - The compiler will build your project, and if there are no errors, the console window will open displaying the output.

6. **View the Output:**
   - The console will show the reconstructed secret based on the input provided in the `parseJsonFile` function.
   - Make sure to check for any compile-time errors and correct them before running the program.

7. **Testing with Different Inputs:**
   - To test with different JSON-like input values, modify the `parseJsonFile` function.
   - Recompile and run the program to see the output for the new test cases.

8. **Debugging:**
   - If you encounter any errors or unexpected outputs, review the code for logical errors or issues with the input values.
   - Use `cout` statements to print variable values at different stages for debugging.

9. **Clean Up:**
   - After finishing your testing and development, you can clean up your project by deleting any unnecessary files or directories from the project folder.

By following these steps, you should be able to successfully implement and run the Shamir's Secret Sharing algorithm in Dev C++.


## Code
Here is the complete code for the Shamir's Secret Sharing implementation:

```cpp
#include <iostream>
#include <string>
#include <unordered_map>
#include <vector>
#include <sstream>
#include <algorithm>
#include <cmath>

int baseToDecimal(const std::string& value, int base) {
    int decimalValue = 0;
    for (size_t i = 0; i < value.length(); i++) {
        char digit = value[value.length() - 1 - i];
        if (digit >= '0' && digit <= '9') {
            decimalValue += (digit - '0') * pow(base, i);
        } else {
            decimalValue += (digit - 'a' + 10) * pow(base, i);
        }
    }
    return decimalValue;
}

std::unordered_map<std::string, std::unordered_map<std::string, std::string>> parseJsonFile() {
    // Modify this function to read from your JSON file or hardcode as needed
    return {
        {"keys", {{"n", "4"}, {"k", "3"}}},
        {"1", {{"base", "10"}, {"value", "4"}}},
        {"2", {{"base", "2"}, {"value", "111"}}},
        {"3", {{"base", "10"}, {"value", "12"}}},
        {"6", {{"base", "4"}, {"value", "213"}}}
    };
}

int lagrangeInterpolation(const std::vector<int>& x, const std::vector<int>& y, int k) {
    int secret = 0;
    for (size_t i = 0; i < k; i++) {
        int term = y[i];
        for (size_t j = 0; j < k; j++) {
            if (j != i) {
                term *= (0 - x[j]) * pow((x[i] - x[j]), -1); // using modular inverse
            }
        }
        secret += term;
    }
    return secret;
}

int main() {
    auto roots = parseJsonFile();
    
    int n = std::stoi(roots["keys"]["n"]);
    int k = std::stoi(roots["keys"]["k"]);
    
    std::vector<int> x(k), y(k);
    for (int i = 1; i <= k; ++i) {
        int base = std::stoi(roots[std::to_string(i)]["base"]);
        std::string value = roots[std::to_string(i)]["value"];
        x[i - 1] = i; // using 1-based index for shares
        y[i - 1] = baseToDecimal(value, base);
    }

    int secret = lagrangeInterpolation(x, y, k);
    std::cout << "Secret: " << secret << std::endl;

    return 0;
}



