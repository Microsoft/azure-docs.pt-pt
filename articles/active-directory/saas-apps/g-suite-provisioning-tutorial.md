---
title: 'Tutorial: Configurar suíte G para fornecimento automático de utilizadores com Azure Ative Directory | Microsoft Docs'
description: Saiba como provisão e desavisagem automática de contas de utilizadores de Azure AD a G Suite.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/18/2021
ms.author: Zhchia
ms.openlocfilehash: f6ef2f91d178db01e618c19df55705bc9c1209e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104583756"
---
# <a name="tutorial-configure-g-suite-for-automatic-user-provisioning"></a>Tutorial: Configurar suíte G para fornecimento automático de utilizadores

Este tutorial descreve os passos necessários para realizar tanto na Suíte G como no Azure Ative Directory (Azure AD) para configurar o fornecimento automático do utilizador. Quando configurado, a Azure AD fornece automaticamente e desescvisões utilizadores e grupos para [A Suite G](https://gsuite.google.com/) utilizando o serviço de provisionamento Azure AD. Para obter detalhes importantes sobre o que este serviço faz, como funciona e perguntas frequentes, veja [Automatizar o aprovisionamento e desaprovisionamento de utilizadores em aplicações SaaS no Azure Active Directory](../app-provisioning/user-provisioning.md). 

> [!NOTE]
> Este tutorial descreve um conector construído em cima do Serviço de Provisionamento de Utilizadores Azure AD. Para obter detalhes importantes sobre o que este serviço faz, como funciona e perguntas frequentes, veja [Automatizar o aprovisionamento e desaprovisionamento de utilizadores em aplicações SaaS no Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="capabilities-supported"></a>Capacidades suportadas
> [!div class="checklist"]
> * Criar utilizadores em Suíte G
> * Remova os utilizadores na Suíte G quando já não necessitam de acesso
> * Mantenha os atributos do utilizador sincronizados entre Azure AD e G Suite
> * Grupos de provisão e membros do grupo na Suíte G
> * [Único sinal de inscrição na](./google-apps-tutorial.md) Suíte G (recomendado)

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* [Um inquilino da AD AZure](../develop/quickstart-create-new-tenant.md) 
* Uma conta de utilizador no Azure AD com [permissão](../roles/permissions-reference.md) para configurar o aprovisionamento (por ex., Administrador de Aplicações, Administrador de Aplicações de Cloud, Proprietário da Aplicação ou Administrador Global). 
* [Um inquilino da Suíte G](https://gsuite.google.com/pricing.html)
* Uma conta de utilizador numa Suíte G com permissões de Administração.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passo 1. Planear a sua implementação de aprovisionamento
1. Saiba [como funciona o serviço de aprovisionamento](../app-provisioning/user-provisioning.md).
2. Determine quem vai estar no [âmbito do aprovisionamento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determine quais os dados a [mapear entre Azure AD e G Suite](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-g-suite-to-support-provisioning-with-azure-ad"></a>Passo 2. Configure a Suíte G para apoiar o provisionamento com a Azure AD

Antes de configurar o G Suite para o fornecimento automático do utilizador com a Azure AD, terá de ativar o fornecimento scim em Suíte G.

1. Inscreva-se na [consola G Suite Admin](https://admin.google.com/) com a sua conta de administrador e, em seguida, selecione **Security**. Se não vir a ligação, pode estar escondido no menu **Mais Controlos** na parte inferior do ecrã.

    ![Segurança G Suite](./media/g-suite-provisioning-tutorial/gapps-security.png)

2. Na página **'Segurança',** selecione **Referência API.**

    ![G Suite API](./media/g-suite-provisioning-tutorial/gapps-api.png)

3. Selecione **Ativar o acesso à API**.

    ![G Suite API Ativada](./media/g-suite-provisioning-tutorial/gapps-api-enabled.png)

    > [!IMPORTANT]
   > Para cada utilizador que pretende providenciar à Suíte G, o seu nome de utilizador em Azure AD **deve** estar ligado a um domínio personalizado. Por exemplo, os nomes de utilizador que se parecem bob@contoso.onmicrosoft.com não são aceites pela Suíte G. Por outro lado, bob@contoso.com é aceite. Pode alterar o domínio de um utilizador existente seguindo as instruções [aqui](../fundamentals/add-custom-domain.md).

4. Uma vez adicionado e verificado os seus domínios personalizados desejados com Azure AD, deve verificar novamente com a Suíte G. Para verificar os domínios em Suíte G, consulte os seguintes passos:

    a. Na [consola de administração G Suite](https://admin.google.com/), selecione **Domínios**.

    ![Domínios G Suite](./media/g-suite-provisioning-tutorial/gapps-domains.png)

    b. **Selecione Adicionar um domínio ou um pseudónimo de domínio**.

    ![G Suite Adicionar Domínio](./media/g-suite-provisioning-tutorial/gapps-add-domain.png)

    c. **Selecione Adicione outro domínio** e, em seguida, escreva no nome do domínio que pretende adicionar.

    ![G Suite Adicionar Outro](./media/g-suite-provisioning-tutorial/gapps-add-another.png)

    d. **Selecione Continue e verifique a propriedade do domínio**. Em seguida, siga os passos para verificar se é dono do nome de domínio. Para obter instruções completas sobre como verificar o seu domínio com o Google, consulte [Verifique a propriedade do seu site](https://support.google.com/webmasters/answer/35179).

    e. Repita os passos anteriores para quaisquer domínios adicionais que pretende adicionar à Suíte G.

5. Em seguida, determine qual a conta de administração que pretende utilizar para gerir o fornecimento do utilizador em Suíte G. Navegue para **funções de administração.**

    ![G Suite Administrador](./media/g-suite-provisioning-tutorial/gapps-admin.png)

6. Para o papel de **Administrador** dessa conta, edite os **Privilégios** para esse papel. Certifique-se de que ativa todos os **Privilégios ADMIN API** para que esta conta possa ser utilizada para o provisionamento.

    ![Privilégios G Suite Admin](./media/g-suite-provisioning-tutorial/gapps-admin-privileges.png)

## <a name="step-3-add-g-suite-from-the-azure-ad-application-gallery"></a>Passo 3. Adicione suíte G da galeria de aplicações AZure AD

Adicione suíte G da galeria de aplicações AZure AD para começar a gerir o fornecimento para a Suíte G. Se já configurar previamente O G Suite para SSO, pode utilizar a mesma aplicação. No entanto, é recomendável criar uma aplicação separada ao testar a integração inicialmente. Saiba mais sobre como adicionar uma aplicação a partir da galeria [aqui](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passo 4: Determinar quem vai estar no âmbito do aprovisionamento 

O serviço de aprovisionamento do Azure AD permite-lhe determinar quem vai ser aprovisionado com base na atribuição à aplicação e/ou com base em atributos do utilizador/grupo. Se optar por determinar quem vai ser aprovisionado na sua aplicação com base na atribuição, pode utilizar os seguintes [passos](../manage-apps/assign-user-or-group-access-portal.md) para atribuir utilizadores e grupos à aplicação. Se escolher determinar quem vai ser aprovisionado com base apenas em atributos do utilizador ou grupo, pode utilizar um filtro de âmbito conforme descrito [aqui](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Ao atribuir utilizadores e grupos a Suíte G, deve selecionar uma outra função que não **o Acesso Predefinido**. Os utilizadores com a função Acesso Predefinido são excluídos do aprovisionamento e marcados como não autorizados de forma efetiva nos registos de aprovisionamento. Se a única função disponível na aplicação for a função de acesso predefinido, pode [atualizar o manifesto de aplicação](../develop/howto-add-app-roles-in-azure-ad-apps.md) para adicionar funções adicionais. 

* Comece pequeno. Teste com um pequeno conjunto de utilizadores e grupos antes de implementar para todos. Quando o âmbito do aprovisionamento está definido para os utilizadores e os grupos atribuídos, pode controlar isto ao atribuir um ou dois utilizadores ou grupos à aplicação. Quando o âmbito está definido para todos os utilizadores e grupos, pode especificar um [filtro de âmbito baseado em atributos](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-g-suite"></a>Passo 5. Configure o fornecimento automático do utilizador para a Suíte G 

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos no TestApp com base em atribuições de utilizador e/ou grupo em Azure AD.

> [!NOTE]
> Para saber mais sobre o ponto final da API do Diretório da G Suite, consulte a [API do Diretório](https://developers.google.com/admin-sdk/directory).

### <a name="to-configure-automatic-user-provisioning-for-g-suite-in-azure-ad"></a>Para configurar o fornecimento automático do utilizador para a Suíte G em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais** e, em seguida, **Todas as aplicações**. Os utilizadores terão de iniciar sessão para portal.azure.com e não poderão utilizar aad.portal.azure.com

    ![Painel Aplicações empresariais](./media/g-suite-provisioning-tutorial/enterprise-applications.png)

    ![Painel Todas as aplicações](./media/g-suite-provisioning-tutorial/all-applications.png)

2. Na lista de candidaturas, selecione **G Suite**.

    ![O link G Suite na lista de Aplicações](common/all-applications.png)

3. Selecione o **separador Provisioning.** Clique em **Começar.**

    ![Screenshot das opções De gestão com a opção Provisioning chamada.](common/provisioning.png)

      ![Começar a lâmina](./media/g-suite-provisioning-tutorial/get-started.png)

4. Defina o **Modo de Aprovisionamento** como **Automático**.

    ![Screenshot da lista de retirada do modo de provisionamento com a opção Automática chamada.](common/provisioning-automatic.png)

5. Na secção **Credenciais de Administração,** clique em **Authorize**. Você será redirecionado para uma caixa de diálogo de autorização do Google em uma nova janela do navegador.

      ![G Suite autorizar](./media/g-suite-provisioning-tutorial/authorize-1.png)

6. Confirme que deseja dar permissão à Azure AD para esporar alterações no seu inquilino da Suite G. Selecione **Aceitar**.

     ![G Suite Tenant Auth](./media/g-suite-provisioning-tutorial/gapps-auth.png)

7. No portal Azure, clique em **Test Connection** para garantir que o Azure AD pode ligar-se à Suite G. Se a ligação falhar, certifique-se de que a sua conta G Suite tem permissões de Administração e tente novamente. Então tente o passo **autorizado** novamente.

6. No campo **E-mail de Notificação**, introduza o endereço de e-mail de uma pessoa ou um grupo que deve receber as notificações de erro de aprovisionamento e marque a caixa de verificação **Enviar uma notificação de e-mail quando ocorre uma falha**.

    ![E-mail de Notificação](common/provisioning-notification-email.png)

7. Selecione **Guardar**.

8. Na secção **Mappings,** selecione **Provision Azure Ative Directory Users**.

9. Reveja os atributos do utilizador que são sincronizados de Azure AD a G Suite na secção **De mapeamento de atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas do utilizador em Suíte G para operações de atualização. Se optar por alterar o [atributo de alvo correspondente,](../app-provisioning/customize-application-attributes.md)terá de garantir que a API da Suíte G suporta utilizadores filtrantes com base nesse atributo. Selecione o botão **Guardar** para escoar quaisquer alterações.

   |Atributo|Tipo|
   |---|---|
   |correio primárioEmail|String|
   |relações. [tipo eq "manager"].value|String|
   |name.familyName|String|
   |name.givenName|String|
   |suspenso|String|
   |externalids. [tipo eq "custom"].valor|String|
   |externalids. [tipo eq "organização"].valor|String|
   |endereços. [tipo eq "work"].país|String|
   |endereços. [tipo eq "work"].streetAddress|String|
   |endereços. [tipo eq "work"].região|String|
   |endereços. [tipo eq "work"].localidade|String|
   |endereços. [tipo eq "work"].postalCode|String|
   |e-mails. [tipo eq "work"].endereço|String|
   |organizações. [tipo eq "work"].departamento|String|
   |organizações. [tipo eq "work"].title|String|
   |números de telefone. [tipo eq "work"].value|String|
   |números de telefone. [tipo eq "mobile"].valor|String|
   |números de telefone. [tipo eq "work_fax"].valor|String|
   |e-mails. [tipo eq "work"].endereço|String|
   |organizações. [tipo eq "work"].departamento|String|
   |organizações. [tipo eq "work"].title|String|
   |números de telefone. [tipo eq "work"].value|String|
   |números de telefone. [tipo eq "mobile"].valor|String|
   |números de telefone. [tipo eq "work_fax"].valor|String|
   |endereços. [tipo eq "casa"].país|String|
   |endereços. [tipo eq "home"].formatada|String|
   |endereços. [tipo eq "casa"].localidade|String|
   |endereços. [tipo eq "home"].postalCode|String|
   |endereços. [tipo eq "casa"].região|String|
   |endereços. [tipo eq "home"].streetAddress|String|
   |endereços. [tipo eq "outro"].país|String|
   |endereços. [tipo eq "outro"].formatado|String|
   |endereços. [tipo eq "outro"].localidade|String|
   |endereços. [tipo eq "outro"].postalCode|String|
   |endereços. [tipo eq "outro"].região|String|
   |endereços. [tipo eq "outro"].streetAddress|String|
   |endereços. [tipo eq "work"].formatada|String|
   |changePasswordAtNextLogin|String|
   |e-mails. [tipo eq "home"].endereço|String|
   |e-mails. [tipo eq "outro"].endereço|String|
   |externalids. [tipo eq "conta"].valor|String|
   |externalids. [tipo eq "custom"].customType|String|
   |externalids. [tipo eq "cliente"].valor|String|
   |externalids. [tipo eq "login_id"].value|String|
   |externalids. [tipo eq "rede"].valor|String|
   |género.tipo|String|
   |GeradoImmutableId|String|
   |Identificador|String|
   |Ims. [tipo eq "casa"].protocolo|String|
   |Ims. [tipo eq "outro"].protocolo|String|
   |Ims. [tipo eq "work"].protocolo|String|
   |incluemInGlobalAddressList|String|
   |ipWhitelisted|String|
   |organizações. [tipo eq "escola"].costCenter|String|
   |organizações. [tipo eq "escola"].departamento|String|
   |organizações. [tipo eq "escola"].domínio|String|
   |organizações. [tipo eq "escola"].FullTimeEquivalente|String|
   |organizações. [tipo eq "escola"].localização|String|
   |organizações. [tipo eq "escola"].nome|String|
   |organizações. [tipo eq "escola"].símbolo|String|
   |organizações. [tipo eq "escola"].título|String|
   |organizações. [tipo eq "work"].costCenter|String|
   |organizações. [tipo eq "work"].domínio|String|
   |organizações. [tipo eq "work"].FullTimeEquivallent|String|
   |organizações. [tipo eq "work"].localização|String|
   |organizações. [tipo eq "work"].name|String|
   |organizações. [tipo eq "work"].símbolo|String|
   |OrgUnitPath|String|
   |números de telefone. [tipo eq "casa"].valor|String|
   |números de telefone. [tipo eq "outro"].valor|String|
   |sites. [tipo eq "casa"].valor|String|
   |sites. [tipo eq "outro"].valor|String|
   |sites. [tipo eq "work"].value|String|
   

10. Na secção **Mappings,** selecione **Provision Azure Ative Directory Groups**.

11. Reveja os atributos do grupo que são sincronizados de Azure AD a G Suite na secção **Atributo-Mapping.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar com os grupos em Suíte G para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

      |Atributo|Tipo|
      |---|---|
      |e-mail|String|
      |Membros|String|
      |name|String|
      |descrição|String|

12. Para configurar filtros de âmbito, veja as instruções seguintes disponibilizadas no [Tutorial de filtro de âmbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para ativar o serviço de prestação de Azure AD para a Suite G, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

    ![Estado do Aprovisionamento Ativado](common/provisioning-toggle-on.png)

14. Defina os utilizadores e/ou grupos que deseja prestar à Suíte G, escolhendo os valores pretendidos no **Âmbito** na secção **Definições.**

    ![Âmbito de Aprovisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para aprovisionar, clique em **Guardar**.

    ![Guardar Configuração de Aprovisionamento](common/provisioning-configuration-save.png)

Esta operação inicia o ciclo de sincronização inicial de todos os utilizadores e grupos definidos no **Âmbito** na secção **Definições**. O ciclo inicial leva mais tempo a ser executado do que os ciclos subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de aprovisionamento do Azure AD esteja em execução.

> [!NOTE]
> Se os utilizadores já tiverem uma conta pessoal/consumidor existente utilizando o endereço de e-mail do utilizador Azure AD, então pode causar algum problema que possa ser resolvido utilizando a Ferramenta de Transferência do Google antes de realizar a sincronização do diretório.

## <a name="step-6-monitor-your-deployment"></a>Passo 6. Monitorizar a implementação
Depois de configurar o aprovisionamento, utilize os seguintes recursos para monitorizar a sua implementação:

1. Utilize os [registos de aprovisionamento](../reports-monitoring/concept-provisioning-logs.md) para determinar quais os utilizadores que foram aprovisionados com ou sem êxito
2. Verifique a [barra de progresso](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) para ver o estado do ciclo de aprovisionamento e quão próximo está da conclusão
3. Se a configuração de aprovisionamento parecer estar num mau estado de funcionamento, a aplicação vai entrar em quarentena. Saiba mais sobre os estados de quarentena [aqui](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="change-log"></a>Change log

* 10/17/2020 - Suporte adicional para utilizador e atributos adicionais do g suite e do grupo.
* 10/17/2020 - Nomes-alvo da Suíte G atualizados atribuem nomes para corresponder ao que está definido [aqui](https://developers.google.com/admin-sdk/directory).
* 10/17/2020 - Mapeamentos de atributos predefinidos atualizados.
* 03/18/2021 - O email do gerente está agora sincronizado em vez de ID para todos os novos utilizadores. Para quaisquer utilizadores existentes que foram a provisionados com um gestor como ID, pode fazer um reinício através [do Microsoft Graph](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http) com âmbito "completo" para garantir que o e-mail é a provisionado. Esta alteração só afeta o trabalho de provisão GSuite e não o trabalho de probisção mais antigo a começar pela Goov2OutDelta. Nota: o e-mail do gestor é a provisionado quando o utilizador é criado pela primeira vez ou quando o gestor muda. O e-mail do gestor não é a provisionado se o gestor alterar o seu endereço de e-mail. 

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento de contas de utilizador para Aplicações Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como analisar os registos e obter relatórios sobre a atividade de aprovisionamento](../app-provisioning/check-status-user-account-provisioning.md)
