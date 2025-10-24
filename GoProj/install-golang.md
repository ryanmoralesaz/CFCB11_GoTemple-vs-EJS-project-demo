## use winget
`winget install GoLang.Go`

## each powershell session
`$env:PATH += ";C:\Program Files\Go\bin"`


## with gitbash
echo 'export PATH="/c/Program Files/Go/bin:$PATH"' >> ~/.bashrc
   source ~/.bashrc
   go version

## with powershell
# Check if profile exists
Test-Path $PROFILE

# Create profile if it doesn't exist
if (!(Test-Path $PROFILE)) { New-Item -Path $PROFILE -Type File -Force }

# Add Go to PATH in your profile
Add-Content $PROFILE '$env:PATH += ";C:\Program Files\Go\bin"'

# Reload your profile
. $PROFILE

# Test
go version