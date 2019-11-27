---
title: Criar um membro do serviço Blockchain do Azure-CLI do Azure
description: Crie um membro do serviço Blockchain do Azure para um consórcio Blockchain usando o CLI do Azure.
ms.date: 11/20/2019
ms.topic: quickstart
ms.reviewer: janders
ms.openlocfilehash: 6a9673431c3da21b3ce645b9aff30506be1012f3
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455957"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-azure-cli"></a>Início rápido: criar um membro Blockchain do serviço Blockchain do Azure usando CLI do Azure

Neste guia de início rápido, você implanta um novo membro do blockchain e o consórcio no serviço Blockchain do Azure usando o CLI do Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta.

Para abrir o Cloud Shell, basta selecionar **Experimentar** no canto superior direito de um bloco de código. Também pode iniciar o Cloud Shell num separador do browser separado ao aceder a [https://shell.azure.com/bash](https://shell.azure.com/bash). Selecione **Copiar** para copiar os blocos de código, cole-o no Cloud Shell e prima Enter para executá-lo.

Se você preferir instalar e usar a CLI localmente, este início rápido exigirá CLI do Azure versão 2.0.51 ou posterior. Executar `az --version` para localizar a versão. Se você precisar instalar ou atualizar, consulte [instalar CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](https://docs.microsoft.com/cli/azure/group). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*:

```azurecli-interactive
az group create \
                 --name myResourceGroup \
                 --location westus2
```

## <a name="create-a-blockchain-member"></a>Crie um membro de blockchain

Crie um membro blockchain no serviço Blockchain do Azure que executa o protocolo de razão de quorum em um novo consórcio. Há vários parâmetros e propriedades que você precisa passar. Substitua os parâmetros de exemplo pelos seus valores.

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
| **grupo de recursos** | Nome do grupo de recursos em que os recursos do serviço Blockchain do Azure são criados. Use o grupo de recursos que você criou na seção anterior.
| **nomes** | Um nome exclusivo que identifica o membro Blockchain do serviço Blockchain do Azure. O nome é usado para o endereço do ponto de extremidade público. Por exemplo, `myblockchainmember.blockchain.azure.com`.
| **localização** | Região do Azure em que o membro blockchain é criado. Por exemplo, `westus2`. Escolha a localização que estiver mais próxima dos seus utilizadores ou das suas outras aplicações do Azure.
| **la** | A senha para o nó de transação padrão do membro. Use a senha para autenticação básica ao se conectar ao ponto de extremidade público do nó de transação padrão do membro do blockchain.
| **Associação** | Nome do consórcio para ingressar ou criar.
| **consortiumAccountPassword** | A senha da conta do Consortium também é conhecida como a senha da conta do membro. A senha da conta de membro é usada para criptografar a chave privada para a conta Ethereum que é criada para o seu membro. Use a conta de membro e a senha da conta do membro para o gerenciamento do consórcio.
| **skuName** | Tipo de camada. Use S0 para Standard e B0 para Basic.

Leva cerca de 10 minutos para criar o membro blockchain e os recursos de suporte.

## <a name="clean-up-resources"></a>Limpar recursos

Você pode usar o membro blockchain criado para o próximo início rápido ou tutorial. Quando não for mais necessário, você poderá excluir os recursos excluindo o `myResourceGroup` grupo de recursos criado para o guia de início rápido.

Execute o comando a seguir para remover o grupo de recursos e todos os recursos relacionados.

```azurecli-interactive
az group delete \
                 --name myResourceGroup \
                 --yes
```

## <a name="next-steps"></a>Passos Seguintes

Neste guia de início rápido, você implantou um membro do serviço Blockchain do Azure e um novo consórcio. Experimente o próximo início rápido para usar o kit de desenvolvimento do Azure Blockchain para Ethereum para anexar a um consórcio no serviço Blockchain do Azure.

> [!div class="nextstepaction"]
> [Usar Visual Studio Code para se conectar ao serviço Blockchain do Azure](connect-vscode.md)
