---
title: Criar um membro do Serviço Azure Blockchain - Azure CLI
description: Crie um membro do Serviço Azure Blockchain para um consórcio blockchain usando o Azure CLI.
ms.date: 07/23/2020
ms.topic: quickstart
ms.reviewer: ravastra
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 3442c3b6023edcde97aabcb13e91120ba6811027
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91323078"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-azure-cli"></a>Quickstart: Criar um membro blockchain do Azure Blockchain Service usando O Azure CLI

Neste quickstart, você implementa um novo membro blockchain e consórcio no Azure Blockchain Service usando Azure CLI.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Nenhum.

## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta.

Para abrir o Cloud Shell, basta selecionar **Experimente** no canto superior direito de um bloco de código. Também pode lançar cloud Shell num separador de navegador indo para [https://shell.azure.com/bash](https://shell.azure.com/bash) . Selecione **Copiar** para copiar os blocos de código, cole-o no Cloud Shell e prima Enter para executá-lo.

Se preferir instalar e utilizar o CLI localmente, este arranque rápido requer a versão 2.0.51 do CLI Azure ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, consulte [a instalação do Azure CLI](/cli/azure/install-azure-cli).

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

1. Crie um grupo de recursos.

    O Serviço Azure Blockchain, como todos os recursos da Azure, deve ser implantado num grupo de recursos. Os grupos de recursos permitem organizar e gerir recursos relacionados do Azure.

    Para este arranque rápido, crie um grupo de recursos chamado _myResourceGroup_ na localização _este_ com o seguinte [grupo az criar](/cli/azure/group#az-group-create) comando:

    ```azurecli-interactive
    az group create \
                     --name "myResourceGroup" \
                     --location "eastus"
    ```

## <a name="create-a-blockchain-member"></a>Criar um membro blockchain

Um membro do Azure Blockchain Service é um nó blockchain numa rede de blockchain de consórcio privado. Ao providenciar um membro, pode criar ou aderir a uma rede de consórcios. Precisa de pelo menos um membro para uma rede de consórcios. O número de membros blockchain necessários pelos participantes depende do seu cenário. Os participantes do consórcio podem ter um ou mais membros blockchain ou podem partilhar membros com outros participantes. Para obter mais informações sobre consórcios, consulte [o consórcio Azure Blockchain Service](consortium.md).

Existem vários parâmetros e propriedades que precisa passar. Substitua os parâmetros de exemplo pelos seus valores.

```azurecli-interactive
az blockchain member create \
                            --resource-group "MyResourceGroup" \
                            --name "myblockchainmember" \
                            --location "eastus" \
                            --password "strongMemberAccountPassword@1" \
                            --protocol "Quorum" \
                            --consortium "myconsortium" \
                            --consortium-management-account-password "strongConsortiumManagementPassword@1" \
                            --sku "Basic"
```

| Parâmetro | Descrição |
|---------|-------------|
| **grupo de recursos** | Nome do grupo de recursos onde são criados os recursos do Serviço Azure Blockchain. Utilize o grupo de recursos que criou na secção anterior.
| **nome** | Um nome único que identifica o seu membro blockchain do Azure Blockchain Service. O nome é usado para o endereço de ponto final público. Por exemplo, `myblockchainmember.blockchain.azure.com`.
| **localização** | Região de Azure onde o membro blockchain é criado. Por exemplo, `westus2`. Escolha a localização que estiver mais próxima dos seus utilizadores ou das suas outras aplicações do Azure. As funcionalidades podem não estar disponíveis em algumas regiões. O Azure Blockchain Data Manager está disponível nas seguintes regiões de Azure: Leste dos EUA e Europa Ocidental.
| **palavra-passe** | A palavra-passe para o nó de transação padrão do membro. Utilize a palavra-passe para autenticação básica ao ligar-se ao ponto final público do nó de transação padrão do membro blockchain.
| **protocolo** | Protocolo blockchain. Atualmente, o protocolo *Qurum* é suportado.
| **consórcio** | Nome do consórcio para aderir ou criar. Para obter mais informações sobre consórcios, consulte [o consórcio Azure Blockchain Service](consortium.md).
| **consórcio-gestão-palavra-passe-conta** | A senha da conta do consórcio também é conhecida como a palavra-passe da conta do membro. A palavra-passe da conta do membro é usada para encriptar a chave privada da conta Ethereum que é criada para o seu membro. Utilize a conta de membro e a palavra-passe da conta do membro para a gestão do consórcio.
| **sku** | Tipo de nível. *Padrão* ou *Básico*. Utilize o nível *básico* para desenvolvimento, teste e prova de conceitos. Utilize o nível *Standard* para implantações de nível de produção. Utilize também o nível *Standard* se estiver a utilizar o Blockchain Data Manager ou a enviar um grande volume de transações privadas. A alteração do nível de preços entre o básico e o padrão após a criação dos membros não é apoiada.

Leva cerca de 10 minutos para criar o membro blockchain e recursos de apoio.

## <a name="clean-up-resources"></a>Limpar os recursos

Pode utilizar o membro blockchain que criou para o próximo quickstart ou tutorial. Quando já não é necessário, pode eliminar os recursos eliminando o `myResourceGroup` grupo de recursos que criou para o arranque rápido.

Executar o seguinte comando para remover o grupo de recursos e todos os recursos relacionados.

```azurecli-interactive
az group delete \
                 --name myResourceGroup \
                 --yes
```

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, você lançou um membro do Azure Blockchain Service e um novo consórcio. Experimente o próximo quickstart para usar o Kit de Desenvolvimento Azure Blockchain para o Ethereum para se ligar a um membro do Serviço Azure Blockchain.

> [!div class="nextstepaction"]
> [Use código de estúdio visual para ligar ao Serviço Azure Blockchain](connect-vscode.md)
