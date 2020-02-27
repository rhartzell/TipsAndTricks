# Bash & Zsh tricks and tips

## prettify directory structure
```
$-> ls -R | grep ":$" | sed -e 's/:$//' -e 's/[^-][^\/]*\//──/g' -e 's/─/├/' -e '$s/├/└/'
├─zsh_demo
├───calculations
├─────africa
├───────kenya
├───────malawi
├─────asia
├───────laos
├───────nepal
├─────europe
├───────malta
├───────poland
├───data
├─────africa
├───────kenya
├───────malawi
├─────asia
├───────laos
├───────nepal
├─────europe
├───────malta
└───────poland
```
# list every file directly below the zsh_demo folder
```
ls zsh_demo
```
# list every file in the folders directly below the zsh_demo folder
```
ls zsh_demo/*
```
# list every file in every folder two levels below the zsh_demo folder
```
ls zsh_demo/*/*
```
# list every file anywhere below the zsh_demo folder
```
ls zsh_demo/**/*
```

# list every file that ends in .txt in every folder at any level below the zsh_demo folder
ls zsh_demo/**/*.txt
