---
title: 'Tutorial: Configurar Matriz Prioritária para o provisionamento automático do utilizador com diretório ativo Azure / Microsoft Docs'
description: Aprenda a configurar o Azure Ative Directory para provisão automática e desa provisionar contas de utilizadores à Matriz Prioritária.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/08/2019
ms.author: Zhchia
ms.openlocfilehash: e79f21300325c6b451dd564bf2c69830f003f55c
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2020
ms.locfileid: "94357866"
---
# <a name="tutorial-configure-priority-matrix-for-automatic-user-provisioning"></a>Tutorial: Configure Matriz Prioritária para o provisionamento automático do utilizador

O objetivo deste tutorial é demonstrar os passos a serem realizados na Matriz Prioritária e no Diretório Ativo Azure (Ad) para configurar a Azure AD para fornecimento e desavisagem automática de utilizadores e/ou grupos para a Matriz Prioritária.

> [!NOTE]
> Este tutorial descreve um conector construído em cima do Serviço de Provisionamento de Utilizadores Azure AD. Para obter detalhes importantes sobre o que este serviço faz, como funciona e perguntas frequentes, veja [Automatizar o aprovisionamento e desaprovisionamento de utilizadores em aplicações SaaS no Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Este conector encontra-se atualmente em Visualização Pública. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* Um inquilino da AD AZure
* [Um inquilino da Matriz Prioritária](https://appfluence.com/pricing/)
* Uma conta de utilizador numa Matriz Prioritária com permissões de Administração.

## <a name="assign-users-to-priority-matrix"></a>Atribuir utilizadores à Matriz Prioritária

O Azure Ative Directory utiliza um conceito chamado atribuições para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores e/ou grupos que foram designados para uma aplicação em Azure AD são sincronizados.

Antes de configurar e permitir o fornecimento automático do utilizador, deve decidir quais os utilizadores e/ou grupos em Azure AD que precisam de acesso à Matriz Prioritária. Uma vez decididos, pode atribuir estes utilizadores e/ou grupos à Matriz Prioritária seguindo as instruções aqui:

* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-priority-matrix"></a>Dicas importantes para atribuir utilizadores à Matriz Prioritária

* Recomenda-se que um único utilizador AZure AD seja atribuído à Matriz Prioritária para testar a configuração automática de provisionamento do utilizador. Utilizadores e/ou grupos adicionais podem ser atribuídos mais tarde.

* Ao atribuir um utilizador à Matriz Prioritária, deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função **Acesso Predefinido** estão excluídos do provisionamento.

## <a name="set-up-priority-matrix-for-provisioning"></a>Configurar matriz prioritária para o provisionamento

Antes de configurar a Matriz Prioritária para o fornecimento automático do utilizador com Azure AD, terá de obter algumas informações de fornecimento da Matriz Prioritária.

1. Inscreva-se na sua [Consola de Administração Matrix Prioritária.](https://sync.appfluence.com/accounts/login/?next=/accounts/provisioning)

3. Clique **em token de login de Oauth** para Matriz Prioritária

    ![Matriz prioritária Adicionar SCIM](media/priority-matrix-provisioning-tutorial/oauthlogin.png)

4. Clique no botão **GET NEW TOKEN.** Copie a **corda token**. Este valor será introduzido no campo **Secret Token** no separador Provisioning da sua aplicação Matriz Prioritária no portal Azure. 

    ![Matriz prioritária Criar Token](media/priority-matrix-provisioning-tutorial/token.png)

## <a name="add-priority-matrix-from-the-gallery"></a>Adicione Matriz Prioritária da galeria

Para configurar a Matriz Prioritária para o fornecimento automático de utilizadores com Azure AD, é necessário adicionar a Matriz Prioritária da galeria de aplicações AD AZure à sua lista de aplicações geridas pelo SaaS.

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, selecione **Azure Ative Directory**.

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise** e, em seguida, selecione **Todas as aplicações**.

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, insira **Matriz Prioritária,** selecione **Matriz Prioritária** no painel de resultados. 

    ![Matriz Prioritária na lista de resultados](common/search-new-app.png)

5. Selecione o **botão Desinsução para Matriz Prioritária** que irá redirecioná-lo para a página de login da Matrix Prioritária. 

    ![Matriz prioritária OIDC Adicionar](media/priority-matrix-provisioning-tutorial/signup.png)

6. Como Matrix Prioritária é uma aplicação OpenIDConnect, opte por iniciar sessão na Matriz Prioritária utilizando a sua conta de trabalho da Microsoft.

    ![Login OIDC da Matriz Prioritária](media/priority-matrix-provisioning-tutorial/msftsignin.png)

7. Após uma autenticação bem sucedida, aceite o pedido de consentimento para a página de consentimento. A aplicação será então automaticamente adicionada ao seu inquilino e será redirecionado para a sua conta Matriz Prioritária.

    ![Consentimento OIDc da Matriz Prioritária](media/priority-matrix-provisioning-tutorial/consent.png)

## <a name="configure-automatic-user-provisioning-to-priority-matrix"></a>Configure o fornecimento automático do utilizador à Matriz Prioritária 

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos na Matriz Prioritária com base em atribuições de utilizador e/ou grupo em Azure AD.

> [!NOTE]
> Para saber mais sobre o ponto final SCIM da Matrix Prioritária, consulte o fornecimento do [utilizador e a Matriz Prioritária](https://appfluence.com/help/article/user-provisioning/).

### <a name="to-configure-automatic-user-provisioning-for-priority-matrix-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para matriz prioritária em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais** e, em seguida, **Todas as aplicações**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Priority Matrix**.

    ![O link Matriz Prioritária na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Aprovisionamento**.

    ![Screenshot das opções De gestão com a opção Provisioning chamada.](common/provisioning.png)

4. Defina o **Modo de Aprovisionamento** como **Automático**.

    ![Screenshot da lista de retirada do modo de provisionamento com a opção Automática chamada.](common/provisioning-automatic.png)

5. Sob a secção **de Credenciais de Administração,** entrada `https://sync.appfluence.com/scim/v2/` na URL do **inquilino.** Insira o valor que recuperou e guardou anteriormente da Priority Matrix em **Secret Token**. Clique em **'Testar' Ligação** para garantir que o Azure AD pode ligar-se à Matriz Prioritária. Se a ligação falhar, certifique-se de que a sua conta Matrix Prioritária tem permissões de Administração e tente novamente.

    ![INQUILINO URL + Token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **'Email' de Notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e verifique a caixa de verificação - **Envie uma notificação de e-mail quando ocorrer uma falha**.

    ![E-mail de Notificação](common/provisioning-notification-email.png)

7. Clique em **Guardar**.

8. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users to Priority Matrix**.

    ![Mapeamentos de utilizadores de matriz prioritária](media/priority-matrix-provisioning-tutorial/usermappings.png)

9. Reveja os atributos do utilizador que são sincronizados de AD AD AD a Matriz Prioritária na secção **De Mapeamento de Atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas do utilizador na Matriz Prioritária para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

    ![Atributos de utilizador de matriz prioritária](media/priority-matrix-provisioning-tutorial/userattributes.png)

10. Para configurar filtros de âmbito, veja as instruções seguintes disponibilizadas no [Tutorial de filtro de âmbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para ativar o serviço de prestação de Ad Azure para Matriz Prioritária, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

    ![Estado do Aprovisionamento Ativado](common/provisioning-toggle-on.png)

12. Defina os utilizadores e/ou grupos que deseja prestar à Matriz Prioritária, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de Aprovisionamento](common/provisioning-scope.png)

13. Quando estiver pronto para aprovisionar, clique em **Guardar**.

    ![Guardar Configuração de Aprovisionamento](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de fornecimento AZure AD esteja em execução. Pode utilizar a secção Detalhes da **Sincronização** para monitorizar o progresso e seguir ligações ao relatório de atividades de provisionamento, que descreve todas as ações realizadas pelo serviço de fornecimento de AD Azure na Matriz Prioritária.

Para obter mais informações sobre como ler os registos de provisionamento da AZure AD, consulte [Reportar sobre o provisionamento automático da conta de utilizador](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento de contas de utilizador para Aplicações Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como analisar os registos e obter relatórios sobre a atividade de aprovisionamento](../app-provisioning/check-status-user-account-provisioning.md)


