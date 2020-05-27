---
title: 'Tutorial: Configure Hootsuite para fornecimento automático de utilizadores com Diretório Ativo Azure [ Microsoft Docs'
description: Aprenda a fornecer e desfornecer automaticamente contas de utilizador de Azure AD para Hootsuite.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: fbbee24e-272f-4fa6-819c-10c548bf0215
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/15/2020
ms.author: Zhchia
ms.openlocfilehash: 1adbb0961ab610db936107f2fff210f4acecf2ed
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83883101"
---
# <a name="tutorial-configure-hootsuite-for-automatic-user-provisioning"></a>Tutorial: Configure Hootsuite para fornecimento automático de utilizadores

Este tutorial descreve os passos necessários para executar tanto no Hootsuite como no Azure Ative Directory (Azure AD) para configurar o fornecimento automático de utilizadores. Quando configurado, a Azure AD disponibiliza e desativa automaticamente utilizadores e grupos para [a Hootsuite](https://hootsuite.com/) utilizando o serviço de provisionamento de AD Azure. Para detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte o fornecimento e o [desprovisionamento de utilizadores automate para aplicações SaaS com o Diretório Ativo Azure.](../manage-apps/user-provisioning.md)

## <a name="capabilities-supported"></a>Capacidades suportadas
> [!div class="checklist"]
> * Criar utilizadores em Hootsuite
> * Remova os utilizadores em Hootsuite quando já não necessitam de acesso
> * Mantenha os atributos do utilizador sincronizados entre a Azure AD e a Hootsuite
> * Grupos de provisão e membros do grupo na Hootsuite
> * [Inscrição única](https://docs.microsoft.com/azure/active-directory/saas-apps/hootsuite-tutorial) na Hootsuite (recomendado)

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* [Um inquilino da AD Azure](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Uma conta de utilizador em Azure AD com [permissão](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) para configurar o provisionamento (por exemplo, Administrador de Aplicação, Administrador de Aplicação na Nuvem, Proprietário de Aplicações ou Administrador Global). 
* Uma conta de utilizador com [a Hootsuite](http://www.hootsuite.com/) que tem permissões **de Membro sinuoso** na organização.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passo 1. Planeie a sua implantação de provisionamento
1. Saiba como funciona o serviço de [provisionamento.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)
2. Determinar quem estará no [âmbito do provisionamento.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)
3. Determine quais os dados a [mapear entre a Azure AD e a Hootsuite](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-hootsuite-to-support-provisioning-with-azure-ad"></a>Passo 2. Configure Hootsuite para apoiar o provisionamento com a Azure AD

Procure dev.support@hootsuite.com obter um símbolo secreto duradouro que será necessário em etapas posteriores. 

## <a name="step-3-add-hootsuite-from-the-azure-ad-application-gallery"></a>Passo 3. Adicione hootsuite da galeria de aplicações da AD Azure

Adicione a Hootsuite da galeria de aplicações Azure AD para começar a gerir o provisionamento à Hootsuite. Se já tiver configurado o Hootsuite para soo, pode utilizar a mesma aplicação. No entanto, recomenda-se que crie uma aplicação separada ao testar a integração inicialmente. Saiba mais sobre a adição de uma aplicação na galeria [aqui.](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passo 4. Definir quem estará no âmbito do provisionamento 

O serviço de provisionamento de AD Azure permite-lhe examinar quem será provisionado com base na atribuição à aplicação e ou com base em atributos do utilizador/grupo. Se optar por examinar quem será aprovisionado na sua app com base na atribuição, pode utilizar os [seguintes passos](../manage-apps/assign-user-or-group-access-portal.md) para atribuir utilizadores e grupos à aplicação. Se optar por examinar quem será aprovisionado apenas com base em atributos do utilizador ou do grupo, pode utilizar um filtro de deteção como descrito [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Ao atribuir utilizadores e grupos à Hootsuite, deve selecionar uma função diferente do **Acesso Padrão**. Os utilizadores com a função de Acesso Predefinido estão excluídos do fornecimento e serão marcados como não tendo direito efetivamente nos registos de fornecimento. Se a única função disponível na aplicação for a função de acesso padrão, pode [atualizar o manifesto de aplicação](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) para adicionar funções adicionais. 

* Comece minúsculo. Teste com um pequeno conjunto de utilizadores e grupos antes de passar para todos. Quando o âmbito de fornecimento for definido para utilizadores e grupos atribuídos, pode controlá-lo atribuindo um ou dois utilizadores ou grupos à aplicação. Quando o âmbito é definido para todos os utilizadores e grupos, pode especificar um filtro de [deteção baseado em atributos](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-hootsuite"></a>Passo 5. Configure o fornecimento automático de utilizadores à Hootsuite 

Esta secção orienta-o através dos passos para configurar o serviço de provisionamento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos no TestApp com base em atribuições de utilizador e/ou grupo em Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-hootsuite-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para a Hootsuite em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais**e, em seguida, selecione **Todas as aplicações**.

    ![Lâmina de aplicações da empresa](./media/hootsuite-provisioning-tutorial/enterprise-applications.png)

    ![Painel Todas as aplicações](./media/hootsuite-provisioning-tutorial/all-applications.png)

2. Na lista de aplicações, selecione **Hootsuite**.

    ![O link Hootsuite na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Provisioning.** Clique em **Iniciar**.

    ![Guia de provisionamento](common/provisioning.png)

    ![Começar a lâmina](./media/hootsuite-provisioning-tutorial/get-started.png)

4. Detete o **modo de provisionamento** para **automático**.

    ![Guia de provisionamento](common/provisioning-automatic.png)

5. Sob a secção **de Credenciais de Administrador,** entrada `https://platform.hootsuite.com/scim/v2` em URL de Inquilino. Insera o valor secreto de longo prazo recuperado anteriormente no **passo 2**. Clique em **Ligação de Teste** para garantir que o Azure AD pode ligar-se à Hootsuite. Se a ligação falhar, certifique-se de que a sua conta Hootsuite tem permissões de administração e tente novamente.

    ![provisionamento](./media/hootsuite-provisioning-tutorial/provisioning.png)

6. No campo de email de **notificação,** introduza o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de fornecimento e selecione a **notificação de e-mail enviar uma notificação de e-mail quando ocorrer uma falha** verificar a caixa.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Selecione **Guardar**.

8. Na secção **Mapeamentos,** selecione **Provision Azure Ative Directory Users**.

9. Reveja os atributos do utilizador que são sincronizados de Azure AD para Hootsuite na secção **Attribute-Mapping.** Os atributos selecionados como propriedades **Correspondentes** são usados para combinar as contas de utilizador em Hootsuite para operações de atualização. Se optar por alterar o [atributo-alvo correspondente,](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)terá de garantir que a API da Hootsuite suporta filtrar utilizadores com base nesse atributo. Selecione o botão **Guardar** para elegiro qualquer alteração.

   |Atributo|Tipo|
   |---|---|
   |userName|Cadeia|
   |e-mails[tipo eq "work"].valor|Cadeia|
   |ativo|Booleano|
   |displayName|Cadeia|
   |língua preferida|Cadeia|
   |fuso horário|Cadeia|
   |urn:ietf:params:scim:schemas:extensão:Hootsuite:2.0:User:organizationIds|Cadeia|
   |urn:ietf:params:scim:schemas:extensão:Hootsuite:2.0:User:teamIds|Cadeia|

10. Para ativar o serviço de provisionamento de AD Azure para a Hootsuite, altere o Estado de **Provisionamento** para **On** na secção **Definições.**

    ![Estatuto de provisionamento Alternado](common/provisioning-toggle-on.png)

11. Defina os utilizadores e/ou grupos que deseja fornecer à Hootsuite, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de provisionamento](common/provisioning-scope.png)

12. Quando estiver pronto para fornecer, clique em **Guardar**.

    ![Configuração de fornecimento de poupança](common/provisioning-configuration-save.png)

Esta operação inicia o ciclo inicial de sincronização de todos os utilizadores e grupos definidos no **Âmbito** na secção **Definições.** O ciclo inicial demora mais tempo a realizar do que os ciclos subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento da AD Azure esteja em funcionamento. 

## <a name="step-6-monitor-your-deployment"></a>Passo 6. Monitorizar a implementação
Depois de configurar o fornecimento, utilize os seguintes recursos para monitorizar a sua implementação:

* Utilize os registos de [provisionamento](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) para determinar quais os utilizadores que foram provisionados com sucesso ou sem sucesso
* Verifique a barra de [progresso](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) para ver o estado do ciclo de provisionamento e quão perto está da conclusão
* Se a configuração do fornecimento parecer estar num estado pouco saudável, a aplicação entrará em quarentena. Saiba mais sobre estados de quarentena [aqui.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)  


## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do provisionamento de conta de utilizador para aplicações empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)
