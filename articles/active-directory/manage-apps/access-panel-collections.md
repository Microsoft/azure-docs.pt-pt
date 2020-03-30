---
title: Criar coleções para portais My Apps no Diretório Ativo do Azure Microsoft Docs
description: Use as coleções das Minhas Apps para personalizar as páginas das minhas apps para uma experiência mais simples das Minhas Aplicações para os seus utilizadores finais. Organize aplicações em grupos com separadores separados.
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
ms.date: 02/10/2020
ms.author: mimart
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
ms.openlocfilehash: c91b9ffc9e3487e492c91cb0f5825d0b725f9410
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77120094"
---
# <a name="create-collections-on-the-my-apps-portal"></a>Crie coleções no portal My Apps

Os seus utilizadores podem utilizar o portal My Apps para visualizar e iniciar as aplicações baseadas na nuvem a que têm acesso. Por predefinição, todas as aplicações a que um utilizador pode aceder estão listadas numa única página. Para melhor organizar esta página para os seus utilizadores, se tiver uma licença Azure AD Premium P1 ou P2 pode configurar coleções. Com uma coleção, pode agrupar aplicações relacionadas (por exemplo, por função de trabalho, tarefa ou projeto) e exibi-las num separador separado. Uma recolha aplica essencialmente um filtro às aplicações a que o utilizador já pode aceder, pelo que o utilizador vê apenas as aplicações da coleção que lhes foram atribuídas.

> [!NOTE]
> Este artigo aborda como um administrador pode ativar e criar coleções. Para obter informações para o utilizador final sobre como usar o portal e coleções das Minhas Apps, consulte [O Acesso e utilize coleções.](https://docs.microsoft.com/azure/active-directory/user-help/my-applications-portal-workspaces)

## <a name="enable-the-latest-my-apps-features"></a>Ativar as mais recentes funcionalidades das Minhas Apps

1. Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como administrador de utilizador ou administrador global.

2. Vá às > **definições**de utilizador **do Diretório Ativo Azure**.

3. Nas **pré-visualizações**da funcionalidade user , selecione Gerir as definições de **pré-visualização**do utilizador .

4. No caso de Utilizadores podem utilizar funcionalidades de **pré-visualização para As Minhas Apps,** escolha uma das seguintes opções:
   * **Selecionado** - Permite as funcionalidades para um grupo específico. Utilize a opção **Select uma opção** de grupo para selecionar o grupo para o qual pretende ativar as funcionalidades.  
   * **Tudo** - Permite as funcionalidades para todos os utilizadores.

> [!NOTE]
> Para abrir o portal My Apps, `https://myapps.microsoft.com` os utilizadores podem usar o `https://myapps.microsoft.com/contoso.com`link ou o link personalizado para a sua organização, como . Depois de ativar a nova experiência My Apps, a **experiência An update My Applications está disponível** no topo da página My Apps, e os utilizadores podem selecionar **Experimentá-lo** para ver a nova experiência. Para parar de usar a nova experiência, os utilizadores podem selecionar **Sim** do banner leave **new experience** no topo da página.

## <a name="create-a-collection"></a>Criar uma coleção

Para criar uma coleção, deve ter uma licença Azure AD Premium P1 ou P2.

1. Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como administrador com uma licença Azure AD Premium P1 ou P2.

2. Vá a > **Aplicações empresariais**de **diretório ativo azure.**

3. Under **Manage**, selecione **Collections**.

4. Selecione **Nova coleção.** Na página **de recolha Nova,** insira um **Nome** para a coleção (recomendamos não usar a "recolha" no nome. Em seguida, introduza uma **Descrição**.

   ![Nova página de recolha](media/acces-panel-collections/new-collection.png)

5. Selecione o separador **Aplicações.** Selecione **+ Adicionar aplicação,** e depois na página **de aplicações Adicionar,** selecione todas as aplicações que pretende adicionar à recolha, ou utilize a caixa **de pesquisa** para encontrar aplicações.

   ![Adicione uma aplicação à coleção](media/acces-panel-collections/add-applications.png)

6. Quando terminar de adicionar aplicações, **selecione Adicionar**. Aparece a lista de aplicações selecionadas. Pode utilizar as setas para alterar a ordem das aplicações na lista. Para deslocalizar uma aplicação ou eliminá-la da coleção, selecione o menu **Mais** (**...**).

7. Selecione o separador **Owners.** Selecione **+ Adicione utilizadores e grupos,** e depois na página adicionar utilizadores e **grupos,** selecione os utilizadores ou grupos a que pretende atribuir a propriedade. Quando terminar de selecionar utilizadores e grupos, escolha **Selecionar**.

9. Selecione o separador **Utilizadores e grupos.** Selecione **+ Adicione utilizadores e grupos,** e depois na página **Adicionar utilizadores e grupos,** selecione os utilizadores ou grupos a que pretende atribuir a recolha. Ou utilize a caixa **de pesquisa** para encontrar utilizadores ou grupos. Quando terminar de selecionar utilizadores e grupos, escolha **Selecionar**.

   ![Adicionar utilizadores e grupos](media/acces-panel-collections/add-users-and-groups.png)

11. Selecione **Rever + Criar**. As propriedades da nova coleção aparecem.


## <a name="view-audit-logs"></a>Ver registos de auditoria

Os registos de auditoria registam as minhas operações de recolha de apps, incluindo ações de criação de recolha de utilizadores finais. Os seguintes eventos são gerados a partir de My Apps:

* Criar coleção
* Editar coleção
* Eliminar coleção
* Lançar uma aplicação (utilizador final)
* Adição de aplicação self-service (utilizador final)
* Eliminação da aplicação self-service (utilizador final)

Pode aceder a registos de auditoria no [portal Azure](https://portal.azure.com) selecionando**registos** de auditoria de > **aplicações** > empresariais de **diretório ativo azure**na secção Atividade. Para **serviço,** selecione **Minhas Apps**.

## <a name="get-support-for-my-account-pages"></a>Obtenha suporte para as páginas da Minha Conta

A partir da página My Apps, um utilizador pode selecionar **a minha conta** > Ver a**minha conta** para abrir as definições da sua conta. Na página Azure AD **My Account,** os utilizadores podem gerir as suas informações de segurança, dispositivos, senhas e muito mais. Também podem aceder às definições da sua conta do Office.

Caso necessite de submeter um pedido de apoio para um problema com a página da conta Azure AD ou a página da conta do Office, siga estes passos para que o seu pedido seja encaminhado corretamente: 

* Para problemas com a página **Azure AD "AD My Account",** abra um pedido de apoio dentro do portal Azure. Vá ao **portal** > **Azure Azure Ative Directory** > Novo pedido de**apoio.**

* Para problemas com a página **"A minha conta" do Office,** abra um pedido de suporte dentro do centro de administração da Microsoft 365. Vá ao > **Suporte**do centro de **administração microsoft 365**. 

## <a name="next-steps"></a>Passos seguintes
[Experiências de utilizador final para aplicações no Diretório Ativo do Azure](end-user-experiences.md)