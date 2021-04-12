---
title: 'Tutorial: Proteger novos recursos com fechaduras'
description: Neste tutorial, utiliza as opções de bloqueio de recursos Azure Blueprints Leia Apenas e Não Elimine para proteger os recursos recém-implantados.
ms.date: 03/08/2021
ms.topic: tutorial
ms.openlocfilehash: 87da0f5a1fff2feb103b32533c8d314fb7690f80
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "102485746"
---
# <a name="tutorial-protect-new-resources-with-azure-blueprints-resource-locks"></a>Tutorial: Proteja novos recursos com bloqueios de recursos da Azure Blueprints

Com [as fechaduras de recursos](../concepts/resource-locking.md)da Azure Blueprints, pode proteger os recursos recém-implantados de serem adulterados, mesmo através de uma conta com a função _Proprietário._ Pode adicionar esta proteção nas definições de recursos criados por um artefacto do Azure Resource Manager (modelo ARM). A fechadura de recursos Blueprint é definida durante a atribuição da planta.

Neste tutorial, você completará estes passos:

> [!div class="checklist"]
> - Criar uma definição de planta
> - Marque a definição do seu projeto como **Publicado**
> - Atribua a sua definição de planta a uma subscrição existente **(conjunto de bloqueios de recursos)**
> - Inspecione o novo grupo de recursos
> - Desatribua a planta para remover as fechaduras

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

## <a name="create-a-blueprint-definition"></a>Criar uma definição de planta

Primeiro, criar a definição de planta.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Esquemas**.

1. Na página **'Iniciar'** à esquerda, selecione **Criar** na **criação de uma planta**.

1. Encontre a amostra **de planta em branco** no topo da página. Selecione **Iniciar com a planta em branco**.

1. Introduza esta informação no separador **Básicos:**

   - **Nome da planta**: Forneça um nome para a sua cópia da amostra de planta. Para este tutorial, usaremos o nome **de contagem de armazenamento bloqueado.**
   - **Descrição da planta**: Adicione uma descrição para a definição da planta. Utilização **Para testar o bloqueio de recursos de plantas em recursos implantados**.
   - **Localização de definição**: Selecione o botão de elipse (...) e, em seguida, selecione o grupo de gestão ou subscrição para guardar a definição do seu projeto para.

1. Selecione o **separador Artefactos** na parte superior da página ou selecione **Seguinte: Artefactos** na parte inferior da página.

1. Adicione um grupo de recursos ao nível de subscrição:
   1. Selecione a linha **de artefacto adicionar** sob **subscrição.**
   1. Selecione **Grupo de Recursos** sob o tipo **Artefacto**.
   1. Desaveie o **nome de exibição do artefacto** para **RGtoLock**.
   1. Deixe em branco as caixas de Nome e **Localização** **do Grupo de Recursos,** mas certifique-se de que a caixa de verificação está selecionada em cada propriedade para os tornar **parâmetros dinâmicos.**
   1. **Selecione Adicionar** para adicionar o artefacto à planta.

1. Adicione um modelo sob o grupo de recursos:
   1. Selecione a linha **de artefacto adicionar** sob a entrada **RGtoLock.**
   1. Selecione **o modelo do Gestor de Recursos Azure** sob o tipo De **artefacto,** desenhe **o nome do visor do artefacto** para o **StorageAccount** e deixe **a Descrição** em branco.
   1. No **separador Modelo,** cole o modelo ARM a seguir na caixa do editor. Depois de colar no modelo, **selecione Adicione** para adicionar o artefacto à planta.

      > [!NOTE]
      > Este passo define os recursos a serem implantados que ficam bloqueados pelo bloqueio de recursos Blueprint, mas não inclui as fechaduras de recursos Blueprint. As fechaduras de recursos da planta são definidas como um parâmetro da atribuição da planta.

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

1. **Selecione Guardar o Projeto** na parte inferior da página.

Este passo cria a definição de planta no grupo de gestão ou subscrição selecionados.

Depois da **definição de projeto Desemundo a** notificação do portal ter sido bem sucedida, vá para o passo seguinte.

## <a name="publish-the-blueprint-definition"></a>Publicar a definição de planta

A sua definição de planta foi agora criada no seu ambiente. É criado no modo **Draft** e deve ser publicado antes de poder ser atribuído e implementado.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Esquemas**.

1. Selecione a página **Definições de esquema** à esquerda. Utilize os filtros para encontrar a definição de planta **de contagem de armazenamento bloqueado** e, em seguida, selecione-os.

1. Selecione **Publicar esquema**, na parte superior da página. No novo painel à direita, insira **o 1.0** como **a Versão**. Esta propriedade é útil se você fizer uma mudança mais tarde. Introduza **notas de Alteração**, como a primeira versão publicada para bloquear recursos **implantados**. Em seguida, selecione **Publicar**, na parte inferior da página.

Este passo permite atribuir a planta a uma subscrição. Depois da definição do projeto ser publicada, ainda pode fazer alterações. Se fizer alterações, tem de publicar a definição com um novo valor de versão para rastrear as diferenças entre versões da mesma definição de projeto.

Depois de aparecer **a definição de projeto de publicação que sucedeu** à notificação do portal, vá para o passo seguinte.

## <a name="assign-the-blueprint-definition"></a>Atribuir a definição de planta

Após a publicação da definição de planta, pode atribuí-la a uma subscrição dentro do grupo de gestão onde a guardou. Neste passo, você fornece parâmetros para tornar cada implementação da definição de planta única.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Esquemas**.

1. Selecione a página **Definições de esquema** à esquerda. Utilize os filtros para encontrar a definição de planta **de contagem de armazenamento bloqueado** e, em seguida, selecione-os.

1. Selecione **Atribuir esquema**, na parte superior da página de definição do esquema.

1. Indique os valores dos parâmetros para a atribuição do esquema:

   - **Noções básicas**

     - **Subscrições**: Selecione uma ou mais das subscrições que estão no grupo de gestão onde guardou a definição do seu projeto. Se selecionar mais de uma subscrição, será criada uma atribuição para cada subscrição, utilizando os parâmetros introduzidos.
     - **Nome da atribuição**: O nome é pré-povoado com base no nome da definição de planta. Queremos que esta atribuição represente o bloqueio do novo grupo de recursos, por isso altere o nome de atribuição para o relatório **de armazenamento bloqueado de atribuição-TestingBPLocks**.
     - **Localização**: Selecione uma região para criar a identidade gerida. O Azure Blueprint utiliza esta identidade gerida para implementar todos os artefactos no esquema atribuído. Para saber mais, consulte [identidades geridas para recursos Azure.](../../../active-directory/managed-identities-azure-resources/overview.md)
       Para este tutorial, selecione **East US 2**.
     - **Versão de definição de planta**: Selecione a versão **1.0** publicada da definição de planta.

   - **Atribuição de bloqueio**

     Selecione o modo de bloqueio de planta **Apenas de leitura.** Para obter mais informações, veja [bloqueio de recurso em esquemas](../concepts/resource-locking.md).

     > [!NOTE]
     > Este passo configura o bloqueio de recursos blueprint nos recursos recém-implantados.

   - **Identidade Gerida**

     Utilize a opção **predefinida: Sistema atribuído**. Para mais informações, consulte [identidades geridas.](../../../active-directory/managed-identities-azure-resources/overview.md)

   - **Parâmetros dos artefactos**

     Os parâmetros definidos nesta secção aplicam-se ao artefacto sob o qual são definidos. Estes parâmetros são [parâmetros dinâmicos](../concepts/parameters.md#dynamic-parameters) porque são definidos durante a atribuição da planta. Para cada artefacto, desa um valor de parâmetro para o que vê na coluna **Valor.**

     |Nome do artefacto|Tipo de artefacto|Nome do parâmetro|Valor|Descrição|
     |-|-|-|-|-|
     |Grupo de recursos RGtoLock|Grupo de recursos|Name|TestebPLocks|Define o nome do novo grupo de recursos para aplicar fechaduras de planta a.|
     |Grupo de recursos RGtoLock|O grupo de recursos|A localização|E.U.A. Oeste 2|Define a localização do novo grupo de recursos para aplicar fechaduras de planta para.|
     |StorageAccount|Modelo do Resource Manager|armazenamentoAccountType (Contagem de armazenamento)|Standard_GRS|O armazém SKU. O valor _predefinido_ é Standard_LRS .|

1. Depois de introduzir todos os parâmetros, **selecione Atribuir** na parte inferior da página.

Este passo implementa os recursos definidos e configura a atribuição de **bloqueio** selecionada. Pode levar até 30 minutos para aplicar fechaduras de planta.

Depois de aparecer **a definição do projeto de atribuição de** notificação do portal, vá para o passo seguinte.

## <a name="inspect-resources-deployed-by-the-assignment"></a>Inspecione os recursos utilizados pela atribuição

A atribuição cria o grupo de recursos _TestingBPLocks_ e a conta de armazenamento implementada pelo artefacto do modelo ARM. O novo grupo de recursos e o estado de bloqueio selecionado são apresentados na página de detalhes da atribuição.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Esquemas**.

1. Selecione a página **de plantas atribuídas** à esquerda. Utilize os filtros para encontrar a **atribuição de armazenamento bloqueado-contagem-TestingBPLocks** design e, em seguida, selecione-o.

   A partir desta página, podemos ver que a atribuição foi bem sucedida e que os recursos foram implantados com o novo estado de bloqueio de plantas. Se a atribuição for atualizada, a **operação** de atribuição mostra detalhes sobre a implementação de cada versão de definição. Pode selecionar o grupo de recursos para abrir a página da propriedade.

1. Selecione o grupo de recursos **TestingBPLocks.**

1. Selecione a página **de controlo de acesso (IAM)** à esquerda. Em seguida, selecione o **separador Funções.**

   Aqui vemos que a _atribuição de armazenamento bloqueado-armazenamento-testingBPLocks_ design design tem a função _proprietário._ Tem este papel porque este papel foi usado para implantar e bloquear o grupo de recursos.

1. Selecione o **separador Descoduções Desemacorar.**

   A atribuição da planta criou uma [atribuição de negação](../../../role-based-access-control/deny-assignments.md) no grupo de recursos implantado para impor o modo de bloqueio de planta **Read Only.** A atribuição de negação impede que alguém com direitos adequados no **separador de atribuições** de Função tome ações específicas. A atribuição de negação afeta _todos os principais._

   Para obter informações sobre a exclusão de um principal de uma atribuição de negação, consulte [o bloqueio de recursos de plantas](../concepts/resource-locking.md#exclude-a-principal-from-a-deny-assignment).

1. Selecione a atribuição de negação e, em seguida, selecione a página **Permissões Negadas** à esquerda.

   A atribuição de negação está a impedir todas as operações com a configuração **\* *_ e _* Action,** mas permite ler o acesso excluindo **\* /ler** via **NotActions**.

1. No portal Azure, selecione **TestingBPLocks - Access control (IAM)**. Em seguida, selecione a página **'Vista Geral'** à esquerda e, em seguida, o botão **de grupo de recursos Delete.** Introduza o nome **TestingBPLocks** para confirmar a eliminação e, em seguida, **selecione Eliminar** na parte inferior do painel.

   A notificação do portal **Eliminar o grupo de recursos TestingBPLocks falhou.** O erro afirma que, embora a sua conta tenha permissão para eliminar o grupo de recursos, o acesso é negado pela atribuição do projeto. Lembre-se que selecionámos o modo de bloqueio de planta **Read Only** durante a atribuição da planta. O bloqueio da planta impede que uma conta com permissão, mesmo _proprietário,_ a supressão do recurso. Para obter mais informações, veja [bloqueio de recurso em esquemas](../concepts/resource-locking.md).

Estes passos mostram que os nossos recursos implantados estão agora protegidos com fechaduras de planta que impedem a eliminação indesejada, mesmo de uma conta que tem permissão para apagar os recursos.

## <a name="unassign-the-blueprint"></a>Unassign the blueprint

O último passo é remover a atribuição da definição de planta. Remover a missão não remove os artefactos associados.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Esquemas**.

1. Selecione a página **de plantas atribuídas** à esquerda. Utilize os filtros para encontrar a **atribuição de armazenamento bloqueado-contagem-TestingBPLocks** design e, em seguida, selecione-o.

1. **Selecione a planta de adign** no topo da página. Leia o aviso na caixa de diálogo de confirmação e, em seguida, selecione **OK**.

   Quando a atribuição da planta é removida, as fechaduras da planta também são removidas. Os recursos podem ser novamente eliminados por uma conta com permissões apropriadas.

1. Selecione **grupos** de recursos a partir do menu Azure e, em seguida, selecione **TestingBPLocks**.

1. Selecione a página **de controlo de acesso (IAM)** à esquerda e, em seguida, selecione o **separador Atribuições de Função.**

A segurança do grupo de recursos mostra que a atribuição do projeto já não tem acesso _ao Proprietário._

Depois de aparecer a notificação do portal **de remoção do portal,** vá para o passo seguinte.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando terminar este tutorial, elimine estes recursos:

- Grupo de recursos _TestingBPLocks_
- Definição de planta _contagem de armazenamento bloqueado_

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a proteger novos recursos implantados com a Azure Blueprints. Para saber mais sobre a Azure Blueprints, continue para o artigo do ciclo de vida do projeto.

> [!div class="nextstepaction"]
> [Conheça o ciclo de vida da planta](../concepts/lifecycle.md)