---
title: 'Tutorial: Configure Zapier para fornecimento automático de utilizadores com Diretório Ativo Azure  Microsoft Docs'
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
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76992160"
---
# <a name="tutorial-configure-zapier-for-automatic-user-provisioning"></a>Tutorial: Configure Zapier para fornecimento automático de utilizadores

Este tutorial descreve os passos necessários para executar tanto no Zapier como no Azure Ative Directory (Azure AD) para configurar o fornecimento automático de utilizadores. Quando configurado, a AD Azure aprovisiona automaticamente e despresta utilizadores e grupos a [Zapier](https://zapier.com/pricing) utilizando o serviço de provisionamento de AD Azure. Para obter detalhes importantes sobre o que esse serviço faz, como ele funciona e perguntas frequentes, consulte [automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Capacidades suportadas
> [!div class="checklist"]
> * Criar utilizadores em Zapier
> * Remova os utilizadores em Zapier quando já não necessitam de acesso
> * Mantenha os atributos dos utilizadores sincronizados entre a Azure AD e a Zapier
> * Grupos de provisões e membros do grupo em Zapier
> * Inscrição única para Zapier (recomendado)

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* [Um locatário do Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Uma conta de usuário no Azure AD com [permissão](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) para configurar o provisionamento (por exemplo, administrador de aplicativos, administrador de aplicativos de nuvem, proprietário do aplicativo ou administrador global). 
* Uma conta de utilizador em Zapier com permissões de administrador.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passo 1. Planejar sua implantação de provisionamento
1. Saiba mais sobre [como o serviço de provisionamento funciona](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Determine quem estará no [escopo do provisionamento](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Determine que dados a [mapear entre Azure AD e Zapier](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-zapier-to-support-provisioning-with-azure-ad"></a>Passo 2. Configure Zapier para apoiar o provisionamento com a Azure AD

1. Inscreva-se na consola [Zapier Admin](https://zapier.com/app/login/). Navegue para **Definições** sob o ID do inquilino.

    ![Consola Zapier Admin](media/zapier-provisioning-tutorial/admin.png)

2. Em **definições de EMPRESA,** selecione **o fornecimento do utilizador**.

    ![Zapier Adicionar SCIM](media/zapier-provisioning-tutorial/user.png)

3. Copie o URL base do **SCIM** e **o token do portador do SCIM**. Estes valores serão inscritos nos campos URL do Tenant e Secret Token, respectivamente, no separador de fornecimento da sua aplicação Zapier no portal Azure.

    ![Zapier Criar Token](media/zapier-provisioning-tutorial/token.png)

## <a name="step-3-add-zapier-from-the-azure-ad-application-gallery"></a>Passo 3. Adicione Zapier da galeria de aplicações da AD Azure

Adicione Zapier da galeria de aplicações da AD Azure para começar a gerir o provisionamento a Zapier. Se já configurar o Zapier para sSO, pode utilizar a mesma aplicação. No entanto, é recomendável que você crie um aplicativo separado ao testar a integração inicialmente. Saiba mais sobre como adicionar um aplicativo da Galeria [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passo 4. Definir quem estará no escopo para provisionamento 

O serviço de provisionamento do Azure AD permite o escopo que será provisionado com base na atribuição ao aplicativo e ou com base em atributos do usuário/grupo. Se você optar por definir o escopo que será provisionado em seu aplicativo com base na atribuição, poderá usar as [etapas](../manage-apps/assign-user-or-group-access-portal.md) a seguir para atribuir usuários e grupos ao aplicativo. Se você escolher o escopo que será provisionado com base apenas em atributos do usuário ou grupo, você poderá usar um filtro de escopo conforme descrito [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Ao atribuir utilizadores e grupos a Zapier, deve selecionar uma função diferente do **Acesso Padrão**. Os usuários com a função de acesso padrão são excluídos do provisionamento e serão marcados como não habilitados com eficiência nos logs de provisionamento. Se a única função disponível no aplicativo for a função de acesso padrão, você poderá [atualizar o manifesto do aplicativo](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) para adicionar outras funções. 

* Comece pequeno. Teste com um pequeno conjunto de usuários e grupos antes de distribuir para todos. Quando o escopo do provisionamento é definido como usuários e grupos atribuídos, você pode controlar isso atribuindo um ou dois usuários ou grupos ao aplicativo. Quando o escopo é definido como todos os usuários e grupos, você pode especificar um [filtro de escopo baseado em atributo](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-zapier"></a>Passo 5. Configure o fornecimento automático de utilizadores a Zapier 

Esta seção orienta você pelas etapas para configurar o serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no TestApp com base em atribuições de usuário e/ou grupo no Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-zapier-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para zapier em Azure AD:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com). Selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.

   ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Zapier**.

   ![O link Zapier na lista de aplicações](common/all-applications.png)

3. Selecione a guia **provisionamento** .

   ![Guia provisionamento](common/provisioning.png)

4. Defina o **modo de provisionamento** como **automático**.

   ![Guia provisionamento](common/provisioning-automatic.png)

5. Sob a secção **de Credenciais de Administrador,** insera o url do **Inquilino** Zapier e **o Token Secreto.** Clique em **Ligação de Teste** para garantir que o Azure AD pode ligar-se a Zapier. Se a ligação falhar, certifique-se de que a sua conta Zapier tem permissões de administrador e tente novamente.

   ![provisionamento](./media/zapier-provisioning-tutorial/provisioning.png)

6. No campo **email de notificação** , insira o endereço de email de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e marque a caixa de seleção **Enviar uma notificação por email quando ocorrer uma falha** .

   ![Email de notificação](common/provisioning-notification-email.png)

7. Selecione **Guardar**.

8. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Users to Zapier**.

9. Reveja os atributos do utilizador que são sincronizados de Azure AD a Zapier na secção **Attribute-Mapping.** Os atributos selecionados como propriedades **Correspondentes** são usados para combinar as contas de utilizador em Zapier para operações de atualização. Se optar por alterar o [atributo-alvo correspondente,](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)terá de garantir que a API Zapier suporta filtrar utilizadores com base nesse atributo. Selecione o botão **salvar** para confirmar as alterações.

   |Variável|Tipo|
   |---|---|
   |userName|Cadeia|
   |ativo|Booleano|
   |externalId|Cadeia|
   |name.givenName|Cadeia|
   |name.familyName|Cadeia|
   |. Value de e-mails [tipo eq "trabalho"]|Cadeia|

10. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Groups to Zapier**.

11. Reveja os atributos do grupo que são sincronizados de Azure AD a Zapier na secção **Attribute-Mapping.** Os atributos selecionados como propriedades **correspondentes** são usados para combinar os grupos em Zapier para operações de atualização. Selecione o botão **salvar** para confirmar as alterações.

    |Variável|Tipo|
    |---|---|
    |displayName|Cadeia|
    |membros|Referência|

12. Para configurar filtros de escopo, consulte as instruções a seguir fornecidas no [tutorial de filtro de escopo](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para ativar o serviço de provisionamento de AD Azure para o Zapier, altere o Estado de **Provisionamento** para **On** na secção **Definições.**

    ![Status de provisionamento alternado em](common/provisioning-toggle-on.png)

14. Defina os utilizadores e/ou grupos que gostaria de fornecer a Zapier, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Escopo de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para provisionar, clique em **salvar**.

    ![Salvando configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia o ciclo de sincronização inicial de todos os usuários e grupos definidos no **escopo** na seção **configurações** . O ciclo inicial leva mais tempo para ser executado do que os ciclos subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure AD esteja em execução. 

## <a name="step-6-monitor-your-deployment"></a>Passo 6. Monitorizar a implementação
Depois de configurar o provisionamento, use os seguintes recursos para monitorar sua implantação:

- Use os [logs de provisionamento](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) para determinar quais usuários foram provisionados com êxito ou sem êxito
- Verifique a [barra de progresso](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) para ver o status do ciclo de provisionamento e como fechá-lo para conclusão
- Se a configuração de provisionamento parecer estar em um estado não íntegro, o aplicativo entrará em quarentena. Saiba mais sobre os Estados de quarentena [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciando o provisionamento de conta de usuário para aplicativos empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como examinar os logs e obter relatórios sobre a atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)
