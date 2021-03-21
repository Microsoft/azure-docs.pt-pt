---
title: 'Tutorial: Configure TheOrgWiki para fornecimento automático de utilizadores com Azure Ative Directory | Microsoft Docs'
description: Saiba como configurar o Azure Ative Directory para provisão automática e desa provisionar contas de utilizadores ao TheOrgWiki.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 8238b9902aafcabc079c551a0eabc7170042209a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94357628"
---
# <a name="tutorial-configure-theorgwiki-for-automatic-user-provisioning"></a>Tutorial: Configurar TheOrgWiki para fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados no TheOrgWiki e no Azure Ative Directory (Azure AD) para configurar a Azure AD para fornecimento e desavisagem automática de utilizadores e/ou grupos ao TheOrgWiki.

> [!NOTE]
> Este tutorial descreve um conector construído em cima do Serviço de Provisionamento de Utilizadores Azure AD. Para obter detalhes importantes sobre o que este serviço faz, como funciona e perguntas frequentes, veja [Automatizar o aprovisionamento e desaprovisionamento de utilizadores em aplicações SaaS no Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Este conector encontra-se atualmente em Visualização Pública. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* Um inquilino do Azure AD.
* [Um inquilino orgWiki.](https://www.theorgwiki.com/welcome/)
* Uma conta de utilizador no TheOrgWiki com permissões de Administração.

## <a name="assign-users-to-theorgwiki"></a>Atribuir utilizadores ao TheOrgWiki

O Azure Ative Directory utiliza um conceito chamado atribuições para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores e/ou grupos que foram designados para uma aplicação em Azure AD são sincronizados.

Antes de configurar e permitir o fornecimento automático do utilizador, deve decidir quais os utilizadores e/ou grupos em AZure AD que precisam de acesso ao TheOrgWiki. Uma vez decididos, pode atribuir estes utilizadores e/ou grupos ao TheOrgWiki seguindo as instruções aqui:

* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-theorgwiki"></a>Dicas importantes para a atribuição de utilizadores ao TheOrgWiki

* Recomenda-se que um único utilizador Azure AD seja atribuído ao TheOrgWiki para testar a configuração automática de provisionamento do utilizador. Utilizadores e/ou grupos adicionais podem ser atribuídos mais tarde.

* Ao atribuir um utilizador ao TheOrgWiki, deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função **Acesso Predefinido** estão excluídos do provisionamento.

## <a name="set-up-theorgwiki-for-provisioning"></a>Configurar o TheOrgWiki para o provisionamento

Antes de configurar o TheOrgWiki para o fornecimento automático de utilizadores com Azure AD, terá de ativar o fornecimento scim no TheOrgWiki.

1. Inscreva-se na sua [Consola de Administração TheOrgWiki](https://www.theorgwiki.com/login/). Clique na **Consola de Administração.**

    ![Screenshot de Org Wiki com o avatar do utilizador e a Consola Admin chamadas.](media/theorgwiki-provisioning-tutorial/login.png)

2. Na consola de administração, clique no **separador Definições**. 

    ![Screenshot da Consola de Administração Org Wiki com o separador Definições chamado.](media/theorgwiki-provisioning-tutorial/settings.png)
    
3. Navegar para contas **de serviço.**

    ![Screenshot da página 'Contas de Serviço' na Consola Org Wiki Admin.](media/theorgwiki-provisioning-tutorial/serviceaccount.png)

4. Clique em **+Conta de Serviço.** No **tipo de conta de serviço**,selecione **Token Based**. Clique em **Guardar**.

    ![Screenshot da caixa de diálogo de nova conta de serviço com as opções de Serviço Tipo de Conta, Token Based e Save.](media/theorgwiki-provisioning-tutorial/auth.png)

5.  Copiar os **Tokens Ativos.** Este valor será introduzido no campo Secret Token no separador Provisioning da sua aplicação TheOrgWiki no portal Azure.
     
    ![Screenshot da caixa de diálogo de provisionamento de S C I M.](media/theorgwiki-provisioning-tutorial/token.png)

## <a name="add-theorgwiki-from-the-gallery"></a>Adicione TheOrgWiki da galeria

Para configurar o TheOrgWiki para o fornecimento automático de utilizadores com Azure AD, precisa adicionar TheOrgWiki da galeria de aplicações AD AD Azure à sua lista de aplicações geridas pelo SaaS.

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, selecione **Azure Ative Directory**.

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise** e, em seguida, selecione **Todas as aplicações**.

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, insira **TheOrgWiki,** selecione **TheOrgWiki** no painel de resultados. 

    ![TheOrgWiki na lista de resultados](common/search-new-app.png)

5. Selecione o **botão De Inscrição para OOrgWiki** que irá redirecioná-lo para a página de login do TheOrgWiki. 

    ![Screenshot da página de login do Org Wiki com o URL chamado](media/theorgwiki-provisioning-tutorial/image00.png)

6.  No canto superior direito, **selecione Login**.

    ![Screenshot do canto superior direito da página de login com a opção 'Log In' chamada.](media/theorgwiki-provisioning-tutorial/image02.png)

7. Como o TheOrgWiki é uma aplicação OpenIDConnect, opte por iniciar sessão no OrgWiki utilizando a sua conta de trabalho da Microsoft.

    ![Screenshot do sinal org Wiki na página com a opção Iniciar com a Microsoft chamada.](media/theorgwiki-provisioning-tutorial/image03.png)
    
8. Após uma autenticação bem sucedida, a aplicação será automaticamente adicionada ao seu inquilino e será redirecionado para a sua conta TheOrgWiki.

    ![OrgWiki Add SCIM](media/theorgwiki-provisioning-tutorial/image04.png)

## <a name="configure-automatic-user-provisioning-to-theorgwiki"></a>Configure o fornecimento automático de utilizadores ao TheOrgWiki 

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos no TheOrgWiki com base em atribuições de utilizador e/ou grupo em Azure AD.


### <a name="to-configure-automatic-user-provisioning-for-theorgwiki-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para o TheOrgWiki em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais** e, em seguida, **Todas as aplicações**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **TheOrgWiki**.

    ![O link OrgWiki na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Aprovisionamento**.

    ![Screenshot das opções De gestão com a opção Provisioning chamada.](common/provisioning.png)

4. Defina o **Modo de Aprovisionamento** como **Automático**.

    ![Screenshot da lista de retirada do modo de provisionamento com a opção Automática chamada.](common/provisioning-automatic.png)

5. Sob a secção **de Credenciais de Administração,** entrada `https://<TheOrgWiki Subdomain        value>.theorgwiki.com/api/v2/scim/v2/` na URL do **inquilino.** 

    Exemplo: `https://test1.theorgwiki.com/api/v2/scim/v2/`

> [!NOTE]
> O **Valor Subdomínio** só pode ser definido durante o processo inicial de inscrição para o TheOrgWiki.
 
6. Insira o valor simbólico no campo **Secret Token,** que recuperou mais cedo do TheOrgWiki. Clique em **'Test Connection'** para garantir que o Azure AD pode ligar-se ao TheOrgWiki. Se a ligação falhar, certifique-se de que a sua conta TheOrgWiki tem permissões de Administração e tente novamente.

    ![INQUILINO URL + Token](common/provisioning-testconnection-tenanturltoken.png)

7. No campo **'Email' de Notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e verifique a caixa de verificação - **Envie uma notificação de e-mail quando ocorrer uma falha**.

    ![E-mail de Notificação](common/provisioning-notification-email.png)

8. Clique em **Guardar**.

9. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users to TheOrgWiki**.

    ![Mapeamentos do utilizador TheOrgWiki](media/theorgwiki-provisioning-tutorial/usermapping.png)

10. Reveja os atributos do utilizador que são sincronizados de AZure AD a TheOrgWiki na secção **Atributo-Mapeamento.** Os atributos selecionados como propriedades **de correspondência** são utilizados para corresponder às contas do utilizador no TheOrgWiki para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

    ![Atributos do utilizador TheOrgWiki](media/theorgwiki-provisioning-tutorial/userattribute.png).

11. Para configurar filtros de âmbito, veja as instruções seguintes disponibilizadas no [Tutorial de filtro de âmbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Para ativar o serviço de fornecimento de AD Azure para o TheOrgWiki, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

    ![Estado do Aprovisionamento Ativado](common/provisioning-toggle-on.png)

13. Defina os utilizadores e/ou grupos que deseja prestar à OrgWiki, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de Aprovisionamento](common/provisioning-scope.png)

14. Quando estiver pronto para aprovisionar, clique em **Guardar**.

    ![Guardar Configuração de Aprovisionamento](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser efetua do que as sincronizações subsequentes. Para obter mais informações sobre o tempo que os utilizadores e/ou grupos demorarão a providenciar, consulte [quanto tempo demorará a providenciar aos utilizadores](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

Pode utilizar a secção **Estado Presente** para monitorizar o progresso e seguir links para o seu relatório de atividades de provisionamento, que descreve todas as ações realizadas pelo serviço de fornecimento de AD Azure no TheOrgWiki. Para obter mais informações, [consulte verifique o estado do fornecimento do utilizador](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Para ler os registos de provisionamento da AD Azure, consulte [reportar sobre o provisionamento automático da conta de utilizador](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do fornecimento de conta de utilizador para apps empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md).