---
title: 'Tutorial: Configure ServiceNow para fornecimento automático de utilizadores com Diretório Ativo Azure / Microsoft Docs'
description: Saiba como provisão e desaconsebilização automática de contas de utilizador do Azure AD para o ServiceNow.
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
ms.openlocfilehash: 099216d5ffde48c404ae733c09b568b3859c25ad
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/07/2020
ms.locfileid: "87922205"
---
# <a name="tutorial-configure-servicenow-for-automatic-user-provisioning"></a>Tutorial: Configure ServiceNow para o fornecimento automático de utilizadores

Este tutorial descreve os passos necessários para realizar tanto no ServiceNow como no Azure Ative Directory (Azure AD) para configurar o fornecimento automático do utilizador. Quando configurado, a Azure AD fornece automaticamente e desresace os utilizadores e grupos de provisionamentos para [o ServiceNow](https://www.servicenow.com/) utilizando o serviço de provisionamento Azure AD. Para obter detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte [automatizar o fornecimento e desprovisionamento de aplicações saaS com diretório Azure Ative.](../app-provisioning/user-provisioning.md) 


## <a name="capabilities-supported"></a>Capacidades Suportadas
> [!div class="checklist"]
> * Criar utilizadores no ServiceNow
> * Remova os utilizadores no ServiceNow quando já não necessitam de acesso
> * Mantenha os atributos do utilizador sincronizados entre Azure AD e ServiceNow
> * Grupos de provisão e membros do grupo no ServiceNow
> * [Único sinal de serviço para](servicenow-tutorial.md) o ServiceNow (recomendado)

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* [Um inquilino da AD AZure](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Uma conta de utilizador em Azure AD com [permissão](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) para configurar o provisionamento (por exemplo, Administrador de Aplicação, Administrador de Aplicação cloud, Proprietário de Aplicações ou Administrador Global). 
* Um [caso de ServiçoNow](https://www.servicenow.com/) de Calgary ou superior
* Um [serviceNow express instância](https://www.servicenow.com/) de Helsínquia ou superior
* Uma conta de utilizador no ServiceNow com a função de administrador

## <a name="step-1-plan-your-provisioning-deployment"></a>Passo 1. Planeie a sua implantação de provisionamento
1. Saiba [como funciona o serviço de prestação de serviços.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)
2. Determinar quem estará no [âmbito do provisionamento](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Determine quais os dados a [mapear entre Azure AD e ServiceNow](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-servicenow-to-support-provisioning-with-azure-ad"></a>Passo 2. Configure o ServiceNow para apoiar o fornecimento com a Azure AD

1. Identifique o nome da sua instância ServiceNow. Pode encontrar o nome da instância no URL que utiliza para aceder ao ServiceNow. No exemplo abaixo, o nome da instância é dev35214.

   ![Instância de ServiçoNow](media/servicenow-provisioning-tutorial/servicenow_instance.png)

2. Obtenha credenciais para um administrador no ServiceNow. Navegue para o perfil do utilizador no ServiceNow e verifique se o utilizador tem a função de administrador. 

   ![Função de administração ServiceNow](media/servicenow-provisioning-tutorial/servicenow-admin-role.png)

3. Verifique se as seguintes definições estão **desativadas** no ServiceNow:

   1. Selecione **System Security**  >  **definições de segurança de segurança do**sistema  >  **Requerem autenticação básica para pedidos de SCHEMA de entrada**.
   2. Selecione **System Properties**  >  **Web Services**Require basic authorization for incoming SOAP  >  **requests**.
     
   > [!IMPORTANT]
   > Se estas definições *estiverem ativadas,* o motor de a provisionamento deixará de comunicar com o ServiceNow.

## <a name="step-3-add-servicenow-from-the-azure-ad-application-gallery"></a>Passo 3. Adicionar ServiçoNow da galeria de aplicações AZure AD

Adicione o ServiceNow da galeria de aplicações AZure AD para começar a gerir o fornecimento ao ServiceNow. Se tiver configurado o ServiceNow para SSO, pode utilizar a mesma aplicação. No entanto, recomenda-se que crie uma aplicação separada ao testar inicialmente a integração. Saiba mais sobre a adição de uma aplicação na galeria [aqui.](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passo 4: Definir quem estará no âmbito do provisionamento 

O serviço de prestação de Ad Azure permite-lhe atear âmbito a quem será a provisionado com base na atribuição à aplicação e ou com base em atributos do utilizador/grupo. Se optar por escolher o âmbito de aplicação de quem será aprovisionado na sua aplicação com base na atribuição, pode utilizar os [seguintes passos](../manage-apps/assign-user-or-group-access-portal.md) para atribuir utilizadores e grupos à aplicação. Se optar por escolher o âmbito de aplicação de quem será a provisionado apenas com base em atributos do utilizador ou grupo, pode utilizar um filtro de deteção como descrito [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Ao atribuir utilizadores e grupos ao ServiceNow, tem de selecionar outra função que não o **Acesso Predefinido**. Os utilizadores com a função De Acesso Predefinido estão excluídos do provisionamento e serão marcados como não efetivamente intitulados nos registos de provisionamento. Se a única função disponível na aplicação for a função de acesso predefinido, pode [atualizar o manifesto de aplicação](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) para adicionar funções adicionais. 

* Comece minúsculo. Teste com um pequeno conjunto de utilizadores e grupos antes de rolar para todos. Quando o âmbito de provisão é definido para utilizadores e grupos atribuídos, pode controlá-lo atribuindo um ou dois utilizadores ou grupos à aplicação. Quando o âmbito é definido para todos os utilizadores e grupos, pode especificar um [filtro de deteção baseado no atributo](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-servicenow"></a>Passo 5. Configure o fornecimento automático de utilizadores ao ServiceNow 

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos no TestApp com base em atribuições de utilizador e/ou grupo em Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-servicenow-in-azure-ad"></a>Para configurar o fornecimento automático do utilizador para o ServiceNow em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **ServiceNow**.

    ![O link ServiceNow na lista de Aplicações](common/all-applications.png)

3. Selecione o **separador Provisioning.**

    ![Separador de provisionamento](common/provisioning.png)

4. Desa ajuste o **modo de provisionamento** para **automático**.

    ![Separador de provisionamento](common/provisioning-automatic.png)

5. Na secção **Credenciais de Administração,** insira as suas credenciais de administração ServiceNow e o nome de utilizador. Clique em **'Testar' Ligação** para garantir que o Azure AD pode ligar-se ao ServiceNow. Se a ligação falhar, certifique-se de que a sua conta ServiceNow tem permissões de Administração e tente novamente.

    ![provisionamento](./media/servicenow-provisioning-tutorial/provisioning.png)

6. No campo **'Email' de Notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e selecione a **notificação enviar uma notificação por e-mail quando ocorrer uma falha** na caixa de verificação.

    ![E-mail de notificação](common/provisioning-notification-email.png)

7. Selecione **Guardar**.

8. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users to ServiceNow**.

9. Reveja os atributos do utilizador que são sincronizados de Azure AD para ServiceNow na secção **De mapeamento de atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas do utilizador no ServiceNow para operações de atualização. Se optar por alterar o [atributo de alvo correspondente,](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)terá de garantir que a API do ServiceNow suporta utilizadores filtrantes com base nesse atributo. Selecione o botão **Guardar** para escoar quaisquer alterações.

10. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Groups to ServiceNow**.

11. Reveja os atributos do grupo que são sincronizados de Azure AD para ServiceNow na secção **De mapeamento de atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para corresponder aos grupos no ServiceNow para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

12. Para configurar filtros de deteção, consulte as seguintes instruções fornecidas no tutorial do [filtro de escotagem](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para ativar o serviço de prestação de Ad Azure para o ServiceNow, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

    ![Estatuto de Provisionamento Toggled On](common/provisioning-toggle-on.png)

14. Defina os utilizadores e/ou grupos que deseja prestar ao ServiceNow, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para a provisão, clique em **Guardar**.

    ![Configuração de provisionamento de poupança](common/provisioning-configuration-save.png)

Esta operação inicia o ciclo inicial de sincronização de todos os utilizadores e grupos definidos no **Âmbito** na secção **Definições.** O ciclo inicial demora mais tempo a ser efetuado do que os ciclos subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de fornecimento AZure AD esteja em funcionamento. 

## <a name="step-6-monitor-your-deployment"></a>Passo 6. Monitorizar a implementação
Depois de configurar o provisionamento, utilize os seguintes recursos para monitorizar a sua implantação:

1. Utilize os [registos de provisionamento](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) para determinar quais os utilizadores que foram a provisionados com sucesso ou sem sucesso
2. Verifique a [barra de progresso](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) para ver o estado do ciclo de provisionamento e quão perto está da sua conclusão
3. Se a configuração do provisionamento parecer estar num estado pouco saudável, a aplicação entrará em quarentena. Saiba mais sobre estados de quarentena [aqui.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)  

## <a name="troubleshooting-tips"></a>Sugestões de Resolução de Problemas
* **InvalidLookupReference:** Ao ausi-lo determinados atributos, tais como Department and Location in ServiceNow, os valores já devem existir numa tabela de referência no ServiceNow. Por exemplo, você pode ter duas localizações (Seattle, Los Angeles) e três departamentos (Vendas, Finanças, Marketing) na tabela de **nomes** de mesa inserida no ServiceNow. Se tentar providenciar um utilizador onde o seu departamento é "Sales" e a localização é "Seattle", ele será a provisionado com sucesso. Se tentar fortear um utilizador com o departamento "Vendas" e a localização "LA" o utilizador não será provisionado. A localização LA deve ser adicionada à tabela de referência no ServiceNow ou o atributo do utilizador em Azure AD deve ser atualizado para corresponder ao formato no ServiceNow. 
* **Entrada PresidentesperpertyValueIsMisS:** Reveja os [mapeamentos do](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) seu atributo para identificar o atributo correspondente. Este valor deve estar presente no utilizador ou grupo que está a tentar obter. 
* Reveja a [API serviceNow SOAP](https://docs.servicenow.com/bundle/newyork-application-development/page/integrate/web-services-apis/reference/r_DirectWebServiceAPIFunctions.html) para compreender quaisquer requisitos ou limitações (por exemplo, formato para especificar código de país para um utilizador)
* Os pedidos de provisionamento são enviados por padrão para https://{your-instance-name}.service-now.com/{table-name} . Se você precisar de um URL de inquilino personalizado, você pode fornecer todo o URL inteiro no campo de nomes de exemplo.
* **ServiceNowInstanceInvalid** 
  
  `Details: Your ServiceNow instance name appears to be invalid.  Please provide a current ServiceNow administrative user name and          password along with the name of a valid ServiceNow instance.`                                                              

   Este erro indica um problema de comunicação com a instância ServiceNow. Verifique duas vezes se as seguintes definições são *desativadas* no ServiceNow:
   
   1. Selecione **System Security**  >  **definições de segurança de segurança do**sistema  >  **Requerem autenticação básica para pedidos de SCHEMA de entrada**.
   2. Selecione **System Properties**  >  **Web Services**Require basic authorization for incoming SOAP  >  **requests**.

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do fornecimento de conta de utilizador para apps empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
