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
ms.openlocfilehash: da733eef4dcfc15db10bb5bf303086ae601189ed
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75443185"
---
# <a name="tutorial-configure-servicenow-for-automatic-user-provisioning"></a>Tutorial: configurar o ServiceNow para provisionamento automático de usuário

Este tutorial descreve as etapas que você precisa executar tanto no ServiceNow quanto no Azure Active Directory (Azure AD) para configurar o provisionamento automático de usuário. Quando configurado, o Azure AD provisiona e desprovisiona automaticamente usuários e grupos para o [ServiceNow](https://www.servicenow.com/) usando o serviço de provisionamento do Azure AD. Para obter detalhes importantes sobre o que esse serviço faz, como ele funciona e perguntas frequentes, consulte [automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funcionalidades com suporte
> [!div class="checklist"]
> * Criar usuários no ServiceNow
> * Remover usuários no ServiceNow quando eles não precisam mais de acesso
> * Manter os atributos de usuário sincronizados entre o Azure AD e o ServiceNow
> * Provisionar grupos e associações de grupo no ServiceNow
> * [Logon único](servicenow-tutorial.md) no ServiceNow (recomendado)

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* [Um locatário do Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Uma conta de usuário no Azure AD com [permissão](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) para configurar o provisionamento (por exemplo, administrador de aplicativos, administrador de aplicativos de nuvem, proprietário do aplicativo ou administrador global). 
* Uma [instância do ServiceNow](https://www.servicenow.com/) de Calgary ou superior
* Uma [instância do ServiceNow Express](https://www.servicenow.com/) do Helsinque ou superior
* Uma conta de usuário no ServiceNow com a função de administrador

## <a name="step-1-plan-your-provisioning-deployment"></a>Passo 1. Planejar sua implantação de provisionamento
1. Saiba mais sobre [como o serviço de provisionamento funciona](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Determine quem estará no [escopo do provisionamento](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Determine quais dados [mapeados entre o Azure AD e o ServiceNow](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-servicenow-to-support-provisioning-with-azure-ad"></a>Passo 2. Configurar o ServiceNow para dar suporte ao provisionamento com o Azure AD

1. Identifique o nome da instância do ServiceNow. Você pode encontrar o nome da instância na URL que você usa para acessar o ServiceNow. No exemplo a seguir, o nome da instância é dev35214.

![Instância do ServiceNow](media/servicenow-provisioning-tutorial/servicenow_instance.png)

    
2. Obtenha as credenciais para um administrador no ServiceNow. Navegue até o perfil de usuário no ServiceNow e verifique se o usuário tem a função de administrador. 

![Função de administrador do ServiceNow](media/servicenow-provisioning-tutorial/servicenow-admin-role.png)

## <a name="step-3-add-servicenow-from-the-azure-ad-application-gallery"></a>Passo 3. Adicionar o ServiceNow da Galeria de aplicativos do Azure AD

Adicione o ServiceNow da Galeria de aplicativos do Azure AD para começar a gerenciar o provisionamento no ServiceNow. Se você tiver configurado anteriormente o ServiceNow para SSO, poderá usar o mesmo aplicativo. No entanto, é recomendável que você crie um aplicativo separado ao testar a integração inicialmente. Saiba mais sobre como adicionar um aplicativo da Galeria [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passo 4. Definir quem estará no escopo para provisionamento 

O serviço de provisionamento do Azure AD permite o escopo que será provisionado com base na atribuição ao aplicativo e ou com base em atributos do usuário/grupo. Se você optar por definir o escopo que será provisionado em seu aplicativo com base na atribuição, poderá usar as [etapas](../manage-apps/assign-user-or-group-access-portal.md) a seguir para atribuir usuários e grupos ao aplicativo. Se você escolher o escopo que será provisionado com base apenas em atributos do usuário ou grupo, você poderá usar um filtro de escopo conforme descrito [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Ao atribuir usuários e grupos ao ServiceNow, você deve selecionar uma função diferente de **acesso padrão**. Os usuários com a função de acesso padrão são excluídos do provisionamento e serão marcados como não habilitados com eficiência nos logs de provisionamento. Se a única função disponível no aplicativo for a função de acesso padrão, você poderá [atualizar o manifesto do aplicativo](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) para adicionar outras funções. 

* Comece pequeno. Teste com um pequeno conjunto de usuários e grupos antes de distribuir para todos. Quando o escopo do provisionamento é definido como usuários e grupos atribuídos, você pode controlar isso atribuindo um ou dois usuários ou grupos ao aplicativo. Quando o escopo é definido como todos os usuários e grupos, você pode especificar um [filtro de escopo baseado em atributo](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-servicenow"></a>Passo 5. Configurar o provisionamento automático de usuário para o ServiceNow 

Esta seção orienta você pelas etapas para configurar o serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no TestApp com base em atribuições de usuário e/ou grupo no Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-servicenow-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para o ServiceNow no Azure AD:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com). Selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **ServiceNow**.

    ![O link do ServiceNow na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **provisionamento** .

    ![Guia provisionamento](common/provisioning.png)

4. Defina o **modo de provisionamento** como **automático**.

    ![Guia provisionamento](common/provisioning-automatic.png)

5. Na seção **credenciais de administrador** , insira suas credenciais de administrador do ServiceNow e o nome de usuário. Clique em **testar conexão** para garantir que o Azure ad possa se conectar ao ServiceNow. Se a conexão falhar, verifique se sua conta do ServiceNow tem permissões de administrador e tente novamente.

    ![provisionamento](./media/servicenow-provisioning-tutorial/provisioning.png)

6. No campo **email de notificação** , insira o endereço de email de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e marque a caixa de seleção **Enviar uma notificação por email quando ocorrer uma falha** .

    ![Email de notificação](common/provisioning-notification-email.png)

7. Selecione **Guardar**.

8. Na seção **mapeamentos** , selecione **sincronizar Azure Active Directory usuários para o ServiceNow**.

9. Examine os atributos de usuário que são sincronizados do Azure AD para o ServiceNow na seção de **mapeamento de atributo** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário no ServiceNow para operações de atualização. Se você optar por alterar o [atributo de destino correspondente](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), será necessário garantir que a API do ServiceNow dê suporte à filtragem de usuários com base nesse atributo. Selecione o botão **salvar** para confirmar as alterações.

10. Na seção **mapeamentos** , selecione **sincronizar grupos de Azure Active Directory para o ServiceNow**.

11. Examine os atributos de grupo que são sincronizados do Azure AD para o ServiceNow na seção de **mapeamento de atributo** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder os grupos no ServiceNow para operações de atualização. Selecione o botão **salvar** para confirmar as alterações.

12. Para configurar filtros de escopo, consulte as instruções a seguir fornecidas no [tutorial de filtro de escopo](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para habilitar o serviço de provisionamento do Azure AD para o ServiceNow, altere o **status de provisionamento** para **ativado** na seção **configurações** .

    ![Status de provisionamento alternado em](common/provisioning-toggle-on.png)

14. Defina os usuários e/ou grupos que você deseja provisionar para o ServiceNow escolhendo os valores desejados no **escopo** na seção **configurações** .

    ![Escopo de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para provisionar, clique em **salvar**.

    ![Salvando configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia o ciclo de sincronização inicial de todos os usuários e grupos definidos no **escopo** na seção **configurações** . O ciclo inicial leva mais tempo para ser executado do que os ciclos subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure AD esteja em execução. 

## <a name="step-6-monitor-your-deployment"></a>Passo 6. Monitorizar a implementação
Depois de configurar o provisionamento, use os seguintes recursos para monitorar sua implantação:

1. Use os [logs de provisionamento](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) para determinar quais usuários foram provisionados com êxito ou sem êxito
2. Verifique a [barra de progresso](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) para ver o status do ciclo de provisionamento e como fechá-lo para conclusão
3. Se a configuração de provisionamento parecer estar em um estado não íntegro, o aplicativo entrará em quarentena. Saiba mais sobre os Estados de quarentena [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## <a name="troubleshooting-tips"></a>Sugestões de Resolução de Problemas
* **InvalidLookupReference:** Ao provisionar determinados atributos, como Department e Location no ServiceNow, os valores já devem existir em uma tabela de referência no ServiceNow. Por exemplo, você pode ter dois locais (Seattle, Los Angeles) e três departamentos (vendas, finanças, marketing) na tabela **nome da tabela de inserção** no ServiceNow. Se você tentar provisionar um usuário em que seu departamento é "vendas" e o local for "Seattle", ele será provisionado com êxito. Se você tentar provisionar um usuário com o departamento "vendas" e o local "LA", o usuário não será provisionado. O local LA deve ser adicionado à tabela de referência no ServiceNow ou o atributo de usuário no Azure AD deve ser atualizado para corresponder ao formato no ServiceNow. 
* **EntryJoiningPropertyValueIsMissing:** Examine os [mapeamentos de atributo](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) para identificar o atributo correspondente. Esse valor deve estar presente no usuário ou grupo que você está tentando provisionar. 
* Examine a [API de SOAP do ServiceNow](https://docs.servicenow.com/bundle/newyork-application-development/page/integrate/web-services-apis/reference/r_DirectWebServiceAPIFunctions.html) para entender quaisquer requisitos ou limitações (por exemplo, formato para especificar o código do país para um usuário)
* Algumas implantações do ServiceNow exigem a permissão de intervalos de IP para o serviço de provisionamento do Azure AD. Os intervalos IP reservados para o serviço de provisionamento do Azure AD podem ser encontrados [aqui](https://www.microsoft.com/download/details.aspx?id=56519) em "AzureActiveDirectoryDomainServices".

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciando o provisionamento de conta de usuário para aplicativos empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como examinar os logs e obter relatórios sobre a atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)
