---
title: 'Tutorial: Configurar gramática para o fornecimento automático de utilizadores com Azure Ative Directory | Microsoft Docs'
description: Saiba como provisão e desa provisionamento automaticamente de contas de utilizador de Azure AD para Grammarly.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: cd2dd9d7-4901-40c8-8888-98850557b072
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2021
ms.author: Zhchia
ms.openlocfilehash: ca01289ce66afe642081e5be17373e640dd1e46d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104864827"
---
# <a name="tutorial-configure-grammarly-for-automatic-user-provisioning"></a>Tutorial: Configurar gramática para o fornecimento automático de utilizadores

Este tutorial descreve os passos necessários para realizar tanto no Diretório Grammarly como no Azure Ative (Azure AD) para configurar o fornecimento automático do utilizador. Quando configurado, a Azure AD fornece automaticamente e desescvisões utilizadores e grupos para [a Grammarly](https://www.grammarly.com/) utilizando o serviço de provisionamento Azure AD. Para obter detalhes importantes sobre o que este serviço faz, como funciona e perguntas frequentes, veja [Automatizar o aprovisionamento e desaprovisionamento de utilizadores em aplicações SaaS no Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Capacidades Suportadas
> [!div class="checklist"]
> * Criar utilizadores em Gramática
> * Remova os utilizadores em Gramática quando já não necessitam de acesso
> * Mantenha os atributos do utilizador sincronizados entre Azure AD e Grammarly

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* [Um inquilino da AD AZure](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Uma conta de utilizador no Azure AD com [permissão](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) para configurar o aprovisionamento (por ex., Administrador de Aplicações, Administrador de Aplicações de Cloud, Proprietário da Aplicação ou Administrador Global). 
* Uma conta Grammarly Business com acesso a administrador.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passo 1. Planear a sua implementação de aprovisionamento
1. Saiba [como funciona o serviço de aprovisionamento](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
1. Determine quem vai estar no [âmbito do aprovisionamento](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
1. Determine quais os dados a [mapear entre Azure AD e Grammarly](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-grammarly-to-support-provisioning-with-azure-ad"></a>Passo 2. Configure gramática para apoiar o provisionamento com Azure AD

Contacte o seu representante grammarly ou escreva <support@grammarly.com> para solicitar o seu token de provisionamento.

## <a name="step-3-add-grammarly-from-the-azure-ad-application-gallery"></a>Passo 3. Adicione gramática da galeria de aplicações AZure AD

Adicione gramática da galeria de aplicações AZure AD para começar a gerir o fornecimento à Grammarly. Se já configurar grammarly para SSO, pode utilizar a mesma aplicação. Recomendamos que crie uma aplicação separada quando testar a integração inicialmente. Para saber mais sobre como adicionar uma aplicação na galeria, consulte [este arranque rápido.](../manage-apps/add-application-portal.md)

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passo 4: Determinar quem vai estar no âmbito do aprovisionamento

Pode utilizar o serviço de fornecimento Azure AD para o âmbito de aplicação que será prestado com base na atribuição à aplicação ou com base em atributos do utilizador ou grupo. Se optar por determinar quem vai ser aprovisionado na sua aplicação com base na atribuição, pode utilizar os seguintes [passos](../manage-apps/assign-user-or-group-access-portal.md) para atribuir utilizadores e grupos à aplicação. Se optar por escolher o âmbito de aplicação de quem será a provisionado apenas com base em atributos do utilizador ou grupo, pode utilizar um filtro de deteção conforme descrito nas [aplicações Provision com filtros de escoamento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

* Quando atribui utilizadores e grupos à Grammarly, tem de selecionar outra função que não o **Acesso Predefinido**. Os utilizadores com a função de acesso predefinido estão excluídos do provisionamento e serão marcados como não efetivamente intitulados nos registos de provisionamento. Se a única função disponível na aplicação for a função de acesso predefinido, pode [atualizar o manifesto de aplicação](../develop/howto-add-app-roles-in-azure-ad-apps.md) para adicionar mais funções.

* Comece pequeno. Teste com um pequeno conjunto de utilizadores e grupos antes de chegar a todos. Quando o âmbito de provisão é definido para utilizadores e grupos atribuídos, pode controlar esta opção atribuindo um ou dois utilizadores ou grupos à aplicação. Quando o âmbito é definido para todos os utilizadores e grupos, pode especificar um [filtro de deteção baseado em atributos](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).


## <a name="step-5-configure-automatic-user-provisioning-to-grammarly"></a>Passo 5. Configure o fornecimento automático de utilizadores à Grammarly

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores ou grupos no TestApp com base em atribuições de utilizador ou grupo em Ad Azure.

### <a name="configure-automatic-user-provisioning-for-grammarly-in-azure-ad"></a>Configure o fornecimento automático de utilizadores para gramática em Azure AD

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **aplicações da Empresa**  >  **Todas as aplicações**.

    ![Screenshot que mostra o painel de aplicações da Enterprise.](common/enterprise-applications.png)

1. Na lista de candidaturas, selecione **Grammarly**.

    ![Screenshot que mostra o link grammarly na lista de aplicações.](common/all-applications.png)

1. Selecione o separador **Aprovisionamento**.

    ![Screenshot que mostra o separador Provisioning.](common/provisioning.png)

1. Desa ajuste **o modo de provisionamento** para **automático**.

    ![Screenshot que mostra o modo de provisionamento definido para Automático.](common/provisioning-automatic.png)

1. Sob a secção **credenciais de administrador,** no campo URL do **inquilino** `https://sso.grammarly.com/scim/v2` entram, e no campo **Token Secreto** introduza o símbolo fornecido pela Grammarly (ver passo 2 acima). Clique em **Testar a Ligação** para garantir que o Azure AD pode ligar-se à Grammarly. Se a ligação falhar, certifique-se de que a sua conta Grammarly tem permissões de Administração e tente novamente.

    ![Screenshot que mostra a URL do inquilino e caixas de token secretos.](common/provisioning-testconnection-tenanturltoken.png)

1. Na caixa de **e-mail de notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento. Selecione a **notificação Enviar uma notificação por e-mail quando ocorrer uma falha** na caixa de verificação.

    ![Screenshot que mostra a caixa de e-mail de notificação.](common/provisioning-notification-email.png)

1. Selecione **Guardar**.

1. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users para a Grammarly**.

1. Reveja os atributos do utilizador que são sincronizados de AD AD a Grammarly na secção **De mapeamento de atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas do utilizador em Grammarly para operações de atualização. Se alterar o [atributo de alvo correspondente,](../app-provisioning/customize-application-attributes.md)terá de garantir que a API Grammarly suporta utilizadores filtrantes com base nesse atributo. **Selecione Guardar** para escoar quaisquer alterações.

   |Atributo|Tipo|Suportado para filtragem|
   |---|---|---|
   |userName|String|&check;|
   |externalId|String|
   |active|Booleano|
   |displayName|String|
   |emails[type eq "work"].value|String|


1. Para configurar filtros de deteção, consulte as instruções fornecidas no tutorial do [filtro de escotagem](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Para ativar o serviço de prestação de AD Azure para gramática, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

    ![Screenshot que mostra o Estado de Provisionamento alternado.](common/provisioning-toggle-on.png)

1. Defina os utilizadores ou grupos que pretende prever para a Grammarly selecionando os valores pretendidos no **Âmbito** na secção **Definições.**

    ![Screenshot que mostra o Âmbito de Provisionamento.](common/provisioning-scope.png)

1. Quando estiver pronto para a provisão, **selecione Save**.

    ![Screenshot que mostra o botão Guardar.](common/provisioning-configuration-save.png)

Esta operação inicia o ciclo de sincronização inicial de todos os utilizadores e grupos definidos no **Âmbito** na secção **Definições**. O ciclo inicial demora mais tempo a ser efetuado do que os ciclos subsequentes, que ocorrem a cada 40 minutos, desde que o serviço de fornecimento AZure AD esteja em funcionamento.

## <a name="step-6-monitor-your-deployment"></a>Passo 6. Monitorizar a implementação

Depois de configurar o provisionamento, use os seguintes recursos para monitorizar a sua implantação:

* Utilize os [registos de provisionamento](../reports-monitoring/concept-provisioning-logs.md) para determinar quais os utilizadores que foram a provisionados com sucesso ou sem sucesso.
* Verifique a barra de [progresso](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) para ver o estado do ciclo de provisionamento e o quão perto está de ser concluído.
* Se a configuração de aprovisionamento parecer estar num mau estado de funcionamento, a aplicação vai entrar em quarentena. Para saber mais sobre estados de quarentena, consulte [o estado de colocação de quarentena.](../app-provisioning/application-provisioning-quarantine-status.md)

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do provisionamento de conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como analisar os registos e obter relatórios sobre a atividade de aprovisionamento](../app-provisioning/check-status-user-account-provisioning.md)
