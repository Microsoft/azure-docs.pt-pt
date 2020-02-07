---
title: 'Tutorial: Configure TeamViewer para fornecimento automático de utilizadores com Diretório Ativo Azure  Microsoft Docs'
description: Aprenda a fornecer e desfornecer automaticamente contas de utilizadores de Azure AD para TeamViewer.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 73dd5494-f572-42bb-9071-d599361b9bd7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2020
ms.author: Zhchia
ms.openlocfilehash: ba7afb506a21df7fc2fe4cc4b7194549b6efde83
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/06/2020
ms.locfileid: "77050567"
---
# <a name="tutorial-configure-teamviewer-for-automatic-user-provisioning"></a>Tutorial: Configure TeamViewer para fornecimento automático de utilizadores

Este tutorial descreve os passos que precisa de executar tanto no TeamViewer como no Azure Ative Directory (Azure AD) para configurar o fornecimento automático de utilizadores. Quando configurado, o Azure AD disponibiliza e desativa automaticamente utilizadores e grupos ao [TeamViewer](https://www.teamviewer.com/buy-now/) utilizando o serviço de provisionamento de AD Azure. Para detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte o fornecimento e o [desprovisionamento de utilizadores automate para aplicações SaaS com o Diretório Ativo Azure.](../manage-apps/user-provisioning.md) 


## <a name="capabilities-supported"></a>Capacidades suportadas
> [!div class="checklist"]
> * Criar utilizadores no TeamViewer
> * Remova os utilizadores no TeamViewer quando já não necessitarem de acesso
> * Mantenha os atributos dos utilizadores sincronizados entre o Azure AD e o TeamViewer
> * [Inscrição única](https://docs.microsoft.com/azure/active-directory/saas-apps/teamviewer-tutorial) para TeamViewer (recomendado)

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* [Um inquilino da AD Azure](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Uma conta de utilizador em Azure AD com [permissão](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) para configurar o provisionamento (por exemplo, Administrador de Aplicação, Administrador de Aplicação na Nuvem, Proprietário de Aplicações ou Administrador Global). 
* Uma conta TeamViewer com permissões de administração.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passo 1. Planejar sua implantação de provisionamento
1. Saiba como funciona o serviço de [provisionamento.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)
2. Determinar quem estará no [âmbito do provisionamento.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)
3. Determine quais os dados a [mapear entre O Anúncio De Azure e TeamViewer](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-teamviewer-to-support-provisioning-with-azure-ad"></a>Passo 2. Configure TeamViewer para apoiar o provisionamento com a AD Azure

1. Inicie sessão na aplicação 'Consola Admin' do TeamViewer. Navegar para **editar perfil**.

    ![Consola de admina TeamViewer](./media/teamviewer-provisioning-tutorial/admin.png)

2.  Navegar para **Apps**. Clique em **Criar Ficha de Script**.

    ![TeamViewer Criar Token](./media/teamviewer-provisioning-tutorial/createtoken.png)

3.  Forneça um nome para o símbolo do guião. Clique no botão **Guardar.**

    ![Nome token teamviewer](./media/teamviewer-provisioning-tutorial/tokenname.png)

4. Copie o **Token** e clique **ok**. Este valor será inserido no campo **Secret Token** da sua aplicação TeamViewer no portal Azure.

    ![TeamViewer Token](./media/teamviewer-provisioning-tutorial/token.png)

## <a name="step-3-add-teamviewer-from-the-azure-ad-application-gallery"></a>Passo 3. Adicione TeamViewer da galeria de aplicações da AD Azure

Adicione teamViewer da galeria de aplicações Azure AD para começar a gerir o provisionamento ao TeamViewer. Se já tiver configurado o TeamViewer para SSO, pode utilizar a mesma aplicação. No entanto, é recomendável que você crie um aplicativo separado ao testar a integração inicialmente. Saiba mais sobre a adição de uma aplicação na galeria [aqui.](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passo 4. Definir quem estará no escopo para provisionamento 

O serviço de provisionamento do Azure AD permite o escopo que será provisionado com base na atribuição ao aplicativo e ou com base em atributos do usuário/grupo. Se optar por examinar quem será aprovisionado na sua app com base na atribuição, pode utilizar os [seguintes passos](../manage-apps/assign-user-or-group-access-portal.md) para atribuir utilizadores e grupos à aplicação. Se optar por examinar quem será aprovisionado apenas com base em atributos do utilizador ou do grupo, pode utilizar um filtro de deteção como descrito [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Ao atribuir utilizadores e grupos ao TeamViewer, deve selecionar uma função diferente do **Acesso Padrão**. Os usuários com a função de acesso padrão são excluídos do provisionamento e serão marcados como não habilitados com eficiência nos logs de provisionamento. Se a única função disponível na aplicação for a função de acesso padrão, pode [atualizar o manifesto de aplicação](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) para adicionar funções adicionais. 

* Comece pequeno. Teste com um pequeno conjunto de usuários e grupos antes de distribuir para todos. Quando o escopo do provisionamento é definido como usuários e grupos atribuídos, você pode controlar isso atribuindo um ou dois usuários ou grupos ao aplicativo. Quando o âmbito é definido para todos os utilizadores e grupos, pode especificar um filtro de [deteção baseado em atributos](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-teamviewer"></a>Passo 5. Configure o fornecimento automático de utilizadores ao TeamViewer 

Esta seção orienta você pelas etapas para configurar o serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no TestApp com base em atribuições de usuário e/ou grupo no Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-teamviewer-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para TeamViewer em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais**e, em seguida, selecione **Todas as aplicações**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **TeamViewer**.

    ![O link TeamViewer na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Provisioning.**

    ![Guia provisionamento](common/provisioning.png)

4. Detete o **modo de provisionamento** para **automático**.

    ![Guia provisionamento](common/provisioning-automatic.png)

5. Na secção **credenciais de administrador,** insere as credenciais de administrador do TeamViewer e o seu nome de utilizador. Clique em **Ligação** de Teste para garantir que o Azure AD pode ligar-se ao TeamViewer. Se a ligação falhar, certifique-se de que a sua conta TeamViewer tem permissões de Administrador e tente novamente.

    ![provisionamento](./media/teamViewer-provisioning-tutorial/provisioning.png)

6. No campo de email de **notificação,** introduza o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de fornecimento e selecione a **notificação de e-mail enviar uma notificação de e-mail quando ocorrer uma falha** verificar a caixa.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Selecione **Guardar**.

8. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Users to TeamViewer**.

9. Reveja os atributos do utilizador que são sincronizados de Azure AD para TeamViewer na secção **Attribute-Mapping.** Os atributos selecionados como propriedades **Correspondentes** são usados para combinar as contas de utilizador no TeamViewer para operações de atualização. Se optar por alterar o [atributo-alvo correspondente,](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)terá de garantir que a API do TeamViewer suporta filtrar os utilizadores com base nesse atributo. Selecione o botão **Guardar** para elegiro qualquer alteração.

   |Atributo|Tipo|
   |---|---|
   |userName|String|
   |displayName|String|
   |ativo|Booleano|

10. Para configurar filtros de deteção, consulte as seguintes instruções fornecidas no tutorial do [filtro Descodificação](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para ativar o serviço de provisionamento de AD Azure para TeamViewer, altere o Estado de **Provisionamento** para **On** na secção **Definições.**

    ![Status de provisionamento alternado em](common/provisioning-toggle-on.png)

12. Defina os utilizadores e/ou grupos que gostaria de fornecer ao TeamViewer, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Escopo de provisionamento](common/provisioning-scope.png)

13. Quando estiver pronto para fornecer, clique em **Guardar**.

    ![Salvando configuração de provisionamento](common/provisioning-configuration-save.png)

Esta operação inicia o ciclo inicial de sincronização de todos os utilizadores e grupos definidos no **Âmbito** na secção **Definições.** O ciclo inicial leva mais tempo para ser executado do que os ciclos subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure AD esteja em execução. 

## <a name="step-6-monitor-your-deployment"></a>Passo 6. Monitorizar a implementação
Depois de configurar o provisionamento, use os seguintes recursos para monitorar sua implantação:

* Utilize os registos de [provisionamento](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) para determinar quais os utilizadores que foram provisionados com sucesso ou sem sucesso
* Verifique a barra de [progresso](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) para ver o estado do ciclo de provisionamento e quão perto está da conclusão
* Se a configuração de provisionamento parecer estar em um estado não íntegro, o aplicativo entrará em quarentena. Saiba mais sobre estados de quarentena [aqui.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do provisionamento de conta de utilizador para aplicações empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)
