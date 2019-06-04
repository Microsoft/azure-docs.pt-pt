---
title: Tutorial - proteger os recursos novos com bloqueios de recursos de esquema
description: Neste tutorial, irá aprender a utilizar as opções de bloqueios de recursos de esquemas do Azure só de leitura e não elimine recentemente a proteger os recursos implementados.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/28/2019
ms.topic: tutorial
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 274c437acd8df50d631727fc352c4b9ebecead18
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66479968"
---
# <a name="tutorial-protect-new-resources-with-azure-blueprints-resource-locks"></a>Tutorial: Proteger os recursos novos com bloqueios de recursos de esquemas do Azure

Com o Azure esquemas [bloqueios de recursos](../concepts/resource-locking.md), pode proteger os recursos implementados recentemente seja alterado, até mesmo por uma conta com o _proprietário_ função. Pode adicionar esta proteção nas definições de esquema de recursos criados por um artefato de modelo do Resource Manager.

Neste tutorial, irá concluir estes passos:

> [!div class="checklist"]
> - Criar uma definição de esquema
> - Marcar a definição de esquema como **publicado**
> - Atribuir a definição do esquema a uma subscrição existente
> - Inspecione o novo grupo de recursos
> - Anular atribuição do esquema para remover os bloqueios

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-a-blueprint-definition"></a>Criar uma definição de esquema

Primeiro, crie a definição do esquema.

1. Selecione **todos os serviços** no painel esquerdo. Procure e selecione **esquemas**.

1. Sobre o **introdução** página à esquerda, selecione **criar** sob **criar um plano gráfico**.

1. Encontrar o **em branco esquema** exemplo de plano gráfico na parte superior da página. Selecione **começar com o esquema em branco**.

1. Introduza estas informações o **Noções básicas** separador:

   - **Nome do blueprint**: Forneça um nome para a sua cópia do exemplo de esquema. Para este tutorial, vamos utilizar o nome **storageaccount bloqueado**.
   - **Descrição de esquema**: Adicione uma descrição para a definição do esquema. Uso **para o teste esquema recursos bloqueio em recursos implementados**.
   - **Localização da definição**: Selecione o botão de reticências (...) e, em seguida, selecione a subscrição ou grupo de gestão para guardar a definição de esquema, para.

1. Selecione o **artefactos** separador na parte superior da página ou selecione **seguinte: Artefactos** na parte inferior da página.

1. Adicione um grupo de recursos ao nível da subscrição:
   1. Selecione o **adicionar artefacto** linha sob **subscrição**.
   1. Selecione **grupo de recursos** sob **tipo de Artefato**.
   1. Definir o **nome a apresentar do artefacto** ao **RGtoLock**.
   1. Deixe o **nome do grupo de recursos** e **localização** caixas em branco, mas certifique-se de que a caixa de verificação está selecionada em cada propriedade para torná-los **parâmetros dinâmicos**.
   1. Selecione **adicionar** para adicionar os artefactos ao esquema.

1. Adicione um modelo no grupo de recursos:
   1. Selecione o **adicionar artefacto** linha sob o **RGtoLock** entrada. 
   1. Selecione **modelo Azure Resource Manager** sob **tipo de Artefato**, defina **nome a apresentar do artefacto** para **StorageAccount**e deixe  **Descrição** em branco. 
   1. Sobre o **modelo** separador, cole o seguinte modelo do Resource Manager na caixa de editor. Depois de o colar no modelo, selecione **adicionar** para adicionar os artefactos ao esquema.

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
           "storageAccountType": {
               "type": "string",
               "defaultValue": "Standard_LRS",
               "allowedValues": [
                   "Standard_LRS",
                   "Standard_GRS",
                   "Standard_ZRS",
                   "Premium_LRS"
               ],
               "metadata": {
                   "description": "Storage Account type"
               }
           }
       },
       "variables": {
           "storageAccountName": "[concat('store', uniquestring(resourceGroup().id))]"
       },
       "resources": [{
           "type": "Microsoft.Storage/storageAccounts",
           "name": "[variables('storageAccountName')]",
           "location": "[resourceGroup().location]",
           "apiVersion": "2018-07-01",
           "sku": {
               "name": "[parameters('storageAccountType')]"
           },
           "kind": "StorageV2",
           "properties": {}
       }],
       "outputs": {
           "storageAccountName": {
               "type": "string",
               "value": "[variables('storageAccountName')]"
           }
       }
   }
   ```

1. Selecione **Guardar rascunho** na parte inferior da página.

Este passo cria a definição do esquema na subscrição ou grupo de gestão selecionado.

Depois do **gravação de definição do esquema foi efetuada com êxito** aparece a notificação do portal, vá para o passo seguinte.

## <a name="publish-the-blueprint-definition"></a>Publicar a definição do esquema

A definição do esquema agora foi criada no seu ambiente. Ele é criado na **rascunho** modo e tem de ser publicado antes de pode ser atribuída e implantada.

1. Selecione **todos os serviços** no painel esquerdo. Procure e selecione **esquemas**.

1. Selecione o **definições de esquema** página à esquerda. Utilize os filtros para encontrar os **storageaccount bloqueado** definição de esquema e, em seguida, selecioná-lo.

1. Selecione **publicar esquema** na parte superior da página. No novo painel à direita, introduza **1.0** como o **versão**. Esta propriedade é útil se fizer uma alteração mais tarde. Introduza **alterar notas**, tal como **primeira versão publicada para bloquear os recursos de esquema implementada**. Em seguida, selecione **publicar** na parte inferior da página.

Este passo torna possível atribuir o esquema para uma subscrição. Depois da definição do esquema é publicada, ainda é possível fazer alterações. Se fizer alterações, terá de publicar a definição com um novo valor de versão para controlar as diferenças entre as versões da mesma definição de esquema.

Depois do **publicação foi efetuada com êxito de definição de esquema** aparece a notificação do portal, vá para o passo seguinte.

## <a name="assign-the-blueprint-definition"></a>Atribuir a definição do esquema

Depois da definição do esquema é publicada, pode atribuí-la para uma subscrição dentro do grupo de gestão onde o guardou. Neste passo, é fornecer os parâmetros para tornar cada implementação da definição do esquema exclusivo.

1. Selecione **todos os serviços** no painel esquerdo. Procure e selecione **esquemas**.

1. Selecione o **definições de esquema** página à esquerda. Utilize os filtros para encontrar os **storageaccount bloqueado** definição de esquema e, em seguida, selecioná-lo.

1. Selecione **esquema de atribuir** na parte superior da página de definição de esquema.

1. Forneça os valores de parâmetro para a atribuição do esquema:

   - **Noções básicas**

     - **Subscrições**: Selecione uma ou mais das subscrições que estão no grupo de gestão onde guardou a definição do esquema. Se selecionar mais do que uma subscrição, será criada uma atribuição para cada subscrição, utilizando os parâmetros que introduzir.
     - **Nome da atribuição**: O nome é preenchido previamente com base no nome da definição de esquema. Queremos que esta atribuição para representar o bloqueio de novo grupo de recursos, portanto, altere o nome da atribuição para **atribuição-bloqueado-storageaccount-TestingBPLocks**.
     - **Localização**: Selecione uma região em que pretende criar a identidade gerida. O Azure Blueprint utiliza esta identidade gerida para implementar todos os artefactos no esquema atribuído. Para saber mais, veja [identidades geridas dos recursos do Azure](../../../active-directory/managed-identities-azure-resources/overview.md).
       Neste tutorial, selecione **E.U.A. Leste 2**.
     - **Versão da definição de esquema**: Selecione a versão publicada **1.0** da definição de esquema.

   - **Atribuição de bloqueio**

     Selecione o **só de leitura** modo de bloqueio de esquema. Para obter mais informações, veja [bloqueio de recurso em esquemas](../concepts/resource-locking.md).

   - **Identidade gerida**

     Utilize a opção predefinida: **Sistema atribuído**. Para obter mais informações, consulte [geridos identidades](../../../active-directory/managed-identities-azure-resources/overview.md).

   - **Parâmetros de artefacto**

     Os parâmetros definidos nesta secção aplicam-se para o artefacto sob a qual estão definidos. Esses parâmetros são [parâmetros dinâmicos](../concepts/parameters.md#dynamic-parameters) porque estão definidos durante a atribuição do esquema. Para cada artefato, defina o valor de parâmetro para que vê na **valor** coluna.

     |Nome do artefacto|Tipo de artefacto|Nome do parâmetro|Value|Descrição|
     |-|-|-|-|-|
     |Grupo de recursos de RGtoLock|Grupo de recursos|Name|TestingBPLocks|Define o nome do novo grupo de recursos para aplicar o esquema de bloqueios para.|
     |Grupo de recursos de RGtoLock|Grupo de recursos|Location|EUA Oeste 2|Define a localização do novo grupo de recursos para aplicar o esquema de bloqueios para.|
     |StorageAccount|Modelo do Resource Manager|storageAccountType (StorageAccount)|Standard_GRS|O SKU de armazenamento. O valor predefinido é _Standard_LRS_.|

1. Depois de introduzir todos os parâmetros, selecione **atribuir** na parte inferior da página.

Este passo implementa os recursos definidos e configura o selecionado **atribuição de bloqueio**. Pode demorar até 30 minutos para aplicar o esquema de bloqueios.

Depois do **atribuir da definição do esquema foi efetuada com êxito** aparece a notificação do portal, vá para o passo seguinte.

## <a name="inspect-resources-deployed-by-the-assignment"></a>Inspecionar os recursos implementados pela atribuição

A atribuição cria o grupo de recursos _TestingBPLocks_ e a conta de armazenamento implementadas pelo artefacto de modelo do Resource Manager. Novo grupo de recursos e o estado de bloqueio selecionada são apresentados na página de detalhes de atribuição.

1. Selecione **todos os serviços** no painel esquerdo. Procure e selecione **esquemas**.

1. Selecione o **atribuído a planos gráficos** página à esquerda. Utilize os filtros para encontrar os **atribuição-bloqueado-storageaccount-TestingBPLocks** atribuição de esquema e, em seguida, selecioná-lo.

   Nesta página, podemos ver que foi concluída com êxito a atribuição e que os recursos foram implementados com o novo estado de bloqueio de esquema. Se a atribuição é atualizada, o **operação de atribuição** pendente mostra detalhes sobre a implementação de cada versão de definição. Pode selecionar o grupo de recursos para abrir a página de propriedade.

1. Selecione o **TestingBPLocks** grupo de recursos.

1. Selecione o **controlo de acesso (IAM)** página à esquerda. Em seguida, selecione o **atribuições de funções** separador.

   Aqui, Vemos que o _atribuição-bloqueado-storageaccount-TestingBPLocks_ atribuição do esquema tem a _proprietário_ função. Ele tem esta função porque esta função foi utilizada para implementar e bloquear o grupo de recursos.

1. Selecione o **negar atribuições** separador.

   A atribuição do esquema de criar um [negar a atribuição](../../../role-based-access-control/deny-assignments.md) no grupo de recursos implementados para impor a **só de leitura** modo de bloqueio de esquema. A atribuição de negar impede que uma pessoa com direitos adequados no **atribuições de funções** separador de realizar ações específicas. A atribuição de negar afeta _todos os principais_.

   Para obter informações sobre excluindo um principal de uma atribuição de negar, consulte [esquemas de bloqueio do recurso](../concepts/resource-locking.md#exclude-a-principal-from-a-deny-assignment).

1. Selecione a atribuição de negação e, em seguida, selecione o **negado permissões** página à esquerda.

   A atribuição de negação é impedir todas as operações com o **\*** e **ação** configuração, mas permite o acesso de leitura excluindo  **\* /leitura**via **NotActions**.

1. Os seus portal do Azure, selecione **TestingBPLocks - controlo de acesso (IAM)** . Em seguida, selecione o **descrição geral** página à esquerda e, em seguida, o **eliminar grupo de recursos** botão. Introduza o nome **TestingBPLocks** para confirmar a eliminação e, em seguida, selecione **eliminar** na parte inferior do painel.

   A notificação do portal **eliminar grupo de recursos falha TestingBPLocks** aparece. O erro indica que, apesar de sua conta tem permissão para eliminar o grupo de recursos, o acesso é negado pela atribuição do esquema. Lembre-se de que selecionamos os **só de leitura** modo de bloqueio de esquema durante a atribuição do esquema. O bloqueio de esquema impede que uma conta com permissões, até mesmo _proprietário_, de eliminar o recurso. Para obter mais informações, veja [bloqueio de recurso em esquemas](../concepts/resource-locking.md).

Estes passos mostram que os nossos recursos implementados estão agora protegidos com bloqueios de esquema que impedem a eliminação indesejada, mesmo a partir de uma conta que tenha permissão para eliminar os recursos.

## <a name="unassign-the-blueprint"></a>Anular a atribuição do esquema

A última etapa é remover a atribuição da definição de esquema. Remover a atribuição não remove os artefactos associados.

1. Selecione **todos os serviços** no painel esquerdo. Procure e selecione **esquemas**.

1. Selecione o **atribuído a planos gráficos** página à esquerda. Utilize os filtros para encontrar os **atribuição-bloqueado-storageaccount-TestingBPLocks** atribuição de esquema e, em seguida, selecioná-lo.

1. Selecione **Unassign esquema** na parte superior da página. Leia o aviso na caixa de diálogo de confirmação e, em seguida, selecione **OK**.

   Quando a atribuição do esquema é removida, os bloqueios de plano gráfico também são removidos. Os recursos mais uma vez podem ser eliminados por uma conta com permissões adequadas.

1. Selecione **grupos de recursos** no menu do Azure e, em seguida, selecione **TestingBPLocks**.

1. Selecione o **controlo de acesso (IAM)** página à esquerda e, em seguida, selecione a **atribuições de funções** separador.

A segurança para o grupo de recursos mostra que já não tem a atribuição do esquema _proprietário_ acesso.

Depois do **remover atribuição do esquema foi efetuada com êxito** aparece a notificação do portal, vá para o passo seguinte.

## <a name="clean-up-resources"></a>Limpar recursos

Quando tiver terminado com este tutorial, elimine estes recursos:

- Grupo de recursos _TestingBPLocks_
- Definição de esquema _storageaccount bloqueado_

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre o [ciclo de vida de esquema](../concepts/lifecycle.md).
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../concepts/parameters.md).
- Descubra como usar [esquema de bloqueio do recurso](../concepts/resource-locking.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../concepts/sequencing-order.md).
- Saiba como [atualizar as atribuições existentes](../how-to/update-existing-assignments.md).
- [Resolver problemas de](../troubleshoot/general.md) durante a atribuição de um plano gráfico.
