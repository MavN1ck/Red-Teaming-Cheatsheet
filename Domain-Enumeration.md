# Domain Enumeration

## Domain 
### Powerview 
- #### Get Current Domain
```
Get-NetDomain
```
- #### Get object of another domain
```
Get-NetDomain -Domain <D O M A I N _ N A M E>
```
- #### Get Domain SID for the current Domain
```
Get-DomainSID
```
- ### Get Domain SID for another Domain
```
Get-DomainSID -Domain <D O M A I N _ N A M E>
```
- ### Get Domain Policy for the current Domain
```
Get-DomainPolicy
```
- ### Get Domain Policy for the current Domain and for specific Policy 
```
(Get-DomainPolicy)."<P O L I C Y _ N A M E>"
``` 
- ### Get Domain Policy for the another Domain
```
Get-DomainPolicy -Domain <D O M A I N _ N A M E>
```
- ### Get Domain Policy for the another Domain and for specific Policy 
```
(Get-DomainPolicy -Domain <D O M A I N _ N A M E>)."<P O L I C Y _ N A M E>"
```
- ### Get Domain Controller Information for current Domain
```
Get-NetDomainController
```
- ### Get Domain Controller Information for another Domain
```
Get-NetDomainController -Domain <D O M A I N _ N A M E>
```

## Users 
- ### List of all usernames on AD on current Domain
```
Get-NetUser | select samaccountname
```
- ### List all user details on AD on current domain
```
Get-NetUser
```
- ### List details of a particular user on AD on current domain
```
Get-NetUser <U S E R N A M E (should be samaccountname)>
```