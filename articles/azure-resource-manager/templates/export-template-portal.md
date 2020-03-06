---
title: Modelo de exportação no portal Azure
description: Utilize o portal Azure para exportar um modelo de Gestor de Recursos Azure a partir de recursos na sua subscrição.
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: 8cdba58a7a2ba998bac7fc0225ff957047cd69b0
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78384961"
---
# <a name="single-and-multi-resource-export-to-a-template-in-azure-portal"></a>Exportação de recursos únicos e múltiplos para um modelo no portal Azure

Para ajudar na criação de modelos do Gestor de Recursos Azure, pode exportar um modelo a partir de recursos existentes. O modelo exportado ajuda-o a compreender a sintaxe jSON e propriedades que implantam os seus recursos. Para automatizar futuras implementações, comece com o modelo exportado e modifique-o para o seu cenário.

O Gestor de Recursos permite-lhe escolher um ou mais recursos para exportar para um modelo. Pode focar-se exatamente nos recursos necessários no modelo.

Este artigo mostra como exportar modelos através do portal. Também pode utilizar [o Azure CLI,](../management/manage-resource-groups-cli.md#export-resource-groups-to-templates) [O PowerShell Azure](../management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)ou o [REST API](/rest/api/resources/resourcegroups/exporttemplate).

## <a name="choose-the-right-export-option"></a>Escolha a opção de exportação certa

Existem duas formas de exportar um modelo:

* **Exportação de grupo de recursos ou recursos.** Esta opção gera um novo modelo a partir de recursos existentes. O modelo exportado é um "instantâneo" do estado atual do grupo de recursos. Você pode exportar um grupo de recursos inteiro ou recursos específicos dentro desse grupo de recursos.

* **Exportar antes da implantação ou da história.** Esta opção recupera uma cópia exata de um modelo utilizado para a implantação.

Dependendo da opção que escolher, os modelos exportados têm diferentes qualidades.

| Do grupo de recursos ou recursos | Antes da implantação ou da história |
| --------------------- | ----------------- |
| O modelo é instantâneo do estado atual dos recursos. Inclui quaisquer alterações manuais que tenha feito após a implantação. | O modelo só mostra estado de recursos no momento da implantação. Quaisquer alterações manuais que tenha feito após a implantação não estão incluídas. |
| Pode selecionar quais os recursos de um grupo de recursos para exportar. | Todos os recursos para uma implantação específica estão incluídos. Não se pode escolher um subconjunto desses recursos ou adicionar recursos que foram adicionados num momento diferente. |
| O modelo inclui todas as propriedades para os recursos, incluindo algumas propriedades que normalmente não definiria durante a implementação. É melhor remover ou limpar estas propriedades antes de reutilizar o modelo. | O modelo inclui apenas as propriedades necessárias para a implementação. O modelo está pronto a usar. |
| O modelo provavelmente não inclui todos os parâmetros necessários para a reutilização. A maioria dos valores de propriedade são codificados no modelo. Para reimplantar o modelo noutros ambientes, é necessário adicionar parâmetros que aumentem a capacidade de configurar os recursos.  Pode desseleccionar **Incluir parâmetros** para que possa ser autor dos seus próprios parâmetros. | O modelo inclui parâmetros que facilitam a reimplantação em diferentes ambientes. |

Exportar o modelo de um grupo de recursos ou recurso, quando:

* Tens de capturar alterações nos recursos que foram feitos após a implantação original.
* Quer selecionar quais os recursos que são exportados.

Exportar o modelo antes da implantação ou da história, quando:

* Você quer um modelo fácil de reutilizar.
* Não precisa de incluir as alterações que fez após a implantação original.

## <a name="limitations"></a>Limitações

Ao exportar de um grupo de recursos ou recurso, o modelo exportado é gerado a partir dos [esquemas publicados](https://github.com/Azure/azure-resource-manager-schemas/tree/master/schemas) para cada tipo de recurso. Ocasionalmente, o esquema não tem a versão mais recente para um tipo de recurso. Verifique o seu modelo exportado para se certificar de que inclui as propriedades de que necessita. Se necessário, edite o modelo exportado para utilizar a versão API de que necessita.

O modelo de exportação não suporta a exportação de recursos da Azure Data Factory. Para saber como pode exportar recursos da Fábrica de Dados, consulte [copiar ou clonar uma fábrica](https://aka.ms/exportTemplateViaAdf)de dados na Azure Data Factory .

Para exportar recursos criados através de um modelo de implantação clássico, deve [emigrar para o modelo](https://aka.ms/migrateclassicresourcetoarm)de implantação do Gestor de Recursos.

## <a name="export-template-from-a-resource-group"></a>Modelo de exportação de um grupo de recursos

Exportar um ou mais recursos de um grupo de recursos:

1. Selecione o grupo de recursos que contém os recursos que pretende exportar.

1. Selecione um ou mais recursos selecionando as caixas de verificação.  Para selecionar tudo, selecione a caixa de verificação à esquerda do **Nome**. O item do menu do **modelo de exportação** só fica ativado depois de ter selecionado pelo menos um recurso.

   ![Exportar todos os recursos](./media/export-template-portal/select-all-resources.png)

    Na imagem, apenas a conta de armazenamento é selecionada.
1. Selecione **modelo de exportação**.

1. O modelo exportado é apresentado e está disponível para descarregar e implementar.

   ![Modelo de show](./media/export-template-portal/show-template.png)

   **Incluir parâmetros** é selecionado por padrão.  Quando selecionados, todos os parâmetros do modelo serão incluídos quando o modelo for gerado. Se quiser ser autor dos seus próprios parâmetros, altere esta caixa de verificação para não incluí-los.

## <a name="export-template-from-a-resource"></a>Modelo de exportação de um recurso

Para exportar um recurso:

1. Selecione o grupo de recursos que contém o recurso que pretende exportar.

1. Selecione o recurso que pretende exportar para abrir o recurso.

1. Para esse recurso, selecione modelo de **exportação** no painel esquerdo.

   ![Recurso de exportação](./media/export-template-portal/export-single-resource.png)

1. O modelo exportado é apresentado e está disponível para descarregar e implementar. O modelo contém apenas o único recurso. **Incluir parâmetros** é selecionado por padrão.  Quando selecionados, todos os parâmetros do modelo serão incluídos quando o modelo for gerado. Se quiser ser autor dos seus próprios parâmetros, altere esta caixa de verificação para não incluí-los.

## <a name="export-template-before-deployment"></a>Modelo de exportação antes da implantação

1. Selecione o serviço Azure que pretende implementar.

1. Preencha os valores para o novo serviço.

1. Depois de passar a validação, mas antes de iniciar a implementação, selecione **Descarregue um modelo para automação**.

   ![Transferir modelo](./media/export-template-portal/download-before-deployment.png)

1. O modelo é apresentado e está disponível para download e implementação.


## <a name="export-template-after-deployment"></a>Modelo de exportação após implantação

Pode exportar o modelo que foi usado para implantar recursos existentes. O modelo que obtém é exatamente o que foi usado para a implantação.

1. Selecione o grupo de recursos que pretende exportar.

1. Selecione o link em **'Implementações**'.

   ![Selecione histórico de implementação](./media/export-template-portal/select-deployment-history.png)

1. Selecione uma das implementações do histórico de implantação.

   ![Selecione implementação](./media/export-template-portal/select-details.png)

1. Modelode seleção . O modelo utilizado para esta implementação é apresentado e está disponível para download.

   ![Modelo selecionado](./media/export-template-portal/show-template-from-history.png)

## <a name="next-steps"></a>Passos seguintes

- Saiba como exportar modelos com [Azure CLI,](../management/manage-resource-groups-cli.md#export-resource-groups-to-templates) [Azure PowerShell,](../management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)ou [REST API](/rest/api/resources/resourcegroups/exporttemplate).
- Para aprender a sintaxe do modelo do Gestor de Recursos, consulte [Compreender a estrutura e a sintaxe dos modelos do Gestor](template-syntax.md)de Recursos Azure .
- Para aprender a desenvolver modelos, consulte os [tutoriais passo a passo.](/azure/azure-resource-manager/)
- Para ver os esquemas de modelo do Gestor de Recursos Azure, consulte a referência do [modelo](/azure/templates/).
