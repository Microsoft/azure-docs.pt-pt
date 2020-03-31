---
title: Gerir membros do consórcio Azure Blockchain Service - PowerShell
description: Saiba como gerir os membros do consórcio Azure Blockchain Service utilizando o Azure PowerShell.
ms.date: 10/14/2019
ms.topic: article
ms.reviewer: zeyadr
ms.openlocfilehash: e819dd39481b58d446384a5e2253c548ce0c267c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77505979"
---
# <a name="manage-consortium-members-in-azure-blockchain-service-using-powershell"></a>Gerir membros do consórcio no Serviço Azure Blockchain usando powerShell

Pode utilizar o PowerShell para gerir os membros do consórcio blockchain para o seu Serviço Azure Blockchain. Os membros que tenham privilégios de administrador podem convidar, adicionar, remover e alterar funções para todos os participantes no consórcio blockchain. Os membros que tenham privilégios de utilizador podem ver todos os participantes no consórcio blockchain e alterar o nome de exibição do membro.

## <a name="prerequisites"></a>Pré-requisitos

* Crie um membro blockchain utilizando o [portal Azure](create-member.md).
* Para obter mais informações sobre consórcios, membros e nós, consulte o [consórcio Azure Blockchain Service](consortium.md).

## <a name="open-azure-cloud-shell"></a>Abrir o Azure Cloud Shell

A Azure Cloud Shell é uma concha interativa gratuita que pode suster para executar os passos deste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta.

Também pode abrir a Cloud Shell num separado separado do [navegador, indo](https://shell.azure.com/powershell)para shell.azure.com/powershell . Selecione **Copiar** para copiar os blocos de código, cole-o na Cloud Shell e selecione **Enter** para executá-lo.

## <a name="install-the-powershell-module"></a>Instale o módulo PowerShell

Instale o pacote Microsoft.AzureBlockchainService.ConsortiumManagement.PS na Galeria PowerShell.

```powershell-interactive
Install-Module -Name Microsoft.AzureBlockchainService.ConsortiumManagement.PS -Scope CurrentUser
Import-Module Microsoft.AzureBlockchainService.ConsortiumManagement.PS
```

## <a name="set-the-information-preference"></a>Definir a preferência de informação

Pode obter mais informações ao executar os cmdlets definindo a variável de preferência de informação. Por defeito, *$InformationPreference* está definido para *SilenciosamenteContinuar*.

Para obter mais informações verbosas a partir de cmdlets, delicie a preferência na PowerShell da seguinte forma:

```powershell-interactive
$InformationPreference = 'Continue'
```

## <a name="establish-a-web3-connection"></a>Estabelecer uma ligação Web3

Para gerir os membros do consórcio, estabeleça uma ligação Web3 ao seu ponto final do membro do Serviço Blockchain. Você pode usar este script para definir variáveis globais para chamar a gestão do consórcio cmdlets.

```powershell-interactive
$Connection = New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
$MemberAccount = Import-Web3Account -ManagedAccountAddress '<Member account address>' -ManagedAccountPassword '<Member account password>'
$ContractConnection = Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>' -Web3Client $Connection
```

Substitua * \<a\> palavra-passe da conta do membro* pela palavra-passe da conta membro que usou quando criou o membro.

Encontre os outros valores no portal Azure:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
1. Vá à página de **visão geral** do membro do Serviço Blockchain.

    ![Visão geral dos membros](./media/manage-consortium-powershell/member-overview.png)

    Substitua * \<\> * a conta de membro e * \<\> * o endereço RootContract pelos valores do portal.

1. Para o endereço de ponto final, selecione os nós de **transação**e, em seguida, selecione o nó de **transação predefinido**. O nó padrão tem o mesmo nome que o membro blockchain.
1. Selecione **as cordas de ligação**.

    ![Cadeias de ligação](./media/manage-consortium-powershell/connection-strings.png)

    Substitua * \<o\> endereço Endpoint* pelo valor de **HTTPS (Tecla** de acesso 1) ou HTTPS (Tecla de **acesso 2)**.

## <a name="manage-the-network-and-smart-contracts"></a>Gerir a rede e os contratos inteligentes

Utilize a rede e os cmdlets de contrato inteligente para estabelecer uma ligação aos contratos inteligentes do blockchain endpoint responsáveis pela gestão do consórcio.

### <a name="import-consortiummanagementcontracts"></a>Contratos de Gestão de Importações-Consórcios

Utilize este cmdlet para se conectar aos contratos inteligentes da gestão do consórcio. Estes contratos são utilizados para gerir e impor membros dentro do consórcio.

`Import-ConsortiumManagementContracts -RootContractAddress <String> -Web3Client <IClient>`

| Parâmetro | Descrição | Necessário |
|-----------|-------------|:--------:|
| Endereço RootContract | Endereço de contrato de raiz dos contratos inteligentes de gestão do consórcio | Sim |
| Web3Client | Objeto Web3Client obtido a partir de New-Web3Connection | Sim |

#### <a name="example"></a>Exemplo

```powershell-interactive
Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>'  -Web3Client $Connection
```

### <a name="import-web3account"></a>Import-Web3Account

Utilize este cmdlet para criar um objeto para guardar a informação para a conta de gestão de um nó remoto.

`Import-Web3Account -ManagedAccountAddress <String> -ManagedAccountPassword <String>`

| Parâmetro | Descrição | Necessário |
|-----------|-------------|:--------:|
| Endereço de Conta Gerido | Endereço de conta membro blockchain | Sim |
| ManagedAccountPassword | Palavra-passe de endereço de conta | Sim |

#### <a name="example"></a>Exemplo

```powershell-interactive
Import-Web3Account -ManagedAccountAddress '<Member account address>'  -ManagedAccountPassword '<Member account password>'
```

### <a name="new-web3connection"></a>New-Web3Connection

Utilize este cmdlet para estabelecer uma ligação ao ponto final rPC de um nó de transação.

`New-Web3Connection [-RemoteRPCEndpoint <String>]`

| Parâmetro | Descrição | Necessário |
|-----------|-------------|:--------:|
| Ponto de extremidade RemotoRPC | Endereço final do membro blockchain | Sim |

#### <a name="example"></a>Exemplo

```powershell-interactive
New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
```

## <a name="manage-the-consortium-members"></a>Gerir os membros do consórcio

Utilize cmdlets de gestão de membros do consórcio para gerir membros dentro do consórcio. As ações disponíveis dependem do seu papel de consórcio.

### <a name="get-blockchainmember"></a>Get-BlockchainMember

Utilize este cmdlet para obter detalhes do membro ou listar membros do consórcio.

`Get-BlockchainMember [[-Name] <String>] -Members <IContract> -Web3Client <IClient>`

| Parâmetro | Descrição | Necessário |
|-----------|-------------|:--------:|
| Nome | O nome do membro do Serviço Blockchain que pretende recuperar detalhes. Quando um nome é introduzido, devolve os detalhes do membro. Quando um nome é omitido, devolve uma lista de todos os membros do consórcio. | Não |
| Membros | Objeto de membros obtido a partir de Contratos de Gestão de Importações-Consórcios | Sim |
| Web3Client | Objeto Web3Client obtido a partir de New-Web3Connection | Sim |

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

### <a name="remove-blockchainmember"></a>Remover-BlockchainMember

Utilize este cmdlet para remover um membro blockchain.

`Remove-BlockchainMember -Name <String> -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>`

| Parâmetro | Descrição | Necessário |
|-----------|-------------|:--------:|
| Nome | Nome do membro para remover | Sim |
| Membros | Objeto de membros obtido a partir de Contratos de Gestão de Importações-Consórcios | Sim |
| Conta Web3 | Objeto Web3Account obtido a partir de Import-Web3Account | Sim |
| Web3Client | Objeto Web3Client obtido a partir de New-Web3Connection | Sim |

#### <a name="example"></a>Exemplo

[Estabeleça uma ligação Web3](#establish-a-web3-connection) para definir as variáveis $ContractConnection e $MemberAccount.

```powershell-interactive
$ContractConnection | Remove-BlockchainMember -Name <Member Name> -Web3Account $MemberAccount
```

### <a name="set-blockchainmember"></a>Set-BlockchainMember

Utilize este cmdlet para definir os atributos do membro blockchain, incluindo o nome do display e o papel do consórcio.

Os administradores do consórcio podem definir **o DisplayName** e **O Papel** para todos os membros. Um membro do consórcio com a função de utilizador só pode alterar o nome de exibição do seu próprio membro.

```
Set-BlockchainMember -Name <String> [-DisplayName <String>] [-AccountAddress <String>] [-Role <String>]
 -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>
```

| Parâmetro | Descrição | Necessário |
|-----------|-------------|:--------:|
| Nome | Nome do membro blockchain | Sim |
| DisplayName | Novo nome de exibição | Não |
| Endereço de Conta | Endereço de conta | Não |
| Membros | Objeto de membros obtido a partir de Contratos de Gestão de Importações-Consórcios | Sim |
| Conta Web3 | Objeto Web3Account obtido a partir de Import-Web3Account | Sim |
| Web3Client |  Objeto Web3Client obtido a partir de New-Web3Connection| Sim |

#### <a name="example"></a>Exemplo

[Estabeleça uma ligação Web3](#establish-a-web3-connection) para definir as variáveis $ContractConnection e $MemberAccount.

```powershell-interactive
$ContractConnection | Set-BlockchainMember -Name <Member Name> -DisplayName <Display name> -Web3Account $MemberAccount
```

## <a name="manage-the-consortium-members-invitations"></a>Gerir os convites dos membros do consórcio

Utilize a gestão de convites do consórcio para gerir os convites dos membros do consórcio. As ações disponíveis dependem do seu papel de consórcio.

### <a name="new-blockchainmemberinvitation"></a>Convite new-blockchainmember

Use este cmdlet para convidar novos membros para o consórcio.

```
New-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>
```

| Parâmetro | Descrição | Necessário |
|-----------|-------------|:--------:|
| SubscriptionId | Id de assinatura Azure do membro para convidar | Sim |
| Função | O papel do consórcio. Os valores podem ser ADMIN ou USER. AdmIN é o papel de administrador do consórcio. USER é o papel de membro do consórcio. | Sim |
| Membros | Objeto de membros obtido a partir de Contratos de Gestão de Importações-Consórcios | Sim |
| Conta Web3 | Objeto Web3Account obtido a partir de Import-Web3Account | Sim |
| Web3Client | Objeto Web3Client obtido a partir de New-Web3Connection | Sim |

#### <a name="example"></a>Exemplo

[Estabeleça uma ligação Web3](#establish-a-web3-connection) para definir as variáveis $ContractConnection e $MemberAccount.

```powershell-interactive
$ContractConnection | New-BlockchainMemberInvitation -SubscriptionId <Azure Subscription ID> -Role USER -Web3Account $MemberAccount
```

### <a name="get-blockchainmemberinvitation"></a>Convite get-blockchainmember

Utilize este cmdlet para recuperar ou listar o estatuto de convite de um membro do consórcio.

`Get-BlockchainMemberInvitation [[-SubscriptionId] <String>] -Members <IContract> -Web3Client <IClient>`

| Parâmetro | Descrição | Necessário |
|-----------|-------------|:--------:|
| SubscriptionId | A identificação de assinatura Azure do membro para convidar. Se o ID de subscrição for fornecido, devolve os dados do convite do II de subscrição. Se o ID de subscrição for omitido, devolve uma lista de todos os convites dos membros. | Não |
| Membros | Objeto de membros obtido a partir de Contratos de Gestão de Importações-Consórcios | Sim |
| Web3Client | Objeto Web3Client obtido a partir de New-Web3Connection | Sim |

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

### <a name="remove-blockchainmemberinvitation"></a>Convite remover-blockchainmember

Use este cmdlet para revogar o convite de um membro do consórcio.

```
Remove-BlockchainMemberInvitation -SubscriptionId <String> -Members <IContract> -Web3Account <IAccount>
 -Web3Client <IClient>
```

| Parâmetro | Descrição | Necessário |
|-----------|-------------|:--------:|
| SubscriptionId | Id de assinatura Azure do membro para revogar | Sim |
| Membros | Objeto de membros obtido a partir de Contratos de Gestão de Importações-Consórcios | Sim |
| Conta Web3 | Objeto Web3Account obtido a partir de Import-Web3Account | Sim |
| Web3Client | Objeto Web3Client obtido a partir de New-Web3Connection | Sim |

#### <a name="example"></a>Exemplo

[Estabeleça uma ligação Web3](#establish-a-web3-connection) para definir as variáveis $ContractConnection e $MemberAccount.

```powershell-interactive
$ContractConnection | Remove-BlockchainMemberInvitation -SubscriptionId <Subscription ID> -Web3Account $MemberAccount
```

### <a name="set-blockchainmemberinvitation"></a>Convite set-blockchainmember

Utilize este cmdlet para definir o **Papel** para um convite existente. Só os administradores do consórcio podem alterar convites.

```
Set-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>
```

| Parâmetro | Descrição | Necessário |
|-----------|-------------|:--------:|
| SubscriptionId | Id de assinatura Azure do membro para convidar | Sim |
| Função | Novo papel de consórcio para convite. Os valores podem ser **USER** ou **ADMIN**. | Sim |
| Membros |  Objeto de membros obtido a partir de Contratos de Gestão de Importações-Consórcios | Sim |
| Conta Web3 | Objeto Web3Account obtido a partir de Import-Web3Account | Sim |
| Web3Client | Objeto Web3Client obtido a partir de New-Web3Connection | Sim |

#### <a name="example"></a>Exemplo

[Estabeleça uma ligação Web3](#establish-a-web3-connection) para definir as variáveis $ContractConnection e $MemberAccount.

```powershell-interactive
$ContractConnection | Set-BlockchainMemberInvitation -SubscriptionId <Azure subscription ID> -Role USER -Web3Account $MemberAccount
```

## <a name="next-steps"></a>Passos seguintes

Para mais informações sobre consórcios, membros e nós, consulte [o consórcio Azure Blockchain Service](consortium.md)
