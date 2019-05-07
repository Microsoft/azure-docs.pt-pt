---
title: Criar um serviço de Blockchain do Azure com a CLI do Azure
description: Utilize o serviço do Azure Blockchain para criar um membro de blockchain com a CLI do Azure.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: seal
manager: femila
ms.openlocfilehash: e1b7558ea83c8948a8984215e15040e4d929cb1b
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65141388"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-azure-cli"></a>Início rápido: Criar um membro de blockchain de serviço do Azure Blockchain com a CLI do Azure

O serviço do Azure Blockchain é uma plataforma de blockchain que pode utilizar para executar a lógica de negócios dentro de um contrato inteligente. Este guia de introdução mostra-lhe como começar com a criação de um membro de blockchain com a CLI do Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta.

Para abrir o Cloud Shell, basta selecionar **Experimentar** no canto superior direito de um bloco de código. Também pode iniciar o Cloud Shell num separador do browser separado ao aceder a [https://shell.azure.com/bash](https://shell.azure.com/bash). Selecione **Copiar** para copiar os blocos de código, cole-o no Cloud Shell e prima Enter para executá-lo.

Se preferir instalar e utilizar a CLI localmente, este início rápido requer a CLI do Azure versão 2.0.51 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](https://docs.microsoft.com/cli/azure/group). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-blockchain-member"></a>Criar um membro de blockchain

Crie um membro de blockchain no Azure Blockchain Service que executa o protocolo de contabilidade de quórum num consórcio de novo.

Existem vários parâmetros e as propriedades que precisa passar. Substitua os seguintes parâmetros com os seus valores.

| Parâmetro | Descrição |
|---------|-------------|
| **resource-group** | Nome do grupo de recursos onde os recursos de serviço do Azure Blockchain são criados. Utilize o grupo de recursos que criou na secção anterior.
| **name** | Um nome exclusivo que identifica o membro de blockchain de serviço do Azure Blockchain. O nome é utilizado para o endereço de ponto final público. Por exemplo, `myblockchainmember.blockchain.azure.com`.
| **localização** | Região do Azure onde o membro de blockchain é criado. Por exemplo, `eastus`. Escolha a localização que estiver mais próxima dos seus utilizadores ou das suas outras aplicações do Azure.
| **password** | A palavra-passe de conta de membro. A palavra-passe da conta de membro é utilizada para autenticar para ponto final público o membro de blockchain usando a autenticação básica.
| **consortium** | Nome do consortium para aderir ou criar.
| **consortiumManagementAccountPassword** | A palavra-passe de gestão de consortium. Isto é utilizado para ingressar num consórcio.
| **skuName** | Tipo de camada. Utilize S0 Standard e B0 para o básico.

```azurecli-interactive
az resource create --resource-group myResourceGroup --name myblockchainmember --resource-type Microsoft.Blockchain/blockchainMembers --is-full-object --properties "{ \"location\": \"eastus\", \"properties\": {\"password\": \"strongMemberAccountPassword@1\", \"protocol\": \"Quorum\", \"consortium\": \"myConsortiumName\", \"consortiumManagementAccountPassword\": \"strongConsortiumManagementPassword@1\" }, \"sku\": { \"name\": \"S0\" } }"
```

Demora cerca de 10 minutos para criar o membro de blockchain e recursos de suporte.

O seguinte resultado de exemplo mostra um bem-sucedida de operação de criação.

```json
{
  "id": "/subscriptions/<subscriptionId>/resourceGroups/myResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/mymembername",
  "kind": null,
  "location": "eastus",
  "name": "mymembername",
  "properties": {
    "ConsortiumMemberDisplayName": "mymembername",
    "consortium": "myConsortiumName",
    "consortiumManagementAccountAddress": "0xfe5fbb9d1036298abf415282f52397ade5d5beef",
    "consortiumManagementAccountPassword": null,
    "consortiumRole": "ADMIN",
    "dns": "mymembername.blockchain.azure.com",
    "protocol": "Quorum",
    "provisioningState": "Succeeded",
    "userName": "mymembername",
    "validatorNodesSku": {
      "capacity": 2
    }
  },
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "S0",
    "tier": "Standard"
  },
  "type": "Microsoft.Blockchain/blockchainMembers"
}
```

## <a name="clean-up-resources"></a>Limpar recursos

Pode usar o membro de blockchain que criou para o próximo início rápido ou tutorial. Quando já não for necessário, pode eliminar os recursos a eliminar o `myResourceGroup` grupo de recursos que criou o serviço do Azure Blockchain.

Execute o seguinte comando para remover o grupo de recursos e todos os recursos relacionados.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Passos Seguintes

Agora que já criou um membro de blockchain, experimente uma ligar ao inícios rápidos de nó de transação para [Geth](connect-geth.md), [MetaMask](connect-metamask.md), ou [Truffle](connect-truffle.md).

> [!div class="nextstepaction"]
> [Utilizar Truffle para ligar a uma rede de um serviço do Azure Blockchain](connect-truffle.md)
