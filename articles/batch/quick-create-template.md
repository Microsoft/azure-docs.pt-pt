---
title: Azure Quickstart - Criar uma conta batch - Modelo de Gestor de Recursos Azure
description: Este quickstart mostra como criar uma conta Batch utilizando um modelo ARM.
ms.topic: quickstart
ms.date: 08/17/2020
ms.custom: subject-armqs
ms.openlocfilehash: fc6f1537b32c8ced2cfc2bf09f16106f791001fc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97106585"
---
# <a name="quickstart-create-a-batch-account-by-using-arm-template"></a>Quickstart: Criar uma conta batch utilizando o modelo ARM

Você precisa de uma conta Batch para criar recursos compute (conjuntos de nós de computação) e trabalhos de Batch. Pode ligar uma conta de Armazenamento Azure à sua conta Batch, que é útil para implementar aplicações e armazenar dados de entradas e saídas para a maioria das cargas de trabalho no mundo real. Este quickstart mostra como usar um modelo de Gestor de Recursos Azure (modelo ARM) para criar uma conta Batch, incluindo armazenamento. Depois de concluir este início rápido, irá compreender os conceitos principais do serviço do Batch e estará pronto para experimentar o Batch com cargas de trabalho mais realistas em grande escala.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se o seu ambiente cumpre os pré-requisitos e se está familiarizado com a utilização de modelos ARM, selecione o botão **Implementar no Azure**. O modelo será aberto no portal do Azure.

[![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-batchaccount-with-storage%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

Deve ter uma subscrição ativa do Azure.

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="review-the-template"></a>Rever o modelo

O modelo utilizado neste início rápido pertence aos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-batchaccount-with-storage/).

:::code language="json" source="~/quickstart-templates/101-batchaccount-with-storage/azuredeploy.json":::

Dois recursos Azure são definidos no modelo:

- [Microsoft.Storage/storageAcounts:](/azure/templates/microsoft.storage/storageaccounts)Cria uma conta de armazenamento.
- [Microsoft.Batch/batchAcounts](/azure/templates/microsoft.batch/batchaccounts): Cria uma conta Batch.

## <a name="deploy-the-template"></a>Implementar o modelo

1. Selecione a imagem seguinte para iniciar sessão no Azure e abrir um modelo. O modelo cria uma conta Azure Batch e uma conta de armazenamento.

   [![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-batchaccount-with-storage%2Fazuredeploy.json)

1. Selecione ou introduza os seguintes valores.

   ![Modelo de gestor de recursos, criação de conta de lote, implementar portal](media/quick-create-template/batch-template.png)

   - **Subscrição**: selecione uma subscrição do Azure.
   - **Grupo de recursos**: selecione **Criar novo,** insira um nome único para o grupo de recursos e, em seguida, clique em **OK**.
   - **Localização**: selecione uma localização. Por exemplo, **E.U.A. Central**.
   - **Nome da conta do lote**: Deixe o valor predefinido.
   - **Storage Accountsku**: selecione um tipo de conta de armazenamento. Por exemplo, **Standard_LRS.**
   - **Localização**: Deixe o padrão para que os recursos fiquem no mesmo local que o seu grupo de recursos.
   - Concordo com os termos e condições acima indicados: **Selecione**.

1. Selecione **Comprar**.

Após alguns minutos, deverá ver uma notificação de que a conta Batch foi criada com sucesso.

Neste exemplo, o portal Azure é utilizado para implantar o modelo. Além do portal Azure, também pode utilizar o Azure PowerShell, Azure CLI e REST API. Para aprender outros métodos de implementação, consulte [os modelos de implementação](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="validate-the-deployment"></a>Validar a implementação

Pode validar a implementação no portal Azure navegando para o grupo de recursos que criou. No ecrã **de visão geral,** confirme que a conta Batch e a conta de armazenamento estão presentes.

## <a name="clean-up-resources"></a>Limpar os recursos

Se pretender continuar a trabalhar com [tutoriais subsequentes,](./tutorial-parallel-dotnet.md)poderá querer deixar estes recursos no lugar. Ou, se já não precisar deles, pode [eliminar o grupo de recursos](../azure-resource-manager/management/delete-resource-group.md?tabs=azure-portal#delete-resource-group), que também eliminará a conta Batch e a conta de armazenamento que criou.

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou uma conta Batch e uma conta de armazenamento. Para saber mais acerca do Azure Batch, avance para os tutoriais do Azure Batch.

> [!div class="nextstepaction"]
> [Azure Batch tutorials](./tutorial-parallel-dotnet.md) (Tutoriais do Azure Batch)
