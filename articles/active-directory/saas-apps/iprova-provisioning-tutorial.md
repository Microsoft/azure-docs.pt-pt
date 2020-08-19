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
ms.openlocfilehash: 1d0f5c974b19b83cdd2fdab832ae0cac0dab9d9a
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88552870"
---
# <a name="tutorial-configure-iprova-for-automatic-user-provisioning"></a>Tutorial: Configurar iProva para fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados no iProva e no Azure Ative Directory (Azure AD) para configurar a Azure AD para provisão automática e desaprovisionamento de utilizadores e/ou grupos para [o iProva](https://www.iProva.com/). Para obter detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte [automatizar o fornecimento e desprovisionamento de aplicações saaS com diretório Azure Ative.](../manage-apps/user-provisioning.md) 

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

* [Um inquilino da AD Azure.](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)
* Uma conta de utilizador em Azure AD com [permissão](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) para configurar o provisionamento (por exemplo, Administrador de Aplicação, Administrador de Aplicação cloud, Proprietário de Aplicações ou Administrador Global).
* [Um inquilino da iProva.](https://www.iProva.com/)
* Uma conta de utilizador no iProva com permissões de Administração.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passo 1. Planeie a sua implantação de provisionamento
1. Saiba [como funciona o serviço de prestação de serviços.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)
2. Determinar quem estará no [âmbito do provisionamento](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
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

Adicione iProva da galeria de aplicações AZure AD para começar a gerir o provisionamento ao iProva. Se tiver configurado previamente o iProva para SSO, pode utilizar a mesma aplicação. No entanto, recomenda-se que crie uma aplicação separada ao testar inicialmente a integração. Saiba mais sobre a adição de uma aplicação na galeria [aqui.](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passo 4: Definir quem estará no âmbito do provisionamento 

O serviço de prestação de Ad Azure permite-lhe atear âmbito a quem será a provisionado com base na atribuição à aplicação e ou com base em atributos do utilizador/grupo. Se optar por escolher o âmbito de aplicação de quem será aprovisionado na sua aplicação com base na atribuição, pode utilizar os [seguintes passos](../manage-apps/assign-user-or-group-access-portal.md) para atribuir utilizadores e grupos à aplicação. Se optar por escolher o âmbito de aplicação de quem será a provisionado apenas com base em atributos do utilizador ou grupo, pode utilizar um filtro de deteção como descrito [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Ao atribuir utilizadores e grupos ao iProva, tem de selecionar outra função que não o **Acesso Predefinido.** Os utilizadores com a função De Acesso Predefinido estão excluídos do provisionamento e serão marcados como não efetivamente intitulados nos registos de provisionamento. Se a única função disponível na aplicação for a função de acesso predefinido, pode [atualizar o manifesto de aplicação](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) para adicionar funções adicionais. 

* Comece minúsculo. Teste com um pequeno conjunto de utilizadores e grupos antes de rolar para todos. Quando o âmbito de provisão é definido para utilizadores e grupos atribuídos, pode controlá-lo atribuindo um ou dois utilizadores ou grupos à aplicação. Quando o âmbito é definido para todos os utilizadores e grupos, pode especificar um [filtro de deteção baseado no atributo](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

## <a name="step-5-configure-automatic-user-provisioning-to-iprova"></a>Passo 5. Configure o fornecimento automático de utilizadores ao iProva 

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos no iProva com base em atribuições de utilizador e/ou grupo em Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-iprova-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para iProva em Ad AZure:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **iProva**.

    ![O link iProva na lista de Aplicações](common/all-applications.png)

3. Selecione o **separador Provisioning.**

    ![Separador de provisionamento](common/provisioning.png)

4. Desa ajuste o **modo de provisionamento** para **automático**.

    ![Separador de provisionamento](common/provisioning-automatic.png)

5. Sob a secção **credenciais de administração,** insira os valores **DE URL base SCIM 2.0 e Token Permanente** recuperados anteriormente nos campos URL e Secret **Token** do **arrendatário,** respectivamente. Clique em **Testar A Ligação** para garantir que a Azure AD pode ligar-se ao iProva. Se a ligação falhar, certifique-se de que a sua conta iProva tem permissões de Administração e tente novamente.

    ![INQUILINO URL + Token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **'Email' de Notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e verifique a caixa de verificação - **Envie uma notificação de e-mail quando ocorrer uma falha**.

    ![E-mail de notificação](common/provisioning-notification-email.png)

7. Clique em **Guardar**.

8. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users para iProva**.

9. Reveja os atributos do utilizador que são sincronizados de Azure AD para iProva na secção **De Mapeamento de Atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas do utilizador no iProva para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

   |Atributo|Tipo|
   |---|---|
   |ativo|Booleano|
   |displayName|String|
   |título|String|
   |e-mails[tipo eq "work"].value|String|
   |preferiuLanguage|String|
   |userName|String|
   |endereços[tipo eq "work"].país|String|
   |endereços[tipo eq "work"].localidade|String|
   |endereços[tipo eq "work"].postalCode|String|
   |endereços[tipo eq "work"].formatado|String|
   |endereços[tipo eq "work"].região|String|
   |endereços[tipo eq "work"].streetAddress|String|
   |endereços[tipo eq "outros"].formatados|String|
   |nome.dado Nome|String|
   |nome.famíliaName|String|
   |nome.formatado|String|
   |números de telefone[tipo eq "fax"].valor|String|
   |números de telefone[tipo eq "mobile"].valor|String|
   |phoneNumbers[tipo eq "work"].value|String|
   |externoId|String|
   |papéis[eq primário "True"].display|String|
   |papéis[eq primário "Verdadeiro"].tipo|String|
   |papéis[eq primário "Verdadeiro"].valor|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:organization|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|String|


10. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Groups para iProva**.

11. Reveja os atributos do grupo que são sincronizados de Azure AD para iProva na secção **De Mapeamento de Atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar com os grupos no iProva para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

      |Atributo|Tipo|
      |---|---|
      |displayName|String|
      |membros|Referência|

12. Para configurar filtros de deteção, consulte as seguintes instruções fornecidas no tutorial do [filtro de escotagem](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para ativar o serviço de prestação de Ad Azure para iProva, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

    ![Estatuto de Provisionamento Toggled On](common/provisioning-toggle-on.png)

14. Defina os utilizadores e/ou grupos que deseja providenciar ao iProva, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para a provisão, clique em **Guardar**.

    ![Configuração de provisionamento de poupança](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de fornecimento AZure AD esteja em execução. 


## <a name="step-6-monitor-your-deployment"></a>Passo 6. Monitorizar a implementação
Depois de configurar o provisionamento, utilize os seguintes recursos para monitorizar a sua implantação:

1. Utilize os [registos de provisionamento](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) para determinar quais os utilizadores que foram a provisionados com sucesso ou sem sucesso
2. Verifique a [barra de progresso](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) para ver o estado do ciclo de provisionamento e quão perto está da sua conclusão
3. Se a configuração do provisionamento parecer estar num estado pouco saudável, a aplicação entrará em quarentena. Saiba mais sobre estados de quarentena [aqui.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)  

## <a name="change-log"></a>Change log

* 06/17/2020 - O atributo de extensão da empresa "Manager" foi removido.

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do fornecimento de conta de utilizador para apps empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)

