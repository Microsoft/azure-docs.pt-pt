---
title: 'Tutorial: Configure Azure Databricks SCIM Connector para fornecimento automático de utilizadores com Diretório Ativo Azure  Microsoft Docs'
description: Aprenda a fornecer e desfornecer automaticamente contas de utilizadores de Azure AD para Azure Databricks SCIM Connector.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: b16eaf27-4bd1-4509-be2c-9a4f66b6badc
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2020
ms.author: Zhchia
ms.openlocfilehash: de60b4ea1b09998e84bab4d204e3c8c3bc8779a4
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/06/2020
ms.locfileid: "77050450"
---
# <a name="tutorial-configure-azure-databricks-scim-connector-for-automatic-user-provisioning"></a>Tutorial: Configure Azure Databricks SCIM Connector para fornecimento automático de utilizadores

Este tutorial descreve os passos necessários para executar tanto no Azure Databricks SCIM Connector como no Azure Ative Directory (Azure AD) para configurar o fornecimento automático de utilizadores. Quando configurado, a Azure AD disponibiliza e desativa automaticamente utilizadores e grupos para [o Conector SCIM](https://databricks.com/) de Tijolos de Dados Azure utilizando o serviço de provisionamento De AD Azure. Para detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte o fornecimento e o [desprovisionamento de utilizadores automate para aplicações SaaS com o Diretório Ativo Azure.](../manage-apps/user-provisioning.md) 


## <a name="capabilities-supported"></a>Capacidades suportadas
> [!div class="checklist"]
> * Criar utilizadores no Conector SCIM de Tijolos de Dados Azure
> * Remova os utilizadores do Conector SCIM de Tijolos de Dados Azure quando já não necessitam de acesso
> * Mantenha os atributos dos utilizadores sincronizados entre o Azure AD e o Azure Databricks SCIM Connector
> * Grupos de provisões e membros do grupo no Connector SCIM de Databricks Azure

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* [Um inquilino da AD Azure](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Uma conta de utilizador em Azure AD com [permissão](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) para configurar o provisionamento (por exemplo, Administrador de Aplicação, Administrador de Aplicação na Nuvem, Proprietário de Aplicações ou Administrador Global). 
* Uma conta Azure Databricks com permissões de administração.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passo 1. Planejar sua implantação de provisionamento
1. Saiba como funciona o serviço de [provisionamento.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)
2. Determinar quem estará no [âmbito do provisionamento.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)
3. Determine quais os dados a [mapear entre o Azure AD e o Azure Databricks SCIM Connector](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-azure-databricks-scim-connector-to-support-provisioning-with-azure-ad"></a>Passo 2. Configure Azure Databricks SCIM Connector para apoiar o provisionamento com a AD Azure

1. Para configurar o fornecimento de SCIM de Tijolos de Dados Azure, adicione-o como um recurso no seu inquilino do Diretório Ativo Azure e configure-o utilizando as definições abaixo.

    ![Configuração de Tijolos de Dados Azure](./media/azure-databricks-scim-provisioning-connector-provisioning-tutorial/setup.png)

2. Para gerar um token de acesso pessoal em Tijolos de Dados Azure, consulte [isto](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/authentication#token-management).

3. Copie o **Token.** Este valor será inserido no campo Secret Token no separador de fornecimento da sua aplicação de conector SCIM de tijolos de dados Azure no portal Azure.

## <a name="step-3-add-azure-databricks-scim-connector-from-the-azure-ad-application-gallery"></a>Passo 3. Adicione o Conector SCIM de Tijolos de Dados Azure da galeria de aplicações da AD Azure

Adicione o Conector SCIM de Tijolos Azure da galeria de aplicações Azure AD para começar a gerir o provisionamento ao Conector SCIM de Tijolos de Dados Azure. Se já configurar previamente o Conector SCIM de Tijolos De Dados Azure para SSO, pode utilizar a mesma aplicação. No entanto, é recomendável que você crie um aplicativo separado ao testar a integração inicialmente. Saiba mais sobre a adição de uma aplicação na galeria [aqui.](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passo 4. Definir quem estará no escopo para provisionamento 

O serviço de provisionamento do Azure AD permite o escopo que será provisionado com base na atribuição ao aplicativo e ou com base em atributos do usuário/grupo. Se optar por examinar quem será aprovisionado na sua app com base na atribuição, pode utilizar os [seguintes passos](../manage-apps/assign-user-or-group-access-portal.md) para atribuir utilizadores e grupos à aplicação. Se optar por examinar quem será aprovisionado apenas com base em atributos do utilizador ou do grupo, pode utilizar um filtro de deteção como descrito [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Ao atribuir utilizadores e grupos ao Conector SCIM de Tijolos de Dados Do Azure, deve selecionar uma função diferente do **Acesso Predefinido**. Os usuários com a função de acesso padrão são excluídos do provisionamento e serão marcados como não habilitados com eficiência nos logs de provisionamento. Se a única função disponível na aplicação for a função de acesso padrão, pode [atualizar o manifesto de aplicação](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) para adicionar funções adicionais. 

* Comece pequeno. Teste com um pequeno conjunto de usuários e grupos antes de distribuir para todos. Quando o escopo do provisionamento é definido como usuários e grupos atribuídos, você pode controlar isso atribuindo um ou dois usuários ou grupos ao aplicativo. Quando o âmbito é definido para todos os utilizadores e grupos, pode especificar um filtro de [deteção baseado em atributos](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-azure-databricks-scim-connector"></a>Passo 5. Configure o fornecimento automático de utilizadores ao Conector SCIM de Tijolos de Dados Do Azure 

Esta seção orienta você pelas etapas para configurar o serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no TestApp com base em atribuições de usuário e/ou grupo no Azure AD.

> [!NOTE]
> Para saber mais sobre o ponto final do SCIM da Azure Databricks, consulte [este](https://docs.databricks.com/dev-tools/api/latest/scim.html
).

### <a name="to-configure-automatic-user-provisioning-for-azure-databricks-scim-connector-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para o Conector SCIM de Tijolos de Dados Azure em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais**e, em seguida, selecione **Todas as aplicações**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Azure Databricks SCIM Connector**.

    ![O link do Conector SCIM de Tijolos De Dados Azure na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Provisioning.**

    ![Guia provisionamento](common/provisioning.png)

4. Detete o **modo de provisionamento** para **automático**.

    ![Guia provisionamento](common/provisioning-automatic.png)

5. No âmbito da secção **credenciais de administrador,** insere as suas credenciais de administrador e nome de utilizador do Azure Databricks SCIM Connector. Clique na **ligação de teste** para garantir que o Azure AD pode ligar-se ao Conector SCIM de tijolos de dados Azure. Se a ligação falhar, certifique-se de que a sua conta de Conector SCIM de Tijolos De Dados Azure tem permissões de administrador e tente novamente.

    ![provisionamento](./media/azure-databricks-scim-provisioning-connector-provisioning-tutorial/provisioning.png)

6. No campo de email de **notificação,** introduza o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de fornecimento e selecione a **notificação de e-mail enviar uma notificação de e-mail quando ocorrer uma falha** verificar a caixa.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Selecione **Guardar**.

8. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Users to Azure Databricks SCIM Connector**.

9. Reveja os atributos do utilizador que são sincronizados de Azure AD para Azure Databricks SCIM Connector na secção **Attribute-Mapping.** Os atributos selecionados como propriedades **Correspondentes** são usados para combinar as contas de utilizador no Conector SCIM de Datades Azure para operações de atualização. Se optar por alterar o [atributo-alvo correspondente,](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)terá de garantir que o Azure Databricks SCIM Connector API suporta filtrar os utilizadores com base nesse atributo. Selecione o botão **Guardar** para elegiro qualquer alteração.

   |Atributo|Tipo|
   |---|---|
   |userName|String|
   |displayName|String|
   |ativo|Booleano|

10. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Groups to Azure Databricks SCIM Connector**.

11. Reveja os atributos do grupo que são sincronizados de Azure AD a Azure Databricks SCIM Connector na secção **Attribute-Mapping.** Os atributos selecionados como propriedades **correspondentes** são usados para combinar os grupos em Azure Databricks SCIM Connector para operações de atualização. Selecione o botão **Guardar** para elegiro qualquer alteração.

     |Atributo|Tipo|
     |---|---|
     |displayName|String|
     |membros|Referência|

11. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Groups to Azure Databricks SCIM Connector**.

12. Para ativar o serviço de provisionamento de AD Azure para o Conector SCIM de Tijolos de Dados Azure, altere o Estado de **Provisionamento** para **ligar** na secção **Definições.**

    ![Status de provisionamento alternado em](common/provisioning-toggle-on.png)

13. Defina os utilizadores e/ou grupos que deseja fornecer ao Azure Databricks SCIM Connector, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Escopo de provisionamento](common/provisioning-scope.png)

14. Quando estiver pronto para fornecer, clique em **Guardar**.

    ![Salvando configuração de provisionamento](common/provisioning-configuration-save.png)

Esta operação inicia o ciclo inicial de sincronização de todos os utilizadores e grupos definidos no **Âmbito** na secção **Definições.** O ciclo inicial leva mais tempo para ser executado do que os ciclos subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure AD esteja em execução. 

## <a name="step-6-monitor-your-deployment"></a>Passo 6. Monitorizar a implementação
Depois de configurar o provisionamento, use os seguintes recursos para monitorar sua implantação:

* Utilize os registos de [provisionamento](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) para determinar quais os utilizadores que foram provisionados com sucesso ou sem sucesso
* Verifique a barra de [progresso](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) para ver o estado do ciclo de provisionamento e quão perto está da conclusão
* Se a configuração de provisionamento parecer estar em um estado não íntegro, o aplicativo entrará em quarentena. Saiba mais sobre estados de quarentena [aqui.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)  

## <a name="troubleshooting-tips"></a>Sugestões de resolução de problemas
* Os Databricks convertem sempre os seus valores de nome de utilizador para minúsculas quando poupam para o seu diretório, independentemente da capitalização que lhes enviamos através do SCIM.
* Atualmente, os pedidos da GET contra a API SCIM da Azure Databricks para os utilizadores são sensíveis a casos, por isso, se pedirmos USER@contoso.com, apresentará resultados 0 à medida que o armazenam como user@contoso.com.

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do provisionamento de conta de utilizador para aplicações empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)
