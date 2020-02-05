---
title: 'Tutorial: Configure Workplace by Facebook para fornecimento automático de utilizadores com Diretório Ativo Azure  Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Workplace by Facebook.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 6341e67e-8ce6-42dc-a4ea-7295904a53ef
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 81c9d8582eb41d4a13799c42383ff22010c60577
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76985182"
---
# <a name="tutorial-configure-workplace-by-facebook-for-automatic-user-provisioning"></a>Tutorial: Configure Local de Trabalho pelo Facebook para fornecimento automático de utilizadores

Este tutorial descreve os passos que precisa de executar tanto no Workplace pelo Facebook como pelo Azure Ative Directory (Azure AD) para configurar o fornecimento automático de utilizadores. Quando configurado, a Azure AD disponibiliza e desativa automaticamente utilizadores e grupos para o Local de [Trabalho pelo Facebook](https://work.workplace.com/) utilizando o serviço de provisionamento de AD Azure. Para obter detalhes importantes sobre o que esse serviço faz, como ele funciona e perguntas frequentes, consulte [automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com Azure Active Directory](../manage-apps/user-provisioning.md).

> [!NOTE]
> A aplicação de terceiros da AD Azure no Workplace pelo Facebook foi aprovada. Os clientes não terão interrupção de serviço no dia 16 de dezembro. Verá uma nota no Workplace pela consola Facebook Admin indicando um prazo de 28-Fevereiro-2020 até quando terá de transitar para a nova aplicação. Estamos a trabalhar para manter a transição o mais simples possível e forneceremos uma atualização aqui sobre a transição até ao final do mês.

## <a name="capabilities-supported"></a>Capacidades suportadas
> [!div class="checklist"]
> * Criar utilizadores no Workplace by Facebook
> * Remova os utilizadores no Workplace pelo Facebook quando já não necessitam de acesso
> * Mantenha os atributos dos utilizadores sincronizados entre o Azure AD e o Workplace pelo Facebook
> * [Inscrição única](https://docs.microsoft.com/azure/active-directory/saas-apps/workplacebyfacebook-tutorial) no Workplace by Facebook (recomendado)

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* [Um locatário do Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Uma conta de utilizador em Azure AD com [permissão](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) para configurar o provisionamento (por exemplo, Administrador de Aplicação, Administrador de Aplicação na Nuvem, Proprietário de Aplicações ou Administrador Global)
* Um Workplace by Facebook single-sign on enabled subscrição

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de julgamento da AD Azure, pode ter um julgamento de um mês [aqui.](https://azure.microsoft.com/pricing/free-trial/)

## <a name="step-1-plan-your-provisioning-deployment"></a>Passo 1. Planejar sua implantação de provisionamento
1. Saiba mais sobre [como o serviço de provisionamento funciona](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Determine quem estará no [escopo do provisionamento](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Determine quais os dados a [mapear entre o Azure AD e o Workplace pelo Facebook](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes).

## <a name="step-2-configure-workplace-by-facebook-to-support-provisioning-with-azure-ad"></a>Passo 2. Configure Local de Trabalho pelo Facebook para apoiar o provisionamento com a AD Azure

Antes de configurar e ativar o serviço de provisionamento, precisa decidir quais os utilizadores e/ou grupos em Azure AD que representam os utilizadores que precisam de acesso ao seu Workplace através da aplicação facebook. Uma vez decidido, pode atribuir estes utilizadores ao seu Workplace através da aplicação Facebook seguindo as instruções aqui:

*   Recomenda-se que um único utilizador da AD Azure seja designado para o Local de Trabalho pelo Facebook para testar a configuração de provisionamento. Usuários e/ou grupos adicionais podem ser atribuídos posteriormente.

*   Ao atribuir um utilizador ao Workplace pelo Facebook, deve selecionar uma função de utilizador válida. A função "Acesso Predefinido" não funciona para o provisionamento.

## <a name="step-3-add-workplace-by-facebook-from-the-azure-ad-application-gallery"></a>Passo 3. Adicionar Workplace by Facebook da galeria de aplicações Azure AD

Adicione workplace by Facebook da galeria de aplicações Azure AD para começar a gerir o provisionamento para o Workplace pelo Facebook. Se já configurar o Workplace by Facebook para SSO, pode utilizar a mesma aplicação. No entanto, é recomendável que você crie um aplicativo separado ao testar a integração inicialmente. Saiba mais sobre como adicionar um aplicativo da Galeria [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passo 4. Definir quem estará no escopo para provisionamento 

O serviço de provisionamento do Azure AD permite o escopo que será provisionado com base na atribuição ao aplicativo e ou com base em atributos do usuário/grupo. Se você optar por definir o escopo que será provisionado em seu aplicativo com base na atribuição, poderá usar as [etapas](../manage-apps/assign-user-or-group-access-portal.md) a seguir para atribuir usuários e grupos ao aplicativo. Se você escolher o escopo que será provisionado com base apenas em atributos do usuário ou grupo, você poderá usar um filtro de escopo conforme descrito [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Ao atribuir utilizadores e grupos ao Workplace pelo Facebook, deve selecionar uma função diferente do **Acesso Padrão**. Os usuários com a função de acesso padrão são excluídos do provisionamento e serão marcados como não habilitados com eficiência nos logs de provisionamento. Se a única função disponível no aplicativo for a função de acesso padrão, você poderá [atualizar o manifesto do aplicativo](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) para adicionar outras funções. 

* Comece pequeno. Teste com um pequeno conjunto de usuários e grupos antes de distribuir para todos. Quando o escopo do provisionamento é definido como usuários e grupos atribuídos, você pode controlar isso atribuindo um ou dois usuários ou grupos ao aplicativo. Quando o escopo é definido como todos os usuários e grupos, você pode especificar um [filtro de escopo baseado em atributo](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

1. Inicie sessão no [Portal do Azure](https://portal.azure.com). Selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Workplace by Facebook**.

    ![O workplace by Facebook link na lista de Aplicações](common/all-applications.png)

3. Selecione a guia **provisionamento** .

    ![Guia provisionamento](common/provisioning.png)

4. Defina o **modo de provisionamento** como **automático**.

    ![Guia provisionamento](common/provisioning-automatic.png)

5. Na secção **credenciais de administrador,** clique em **Autorizar**. Será redirecionado para o Local de Trabalho pela página de autorização do Facebook. Insera o seu Workplace pelo nome de utilizador do Facebook e clique no botão **Continuar.** Clique em **Ligação** de Teste para garantir que o Azure AD pode ligar-se ao Local de Trabalho pelo Facebook. Se a ligação falhar, certifique-se de que a sua conta de Local de Trabalho através do Facebook tem permissões de Administrador e tente novamente.

    ![provisionamento](./media/workplacebyfacebook-provisioning-tutorial/provisioning.png)

    ![autorizar](./media/workplacebyfacebook-provisioning-tutorial/workplacelogin.png)

6. No campo **email de notificação** , insira o endereço de email de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e marque a caixa de seleção **Enviar uma notificação por email quando ocorrer uma falha** .

    ![Email de notificação](common/provisioning-notification-email.png)

7. Selecione **Guardar**.

8. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Users to Workplace by Facebook**.

9. Reveja os atributos do utilizador que são sincronizados de Azure AD para Workplace pelo Facebook na secção **Attribute-Mapping.** Os atributos selecionados como propriedades **Correspondentes** são usados para combinar as contas de utilizador no Local de Trabalho pelo Facebook para operações de atualização. Se optar por alterar o [atributo-alvo correspondente,](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)terá de garantir que o Workplace by Facebook API suporta filtrar os utilizadores com base nesse atributo. Selecione o botão **salvar** para confirmar as alterações.

   |Atributo|Tipo|
   |---|---|
   |userName|Cadeia|
   |displayName|Cadeia|
   |ativo|Booleano|
   |título|Booleano|
   |. Value de e-mails [tipo eq "trabalho"]|Cadeia|
   |name.givenName|Cadeia|
   |name.familyName|Cadeia|
   |nome.formatado|Cadeia|
   |endereços[tipo eq "trabalho"].formado|Cadeia|
   |.streetAddress endereços [tipo eq "trabalho"]|Cadeia|
   |endereços[tipo eq "trabalho"].localidade|Cadeia|
   |endereços[tipo eq "trabalho"].região|Cadeia|
   |endereços[tipo eq "trabalho"].país|Cadeia|
   |.postalCode endereços [tipo eq "trabalho"]|Cadeia|
   |endereços[tipo eq "outros"].formados|Cadeia|
   |. Value de phoneNumbers [tipo eq "trabalho"]|Cadeia|
   |. Value de phoneNumbers [tipo eq "móvel"]|Cadeia|
   |. Value de phoneNumbers [tipo eq "fax"]|Cadeia|
   |externalId|Cadeia|
   |preferredLanguage|Cadeia|
   |urn: IETF: params: SCIM: schemas: Extension: Enterprise: 2.0: User: Manager|Cadeia|
   |urn: IETF: params: SCIM: esquemas: extensão: Enterprise: 2.0: User: Department|Cadeia|

10. Para configurar filtros de escopo, consulte as instruções a seguir fornecidas no [tutorial de filtro de escopo](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para ativar o serviço de provisionamento de AD Azure para o Local de Trabalho pelo Facebook, altere o Estado de **Provisionamento** para **On** na secção **Definições.**

    ![Status de provisionamento alternado em](common/provisioning-toggle-on.png)

12. Defina os utilizadores e/ou grupos que gostaria de fornecer ao Workplace pelo Facebook, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Escopo de provisionamento](common/provisioning-scope.png)

13. Quando estiver pronto para provisionar, clique em **salvar**.

    ![Salvando configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia o ciclo de sincronização inicial de todos os usuários e grupos definidos no **escopo** na seção **configurações** . O ciclo inicial leva mais tempo para ser executado do que os ciclos subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure AD esteja em execução. 

## <a name="step-6-monitor-your-deployment"></a>Passo 6. Monitorizar a implementação
Depois de configurar o provisionamento, use os seguintes recursos para monitorar sua implantação:

1. Use os [logs de provisionamento](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) para determinar quais usuários foram provisionados com êxito ou sem êxito
2. Verifique a [barra de progresso](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) para ver o status do ciclo de provisionamento e como fechá-lo para conclusão
3. Se a configuração de provisionamento parecer estar em um estado não íntegro, o aplicativo entrará em quarentena. Saiba mais sobre os Estados de quarentena [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="troubleshooting-tips"></a>Sugestões para a resolução de problemas
*  Se vir um utilizador sem sucesso criado e houver um evento de registo de auditoria com o código "1789003", significa que o utilizador é de um domínio não verificado.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciando o provisionamento de conta de usuário para aplicativos empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como examinar os logs e obter relatórios sobre a atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)