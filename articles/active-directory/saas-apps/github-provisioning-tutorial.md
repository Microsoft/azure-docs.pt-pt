---
title: 'Tutorial: Fornecimento de utilizadores para GitHub - Azure AD'
description: Aprenda a configurar o Diretório Ativo do Azure para fornecer e desfornecer automaticamente contas de utilizadores ao GitHub.
services: active-directory
documentationcenter: ''
author: ArvindHarinder1
manager: CelesteDG
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 82f7252f2d9cdd2c54fae593d8463bfe84bd6ce2
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77057656"
---
# <a name="tutorial-configure-github-for-automatic-user-provisioning"></a>Tutorial: Configure GitHub para fornecimento automático de utilizadores

O objetivo deste tutorial é mostrar-lhe os passos necessários para realizar no GitHub e Azure AD para fornecer e desfornecer automaticamente contas de utilizadores de Azure AD para GitHub.

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Um inquilino de diretório Azure Ative
* Uma organização GitHub criada na [GitHub Enterprise Cloud,](https://help.github.com/articles/github-s-products/#github-enterprise)que requer o plano de faturação da [GitHub Enterprise](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations)
* Uma conta de utilizador no GitHub com permissões da Admin para a organização
* Certifique-se de que o acesso da OAuth foi fornecido para a sua organização, conforme descrito [aqui](https://help.github.com/en/github/setting-up-and-managing-organizations-and-teams/approving-oauth-apps-for-your-organization)

> [!NOTE]
> A integração de provisionamento de AD Azure baseia-se na [API GitHub SCIM,](https://developer.github.com/v3/scim/)que está disponível para os clientes [gitHub Enterprise Cloud](https://help.github.com/articles/github-s-products/#github-enterprise) no plano de faturação da [GitHub Enterprise.](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations)

## <a name="assigning-users-to-github"></a>Atribuir utilizadores ao GitHub

O Azure Ative Directory utiliza um conceito chamado "atribuições" para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de conta de utilizador, apenas os utilizadores e grupos que foram "atribuídos" a uma aplicação em Azure AD são sincronizados. 

Antes de configurar e ativar o serviço de provisionamento, tem de decidir quais os utilizadores e/ou grupos em Azure AD que representam os utilizadores que precisam de acesso à sua aplicação GitHub. Uma vez decidido, pode atribuir estes utilizadores à sua aplicação GitHub seguindo as instruções aqui:

[Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-github"></a>Dicas importantes para atribuir utilizadores ao GitHub

* Recomenda-se que um único utilizador da AD Azure seja atribuído ao GitHub para testar a configuração de provisionamento. Usuários e/ou grupos adicionais podem ser atribuídos posteriormente.

* Ao atribuir um utilizador ao GitHub, deve selecionar a função **Utilizador** ou outra função específica de aplicação válida (se disponível) no diálogo de atribuição. A função **De Acesso Predefinido** não funciona para o provisionamento, e estes utilizadores são ignorados.

## <a name="configuring-user-provisioning-to-github"></a>Configurar o fornecimento de utilizadores ao GitHub

Esta secção guia-o através da ligação do seu Azure AD à conta de utilizador do GitHub que aprovisiona a API, e configurando o serviço de provisionamento para criar, atualizar e desativar as contas de utilizador atribuídas no GitHub com base na atribuição de utilizador e grupo em Azure AD.

> [!TIP]
> Também pode optar por ativar o Single Sign-On baseado em SAML para o GitHub, seguindo as instruções fornecidas no [portal Azure](https://portal.azure.com). O único sinal de inscrição pode ser configurado independentemente do fornecimento automático, embora estas duas funcionalidades se elogiem mutuamente.

### <a name="configure-automatic-user-account-provisioning-to-github-in-azure-ad"></a>Configure o fornecimento automático de conta de utilizador ao GitHub em Azure AD

1. No [portal Azure,](https://portal.azure.com)navegue até ao **Azure Ative Directory > Enterprise Apps > Todas as aplicações.**

2. Se já configurou o GitHub para uma única inscrição, procure a sua instância de GitHub utilizando o campo de pesquisa. Caso contrário, selecione **Adicionar** e procurar **gitHub** na galeria de aplicações. Selecione GitHub a partir dos resultados da pesquisa e adicione-o à sua lista de aplicações.

3. Selecione a sua instância de GitHub e, em seguida, selecione o separador **Provisioning.**

4. Detete o **modo de provisionamento** para **automático**.

    ![Fornecimento GitHub](./media/github-provisioning-tutorial/GitHub1.png)

5. Na secção **credenciais de administrador,** clique **em Autorizar**. Esta operação abre um diálogo de autorização GitHub numa nova janela do navegador. Note que precisa de garantir que está aprovado para autorizar o acesso. Siga as instruções [aqui](https://help.github.com/github/setting-up-and-managing-organizations-and-teams/approving-oauth-apps-for-your-organization)descritas.

6. Na nova janela, inscreva-se no GitHub usando a sua conta De administrador. No diálogo de autorização resultante, selecione a equipa GitHub que pretende ativar o provisionamento e, em seguida, **selecione Autorizar**. Uma vez concluído, volte ao portal Azure para completar a configuração de provisionamento.

    ![Diálogo de Autorização](./media/github-provisioning-tutorial/GitHub2.png)

7. No portal Azure, insere o **URL do Inquilino** e clique em Test **Connection** para garantir que o Azure AD pode ligar-se à sua aplicação GitHub. Se a ligação falhar, certifique-se de que a sua conta GitHub tem permissões de administrador e a **URl** do Inquilino é inputada corretamente, então tente o passo "Autorizar" novamente (pode constituir URL de **Inquilino** por regra: `https://api.github.com/scim/v2/organizations/<Organization_name>`, pode encontrar as suas organizações sob a sua conta GitHub: **Definições** > **Organizações).**

    ![Diálogo de Autorização](./media/github-provisioning-tutorial/GitHub3.png)

8. Insira o endereço de e-mail de uma pessoa ou grupo que deve receber notificações de erro no campo de email de **notificação** e consulte a caixa de verificação "Envie uma notificação de e-mail quando ocorrer uma falha".

9. Clique em **Guardar**.

10. Na secção Mapeamentos, **selecione Synchronize Azure Ative Directory Users to GitHub**.

11. Na secção **DeMapeamentos de Atributos,** reveja os atributos do utilizador que são sincronizados de Azure AD para GitHub. Os atributos selecionados como propriedades **Correspondentes** são usados para combinar as contas de utilizador no GitHub para operações de atualização. Selecione o botão Guardar para consolidar as alterações.

12. Para ativar o serviço de provisionamento de AD Azure para o GitHub, altere o Estado de **Provisionamento** para **On** na secção **Definições**

13. Clique em **Guardar**.

Esta operação inicia a sincronização inicial de quaisquer utilizadores e/ou grupos atribuídos ao GitHub na secção Utilizadores e Grupos. A sincronização inicial demora mais tempo a serem executados do que as sincronizações subsequentes, o que ocorrer aproximadamente a cada 40 minutos, desde que o serviço está em execução. Pode utilizar a secção Detalhes de **Sincronização** para monitorizar o progresso e seguir ligações aos registos de atividades de provisionamento, que descrevem todas as ações realizadas pelo serviço de provisionamento.

Para obter mais informações sobre como ler os registos de provisionamento da AD Azure, consulte [relatórios sobre o fornecimento automático](../app-provisioning/check-status-user-account-provisioning.md)de conta de utilizador .

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do provisionamento de conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
