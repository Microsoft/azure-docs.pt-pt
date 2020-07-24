---
title: 'Tutorial: Fornecimento de utilizadores para Slack - Azure AD'
description: Saiba como configurar o Azure Ative Directory para provisão automática e desa provisionar contas de utilizadores à Slack.
services: active-directory
documentationcenter: ''
author: ArvindHarinder1
manager: CelesteDG
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/06/2020
ms.author: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5ca536ddacb0f81459625b733eb79282e145afba
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87016344"
---
# <a name="tutorial-configure-slack-for-automatic-user-provisioning"></a>Tutorial: Configure Slack para o fornecimento automático de utilizadores

O objetivo deste tutorial é mostrar-lhe os passos que precisa de executar em Slack e Azure AD para automaticamente fornecendo e desresaguindo contas de utilizadores de Azure AD a Slack. Para obter detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte [automatizar o fornecimento e desprovisionamento de aplicações saaS com diretório Azure Ative.](../manage-apps/user-provisioning.md) 


## <a name="capabilities-supported"></a>Capacidades suportadas
> [!div class="checklist"]
> * Criar utilizadores em Slack
> * Remova os utilizadores em Slack quando já não necessitam de acesso
> * Mantenha os atributos do utilizador sincronizados entre Azure AD e Slack
> * Grupos de provisão e membros do grupo em Slack
> * [Único sinal de](https://docs.microsoft.com/azure/active-directory/saas-apps/slack-tutorial) Slack (recomendado)


## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes itens:

* [Um inquilino da AD Azure.](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)
* Uma conta de utilizador em Azure AD com [permissão](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) para configurar o provisionamento (por exemplo, Administrador de Aplicação, Administrador de Aplicação cloud, Proprietário de Aplicações ou Administrador Global).
* Um inquilino slack com o [plano Plus](https://aadsyncfabric.slack.com/pricing) ou melhor habilitado.
* Uma conta de utilizador em Slack com permissões de Administração da Equipa.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passo 1. Planeie a sua implantação de provisionamento
1. Saiba [como funciona o serviço de prestação de serviços.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)
2. Determinar quem estará no [âmbito do provisionamento](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Determine quais os dados a [mapear entre Azure AD e Slack](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-add-slack-from-the-azure-ad-application-gallery"></a>Passo 2. Adicione Slack da galeria de aplicações AZure AD

Adicione Slack da galeria de aplicações Azure AD para começar a gerir o fornecimento à Slack. Se tiver configurado anteriormente Slack para SSO, pode utilizar a mesma aplicação. No entanto, recomenda-se que crie uma aplicação separada ao testar inicialmente a integração. Saiba mais sobre a adição de uma aplicação na galeria [aqui.](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) 

## <a name="step-3-define-who-will-be-in-scope-for-provisioning"></a>Passo 3. Definir quem estará no âmbito do provisionamento 

O serviço de prestação de Ad Azure permite-lhe atear âmbito a quem será a provisionado com base na atribuição à aplicação e ou com base em atributos do utilizador/grupo. Se optar por escolher o âmbito de aplicação de quem será aprovisionado na sua aplicação com base na atribuição, pode utilizar os [seguintes passos](../manage-apps/assign-user-or-group-access-portal.md) para atribuir utilizadores e grupos à aplicação. Se optar por escolher o âmbito de aplicação de quem será a provisionado apenas com base em atributos do utilizador ou grupo, pode utilizar um filtro de deteção como descrito [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Ao atribuir utilizadores e grupos ao Slack, tem de selecionar outra função que não o **Acesso Predefinido**. Os utilizadores com a função De Acesso Predefinido estão excluídos do provisionamento e serão marcados como não efetivamente intitulados nos registos de provisionamento. Se a única função disponível na aplicação for a função de acesso predefinido, pode [atualizar o manifesto de aplicação](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) para adicionar funções adicionais. 

* Comece minúsculo. Teste com um pequeno conjunto de utilizadores e grupos antes de rolar para todos. Quando o âmbito de provisão é definido para utilizadores e grupos atribuídos, pode controlá-lo atribuindo um ou dois utilizadores ou grupos à aplicação. Quando o âmbito é definido para todos os utilizadores e grupos, pode especificar um [filtro de deteção baseado no atributo](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).

## <a name="step-4-configure-automatic-user-provisioning-to-slack"></a>Passo 4: Configure o fornecimento automático de utilizadores ao Slack 

Esta secção guia-o através da ligação do seu AD Azure à conta de utilizador da Slack que fornece a API, e configura o serviço de fornecimento para criar, atualizar e desativar as contas de utilizador atribuídas em Slack com base na atribuição de utilizadores e grupos em Azure AD.

### <a name="to-configure-automatic-user-account-provisioning-to-slack-in-azure-ad"></a>Para configurar o fornecimento automático de conta de utilizador à Slack em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Slack**.

    ![O link Slack na lista de Aplicações](common/all-applications.png)

3. Selecione o **separador Provisioning.**

    ![Separador de provisionamento](common/provisioning.png)

4. Desa ajuste o **modo de provisionamento** para **automático**.

    ![Separador de provisionamento](common/provisioning-automatic.png)

5. Na secção **Credenciais de Administração,** clique **em Autorizor**. Isto abre um diálogo de autorização Slack numa nova janela do navegador.

    ![Autorização](media/slack-provisioning-tutorial/authorization.png)


6. Na nova janela, inscreva-se na Slack utilizando a sua conta Team Admin. no diálogo de autorização resultante, selecione a equipa Slack para a quais pretende permitir o provisionamento e, em seguida, **selecione Authorize**. Uma vez concluído, volte ao portal Azure para completar a configuração de provisionamento.

    ![Diálogo de Autorização](./media/slack-provisioning-tutorial/slackauthorize.png)

7. No portal Azure, clique em **Test Connection** para garantir que o Azure AD pode ligar-se à sua aplicação Slack. Se a ligação falhar, certifique-se de que a sua conta Slack tem permissões de Administração da Equipa e tente novamente o passo "Autorizar".

8. No campo **'Email' de Notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e selecione a **notificação enviar uma notificação por e-mail quando ocorrer uma falha** na caixa de verificação.

    ![E-mail de notificação](common/provisioning-notification-email.png)

9. Selecione **Guardar**.

10. Na secção Mappings, selecione **Synchronize Azure Ative Directory Users to Slack**.

11. Na secção **De Mapeamentos de Atributos,** reveja os atributos do utilizador que serão sincronizados de Azure AD a Slack. Note que os atributos selecionados como propriedades **de correspondência** serão utilizados para combinar as contas do utilizador em Slack para operações de atualização. Selecione o botão Guardar para escoar quaisquer alterações.

   |Atributo|Tipo|
   |---|---|
   |ativo|Booleano|
   |externoId|Cadeia|
   |displayName|Cadeia|
   |nome.famíliaName|Cadeia|
   |nome.dado Nome|Cadeia|
   |título|Cadeia|
   |e-mails[tipo eq "work"].value|Cadeia|
   |userName|Cadeia|
   |nickName|Cadeia|
   |endereços[tipo eq "untyped"].streetAddress|Cadeia|
   |endereços[tipo eq "untyped"].localidade|Cadeia|
   |endereços[tipo eq "untyped"].região|Cadeia|
   |endereços[tipo eq "untyped"].postalCode|Cadeia|
   |endereços[tipo eq "untyped"].país|Cadeia|
   |números de telefone[tipo eq "mobile"].valor|Cadeia|
   |phoneNumbers[tipo eq "work"].value|Cadeia|
   |papéis[eq primário "Verdadeiro"].valor|Cadeia|
   |região|Cadeia|
   |nome.honorificPrefix|Cadeia|
   |fotos[tipo eq "foto"].valor|Cadeia|
   |profileUrl|Cadeia|
   |timezone|Cadeia|
   |userType|Cadeia|
   |urn:scim:schemas:extensão:enterprise:1.0.department|Cadeia|
   |urn:scim:schemas:extensão:enterprise:1.0.manager|Referência|
   |urn:scim:schemas:extensão:enterprise:1.0.employeeNumber|Cadeia|
   |urn:scim:schemas:extensão:enterprise:1.0.costCenter|Cadeia|
   |urn:scim:schemas:extensão:enterprise:1.0.organization|Cadeia|
   |urn:scim:schemas:extensão:enterprise:1.0.division|Cadeia|

12. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Groups to Slack**.

13. Na secção **De mapeamentos de atributos,** reveja os atributos do grupo que serão sincronizados de Azure AD a Slack. Note que os atributos selecionados como propriedades **de correspondência** serão utilizados para combinar com os grupos em Slack para operações de atualização. Selecione o botão Guardar para escoar quaisquer alterações.

      |Atributo|Tipo|
      |---|---|
      |displayName|Cadeia|
      |membros|Referência|

14. Para configurar filtros de deteção, consulte as seguintes instruções fornecidas no tutorial do [filtro de escotagem](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Para ativar o serviço de prestação de Ad Azure para a Slack, altere o **Estado de Provisionamento** para **On** na secção **Definições**

    ![Estatuto de Provisionamento Toggled On](common/provisioning-toggle-on.png)

16. Defina os utilizadores e/ou grupos que deseja prestar à Slack, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de provisionamento](common/provisioning-scope.png)

17. Quando estiver pronto para a provisão, clique em **Guardar**.

    ![Configuração de provisionamento de poupança](common/provisioning-configuration-save.png)

Esta operação inicia o ciclo inicial de sincronização de todos os utilizadores e grupos definidos no **Âmbito** na secção **Definições.** O ciclo inicial demora mais tempo a ser efetuado do que os ciclos subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de fornecimento AZure AD esteja em funcionamento. 

## <a name="step-5-monitor-your-deployment"></a>Passo 5. Monitorizar a implementação
Depois de configurar o provisionamento, utilize os seguintes recursos para monitorizar a sua implantação:

1. Utilize os [registos de provisionamento](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) para determinar quais os utilizadores que foram a provisionados com sucesso ou sem sucesso
2. Verifique a [barra de progresso](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) para ver o estado do ciclo de provisionamento e quão perto está da sua conclusão
3. Se a configuração do provisionamento parecer estar num estado pouco saudável, a aplicação entrará em quarentena. Saiba mais sobre estados de quarentena [aqui.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)

## <a name="troubleshooting-tips"></a>Sugestões de Resolução de Problemas

* Ao configurar o atributo de nome de **exibição** de Slack, esteja ciente dos seguintes comportamentos:

  * Os valores não são inteiramente únicos (por exemplo, 2 utilizadores podem ter o mesmo nome de ecrã)

  * Suporta caracteres não ingleses, espaços, capitalização. 
  
  * A pontuação permitida inclui períodos, sublinhados, hífens, apóstrofos, suportes **(por exemplo, [ { } )** e separadores (por **exemplo, / .**
  
  * displayName propriedade não pode ter um caráter '@'. Se um '@' estiver incluído, poderá encontrar um evento ignorado nos registos de provisionamento com a descrição "AttributeValidationFailed".

  * Apenas atualizações se estas duas definições estiverem configuradas no local de trabalho/organização da Slack - A sincronização de **perfis está ativada** e os **utilizadores não podem alterar o seu nome de exibição**.

* O **atributo de nome** de utilizador da Slack tem de ter menos de 21 caracteres e ter um valor único.

* A folga só permite combinar com os atributos **userName** e **e-mail**.  
  
* Os códigos erorr comuns são documentados na documentação oficial da Slack -https://api.slack.com/scim#errors

## <a name="change-log"></a>Change log

* 06/16/2020 - Atributo DisplayName modificado só deve ser atualizado durante a criação do novo utilizador.

## <a name="additional-resources"></a>Recursos Adicionais

* [Gestão do fornecimento de conta de utilizador para apps empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)