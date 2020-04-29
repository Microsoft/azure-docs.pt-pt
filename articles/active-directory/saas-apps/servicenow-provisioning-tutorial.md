---
title: 'Tutorial: Configure ServiceNow para fornecimento automático de utilizadores com Diretório Ativo Azure [ Microsoft Docs'
description: Aprenda a fornecer e desfornecer automaticamente contas de utilizador de Azure AD para ServiceNow.
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
ms.openlocfilehash: 9e93d4b3f1880f2ac56a32a7b85aa6801fb7c14e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78205114"
---
# <a name="tutorial-configure-servicenow-for-automatic-user-provisioning"></a>Tutorial: Configure ServiceNow para fornecimento automático de utilizadores

Este tutorial descreve os passos necessários para executar tanto no ServiceNow como no Azure Ative Directory (Azure AD) para configurar o fornecimento automático de utilizadores. Quando configurado, o Azure AD disponibiliza automaticamente e desativa utilizadores e grupos para [serviceNow](https://www.servicenow.com/) utilizando o serviço de provisionamento de AD Azure. Para detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte o fornecimento e o [desprovisionamento de utilizadores automate para aplicações SaaS com o Diretório Ativo Azure.](../app-provisioning/user-provisioning.md) 


## <a name="capabilities-supported"></a>Capacidades Suportadas
> [!div class="checklist"]
> * Criar utilizadores no ServiceNow
> * Remova os utilizadores no ServiceNow quando já não necessitarem de acesso
> * Mantenha os atributos dos utilizadores sincronizados entre o Azure AD e o ServiceNow
> * Grupos de provisão e membros do grupo no ServiceNow
> * [Inscrição única](servicenow-tutorial.md) no ServiceNow (recomendado)

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* [Um inquilino da AD Azure](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Uma conta de utilizador em Azure AD com [permissão](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) para configurar o provisionamento (por exemplo, Administrador de Aplicação, Administrador de Aplicação na Nuvem, Proprietário de Aplicações ou Administrador Global). 
* Um [caso ServiceNow](https://www.servicenow.com/) de Calgary ou superior
* Uma [instância ServiceNow Express](https://www.servicenow.com/) de Helsínquia ou superior
* Uma conta de utilizador no ServiceNow com a função de administrador

## <a name="step-1-plan-your-provisioning-deployment"></a>Passo 1. Planeie a sua implantação de provisionamento
1. Saiba como funciona o serviço de [provisionamento.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)
2. Determinar quem estará no [âmbito do provisionamento.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)
3. Determine quais os dados a [mapear entre o Azure AD e](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)o ServiceNow . 

## <a name="step-2-configure-servicenow-to-support-provisioning-with-azure-ad"></a>Passo 2. Configure ServiceNow para apoiar o provisionamento com a Azure AD

1. Identifique o nome da sua instância ServiceNow. Pode encontrar o nome da instância no URL que utiliza para aceder ao ServiceNow. No exemplo abaixo, o nome da instância é dev35214.

![Caso ServiceNow](media/servicenow-provisioning-tutorial/servicenow_instance.png)

    
2. Obtenha credenciais para um administrador no ServiceNow. Navegue para o perfil do utilizador no ServiceNow e verifique se o utilizador tem a função de administrador. 

![Função de administrador serviceNow](media/servicenow-provisioning-tutorial/servicenow-admin-role.png)

## <a name="step-3-add-servicenow-from-the-azure-ad-application-gallery"></a>Passo 3. Adicione ServiceNow da galeria de aplicações da AD Azure

Adicione serviceNow da galeria de aplicações Azure AD para começar a gerir o provisionamento ao ServiceNow. Se já tiver configurado o ServiceNow para SSO, pode utilizar a mesma aplicação. No entanto, recomenda-se que crie uma aplicação separada ao testar a integração inicialmente. Saiba mais sobre a adição de uma aplicação na galeria [aqui.](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passo 4. Definir quem estará no âmbito do provisionamento 

O serviço de provisionamento de AD Azure permite-lhe examinar quem será provisionado com base na atribuição à aplicação e ou com base em atributos do utilizador/grupo. Se optar por examinar quem será aprovisionado na sua app com base na atribuição, pode utilizar os [seguintes passos](../manage-apps/assign-user-or-group-access-portal.md) para atribuir utilizadores e grupos à aplicação. Se optar por examinar quem será aprovisionado apenas com base em atributos do utilizador ou do grupo, pode utilizar um filtro de deteção como descrito [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Ao atribuir utilizadores e grupos ao ServiceNow, deve selecionar uma função diferente do **Acesso Padrão**. Os utilizadores com a função de Acesso Predefinido estão excluídos do fornecimento e serão marcados como não tendo direito efetivamente nos registos de fornecimento. Se a única função disponível na aplicação for a função de acesso padrão, pode [atualizar o manifesto de aplicação](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) para adicionar funções adicionais. 

* Comece minúsculo. Teste com um pequeno conjunto de utilizadores e grupos antes de passar para todos. Quando o âmbito de fornecimento for definido para utilizadores e grupos atribuídos, pode controlá-lo atribuindo um ou dois utilizadores ou grupos à aplicação. Quando o âmbito é definido para todos os utilizadores e grupos, pode especificar um filtro de [deteção baseado em atributos](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-servicenow"></a>Passo 5. Configure o fornecimento automático de utilizadores ao ServiceNow 

Esta secção orienta-o através dos passos para configurar o serviço de provisionamento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos no TestApp com base em atribuições de utilizador e/ou grupo em Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-servicenow-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para o ServiceNow em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais**e, em seguida, selecione **Todas as aplicações**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **ServiceNow**.

    ![O link ServiceNow na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Provisioning.**

    ![Guia de provisionamento](common/provisioning.png)

4. Detete o **modo de provisionamento** para **automático**.

    ![Guia de provisionamento](common/provisioning-automatic.png)

5. Na secção **credenciais de administrador,** insera as suas credenciais de administração ServiceNow e o seu nome de utilizador. Clique em **Ligação de Teste** para garantir que o Azure AD pode ligar-se ao ServiceNow. Se a ligação falhar, certifique-se de que a sua conta ServiceNow tem permissões de Administrador e tente novamente.

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

    ![Estatuto de provisionamento Alternado](common/provisioning-toggle-on.png)

14. Defina os utilizadores e/ou grupos que gostaria de fornecer ao ServiceNow, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para fornecer, clique em **Guardar**.

    ![Configuração de fornecimento de poupança](common/provisioning-configuration-save.png)

Esta operação inicia o ciclo inicial de sincronização de todos os utilizadores e grupos definidos no **Âmbito** na secção **Definições.** O ciclo inicial demora mais tempo a realizar do que os ciclos subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento da AD Azure esteja em funcionamento. 

## <a name="step-6-monitor-your-deployment"></a>Passo 6. Monitorizar a implementação
Depois de configurar o fornecimento, utilize os seguintes recursos para monitorizar a sua implementação:

1. Utilize os registos de [provisionamento](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) para determinar quais os utilizadores que foram provisionados com sucesso ou sem sucesso
2. Verifique a barra de [progresso](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) para ver o estado do ciclo de provisionamento e quão perto está da conclusão
3. Se a configuração do fornecimento parecer estar num estado pouco saudável, a aplicação entrará em quarentena. Saiba mais sobre estados de quarentena [aqui.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)  

## <a name="troubleshooting-tips"></a>Sugestões de Resolução de Problemas
* **InvalidLookupReference:** Ao fornecer certos atributos, como Department and Location in ServiceNow, os valores já devem existir numa tabela de referência no ServiceNow. Por exemplo, você pode ter duas localizações (Seattle, Los Angeles) e três departamentos (Vendas, Finanças, Marketing) na tabela de nomes de mesa de **inserção** no ServiceNow. Se tentar fornecer um utilizador onde o seu departamento é "Sales" e a localização for "Seattle" ele será provisionado com sucesso. Se tentar fornecer a um utilizador com "Vendas" e localização "LA" o utilizador não será provisionado. A localização LA deve ser adicionada à tabela de referência no ServiceNow ou o atributo do utilizador em Azure AD deve ser atualizado para corresponder ao formato no ServiceNow. 
* **EntryJoiningPropertyValueIs Missing:** Reveja os [mapeamentos](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) do atributo para identificar o atributo correspondente. Este valor deve estar presente no utilizador ou grupo que está a tentar fornecer. 
* Reveja a [API serviceNow SOAP](https://docs.servicenow.com/bundle/newyork-application-development/page/integrate/web-services-apis/reference/r_DirectWebServiceAPIFunctions.html) para compreender quaisquer requisitos ou limitações (por exemplo, formato para especificar código de país para um utilizador)
* Algumas implementações serviceNow requerem permitir gamas IP para o serviço de provisionamento De AD Azure. As gamas IP reservadas para o serviço de provisionamento De AD Azure podem ser consultadas [aqui](https://www.microsoft.com/download/details.aspx?id=56519) no âmbito do "AzureActiveDirectoryDomainServices".
* Os pedidos de fornecimento são enviados por padrão para https://{your-instance-name}.service-now.com/{table-name} . Se necessitar de um URL de inquilino personalizado, pode fornecer todo o URL no campo de nomes da instância.

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do provisionamento de conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
