---
title: 'Tutorial: Configure 8x8 para fornecimento automático de utilizadores com Diretório Ativo Azure [ Microsoft Docs'
description: Aprenda a fornecer e desfornecer automaticamente contas de utilizador de Azure AD para 8x8.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 81b4cde7-4938-4a1a-8495-003c06239b27
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2020
ms.author: Zhchia
ms.openlocfilehash: 6fc14bd41faf6a86953b82f8a7ea7bd75b746cf9
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/22/2020
ms.locfileid: "83801682"
---
# <a name="tutorial-configure-8x8-for-automatic-user-provisioning"></a>Tutorial: Configure 8x8 para fornecimento automático de utilizadores

Este tutorial descreve os passos necessários para executar tanto no 8x8 Configuration Manager como no Azure Ative Directory (Azure AD) para configurar o fornecimento automático de utilizadores. Quando configurado, a AD Azure aprovisiona automaticamente e despresta utilizadores e grupos a [8x8](https://www.8x8.com) utilizando o serviço de provisionamento AD Azure. Para detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte o fornecimento e o [desprovisionamento de utilizadores automate para aplicações SaaS com o Diretório Ativo Azure.](../manage-apps/user-provisioning.md) 

## <a name="capabilities-supported"></a>Capacidades suportadas
> [!div class="checklist"]
> * Criar utilizadores em 8x8
> * Remova os utilizadores em 8x8 quando já não necessitam de acesso
> * Mantenha os atributos dos utilizadores sincronizados entre o Azure AD e o 8x8
> * [Inscrição única](https://docs.microsoft.com/azure/active-directory/saas-apps/8x8virtualoffice-tutorial) para 8x8 (recomendado)

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* [Um inquilino da AD Azure](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Uma conta de utilizador em Azure AD com [permissão](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) para configurar o provisionamento (por exemplo, Administrador de Aplicação, Administrador de Aplicação na Nuvem, Proprietário de Aplicações ou Administrador Global).
* Uma subscrição da série 8x8 X de qualquer nível.
* Uma conta de utilizador de 8x8 com permissão do administrador no [Gestor de Configuração](https://vo-cm.8x8.com).
* [O Single Sign-On com o Azure AD](https://docs.microsoft.com/azure/active-directory/saas-apps/8x8virtualoffice-tutorial) já foi configurado.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passo 1. Planeie a sua implantação de provisionamento
1. Saiba como funciona o serviço de [provisionamento.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)
2. Determinar quem estará no [âmbito do provisionamento.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)
3. Determine quais os dados a [mapear entre o Azure AD e o 8x8](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes).

## <a name="step-2-configure-8x8-to-support-provisioning-with-azure-ad"></a>Passo 2. Configure 8x8 para apoiar o provisionamento com a Azure AD

Esta secção guia-o através dos passos para configurar o 8x8 para suportar o fornecimento com a AD Azure.

### <a name="to-configure-a-user-provisioning-access-token-in-8x8-configuration-manager"></a>Para configurar um utilizador que aprovisiona o acesso ao token no 8x8 Configurmanager:

1. Inscreva-se no [Gestor de Configuração](https://vo-cm.8x8.com). Selecione **Gestão de Identidade.**

   ![Gestão de Identidade](./media/8x8-provisioning-tutorial/8x8-identity-management.png)

2. Clique no link de informação de fornecimento de **informação** do utilizador show para gerar um token.

   ![Mostrar o provisionamento do utilizador](./media/8x8-provisioning-tutorial/8x8-show-user-provisioning.png)

3. Copie os valores **8x8 URL** e **8x8 API Token.** Estes valores serão inscritos nos campos URL do **Tenant** e **Secret Token,** respectivamente, no separador de fornecimento da sua aplicação 8x8 no portal Azure.

   ![URL de cópia e token](./media/8x8-provisioning-tutorial/8x8-copy-url-token.png)

## <a name="step-3-add-8x8-from-the-azure-ad-application-gallery"></a>Passo 3. Adicione 8x8 da galeria de aplicações da AD Azure

Adicione 8x8 da galeria de aplicações azure AD para começar a gerir o provisionamento para 8x8. Se já tiver configurado 8x8 para SSO, pode utilizar a mesma aplicação. No entanto, recomenda-se que crie uma aplicação separada ao testar a integração inicialmente. Saiba mais sobre a adição de uma aplicação na galeria [aqui.](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passo 4. Definir quem estará no âmbito do provisionamento

O serviço de provisionamento de AD Azure permite-lhe examinar quem será provisionado com base na atribuição à aplicação e ou com base em atributos do utilizador/grupo. Se optar por examinar quem será aprovisionado na sua app com base na atribuição, pode utilizar os [seguintes passos](../manage-apps/assign-user-or-group-access-portal.md) para atribuir utilizadores e grupos à aplicação. Esta é a opção mais simples e é usada pela maioria das pessoas.

Se optar por examinar quem será aprovisionado apenas com base em atributos do utilizador ou do grupo, pode utilizar um filtro de deteção como descrito [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Ao atribuir utilizadores e grupos a 8x8, deve selecionar uma função diferente do **Acesso Predefinido**. Os utilizadores com a função de Acesso Predefinido estão excluídos do fornecimento e serão marcados como não tendo direito efetivamente nos registos de fornecimento. Se a única função disponível na aplicação for a função de acesso padrão, pode [atualizar o manifesto de aplicação](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) para adicionar funções adicionais. 

* Comece minúsculo. Teste com um pequeno conjunto de utilizadores e grupos antes de passar para todos. Quando o âmbito de fornecimento for definido para utilizadores e grupos atribuídos, pode controlá-lo atribuindo um ou dois utilizadores ou grupos à aplicação. Quando o âmbito é definido para todos os utilizadores e grupos, pode especificar um filtro de [deteção baseado em atributos](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

## <a name="step-5-configure-automatic-user-provisioning-to-8x8"></a>Passo 5. Configure o fornecimento automático de utilizadores para 8x8 

Esta secção guia-o através dos passos para configurar o serviço de provisionamento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos em 8x8 com base em atribuições de utilizador e/ou grupo em Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-8x8-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para 8x8 em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais**e, em seguida, selecione **Todas as aplicações**.

    ![Lâmina de aplicações da empresa](./media/8x8-provisioning-tutorial/enterprise-applications.png)

    ![Painel Todas as aplicações](./media/8x8-provisioning-tutorial/all-applications.png)

2. Na lista de aplicações, selecione **8x8**.

    ![O link 8x8 na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Provisioning.** Clique em **Iniciar**.

    ![Guia de provisionamento](common/provisioning.png)

   ![Começar a lâmina](./media/8x8-provisioning-tutorial/get-started.png)

4. Detete o **modo de provisionamento** para **automático**.

    ![Guia de provisionamento](common/provisioning-automatic.png)

5. Na secção **credenciais de administrador,** copie o **URL 8x8** do Gestor de Configuração para **O URL do Inquilino**. Copie o **8x8 API Token** do Gestor de Configuração em **Ficha Secreta**. Clique na **ligação de teste** para garantir que o Azure AD pode ligar-se ao 8x8. Se a ligação falhar, certifique-se de que a sua conta 8x8 tem permissões de administrador e tente novamente.

    ![Aprovisionamento](./media/8x8-provisioning-tutorial/provisioning.png)

6. No campo de email de **notificação,** introduza o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de fornecimento e selecione a **notificação de e-mail enviar uma notificação de e-mail quando ocorrer uma falha** verificar a caixa.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Selecione **Guardar**.

8. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Users para 8x8**.

9. Reveja os atributos do utilizador sincronizados de Azure AD a 8x8 na secção **Attribute-Mapping.** Os atributos selecionados como propriedades **Correspondentes** são usados para combinar as contas do utilizador em 8x8 para operações de atualização. Se optar por alterar o [atributo-alvo correspondente,](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)terá de garantir que o API 8x8 suporta filtrar os utilizadores com base nesse atributo. Selecione o botão **Guardar** para elegiro qualquer alteração.

   |Atributo|Tipo|Notas|
   |---|---|---|
   |userName|String|Define tanto o nome de utilizador como o ID da Federação|
   |id externo|String||
   |ativo|Booleano||
   |título|String||
   |e-mails[tipo eq "work"].valor|String||
   |nome.dadoNome|String||
   |nome.familyName|String||
   |telefoneNumbers[tipo eq "mobile"].valor|String|Número de contacto pessoal|
   |telefoneNumbers[tipo eq "trabalho"].valor|String|Número de contacto pessoal|
   |urn:ietf:params:scim:schemas:extens:enterprise:2.0:User:department|String||
   |urn:ietf:params:scim:schemas:extensão:8x8:1:1:Utilizador:site|String|Não pode ser atualizado após a criação do utilizador|
   |região|String|Não mapeado por padrão|
   |fuso horário|String|Não mapeado por padrão|

10. Para configurar filtros de deteção, consulte as seguintes instruções fornecidas no tutorial do [filtro Descodificação](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para ativar o serviço de provisionamento AD Azure para 8x8, altere o Estado de **Provisionamento** para **On** na secção **Definições.**

    ![Estatuto de provisionamento Alternado](common/provisioning-toggle-on.png)

12. Defina os utilizadores e/ou grupos que deseja fornecer ao 8x8, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de provisionamento](common/provisioning-scope.png)

13. Quando estiver pronto para fornecer, clique em **Guardar**.

    ![Configuração de fornecimento de poupança](common/provisioning-configuration-save.png)

Esta operação inicia o ciclo inicial de sincronização de todos os utilizadores e grupos definidos no **Âmbito** na secção **Definições.** O ciclo inicial demora mais tempo a realizar do que os ciclos subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento da AD Azure esteja em funcionamento. 

## <a name="step-6-monitor-your-deployment"></a>Passo 6. Monitorizar a implementação
Depois de configurar o fornecimento, utilize os seguintes recursos para monitorizar a sua implementação:

1. Utilize os [registos](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) de fornecimento para determinar quais os utilizadores que foram provisionados com sucesso ou sem sucesso.
2. Verifique a barra de [progresso](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) para ver o estado do ciclo de provisionamento e a sua proximidade até à conclusão.
3. Se a configuração do fornecimento parecer estar num estado pouco saudável, a aplicação entrará em quarentena. Saiba mais sobre estados de quarentena [aqui.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do provisionamento de conta de utilizador para aplicações empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)
