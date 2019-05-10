---
title: Exportar o modelo Azure Resource Manager com o portal do Azure
description: Utilize o portal do Azure para exportar um modelo Azure Resource Manager a partir de recursos na sua subscrição.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 05/09/2019
ms.author: tomfitz
ms.openlocfilehash: ea9499da3dac67635a48704f439f6592c6ed467e
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/09/2019
ms.locfileid: "65515388"
---
# <a name="single-and-multi-resource-export-to-template-in-azure-portal"></a>Exportação única e vários recurso para o modelo no portal do Azure

Para auxiliar a criação de modelos Azure Resource Manager, pode exportar um modelo a partir de recursos existentes. O modelo exportado ajuda-o a compreender a sintaxe JSON e propriedades que implementar os recursos. Para automatizar Implantações futuras, comece com o modelo exportado e modificá-lo para o seu cenário.

O Resource Manager permite-lhe escolher um ou mais recursos para exportação para um modelo. Pode focalizar exatamente os recursos que necessários no modelo.

## <a name="choose-the-right-export-option"></a>Escolha a opção de exportação certo

Existem duas formas de exportar um modelo:

* **Exportar a partir do grupo de recursos ou recurso**. Esta opção gera um novo modelo a partir de recursos existentes. O modelo exportado é um "instantâneo" do estado atual do grupo de recursos. Pode exportar um grupo de recursos inteiro ou recursos específicos dentro do grupo de recursos.

* **Exportar antes da implantação ou a partir do histórico**. Esta opção recupera uma cópia exata de um modelo utilizado para a implementação.

Dependendo da opção escolhida, os Modelos exportados tem qualidades diferentes.

| Do grupo de recursos ou recursos | Antes da implantação ou a partir do histórico |
| --------------------- | ----------------- |
| Modelo é instantâneo do estado atual dos recursos. Ele inclui quaisquer alterações manuais efetuadas após a implementação. | Modelo mostra apenas o estado dos recursos no momento da implantação. Quaisquer alterações manuais efetuadas após a implementação não estão incluídos. |
| Pode selecionar quais os recursos de um grupo de recursos para exportar. | Todos os recursos para uma implementação específica são incluídos. Não é possível escolher um subconjunto desses recursos ou adicionar recursos que foram adicionados ao mesmo tempo diferente. |
| Modelo inclui todas as propriedades para os recursos, incluindo algumas propriedades que normalmente não faria definir durante a implementação. Pode querer remover ou limpar estas propriedades antes de reutilizar o modelo. | Modelo inclui apenas as propriedades necessárias para a implementação. O modelo está pronto para utilizar. |
| Modelo provavelmente não inclui todos os parâmetros que necessários para reutilização. A maioria dos valores de propriedade são codificados no modelo. Para Reimplementar o modelo em outros ambientes, terá de adicionar parâmetros que aumentam a capacidade de configurar os recursos. | Modelo inclui parâmetros que facilitam a voltar a implementar em ambientes diferentes. |

Exportar o modelo a partir de um grupo de recursos ou recurso, quando:

* Precisa capturar as alterações aos recursos que foram feitas após a implementação original.
* Pretende selecionar quais os recursos são exportados.

Exportar o modelo antes da implantação ou a partir do histórico, quando:

* Quer um modelo de fácil reutilização.
* Não precisa de incluem as alterações efetuadas após a implementação original.

## <a name="export-template-from-resource-group"></a>Exportar modelo de grupo de recursos

Para exportar um ou mais recursos de um grupo de recursos:

1. Selecione o grupo de recursos que contém os recursos que pretende exportar.

1. Para exportar todos os recursos no grupo de recursos, Selecionar tudo e, em seguida **exportar modelo**. O **exportar modelo** opção torna-se ativada apenas depois de selecionar pelo menos um recurso.

   ![Exportar todos os recursos](./media/export-template-portal/select-all-resources.png)

1. Ao escolher recursos específicos para a exportação, selecione as caixas de verificação junto a esses recursos. Em seguida, selecione **exportar modelo**.

   ![Selecionar recursos a exportar](./media/export-template-portal/select-resources.png)

1. O modelo exportado é apresentado e está disponível para download.

   ![Mostrar modelo](./media/export-template-portal/show-template.png)

## <a name="export-template-from-resource"></a>Exportar modelo de recurso

Para exportar um recurso:

1. Selecione o grupo de recursos que contém o recurso que pretende exportar.

1. Selecione o recurso para exportar.

   ![Selecionar o recurso](./media/export-template-portal/select-link-resource.png)

1. Para esse recurso, selecione **exportar modelo** no painel esquerdo.

   ![Exportar recursos](./media/export-template-portal/export-single-resource.png)

1. O modelo exportado é apresentado e está disponível para download. O modelo contém apenas o único recurso.

## <a name="export-template-before-deployment"></a>Exportar modelo antes da implantação

1. Selecione o serviço do Azure que pretende implementar.

1. Preencha os valores para o novo serviço.

1. Após passar na validação, mas antes de iniciar a implementação, selecione **transferir um modelo para automatização**.

   ![Transferir modelo](./media/export-template-portal/download-before-deployment.png)

1. O modelo é exibido e está disponível para download.

   ![Mostrar modelo](./media/export-template-portal/show-template-before-deployment.png)

## <a name="export-template-after-deployment"></a>Exportar modelo após a implementação

Pode exportar o modelo que foi utilizado para implementar recursos existentes. O modelo, que obtém é exatamente o que foi utilizado para implementação.

1. Selecione o grupo de recursos que pretende exportar.

1. Selecione a ligação em **implementações**.

   ![Selecione o histórico de implementação](./media/export-template-portal/select-deployment-history.png)

1. Selecione uma das implementações do histórico de implementação.

   ![Selecione a implementação](./media/export-template-portal/select-details.png)

1. Selecione **modelo**. O modelo utilizado para esta implementação é apresentado e está disponível para download.

   ![Seleccionar modelo](./media/export-template-portal/show-template-from-history.png)

## <a name="next-steps"></a>Passos Seguintes

- Para obter o Azure Resource Manager, veja [descrição geral do Azure Resource Manager](./resource-group-overview.md).
- Para obter a sintaxe do modelo do Resource Manager, veja [compreender a estrutura e a sintaxe de modelos Azure Resource Manager](./resource-group-authoring-templates.md).
- Para saber como desenvolver modelos, veja a [tutoriais passo a passo](/azure/azure-resource-manager/).
- Para ver os esquemas de modelo do Azure Resource Manager, consulte [referência de modelo](/azure/templates/).