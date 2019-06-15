---
title: Gerir membros de um consórcio de serviço do Azure Blockchain com o Azure PowerShell
description: Saiba como gerir os membros de consórcio de serviço do Azure Blockchain com o Azure PowerShell.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/10/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 4bb72bc3fe8b85a8d2aed88e02f5f3150abb6899
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66493639"
---
# <a name="manage-consortium-members-in-azure-blockchain-service-by-using-powershell"></a>Gerir Membros consortium no serviço do Azure Blockchain com o PowerShell

Pode utilizar o PowerShell para gerir os membros de consórcio de blockchain para o seu serviço do Azure Blockchain. Os membros com privilégios de administrador podem convidar, adicionar, remover e alterar as funções para todos os participantes no consortium blockchain. Os membros que têm privilégios de utilizador podem ver todos os participantes no blockchain consortium e alterar o nome a apresentar de membro.

## <a name="prerequisites"></a>Pré-requisitos

* Criar um membro de blockchain com o [portal do Azure](create-member.md).
* Para obter mais informações sobre consortia, membros e os nós, consulte [consortium de serviço do Azure Blockchain](consortium.md).

## <a name="open-azure-cloud-shell"></a>Abrir o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta.

Também pode abrir Cloud Shell no separador do browser separado, acedendo a [shell.azure.com/powershell](https://shell.azure.com/powershell). Selecione **cópia** para copiar os blocos de código, colá-lo no Cloud Shell e selecione **Enter** executá-lo.

## <a name="install-the-powershell-module"></a>Instalar o módulo do PowerShell

Instale o pacote de Microsoft.AzureBlockchainService.ConsortiumManagement.PS da galeria do PowerShell.

```powershell-interactive
Install-Module -Name Microsoft.AzureBlockchainService.ConsortiumManagement.PS -Scope CurrentUser
Import-Module Microsoft.AzureBlockchainService.ConsortiumManagement.PS
```

## <a name="set-the-information-preference"></a>Definir a preferência de informações

Pode obter mais informações ao executar os cmdlets ao definir a variável de preferência de informações. Por predefinição, *$InformationPreference* está definida como *SilentlyContinue*.

Para obter informações mais detalhadas dos cmdlets, defina a preferência no PowerShell da seguinte forma:

```powershell-interactive
$InformationPreference = 'Continue'
```

## <a name="establish-a-web3-connection"></a>Estabelecer uma ligação de Web3

Para gerir os membros de consortium, estabelece uma ligação de Web3 para o ponto de final do serviço de Blockchain membro. Pode utilizar este script para definir as variáveis globais para chamar os cmdlets de gestão consortium.

```powershell-interactive
$Connection = New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
$MemberAccount = Import-Web3Account -ManagedAccountAddress '<Member account address>' -ManagedAccountPassword '<Member account password>'
$ContractConnection = Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>' -Web3Client $Connection
```

Substitua *\<palavra-passe da conta de membro\>* com a palavra-passe de conta membro que utilizou quando criou o membro.

Localize os outros valores no portal do Azure:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Vá para o membro predefinido de serviço de Blockchain **descrição geral** página.

    ![Descrição geral de membro](./media/manage-consortium-powershell/member-overview.png)

    Substitua *\<conta membro\>* e *\<RootContract endereço\>* com os valores a partir do portal.

1. Para o endereço de ponto de extremidade, selecione **nós de transação**e, em seguida, selecione a **nó de transação de predefinição**. O nó de padrão tem o mesmo nome que o membro de blockchain.
1. Selecione **cadeias de ligação**.

    ![Cadeias de ligação](./media/manage-consortium-powershell/connection-strings.png)

    Substitua *\<endereço do ponto final\>* com o valor de **HTTPS (chave de acesso 1)** ou **HTTPS (chave de acesso 2)** .

## <a name="manage-the-network-and-smart-contracts"></a>Gerir a rede e contratos inteligentes

Utilize os cmdlets de contrato inteligentes e de rede para estabelecer uma ligação para contratos inteligentes do ponto de extremidade blockchain responsáveis pela gestão consortium.

### <a name="import-consortiummanagementcontracts"></a>Import-ConsortiumManagementContracts

Utilize este cmdlet para ligar a contratos inteligentes da gestão de consortium. Esses contratos são utilizados para gerir e impor os membros dentro do consórcio.

`Import-ConsortiumManagementContracts -RootContractAddress <String> -Web3Client <IClient>`

| Parâmetro | Descrição | Necessário |
|-----------|-------------|:--------:|
| RootContractAddress | Endereço de contrato de raiz dos contratos inteligentes consortium de gestão | Sim |
| Web3Client | Objeto de Web3Client obtido a partir do New-Web3Connection | Sim |

#### <a name="example"></a>Exemplo

```powershell-interactive
Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>'  -Web3Client $Connection
```

### <a name="import-web3account"></a>Importar-Web3Account

Utilize este cmdlet para criar um objeto para conter as informações da conta de gestão de um nó remoto.

`Import-Web3Account -ManagedAccountAddress <String> -ManagedAccountPassword <String>`

| Parâmetro | Descrição | Necessário |
|-----------|-------------|:--------:|
| ManagedAccountAddress | Endereço de conta de membro de Blockchain | Sim |
| ManagedAccountPassword | Palavra-passe de endereço de conta | Sim |

#### <a name="example"></a>Exemplo

```powershell-interactive
Import-Web3Account -ManagedAccountAddress '<Member account address>'  -ManagedAccountPassword '<Member account password>'
```

### <a name="new-web3connection"></a>New-Web3Connection

Utilize este cmdlet para estabelecer uma ligação para o ponto de extremidade RPC de um nó de transação.

`New-Web3Connection [-RemoteRPCEndpoint <String>]`

| Parâmetro | Descrição | Necessário |
|-----------|-------------|:--------:|
| RemoteRPCEndpoint | Endereço de ponto final de membro de Blockchain | Sim |

#### <a name="example"></a>Exemplo

```powershell-interactive
New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
```

## <a name="manage-the-consortium-members"></a>Gerir os membros de consórcio

Utilize cmdlets de gestão do membro consortium para gerir membros do consortium. As ações disponíveis dependem de sua função consortium.

### <a name="get-blockchainmember"></a>Get-BlockchainMember

Utilize este cmdlet para obter detalhes de membro ou membros do consortium de lista.

`Get-BlockchainMember [[-Name] <String>] -Members <IContract> -Web3Client <IClient>`

| Parâmetro | Descrição | Necessário |
|-----------|-------------|:--------:|
| Name | O nome do membro Blockchain serviço que pretende obter os detalhes sobre. Quando é introduzido um nome, devolve detalhes do membro. Quando um nome for omitido, ele retorna uma lista de todos os membros de consortium. | Não |
| Membros | Objeto de membros obtido a partir do ConsortiumManagementContracts de importação | Sim |
| Web3Client | Objeto de Web3Client obtido a partir do New-Web3Connection | Sim |

#### <a name="example"></a>Exemplo

```powershell-interactive
$ContractConnection | Get-BlockchainMember -Name <Member Name>
```

#### <a name="example-output"></a>Exemplo de saída

```
Name           : myblockchainmember
CorrelationId  : 0
DisplayName    : myCompany
SubscriptionId : <Azure subscription ID>
AccountAddress : 0x85b911c9e103d6405573151258d668479e9ebeef
Role           : ADMIN
```

### <a name="remove-blockchainmember"></a>Remove-BlockchainMember

Utilize este cmdlet para remover um membro de blockchain.

`Remove-BlockchainMember -Name <String> -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>`

| Parâmetro | Descrição | Necessário |
|-----------|-------------|:--------:|
| Name | Nome do membro a remover | Sim |
| Membros | Objeto de membros obtido a partir do ConsortiumManagementContracts de importação | Sim |
| Web3Account | Objeto de Web3Account obtido a partir do Web3Account de importação | Sim |
| Web3Client | Objeto de Web3Client obtido a partir do New-Web3Connection | Sim |

#### <a name="example"></a>Exemplo

```powershell-interactive
$ContractConnection | Remove-BlockchainMember -Name <Member Name> -Web3Account $MemberAccount
```

### <a name="set-blockchainmember"></a>Set-BlockchainMember

Utilize este cmdlet para definir atributos de membro, incluindo o nome a apresentar e a função de consórcio de blockchain.

Os administradores de consórcio podem definir **DisplayName** e **função** para todos os membros. Um membro de consórcio com a função de utilizador pode alterar o nome a apresentar apenas seu próprio membro.

`Set-BlockchainMember -Name <String> [-DisplayName <String>] [-AccountAddress <String>] [-Role <String>]
 -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>`

| Parâmetro | Descrição | Necessário |
|-----------|-------------|:--------:|
| Name | Nome do membro de blockchain | Sim |
| displayName | Novo nome a apresentar | Não |
| AccountAddress | Endereço da conta | Não |
| Membros | Objeto de membros obtido a partir do ConsortiumManagementContracts de importação | Sim |
| Web3Account | Objeto de Web3Account obtido a partir do Web3Account de importação | Sim |
| Web3Client |  Objeto de Web3Client obtido a partir do New-Web3Connection| Sim |

#### <a name="example"></a>Exemplo

```powershell-interactive
$ContractConnection | Set-BlockchainMember -Name <Member Name> -DisplayName <Display name> -Web3Account $MemberAccount
```

## <a name="manage-the-consortium-members-invitations"></a>Gerenciar convites dos membros consortium

Utilize os cmdlets de gestão de convite de membro consortium para gerir convites de membros consortium. As ações disponíveis dependem de sua função consortium.

### <a name="new-blockchainmemberinvitation"></a>New-BlockchainMemberInvitation

Utilize este cmdlet para convidar novos membros para o consortium.

`New-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>`

| Parâmetro | Descrição | Necessário |
|-----------|-------------|:--------:|
| SubscriptionId | ID de subscrição do Azure do membro a convidar | Sim |
| Função | A função de consortium. Valores podem ser administrador ou utilizador. ADMINISTRAÇÃO é a função de administrador do consórcio. UTILIZADOR é a função de membro de consortium. | Sim |
| Membros | Objeto de membros obtido a partir do ConsortiumManagementContracts de importação | Sim |
| Web3Account | Objeto de Web3Account obtido a partir do Web3Account de importação | Sim |
| Web3Client | Objeto de Web3Client obtido a partir do New-Web3Connection | Sim |

#### <a name="example"></a>Exemplo

```powershell-interactive
$ContractConnection | New-BlockchainMemberInvitation -SubscriptionId <Azure Subscription ID> -Role USER -Web3Account $MemberAccount
```

### <a name="get-blockchainmemberinvitation"></a>Get-BlockchainMemberInvitation

Utilize este cmdlet para recuperar ou lista de estado de convite de um membro de consortium.

`Get-BlockchainMemberInvitation [[-SubscriptionId] <String>] -Members <IContract> -Web3Client <IClient>`

| Parâmetro | Descrição | Necessário |
|-----------|-------------|:--------:|
| SubscriptionId | O ID de subscrição do Azure do membro a convidar. Se a subscrição do ID é fornecida, ele retorna a subscrição IDs de detalhes de convite. Se o ID de subscrição for omitido, ele retorna uma lista de todos os convites de membro. | Não |
| Membros | Objeto de membros obtido a partir do ConsortiumManagementContracts de importação | Sim |
| Web3Client | Objeto de Web3Client obtido a partir do New-Web3Connection | Sim |

#### <a name="example"></a>Exemplo

```powershell-interactive
$ContractConnection | Get-BlockchainMemberInvitation – SubscriptionId <Azure subscription ID>
```

#### <a name="example-output"></a>Exemplo de saída

```
SubscriptionId                       Role CorrelationId
--------------                       ---- -------------
<Azure subscription ID>              USER             2
```

### <a name="remove-blockchainmemberinvitation"></a>Remove-BlockchainMemberInvitation

Utilize este cmdlet para revogar o convite de um membro de consortium.

`Remove-BlockchainMemberInvitation -SubscriptionId <String> -Members <IContract> -Web3Account <IAccount>
 -Web3Client <IClient>`

| Parâmetro | Descrição | Necessário |
|-----------|-------------|:--------:|
| SubscriptionId | ID de subscrição do Azure do membro a revogar | Sim |
| Membros | Objeto de membros obtido a partir do ConsortiumManagementContracts de importação | Sim |
| Web3Account | Objeto de Web3Account obtido a partir do Web3Account de importação | Sim |
| Web3Client | Objeto de Web3Client obtido a partir do New-Web3Connection | Sim |

#### <a name="example"></a>Exemplo

```powershell-interactive
$ContractConnection | Remove-BlockchainMemberInvitation -SubscriptionId <Subscription ID> -Web3Account $MemberAccount
```

### <a name="set-blockchainmemberinvitation"></a>Set-BlockchainMemberInvitation

Utilize este cmdlet para definir o **função** para um convite existente. Apenas os administradores de consortium podem alterar os convites.

`Set-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>`

| Parâmetro | Descrição | Necessário |
|-----------|-------------|:--------:|
| SubscriptionId | ID de subscrição do Azure do membro a convidar | Sim |
| Função | Nova função de consórcio para o convite. Os valores podem ser **USUÁRIO** ou **administrador**. | Sim |
| Membros |  Objeto de membros obtido a partir do ConsortiumManagementContracts de importação | Sim |
| Web3Account | Objeto de Web3Account obtido a partir do Web3Account de importação | Sim |
| Web3Client | Objeto de Web3Client obtido a partir do New-Web3Connection | Sim |

#### <a name="example"></a>Exemplo

```powershell-interactive
$ContractConnection | Set-BlockchainMemberInvitation -SubscriptionId <Azure subscription ID> -Role USER -Web3Account $MemberAccount
```

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre consortia, membros e os nós, consulte:

> [!div class="nextstepaction"]
> [Azure consortium do serviço de Blockchain](consortium.md)
