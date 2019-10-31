---
title: Criar espaços de trabalho para os portais dos meus aplicativos no Azure Active Directory | Microsoft Docs
description: Use meus espaços de trabalho de aplicativos para personalizar páginas meus aplicativos para uma experiência mais simples de meus aplicativos para seus usuários finais. Organize aplicativos em grupos com guias separadas.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/29/2019
ms.author: mimart
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5a986c2032fc41bbd34230c5b0ead745f88b000f
ms.sourcegitcommit: fa5ce8924930f56bcac17f6c2a359c1a5b9660c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2019
ms.locfileid: "73199819"
---
# <a name="create-workspaces-on-the-my-apps-preview-portal"></a>Criar espaços de trabalho no portal meus aplicativos (versão prévia)

Os usuários podem usar o portal meus aplicativos (versão prévia) para exibir e iniciar os aplicativos baseados em nuvem aos quais eles têm acesso. Por padrão, todos os aplicativos que um usuário pode acessar são listados juntos em uma única página. Para organizar melhor essa página para seus usuários, se você tiver uma licença Azure AD Premium P1 ou P2, poderá configurar espaços de trabalho. Com um espaço de trabalho, você pode agrupar aplicativos relacionados (por exemplo, por função de trabalho, tarefa ou projeto) e exibi-los em uma guia separada. Um espaço de trabalho basicamente aplica um filtro aos aplicativos que um usuário já pode acessar, para que o usuário veja apenas os aplicativos no espaço de trabalho que foram atribuídos a eles.

> [!NOTE]
> Este artigo aborda como um administrador pode habilitar e criar espaços de trabalho. Para obter informações para o usuário final sobre como usar o portal meus aplicativos e espaços de trabalho, consulte [acessar e usar espaços de trabalho](https://docs.microsoft.com/azure/active-directory/user-help/my-applications-portal-workspaces).

## <a name="enable-my-apps-preview-features"></a>Habilitar recursos de visualização de meus aplicativos

1. Abra o [**portal do Azure**](https://portal.azure.com/) e entre como administrador de usuário ou administrador global.

2. Vá para **Azure Active Directory** > **configurações do usuário**.

3. Em **visualizações de recurso de usuário**, selecione **gerenciar configurações de visualização de recurso do usuário**.

4. Em **os usuários podem usar os recursos de visualização para meus aplicativos**, escolha uma das seguintes opções:
   * **Selecionado** – habilita os recursos de visualização para um grupo específico. Use a opção **selecionar um grupo** para selecionar o grupo para o qual você deseja habilitar os recursos de visualização.  
   * **All** – habilita recursos de visualização para todos os usuários.

   ![Recursos de visualização do usuário](media/access-panel-workspaces/user-preview-features.png)

> [!NOTE]
> Para abrir o portal meus aplicativos, os usuários podem usar o link `https://myapps.microsoft.com` ou o link personalizado para sua organização, como `https://myapps.microsoft.com/contoso.com`. Se os usuários não forem redirecionados para a versão de visualização meus aplicativos, os usuários deverão tentar `https://myapplications.microsoft.com` ou `https://myapplications.microsoft.com/contoso.com`.

## <a name="create-a-workspace"></a>Criar áreas de trabalho

Para criar um espaço de trabalho, você deve ter uma licença Azure AD Premium P1 ou P2.

1. Abra o [**portal do Azure**](https://portal.azure.com/) e entre como um administrador com uma licença Azure ad Premium P1 ou P2.

2. Vá para **Azure Active Directory** > **aplicativos empresariais**.

3. Em **gerenciar**, selecione **espaços de trabalho (versão prévia)** .

4. Selecione **novo espaço de trabalho**. Na página **novo espaço de trabalho** , insira um **nome** para o espaço de trabalho (é recomendável não usar "espaço de trabalho" no nome. Em seguida, insira uma **Descrição**.

   ![Criar um novo espaço de trabalho](media/access-panel-workspaces/new-workspace.png)

5. Selecione **revisão + criar**. As propriedades do novo espaço de trabalho são exibidas.

6. Selecione a guia **aplicativos** . Em **adicionar aplicativos**, selecione todos os aplicativos que você deseja adicionar ao espaço de trabalho ou use a caixa de **pesquisa** para localizar aplicativos. 

   ![Adicionar aplicativos ao espaço de trabalho](media/access-panel-workspaces/add-applications.png)

7. Selecione **Adicionar**. A lista de aplicativos selecionados é exibida. Você pode usar as setas para cima e para baixo para alterar a ordem dos aplicativos na lista.

   ![Lista de aplicativos no espaço de trabalho](media/access-panel-workspaces/add-applications-list.png)

8. Selecione a guia **usuários e grupos** . Para adicionar um usuário ou grupo, selecione **Adicionar usuário**. 

9. Na página **selecionar Membros** , selecione os usuários ou grupos aos quais você deseja atribuir o espaço de trabalho. Ou use a caixa de **pesquisa** para localizar usuários ou grupos.

   ![Atribuir usuários e grupos ao espaço de trabalho](media/access-panel-workspaces/add-users-and-groups.png)

10. Quando você terminar de selecionar usuários e grupos, escolha **selecionar**.

11. Para alterar a função de um usuário de **acesso de leitura** para **proprietário** ou vice-versa, clique na função atual e selecione uma nova função.

    ![Atribuir funções a usuários e grupos](media/access-panel-workspaces/users-groups-list-role.png)

## <a name="view-audit-logs"></a>Ver registos de auditoria

Os logs de auditoria registram minhas operações de espaços de trabalho de aplicativos, incluindo ações de usuário final de criação de espaço de trabalho. Os seguintes eventos são gerados em meus aplicativos:

* Criar área de trabalho
* Editar espaço de trabalho
* Excluir espaço de trabalho
* Iniciar um aplicativo (usuário final)
* Adição de aplicativo de autoatendimento (usuário final)
* Exclusão de aplicativo de autoatendimento (usuário final)

Você pode acessar os logs de auditoria no [portal do Azure](https://portal.azure.com) selecionando **Azure Active Directory** > **aplicativos empresariais** > **logs de auditoria** na seção atividade. Para **serviço**, selecione **meus aplicativos**.

   ![Atribuir funções a usuários e grupos](media/access-panel-workspaces/audit-log-myapps.png)


## <a name="next-steps"></a>Passos seguintes
[Experiências do usuário final para aplicativos no Azure Active Directory](end-user-experiences.md)