---
title: Criar um serviço de Blockchain do Azure com a CLI do Azure
description: Utilize o serviço do Azure Blockchain para criar um membro de blockchain com a CLI do Azure.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/29/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: seal
manager: femila
ms.openlocfilehash: be5a8151f0de0a33db09194a7159aded6848c78a
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66416183"
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

## <a name="create-a-blockchain-member"></a>Crie um membro de blockchain

Crie um membro de blockchain no Azure Blockchain Service que executa o protocolo de contabilidade de quórum num consórcio de novo. Existem vários parâmetros e as propriedades que precisa passar. Substitua os parâmetros de exemplo pelos seus valores.

```azurecli-interactive
az resource create --resource-group myResourceGroup --name myblockchainmember --resource-type Microsoft.Blockchain/blockchainMembers --is-full-object --properties "{ \"location\": \"eastus\", \"properties\": {\"password\": \"strongMemberAccountPassword@1\", \"protocol\": \"Quorum\", \"consortium\": \"myConsortiumName\", \"consortiumManagementAccountPassword\": \"strongConsortiumManagementPassword@1\" }, \"sku\": { \"name\": \"S0\" } }"
```

| Parâmetro | Descrição |
|---------|-------------|
| **resource-group** | Nome do grupo de recursos onde os recursos de serviço do Azure Blockchain são criados. Utilize o grupo de recursos que criou na secção anterior.
| **name** | Um nome exclusivo que identifica o membro de blockchain de serviço do Azure Blockchain. O nome é utilizado para o endereço de ponto final público. Por exemplo, `myblockchainmember.blockchain.azure.com`.
| **localização** | Região do Azure onde o membro de blockchain é criado. Por exemplo, `eastus`. Escolha a localização que estiver mais próxima dos seus utilizadores ou das suas outras aplicações do Azure.
| **password** | A palavra-passe para o nó de transação o membro predefinido. Utilize a palavra-passe para a autenticação básica quando ligar ao ponto de extremidade no padrão transação nó pública do membro de blockchain.
| **consortium** | Nome do consortium para aderir ou criar.
| **consortiumAccountPassword** | A palavra-passe da conta de consortium também é conhecido como a palavra-passe da conta de membro. A palavra-passe da conta de membro é utilizada para encriptar a chave privada para a conta de Ethereum, que é criada para o membro. Utilizar a conta de membro e a palavra-passe da conta de membro para a gestão de consortium.
| **skuName** | Tipo de camada. Utilize S0 Standard e B0 para o básico.

Demora cerca de 10 minutos para criar o membro de blockchain e recursos de suporte.

## <a name="clean-up-resources"></a>Limpar recursos

Pode usar o membro de blockchain que criou para o próximo início rápido ou tutorial. Quando já não for necessário, pode eliminar os recursos a eliminar o `myResourceGroup` grupo de recursos que criou o serviço do Azure Blockchain.

Execute o seguinte comando para remover o grupo de recursos e todos os recursos relacionados.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Passos Seguintes

Agora que já criou um membro de blockchain, experimente uma os inícios rápidos de ligação para [Geth](connect-geth.md), [MetaMask](connect-metamask.md), ou [Truffle](connect-truffle.md).

> [!div class="nextstepaction"]
> [Utilizar Truffle para ligar a uma rede de um serviço do Azure Blockchain](connect-truffle.md)
