---
title: 'Tutorial: Configure MediusFlow para fornecimento automático de utilizadores com Diretório Ativo Azure / Microsoft Docs'
description: Saiba como provisão e desa provisionamento automático de contas de utilizadores de Azure AD a MediusFlow.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 04/30/2020
ms.author: Zhchia
ms.openlocfilehash: e8a64b3f42e52363f70d536963594bb6083a0992
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88548077"
---
# <a name="tutorial-configure-mediusflow-for-automatic-user-provisioning"></a>Tutorial: Configure MediusFlow para o provisionamento automático do utilizador

Este tutorial descreve os passos necessários para realizar tanto no MediusFlow como no Azure Ative Directory (Azure AD) para configurar o fornecimento automático do utilizador. Quando configurado, a Azure AD fornece automaticamente e desescvisões utilizadores e grupos para [MediusFlow](https://www.mediusflow.com/) utilizando o serviço de provisionamento Azure AD. Para obter detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte [automatizar o fornecimento e desprovisionamento de aplicações saaS com diretório Azure Ative.](../manage-apps/user-provisioning.md) 


## <a name="capabilities-supported"></a>Capacidades suportadas
> [!div class="checklist"]
> * Criar utilizadores no MediusFlow
> * Remova os utilizadores no MediusFlow quando já não necessitam de acesso
> * Mantenha os atributos do utilizador sincronizados entre Azure AD e MediusFlow
> * Grupos de provisão e membros do grupo no MediusFlow
> * Único sinal de entrada no MediusFlow (recomendado)

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* [Um inquilino da AD AZure](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Uma conta de utilizador em Azure AD com [permissão](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) para configurar o provisionamento (por exemplo, Administrador de Aplicação, Administrador de Aplicação cloud, Proprietário de Aplicações ou Administrador Global). 
* Uma subscrição ativa da MediusFlow com uma garantia de qualidade ou inquilino de produção.
* Uma conta de utilizador no MediusFlow com direitos de acesso administrativo para poder realizar a configuração dentro do MediusFlow.
* As empresas adicionadas no inquilino MediusFlow onde os utilizadores devem ser a provisionados.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passo 1. Planeie a sua implantação de provisionamento
1. Saiba [como funciona o serviço de prestação de serviços.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)
2. Determinar quem estará no [âmbito do provisionamento](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Determine quais os dados a [mapear entre Azure AD e MediusFlow](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-mediusflow-to-support-provisioning-with-azure-ad"></a>Passo 2. Configure MediusFlow para apoiar o provisionamento com Azure AD

### <a name="activate-the-microsoft-365-app-within-mediusflow"></a>Ativar a aplicação Microsoft 365 dentro do MediusFlow
Comece por permitir o acesso do login AD Azure e da funcionalidade de configuração AD Azure dentro do MediusFlow, realizando os seguintes passos:

#### <a name="user-login"></a>Login do utilizador
Para ativar o fluxo de login para o Microsoft 365/Azure AD consulte [este] https://success.mediusflow.com/documentation/administration_guide/user_login_and_transfer/office365userintegration/#user-login-setup) (artigo.

#### <a name="user-transfer-configuration"></a>Configuração de transferência de utilizadores
Para ativar o portal de configuração dos utilizadores para o provisionamento a partir de Azure AD consulte [este](
https://success.mediusflow.com/documentation/administration_guide/user_login_and_transfer/office365userintegration/#user-sync-setup) artigo.

#### <a name="configure-user-provisioning"></a>Configurar o aprovisionamento de utilizadores

1.  Faça login na [consola de administração MediusFlow](https://office365.cloudapp.mediusflow.com/) fornecendo o ID do inquilino.

    ![Autenticar](./media/mediusflow-provisioning-tutorial/1-auth.png)

2. Verifique a ligação com MediusFlow.

    ![Verificação](./media/mediusflow-provisioning-tutorial/2-verify-connection.png)

3. Forneça a identificação do inquilino da Azure.

    ![fornecer ID do inquilino](./media/mediusflow-provisioning-tutorial/3-provide-azuread-tenantid.png)

   Pode ler mais nas [FAQ](https://success.mediusflow.com/documentation/administration_guide/user_login_and_transfer/office365userintegration/#how-do-i-get-the-azure-tenant-id) sobre como encontrá-lo.

4. Guarde a configuração.

    ![Autenticar](./media/mediusflow-provisioning-tutorial/4-save-config.png)

5. Selecione o fornecimento do utilizador e clique **em OK**.

    ![Autenticar](./media/mediusflow-provisioning-tutorial/5-select-user-provisioning.png)

6. Clique em **Gerar Chave Secreta.** Copie e guarde este valor. Este valor será introduzido no campo **Secret Token** no **separador Provisioning** da sua aplicação MediusFLow no portal Azure.

    ![Autenticar](./media/mediusflow-provisioning-tutorial/6-create-secret-1.png)

7. Clique em **OK**.

    ![Autenticar](./media/mediusflow-provisioning-tutorial/7-confirm-secret.png)

8. Para que os utilizadores sejam importados com um conjunto pré-definido de funções, empresas e outras configurações gerais no MediusFlow, terá de o configurar primeiro. Comece por adicionar a configuração clicando na **Nova configuração**.

    ![Utilizadores config](./media/mediusflow-provisioning-tutorial/8-configure-user-configuration-1.png)

9. Forneça as definições predefinidos para os utilizadores. Nesta perspetiva, é possível definir o atributo predefinido. Se as definições padrão estiverem bem, basta fornecer apenas um nome de empresa válido. Uma vez que estas configurações são recolhidas a partir do Mediusflow, precisam de ser configuradas primeiro. Para mais informações consulte a secção **Pré-Requisitos** deste artigo.

    ![Utilizadores config](./media/mediusflow-provisioning-tutorial/9-configure-user-config-detail-1.png)

10. Clique em **Guardar** para guardar a configuração do utilizador.

    ![Utilizadores config](./media/mediusflow-provisioning-tutorial/10-done-1.png)

11. Para obter o link de provisionamento do utilizador clique no **Link Copy SCIM**. Copie e guarde este valor. Este valor é introduzido no campo URL do **inquilino** no **separador Provisioning** da sua aplicação MediusFLow no portal Azure.
 
    ![Utilizadores config](./media/mediusflow-provisioning-tutorial/11-get-scim-link.png)

## <a name="step-3-add-mediusflow-from-the-azure-ad-application-gallery"></a>Passo 3. Adicione MediusFlow da galeria de aplicações AZure AD

Adicione MediusFlow da galeria de aplicações AZure AD para começar a gerir o fornecimento ao MediusFlow. Se já configurar mediusFlow para SSO, pode utilizar a mesma aplicação. No entanto, recomenda-se que crie uma aplicação separada ao testar inicialmente a integração. Saiba mais sobre a adição de uma aplicação na galeria [aqui.](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passo 4: Definir quem estará no âmbito do provisionamento 

O serviço de prestação de Ad Azure permite-lhe atear âmbito a quem será a provisionado com base na atribuição à aplicação e ou com base em atributos do utilizador/grupo. Se optar por escolher o âmbito de aplicação de quem será aprovisionado na sua aplicação com base na atribuição, pode utilizar os [seguintes passos](../manage-apps/assign-user-or-group-access-portal.md) para atribuir utilizadores e grupos à aplicação. Se optar por escolher o âmbito de aplicação de quem será a provisionado apenas com base em atributos do utilizador ou grupo, pode utilizar um filtro de deteção como descrito [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Ao atribuir utilizadores e grupos ao MediusFlow, tem de selecionar outra função que não o **Acesso Predefinido.** Os utilizadores com a função De Acesso Predefinido estão excluídos do provisionamento e serão marcados como não efetivamente intitulados nos registos de provisionamento. Se a única função disponível na aplicação for a função de acesso predefinido, pode [atualizar o manifesto de aplicação](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) para adicionar funções adicionais. 

* Comece minúsculo. Teste com um pequeno conjunto de utilizadores e grupos antes de rolar para todos. Quando o âmbito de provisão é definido para utilizadores e grupos atribuídos, pode controlá-lo atribuindo um ou dois utilizadores ou grupos à aplicação. Quando o âmbito é definido para todos os utilizadores e grupos, pode especificar um [filtro de deteção baseado no atributo](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-mediusflow"></a>Passo 5. Configure o fornecimento automático de utilizadores ao MediusFlow 

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos no TestApp com base em atribuições de utilizador e/ou grupo em Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-mediusflow-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para MediusFlow em AD Azure:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **MediusFlow**.

    ![O link MediusFlow na lista de Aplicações](common/all-applications.png)

3. Selecione o **separador Provisioning.**

    ![Separador de provisionamento](common/provisioning.png)

4. Desa ajuste o **modo de provisionamento** para **automático**.

    ![Separador de provisionamento](common/provisioning-automatic.png)

5. Sob a secção **de Credenciais de Administração,** insira o valor URL do inquilino recuperado anteriormente na **URL do inquilino.** Insira o valor secreto de Token recuperado anteriormente em **Secret Token**. Clique em **'Testar' Ligação** para garantir que o Azure AD pode ligar-se ao MediusFlow. Se a ligação falhar, certifique-se de que a sua conta MediusFlow tem permissões de Administração e tente novamente.

      ![provisionamento](./media/mediusflow-provisioning-tutorial/provisioning.png)

6. No campo **'Email' de Notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e selecione a **notificação enviar uma notificação por e-mail quando ocorrer uma falha** na caixa de verificação.

    ![E-mail de notificação](common/provisioning-notification-email.png)

7. Selecione **Guardar**.

8. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users to MediusFlow**.

9. Reveja os atributos do utilizador que são sincronizados de AD AD a MediusFlow na secção **De mapeamento de atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas de utilizador no MediusFlow para operações de atualização. Se optar por alterar o [atributo de alvo correspondente,](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)terá de garantir que a API MediusFlow suporta utilizadores filtrantes com base nesse atributo. Selecione o botão **Guardar** para escoar quaisquer alterações.

   |Atributo|Tipo|
   |---|---|
   |userName|String|
   |e-mails[tipo eq "work"].value|String|
   |nome.displayName|String|
   |ativo|Booleano|
   |nome.dado Nome|String|
   |nome.famíliaName|String|
   |nome.formatado|String|
   |externalID|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|Referência|


10. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Groups to MediusFlow**.

11. Reveja os atributos do grupo que são sincronizados de AD AD a MediusFlow na secção **De mapeamento de atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar com os grupos no MediusFlow para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

      |Atributo|Tipo|
      |---|---|
      |displayName|String|
      |externalID|String|
      |membros|Referência|

12. Para configurar filtros de deteção, consulte as seguintes instruções fornecidas no tutorial do [filtro de escotagem](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para ativar o serviço de prestação de Ad Azure para MediusFlow, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

    ![Estatuto de Provisionamento Toggled On](common/provisioning-toggle-on.png)

14. Defina os utilizadores e/ou grupos que deseja prestar ao MediusFlow, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para a provisão, clique em **Guardar**.

    ![Configuração de provisionamento de poupança](common/provisioning-configuration-save.png)

Esta operação inicia o ciclo inicial de sincronização de todos os utilizadores e grupos definidos no **Âmbito** na secção **Definições.** O ciclo inicial demora mais tempo a ser efetuado do que os ciclos subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de fornecimento AZure AD esteja em funcionamento. 

## <a name="step-6-monitor-your-deployment"></a>Passo 6. Monitorizar a implementação
Depois de configurar o provisionamento, utilize os seguintes recursos para monitorizar a sua implantação:

1. Utilize os [registos de provisionamento](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) para determinar quais os utilizadores que foram a provisionados com sucesso ou sem sucesso
2. Verifique a [barra de progresso](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) para ver o estado do ciclo de provisionamento e quão perto está da sua conclusão
3. Se a configuração do provisionamento parecer estar num estado pouco saudável, a aplicação entrará em quarentena. Saiba mais sobre estados de quarentena [aqui.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do fornecimento de conta de utilizador para apps empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)
