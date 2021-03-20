---
title: 'Azure AD Connect: ADSyncConfig PowerShell Reference | Microsoft Docs'
description: Este documento fornece informações de referência para o módulo ADSyncConfig.psm1 PowerShell.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.date: 01/24/2019
ms.subservice: hybrid
ms.author: billmath
ms.topic: reference
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2a2126aceba8724b46de094d14db754d704500c6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "85850976"
---
# <a name="azure-ad-connect--adsyncconfig-powershell-reference"></a>Azure AD Connect: ADSyncConfig PowerShell Reference
A documentação que se segue fornece informações de referência para o Módulo PowerShell ADSyncConfig.psm1 que está incluído no Azure AD Connect.


## <a name="get-adsyncadconnectoraccount"></a>Get-ADSyncADConnectorAccount

### <a name="synopsis"></a>SINOPSE
Obtém o nome e domínio da conta que está configurado em cada Conector AD

### <a name="syntax"></a>SINTAXE

```
Get-ADSyncADConnectorAccount
```

### <a name="description"></a>DESCRIÇÃO
Esta função utiliza o cmdlet 'Get-ADSyncConnector' que está presente no AAD Connect para recuperar dos Parâmetros de Conectividade uma tabela que mostra a conta de Conector AD.

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
Pesquisas em AD a partir do parâmetro SearchBase e devolve todos os objetos, filtrados pelo parâmetro ObjectClass, que têm a Herança ACL atualmente desativada.

### <a name="examples"></a>EXEMPLOS

#### <a name="example-1"></a>EXEMPLO 1
Encontre objetos com herança desativada no domínio 'Contoso' (por defeito devolve apenas objetos 'organizationalUnit')
```
Get-ADSyncObjectsWithInheritanceDisabled -SearchBase 'Contoso'
```

#### <a name="example-2"></a>EXEMPLO 2
Encontre objetos 'user' com herança desativada no domínio 'Contoso'
```
Get-ADSyncObjectsWithInheritanceDisabled -SearchBase 'Contoso' -ObjectClass 'user'
```

#### <a name="example-3"></a>EXEMPLO 3
Encontre todos os tipos de objetos com herança desativada num OU
```
Get-ADSyncObjectsWithInheritanceDisabled -SearchBase OU=AzureAD,DC=Contoso,DC=com -ObjectClass '*'
```



### <a name="parameters"></a>PARÂMETROS

#### <a name="-searchbase"></a>- Base de Pesquisa
A Base de Pesquisa para a consulta LDAP que pode ser um Nome Distinto de Domínio AD ou um FQDN

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

#### <a name="-objectclass"></a>-Classe de Objetos
A classe dos objetos para pesquisar que podem ser '*' (para qualquer classe de objetos), 'utilizador', 'grupo', 'recipiente', etc. Por predefinição, esta função procurará a classe de objeto 'organizationalUnit'.

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
Para mais informações, consulte about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) about_CommonParameters.

## <a name="set-adsyncbasicreadpermissions"></a>Set-ADSyncBasicReadPermissions

### <a name="synopsis"></a>SINOPSE
Inicialize a sua floresta e domínio ative directory para permissões básicas de leitura.

### <a name="syntax"></a>SINTAXE

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>Nome distinto
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [-SkipAdminSdHolders]
 [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>DESCRIÇÃO
A Função Set-ADSyncBasicReadPermissions dará permissões necessárias à conta de sincronização de AD, que incluem o seguinte:
1.
Leia o acesso da propriedade em todos os atributos para todos os objetos de computador descendentes
2.
Leia o acesso da propriedade em todos os atributos para todos os objetos de dispositivo descendente
3.
Leia o acesso à propriedade em todos os atributos para todos os objetos de segurança estrangeira descendentes
5.
Leia o acesso da propriedade em todos os atributos para todos os objetos de utilizador descendentes
6.
Leia o acesso da propriedade em todos os atributos para todos os objetos inetorgperson descendentes
7.
Leia o acesso da propriedade em todos os atributos para todos os objetos de grupo descendentes
8.
Leia o acesso da propriedade em todos os atributos para todos os objetos de contacto descendentes

Estas permissões são aplicadas a todos os domínios da floresta.
Opcionalmente, pode fornecer um Nome Distinto no parâmetro ADobjectDN para definir estas permissões apenas nesse Objeto AD (incluindo herança a sub objetos).

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
O Nome da conta Ative Directory que é ou será utilizado pelo Azure AD Connect Sync para gerir objetos no diretório.

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
O Domínio da conta Ative Directory que é ou será utilizado pelo Azure AD Connect Sync para gerir objetos no diretório.

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

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountdn
O Nome Distinto da conta Ative Directory que é ou será utilizado pela Azure AD Connect Sync para gerir objetos no diretório.

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
Nome distinto do objeto AD alvo para definir permissões (opcional)

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
Parâmetro opcional para indicar se o contentor AdminSDHolder não deve ser atualizado com estas permissões

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

## <a name="set-adsyncexchangehybridpermissions"></a>Set-ADSyncExchangeHybridPermissions

### <a name="synopsis"></a>SINOPSE
Inicialize a sua floresta de Diretório Ativo e domínio para a funcionalidade Exchange Hybrid.

### <a name="syntax"></a>SINTAXE

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>Nome distinto
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [-SkipAdminSdHolders]
 [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>DESCRIÇÃO
A Função Set-ADSyncExchangeHybridPermissions dará permissões necessárias à conta de sincronização de AD, que incluem o seguinte:
1.
Ler/Escrever Acesso de propriedade em todos os atributos para todos os objetos de utilizador descendentes
2.
Ler/Escrever Acesso de propriedade em todos os atributos para todos os objetos inetorgperson descendentes
3.
Ler/Escrever Acesso de propriedade em todos os atributos para todos os objetos de grupo descendentes
4.
Ler/Escrever Acesso de propriedade em todos os atributos para todos os objetos de contacto descendentes

Estas permissões são aplicadas a todos os domínios da floresta.
Opcionalmente, pode fornecer um Nome Distinto no parâmetro ADobjectDN para definir estas permissões apenas nesse Objeto AD (incluindo herança a sub objetos).

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
O Nome da conta Ative Directory que é ou será utilizado pelo Azure AD Connect Sync para gerir objetos no diretório.

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
O Domínio da conta Ative Directory que é ou será utilizado pelo Azure AD Connect Sync para gerir objetos no diretório.

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

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountdn
O Nome Distinto da conta Ative Directory que é ou será utilizado pela Azure AD Connect Sync para gerir objetos no diretório.

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
Nome distinto do objeto AD alvo para definir permissões (opcional)

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
Parâmetro opcional para indicar se o contentor AdminSDHolder não deve ser atualizado com estas permissões

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

## <a name="set-adsyncexchangemailpublicfolderpermissions"></a>Set-ADSyncExchangeMailPublicFolderPermissions

### <a name="synopsis"></a>SINOPSE
Inicialize a sua floresta de Diretório Ativo e domínio para a funcionalidade Pasta Pública de Câmbio.

### <a name="syntax"></a>SINTAXE

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountName <String>
 -ADConnectorAccountDomain <String> [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm]
 [<CommonParameters>]
```

#### <a name="distinguishedname"></a>Nome distinto
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>]
 [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>DESCRIÇÃO
A Função Set-ADSyncExchangeMailPublicFolderPermissions dará permissões necessárias à conta de sincronização de AD, que incluem o seguinte:
1.
Leia o acesso à propriedade em todos os atributos para todos os objetos de dobradores públicos descendentes

Estas permissões são aplicadas a todos os domínios da floresta.
Opcionalmente, pode fornecer um Nome Distinto no parâmetro ADobjectDN para definir estas permissões apenas nesse Objeto AD (incluindo herança a sub objetos).

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
O Nome da conta Ative Directory que é ou será utilizado pelo Azure AD Connect Sync para gerir objetos no diretório.

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
O Domínio da conta Ative Directory que é ou será utilizado pelo Azure AD Connect Sync para gerir objetos no diretório.

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

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountdn
O Nome Distinto da conta Ative Directory que é ou será utilizado pela Azure AD Connect Sync para gerir objetos no diretório.

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
Nome distinto do objeto AD alvo para definir permissões (opcional)

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
Parâmetro opcional para indicar se o contentor AdminSDHolder não deve ser atualizado com estas permissões

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

## <a name="set-adsyncmsdsconsistencyguidpermissions"></a>Set-ADSyncMsDsConsistencyGuidPermissions

### <a name="synopsis"></a>SINOPSE
Inicialize a floresta e o domínio do Diretório Ativo para a função mS-DS-ConsistencyGuid.

### <a name="syntax"></a>SINTAXE

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>Nome distinto
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>]
 [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>DESCRIÇÃO
A Função Set-ADSyncMsDsConsistencyGuidPermissions dará permissões necessárias à conta de sincronização de AD, que incluem o seguinte:
1.
Ler/escrever Acesso de propriedade no atributo mS-DS-ConsistênciaGuid para todos os objetos de utilizador descendentes

Estas permissões são aplicadas a todos os domínios da floresta.
Opcionalmente, pode fornecer um Nome Distinto no parâmetro ADobjectDN para definir estas permissões apenas nesse Objeto AD (incluindo herança a sub objetos).

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
O Nome da conta Ative Directory que é ou será utilizado pelo Azure AD Connect Sync para gerir objetos no diretório.

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
O Domínio da conta Ative Directory que é ou será utilizado pelo Azure AD Connect Sync para gerir objetos no diretório.

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

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountdn
O Nome Distinto da conta Ative Directory que é ou será utilizado pela Azure AD Connect Sync para gerir objetos no diretório.

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
Nome distinto do objeto AD alvo para definir permissões (opcional)

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
Parâmetro opcional para indicar se o contentor AdminSDHolder não deve ser atualizado com estas permissões

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

## <a name="set-adsyncpasswordhashsyncpermissions"></a>Set-ADSyncPasswordHashSyncPermissions

### <a name="synopsis"></a>SINOPSE
Inicialize a floresta e o domínio do Diretório Ativo para sincronização de hash de palavra-passe.

### <a name="syntax"></a>SINTAXE

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>Nome distinto
```
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN <String> [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>DESCRIÇÃO
A Função Set-ADSyncPasswordHashSyncPermissions dará permissões necessárias à conta de sincronização de AD, que incluem o seguinte:
1.
Alterações de Diretório de Replicação
2.
Replicando mudanças de diretório tudo

Estas permissões são dadas a todos os domínios da floresta.

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
O Nome da conta Ative Directory que será utilizada pelo Azure AD Connect Sync para gerir objetos no diretório.

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
O Domínio da conta Ative Directory que será utilizado pelo Azure AD Connect Sync para gerir objetos no diretório.

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

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountdn
O Nome Distinto da conta Ative Directory que será utilizado pelo Azure AD Connect Sync para gerir objetos no diretório.

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

## <a name="set-adsyncpasswordwritebackpermissions"></a>Set-ADSyncPasswordWritebackPermissions

### <a name="synopsis"></a>SINOPSE
Inicialize a floresta e o domínio do Ative Directory para a gravação de passwords do Azure AD.

### <a name="syntax"></a>SINTAXE

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>Nome distinto
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>]
 [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>DESCRIÇÃO
A Função Set-ADSyncPasswordWritebackPermissions dará permissões necessárias à conta de sincronização de AD, que incluem o seguinte:
1.
Redefinir palavra-passe em objetos de utilizador descendentes
2.
Escreva o acesso da propriedade no atributo lockoutTime para todos os objetos de utilizador descendentes
3.
Escreva o acesso à propriedade no atributo pwdLastSet para todos os objetos de utilizador descendentes

Estas permissões são aplicadas a todos os domínios da floresta.
Opcionalmente, pode fornecer um Nome Distinto no parâmetro ADobjectDN para definir estas permissões apenas nesse Objeto AD (incluindo herança a sub objetos).

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
O Nome da conta Ative Directory que é ou será utilizado pelo Azure AD Connect Sync para gerir objetos no diretório.

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
O Domínio da conta Ative Directory que é ou será utilizado pelo Azure AD Connect Sync para gerir objetos no diretório.

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

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountdn
O Nome Distinto da conta Ative Directory que é ou será utilizado pela Azure AD Connect Sync para gerir objetos no diretório.

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
Nome distinto do objeto AD alvo para definir permissões (opcional)

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
Parâmetro opcional para indicar se o contentor AdminSDHolder não deve ser atualizado com estas permissões

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

## <a name="set-adsyncrestrictedpermissions"></a>Set-ADSyncRestrictedPermissions

### <a name="synopsis"></a>SINOPSE
Aperte as permissões num objeto AD que não esteja incluído em nenhum grupo de segurança protegido por AD.
Um exemplo típico é a conta AD Connect (MSOL) criada automaticamente pelo AAD Connect.
Esta conta tem permissões replicadas em todos os domínios, no entanto pode ser facilmente comprometida uma vez que não está protegida.

### <a name="syntax"></a>SINTAXE

```
Set-ADSyncRestrictedPermissions [-ADConnectorAccountDN] <String> [-Credential] <PSCredential>
 [-DisableCredentialValidation] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>DESCRIÇÃO
A Função Set-ADSyncRestrictedPermissions irá apertar as permissões oo a conta fornecida.
As permissões de aperto envolvem os seguintes passos:
1. Desativar a herança no objeto especificado
2. Remova todos os ACEs no objeto específico, exceto ACEs específicos de SELF.
Queremos manter as permissões por defeito intactas quando se trata de SELF.
3. Atribua estas permissões específicas:

   | Tipo | Name | Access | Aplica-se A |
   |------|------|--------|------------|
   | Permitir | SISTEMA | Controlo Total | Este objeto |
   | Permitir | Administradores da Empresa | Controlo Total | Este objeto |
   | Permitir | Administradores do Domínio | Controlo Total | Este objeto | 
   | Permitir | Administradores | Controlo Total | Este objeto |
   | Permitir | Controladores de domínio da empresa | Conteúdo da Lista <br> Ler todas as propriedades <br> Ler Permissões | Este objeto |
   | Permitir | Utilizadores Autenticados | Conteúdo da Lista <br> Ler todas as propriedades <br> Ler Permissões | Este objeto |

### <a name="examples"></a>EXEMPLOS

#### <a name="example-1"></a>EXEMPLO 1
```
Set-ADSyncRestrictedPermissions -ADConnectorAccountDN "CN=TestAccount1,CN=Users,DC=Contoso,DC=com" -Credential $(Get-Credential)
```

### <a name="parameters"></a>PARÂMETROS

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountdn
Nome distinto da conta Ative Directory cujas permissões precisam de ser reforçadas.
Esta é tipicamente a conta MSOL_nnnnnnnnnn ou uma conta de domínio personalizada que está configurada no seu Conector AD.

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
Credencial de administrador que tem os privilégios necessários para restringir as permissões na conta ADConnectorAccountDN. Este é tipicamente o administrador da Enterprise ou do Domínio. Utilize o nome de domínio totalmente qualificado da conta do administrador para evitar falhas de procura de conta.
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

#### <a name="-disablecredentialvalidation"></a>-DeficientesCredentialValidation
Quando a Desativação DoValidation for utilizada, a função não verificará se as credenciais fornecidas em -Credencial são válidas em AD e se a conta fornecida tem os privilégios necessários para restringir as permissões na conta ADConnectorAccountDN.

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

## <a name="set-adsyncunifiedgroupwritebackpermissions"></a>Set-ADSyncUnifiedGroupWritebackPermissions

### <a name="synopsis"></a>SINOPSE
Inicialize a sua floresta de Diretório Ativo e domínio para a gravação do Grupo a partir do Azure AD.

### <a name="syntax"></a>SINTAXE

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>Nome distinto
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>]
 [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>DESCRIÇÃO
A Função Set-ADSyncUnifiedGroupWritebackPermissions dará permissões necessárias à conta de sincronização de AD, que incluem o seguinte:
1.
Leitura/Escrita Genérica, Apagar, Eliminar Árvore e Criar\Eliminar Criança para todos os tipos de objetos de grupo e subObjectos

Estas permissões são aplicadas a todos os domínios da floresta.
Opcionalmente, pode fornecer um Nome Distinto no parâmetro ADobjectDN para definir estas permissões apenas nesse Objeto AD (incluindo herança a sub objetos).
Neste caso, a ADobjectDN será o Nome Distinto do Recipiente que pretende ligar com a função GroupWriteback.

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
O Nome da conta Ative Directory que é ou será utilizado pelo Azure AD Connect Sync para gerir objetos no diretório.

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
O Domínio da conta Ative Directory que é ou será utilizado pelo Azure AD Connect Sync para gerir objetos no diretório.

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

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountdn
O Nome Distinto da conta Ative Directory que é ou será utilizado pela Azure AD Connect Sync para gerir objetos no diretório.

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
Nome distinto do objeto AD alvo para definir permissões (opcional)

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
Parâmetro opcional para indicar se o contentor AdminSDHolder não deve ser atualizado com estas permissões

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

## <a name="show-adsyncadobjectpermissions"></a>Show-ADSyncADObjectPermissions

### <a name="synopsis"></a>SINOPSE
Mostra permissões de um objeto AD especificado.

### <a name="syntax"></a>SINTAXE

```
Show-ADSyncADObjectPermissions [-ADobjectDN] <String> [<CommonParameters>]
```

### <a name="description"></a>DESCRIÇÃO
Esta função devolve todas as permissões de AD atualmente definidas para um determinado objeto AD fornecido no parâmetro -ADobjectDN.
O ADobjectDN deve ser fornecido num formato Distinto Nome.

### <a name="examples"></a>EXEMPLOS

#### <a name="example-1"></a>EXEMPLO 1
```
Show-ADSyncADObjectPermissions -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARÂMETROS

#### <a name="-adobjectdn"></a>-ADobjectDN
{{Preencha a descrição de ADobjectdN}}

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
Para mais informações, consulte about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) about_CommonParameters.
