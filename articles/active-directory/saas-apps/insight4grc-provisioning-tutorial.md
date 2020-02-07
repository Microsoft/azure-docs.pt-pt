---
title: 'Tutorial: Configure Insight4GRC para fornecimento automático de utilizadores com Diretório Ativo Azure  Microsoft Docs'
description: Aprenda a fornecer e desfornecer automaticamente contas de utilizadores de Azure AD para Insight4GRC.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: d0eab8a0-571b-4609-96b1-bdbc761a25de
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/04/2020
ms.author: Zhchia
ms.openlocfilehash: 0ca9ed8781a13f9ab5e949e0e5f019a851dc75f4
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77057486"
---
# <a name="tutorial-configure-insight4grc-for-automatic-user-provisioning"></a>Tutorial: Configure Insight4GRC para fornecimento automático de utilizadores

Este tutorial descreve os passos necessários para executar tanto no Insight4GRC como no Azure Ative Directory (Azure AD) para configurar o fornecimento automático de utilizadores. Quando configurado, a Azure AD disponibiliza e desativa automaticamente utilizadores e grupos para [insight4GRC](https://www.rsmuk.com/) utilizando o serviço de provisionamento de AD Azure. Para detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte o fornecimento e o [desprovisionamento de utilizadores automate para aplicações SaaS com o Diretório Ativo Azure.](../manage-apps/user-provisioning.md) 


## <a name="capabilities-supported"></a>Capacidades suportadas
> [!div class="checklist"]
> * Criar utilizadores no Insight4GRC
> * Remova os utilizadores no Insight4GRC quando já não necessitam de acesso
> * Mantenha os atributos dos utilizadores sincronizados entre o Azure AD e o Insight4GRC
> * Grupos de provisões e membros do grupo no Insight4GRC
> * [Inscrição única](https://docs.microsoft.com/azure/active-directory/saas-apps/insight4grc-tutorial) no Insight4GRC (recomendado)

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* [Um inquilino da AD Azure](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Uma conta de utilizador em Azure AD com [permissão](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) para configurar o provisionamento (por exemplo, Administrador de Aplicação, Administrador de Aplicação na Nuvem, Proprietário de Aplicações ou Administrador Global). 
* Uma conta de utilizador no Insight4GRC com permissões de administrador.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passo 1. Planejar sua implantação de provisionamento
1. Saiba como funciona o serviço de [provisionamento.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)
2. Determinar quem estará no [âmbito do provisionamento.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)
3. Determine quais os dados a [mapear entre o Azure AD e o Insight4GRC](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-insight4grc-to-support-provisioning-with-azure-ad"></a>Passo 2. Configure insight4GRC para apoiar o provisionamento com a Azure AD

Antes de configurar o Insight4GRC para o fornecimento automático de utilizadores com a AD Azure, terá de ativar o fornecimento de SCIM no Insight4GRC.

1. Para obter o token do portador, o cliente final precisa de contactar a equipa de [suporte](mailto:support.ss@rsmuk.com).
2. Para obter o URL final do Ponto Final do SCIM, terá de ter o seu nome de domínio Insight4GRC pronto, uma vez que será utilizado para construir o seu URL final de ponto final do SCIM. Pode recuperar o nome de domínio Insight4GRC como parte da compra inicial de software com o Insight4GRC.

## <a name="step-3-add-insight4grc-from-the-azure-ad-application-gallery"></a>Passo 3. Adicione insight4GRC da galeria de aplicações da AD Azure

Adicione o Insight4GRC da galeria de aplicações Azure AD para começar a gerir o provisionamento ao Insight4GRC. Se já configurar previamente o Insight4GRC para SSO, pode utilizar a mesma aplicação. No entanto, é recomendável que você crie um aplicativo separado ao testar a integração inicialmente. Saiba mais sobre a adição de uma aplicação na galeria [aqui.](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passo 4. Definir quem estará no escopo para provisionamento 

O serviço de provisionamento do Azure AD permite o escopo que será provisionado com base na atribuição ao aplicativo e ou com base em atributos do usuário/grupo. Se optar por examinar quem será aprovisionado na sua app com base na atribuição, pode utilizar os [seguintes passos](../manage-apps/assign-user-or-group-access-portal.md) para atribuir utilizadores e grupos à aplicação. Se optar por examinar quem será aprovisionado apenas com base em atributos do utilizador ou do grupo, pode utilizar um filtro de deteção como descrito [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Ao atribuir utilizadores e grupos ao Insight4GRC, deve selecionar uma função diferente do **Acesso Predefinido**. Os usuários com a função de acesso padrão são excluídos do provisionamento e serão marcados como não habilitados com eficiência nos logs de provisionamento. Se a única função disponível na aplicação for a função de acesso padrão, pode [atualizar o manifesto de aplicação](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) para adicionar funções adicionais. 

* Comece pequeno. Teste com um pequeno conjunto de usuários e grupos antes de distribuir para todos. Quando o escopo do provisionamento é definido como usuários e grupos atribuídos, você pode controlar isso atribuindo um ou dois usuários ou grupos ao aplicativo. Quando o âmbito é definido para todos os utilizadores e grupos, pode especificar um filtro de [deteção baseado em atributos](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-insight4grc"></a>Passo 5. Configure o fornecimento automático de utilizadores ao Insight4GRC 

Esta seção orienta você pelas etapas para configurar o serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no TestApp com base em atribuições de usuário e/ou grupo no Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-insight4grc-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para insight4GRC em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais**e, em seguida, selecione **Todas as aplicações**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Insight4GRC**.

    ![A ligação Insight4GRC na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Provisioning.**

    ![Guia provisionamento](common/provisioning.png)

4. Detete o **modo de provisionamento** para **automático**.

    ![Guia provisionamento](common/provisioning-automatic.png)

5. Na secção **credenciais de administrador,** insera as suas credenciais de administrador Insight4GRC e o seu nome de utilizador. Clique em **Ligação de Teste** para garantir que o Azure AD pode ligar-se ao Insight4GRC. Se a ligação falhar, certifique-se de que a sua conta Insight4GRC tem permissões de administrador e tente novamente.

    ![provisionamento](./media/insight4grc-provisioning-tutorial/provisioning.png)

6. No campo de email de **notificação,** introduza o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de fornecimento e selecione a **notificação de e-mail enviar uma notificação de e-mail quando ocorrer uma falha** verificar a caixa.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Selecione **Guardar**.

8. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Users to Insight4GRC**.

9. Reveja os atributos do utilizador que são sincronizados de Azure AD para Insight4GRC na secção **Attribute-Mapping.** Os atributos selecionados como propriedades **Correspondentes** são usados para combinar as contas de utilizador no Insight4GRC para operações de atualização. Se optar por alterar o [atributo-alvo correspondente,](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)terá de garantir que a API insight4GRC suporta filtrar os utilizadores com base nesse atributo. Selecione o botão **Guardar** para elegiro qualquer alteração.

   |Atributo|Tipo|
   |---|---|
   |userName|String|
   |externalId|String|
   |ativo|Booleano|
   |Título|String|
   |name.givenName|String|
   |name.familyName|String|
   |. Value de e-mails [tipo eq "trabalho"]|String|
   |. Value de phoneNumbers [tipo eq "trabalho"]|String|

10. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Groups to Insight4GRC**.

11. Reveja os atributos do grupo que são sincronizados de Azure AD para Insight4GRC na secção **Attribute-Mapping.** Os atributos selecionados como propriedades **correspondentes** são usados para combinar os grupos no Insight4GRC para operações de atualização. Selecione o botão **Guardar** para elegiro qualquer alteração.

      |Atributo|Tipo|
      |---|---|
      |displayName|String|
      |externalId|String|
      |membros|Referência|

10. Para configurar filtros de deteção, consulte as seguintes instruções fornecidas no tutorial do [filtro Descodificação](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para ativar o serviço de provisionamento De AD Azure para insight4GRC, altere o Estado de **Provisionamento** para **On** na secção **Definições.**

    ![Status de provisionamento alternado em](common/provisioning-toggle-on.png)

14. Defina os utilizadores e/ou grupos que deseja fornecer ao Insight4GRC, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Escopo de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para fornecer, clique em **Guardar**.

    ![Salvando configuração de provisionamento](common/provisioning-configuration-save.png)

Esta operação inicia o ciclo inicial de sincronização de todos os utilizadores e grupos definidos no **Âmbito** na secção **Definições.** O ciclo inicial leva mais tempo para ser executado do que os ciclos subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure AD esteja em execução. 

## <a name="step-6-monitor-your-deployment"></a>Passo 6. Monitorizar a implementação
Depois de configurar o provisionamento, use os seguintes recursos para monitorar sua implantação:

* Utilize os [registos](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) de fornecimento para determinar quais os utilizadores que foram provisionados com sucesso ou sem sucesso.
* Verifique a barra de [progresso](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) para ver o estado do ciclo de provisionamento e a sua proximidade até à conclusão.
* Se a configuração de provisionamento parecer estar em um estado não íntegro, o aplicativo entrará em quarentena. Saiba mais sobre estados de quarentena [aqui.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o provisionamento de contas de utilizador para aplicações empresariais.](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Aprenda a rever os registos e obtenha relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md).
