---
title: 'Tutorial: configurar o ServiceNow para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como provisionar e desprovisionar automaticamente as contas de usuário do Azure AD para o ServiceNow.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 4d6f06dd-a798-4c22-b84f-8a11f1b8592a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/10/2019
ms.author: jeedes
ms.openlocfilehash: 5e954de97e6573e330c42915b44bbc0c40abc54d
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77063252"
---
# <a name="tutorial-configure-servicenow-for-automatic-user-provisioning"></a>Tutorial: configurar o ServiceNow para provisionamento automático de usuário

Este tutorial descreve as etapas que você precisa executar tanto no ServiceNow quanto no Azure Active Directory (Azure AD) para configurar o provisionamento automático de usuário. Quando configurado, o Azure AD disponibiliza automaticamente e desativa utilizadores e grupos para [serviceNow](https://www.servicenow.com/) utilizando o serviço de provisionamento de AD Azure. Para detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte o fornecimento e o [desprovisionamento de utilizadores automate para aplicações SaaS com o Diretório Ativo Azure.](../app-provisioning/user-provisioning.md) 


## <a name="capabilities-supported"></a>Funcionalidades com suporte
> [!div class="checklist"]
> * Criar usuários no ServiceNow
> * Remover usuários no ServiceNow quando eles não precisam mais de acesso
> * Manter os atributos de usuário sincronizados entre o Azure AD e o ServiceNow
> * Provisionar grupos e associações de grupo no ServiceNow
> * [Inscrição única](servicenow-tutorial.md) no ServiceNow (recomendado)

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* [Um inquilino da AD Azure](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Uma conta de utilizador em Azure AD com [permissão](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) para configurar o provisionamento (por exemplo, Administrador de Aplicação, Administrador de Aplicação na Nuvem, Proprietário de Aplicações ou Administrador Global). 
* Um [caso ServiceNow](https://www.servicenow.com/) de Calgary ou superior
* Uma [instância ServiceNow Express](https://www.servicenow.com/) de Helsínquia ou superior
* Uma conta de usuário no ServiceNow com a função de administrador

## <a name="step-1-plan-your-provisioning-deployment"></a>Passo 1. Planejar sua implantação de provisionamento
1. Saiba como funciona o serviço de [provisionamento.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)
2. Determinar quem estará no [âmbito do provisionamento.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)
3. Determine quais os dados a [mapear entre o Azure AD e](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)o ServiceNow . 

## <a name="step-2-configure-servicenow-to-support-provisioning-with-azure-ad"></a>Passo 2. Configurar o ServiceNow para dar suporte ao provisionamento com o Azure AD

1. Identifique o nome da instância do ServiceNow. Você pode encontrar o nome da instância na URL que você usa para acessar o ServiceNow. No exemplo a seguir, o nome da instância é dev35214.

![Instância do ServiceNow](media/servicenow-provisioning-tutorial/servicenow_instance.png)

    
2. Obtenha as credenciais para um administrador no ServiceNow. Navegue até o perfil de usuário no ServiceNow e verifique se o usuário tem a função de administrador. 

![Função de administrador do ServiceNow](media/servicenow-provisioning-tutorial/servicenow-admin-role.png)

## <a name="step-3-add-servicenow-from-the-azure-ad-application-gallery"></a>Passo 3. Adicionar o ServiceNow da Galeria de aplicativos do Azure AD

Adicione o ServiceNow da Galeria de aplicativos do Azure AD para começar a gerenciar o provisionamento no ServiceNow. Se você tiver configurado anteriormente o ServiceNow para SSO, poderá usar o mesmo aplicativo. No entanto, é recomendável que você crie um aplicativo separado ao testar a integração inicialmente. Saiba mais sobre a adição de uma aplicação na galeria [aqui.](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passo 4. Definir quem estará no escopo para provisionamento 

O serviço de provisionamento do Azure AD permite o escopo que será provisionado com base na atribuição ao aplicativo e ou com base em atributos do usuário/grupo. Se optar por examinar quem será aprovisionado na sua app com base na atribuição, pode utilizar os [seguintes passos](../manage-apps/assign-user-or-group-access-portal.md) para atribuir utilizadores e grupos à aplicação. Se optar por examinar quem será aprovisionado apenas com base em atributos do utilizador ou do grupo, pode utilizar um filtro de deteção como descrito [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Ao atribuir utilizadores e grupos ao ServiceNow, deve selecionar uma função diferente do **Acesso Padrão**. Os usuários com a função de acesso padrão são excluídos do provisionamento e serão marcados como não habilitados com eficiência nos logs de provisionamento. Se a única função disponível na aplicação for a função de acesso padrão, pode [atualizar o manifesto de aplicação](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) para adicionar funções adicionais. 

* Comece pequeno. Teste com um pequeno conjunto de usuários e grupos antes de distribuir para todos. Quando o escopo do provisionamento é definido como usuários e grupos atribuídos, você pode controlar isso atribuindo um ou dois usuários ou grupos ao aplicativo. Quando o âmbito é definido para todos os utilizadores e grupos, pode especificar um filtro de [deteção baseado em atributos](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-servicenow"></a>Passo 5. Configurar o provisionamento automático de usuário para o ServiceNow 

Esta seção orienta você pelas etapas para configurar o serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no TestApp com base em atribuições de usuário e/ou grupo no Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-servicenow-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para o ServiceNow no Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais**e, em seguida, selecione **Todas as aplicações**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **ServiceNow**.

    ![O link do ServiceNow na lista de aplicativos](common/all-applications.png)

3. Selecione o separador **Provisioning.**

    ![Guia provisionamento](common/provisioning.png)

4. Detete o **modo de provisionamento** para **automático**.

    ![Guia provisionamento](common/provisioning-automatic.png)

5. Na secção **credenciais de administrador,** insera as suas credenciais de administração ServiceNow e o seu nome de utilizador. Clique em **Ligação de Teste** para garantir que o Azure AD pode ligar-se ao ServiceNow. Se a conexão falhar, verifique se sua conta do ServiceNow tem permissões de administrador e tente novamente.

    ![provisionamento](./media/servicenow-provisioning-tutorial/provisioning.png)

6. No campo de email de **notificação,** introduza o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de fornecimento e selecione a **notificação de e-mail enviar uma notificação de e-mail quando ocorrer uma falha** verificar a caixa.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Selecione **Guardar**.

8. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Users to ServiceNow**.

9. Reveja os atributos do utilizador que são sincronizados de Azure AD para ServiceNow na secção **Attribute-Mapping.** Os atributos selecionados como propriedades **Correspondentes** são usados para combinar as contas de utilizador no ServiceNow para operações de atualização. Se optar por alterar o [atributo-alvo correspondente,](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)terá de garantir que o ServiceNow API suporta filtrar os utilizadores com base nesse atributo. Selecione o botão **Guardar** para elegiro qualquer alteração.

10. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Groups to ServiceNow**.

11. Reveja os atributos do grupo que são sincronizados de Azure AD para ServiceNow na secção **Attribute-Mapping.** Os atributos selecionados como propriedades **Correspondentes** são usados para combinar os grupos no ServiceNow para operações de atualização. Selecione o botão **Guardar** para elegiro qualquer alteração.

12. Para configurar filtros de deteção, consulte as seguintes instruções fornecidas no tutorial do [filtro Descodificação](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para ativar o serviço de provisionamento De AD Azure para o ServiceNow, altere o Estado de **Provisionamento** para **On** na secção **Definições.**

    ![Status de provisionamento alternado em](common/provisioning-toggle-on.png)

14. Defina os utilizadores e/ou grupos que gostaria de fornecer ao ServiceNow, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Escopo de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para fornecer, clique em **Guardar**.

    ![Salvando configuração de provisionamento](common/provisioning-configuration-save.png)

Esta operação inicia o ciclo inicial de sincronização de todos os utilizadores e grupos definidos no **Âmbito** na secção **Definições.** O ciclo inicial leva mais tempo para ser executado do que os ciclos subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure AD esteja em execução. 

## <a name="step-6-monitor-your-deployment"></a>Passo 6. Monitorizar a implementação
Depois de configurar o provisionamento, use os seguintes recursos para monitorar sua implantação:

1. Utilize os registos de [provisionamento](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) para determinar quais os utilizadores que foram provisionados com sucesso ou sem sucesso
2. Verifique a barra de [progresso](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) para ver o estado do ciclo de provisionamento e quão perto está da conclusão
3. Se a configuração de provisionamento parecer estar em um estado não íntegro, o aplicativo entrará em quarentena. Saiba mais sobre estados de quarentena [aqui.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)  

## <a name="troubleshooting-tips"></a>Dicas de resolução de problemas
* **InvalidLookupReference:** Ao fornecer certos atributos, como Department and Location in ServiceNow, os valores já devem existir numa tabela de referência no ServiceNow. Por exemplo, você pode ter duas localizações (Seattle, Los Angeles) e três departamentos (Vendas, Finanças, Marketing) na tabela de nomes de mesa de **inserção** no ServiceNow. Se você tentar provisionar um usuário em que seu departamento é "vendas" e o local for "Seattle", ele será provisionado com êxito. Se você tentar provisionar um usuário com o departamento "vendas" e o local "LA", o usuário não será provisionado. O local LA deve ser adicionado à tabela de referência no ServiceNow ou o atributo de usuário no Azure AD deve ser atualizado para corresponder ao formato no ServiceNow. 
* **EntryJoiningPropertyValueIs Missing:** Reveja os [mapeamentos](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) do atributo para identificar o atributo correspondente. Esse valor deve estar presente no usuário ou grupo que você está tentando provisionar. 
* Reveja a [API serviceNow SOAP](https://docs.servicenow.com/bundle/newyork-application-development/page/integrate/web-services-apis/reference/r_DirectWebServiceAPIFunctions.html) para compreender quaisquer requisitos ou limitações (por exemplo, formato para especificar código de país para um utilizador)
* Algumas implantações do ServiceNow exigem a permissão de intervalos de IP para o serviço de provisionamento do Azure AD. As gamas IP reservadas para o serviço de provisionamento De AD Azure podem ser consultadas [aqui](https://www.microsoft.com/download/details.aspx?id=56519) no âmbito do "AzureActiveDirectoryDomainServices".
* Atualmente, não há suporte para o provisionamento de usuários para a nuvem do ServiceNow governamental.

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do provisionamento de conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
