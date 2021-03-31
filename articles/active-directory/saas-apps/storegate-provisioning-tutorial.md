---
title: 'Tutorial: Configurar o Storegate para o fornecimento automático de utilizadores com o Azure Ative Directory | Microsoft Docs'
description: Saiba como configurar o Azure Ative Directory para provisão automática e desa provisionar contas de utilizadores ao Storegate.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/15/2019
ms.author: Zhchia
ms.openlocfilehash: c984beff630ef90ea33a13e2fef1bca0189c2314
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94357949"
---
# <a name="tutorial-configure-storegate-for-automatic-user-provisioning"></a>Tutorial: Configurar Storegate para fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados no Storegate e no Azure Ative Directory (Azure AD) para configurar a Azure AD para fornecimento e desagregado automaticamente utilizadores e/ou grupos ao Storegate.

> [!NOTE]
> Este tutorial descreve um conector construído em cima do Serviço de Provisionamento de Utilizadores Azure AD. Para obter detalhes importantes sobre o que este serviço faz, como funciona e perguntas frequentes, veja [Automatizar o aprovisionamento e desaprovisionamento de utilizadores em aplicações SaaS no Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Este conector encontra-se atualmente em Visualização Pública. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* Um inquilino da AD AZure
* [Um inquilino de Storegate](https://www.storegate.com)
* Uma conta de utilizador num Storegate com permissões de Administrador.

## <a name="assign-users-to-storegate"></a>Atribuir utilizadores ao Storegate

O Azure Ative Directory utiliza um conceito chamado atribuições para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores e/ou grupos que foram designados para uma aplicação em Azure AD são sincronizados.

Antes de configurar e permitir o fornecimento automático do utilizador, deve decidir quais os utilizadores e/ou grupos em Azure AD que precisam de acesso ao Storegate. Uma vez decididos, pode atribuir estes utilizadores e/ou grupos ao Storegate seguindo as instruções aqui:

* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-storegate"></a>Dicas importantes para atribuir utilizadores ao Storegate

* Recomenda-se que um único utilizador Azure AD seja designado ao Storegate para testar a configuração automática de provisionamento do utilizador. Utilizadores e/ou grupos adicionais podem ser atribuídos mais tarde.

* Ao atribuir um utilizador ao Storegate, deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função **Acesso Predefinido** estão excluídos do provisionamento.

## <a name="set-up-storegate-for-provisioning"></a>Configurar o Storegate para o provisionamento

Antes de configurar o Storegate para o fornecimento automático do utilizador com a Azure AD, terá de obter algumas informações de fornecimento do Storegate.

1. Inscreva-se na consola [de admin Storegate](https://ws1.storegate.com/identity/core/login?signin=c71fb8fe18243c571da5b333d5437367) e navegue para as definições clicando no ícone do utilizador no canto superior direito e selecione **Definições de Conta**.

    ![Storegate Add SCIM](media/storegate-provisioning-tutorial/admin.png)

2. Dentro das definições, navegue para **a Team > Definições** e verifique se o interruptor de alternância está ligado na secção **de inscrição única.**

    ![Definições de Storegate](media/storegate-provisioning-tutorial/team.png)

    ![Botão de alternação do Storegate](media/storegate-provisioning-tutorial/sso.png)

3. Copie o URL do **Inquilino** e **Token.** Estes valores serão introduzidos nos campos **URL** e **Secret Token,** respectivamente, no separador Provisioning da sua aplicação Storegate no portal Azure. 

    ![Conjunto de criação de storegate](media/storegate-provisioning-tutorial/token.png)

## <a name="add-storegate-from-the-gallery"></a>Adicione Storegate da galeria

Para configurar o Storegate para o fornecimento automático de utilizadores com Azure AD, é necessário adicionar o Storegate da galeria de aplicações AD AZure à sua lista de aplicações geridas pelo SaaS.

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, selecione **Azure Ative Directory**.

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise** e, em seguida, selecione **Todas as aplicações**.

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, insira **o Storegate,** selecione **Storegate** no painel de resultados. 

    ![Storegate na lista de resultados](common/search-new-app.png)

5. Selecione o **botão de inscrição para Storegate** que irá redirecioná-lo para a página de login do Storegate. 

    ![Storegate OIDC Adicionar](media/storegate-provisioning-tutorial/signup.png)

6.  Inscreva-se na consola [de admin Storegate](https://ws1.storegate.com/identity/core/login?signin=c71fb8fe18243c571da5b333d5437367) e navegue para as definições clicando no ícone do utilizador no canto superior direito e selecione **Definições de Conta**.

    ![Login do Storegate](media/storegate-provisioning-tutorial/admin.png)

7. Dentro das definições, navegue para **Definições de > de Equipa** e clique no interruptor de alternação na secção de inscrição única, isto iniciará o fluxo de consentimento. Clique em **Ativar**.

    ![Equipa storegate](media/storegate-provisioning-tutorial/team.png)

    ![Sso storegate](media/storegate-provisioning-tutorial/sso.png)

    ![Ativar o Storegate](media/storegate-provisioning-tutorial/activate.png)

8. Como o Storegate é uma aplicação OpenIDConnect, opte por iniciar sessão no Storegate utilizando a sua conta de trabalho da Microsoft.

    ![Login OIDC do Storegate](media/storegate-provisioning-tutorial/login.png)

9. Após uma autenticação bem sucedida, aceite o pedido de consentimento para a página de consentimento. A aplicação será então automaticamente adicionada ao seu inquilino e será redirecionado para a sua conta Storegate.

    ![Consentimento do OIDc de Storegate](media/storegate-provisioning-tutorial/accept.png)

## <a name="configure-automatic-user-provisioning-to-storegate"></a>Configure o fornecimento automático de utilizadores ao Storegate 

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos em Storegate com base em atribuições de utilizador e/ou grupo em Azure AD.

> [!NOTE]
> Para saber mais sobre o ponto final do SCIM do Storegate, consulte [isto](https://en-support.storegate.com/article/step-by-step-instruction-how-to-enable-azure-provisioning-to-your-storegate-team-account/).

### <a name="to-configure-automatic-user-provisioning-for-storegate-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para o Storegate em Azure AD

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais** e, em seguida, **Todas as aplicações**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Storegate**.

    ![O link Storegate na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Aprovisionamento**.

    ![Screenshot das opções De gestão com a opção Provisioning chamada.](common/provisioning.png)

4. Defina o **Modo de Aprovisionamento** como **Automático**.

    ![Screenshot da lista de retirada do modo de provisionamento com a opção Automática chamada.](common/provisioning-automatic.png)

5. Sob a secção **de Credenciais de Administração,** entrada `https://dialpad.com/scim` na URL do **inquilino.** Insira o valor que recuperou e guardou mais cedo do Storegate em **Secret Token.** Clique em **Testar a Ligação** para garantir que o Azure AD pode ligar-se ao Storegate. Se a ligação falhar, certifique-se de que a sua conta Storegate tem permissões de Administração e tente novamente.

    ![INQUILINO URL + Token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **'Email' de Notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e verifique a caixa de verificação - **Envie uma notificação de e-mail quando ocorrer uma falha**.

    ![E-mail de Notificação](common/provisioning-notification-email.png)

7. Clique em **Guardar**.

8. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users to Storegate**.

    ![Mapeamentos do utilizador do Storegate](media/storegate-provisioning-tutorial/usermappings.png)

9. Reveja os atributos do utilizador que são sincronizados de Azure AD para Storegate na secção **De Mapeamento de Atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas do utilizador no Storegate para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

    ![Atributos do utilizador storegate](media/storegate-provisioning-tutorial/userattributes.png)

10. Para configurar filtros de âmbito, veja as instruções seguintes disponibilizadas no [Tutorial de filtro de âmbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para ativar o serviço de prestação de Ad Azure para o Storegate, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

    ![Estado do Aprovisionamento Ativado](common/provisioning-toggle-on.png)

12. Defina os utilizadores e/ou grupos que deseja prestar ao Storegate, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de Aprovisionamento](common/provisioning-scope.png)

13. Quando estiver pronto para aprovisionar, clique em **Guardar**.

    ![Guardar Configuração de Aprovisionamento](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de fornecimento AZure AD esteja em execução. Pode utilizar a secção Detalhes da **Sincronização** para monitorizar o progresso e seguir links para o relatório de atividades de provisionamento, que descreve todas as ações realizadas pelo serviço de fornecimento de Azure AD no Storegate.

Para obter mais informações sobre como ler os registos de provisionamento da AZure AD, consulte [Reportar sobre o provisionamento automático da conta de utilizador](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento de contas de utilizador para Aplicações Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como analisar os registos e obter relatórios sobre a atividade de aprovisionamento](../app-provisioning/check-status-user-account-provisioning.md)
