---
title: 'Tutorial: Configurar 15Five para fornecimento automático de utilizadores com Diretório Ativo Azure / Microsoft Docs'
description: Aprenda a configurar o Azure Ative Directory para provisões automáticas e desavisagem de contas de utilizadores para 15Five.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: b42833056d6c9c0e6053dbf34c7de17b4136a797
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88539186"
---
# <a name="tutorial-configure-15five-for-automatic-user-provisioning"></a>Tutorial: Configurar 15Five para fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados no Diretório Ativo 15Five e Azure Ative (Ad) para configurar a Azure AD para provisão automática e desa provisionamento de utilizadores e/ou grupos a [15Five](https://www.15five.com/pricing/). Para obter detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte o fornecimento de utilizadores da Automate e desprovisionamento para aplicações SaaS com O Diretório Ativo Azure.

> [!NOTE]
> Este conector encontra-se atualmente em Visualização Pública. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="capabilities-supported"></a>Capacidades suportadas
> [!div class="checklist"]
> * Criar utilizadores em 15Five
> * Remova os utilizadores em 15Five quando já não necessitam de acesso
> * Mantenha os atributos do utilizador sincronizados entre Azure AD e 15Five
> * Grupos de provisão e membros do grupo em 15Five
> * [Único sinal de](https://docs.microsoft.com/azure/active-directory/saas-apps/15five-tutorial) 15Five (recomendado)

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* [Um inquilino da AD Azure.](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)
* Uma conta de utilizador em Azure AD com [permissão](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) para configurar o provisionamento (por exemplo, Administrador de Aplicação, Administrador de Aplicação cloud, Proprietário de Aplicações ou Administrador Global).
* [Um inquilino de 155.](https://www.15five.com/pricing/)
* Uma conta de utilizador em 15Five com permissões de Administração.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passo 1. Planeie a sua implantação de provisionamento
1. Saiba [como funciona o serviço de prestação de serviços.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)
2. Determinar quem estará no [âmbito do provisionamento](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Determine quais os dados a [mapear entre Azure AD e 15Five](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-15five-to-support-provisioning-with-azure-ad"></a>Passo 2. Configure 15Five para apoiar o provisionamento com Azure AD

Antes de configurar 15Five para o fornecimento automático de utilizadores com Azure AD, terá de ativar o fornecimento scim em 15Five.

1. Inscreva-se na sua [Consola de Administração 15Five](https://my.15five.com/). Navegue para **funcionalidades > Integrações.**

    ![Consola de administração 15Five](media/15five-provisioning-tutorial/integration.png)

2.  Clique no **SCIM 2.0**.

    ![Consola de administração 15Five](media/15five-provisioning-tutorial/image00.png)

3.  Navegue para a **integração SCIM > Gere o símbolo da OAuth**.

    ![15Five Add SCIM](media/15five-provisioning-tutorial/image02.png)

4.  Copie os valores para **SCIM 2.0 base URL** e **Access Token**. Este valor será inserido no campo **URL** e **Secret Token** no separador Provisioning da sua aplicação 15Five no portal Azure.
    
    ![15Five Add SCIM](media/15five-provisioning-tutorial/image03.png)

## <a name="step-3-add-15five-from-the-azure-ad-application-gallery"></a>Passo 3. Adicione 15Five da galeria de aplicações Azure AD

Adicione 15Five da galeria de aplicações Azure AD para começar a gerir o provisionamento a 15Five. Se tiver configurado previamente 15Five para SSO, pode utilizar a mesma aplicação. No entanto, recomenda-se que crie uma aplicação separada ao testar inicialmente a integração. Saiba mais sobre a adição de uma aplicação na galeria [aqui.](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passo 4: Definir quem estará no âmbito do provisionamento 

O serviço de prestação de Ad Azure permite-lhe atear âmbito a quem será a provisionado com base na atribuição à aplicação e ou com base em atributos do utilizador/grupo. Se optar por escolher o âmbito de aplicação de quem será aprovisionado na sua aplicação com base na atribuição, pode utilizar os [seguintes passos](../manage-apps/assign-user-or-group-access-portal.md) para atribuir utilizadores e grupos à aplicação. Se optar por escolher o âmbito de aplicação de quem será a provisionado apenas com base em atributos do utilizador ou grupo, pode utilizar um filtro de deteção como descrito [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Ao atribuir utilizadores e grupos a 15Five, tem de selecionar outra função que não o **Acesso Predefinido**. Os utilizadores com a função De Acesso Predefinido estão excluídos do provisionamento e serão marcados como não efetivamente intitulados nos registos de provisionamento. Se a única função disponível na aplicação for a função de acesso predefinido, pode [atualizar o manifesto de aplicação](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) para adicionar funções adicionais. 

* Comece minúsculo. Teste com um pequeno conjunto de utilizadores e grupos antes de rolar para todos. Quando o âmbito de provisão é definido para utilizadores e grupos atribuídos, pode controlá-lo atribuindo um ou dois utilizadores ou grupos à aplicação. Quando o âmbito é definido para todos os utilizadores e grupos, pode especificar um [filtro de deteção baseado no atributo](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).

## <a name="step-5-configure-automatic-user-provisioning-to-15five"></a>Passo 5. Configure o fornecimento automático de utilizadores a 15Five 

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos em 15Five com base nas atribuições de utilizador e/ou grupo em Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-15five-in-azure-ad"></a>Para configurar o provisionamento automático do utilizador para 15Five em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **15Five**.

    ![O link 15Five na lista de Aplicações](common/all-applications.png)

3. Selecione o **separador Provisioning.**

    ![Separador de provisionamento](common/provisioning.png)

4. Desa ajuste o **modo de provisionamento** para **automático**.

    ![Separador de provisionamento](common/provisioning-automatic.png)

5.  Sob a secção Credenciais de Administração, insira os valores **DE URL base SCIM 2.0 e Access Token** recuperados anteriormente nos campos URL e **Secret Token** do **arrendatário,** respectivamente. Clique em **Testar A Ligação** para garantir que a Azure AD pode ligar-se a 15Five. Se a ligação falhar, certifique-se de que a sua conta 15Five tem permissões de Administração e tente novamente.

    ![INQUILINO URL + Token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **'Email' de Notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e verifique a caixa de verificação - **Envie uma notificação de e-mail quando ocorrer uma falha**.

    ![E-mail de notificação](common/provisioning-notification-email.png)

7. Clique em **Guardar**.

8. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users para 15Five**.

9. Reveja os atributos do utilizador que são sincronizados de AD AD a 15Five na secção **De Mapeamento** de Atributos. Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas do utilizador em 15Five para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.


   |Atributo|Tipo|
   |---|---|
   |ativo|Booleano|
   |título|String|
   |e-mails[tipo eq "work"].value|String|
   |userName|String|
   |nome.dado Nome|String|
   |nome.famíliaName|String|
   |externoId|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|Referência|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|String|
   |urn:ietf:params:scim:schemas:extensão:15Five:2.0:Utilizador:localização|String|
   |urn:ietf:params:scim:schemas:extensão:15Five:2.0:User:startDate|String|

10. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Groups a 15Five**.

11. Reveja os atributos do grupo que são sincronizados de AD AD a 15Five na secção **De Mapeamento de Atributos.** Os atributos selecionados como propriedades **de correspondência** são usados para combinar os grupos em 15Five para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

      |Atributo|Tipo|
      |---|---|
      |externoId|String|
      |displayName|String|
      |membros|Referência|

12. Para configurar filtros de deteção, consulte as seguintes instruções fornecidas no tutorial do [filtro de escotagem](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para ativar o serviço de prestação de Ad Azure para 15Five, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

    ![Estatuto de Provisionamento Toggled On](common/provisioning-toggle-on.png)

14. Defina os utilizadores e/ou grupos que deseja prever a 15Five, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para a provisão, clique em **Guardar**.

    ![Configuração de provisionamento de poupança](common/provisioning-configuration-save.png)

    Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de fornecimento AZure AD esteja em execução.

## <a name="step-6-monitor-your-deployment"></a>Passo 6. Monitorizar a implementação
Depois de configurar o provisionamento, utilize os seguintes recursos para monitorizar a sua implantação:

1. Utilize os [registos de provisionamento](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) para determinar quais os utilizadores que foram a provisionados com sucesso ou sem sucesso
2. Verifique a [barra de progresso](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) para ver o estado do ciclo de provisionamento e quão perto está da sua conclusão
3. Se a configuração do provisionamento parecer estar num estado pouco saudável, a aplicação entrará em quarentena. Saiba mais sobre estados de quarentena [aqui.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)  
    
## <a name="connector-limitations"></a>Limitações do conector

* 15Five não suporta eliminações suaves para os utilizadores.

## <a name="change-log"></a>Change log

* 06/16/2020 - Suporte adicional para atributo de extensão da empresa "Manager" e atributos personalizados "Localização" e "Data de Início" para os utilizadores.

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do fornecimento de conta de utilizador para apps empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md).
