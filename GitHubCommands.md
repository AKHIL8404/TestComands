# Steps to push code to GitHub from **Termux**:

* * *

### **1\. Install Git**

`pkg update && pkg upgrade`
`pkg install git` 

* * *

### **2\. Configure Git**

`git config --global user.name "Your Name" git config --global user.email "youremail@example.com"` 

* * *

### **3\. Create a Local Repository**

`mkdir myproject cd myproject`
`git init` 

* * *

### **4\. Add Your Project Files**

You can create or copy files here:

`echo "# My Project" > README.md` 

* * *

### **5\. Add & Commit Files**

`git add .`
`git commit -m "Initial commit"` 

* * *

### **6\. Add Remote Repository**

Go to GitHub, create a new repository (without README or .gitignore), and copy the HTTPS URL like:

arduino

`https://github.com/yourusername/your-repo.git` 

Then run:

`git remote add origin https://github.com/yourusername/your-repo.git` 

* * *

### **7\. Push to GitHub**

`git branch -M main`

`git push -u origin master`

_(or `main`, depending on your default branch)_

* * *

### **8\. GitHub Authentication**

GitHub now requires a **Personal Access Token (PAT)** instead of a password for HTTPS.

#### **To generate one:**

*   Go to GitHub > Settings > Developer Settings > Personal Access Tokens
    
*   Generate a token with repo permissions.
    
*   When Termux asks for your GitHub **username**, enter it.
    
*   When it asks for your **password**, paste the **token** instead.
    

* * *
