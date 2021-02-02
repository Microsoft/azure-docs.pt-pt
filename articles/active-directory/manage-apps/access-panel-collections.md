---
title: Crie coleções para os portais my Apps em Azure Ative Directory | Microsoft Docs
description: Utilize as coleções My Apps para personalizar as páginas das minhas apps para uma experiência mais simples das Minhas Apps para os seus utilizadores finais. Organize aplicações em grupos com separadores.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 02/10/2020
ms.author: kenwith
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7743a1af54b01f848c4ac6f0cb1d4526d66f8132
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/02/2021
ms.locfileid: "99254987"
---
# <a name="create-collections-on-the-my-apps-portal"></a>Criar coleções no portal As Minhas Aplicações

Os seus utilizadores podem utilizar o portal My Apps para visualizar e iniciar as aplicações baseadas na nuvem a que têm acesso. Por predefinição, todas as aplicações a que um utilizador pode aceder estão listadas numa única página. Para organizar melhor esta página para os seus utilizadores, se tiver uma licença Azure AD Premium P1 ou P2 pode configurar coleções. Com uma coleção, pode agrupar aplicações relacionadas (por exemplo, por função de trabalho, tarefa ou projeto) e exibi-las num separador. Uma coleção aplica essencialmente um filtro às aplicações que um utilizador já pode aceder, pelo que o utilizador vê apenas as aplicações na recolha que lhes foram atribuídas.

> [!NOTE]
> Este artigo abrange como um administrador pode ativar e criar coleções. Para obter informações para o utilizador final sobre como utilizar o portal e coleções My Apps, consulte [o Access e utilize coleções](../user-help/my-applications-portal-workspaces.md).

## <a name="enable-the-latest-my-apps-features"></a>Ativar as mais recentes funcionalidades das My Apps

1. Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como administrador de utilizador ou Administrador Global.

2. Aceda às definições do Utilizador **do Diretório Ativo Azure**  >  .

3. Nas **pré-visualizações da funcionalidade do Utilizador**, selecione','s **Manage's previews**.

4. Em **Utilizadores podem utilizar funcionalidades de pré-visualização para as minhas apps,** escolha uma das seguintes opções:
   * **Selecionado** - Ativa as funcionalidades para um grupo específico. Utilize a opção **De grupo Para** selecionar o grupo para o qual pretende ativar as funcionalidades.  
   * **Tudo** - Ativa as funcionalidades para todos os utilizadores.

> [!NOTE]
> Para abrir o portal My Apps, os utilizadores podem utilizar o link `https://myapps.microsoft.com` ou o link personalizado para a sua organização, `https://myapps.microsoft.com/contoso.com` como. Depois de ativar a nova experiência My Apps, o banner **de Aplicações An atualizado está disponível** será exibido no topo da página My Apps, e os utilizadores podem selecionar **Experimentá-lo** para ver a nova experiência. Para parar de usar a nova experiência, os utilizadores podem selecionar **Sim** a partir do **banner de experiência sair** no topo da página.

## <a name="create-a-collection"></a>Criar uma coleção

Para criar uma coleção, você deve ter uma licença Azure AD Premium P1 ou P2.

1. Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como administrador com uma licença Azure AD Premium P1 ou P2.

2. Vá a **Azure Ative Directory**  >  **Enterprise Applications**.

3. Em **Gestão**, selecione **Coleções**.

4. Selecione **Nova coleção**. Na nova página de **recolha,** insira um **Nome** para a coleção (recomendamos não utilizar "coleção" no nome. Em seguida, introduza uma **Descrição**.

   ![Nova página de coleção](media/acces-panel-collections/new-collection.png)

5. Selecione o separador **Aplicações.** Selecione **+ Adicionar a aplicação**, e, em seguida, na página **de aplicações Adicionar,** selecione todas as aplicações que pretende adicionar à coleção ou use a caixa **de Busca** para encontrar aplicações.

   ![Adicione uma aplicação à coleção](media/acces-panel-collections/add-applications.png)

6. Quando terminar de adicionar aplicações, **selecione Adicionar**. A lista de candidaturas selecionadas aparece. Pode utilizar as setas para cima para alterar a ordem das aplicações na lista. Para mover uma aplicação para baixo ou para a eliminar da coleção, selecione o menu **Mais** **(...**).

7. Selecione o **separador Proprietários.** **Selecione + Adicionar utilizadores e grupos**, e, em seguida, na página **'Adicionar' utilizadores e grupos,** selecione os utilizadores ou grupos a que pretende atribuir a propriedade. Quando terminar de selecionar utilizadores e grupos, escolha **Select**.

9. Selecione o **separador Utilizadores e grupos.** **Selecione + Adicionar utilizadores e grupos**, e, em seguida, na página **'Adicionar' utilizadores e grupos,** selecione os utilizadores ou grupos a que pretende atribuir a recolha. Ou use a caixa **de Pesquisa** para encontrar utilizadores ou grupos. Quando terminar de selecionar utilizadores e grupos, escolha **Select**.

   ![Adicionar utilizadores e grupos](media/acces-panel-collections/add-users-and-groups.png)

11. Selecione **Rever + Criar**. As propriedades da nova coleção aparecem.


## <a name="view-audit-logs"></a>Ver registos de auditoria

Os registos de auditoria registam as operações de recolha de My Apps, incluindo ações de utilizador final de criação de coleção. Os seguintes eventos são gerados a partir de My Apps:

* Criar coleção
* Editar coleção
* Eliminar coleção
* Lançar uma aplicação (utilizador final)
* Adicionar aplicações de self-service (utilizador final)
* Eliminação de aplicações de autosserviço (utilizador final)

Pode aceder aos registos de auditoria no [portal Azure](https://portal.azure.com) selecionando registos de Auditoria de Aplicações empresariais **do Azure Ative**  >  **Directory**  >   na secção Atividade. Para **Serviço**, selecione **As minhas aplicações**.

## <a name="get-support-for-my-account-pages"></a>Obtenha suporte para as páginas da Minha Conta

Na página My Apps, um utilizador pode selecionar **A minha conta** Ver a minha  >  **conta** para abrir as definições da sua conta. Na página Azure AD **My Account,** os utilizadores podem gerir as suas informações de segurança, dispositivos, senhas e muito mais. Também podem aceder às definições da conta do Office.

Caso necessite de submeter um pedido de apoio para um problema com a página da conta AZure ou com a página da conta do Office, siga estes passos para que o seu pedido seja encaminhado corretamente: 

* Para problemas com a página **Azure AD "A minha conta",** abra um pedido de apoio dentro do portal Azure. Vá ao **portal Azure**  >  **Ative Directory**  >  **Novo pedido de suporte**.

* Para problemas com a página **do Office "A minha conta",** abra um pedido de suporte dentro do centro de administração microsoft 365. Vá ao **Microsoft 365 admin center**  >  **Support**. 

## <a name="next-steps"></a>Passos seguintes
[Experiências de utilizador final para aplicações no Azure Ative Directory](end-user-experiences.md)