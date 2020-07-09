---
title: 'Tutorial: Configure IDEO para fornecimento automático de utilizadores com Diretório Ativo Azure / Microsoft Docs'
description: Saiba como configurar o Azure Ative Directory para provisão automática e desaferição de contas de utilizador ao IDEO.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: de4f06a3-83e9-46ce-80ee-03d706b91c81
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2019
ms.author: Zhchia
ms.openlocfilehash: 2de7bb81726bcb4aa1943b3f1816d6171f6ac97d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85554617"
---
# <a name="tutorial-configure-ideo-for-automatic-user-provisioning"></a>Tutorial: Configure IDEO para o fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados no Diretório Ativo IDEO e Azure Ative (Azure AD) para configurar a Azure AD para fornecimento e desavisionamento automática de utilizadores e/ou grupos ao IDEO. Para obter detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte [automatizar o fornecimento e desprovisionamento de aplicações saaS com diretório Azure Ative.](../app-provisioning/user-provisioning.md)

> [!NOTE]
> Este conector encontra-se atualmente em Visualização Pública. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="capabilities-supported"></a>Capacidades suportadas
> [!div class="checklist"]
> * Criar utilizadores no IDEO
> * Remova os utilizadores no IDEO quando já não necessitam de acesso
> * Mantenha os atributos do utilizador sincronizados entre AZure AD e IDEO
> * Grupos de provisão e membros do grupo no IDEO
> * Único sinal de IDEO (recomendado)

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* [Um inquilino da AD Azure.](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)
* Uma conta de utilizador em Azure AD com [permissão](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) para configurar o provisionamento (por exemplo, Administrador de Aplicação, Administrador de Aplicação cloud, Proprietário de Aplicações ou Administrador Global).
* [Um inquilino IDEO](https://www.shape.space/product/pricing)
* Uma conta de utilizador no IDEO ! Forma com permissões de administração.


## <a name="step-1-plan-your-provisioning-deployment"></a>Passo 1. Planeie a sua implantação de provisionamento
1. Saiba [como funciona o serviço de prestação de serviços.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)
2. Determinar quem estará no [âmbito do provisionamento](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Determinar quais os dados a [mapear entre Azure AD e IDEO](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-ideo-to-support-provisioning-with-azure-ad"></a>Passo 2. Configure IDEO para apoiar o provisionamento com Azure AD

Antes de configurar o IDEO para o fornecimento automático de utilizadores com Azure AD, terá de obter algumas informações de fornecimento da IDEO.

* Para a equipa de apoio **da Secret Token** contacte a equipa de apoio da IDEO em productsupport@ideo.com . Este valor será introduzido no campo **Secret Token** no separador Provisioning da sua aplicação IDEO no portal Azure. 

## <a name="step-3-add-ideo-from-the-azure-ad-application-gallery"></a>Passo 3. Adicione IDEO da galeria de aplicações AZure AD

Adicione o IDEO da galeria de aplicações AZure AD para começar a gerir o fornecimento ao IDEO. Se já configurar o IDEO para SSO, pode utilizar a mesma aplicação. No entanto, recomenda-se que crie uma aplicação separada ao testar inicialmente a integração. Saiba mais sobre a adição de uma aplicação na galeria [aqui.](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passo 4. Definir quem estará no âmbito do provisionamento 

O serviço de prestação de Ad Azure permite-lhe atear âmbito a quem será a provisionado com base na atribuição à aplicação e ou com base em atributos do utilizador/grupo. Se optar por escolher o âmbito de aplicação de quem será aprovisionado na sua aplicação com base na atribuição, pode utilizar os [seguintes passos](../manage-apps/assign-user-or-group-access-portal.md) para atribuir utilizadores e grupos à aplicação. Se optar por escolher o âmbito de aplicação de quem será a provisionado apenas com base em atributos do utilizador ou grupo, pode utilizar um filtro de deteção como descrito [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Ao atribuir utilizadores e grupos ao IDEO, tem de selecionar outra função que não o **Acesso Predefinido.** Os utilizadores com a função De Acesso Predefinido estão excluídos do provisionamento e serão marcados como não efetivamente intitulados nos registos de provisionamento. Se a única função disponível na aplicação for a função de acesso predefinido, pode [atualizar o manifesto de aplicação](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) para adicionar funções adicionais. 

* Comece minúsculo. Teste com um pequeno conjunto de utilizadores e grupos antes de rolar para todos. Quando o âmbito de provisão é definido para utilizadores e grupos atribuídos, pode controlá-lo atribuindo um ou dois utilizadores ou grupos à aplicação. Quando o âmbito é definido para todos os utilizadores e grupos, pode especificar um [filtro de deteção baseado no atributo](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-ideo"></a>Passo 5. Configure o fornecimento automático de utilizadores ao IDEO 

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos em IDEO com base em atribuições de utilizador e/ou grupo em Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-ideo-in-azure-ad"></a>Para configurar o fornecimento automático de IDEO para o IDEO em Ad Azure:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **IDEO**.

    ![O link IDEO na lista de Aplicações](common/all-applications.png)

3. Selecione o **separador Provisioning.**

    ![Separador de provisionamento](common/provisioning.png)

4. Desa ajuste o **modo de provisionamento** para **automático**.

    ![Separador de provisionamento](common/provisioning-automatic.png)

5. Sob a secção **Credenciais de Administração,** insira os valores **DE URL base SCIM 2.0 e Access Token** recuperados anteriormente da equipa de apoio IDEO nos campos URL e **Secret Token** do **arrendatário,** respectivamente. Clique em **Testar a Ligação** para garantir que o Azure AD pode ligar-se ao IDEO. Se a ligação falhar, certifique-se de que a sua conta IDEO tem permissões de Administração e tente novamente.

    ![INQUILINO URL + Token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **'Email' de Notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e verifique a caixa de verificação - **Envie uma notificação de e-mail quando ocorrer uma falha**.

    ![E-mail de notificação](common/provisioning-notification-email.png)

7. Clique em **Guardar**.

8. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users para IDEO**.

9. Reveja os atributos do utilizador que são sincronizados de Azure AD a IDEO na secção **De Mapeamento de Atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas do utilizador no IDEO para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

   |Atributo|Tipo|
   |---|---|
   |userName|String|
   |e-mails[tipo eq "work"].value|String|
   |ativo|Booleano|
   |nome.dado Nome|String|
   |nome.famíliaName|String|

10. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Groups para IDEO**.
   
11. Reveja os atributos do grupo que são sincronizados de Azure AD para ideo na secção **De mapeamento de Atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar com os grupos no IDEO para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

      |Atributo|Tipo|
      |---|---|
      |displayName|String|
      |membros|Referência|

12. Para configurar filtros de deteção, consulte as seguintes instruções fornecidas no tutorial do [filtro de escotagem](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para ativar o serviço de prestação de Ad Azure para o IDEO, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

    ![Estatuto de Provisionamento Toggled On](common/provisioning-toggle-on.png)

14. Defina os utilizadores e/ou grupos que deseja prestar ao IDEO, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para a provisão, clique em **Guardar**.

    ![Configuração de provisionamento de poupança](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de fornecimento AZure AD esteja em execução. 

## <a name="step-6-monitor-your-deployment"></a>Passo 6. Monitorizar a implementação
Depois de configurar o provisionamento, utilize os seguintes recursos para monitorizar a sua implantação:

1. Utilize os [registos de provisionamento](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) para determinar quais os utilizadores que foram a provisionados com sucesso ou sem sucesso
2. Verifique a [barra de progresso](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) para ver o estado do ciclo de provisionamento e quão perto está da sua conclusão
3. Se a configuração do provisionamento parecer estar num estado pouco saudável, a aplicação entrará em quarentena. Saiba mais sobre estados de quarentena [aqui.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)  

## <a name="change-log"></a>Change log

* 06/15/2020 - Apoio adicional para utilizar operações PATCH para grupos em vez de PUT.

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do fornecimento de conta de utilizador para apps empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Próximos passos

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)


