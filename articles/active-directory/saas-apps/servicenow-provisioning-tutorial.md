---
title: 'Tutorial: Configurar o Serviço Agora para fornecimento automático de utilizadores com o Azure Ative Directory | Microsoft Docs'
description: Saiba como provisão e desprovisionar automaticamente as contas dos utilizadores do Azure AD para o ServiceNow.
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
ms.openlocfilehash: b3b62e7c16106fd9d94d4a3438331dab4ce8b6e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99539048"
---
# <a name="tutorial-configure-servicenow-for-automatic-user-provisioning"></a>Tutorial: Configure ServiceNow para o fornecimento automático de utilizadores

Este tutorial descreve os passos que executa tanto no ServiceNow como no Azure Ative Directory (Azure AD) para configurar o fornecimento automático do utilizador. Quando o Azure AD está configurado, fornece e desprovisiona automaticamente utilizadores e grupos para [o ServiceNow](https://www.servicenow.com/) utilizando o serviço de fornecimento AZure AD. 

Para obter detalhes importantes sobre o que este serviço faz, como funciona e perguntas frequentes, veja [Automatizar o aprovisionamento e desaprovisionamento de utilizadores em aplicações SaaS no Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Capacidades suportadas
> [!div class="checklist"]
> * Criar utilizadores no ServiceNow
> * Remova os utilizadores no ServiceNow quando já não precisam de acesso
> * Mantenha os atributos do utilizador sincronizados entre Azure AD e ServiceNow
> * Grupos de provisão e membros do grupo no ServiceNow
> * Permitir [um único acesso](servicenow-tutorial.md) ao ServiceNow (recomendado)

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* [Um inquilino da AD AZure](../develop/quickstart-create-new-tenant.md) 
* Uma conta de utilizador em Azure AD com [permissão](../roles/permissions-reference.md) para configurar o provisionamento (Administrador de Aplicação, Administrador de Aplicação cloud, Proprietário de Aplicações ou Administrador Global)
* Um [caso de ServiçoNow](https://www.servicenow.com/) de Calgary ou superior
* Um [serviceNow express instância](https://www.servicenow.com/) de Helsínquia ou superior
* Uma conta de utilizador no ServiceNow com a função de administrador

## <a name="step-1-plan-your-provisioning-deployment"></a>Passo 1: Planeie a sua implantação de provisionamento
1. Saiba [como funciona o serviço de aprovisionamento](../app-provisioning/user-provisioning.md).
2. Determine quem vai estar no [âmbito do aprovisionamento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determine quais os dados a [mapear entre Azure AD e ServiceNow](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-servicenow-to-support-provisioning-with-azure-ad"></a>Passo 2: Configure ServiceNow para apoiar o fornecimento com Azure AD

1. Identifique o nome da sua instância ServiceNow. Pode encontrar o nome da instância no URL que utiliza para aceder ao ServiceNow. No exemplo seguinte, o nome da instância é **dev35214**.

   ![Screenshot que mostra uma instância ServiceNow.](media/servicenow-provisioning-tutorial/servicenow-instance.png)

2. Obtenha credenciais para um administrador no ServiceNow. Vá ao perfil do utilizador no ServiceNow e verifique se o utilizador tem a função de administrador. 

   ![Screenshot que mostra um papel de administrador do ServiceNow.](media/servicenow-provisioning-tutorial/servicenow-admin-role.png)


## <a name="step-3-add-servicenow-from-the-azure-ad-application-gallery"></a>Passo 3: Adicione o ServiceNow da galeria de aplicações AZure AD

Adicione o ServiceNow da galeria de aplicações AZure AD para começar a gerir o fornecimento ao ServiceNow. Se tiver configurado o ServiceNow para uma única sincção (SSO), pode utilizar a mesma aplicação. No entanto, recomendamos que crie uma aplicação separada quando estiver a testar a integração. [Saiba mais sobre a adição de uma aplicação na galeria.](../manage-apps/add-application-portal.md) 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passo 4: Definir quem estará no âmbito do provisionamento 

O serviço de prestação de Ad Azure permite-lhe atear o âmbito de aplicação de quem será a provisionado com base na atribuição à aplicação, ou com base em atributos do utilizador ou grupo. Se optar por escolher quem será aprovisionado na sua app com base na atribuição, pode utilizar os [passos para atribuir utilizadores e grupos à aplicação.](../manage-apps/assign-user-or-group-access-portal.md) Se optar por escolher o âmbito de aplicação de quem será a provisionado apenas com base em atributos do utilizador ou grupo, pode [utilizar um filtro de deteção](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

Tenha estas dicas em mente:

* Ao atribuir utilizadores e grupos ao ServiceNow, tem de selecionar outra função que não o Acesso Predefinido. Os utilizadores com a função Acesso Predefinido são excluídos do aprovisionamento e marcados como não autorizados de forma efetiva nos registos de aprovisionamento. Se a única função disponível na aplicação for a função Acesso Predefinido, pode [atualizar o manifesto de aplicação](../develop/howto-add-app-roles-in-azure-ad-apps.md) para adicionar mais funções. 

* Comece pequeno. Teste com um pequeno conjunto de utilizadores e grupos antes de implementar para todos. Quando o âmbito de provisionamento é definido para utilizadores e grupos atribuídos, você pode controlá-lo atribuindo um ou dois utilizadores ou grupos à app. Quando o âmbito é definido para todos os utilizadores e grupos, pode especificar um [filtro de deteção baseado em atributos](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-servicenow"></a>Passo 5: Configurar o fornecimento automático do utilizador ao ServiceNow 

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores e grupos no TestApp. Pode basear a configuração nas atribuições de utilizador e grupo em Azure AD.

Para configurar o fornecimento automático do utilizador para o ServiceNow em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **aplicações da Empresa**  >  **Todas as aplicações**.

    ![Screenshot que mostra o painel de aplicações da Enterprise.](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **ServiceNow**.

    ![Screenshot que mostra uma lista de aplicações.](common/all-applications.png)

3. Selecione o separador **Aprovisionamento**.

    ![Screenshot das opções De gestão com a opção Provisioning chamada.](common/provisioning.png)

4. Desa ajuste **o modo de provisionamento** para **automático**.

    ![Screenshot da lista de abandono do modo de provisionamento com a opção Automática chamada.](common/provisioning-automatic.png)

5. Na secção **Credenciais de Administração,** insira as suas credenciais de administração ServiceNow e o nome de utilizador. Selecione **a Ligação de Teste** para garantir que o Azure AD pode ligar-se ao ServiceNow. Se a ligação falhar, certifique-se de que a sua conta ServiceNow tem permissões de administração e tente novamente.

    ![Screenshot que mostra a página de Fornecimento de Serviço, onde pode introduzir credenciais de administração.](./media/servicenow-provisioning-tutorial/servicenow-provisioning.png)

6. Na caixa de **e-mail de notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento. Em seguida, selecione a **notificação enviar uma notificação por e-mail quando ocorrer uma falha** na caixa de verificação.

    ![Screenshot que mostra caixas para e-mail de notificação.](common/provisioning-notification-email.png)

7. Selecione **Guardar**.

8. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users to ServiceNow**.

9. Reveja os atributos do utilizador que são sincronizados de Azure AD para ServiceNow na secção **De mapeamento de atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas do utilizador no ServiceNow para operações de atualização. 

   Se optar por alterar o [atributo de alvo correspondente,](../app-provisioning/customize-application-attributes.md)terá de garantir que a API do ServiceNow suporta utilizadores filtrantes com base nesse atributo. 
   
   Selecione o botão **Guardar** para escoar quaisquer alterações.

10. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Groups to ServiceNow**.

11. Reveja os atributos do grupo que são sincronizados de Azure AD para ServiceNow na secção **De mapeamento de atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para corresponder aos grupos no ServiceNow para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

12. Para configurar filtros de deteção, consulte as instruções no tutorial do [filtro de escotagem](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para ativar o serviço de prestação de Ad Azure para o ServiceNow, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

    ![Screenshot que mostra o Estado de Provisionamento ligado.](common/provisioning-toggle-on.png)

14. Defina os utilizadores e grupos que pretende prestar ao ServiceNow, escolhendo os valores pretendidos no **Âmbito** na secção **Definições.**

    ![Screenshot que mostra escolhas para o âmbito de provisionamento.](common/provisioning-scope.png)

15. Quando estiver pronto para a provisão, **selecione Save**.

    ![Screenshot do botão para guardar uma configuração de provisionamento.](common/provisioning-configuration-save.png)

Esta operação inicia o ciclo de sincronização inicial de todos os utilizadores e grupos definidos no **Âmbito** na secção **Definições**. O ciclo inicial demora mais tempo a ser efetua do que os ciclos subsequentes. Os ciclos subsequentes ocorrem a cada 40 minutos, desde que o serviço de fornecimento AZure AD esteja em execução. 

## <a name="step-6-monitor-your-deployment"></a>Passo 6: Monitorize a sua implantação
Depois de configurar o provisionamento, use os seguintes recursos para monitorizar a sua implantação:

- Utilize os [registos de provisionamento](../reports-monitoring/concept-provisioning-logs.md) para determinar quais os utilizadores que foram a provisionados com sucesso ou sem sucesso.
- Verifique a barra de [progresso](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) para ver o estado do ciclo de provisionamento e o quão perto está de ser concluído.
- Se a configuração de aprovisionamento parecer estar num mau estado de funcionamento, a aplicação vai entrar em quarentena. [Saiba mais sobre estados de quarentena.](../app-provisioning/application-provisioning-quarantine-status.md)  

## <a name="troubleshooting-tips"></a>Sugestões de resolução de problemas
* Quando está a atar determinados atributos (como **Departamento** e **Localização)** no ServiceNow, os valores já devem existir numa tabela de referência no ServiceNow. Se não o fizerem, terás um erro **de invalidLookupReference.** 

   Por exemplo, você pode ter duas localizações (Seattle, Los Angeles) e três departamentos (Vendas, Finanças, Marketing) em uma determinada tabela no ServiceNow. Se tentar provisionar um utilizador cujo departamento é "Sales" e cuja localização é "Seattle", esse utilizador será a provisionado com sucesso. Se tentar provisionar um utilizador cujo departamento é "Sales" e cuja localização é "LA", o utilizador não será provisionado. A localização "LA" deve ser adicionada à tabela de referência no ServiceNow, ou o atributo do utilizador em Azure AD deve ser atualizado para corresponder ao formato no ServiceNow. 
* Se obtém um erro **de Entrada PropertyValueIsMisMisSing,** reveja os [mapeamentos do](../app-provisioning/customize-application-attributes.md) seu atributo para identificar o atributo correspondente. Este valor deve estar presente no utilizador ou grupo que está a tentar obter. 
* Para compreender quaisquer requisitos ou limitações (por exemplo, o formato para especificar um código de país para um utilizador), reveja a [API de SABONETE ServiceNow](https://docs.servicenow.com/bundle/newyork-application-development/page/integrate/web-services-apis/reference/r_DirectWebServiceAPIFunctions.html).
* Os pedidos de provisionamento são enviados por padrão para https://{your-instance-name}.service-now.com/{table-name}. Se você precisar de um URL de inquilino personalizado, você pode fornecer todo o URL inteiro como o nome de instância.
* O erro **ServiceNowInstanceInvalid** indica um problema de comunicação com a instância ServiceNow. Aqui está o texto do erro:
  
  `Details: Your ServiceNow instance name appears to be invalid.  Please provide a current ServiceNow administrative user name and          password along with the name of a valid ServiceNow instance.`                                                              

  Se tiver problemas de ligação de teste, tente selecionar **Não** para as seguintes definições no ServiceNow:
   
  - Segurança do **Sistema**  >  Configurações de **alta segurança**  >  **Requera autenticação básica para pedidos de SCHEMA**
  - Propriedades do **sistema**  >  **Serviços**  >  Web **Requerem autorização básica para pedidos de SOAP que chegam**

     ![Screenshot que mostra a opção para autorizar pedidos de SOAP.](media/servicenow-provisioning-tutorial/servicenow-webservice.png)

  Se ainda não conseguir resolver o seu problema, contacte o suporte do ServiceNow e peça-lhes que liguem a depuração SOAP para ajudar a resolver problemas. 

* O serviço de prestação de AD Azure opera atualmente ao abrigo de [gamas IP específicas](../app-provisioning/use-scim-to-provision-users-and-groups.md#ip-ranges). Se necessário, pode restringir outras gamas de IP e adicionar estes intervalos IP específicos à lista de admissões. Esta técnica permitirá o fluxo de tráfego do serviço de fornecimento Azure AD para a sua aplicação.

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do provisionamento de conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que são acesso a aplicações e um único acesso com o Azure Ative Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como analisar os registos e obter relatórios sobre a atividade de aprovisionamento](../app-provisioning/check-status-user-account-provisioning.md)
