---
title: 'Tutorial: Configure Olfeo SAAS para o fornecimento automático de utilizadores com Azure Ative Directory | Microsoft Docs'
description: Saiba como provisão e desprovisionamento automática de contas de utilizadores de Azure AD a Olfeo SAAS.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 5f6b0320-dfe7-451c-8cd8-6ba7f2e40434
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2021
ms.author: Zhchia
ms.openlocfilehash: b74175c7847bb19aa9410edd613afbfe1d762d05
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105549321"
---
# <a name="tutorial-configure-olfeo-saas-for-automatic-user-provisioning"></a>Tutorial: Configure Olfeo SAAS para o fornecimento automático de utilizadores

Este tutorial descreve os passos que precisa de fazer tanto no Olfeo SAAS como no Azure Ative Directory (Azure AD) para configurar o fornecimento automático do utilizador. Quando configurado, a Azure AD fornece automaticamente e desprovisiona utilizadores e grupos para [o Olfeo SAAS](https://www.olfeo.com) utilizando o serviço de provisionamento Azure AD. Para obter detalhes importantes sobre o que este serviço faz, como funciona e perguntas frequentes, veja [Automatizar o aprovisionamento e desaprovisionamento de utilizadores em aplicações SaaS no Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Capacidades Suportadas
> [!div class="checklist"]
> * Criar utilizadores em Olfeo SAAS
> * Remova os utilizadores em Olfeo SAAS quando já não necessitam de acesso
> * Mantenha os atributos do utilizador sincronizados entre Azure AD e Olfeo SAAS
> * Grupos de provisão e membros do grupo na Olfeo SAAS
> * [Único sinal de](olfeo-saas-tutorial.md) acesso ao Olfeo SAAS (recomendado)

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* [Um inquilino da AD AZure](../develop/quickstart-create-new-tenant.md) 
* Uma conta de utilizador em Azure AD com [permissão](../roles/permissions-reference.md) para configurar o provisionamento (por exemplo, Administrador de Aplicação, Administrador de Aplicação cloud, Proprietário de Aplicações ou Administrador Global). 
* Um [inquilino Olfeo SAAS.](https://www.olfeo.com/)
* Uma conta de utilizador em Olfeo SAAS com permissões de Administração.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passo 1. Planear a sua implementação de aprovisionamento

1. Saiba [como funciona o serviço de aprovisionamento](../app-provisioning/user-provisioning.md).
1. Determine quem vai estar no [âmbito do aprovisionamento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
1. Determine quais os dados a [mapear entre Azure AD e Olfeo SAAS](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-olfeo-saas-to-support-provisioning-with-azure-ad"></a>Passo 2. Configure a Olfeo SAAS para apoiar o provisionamento com a Azure AD

1. Faça login na consola de administração Olfeo SAAS. 
1. Navegue para **a Configuração > Annuaires**.
1. Crie um novo diretório e depois diga-lhe.
1. Selecione o fornecedor **Azure** e, em seguida, clique em **Cr er** para salvar o novo diretório. 
1. Navegue até ao separador **Synchronization** para ver o URL do **inquilino** e o **segredo jeton.** Estes valores serão copiados e colados nos campos **URL** e **Secret Token** no separador Provisioning da sua aplicação Olfeo SAAS no portal Azure.

## <a name="step-3-add-olfeo-saas-from-the-azure-ad-application-gallery"></a>Passo 3. Adicione Olfeo SAAS da galeria de aplicações AZure AD

Adicione Olfeo SAAS da galeria de aplicações AZure AD para começar a gerir o fornecimento à Olfeo SAAS. Se já configurar o Olfeo SAAS para SSO, pode utilizar a mesma aplicação. No entanto, recomenda-se que crie uma aplicação separada ao testar a integração inicialmente. Saiba mais sobre como adicionar uma aplicação a partir da galeria [aqui](../manage-apps/add-gallery-app.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passo 4: Determinar quem vai estar no âmbito do aprovisionamento 

O serviço de aprovisionamento do Azure AD permite-lhe determinar quem vai ser aprovisionado com base na atribuição à aplicação e/ou com base em atributos do utilizador/grupo. Se optar por determinar quem vai ser aprovisionado na sua aplicação com base na atribuição, pode utilizar os seguintes [passos](../manage-apps/assign-user-or-group-access-portal.md) para atribuir utilizadores e grupos à aplicação. Se escolher determinar quem vai ser aprovisionado com base apenas em atributos do utilizador ou grupo, pode utilizar um filtro de âmbito conforme descrito [aqui](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Ao atribuir utilizadores e grupos à Olfeo SAAS, tem de selecionar outra função que não o **Acesso Predefinido**. Os utilizadores com a função Acesso Predefinido são excluídos do aprovisionamento e marcados como não autorizados de forma efetiva nos registos de aprovisionamento. Se a única função disponível na aplicação for a função de acesso predefinido, pode [atualizar o manifesto de aplicação](../develop/howto-add-app-roles-in-azure-ad-apps.md) para adicionar mais funções. 

* Comece pequeno. Teste com um pequeno conjunto de utilizadores e grupos antes de implementar para todos. Quando o âmbito do aprovisionamento está definido para os utilizadores e os grupos atribuídos, pode controlar isto ao atribuir um ou dois utilizadores ou grupos à aplicação. Quando o âmbito está definido para todos os utilizadores e grupos, pode especificar um [filtro de âmbito baseado em atributos](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-olfeo-saas"></a>Passo 5. Configure o fornecimento automático de utilizadores ao Olfeo SAAS 

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores e grupos na aplicação Olfeo SAAS com base em atribuições de utilizador e grupo em Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-olfeo-saas-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para o Olfeo SAAS em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais** e, em seguida, **Todas as aplicações**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

1. Na lista de candidaturas, **selecione Olfeo SAAS**.

    ![O link Olfeo SAAS na lista de Aplicações](common/all-applications.png)

1. Selecione o separador **Aprovisionamento**.

    ![Separador Aprovisionamento](common/provisioning.png)

1.  Defina o **Modo de Aprovisionamento** como **Automático**.

    ![Separador de provisionamento automático](common/provisioning-automatic.png)

1. Na secção **Credenciais Admin,** insira a sua **URL de Inquilino** Olfeo SAAS e informações **secretas.** Selecione **a Ligação de Teste** para garantir que o Azure AD pode ligar-se ao Olfeo SAAS. Se a ligação falhar, certifique-se de que a sua conta Olfeo SAAS tem permissões de administração e tente novamente.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

1. No campo **'Email' de Notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento. Selecione a **notificação Enviar uma notificação por e-mail quando ocorrer uma falha** na caixa de verificação.

    ![E-mail de Notificação](common/provisioning-notification-email.png)

1. Selecione **Guardar**.

1. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users to Olfeo SAAS**.

1. Reveja os atributos do utilizador que são sincronizados de Azure AD a Olfeo SAAS na secção **De Mapeamento de Atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas de utilizador no Olfeo SAAS para operações de atualização. Se alterar o [atributo de alvo correspondente,](../app-provisioning/customize-application-attributes.md)terá de garantir que a API Olfeo SAAS suporta utilizadores filtrantes com base nesse atributo. **Selecione Guardar** para escoar quaisquer alterações.

   |Atributo|Tipo|Suportado para filtragem|
   |---|---|---|
   |userName|String|&check;|
   |displayName|String|
   |active|Booleano|
   |emails[type eq "work"].value|String|
   |preferiuLanguage|String|
   |name.givenName|String|
   |name.familyName|String|
   |nome.formatado|String|
   |externalId|String|  

1. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Groups to Olfeo SAAS**.

1. Reveja os atributos do grupo que são sincronizados de Azure AD a Olfeo SAAS na secção **De mapeamento de atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar com os grupos em Olfeo SAAS para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

      |Atributo|Tipo|Suportado para filtragem|
      |---|---|---|
      |displayName|String|&check;|
      |externalId|String|
      |membros|Referência|

1. Para configurar filtros de deteção, consulte as instruções fornecidas no tutorial do [filtro de escotagem](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Para ativar o serviço de prestação de Ad Azure para o Olfeo SAAS, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

    ![Estado do Aprovisionamento Ativado](common/provisioning-toggle-on.png)

1. Defina os utilizadores ou grupos que pretende prestar à Olfeo SAAS selecionando os valores pretendidos no **Âmbito** na secção **Definições.**

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