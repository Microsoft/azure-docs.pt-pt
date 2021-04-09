---
title: 'Tutorial: Configurar as minhas Políticas para o fornecimento automático de utilizadores com o Azure Ative Directory | Microsoft Docs'
description: Saiba como configurar o Azure Ative Directory para provisão automática e desa provisionamento de contas de utilizadores para myPolicies.
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
ms.openlocfilehash: 221f63ab9a7eb3f71a4c730a11565dda64c9edc9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96353590"
---
# <a name="tutorial-configure-mypolicies-for-automatic-user-provisioning"></a>Tutorial: Configurar as minhas Políticas para o provisionamento automático do utilizador

O objetivo deste tutorial é demonstrar os passos a serem realizados no myPolicies e no Azure Ative Directory (Azure AD) para configurar a Azure AD para fornecimento e desavisagem automática de utilizadores e/ou grupos para as myPolicies.

> [!NOTE]
> Este tutorial descreve um conector construído em cima do Serviço de Provisionamento de Utilizadores Azure AD. Para obter detalhes importantes sobre o que este serviço faz, como funciona e perguntas frequentes, veja [Automatizar o aprovisionamento e desaprovisionamento de utilizadores em aplicações SaaS no Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Este conector encontra-se atualmente em Visualização Pública. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* Um inquilino do Azure AD.
* [Um inquilino da MyPolicies.](https://mypolicies.com/index.html#section10)
* Uma conta de utilizador na myPolicies com permissões de Administração.

## <a name="assigning-users-to-mypolicies"></a>Atribuir utilizadores às myPolicies

O Azure Ative Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores e/ou grupos que foram designados para uma aplicação em Azure AD são sincronizados.

Antes de configurar e permitir o fornecimento automático de utilizadores, deve decidir quais os utilizadores e/ou grupos em Azure AD que precisam de acesso às myPolicies. Uma vez decididos, pode atribuir estes utilizadores e/ou grupos à myPolicies seguindo as instruções aqui:
* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-mypolicies"></a>Dicas importantes para atribuir utilizadores às myPolicies

* Recomenda-se que um único utilizador Azure AD seja atribuído à myPolicies para testar a configuração automática de provisionamento do utilizador. Utilizadores e/ou grupos adicionais podem ser atribuídos mais tarde.

* Ao atribuir um utilizador à myPolicies, deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função **Acesso Predefinido** estão excluídos do provisionamento.

## <a name="setup-mypolicies-for-provisioning"></a>Configurar as minhas Políticas para o provisionamento

Antes de configurar myPolicies para o fornecimento automático de utilizadores com Azure AD, você precisará de ativar o fornecimento scim nas myPolicies.

1. Contacte o seu representante da MyPolicies **support@mypolicies.com** para obter o símbolo secreto necessário para configurar o fornecimento do SCIM.

2.  Guarde o valor simbólico fornecido pelo representante da myPolicies. Este valor será inserido no campo **Secret Token** no separador Provisioning da sua aplicação myPolicies no portal Azure.

## <a name="add-mypolicies-from-the-gallery"></a>Adicione as minhas Políticas da galeria

Para configurar as minhas Políticas para o fornecimento automático de utilizadores com Azure AD, é necessário adicionar as minhas Políticas da galeria de aplicações AD AD do Azure à sua lista de aplicações geridas pelo SaaS.

**Para adicionar myPolicies da galeria de aplicações AZure, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, selecione **Azure Ative Directory**.

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise** e, em seguida, selecione **Todas as aplicações**.

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **insira myPolicies,** selecione **myPolicies** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![myPolicies na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-mypolicies"></a>Configurar o fornecimento automático de utilizadores às myPolicies 

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos em myPolicies com base em atribuições de utilizador e/ou grupo em Azure AD.

> [!TIP]
> Pode também optar por ativar um único sign-on baseado em SAML para myPolicies, seguindo as instruções fornecidas no [tutorial de assinatura única myPolicies](mypolicies-tutorial.md). O único sinal pode ser configurado independentemente do fornecimento automático do utilizador, embora estas duas funcionalidades se elogiem mutuamente.

### <a name="to-configure-automatic-user-provisioning-for-mypolicies-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para as myPolicies em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais** e, em seguida, **Todas as aplicações**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **myPolicies**.

    ![O link myPolicies na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Aprovisionamento**.

    ![Screenshot das opções De gestão com a opção Provisioning chamada.](common/provisioning.png)

4. Defina o **Modo de Aprovisionamento** como **Automático**.

    ![Screenshot da lista de retirada do modo de provisionamento com a opção Automática chamada.](common/provisioning-automatic.png)

5. Sob a secção **de Credenciais de Administrador,** insira `https://<myPoliciesCustomDomain>.mypolicies.com/scim` em URL de **inquilino** onde está o seu domínio `<myPoliciesCustomDomain>` personalizado myPolicies. Pode recuperar o seu domínio de cliente myPolicies, a partir do seu URL.
Exemplo: `<demo0-qa>` .mypolicies.com.

6. Em **Secret Token,** insira o valor simbólico que foi recuperado anteriormente. Clique em **Testar a Ligação** para garantir que o Azure AD pode ligar-se às minhas Políticas. Se a ligação falhar, certifique-se de que a sua conta myPolicies tem permissões de Administração e tente novamente.

    ![INQUILINO URL + Token](common/provisioning-testconnection-tenanturltoken.png)

7. No campo **'Email' de Notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e verifique a caixa de verificação - **Envie uma notificação de e-mail quando ocorrer uma falha**.

    ![E-mail de Notificação](common/provisioning-notification-email.png)

8. Clique em **Guardar**.

9. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users to myPolicies**.

    :::image type="content" source="media/mypolicies-provisioning-tutorial/usermapping.png" alt-text="Screenshot da secção mappings. Em Nome, é visível o Synchronize Azure Ative Directory Users para personalappsso." border="false":::

10. Reveja os atributos do utilizador que são sincronizados do AD AD do Azure para as minhas Políticas na secção **De Mapeamento** de Atributos. Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas de utilizador no myPolicies para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

   |Atributo|Tipo|
   |---|---|
   |userName|String|
   |active|Booleano|
   |emails[type eq "work"].value|String|
   |name.givenName|String|
   |name.familyName|String|
   |nome.formatado|String|
   |externalId|String|
   |endereços[tipo eq "work"].país|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|Referência|


11. Para configurar filtros de âmbito, veja as instruções seguintes disponibilizadas no [Tutorial de filtro de âmbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Para ativar o serviço de prestação de Ad Azure para as minhas Políticas, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

    ![Estado do Aprovisionamento Ativado](common/provisioning-toggle-on.png)

13. Defina os utilizadores e/ou grupos que deseja prestar às myPolicies, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de Aprovisionamento](common/provisioning-scope.png)

14. Quando estiver pronto para aprovisionar, clique em **Guardar**.

    ![Guardar Configuração de Aprovisionamento](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de fornecimento AZure AD esteja em execução. Pode utilizar a secção Detalhes da **Sincronização** para monitorizar o progresso e seguir ligações ao relatório de atividades de provisionamento, que descreve todas as ações realizadas pelo serviço de fornecimento de AD Azure nas myPolicies.

Para obter mais informações sobre como ler os registos de provisionamento da AZure AD, consulte [Reportar sobre o provisionamento automático da conta de utilizador](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações do conector

* myPolicies requer sempre **userName,** **email** e **externalId**.
* myPolicies não suporta eliminações duras para atributos do utilizador.

## <a name="change-log"></a>Change log

* 09/15/2020 - Apoio adicional ao atributo "país" para utilizadores.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento de contas de utilizador para Aplicações Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como analisar os registos e obter relatórios sobre a atividade de aprovisionamento](../app-provisioning/check-status-user-account-provisioning.md)
