---
title: 'Tutorial: Configure Juno Journey para fornecimento automático de utilizadores com Diretório Ativo Azure / Microsoft Docs'
description: Saiba como providenciar e desa provisionar automaticamente as contas de utilizadores de Azure AD a Juno Journey.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 79813b19-c96e-4459-a4e5-636a6b6f5041
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2020
ms.author: Zhchia
ms.openlocfilehash: 0538902897d088c8ce271487b3f30698af0bb1eb
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/07/2020
ms.locfileid: "87926557"
---
# <a name="tutorial-configure-juno-journey-for-automatic-user-provisioning"></a>Tutorial: Configurar a Viagem juno para o fornecimento automático de utilizadores

Este tutorial descreve os passos que precisa de executar tanto no Juno Journey como no Azure Ative Directory (Azure AD) para configurar o fornecimento automático do utilizador. Quando configurado, a Azure AD fornece automaticamente e desescvisões utilizadores e grupos para [Juno Journey](https://www.junojourney.com/) usando o serviço de provisionamento Azure AD. Para obter detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte [automatizar o fornecimento e desprovisionamento de aplicações saaS com diretório Azure Ative.](../manage-apps/user-provisioning.md) 


## <a name="capabilities-supported"></a>Capacidades suportadas
> [!div class="checklist"]
> * Criar utilizadores em Juno Journey
> * Remova os utilizadores em Juno Journey quando já não necessitam de acesso
> * Mantenha os atributos do utilizador sincronizados entre Azure AD e Juno Journey
> * [Único sinal de juno](https://docs.microsoft.com/azure/active-directory/saas-apps/juno-journey-tutorial) journey (recomendado)

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* [Um inquilino da AD AZure](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Uma conta de utilizador em Azure AD com [permissão](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) para configurar o provisionamento (por exemplo, Administrador de Aplicação, Administrador de Aplicação cloud, Proprietário de Aplicações ou Administrador Global). 
*  Um [inquilino da Viagem Juno.](https://www.junojourney.com/getstarted)
*  Uma conta de utilizador em Juno Journey com permissões de Administração.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passo 1. Planeie a sua implantação de provisionamento
1. Saiba [como funciona o serviço de prestação de serviços.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)
2. Determinar quem estará no [âmbito do provisionamento](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Determine quais os dados a [mapear entre Azure AD e Juno Journey](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-juno-journey-to-support-provisioning-with-azure-ad"></a>Passo 2. Configure a Viagem de Juno para apoiar o provisionamento com a Azure AD

1. Para **a Secret Token** e **a TENANT URL** contacte a equipa de apoio da Juno Journey em support@the-juno.com . Este valor será introduzido nos campos URL **Secret Token** e **Tenant,** respectivamente, no separador Provisioning da sua aplicação Juno Journey no portal Azure. 

## <a name="step-3-add-juno-journey-from-the-azure-ad-application-gallery"></a>Passo 3. Adicione Juno Journey da galeria de aplicações AZure AD

Adicione Juno Journey da galeria de aplicações AZure AD para começar a gerir o fornecimento à Juno Journey. Se já configurar juno journey para SSO, pode usar a mesma aplicação. No entanto, recomenda-se que crie uma aplicação separada ao testar inicialmente a integração. Saiba mais sobre a adição de uma aplicação na galeria [aqui.](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passo 4: Definir quem estará no âmbito do provisionamento 

O serviço de prestação de Ad Azure permite-lhe atear âmbito a quem será a provisionado com base na atribuição à aplicação e ou com base em atributos do utilizador/grupo. Se optar por escolher o âmbito de aplicação de quem será aprovisionado na sua aplicação com base na atribuição, pode utilizar os [seguintes passos](../manage-apps/assign-user-or-group-access-portal.md) para atribuir utilizadores e grupos à aplicação. Se optar por escolher o âmbito de aplicação de quem será a provisionado apenas com base em atributos do utilizador ou grupo, pode utilizar um filtro de deteção como descrito [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Ao atribuir utilizadores e grupos a Juno Journey, deve selecionar outra função que não **o Acesso Predefinido**. Os utilizadores com a função De Acesso Predefinido estão excluídos do provisionamento e serão marcados como não efetivamente intitulados nos registos de provisionamento. Se a única função disponível na aplicação for a função de acesso predefinido, pode [atualizar o manifesto de aplicação](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) para adicionar funções adicionais. 

* Comece minúsculo. Teste com um pequeno conjunto de utilizadores e grupos antes de rolar para todos. Quando o âmbito de provisão é definido para utilizadores e grupos atribuídos, pode controlá-lo atribuindo um ou dois utilizadores ou grupos à aplicação. Quando o âmbito é definido para todos os utilizadores e grupos, pode especificar um [filtro de deteção baseado no atributo](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-juno-journey"></a>Passo 5. Configure o fornecimento automático de utilizadores ao Juno Journey 

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos no TestApp com base em atribuições de utilizador e/ou grupo em Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-juno-journey-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para a Viagem juno em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Juno Journey**.

    ![O link Juno Journey na lista de Aplicações](common/all-applications.png)

3. Selecione o **separador Provisioning.**

    ![Separador de provisionamento](common/provisioning.png)

4. Desa ajuste o **modo de provisionamento** para **automático**.

    ![Separador de provisionamento](common/provisioning-automatic.png)

5. Sob a secção **de Credenciais de Administração,** insira o valor URL do inquilino recuperado anteriormente na **URL do inquilino.** Insira o valor simbólico secreto recuperado anteriormente em **Secret Token**. Clique em **Testar a Ligação** para garantir que o Azure AD pode ligar-se à Viagem de Juno. Se a ligação falhar, certifique-se de que a sua conta Juno Journey tem permissões de administração e tente novamente.

    ![provisionamento](./media/juno-journey-provisioning-tutorial/provisioning.png)

6. No campo **'Email' de Notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e selecione a **notificação enviar uma notificação por e-mail quando ocorrer uma falha** na caixa de verificação.

    ![E-mail de notificação](common/provisioning-notification-email.png)

7. Selecione **Guardar**.

8. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users to Juno Journey**.

9. Reveja os atributos do utilizador que são sincronizados de AD AD a Juno Journey na secção **De mapeamento de atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas de utilizador em Juno Journey para operações de atualização. Se optar por alterar o [atributo de alvo correspondente,](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)terá de garantir que a API da Viagem juno suporta utilizadores filtrantes com base nesse atributo. Selecione o botão **Guardar** para escoar quaisquer alterações.

   |Variável|Tipo|
   |---|---|
   |userName|String|
   |externoId|String|
   |displayName|String|
   |título|String|
   |ativo|Booleano|
   |preferiuLanguage|String|
   |e-mails[tipo eq "work"].value|String|
   |endereços[tipo eq "work"].país|String|
   |endereços[tipo eq "work"].região|String|
   |endereços[tipo eq "work"].localidade|String|
   |endereços[tipo eq "work"].postalCode|String|
   |endereços[tipo eq "work"].formatado|String|
   |endereços[tipo eq "work"].streetAddress|String|
   |nome.dado Nome|String|
   |nome.famíliaName|String|
   |nome.middleName|String|
   |nome.formatado|String|
   |números de telefone[tipo eq "fax"].valor|String|
   |números de telefone[tipo eq "mobile"].valor|String|
   |phoneNumbers[tipo eq "work"].value|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|String|
   urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|String|
   urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|String|
   urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:organization|String|


10. Para configurar filtros de deteção, consulte as seguintes instruções fornecidas no tutorial do [filtro de escotagem](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para ativar o serviço de fornecimento de Ad Azure para a Viagem de Juno, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

    ![Estatuto de Provisionamento Toggled On](common/provisioning-toggle-on.png)

12. Defina os utilizadores e/ou grupos que deseja prestar à Juno Journey, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de provisionamento](common/provisioning-scope.png)

13. Quando estiver pronto para a provisão, clique em **Guardar**.

    ![Configuração de provisionamento de poupança](common/provisioning-configuration-save.png)

Esta operação inicia o ciclo inicial de sincronização de todos os utilizadores e grupos definidos no **Âmbito** na secção **Definições.** O ciclo inicial demora mais tempo a ser efetuado do que os ciclos subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de fornecimento AZure AD esteja em funcionamento. 

## <a name="step-6-monitor-your-deployment"></a>Passo 6. Monitorizar a implementação
Depois de configurar o provisionamento, utilize os seguintes recursos para monitorizar a sua implantação:

* Utilize os [registos de provisionamento](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) para determinar quais os utilizadores que foram a provisionados com sucesso ou sem sucesso
* Verifique a [barra de progresso](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) para ver o estado do ciclo de provisionamento e quão perto está da sua conclusão
* Se a configuração do provisionamento parecer estar num estado pouco saudável, a aplicação entrará em quarentena. Saiba mais sobre estados de quarentena [aqui.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do fornecimento de conta de utilizador para apps empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)
