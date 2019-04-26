---
title: 'Azure AD Connect: Referência do PowerShell ADSyncConfig | Documentos da Microsoft'
description: Este documento fornece informações de referência para o módulo do ADSyncConfig.psm1 PowerShell.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.date: 01/24/2019
ms.subservice: hybrid
ms.author: billmath
ms.topic: reference
ms.collection: M365-identity-device-management
ms.openlocfilehash: 554bb99121190198982f64deb6ee0674aa8831ed
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60381200"
---
# <a name="azure-ad-connect--adsyncconfig-powershell-reference"></a>Azure AD Connect:  Referência do PowerShell ADSyncConfig
A seguinte documentação fornece informações de referência para o módulo do PowerShell ADSyncConfig.psm1 que está incluído com o Azure AD Connect.


## <a name="get-adsyncadconnectoraccount"></a>Get-ADSyncADConnectorAccount

### <a name="synopsis"></a>SINOPSE
Obtém o nome da conta e o domínio que está configurado em cada conector AD

### <a name="syntax"></a>SINTAXE

```
Get-ADSyncADConnectorAccount
```

### <a name="description"></a>DESCRIÇÃO
Esta função utiliza o cmdlet 'Get-ADSyncConnector' que está presente no AAD Connect para recuperar de parâmetros de conectividade de uma tabela que mostra a conta de conector ou conectores do AD.

### <a name="examples"></a>EXEMPLOS

#### <a name="example-1"></a>EXEMPLO 1
```
Get-ADSyncADConnectorAccount
```

## <a name="get-adsyncobjectswithinheritancedisabled"></a>Get-ADSyncObjectsWithInheritanceDisabled

### <a name="synopsis"></a>SINOPSE
Obtém objetos de AD com a herança de permissão desativada

### <a name="syntax"></a>SINTAXE

```
Get-ADSyncObjectsWithInheritanceDisabled [-SearchBase] <String> [[-ObjectClass] <String>] [<CommonParameters>]
```

### <a name="description"></a>DESCRIÇÃO
Pesquisa no AD a partir do parâmetro SearchBase e retorna todos os objetos, filtrados pelo parâmetro de ObjectClass, que têm a herança de ACL atualmente desativada.

### <a name="examples"></a>EXEMPLOS

#### <a name="example-1"></a>EXEMPLO 1
```
Find objects with disabled inheritance in 'Contoso' domain (by default returns 'organizationalUnit' objects only)
```

Get-ADSyncObjectsWithInheritanceDisabled -SearchBase 'Contoso'

#### <a name="example-2"></a>EXEMPLO 2
```
Find 'user' objects with disabled inheritance in 'Contoso' domain
```

Get-ADSyncObjectsWithInheritanceDisabled -SearchBase 'Contoso' -ObjectClass 'user'

#### <a name="example-3"></a>EXEMPLO 3
```
Find all types of objects with disabled inheritance in a OU
```

Get-ADSyncObjectsWithInheritanceDisabled -SearchBase OU=AzureAD,DC=Contoso,DC=com -ObjectClass '*'

### <a name="parameters"></a>PARÂMETROS

#### <a name="-searchbase"></a>-SearchBase
SearchBase para a consulta LDAP que pode ser um DistinguishedName de domínio do AD ou um FQDN

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

#### <a name="-objectclass"></a>-ObjectClass
A classe dos objetos para pesquisar o que pode ser ' *' (para qualquer classe de objeto), "user", "grupo", "contentor", etc. Por predefinição, esta função irá procurar a classe de objeto 'organizationalUnit'.

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
Default value: OrganizationalUnit
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Este cmdlet suporta os parâmetros comuns: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.
Para obter mais informações, veja about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="set-adsyncbasicreadpermissions"></a>Set-ADSyncBasicReadPermissions

### <a name="synopsis"></a>SINOPSE
Inicialize sua floresta do Active Directory e o domínio de acordo com as permissões de leitura básicos.

### <a name="syntax"></a>SINTAXE

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [-SkipAdminSdHolders]
 [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>DESCRIÇÃO
A função de conjunto ADSyncBasicReadPermissions dará as permissões necessárias para a conta de sincronização do AD, que incluem o seguinte:
1.
Acesso de propriedade de leitura em todos os atributos para todos os objetos de computador subordinados
2.
Acesso de propriedade de leitura em todos os atributos para todos os objetos de dispositivo subordinados
3.
Acesso de propriedade de leitura em todos os atributos para todos os objetos subordinados foreignsecurityprincipal
5.
Acesso de propriedade de leitura em todos os atributos para todos os objetos de utilizador de subordinados
6.
Acesso de propriedade de leitura em todos os atributos para todos os objetos inetorgperson subordinados
7.
Acesso de propriedade de leitura em todos os atributos para todos os objetos subordinados do grupo
8.
Acesso de propriedade de leitura em todos os atributos para os objetos subordinados de contactos

Estas permissões são aplicadas a todos os domínios na floresta.
Opcionalmente, pode fornecer um DistinguishedName no parâmetro ADobjectDN para definir estas permissões nesse objeto AD só (incluindo herança para objetos sub).

### <a name="examples"></a>EXEMPLOS

#### <a name="example-1"></a>EXEMPLO 1
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>EXEMPLO 2
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>EXEMPLO 3
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>EXEMPLO 4
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARÂMETROS

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
O nome da conta do Active Directory ou será utilizada pelo Azure AD Connect Sync para gerenciar objetos no diretório.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
O domínio da conta do Active Directory ou será utilizada pelo Azure AD Connect Sync para gerenciar objetos no diretório.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
O DistinguishedName da conta do Active Directory que está ou será utilizada pelo Azure AD Connect Sync para gerenciar objetos no diretório.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
DistinguishedName do objeto alvo AD para definir permissões (opcional)

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
Parâmetro opcional para indicar se o contêiner AdminSDHolder não deve ser atualizado com estas permissões

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

## <a name="set-adsyncexchangehybridpermissions"></a>Set-ADSyncExchangeHybridPermissions

### <a name="synopsis"></a>SINOPSE
Inicialize sua floresta do Active Directory e o domínio de recurso de híbrida do Exchange.

### <a name="syntax"></a>SINTAXE

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [-SkipAdminSdHolders]
 [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>DESCRIÇÃO
A função de conjunto ADSyncExchangeHybridPermissions dará as permissões necessárias para a conta de sincronização do AD, que incluem o seguinte:
1.
Acesso de propriedade de leitura/escrita em todos os atributos para todos os objetos de utilizador de subordinados
2.
Acesso de propriedade de leitura/escrita em todos os atributos para todos os objetos inetorgperson subordinados
3.
Acesso de propriedade de leitura/escrita em todos os atributos para todos os objetos subordinados do grupo
4.
Acesso de propriedade de leitura/escrita em todos os atributos para os objetos subordinados de contactos

Estas permissões são aplicadas a todos os domínios na floresta.
Opcionalmente, pode fornecer um DistinguishedName no parâmetro ADobjectDN para definir estas permissões nesse objeto AD só (incluindo herança para objetos sub).

### <a name="examples"></a>EXEMPLOS

#### <a name="example-1"></a>EXEMPLO 1
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>EXEMPLO 2
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>EXEMPLO 3
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>EXEMPLO 4
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARÂMETROS

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
O nome da conta do Active Directory ou será utilizada pelo Azure AD Connect Sync para gerenciar objetos no diretório.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
O domínio da conta do Active Directory ou será utilizada pelo Azure AD Connect Sync para gerenciar objetos no diretório.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
O DistinguishedName da conta do Active Directory que está ou será utilizada pelo Azure AD Connect Sync para gerenciar objetos no diretório.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
DistinguishedName do objeto alvo AD para definir permissões (opcional)

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
Parâmetro opcional para indicar se o contêiner AdminSDHolder não deve ser atualizado com estas permissões

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

## <a name="set-adsyncexchangemailpublicfolderpermissions"></a>Set-ADSyncExchangeMailPublicFolderPermissions

### <a name="synopsis"></a>SINOPSE
Inicialize sua floresta do Active Directory e o domínio de recurso de pastas públicas de correio Exchange.

### <a name="syntax"></a>SINTAXE

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountName <String>
 -ADConnectorAccountDomain <String> [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm]
 [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>]
 [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>DESCRIÇÃO
A função de conjunto ADSyncExchangeMailPublicFolderPermissions dará as permissões necessárias para a conta de sincronização do AD, que incluem o seguinte:
1.
Acesso de propriedade de leitura em todos os atributos para todos os objetos subordinados publicfolder

Estas permissões são aplicadas a todos os domínios na floresta.
Opcionalmente, pode fornecer um DistinguishedName no parâmetro ADobjectDN para definir estas permissões nesse objeto AD só (incluindo herança para objetos sub).

### <a name="examples"></a>EXEMPLOS

#### <a name="example-1"></a>EXEMPLO 1
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>EXEMPLO 2
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>EXEMPLO 3
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>EXEMPLO 4
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARÂMETROS

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
O nome da conta do Active Directory ou será utilizada pelo Azure AD Connect Sync para gerenciar objetos no diretório.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
O domínio da conta do Active Directory ou será utilizada pelo Azure AD Connect Sync para gerenciar objetos no diretório.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
O DistinguishedName da conta do Active Directory que está ou será utilizada pelo Azure AD Connect Sync para gerenciar objetos no diretório.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
DistinguishedName do objeto alvo AD para definir permissões (opcional)

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
Parâmetro opcional para indicar se o contêiner AdminSDHolder não deve ser atualizado com estas permissões

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

## <a name="set-adsyncmsdsconsistencyguidpermissions"></a>Set-ADSyncMsDsConsistencyGuidPermissions

### <a name="synopsis"></a>SINOPSE
Inicialize sua floresta do Active Directory e o domínio de recurso de mS-DS-ConsistencyGuid.

### <a name="syntax"></a>SINTAXE

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>]
 [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>DESCRIÇÃO
A função de conjunto ADSyncMsDsConsistencyGuidPermissions dará as permissões necessárias para a conta de sincronização do AD, que incluem o seguinte:
1.
Acesso de propriedade de leitura/escrita no atributo mS-DS-ConsistencyGuid para todos os objetos de utilizador de subordinados

Estas permissões são aplicadas a todos os domínios na floresta.
Opcionalmente, pode fornecer um DistinguishedName no parâmetro ADobjectDN para definir estas permissões nesse objeto AD só (incluindo herança para objetos sub).

### <a name="examples"></a>EXEMPLOS

#### <a name="example-1"></a>EXEMPLO 1
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>EXEMPLO 2
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>EXEMPLO 3
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>EXEMPLO 4
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARÂMETROS

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
O nome da conta do Active Directory ou será utilizada pelo Azure AD Connect Sync para gerenciar objetos no diretório.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
O domínio da conta do Active Directory ou será utilizada pelo Azure AD Connect Sync para gerenciar objetos no diretório.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
O DistinguishedName da conta do Active Directory que está ou será utilizada pelo Azure AD Connect Sync para gerenciar objetos no diretório.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
DistinguishedName do objeto alvo AD para definir permissões (opcional)

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
Parâmetro opcional para indicar se o contêiner AdminSDHolder não deve ser atualizado com estas permissões

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

## <a name="set-adsyncpasswordhashsyncpermissions"></a>Set-ADSyncPasswordHashSyncPermissions

### <a name="synopsis"></a>SINOPSE
Inicialize sua floresta do Active Directory e o domínio de sincronização de hash de palavra-passe.

### <a name="syntax"></a>SINTAXE

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN <String> [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>DESCRIÇÃO
A função de conjunto ADSyncPasswordHashSyncPermissions dará as permissões necessárias para a conta de sincronização do AD, que incluem o seguinte:
1.
Replicação de alterações de diretório
2.
Replicar diretório muda tudo

Estas permissões são atribuídas a todos os domínios na floresta.

### <a name="examples"></a>EXEMPLOS

#### <a name="example-1"></a>EXEMPLO 1
```
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>EXEMPLO 2
```
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARÂMETROS

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
O nome da conta do Active Directory que será utilizada pelo Azure AD Connect Sync para gerenciar objetos no diretório.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
O domínio da conta do Active Directory que será utilizada pelo Azure AD Connect Sync para gerenciar objetos no diretório.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
O DistinguishedName da conta do Active Directory que será utilizada pelo Azure AD Connect Sync para gerenciar objetos no diretório.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
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

## <a name="set-adsyncpasswordwritebackpermissions"></a>Set-ADSyncPasswordWritebackPermissions

### <a name="synopsis"></a>SINOPSE
Inicialize sua floresta do Active Directory e o domínio de escrita-repetição de palavras-passe do Azure AD.

### <a name="syntax"></a>SINTAXE

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>]
 [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>DESCRIÇÃO
A função de conjunto ADSyncPasswordWritebackPermissions dará as permissões necessárias para a conta de sincronização do AD, que incluem o seguinte:
1.
Repor palavra-passe em objetos de utilizador de subordinados
2.
Acesso de propriedade de escrita no atributo lockoutTime para todos os objetos de utilizador de subordinados
3.
Acesso de propriedade de escrita no atributo pwdLastSet para todos os objetos de utilizador de subordinados

Estas permissões são aplicadas a todos os domínios na floresta.
Opcionalmente, pode fornecer um DistinguishedName no parâmetro ADobjectDN para definir estas permissões nesse objeto AD só (incluindo herança para objetos sub).

### <a name="examples"></a>EXEMPLOS

#### <a name="example-1"></a>EXEMPLO 1
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>EXEMPLO 2
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>EXEMPLO 3
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>EXEMPLO 4
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARÂMETROS

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
O nome da conta do Active Directory ou será utilizada pelo Azure AD Connect Sync para gerenciar objetos no diretório.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
O domínio da conta do Active Directory ou será utilizada pelo Azure AD Connect Sync para gerenciar objetos no diretório.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
O DistinguishedName da conta do Active Directory que está ou será utilizada pelo Azure AD Connect Sync para gerenciar objetos no diretório.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
DistinguishedName do objeto alvo AD para definir permissões (opcional)

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
Parâmetro opcional para indicar se o contêiner AdminSDHolder não deve ser atualizado com estas permissões

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

## <a name="set-adsyncrestrictedpermissions"></a>Set-ADSyncRestrictedPermissions

### <a name="synopsis"></a>SINOPSE
Restringir permissões num objeto AD que caso contrário, não está incluído em qualquer grupo de segurança protegido do AD.
Um exemplo típico é a conta do AD Connect (MSOL) criada automaticamente pelo AAD Connect.
Esta conta tem permissões de replicadas em todos os domínios, no entanto pode ficar facilmente comprometido, não está protegida.

### <a name="syntax"></a>SINTAXE

```
Set-ADSyncRestrictedPermissions [-ADConnectorAccountDN] <String> [-Credential] <PSCredential>
 [-DisableCredentialValidation] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>DESCRIÇÃO
A função de conjunto ADSyncRestrictedPermissions irá restringir permissões oo a conta fornecida.
Fortalecendo permissões envolve os seguintes passos:
1.
Desativar a herança do objeto especificado
2.
Remova todas as ACEs de objeto específico, exceto ACEs específicos para o próprio.
Queremos manter as permissões predefinidas intactos quando se trata-se para o próprio.
3.
Atribua estas permissões específicas:

        Type    Name                                        Access              Applies To
        =============================================================================================
        Allow   SYSTEM                                      Full Control        This object
        Allow   Enterprise Admins                           Full Control        This object
        Allow   Domain Admins                               Full Control        This object
        Allow   Administrators                              Full Control        This object

        Allow   Enterprise Domain Controllers               List Contents
                                                            Read All Properties
                                                            Read Permissions    This object

        Allow   Authenticated Users                         List Contents
                                                            Read All Properties
                                                            Read Permissions    This object

### <a name="examples"></a>EXEMPLOS

#### <a name="example-1"></a>EXEMPLO 1
```
Set-ADSyncRestrictedPermissions -ADConnectorAccountDN "CN=TestAccount1,CN=Users,DC=Contoso,DC=com" -Credential $(Get-Credential)
```

### <a name="parameters"></a>PARÂMETROS

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
DistinguishedName da conta do Active Directory tem de ser fortalecida cujas permissões.
Isto é, normalmente, a conta de MSOL_nnnnnnnnnn ou uma conta de domínio personalizado que está configurada no seu conector do AD.

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

#### <a name="-credential"></a>-Credential
Credencial de administrador que tem os privilégios necessários para restringir as permissões na conta ADConnectorAccountDN. Isto é, normalmente, o administrador de empresa ou de domínio. Utilize o nome de domínio completamente qualificado da conta de administrador para evitar falhas de pesquisa de conta.
Exemplo: CONTOSO\admin

```yaml
Type: PSCredential
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-disablecredentialvalidation"></a>-DisableCredentialValidation
Quando DisableCredentialValidation é usado, a função não irá verificar se as credenciais fornecidas no - credenciais são válidas no AD e se a conta fornecida tem os privilégios necessários para restringir as permissões na conta ADConnectorAccountDN.

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

## <a name="set-adsyncunifiedgroupwritebackpermissions"></a>Set-ADSyncUnifiedGroupWritebackPermissions

### <a name="synopsis"></a>SINOPSE
Inicialize sua floresta do Active Directory e o domínio de repetição de escrita do grupo do Azure AD.

### <a name="syntax"></a>SINTAXE

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>]
 [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>DESCRIÇÃO
A função de conjunto ADSyncUnifiedGroupWritebackPermissions dará as permissões necessárias para a conta de sincronização do AD, que incluem o seguinte:
1.
Grupo de genérica leitura/escrita, Delete, Eliminar árvore e subordinados Create\Delete para todos os tipos de objeto e Subobjetos

Estas permissões são aplicadas a todos os domínios na floresta.
Opcionalmente, pode fornecer um DistinguishedName no parâmetro ADobjectDN para definir estas permissões nesse objeto AD só (incluindo herança para objetos sub).
Neste caso, ADobjectDN será o nome distinto do contentor que desejar ligar com a funcionalidade de GroupWriteback.

### <a name="examples"></a>EXEMPLOS

#### <a name="example-1"></a>EXEMPLO 1
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>EXEMPLO 2
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>EXEMPLO 3
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>EXEMPLO 4
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARÂMETROS

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
O nome da conta do Active Directory ou será utilizada pelo Azure AD Connect Sync para gerenciar objetos no diretório.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
O domínio da conta do Active Directory ou será utilizada pelo Azure AD Connect Sync para gerenciar objetos no diretório.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
O DistinguishedName da conta do Active Directory que está ou será utilizada pelo Azure AD Connect Sync para gerenciar objetos no diretório.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
DistinguishedName do objeto alvo AD para definir permissões (opcional)

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
Parâmetro opcional para indicar se o contêiner AdminSDHolder não deve ser atualizado com estas permissões

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

## <a name="show-adsyncadobjectpermissions"></a>Show-ADSyncADObjectPermissions

### <a name="synopsis"></a>SINOPSE
Mostra as permissões de um objeto do AD especificado.

### <a name="syntax"></a>SINTAXE

```
Show-ADSyncADObjectPermissions [-ADobjectDN] <String> [<CommonParameters>]
```

### <a name="description"></a>DESCRIÇÃO
Esta função devolve todas as permissões do AD atualmente definidas para um determinado objeto AD fornecido no parâmetro - ADobjectDN.
O ADobjectDN deve ser fornecido num formato DistinguishedName.

### <a name="examples"></a>EXEMPLOS

#### <a name="example-1"></a>EXEMPLO 1
```
Show-ADSyncADObjectPermissions -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARÂMETROS

#### <a name="-adobjectdn"></a>-ADobjectDN
{{Preencher a descrição de ADobjectDN}}

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

#### <a name="commonparameters"></a>CommonParameters
Este cmdlet suporta os parâmetros comuns: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.
Para obter mais informações, veja about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).
