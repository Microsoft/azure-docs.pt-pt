---
title: 'Quickstart: Criar um grupo de gestão com portal'
description: Neste quickstart, você usa o portal Azure para criar um grupo de gestão para organizar os seus recursos numa hierarquia de recursos.
ms.date: 02/05/2021
ms.topic: quickstart
ms.openlocfilehash: 5a336fe4caf5d2f825a93b5c7b8f8d05f6255c39
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2021
ms.locfileid: "99592437"
---
# <a name="quickstart-create-a-management-group"></a>Quickstart: Criar um grupo de gestão

Os grupos de gestão são contentores que o ajudam a gerir o acesso, a política e o cumprimento em várias subscrições. Crie estes recipientes para construir uma hierarquia eficaz e eficiente que possa ser usada com [a Azure Policy](../policy/overview.md) e [os Controlos de Acesso Baseados em Funções Azure](../../role-based-access-control/overview.md). Para obter mais informações sobre grupos de gestão, consulte [Organizar os seus recursos com grupos de gestão Azure.](overview.md)

O primeiro grupo de gestão criado no diretório pode demorar até 15 minutos para ser concluído. Há processos que correm pela primeira vez para configurar o serviço de grupos de gestão dentro do Azure para o seu diretório. Recebe uma notificação quando o processo estiver concluído. Para mais informações, consulte [a configuração inicial dos grupos de gestão.](./overview.md#initial-setup-of-management-groups)

## <a name="prerequisites"></a>Pré-requisitos

- Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

- Qualquer utilizador Azure AD no arrendatário pode criar um grupo de gestão sem que o grupo de gestão escreva a permissão atribuída a esse utilizador se a [proteção da hierarquia](./how-to/protect-resource-hierarchy.md#setting---require-authorization) não estiver ativada. Este novo grupo de gestão torna-se filho do Grupo de Gestão de Raízes ou do [grupo de gestão padrão](./how-to/protect-resource-hierarchy.md#setting---default-management-group) e o criador recebe uma atribuição de função de "Proprietário". O serviço de grupo de gestão permite esta capacidade para que as atribuições de funções não sejam necessárias ao nível da raiz. Nenhum utilizadores tem acesso ao Grupo de Gestão de Raízes quando este é criado. Para evitar o obstáculo de encontrar os Azure AD Global Admins para começar a usar grupos de gestão, permitimos a criação dos grupos de gestão iniciais ao nível da raiz.

### <a name="create-in-portal"></a>Criar em portal

1. Inicie sessão no [portal Azure](https://portal.azure.com).

1. Selecione **Todos os serviços**  >  **Gestão + governação**.

1. Selecione **Grupos de Gestão**.

1. Selecione **+ Adicionar grupo de gestão**.

   :::image type="content" source="./media/main.png" alt-text="Screenshot da página de grupos de gestão mostrando grupos de gestão de crianças e subscrições." border="false":::

1. Sair **Crie novos** selecionados e preencha o campo de ID do grupo de gestão.

   - O **ID do Grupo de Gestão** é o identificador único do diretório que é usado para submeter comandos neste grupo de gestão. Este identificador não é editável após a criação, pois é usado em todo o sistema Azure para identificar este grupo. O [grupo de gestão de raiz](./overview.md#root-management-group-for-each-directory) é automaticamente criado com um ID que é o ID do Diretório Ativo Azure. Para todos os outros grupos de gestão, atribua uma identificação única.
   - O campo de nome de exibição é o nome que é apresentado dentro do portal Azure. Um nome de exibição separado é um campo opcional ao criar o grupo de gestão e pode ser alterado a qualquer momento.

   :::image type="content" source="./media/create_context_menu.png" alt-text="Screenshot das opções do &quot;Add Management Group&quot; para criar um novo grupo de gestão.":::

1. Selecione **Guardar**.

## <a name="clean-up-resources"></a>Limpar os recursos

Para remover o grupo de gestão criado, siga estes passos:

1. Selecione **Todos os serviços**  >  **Gestão + governação**.

1. Selecione **Grupos de Gestão**.

1. Encontre o grupo de gestão acima criado, selecione-o e, em seguida, selecione **Detalhes** ao lado do nome.
   Em seguida, **selecione Eliminar** e confirmar a solicitação.

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, criou um grupo de gestão para organizar a sua hierarquia de recursos. O grupo de gestão pode realizar subscrições ou outros grupos de gestão.

Para saber mais sobre grupos de gestão e como gerir a sua hierarquia de recursos, continue a:

> [!div class="nextstepaction"]
> [Gerir os seus recursos com grupos de gestão](./manage.md)