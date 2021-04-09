---
title: 'Tutorial: Configure PureCloud by Genesys para o fornecimento automático de utilizadores com Azure Ative Directory | Microsoft Docs'
description: Saiba como providenciar e desprovisionar automaticamente as contas de utilizadores do Azure AD para PureCloud by Genesys.
services: active-directory
author: Zhchia
writer: Zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/05/2020
ms.author: Zhchia
ms.openlocfilehash: bbb9b47e42ce195a98801ee08d177efd409c597e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96181669"
---
# <a name="tutorial-configure-purecloud-by-genesys-for-automatic-user-provisioning"></a>Tutorial: Configurar PureCloud by Genesys para fornecimento automático de utilizadores

Este tutorial descreve os passos que precisa de executar tanto no PureCloud da Genesys como no Azure Ative Directory (Azure AD) para configurar o fornecimento automático do utilizador. Quando configurado, a Azure AD fornece automaticamente e desprovisiona utilizadores e grupos para [PureCloud by Genesys](https://www.genesys.com) usando o serviço de provisionamento Azure AD. Para obter detalhes importantes sobre o que este serviço faz, como funciona e perguntas frequentes, veja [Automatizar o aprovisionamento e desaprovisionamento de utilizadores em aplicações SaaS no Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Capacidades suportadas
> [!div class="checklist"]
> * Criar utilizadores em PureCloud by Genesys
> * Remova os utilizadores em PureCloud pela Genesys quando já não necessitam de acesso
> * Mantenha os atributos do utilizador sincronizados entre Azure AD e PureCloud por Genesys
> * Grupos de provisão e membros do grupo no PureCloud by Genesys
> * [Único sinal de PureCloud](./purecloud-by-genesys-tutorial.md) por Genesys (recomendado)

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* [Um inquilino da AD AZure](../develop/quickstart-create-new-tenant.md) 
* Uma conta de utilizador no Azure AD com [permissão](../roles/permissions-reference.md) para configurar o aprovisionamento (por ex., Administrador de Aplicações, Administrador de Aplicações de Cloud, Proprietário da Aplicação ou Administrador Global). 
* Uma [organização](https://help.mypurecloud.com/?p=81984)PureCloud.
* Um Utilizador com [permissões](https://help.mypurecloud.com/?p=24360) para criar um Cliente Oauth.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passo 1. Planear a sua implementação de aprovisionamento
1. Saiba [como funciona o serviço de aprovisionamento](../app-provisioning/user-provisioning.md).
2. Determine quem vai estar no [âmbito do aprovisionamento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determine quais os dados a [mapear entre Azure AD e PureCloud por Genesys.](../app-provisioning/customize-application-attributes.md) 

## <a name="step-2-configure-purecloud-by-genesys-to-support-provisioning-with-azure-ad"></a>Passo 2. Configure PureCloud by Genesys para apoiar o provisionamento com Azure AD

1. Crie um [Cliente Oauth](https://help.mypurecloud.com/?p=188023) configurado na sua organização PureCloud.
2. Gere um símbolo [com o seu cliente de autor.](https://developer.mypurecloud.com/api/rest/authorization/use-client-credentials.html)
3. Se pretender obter automaticamente a adesão ao Grupo dentro do PureCloud, tem de [criar grupos](https://help.mypurecloud.com/?p=52397) no PureCloud com um nome idêntico ao do grupo em Azure AD.

## <a name="step-3-add-purecloud-by-genesys-from-the-azure-ad-application-gallery"></a>Passo 3. Adicione PureCloud by Genesys da galeria de aplicações AZure AD

Adicione PureCloud by Genesys da galeria de aplicações Azure AD para começar a gerir o fornecimento para PureCloud by Genesys. Se já configurar o PureCloud by Genesys para SSO, pode utilizar a mesma aplicação. No entanto, é recomendável criar uma aplicação separada ao testar a integração inicialmente. Saiba mais sobre como adicionar uma aplicação a partir da galeria [aqui](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passo 4: Determinar quem vai estar no âmbito do aprovisionamento 

O serviço de aprovisionamento do Azure AD permite-lhe determinar quem vai ser aprovisionado com base na atribuição à aplicação e/ou com base em atributos do utilizador/grupo. Se optar por determinar quem vai ser aprovisionado na sua aplicação com base na atribuição, pode utilizar os seguintes [passos](../manage-apps/assign-user-or-group-access-portal.md) para atribuir utilizadores e grupos à aplicação. Se escolher determinar quem vai ser aprovisionado com base apenas em atributos do utilizador ou grupo, pode utilizar um filtro de âmbito conforme descrito [aqui](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Ao atribuir utilizadores e grupos ao PureCloud by Genesys, tem de selecionar outra função que não o **Acesso Padrão.** Os utilizadores com a função Acesso Predefinido são excluídos do aprovisionamento e marcados como não autorizados de forma efetiva nos registos de aprovisionamento. Se a única função disponível na aplicação for a função de acesso predefinido, pode [atualizar o manifesto de aplicação](../develop/howto-add-app-roles-in-azure-ad-apps.md) para adicionar funções adicionais. 

* Comece pequeno. Teste com um pequeno conjunto de utilizadores e grupos antes de implementar para todos. Quando o âmbito do aprovisionamento está definido para os utilizadores e os grupos atribuídos, pode controlar isto ao atribuir um ou dois utilizadores ou grupos à aplicação. Quando o âmbito está definido para todos os utilizadores e grupos, pode especificar um [filtro de âmbito baseado em atributos](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-purecloud-by-genesys"></a>Passo 5. Configure o fornecimento automático de utilizadores ao PureCloud by Genesys 

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos no TestApp com base em atribuições de utilizador e/ou grupo em Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-purecloud-by-genesys-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para PureCloud by Genesys em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais** e, em seguida, **Todas as aplicações**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **PureCloud by Genesys**.

    ![O link PureCloud by Genesys na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Aprovisionamento**.

    ![Screenshot das opções De gestão com a opção Provisioning chamada.](common/provisioning.png)

4. Defina o **Modo de Aprovisionamento** como **Automático**.

    ![Screenshot da lista de retirada do modo de provisionamento com a opção Automática chamada.](common/provisioning-automatic.png)

5. Sob a secção **credenciais de administração,** insira o seu PureCloud by Genesys API URL e Oauth Token nos campos URL e **Secret Token** do **arrendatário,** respectivamente. O URL API será estruturado como `{{API Url}}/api/v2/scim/v2` , utilizando o URL API para a sua região PureCloud a partir do [PureCloud Developer Center](https://developer.mypurecloud.com/api/rest/index.html). Clique em **Test Connection** para garantir que o Azure AD pode ligar-se ao PureCloud by Genesys. Se a ligação falhar, certifique-se de que a sua conta PureCloud by Genesys tem permissões de administração e tente novamente.

    ![A screenshot mostra a caixa de diálogo de Admin Credentials, onde pode inserir o seu Inquilino U R L e Secret Token.](./media/purecloud-by-genesys-provisioning-tutorial/provisioning.png)

6. No campo **E-mail de Notificação**, introduza o endereço de e-mail de uma pessoa ou um grupo que deve receber as notificações de erro de aprovisionamento e marque a caixa de verificação **Enviar uma notificação de e-mail quando ocorre uma falha**.

    ![E-mail de Notificação](common/provisioning-notification-email.png)

7. Selecione **Guardar**.

8. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users to PureCloud by Genesys**.

9. Reveja os atributos do utilizador que são sincronizados de AZure AD a PureCloud por Genesys na secção **De mapeamento de atributos.** Os atributos selecionados como propriedades **de correspondência** são usados para combinar as contas de utilizador em PureCloud by Genesys para operações de atualização. Se optar por alterar o [atributo de alvo correspondente,](../app-provisioning/customize-application-attributes.md)terá de garantir que o PureCloud by Genesys API suporta utilizadores filtrantes com base nesse atributo. Selecione o botão **Guardar** para escoar quaisquer alterações.

     |Atributo|Tipo|
     |---|---|
     |userName|String|
     |active|Booleano|
     |displayName|String|
     |emails[type eq "work"].value|String|
     |título|String|
     |phoneNumbers[type eq "mobile"].value|String|
     |phoneNumbers[type eq "work"].value|String|
     |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String|
     |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|Referência|
     |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|String|
     

10. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Groups to PureCloud by Genesys**.

11. Reveja os atributos do grupo que são sincronizados de Azure AD a PureCloud por Genesys na secção **De mapeamento de atributos.** Os atributos selecionados como propriedades **de correspondência** são usados para combinar os grupos em PureCloud by Genesys para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações. PureCloud by Genesys não suporta a criação ou eliminação de grupos e apenas suporta a atualização de grupos.

      |Atributo|Tipo|
      |---|---|
      |displayName|String|
      |externalId|String|
      |membros|Referência|

12. Para configurar filtros de âmbito, veja as instruções seguintes disponibilizadas no [Tutorial de filtro de âmbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para ativar o serviço de fornecimento de AD Azure para PureCloud by Genesys, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

    ![Estado do Aprovisionamento Ativado](common/provisioning-toggle-on.png)

14. Defina os utilizadores e/ou grupos que deseja prestar ao PureCloud pela Genesys, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de Aprovisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para aprovisionar, clique em **Guardar**.

    ![Guardar Configuração de Aprovisionamento](common/provisioning-configuration-save.png)

Esta operação inicia o ciclo de sincronização inicial de todos os utilizadores e grupos definidos no **Âmbito** na secção **Definições**. O ciclo inicial leva mais tempo a ser executado do que os ciclos subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de aprovisionamento do Azure AD esteja em execução. 

## <a name="step-6-monitor-your-deployment"></a>Passo 6. Monitorizar a implementação
Depois de configurar o aprovisionamento, utilize os seguintes recursos para monitorizar a sua implementação:

* Utilize os [registos de aprovisionamento](../reports-monitoring/concept-provisioning-logs.md) para determinar quais os utilizadores que foram aprovisionados com ou sem êxito
* Verifique a [barra de progresso](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) para ver o estado do ciclo de aprovisionamento e quão próximo está da conclusão
* Se a configuração de aprovisionamento parecer estar num mau estado de funcionamento, a aplicação vai entrar em quarentena. Saiba mais sobre os estados de quarentena [aqui](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="change-log"></a>Change log

09/10 - Apoio adicional ao atributo da empresa "EmployeeNumber".

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento de contas de utilizador para Aplicações Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como analisar os registos e obter relatórios sobre a atividade de aprovisionamento](../app-provisioning/check-status-user-account-provisioning.md)