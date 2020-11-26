---
title: 'Tutorial: Aprovisionamento de utilizadores para o Slack – Azure AD'
description: Saiba como configurar o Azure Active Directory para aprovisionar e desaprovisionar automaticamente contas de utilizador para o Slack.
services: active-directory
author: ArvindHarinder1
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/06/2020
ms.author: arvinh
ms.openlocfilehash: 92c2ae13b840d7a73d86365ce88584bcafc878e8
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96181463"
---
# <a name="tutorial-configure-slack-for-automatic-user-provisioning"></a>Tutorial: Configurar o Slack para o aprovisionamento automático de utilizadores

O objetivo deste tutorial é mostrar os passos que precisam de ser realizados no Slack e no Azure AD para aprovisionar e desaprovisionar automaticamente contas de utilizador do Azure AD para o Slack. Para obter detalhes importantes sobre o que este serviço faz, como funciona e perguntas frequentes, veja [Automatizar o aprovisionamento e desaprovisionamento de utilizadores em aplicações SaaS no Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Capacidades suportadas
> [!div class="checklist"]
> * Criar utilizadores no Slack
> * Remover utilizadores no Slack quando não precisam mais do acesso
> * Manter os atributos de utilizador sincronizados entre o Azure AD e o Slack
> * Aprovisionar grupos e associações a grupos no Slack
> * [Início de sessão único](./slack-tutorial.md) no Slack (recomendado)


## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que já tem os seguintes itens:

* [Um inquilino do Azure AD](../develop/quickstart-create-new-tenant.md).
* Uma conta de utilizador no Azure AD com [permissão](../roles/permissions-reference.md) para configurar o aprovisionamento (por ex., Administrador de Aplicações, Administrador de Aplicações de Cloud, Proprietário da Aplicação ou Administrador Global).
* Um inquilino do Slack com o [plano Plus](https://aadsyncfabric.slack.com/pricing) ou melhor ativado.
* Uma conta de utilizador no Slack com permissões de Administrador de Equipa.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passo 1. Planear a sua implementação de aprovisionamento
1. Saiba [como funciona o serviço de aprovisionamento](../app-provisioning/user-provisioning.md).
2. Determine quem vai estar no [âmbito do aprovisionamento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determine quais os dados a [mapear entre o Azure AD e o Slack](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-add-slack-from-the-azure-ad-application-gallery"></a>Passo 2. Adicionar o Slack a partir da galeria de aplicações do Azure AD

Adicione o Slack a partir da galeria de aplicações do Azure AD para começar a gerir o aprovisionamento para o Slack. Se tiver configurado anteriormente o Slack para SSO, pode utilizar a mesma aplicação. No entanto, é recomendável criar uma aplicação separada ao testar a integração inicialmente. Saiba mais sobre como adicionar uma aplicação a partir da galeria [aqui](../manage-apps/add-application-portal.md). 

## <a name="step-3-define-who-will-be-in-scope-for-provisioning"></a>Passo 3. Determinar quem vai estar no âmbito do aprovisionamento 

O serviço de aprovisionamento do Azure AD permite-lhe determinar quem vai ser aprovisionado com base na atribuição à aplicação e/ou com base em atributos do utilizador/grupo. Se optar por determinar quem vai ser aprovisionado na sua aplicação com base na atribuição, pode utilizar os seguintes [passos](../manage-apps/assign-user-or-group-access-portal.md) para atribuir utilizadores e grupos à aplicação. Se escolher determinar quem vai ser aprovisionado com base apenas em atributos do utilizador ou grupo, pode utilizar um filtro de âmbito conforme descrito [aqui](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Ao atribuir utilizadores e grupos ao Slack, deve selecionar uma função diferente de **Acesso Predefinido**. Os utilizadores com a função Acesso Predefinido são excluídos do aprovisionamento e marcados como não autorizados de forma efetiva nos registos de aprovisionamento. Se a única função disponível na aplicação for a função de acesso predefinido, pode [atualizar o manifesto de aplicação](../develop/howto-add-app-roles-in-azure-ad-apps.md) para adicionar funções adicionais. 

* Comece pequeno. Teste com um pequeno conjunto de utilizadores e grupos antes de implementar para todos. Quando o âmbito do aprovisionamento está definido para os utilizadores e os grupos atribuídos, pode controlar isto ao atribuir um ou dois utilizadores ou grupos à aplicação. Quando o âmbito está definido para todos os utilizadores e grupos, pode especificar um [filtro de âmbito baseado em atributos](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

## <a name="step-4-configure-automatic-user-provisioning-to-slack"></a>Passo 4: Configurar o aprovisionamento automático de utilizadores para o Slack 

Esta secção orienta-o ao longo da ligação do Azure AD à API de aprovisionamento de contas de utilizador do Slack e da configuração do serviço de aprovisionamento para criar, atualizar e desativar contas de utilizador atribuídas no Slack com base na atribuição de utilizadores e grupos no Azure AD.

### <a name="to-configure-automatic-user-account-provisioning-to-slack-in-azure-ad"></a>Para configurar o aprovisionamento automático de contas de utilizador para o Slack no Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais** e, em seguida, **Todas as aplicações**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Slack**.

    ![A ligação do Slack na lista Aplicações](common/all-applications.png)

3. Selecione o separador **Aprovisionamento**.

    ![Screenshot das opções De gestão com a opção Provisioning chamada.](common/provisioning.png)

4. Defina o **Modo de Aprovisionamento** como **Automático**.

    ![Screenshot da lista de retirada do modo de provisionamento com a opção Automática chamada.](common/provisioning-automatic.png)

5. Na secção **Credenciais de Administrador**, clique em **Autorizar**. Esta ação abre uma caixa de diálogo de autorização do Slack numa nova janela do browser.

    ![A captura de ecrã mostra o botão Autorizar Credenciais de Administrador.](media/slack-provisioning-tutorial/authorization.png)


6. Na nova janela, inicie sessão no Slack com a sua conta de Administrador de Equipa. Na caixa de diálogo de autorização resultante, selecione a equipa do Slack para a qual pretende ativar o aprovisionamento e, em seguida, selecione **Autorizar**. Depois de concluído, regresse ao portal do Azure para concluir a configuração do aprovisionamento.

    ![Caixa de Diálogo de Autorização](./media/slack-provisioning-tutorial/slackauthorize.png)

7. No portal do Azure, clique em **Testar Ligação** para garantir que o Azure AD consegue ligar à sua aplicação Slack. Se a ligação falhar, confirme que a sua conta do Slack tem permissões de Administrador de Equipa e tente novamente o passo "Autorizar".

8. No campo **E-mail de Notificação**, introduza o endereço de e-mail de uma pessoa ou um grupo que deve receber as notificações de erro de aprovisionamento e marque a caixa de verificação **Enviar uma notificação de e-mail quando ocorre uma falha**.

    ![E-mail de Notificação](common/provisioning-notification-email.png)

9. Selecione **Guardar**.

10. Na secção Mapeamentos, selecione **Sincronizar Utilizadores do Azure Active Directory com o Slack**.

11. Na secção **Mapeamentos de Atributos**, reveja os atributos de utilizadores que vão ser sincronizados a partir do Azure AD para o Slack. Observe que os atributos selecionados como propriedades **Correspondentes** vão servir para fazer corresponder as contas de utilizador no Slack para operações de atualização. Selecione o botão Guardar para confirmar as alterações.

   |Atributo|Tipo|
   |---|---|
   |active|Booleano|
   |externalId|String|
   |displayName|String|
   |name.familyName|String|
   |name.givenName|String|
   |título|String|
   |emails[type eq "work"].value|String|
   |userName|String|
   |nickName|String|
   |addresses[type eq "untyped"].streetAddress|String|
   |addresses[type eq "untyped"].locality|String|
   |addresses[type eq "untyped"].region|String|
   |addresses[type eq "untyped"].postalCode|String|
   |addresses[type eq "untyped"].country|String|
   |phoneNumbers[type eq "mobile"].value|String|
   |phoneNumbers[type eq "work"].value|String|
   |roles[primary eq "True"].value|String|
   |região|String|
   |name.honorificPrefix|String|
   |photos[type eq "photo"].value|String|
   |profileUrl|String|
   |timezone|String|
   |userType|String|
   |urn:scim:schemas:extension:enterprise:1.0.department|String|
   |urn:scim:schemas:extension:enterprise:1.0.manager|Referência|
   |urn:scim:schemas:extension:enterprise:1.0.employeeNumber|String|
   |urn:scim:schemas:extension:enterprise:1.0.costCenter|String|
   |urn:scim:schemas:extension:enterprise:1.0.organization|String|
   |urn:scim:schemas:extension:enterprise:1.0.division|String|

12. Na secção **Mapeamentos**, selecione **Sincronizar Grupos do Azure Active Directory com o Slack**.

13. Na secção **Mapeamentos de Atributos**, reveja os atributos de grupos que vão ser sincronizados a partir do Azure AD para o Slack. Observe que os atributos selecionados como propriedades **Correspondentes** vão servir para fazer corresponder os grupos no Slack para operações de atualização. Selecione o botão Guardar para confirmar as alterações.

      |Atributo|Tipo|
      |---|---|
      |displayName|String|
      |membros|Referência|

14. Para configurar filtros de âmbito, veja as instruções seguintes disponibilizadas no [Tutorial de filtro de âmbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Para ativar o serviço de aprovisionamento do Azure AD para o Slack, altere o **Estado do Aprovisionamento** para **Ativado** na secção **Definições**

    ![Estado do Aprovisionamento Ativado](common/provisioning-toggle-on.png)

16. Defina os utilizadores e/ou grupos que gostaria de aprovisionar para o Slack ao escolher os valores desejados em **Âmbito** na secção **Definições**.

    ![Âmbito de Aprovisionamento](common/provisioning-scope.png)

17. Quando estiver pronto para aprovisionar, clique em **Guardar**.

    ![Guardar Configuração de Aprovisionamento](common/provisioning-configuration-save.png)

Esta operação inicia o ciclo de sincronização inicial de todos os utilizadores e grupos definidos no **Âmbito** na secção **Definições**. O ciclo inicial leva mais tempo a ser executado do que os ciclos subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de aprovisionamento do Azure AD esteja em execução. 

## <a name="step-5-monitor-your-deployment"></a>Passo 5. Monitorizar a implementação
Depois de configurar o aprovisionamento, utilize os seguintes recursos para monitorizar a sua implementação:

1. Utilize os [registos de aprovisionamento](../reports-monitoring/concept-provisioning-logs.md) para determinar quais os utilizadores que foram aprovisionados com ou sem êxito
2. Verifique a [barra de progresso](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) para ver o estado do ciclo de aprovisionamento e quão próximo está da conclusão
3. Se a configuração de aprovisionamento parecer estar num mau estado de funcionamento, a aplicação vai entrar em quarentena. Saiba mais sobre os estados de quarentena [aqui](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="troubleshooting-tips"></a>Sugestões de Resolução de Problemas

* Ao configurar o atributo **displayName** do Slack, esteja ciente dos seguintes comportamentos:

  * Os valores não são totalmente exclusivos (por ex., 2 utilizadores podem ter o mesmo nome a apresentar)

  * Suporta carateres não ingleses, espaços e maiúsculas/minúsculas. 
  
  * A pontuação permitida inclui pontos finais, carateres de sublinhado, hífenes, apóstrofes, parênteses (por ex., **( [ { } ] )** ) e separadores (por ex., **, / ;** ).
  
  * A propriedade displayName não pode ter um caráter "@". Se um "@" estiver incluído, pode encontrar um evento ignorado nos registos de aprovisionamento com a descrição "AttributeValidationFailed".

  * Apenas atualiza se estas duas definições forem configuradas no local de trabalho/organização do Slack – **Sincronização do perfil ativada** e **Os utilizadores não podem alterar o respetivo nome a apresentar**.

* O atributo **userName** do Slack deve ter menos de 21 carateres e um valor exclusivo.

* O Slack permite apenas correspondência com os atributos **userName** e **email**.  
  
* Os códigos de erro comuns estão documentados na documentação oficial do Slack – https://api.slack.com/scim#errors

## <a name="change-log"></a>Change log

* 16/06/2020 – Atributo DisplayName modificado para ser atualizado apenas durante a criação de um novo utilizador.

## <a name="additional-resources"></a>Recursos Adicionais

* [Gerir o aprovisionamento de contas de utilizador para Aplicações Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como analisar os registos e obter relatórios sobre a atividade de aprovisionamento](../app-provisioning/check-status-user-account-provisioning.md)