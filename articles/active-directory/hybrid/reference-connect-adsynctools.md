---
title: 'Azure AD Connect: ADSyncTools PowerShell Reference [ Microsoft Docs'
description: Este documento fornece informações de referência para o módulo ADSyncTools.psm1 PowerShell.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.date: 04/23/2020
ms.subservice: hybrid
ms.author: billmath
ms.topic: reference
ms.collection: M365-identity-device-management
ms.openlocfilehash: 11c9d66f891e8f5d53fc2a965e75f095417d20d4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82184015"
---
# <a name="azure-ad-connect--adsynctools-powershell-reference"></a>Ligação Azure AD: Referência aDSyncTools PowerShell
A seguinte documentação fornece informações de referência para o Módulo PowerShell ADSyncTools.psm1 que está incluído com Azure AD Connect.

## <a name="install-the-adsynctools-powershell-module"></a>Instale o Módulo PowerShell ADSyncTools
Para instalar o Módulo PowerShell ADSyncTools faça o seguinte:

1.  Open Windows PowerShell com priviledges administrativos
2.  Digite ou copie e cole o seguinte: 
    ``` powershell
    Import-module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\Tools\AdSyncTools"
    ```
3.  Bata no enter.
4.  Para verificar se o módulo foi instalado, insira ou copie e cole o seguinte"
    ```powershell
    Get-module AdSyncTools
    ```
5.  Agora deve ver informações sobre o módulo.


## <a name="clear-adsynctoolsconsistencyguid"></a>Clear-ADSyncToolsConsistênciaGuid

### <a name="synopsis"></a>SINOPSE
Limpe o mS-Ds-Consistência-Guid do Utilizador AD

### <a name="syntax"></a>SINTAXE

```
Clear-ADSyncToolsConsistencyGuid [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>DESCRIÇÃO
Limpar o valor em mS-Ds-Consistência-Guia para o utilizador ad-alvo

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
Utilizador-alvo em AD para definir

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
Para mais informações,https://go.microsoft.com/fwlink/?LinkID=113216)consulte about_CommonParameters .

## <a name="confirm-adsynctoolsadmoduleloaded"></a>Confirm-AdsynctoolsAdmoduleloaded

### <a name="synopsis"></a>SINOPSE
{{Preencha a Sinopse}}

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
{{Preencha a Sinopse}}

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

#### <a name="-database"></a>-Base de Dados
{{Preencher Descrição da base de dados}}

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
{{Preencher Descrição da instância}}

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
{{Preencher Descrição da palavra-passe}}

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
{{Preencher Descrição do servidor}}

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
{{Preencher descrição do nome de utilizador}}

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
Para mais informações,https://go.microsoft.com/fwlink/?LinkID=113216)consulte about_CommonParameters .

## <a name="export-adsynctoolsconsistencyguidmigration"></a>Export-ADSyncToolsConsistênciaGuidMigration

### <a name="synopsis"></a>SINOPSE
Relatório de consistência das exportaçõesGuid

### <a name="syntax"></a>SINTAXE

```
Export-ADSyncToolsConsistencyGuidMigration [-AlternativeLoginId] [-UserPrincipalName] <String>
 [-ImmutableIdGUID] <String> [-Output] <String> [<CommonParameters>]
```

### <a name="description"></a>DESCRIÇÃO
Gera um relatório ConsistencyGuid baseado num ficheiro CSV de importação de Import-ADSyncToolsImmutableIdMigration

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
Utilize o ID de login alternativo (correio)

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

#### <a name="-immutableidguid"></a>-ImutávelIdGUID
IdGUID imutável

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
Para mais informações,https://go.microsoft.com/fwlink/?LinkID=113216)consulte about_CommonParameters .

## <a name="get-adsyncsqlbrowserinstances"></a>Get-ADSyncSQLBrowserInstances

### <a name="synopsis"></a>SINOPSE
{{Preencha a Sinopse}}

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

#### <a name="-hostname"></a>-hostName
{{Preencher descrição do nome do anfitrião}}

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
Devolve um objeto ad TO DO: Apoio multiflorestal

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
Utilizador-alvo em AD para definir ConsistênciaGuid

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
Para mais informações,https://go.microsoft.com/fwlink/?LinkID=113216)consulte about_CommonParameters .

## <a name="get-adsynctoolsconsistencyguid"></a>Get-ADSyncToolsConsistênciaGuid

### <a name="synopsis"></a>SINOPSE
Obtenha o mS-Ds-Consistência-Guid do Utilizador AD

### <a name="syntax"></a>SINTAXE

```
Get-ADSyncToolsConsistencyGuid [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>DESCRIÇÃO
Devolve o valor em mS-Ds-Consistência-Atributo do utilizador ad-alvo em formato GUID

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
Utilizador-alvo em AD para definir

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
Para mais informações,https://go.microsoft.com/fwlink/?LinkID=113216)consulte about_CommonParameters .

## <a name="get-adsynctoolsobjectguid"></a>Get-Adsynctoolsobjectguid

### <a name="synopsis"></a>SINOPSE
Obtenha o ObjectGuid do Utilizador AD

### <a name="syntax"></a>SINTAXE

```
Get-ADSyncToolsObjectGuid [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>DESCRIÇÃO
Devolve o valor no atributo objectguid do utilizador ad-alvo em formato GUID

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
Utilizador-alvo em AD para definir

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
Para mais informações,https://go.microsoft.com/fwlink/?LinkID=113216)consulte about_CommonParameters .

## <a name="get-adsynctoolsrunhistory"></a>Get-AdsynctoolsRunHistory

### <a name="synopsis"></a>SINOPSE
Obtenha aAD Connect Executar Histórico

### <a name="syntax"></a>SINTAXE

```
Get-ADSyncToolsRunHistory [[-Days] <Int32>] [<CommonParameters>]
```

### <a name="description"></a>DESCRIÇÃO
Função que devolve o Histórico de Execução de Ligação AAD em formato XML

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
{{Preencher descrição dos dias}}

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
Para mais informações,https://go.microsoft.com/fwlink/?LinkID=113216)consulte about_CommonParameters .

## <a name="get-adsynctoolssourceanchorchanged"></a>Get-AdsynctoolsSourceAnchorchanged

### <a name="synopsis"></a>SINOPSE
Obtenha utilizadores com SourceAnchor erros alterados

### <a name="syntax"></a>SINTAXE

```
Get-ADSyncToolsSourceAnchorChanged [-sourcePath] <Object> [-outputPath] <Object> [<CommonParameters>]
```

### <a name="description"></a>DESCRIÇÃO
Consultas de função AAD Connect Run History e exporta todos os utilizadores que relatam o Erro: "O atributo SourceAnchor mudou."

### <a name="examples"></a>EXEMPLOS

#### <a name="example-1"></a>EXEMPLO 1
```
#Required Parameters
```

$sourcePath = Read-Host -Prompt "Enter your log file\<\>path with file name" #" Source_Path " $outputPath =\<Read-Host -Prompt "Enter your file path with file name" #" out_Path\>"
 
 Get-ADSyncToolsUsersSourceAnchorChanged -sourcePath $sourcePath -outputPath $outputPath

#### <a name="example-2"></a>EXEMPLO 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARÂMETROS

#### <a name="-sourcepath"></a>-fonteCaminho
{{Preencher a descrição do caminho de origem}}

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
{{Preenchimento Descrição do caminho}}

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
Para mais informações,https://go.microsoft.com/fwlink/?LinkID=113216)consulte about_CommonParameters .

## <a name="import-adsynctoolsimmutableidmigration"></a>Import-ADSyncToolsImmutableIdMigration

### <a name="synopsis"></a>SINOPSE
Importação imutável ID da AAD

### <a name="syntax"></a>SINTAXE

```
Import-ADSyncToolsImmutableIdMigration [-Output] <String> [-IncludeSyncUsersFromRecycleBin]
 [<CommonParameters>]
```

### <a name="description"></a>DESCRIÇÃO
Gera um ficheiro com todos os utilizadores sincronizados Da Azure AD que contenham o valor Imutável id em requisitos de formato GUID: Módulo MSOnline PowerShell

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

#### <a name="-includesyncusersfromrecyclebin"></a>-Incluir Utilizadores SincronizadosFromRecycleBin
Obtenha utilizadores sincronizados do Bin de Reciclagem de AD Azure

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
Para mais informações,https://go.microsoft.com/fwlink/?LinkID=113216)consulte about_CommonParameters .


## <a name="invoke-adsyncdatabasequery"></a>Invocar-AdSyncDatabaseQuery

### <a name="synopsis"></a>SINOPSE
{{Preencha a Sinopse}}

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
{{Preencher descrição de consulta}}

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
{{Preencher Descrição de SqlConnection}}

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
Para mais informações,https://go.microsoft.com/fwlink/?LinkID=113216)consulte about_CommonParameters .

## <a name="remove-adsynctoolsexpiredcertificates"></a>Remover ADSyncToolsCertificados expirados

### <a name="synopsis"></a>SINOPSE
Script para remover certificados expirados do atributo do Certificado de Utilizador

### <a name="syntax"></a>SINTAXE

```
Remove-ADSyncToolsExpiredCertificates [-TargetOU] <String> [[-BackupOnly] <Boolean>] [-ObjectClass] <String>
 [<CommonParameters>]
```

### <a name="description"></a>DESCRIÇÃO
Este script retira todos os objetos de uma Unidade Organizacional alvo no seu domínio de Diretório Ativo - filtrado pela Classe objeto (Utilizador/Computador) e elimina todos os certificados expirados presentes no atributo do UserCertificate.
Por predefinição (modo BackupOnly) só irá fazer backup de certificados caducados para um ficheiro e não fazer quaisquer alterações em AD.
Se utilizar -BackupOnly $false então qualquer Certificado Expirado presente no atributo do UserCertificate para estes objetos será removido da AD depois de ter sido copiado para arquivar.
Cada certificado será apoiado num nome de ficheiro separado: ObjectClass_ObjectGUID_CertThumprint.cer O script também criará um ficheiro de registo no formato CSV mostrando a todos os utilizadores certificados válidos ou caducados, incluindo a ação real tomada (Skiped/Exported/Deleted).

### <a name="examples"></a>EXEMPLOS

#### <a name="example-1"></a>EXEMPLO 1
```
Check all users in target OU - Expired Certificates will be copied to separated files and no certificates will be removed
```

Remover ADSyncToolsCertificados expirados -TargetOU "OU=Utilizadores,OU=Corp,DC=Contoso,DC=com" -ObjectClass user

#### <a name="example-2"></a>EXEMPLO 2
```
Delete Expired Certs from all Computer objects in target OU - Expired Certificates will be copied to files and removed from AD
```

Remover ADSyncToolsExpiredCertificates -TargetOU "OU=Computers,OU=Corp,DC=Contoso,DC=com" -ObjectClass computer -BackupOnly $false

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
BackupOnly não eliminará quaisquer certificados da AD

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
Para mais informações,https://go.microsoft.com/fwlink/?LinkID=113216)consulte about_CommonParameters .

## <a name="repair-adsynctoolsautoupgradestate"></a>Reparação-AdsynctoolsAutoUpgradeState

### <a name="synopsis"></a>SINOPSE
Descrição curta

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

## <a name="resolve-adsynchostaddress"></a>Endereço de anfitrião resolve-adsync

### <a name="synopsis"></a>SINOPSE
{{Preencha a Sinopse}}

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

#### <a name="-hostname"></a>-hostName
{{Preencher descrição do nome do anfitrião}}

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

## <a name="restore-adsynctoolsexpiredcertificates"></a>Restaurar-ADSyncToolsCertificados expirados

### <a name="synopsis"></a>SINOPSE
(A FAZER) Restaura a atribuição de Certificado de Utilizador AD a partir de um ficheiro de certificado

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

## <a name="set-adsynctoolsconsistencyguid"></a>Set-ADSyncToolsConsistênciaGuid

### <a name="synopsis"></a>SINOPSE
Definir mS-Ds-Consistência-Guia no utilizador ad

### <a name="syntax"></a>SINTAXE

```
Set-ADSyncToolsConsistencyGuid [-User] <Object> [-Value] <Object> [<CommonParameters>]
```

### <a name="description"></a>DESCRIÇÃO
Detete um valor no atributo mS-Ds-Consistência-Guia para o utilizador ad-alvo

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
Utilizador-alvo em AD para definir ConsistênciaGuid

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
ImutávelId (matriz byte, fio GUID, corda GUID ou corda Base64)

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
Para mais informações,https://go.microsoft.com/fwlink/?LinkID=113216)consulte about_CommonParameters .

## <a name="test-adsyncnetworkport"></a>Teste-AdsyncNetworkport

### <a name="synopsis"></a>SINOPSE
{{Preencha a Sinopse}}

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

#### <a name="-hostname"></a>-hostName
{{Preencher descrição do nome do anfitrião}}

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
{{Preencher porta Descrição}}

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

## <a name="trace-adsynctoolsadimport"></a>Trace-Adsynctoolsadimport

### <a name="synopsis"></a>SINOPSE
Cria um ficheiro de vestígios e Passo de Importação AD

### <a name="syntax"></a>SINTAXE

```
Trace-ADSyncToolsADImport [[-ADConnectorXML] <String>] [[-dc] <String>] [[-rootDN] <String>]
 [[-filter] <String>] [-SkipCredentials] [[-ADwatermark] <String>] [<CommonParameters>]
```

### <a name="description"></a>DESCRIÇÃO
Traça todas as consultas de ldap de um AAD Connect AD Import executado a partir de um determinado ponto de verificação de marca de água AD (cookie de partição). Cria um ficheiro de rastreio '.\ADimportTrace_yyyyMMddHHmmss.log' na pasta atual.

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
{{Preencher ADConnectorXML Descrição}}

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
Ficheiro XML de Exportação de Conector AD

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

#### <a name="-rootdn"></a>-raizDN
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

#### <a name="-skipcredentials"></a>-SkipCredenciais
Tipos de objetos \> AD para rastrear * = todos os tipos de objetos

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
Se já está a funcionar como Administrador de Domínio, não há necessidade de fornecer credenciais de AD.
Entrada manual de marca de água, em vez de ficheiro XML, por exemplo, $ADwatermark = "TVNEUwMAAAAAXyK9ir1zSAQAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA

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
Para mais informações,https://go.microsoft.com/fwlink/?LinkID=113216)consulte about_CommonParameters .

## <a name="trace-adsynctoolsldapquery"></a>Trace-ADSyncToolsLdapQuery

### <a name="synopsis"></a>SINOPSE
Descrição curta

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
Descrição da ajuda param1

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
Descrição da ajuda param2

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
Descrição da ajuda param2

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
Descrição da ajuda param2

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
Para mais informações,https://go.microsoft.com/fwlink/?LinkID=113216)consulte about_CommonParameters .

## <a name="update-adsynctoolsconsistencyguidmigration"></a>AdSyncToolsConsistênciaGuidMigration

### <a name="synopsis"></a>SINOPSE
Atualiza os utilizadores com o novo Consistência (ImutávelId)

### <a name="syntax"></a>SINTAXE

```
Update-ADSyncToolsConsistencyGuidMigration [[-DistinguishedName] <String>] [-ImmutableIdGUID] <String>
 [-Action] <String> [-Output] <String> [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>DESCRIÇÃO
Atualiza os utilizadores com o novo valor ConsistênciaGuid (ImutableId) retirado do Relatório ConsistênciaGuid Esta função suporta o interruptor WhatIf Nota: Relatório DeconsistênciaDeve ser importado com Tab Demiliter

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

#### <a name="-distinguishedname"></a>-Distinto Nome
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

#### <a name="-immutableidguid"></a>-ImutávelIdGUID
IdGUID imutável

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

#### <a name="-whatif"></a>- O que se
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
Para mais informações,https://go.microsoft.com/fwlink/?LinkID=113216)consulte about_CommonParameters .
