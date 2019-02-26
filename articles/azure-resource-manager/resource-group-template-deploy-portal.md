---
title: Utilizar o portal do Azure para implementar recursos do Azure | Documentos da Microsoft
description: Utilize o portal do Azure e Azure Resource Manager para implementar os recursos.
services: azure-resource-manager,azure-portal
documentationcenter: ''
author: tfitzmac
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/03/2018
ms.author: tomfitz
ms.openlocfilehash: 0711743d46aaac08f8572917d91e452ee919134c
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56816911"
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-portal"></a>Implementar recursos com modelos do Resource Manager e do Portal do Azure

Este artigo mostra como utilizar o [portal do Azure](https://portal.azure.com) com [do Azure Resource Manager](resource-group-overview.md) para implementar os recursos do Azure. Para saber mais sobre a gestão dos seus recursos, veja [recursos de gerir o Azure com o portal do Azure](manage-resources-portal.md).

## <a name="create-resource-group"></a>Criar grupo de recursos

1. Para criar um grupo de recursos vazio, selecione **grupos de recursos**.

   ![Selecione os grupos de recursos](./media/resource-group-template-deploy-portal/select-resource-groups.png)

1. Em grupos de recursos, selecione **adicionar**.

   ![Adicionar grupo de recursos](./media/resource-group-template-deploy-portal/add-resource-group.png)

1. Dê a ele um nome e localização e, se necessário, selecione uma subscrição. Tem de fornecer uma localização para o grupo de recursos porque o grupo de recursos armazena metadados sobre os recursos. Por motivos de conformidade, talvez queira especificar onde esses metadados estão armazenados. Em geral, recomendamos que especificar uma localização onde a maioria dos seus recursos irão residir. Use o mesmo local pode simplificar o seu modelo.

   ![Definir valores de grupo](./media/resource-group-template-deploy-portal/set-group-properties.png)

   Quando tiver concluído a definir as propriedades, selecione **criar**.

1. Para ver o seu novo grupo de recursos, selecione **atualizar**.

   ![Grupos de recursos de atualização](./media/resource-group-template-deploy-portal/refresh-resource-groups.png)

## <a name="deploy-resources-from-marketplace"></a>Implementar recursos do Marketplace

Depois de criar um grupo de recursos, pode implementar recursos para o mesmo do Marketplace. O Marketplace proporciona soluções predefinidas para cenários comuns.

1. Para iniciar uma implementação, selecione **criar um recurso**.

   ![Novo recurso](./media/resource-group-template-deploy-portal/new-resources.png)

1. Encontre o tipo de recurso que pretende implementar.

   ![Selecione o tipo de recurso](./media/resource-group-template-deploy-portal/select-resource-type.png)

1. Se não vir a solução específica que pretende implementar, pode procurar no Marketplace para o mesmo. Por exemplo, para encontrar uma solução de Wordpress, comece a escrever **Wordpress** e selecione a opção que pretende.

   ![Procurar marketplace](./media/resource-group-template-deploy-portal/search-resource.png)

1. Dependendo do tipo de recursos selecionado, tem uma coleção de propriedades relevantes para definir antes da implantação. Para todos os tipos, tem de selecionar um grupo de recursos de destino. A imagem seguinte mostra como criar uma aplicação web e implementá-la para o grupo de recursos que criou.

   ![Criar grupo de recursos](./media/resource-group-template-deploy-portal/select-existing-group.png)

   Em alternativa, pode optar por criar um grupo de recursos durante a implantação de seus recursos. Selecione **criar novo** e dê um nome ao grupo de recursos.

   ![Criar um novo grupo de recursos](./media/resource-group-template-deploy-portal/select-new-group.png)

1. Início da sua implantação. A implementação pode demorar alguns minutos. Quando a implementação estiver concluída, verá uma notificação.

   ![Notificação de vista](./media/resource-group-template-deploy-portal/view-notification.png)

1. Depois de implementar os seus recursos, pode adicionar mais recursos para o grupo de recursos, selecionando **adicionar**.

   ![Adicionar recurso](./media/resource-group-template-deploy-portal/add-resource.png)

## <a name="deploy-resources-from-custom-template"></a>Implementar recursos de modelo personalizado

Se quiser executar uma implementação, mas não utilizar qualquer um dos modelos no Marketplace, pode criar um modelo personalizado que define a infraestrutura para a sua solução. Para saber mais sobre a criação de modelos, veja [compreender a estrutura e a sintaxe de modelos Azure Resource Manager](resource-group-authoring-templates.md).

> [!NOTE]
> Interface do portal não suporta a referenciar um [segredo a partir de um cofre de chave](resource-manager-keyvault-parameter.md). Em alternativa, utilize [PowerShell](resource-group-template-deploy.md) ou [CLI do Azure](resource-group-template-deploy-cli.md) para implementar o modelo localmente ou a partir de um URI externo.

1. Para implementar um modelo personalizado através do portal, selecione **criar um recurso**e procure **implementação do modelo** até que pode selecioná-lo entre as opções.

   ![Implementação do modelo de pesquisa](./media/resource-group-template-deploy-portal/search-template.png)

1. Selecione **Criar**.

   ![Selecione criar](./media/resource-group-template-deploy-portal/show-template-option.png)

1. Verá várias opções para criar um modelo. Selecione **Crie o seu próprio modelo no editor**.

   ![Opções de visualização](./media/resource-group-template-deploy-portal/see-options.png)

1. Tem um modelo em branco que está disponível para a personalização.

   ![Criar o modelo](./media/resource-group-template-deploy-portal/blank-template.png)

1. Pode editar a sintaxe JSON manualmente ou selecione um modelo previamente criado a partir da [Galeria de modelos de início rápido](https://azure.microsoft.com/resources/templates/). No entanto, neste artigo, utilize o **adicionar recurso** opção.

   ![Editar modelo](./media/resource-group-template-deploy-portal/select-add-resource.png)

1. Selecione **conta de armazenamento** e forneça um nome. Quando terminar de fornecer valores, selecione **OK**.

   ![Selecionar a conta de armazenamento](./media/resource-group-template-deploy-portal/add-storage-account.png)

1. O editor adiciona automaticamente o JSON para o tipo de recurso. Tenha em atenção que inclua um parâmetro para definir o tipo de conta de armazenamento. Selecione **Guardar**.

   ![Mostrar modelo](./media/resource-group-template-deploy-portal/show-json.png)

1. Agora, tem a opção de implementar os recursos definidos no modelo. Para implementar, aceite os termos e condições e selecione **Compra**.

   ![Implementar o modelo](./media/resource-group-template-deploy-portal/provide-custom-template-values.png)

## <a name="deploy-resources-from-a-template-saved-to-your-account"></a>Implementar recursos a partir de um modelo guardadas na sua conta

O portal permite-lhe guardar um modelo para sua conta do Azure e reimplementá-la mais tarde. Para obter mais informações sobre modelos, consulte [criar e implementar o modelo Azure Resource Manager primeira](resource-manager-create-first-template.md).

1. Para localizar seus modelos de guardado, selecione **mais serviços**.

   ![Mais serviços](./media/resource-group-template-deploy-portal/more-services.png)

1. Procure **modelos** e selecione essa opção.

   ![Procurar modelos](./media/resource-group-template-deploy-portal/find-templates.png)

1. Na lista de modelos guardadas na sua conta, selecione aquela que pretende utilizar.

   ![Modelos guardados](./media/resource-group-template-deploy-portal/saved-templates.png)

1. Selecione **Deploy** para voltar a implementar este modelo guardado.

   ![Implementar o modelo guardado](./media/resource-group-template-deploy-portal/deploy-saved-template.png)

## <a name="next-steps"></a>Passos Seguintes
* Para ver os registos de auditoria, consulte [auditar operações com o Resource Manager](./resource-group-audit.md).
* Para resolver erros de implementação, consulte o artigo [ver as operações de implementação](./resource-manager-deployment-operations.md).
* Para exportar um modelo a partir de uma implementação ou o grupo de recursos, consulte [modelos Azure Resource Manager exportar](./manage-resource-groups-portal.md#export-resource-groups-to-templates).
* Para implementação com segurança o seu serviço em várias regiões, consulte [Gestor de implementação do Azure](./deployment-manager-overview.md).
