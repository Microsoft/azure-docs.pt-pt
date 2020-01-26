---
title: Criar um membro do Serviço Azure Blockchain - Azure CLI
description: Crie um membro do Serviço Azure Blockchain para um consórcio blockchain utilizando o Azure CLI.
ms.date: 01/23/2020
ms.topic: quickstart
ms.reviewer: janders
ms.openlocfilehash: 703444b6077c2301e1ffec77c8096fb76ddaa731
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759931"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-azure-cli"></a>Quickstart: Criar um membro blockchain Azure Blockchain Service usando o Azure CLI

Neste arranque rápido, você implementa um novo membro blockchain e consórcio no Azure Blockchain Service usando o Azure CLI.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta.

Para abrir o Cloud Shell, basta selecionar **Experimentar** no canto superior direito de um bloco de código. Também pode iniciar o Cloud Shell num separador do browser separado ao aceder a [https://shell.azure.com/bash](https://shell.azure.com/bash). Selecione **Copiar** para copiar os blocos de código, cole-o no Cloud Shell e prima Enter para executá-lo.

Se preferir instalar e utilizar o CLI localmente, este quickstart requer a versão Azure CLI 2.0.51 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, consulte [a instalação do Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](https://docs.microsoft.com/cli/azure/group). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*:

```azurecli-interactive
az group create \
                 --name myResourceGroup \
                 --location westus2
```

## <a name="create-a-blockchain-member"></a>Crie um membro de blockchain

Um membro do Azure Blockchain Service é um nó blockchain numa rede privada de blockchain de consórcio. Ao fornecer um membro, pode criar ou aderir a uma rede de consórcios. Precisa de pelo menos um membro para uma rede de consórcios. O número de membros blockchain necessários pelos participantes depende do seu cenário. Os participantes do consórcio podem ter um ou mais membros blockchain ou podem partilhar membros com outros participantes. Para obter mais informações sobre consórcios, consulte [o consórcio Azure Blockchain Service](consortium.md).

Existem vários parâmetros e propriedades que precisa de passar. Substitua os parâmetros de exemplo com os seus valores.

```azurecli-interactive
az resource create \
                    --resource-group myResourceGroup \
                    --name myblockchainmember \
                    --resource-type Microsoft.Blockchain/blockchainMembers \
                    --is-full-object \
                    --properties '{"location":"westus2", "properties":{"password":"strongMemberAccountPassword@1", "protocol":"Quorum", "consortium":"myConsortiumName", "consortiumManagementAccountPassword":"strongConsortiumManagementPassword@1"}, "sku":{"name":"S0"}}'
```

| Parâmetro | Descrição |
|---------|-------------|
| **resource-group** | Nome de grupo de recursos onde os recursos do Serviço Azure Blockchain são criados. Utilize o grupo de recursos que criou na secção anterior.
| **name** | Um nome único que identifica o seu membro blockchain Azure Blockchain Service. O nome é usado para o endereço final do público. Por exemplo, `myblockchainmember.blockchain.azure.com`.
| **localização** | Região azul onde o membro blockchain é criado. Por exemplo, `westus2`. Escolha a localização que estiver mais próxima dos seus utilizadores ou das suas outras aplicações do Azure.
| **password** | A palavra-passe para o nó de transação padrão do membro. Utilize a palavra-passe para autenticação básica quando ligar ao ponto final de transação padrão do membro blockchain.
| **consortium** | Nome do consórcio para aderir ou criar. Para obter mais informações sobre consórcios, consulte [o consórcio Azure Blockchain Service](consortium.md).
| **consortiumAccountPassword** | A palavra-passe da conta do consórcio também é conhecida como a palavra-passe da conta membro. A palavra-passe da conta membro é usada para encriptar a chave privada da conta Ethereum que é criada para o seu membro. Utiliza a conta membro e a palavra-passe da conta membro para a gestão do consórcio.
| **skuName** | Tipo nível. Utilize S0 para Standard e B0 para Básico.

Leva cerca de 10 minutos para criar o membro blockchain e apoiar recursos.

## <a name="clean-up-resources"></a>Limpar recursos

Pode utilizar o membro blockchain que criou para o próximo quickstart ou tutorial. Quando já não for necessário, pode eliminar os recursos eliminando o grupo de recursos `myResourceGroup` que criou para o arranque rápido.

Executar o seguinte comando para remover o grupo de recursos e todos os recursos relacionados.

```azurecli-interactive
az group delete \
                 --name myResourceGroup \
                 --yes
```

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, você implantou um membro do Serviço Azure Blockchain e um novo consórcio. Experimente o próximo quickstart a usar o Kit de Desenvolvimento Azure Blockchain para o Ethereum para ligar a um membro do Serviço Azure Blockchain.

> [!div class="nextstepaction"]
> [Use o Código do Estúdio Visual para ligar ao Serviço Azure Blockchain](connect-vscode.md)
