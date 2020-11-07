---
title: 'Tutorial: Configure ServiceNow para fornecimento automático de utilizadores com Diretório Ativo Azure / Microsoft Docs'
description: Saiba como provisão e desaconsebilização automática de contas de utilizador do Azure AD para o ServiceNow.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/10/2019
ms.author: jeedes
ms.openlocfilehash: 1d6213d49c98f5e09f22e7310183315800d0c6f6
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359787"
---
# <a name="tutorial-configure-servicenow-for-automatic-user-provisioning"></a>Tutorial: Configure ServiceNow para o fornecimento automático de utilizadores

Este tutorial descreve os passos necessários para realizar tanto no ServiceNow como no Azure Ative Directory (Azure AD) para configurar o fornecimento automático do utilizador. Quando configurado, a Azure AD fornece automaticamente e desresace os utilizadores e grupos de provisionamentos para [o ServiceNow](https://www.servicenow.com/) utilizando o serviço de provisionamento Azure AD. Para obter detalhes importantes sobre o que este serviço faz, como funciona e perguntas frequentes, veja [Automatizar o aprovisionamento e desaprovisionamento de utilizadores em aplicações SaaS no Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Capacidades Suportadas
> [!div class="checklist"]
> * Criar utilizadores no ServiceNow
> * Remova os utilizadores no ServiceNow quando já não necessitam de acesso
> * Mantenha os atributos do utilizador sincronizados entre Azure AD e ServiceNow
> * Grupos de provisão e membros do grupo no ServiceNow
> * [Único sinal de serviço para](servicenow-tutorial.md) o ServiceNow (recomendado)

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* [Um inquilino da AD AZure](../develop/quickstart-create-new-tenant.md) 
* Uma conta de utilizador no Azure AD com [permissão](../users-groups-roles/directory-assign-admin-roles.md) para configurar o aprovisionamento (por ex., Administrador de Aplicações, Administrador de Aplicações de Cloud, Proprietário da Aplicação ou Administrador Global). 
* Um [caso de ServiçoNow](https://www.servicenow.com/) de Calgary ou superior
* Um [serviceNow express instância](https://www.servicenow.com/) de Helsínquia ou superior
* Uma conta de utilizador no ServiceNow com a função de administrador

## <a name="step-1-plan-your-provisioning-deployment"></a>Passo 1. Planear a sua implementação de aprovisionamento
1. Saiba [como funciona o serviço de aprovisionamento](../app-provisioning/user-provisioning.md).
2. Determine quem vai estar no [âmbito do aprovisionamento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determine quais os dados a [mapear entre Azure AD e ServiceNow](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-servicenow-to-support-provisioning-with-azure-ad"></a>Passo 2. Configure o ServiceNow para apoiar o fornecimento com a Azure AD

1. Identifique o nome da sua instância ServiceNow. Pode encontrar o nome da instância no URL que utiliza para aceder ao ServiceNow. No exemplo abaixo, o nome da instância é dev35214.

   ![Instância de ServiçoNow](media/servicenow-provisioning-tutorial/servicenow_instance.png)

2. Obtenha credenciais para um administrador no ServiceNow. Navegue para o perfil do utilizador no ServiceNow e verifique se o utilizador tem a função de administrador. 

   ![Função de administração ServiceNow](media/servicenow-provisioning-tutorial/servicenow-admin-role.png)

3. Verifique se as seguintes definições estão **desativadas** no ServiceNow:

   1. Selecione **System Security**  >  **definições de segurança de segurança do** sistema  >  **Requerem autenticação básica para pedidos de SCHEMA de entrada**.
   2. Selecione **System Properties**  >  **Web Services** Require basic authorization for incoming SOAP  >  **requests**.
     
   > [!IMPORTANT]
   > Se estas definições *estiverem ativadas,* o motor de a provisionamento deixará de comunicar com o ServiceNow.

## <a name="step-3-add-servicenow-from-the-azure-ad-application-gallery"></a>Passo 3. Adicionar ServiçoNow da galeria de aplicações AZure AD

Adicione o ServiceNow da galeria de aplicações AZure AD para começar a gerir o fornecimento ao ServiceNow. Se tiver configurado o ServiceNow para SSO, pode utilizar a mesma aplicação. No entanto, é recomendável criar uma aplicação separada ao testar a integração inicialmente. Saiba mais sobre como adicionar uma aplicação a partir da galeria [aqui](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passo 4: Determinar quem vai estar no âmbito do aprovisionamento 

O serviço de aprovisionamento do Azure AD permite-lhe determinar quem vai ser aprovisionado com base na atribuição à aplicação e/ou com base em atributos do utilizador/grupo. Se optar por determinar quem vai ser aprovisionado na sua aplicação com base na atribuição, pode utilizar os seguintes [passos](../manage-apps/assign-user-or-group-access-portal.md) para atribuir utilizadores e grupos à aplicação. Se escolher determinar quem vai ser aprovisionado com base apenas em atributos do utilizador ou grupo, pode utilizar um filtro de âmbito conforme descrito [aqui](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Ao atribuir utilizadores e grupos ao ServiceNow, tem de selecionar outra função que não o **Acesso Predefinido**. Os utilizadores com a função Acesso Predefinido são excluídos do aprovisionamento e marcados como não autorizados de forma efetiva nos registos de aprovisionamento. Se a única função disponível na aplicação for a função de acesso predefinido, pode [atualizar o manifesto de aplicação](../develop/howto-add-app-roles-in-azure-ad-apps.md) para adicionar funções adicionais. 

* Comece pequeno. Teste com um pequeno conjunto de utilizadores e grupos antes de implementar para todos. Quando o âmbito do aprovisionamento está definido para os utilizadores e os grupos atribuídos, pode controlar isto ao atribuir um ou dois utilizadores ou grupos à aplicação. Quando o âmbito está definido para todos os utilizadores e grupos, pode especificar um [filtro de âmbito baseado em atributos](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-servicenow"></a>Passo 5. Configure o fornecimento automático de utilizadores ao ServiceNow 

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos no TestApp com base em atribuições de utilizador e/ou grupo em Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-servicenow-in-azure-ad"></a>Para configurar o fornecimento automático do utilizador para o ServiceNow em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais** e, em seguida, **Todas as aplicações**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **ServiceNow**.

    ![O link ServiceNow na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Aprovisionamento**.

    ![Screenshot das opções De gestão com a opção Provisioning chamada.](common/provisioning.png)

4. Defina o **Modo de Aprovisionamento** como **Automático**.

    ![Screenshot da lista de retirada do modo de provisionamento com a opção Automática chamada.](common/provisioning-automatic.png)

5. Na secção **Credenciais de Administração,** insira as suas credenciais de administração ServiceNow e o nome de utilizador. Clique em **'Testar' Ligação** para garantir que o Azure AD pode ligar-se ao ServiceNow. Se a ligação falhar, certifique-se de que a sua conta ServiceNow tem permissões de Administração e tente novamente.

    ![A screenshot mostra a página de Fornecimento de Serviço, onde pode introduzir Credenciais de Administrador.](./media/servicenow-provisioning-tutorial/provisioning.png)

6. No campo **E-mail de Notificação** , introduza o endereço de e-mail de uma pessoa ou um grupo que deve receber as notificações de erro de aprovisionamento e marque a caixa de verificação **Enviar uma notificação de e-mail quando ocorre uma falha**.

    ![E-mail de Notificação](common/provisioning-notification-email.png)

7. Selecione **Guardar**.

8. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users to ServiceNow**.

9. Reveja os atributos do utilizador que são sincronizados de Azure AD para ServiceNow na secção **De mapeamento de atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas do utilizador no ServiceNow para operações de atualização. Se optar por alterar o [atributo de alvo correspondente,](../app-provisioning/customize-application-attributes.md)terá de garantir que a API do ServiceNow suporta utilizadores filtrantes com base nesse atributo. Selecione o botão **Guardar** para escoar quaisquer alterações.

10. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Groups to ServiceNow**.

11. Reveja os atributos do grupo que são sincronizados de Azure AD para ServiceNow na secção **De mapeamento de atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para corresponder aos grupos no ServiceNow para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

12. Para configurar filtros de âmbito, veja as instruções seguintes disponibilizadas no [Tutorial de filtro de âmbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para ativar o serviço de prestação de Ad Azure para o ServiceNow, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

    ![Estado do Aprovisionamento Ativado](common/provisioning-toggle-on.png)

14. Defina os utilizadores e/ou grupos que deseja prestar ao ServiceNow, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de Aprovisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para aprovisionar, clique em **Guardar**.

    ![Guardar Configuração de Aprovisionamento](common/provisioning-configuration-save.png)

Esta operação inicia o ciclo de sincronização inicial de todos os utilizadores e grupos definidos no **Âmbito** na secção **Definições**. O ciclo inicial leva mais tempo a ser executado do que os ciclos subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de aprovisionamento do Azure AD esteja em execução. 

## <a name="step-6-monitor-your-deployment"></a>Passo 6. Monitorizar a implementação
Depois de configurar o aprovisionamento, utilize os seguintes recursos para monitorizar a sua implementação:

1. Utilize os [registos de aprovisionamento](../reports-monitoring/concept-provisioning-logs.md) para determinar quais os utilizadores que foram aprovisionados com ou sem êxito
2. Verifique a [barra de progresso](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) para ver o estado do ciclo de aprovisionamento e quão próximo está da conclusão
3. Se a configuração de aprovisionamento parecer estar num mau estado de funcionamento, a aplicação vai entrar em quarentena. Saiba mais sobre os estados de quarentena [aqui](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="troubleshooting-tips"></a>Sugestões de Resolução de Problemas
* **InvalidLookupReference:** Ao ausi-lo determinados atributos, tais como Department and Location in ServiceNow, os valores já devem existir numa tabela de referência no ServiceNow. Por exemplo, você pode ter duas localizações (Seattle, Los Angeles) e três departamentos (Vendas, Finanças, Marketing) na tabela de **nomes** de mesa inserida no ServiceNow. Se tentar providenciar um utilizador onde o seu departamento é "Sales" e a localização é "Seattle", ele será a provisionado com sucesso. Se tentar fortear um utilizador com o departamento "Vendas" e a localização "LA" o utilizador não será provisionado. A localização LA deve ser adicionada à tabela de referência no ServiceNow ou o atributo do utilizador em Azure AD deve ser atualizado para corresponder ao formato no ServiceNow. 
* **Entrada PresidentesperpertyValueIsMisS:** Reveja os [mapeamentos do](../app-provisioning/customize-application-attributes.md) seu atributo para identificar o atributo correspondente. Este valor deve estar presente no utilizador ou grupo que está a tentar obter. 
* Reveja a [API serviceNow SOAP](https://docs.servicenow.com/bundle/newyork-application-development/page/integrate/web-services-apis/reference/r_DirectWebServiceAPIFunctions.html) para compreender quaisquer requisitos ou limitações (por exemplo, formato para especificar código de país para um utilizador)
* Os pedidos de provisionamento são enviados por padrão para https://{your-instance-name}.service-now.com/{table-name} . Se você precisar de um URL de inquilino personalizado, você pode fornecer todo o URL inteiro no campo de nomes de exemplo.
* **ServiceNowInstanceInvalid** 
  
  `Details: Your ServiceNow instance name appears to be invalid.  Please provide a current ServiceNow administrative user name and          password along with the name of a valid ServiceNow instance.`                                                              

   Este erro indica um problema de comunicação com a instância ServiceNow. Verifique duas vezes se as seguintes definições são *desativadas* no ServiceNow:
   
   1. Selecione **System Security**  >  **definições de segurança de segurança do** sistema  >  **Requerem autenticação básica para pedidos de SCHEMA de entrada**.
   2. Selecione **System Properties**  >  **Web Services** Require basic authorization for incoming SOAP  >  **requests**.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento de contas de utilizador para Aplicações Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como analisar os registos e obter relatórios sobre a atividade de aprovisionamento](../app-provisioning/check-status-user-account-provisioning.md)