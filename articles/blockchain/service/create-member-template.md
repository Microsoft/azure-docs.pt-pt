---
title: Crie um membro do Serviço Blockchain Azure utilizando o modelo de Gestor de Recursos Azure
description: Aprenda a criar um membro do Serviço Blockchain Azure utilizando o modelo do Gestor de Recursos Azure.
services: azure-resource-manager
author: PatAltimore
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: patricka
ms.date: 04/22/2020
ms.openlocfilehash: 8fd5a1775a6a217ce9dd8c80439dd2c54f9cd727
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82087598"
---
# <a name="create-an-azure-blockchain-service-member-using-an-azure-resource-manager-template"></a>Crie um membro do Serviço Blockchain Azure usando um modelo de Gestor de Recursos Azure

Neste arranque rápido, você implementa um novo membro blockchain e consórcio no Azure Blockchain Service usando um modelo de Gestor de Recursos Azure.

Um membro do Azure Blockchain Service é um nó blockchain numa rede privada de blockchain de consórcio. Ao fornecer um membro, pode criar ou aderir a uma rede de consórcios. Precisa de pelo menos um membro para uma rede de consórcios. O número de membros blockchain necessários pelos participantes depende do seu cenário. Os participantes do consórcio podem ter um ou mais membros blockchain ou podem partilhar membros com outros participantes. Para obter mais informações sobre consórcios, consulte [o consórcio Azure Blockchain Service](consortium.md).

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Se não tiver uma subscrição Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Nenhum.

## <a name="review-the-template"></a>Reveja o modelo

O modelo utilizado neste quickstart é de [modelos Azure Quickstart](https://github.com/Azure/azure-quickstart-templates/blob/master/201-blockchain-asaservice/).

[!code-json[<Azure Resource Manager template create blockchain member>](~/quickstart-templates/201-blockchain-asaservice/azuredeploy.json)]

Recursos azure definidos no modelo:

* [**Microsoft.Blockchain/blockchainMembers**](https://docs.microsoft.com/azure/templates/microsoft.blockchain/blockchainmembers)

## <a name="deploy-the-template"></a>Implementar o modelo

1. Selecione o seguinte link para iniciar sessão no Azure e abra um modelo.

    [![Implementar no Azure](./media/create-member-template/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-blockchain-asaservice%2Fazuredeploy.json)

1. Especifique as definições para o membro do Serviço Blockchain Azure.

    Definição | Descrição
    --------|------------
    Subscrição | Selecione a subscrição Azure que pretende utilizar para o seu serviço. Se tiver várias subscrições, escolha a subscrição na qual o recurso é cobrado.
    Grupo de recursos | Crie um novo nome de grupo de recursos ou escolha um existente a partir da sua subscrição.
    Localização | Escolha um local para criar o membro. Todos os membros do consórcio devem estar no mesmo local. As localizações disponíveis para o destacamento são *a Europa Ocidental, Leste, Sudeste, Europa Ocidental, Noroeste da Europa, Westus2*e *Japãoeast.*
    Nome do membro | Escolha um nome único para o membro do Serviço Azure Blockchain. O nome do membro blockchain só pode conter letras minúsculas e números. O primeiro caráter tem de ser uma letra. O valor deve ter entre 2 e 20 caracteres de comprimento.
    Nome do consórcio | Introduza um nome exclusivo. Para obter mais informações sobre consórcios, consulte [o consórcio Azure Blockchain Service](consortium.md).
    Senha do membro | A palavra-passe da conta membro é usada para encriptar a chave privada da conta Ethereum que é criada para o seu membro. Utiliza a conta membro e a palavra-passe da conta membro para a gestão do consórcio.
    Sku tier | O nível de preços para o seu novo serviço. Escolha entre os níveis **Standard** e **Basic.** Utilize o nível *Básico* para desenvolvimento, teste e prova de conceitos. Utilize o nível *Standard* para implementações de qualidade de produção. Também deve utilizar o nível *Standard* se estiver a utilizar o Gestor de Dados blockchain ou a enviar um elevado volume de transações privadas. A alteração do nível de preços entre a criação de base e a norma após a criação dos membros não é suportada.
    Nome Sku | A configuração do nó e o custo do seu novo serviço.

1. **Selecione Comprar** para implementar o modelo.

  O portal Azure é usado aqui para implementar o modelo. Também pode utilizar a API Azure PowerShell, Azure CLI e REST. Para aprender outros métodos de implementação, consulte [os modelos de implantação](../../azure-resource-manager/templates/deploy-powershell.md).

## <a name="clean-up-resources"></a>Limpar recursos

Pode utilizar o membro blockchain que criou para o próximo quickstart ou tutorial. Quando já não for necessário, pode eliminar os recursos eliminando o grupo de recursos que criou para o arranque rápido.

Para eliminar o grupo de recursos:

1. No portal Azure, navegue para o **grupo Recurso** no painel de navegação à esquerda e selecione o grupo de recursos que pretende eliminar.
2. Selecione **Eliminar grupo de recursos**. Verifique a eliminação inserindo o nome do grupo de recursos e selecione **Delete**.

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, você implantou um membro do Serviço Azure Blockchain e um novo consórcio. Experimente o próximo quickstart a usar o Kit de Desenvolvimento Azure Blockchain para o Ethereum para ligar a um membro do Serviço Azure Blockchain.

> [!div class="nextstepaction"]
> [Use o Código do Estúdio Visual para ligar ao Serviço Azure Blockchain](connect-vscode.md)