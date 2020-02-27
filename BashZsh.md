# Bash & Zsh tricks and tips

## prettify directory structure
```
ls -R | grep ":$" | sed -e 's/:$//' -e 's/[^-][^\/]*\//──/g' -e 's/─/├/' -e '$s/├/└/'
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

