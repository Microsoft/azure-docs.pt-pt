---
title: 'Tutorial: Fornecimento de utilizadores para GitHub - Azure AD'
description: Saiba como configurar o Azure Ative Directory para provisão automática e desa provisionar contas de utilizadores ao GitHub.
services: active-directory
author: ArvindHarinder1
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 03/27/2019
ms.author: arvinh
ms.openlocfilehash: 847c69a18a73d67b9b994e72686a4073ddd6d27f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2020
ms.locfileid: "91857542"
---
# <a name="tutorial-configure-github-for-automatic-user-provisioning"></a>Tutorial: Configurar GitHub para fornecimento automático de utilizadores

O objetivo deste tutorial é mostrar-lhe os passos que precisa de executar no GitHub e AD AZure para provisões automáticas e desavisagem de contas de utilizadores de Azure AD para GitHub.

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que já tem os seguintes itens:

* Um inquilino de diretório Azure Ative
* Uma organização GitHub criada em [GitHub Enterprise Cloud,](https://help.github.com/articles/github-s-products/#github-enterprise)que requer o [plano de faturação](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations) da GitHub Enterprise
* Uma conta de utilizador no GitHub com permissões de administração para a organização
* [SAML configurado para a organização GitHub Enterprise Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/github-tutorial)
* Certifique-se de que o acesso da OAuth foi fornecido para a sua organização, como descrito [aqui](https://help.github.com/en/github/setting-up-and-managing-organizations-and-teams/approving-oauth-apps-for-your-organization)
* O fornecimento de SCIM a uma única organização só é suportado quando o SSO é ativado a nível da organização

> [!NOTE]
> A integração de provisionamento Azure AD depende da [GitHub SCIM API,](https://developer.github.com/v3/scim/)que está disponível para os clientes [GitHub Enterprise Cloud](https://help.github.com/articles/github-s-products/#github-enterprise) no [plano de faturação](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations)da GitHub Enterprise .

## <a name="assigning-users-to-github"></a>Atribuir utilizadores ao GitHub

O Azure Ative Directory utiliza um conceito chamado "atribuições" para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do provisionamento automático da conta de utilizador, apenas os utilizadores e grupos que foram "atribuídos" a uma aplicação em AZure AD são sincronizados. 

Antes de configurar e ativar o serviço de fornecimento, tem de decidir quais os utilizadores e/ou grupos em Azure AD que representam os utilizadores que precisam de acesso à sua aplicação GitHub. Uma vez decididos, pode atribuir estes utilizadores à sua aplicação GitHub seguindo as instruções aqui:

[Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-github"></a>Dicas importantes para atribuir utilizadores ao GitHub

* Recomenda-se que um único utilizador AD Azure seja designado para o GitHub para testar a configuração de provisionamento. Utilizadores e/ou grupos adicionais podem ser atribuídos mais tarde.

* Ao atribuir um utilizador ao GitHub, tem de selecionar a função **Do Utilizador** ou outra função específica de aplicação válida (se disponível) no diálogo de atribuição. A função **de Acesso Predefinido** não funciona para o provisionamento, e estes utilizadores são ignorados.

## <a name="configuring-user-provisioning-to-github"></a>Configurar o fornecimento de utilizadores ao GitHub

Esta secção guia-o através da ligação do seu AD Azure à conta de utilizador do GitHub que fornece a API e configura o serviço de fornecimento para criar, atualizar e desativar as contas de utilizador atribuídas no GitHub com base na atribuição de utilizadores e grupos em Azure AD.

### <a name="configure-automatic-user-account-provisioning-to-github-in-azure-ad"></a>Configure o fornecimento automático de conta de utilizador ao GitHub em Azure AD

1. No [portal Azure,](https://portal.azure.com)consulte a secção **Azure Ative Directory > Enterprise Apps > Todas as aplicações.**

2. Se já configurar o GitHub para um único sinal de acesso, procure a sua instância do GitHub utilizando o campo de pesquisa. Caso contrário, **selecione Add** e procure **gitHub** na galeria de aplicações. Selecione GitHub a partir dos resultados da pesquisa e adicione-o à sua lista de aplicações.

3. Selecione a sua instância de GitHub e, em seguida, selecione o **separador Provisioning.**

4. Defina o **Modo de Aprovisionamento** como **Automático**.

    ![Provisão gitHub](./media/github-provisioning-tutorial/GitHub1.png)

5. Na secção **Credenciais de Administrador**, clique em **Autorizar**. Esta operação abre um diálogo de autorização GitHub numa nova janela do navegador. Tenha em atenção que tem de garantir que está aprovado para autorizar o acesso. Siga as instruções descritas [aqui.](https://help.github.com/github/setting-up-and-managing-organizations-and-teams/approving-oauth-apps-for-your-organization)

6. Na nova janela, inscreva-se no GitHub usando a sua conta de administrador. No diálogo de autorização resultante, selecione a equipa do GitHub para a que pretende permitir o provisionamento e, em seguida, **selecione Authorize**. Depois de concluído, regresse ao portal do Azure para concluir a configuração do aprovisionamento.

    ![A screenshot mostra a página de sindes de sinspe para GitHub.](./media/github-provisioning-tutorial/GitHub2.png)

7. No portal Azure, **insira o URL do inquilino** e clique em Test **Connection** para garantir que o Azure AD pode ligar-se à sua aplicação GitHub. Se a ligação falhar, certifique-se de que a sua conta GitHub tem permissões de administração e **o Inquilino URl** é introduzido corretamente, então tente novamente o passo "Authorize" (pode constituir **o Inquilino URL** por regra: , pode encontrar as suas `https://api.github.com/scim/v2/organizations/<Organization_name>` organizações sob a sua conta GitHub: **Organizações de Definições).**  >  **Organizations**

    ![A screenshot mostra a página das Organizações no GitHub.](./media/github-provisioning-tutorial/GitHub3.png)

8. Insira o endereço de e-mail de uma pessoa ou grupo que deve receber notificações de erro no campo **de e-mail de notificação** e verifique a caixa de verificação "Envie uma notificação de e-mail quando ocorrer uma falha."

9. Clique em **Guardar**.

10. Na secção Mappings, selecione **Synchronize Azure Ative Directory Users para GitHub**.

11. Na secção **De mapeamentos de atributos,** reveja os atributos do utilizador que são sincronizados de Azure AD para GitHub. Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas de utilizador no GitHub para operações de atualização. Selecione o botão Guardar para confirmar as alterações.

12. Para ativar o serviço de prestação de Ad Azure para o GitHub, altere o **Estado de Provisionamento** para **On** na secção **Definições**

13. Clique em **Guardar**.

Esta operação inicia a sincronização inicial de quaisquer utilizadores e/ou grupos atribuídos ao GitHub na secção Utilizadores e Grupos. A sincronização inicial demora mais tempo a ser efetuada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço esteja em funcionamento. Pode utilizar a secção Detalhes da **Sincronização** para monitorizar o progresso e seguir links para os registos de atividade de provisionamento, que descrevem todas as ações realizadas pelo serviço de fornecimento.

Para obter mais informações sobre como ler os registos de provisionamento da AZure AD, consulte [Reportar sobre o provisionamento automático da conta de utilizador](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento de contas de utilizador para Aplicações Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como analisar os registos e obter relatórios sobre a atividade de aprovisionamento](../app-provisioning/check-status-user-account-provisioning.md)
