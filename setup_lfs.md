# Python ML Project Setup Guide

## Prerequisites
1. Install Git (https://git-scm.com/downloads)
2. Install Python 3.11 (https://www.python.org/downloads/)
3. Install AWS CLI (https://awscli.amazonaws.com/AWSCLIV2.msi)
   - After installation, restart your computer to ensure AWS CLI is in PATH
   - Verify with: `aws --version`
4. Install Git LFS (https://git-lfs.com)
   - After installation, verify with: `git lfs --version`

## Setup Steps

### 1. Create Project Directory
```powershell
mkdir D:\UCSD_Proj_25_5_5_Machine_Learning
cd D:\UCSD_Proj_25_5_5_Machine_Learning
```

### 2. Version Control Setup - MAKE GITIGNORE FIRST!!!
* Create .gitignore
@"
.venv/
__pycache__/
*.pyc
.env
/data/
/models/
*.ipynb_checkpoints/
poetry.lock
"@ | Out-File -FilePath .gitignore

* Initialize git
git init

* Initialize Git LFS
git lfs install

* Configure Git LFS tracking
@"
data/**/*.csv filter=lfs diff=lfs merge=lfs -text
data/**/*.pkl filter=lfs diff=lfs merge=lfs -text
data/**/*.npy filter=lfs diff=lfs merge=lfs -text
data/**/*.parquet filter=lfs diff=lfs merge=lfs -text
"@ | Out-File -FilePath .gitattributes

* inital git commit
git add .gitignore
git add .gitattributes
git commit -m "Initial Commit"

### 3. AWS Configuration
*  Configure AWS credentials
aws configure
Enter your:
AWS Access Key ID
AWS Secret Access Key
Default region (e.g., us-east-2)
Default output format (json)

* Verify 
aws sts get-caller-identity

* Troubleshooting AWS Configuration
If you encounter "Unable to parse config file" errors, follow these recovery steps:

  * Remove existing AWS configuration:

  powershellCopyRemove-Item -Recurse -Force "$env:USERPROFILE\.aws"
  mkdir "$env:USERPROFILE\.aws"

  * Create credentials file with exact formatting (no spaces around =):

powershellCopy@"
[default]
aws_access_key_id=YOUR_ACCESS_KEY
aws_secret_access_key=YOUR_SECRET_KEY
"@ | Set-Content "$env:USERPROFILE\.aws\credentials"

    * Create config file with exact formatting:

powershellCopy@"
[default]
region=us-east-2
output=json
"@ | Set-Content "$env:USERPROFILE\.aws\config"

    * Verify the fix:

aws sts get-caller-identity
Common Issues:

Spaces around = signs in config files will cause parsing errors
Mixed forward/backward slashes in paths can cause issues
Extra newlines or special characters in config files
Region mismatch with AWS account region

### 4. Activate venv and Install Poetry
& "C:\Users\alden\AppData\Local\Programs\Python\Python311\python.exe" -m venv .venv
.venv\Scripts\Activate.ps1

pip install poetry
$env:POETRY_VIRTUALENVS_IN_PROJECT = "true"

### 5. Project Configuration
* Initialize poetry project
poetry init --name "UCSD_CodeAlong_25_5_5_Machine_Learning" --python ">=3.11,<3.12" --no-interaction

# Add core dependencies first
poetry add "platformdirs>=4.0.0,<5.0.0"
poetry add boto3 sagemaker

# Add ML dependencies
poetry add "numpy>=1.26.0,<2.1.0"
poetry add pandas scipy scikit-learn
poetry add matplotlib seaborn



# Add TensorFlow (if needed)
poetry add tensorflow-io-gcs-filesystem==0.31.0
poetry add tensorflow

# Add Jupyter support
poetry add jupyter ipykernel

### 6. Project Structure
```powershell
# Create project directories
mkdir notebooks
mkdir data
mkdir models
```

### 7. Git LFS S3 Configuration
```powershell
# Configure LFS to use S3
@"
[lfs]
    url = s3://your-bucket-name/path
"@ | Out-File -FilePath .lfsconfig -Encoding utf8
```

### 8. Initial Commit
```powershell
git add .gitignore .gitattributes .lfsconfig
git add pyproject.toml
git commit -m "Initial project setup"
```

### 9. GitHub Repository Setup
1. Create new repository on GitHub
2. Connect local repository:
```powershell
git remote add origin https://github.com/YOUR-USERNAME/REPO-NAME.git
git branch -M main
git push -u origin main
```

## Usage Notes
- Always activate virtual environment before working:
  ```powershell
  .\.venv\Scripts\Activate.ps1
  ```
- Add new data files to Git LFS:
  ```powershell
  git lfs track "data/**/*.new-extension"
  git add .gitattributes
  ```
- When adding data:
  ```powershell
  git add data/your-file
  git commit -m "Add dataset"
  git push origin main
  ```