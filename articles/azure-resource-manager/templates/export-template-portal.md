---
title: Modelo de exportação no portal Azure
description: Utilize o portal Azure para exportar um modelo de Gestor de Recursos Azure a partir de recursos na sua subscrição.
ms.topic: conceptual
ms.date: 07/29/2020
ms.openlocfilehash: 3cc790b67b6076236a550c1fa202e0d173fb360e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105731943"
---
# <a name="single-and-multi-resource-export-to-a-template-in-azure-portal"></a>Exportação única e multi-recursos para um modelo no portal Azure

Para ajudar na criação de modelos de Gestor de Recursos Azure, pode exportar um modelo a partir dos recursos existentes. O modelo exportado ajuda-o a compreender a sintaxe JSON e as propriedades que implementam os seus recursos. Para automatizar futuras implementações, comece com o modelo exportado e modifique-o para o seu cenário.

O Gestor de Recursos permite-lhe escolher um ou mais recursos para exportar para um modelo. Você pode focar-se exatamente nos recursos que você precisa no modelo.

Este artigo mostra como exportar modelos através do portal. Também pode utilizar [Azure CLI,](../management/manage-resource-groups-cli.md#export-resource-groups-to-templates) [Azure PowerShell,](../management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)ou [REST API](/rest/api/resources/resources/resourcegroups/exporttemplate).

## <a name="choose-the-right-export-option"></a>Escolha a opção de exportação certa

Existem duas formas de exportar um modelo:

* **Exportação de grupo de recursos ou recursos.** Esta opção gera um novo modelo a partir dos recursos existentes. O modelo exportado é um "instantâneo" do estado atual do grupo de recursos. Pode exportar um grupo de recursos inteiro ou recursos específicos dentro desse grupo de recursos.

* **Exportar antes da implantação ou da história.** Esta opção recupera uma cópia exata de um modelo utilizado para a implantação.

Dependendo da opção que escolher, os modelos exportados têm qualidades diferentes.

| Do grupo de recursos ou recursos | Antes da implantação ou da história |
| --------------------- | ----------------- |
| O modelo é instantâneo do estado atual dos recursos. Inclui quaisquer alterações manuais efetuadas após a colocação. | O modelo só mostra o estado dos recursos no momento da implantação. Quaisquer alterações manuais efetuadas após a colocação não estão incluídas. |
| Pode selecionar quais os recursos de um grupo de recursos para exportar. | Todos os recursos para uma implantação específica estão incluídos. Não se pode escolher um subconjunto desses recursos ou adicionar recursos que foram adicionados numa altura diferente. |
| O modelo inclui todas as propriedades para os recursos, incluindo algumas propriedades que normalmente não definiria durante a implementação. É melhor remover ou limpar estas propriedades antes de reutilizar o modelo. | O modelo inclui apenas as propriedades necessárias para a implantação. O modelo está pronto a ser utilizado. |
| O modelo provavelmente não inclui todos os parâmetros necessários para reutilização. A maioria dos valores de propriedade são codificados no modelo. Para recolocar o modelo noutros ambientes, é necessário adicionar parâmetros que aumentem a capacidade de configurar os recursos.  Pode desmarcar **Incluir parâmetros** para que possa autorizar os seus próprios parâmetros. | O modelo inclui parâmetros que facilitam a recolocação em diferentes ambientes. |

Exporte o modelo a partir de um grupo de recursos ou recurso, quando:

* É preciso capturar alterações nos recursos que foram feitos após a implantação original.
* Pretende selecionar quais os recursos exportados.

Exporte o modelo antes da implantação ou da história, quando:

* Você quer um modelo de fácil reutilização.
* Não precisa de incluir alterações feitas após a implantação original.

## <a name="limitations"></a>Limitações

Ao exportar de um grupo de recursos ou recurso, o modelo exportado é gerado a partir dos [esquemas publicados](https://github.com/Azure/azure-resource-manager-schemas/tree/master/schemas) para cada tipo de recurso. Ocasionalmente, o esquema não tem a versão mais recente para um tipo de recurso. Verifique o seu modelo exportado para se certificar de que inclui as propriedades necessárias. Se necessário, edite o modelo exportado para utilizar a versão API de que necessita.

A funcionalidade do modelo de exportação não suporta a exportação de recursos da Azure Data Factory. Para saber como pode exportar recursos da Data Factory, consulte [Copy ou clone uma fábrica de dados na Azure Data Factory.](../../data-factory/copy-clone-data-factory.md)

Para exportar recursos criados através de um modelo de implementação clássico, deve [migrar para o modelo de implementação do Gestor de Recursos.](../../virtual-machines/migration-classic-resource-manager-overview.md)

Se receber um aviso ao exportar um modelo que indique que um tipo de recurso não foi exportado, ainda pode descobrir as propriedades desse recurso. Para saber mais sobre as diferentes opções para visualizar propriedades de recursos, consulte [as propriedades de recursos Discover.](view-resources.md) Também pode olhar para a [API Azure REST](/rest/api/azure/) para o tipo de recurso.

Há um limite de 200 recursos no grupo de recursos para o qual se cria o modelo exportado. Se tentar exportar um grupo de recursos com mais de 200 recursos, a mensagem de erro `Export template is not supported for resource groups more than 200 resources` é mostrada.

## <a name="export-template-from-a-resource-group"></a>Exportar modelo a partir de um grupo de recursos

Exportar um ou mais recursos de um grupo de recursos:

1. Selecione o grupo de recursos que contém os recursos que pretende exportar.

1. Selecione um ou mais recursos selecionando as caixas de verificação.  Para selecionar todos, selecione a caixa de verificação à esquerda do **Nome**. O item do menu **do modelo de exportação** só fica ativado depois de ter selecionado pelo menos um recurso.

   ![Exportar todos os recursos](./media/export-template-portal/select-all-resources.png)

    Na imagem, apenas a conta de armazenamento é selecionada.
1. Selecione **o modelo de exportação.**

1. O modelo exportado é apresentado e está disponível para descarregar e implementar.

   ![Modelo de show](./media/export-template-portal/show-template.png)

   **Os parâmetros incluem** que os parâmetros são selecionados por predefinição.  Quando selecionados, todos os parâmetros do modelo serão incluídos quando o modelo for gerado. Se quiser autorar os seus próprios parâmetros, altere esta caixa de verificação para não incluí-los.

## <a name="export-template-from-a-resource"></a>Exportar modelo a partir de um recurso

Para exportar um recurso:

1. Selecione o grupo de recursos que contém o recurso que pretende exportar.

1. Selecione o recurso que pretende exportar para abrir o recurso.

1. Para esse recurso, selecione **o modelo de exportação** no painel esquerdo.

   ![Recurso de exportação](./media/export-template-portal/export-single-resource.png)

1. O modelo exportado é apresentado e está disponível para descarregar e implementar. O modelo contém apenas o único recurso. **Os parâmetros incluem** que os parâmetros são selecionados por predefinição.  Quando selecionados, todos os parâmetros do modelo serão incluídos quando o modelo for gerado. Se quiser autorar os seus próprios parâmetros, altere esta caixa de verificação para não incluí-los.

## <a name="export-template-before-deployment"></a>Modelo de exportação antes da implantação

1. Selecione o serviço Azure que pretende implementar.

1. Preencha os valores para o novo serviço.

1. Depois de passar a validação, mas antes de iniciar a implementação, selecione **Descarregue um modelo para automatização**.

   ![Transferir modelo](./media/export-template-portal/download-before-deployment.png)

1. O modelo é apresentado e está disponível para download e implementação.


## <a name="export-template-after-deployment"></a>Modelo de exportação após implantação

Pode exportar o modelo que foi usado para implantar recursos existentes. O modelo que obtém é exatamente o que foi usado para implantação.

1. Selecione o grupo de recursos que pretende exportar.

1. Selecione o link em **Implementações**.

   ![Selecione o histórico de implementação](./media/export-template-portal/select-deployment-history.png)

1. Selecione uma das implementações do histórico de implantação.

   ![Selecione implantação](./media/export-template-portal/select-details.png)

1. Selecione **modelo**. O modelo utilizado para esta implementação é apresentado e está disponível para download.

   ![Selecionar modelo](./media/export-template-portal/show-template-from-history.png)

## <a name="next-steps"></a>Passos seguintes

- Saiba como exportar modelos com [Azure CLI,](../management/manage-resource-groups-cli.md#export-resource-groups-to-templates) [Azure PowerShell,](../management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)ou [REST API](/rest/api/resources/resources/resourcegroups/exporttemplate).
- Para aprender a sintaxe do modelo do Gestor de Recursos, consulte [compreender a estrutura e a sintaxe dos modelos do Gestor de Recursos Azure](template-syntax.md).
- Para aprender a desenvolver modelos, consulte os [tutoriais passo a passo.](../index.yml)
- Para ver os esquemas de modelo do Gestor de Recursos Azure, consulte [a referência do modelo](/azure/templates/).