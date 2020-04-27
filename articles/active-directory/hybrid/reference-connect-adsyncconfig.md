---
title: 'Azure AD Connect: Referência ADSyncConfig PowerShell [ Microsoft Docs'
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
ms.openlocfilehash: 554bb99121190198982f64deb6ee0674aa8831ed
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/26/2020
ms.locfileid: "60381200"
---
# <a name="azure-ad-connect--adsyncconfig-powershell-reference"></a>Ligação Azure AD: Referência aDSyncConfig PowerShell
A seguinte documentação fornece informações de referência para o Módulo PowerShell ADSyncConfig.psm1 que está incluído com Azure AD Connect.


## <a name="get-adsyncadconnectoraccount"></a>Get-ADSyncADConnectorAccount

### <a name="synopsis"></a>SINOPSE
Obtém o nome da conta e domínio que é configurado em cada Conector AD

### <a name="syntax"></a>SINTAXE

```
Get-ADSyncADConnectorAccount
```

### <a name="description"></a>DESCRIÇÃO
Esta função utiliza o cmdlet 'Get-ADSyncConnector' que está presente no AAD Connect para recuperar dos Parâmetros de Conectividade uma tabela que mostra a conta do Conector AD.

### <a name="examples"></a>EXEMPLOS

#### <a name="example-1"></a>EXEMPLO 1
```
Get-ADSyncADConnectorAccount
```

## <a name="get-adsyncobjectswithinheritancedisabled"></a>Get-ADSyncObjects WithHerherDisabled

### <a name="synopsis"></a>SINOPSE
Obtém objetos aD com permissão de herança desativada

### <a name="syntax"></a>SINTAXE

```
Get-ADSyncObjectsWithInheritanceDisabled [-SearchBase] <String> [[-ObjectClass] <String>] [<CommonParameters>]
```

### <a name="description"></a>DESCRIÇÃO
Pesquisas em AD a partir do parâmetro SearchBase e devolvem todos os objetos, filtrados pelo parâmetro ObjectClass, que têm a Herança ACL atualmente desativada.

### <a name="examples"></a>EXEMPLOS

#### <a name="example-1"></a>EXEMPLO 1
```
Find objects with disabled inheritance in 'Contoso' domain (by default returns 'organizationalUnit' objects only)
```

Get-ADSyncObjects WithHerherDisabled -SearchBase 'Contoso'

#### <a name="example-2"></a>EXEMPLO 2
```
Find 'user' objects with disabled inheritance in 'Contoso' domain
```

Get-ADSyncObjectsWithHerDisabled -SearchBase 'Contoso' -ObjectClass 'user'

#### <a name="example-3"></a>EXEMPLO 3
```
Find all types of objects with disabled inheritance in a OU
```

Get-ADSyncObjectsWithHerDisabled -SearchBase OU=AzureAD,DC=Contoso,DC=com -ObjectClass '*'

### <a name="parameters"></a>PARÂMETROS

#### <a name="-searchbase"></a>-SearchBase
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

#### <a name="-objectclass"></a>-ObjectClass
A classe dos objetos a procurar que possa ser '*' (para qualquer classe de objetos), 'utilizador', 'grupo', 'contentor', etc. Por predefinição, esta função procurará a classe de objetos 'organizationalUnit'.

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
Para mais informações,https://go.microsoft.com/fwlink/?LinkID=113216)consulte about_CommonParameters .

## <a name="set-adsyncbasicreadpermissions"></a>Set-ADSyncBasicReadPermissions

### <a name="synopsis"></a>SINOPSE
Inicialize a sua floresta de Diretório Ativo e domínio para permissões de leitura básicas.

### <a name="syntax"></a>SINTAXE

#### <a name="userdomain"></a>Domínio do utilizador
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
A Função Set-ADSyncBasicReadPermissions dará permissões necessárias à conta de sincronização ad, que incluem o seguinte:
1.
Leia o acesso à propriedade em todos os atributos para todos os objetos de computador descendentes
2.
Leia o acesso à propriedade em todos os atributos para todos os objetos de dispositivodescendentes
3.
Leia o acesso à propriedade em todos os atributos para todos os objetos de segurança estrangeira descendentes
5.
Leia o acesso à propriedade em todos os atributos para todos os objetos de utilizador descendentes
6.
Leia o acesso à propriedade em todos os atributos para todos os objetos descendentes inetorgperson
7.
Leia o acesso à propriedade em todos os atributos para todos os objetos de grupo descendentes
8.
Leia o acesso à propriedade em todos os atributos para todos os objetos de contacto descendentes

Estas permissões são aplicadas a todos os domínios da floresta.
Opcionalmente, pode fornecer um Nome Distinto no parâmetro ADobjectDN para definir estas permissões apenas nesse Objeto AD (incluindo herança a sub-objectos).

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
O Nome da conta De Diretório Ativo que é ou será utilizado pelo Azure AD Connect Sync para gerir objetos no diretório.

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
O Domínio da conta De Diretório Ativo que é ou será utilizado pelo Azure AD Connect Sync para gerir objetos no diretório.

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
O Nome Distinto da conta de Diretório Ativo que é ou será utilizado pelo Azure AD Connect Sync para gerir objetos no diretório.

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
Nome distinto do objeto ad alvo para definir permissões (opcional)

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
Parâmetro opcional para indicar se o recipiente AdminSDHolder não deve ser atualizado com estas permissões

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

## <a name="set-adsyncexchangehybridpermissions"></a>Set-ADSyncExchangeHybridAutorizas

### <a name="synopsis"></a>SINOPSE
Inicialize a sua floresta de Diretório Ativo e domínio para a funcionalidade Exchange Hybrid.

### <a name="syntax"></a>SINTAXE

#### <a name="userdomain"></a>Domínio do utilizador
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
A Função Set-ADSyncExchangeHybridPermissions dará permissões necessárias à conta de sincronização ad, que incluem o seguinte:
1.
Ler/Escrever Acesso de propriedade em todos os atributos para todos os objetos de utilizador descendentes
2.
Ler/Escrever Acesso de propriedade em todos os atributos para todos os objetos descendentes inetorgperson
3.
Ler/Escrever Acesso de propriedade em todos os atributos para todos os objetos de grupo descendente
4.
Ler/Escrever Acesso de propriedade em todos os atributos para todos os objetos de contacto descendentes

Estas permissões são aplicadas a todos os domínios da floresta.
Opcionalmente, pode fornecer um Nome Distinto no parâmetro ADobjectDN para definir estas permissões apenas nesse Objeto AD (incluindo herança a sub-objectos).

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
O Nome da conta De Diretório Ativo que é ou será utilizado pelo Azure AD Connect Sync para gerir objetos no diretório.

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
O Domínio da conta De Diretório Ativo que é ou será utilizado pelo Azure AD Connect Sync para gerir objetos no diretório.

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
O Nome Distinto da conta de Diretório Ativo que é ou será utilizado pelo Azure AD Connect Sync para gerir objetos no diretório.

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
Nome distinto do objeto ad alvo para definir permissões (opcional)

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
Parâmetro opcional para indicar se o recipiente AdminSDHolder não deve ser atualizado com estas permissões

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

## <a name="set-adsyncexchangemailpublicfolderpermissions"></a>Set-ADSyncExchangeMailPublicFolderPermissions

### <a name="synopsis"></a>SINOPSE
Inicialize a sua floresta de Diretório Ativo e domínio para a funcionalidade de pastas públicas exchange mail.

### <a name="syntax"></a>SINTAXE

#### <a name="userdomain"></a>Domínio do utilizador
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
A Função de Permissões de Permissões de Definição ADSyncExchangeMailPublicFolder irá dar permissões necessárias à conta de sincronização ad, que incluem o seguinte:
1.
Leia o acesso à propriedade em todos os atributos para todos os objetos de pasta pública descendente

Estas permissões são aplicadas a todos os domínios da floresta.
Opcionalmente, pode fornecer um Nome Distinto no parâmetro ADobjectDN para definir estas permissões apenas nesse Objeto AD (incluindo herança a sub-objectos).

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
O Nome da conta De Diretório Ativo que é ou será utilizado pelo Azure AD Connect Sync para gerir objetos no diretório.

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
O Domínio da conta De Diretório Ativo que é ou será utilizado pelo Azure AD Connect Sync para gerir objetos no diretório.

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
O Nome Distinto da conta de Diretório Ativo que é ou será utilizado pelo Azure AD Connect Sync para gerir objetos no diretório.

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
Nome distinto do objeto ad alvo para definir permissões (opcional)

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
Parâmetro opcional para indicar se o recipiente AdminSDHolder não deve ser atualizado com estas permissões

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

## <a name="set-adsyncmsdsconsistencyguidpermissions"></a>Set-ADSyncMsDsConsistênciaOrientapers

### <a name="synopsis"></a>SINOPSE
Inicialize a sua floresta e domínio de Diretório Ativo para a função mS-DS-Consistência.

### <a name="syntax"></a>SINTAXE

#### <a name="userdomain"></a>Domínio do utilizador
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
A função set-adsyncMsDsConsistênciaGuidPermissions dará permissões necessárias à conta de sincronização AD, que incluem o seguinte:
1.
Ler/Escrever Acesso à propriedade em mS-DS-ConsistênciaAtributo para todos os objetos de utilizador descendentes

Estas permissões são aplicadas a todos os domínios da floresta.
Opcionalmente, pode fornecer um Nome Distinto no parâmetro ADobjectDN para definir estas permissões apenas nesse Objeto AD (incluindo herança a sub-objectos).

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
O Nome da conta De Diretório Ativo que é ou será utilizado pelo Azure AD Connect Sync para gerir objetos no diretório.

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
O Domínio da conta De Diretório Ativo que é ou será utilizado pelo Azure AD Connect Sync para gerir objetos no diretório.

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
O Nome Distinto da conta de Diretório Ativo que é ou será utilizado pelo Azure AD Connect Sync para gerir objetos no diretório.

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
Nome distinto do objeto ad alvo para definir permissões (opcional)

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
Parâmetro opcional para indicar se o recipiente AdminSDHolder não deve ser atualizado com estas permissões

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

## <a name="set-adsyncpasswordhashsyncpermissions"></a>Set-ADSyncPasswordHashSyncPermissions

### <a name="synopsis"></a>SINOPSE
Inicialize a sua floresta de Diretório Ativo e domínio para sincronização de hash de palavra-passe.

### <a name="syntax"></a>SINTAXE

#### <a name="userdomain"></a>Domínio do utilizador
```
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>Nome distinto
```
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN <String> [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>DESCRIÇÃO
A função Set-ADSyncPasswordHashSyncPermissions dará permissões necessárias à conta de sincronização ad, que incluem o seguinte:
1.
Replicando alterações de diretório
2.
Replicando mudanças de diretório todos

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
O Nome da conta Ative Diretório que será utilizada pelo Azure AD Connect Sync para gerir objetos no diretório.

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
O Domínio da conta De Diretório Ativo que será utilizado pelo Azure AD Connect Sync para gerir objetos no diretório.

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
O Nome Distinto da conta de Diretório Ativo que será utilizado pelo Azure AD Connect Sync para gerir objetos no diretório.

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

## <a name="set-adsyncpasswordwritebackpermissions"></a>Set-ADSyncPasswordWritebackPermissions

### <a name="synopsis"></a>SINOPSE
Inicialize a sua floresta de Diretório Ativo e domínio para a devolução de palavras-passe a partir de Azure AD.

### <a name="syntax"></a>SINTAXE

#### <a name="userdomain"></a>Domínio do utilizador
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
A função set-ADSyncPasswordWritebackPermissions dará permissões necessárias à conta de sincronização ad, que incluem o seguinte:
1.
Redefinir palavra-passe em objetos de utilizador descendentes
2.
Escreva acesso à propriedade no lockoutA atributo para todos os objetos de utilizador descendentes
3.
Escreva acesso à propriedade no atributo pwdLastSet para todos os objetos de utilizador descendentes

Estas permissões são aplicadas a todos os domínios da floresta.
Opcionalmente, pode fornecer um Nome Distinto no parâmetro ADobjectDN para definir estas permissões apenas nesse Objeto AD (incluindo herança a sub-objectos).

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
O Nome da conta De Diretório Ativo que é ou será utilizado pelo Azure AD Connect Sync para gerir objetos no diretório.

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
O Domínio da conta De Diretório Ativo que é ou será utilizado pelo Azure AD Connect Sync para gerir objetos no diretório.

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
O Nome Distinto da conta de Diretório Ativo que é ou será utilizado pelo Azure AD Connect Sync para gerir objetos no diretório.

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
Nome distinto do objeto ad alvo para definir permissões (opcional)

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
Parâmetro opcional para indicar se o recipiente AdminSDHolder não deve ser atualizado com estas permissões

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

## <a name="set-adsyncrestrictedpermissions"></a>Permissões restritas de set-ADSyncRestricted

### <a name="synopsis"></a>SINOPSE
Aperte as permissões num objeto ad que não esteja incluído em qualquer grupo de segurança protegido pela AD.
Um exemplo típico é a conta AD Connect (MSOL) criada automaticamente pela AAD Connect.
Esta conta tem permissões replicadas em todos os domínios, no entanto pode ser facilmente comprometida por não estar protegida.

### <a name="syntax"></a>SINTAXE

```
Set-ADSyncRestrictedPermissions [-ADConnectorAccountDN] <String> [-Credential] <PSCredential>
 [-DisableCredentialValidation] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>DESCRIÇÃO
A Função de Permissões Configuradas ADSyncRestricted Permissions irá apertar as permissões oo a conta fornecida.
Permissões de aperto envolvem os seguintes passos:
1.
Desativar a herança no objeto especificado
2.
Remova todos os ACEs no objeto específico, exceto ACEs específicos de SELF.
Queremos manter intactas as permissões por defeito no que diz respeito a SELF.
3.
Atribuir estas permissões específicas:

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
Nome distinto da conta Diretório Ativo cujas permissões precisam de ser apertadas.
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
Credencial de administrador que tenha os privilégios necessários para restringir as permissões na conta ADConnectorAccountDN. Este é tipicamente o administrador da Enterprise ou do Domínio. Utilize o nome de domínio totalmente qualificado da conta do administrador para evitar falhas de procura de conta.
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

#### <a name="-disablecredentialvalidation"></a>-DesactivaçãoDaValidação Credential
Quando a Validação de Deficientes É utilizada, a função não verificará se as credenciais fornecidas em -Credenciais são válidas em AD e se a conta fornecida tem os privilégios necessários para restringir as permissões na conta ADConnectorAccountDN.

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

## <a name="set-adsyncunifiedgroupwritebackpermissions"></a>Set-ADSyncUnifiedGroupWritebackPermissions

### <a name="synopsis"></a>SINOPSE
Inicialize a sua floresta de Diretório Ativo e domínio para a reprodução do Grupo a partir de Azure AD.

### <a name="syntax"></a>SINTAXE

#### <a name="userdomain"></a>Domínio do utilizador
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
A Função de Definição ADSyncUnifiedGroupWritebackPermissions dará permissões necessárias à conta de sincronização AD, que incluem o seguinte:
1.
Leitura/Escrita genérica, excluir, excluir árvore e criar\Excluir criança para todos os tipos de objetos de grupo e subobjetos

Estas permissões são aplicadas a todos os domínios da floresta.
Opcionalmente, pode fornecer um Nome Distinto no parâmetro ADobjectDN para definir estas permissões apenas nesse Objeto AD (incluindo herança a sub-objectos).
Neste caso, a ADobjectDN será o Nome Distinto do Recipiente que pretende ligar à funcionalidade GroupWriteback.

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
O Nome da conta De Diretório Ativo que é ou será utilizado pelo Azure AD Connect Sync para gerir objetos no diretório.

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
O Domínio da conta De Diretório Ativo que é ou será utilizado pelo Azure AD Connect Sync para gerir objetos no diretório.

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
O Nome Distinto da conta de Diretório Ativo que é ou será utilizado pelo Azure AD Connect Sync para gerir objetos no diretório.

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
Nome distinto do objeto ad alvo para definir permissões (opcional)

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
Parâmetro opcional para indicar se o recipiente AdminSDHolder não deve ser atualizado com estas permissões

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

## <a name="show-adsyncadobjectpermissions"></a>Mostra-ADSyncADObjectPermissions

### <a name="synopsis"></a>SINOPSE
Mostra permissões de um objeto ad.

### <a name="syntax"></a>SINTAXE

```
Show-ADSyncADObjectPermissions [-ADobjectDN] <String> [<CommonParameters>]
```

### <a name="description"></a>DESCRIÇÃO
Esta função devolve todas as permissões AD atualmente definidas para um determinado objeto AD fornecido no parâmetro -ADobjectDN.
O ADobjectDN deve ser fornecido num formato DistinguishedName.

### <a name="examples"></a>EXEMPLOS

#### <a name="example-1"></a>EXEMPLO 1
```
Show-ADSyncADObjectPermissions -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARÂMETROS

#### <a name="-adobjectdn"></a>-ADobjectDN
{{Preencher Descrição ADobjectDN}}

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
Para mais informações,https://go.microsoft.com/fwlink/?LinkID=113216)consulte about_CommonParameters .
