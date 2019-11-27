---
title: 'Tutorial: proteger novos recursos com bloqueios'
description: Neste tutorial, você usará as opções de bloqueios de recursos de plantas do Azure somente leitura e não excluirá para proteger recursos implantados recentemente.
ms.date: 11/21/2019
ms.topic: tutorial
ms.openlocfilehash: ee57ff0c08f4fb8aa710dd2fa4dcef664484973d
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327450"
---
# <a name="tutorial-protect-new-resources-with-azure-blueprints-resource-locks"></a>Tutorial: proteger novos recursos com bloqueios de recursos de plantas do Azure

Com os [bloqueios de recursos](../concepts/resource-locking.md)de plantas do Azure, você pode proteger os recursos implantados recentemente de serem adulterados, mesmo por uma conta com a função de _proprietário_ . Você pode adicionar essa proteção nas definições do Blueprint de recursos criados por um artefato do modelo do Resource Manager.

Neste tutorial, você concluirá estas etapas:

> [!div class="checklist"]
> - Criar uma definição de Blueprint
> - Marcar sua definição de Blueprint como **publicada**
> - Atribuir sua definição de Blueprint a uma assinatura existente
> - Inspecionar o novo grupo de recursos
> - Cancelar a atribuição do plano gráfico para remover os bloqueios

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

## <a name="create-a-blueprint-definition"></a>Criar uma definição de Blueprint

Primeiro, crie a definição do Blueprint.

1. Selecione **todos os serviços** no painel esquerdo. Pesquise e selecione **plantas**.

1. Na página **Guia de introdução** à esquerda, selecione **criar** em **criar um plano gráfico**.

1. Encontre o exemplo de plano gráfico do **Blueprint em branco** na parte superior da página. Selecione **Iniciar com plano gráfico em branco**.

1. Insira essas informações na guia **noções básicas** :

   - **Nome do plano gráfico**: forneça um nome para a sua cópia do exemplo Blueprint. Para este tutorial, usaremos o nome **Locked-storageAccount**.
   - **Descrição do Blueprint**: adicionar uma descrição para a definição do Blueprint. Use **para testar o bloqueio de recursos do Blueprint em recursos implantados**.
   - **Local da definição**: selecione o botão de reticências (...) e, em seguida, selecione o grupo de gerenciamento ou a assinatura para salvar a definição do plano gráfico.

1. Selecione a guia **artefatos** na parte superior da página ou selecione **Avançar: artefatos** na parte inferior da página.

1. Adicione um grupo de recursos no nível da assinatura:
   1. Selecione a linha **Adicionar artefato** em **assinatura**.
   1. Selecione **grupo de recursos** em **tipo de artefato**.
   1. Defina o **nome de exibição do artefato** como **RGtoLock**.
   1. Deixe as caixas nome e **local** do **grupo de recursos** em branco, mas verifique se a caixa de seleção está selecionada em cada propriedade para torná-las **parâmetros dinâmicos**.
   1. Selecione **Adicionar** para adicionar o artefato ao plano gráfico.

1. Adicione um modelo sob o grupo de recursos:
   1. Selecione a linha **Adicionar artefato** na entrada **RGtoLock** .
   1. Selecione **Azure Resource Manager modelo** em **tipo de artefato**, defina **nome de exibição do artefato** como **StorageAccount**e deixe a **Descrição** em branco.
   1. Na guia **modelo** , Cole o seguinte modelo do Resource Manager na caixa editor.
      Depois de colar no modelo, selecione **Adicionar** para adicionar o artefato ao plano gráfico.

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

1. Selecione **salvar rascunho** na parte inferior da página.

Esta etapa cria a definição do Blueprint no grupo de gerenciamento ou na assinatura selecionada.

Depois que a notificação de **salvar a definição do plano gráfico com êxito** for exibida, vá para a próxima etapa.

## <a name="publish-the-blueprint-definition"></a>Publicar a definição do Blueprint

Sua definição de Blueprint agora foi criada em seu ambiente. Ele é criado no modo de **rascunho** e deve ser publicado antes que possa ser atribuído e implantado.

1. Selecione **todos os serviços** no painel esquerdo. Pesquise e selecione **plantas**.

1. Selecione a página **definições de plantas** à esquerda. Use os filtros para localizar a definição de Blueprint **storageAccount bloqueada** e, em seguida, selecione-a.

1. Selecione **publicar Blueprint** na parte superior da página. No novo painel à direita, digite **1,0** como a **versão**. Essa propriedade será útil se você fizer uma alteração posteriormente. Insira **observações de alteração**, como a **primeira versão publicada para bloqueio de recursos implantados pelo Blueprint**. Em seguida, selecione **publicar** na parte inferior da página.

Essa etapa torna possível atribuir o plano gráfico a uma assinatura. Depois que a definição do plano gráfico for publicada, você ainda poderá fazer alterações. Se você fizer alterações, será necessário publicar a definição com um novo valor de versão para controlar as diferenças entre as versões da mesma definição de Blueprint.

Depois que a notificação de **definição do plano gráfico de publicação com êxito** for exibida, vá para a próxima etapa.

## <a name="assign-the-blueprint-definition"></a>Atribuir a definição de Blueprint

Depois que a definição do plano gráfico for publicada, você poderá atribuí-la a uma assinatura dentro do grupo de gerenciamento no qual você a salvou. Nesta etapa, você fornece parâmetros para tornar cada implantação da definição Blueprint exclusiva.

1. Selecione **todos os serviços** no painel esquerdo. Pesquise e selecione **plantas**.

1. Selecione a página **definições de plantas** à esquerda. Use os filtros para localizar a definição de Blueprint **storageAccount bloqueada** e, em seguida, selecione-a.

1. Selecione **atribuir plano gráfico** na parte superior da página de definição do Blueprint.

1. Forneça os valores de parâmetro para a atribuição Blueprint:

   - **Noções básicas**

     - **Assinaturas**: selecione uma ou mais das assinaturas que estão no grupo de gerenciamento em que você salvou a definição do Blueprint. Se você selecionar mais de uma assinatura, uma atribuição será criada para cada assinatura, usando os parâmetros inseridos.
     - **Nome da atribuição**: o nome é preenchido previamente com base no nome da definição do Blueprint. Queremos que essa atribuição represente o bloqueio do novo grupo de recursos, portanto, altere o nome da atribuição para **Assignment-Locked-storageAccount-TestingBPLocks**.
     - **Local**: selecione uma região na qual criar a identidade gerenciada. O Azure Blueprint utiliza esta identidade gerida para implementar todos os artefactos no esquema atribuído. Para saber mais, veja [identidades geridas dos recursos do Azure](../../../active-directory/managed-identities-azure-resources/overview.md).
       Para este tutorial, selecione **leste dos EUA 2**.
     - **Versão de definição do Blueprint**: selecione a versão publicada **1,0** da definição do Blueprint.

   - **Atribuição de bloqueio**

     Selecione o modo de bloqueio de Blueprint **somente leitura** . Para obter mais informações, veja [bloqueio de recurso em esquemas](../concepts/resource-locking.md).

   - **Identidade gerenciada**

     Use a opção padrão: **atribuído pelo sistema**. Para obter mais informações, consulte [identidades gerenciadas](../../../active-directory/managed-identities-azure-resources/overview.md).

   - **Parâmetros de artefato**

     Os parâmetros definidos nesta seção se aplicam ao artefato sob o qual eles são definidos. Esses parâmetros são [parâmetros dinâmicos](../concepts/parameters.md#dynamic-parameters) porque são definidos durante a atribuição do plano gráfico. Para cada artefato, defina o valor do parâmetro para o que você vê na coluna **valor** .

     |Nome do artefato|Tipo de artefato|Nome do parâmetro|Valor|Descrição|
     |-|-|-|-|-|
     |Grupo de recursos RGtoLock|Grupo de recursos|Nome|TestingBPLocks|Define o nome do novo grupo de recursos ao qual aplicar os bloqueios de Blueprint.|
     |Grupo de recursos RGtoLock|Grupo de recursos|Localização|EUA Oeste 2|Define o local do novo grupo de recursos ao qual aplicar os bloqueios de Blueprint.|
     |StorageAccount|Modelo do Resource Manager|storageAccountType (StorageAccount)|Standard_GRS|A SKU de armazenamento. O valor padrão é _Standard_LRS_.|

1. Depois de inserir todos os parâmetros, selecione **atribuir** na parte inferior da página.

Esta etapa implanta os recursos definidos e configura a **atribuição de bloqueio**selecionada. Pode levar até 30 minutos para aplicar bloqueios de plantas.

Depois que a notificação de **atribuição de plano gráfico com êxito** for exibida, vá para a próxima etapa.

## <a name="inspect-resources-deployed-by-the-assignment"></a>Inspecionar os recursos implantados pela atribuição

A atribuição cria o grupo de recursos _TestingBPLocks_ e a conta de armazenamento implantada pelo artefato do modelo do Resource Manager. O novo grupo de recursos e o estado de bloqueio selecionado são mostrados na página detalhes da atribuição.

1. Selecione **todos os serviços** no painel esquerdo. Pesquise e selecione **plantas**.

1. Selecione a página **plantas atribuídas** à esquerda. Use os filtros para localizar a atribuição de Blueprint de **atribuição-bloqueada-storageAccount-TestingBPLocks** e, em seguida, selecione-a.

   Nessa página, podemos ver que a atribuição foi bem-sucedida e que os recursos foram implantados com o novo estado de bloqueio do Blueprint. Se a atribuição for atualizada, a lista suspensa **operação de atribuição** mostrará detalhes sobre a implantação de cada versão de definição. Você pode selecionar o grupo de recursos para abrir a página de propriedades.

1. Selecione o grupo de recursos **TestingBPLocks** .

1. Selecione a página de **controle de acesso (iam)** à esquerda. Em seguida, selecione a guia **atribuições de função** .

   Aqui vemos que a atribuição de Blueprint de _atribuição bloqueada-storageAccount-TestingBPLocks_ tem a função de _proprietário_ . Ele tem essa função porque essa função foi usada para implantar e bloquear o grupo de recursos.

1. Selecione a guia **atribuições de negação** .

   A atribuição de Blueprint criou uma [atribuição de negação](../../../role-based-access-control/deny-assignments.md) no grupo de recursos implantado para impor o modo de bloqueio de Blueprint **somente leitura** . A atribuição Deny impede que alguém com direitos apropriados na guia **atribuições de função** faça ações específicas. A atribuição de negação afeta _todas as entidades de segurança_.

   Para obter informações sobre como excluir uma entidade de segurança de uma atribuição de negação, consulte [bloqueio de recursos de planos gráficos](../concepts/resource-locking.md#exclude-a-principal-from-a-deny-assignment).

1. Selecione a atribuição negar e, em seguida, selecione a página **permissões negadas** à esquerda.

   A atribuição de negação está impedindo todas as operações com a configuração de **\*** e **ação** , mas permite acesso de leitura, excluindo **\*/Read** por meio de **ações**.

1. No portal do Azure trilha, selecione **iam (TestingBPLocks-Access Control)** . Em seguida, selecione a página **visão geral** à esquerda e, em seguida, o botão **excluir grupo de recursos** . Insira o nome **TestingBPLocks** para confirmar a exclusão e, em seguida, selecione **excluir** na parte inferior do painel.

   A notificação do portal **excluir grupo de recursos TestingBPLocks falha** aparece. O erro informa que, embora sua conta tenha permissão para excluir o grupo de recursos, o acesso é negado pela atribuição Blueprint. Lembre-se de que selecionamos o modo de bloqueio de Blueprint **somente leitura** durante a atribuição de Blueprint. O bloqueio Blueprint impede que uma conta com permissão, mesmo _proprietário_, exclua o recurso. Para obter mais informações, veja [bloqueio de recurso em esquemas](../concepts/resource-locking.md).

Essas etapas mostram que nossos recursos implantados agora estão protegidos com bloqueios de plantas que impedem a exclusão indesejada, mesmo de uma conta que tenha permissão para excluir os recursos.

## <a name="unassign-the-blueprint"></a>Cancelar a atribuição do plano gráfico

A última etapa é remover a atribuição da definição do Blueprint. A remoção da atribuição não remove os artefatos associados.

1. Selecione **todos os serviços** no painel esquerdo. Pesquise e selecione **plantas**.

1. Selecione a página **plantas atribuídas** à esquerda. Use os filtros para localizar a atribuição de Blueprint de **atribuição-bloqueada-storageAccount-TestingBPLocks** e, em seguida, selecione-a.

1. Selecione **desatribuir plano gráfico** na parte superior da página. Leia o aviso na caixa de diálogo de confirmação e selecione **OK**.

   Quando a atribuição Blueprint é removida, os bloqueios do Blueprint também são removidos. Os recursos podem ser novamente excluídos por uma conta com as permissões apropriadas.

1. Selecione **grupos de recursos** no menu do Azure e, em seguida, selecione **TestingBPLocks**.

1. Selecione a página **controle de acesso (iam)** à esquerda e, em seguida, selecione a guia **atribuições de função** .

A segurança do grupo de recursos mostra que a atribuição de Blueprint não tem mais acesso de _proprietário_ .

Depois que a notificação do portal de **remoção de atribuição de plano gráfico for bem-sucedida** aparecer, vá para a próxima etapa.

## <a name="clean-up-resources"></a>Limpar recursos

Quando tiver concluído este tutorial, exclua estes recursos:

- _TestingBPLocks_ do grupo de recursos
- Definição _de Blueprint bloqueada-storageAccount_

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você aprendeu a proteger novos recursos implantados com plantas do Azure. Para saber mais sobre os planos gráficos do Azure, prossiga para o artigo ciclo de vida do Blueprint.

> [!div class="nextstepaction"]
> [Saiba mais sobre o ciclo de vida do Blueprint](../concepts/lifecycle.md)