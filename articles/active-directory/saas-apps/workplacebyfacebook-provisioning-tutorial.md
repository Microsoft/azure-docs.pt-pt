---
title: 'Tutorial: Configure Workplace by Facebook para fornecimento automático de utilizadores com Diretório Ativo Azure [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Workplace pelo Facebook.
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
ms.openlocfilehash: 22576be8dec021f0f18a6e2dda16891ce70d4f13
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77603217"
---
# <a name="tutorial-configure-workplace-by-facebook-for-automatic-user-provisioning"></a>Tutorial: Configure Local de Trabalho pelo Facebook para fornecimento automático de utilizadores

Este tutorial descreve os passos que precisa de executar tanto no Workplace pelo Facebook como pelo Azure Ative Directory (Azure AD) para configurar o fornecimento automático de utilizadores. Quando configurado, a Azure AD disponibiliza e desativa automaticamente utilizadores e grupos para o Local de [Trabalho pelo Facebook](https://work.workplace.com/) utilizando o serviço de provisionamento de AD Azure. Para detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte o fornecimento e o [desprovisionamento de utilizadores automate para aplicações SaaS com o Diretório Ativo Azure.](../manage-apps/user-provisioning.md)

## <a name="migrating-to-the-new-workplace-by-facebook-application"></a>Migrar para o novo Workplace pela aplicação do Facebook
Se tiver uma integração existente com o Workplace pelo Facebook, consulte a secção abaixo sobre as mudanças que estão a chegar. Se estiver a configurar o Workplace by Facebook pela primeira vez, pode saltar esta secção e passar para as capacidades suportadas. 

#### <a name="whats-changing"></a>O que está a mudar?
* Alterações do lado da AD Azure: O método de autorização para fornecer utilizadores no Local de Trabalho tem sido historicamente um símbolo secreto de longa duração. Em breve verá o método de autorização alterado para a concessão de autorização da OAuth. 
* Mudanças no lado do Workplace: Anteriormente, a aplicação Azure AD era uma integração personalizada no Workplace pelo Facebook. Agora verá o Azure AD no diretório de integrações no Local de Trabalho como uma aplicação de terceiros. 

 

#### <a name="what-do-i-need-to-do-to-migrate-my-existing-custom-integration-to-the-new-application"></a>O que preciso fazer para migrar a minha integração personalizada existente para a nova aplicação?
Se tiver uma integração no Local de Trabalho existente com um símbolo válido, **não é necessária qualquer ação.** Estamos a migrar automaticamente clientes todas as semanas para a nova aplicação. Isto é feito completamente nos bastidores. Se não puder esperar e pretender mudar-se para a nova aplicação manualmente, pode adicionar uma nova instância de Workplace a partir da galeria e configurar novamente o fornecimento. Todas as novas instâncias do Workplace utilizarão automaticamente a nova versão da aplicação. 

 
Se a sua integração no Local de Trabalho estiver em quarentena, terá de fornecer novamente um símbolo válido para que o migramos. A secção de credenciais de administração será acinzentada, mas pode anexar o seguinte **. Microsoft_AAD_IAM_userProvisioningEnableCredentialsOverride=verdadeiro)** ao seu URL para salvar as credenciais novamente. 

https://portal.azure.com/?Microsoft_AAD_IAM_userProvisioningEnableCredentialsOverride=true

#### <a name="how-can-i-tell-if-my-application-has-been-migrated"></a>Como posso saber se o meu pedido foi migrado? 
Quando o seu pedido for migrado, o banner na secção de autorização sobre alterações de upcomming será removido e o campo de fichas secreto será substituído por um botão de autorização azul. 

#### <a name="the-admin-credentials-section-is-greyed-out-on-my-application-and-i-cant-save-why"></a>A secção de credenciais de administração está acinzentada na minha candidatura e não posso salvar. Porquê?
Bloqueámos a secção de credenciais de administração para os clientes existentes no Local de Trabalho. Quando o seu inquilino tiver sido migrado para a nova aplicação workplace poderá atualizar novamente a secção de credenciais de administração. Se não puder esperar, pode usar o URL acima para editar a sua aplicação. 

 
#### <a name="when-will-these-changes-happen"></a>Quando é que estas mudanças vão acontecer?
Todas as novas instâncias do Workplace já estarão a utilizar o novo método de integração/autorização. As integrações existentes serão migradas gradualmente até maio. A equipa de trabalho forneceu uma extensão do prazo de 28 de fevereiro para 1 de maio. 

## <a name="capabilities-supported"></a>Capacidades suportadas
> [!div class="checklist"]
> * Criar utilizadores no Workplace by Facebook
> * Remova os utilizadores no Workplace pelo Facebook quando já não necessitam de acesso
> * Mantenha os atributos dos utilizadores sincronizados entre o Azure AD e o Workplace pelo Facebook
> * [Inscrição única](https://docs.microsoft.com/azure/active-directory/saas-apps/workplacebyfacebook-tutorial) no Workplace by Facebook (recomendado)

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* [Um inquilino da AD Azure](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Uma conta de utilizador em Azure AD com [permissão](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) para configurar o provisionamento (por exemplo, Administrador de Aplicação, Administrador de Aplicação na Nuvem, Proprietário de Aplicações ou Administrador Global)
* Um Workplace by Facebook single-sign on enabled subscrição

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização de um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de julgamento da AD Azure, pode ter um julgamento de um mês [aqui.](https://azure.microsoft.com/pricing/free-trial/)

## <a name="step-1-plan-your-provisioning-deployment"></a>Passo 1. Planeie a sua implantação de provisionamento
1. Saiba como funciona o serviço de [provisionamento.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)
2. Determinar quem estará no [âmbito do provisionamento.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)
3. Determine quais os dados a [mapear entre o Azure AD e o Workplace pelo Facebook](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes).

## <a name="step-2-configure-workplace-by-facebook-to-support-provisioning-with-azure-ad"></a>Passo 2. Configure Local de Trabalho pelo Facebook para apoiar o provisionamento com a AD Azure

Antes de configurar e ativar o serviço de provisionamento, precisa decidir quais os utilizadores e/ou grupos em Azure AD que representam os utilizadores que precisam de acesso ao seu Workplace através da aplicação facebook. Uma vez decidido, pode atribuir estes utilizadores ao seu Workplace através da aplicação Facebook seguindo as instruções aqui:

*   Recomenda-se que um único utilizador da AD Azure seja designado para o Local de Trabalho pelo Facebook para testar a configuração de provisionamento. Posteriormente, os utilizadores e/ou grupos adicionais podem ser atribuídos.

*   Ao atribuir um utilizador ao Workplace pelo Facebook, deve selecionar uma função de utilizador válida. A função "Acesso Predefinido" não funciona para o provisionamento.

## <a name="step-3-add-workplace-by-facebook-from-the-azure-ad-application-gallery"></a>Passo 3. Adicionar Workplace by Facebook da galeria de aplicações Azure AD

Adicione workplace by Facebook da galeria de aplicações Azure AD para começar a gerir o provisionamento para o Workplace pelo Facebook. Se já configurar o Workplace by Facebook para SSO, pode utilizar a mesma aplicação. No entanto, recomenda-se que crie uma aplicação separada ao testar a integração inicialmente. Saiba mais sobre a adição de uma aplicação na galeria [aqui.](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passo 4. Definir quem estará no âmbito do provisionamento 

O serviço de provisionamento de AD Azure permite-lhe examinar quem será provisionado com base na atribuição à aplicação e ou com base em atributos do utilizador/grupo. Se optar por examinar quem será aprovisionado na sua app com base na atribuição, pode utilizar os [seguintes passos](../manage-apps/assign-user-or-group-access-portal.md) para atribuir utilizadores e grupos à aplicação. Se optar por examinar quem será aprovisionado apenas com base em atributos do utilizador ou do grupo, pode utilizar um filtro de deteção como descrito [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Ao atribuir utilizadores e grupos ao Workplace pelo Facebook, deve selecionar uma função diferente do **Acesso Padrão**. Os utilizadores com a função de Acesso Predefinido estão excluídos do fornecimento e serão marcados como não tendo direito efetivamente nos registos de fornecimento. Se a única função disponível na aplicação for a função de acesso padrão, pode [atualizar o manifesto de aplicação](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) para adicionar funções adicionais. 

* Comece minúsculo. Teste com um pequeno conjunto de utilizadores e grupos antes de passar para todos. Quando o âmbito de fornecimento for definido para utilizadores e grupos atribuídos, pode controlá-lo atribuindo um ou dois utilizadores ou grupos à aplicação. Quando o âmbito é definido para todos os utilizadores e grupos, pode especificar um filtro de [deteção baseado em atributos](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

1. Inicie sessão no [Portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais**e, em seguida, selecione **Todas as aplicações**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Workplace by Facebook**.

    ![O workplace by Facebook link na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Provisioning.**

    ![Guia de provisionamento](common/provisioning.png)

4. Detete o **modo de provisionamento** para **automático**.

    ![Guia de provisionamento](common/provisioning-automatic.png)

5. Na secção **credenciais de administrador,** clique em **Autorizar**. Será redirecionado para o Local de Trabalho pela página de autorização do Facebook. Insera o seu Workplace pelo nome de utilizador do Facebook e clique no botão **Continuar.** Clique em **Ligação** de Teste para garantir que o Azure AD pode ligar-se ao Local de Trabalho pelo Facebook. Se a ligação falhar, certifique-se de que a sua conta de Local de Trabalho através do Facebook tem permissões de Administrador e tente novamente.

    ![provisionamento](./media/workplacebyfacebook-provisioning-tutorial/provisioning.png)

    ![autorizar](./media/workplacebyfacebook-provisioning-tutorial/workplacelogin.png)

6. No campo de email de **notificação,** introduza o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de fornecimento e selecione a **notificação de e-mail enviar uma notificação de e-mail quando ocorrer uma falha** verificar a caixa.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Selecione **Guardar**.

8. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Users to Workplace by Facebook**.

9. Reveja os atributos do utilizador que são sincronizados de Azure AD para Workplace pelo Facebook na secção **Attribute-Mapping.** Os atributos selecionados como propriedades **Correspondentes** são usados para combinar as contas de utilizador no Local de Trabalho pelo Facebook para operações de atualização. Se optar por alterar o [atributo-alvo correspondente,](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)terá de garantir que o Workplace by Facebook API suporta filtrar os utilizadores com base nesse atributo. Selecione o botão **Guardar** para elegiro qualquer alteração.

   |Atributo|Tipo|
   |---|---|
   |userName|Cadeia|
   |displayName|Cadeia|
   |ativo|Booleano|
   |título|Booleano|
   |e-mails[tipo eq "work"].valor|Cadeia|
   |nome.dadoNome|Cadeia|
   |nome.familyName|Cadeia|
   |nome.formatado|Cadeia|
   |endereços[tipo eq "trabalho"].formado|Cadeia|
   |endereços[tipo eq "trabalho"].streetAddress|Cadeia|
   |endereços[tipo eq "trabalho"].localidade|Cadeia|
   |endereços[tipo eq "trabalho"].região|Cadeia|
   |endereços[tipo eq "trabalho"].país|Cadeia|
   |endereços[tipo eq "trabalho"].código postal|Cadeia|
   |endereços[tipo eq "outros"].formados|Cadeia|
   |telefoneNumbers[tipo eq "trabalho"].valor|Cadeia|
   |telefoneNumbers[tipo eq "mobile"].valor|Cadeia|
   |telefoneNumbers[tipo eq "fax"].valor|Cadeia|
   |id externo|Cadeia|
   |língua preferida|Cadeia|
   |urn:ietf:params:scim:schemas:extens:enterprise:2.0:User:manager|Cadeia|
   |urn:ietf:params:scim:schemas:extens:enterprise:2.0:User:department|Cadeia|

10. Para configurar filtros de deteção, consulte as seguintes instruções fornecidas no tutorial do [filtro Descodificação](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para ativar o serviço de provisionamento de AD Azure para o Local de Trabalho pelo Facebook, altere o Estado de **Provisionamento** para **On** na secção **Definições.**

    ![Estatuto de provisionamento Alternado](common/provisioning-toggle-on.png)

12. Defina os utilizadores e/ou grupos que gostaria de fornecer ao Workplace pelo Facebook, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de provisionamento](common/provisioning-scope.png)

13. Quando estiver pronto para fornecer, clique em **Guardar**.

    ![Configuração de fornecimento de poupança](common/provisioning-configuration-save.png)

Esta operação inicia o ciclo inicial de sincronização de todos os utilizadores e grupos definidos no **Âmbito** na secção **Definições.** O ciclo inicial demora mais tempo a realizar do que os ciclos subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento da AD Azure esteja em funcionamento. 

## <a name="step-6-monitor-your-deployment"></a>Passo 6. Monitorizar a implementação
Depois de configurar o fornecimento, utilize os seguintes recursos para monitorizar a sua implementação:

1. Utilize os registos de [provisionamento](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) para determinar quais os utilizadores que foram provisionados com sucesso ou sem sucesso
2. Verifique a barra de [progresso](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) para ver o estado do ciclo de provisionamento e quão perto está da conclusão
3. Se a configuração do fornecimento parecer estar num estado pouco saudável, a aplicação entrará em quarentena. Saiba mais sobre estados de quarentena [aqui.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)

## <a name="troubleshooting-tips"></a>Sugestões de resolução de problemas
*  Se vir um utilizador sem sucesso criado e houver um evento de registo de auditoria com o código "1789003", significa que o utilizador é de um domínio não verificado.

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do provisionamento de conta de utilizador para aplicações empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)
