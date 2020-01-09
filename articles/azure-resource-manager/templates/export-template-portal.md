---
title: Exportar modelo no portal do Azure
description: Use portal do Azure para exportar um modelo de Azure Resource Manager de recursos em sua assinatura.
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: 8cdba58a7a2ba998bac7fc0225ff957047cd69b0
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75477476"
---
# <a name="single-and-multi-resource-export-to-a-template-in-azure-portal"></a>Exportação única e de vários recursos para um modelo no portal do Azure

Para ajudar na criação de modelos de Azure Resource Manager, você pode exportar um modelo de recursos existentes. O modelo exportado ajuda a entender a sintaxe JSON e as propriedades que implantam seus recursos. Para automatizar implantações futuras, comece com o modelo exportado e modifique-o para seu cenário.

O Gerenciador de recursos permite que você escolha um ou mais recursos para exportar para um modelo. Você pode se concentrar exatamente nos recursos de que precisa no modelo.

Este artigo mostra como exportar modelos por meio do Portal. Você também pode usar [CLI do Azure](../management/manage-resource-groups-cli.md#export-resource-groups-to-templates), [Azure PowerShell](../management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)ou [API REST](/rest/api/resources/resourcegroups/exporttemplate).

## <a name="choose-the-right-export-option"></a>Escolha a opção de exportação à direita

Existem duas formas de exportar um modelo:

* **Exportar do recurso ou grupo de recursos**. Essa opção gera um novo modelo a partir de recursos existentes. O modelo exportado é um "instantâneo" do estado atual do grupo de recursos. Você pode exportar um grupo de recursos inteiro ou recursos específicos dentro desse grupo de recursos.

* **Exportar antes da implantação ou do histórico**. Essa opção recupera uma cópia exata de um modelo usado para implantação.

Dependendo da opção escolhida, os modelos exportados têm qualidades diferentes.

| Do grupo de recursos ou recurso | Antes da implantação ou do histórico |
| --------------------- | ----------------- |
| O modelo é um instantâneo do estado atual dos recursos. Ele inclui quaisquer alterações manuais feitas após a implantação. | O modelo mostra apenas o estado dos recursos no momento da implantação. As alterações manuais feitas após a implantação não são incluídas. |
| Você pode selecionar quais recursos de um grupo de recursos para exportar. | Todos os recursos para uma implantação específica estão incluídos. Você não pode escolher um subconjunto desses recursos ou adicionar recursos que foram adicionados em um momento diferente. |
| O modelo inclui todas as propriedades para os recursos, incluindo algumas propriedades que normalmente não seriam definidas durante a implantação. Talvez você queira remover ou limpar essas propriedades antes de reutilizar o modelo. | O modelo inclui apenas as propriedades necessárias para a implantação. O modelo está pronto para uso. |
| O modelo provavelmente não inclui todos os parâmetros necessários para reutilização. A maioria dos valores de propriedade são embutidos em código no modelo. Para reimplantar o modelo em outros ambientes, você precisa adicionar parâmetros que aumentam a capacidade de configurar os recursos.  Você pode cancelar a seleção de **incluir parâmetros** para que possa criar seus próprios parâmetros. | O modelo inclui parâmetros que facilitam a reimplantação em ambientes diferentes. |

Exporte o modelo de um grupo de recursos ou recurso, quando:

* Você precisa capturar as alterações nos recursos que foram feitos após a implantação original.
* Você deseja selecionar quais recursos são exportados.

Exporte o modelo antes da implantação ou do histórico, quando:

* Você deseja um modelo fácil de usar.
* Você não precisa incluir as alterações feitas após a implantação original.

## <a name="limitations"></a>Limitações

Ao exportar de um grupo de recursos ou recurso, o modelo exportado é gerado a partir dos [esquemas publicados](https://github.com/Azure/azure-resource-manager-schemas/tree/master/schemas) para cada tipo de recurso. Ocasionalmente, o esquema não tem a versão mais recente para um tipo de recurso. Verifique seu modelo exportado para ter certeza de que ele inclui as propriedades de que você precisa. Se necessário, edite o modelo exportado para usar a versão de API necessária.

O recurso exportar modelo não dá suporte à exportação de Azure Data Factory recursos. Para saber mais sobre como você pode exportar Data Factory recursos, consulte [copiar ou clonar um data Factory em Azure data Factory](https://aka.ms/exportTemplateViaAdf).

Para exportar recursos criados por meio do modelo de implantação clássico, você deve [migrá-los para o modelo de implantação do Gerenciador de recursos](https://aka.ms/migrateclassicresourcetoarm).

## <a name="export-template-from-a-resource-group"></a>Exportar modelo de um grupo de recursos

Para exportar um ou mais recursos de um grupo de recursos:

1. Selecione o grupo de recursos que contém os recursos que você deseja exportar.

1. Selecione um ou mais recursos selecionando as caixas de seleção.  Para selecionar tudo, marque a caixa de seleção à esquerda de **nome**. O item de menu **Exportar modelo** só fica habilitado depois que você seleciona pelo menos um recurso.

   ![Exportar todos os recursos](./media/export-template-portal/select-all-resources.png)

    Na captura de tela, somente a conta de armazenamento é selecionada.
1. Selecione **Exportar modelo**.

1. O modelo exportado é exibido e está disponível para download e implantação.

   ![Mostrar modelo](./media/export-template-portal/show-template.png)

   **Incluir parâmetros** é selecionado por padrão.  Quando selecionado, todos os parâmetros de modelo serão incluídos quando o modelo for gerado. Se você quiser criar seus próprios parâmetros, ative esta caixa de seleção para não incluí-los.

## <a name="export-template-from-a-resource"></a>Exportar modelo de um recurso

Para exportar um recurso:

1. Selecione o grupo de recursos que contém o recurso que você deseja exportar.

1. Selecione o recurso que você deseja exportar para abrir o recurso.

1. Para esse recurso, selecione **Exportar modelo** no painel esquerdo.

   ![Exportar recurso](./media/export-template-portal/export-single-resource.png)

1. O modelo exportado é exibido e está disponível para download e implantação. O modelo contém apenas o recurso único. **Incluir parâmetros** é selecionado por padrão.  Quando selecionado, todos os parâmetros de modelo serão incluídos quando o modelo for gerado. Se você quiser criar seus próprios parâmetros, ative esta caixa de seleção para não incluí-los.

## <a name="export-template-before-deployment"></a>Exportar modelo antes da implantação

1. Selecione o serviço do Azure que você deseja implantar.

1. Preencha os valores para o novo serviço.

1. Depois de passar na validação, mas antes de iniciar a implantação, selecione **baixar um modelo para automação**.

   ![Transferir modelo](./media/export-template-portal/download-before-deployment.png)

1. O modelo é exibido e está disponível para download e implantação.


## <a name="export-template-after-deployment"></a>Exportar modelo após a implantação

Você pode exportar o modelo que foi usado para implantar recursos existentes. O modelo obtido é exatamente aquele que foi usado para implantação.

1. Selecione o grupo de recursos que você deseja exportar.

1. Selecione o link em **implantações**.

   ![Selecionar histórico de implantação](./media/export-template-portal/select-deployment-history.png)

1. Selecione uma das implantações do histórico de implantação.

   ![Selecionar implantação](./media/export-template-portal/select-details.png)

1. Selecione **modelo**. O modelo usado para essa implantação é exibido e está disponível para download.

   ![Selecionar modelo](./media/export-template-portal/show-template-from-history.png)

## <a name="next-steps"></a>Passos seguintes

- Saiba como exportar modelos com [CLI do Azure](../management/manage-resource-groups-cli.md#export-resource-groups-to-templates), [Azure PowerShell](../management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)ou [API REST](/rest/api/resources/resourcegroups/exporttemplate).
- Para saber mais sobre a sintaxe do modelo do Resource Manager, consulte [entender a estrutura e a sintaxe dos modelos de Azure Resource Manager](template-syntax.md).
- Para saber como desenvolver modelos, consulte os tutoriais passo a [passo](/azure/azure-resource-manager/).
- Para exibir os esquemas de modelo de Azure Resource Manager, consulte [referência de modelo](/azure/templates/).
