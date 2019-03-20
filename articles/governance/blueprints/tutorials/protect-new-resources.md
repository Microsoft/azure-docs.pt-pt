---
title: Proteger os recursos novos com bloqueios de recursos de esquema
description: Aprenda a usar os bloqueios de recursos de esquemas do Azure só de leitura e não elimine recentemente a proteger os recursos implementados.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/13/2018
ms.topic: tutorial
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: e3a05329ea247dbf5baa23ae9b3d32f909c0d1bb
ms.sourcegitcommit: b8f9200112cae265155b8877f7e1621c4bcc53fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/14/2019
ms.locfileid: "57858401"
---
# <a name="protect-new-resources-with-azure-blueprints-resource-locks"></a>Proteger os recursos novos com bloqueios de recursos de esquemas do Azure

Planos gráficos do Azure [bloqueios de recursos](../concepts/resource-locking.md) possibilitam a proteger os recursos recentemente implementados seja alterado, até mesmo por uma conta com o _proprietário_ função. Esta proteção pode ser adicionada para recursos criados por um artefato de modelo do Resource Manager na definição do esquema.

São abordados os seguintes passos:

> [!div class="checklist"]
> - Criar uma nova definição de esquema
> - Marcar a definição de esquema como **publicado**
> - Atribuir a definição do esquema a uma subscrição existente
> - Inspecione o novo grupo de recursos
> - Anular atribuição do esquema para remover os bloqueios

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, é necessária uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-new-blueprint-definition"></a>Criar nova definição de esquema

Primeiro, crie a nova definição de esquema.

1. Selecione **todos os serviços** no painel esquerdo. Procure e selecione **esquemas**.

1. Do **introdução** página à esquerda, selecione a **Create** botão sob _criar um plano gráfico_.

1. Encontrar o **exemplo em branco** exemplo de plano gráfico na parte superior da página e selecione **utilizar este exemplo**.

1. Introduza o _Noções básicas_ do exemplo de esquema:

   - **Nome do blueprint**: Forneça um nome para a sua cópia do exemplo de esquema. Para este tutorial, vamos utilizar o nome _storageaccount bloqueado_.
   - **Descrição de esquema**: Descreve a definição do esquema. Utilize "para o teste esquema recurso bloquear recursos implementados."
   - **Localização da definição**: Utilize o botão de reticências e selecione a subscrição ou grupo de gestão para guardar a definição de esquema, para.

1. Selecione o _artefactos_ separador na parte superior da página ou **seguinte: Artefactos** na parte inferior da página.

1. Adicione o grupo de recursos na subscrição: Selecione o **+ adicionar artefacto...**  linha sob **subscrição**.
   Selecione “Grupo de Recursos” para _Tipo de artefacto_. Definir o _nome a apresentar do artefacto_ ao **RGtoLock**.
   Deixe os campos _Nome do Grupo de Recursos_ e _Localização_ em branco, mas garanta que a caixa de verificação está marcada em cada propriedade para torná-los **parâmetros dinâmicos**. Clique em **Adicionar** para adicionar este artefacto ao esquema.

1. Adicione modelo no grupo de recursos: Selecione o **+ adicionar artefacto....** linha sob o **RGtoLock** entrada. Selecione “Modelo do Azure Resource Manager” para _Tipo de artefacto_, defina _Nome do artefacto a apresentar_ como “StorageAccount” e deixe _Descrição_ em branco. No separador **Modelo** na caixa do editor, cole o seguinte modelo do Resource Manager. Depois de colar o modelo, selecione **adicionar** para adicionar este artefacto ao esquema.

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
           "location": "[resourceGroups('RGtoLock').location]",
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

Uma vez a **gravação de definição do esquema foi efetuada com êxito** aparece a notificação do portal, mover para o passo seguinte.

## <a name="publish-the-blueprint-definition"></a>Publicar a definição do esquema

A definição do esquema agora foi criada no seu ambiente. Ele é criado na **rascunho** modo e têm de ser **publicado** antes de pode ser atribuída e implantada.

1. Selecione **todos os serviços** no painel esquerdo. Procure e selecione **esquemas**.

1. Selecione o **definições de esquema** página à esquerda. Utilize os filtros para encontrar os _storageaccount bloqueado_ definição de esquema e, em seguida, selecioná-lo.

1. Selecione **publicar esquema** na parte superior da página. No novo painel à direita, fornecer **versão** como _1.0_. Esta propriedade é útil para se tornar uma modificação mais tarde. Fornecer **alterar notas** como "primeira versão publicada para o bloqueio de recursos de esquema implementada." Em seguida, selecione **publicar** na parte inferior da página.

Este passo torna possível atribuir o esquema para uma subscrição. Depois de publicado, ainda podem ser feitas alterações. Publicação com um novo necessitam de alterações adicionais **versão** valor para controlar as diferenças entre versões diferentes da mesma definição de esquema.

Uma vez a **publicação foi efetuada com êxito de definição de esquema** aparece a notificação do portal, mover para o passo seguinte.

## <a name="assign-the-blueprint-definition"></a>Atribuir a definição do esquema

Assim que a definição do esquema foi com êxito **publicado**, podem ser atribuído a uma subscrição dentro do grupo de gestão que foram salvos. Este passo é onde os parâmetros são fornecidos para que cada implementação da definição do esquema exclusivo.

1. Selecione **todos os serviços** no painel esquerdo. Procure e selecione **esquemas**.

1. Selecione o **definições de esquema** página à esquerda. Utilize os filtros para encontrar os _storageaccount bloqueado_ definição de esquema e, em seguida, selecioná-lo.

1. Selecione **esquema de atribuir** na parte superior da página de definição de esquema.

1. Forneça os valores de parâmetro para a atribuição do esquema:

   - Noções básicas

     - **Subscrições**: Selecione um ou mais das subscrições que estão no grupo de gestão é guardada a definição de esquema, para. Se selecionar mais do que uma subscrição, será criada uma atribuição para cada um usando os parâmetros introduzidos.
     - **Nome da atribuição**: O nome é preenchido previamente com base no nome da definição de esquema. Queremos que esta atribuição para representar o bloqueio de novo grupo de recursos, portanto, altere o nome da atribuição para _atribuição-bloqueado-storageaccount-TestingBPLocks_.
     - **Localização**: Selecione uma região para a identidade gerida a ser criado em. O Azure Blueprint utiliza esta identidade gerida para implementar todos os artefactos no esquema atribuído. Para saber mais, veja [identidades geridas dos recursos do Azure](../../../active-directory/managed-identities-azure-resources/overview.md).
       Neste tutorial, selecione _E.U.A. Leste 2_.
     - **Versão da definição de esquema**: Escolha o **publicada** versão _1.0_ da definição de esquema.

   - Atribuição de Bloqueio

     Selecione o _só de leitura_ modo de bloqueio de esquema. Para obter mais informações, veja [bloqueio de recurso em esquemas](../concepts/resource-locking.md).

   - Identidade Gerida

     Deixe a predefinição _sistema atribuído_ opção. Para obter mais informações, consulte [geridos identidades](../../../active-directory/managed-identities-azure-resources/overview.md).

   - Parâmetros de artefacto

     Os parâmetros definidos nesta secção aplicam-se para o artefacto sob a qual está definido. Esses parâmetros são [parâmetros dinâmicos](../concepts/parameters.md#dynamic-parameters) , uma vez que estão definidos durante a atribuição do esquema. Para cada artefato, defina o valor de parâmetro para o que é definido no **valor** coluna.

     |Nome do artefacto|Tipo de artefacto|Nome do parâmetro|Value|Descrição|
     |-|-|-|-|-|
     |Grupo de recursos de RGtoLock|Grupo de recursos|Name|TestingBPLocks|Define o nome do novo grupo de recursos para aplicar o esquema de bloqueios para.|
     |Grupo de recursos de RGtoLock|Grupo de recursos|Localização|EUA Oeste 2|Define a localização do novo grupo de recursos para aplicar o esquema de bloqueios para.|
     |StorageAccount|Modelo do Resource Manager|storageAccountType (StorageAccount)|Standard_GRS|Selecione o SKU de armazenamento. Valor predefinido é _Standard_LRS_.|

1. Depois de tem sido introduzidos todos os parâmetros, selecione **atribuir** na parte inferior da página.

Este passo implementa os recursos definidos e configura o selecionado **atribuição de bloqueio**. Bloqueios de plano gráfico podem demorar até 30 minutos a aplicar.

Uma vez a **atribuir da definição do esquema foi efetuada com êxito** aparece a notificação do portal, mover para o passo seguinte.

## <a name="inspect-resources-deployed-by-the-assignment"></a>Inspecionar os recursos implementados pela atribuição

A atribuição de criado o grupo de recursos _TestingBPLocks_ e a conta de armazenamento implementadas pelo artefacto de modelo do Resource Manager. Novo grupo de recursos e o estado de bloqueio selecionada são apresentados na página de detalhes de atribuição.

1. Selecione **todos os serviços** no painel esquerdo. Procure e selecione **esquemas**.

1. Selecione o **atribuído a planos gráficos** página à esquerda. Utilize os filtros para encontrar os _atribuição-bloqueado-storageaccount-TestingBPLocks_ atribuição de esquema e, em seguida, selecioná-lo.

   Nesta página, podemos ver a atribuição concluída com êxito e os recursos foram implementados com o novo estado de bloqueio de esquema. Se a atribuição é atualizada, o **operação de atribuição** pendente mostra detalhes sobre a implementação de cada versão de definição. O grupo de recursos pode ser clicado para abrir a página de propriedades diretamente.

1. Selecione o **TestingBPLocks** grupo de recursos.

1. Selecione o **controlo de acesso (IAM)** página à esquerda e, em seguida, o **atribuições de funções** separador.

   Aqui, Vemos que o _atribuição-bloqueado-storageaccount-TestingBPLocks_ atribuição do esquema tem a _proprietário_ função como ele foi utilizada para implementar e bloquear o grupo de recursos.

1. Selecione o **negar atribuições** separador.

   A atribuição do esquema de criar um [negar a atribuição](../../../role-based-access-control/deny-assignments.md) no grupo de recursos implementados para impor a _só de leitura_ modo de bloqueio de esquema. A atribuição de negar impede que uma pessoa com direitos adequados no _atribuições de funções_ separador de realizar ações específicas. A atribuição de negar afeta _todos os principais_.

1. Selecione a atribuição de negar, em seguida, selecione o **negado permissões** página à esquerda.

   A atribuição de negação é impedir todas as operações com o **\*** e **ação** configuração, mas permite o acesso de leitura excluindo  **\* /leitura**via **NotActions**.

1. A trilha de portal do Azure, selecione **TestingBPLocks - controlo de acesso (IAM)**. Em seguida, selecione o **descrição geral** página à esquerda e, em seguida, o **eliminar grupo de recursos** botão. Introduza o nome _TestingBPLocks_ para confirmar a eliminação e selecione **eliminar** na parte inferior do painel.

   A notificação do portal **eliminar grupo de recursos falha TestingBPLocks** é apresentado. O erro indica que, enquanto a sua conta tem permissão para eliminar o grupo de recursos, o acesso é negado pela atribuição do esquema. Lembre-se de que selecionamos os _só de leitura_ modo de bloqueio de esquema durante a atribuição do esquema. O bloqueio de esquema impede que uma conta com permissões, até mesmo _proprietário_, de eliminar o recurso. Para obter mais informações, veja [bloqueio de recurso em esquemas](../concepts/resource-locking.md).

Estes passos mostram que os nossos recursos implementados estão agora protegidos com bloqueios de esquema que impediu a eliminação indesejada, mesmo a partir de uma conta com permissões.

## <a name="unassign-the-blueprint"></a>Anular a atribuição do esquema

A última etapa é remover a atribuição da definição de esquema. Remover a atribuição não remove os artefactos tocados.

1. Selecione **todos os serviços** no painel esquerdo. Procure e selecione **esquemas**.

1. Selecione o **atribuído a planos gráficos** página à esquerda. Utilize os filtros para encontrar os _atribuição-bloqueado-storageaccount-TestingBPLocks_ atribuição de esquema e, em seguida, selecioná-lo.

1. Selecione o **Unassign esquema** botão na parte superior da página. Leia o aviso na caixa de diálogo de confirmação, em seguida, selecione **OK**.

   Com a atribuição do esquema removida, os bloqueios de plano gráfico também são removidos. Mais uma vez podem ser eliminados os recursos criados por uma conta com permissões.

1. Selecione **grupos de recursos** no menu do Azure, em seguida, selecione **TestingBPLocks**.

1. Selecione o **controlo de acesso (IAM)** página à esquerda e, em seguida, o **atribuições de funções** separador.

A segurança para o grupo de recursos mostra que já não tem a atribuição do esquema _proprietário_ acesso.

Uma vez a **remover atribuição do esquema foi efetuada com êxito** aparece a notificação do portal, mover para o passo seguinte.

## <a name="clean-up-resources"></a>Limpar recursos

Quando terminar com este tutorial, elimine os seguintes recursos:

- Grupo de recursos _TestingBPLocks_
- Definição de esquema _storageaccount bloqueado_

## <a name="next-steps"></a>Passos Seguintes

- Saber mais sobre o [ciclo de vida do esquema](../concepts/lifecycle.md)
- Compreender como utilizar [parâmetros estáticos e dinâmicos](../concepts/parameters.md)
- Saber como utilizar o [bloqueio de recursos de esquema](../concepts/resource-locking.md)
- Aprender a personalizar a [ordem de sequenciação do esquema](../concepts/sequencing-order.md)
- Saber como [atualizar as atribuições existentes](../how-to/update-existing-assignments.md)
- Resolver problemas durante a atribuição de um esquema com [resolução de problemas gerais](../troubleshoot/general.md)