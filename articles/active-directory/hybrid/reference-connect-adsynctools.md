---
title: 'Azure AD Connect: ADSyncTools PowerShell Reference / Microsoft Docs'
description: Este documento fornece informações de referência para o módulo ADSyncTools.psm1 PowerShell.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.date: 11/30/2020
ms.subservice: hybrid
ms.author: billmath
ms.topic: reference
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2dd16f89851b9376557e544b86dc5e088891e63b
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96446981"
---
# <a name="azure-ad-connect--adsynctools-powershell-reference"></a>Azure AD Connect: ADSyncTools PowerShell Reference
A documentação que se segue fornece informações de referência para o Módulo PowerShell ADSyncTools.psm1 que está incluído no Azure AD Connect.

## <a name="install-the-adsynctools-powershell-module"></a>Instale o módulo PowerShell ADSyncTools
Para instalar o Módulo PowerShell ADSyncTools, faça o seguinte:

1.  Open Windows PowerShell com priviledges administrativos
2.  Digite ou copie e cole o seguinte: 
    ``` powershell
        [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
        Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force
        Import-module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\Tools\AdSyncTools"
    ```
3.  Acerte entrar.
4.  Para verificar se o módulo foi instalado, introduza ou copie e cole o seguinte"
    ```powershell
    Get-module AdSyncTools
    ```
5.  Deve agora ver informações sobre o módulo.


## <a name="clear-adsynctoolsconsistencyguid"></a>Clear-ADSyncToolsConsistencyGuid

### <a name="synopsis"></a>SINOPSE
Limpe o mS-Ds-ConsistênciaGuid do Utilizador AD

### <a name="syntax"></a>SINTAXE

```
Clear-ADSyncToolsConsistencyGuid [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>DESCRIÇÃO
Limpe o valor em mS-Ds-ConsistênciaGuid para o utilizador-alvo de AD

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
Utilizador alvo em AD para definir

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
Para mais informações, consulte about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) about_CommonParameters.

## <a name="confirm-adsynctoolsadmoduleloaded"></a>Confirm-ADSyncToolsADModuleLoaded

### <a name="synopsis"></a>SINOPSE
{{Preencha a sinopse}}

### <a name="syntax"></a>SINTAXE

```
Confirm-ADSyncToolsADModuleLoaded
```

### <a name="description"></a>DESCRIÇÃO
{{Preencha a Descrição}}

### <a name="examples"></a>EXEMPLOS

#### <a name="example-1"></a>Exemplo 1
```powershell
PS C:\> {{ Add example code here }}
```

{{ Adicione a descrição do exemplo aqui }}

## <a name="connect-adsyncdatabase"></a>Connect-AdSyncDatabase

### <a name="synopsis"></a>SINOPSE
{{Preencha a sinopse}}

### <a name="syntax"></a>SINTAXE

```
Connect-AdSyncDatabase [-Server] <String> [[-Instance] <String>] [[-Database] <String>] [[-UserName] <String>]
 [[-Password] <String>] [<CommonParameters>]
```

### <a name="description"></a>DESCRIÇÃO
{{Preencha a Descrição}}

### <a name="examples"></a>EXEMPLOS

#### <a name="example-1"></a>Exemplo 1
```powershell
PS C:\> {{ Add example code here }}
```

{{ Adicione a descrição do exemplo aqui }}

### <a name="parameters"></a>PARÂMETROS

#### <a name="-database"></a>-Base de dados
{{Preencher descrição da base de dados}}

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

#### <a name="-instance"></a>-Instância
{{Preenchimento descrição de instância}}

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
{{Preencha a descrição da palavra-passe}}

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
{{Preencha a descrição do nome do utilizador}}

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
Para mais informações, consulte about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) about_CommonParameters.

## <a name="export-adsynctoolsconsistencyguidmigration"></a>Export-ADSyncToolsConsistencyGuidMigration

### <a name="synopsis"></a>SINOPSE
Relatório de coerência de exportação

### <a name="syntax"></a>SINTAXE

```
Export-ADSyncToolsConsistencyGuidMigration [-AlternativeLoginId] [-UserPrincipalName] <String>
 [-ImmutableIdGUID] <String> [-Output] <String> [<CommonParameters>]
```

### <a name="description"></a>DESCRIÇÃO
Gera um relatório Da ConsistênciaGuid baseado num ficheiro CSV de importação de Import-ADSyncToolsImmutableIdMigration

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
Utilizar ID de Login Alternativo (correio)

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

#### <a name="-userprincipalname"></a>-Nome Do UtilizadorPrincipal
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

#### <a name="-immutableidguid"></a>-ImuttableIdGUID
ImutávelIdGUID

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

#### <a name="-output"></a>-Saída
Nome de ficheiro de saída para ficheiros CSV e LOG

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
Para mais informações, consulte about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) about_CommonParameters.

## <a name="get-adsyncsqlbrowserinstances"></a>Get-ADSyncSQLBrowserInstances

### <a name="synopsis"></a>SINOPSE
{{Preencha a sinopse}}

### <a name="syntax"></a>SINTAXE

```
Get-ADSyncSQLBrowserInstances [[-hostName] <String>]
```

### <a name="description"></a>DESCRIÇÃO
{{Preencha a Descrição}}

### <a name="examples"></a>EXEMPLOS

#### <a name="example-1"></a>Exemplo 1
```powershell
PS C:\> {{ Add example code here }}
```

{{ Adicione a descrição do exemplo aqui }}

### <a name="parameters"></a>PARÂMETROS

#### <a name="-hostname"></a>-hospedeiroName
{{Preencha o nome de anfitrião Descrição do nome}}

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
Obtenha o utilizador da AD

### <a name="syntax"></a>SINTAXE

```
Get-ADSyncToolsADuser [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>DESCRIÇÃO
Devolve um objeto AD TO DO: Suporte multi-floresta

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
Utilizador-alvo em AD para definir O Guid de Consistência

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
Para mais informações, consulte about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) about_CommonParameters.

## <a name="get-adsynctoolsconsistencyguid"></a>Get-ADSyncToolsConsistencyGuid

### <a name="synopsis"></a>SINOPSE
Obtenha o mS-Ds-ConsistênciaGuid do Utilizador AD

### <a name="syntax"></a>SINTAXE

```
Get-ADSyncToolsConsistencyGuid [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>DESCRIÇÃO
Devolve o valor no atributo mS-Ds-ConsistênciaGuid do utilizador-alvo em AD alvo em formato GUID

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
Utilizador alvo em AD para definir

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
Para mais informações, consulte about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) about_CommonParameters.

## <a name="get-adsynctoolsobjectguid"></a>Get-ADSyncToolsObjectGuid

### <a name="synopsis"></a>SINOPSE
Obtenha o ObjectGuid do Utilizador AD

### <a name="syntax"></a>SINTAXE

```
Get-ADSyncToolsObjectGuid [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>DESCRIÇÃO
Devolve o valor no atributo ObjectGUID do utilizador-alvo em AD alvo no formato GUID

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
Utilizador alvo em AD para definir

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
Para mais informações, consulte about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) about_CommonParameters.

## <a name="get-adsynctoolsrunhistory"></a>Get-ADSyncToolsRunHistory

### <a name="synopsis"></a>SINOPSE
Obtenha o histórico de execução de conexão AAD Connect

### <a name="syntax"></a>SINTAXE

```
Get-ADSyncToolsRunHistory [[-Days] <Int32>] [<CommonParameters>]
```

### <a name="description"></a>DESCRIÇÃO
Função que devolve o histórico de corrida de conexão AAD no formato XML

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

#### <a name="-days"></a>- Dias
{{Preenchimento de dias Descrição}}

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
Para mais informações, consulte about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) about_CommonParameters.

## <a name="get-adsynctoolssourceanchorchanged"></a>Get-ADSyncToolsSourceAnchorChanged

### <a name="synopsis"></a>SINOPSE
Obter utilizadores com SourceAnchor alterados erros

### <a name="syntax"></a>SINTAXE

```
Get-ADSyncToolsSourceAnchorChanged [-sourcePath] <Object> [-outputPath] <Object> [<CommonParameters>]
```

### <a name="description"></a>DESCRIÇÃO
Consultas de função AAD Connect Run History e exporta todos os utilizadores que reportam o Erro: "O atributo SourceAnchor mudou."

### <a name="examples"></a>EXEMPLOS

#### <a name="example-1"></a>EXEMPLO 1
```
#Required Parameters
```

$sourcePath = Read-Host -Prompt "Insira o seu caminho de ficheiro de registo com o nome do ficheiro" \<Source_Path\> #" $outputPath = Read-Host -Prompt "Insira o seu caminho de ficheiro com nome de ficheiro" \<Out_Path\> #"
 
 Get-ADSyncToolsUsersSourceAnchorChanged -sourcePath $sourcePath -outputPath $outputPath

#### <a name="example-2"></a>EXEMPLO 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARÂMETROS

#### <a name="-sourcepath"></a>-fonteApata
{{Preenchimento Origem Descrição}}

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
{{Preencha a descrição do caminho de saída}}

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
Para mais informações, consulte about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) about_CommonParameters.

## <a name="import-adsynctoolsimmutableidmigration"></a>Import-ADSyncToolsImmutableIdMigration

### <a name="synopsis"></a>SINOPSE
Import ImutávelID da AAD

### <a name="syntax"></a>SINTAXE

```
Import-ADSyncToolsImmutableIdMigration [-Output] <String> [-IncludeSyncUsersFromRecycleBin]
 [<CommonParameters>]
```

### <a name="description"></a>DESCRIÇÃO
Gera um ficheiro com todos os utilizadores sincronizados Azure AD que contêm o valor ImuttableID em requisitos de formato GUID: Módulo MSOnline PowerShell

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

#### <a name="-output"></a>-Saída
Arquivo CSV de saída

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

#### <a name="-includesyncusersfromrecyclebin"></a>-Incluir SyncUsersFromRecycleBin
Obtenha utilizadores sincronizados a partir do caixote de reciclagem Azure AD

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
Para mais informações, consulte about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) about_CommonParameters.


## <a name="invoke-adsyncdatabasequery"></a>Invoke-AdSyncDatabaseQuery

### <a name="synopsis"></a>SINOPSE
{{Preencha a sinopse}}

### <a name="syntax"></a>SINTAXE

```
Invoke-AdSyncDatabaseQuery [-SqlConnection] <SqlConnection> [[-Query] <String>] [<CommonParameters>]
```

### <a name="description"></a>DESCRIÇÃO
{{Preencha a Descrição}}

### <a name="examples"></a>EXEMPLOS

#### <a name="example-1"></a>Exemplo 1
```powershell
PS C:\> {{ Add example code here }}
```

{{ Adicione a descrição do exemplo aqui }}

### <a name="parameters"></a>PARÂMETROS

#### <a name="-query"></a>-Consulta
{{Preenchimento Descrição da consulta}}

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
{{Preencha a descrição do sqlConnection}}

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
Para mais informações, consulte about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) about_CommonParameters.

## <a name="remove-adsynctoolsexpiredcertificates"></a>Remove-ADSyncToolsExpiredCertificates

### <a name="synopsis"></a>SINOPSE
Script para remover certificados expirados do atributo UserCertificate

### <a name="syntax"></a>SINTAXE

```
Remove-ADSyncToolsExpiredCertificates [-TargetOU] <String> [[-BackupOnly] <Boolean>] [-ObjectClass] <String>
 [<CommonParameters>]
```

### <a name="description"></a>DESCRIÇÃO
Este script retira todos os objetos de uma Unidade Organizacional alvo no seu domínio ative Directory - filtrado pela Classe Object (Utilizador/Computador) e elimina todos os certificados expirados presentes no atributo UserCertificate.
Por predefinição (modo BackupOnly) apenas irá fazer cópias de segurança de certificados expirados para um ficheiro e não fazer quaisquer alterações em AD.
Se utilizar -BackupOnly $false então qualquer Certificado Expirado presente no atributo UserCertificate para estes objetos será removido da AD depois de ser copiado para arquivar.
Cada certificado será suportado até um nome de ficheiro separado: ObjectClass_ObjectGUID_CertThumprint.cer O script também criará um ficheiro de registo em formato CSV mostrando todos os utilizadores com certificados que são válidos ou expirados, incluindo a ação real tomada (Skipped/Exported/Deleted).

### <a name="examples"></a>EXEMPLOS

#### <a name="example-1"></a>EXEMPLO 1
```
Check all users in target OU - Expired Certificates will be copied to separated files and no certificates will be removed
```

Remove-ADSyncToolsExpiredCertificates -TargetOU "OU=Users,OU=Corp,DC=Contoso,DC=com" -Utilizador da ObjectClass

#### <a name="example-2"></a>EXEMPLO 2
```
Delete Expired Certs from all Computer objects in target OU - Expired Certificates will be copied to files and removed from AD
```

Remove-ADSyncToolsExpiredCertificates -TargetOU "OU=Computers,OU=Corp,DC=Contoso,DC=com" -ObjectClass computer -BackupOnly $false

### <a name="parameters"></a>PARÂMETROS

#### <a name="-targetou"></a>-TargetOU
Target OU para procurar objetos AD

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
BackupOnly não apagará quaisquer certificados de AD

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

#### <a name="-objectclass"></a>-Classe de Objetos
Filtro classe objeto

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
Para mais informações, consulte about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) about_CommonParameters.

## <a name="repair-adsynctoolsautoupgradestate"></a>Repair-ADSyncToolsAutoUpgradeState

### <a name="synopsis"></a>SINOPSE
Breve descrição

### <a name="syntax"></a>SINTAXE

```
Repair-ADSyncToolsAutoUpgradeState
```

### <a name="description"></a>DESCRIÇÃO
Longa descrição

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
{{Preencha a sinopse}}

### <a name="syntax"></a>SINTAXE

```
Resolve-ADSyncHostAddress [[-hostName] <String>]
```

### <a name="description"></a>DESCRIÇÃO
{{Preencha a Descrição}}

### <a name="examples"></a>EXEMPLOS

#### <a name="example-1"></a>Exemplo 1
```powershell
PS C:\> {{ Add example code here }}
```

{{ Adicione a descrição do exemplo aqui }}

### <a name="parameters"></a>PARÂMETROS

#### <a name="-hostname"></a>-hospedeiroName
{{Preencha o nome de anfitrião Descrição do nome}}

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
(A FAZER) Restaura atributo AD UserCertificate de um ficheiro de certificado

### <a name="syntax"></a>SINTAXE

```
Restore-ADSyncToolsExpiredCertificates
```

### <a name="description"></a>DESCRIÇÃO
Longa descrição

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
Definir mS-Ds-ConsistênciaGuid no Utilizador AD

### <a name="syntax"></a>SINTAXE

```
Set-ADSyncToolsConsistencyGuid [-User] <Object> [-Value] <Object> [<CommonParameters>]
```

### <a name="description"></a>DESCRIÇÃO
Desaponte um valor no atributo mS-Ds-ConsistênciaGuid para o utilizador-alvo de AD

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
Utilizador-alvo em AD para definir O Guid de Consistência

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

#### <a name="-value"></a>-Valor
Imutávelide (byte array, GUID, corda GUID ou linha Base64)

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
Para mais informações, consulte about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) about_CommonParameters.

## <a name="test-adsyncnetworkport"></a>Test-ADSyncNetworkPort

### <a name="synopsis"></a>SINOPSE
{{Preencha a sinopse}}

### <a name="syntax"></a>SINTAXE

```
Test-ADSyncNetworkPort [[-hostName] <String>] [[-port] <String>]
```

### <a name="description"></a>DESCRIÇÃO
{{Preencha a Descrição}}

### <a name="examples"></a>EXEMPLOS

#### <a name="example-1"></a>Exemplo 1
```powershell
PS C:\> {{ Add example code here }}
```

{{ Adicione a descrição do exemplo aqui }}

### <a name="parameters"></a>PARÂMETROS

#### <a name="-hostname"></a>-hospedeiroName
{{Preencha o nome de anfitrião Descrição do nome}}

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

#### <a name="-port"></a>-porto
{{Preencha a descrição da porta}}

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
Cria um arquivo de rastreios e passo de importação de AD

### <a name="syntax"></a>SINTAXE

```
Trace-ADSyncToolsADImport [[-ADConnectorXML] <String>] [[-dc] <String>] [[-rootDN] <String>]
 [[-filter] <String>] [-SkipCredentials] [[-ADwatermark] <String>] [<CommonParameters>]
```

### <a name="description"></a>DESCRIÇÃO
Rastreia todas as consultas de ldap de uma importação de AAD Connect AD de um determinado ponto de verificação de marca de água AD (cookie de partição). Cria um ficheiro de rastreio '.\ADimportTrace_yyyyMMddHHmmss.log' na pasta atual.

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
{{Preencha a descrição do ADConnectorxML}}

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
Ficheiro XML da Exportação de Conector AD

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
Controlador de domínio alvo

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

#### <a name="-filter"></a>-filtro
Raiz florestal DN

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
Tipos de objetos AD para rastrear \> * = todos os tipos de objetos

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

#### <a name="-adwatermark"></a>-Adwatermark
Se já estiver a funcionar como Administrador de Domínio, não há necessidade de fornecer credenciais de AD.
Entrada manual da marca de água, em vez de ficheiro XML, por exemplo, $ADwatermark = "TVNEUwMAAAAAAXyK9ir1zSAQAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA

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
Para mais informações, consulte about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) about_CommonParameters.

## <a name="trace-adsynctoolsldapquery"></a>Trace-ADSyncToolsLdapQuery

### <a name="synopsis"></a>SINOPSE
Breve descrição

### <a name="syntax"></a>SINTAXE

```
Trace-ADSyncToolsLdapQuery [-Context] <String> [-Server] <String> [-Port] <Int32> [-Filter] <String>
 [<CommonParameters>]
```

### <a name="description"></a>DESCRIÇÃO
Longa descrição

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

#### <a name="-context"></a>-Contexto
Param1 ajuda descrição

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
Param2 ajuda descrição

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
Param2 ajuda descrição

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
Param2 ajuda descrição

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
Para mais informações, consulte about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) about_CommonParameters.

## <a name="update-adsynctoolsconsistencyguidmigration"></a>Update-ADSyncToolsConsistencyGuidMigration

### <a name="synopsis"></a>SINOPSE
Atualiza os utilizadores com o novo ConsistencyGuid (ImmutableId)

### <a name="syntax"></a>SINTAXE

```
Update-ADSyncToolsConsistencyGuidMigration [[-DistinguishedName] <String>] [-ImmutableIdGUID] <String>
 [-Action] <String> [-Output] <String> [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>DESCRIÇÃO
Atualiza os utilizadores com o novo valor DeIguância (ImmutableId) retirado do Relatório Desmiliter da Consistência Esta função suporta o WhatIf Switch Nota: Relatório De consistênciaGuid deve ser importado com Demiliter de Tab

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

#### <a name="-distinguishedname"></a>-Nome distinto
Nome distinto

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

#### <a name="-immutableidguid"></a>-ImuttableIdGUID
ImutávelIdGUID

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

#### <a name="-action"></a>-Ação
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

#### <a name="-output"></a>-Saída
Nome de ficheiro de saída para ficheiros LOG

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

#### <a name="-whatif"></a>- O QueIf
Apresenta o que aconteceria mediante a execução do cmdlet.
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
Solicita a sua confirmação antes de executar o cmdlet.

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
Para mais informações, consulte about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) about_CommonParameters.
