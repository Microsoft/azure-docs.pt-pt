---
title: Gerir o Serviço Azure Blockchain utilizando o Azure CLI
description: Como gerir o Azure Blockchain Service com a Azure CLI
ms.date: 07/23/2020
ms.topic: how-to
ms.reviewer: ravastra
ms.openlocfilehash: 36b012c486c0c7d3303a81998e88f1605999c899
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87170852"
---
# <a name="manage-azure-blockchain-service-using-azure-cli"></a>Gerir o Serviço Azure Blockchain utilizando o Azure CLI

Além do portal Azure, pode utilizar o Azure CLI para gerir membros blockchain e nóns de transação para o seu Serviço Azure Blockchain.

## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta.

Para abrir o Cloud Shell, basta selecionar **Experimente** no canto superior direito de um bloco de código. Também pode lançar cloud Shell num separador de navegador indo para [https://shell.azure.com/bash](https://shell.azure.com/bash) . Selecione **Copiar** para copiar os blocos de código, cole-o no Cloud Shell e prima Enter para executá-lo.

Se preferir instalar e utilizar o CLI localmente, consulte [a instalação do Azure CLI](/cli/azure/install-azure-cli).

## <a name="prepare-your-environment"></a>Preparar o ambiente

1. Inicie sessão.

    Inicie sessão com o comando [az login](/cli/azure/reference-index#az-login) se estiver a utilizar uma instalação local da CLI.

    ```azurecli
    az login
    ```

    Siga os passos apresentados no seu terminal para concluir o processo de autenticação.

1. Instale a extensão da CLI do Azure.

    Para trabalhar com referências de extensão da CLI do Azure, terá primeiro de instalar a extensão.  As extensões da CLI do Azure dão-lhe acesso a comandos experimentais e de pré-lançamento que ainda não foram enviados como parte do núcleo da CLI.  Para saber mais sobre as extensões, incluindo a atualização e a desinstalação, veja [Utilizar extensões com a CLI do Azure](/cli/azure/azure-cli-extensions-overview).

    Instale a [extensão para o Serviço Azure Blockchain](/cli/azure/ext/blockchain/blockchain) executando o seguinte comando:

    ```azurecli-interactive
    az extension add --name blockchain
    ```

## <a name="create-blockchain-member"></a>Criar membro blockchain

Exemplo [cria um membro blockchain](/cli/azure/ext/blockchain/blockchain/member#ext-blockchain-az-blockchain-member-create) no Azure Blockchain Service que executa o protocolo de contabilidade Do Quorum num novo consórcio.

```azurecli
az blockchain member create \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName> \
                            --location <myBlockchainLocation> \
                            --password <strongMemberAccountPassword> \
                            --protocol "Quorum" \
                            --consortium <myConsortiumName> \
                            --consortium-management-account-password <strongConsortiumManagementPassword> \
                            --sku <skuName>
```

| Parâmetro | Descrição |
|---------|-------------|
| **grupo de recursos** | Nome do grupo de recursos onde são criados os recursos do Serviço Azure Blockchain. |
| **nome** | Um nome único que identifica o seu membro blockchain do Azure Blockchain Service. O nome é usado para o endereço de ponto final público. Por exemplo, `myblockchainmember.blockchain.azure.com`. |
| **localização** | Região de Azure onde o membro blockchain é criado. Por exemplo, `eastus`. Escolha a localização que estiver mais próxima dos seus utilizadores ou das suas outras aplicações do Azure. As funcionalidades podem não estar disponíveis em algumas regiões. |
| **palavra-passe** | A palavra-passe para o nó de transação padrão do membro. Utilize a palavra-passe para autenticação básica ao ligar-se ao ponto final público do nó de transação padrão do membro blockchain. A palavra-passe deve satisfazer três dos quatro requisitos seguintes: o comprimento deve estar entre 12 & 72 caracteres, 1 caracteres maiúsculos, 1 caracteres maiúsculos, 1 número e 1 caracteres especiais que não seja sinal de número (#), por cento(,), vírgula(,), citação \` traseira(, citação dupla(, citação única('), traço(-) e semicolumn (;)|
| **protocolo** | Protocolo blockchain. Atualmente, o protocolo *Qurum* é suportado. |
| **consórcio** | Nome do consórcio para aderir ou criar. Para obter mais informações sobre consórcios, consulte [o consórcio Azure Blockchain Service](consortium.md). |
| **consórcio-gestão-palavra-passe-conta** | A senha da conta do consórcio também é conhecida como a palavra-passe da conta do membro. A palavra-passe da conta do membro é usada para encriptar a chave privada da conta Ethereum que é criada para o seu membro. Utilize a conta de membro e a palavra-passe da conta do membro para a gestão do consórcio. |
| **sku** | Tipo de nível. *Padrão* ou *Básico*. Utilize o nível *básico* para desenvolvimento, teste e prova de conceitos. Utilize o nível *Standard* para implantações de nível de produção. Também deve utilizar o nível *Standard* se estiver a utilizar o Blockchain Data Manager ou a enviar um grande volume de transações privadas. A alteração do nível de preços entre o básico e o padrão após a criação dos membros não é apoiada. |

## <a name="change-blockchain-member-passwords-or-firewall-rules"></a>Alterar palavras-passe dos membros blockchain ou regras de firewall

O exemplo atualiza a palavra-passe de [um membro](/cli/azure/ext/blockchain/blockchain/member#ext-blockchain-az-blockchain-member-update)blockchain, a palavra-passe de gestão do consórcio e a regra da firewall.

```azurecli
az blockchain member update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --password <strongMemberAccountPassword> \
                     --consortium-management-account-password <strongConsortiumManagementPassword> \
                     --firewall-rules <firewallRules>
```

| Parâmetro | Descrição |
|---------|-------------|
| **grupo de recursos** | Nome do grupo de recursos onde são criados os recursos do Serviço Azure Blockchain. |
| **nome** | Nome que identifica o seu membro do Serviço Azure Blockchain. |
| **palavra-passe** | A palavra-passe para o nó de transação padrão do membro. Utilize a palavra-passe para autenticação básica ao ligar-se ao ponto final público do nó de transação padrão do membro blockchain. A palavra-passe deve satisfazer três dos quatro requisitos seguintes: o comprimento deve estar entre 12 & 72 caracteres, 1 caracteres maiúsculos, 1 caracteres maiúsculos, 1 número e 1 caracteres especiais que não seja sinal de número (#), por cento(,), vírgula(,), citação \` traseira(, citação dupla(, citação única('), traço(-) e semicolumn (;)|
| **consórcio-gestão-palavra-passe-conta** | A senha da conta do consórcio também é conhecida como a palavra-passe da conta do membro. A palavra-passe da conta do membro é usada para encriptar a chave privada da conta Ethereum que é criada para o seu membro. Utilize a conta de membro e a palavra-passe da conta do membro para a gestão do consórcio. |
| **regras de firewall** | Início e fim endereço IP para lista de autorizações IP. |

## <a name="create-transaction-node"></a>Criar nó de transação

[Crie um nó de transação](/cli/azure/ext/blockchain/blockchain/transaction-node#ext-blockchain-az-blockchain-transaction-node-create) dentro de um membro blockchain existente. Ao adicionar nós de transação, pode aumentar o isolamento de segurança e distribuir carga. Por exemplo, pode ter um ponto final de nó de transação para diferentes aplicações do cliente.

```azurecli
az blockchain transaction-node create \
                     --resource-group <myResourceGroup> \
                     --member-name <myMemberName> \
                     --password <strongTransactionNodePassword> \
                     --name <myTransactionNodeName>
```

| Parâmetro | Descrição |
|---------|-------------|
| **grupo de recursos** | Nome do grupo de recursos onde são criados os recursos do Serviço Azure Blockchain. |
| **localização** | Região azul do membro blockchain. |
| **nome membro** | Nome que identifica o seu membro do Serviço Azure Blockchain. |
| **palavra-passe** | A senha do nó de transação. Utilize a palavra-passe para autenticação básica ao ligar-se ao ponto final público do nó de transação. A palavra-passe deve satisfazer três dos quatro requisitos seguintes: o comprimento deve estar entre 12 & 72 caracteres, 1 caracteres maiúsculos, 1 caracteres maiúsculos, 1 número e 1 caracteres especiais que não seja sinal de número (#), por cento(,), vírgula(,), citação \` traseira(, citação dupla(, citação única('), traço(-) e semicolumn (;)|
| **nome** | Nome do nó de transação. |

## <a name="change-transaction-node-password"></a>Alterar senha de nó de transação

Exemplo atualiza uma senha [de nó de transação.](/cli/azure/ext/blockchain/blockchain/transaction-node#ext-blockchain-az-blockchain-transaction-node-update)

```azurecli
az blockchain transaction-node update \
                     --resource-group <myResourceGroup> \
                     --member-name <myMemberName> \
                     --password <strongTransactionNodePassword> \
                     --name <myTransactionNodeName>
```

| Parâmetro | Descrição |
|---------|-------------|
| **grupo de recursos** | Nome do grupo de recursos onde existem recursos do Serviço Azure Blockchain. |
| **nome membro** | Nome que identifica o seu membro do Serviço Azure Blockchain. |
| **palavra-passe** | A senha do nó de transação. Utilize a palavra-passe para autenticação básica ao ligar-se ao ponto final público do nó de transação. A palavra-passe deve satisfazer três dos quatro requisitos seguintes: o comprimento deve estar entre 12 & 72 caracteres, 1 caracteres maiúsculos, 1 caracteres maiúsculos, 1 número e 1 caracteres especiais que não seja sinal de número (#), por cento(,), vírgula(,), citação \` traseira(, citação dupla(, citação única('), traço(-) e semicolumn (;)|
| **nome** | Nome do nó de transação. |

## <a name="list-api-keys"></a>Listar chaves API

As teclas API podem ser usadas para acesso a nó semelhante ao nome de utilizador e à palavra-passe. Existem duas chaves API para suportar a rotação da chave. Utilize o seguinte comando para [listar as suas teclas API](/cli/azure/ext/blockchain/blockchain/member#ext-blockchain-az-blockchain-transaction-node-list-api-key).

```azurecli
az blockchain member list-api-key \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName>
```

| Parâmetro | Descrição |
|---------|-------------|
| **grupo de recursos** | Nome do grupo de recursos onde existem recursos do Serviço Azure Blockchain. |
| **nome** | Nome do membro blockchain do Serviço Azure Blockchain |

## <a name="regenerate-api-keys"></a>Chaves API regeneração

Utilize o seguinte comando para [regenerar as suas teclas API](/cli/azure/ext/blockchain/blockchain/member#ext-blockchain-az-blockchain-transaction-node-regenerate-api-key).

```azurecli
az blockchain member regenerate-api-key \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName> \
                            [--key-name {<keyValue1>, <keyValue2>}]
```

| Parâmetro | Descrição |
|---------|-------------|
| **grupo de recursos** | Nome do grupo de recursos onde existem recursos do Serviço Azure Blockchain. |
| **nome** | Nome do membro blockchain do Serviço Azure Blockchain. |
| **nome chave** | \<keyValue\>Substitua-a por uma das chaves1, key2 ou ambas. |

## <a name="delete-a-transaction-node"></a>Apagar um nó de transação

Exemplo elimina um nó de [transação de membro blockchain](/cli/azure/ext/blockchain/blockchain/transaction-node#ext-blockchain-az-blockchain-transaction-node-delete).

```azurecli
az blockchain transaction-node delete \
                     --resource-group <myResourceGroup> \
                     --member-name <myMemberName> \
                     --name <myTransactionNode>
```

| Parâmetro | Descrição |
|---------|-------------|
| **grupo de recursos** | Nome do grupo de recursos onde existem recursos do Serviço Azure Blockchain. |
| **nome membro** | Nome do membro blockchain do Azure Blockchain Service que também inclui o nome do nó de transação a ser eliminado. |
| **nome** | Nome do nó de transação para apagar. |

## <a name="delete-a-blockchain-member"></a>Excluir um membro blockchain

Exemplo [elimina um membro blockchain](/cli/azure/ext/blockchain/blockchain/member#ext-blockchain-az-blockchain-member-delete).

```azurecli
az blockchain member delete \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName>

```

| Parâmetro | Descrição |
|---------|-------------|
| **grupo de recursos** | Nome do grupo de recursos onde existem recursos do Serviço Azure Blockchain. |
| **nome** | Nome do membro blockchain do Serviço Azure Blockchain a ser eliminado. |

## <a name="azure-active-directory"></a>Azure Active Directory

### <a name="grant-access-for-azure-ad-user"></a>Conceder acesso a utilizador Azure AD

```azurecli
az role assignment create \
                            --role <role> \
                            --assignee <assignee> \
                            --scope /subscriptions/<subId>/resourceGroups/<groupName>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>
```

| Parâmetro | Descrição |
|---------|-------------|
| **papel** | Nome do papel da AD Azure. |
| **destinatário** | Identificação do utilizador AD Azure. Por exemplo, `user@contoso.com` |
| **âmbito** | Âmbito da atribuição de papel. Pode ser um membro blockchain ou nó de transação. |

**Exemplo:**

Conceder acesso ao nó para utilizador Azure AD para **membro**blockchain:

```azurecli
az role assignment create \
                            --role 'myRole' \
                            --assignee user@contoso.com \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

**Exemplo:**

Conceder acesso ao nó para o utilizador Azure AD para **o nó de transação**blockchain:

```azurecli
az role assignment create \
                            --role 'MyRole' \
                            --assignee user@contoso.com \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1/transactionNodes/contosoTransactionNode1
```

### <a name="grant-node-access-for-azure-ad-group-or-application-role"></a>Acesso de nó de subvenção para grupo AZure AD ou papel de candidatura

```azurecli
az role assignment create \
                            --role <role> \
                            --assignee-object-id <assignee_object_id>
```

| Parâmetro | Descrição |
|---------|-------------|
| **papel** | Nome do papel da AD Azure. |
| **assignee-object-id** | ID do grupo AD Azure ou ID de aplicação. |
| **âmbito** | Âmbito da atribuição de papel. Pode ser um membro blockchain ou nó de transação. |

**Exemplo:**

Conceder acesso a nó para **função de candidatura**

```azurecli
az role assignment create \
                            --role 'myRole' \
                            --assignee-object-id 22222222-2222-2222-2222-222222222222 \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

### <a name="remove-azure-ad-node-access"></a>Remover acesso ao nó AD AZure

```azurecli
az role assignment delete \
                            --role <myRole> \
                            --assignee <assignee> \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/<myResourceGroup>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>/transactionNodes/<myTransactionNode>
```

| Parâmetro | Descrição |
|---------|-------------|
| **papel** | Nome do papel da AD Azure. |
| **destinatário** | Identificação do utilizador AD Azure. Por exemplo, `user@contoso.com` |
| **âmbito** | Âmbito da atribuição de papel. Pode ser um membro blockchain ou nó de transação. |

## <a name="next-steps"></a>Passos seguintes

Saiba como [configurar os nós de transação do Serviço Azure Blockchain com o portal Azure](configure-transaction-nodes.md).
