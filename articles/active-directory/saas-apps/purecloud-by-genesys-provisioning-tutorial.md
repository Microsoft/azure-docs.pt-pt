---
title: 'Tutorial: Configure PureCloud by Genesys para o fornecimento automático de utilizadores com Diretório Ativo Azure [ Microsoft Docs'
description: Aprenda a fornecer e desfornecer automaticamente contas de utilizadores de Azure AD para PureCloud por Genesys.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 5f04b88b-117e-40da-a15c-e3732b240d5d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/05/2020
ms.author: Zhchia
ms.openlocfilehash: 119690b9046821ab538d879e1209c6ef77277370
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77370678"
---
# <a name="tutorial-configure-purecloud-by-genesys-for-automatic-user-provisioning"></a>Tutorial: Configure PureCloud by Genesys para o fornecimento automático de utilizadores

Este tutorial descreve os passos necessários para executar tanto no PureCloud pela Genesys como pelo Azure Ative Directory (Azure AD) para configurar o fornecimento automático de utilizadores. Quando configurado, o Azure AD disponibiliza e desativa automaticamente utilizadores e grupos para [o PureCloud by Genesys](https://www.genesys.com) utilizando o serviço de provisionamento AD Azure. Para detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte o fornecimento e o [desprovisionamento de utilizadores automate para aplicações SaaS com o Diretório Ativo Azure.](../manage-apps/user-provisioning.md) 


## <a name="capabilities-supported"></a>Capacidades suportadas
> [!div class="checklist"]
> * Criar utilizadores no PureCloud por Genesys
> * Remova os utilizadores no PureCloud por Genesys quando já não necessitam de acesso
> * Mantenha os atributos dos utilizadores sincronizados entre O AD Azure e pureCloud por Genesys
> * Grupos de provisão e membros do grupo no PureCloud por Genesys
> * [Inscrição única](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-tutorial) no PureCloud por Genesys (recomendado)

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* [Um inquilino da AD Azure](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Uma conta de utilizador em Azure AD com [permissão](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) para configurar o provisionamento (por exemplo, Administrador de Aplicação, Administrador de Aplicação na Nuvem, Proprietário de Aplicações ou Administrador Global). 
* Uma [organização](https://help.mypurecloud.com/?p=81984)PureCloud.
* Um Utilizador com [permissões](https://help.mypurecloud.com/?p=24360) para criar um Cliente Oauth.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passo 1. Planeie a sua implantação de provisionamento
1. Saiba como funciona o serviço de [provisionamento.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)
2. Determinar quem estará no [âmbito do provisionamento.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)
3. Determine quais os dados a [mapear entre Azure AD e PureCloud por Genesys](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-purecloud-by-genesys-to-support-provisioning-with-azure-ad"></a>Passo 2. Configure PureCloud by Genesys para apoiar o provisionamento com a AD Azure

1. Crie um [Cliente Oauth](https://help.mypurecloud.com/?p=188023) configurado na sua organização PureCloud.
2. Gere um símbolo [com o seu cliente oauth.](https://developer.mypurecloud.com/api/rest/authorization/use-client-credentials.html)
3. Se pretende fornecer automaticamente a adesão ao Grupo dentro do PureCloud, deve [criar Grupos](https://help.mypurecloud.com/?p=52397) em PureCloud com um nome idêntico ao do grupo em Azure AD.

## <a name="step-3-add-purecloud-by-genesys-from-the-azure-ad-application-gallery"></a>Passo 3. Adicione PureCloud por Genesys da galeria de aplicações da AD Azure

Adicione PureCloud por Genesys da galeria de aplicações Azure AD para começar a gerir o provisionamento para PureCloud por Genesys. Se já configurao pureCloud by Genesys para SSO, pode utilizar a mesma aplicação. No entanto, recomenda-se que crie uma aplicação separada ao testar a integração inicialmente. Saiba mais sobre a adição de uma aplicação na galeria [aqui.](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passo 4. Definir quem estará no âmbito do provisionamento 

O serviço de provisionamento de AD Azure permite-lhe examinar quem será provisionado com base na atribuição à aplicação e ou com base em atributos do utilizador/grupo. Se optar por examinar quem será aprovisionado na sua app com base na atribuição, pode utilizar os [seguintes passos](../manage-apps/assign-user-or-group-access-portal.md) para atribuir utilizadores e grupos à aplicação. Se optar por examinar quem será aprovisionado apenas com base em atributos do utilizador ou do grupo, pode utilizar um filtro de deteção como descrito [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Ao atribuir utilizadores e grupos ao PureCloud por Genesys, deve selecionar uma função diferente do **Acesso Padrão**. Os utilizadores com a função de Acesso Predefinido estão excluídos do fornecimento e serão marcados como não tendo direito efetivamente nos registos de fornecimento. Se a única função disponível na aplicação for a função de acesso padrão, pode [atualizar o manifesto de aplicação](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) para adicionar funções adicionais. 

* Comece minúsculo. Teste com um pequeno conjunto de utilizadores e grupos antes de passar para todos. Quando o âmbito de fornecimento for definido para utilizadores e grupos atribuídos, pode controlá-lo atribuindo um ou dois utilizadores ou grupos à aplicação. Quando o âmbito é definido para todos os utilizadores e grupos, pode especificar um filtro de [deteção baseado em atributos](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-purecloud-by-genesys"></a>Passo 5. Configure o fornecimento automático de utilizadores ao PureCloud por Genesys 

Esta secção orienta-o através dos passos para configurar o serviço de provisionamento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos no TestApp com base em atribuições de utilizador e/ou grupo em Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-purecloud-by-genesys-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para PureCloud por Genesys em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais**e, em seguida, selecione **Todas as aplicações**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **PureCloud by Genesys**.

    ![O link PureCloud by Genesys na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Provisioning.**

    ![Guia de provisionamento](common/provisioning.png)

4. Detete o **modo de provisionamento** para **automático**.

    ![Guia de provisionamento](common/provisioning-automatic.png)

5. Sob a secção **de Credenciais de Administrador,** insera o seu PureCloud por Genesys API URL e Oauth Token nos campos **de URL** do Inquilino e **Token Secreto,** respectivamente. O URL DaPi será estruturado como, `{{API Url}}/api/v2/scim/v2`utilizando o URL API para a sua região PureCloud do [PureCloud Developer Center](https://developer.mypurecloud.com/api/rest/index.html). Clique em **Ligação de Teste** para garantir que o Azure AD pode ligar-se ao PureCloud por Genesys. Se a ligação falhar, certifique-se de que a sua conta PureCloud by Genesys tem permissões de Administrador e tente novamente.

    ![provisionamento](./media/purecloud-by-genesys-provisioning-tutorial/provisioning.png)

6. No campo de email de **notificação,** introduza o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de fornecimento e selecione a **notificação de e-mail enviar uma notificação de e-mail quando ocorrer uma falha** verificar a caixa.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Selecione **Guardar**.

8. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Users to PureCloud by Genesys**.

9. Reveja os atributos do utilizador que são sincronizados de Azure AD para PureCloud por Genesys na secção **Attribute-Mapping.** Os atributos selecionados como propriedades **Correspondentes** são usados para combinar as contas de utilizador no PureCloud por Genesys para operações de atualização. Se optar por alterar o [atributo-alvo correspondente,](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)terá de garantir que o PureCloud by Genesys API suporta filtrar os utilizadores com base nesse atributo. Selecione o botão **Guardar** para elegiro qualquer alteração.

     |Atributo|Tipo|
     |---|---|
     |userName|String|
     |ativo|Booleano|
     |displayName|String|
     |e-mails[tipo eq "work"].valor|String|
     |título|String|
     |telefoneNumbers[tipo eq "mobile"].valor|String|
     |telefoneNumbers[tipo eq "trabalho"].valor|String|
     |urn:ietf:params:scim:schemas:extens:enterprise:2.0:User:department|String|
     |urn:ietf:params:scim:schemas:extens:enterprise:2.0:User:manager|Referência|

10. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Groups to PureCloud by Genesys**.

11. Reveja os atributos do grupo que são sincronizados de Azure AD para PureCloud por Genesys na secção **Attribute-Mapping.** Os atributos selecionados como propriedades **correspondentes** são usados para combinar os grupos em PureCloud por Genesys para operações de atualização. Selecione o botão **Guardar** para elegiro qualquer alteração. PureCloud by Genesys não suporta a criação ou eliminação de grupos e apenas suporta a atualização de grupos.

      |Atributo|Tipo|
      |---|---|
      |displayName|String|
      |id externo|String|
      |membros|Referência|

12. Para configurar filtros de deteção, consulte as seguintes instruções fornecidas no tutorial do [filtro Descodificação](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para ativar o serviço de provisionamento de AD Azure para PureCloud por Genesys, altere o Estado de **Provisionamento** para **On** na secção **Definições.**

    ![Estatuto de provisionamento Alternado](common/provisioning-toggle-on.png)

14. Defina os utilizadores e/ou grupos que gostaria de fornecer ao PureCloud pela Genesys, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para fornecer, clique em **Guardar**.

    ![Configuração de fornecimento de poupança](common/provisioning-configuration-save.png)

Esta operação inicia o ciclo inicial de sincronização de todos os utilizadores e grupos definidos no **Âmbito** na secção **Definições.** O ciclo inicial demora mais tempo a realizar do que os ciclos subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento da AD Azure esteja em funcionamento. 

## <a name="step-6-monitor-your-deployment"></a>Passo 6. Monitorizar a implementação
Depois de configurar o fornecimento, utilize os seguintes recursos para monitorizar a sua implementação:

* Utilize os registos de [provisionamento](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) para determinar quais os utilizadores que foram provisionados com sucesso ou sem sucesso
* Verifique a barra de [progresso](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) para ver o estado do ciclo de provisionamento e quão perto está da conclusão
* Se a configuração do fornecimento parecer estar num estado pouco saudável, a aplicação entrará em quarentena. Saiba mais sobre estados de quarentena [aqui.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do provisionamento de conta de utilizador para aplicações empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)
