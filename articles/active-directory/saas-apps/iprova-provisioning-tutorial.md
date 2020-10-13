---
title: 'Tutorial: Configurar iProva para fornecimento automático de utilizadores com Diretório Ativo Azure / Microsoft Docs'
description: Saiba como configurar o Azure Ative Directory para provisão automática e desprovisionamento de contas de utilizadores para o iProva.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 10/29/2019
ms.author: Zhchia
ms.openlocfilehash: 1dd89198fd1c494ef3002fdc222683f56f2d7e3a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91304764"
---
# <a name="tutorial-configure-iprova-for-automatic-user-provisioning"></a>Tutorial: Configurar iProva para fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados no iProva e no Azure Ative Directory (Azure AD) para configurar a Azure AD para provisão automática e desaprovisionamento de utilizadores e/ou grupos para [o iProva](https://www.iProva.com/). Para obter detalhes importantes sobre o que este serviço faz, como funciona e perguntas frequentes, veja [Automatizar o aprovisionamento e desaprovisionamento de utilizadores em aplicações SaaS no Azure Active Directory](../manage-apps/user-provisioning.md). 

> [!NOTE]
> Este conector encontra-se atualmente em Visualização Pública. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="capabilities-supported"></a>Capacidades suportadas
> [!div class="checklist"]
> * Criar utilizadores no iProva
> * Remova os utilizadores no iProva quando já não necessitam de acesso
> * Mantenha os atributos do utilizador sincronizados entre Azure AD e iProva
> * Grupos de provisão e membros do grupo no iProva
> * [Único sinal para](https://docs.microsoft.com/azure/active-directory/saas-apps/iprova-tutorial) iProva (recomendado)

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* [Um inquilino do Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant).
* Uma conta de utilizador no Azure AD com [permissão](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) para configurar o aprovisionamento (por ex., Administrador de Aplicações, Administrador de Aplicações de Cloud, Proprietário da Aplicação ou Administrador Global).
* [Um inquilino da iProva.](https://www.iProva.com/)
* Uma conta de utilizador no iProva com permissões de Administração.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passo 1. Planear a sua implementação de aprovisionamento
1. Saiba [como funciona o serviço de aprovisionamento](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Determine quem vai estar no [âmbito do aprovisionamento](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Determine quais os dados a [mapear entre Azure AD e iProva](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-iprova-to-support-provisioning-with-azure-ad"></a>Passo 2. Configure iProva para apoiar o provisionamento com Azure AD

1. Inscreva-se na sua [consola de admin iProva](https://www.iProva.com/). Navegar para **ir a > Gestão de Aplicações.**

    ![Consola administradora iProva](media/iprova-provisioning-tutorial/admin.png)

2.  Clique na **gestão externa do utilizador.**

    ![iProva Add SCIM](media/iprova-provisioning-tutorial/external.png)

3. Para adicionar um novo fornecedor, clique no ícone **plus.** Na nova caixa de diálogo **do fornecedor Add,** forneça um **Título**. Pode optar por adicionar **a restrição de acesso baseada em IP.** Clique no botão **OK.**

    ![iProva adicionar novo](media/iprova-provisioning-tutorial/add.png)

    ![iProva adicionar fornecedor](media/iprova-provisioning-tutorial/addprovider.png)

4.  Clique no botão **de símbolo permanente.** Copie o **token Permanente** e guarde-o pois esta será a única altura em que poderá vê-lo. Este valor será introduzido no campo Secret Token no separador Provisioning da sua aplicação iProva no portal Azure.

    ![iProva Criar Token](media/iprova-provisioning-tutorial/token.png)

## <a name="step-3-add-iprova-from-the-azure-ad-application-gallery"></a>Passo 3. Adicione iProva da galeria de aplicações AD Azure

Adicione iProva da galeria de aplicações AZure AD para começar a gerir o provisionamento ao iProva. Se tiver configurado previamente o iProva para SSO, pode utilizar a mesma aplicação. No entanto, é recomendável criar uma aplicação separada ao testar a integração inicialmente. Saiba mais sobre como adicionar uma aplicação a partir da galeria [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passo 4: Determinar quem vai estar no âmbito do aprovisionamento 

O serviço de aprovisionamento do Azure AD permite-lhe determinar quem vai ser aprovisionado com base na atribuição à aplicação e/ou com base em atributos do utilizador/grupo. Se optar por determinar quem vai ser aprovisionado na sua aplicação com base na atribuição, pode utilizar os seguintes [passos](../manage-apps/assign-user-or-group-access-portal.md) para atribuir utilizadores e grupos à aplicação. Se escolher determinar quem vai ser aprovisionado com base apenas em atributos do utilizador ou grupo, pode utilizar um filtro de âmbito conforme descrito [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Ao atribuir utilizadores e grupos ao iProva, tem de selecionar outra função que não o **Acesso Predefinido.** Os utilizadores com a função Acesso Predefinido são excluídos do aprovisionamento e marcados como não autorizados de forma efetiva nos registos de aprovisionamento. Se a única função disponível na aplicação for a função de acesso predefinido, pode [atualizar o manifesto de aplicação](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) para adicionar funções adicionais. 

* Comece pequeno. Teste com um pequeno conjunto de utilizadores e grupos antes de implementar para todos. Quando o âmbito do aprovisionamento está definido para os utilizadores e os grupos atribuídos, pode controlar isto ao atribuir um ou dois utilizadores ou grupos à aplicação. Quando o âmbito está definido para todos os utilizadores e grupos, pode especificar um [filtro de âmbito baseado em atributos](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

## <a name="step-5-configure-automatic-user-provisioning-to-iprova"></a>Passo 5. Configure o fornecimento automático de utilizadores ao iProva 

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos no iProva com base em atribuições de utilizador e/ou grupo em Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-iprova-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para iProva em Ad AZure:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais** e, em seguida, **Todas as aplicações**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **iProva**.

    ![O link iProva na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Aprovisionamento**.

    ![Screenshot das opções De gestão com a opção Provisioning chamada.](common/provisioning.png)

4. Defina o **Modo de Aprovisionamento** como **Automático**.

    ![Screenshot da lista de retirada do modo de provisionamento com a opção Automática chamada.](common/provisioning-automatic.png)

5. Sob a secção **credenciais de administração,** insira os valores **DE URL base SCIM 2.0 e Token Permanente** recuperados anteriormente nos campos URL e Secret **Token** do **arrendatário,** respectivamente. Clique em **Testar A Ligação** para garantir que a Azure AD pode ligar-se ao iProva. Se a ligação falhar, certifique-se de que a sua conta iProva tem permissões de Administração e tente novamente.

    ![INQUILINO URL + Token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **'Email' de Notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e verifique a caixa de verificação - **Envie uma notificação de e-mail quando ocorrer uma falha**.

    ![E-mail de Notificação](common/provisioning-notification-email.png)

7. Clique em **Guardar**.

8. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users para iProva**.

9. Reveja os atributos do utilizador que são sincronizados de Azure AD para iProva na secção **De Mapeamento de Atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas do utilizador no iProva para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

   |Atributo|Tipo|
   |---|---|
   |active|Booleano|
   |displayName|String|
   |título|String|
   |emails[type eq "work"].value|String|
   |preferiuLanguage|String|
   |userName|String|
   |endereços[tipo eq "work"].país|Cadeia|
   |endereços[tipo eq "work"].localidade|Cadeia|
   |endereços[tipo eq "work"].postalCode|Cadeia|
   |endereços[tipo eq "work"].formatado|Cadeia|
   |endereços[tipo eq "work"].região|Cadeia|
   |endereços[tipo eq "work"].streetAddress|Cadeia|
   |endereços[tipo eq "outros"].formatados|String|
   |name.givenName|String|
   |name.familyName|String|
   |nome.formatado|Cadeia|
   |números de telefone[tipo eq "fax"].valor|String|
   |phoneNumbers[type eq "mobile"].value|String|
   |phoneNumbers[type eq "work"].value|String|
   |externalId|String|
   |papéis[eq primário "True"].display|Cadeia|
   |papéis[eq primário "Verdadeiro"].tipo|String|
   |roles[primary eq "True"].value|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|Cadeia|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|Cadeia|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|Cadeia|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:organization|Cadeia|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|Cadeia|


10. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Groups para iProva**.

11. Reveja os atributos do grupo que são sincronizados de Azure AD para iProva na secção **De Mapeamento de Atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar com os grupos no iProva para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

      |Atributo|Tipo|
      |---|---|
      |displayName|String|
      |membros|Referência|

12. Para configurar filtros de âmbito, veja as instruções seguintes disponibilizadas no [Tutorial de filtro de âmbito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para ativar o serviço de prestação de Ad Azure para iProva, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

    ![Estado do Aprovisionamento Ativado](common/provisioning-toggle-on.png)

14. Defina os utilizadores e/ou grupos que deseja providenciar ao iProva, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de Aprovisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para aprovisionar, clique em **Guardar**.

    ![Guardar Configuração de Aprovisionamento](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de fornecimento AZure AD esteja em execução. 


## <a name="step-6-monitor-your-deployment"></a>Passo 6. Monitorizar a implementação
Depois de configurar o aprovisionamento, utilize os seguintes recursos para monitorizar a sua implementação:

1. Utilize os [registos de aprovisionamento](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) para determinar quais os utilizadores que foram aprovisionados com ou sem êxito
2. Verifique a [barra de progresso](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) para ver o estado do ciclo de aprovisionamento e quão próximo está da conclusão
3. Se a configuração de aprovisionamento parecer estar num mau estado de funcionamento, a aplicação vai entrar em quarentena. Saiba mais sobre os estados de quarentena [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## <a name="change-log"></a>Change log

* 06/17/2020 - O atributo de extensão da empresa "Manager" foi removido.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento de contas de utilizador para Aplicações Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como analisar os registos e obter relatórios sobre a atividade de aprovisionamento](../app-provisioning/check-status-user-account-provisioning.md)

