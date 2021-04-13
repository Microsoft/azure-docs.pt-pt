---
title: 'Tutorial: Configurar Jostle para fornecimento automático de utilizadores com Azure Ative Directory | Microsoft Docs'
description: Saiba como provisão e desavisagem automática de contas de utilizador de Azure AD a Jostle.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 6dbb744f-8b8e-4988-b293-ebe079c8c5c5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/05/2021
ms.author: Zhchia
ms.openlocfilehash: d2ab0009f036afa38dc9e401223854a034d45e42
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107368670"
---
# <a name="tutorial-configure-jostle-for-automatic-user-provisioning"></a>Tutorial: Configure Jostle para o fornecimento automático de utilizadores

Este tutorial descreve os passos que precisa de executar tanto no Jostle como no Azure Ative Directory (Azure AD) para configurar o fornecimento automático do utilizador. Quando configurado, a Azure AD fornece automaticamente e desescvisões utilizadores e grupos para [Jostle](https://www.jostle.me/) usando o serviço de provisionamento Azure AD. Para obter detalhes importantes sobre o que este serviço faz, como funciona e perguntas frequentes, veja [Automatizar o aprovisionamento e desaprovisionamento de utilizadores em aplicações SaaS no Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Capacidades Suportadas
> [!div class="checklist"]
> * Criar utilizadores em Jostle
> * Remova os utilizadores em Jostle quando já não necessitam de acesso
> * Mantenha os atributos do utilizador sincronizados entre Azure AD e Jostle
> * [Único sinal de](jostle-tutorial.md) Jostle (recomendado)

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* [Um inquilino da AD AZure](../develop/quickstart-create-new-tenant.md) 
* Uma conta de utilizador em Azure AD com [permissão](../roles/permissions-reference.md) para configurar o provisionamento (por exemplo, Administrador de Aplicação, Administrador de Aplicação cloud, Proprietário de Aplicações ou Administrador Global). 
* Um [inquilino de Jostle.](https://www.jostle.me/)
* Uma conta de utilizador em Jostle com permissões de Administrador.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passo 1. Planear a sua implementação de aprovisionamento

1. Saiba [como funciona o serviço de aprovisionamento](../app-provisioning/user-provisioning.md).
1. Determine quem vai estar no [âmbito do aprovisionamento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
1. Determine quais os dados a [mapear entre Azure AD e Jostle](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-jostle-to-support-provisioning-with-azure-ad"></a>Passo 2. Configure Jostle para apoiar o provisionamento com Azure AD

### <a name="automation-account"></a>Conta de automatização

Antes de começar, terá de criar um **utilizador de Automação** na sua intranet Jostle. Esta será a conta que usará para configurar com o Azure. Os utilizadores de automatização podem ser criados em **Admin Definições > contas do Utilizador e dados > Gerir os utilizadores da Automação.**

Para mais detalhes sobre os utilizadores da Automação e como criar um, consulte [este artigo.](https://forum.jostle.us/hc/en-us/articles/360057364073)

Uma vez criada, a conta de utilizador automation **deve ser ativada** (ou seja, iniciar sessão na sua intranet pelo menos uma vez) antes de poder ser utilizada para configurar o Azure.

### <a name="manage-user-provisioning"></a>Gerir o fornecimento de utilizadores

Antes de começar, certifique-se de que a subscrição da sua conta **inclui funcionalidades de provisionamento SSO/utilizador**. Caso isso não o faça, pode contactar o seu Gestor de Sucesso do Cliente <success@jostle.me> e eles podem ajudá-lo a adicioná-lo à sua conta.

O próximo passo é obter a **chave API URL** e **API** da Jostle:

1. Vá à Navegação Principal e clique em **Definições de Administração**.
1. Sob **os dados do Utilizador de/para outros sistemas** clique **Em gerir o fornecimento do utilizador.** Se não vir aqui "Gerir o provisionamento do utilizador" e tiver verificado que a sua conta inclui provisão SSO/utilizador, contacte o Support <support@jostle.me> para ter esta página ativada nas definições de administrador.
1. Na secção de detalhes da **API de Provisionamento** do Utilizador, aceda ao campo **DE URL base,** clique no botão Copiar e guarde o URL em algum lugar onde possa aceder facilmente mais tarde.                                                           

   ![Aprovisionamento](media/jostle-provisioning-tutorial/manage-user-provisioning.png)
                
1. Em seguida, clique no **Adicionar uma nova tecla**... botão
1. No ecrã seguinte, aceda ao campo **Utilizador Automation e** utilize o menu suspenso para selecionar a sua conta de utilizador da Automação. 

   ![Conta de Integração](media/jostle-provisioning-tutorial/select-integration-account.png)                                                                                                                                     
1. No campo de descrição da **chave API de Provisioning,** dê à sua chave um nome (isto é, "Azure") e, em seguida, clique no botão **Adicionar.**

1. Assim que a chave for gerada, **certifique-se de copiá-la imediatamente** e guarde-a onde guardou o seu URL (uma vez que esta será a única altura em que a sua chave aparecerá).                                                               
1. Em seguida, você usará a **chave API URL** e **API** para configurar a integração em Azure.
## <a name="step-3-add-jostle-from-the-azure-ad-application-gallery"></a>Passo 3. Adicione Jostle da galeria de aplicações AD Azure

Adicione Jostle da galeria de aplicações AZure AD para começar a gerir o fornecimento a Jostle. Se já configurar jostle para SSO, pode utilizar a mesma aplicação. No entanto, recomenda-se que crie uma aplicação separada ao testar a integração inicialmente. Saiba mais sobre como adicionar uma aplicação a partir da galeria [aqui](../manage-apps/add-gallery-app.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passo 4: Determinar quem vai estar no âmbito do aprovisionamento 

O serviço de aprovisionamento do Azure AD permite-lhe determinar quem vai ser aprovisionado com base na atribuição à aplicação e/ou com base em atributos do utilizador/grupo. Se optar por determinar quem vai ser aprovisionado na sua aplicação com base na atribuição, pode utilizar os seguintes [passos](../manage-apps/assign-user-or-group-access-portal.md) para atribuir utilizadores e grupos à aplicação. Se escolher determinar quem vai ser aprovisionado com base apenas em atributos do utilizador ou grupo, pode utilizar um filtro de âmbito conforme descrito [aqui](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Ao atribuir utilizadores e grupos a Jostle, deve selecionar outra função que não o **Acesso Predefinido**. Os utilizadores com a função Acesso Predefinido são excluídos do aprovisionamento e marcados como não autorizados de forma efetiva nos registos de aprovisionamento. Se a única função disponível na aplicação for a função de acesso predefinido, pode [atualizar o manifesto de aplicação](../develop/howto-add-app-roles-in-azure-ad-apps.md) para adicionar mais funções. 

* Comece pequeno. Teste com um pequeno conjunto de utilizadores e grupos antes de implementar para todos. Quando o âmbito do aprovisionamento está definido para os utilizadores e os grupos atribuídos, pode controlar isto ao atribuir um ou dois utilizadores ou grupos à aplicação. Quando o âmbito está definido para todos os utilizadores e grupos, pode especificar um [filtro de âmbito baseado em atributos](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-jostle"></a>Passo 5. Configure o fornecimento automático de utilizadores a Jostle 

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores e grupos na aplicação Jostle com base em atribuições de utilizador e grupo em Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-jostle-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para Jostle em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais** e, em seguida, **Todas as aplicações**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

1. Na lista de candidaturas, selecione **Jostle**.

    ![O link Jostle na lista de Aplicações](common/all-applications.png)

1. Selecione o separador **Aprovisionamento**.

    ![Separador Aprovisionamento](common/provisioning.png)

1.  Defina o **Modo de Aprovisionamento** como **Automático**.

    ![Separador de provisionamento automático](common/provisioning-automatic.png)

1. Na secção **credenciais de administrador,** insira a url **do seu Inquilino** Jostle e informações **secretas.** Selecione **a Ligação** de Teste para garantir que o Azure AD pode ligar-se ao Jostle. Se a ligação falhar, certifique-se de que a sua conta Jostle tem permissões de administração e tente novamente.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

1. No campo **'Email' de Notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento. Selecione a **notificação Enviar uma notificação por e-mail quando ocorrer uma falha** na caixa de verificação.

    ![E-mail de Notificação](common/provisioning-notification-email.png)

1. Selecione **Guardar**.

1. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users to Jostle**.

1. Reveja os atributos do utilizador que são sincronizados de Azure AD a Jostle na secção **De Mapeamento de Atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas do utilizador em Jostle para operações de atualização. Se alterar o [atributo de alvo correspondente,](../app-provisioning/customize-application-attributes.md)terá de garantir que a API Jostle suporta utilizadores filtrantes com base nesse atributo. **Selecione Guardar** para escoar quaisquer alterações.

   |Atributo|Tipo|Suportado para filtragem|
   |---|---|---|
   |userName|String|&check;|
   |active|Booleano|
   |name.givenName|String|
   |name.familyName|String|
   |emails[type eq "work"].value|String|
   |e-mails[tipo eq "pessoal"].valor|String|
   |e-mails[tipo eq "alternate1"].value|String|
   |e-mails[tipo eq "alternate2"].value|String|  
   |urn:ietf:params:scim:schemas:extension:jostle:2.0:User:alternateEmail1Label|String|
   |urn:ietf:params:scim:schemas:extension:jostle:2.0:User:alternateEmail2Label |String|  

1. Para configurar filtros de deteção, consulte as instruções fornecidas no tutorial do [filtro de escotagem](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Para ativar o serviço de prestação de Ad Azure para o Jostle, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

    ![Estado do Aprovisionamento Ativado](common/provisioning-toggle-on.png)

1. Defina os utilizadores ou grupos que pretende prever ao Jostle selecionando os valores pretendidos no **Âmbito** na secção **Definições.**

    ![Âmbito de Aprovisionamento](common/provisioning-scope.png)

1. Quando estiver pronto para a provisão, **selecione Save**.

    ![Guardar Configuração de Aprovisionamento](common/provisioning-configuration-save.png)

Esta operação inicia o ciclo de sincronização inicial de todos os utilizadores e grupos definidos no **Âmbito** na secção **Definições**. O ciclo inicial demora mais tempo a ser concluído do que os ciclos seguintes, que ocorrem a cada 40 minutos, desde que o serviço de fornecimento AZure AD esteja em funcionamento.

## <a name="step-6-monitor-your-deployment"></a>Passo 6. Monitorizar a implementação

Depois de configurar o provisionamento, use os seguintes recursos para monitorizar a sua implantação:

* Utilize os [registos de provisionamento](../reports-monitoring/concept-provisioning-logs.md) para determinar quais os utilizadores que foram a provisionados com sucesso ou sem sucesso.
* Verifique a barra de [progresso](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) para ver o estado do ciclo de provisionamento e quão perto está da conclusão.
* Se a configuração de aprovisionamento parecer estar num mau estado de funcionamento, a aplicação vai entrar em quarentena. Para saber mais sobre estados de quarentena, consulte [o estado de colocação de quarentena.](../app-provisioning/application-provisioning-quarantine-status.md)

## <a name="more-resources"></a>Mais recursos

* [Gestão do provisionamento de conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como analisar os registos e obter relatórios sobre a atividade de aprovisionamento](../app-provisioning/check-status-user-account-provisioning.md)