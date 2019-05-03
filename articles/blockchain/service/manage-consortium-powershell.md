---
title: Gestão de consórcio Blockchain serviço do Azure com o PowerShell
description: Como gerir os membros de consórcio de serviço do Azure Blockchain com o PowerShell
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: bef0c5d776e8ab6424b8604a49782088c45b0538
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028234"
---
# <a name="manage-consortium-members-in-azure-blockchain-service-using-powershell"></a>Gerir Membros consortium no serviço do Azure Blockchain com o PowerShell

Pode utilizar o PowerShell para gerir os membros de consórcio de blockchain para o seu serviço do Azure Blockchain. Os membros com privilégios de administrador podem convidar, adicionar, remover e alterações de funções para todos os participantes no consortium de blockchain. Os membros com privilégios de utilizador podem ver todos os participantes no blockchain consortium e podem alterar o nome a apresentar de membro.

## <a name="prerequisites"></a>Pré-requisitos

* [Criar um membro de blockchain com o portal do Azure](create-member.md)
* Para obter mais informações sobre consortia, membros e os nós, consulte [consortium de serviço do Azure Blockchain](consortium.md)

## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. 

Também pode iniciar o Cloud Shell num separador do browser separado ao aceder a [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Selecione **Copiar** para copiar os blocos de código, cole-o no Cloud Shell e prima Enter para executá-lo.

## <a name="install-powershell-module"></a>Instalar o módulo do PowerShell

Instale o pacote de Microsoft.AzureBlockchainService.ConsortiumManagement.PS da galeria do PowerShell.

```powershell
Install-Module -Name Microsoft.AzureBlockchainService.ConsortiumManagement.PS -Scope CurrentUser
Import-Module Microsoft.AzureBlockchainService.ConsortiumManagement.PS
```

## <a name="establish-a-web3-connection"></a>Estabelecer uma ligação de Web3

Para gerir os membros de consortium, tem de estabelecer uma ligação de Web3 para o ponto de final do serviço do Azure Blockchain membro. Pode utilizar este script para definir as variáveis globais que podem ser utilizadas ao chamar os cmdlets de gestão consortium.

```powershell
$Connection = New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
$MemberAccount = Import-Web3Account -ManagedAccountAddress '<Member account address>' -ManagedAccountPassword '<Member account password>'
$ContractConnection = Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>' -Web3Client $Connection
```

Substitua \<palavra-passe da conta de membro\> com a palavra-passe da conta de membro utilizada quando criou o membro.

Localize os outros valores no portal do Azure:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Navegue para o membro predefinido de serviço do Azure Blockchain **descrição geral** página.

    ![Descrição geral de membro](./media/manage-consortium-powershell/member-overview.png)

    Substitua \<conta do membro\>, e \<endereço RootContract\> com os valores a partir do portal.

1. Para o endereço de ponto de extremidade, selecione **nós de transação** e selecione um nó de transação.
1. Selecione **cadeias de ligação**.

    ![Cadeias de ligação](./media/manage-consortium-powershell/connection-strings.png)

    Substitua \<endereço do ponto final\> com o valor de **HTTPS (chave de acesso 1)** ou **HTTPS (chave de acesso 2)**.

## <a name="network-and-smart-contract-management"></a>Rede e gerenciamento inteligentes

Utilize os cmdlets de contrato inteligentes e de rede para estabelecer uma ligação ao seu blockchain endpoint contratos inteligentes responsáveis pela gestão consortium.

### <a name="import-consortiummanagementcontracts"></a>Import-ConsortiumManagementContracts

Liga-se para os contratos de inteligente da gestão consortium, que são utilizados para gerir e impor os membros dentro do consórcio.

```powershell
Import-ConsortiumManagementContracts -RootContractAddress <String> -Web3Client <IClient>
```

| Parâmetro | Descrição | Necessário |
|-----------|-------------|:--------:|
| RootContractAddress | O endereço de contrato de raiz dos contratos inteligentes consortium de gestão | Sim |
| Web3Client | Objeto de Web3Client obtido a partir do New-Web3Connection | Sim |

**Exemplo**

```powershell
Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>'  -Web3Client $Connection
```

### <a name="import-web3account"></a>Importar-Web3Account

Utilize este cmdlet para criar um objeto para manter a conta de gestão de nó remoto de informações.

```powershell
Import-Web3Account -ManagedAccountAddress <String> -ManagedAccountPassword <String>
```

| Parâmetro | Descrição | Necessário |
|-----------|-------------|:--------:|
| ManagedAccountAddress | Endereço de conta de membro de Blockchain | Sim |
| ManagedAccountPassword | Palavra-passe de endereço de conta | Sim |

**Exemplo**

```powershell
Import-Web3Account -ManagedAccountAddress '<Member account address>'  -ManagedAccountPassword '<Member account password>'
```

### <a name="new-web3connection"></a>New-Web3Connection

Estabelece uma ligação para o ponto de extremidade RPC de um nó de transação.

```powershell
New-Web3Connection [-RemoteRPCEndpoint <String>]
```

| Parâmetro | Descrição | Necessário |
|-----------|-------------|:--------:|
| RemoteRPCEndpoint | Endereço de ponto final de membro de Blockchain | Sim |


**Exemplo**

```powershell
New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
```

## <a name="consortium-member-management"></a>Gestão de membro Consortium

Utilize cmdlets de gestão do membro consortium para gerir membros do consortium. Ações disponíveis dependem de sua função consortium.

### <a name="get-blockchainmember"></a>Get-BlockchainMember

Obtém os detalhes de membro ou membros de lista do consortium.

```powershell
Get-BlockchainMember [[-Name] <String>] -Members <IContract> -Web3Client <IClient>
```

| Parâmetro | Descrição | Necessário |
|-----------|-------------|:--------:|
| Name | O nome do membro de serviço do Azure Blockchain para obter detalhes sobre. Se fornecer um nome de membro, são devolvidos detalhes do membro. Se o nome for omitido, é devolvida uma lista de todos os membros de consortium. | Não |
| Membros | Objeto de membros obtido a partir do ConsortiumManagementContracts de importação | Sim |
| Web3Client | Objeto de Web3Client obtido a partir do New-Web3Connection | Sim |

**Exemplo**

```powershell
$ContractConnection | Get-BlockchainMember -Name <Member Name>
```

**Exemplo de saída**

```
Name           : myblockchainmember
CorrelationId  : 0
DisplayName    : myCompany
SubscriptionId : <Azure subscription ID>
AccountAddress : 0x85b911c9e103d6405573151258d668479e9ebeef
Role           : ADMIN
```

### <a name="remove-blockchainmember"></a>Remove-BlockchainMember

Remove um membro de blockchain.

```powershell
Remove-BlockchainMember -Name <String> -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>
```

| Parâmetro | Descrição | Necessário |
|-----------|-------------|:--------:|
| Name | Nome do membro a remover | Sim |
| Membros | Objeto de membros obtido a partir do ConsortiumManagementContracts de importação | Sim |
| Web3Account | Objeto de Web3Account obtido a partir do Web3Account de importação | Sim |
| Web3Client | Objeto de Web3Client obtido a partir do New-Web3Connection | Sim |

**Exemplo**

```powershell
$ContractConnection | Remove-BlockchainMember -Name <Member Name> -Web3Account $MemberAccount
```

### <a name="set-blockchainmember"></a>Set-BlockchainMember

Define o blockchain atributos de membro, incluindo a função de nome e consortium de exibição.

Os administradores de consórcio podem definir **DisplayName** e **função** para todos os membros. Membro de consórcio com a função de utilizador só pode alterar o nome a apresentar do seu próprio membro.

```powershell
Set-BlockchainMember -Name <String> [-DisplayName <String>] [-AccountAddress <String>] [-Role <String>]
 -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>
```

| Parâmetro | Descrição | Necessário |
|-----------|-------------|:--------:|
| Name | Nome do membro de blockchain | Sim |
| Nome a Apresentar | Novo nome a apresentar | Não |
| AccountAddress | Endereço da conta | Não |
| Membros | Objeto de membros obtido a partir do ConsortiumManagementContracts de importação | Sim |
| Web3Account | Objeto de Web3Account obtido a partir do Web3Account de importação | Sim |
| Web3Client |  Objeto de Web3Client obtido a partir do New-Web3Connection| Sim |

**Exemplo**

```powershell
$ContractConnection | Set-BlockchainMember -Name <Member Name> -DisplayName <Display name> -Web3Account $MemberAccount
```

## <a name="consortium-member-invitation-management"></a>Gestão de convite de membro Consortium

Utilize cmdlets de gestão de convite de membro consortium para gerir convites de membro consortium. Ações disponíveis dependem de sua função consortium.

### <a name="new-blockchainmemberinvitation"></a>New-BlockchainMemberInvitation

Convide novos membros para o consortium.

```powershell
New-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>
```

| Parâmetro | Descrição | Necessário |
|-----------|-------------|:--------:|
| SubscriptionId | ID de subscrição do Azure do membro convidado | Sim |
| Função | Função de Consortium. Valores podem ser administrador ou utilizador. ADMINISTRAÇÃO é a função de administrador do consórcio. UTILIZADOR é a função de membro de consortium. | Sim |
| Membros | Objeto de membros obtido a partir do ConsortiumManagementContracts de importação | Sim |
| Web3Account | Objeto de Web3Account obtido a partir do Web3Account de importação | Sim |
| Web3Client | Objeto de Web3Client obtido a partir do New-Web3Connection | Sim |

**Exemplo**

```powershell
$ContractConnection | New-BlockchainMemberInvitation -SubscriptionId <Azure Subscription ID> -Role USER -Web3Account $MemberAccount
```

### <a name="get-blockchainmemberinvitation"></a>Get-BlockchainMemberInvitation

Obtém ou lista de estado de convite de membro consortium.

```powershell
Get-BlockchainMemberInvitation [[-SubscriptionId] <String>] -Members <IContract> -Web3Client <IClient>
```

| Parâmetro | Descrição | Necessário |
|-----------|-------------|:--------:|
| SubscriptionId | ID de subscrição do Azure do membro convidado. Se não for fornecido SubscriptionID, detalhes de convite do ID de subscrição são devolvidos. Se for omitido SubscriptionID, uma lista de todos os convites de membro são devolvidos. | Não |
| Membros | Objeto de membros obtido a partir do ConsortiumManagementContracts de importação | Sim |
| Web3Client | Objeto de Web3Client obtido a partir do New-Web3Connection | Sim |

**Exemplo**

```powershell
$ContractConnection | Get-BlockchainMemberInvitation – SubscriptionId <Azure subscription ID>
```

**Exemplo de saída**

```
SubscriptionId                       Role CorrelationId
--------------                       ---- -------------
<Azure subscription ID>              USER             2
```

### <a name="remove-blockchainmemberinvitation"></a>Remove-BlockchainMemberInvitation

Revoga um convite de membro consortium.

```powershell
Remove-BlockchainMemberInvitation -SubscriptionId <String> -Members <IContract> -Web3Account <IAccount>
 -Web3Client <IClient>
```

| Parâmetro | Descrição | Necessário |
|-----------|-------------|:--------:|
| SubscriptionId | ID de subscrição do Azure de membro para revogar | Sim |
| Membros | Objeto de membros obtido a partir do ConsortiumManagementContracts de importação | Sim |
| Web3Account | Objeto de Web3Account obtido a partir do Web3Account de importação | Sim |
| Web3Client | Objeto de Web3Client obtido a partir do New-Web3Connection | Sim |

**Exemplo**

```powershell
$ContractConnection | Remove-BlockchainMemberInvitation -SubscriptionId <Subscription ID> -Web3Account $MemberAccount
```

### <a name="set-blockchainmemberinvitation"></a>Set-BlockchainMemberInvitation

Conjuntos de **função** para um convite existente. Apenas os administradores de consortium podem alterar os convites.

```powershell
Set-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>
```

| Parâmetro | Descrição | Necessário |
|-----------|-------------|:--------:|
| SubscriptionId | ID de subscrição do Azure do membro convidado | Sim |
| Função | Nova função de consórcio para o convite. Os valores podem ser **USUÁRIO** ou **ADMIN** | Sim |
| Membros |  Objeto de membros obtido a partir do ConsortiumManagementContracts de importação | Sim |
| Web3Account | Objeto de Web3Account obtido a partir do Web3Account de importação | Sim |
| Web3Client | Objeto de Web3Client obtido a partir do New-Web3Connection | Sim |

**Exemplo**

```powershell
$ContractConnection | Set-BlockchainMemberInvitation -SubscriptionId <Azure subscription ID> -Role USER -Web3Account $MemberAccount
```

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre consortia, membros e os nós, consulte:

> [!div class="nextstepaction"]
> [Azure consortium do serviço de Blockchain](consortium.md)