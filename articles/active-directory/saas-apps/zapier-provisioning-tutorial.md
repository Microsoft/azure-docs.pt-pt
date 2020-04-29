---
title: 'Tutorial: Configure Zapier para fornecimento automático de utilizadores com Diretório Ativo Azure [ Microsoft Docs'
description: Aprenda a fornecer e desfornecer automaticamente contas de utilizadores de Azure AD para Zapier.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 66e224f9-2311-4564-bb84-99fce59a398f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: Zhchia
ms.openlocfilehash: 4091e4fd544dbc6450bc14bd0e0731c4d3024592
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76992160"
---
# <a name="tutorial-configure-zapier-for-automatic-user-provisioning"></a>Tutorial: Configure Zapier para fornecimento automático de utilizadores

Este tutorial descreve os passos necessários para executar tanto no Zapier como no Azure Ative Directory (Azure AD) para configurar o fornecimento automático de utilizadores. Quando configurado, a AD Azure aprovisiona automaticamente e despresta utilizadores e grupos a [Zapier](https://zapier.com/pricing) utilizando o serviço de provisionamento de AD Azure. Para detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte o fornecimento e o [desprovisionamento de utilizadores automate para aplicações SaaS com o Diretório Ativo Azure.](../manage-apps/user-provisioning.md) 


## <a name="capabilities-supported"></a>Capacidades suportadas
> [!div class="checklist"]
> * Criar utilizadores em Zapier
> * Remova os utilizadores em Zapier quando já não necessitam de acesso
> * Mantenha os atributos dos utilizadores sincronizados entre a Azure AD e a Zapier
> * Grupos de provisões e membros do grupo em Zapier
> * Inscrição única para Zapier (recomendado)

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* [Um inquilino da AD Azure](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Uma conta de utilizador em Azure AD com [permissão](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) para configurar o provisionamento (por exemplo, Administrador de Aplicação, Administrador de Aplicação na Nuvem, Proprietário de Aplicações ou Administrador Global). 
* Uma conta de utilizador em Zapier com permissões de administrador.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passo 1. Planeie a sua implantação de provisionamento
1. Saiba como funciona o serviço de [provisionamento.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)
2. Determinar quem estará no [âmbito do provisionamento.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)
3. Determine que dados a [mapear entre Azure AD e Zapier](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-zapier-to-support-provisioning-with-azure-ad"></a>Passo 2. Configure Zapier para apoiar o provisionamento com a Azure AD

1. Inscreva-se na consola [Zapier Admin](https://zapier.com/app/login/). Navegue para **Definições** sob o ID do inquilino.

    ![Consola Zapier Admin](media/zapier-provisioning-tutorial/admin.png)

2. Em **definições de EMPRESA,** selecione **o fornecimento do utilizador**.

    ![Zapier Adicionar SCIM](media/zapier-provisioning-tutorial/user.png)

3. Copie o URL base do **SCIM** e **o token do portador do SCIM**. Estes valores serão inscritos nos campos URL do Tenant e Secret Token, respectivamente, no separador de fornecimento da sua aplicação Zapier no portal Azure.

    ![Zapier Criar Token](media/zapier-provisioning-tutorial/token.png)

## <a name="step-3-add-zapier-from-the-azure-ad-application-gallery"></a>Passo 3. Adicione Zapier da galeria de aplicações da AD Azure

Adicione Zapier da galeria de aplicações da AD Azure para começar a gerir o provisionamento a Zapier. Se já configurar o Zapier para sSO, pode utilizar a mesma aplicação. No entanto, recomenda-se que crie uma aplicação separada ao testar a integração inicialmente. Saiba mais sobre a adição de uma aplicação na galeria [aqui.](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passo 4. Definir quem estará no âmbito do provisionamento 

O serviço de provisionamento de AD Azure permite-lhe examinar quem será provisionado com base na atribuição à aplicação e ou com base em atributos do utilizador/grupo. Se optar por examinar quem será aprovisionado na sua app com base na atribuição, pode utilizar os [seguintes passos](../manage-apps/assign-user-or-group-access-portal.md) para atribuir utilizadores e grupos à aplicação. Se optar por examinar quem será aprovisionado apenas com base em atributos do utilizador ou do grupo, pode utilizar um filtro de deteção como descrito [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Ao atribuir utilizadores e grupos a Zapier, deve selecionar uma função diferente do **Acesso Padrão**. Os utilizadores com a função de Acesso Predefinido estão excluídos do fornecimento e serão marcados como não tendo direito efetivamente nos registos de fornecimento. Se a única função disponível na aplicação for a função de acesso padrão, pode [atualizar o manifesto de aplicação](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) para adicionar funções adicionais. 

* Comece minúsculo. Teste com um pequeno conjunto de utilizadores e grupos antes de passar para todos. Quando o âmbito de fornecimento for definido para utilizadores e grupos atribuídos, pode controlá-lo atribuindo um ou dois utilizadores ou grupos à aplicação. Quando o âmbito é definido para todos os utilizadores e grupos, pode especificar um filtro de [deteção baseado em atributos](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-zapier"></a>Passo 5. Configure o fornecimento automático de utilizadores a Zapier 

Esta secção orienta-o através dos passos para configurar o serviço de provisionamento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos no TestApp com base em atribuições de utilizador e/ou grupo em Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-zapier-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para zapier em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais**e, em seguida, selecione **Todas as aplicações**.

   ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Zapier**.

   ![O link Zapier na lista de aplicações](common/all-applications.png)

3. Selecione o separador **Provisioning.**

   ![Guia de provisionamento](common/provisioning.png)

4. Detete o **modo de provisionamento** para **automático**.

   ![Guia de provisionamento](common/provisioning-automatic.png)

5. Sob a secção **de Credenciais de Administrador,** insera o url do **Inquilino** Zapier e **o Token Secreto.** Clique em **Ligação de Teste** para garantir que o Azure AD pode ligar-se a Zapier. Se a ligação falhar, certifique-se de que a sua conta Zapier tem permissões de administrador e tente novamente.

   ![provisionamento](./media/zapier-provisioning-tutorial/provisioning.png)

6. No campo de email de **notificação,** introduza o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de fornecimento e selecione a **notificação de e-mail enviar uma notificação de e-mail quando ocorrer uma falha** verificar a caixa.

   ![Email de notificação](common/provisioning-notification-email.png)

7. Selecione **Guardar**.

8. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Users to Zapier**.

9. Reveja os atributos do utilizador que são sincronizados de Azure AD a Zapier na secção **Attribute-Mapping.** Os atributos selecionados como propriedades **Correspondentes** são usados para combinar as contas de utilizador em Zapier para operações de atualização. Se optar por alterar o [atributo-alvo correspondente,](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)terá de garantir que a API Zapier suporta filtrar utilizadores com base nesse atributo. Selecione o botão **Guardar** para elegiro qualquer alteração.

   |Variável|Tipo|
   |---|---|
   |userName|String|
   |ativo|Booleano|
   |id externo|String|
   |nome.dadoNome|String|
   |nome.familyName|String|
   |e-mails[tipo eq "work"].valor|String|

10. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Groups to Zapier**.

11. Reveja os atributos do grupo que são sincronizados de Azure AD a Zapier na secção **Attribute-Mapping.** Os atributos selecionados como propriedades **correspondentes** são usados para combinar os grupos em Zapier para operações de atualização. Selecione o botão **Guardar** para elegiro qualquer alteração.

    |Variável|Tipo|
    |---|---|
    |displayName|String|
    |membros|Referência|

12. Para configurar filtros de deteção, consulte as seguintes instruções fornecidas no tutorial do [filtro Descodificação](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para ativar o serviço de provisionamento de AD Azure para o Zapier, altere o Estado de **Provisionamento** para **On** na secção **Definições.**

    ![Estatuto de provisionamento Alternado](common/provisioning-toggle-on.png)

14. Defina os utilizadores e/ou grupos que gostaria de fornecer a Zapier, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para fornecer, clique em **Guardar**.

    ![Configuração de fornecimento de poupança](common/provisioning-configuration-save.png)

Esta operação inicia o ciclo inicial de sincronização de todos os utilizadores e grupos definidos no **Âmbito** na secção **Definições.** O ciclo inicial demora mais tempo a realizar do que os ciclos subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento da AD Azure esteja em funcionamento. 

## <a name="step-6-monitor-your-deployment"></a>Passo 6. Monitorizar a implementação
Depois de configurar o fornecimento, utilize os seguintes recursos para monitorizar a sua implementação:

- Utilize os registos de [provisionamento](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) para determinar quais os utilizadores que foram provisionados com sucesso ou sem sucesso
- Verifique a barra de [progresso](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) para ver o estado do ciclo de provisionamento e quão perto está da conclusão
- Se a configuração do fornecimento parecer estar num estado pouco saudável, a aplicação entrará em quarentena. Saiba mais sobre estados de quarentena [aqui.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do provisionamento de conta de utilizador para aplicações empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)
