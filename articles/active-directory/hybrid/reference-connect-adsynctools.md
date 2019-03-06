---
title: 'Azure AD Connect: Referência do PowerShell ADSyncTools | Documentos da Microsoft'
description: Este documento fornece informações de referência para o módulo do ADSyncTools.psm1 PowerShell.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.date: 10/19/2018
ms.subservice: hybrid
ms.author: billmath
ms.topic: reference
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9a1b8abf15233c06e8ff9e507b315cc8a3703970
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57443339"
---
# <a name="azure-ad-connect--adsynctools-powershell-reference"></a>Azure AD Connect:  Referência do PowerShell ADSyncTools
A seguinte documentação fornece informações de referência para o módulo do PowerShell ADSyncTools.psm1 que está incluído com o Azure AD Connect.

## <a name="clear-adsynctoolsconsistencyguid"></a>Clear-ADSyncToolsConsistencyGuid

### <a name="synopsis"></a>SINOPSE
Limpar o mS-Ds-ConsistencyGuid de utilizador do AD

### <a name="syntax"></a>SINTAXE

```
Clear-ADSyncToolsConsistencyGuid [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>DESCRIÇÃO
Limpar o valor no mS-Ds-ConsistencyGuid para o utilizador de destino AD

### <a name="examples"></a>EXEMPLOS

#### <a name="example-1"></a>EXEMPLO 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>EXEMPLO 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARÂMETROS

#### <a name="-user"></a>-User
Utilizador de destino no AD para definir

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Este cmdlet suporta os parâmetros comuns: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.
Para obter mais informações, veja about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="confirm-adsynctoolsadmoduleloaded"></a>Confirm-ADSyncToolsADModuleLoaded

### <a name="synopsis"></a>SINOPSE
{{Preencher o sinopse}}

### <a name="syntax"></a>SINTAXE

```
Confirm-ADSyncToolsADModuleLoaded
```

### <a name="description"></a>DESCRIÇÃO
{{Preencha a descrição}}

### <a name="examples"></a>EXEMPLOS

#### <a name="example-1"></a>Exemplo 1
```powershell
PS C:\> {{ Add example code here }}
```

{{Adicionar descrição de exemplo aqui}}

## <a name="connect-adsyncdatabase"></a>Connect-AdSyncDatabase

### <a name="synopsis"></a>SINOPSE
{{Preencher o sinopse}}

### <a name="syntax"></a>SINTAXE

```
Connect-AdSyncDatabase [-Server] <String> [[-Instance] <String>] [[-Database] <String>] [[-UserName] <String>]
 [[-Password] <String>] [<CommonParameters>]
```

### <a name="description"></a>DESCRIÇÃO
{{Preencha a descrição}}

### <a name="examples"></a>EXEMPLOS

#### <a name="example-1"></a>Exemplo 1
```powershell
PS C:\> {{ Add example code here }}
```

{{Adicionar descrição de exemplo aqui}}

### <a name="parameters"></a>PARÂMETROS

#### <a name="-database"></a>-Database
{{Preencher a descrição da base de dados}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-instance"></a>-Instance
{{Preencher a descrição de instância}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-password"></a>-Password
{{Preencher a descrição de palavra-passe}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 4
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-server"></a>-Server
{{Preencher a descrição do servidor}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-username"></a>-UserName
{{Preencher a descrição de nome de utilizador}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Este cmdlet suporta os parâmetros comuns: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.
Para obter mais informações, veja about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="export-adsynctoolsconsistencyguidmigration"></a>Export-ADSyncToolsConsistencyGuidMigration

### <a name="synopsis"></a>SINOPSE
Exportar relatório ConsistencyGuid

### <a name="syntax"></a>SINTAXE

```
Export-ADSyncToolsConsistencyGuidMigration [-AlternativeLoginId] [-UserPrincipalName] <String>
 [-ImmutableIdGUID] <String> [-Output] <String> [<CommonParameters>]
```

### <a name="description"></a>DESCRIÇÃO
Gera um relatório de ConsistencyGuid com base num ficheiro de importação de CSV da importação ADSyncToolsImmutableIdMigration

### <a name="examples"></a>EXEMPLOS

#### <a name="example-1"></a>EXEMPLO 1
```
Import-Csv .\AllSyncUsers.csv | Export-ADSyncToolsConsistencyGuidMigration -Output ".\AllSyncUsers-Report"
```

#### <a name="example-2"></a>EXEMPLO 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARÂMETROS

#### <a name="-alternativeloginid"></a>-AlternativeLoginId
Utilizar o ID de início de sessão alternativa (correio)

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-userprincipalname"></a>-UserPrincipalName
UserPrincipalName

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-immutableidguid"></a>-ImmutableIdGUID
ImmutableIdGUID

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-output"></a>-Output
Nome de ficheiro de saída para ficheiros de registo e de CSV

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Este cmdlet suporta os parâmetros comuns: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.
Para obter mais informações, veja about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="get-adsyncsqlbrowserinstances"></a>Get-ADSyncSQLBrowserInstances

### <a name="synopsis"></a>SINOPSE
{{Preencher o sinopse}}

### <a name="syntax"></a>SINTAXE

```
Get-ADSyncSQLBrowserInstances [[-hostName] <String>]
```

### <a name="description"></a>DESCRIÇÃO
{{Preencha a descrição}}

### <a name="examples"></a>EXEMPLOS

#### <a name="example-1"></a>Exemplo 1
```powershell
PS C:\> {{ Add example code here }}
```

{{Adicionar descrição de exemplo aqui}}

### <a name="parameters"></a>PARÂMETROS

#### <a name="-hostname"></a>-hostName
{{Nome do anfitrião descrição preencher}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

## <a name="get-adsynctoolsaduser"></a>Get-ADSyncToolsADuser

### <a name="synopsis"></a>SINOPSE
Obter o utilizador do AD

### <a name="syntax"></a>SINTAXE

```
Get-ADSyncToolsADuser [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>DESCRIÇÃO
Devolve um objeto do AD para fazer: Suporte a várias florestas

### <a name="examples"></a>EXEMPLOS

#### <a name="example-1"></a>EXEMPLO 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>EXEMPLO 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARÂMETROS

#### <a name="-user"></a>-User
Utilizador de destino no AD para definir ConsistencyGuid

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Este cmdlet suporta os parâmetros comuns: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.
Para obter mais informações, veja about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="get-adsynctoolsconsistencyguid"></a>Get-ADSyncToolsConsistencyGuid

### <a name="synopsis"></a>SINOPSE
Obter o mS-Ds-ConsistencyGuid do utilizador do AD

### <a name="syntax"></a>SINTAXE

```
Get-ADSyncToolsConsistencyGuid [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>DESCRIÇÃO
Devolve o valor no atributo mS-Ds-ConsistencyGuid o AD do utilizador de destino no formato GUID

### <a name="examples"></a>EXEMPLOS

#### <a name="example-1"></a>EXEMPLO 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>EXEMPLO 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARÂMETROS

#### <a name="-user"></a>-User
Utilizador de destino no AD para definir

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Este cmdlet suporta os parâmetros comuns: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.
Para obter mais informações, veja about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="get-adsynctoolsobjectguid"></a>Get-ADSyncToolsObjectGuid

### <a name="synopsis"></a>SINOPSE
Obter o ObjectGuid do utilizador do AD

### <a name="syntax"></a>SINTAXE

```
Get-ADSyncToolsObjectGuid [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>DESCRIÇÃO
Devolve o valor no atributo de ObjectGUID do AD do utilizador de destino no formato GUID

### <a name="examples"></a>EXEMPLOS

#### <a name="example-1"></a>EXEMPLO 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>EXEMPLO 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARÂMETROS

#### <a name="-user"></a>-User
Utilizador de destino no AD para definir

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Este cmdlet suporta os parâmetros comuns: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.
Para obter mais informações, veja about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="get-adsynctoolsrunhistory"></a>Get-ADSyncToolsRunHistory

### <a name="synopsis"></a>SINOPSE
Get AAD Connect histórico de execuções

### <a name="syntax"></a>SINTAXE

```
Get-ADSyncToolsRunHistory [[-Days] <Int32>] [<CommonParameters>]
```

### <a name="description"></a>DESCRIÇÃO
Função que retorna o histórico de execuções de ligar para AAD no formato XML

### <a name="examples"></a>EXEMPLOS

#### <a name="example-1"></a>EXEMPLO 1
```
Get-ADSyncToolsRunHistory
```

#### <a name="example-2"></a>EXEMPLO 2
```
Get-ADSyncToolsRunHistory -Days 1
```

### <a name="parameters"></a>PARÂMETROS

#### <a name="-days"></a>-Dias
{{Preencher dias descrição}}

```yaml
Type: Int32
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: 3
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Este cmdlet suporta os parâmetros comuns: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.
Para obter mais informações, veja about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="get-adsynctoolssourceanchorchanged"></a>Get-ADSyncToolsSourceAnchorChanged

### <a name="synopsis"></a>SINOPSE
Obter os utilizadores com erros de SourceAnchor alterado

### <a name="syntax"></a>SINTAXE

```
Get-ADSyncToolsSourceAnchorChanged [-sourcePath] <Object> [-outputPath] <Object> [<CommonParameters>]
```

### <a name="description"></a>DESCRIÇÃO
Consultas de AAD Connect histórico de execuções de função e exporta todos os utilizadores de relatórios de erro: "O atributo SourceAnchor foi alterado."

### <a name="examples"></a>EXEMPLOS

#### <a name="example-1"></a>EXEMPLO 1
```
#Required Parameters
```

$sourcePath = Read-Host -Prompt "Enter your log file path with file name" #"\<Source_Path\>" $outputPath = Read-Host -Prompt "Enter your out file path with file name" #"\<Out_Path\>"
 
 Get-ADSyncToolsUsersSourceAnchorChanged -sourcePath $sourcePath -outputPath $outputPath

#### <a name="example-2"></a>EXEMPLO 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARÂMETROS

#### <a name="-sourcepath"></a>-sourcePath
{{Preencher sourcePath descrição}}

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-outputpath"></a>-outputPath
{{Preencher outputPath descrição}}

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Este cmdlet suporta os parâmetros comuns: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.
Para obter mais informações, veja about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="import-adsynctoolsimmutableidmigration"></a>Import-ADSyncToolsImmutableIdMigration

### <a name="synopsis"></a>SINOPSE
Importar o ImmutableID do AAD

### <a name="syntax"></a>SINTAXE

```
Import-ADSyncToolsImmutableIdMigration [-Output] <String> [-IncludeSyncUsersFromRecycleBin]
 [<CommonParameters>]
```

### <a name="description"></a>DESCRIÇÃO
Gera um ficheiro com todos os utilizadores do Azure AD sincronizado que contém o valor de ImmutableID no formato GUID requisitos: Módulo PowerShell do MSOnline

### <a name="examples"></a>EXEMPLOS

#### <a name="example-1"></a>EXEMPLO 1
```
Import-ADSyncToolsImmutableIdMigration -OutputFile '.\AllSyncUsers.csv'
```

#### <a name="example-2"></a>EXEMPLO 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARÂMETROS

#### <a name="-output"></a>-Output
Ficheiro CSV de saída

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-includesyncusersfromrecyclebin"></a>-IncludeSyncUsersFromRecycleBin
A sincronizar os utilizadores do Azure AD Reciclagem

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Este cmdlet suporta os parâmetros comuns: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.
Para obter mais informações, veja about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).


## <a name="invoke-adsyncdatabasequery"></a>Invoke-AdSyncDatabaseQuery

### <a name="synopsis"></a>SINOPSE
{{Preencher o sinopse}}

### <a name="syntax"></a>SINTAXE

```
Invoke-AdSyncDatabaseQuery [-SqlConnection] <SqlConnection> [[-Query] <String>] [<CommonParameters>]
```

### <a name="description"></a>DESCRIÇÃO
{{Preencha a descrição}}

### <a name="examples"></a>EXEMPLOS

#### <a name="example-1"></a>Exemplo 1
```powershell
PS C:\> {{ Add example code here }}
```

{{Adicionar descrição de exemplo aqui}}

### <a name="parameters"></a>PARÂMETROS

#### <a name="-query"></a>-Query
{{Preencher a descrição do consultar}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-sqlconnection"></a>-SqlConnection
{{Preencher a descrição do SqlConnection}}

```yaml
Type: SqlConnection
Parameter Sets: (All)
Aliases:

Required: True
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Este cmdlet suporta os parâmetros comuns: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.
Para obter mais informações, veja about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="remove-adsynctoolsexpiredcertificates"></a>Remove-ADSyncToolsExpiredCertificates

### <a name="synopsis"></a>SINOPSE
Script para remover o atributo UserCertificate certificados expirados

### <a name="syntax"></a>SINTAXE

```
Remove-ADSyncToolsExpiredCertificates [-TargetOU] <String> [[-BackupOnly] <Boolean>] [-ObjectClass] <String>
 [<CommonParameters>]
```

### <a name="description"></a>DESCRIÇÃO
Este script usa todos os objetos de um unidade organizacional de destino no seu domínio do Active Directory - filtrado por classe de objeto (utilizador/computador) e elimina todos os certificados expirados presentes no atributo UserCertificate.
Por predefinição (BackupOnly modo), que ele será apenas de cópia de segurança de certificados para um ficheiro expiraram e não efetuar quaisquer alterações no AD.
Se usar - BackupOnly $false, em seguida, qualquer certificado expirado presente no atributo UserCertificate para esses objetos será removido do AD após ser copiado para o ficheiro.
Cada certificado será efetuado num nome de ficheiro separado: ObjectClass_ObjectGUID_CertThumprint.cer o script também irá criar um ficheiro de registo no formato CSV que mostra todos os utilizadores com certificados que quer estão válido ou expirados, incluindo a ação real tomada (ignorados/exportados/Deleted).

### <a name="examples"></a>EXEMPLOS

#### <a name="example-1"></a>EXEMPLO 1
```
Check all users in target OU - Expired Certificates will be copied to separated files and no certificates will be removed
```

Remove-ADSyncToolsExpiredCertificates - TargetOU "UO = Users, UO = Corp, DC = Contoso, DC = com" - ObjectClass utilizador

#### <a name="example-2"></a>EXEMPLO 2
```
Delete Expired Certs from all Computer objects in target OU - Expired Certificates will be copied to files and removed from AD
```

Remove-ADSyncToolsExpiredCertificates - TargetOU "UO = computadores, UO = Corp, DC = Contoso, DC = com" - ObjectClass computador - BackupOnly $false

### <a name="parameters"></a>PARÂMETROS

#### <a name="-targetou"></a>-TargetOU
UO de destino para a pesquisa para objetos do AD

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-backuponly"></a>-BackupOnly
BackupOnly não irá eliminar todos os certificados do AD

```yaml
Type: Boolean
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
Default value: True
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-objectclass"></a>-ObjectClass
Filtro de classe de objeto

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Este cmdlet suporta os parâmetros comuns: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.
Para obter mais informações, veja about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="repair-adsynctoolsautoupgradestate"></a>Repair-ADSyncToolsAutoUpgradeState

### <a name="synopsis"></a>SINOPSE
Breve descrição

### <a name="syntax"></a>SINTAXE

```
Repair-ADSyncToolsAutoUpgradeState
```

### <a name="description"></a>DESCRIÇÃO
Descrição longa

### <a name="examples"></a>EXEMPLOS

#### <a name="example-1"></a>EXEMPLO 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>EXEMPLO 2
```
Another example of how to use this cmdlet
```

## <a name="resolve-adsynchostaddress"></a>Resolve-ADSyncHostAddress

### <a name="synopsis"></a>SINOPSE
{{Preencher o sinopse}}

### <a name="syntax"></a>SINTAXE

```
Resolve-ADSyncHostAddress [[-hostName] <String>]
```

### <a name="description"></a>DESCRIÇÃO
{{Preencha a descrição}}

### <a name="examples"></a>EXEMPLOS

#### <a name="example-1"></a>Exemplo 1
```powershell
PS C:\> {{ Add example code here }}
```

{{Adicionar descrição de exemplo aqui}}

### <a name="parameters"></a>PARÂMETROS

#### <a name="-hostname"></a>-hostName
{{Nome do anfitrião descrição preencher}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

## <a name="restore-adsynctoolsexpiredcertificates"></a>Restore-ADSyncToolsExpiredCertificates

### <a name="synopsis"></a>SINOPSE
(PARA O FAZER) Restaura o atributo AD UserCertificate a partir de um ficheiro de certificado

### <a name="syntax"></a>SINTAXE

```
Restore-ADSyncToolsExpiredCertificates
```

### <a name="description"></a>DESCRIÇÃO
Descrição longa

### <a name="examples"></a>EXEMPLOS

#### <a name="example-1"></a>EXEMPLO 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>EXEMPLO 2
```
Another example of how to use this cmdlet
```

## <a name="set-adsynctoolsconsistencyguid"></a>Set-ADSyncToolsConsistencyGuid

### <a name="synopsis"></a>SINOPSE
Definir o mS-Ds-ConsistencyGuid no utilizador do AD

### <a name="syntax"></a>SINTAXE

```
Set-ADSyncToolsConsistencyGuid [-User] <Object> [-Value] <Object> [<CommonParameters>]
```

### <a name="description"></a>DESCRIÇÃO
Definir um valor no atributo mS-Ds-ConsistencyGuid para o utilizador do AD de destino

### <a name="examples"></a>EXEMPLOS

#### <a name="example-1"></a>EXEMPLO 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>EXEMPLO 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARÂMETROS

#### <a name="-user"></a>-User
Utilizador de destino no AD para definir ConsistencyGuid

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-value"></a>-Value
ImmutableId (matriz de bytes, GUID, GUID cadeia ou cadeia Base64)

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Este cmdlet suporta os parâmetros comuns: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.
Para obter mais informações, veja about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="test-adsyncnetworkport"></a>Test-ADSyncNetworkPort

### <a name="synopsis"></a>SINOPSE
{{Preencher o sinopse}}

### <a name="syntax"></a>SINTAXE

```
Test-ADSyncNetworkPort [[-hostName] <String>] [[-port] <String>]
```

### <a name="description"></a>DESCRIÇÃO
{{Preencha a descrição}}

### <a name="examples"></a>EXEMPLOS

#### <a name="example-1"></a>Exemplo 1
```powershell
PS C:\> {{ Add example code here }}
```

{{Adicionar descrição de exemplo aqui}}

### <a name="parameters"></a>PARÂMETROS

#### <a name="-hostname"></a>-hostName
{{Nome do anfitrião descrição preencher}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-port"></a>-porta
{{Preencher porta descrição}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

## <a name="trace-adsynctoolsadimport"></a>Trace-ADSyncToolsADImport

### <a name="synopsis"></a>SINOPSE
Cria um ficheiro de rastreio do e o passo de importação do AD

### <a name="syntax"></a>SINTAXE

```
Trace-ADSyncToolsADImport [[-ADConnectorXML] <String>] [[-dc] <String>] [[-rootDN] <String>]
 [[-filter] <String>] [-SkipCredentials] [[-ADwatermark] <String>] [<CommonParameters>]
```

### <a name="description"></a>DESCRIÇÃO
Rastreios de todas as consultas de ldap de uma importação do AAD Connect AD executam a partir de um determinado AD marca d'água ponto de verificação (cookie de partição). Cria um ficheiro de rastreio ".\ADimportTrace_yyyyMMddHHmmss.log" na pasta atual.

### <a name="examples"></a>EXEMPLOS

#### <a name="example-1"></a>EXEMPLO 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>EXEMPLO 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARÂMETROS

#### <a name="-adconnectorxml"></a>-ADConnectorXML
{{Preencher a descrição de ADConnectorXML}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-dc"></a>-dc
Ficheiro XML de exportação de conector AD

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
Default value: DC1.domain.contoso.com
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-rootdn"></a>-rootDN
Controlador de domínio de destino

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 3
Default value: DC=Domain,DC=Contoso,DC=com
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-filter"></a>-filter
DN de raiz de floresta

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 4
Default value: (&(objectClass=*))
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipcredentials"></a>-SkipCredentials
Tipos de objetos de AD para rastreio \> * = todos os tipos de objeto

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adwatermark"></a>-ADwatermark
Se já em execução como administrador de domínio não é necessário fornecer as credenciais do AD.
Por exemplo, $ADwatermark de ficheiros de entrada manual de marca d'água, em vez de XML = "TVNEUwMAAAAXyK9ir1zSAQAAAAAAAAAA(...)"

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 5
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Este cmdlet suporta os parâmetros comuns: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.
Para obter mais informações, veja about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="trace-adsynctoolsldapquery"></a>Trace-ADSyncToolsLdapQuery

### <a name="synopsis"></a>SINOPSE
Breve descrição

### <a name="syntax"></a>SINTAXE

```
Trace-ADSyncToolsLdapQuery [-Context] <String> [-Server] <String> [-Port] <Int32> [-Filter] <String>
 [<CommonParameters>]
```

### <a name="description"></a>DESCRIÇÃO
Descrição longa

### <a name="examples"></a>EXEMPLOS

#### <a name="example-1"></a>EXEMPLO 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>EXEMPLO 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARÂMETROS

#### <a name="-context"></a>-Context
Descrição de ajuda de param1

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-server"></a>-Server
Descrição de ajuda de Param2

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: Localhost
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-port"></a>-Port
Descrição de ajuda de Param2

```yaml
Type: Int32
Parameter Sets: (All)
Aliases:

Required: True
Position: 3
Default value: 389
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-filter"></a>-Filtro
Descrição de ajuda de Param2

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 4
Default value: (objectClass=*)
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Este cmdlet suporta os parâmetros comuns: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.
Para obter mais informações, veja about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="update-adsynctoolsconsistencyguidmigration"></a>Update-ADSyncToolsConsistencyGuidMigration

### <a name="synopsis"></a>SINOPSE
Atualiza os utilizadores com o novo ConsistencyGuid (ImmutableId)

### <a name="syntax"></a>SINTAXE

```
Update-ADSyncToolsConsistencyGuidMigration [[-DistinguishedName] <String>] [-ImmutableIdGUID] <String>
 [-Action] <String> [-Output] <String> [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>DESCRIÇÃO
Atualiza os utilizadores com o novo valor de ConsistencyGuid (ImmutableId.) retirado da ConsistencyGuid relatório este suporta a função a opção de WhatIf observação: Relatório de ConsistencyGuid têm de ser importado com separador Demiliter

### <a name="examples"></a>EXEMPLOS

#### <a name="example-1"></a>EXEMPLO 1
```
Import-Csv .\AllSyncUsersTEST-Report.csv -Delimiter "`t"| Update-ADSyncToolsConsistencyGuidMigration -Output .\AllSyncUsersTEST-Result2 -WhatIf
```

#### <a name="example-2"></a>EXEMPLO 2
```
Import-Csv .\AllSyncUsersTEST-Report.csv -Delimiter "`t"| Update-ADSyncToolsConsistencyGuidMigration -Output .\AllSyncUsersTEST-Result2
```

### <a name="parameters"></a>PARÂMETROS

#### <a name="-distinguishedname"></a>-DistinguishedName
DistinguishedName

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: False
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-immutableidguid"></a>-ImmutableIdGUID
ImmutableIdGUID

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-action"></a>-Action
Ação

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 3
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-output"></a>-Output
Nome de ficheiro de saída para ficheiros de registo

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 4
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-whatif"></a>-WhatIf
Mostra o que aconteceria se a execução do cmdlet.
O cmdlet não é executado.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: wi

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-confirm"></a>-Confirm
Pede-lhe confirmação antes de executar o cmdlet.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: cf

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Este cmdlet suporta os parâmetros comuns: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.
Para obter mais informações, veja about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).
