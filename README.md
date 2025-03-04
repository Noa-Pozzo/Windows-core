# TP3 : PowerShell - Installation et Configuration d'Active Directory

---

## Étape 1 : Installer les rôles Active Directory

### Installation du rôle AD DS
```powershell
Install-WindowsFeature -Name AD-Domain-Services -IncludeManagementTools
```

### Vérification de l'installation
```powershell
Get-WindowsFeature | Where-Object { $_.Name -eq "AD-Domain-Services" }
```

---

## Étape 2 : Configurer le contrôleur de domaine

### Promotion du serveur en contrôleur de domaine
```powershell
Install-ADDSForest -DomainName "Avousdechoisir.com" -SafeModeAdministratorPassword (ConvertTo-SecureString "MonSuperMotDePasse123!" -AsPlainText -Force) -Force
```
*Le serveur redémarrera automatiquement après cette commande.*

---

## Étape 3 : Vérification du domaine

### Vérification que le domaine est bien créé
```powershell
Get-ADDomain
```

### Vérification que le serveur est un contrôleur de domaine
```powershell
Get-ADDomainController
```

---

## Étape 4 : Créer des unités d'organisation (OU)

### Création des OUs
```powershell
New-ADOrganizationalUnit -Name "Utilisateurs" -ProtectedFromAccidentalDeletion $false
New-ADOrganizationalUnit -Name "Groupes" -ProtectedFromAccidentalDeletion $false
New-ADOrganizationalUnit -Name "Ordinateurs" -ProtectedFromAccidentalDeletion $false
```

### Vérification des OUs créées
```powershell
Get-ADOrganizationalUnit -Filter *
```

---

## Étape 5 : Créer des utilisateurs

### Création d'un utilisateur simple
```powershell
New-ADUser -Name "Jean Dupont" -GivenName "Jean" -Surname "Dupont" -UserPrincipalName "j.dupont@Avousdechoisir.com" -SamAccountName "jdupont" -Path "OU=Utilisateurs,DC=Avousdechoisir,DC=com" -AccountPassword (ConvertTo-SecureString "P@ssw0rd" -AsPlainText -Force) -Enabled $true
```

### Création de plusieurs utilisateurs avec un tableau
```powershell
$users = @(
    @{ Name="Alice Martin"; UserPrincipalName="alice.m@Avousdechoisir.com"; SamAccountName="alice.m" },
    @{ Name="Bob Durand"; UserPrincipalName="bob.d@Avousdechoisir.com"; SamAccountName="bob.d" }
)

foreach ($user in $users) {
    New-ADUser -Name $user.Name -UserPrincipalName $user.UserPrincipalName -SamAccountName $user.SamAccountName -Path "OU=Utilisateurs,DC=Avousdechoisir,DC=com" -AccountPassword (ConvertTo-SecureString "P@ssw0rd" -AsPlainText -Force) -Enabled $true
}
```

### Vérification des utilisateurs créés
```powershell
Get-ADUser -Filter *
```

---

## Étape 6 : Créer des groupes

### Création d'un groupe
```powershell
New-ADGroup -Name "IT-Support" -GroupScope Global -Path "OU=Groupes,DC=Avousdechoisir,DC=com"
```

### Ajout d'utilisateurs au groupe
```powershell
Add-ADGroupMember -Identity "IT-Support" -Members "jdupont", "alice.m"
```

### Vérification des membres du groupe
```powershell
Get-ADGroupMember -Identity "IT-Support"
```

---

## Étape 7 : Ajouter des ordinateurs

### Création d'un compte ordinateur
```powershell
New-ADComputer -Name "PC01" -Path "OU=Ordinateurs,DC=Avousdechoisir,DC=com"
```

### Vérification des ordinateurs créés
```powershell
Get-ADComputer -Filter *
```

---

## Étape 8 : Exporter les données pour vérification

### Exportation de la liste des utilisateurs
```powershell
Get-ADUser -Filter * | Select-Object Name,SamAccountName | Export-Csv -Path "C:\Users.csv" -NoTypeInformation
```

### Exportation de la liste des groupes
```powershell
Get-ADGroup -Filter * | Select-Object Name | Export-Csv -Path "C:\Groups.csv" -NoTypeInformation
```

---

**Fin du TP3 - Bonne configuration ! 🚀**
