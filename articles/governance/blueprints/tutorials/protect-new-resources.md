---
title: 'Tutorial: Proteja novos recursos com fechaduras'
description: Neste tutorial, utiliza as opções de bloqueios de recursos do Azure Blueprints Leia Apenas e Não Elimine para proteger os recursos recém-implantados.
ms.date: 11/21/2019
ms.topic: tutorial
ms.openlocfilehash: ee57ff0c08f4fb8aa710dd2fa4dcef664484973d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "74327450"
---
# <a name="tutorial-protect-new-resources-with-azure-blueprints-resource-locks"></a>Tutorial: Proteja novos recursos com fechaduras de recursos da Azure Blueprints

Com [os bloqueios](../concepts/resource-locking.md)de recursos da Azure Blueprints, pode proteger os recursos recentemente implantados de serem adulterados, mesmo por uma conta com o papel _de Proprietário._ Pode adicionar esta proteção nas definições de plantas de recursos criados por um artefacto de modelo de Gestor de Recursos.

Neste tutorial, você completará estes passos:

> [!div class="checklist"]
> - Criar uma definição de planta
> - Marque a sua definição de projeto como **Publicado**
> - Atribuir a sua definição de projeto a uma subscrição existente
> - Inspecione o novo grupo de recursos
> - Desatribua a planta para remover as fechaduras

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

## <a name="create-a-blueprint-definition"></a>Criar uma definição de planta

Primeiro, criar a definição de planta.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Plantas**.

1. Na página **de início** de ficando à esquerda, selecione **Criar** sob criar **uma planta**.

1. Encontre a amostra da planta **em branco** no topo da página. Selecione **Começar com a planta em branco**.

1. Introduza esta informação no separador **Basics:**

   - **Nome**da planta : Forneça um nome para a sua cópia da amostra de plantas. Para este tutorial, usaremos o nome **de armazenagem bloqueada.**
   - **Descrição da planta**: Adicione uma descrição para a definição da planta. Utilização Para testar o bloqueio do **recurso de plantas em recursos implantados**.
   - **Localização da definição**: Selecione o botão de elipse (...) e, em seguida, selecione o grupo de gestão ou subscrição para guardar a definição de projeto para.

1. Selecione o separador **Artefactos** na parte superior da página ou selecione **Seguinte: Artefactos** na parte inferior da página.

1. Adicione um grupo de recursos ao nível da subscrição:
   1. Selecione a linha **de artefacto adicionar** em **Subscrição**.
   1. Selecione **Grupo de Recursos** sob o tipo de **Artefacto**.
   1. Defino o nome do **ecrã do Artefacto** para **RGtoLock**.
   1. Deixe as caixas de nome e **localização** do **Grupo de Recursos** em branco, mas certifique-se de que a caixa de verificação é selecionada em cada propriedade para torná-las **parâmetros dinâmicos**.
   1. Selecione **Adicionar** para adicionar o artefacto à planta.

1. Adicione um modelo sob o grupo de recursos:
   1. Selecione a linha **de artefacto adicionar** sob a entrada **RGtoLock.**
   1. Selecione **o modelo do Gestor de Recursos Azure** sob o tipo de **artefacto,** coloque o nome do **ecrã do artefacto** para **StorageAccount**, e deixe a **descrição** em branco.
   1. No separador **Modelo,** cola o seguinte modelo de Gestor de Recursos na caixa do editor.
      Depois de colar no modelo, selecione **Adicionar** para adicionar o artefacto à planta.

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

1. Selecione **Guardar Rascunho** na parte inferior da página.

Este passo cria a definição de projeto no grupo de gestão selecionado ou subscrição.

Depois da definição de **planta De salvação ter sido bem sucedida,** a notificação do portal aparece, vá para o passo seguinte.

## <a name="publish-the-blueprint-definition"></a>Publicar a definição de projeto

A sua definição de projeto foi agora criada no seu ambiente. É criado em modo **Draft** e deve ser publicado antes de ser atribuído e implantado.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Plantas**.

1. Selecione a página de **definições** de Blueprint à esquerda. Utilize os filtros para encontrar a definição de planta **de armazenamento bloqueado** e, em seguida, selecione-a.

1. Selecione **Publicar a planta** no topo da página. No novo painel à direita, insira **1.0** como **versão**. Esta propriedade é útil se você fizer uma mudança mais tarde. Insira **as notas de alteração**, como a primeira versão publicada para bloquear os recursos implantados do **projeto.** Em seguida, **selecione Publicar** na parte inferior da página.

Este passo permite atribuir a planta a uma subscrição. Após a publicação da definição de projeto, ainda pode fazer alterações. Se fizer alterações, tem de publicar a definição com um novo valor de versão para acompanhar as diferenças entre versões da mesma definição de projeto.

Depois de a definição de projeto de **publicação ter sido bem sucedida,** a notificação do portal aparece, vá para o passo seguinte.

## <a name="assign-the-blueprint-definition"></a>Atribuir a definição de planta

Após a publicação da definição de projeto, pode atribuí-la a uma subscrição dentro do grupo de gestão onde a guardou. Neste passo, fornece parâmetros para tornar cada implementação da definição de planta única.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Plantas**.

1. Selecione a página de **definições** de Blueprint à esquerda. Utilize os filtros para encontrar a definição de planta **de armazenamento bloqueado** e, em seguida, selecione-a.

1. Selecione **a planta de atribuição** na parte superior da página de definição de planta.

1. Fornecer os valores do parâmetro para a atribuição do projeto:

   - **Noções básicas**

     - **Subscrições**: Selecione uma ou mais subscrições que estão no grupo de gestão onde guardou a definição de projeto. Se selecionar mais do que uma subscrição, será criada uma atribuição para cada subscrição, utilizando os parâmetros em que entra.
     - **Nome de atribuição**: O nome é pré-povoado com base no nome da definição de projeto. Queremos que esta missão represente o bloqueio do novo grupo de recursos, por isso altere o nome de atribuição para a **conta de armazenamento bloqueado-testa-testadesBPLocks**.
     - **Localização**: Selecione uma região para criar a identidade gerida. O Azure Blueprint utiliza esta identidade gerida para implementar todos os artefactos no esquema atribuído. Para saber mais, consulte [identidades geridas para os recursos do Azure.](../../../active-directory/managed-identities-azure-resources/overview.md)
       Para este tutorial, selecione **East US 2**.
     - Versão de **definição**de planta : Selecione a versão **1.0** publicada da definição de projeto.

   - **Atribuição de bloqueio**

     Selecione o modo de bloqueio de planta **"Ler Apenas".** Para obter mais informações, veja [bloqueio de recurso em esquemas](../concepts/resource-locking.md).

   - **Identidade Gerida**

     Utilize a opção predefinida: **Sistema atribuído**. Para mais informações, consulte [identidades geridas.](../../../active-directory/managed-identities-azure-resources/overview.md)

   - **Parâmetros de artefacto**

     Os parâmetros definidos nesta secção aplicam-se ao artefacto sob o qual são definidos. Estes parâmetros são [parâmetros dinâmicos](../concepts/parameters.md#dynamic-parameters) porque são definidos durante a atribuição da planta. Para cada artefacto, defina o valor do parâmetro para o que vê na coluna **Valor.**

     |Nome do artefacto|Tipo de artefacto|Nome do parâmetro|Valor|Descrição|
     |-|-|-|-|-|
     |Grupo de recursos RGtoLock|Grupo de recursos|Nome|TestingBPLocks|Define o nome do novo grupo de recursos para aplicar fechaduras de plantas.|
     |Grupo de recursos RGtoLock|Grupo de recursos|Localização|E.U.A.Oeste 2|Define a localização do novo grupo de recursos para aplicar fechaduras de plantas.|
     |StorageAccount|Modelo do Resource Manager|armazenamentoAccountType (StorageAccount)|Standard_GRS|O armazém SKU. O valor predefinido é _Standard_LRS_.|

1. Depois de ter introduzido todos os parâmetros, selecione **Atribuir** na parte inferior da página.

Este passo implanta os recursos definidos e configura a atribuição de **bloqueio**selecionada . Pode levar até 30 minutos para aplicar fechaduras de plantas.

Depois da definição de **planta de atribuição ter sido bem sucedida,** a notificação do portal aparece, vá para o passo seguinte.

## <a name="inspect-resources-deployed-by-the-assignment"></a>Inspecionar os recursos utilizados pela atribuição

A atribuição cria o grupo de recursos _TestingBPLocks_ e a conta de armazenamento implementada pelo artefacto do modelo do Gestor de Recursos. O novo grupo de recursos e o estado de bloqueio selecionado são mostrados na página de detalhes da atribuição.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Plantas**.

1. Selecione a página de **plantas atribuída** à esquerda. Utilize os filtros para encontrar a atribuição da área de armazenamento bloqueado de **atribuição de armazenamento-TestBPLocks** e, em seguida, selecione-a.

   A partir desta página, podemos ver que a atribuição foi bem sucedida e que os recursos foram implantados com o novo estado de bloqueio de plantas. Se a atribuição for atualizada, a **operação de atribuição** mostra detalhes sobre a implementação de cada versão de definição. Pode selecionar o grupo de recursos para abrir a página da propriedade.

1. Selecione o grupo de recursos **TestingBPLocks.**

1. Selecione a página de controlo de **acesso (IAM)** à esquerda. Em seguida, selecione o separador de **tarefas role.**

   Aqui vemos que a atribuição de área de _armazenamento bloqueado de atribuição de armazenamento-testeBPLocks_ tem a função _proprietário._ Tem este papel porque este papel foi usado para implantar e bloquear o grupo de recursos.

1. Selecione o separador **de atribuições Negar.**

   A atribuição da planta criou uma [atribuição de negação](../../../role-based-access-control/deny-assignments.md) no grupo de recursos implantados para impor o modo de bloqueio de plantas **Read Only.** A atribuição de negação impede que alguém com direitos adequados sobre o separador de atribuições role atomar ações **específicas.** A atribuição de negação afeta _todos os princípios._

   Para obter informações sobre a exclusão de um diretor de uma atribuição de negação, consulte o bloqueio de recursos das [plantas.](../concepts/resource-locking.md#exclude-a-principal-from-a-deny-assignment)

1. Selecione a atribuição de negação e, em seguida, selecione a página **Permissões Negadas** à esquerda.

   A atribuição de negação está **\*** a impedir todas as operações com a configuração e **a configuração Action,** mas permite ler o acesso excluindo/ler ** \*** via **NotActions**.

1. No portal Azure, selecione **TestingBPLocks - Controle de acesso (IAM)**. Em seguida, selecione a página **'Visão Geral'** à esquerda e, em seguida, o botão do **grupo de recursos Delete.** Introduza o nome **TestingBPLocks** para confirmar a eliminação e, em seguida, **selecione Apagar** na parte inferior do painel.

   Notificação do portal **Apagar grupo de recursos TestingBPLocks falhados** aparece. O erro indica que, embora a sua conta tenha permissão para eliminar o grupo de recursos, o acesso é negado pela atribuição do projeto. Lembre-se que selecionamos o modo de bloqueio de plantas **Read Only** durante a atribuição da planta. O bloqueio da planta impede uma conta com permissão, mesmo _proprietário,_ de apagar o recurso. Para obter mais informações, veja [bloqueio de recurso em esquemas](../concepts/resource-locking.md).

Estas medidas mostram que os nossos recursos implantados estão agora protegidos com fechaduras de plantas que impedem a eliminação indesejada, mesmo a partir de uma conta que tem permissão para apagar os recursos.

## <a name="unassign-the-blueprint"></a>Desatribua a planta

O último passo é remover a atribuição da definição de projeto. Remover a atribuição não remove os artefactos associados.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Plantas**.

1. Selecione a página de **plantas atribuída** à esquerda. Utilize os filtros para encontrar a atribuição da área de armazenamento bloqueado de **atribuição de armazenamento-TestBPLocks** e, em seguida, selecione-a.

1. Selecione a **planta Unassign** na parte superior da página. Leia o aviso na caixa de diálogo de confirmação e, em seguida, selecione **OK**.

   Quando a atribuição da planta é removida, as fechaduras da planta também são removidas. Os recursos podem ser novamente eliminados por uma conta com permissões adequadas.

1. Selecione **grupos de recursos** do menu Azure e, em seguida, selecione **TestingBPLocks**.

1. Selecione a página de controlo de **acesso (IAM)** à esquerda e, em seguida, selecione o separador de **atribuições de funções.**

A segurança do grupo de recursos mostra que a atribuição de plantas já não tem acesso ao _Proprietário._

Depois da atribuição do **projeto de remoção ter sido bem sucedida,** a notificação do portal aparece, vá para o passo seguinte.

## <a name="clean-up-resources"></a>Limpar recursos

Quando terminar este tutorial, elimine estes recursos:

- Grupo de recursos _TestingBPLocks_
- Conta _de armazenamento bloqueado de_ definição de plantas

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a proteger novos recursos implantados com plantas Azure. Para saber mais sobre as Plantas Azure, continue o artigo blueprint lifecycle.

> [!div class="nextstepaction"]
> [Conheça o ciclo de vida da planta](../concepts/lifecycle.md)