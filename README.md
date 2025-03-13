# 📌 Automated Grading System for C Assignments

## 📝 Overview
This repository contains scripts for automating the grading process of C assignments submitted via the Canvas portal. The automation workflow includes extracting submissions, compiling and executing C programs, capturing outputs, and generating a grading report.

## ⚡ Step-by-Step Process to Automate Grading for the Assignment

### **1️⃣ Download and Extract Submissions**
- 📥 Download the ZIP file containing all student submissions from Canvas.
- 📂 Extract the ZIP file into a **submissions** directory.

```bash
unzip submissions.zip -d submissions
```

### **2️⃣ Organize Submissions**
- 📌 If students submitted files with names like `studentID_assignment4.c`, organize them into individual folders.

```bash
#!/bin/bash
cd submissions
for file in *.c; do
    student_name=$(basename "$file" .c)
    mkdir -p "$student_name"
    mv "$file" "$student_name/"
done
```

### **3️⃣ Compile Each Submission**
- 🔄 For each student, compile their **.c** file.
- 🛠️ Capture compilation errors in a log.

```bash
#!/bin/bash
for dir in submissions/*; do
    if [ -d "$dir" ]; then
        gcc "$dir"/*.c -o "$dir"/output 2> "$dir"/compile_errors.log
    fi
done
```

### **4️⃣ Run the Executable and Capture Output**
- ▶️ If the program compiles successfully, run it and save its output.

```bash
#!/bin/bash
for dir in submissions/*; do
    if [ -f "$dir/output" ]; then
        "$dir"/output > "$dir"/program_output.txt
    fi
done
```

### **5️⃣ Compare Output with Expected Results**
- 📊 Create an **expected_output.txt** file with the correct output.
- 🔍 Use `diff` to check differences.

```bash
for dir in submissions/*; do
    if [ -f "$dir/program_output.txt" ]; then
        diff "$dir/program_output.txt" expected_output.txt > "$dir/diff.log"
    fi
done
```

### **6️⃣ Check Code Quality**
- 🧐 Use **cppcheck** for static analysis.

```bash
for dir in submissions/*; do
    cppcheck --enable=all --quiet "$dir"/*.c 2> "$dir/style_issues.log"
done
```

### **7️⃣ Generate Final Grading Report**
- 📑 Create a CSV file summarizing compilation status, correctness, and code quality.

```python
import os
import csv

report_file = "grading_report.csv"

with open(report_file, "w", newline="") as csvfile:
    writer = csv.writer(csvfile)
    writer.writerow(["Student", "Compilation", "Output Match", "Style Issues"])

    for student in os.listdir("submissions"):
        student_path = os.path.join("submissions", student)
        if os.path.isdir(student_path):
            comp_status = "Passed" if os.path.exists(os.path.join(student_path, "output")) else "Failed"
            output_status = "Matched" if os.path.getsize(os.path.join(student_path, "diff.log")) == 0 else "Mismatch"
            style_status = "Issues Found" if os.path.getsize(os.path.join(student_path, "style_issues.log")) > 0 else "Clean"

            writer.writerow([student, comp_status, output_status, style_status])

print("✅ Grading complete. Check 'grading_report.csv'.")
```

### **8️⃣ Review and Finalize Grades**
- 📌 Open `grading_report.csv` to review each student’s performance.
- 📊 Assign marks based on:
  - **🛠️ Compilation (20%)**
  - **✅ Correctness (60%)**
  - **📎 Coding style (20%)**

---


