---
title: 'Tutorial: Configurar Lucidchart para fornecimento automático de utilizadores com Diretório Ativo Azure / Microsoft Docs'
description: Saiba como provisão e desprovisionamento automática de contas de utilizadores de Azure AD a Lucidchart.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/13/2020
ms.author: Zhchia
ms.openlocfilehash: 0aea258ac52e078c48255b39145193272aa65b25
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85367976"
---
# <a name="tutorial-configure-lucidchart-for-automatic-user-provisioning"></a>Tutorial: Configurar Lucidchart para o provisionamento automático do utilizador

Este tutorial descreve os passos que precisa de executar tanto no Diretório Lucidchart como no Azure Ative (Azure AD) para configurar o fornecimento automático do utilizador. Quando configurado, a Azure AD fornece automaticamente e desproteja utilizadores e grupos para [Lucidchart](https://www.lucidchart.com/user/117598685#/subscriptionLevel) usando o serviço de provisionamento Azure AD. Para obter detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte [automatizar o fornecimento e desprovisionamento de aplicações saaS com diretório Azure Ative.](../manage-apps/user-provisioning.md) 


## <a name="capabilities-supported"></a>Capacidades suportadas
> [!div class="checklist"]
> * Criar utilizadores em Lucidchart
> * Remova os utilizadores em Lucidchart quando já não necessitam de acesso
> * Mantenha os atributos do utilizador sincronizados entre AZure AD e Lucidchart
> * Grupos de provisão e membros do grupo em Lucidchart
> * [Único sinal de](https://docs.microsoft.com/azure/active-directory/saas-apps/lucidchart-tutorial) Lucidchart (recomendado)

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* [Um inquilino da AD AZure](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Uma conta de utilizador em Azure AD com [permissão](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) para configurar o provisionamento (por exemplo, Administrador de Aplicação, Administrador de Aplicação cloud, Proprietário de Aplicações ou Administrador Global). 
* Um inquilino LucidChart com o [plano da Enterprise](https://www.lucidchart.com/user/117598685#/subscriptionLevel) ou melhor habilitado.
* Uma conta de utilizador em LucidChart com permissões de Administração.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passo 1. Planeie a sua implantação de provisionamento
1. Saiba [como funciona o serviço de prestação de serviços.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)
2. Determinar quem estará no [âmbito do provisionamento](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Determine quais os dados a [mapear entre Azure AD e Lucidchart](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-lucidchart-to-support-provisioning-with-azure-ad"></a>Passo 2. Configure Lucidchart para apoiar o provisionamento com Azure AD

1. Faça login na [consola de administração Lucidchart](https://www.lucidchart.com). Navegue para a **Integração de Aplicativos team >.**

      ![Scim lucidchart](./media/lucidchart-provisioning-tutorial/team1.png)

2. Navegue até **SCIM**.

      ![Scim lucidchart](./media/lucidchart-provisioning-tutorial/scim.png)

3. Percorra para baixo para ver o **token do Portador** e **o URL base de Lucidchart.** Copie e guarde o **símbolo do Portador.** Este valor será introduzido no campo **Secret Token** * no separador Provisioning da sua aplicação LucidChart no portal Azure. 

      ![Símbolo de Lucidchart](./media/lucidchart-provisioning-tutorial/token.png)

## <a name="step-3-add-lucidchart-from-the-azure-ad-application-gallery"></a>Passo 3. Adicione Lucidchart da galeria de aplicações AD Azure

Adicione Lucidchart da galeria de aplicações AZure AD para começar a gerir o fornecimento à Lucidchart. Se já configurar lucidchart para SSO, pode utilizar a mesma aplicação. No entanto, recomenda-se que crie uma aplicação separada ao testar inicialmente a integração. Saiba mais sobre a adição de uma aplicação na galeria [aqui.](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passo 4. Definir quem estará no âmbito do provisionamento 

O serviço de prestação de Ad Azure permite-lhe atear âmbito a quem será a provisionado com base na atribuição à aplicação e ou com base em atributos do utilizador/grupo. Se optar por escolher o âmbito de aplicação de quem será aprovisionado na sua aplicação com base na atribuição, pode utilizar os [seguintes passos](../manage-apps/assign-user-or-group-access-portal.md) para atribuir utilizadores e grupos à aplicação. Se optar por escolher o âmbito de aplicação de quem será a provisionado apenas com base em atributos do utilizador ou grupo, pode utilizar um filtro de deteção como descrito [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Ao atribuir utilizadores e grupos a Lucidchart, deve selecionar outra função que não o **Acesso Predefinido**. Os utilizadores com a função De Acesso Predefinido estão excluídos do provisionamento e serão marcados como não efetivamente intitulados nos registos de provisionamento. Se a única função disponível na aplicação for a função de acesso predefinido, pode [atualizar o manifesto de aplicação](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) para adicionar funções adicionais. 

* Comece minúsculo. Teste com um pequeno conjunto de utilizadores e grupos antes de rolar para todos. Quando o âmbito de provisão é definido para utilizadores e grupos atribuídos, pode controlá-lo atribuindo um ou dois utilizadores ou grupos à aplicação. Quando o âmbito é definido para todos os utilizadores e grupos, pode especificar um [filtro de deteção baseado no atributo](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-lucidchart"></a>Passo 5. Configure o fornecimento automático de utilizadores ao Lucidchart 

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos no TestApp com base em atribuições de utilizador e/ou grupo em Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-lucidchart-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para a Lucidchart em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Lucidchart**.

    ![O link Lucidchart na lista de Aplicações](common/all-applications.png)

3. Selecione o **separador Provisioning.**

    ![Separador de provisionamento](common/provisioning.png)

4. Desa ajuste o **modo de provisionamento** para **automático**.

    ![Separador de provisionamento](common/provisioning-automatic.png)

5. Sob a secção **de Credenciais Admin,** insira o valor **do Token do Portador** recuperado anteriormente no campo Secret **Token.** Clique em **Testar a Ligação** para garantir que o Azure AD pode ligar-se ao Lucidchart. Se a ligação falhar, certifique-se de que a sua conta Lucidchart tem permissões de Administração e tente novamente.

      ![provisionamento](./media/Lucidchart-provisioning-tutorial/lucidchart1.png)

6. No campo **'Email' de Notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e selecione a **notificação enviar uma notificação por e-mail quando ocorrer uma falha** na caixa de verificação.

    ![E-mail de notificação](common/provisioning-notification-email.png)

7. Selecione **Guardar**.

8. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users para Lucidchart**.

9. Reveja os atributos do utilizador que são sincronizados de AD AD a Lucidchart na secção **De mapeamento de Atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas de utilizador em Lucidchart para operações de atualização. Se optar por alterar o [atributo de alvo correspondente,](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)terá de garantir que a API Lucidchart suporta utilizadores filtrantes com base nesse atributo. Selecione o botão **Guardar** para escoar quaisquer alterações.

   |Atributo|Tipo|
   |---|---|
   |userName|String|
   |e-mails[tipo eq "work"].value|String|
   |ativo|Booleano|
   |nome.dado Nome|String|
   |nome.famíliaName|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:organization|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|Referência|
   |urn:ietf:params:scim:schemas:extensão:lucidchart:1.0:User:canEdit|Booleano|

10. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Groups para Lucidchart**.

11. Reveja os atributos do grupo que são sincronizados de AD AD a Lucidchart na secção **De mapeamento de Atributos.** Os atributos selecionados como propriedades **de correspondência** são usados para combinar os grupos em Lucidchart para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

      |Atributo|Tipo|
      |---|---|
      |displayName|String|
      |membros|Referência|

12. Para configurar filtros de deteção, consulte as seguintes instruções fornecidas no tutorial do [filtro de escotagem](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para ativar o serviço de prestação de Ad Azure para a Lucidchart, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

    ![Estatuto de Provisionamento Toggled On](common/provisioning-toggle-on.png)

14. Defina os utilizadores e/ou grupos que deseja prestar à Lucidchart, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para a provisão, clique em **Guardar**.

    ![Configuração de provisionamento de poupança](common/provisioning-configuration-save.png)

Esta operação inicia o ciclo inicial de sincronização de todos os utilizadores e grupos definidos no **Âmbito** na secção **Definições.** O ciclo inicial demora mais tempo a ser efetuado do que os ciclos subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de fornecimento AZure AD esteja em funcionamento. 

## <a name="step-6-monitor-your-deployment"></a>Passo 6. Monitorizar a implementação
Depois de configurar o provisionamento, utilize os seguintes recursos para monitorizar a sua implantação:

1. Utilize os [registos de provisionamento](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) para determinar quais os utilizadores que foram a provisionados com sucesso ou sem sucesso
2. Verifique a [barra de progresso](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) para ver o estado do ciclo de provisionamento e quão perto está da sua conclusão
3. Se a configuração do provisionamento parecer estar num estado pouco saudável, a aplicação entrará em quarentena. Saiba mais sobre estados de quarentena [aqui.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)  

## <a name="change-log"></a>Change log

* 04/30/2020 - Suporte adicional para atributo de extensão da empresa e atributo personalizado "CanEdit" para os utilizadores.
* 06/15/2020 - A eliminação suave dos utilizadores está ativada (Atributo [ativo](https://tools.ietf.org/html/rfc7643) de suporte).

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do fornecimento de conta de utilizador para apps empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Próximos passos

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)