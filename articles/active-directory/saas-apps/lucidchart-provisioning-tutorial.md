---
title: 'Tutorial: Configure Lucidchart para fornecimento automático de utilizadores com Diretório Ativo Azure [ Microsoft Docs'
description: Aprenda a fornecer e desfornecer automaticamente contas de utilizadores de Azure AD para Lucidchart.
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
ms.openlocfilehash: 0c7c1f5f633554a88b74694ed2aeafcd30c13a89
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82690559"
---
# <a name="tutorial-configure-lucidchart-for-automatic-user-provisioning"></a>Tutorial: Configure Lucidchart para fornecimento automático de utilizadores

Este tutorial descreve os passos necessários para executar tanto em Lucidchart como azure Ative Directory (Azure AD) para configurar o fornecimento automático de utilizadores. Quando configurado, a AD Azure disponibiliza e desativa automaticamente utilizadores e grupos à [Lucidchart](https://www.lucidchart.com/user/117598685#/subscriptionLevel) utilizando o serviço de provisionamento de AD Azure. Para detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte o fornecimento e o [desprovisionamento de utilizadores automate para aplicações SaaS com o Diretório Ativo Azure.](../manage-apps/user-provisioning.md) 


## <a name="capabilities-supported"></a>Capacidades suportadas
> [!div class="checklist"]
> * Criar utilizadores em Lucidchart
> * Remova os utilizadores em Lucidchart quando já não necessitam de acesso
> * Mantenha os atributos dos utilizadores sincronizados entre a Azure AD e a Lucidchart
> * Grupos de provisões e membros do grupo na Lucidchart
> * [Inscrição única](https://docs.microsoft.com/azure/active-directory/saas-apps/lucidchart-tutorial) para Lucidchart (recomendado)

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* [Um inquilino da AD Azure](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Uma conta de utilizador em Azure AD com [permissão](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) para configurar o provisionamento (por exemplo, Administrador de Aplicação, Administrador de Aplicação na Nuvem, Proprietário de Aplicações ou Administrador Global). 
* Um inquilino LucidChart com o [plano Enterprise](https://www.lucidchart.com/user/117598685#/subscriptionLevel) ou melhor habilitado.
* Uma conta de utilizador em LucidChart com permissões de administrador.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passo 1. Planeie a sua implantação de provisionamento
1. Saiba como funciona o serviço de [provisionamento.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)
2. Determinar quem estará no [âmbito do provisionamento.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)
3. Determine que dados a [mapear entre AD Azure e Lucidchart](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-lucidchart-to-support-provisioning-with-azure-ad"></a>Passo 2. Configure lucidchart para apoiar o provisionamento com a AD Azure

1. Login para [a consola de administração Lucidchart](https://www.lucidchart.com). Navegue para team > Integração de **aplicações.**

      ![Lucidchart scim](./media/lucidchart-provisioning-tutorial/team1.png)

2. Navegar para **o SCIM.**

      ![Lucidchart scim](./media/lucidchart-provisioning-tutorial/scim.png)

3. Desça para ver o **token do Bearer** e o **URL base da Lucidchart**. Copie e guarde o **símbolo do Portador.** Este valor será inserido no campo **Secret Token** * no separador de provisionamento da sua aplicação LucidChart no portal Azure. 

      ![Símbolo de Lucidchart](./media/lucidchart-provisioning-tutorial/token.png)

## <a name="step-3-add-lucidchart-from-the-azure-ad-application-gallery"></a>Passo 3. Adicione Lucidchart da galeria de aplicações da AD Azure

Adicione lucidchart da galeria de aplicações da AD Azure para começar a gerir o provisionamento à Lucidchart. Se já configurar a Lucidchart para sSO, pode utilizar a mesma aplicação. No entanto, recomenda-se que crie uma aplicação separada ao testar a integração inicialmente. Saiba mais sobre a adição de uma aplicação na galeria [aqui.](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passo 4. Definir quem estará no âmbito do provisionamento 

O serviço de provisionamento de AD Azure permite-lhe examinar quem será provisionado com base na atribuição à aplicação e ou com base em atributos do utilizador/grupo. Se optar por examinar quem será aprovisionado na sua app com base na atribuição, pode utilizar os [seguintes passos](../manage-apps/assign-user-or-group-access-portal.md) para atribuir utilizadores e grupos à aplicação. Se optar por examinar quem será aprovisionado apenas com base em atributos do utilizador ou do grupo, pode utilizar um filtro de deteção como descrito [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Ao atribuir utilizadores e grupos à Lucidchart, deve selecionar uma função diferente do **Acesso Padrão**. Os utilizadores com a função de Acesso Predefinido estão excluídos do fornecimento e serão marcados como não tendo direito efetivamente nos registos de fornecimento. Se a única função disponível na aplicação for a função de acesso padrão, pode [atualizar o manifesto de aplicação](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) para adicionar funções adicionais. 

* Comece minúsculo. Teste com um pequeno conjunto de utilizadores e grupos antes de passar para todos. Quando o âmbito de fornecimento for definido para utilizadores e grupos atribuídos, pode controlá-lo atribuindo um ou dois utilizadores ou grupos à aplicação. Quando o âmbito é definido para todos os utilizadores e grupos, pode especificar um filtro de [deteção baseado em atributos](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-lucidchart"></a>Passo 5. Configure o fornecimento automático de utilizadores à Lucidchart 

Esta secção orienta-o através dos passos para configurar o serviço de provisionamento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos no TestApp com base em atribuições de utilizador e/ou grupo em Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-lucidchart-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para lucidchart em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais**e, em seguida, selecione **Todas as aplicações**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Lucidchart**.

    ![O link Lucidchart na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Provisioning.**

    ![Guia de provisionamento](common/provisioning.png)

4. Detete o **modo de provisionamento** para **automático**.

    ![Guia de provisionamento](common/provisioning-automatic.png)

5. Sob a secção **de Credenciais de Administrador,** insere o valor **token** do portador recuperado anteriormente no campo **Secret Token.** Clique na **ligação de teste** para garantir que o Azure AD pode ligar-se à Lucidchart. Se a ligação falhar, certifique-se de que a sua conta Lucidchart tem permissões de administrador e tente novamente.

      ![provisionamento](./media/Lucidchart-provisioning-tutorial/lucidchart1.png)

6. No campo de email de **notificação,** introduza o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de fornecimento e selecione a **notificação de e-mail enviar uma notificação de e-mail quando ocorrer uma falha** verificar a caixa.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Selecione **Guardar**.

8. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Users to Lucidchart**.

9. Reveja os atributos do utilizador que são sincronizados de Azure AD para Lucidchart na secção **Attribute-Mapping.** Os atributos selecionados como propriedades **Correspondentes** são usados para combinar as contas de utilizador em Lucidchart para operações de atualização. Se optar por alterar o [atributo-alvo correspondente,](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)terá de garantir que a API lucidchart suporta filtrar utilizadores com base nesse atributo. Selecione o botão **Guardar** para elegiro qualquer alteração.

   |Atributo|Tipo|
   |---|---|
   |userName|String|
   |e-mails[tipo eq "work"].valor|String|
   |ativo|Booleano|
   |nome.dadoNome|String|
   |nome.familyName|String|
   |urn:ietf:params:scim:schemas:extens:enterprise:2.0:User:department|String|
   |urn:ietf:params:scim:schemas:extens:enterprise:2.0:User:division|String|
   |urn:ietf:params:scim:schemas:extens:enterprise:2.0:User:costCenter|String|
   |urn:ietf:params:scim:schemas:extens:enterprise:2.0:User:organization|String|
   |urn:ietf:params:scim:schemas:extens:enterprise:2.0:Utilizador:empregadoNúmero|String|
   |urn:ietf:params:scim:schemas:extens:enterprise:2.0:User:manager|Referência|
   |urn:ietf:params:scim:schemas:extensão:lucidchart:1.0:Utilizador:canEdit|Booleano|

10. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Groups to Lucidchart**.

11. Reveja os atributos do grupo que são sincronizados de Azure AD para Lucidchart na secção **Attribute-Mapping.** Os atributos selecionados como propriedades **correspondentes** são usados para combinar os grupos em Lucidchart para operações de atualização. Selecione o botão **Guardar** para elegiro qualquer alteração.

      |Atributo|Tipo|
      |---|---|
      |displayName|String|
      |membros|Referência|

12. Para configurar filtros de deteção, consulte as seguintes instruções fornecidas no tutorial do [filtro Descodificação](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para ativar o serviço de provisionamento de AD Azure para lucidchart, altere o Estado de **Provisionamento** para **On** na secção **Definições.**

    ![Estatuto de provisionamento Alternado](common/provisioning-toggle-on.png)

14. Defina os utilizadores e/ou grupos que gostaria de fornecer à Lucidchart, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para fornecer, clique em **Guardar**.

    ![Configuração de fornecimento de poupança](common/provisioning-configuration-save.png)

Esta operação inicia o ciclo inicial de sincronização de todos os utilizadores e grupos definidos no **Âmbito** na secção **Definições.** O ciclo inicial demora mais tempo a realizar do que os ciclos subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento da AD Azure esteja em funcionamento. 

## <a name="step-6-monitor-your-deployment"></a>Passo 6. Monitorizar a implementação
Depois de configurar o fornecimento, utilize os seguintes recursos para monitorizar a sua implementação:

1. Utilize os registos de [provisionamento](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) para determinar quais os utilizadores que foram provisionados com sucesso ou sem sucesso
2. Verifique a barra de [progresso](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) para ver o estado do ciclo de provisionamento e quão perto está da conclusão
3. Se a configuração do fornecimento parecer estar num estado pouco saudável, a aplicação entrará em quarentena. Saiba mais sobre estados de quarentena [aqui.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)  

## <a name="change-log"></a>Change log

* 04/30/2020 - Suporte adicional para aatribuição de extensão da empresa e atributo personalizado "CanEdit" para os utilizadores.

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do provisionamento de conta de utilizador para aplicações empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)