---
title: 'Tutorial: Configurar a autenticação do utilizador netskope para o fornecimento automático de utilizadores com o Azure Ative Directory | Microsoft Docs'
description: Aprenda a configurar o Azure Ative Directory para fornecimento e desavisionamento automática de contas de utilizador para a Autenticação do Utilizador netskope.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/07/2019
ms.author: Zhchia
ms.openlocfilehash: 46766a7439185714648572f3f1b9d51ef96abba6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94357477"
---
# <a name="tutorial-configure-netskope-user-authentication-for-automatic-user-provisioning"></a>Tutorial: Configurar a autenticação do utilizador netskope para o fornecimento automático do utilizador

O objetivo deste tutorial é demonstrar os passos a serem realizados na Autenticação do Utilizador netskope e no Diretório Ativo Azure (AD) para configurar a Azure AD para fornecimento e desavisionamento de utilizadores e/ou grupos para a Autenticação do Utilizador netskope.

> [!NOTE]
> Este tutorial descreve um conector construído em cima do Serviço de Provisionamento de Utilizadores Azure AD. Para obter detalhes importantes sobre o que este serviço faz, como funciona e perguntas frequentes, veja [Automatizar o aprovisionamento e desaprovisionamento de utilizadores em aplicações SaaS no Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Este conector encontra-se atualmente em Visualização Pública. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* Um inquilino da AD AZure
* [Um inquilino de autenticação de utilizadores netskope](https://www.netskope.com/)
* Uma conta de utilizador na Autenticação do Utilizador netskope com permissões de Administração.

## <a name="assigning-users-to-netskope-user-authentication"></a>Atribuir utilizadores à Autenticação do Utilizador netskope

O Azure Ative Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores e/ou grupos que foram designados para uma aplicação em Azure AD são sincronizados.

Antes de configurar e permitir o fornecimento automático do utilizador, deverá decidir quais os utilizadores e/ou grupos em Azure AD que precisam de acesso à Autenticação do Utilizador netskope. Uma vez decididos, pode atribuir estes utilizadores e/ou grupos à Autenticação do Utilizador netskope seguindo as instruções aqui:
* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-netskope-user-authentication"></a>Dicas importantes para atribuir utilizadores à Autenticação do Utilizador netskope

* Recomenda-se que um único utilizador AZure AD seja atribuído à Autenticação do Utilizador netskope para testar a configuração automática de provisionamento do utilizador. Utilizadores e/ou grupos adicionais podem ser atribuídos mais tarde.

* Ao atribuir um utilizador à Autenticação do Utilizador netskope, tem de selecionar qualquer função específica da aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função **Acesso Predefinido** estão excluídos do provisionamento.

## <a name="set-up-netskope-user-authentication-for-provisioning"></a>Configurar a Autenticação do Utilizador netskope para provisionamento

1. Inscreva-se na sua [Consola de Administração de Autenticação do Utilizador netskope](https://netskope.goskope.com/). Navegue para **as definições de > em casa**.

    ![Consola de administração de autenticação de utilizadores netskope](media/netskope-administrator-console-provisioning-tutorial/admin.png)

2.  Navegar para **ferramentas**. No menu **Ferramentas,** navegue para **Ferramentas de Diretório > INTEGRAÇÃO SCIM**.

    ![Ferramentas de autenticação do utilizador netskope](media/netskope-administrator-console-provisioning-tutorial/tools.png)

    ![Autenticação do utilizador netskope Adicionar SCIM](media/netskope-administrator-console-provisioning-tutorial/directory.png)

3. Desloque-se para baixo e clique no botão **Adicionar Token.** Na caixa de diálogo **'Adicionar OAuth Client Name'** fornece-se um **NOME CLIENTE** e clica no botão **Guardar.**

    ![Autenticação do utilizador netskope adicionar token](media/netskope-administrator-console-provisioning-tutorial/add.png)

    ![Nome CLient de autenticação do utilizador netskope](media/netskope-administrator-console-provisioning-tutorial/clientname.png)

3.  Copie o URL do **servidor SCIM** e o **TOKEN**. Estes valores serão introduzidos nos campos URL e Secret Token, respectivamente, no separador Provisioning da sua aplicação de Autenticação do Utilizador netskope no portal Azure.

    ![Autenticação do utilizador netskope criar token](media/netskope-administrator-console-provisioning-tutorial/token.png)

## <a name="add-netskope-user-authentication-from-the-gallery"></a>Adicione autenticação do utilizador netskope da galeria

Antes de configurar a Autenticação do Utilizador netskope para o fornecimento automático de utilizadores com Azure AD, é necessário adicionar a Autenticação do Utilizador netskope da galeria de aplicações AD AZure à sua lista de aplicações geridas pelo SaaS.

**Para adicionar a Autenticação do Utilizador Netskope na galeria de aplicações AZure AD, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, selecione **Azure Ative Directory**.

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise** e, em seguida, selecione **Todas as aplicações**.

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, insira **a Autenticação do Utilizador netskope**, selecione **a Autenticação do Utilizador netskope** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![Autenticação do utilizador netskope na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-netskope-user-authentication"></a>Configurar o fornecimento automático do utilizador à Autenticação do Utilizador netskope 

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos na Autenticação do Utilizador netskope com base nas atribuições de utilizador e/ou grupo em Azure AD.

> [!TIP]
> Pode também optar por ativar o sign-on único baseado em SAML para a autenticação do utilizador netskope seguindo as instruções fornecidas no tutorial de autenticação única do [utilizador netskope.](./netskope-cloud-security-tutorial.md) O único sinal pode ser configurado independentemente do fornecimento automático do utilizador, embora estas duas funcionalidades se complementem.

> [!NOTE]
> Para saber mais sobre o ponto final scim do utilizador netskope, consulte [isto](https://docs.google.com/document/d/1n9P_TL98_kd1sx5PAvZL2HS6MQAqkQqd-OSkWAAU6ck/edit#heading=h.prxq74iwdpon).

### <a name="to-configure-automatic-user-provisioning-for-netskope-user-authentication-in-azure-ad"></a>Para configurar o fornecimento automático do utilizador para a autenticação do utilizador netskope em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais** e, em seguida, **Todas as aplicações**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Netskope User Authentication**.

    ![O link de autenticação do utilizador netskope na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Aprovisionamento**.

    ![Screenshot das opções De gestão com a opção Provisioning chamada.](common/provisioning.png)

4. Defina o **Modo de Aprovisionamento** como **Automático**.

    ![Screenshot da lista de retirada do modo de provisionamento com a opção Automática chamada.](common/provisioning-automatic.png)

5. Sob a secção **de Credenciais de Administração,** insira o valor **URL do SERVIDOR SCIM** recuperado anteriormente no **URL do inquilino**. Insira o valor **TOKEN** recuperado anteriormente em **Secret Token**. Clique em **Testar A Ligação** para garantir que o Azure AD pode ligar-se à Autenticação do Utilizador netskope. Se a ligação falhar, certifique-se de que a sua conta de Autenticação do Utilizador netskope tem permissões de Administração e tente novamente.

    ![INQUILINO URL + Token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **'Email' de Notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e verifique a caixa de verificação - **Envie uma notificação de e-mail quando ocorrer uma falha**.

    ![E-mail de Notificação](common/provisioning-notification-email.png)

7. Clique em **Guardar**.

8. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users to Netskope User Authentication**.

    ![Mapeamentos de autenticação do utilizador do utilizador netskope](media/netskope-administrator-console-provisioning-tutorial/usermappings.png)

9. Reveja os atributos do utilizador que são sincronizados de Azure AD para Autenticação do Utilizador netskope na secção **De Mapeamento** de Atributos. Os atributos selecionados como propriedades **de correspondência** são utilizados para corresponder às contas do utilizador na Autenticação do Utilizador netskope para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

    ![Atributos do utilizador de autenticação do utilizador netskope](media/netskope-administrator-console-provisioning-tutorial/userattributes.png)

10. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Groups to Netskope User Authentication**.

    ![Mapeamentos do grupo de autenticação do utilizador netskope](media/netskope-administrator-console-provisioning-tutorial/groupmappings.png)

11. Reveja os atributos do grupo que são sincronizados de Azure AD para Autenticação do Utilizador netskope na secção **De Mapeamento** de Atributos. Os atributos selecionados como propriedades **de correspondência** são utilizados para corresponder aos grupos na Autenticação do Utilizador netskope para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

    ![Atributos do grupo de autenticação do utilizador netskope](media/netskope-administrator-console-provisioning-tutorial/groupattributes.png)

12. Para configurar filtros de âmbito, veja as instruções seguintes disponibilizadas no [Tutorial de filtro de âmbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para ativar o serviço de provisionamento Azure AD para a autenticação do utilizador netskope, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

    ![Estado do Aprovisionamento Ativado](common/provisioning-toggle-on.png)

14. Defina os utilizadores e/ou grupos que deseja prestar à Autenticação do Utilizador netskope, escolhendo os valores pretendidos no **Âmbito** na secção **Definições.**

    ![Âmbito de Aprovisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para aprovisionar, clique em **Guardar**.

    ![Guardar Configuração de Aprovisionamento](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de fornecimento AZure AD esteja em execução. Pode utilizar a secção Detalhes da **Sincronização** para monitorizar o progresso e seguir links para o relatório de atividades de provisionamento, que descreve todas as ações realizadas pelo serviço de fornecimento de AD Azure na Autenticação do Utilizador netskope.

Para obter mais informações sobre como ler os registos de provisionamento da AZure AD, consulte [Reportar sobre o provisionamento automático da conta de utilizador](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento de contas de utilizador para Aplicações Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como analisar os registos e obter relatórios sobre a atividade de aprovisionamento](../app-provisioning/check-status-user-account-provisioning.md)