---
title: Crie um membro do Serviço Azure Blockchain usando o modelo de Gestor de Recursos Azure
description: Saiba como criar um membro do Serviço Azure Blockchain utilizando o modelo Azure Resource Manager.
services: azure-resource-manager
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs, references_regions
ms.date: 09/16/2020
ms.openlocfilehash: e9893336f2e6633519853aceecc945ee6bf0bf4b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91292766"
---
# <a name="quickstart-create-an-azure-blockchain-service-member-using-an-arm-template"></a>Quickstart: Criar um membro do Serviço Azure Blockchain usando um modelo ARM

Neste quickstart, você implementa um novo membro blockchain e consórcio no Azure Blockchain Service usando um modelo de Gestor de Recursos Azure (modelo ARM). Um membro do Azure Blockchain Service é um nó blockchain numa rede de blockchain de consórcio privado. Ao providenciar um membro, pode criar ou aderir a uma rede de consórcios. Precisa de pelo menos um membro para uma rede de consórcios. O número de membros blockchain necessários pelos participantes depende do seu cenário. Os participantes do consórcio podem ter um ou mais membros blockchain ou podem partilhar membros com outros participantes. Para obter mais informações sobre consórcios, consulte [o consórcio Azure Blockchain Service](consortium.md).

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Se o seu ambiente cumpre os pré-requisitos e se está familiarizado com a utilização de modelos ARM, selecione o botão **Implementar no Azure**. O modelo será aberto no portal do Azure.

[![Implementar no Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-blockchain-asaservice%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="review-the-template"></a>Rever o modelo

O modelo utilizado neste início rápido pertence aos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/201-blockchain-asaservice/).

:::code language="json" source="~/quickstart-templates/201-blockchain-asaservice/azuredeploy.json":::

Recursos azuis definidos no modelo:

* [**Microsoft.Blockchain/blockchainMembers**](/azure/templates/microsoft.blockchain/blockchainmembers)

## <a name="deploy-the-template"></a>Implementar o modelo

1. Selecione a ligação seguinte para iniciar sessão no Azure e abrir um modelo.

    [![Implementar no Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-blockchain-asaservice%2Fazuredeploy.json)

1. Especificar as definições para o membro do Serviço Azure Blockchain.

    Definições | Descrição
    --------|------------
    Subscrição | Selecione a subscrição Azure que pretende utilizar para o seu serviço. Se tiver várias subscrições, escolha a subscrição na qual o recurso é cobrado.
    Grupo de recursos | Crie um novo nome de grupo de recursos ou escolha um existente da sua subscrição.
    Region | Escolha uma região para criar o grupo de recursos. Todos os membros do consórcio devem estar no mesmo local. As localizações disponíveis para o destacamento são *westeurope, eastus, southeastasia, westeurope, northeurope, westus2*, e *japaneast.* As funcionalidades podem não estar disponíveis em algumas regiões. O Azure Blockchain Data Manager está disponível nas seguintes regiões de Azure: Leste dos EUA e Europa Ocidental.
    Nome do membro do BC | Escolha um nome único para o membro do Serviço Azure Blockchain. O nome do membro blockchain só pode conter letras e números minúsculos. O primeiro caráter tem de ser uma letra. O valor deve ter entre 2 e 20 caracteres de comprimento.
    Nome do consórcio | Introduza um nome exclusivo. Para obter mais informações sobre consórcios, consulte [o consórcio Azure Blockchain Service](consortium.md).
    Senha do membro | A palavra-passe para o nó de transação padrão do membro. Utilize a palavra-passe para autenticação básica ao ligar-se ao ponto final público do nó de transação padrão do membro blockchain.
    Senha de conta de gestão de consórcio | A palavra-passe da conta do consórcio é usada para encriptar a chave privada da conta Ethereum que é criada para o seu membro. É utilizado para a gestão de consórcios.
    Nível Sku | O nível de preços para o seu novo serviço. Escolha entre os níveis **Standard** e **Basic.** Utilize o nível *básico* para desenvolvimento, teste e prova de conceitos. Utilize o nível *Standard* para implantações de nível de produção. Utilize também o nível *Standard* se estiver a utilizar o Blockchain Data Manager ou a enviar um grande volume de transações privadas. A alteração do nível de preços entre o básico e o padrão após a criação dos membros não é apoiada.
    Nome Sku | A configuração do nó e o custo do seu novo serviço. Utilize **B0** para Básico e **S0** para Standard.
    Localização | Escolha um local para criar o membro. Por predefinição, a localização do grupo de recursos é utilizada `[resourceGroup().location]` . Todos os membros do consórcio devem estar no mesmo local. As localizações disponíveis para o destacamento são *westeurope, eastus, southeastasia, westeurope, northeurope, westus2*, e *japaneast.* As funcionalidades podem não estar disponíveis em algumas regiões. O Azure Blockchain Data Manager está disponível nas seguintes regiões de Azure: Leste dos EUA e Europa Ocidental.

1. Selecione **Review + Criar** para verificar e implementar o modelo.

  O portal Azure é usado aqui para implementar o modelo. Também pode utilizar a Azure PowerShell, Azure CLI e REST API. Para aprender outros métodos de implementação, consulte [os modelos de implementação](../../azure-resource-manager/templates/deploy-powershell.md).

## <a name="review-deployed-resources"></a>Revisão dos recursos implantados

Pode utilizar o portal Azure para visualizar detalhes do membro do Serviço Azure Blockchain implantado. No portal, aceda ao grupo de recursos que contém o seu membro do Serviço Azure Blockchain. Selecione o membro blockchain que criou.

![Implementado Azure Blockchain Membro resumo detalhes no portal Azure](./media/create-member-template/deployed-member.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Pode utilizar o membro blockchain que criou para o próximo quickstart ou tutorial. Quando já não é necessário, pode eliminar os recursos eliminando o grupo de recursos que criou para o arranque rápido.

Para eliminar o grupo de recursos:

1. No portal Azure, navegue para o **grupo De recursos** no painel de navegação esquerdo e selecione o grupo de recursos que pretende eliminar.
2. Selecione **Eliminar grupo de recursos**. Verifique a eliminação introduzindo o nome do grupo de recursos e selecione **Delete**.

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, você lançou um membro do Azure Blockchain Service e um novo consórcio. Experimente o próximo quickstart para usar o Kit de Desenvolvimento Azure Blockchain para o Ethereum para se ligar a um membro do Serviço Azure Blockchain.

> [!div class="nextstepaction"]
> [Use código de estúdio visual para ligar ao Serviço Azure Blockchain](connect-vscode.md)