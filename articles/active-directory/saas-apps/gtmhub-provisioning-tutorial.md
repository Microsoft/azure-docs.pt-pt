---
title: 'Tutorial: Configurar o Gtmhub para o fornecimento automático de utilizadores com o Azure Ative Directory | Microsoft Docs'
description: Saiba como provisão e desavisagem automática de contas de utilizadores do Azure AD para o Gtmhub.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 10b68d00-a544-480b-9bd6-f6ac291a90d0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/03/2020
ms.author: Zhchia
ms.openlocfilehash: 0e160def31a43bc94e4f6151b46efe72e585e953
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98735729"
---
# <a name="tutorial-configure-gtmhub-for-automatic-user-provisioning"></a>Tutorial: Configurar o Gtmhub para o fornecimento automático de utilizadores

Este tutorial descreve os passos necessários para realizar tanto no Gtmhub como no Azure Ative Directory (Azure AD) para configurar o fornecimento automático do utilizador. Quando configurado, a Azure AD fornece automaticamente e desescvisões utilizadores e grupos para [Gtmhub](https://www.gtmhub.com/) usando o serviço de provisionamento Azure AD. Para obter detalhes importantes sobre o que este serviço faz, como funciona e perguntas frequentes, veja [Automatizar o aprovisionamento e desaprovisionamento de utilizadores em aplicações SaaS no Azure Active Directory](../app-provisioning/user-provisioning.md). 

>[!NOTE]
>Atualmente, quando o fornecimento automático de utilizadores é configurado, o Azure AD apenas desrespeia automaticamente os utilizadores e grupos para o Gtmhub, bem como mapear os utilizadores para as respetivas equipas utilizando o serviço de Provisionamento Azure AD. Mas em 2021, uma vez que o SSO está ativado com o Gtmhub, os utilizadores serão automaticamente a provisionados quando iniciarem sso e serão atribuídos à respetiva equipa.


## <a name="capabilities-supported"></a>Capacidades Suportadas
> [!div class="checklist"]
> * Remova os utilizadores no Gtmhub quando já não necessitam de acesso.
> * Mantenha os atributos do utilizador sincronizados entre Azure AD e Gtmhub.
> * Mapear os utilizadores nas suas equipas automaticamente e alinhá-los.

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* [Um inquilino do Azure AD](../develop/quickstart-create-new-tenant.md).
* Uma conta de utilizador em Azure AD com [permissão](../roles/permissions-reference.md) para configurar o provisionamento (por exemplo, Administrador de Aplicação, Administrador de Aplicação cloud, Proprietário de Aplicações ou Administrador Global). 
* Uma conta da Enterprise Gtmhub.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passo 1. Planear a sua implementação de aprovisionamento
1. Saiba [como funciona o serviço de aprovisionamento](../app-provisioning/user-provisioning.md).
2. Determine quem vai estar no [âmbito do aprovisionamento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determine quais os dados a [mapear entre Azure AD e Gtmhub](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-gtmhub-to-support-team-mapping-and-user-de-provisioning-with-azure-ad"></a>Passo 2. Configure o Gtmhub para apoiar o mapeamento da equipa e o desavisionamento do utilizador com a Azure AD

Para ligar o seu pedido de provisionamento à sua conta Gtmhub, terá de emitir um token SCIM e compilar o URL do inquilino.

### <a name="to-issue-a-new-scim-token"></a>Para emitir um novo símbolo SCIM:

1. Inscreva-se na sua **conta Gtmhub.** Navegue para **Configurações > Configuração > Fichas API**.

    ![Separador API Tokens](media/gtmhub-provisioning-tutorial/api-tokens.png)
2. Clique em **Issue Token** e selecione **SCIM**. Introduza um nome para o token e clique no botão **Generate API Token.**

    ![Gerar separador Tokens](media/gtmhub-provisioning-tutorial/generate-token.png)
3. Uma vez gerado o token, pode copiá-lo e usá-lo na sua aplicação de provisionamento Azure AD.

    ![Ficha de cópia](media/gtmhub-provisioning-tutorial/token.png)

### <a name="to-compile-the-tenant-url"></a>Para compilar o url do inquilino:

1. O url do seu inquilino tem de estar no seguinte formato:

    `https://app.gtmhub.com/api/v1/scim/azure/{account_id}`

2. Se a sua conta Gtmhub estiver localizada no centro de dados dos EUA, também terá de adicionar o centro de dados ao URL:
    
     `https://app.us.gtmhub.com/api/v1/scim/azure/{account_id}`

3. Para obter o ID da conta vá a **Definições,** em seguida, selecione o separador **API Tokens** e copie o ID da conta:  ![ ID da conta](media/gtmhub-provisioning-tutorial/account-id.png)

## <a name="step-3-add-gtmhub-from-the-azure-ad-application-gallery"></a>Passo 3. Adicione o Gtmhub da galeria de aplicações AZure AD

Adicione o Gtmhub da galeria de aplicações AD Azure para começar a gerir o fornecimento ao Gtmhub. Se já configurar o Gtmhub para SSO, pode utilizar a mesma aplicação. No entanto, é recomendável criar uma aplicação separada ao testar a integração inicialmente. Saiba mais sobre como adicionar uma aplicação a partir da galeria [aqui](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passo 4: Determinar quem vai estar no âmbito do aprovisionamento 

O serviço de aprovisionamento do Azure AD permite-lhe determinar quem vai ser aprovisionado com base na atribuição à aplicação e/ou com base em atributos do utilizador/grupo. Se optar por determinar quem vai ser aprovisionado na sua aplicação com base na atribuição, pode utilizar os seguintes [passos](../manage-apps/assign-user-or-group-access-portal.md) para atribuir utilizadores e grupos à aplicação. Se escolher determinar quem vai ser aprovisionado com base apenas em atributos do utilizador ou grupo, pode utilizar um filtro de âmbito conforme descrito [aqui](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Ao atribuir utilizadores e grupos ao Gtmhub, tem de selecionar outra função que não o **Acesso Predefinido**. Os utilizadores com a função Acesso Predefinido são excluídos do aprovisionamento e marcados como não autorizados de forma efetiva nos registos de aprovisionamento. Se a única função disponível na aplicação for a função de acesso predefinido, pode [atualizar o manifesto de aplicação](../develop/howto-add-app-roles-in-azure-ad-apps.md) para adicionar funções adicionais. 

* Comece pequeno. Teste com um pequeno conjunto de utilizadores e grupos antes de implementar para todos. Quando o âmbito do aprovisionamento está definido para os utilizadores e os grupos atribuídos, pode controlar isto ao atribuir um ou dois utilizadores ou grupos à aplicação. Quando o âmbito está definido para todos os utilizadores e grupos, pode especificar um [filtro de âmbito baseado em atributos](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-gtmhub"></a>Passo 5. Configure o fornecimento automático de utilizadores ao Gtmhub 

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos no TestApp com base em atribuições de utilizador e/ou grupo em Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-gtmhub-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para o Gtmhub em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais** e, em seguida, **Todas as aplicações**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Gtmhub**.

    ![O link Gtmhub na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Aprovisionamento**.

    ![Separador Aprovisionamento](common/provisioning.png)

4. Defina o **Modo de Aprovisionamento** como **Automático**.

    ![Separador de provisionamento automático](common/provisioning-automatic.png)

5. Sob a secção **de Credenciais de Administração,** insira o URL do seu Inquilino Gtmhub e o Token Secreto. Clique em **Testar A Ligação** para garantir que o Azure AD pode ligar-se ao Gtmhub. Se a ligação falhar, certifique-se de que a sua conta Gtmhub tem permissões de Administração e tente novamente.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **E-mail de Notificação**, introduza o endereço de e-mail de uma pessoa ou um grupo que deve receber as notificações de erro de aprovisionamento e marque a caixa de verificação **Enviar uma notificação de e-mail quando ocorre uma falha**.

    ![E-mail de Notificação](common/provisioning-notification-email.png)

7. Selecione **Guardar**.

8. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users para o Gtmhub**.

9. Reveja os atributos do utilizador que são sincronizados de Azure AD a Gtmhub na secção **De mapeamento de Atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas de utilizador no Gtmhub para operações de atualização. Se optar por alterar o [atributo de alvo correspondente,](../app-provisioning/customize-application-attributes.md)terá de garantir que a API do Gtmhub suporta utilizadores filtrantes com base nesse atributo. Selecione o botão **Guardar** para escoar quaisquer alterações.

   |Atributo|Tipo|Suportado para filtragem|
   |---|---|---|
   |userName|String|&check;|
   |externalId|String|&check;|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|Referência|

10. Para configurar filtros de âmbito, veja as instruções seguintes disponibilizadas no [Tutorial de filtro de âmbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para ativar o serviço de fornecimento Azure AD para o Gtmhub, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

    ![Estado do Aprovisionamento Ativado](common/provisioning-toggle-on.png)

14. Defina os utilizadores e/ou grupos que deseja prestar ao Gtmhub, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de Aprovisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para aprovisionar, clique em **Guardar**.

    ![Guardar Configuração de Aprovisionamento](common/provisioning-configuration-save.png)

Esta operação inicia o ciclo de sincronização inicial de todos os utilizadores e grupos definidos no **Âmbito** na secção **Definições**. O ciclo inicial leva mais tempo a ser executado do que os ciclos subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de aprovisionamento do Azure AD esteja em execução. 

## <a name="step-6-monitor-your-deployment"></a>Passo 6. Monitorizar a implementação
Depois de configurar o aprovisionamento, utilize os seguintes recursos para monitorizar a sua implementação:

* Utilize os [registos de aprovisionamento](../reports-monitoring/concept-provisioning-logs.md) para determinar quais os utilizadores que foram aprovisionados com ou sem êxito
* Verifique a [barra de progresso](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) para ver o estado do ciclo de aprovisionamento e quão próximo está da conclusão
* Se a configuração de aprovisionamento parecer estar num mau estado de funcionamento, a aplicação vai entrar em quarentena. Saiba mais sobre os estados de quarentena [aqui](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento de contas de utilizador para Aplicações Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como analisar os registos e obter relatórios sobre a atividade de aprovisionamento](../app-provisioning/check-status-user-account-provisioning.md)