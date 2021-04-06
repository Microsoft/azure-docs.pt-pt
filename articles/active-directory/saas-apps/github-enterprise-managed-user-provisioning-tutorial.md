---
title: 'Tutorial: Configurar gitHub Enterprise Managed User para fornecimento automático de utilizadores com Azure Ative Directory | Microsoft Docs'
description: Saiba como provisão e desa provisionamento automaticamente de contas de utilizador do Azure AD para o GitHub Enterprise Managed User.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 6aee39c7-08a1-4110-b936-4c85d129743b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/05/2021
ms.author: Zhchia
ms.openlocfilehash: cbae87a005240c15a2c3c28dcb8ab126d9957ba6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104801235"
---
# <a name="tutorial-configure-github-enterprise-managed-user-for-automatic-user-provisioning"></a>Tutorial: Configurar gitHub Enterprise Managed User para fornecimento automático de utilizadores

Este tutorial descreve os passos necessários para executar tanto no GitHub Enterprise Managed User como no Azure Ative Directory (Azure AD) para configurar o fornecimento automático do utilizador. Quando configurado, o Azure AD fornece automaticamente e desescvisões utilizadores e grupos para GitHub Enterprise Managed User usando o serviço de provisionamento Azure AD. Para obter detalhes importantes sobre o que este serviço faz, como funciona e perguntas frequentes, veja [Automatizar o aprovisionamento e desaprovisionamento de utilizadores em aplicações SaaS no Azure Active Directory](../app-provisioning/user-provisioning.md).


## <a name="capabilities-supported"></a>Capacidades Suportadas
> [!div class="checklist"]
> * Criar utilizadores no GitHub Enterprise Managed User
> * Remova os utilizadores no GitHub Enterprise Managed User quando já não necessitam de acesso
> * Mantenha os atributos do utilizador sincronizados entre o Azure AD e o GitHub Enterprise Managed User
> * Grupos de provisão e membros do grupo no GitHub Enterprise Managed User
> * Único sinal de sação para GitHub Enterprise Managed User (recomendado)

> [!NOTE]
> Este conector de provisionamento está ativado apenas para os participantes beta dos Utilizadores Geridos pela Empresa.


## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* [Um inquilino da AD AZure](../develop/quickstart-create-new-tenant.md)
* Uma conta de utilizador em Azure AD com [permissão](../roles/permissions-reference.md) para configurar o provisionamento (por exemplo, Administrador de Aplicação, Administrador de Aplicação cloud, Proprietário de Aplicações ou Administrador Global).
* Os Utilizadores Geridos pela Empresa ativaram a GitHub Enterprise e configuraram o login com a SAML SSO através do seu inquilino AZure AD.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passo 1. Planear a sua implementação de aprovisionamento
1. Saiba [como funciona o serviço de aprovisionamento](../app-provisioning/user-provisioning.md).
2. Determine quem vai estar no [âmbito do aprovisionamento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determine quais os dados a [mapear entre a Azure AD e o GitHub Enterprise Managed User](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-github-enterprise-managed-user-to-support-provisioning-with-azure-ad"></a>Passo 2. Configure GitHub Enterprise Managed User para apoiar o provisionamento com Azure AD

1. A URL do inquilino `https://api.github.com/scim/v2/enterprises/{enterprise}` é. Este valor será introduzido no campo URL do inquilino no separador Provisioning da sua aplicação GitHub Enterprise Managed User no portal Azure.

2. Como administrador da GitHub Enterprise Managed navigate to the upper-right corner -> clique na sua foto de perfil -> depois clique em **Definições**.

3. Na barra lateral esquerda, clique nas **definições do Desenvolvedor**.

4. Na barra lateral esquerda, clique **em fichas de acesso pessoal.**

5. Clique **Em Gerar novo token**.

6. Selecione o âmbito **de administração:enterprise** para este token.

7. Clique **em gerar token**.

8. Copie e guarde o **símbolo secreto.** Este valor será introduzido no campo Secret Token no separador Provisioning da sua aplicação GitHub Enterprise Managed User no portal Azure.

## <a name="step-3-add-github-enterprise-managed-user-from-the-azure-ad-application-gallery"></a>Passo 3. Adicione o Utilizador Gerido gitHub enterprise da galeria de aplicações Azure AD

Adicione o GitHub Enterprise Managed User da galeria de aplicações Azure AD para começar a gerir o fornecimento ao Utilizador Gerido pela Empresa GitHub. Se tiver configurado previamente o GitHub Enterprise Managed User para SSO, pode utilizar a mesma aplicação. No entanto, é recomendável criar uma aplicação separada ao testar a integração inicialmente. Saiba mais sobre como adicionar uma aplicação a partir da galeria [aqui](../manage-apps/add-application-portal.md).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passo 4: Determinar quem vai estar no âmbito do aprovisionamento

O serviço de aprovisionamento do Azure AD permite-lhe determinar quem vai ser aprovisionado com base na atribuição à aplicação e/ou com base em atributos do utilizador/grupo. Se optar por determinar quem vai ser aprovisionado na sua aplicação com base na atribuição, pode utilizar os seguintes [passos](../manage-apps/assign-user-or-group-access-portal.md) para atribuir utilizadores e grupos à aplicação. Se escolher determinar quem vai ser aprovisionado com base apenas em atributos do utilizador ou grupo, pode utilizar um filtro de âmbito conforme descrito [aqui](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

* Ao atribuir utilizadores e grupos ao Utilizador Gerido pela Empresa GitHub, tem de selecionar outra função que não o **Acesso Predefinido**. Os utilizadores com a função Acesso Predefinido são excluídos do aprovisionamento e marcados como não autorizados de forma efetiva nos registos de aprovisionamento.

* Comece pequeno. Teste com um pequeno conjunto de utilizadores e grupos antes de implementar para todos. Quando o âmbito do aprovisionamento está definido para os utilizadores e os grupos atribuídos, pode controlar isto ao atribuir um ou dois utilizadores ou grupos à aplicação. Quando o âmbito está definido para todos os utilizadores e grupos, pode especificar um [filtro de âmbito baseado em atributos](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).


## <a name="step-5-configure-automatic-user-provisioning-to-github-enterprise-managed-user"></a>Passo 5. Configure o fornecimento automático de utilizadores ao GitHub Enterprise Managed User

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos no TestApp com base em atribuições de utilizador e/ou grupo em Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-github-enterprise-managed-user-in-azure-ad"></a>Para configurar o provisionamento automático do utilizador para o GitHub Enterprise Managed User em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais** e, em seguida, **Todas as aplicações**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **GitHub Enterprise Managed User**.

    ![O link do utilizador gerido pela empresa GitHub na lista de aplicações](common/all-applications.png)

3. Selecione o separador **Aprovisionamento**.

    ![Separador Aprovisionamento](common/provisioning.png)

4. Defina o **Modo de Aprovisionamento** como **Automático**.

    ![Separador de provisionamento automático](common/provisioning-automatic.png)

5. Na secção **Credenciais de Administração,** insira o URL do seu Cliente Gerido pela Empresa GitHub e o Token Secreto. Clique em **Testar a Ligação** para garantir que o Azure AD pode ligar-se ao Utilizador Gerido pela Empresa GitHub. Se a ligação falhar, certifique-se de que a sua conta gitHub Enterprise Managed User criou o símbolo secreto como proprietário da empresa e tente novamente.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **E-mail de Notificação**, introduza o endereço de e-mail de uma pessoa ou um grupo que deve receber as notificações de erro de aprovisionamento e marque a caixa de verificação **Enviar uma notificação de e-mail quando ocorre uma falha**.

    ![E-mail de Notificação](common/provisioning-notification-email.png)

7. Selecione **Guardar**.

8. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users para GitHub Enterprise Managed User**.

9. Reveja os atributos do utilizador que são sincronizados de Azure AD para GitHub Enterprise Managed User na secção **De mapeamento de atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para corresponder às contas de utilizador no GitHub Enterprise Managed User para operações de atualização. Se optar por alterar o [atributo de alvo correspondente,](../app-provisioning/customize-application-attributes.md)terá de garantir que a API do utilizador gerido pela empresa GitHub suporta utilizadores filtrantes com base nesse atributo. Selecione o botão **Guardar** para escoar quaisquer alterações.

   |Atributo|Tipo|Suportado para filtragem|
   |---|---|---|
   |externalId|String|&check;|
   |userName|String|
   |active|Booleano|
   |funções|String|
   |displayName|String|
   |name.givenName|String|
   |name.familyName|String|
   |nome.formatado|String|
   |emails[type eq "work"].value|String|
   |e-mails[tipo eq "casa"].valor|String|
   |e-mails[tipo eq "outros"].valor|String|

10. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Groups para GitHub Enterprise Managed User**.

11. Reveja os atributos do grupo que são sincronizados de Azure AD para GitHub Enterprise Managed User na secção **De mapeamento de atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para corresponder aos grupos do Utilizador Gerido pela Empresa GitHub para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

      |Atributo|Tipo|Suportado para filtragem|
      |---|---|---|
      |externalId|String|&check;|
      |displayName|String|
      |membros|Referência|

12. Para configurar filtros de âmbito, veja as instruções seguintes disponibilizadas no [Tutorial de filtro de âmbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para ativar o serviço de prestação de Ad Azure para o Utilizador Gerido pela Empresa GitHub, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

    ![Estado do Aprovisionamento Ativado](common/provisioning-toggle-on.png)

14. Defina os utilizadores e/ou grupos que pretende prestar ao Utilizador Gerido pela Empresa GitHub, escolhendo os valores pretendidos no **Âmbito** na secção **Definições.**

    ![Âmbito de Aprovisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para aprovisionar, clique em **Guardar**.

    ![Guardar Configuração de Aprovisionamento](common/provisioning-configuration-save.png)

Esta operação inicia o ciclo de sincronização inicial de todos os utilizadores e grupos definidos no **Âmbito** na secção **Definições**. O ciclo inicial leva mais tempo a ser executado do que os ciclos subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de aprovisionamento do Azure AD esteja em execução.

## <a name="step-6-monitor-your-deployment"></a>Passo 6. Monitorizar a implementação
Depois de configurar o aprovisionamento, utilize os seguintes recursos para monitorizar a sua implementação:

1. Utilize os [registos de aprovisionamento](../reports-monitoring/concept-provisioning-logs.md) para determinar quais os utilizadores que foram aprovisionados com ou sem êxito
2. Verifique a [barra de progresso](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) para ver o estado do ciclo de aprovisionamento e quão próximo está da conclusão
3. Se a configuração de aprovisionamento parecer estar num mau estado de funcionamento, a aplicação vai entrar em quarentena. Saiba mais sobre os estados de quarentena [aqui](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento de contas de utilizador para Aplicações Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como analisar os registos e obter relatórios sobre a atividade de aprovisionamento](../app-provisioning/check-status-user-account-provisioning.md)