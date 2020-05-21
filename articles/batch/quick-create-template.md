---
title: Azure Quickstart - Criar uma conta de Lote - Modelo de Gestor de Recursos Azure
description: Aprenda mais rapidamente a executar uma tarefa do Batch com a CLI do Azure. Crie e gerea recursos Azure a partir da linha de comando ou em scripts.
ms.topic: quickstart
ms.date: 05/19/2020
ms.custom: subject-armqs
ms.openlocfilehash: 535c8c34ea7af8e6bc56c3ecfe564de4c1b2bc54
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83694230"
---
# <a name="quickstart-create-a-batch-account-by-using-azure-resource-manager-template"></a>Quickstart: Criar uma conta de Lote utilizando o modelo de Gestor de Recursos Azure

Você precisa de uma conta Batch para criar recursos computacionais (piscinas de nós de computação) e trabalhos de Lote. Pode ligar uma conta de Armazenamento Azure à sua conta Batch, que é útil para implementar aplicações e armazenar dados de entrada e saída para a maioria das cargas de trabalho no mundo real.

Este quickstart mostra como usar um modelo de Gestor de Recursos Azure para criar uma conta Batch, incluindo armazenamento. Depois de concluir este início rápido, irá compreender os conceitos principais do serviço do Batch e estará pronto para experimentar o Batch com cargas de trabalho mais realistas em grande escala.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Pré-requisitos

Deve ter uma subscrição azure ativa.

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

### <a name="review-the-template"></a>Reveja o modelo

O modelo utilizado neste quickstart é de [modelos Azure Quickstart](https://github.com/Azure/azure-quickstart-templates/tree/master/101-batchaccount-with-storage).

:::code language="json" source="~/quickstart-templates/101-batchaccount-with-storage/azuredeploy.json":::

Dois recursos Azure são definidos no modelo:

- [Microsoft.Storage/storageAccounts](https://docs.microsoft.com/azure/templates/microsoft.storage/storageaccounts): Cria uma conta de armazenamento.
- [Microsoft.Batch/batchAccounts](https://docs.microsoft.com/azure/templates/microsoft.batch/batchaccounts): Cria uma conta de Lote.

### <a name="deploy-the-template"></a>Implementar o modelo

1. Selecione a imagem seguinte para iniciar sessão no Azure e abrir um modelo. O modelo cria uma conta Azure Batch e uma conta de armazenamento.

   [![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-batchaccount-with-storage%2Fazuredeploy.json)

1. Selecione ou introduza os seguintes valores.

   ![Modelo de Gestor de Recursos, Criação de conta de lote, portal de implantação](media/quick-create-template/batch-template.png)

   - **Subscrição**: selecione uma subscrição do Azure.
   - **Grupo de recursos**: selecione **Criar novo,** introduza um nome único para o grupo de recursos e, em seguida, clique **OK**.
   - **Localização**: selecione uma localização. Por exemplo, **Centro dos EUA.**
   - **Nome da conta**do lote : Deixe o valor predefinido.
   - **Conta de armazenamento**: selecione um tipo de conta de armazenamento. Por exemplo, **Standard_LRS.**
   - **Localização**: Deixe o padrão para que os recursos fiquem no mesmo local que o seu grupo de recursos.
   - Concordo com os termos e condições acima referidos: **Selecione**.

1. Selecione **Comprar**.

Após alguns minutos, deve ver uma notificação de que a conta Batch foi criada com sucesso.

Neste exemplo, o portal Azure é usado para implantar o modelo. Além do portal Azure, também pode utilizar o Azure PowerShell, Azure CLI e REST API. Para aprender outros métodos de implementação, consulte [os modelos de implantação](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="validate-the-deployment"></a>Validar a implementação

Pode validar a implantação no portal Azure navegando para o grupo de recursos que criou. No ecrã **'Visão Geral',** confirme que a conta Batch e a conta de armazenamento estão presentes.

## <a name="clean-up-resources"></a>Limpar recursos

Se pretende continuar a trabalhar com [tutoriais subsequentes,](./tutorial-parallel-dotnet.md)poderá desejar deixar esses recursos no lugar. Ou, se já não precisar deles, pode [eliminar o grupo de recursos](../azure-resource-manager/management/delete-resource-group.md?tabs=azure-portal#delete-resource-group), que também eliminará a conta 'Lote' e a conta de armazenamento que criou.

## <a name="next-steps"></a>Próximos passos

Neste arranque rápido, criou uma conta Batch e uma conta de armazenamento. Para saber mais acerca do Azure Batch, avance para os tutoriais do Azure Batch.

> [!div class="nextstepaction"]
> [Azure Batch tutorials](./tutorial-parallel-dotnet.md) (Tutoriais do Azure Batch)
