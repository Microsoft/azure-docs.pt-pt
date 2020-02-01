---
title: Criar coleções para portais My Apps no Diretório Ativo do Azure Microsoft Docs
description: Use as coleções das Minhas Apps para personalizar as páginas das minhas apps para uma experiência mais simples das Minhas Aplicações para os seus utilizadores finais. Organize aplicativos em grupos com guias separadas.
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
ms.date: 01/28/2020
ms.author: mimart
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
ms.openlocfilehash: f1ed80ad87d81c2ec1bdfe50e9cd4556f141507b
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76907477"
---
# <a name="create-collections-on-the-my-apps-portal"></a>Crie coleções no portal My Apps

Os seus utilizadores podem utilizar o portal My Apps para visualizar e iniciar as aplicações baseadas na nuvem a que têm acesso. Por padrão, todos os aplicativos que um usuário pode acessar são listados juntos em uma única página. Para melhor organizar esta página para os seus utilizadores, se tiver uma licença Azure AD Premium P1 ou P2 pode configurar coleções. Com uma coleção, pode agrupar aplicações relacionadas (por exemplo, por função de trabalho, tarefa ou projeto) e exibi-las num separador separado. Uma recolha aplica essencialmente um filtro às aplicações a que o utilizador já pode aceder, pelo que o utilizador vê apenas as aplicações da coleção que lhes foram atribuídas.

> [!NOTE]
> Este artigo aborda como um administrador pode ativar e criar coleções. Para obter informações para o utilizador final sobre como usar o portal e coleções das Minhas Apps, consulte [O Acesso e utilize coleções.](https://docs.microsoft.com/azure/active-directory/user-help/my-applications-portal-workspaces)

## <a name="enable-my-apps-preview-features"></a>Habilitar recursos de visualização de meus aplicativos

1. Abra o [**portal do Azure**](https://portal.azure.com/) e entre como administrador de usuário ou administrador global.

2. Vá para **Azure Active Directory** > **configurações do usuário**.

3. Em **visualizações de recurso de usuário**, selecione **gerenciar configurações de visualização de recurso do usuário**.

4. Em **os usuários podem usar os recursos de visualização para meus aplicativos**, escolha uma das seguintes opções:
   * **Selecionado** – habilita os recursos de visualização para um grupo específico. Use a opção **selecionar um grupo** para selecionar o grupo para o qual você deseja habilitar os recursos de visualização.  
   * **All** – habilita recursos de visualização para todos os usuários.

> [!NOTE]
> Para abrir o portal meus aplicativos, os usuários podem usar o link `https://myapps.microsoft.com` ou o link personalizado para sua organização, como `https://myapps.microsoft.com/contoso.com`. Depois de habilitar a nova experiência meus aplicativos, a faixa **uma experiência atualizada meus aplicativos está disponível** será exibida na parte superior da página meus aplicativos, e os usuários poderão selecionar **experimentar** para exibir a nova experiência. Para parar de usar a nova experiência, os usuários podem selecionar **Sim** na faixa **deixar nova experiência** na parte superior da página.

## <a name="create-a-collection"></a>Criar uma coleção

Para criar uma coleção, deve ter uma licença Azure AD Premium P1 ou P2.

1. Abra o [**portal do Azure**](https://portal.azure.com/) e entre como um administrador com uma licença Azure ad Premium P1 ou P2.

2. Vá para **Azure Active Directory** > **aplicativos empresariais**.

3. Under **Manage**, selecione **Collections**.

4. Selecione **Nova coleção.** Na página **de recolha Nova,** insira um **Nome** para a coleção (recomendamos não usar a "recolha" no nome. Em seguida, insira uma **Descrição**.

   ![Nova página de recolha](media/acces-panel-collections/new-collection.png)

5. Selecione o separador **Aplicações.** Selecione **+ Adicionar aplicação,** e depois na página **de aplicações Adicionar,** selecione todas as aplicações que pretende adicionar à recolha, ou utilize a caixa **de pesquisa** para encontrar aplicações.

   ![Adicione uma aplicação à coleção](media/acces-panel-collections/add-applications.png)

6. Quando terminar de adicionar aplicações, **selecione Adicionar**. A lista de aplicativos selecionados é exibida. Pode utilizar as setas para alterar a ordem das aplicações na lista. Para deslocalizar uma aplicação ou eliminá-la da coleção, selecione o menu **Mais** ( **...** ).

7. Selecione o separador **Owners.** Selecione **+ Adicione utilizadores e grupos,** e depois na página adicionar utilizadores e **grupos,** selecione os utilizadores ou grupos a que pretende atribuir a propriedade. Quando você terminar de selecionar usuários e grupos, escolha **selecionar**.

9. Selecione o separador **Utilizadores e grupos.** Selecione **+ Adicione utilizadores e grupos,** e depois na página **Adicionar utilizadores e grupos,** selecione os utilizadores ou grupos a que pretende atribuir a recolha. Ou use a caixa de **pesquisa** para localizar usuários ou grupos. Quando você terminar de selecionar usuários e grupos, escolha **selecionar**.

   ![Adicionar usuários e grupos](media/acces-panel-collections/add-users-and-groups.png)

11. Selecione **revisão + criar**. As propriedades da nova coleção aparecem.


## <a name="view-audit-logs"></a>Ver registos de auditoria

Os registos de auditoria registam as minhas operações de recolha de apps, incluindo ações de criação de recolha de utilizadores finais. Os seguintes eventos são gerados em meus aplicativos:

* Criar coleção
* Editar coleção
* Eliminar coleção
* Iniciar um aplicativo (usuário final)
* Adição de aplicativo de autoatendimento (usuário final)
* Exclusão de aplicativo de autoatendimento (usuário final)

Você pode acessar os logs de auditoria no [portal do Azure](https://portal.azure.com) selecionando **Azure Active Directory** > **aplicativos empresariais** > **logs de auditoria** na seção atividade. Para **serviço**, selecione **meus aplicativos**.

## <a name="get-support-for-my-account-pages"></a>Obter suporte para páginas da minha conta

Na página meus aplicativos, um usuário pode selecionar **minha conta** > **Exibir minha conta** para abrir suas configurações de conta. Na página **minha conta** do Azure AD, os usuários podem gerenciar suas informações de segurança, dispositivos, senhas e muito mais. Eles também podem acessar suas configurações de conta do Office.

Caso você precise enviar uma solicitação de suporte para um problema com a página conta do Azure AD ou a página conta do Office, siga estas etapas para que sua solicitação seja roteada corretamente: 

* Para problemas com a página **"minha conta" do Azure ad** , abra uma solicitação de suporte no portal do Azure. Vá para **portal do Azure** > **Azure Active Directory** > **nova solicitação de suporte**.

* Para problemas com a página **"minha conta" do Office** , abra uma solicitação de suporte no centro de administração do Microsoft 365. Vá para **Microsoft 365 centro de administração** > **suporte**. 

## <a name="next-steps"></a>Passos seguintes
[Experiências do usuário final para aplicativos no Azure Active Directory](end-user-experiences.md)