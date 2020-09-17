# Powershell script to join a Computer to a Windows Domain, also can be run from WinPE

Add-Computer has an error when joining to a specific OU when a computer account with the same name already exists. To avoid this issue this script checks for an existing account.

```markdown
$domain = "domain.local" 
$OUPath = "OU=testOU,DC=domain,DC=Domain,DC=com"
$ldapPath = "LDAP://domain.local/DC=domain,dc=local"
$passwordClear = "P@ssword"
$password = $passwordClear | ConvertTo-Securestring -asplaintext -force 
$username = "domain\ldapUser" 
$credential = New-Object System.Management.Automation.PSCredential($username,$password) 

$ComputerName = $env:ComputerName
$domaininfo = New-Object DirectoryServices.DirectoryEntry($ldapPath,$username,$passwordClear)
$searcher = New-Object System.DirectoryServices.DirectorySearcher($domaininfo)
$searcher.filter = "(cn=$ComputerName)"
$searchparm = $searcher.FindOne()

if (!($searchparm))
{
    Add-computer -ComputerName $ComputerName -DomainName $domain -OUPath $OUPath -credential $credential -UnjoinDomainCredential $credential -restart 
}
else
{
    Add-Computer -ComputerName $ComputerName -DomainName $domain -credential $credential -UnjoinDomainCredential $credential -restart
}
```
