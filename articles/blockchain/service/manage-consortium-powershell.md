---
title: Gerir membros do consórcio Azure Blockchain Service - PowerShell
description: Saiba como gerir os membros do consórcio Azure Blockchain Service utilizando a Azure PowerShell.
ms.date: 10/14/2019
ms.topic: how-to
ms.reviewer: zeyadr
ms.openlocfilehash: d40e55f177bda9edb40383b6e2c61c32633cd005
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "85211345"
---
# <a name="manage-consortium-members-in-azure-blockchain-service-using-powershell"></a>Gerir membros do consórcio no Azure Blockchain Service usando o PowerShell

Pode utilizar o PowerShell para gerir membros do consórcio blockchain para o seu Serviço Azure Blockchain. Os membros que têm privilégios de administrador podem convidar, adicionar, remover e alterar papéis para todos os participantes no consórcio blockchain. Os membros que tenham privilégios de utilizador podem ver todos os participantes no consórcio blockchain e alterar o nome de exibição dos seus membros.

## <a name="prerequisites"></a>Pré-requisitos

* Crie um membro blockchain utilizando o [portal Azure](create-member.md).
* Para obter mais informações sobre consórcios, membros e nós, consulte o [consórcio do Serviço Azure Blockchain](consortium.md).

## <a name="open-azure-cloud-shell"></a>Abrir o Azure Cloud Shell

Azure Cloud Shell é uma concha interativa gratuita que pode usar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta.

Também pode abrir o Cloud Shell num separador de navegador, indo para [shell.azure.com/powershell](https://shell.azure.com/powershell). Selecione **Copy** para copiar os blocos de código, cole-o em Cloud Shell e selecione **Enter** para executá-lo.

## <a name="install-the-powershell-module"></a>Instalar o módulo do PowerShell

Instale o pacote Microsoft.AzureBlockchainService.ConsortiumManagement.PS da Galeria PowerShell.

```powershell-interactive
Install-Module -Name Microsoft.AzureBlockchainService.ConsortiumManagement.PS -Scope CurrentUser
Import-Module Microsoft.AzureBlockchainService.ConsortiumManagement.PS
```

## <a name="set-the-information-preference"></a>Definir a preferência de informação

Pode obter mais informações ao executar os cmdlets definindo a variável de preferência de informação. Por predefinição, *$InformationPreference* está definido para *SilentlyContinue*.

Para obter mais informações verbose de cmdlets, deslote a preferência no PowerShell da seguinte forma:

```powershell-interactive
$InformationPreference = 'Continue'
```

## <a name="establish-a-web3-connection"></a>Estabelecer uma ligação Web3

Para gerir os membros do consórcio, estabeleça uma ligação Web3 ao seu ponto final de membro do Serviço Blockchain. Você pode usar este script para definir variáveis globais para chamar os cmdlets de gestão do consórcio.

```powershell-interactive
$Connection = New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
$MemberAccount = Import-Web3Account -ManagedAccountAddress '<Member account address>' -ManagedAccountPassword '<Member account password>'
$ContractConnection = Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>' -Web3Client $Connection
```

Substitua *\<Member account password\>* a palavra-passe da conta de membro que utilizou quando criou o membro.

Encontre os outros valores no portal Azure:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Aceda à página **geral** do membro do Serviço Blockchain padrão.

    ![Visão geral do membro](./media/manage-consortium-powershell/member-overview.png)

    Substitua *\<Member account\>* e *\<RootContract address\>* pelos valores do portal.

1. Para o endereço ponto final, selecione **os nós de transação** e, em seguida, selecione o **nó de transação padrão**. O nó padrão tem o mesmo nome que o membro blockchain.
1. Selecione **as cordas de ligação**.

    ![Cadeias de ligação](./media/manage-consortium-powershell/connection-strings.png)

    *\<Endpoint address\>* Substitua-o pelo valor de **HTTPS (Tecla de acesso 1)** ou **HTTPS (Tecla de acesso 2)**.

## <a name="manage-the-network-and-smart-contracts"></a>Gerir a rede e contratos inteligentes

Utilize a rede e os cmdlets de contrato inteligentes para estabelecer uma ligação com os contratos inteligentes do ponto final da blockchain responsáveis pela gestão do consórcio.

### <a name="import-consortiummanagementcontracts"></a>Import-ConsortiumManagementContracts

Utilize este cmdlet para ligar aos contratos inteligentes da administração do consórcio. Estes contratos são utilizados para gerir e impor membros dentro do consórcio.

`Import-ConsortiumManagementContracts -RootContractAddress <String> -Web3Client <IClient>`

| Parâmetro | Descrição | Obrigatório |
|-----------|-------------|:--------:|
| RootContractAddress | Endereço de contrato de raiz dos contratos inteligentes de gestão do consórcio | Yes |
| Web3Client | Objeto Web3Client obtido a partir de New-Web3Connection | Yes |

#### <a name="example"></a>Exemplo

```powershell-interactive
Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>'  -Web3Client $Connection
```

### <a name="import-web3account"></a>Import-Web3Account

Utilize este cmdlet para criar um objeto para reter as informações para a conta de gestão de um nó remoto.

`Import-Web3Account -ManagedAccountAddress <String> -ManagedAccountPassword <String>`

| Parâmetro | Descrição | Obrigatório |
|-----------|-------------|:--------:|
| ManagedAccountAddress | Endereço de conta do membro blockchain | Yes |
| ManagedAccountPassword | Senha de endereço de conta | Yes |

#### <a name="example"></a>Exemplo

```powershell-interactive
Import-Web3Account -ManagedAccountAddress '<Member account address>'  -ManagedAccountPassword '<Member account password>'
```

### <a name="new-web3connection"></a>New-Web3Connection

Utilize este cmdlet para estabelecer uma ligação ao ponto final do RPC de um nó de transação.

`New-Web3Connection [-RemoteRPCEndpoint <String>]`

| Parâmetro | Descrição | Obrigatório |
|-----------|-------------|:--------:|
| Ponto RemotoRPCEndpoint | Endereço de ponto final do membro blockchain | Yes |

#### <a name="example"></a>Exemplo

```powershell-interactive
New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
```

## <a name="manage-the-consortium-members"></a>Gerir os membros do consórcio

Utilize cmdlets de gestão de membros do consórcio para gerir membros dentro do consórcio. As ações disponíveis dependem do seu papel de consórcio.

### <a name="get-blockchainmember"></a>Get-BlockchainMember

Utilize este cmdlet para obter detalhes dos membros ou membros da lista do consórcio.

`Get-BlockchainMember [[-Name] <String>] -Members <IContract> -Web3Client <IClient>`

| Parâmetro | Descrição | Obrigatório |
|-----------|-------------|:--------:|
| Name | O nome do membro do Serviço Blockchain sobre o quais pretende obter detalhes. Quando um nome é introduzido, devolve os detalhes do membro. Quando um nome é omitido, devolve uma lista de todos os membros do consórcio. | No |
| Membros | Objeto de membros obtido a partir de Import-ConsortiumManagementContracts | Yes |
| Web3Client | Objeto Web3Client obtido a partir de New-Web3Connection | Yes |

#### <a name="example"></a>Exemplo

[Estabeleça uma ligação Web3](#establish-a-web3-connection) para definir a variável $ContractConnection.

```powershell-interactive
$ContractConnection | Get-BlockchainMember -Name <Member Name>
```

#### <a name="example-output"></a>Saída de exemplo

```
Name           : myblockchainmember
CorrelationId  : 0
DisplayName    : myCompany
SubscriptionId : <Azure subscription ID>
AccountAddress : 0x85b911c9e103d6405573151258d668479e9ebeef
Role           : ADMIN
```

### <a name="remove-blockchainmember"></a>Remove-BlockchainMember

Utilize este cmdlet para remover um membro da blockchain.

`Remove-BlockchainMember -Name <String> -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>`

| Parâmetro | Descrição | Obrigatório |
|-----------|-------------|:--------:|
| Name | Nome do membro para remover | Yes |
| Membros | Objeto de membros obtido a partir de Import-ConsortiumManagementContracts | Yes |
| Web3A contagem de votos | Objeto Web3A contagem obtida a partir de Import-Web3Account | Yes |
| Web3Client | Objeto Web3Client obtido a partir de New-Web3Connection | Yes |

#### <a name="example"></a>Exemplo

[Estabeleça uma ligação Web3](#establish-a-web3-connection) para definir as variáveis $ContractConnection e $MemberAccount.

```powershell-interactive
$ContractConnection | Remove-BlockchainMember -Name <Member Name> -Web3Account $MemberAccount
```

### <a name="set-blockchainmember"></a>Set-BlockchainMember

Utilize este cmdlet para definir os atributos dos membros blockchain, incluindo o nome de exibição e o papel do consórcio.

Os administradores do consórcio podem definir **DisplayName** e **Role** para todos os membros. Um membro do consórcio com a função de utilizador pode alterar apenas o nome de exibição do seu próprio membro.

```
Set-BlockchainMember -Name <String> [-DisplayName <String>] [-AccountAddress <String>] [-Role <String>]
 -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>
```

| Parâmetro | Descrição | Obrigatório |
|-----------|-------------|:--------:|
| Name | Nome do membro blockchain | Yes |
| DisplayName | Novo nome de exibição | No |
| ContaAddress | Endereço da conta | No |
| Membros | Objeto de membros obtido a partir de Import-ConsortiumManagementContracts | Yes |
| Web3A contagem de votos | Objeto Web3A contagem obtida a partir de Import-Web3Account | Yes |
| Web3Client |  Objeto Web3Client obtido a partir de New-Web3Connection| Yes |

#### <a name="example"></a>Exemplo

[Estabeleça uma ligação Web3](#establish-a-web3-connection) para definir as variáveis $ContractConnection e $MemberAccount.

```powershell-interactive
$ContractConnection | Set-BlockchainMember -Name <Member Name> -DisplayName <Display name> -Web3Account $MemberAccount
```

## <a name="manage-the-consortium-members-invitations"></a>Gerir os convites dos membros do consórcio

Utilize os cmdlets de gestão de convites do membro do consórcio para gerir os convites dos membros do consórcio. As ações disponíveis dependem do seu papel de consórcio.

### <a name="new-blockchainmemberinvitation"></a>New-BlockchainMemberInvitation

Use este cmdlet para convidar novos membros para o consórcio.

```
New-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>
```

| Parâmetro | Descrição | Obrigatório |
|-----------|-------------|:--------:|
| SubscriptionId | ID de assinatura Azure do membro para convidar | Yes |
| Função | O papel de consórcio. Os valores podem ser ADMIN ou USER. A ADMIN é a função de administrador do consórcio. USER é o papel de membro do consórcio. | Yes |
| Membros | Objeto de membros obtido a partir de Import-ConsortiumManagementContracts | Yes |
| Web3A contagem de votos | Objeto Web3A contagem obtida a partir de Import-Web3Account | Yes |
| Web3Client | Objeto Web3Client obtido a partir de New-Web3Connection | Yes |

#### <a name="example"></a>Exemplo

[Estabeleça uma ligação Web3](#establish-a-web3-connection) para definir as variáveis $ContractConnection e $MemberAccount.

```powershell-interactive
$ContractConnection | New-BlockchainMemberInvitation -SubscriptionId <Azure Subscription ID> -Role USER -Web3Account $MemberAccount
```

### <a name="get-blockchainmemberinvitation"></a>Get-BlockchainMemberInvitation

Utilize este cmdlet para recuperar ou listar o estatuto de convite de um membro do consórcio.

`Get-BlockchainMemberInvitation [[-SubscriptionId] <String>] -Members <IContract> -Web3Client <IClient>`

| Parâmetro | Descrição | Obrigatório |
|-----------|-------------|:--------:|
| SubscriptionId | O ID de assinatura Azure do membro para convidar. Se o ID de subscrição for fornecido, devolve os dados do convite da assinatura. Se o ID de subscrição for omitido, devolve uma lista de todos os convites dos membros. | No |
| Membros | Objeto de membros obtido a partir de Import-ConsortiumManagementContracts | Yes |
| Web3Client | Objeto Web3Client obtido a partir de New-Web3Connection | Yes |

#### <a name="example"></a>Exemplo

[Estabeleça uma ligação Web3](#establish-a-web3-connection) para definir a variável $ContractConnection.

```powershell-interactive
$ContractConnection | Get-BlockchainMemberInvitation –SubscriptionId <Azure subscription ID>
```

#### <a name="example-output"></a>Saída de exemplo

```
SubscriptionId                       Role CorrelationId
--------------                       ---- -------------
<Azure subscription ID>              USER             2
```

### <a name="remove-blockchainmemberinvitation"></a>Remove-BlockchainMemberInvitation

Use este cmdlet para revogar o convite de um membro do consórcio.

```
Remove-BlockchainMemberInvitation -SubscriptionId <String> -Members <IContract> -Web3Account <IAccount>
 -Web3Client <IClient>
```

| Parâmetro | Descrição | Obrigatório |
|-----------|-------------|:--------:|
| SubscriptionId | ID de assinatura Azure do membro para revogar | Yes |
| Membros | Objeto de membros obtido a partir de Import-ConsortiumManagementContracts | Yes |
| Web3A contagem de votos | Objeto Web3A contagem obtida a partir de Import-Web3Account | Yes |
| Web3Client | Objeto Web3Client obtido a partir de New-Web3Connection | Yes |

#### <a name="example"></a>Exemplo

[Estabeleça uma ligação Web3](#establish-a-web3-connection) para definir as variáveis $ContractConnection e $MemberAccount.

```powershell-interactive
$ContractConnection | Remove-BlockchainMemberInvitation -SubscriptionId <Subscription ID> -Web3Account $MemberAccount
```

### <a name="set-blockchainmemberinvitation"></a>Set-BlockchainMemberInvitation

Utilize este cmdlet para definir o **Papel** para um convite existente. Só os administradores do consórcio podem alterar convites.

```
Set-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>
```

| Parâmetro | Descrição | Obrigatório |
|-----------|-------------|:--------:|
| SubscriptionId | ID de assinatura Azure do membro para convidar | Yes |
| Função | Novo papel de consórcio para convite. Os valores podem ser **USER** ou **ADMIN.** | Yes |
| Membros |  Objeto de membros obtido a partir de Import-ConsortiumManagementContracts | Yes |
| Web3A contagem de votos | Objeto Web3A contagem obtida a partir de Import-Web3Account | Yes |
| Web3Client | Objeto Web3Client obtido a partir de New-Web3Connection | Yes |

#### <a name="example"></a>Exemplo

[Estabeleça uma ligação Web3](#establish-a-web3-connection) para definir as variáveis $ContractConnection e $MemberAccount.

```powershell-interactive
$ContractConnection | Set-BlockchainMemberInvitation -SubscriptionId <Azure subscription ID> -Role USER -Web3Account $MemberAccount
```

## <a name="next-steps"></a>Passos seguintes

Para mais informações sobre consórcios, membros e nóns, consulte [o consórcio do Azure Blockchain Service](consortium.md)
