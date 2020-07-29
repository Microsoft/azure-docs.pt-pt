---
title: 'Tutorial: Configure PureCloud by Genesys para fornecimento automático de utilizadores com Azure Ative Directory / Microsoft Docs'
description: Saiba como providenciar e desprovisionar automaticamente as contas de utilizadores do Azure AD para PureCloud by Genesys.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "77370678"
---
# <a name="tutorial-configure-purecloud-by-genesys-for-automatic-user-provisioning"></a>Tutorial: Configurar PureCloud by Genesys para fornecimento automático de utilizadores

Este tutorial descreve os passos que precisa de executar tanto no PureCloud da Genesys como no Azure Ative Directory (Azure AD) para configurar o fornecimento automático do utilizador. Quando configurado, a Azure AD fornece automaticamente e desprovisiona utilizadores e grupos para [PureCloud by Genesys](https://www.genesys.com) usando o serviço de provisionamento Azure AD. Para obter detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte [automatizar o fornecimento e desprovisionamento de aplicações saaS com diretório Azure Ative.](../manage-apps/user-provisioning.md) 


## <a name="capabilities-supported"></a>Capacidades suportadas
> [!div class="checklist"]
> * Criar utilizadores em PureCloud by Genesys
> * Remova os utilizadores em PureCloud pela Genesys quando já não necessitam de acesso
> * Mantenha os atributos do utilizador sincronizados entre Azure AD e PureCloud por Genesys
> * Grupos de provisão e membros do grupo no PureCloud by Genesys
> * [Único sinal de PureCloud](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-tutorial) por Genesys (recomendado)

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* [Um inquilino da AD AZure](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Uma conta de utilizador em Azure AD com [permissão](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) para configurar o provisionamento (por exemplo, Administrador de Aplicação, Administrador de Aplicação cloud, Proprietário de Aplicações ou Administrador Global). 
* Uma [organização](https://help.mypurecloud.com/?p=81984)PureCloud.
* Um Utilizador com [permissões](https://help.mypurecloud.com/?p=24360) para criar um Cliente Oauth.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passo 1. Planeie a sua implantação de provisionamento
1. Saiba [como funciona o serviço de prestação de serviços.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)
2. Determinar quem estará no [âmbito do provisionamento](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Determine quais os dados a [mapear entre Azure AD e PureCloud por Genesys.](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) 

## <a name="step-2-configure-purecloud-by-genesys-to-support-provisioning-with-azure-ad"></a>Passo 2. Configure PureCloud by Genesys para apoiar o provisionamento com Azure AD

1. Crie um [Cliente Oauth](https://help.mypurecloud.com/?p=188023) configurado na sua organização PureCloud.
2. Gere um símbolo [com o seu cliente de autor.](https://developer.mypurecloud.com/api/rest/authorization/use-client-credentials.html)
3. Se pretender obter automaticamente a adesão ao Grupo dentro do PureCloud, tem de [criar grupos](https://help.mypurecloud.com/?p=52397) no PureCloud com um nome idêntico ao do grupo em Azure AD.

## <a name="step-3-add-purecloud-by-genesys-from-the-azure-ad-application-gallery"></a>Passo 3. Adicione PureCloud by Genesys da galeria de aplicações AZure AD

Adicione PureCloud by Genesys da galeria de aplicações Azure AD para começar a gerir o fornecimento para PureCloud by Genesys. Se já configurar o PureCloud by Genesys para SSO, pode utilizar a mesma aplicação. No entanto, recomenda-se que crie uma aplicação separada ao testar inicialmente a integração. Saiba mais sobre a adição de uma aplicação na galeria [aqui.](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passo 4. Definir quem estará no âmbito do provisionamento 

O serviço de prestação de Ad Azure permite-lhe atear âmbito a quem será a provisionado com base na atribuição à aplicação e ou com base em atributos do utilizador/grupo. Se optar por escolher o âmbito de aplicação de quem será aprovisionado na sua aplicação com base na atribuição, pode utilizar os [seguintes passos](../manage-apps/assign-user-or-group-access-portal.md) para atribuir utilizadores e grupos à aplicação. Se optar por escolher o âmbito de aplicação de quem será a provisionado apenas com base em atributos do utilizador ou grupo, pode utilizar um filtro de deteção como descrito [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Ao atribuir utilizadores e grupos ao PureCloud by Genesys, tem de selecionar outra função que não o **Acesso Padrão.** Os utilizadores com a função De Acesso Predefinido estão excluídos do provisionamento e serão marcados como não efetivamente intitulados nos registos de provisionamento. Se a única função disponível na aplicação for a função de acesso predefinido, pode [atualizar o manifesto de aplicação](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) para adicionar funções adicionais. 

* Comece minúsculo. Teste com um pequeno conjunto de utilizadores e grupos antes de rolar para todos. Quando o âmbito de provisão é definido para utilizadores e grupos atribuídos, pode controlá-lo atribuindo um ou dois utilizadores ou grupos à aplicação. Quando o âmbito é definido para todos os utilizadores e grupos, pode especificar um [filtro de deteção baseado no atributo](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-purecloud-by-genesys"></a>Passo 5. Configure o fornecimento automático de utilizadores ao PureCloud by Genesys 

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos no TestApp com base em atribuições de utilizador e/ou grupo em Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-purecloud-by-genesys-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para PureCloud by Genesys em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **PureCloud by Genesys**.

    ![O link PureCloud by Genesys na lista de Aplicações](common/all-applications.png)

3. Selecione o **separador Provisioning.**

    ![Separador de provisionamento](common/provisioning.png)

4. Desa ajuste o **modo de provisionamento** para **automático**.

    ![Separador de provisionamento](common/provisioning-automatic.png)

5. Sob a secção **credenciais de administração,** insira o seu PureCloud by Genesys API URL e Oauth Token nos campos URL e **Secret Token** do **arrendatário,** respectivamente. O URL API será estruturado como `{{API Url}}/api/v2/scim/v2` , utilizando o URL API para a sua região PureCloud a partir do [PureCloud Developer Center](https://developer.mypurecloud.com/api/rest/index.html). Clique em **Test Connection** para garantir que o Azure AD pode ligar-se ao PureCloud by Genesys. Se a ligação falhar, certifique-se de que a sua conta PureCloud by Genesys tem permissões de administração e tente novamente.

    ![provisionamento](./media/purecloud-by-genesys-provisioning-tutorial/provisioning.png)

6. No campo **'Email' de Notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e selecione a **notificação enviar uma notificação por e-mail quando ocorrer uma falha** na caixa de verificação.

    ![E-mail de notificação](common/provisioning-notification-email.png)

7. Selecione **Guardar**.

8. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users to PureCloud by Genesys**.

9. Reveja os atributos do utilizador que são sincronizados de AZure AD a PureCloud por Genesys na secção **De mapeamento de atributos.** Os atributos selecionados como propriedades **de correspondência** são usados para combinar as contas de utilizador em PureCloud by Genesys para operações de atualização. Se optar por alterar o [atributo de alvo correspondente,](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)terá de garantir que o PureCloud by Genesys API suporta utilizadores filtrantes com base nesse atributo. Selecione o botão **Guardar** para escoar quaisquer alterações.

     |Atributo|Tipo|
     |---|---|
     |userName|String|
     |ativo|Booleano|
     |displayName|String|
     |e-mails[tipo eq "work"].value|String|
     |título|String|
     |números de telefone[tipo eq "mobile"].valor|String|
     |phoneNumbers[tipo eq "work"].value|String|
     |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String|
     |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|Referência|

10. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Groups to PureCloud by Genesys**.

11. Reveja os atributos do grupo que são sincronizados de Azure AD a PureCloud por Genesys na secção **De mapeamento de atributos.** Os atributos selecionados como propriedades **de correspondência** são usados para combinar os grupos em PureCloud by Genesys para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações. PureCloud by Genesys não suporta a criação ou eliminação de grupos e apenas suporta a atualização de grupos.

      |Atributo|Tipo|
      |---|---|
      |displayName|String|
      |externoId|String|
      |membros|Referência|

12. Para configurar filtros de deteção, consulte as seguintes instruções fornecidas no tutorial do [filtro de escotagem](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para ativar o serviço de fornecimento de AD Azure para PureCloud by Genesys, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

    ![Estatuto de Provisionamento Toggled On](common/provisioning-toggle-on.png)

14. Defina os utilizadores e/ou grupos que deseja prestar ao PureCloud pela Genesys, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para a provisão, clique em **Guardar**.

    ![Configuração de provisionamento de poupança](common/provisioning-configuration-save.png)

Esta operação inicia o ciclo inicial de sincronização de todos os utilizadores e grupos definidos no **Âmbito** na secção **Definições.** O ciclo inicial demora mais tempo a ser efetuado do que os ciclos subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de fornecimento AZure AD esteja em funcionamento. 

## <a name="step-6-monitor-your-deployment"></a>Passo 6. Monitorizar a implementação
Depois de configurar o provisionamento, utilize os seguintes recursos para monitorizar a sua implantação:

* Utilize os [registos de provisionamento](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) para determinar quais os utilizadores que foram a provisionados com sucesso ou sem sucesso
* Verifique a [barra de progresso](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) para ver o estado do ciclo de provisionamento e quão perto está da sua conclusão
* Se a configuração do provisionamento parecer estar num estado pouco saudável, a aplicação entrará em quarentena. Saiba mais sobre estados de quarentena [aqui.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do fornecimento de conta de utilizador para apps empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Próximos passos

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)
