---
title: 'Tutorial: Configurar Soloinsight-CloudGate SSO para fornecimento automático de utilizadores com Diretório Ativo Azure / Microsoft Docs'
description: Aprenda a configurar o Azure Ative Directory para provisões automáticas e desavisagem de contas de utilizadores para Soloinsight-CloudGate SSO.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 10/14/2019
ms.author: Zhchia
ms.openlocfilehash: aa9ed0954cbfa2d83eeed1c70f40beedcf4f44cd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91285970"
---
# <a name="tutorial-configure-soloinsight-cloudgate-sso-for-automatic-user-provisioning"></a>Tutorial: Configurar Soloinsight-CloudGate SSO para o fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados no Soloinsight-CloudGate SSO e Azure Ative Directory (Azure AD) para configurar a Azure AD para provisão automática e desa provisionamento de utilizadores e/ou grupos para Soloinsight-CloudGate SSO.

> [!NOTE]
> Este tutorial descreve um conector construído em cima do Serviço de Provisionamento de Utilizadores Azure AD. Para obter detalhes importantes sobre o que este serviço faz, como funciona e perguntas frequentes, veja [Automatizar o aprovisionamento e desaprovisionamento de utilizadores em aplicações SaaS no Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Este conector encontra-se atualmente em Visualização Pública. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* Um inquilino da AD AZure
* [Um inquilino da SSO Soloinsight-CloudGate](https://www.soloinsight.com/)
* Uma conta de utilizador em Soloinsight-CloudGate SSO com permissões de Administração.

## <a name="assigning-users-to-soloinsight-cloudgate-sso"></a>Atribuir utilizadores a Soloinsight-CloudGate SSO

O Azure Ative Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores e/ou grupos que foram designados para uma aplicação em Azure AD são sincronizados.

Antes de configurar e permitir o fornecimento automático do utilizador, deve decidir quais os utilizadores e/ou grupos em Azure AD que precisam de acesso a Soloinsight-CloudGate SSO. Uma vez decididos, pode atribuir estes utilizadores e/ou grupos a Soloinsight-CloudGate SSO seguindo as instruções aqui:
* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-soloinsight-cloudgate-sso"></a>Dicas importantes para atribuir utilizadores a Soloinsight-CloudGate SSO

* Recomenda-se que um único utilizador Azure AD seja designado para Soloinsight-CloudGate SSO para testar a configuração automática de provisionamento do utilizador. Utilizadores e/ou grupos adicionais podem ser atribuídos mais tarde.

* Ao atribuir um utilizador a Soloinsight-CloudGate SSO, deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função **Acesso Predefinido** estão excluídos do provisionamento.

## <a name="set-up-soloinsight-cloudgate-sso-for-provisioning"></a>Configurar Soloinsight-CloudGate SSO para provisionamento

1. Inscreva-se na sua [Consola de Administração SSO Soloinsight-CloudGate](https://soloinsight.sigateway.com/login). Navegue para **a Administração > Configurações do Sistema**.

    ![Consola de administração SSO Soloinsight-CloudGate](media/soloinsight-cloudgate-sso-provisioning-tutorial/admin.png)

2.  Navegue para **o General.**

    ![Soloinsight-CloudGate SSO Add SCIM](media/soloinsight-cloudgate-sso-provisioning-tutorial/config.png)

3.  Percorra até ao final da página para obter o URL do **inquilino** e **o Token Secreto.** Copie o **Símbolo Secreto.** Este valor será introduzido no campo Secret Token no separador Provisioning da sua aplicação SSO Soloinsight-CloudGate no portal Azure.

    ![Soloinsight-CloudGate SSO Criar Token](media/soloinsight-cloudgate-sso-provisioning-tutorial/token.png)

## <a name="add-soloinsight-cloudgate-sso-from-the-gallery"></a>Adicione Soloinsight-CloudGate SSO da galeria

Antes de configurar Soloinsight-CloudGate SSO para fornecimento automático de utilizadores com Azure AD, é necessário adicionar Soloinsight-CloudGate SSO da galeria de aplicações AD AZure à sua lista de aplicações geridas pelo SaaS.

**Para adicionar Soloinsight-CloudGate SSO da galeria de aplicações AZure, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, selecione **Azure Ative Directory**.

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise**e, em seguida, selecione **Todas as aplicações**.

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, insira **Soloinsight-CloudGate SSO**, selecione **Soloinsight-CloudGate SSO** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![SSO Soloinsight-CloudGate na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-soloinsight-cloudgate-sso"></a>Configurar o fornecimento automático do utilizador ao SSO Soloinsight-CloudGate 

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos em Soloinsight-CloudGate SSO com base nas atribuições de utilizador e/ou grupo em Azure AD.

> [!TIP]
> Pode também optar por ativar o sign-on único baseado em SAML para Soloinsight-CloudGate SSO, seguindo as instruções fornecidas no [tutorial de assinatura Soloinsight-CloudGate SSO Single.](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-tutorial) O único sinal pode ser configurado independentemente do fornecimento automático do utilizador, embora estas duas funcionalidades se elogiem mutuamente

### <a name="to-configure-automatic-user-provisioning-for-soloinsight-cloudgate-sso-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para Soloinsight-CloudGate SSO em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais** e, em seguida, **Todas as aplicações**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Soloinsight-CloudGate SSO**.

    ![O link SSO Soloinsight-CloudGate na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Aprovisionamento**.

    ![Screenshot das opções De gestão com a opção Provisioning chamada.](common/provisioning.png)

4. Defina o **Modo de Aprovisionamento** como **Automático**.

    ![Screenshot da lista de retirada do modo de provisionamento com a opção Automática chamada.](common/provisioning-automatic.png)

5. Sob a secção **de Credenciais de Administração,** entrada `https://sigateway.com/scim/v2/sync/serviceproviderconfig` na URL do **inquilino.** Insira o valor **de token de autenticação SCIM** recuperado anteriormente em **Secret Token**. Clique em **'Testar' Ligação** para garantir que o Azure AD pode ligar-se a Soloinsight-CloudGate SSO. Se a ligação falhar, certifique-se de que a sua conta SSO Soloinsight-CloudGate tem permissões de administração e tente novamente.

    ![INQUILINO URL + Token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **'Email' de Notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e verifique a caixa de verificação - **Envie uma notificação de e-mail quando ocorrer uma falha**.

    ![E-mail de Notificação](common/provisioning-notification-email.png)

7. Clique em **Guardar**.

8. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users para Soloinsight-CloudGate SSO**.

    ![Soloinsight-CloudGate Mapeamentos de Utilizadores SSO](media/soloinsight-cloudgate-sso-provisioning-tutorial/usermappings.png)

9. Reveja os atributos do utilizador que são sincronizados de AD AD AD a Soloinsight-CloudGate SSO na secção **De Mapeamento** de Atributos. Os atributos selecionados como propriedades **de correspondência** são utilizados para corresponder às contas do utilizador em Soloinsight-CloudGate SSO para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

    ![Soloinsight-CloudGate Atributos do Utilizador SSO](media/soloinsight-cloudgate-sso-provisioning-tutorial/userattributes.png)

10. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Groups para Soloinsight-CloudGate SSO**.

    ![Mapeamentos do Grupo SSO Soloinsight-CloudGate](media/soloinsight-cloudgate-sso-provisioning-tutorial/groupmappings.png)

11. Reveja os atributos do grupo que são sincronizados de AD AD a Soloinsight-CloudGate SSO na secção **De Mapeamento** de Atributos. Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar com os grupos em Soloinsight-CloudGate SSO para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

    ![Atributos do grupo SSO Soloinsight-CloudGate](media/soloinsight-cloudgate-sso-provisioning-tutorial/groupattributes.png)

12. Para configurar filtros de âmbito, veja as instruções seguintes disponibilizadas no [Tutorial de filtro de âmbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para ativar o serviço de prestação de Ad Azure para Soloinsight-CloudGate SSO, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

    ![Estado do Aprovisionamento Ativado](common/provisioning-toggle-on.png)

14. Defina os utilizadores e/ou grupos que deseja prestar à Soloinsight-CloudGate SSO, escolhendo os valores pretendidos no **Âmbito** na secção **Definições.**

    ![Âmbito de Aprovisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para aprovisionar, clique em **Guardar**.

    ![Guardar Configuração de Aprovisionamento](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de fornecimento AZure AD esteja em execução. Pode utilizar a secção Detalhes da **Sincronização** para monitorizar o progresso e seguir ligações ao relatório de atividades de provisionamento, que descreve todas as ações realizadas pelo serviço de fornecimento de Ad AZure em Soloinsight-CloudGate SSO.

Para obter mais informações sobre como ler os registos de provisionamento da AZure AD, consulte [Reportar sobre o provisionamento automático da conta de utilizador](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento de contas de utilizador para Aplicações Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como analisar os registos e obter relatórios sobre a atividade de aprovisionamento](../app-provisioning/check-status-user-account-provisioning.md)

