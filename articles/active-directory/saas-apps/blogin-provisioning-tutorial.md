---
title: 'Tutorial: Configurar blogIn para fornecimento automático de utilizadores com Azure Ative Directory | Microsoft Docs'
description: Saiba como provisão e desa provisionar automaticamente as contas de utilizadores do Azure AD para o BlogIn.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 4b2ef46c-97a1-450d-bbc8-b2fa76280219
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/08/2020
ms.author: Zhchia
ms.openlocfilehash: 5d75aeb4771d49266e6cb09286b026054319591b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97674247"
---
# <a name="tutorial-configure-blogin-for-automatic-user-provisioning"></a>Tutorial: Configurar blogIn para fornecimento automático de utilizadores

Este tutorial descreve os passos que precisa de executar tanto no BlogIn como no Azure Ative Directory (Azure AD) para configurar o fornecimento automático do utilizador. Quando configurado, a Azure AD fornece automaticamente e desresiná-lo os utilizadores e grupos para [o BlogIn](https://blogin.co/) utilizando o serviço de provisionamento Azure AD. Para obter detalhes importantes sobre o que este serviço faz, como funciona e perguntas frequentes, veja [Automatizar o aprovisionamento e desaprovisionamento de utilizadores em aplicações SaaS no Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Capacidades Suportadas
> [!div class="checklist"]
> * Criar utilizadores no BlogIn
> * Remova os utilizadores no BlogIn quando já não necessitam de acesso
> * Mantenha os atributos do utilizador sincronizados entre Azure AD e BlogIn
> * Grupos de provisão e membros do grupo no BlogIn
> * [Único sinal de insusição no](./blogin-tutorial.md) BlogIn (recomendado)

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* [Um inquilino da AD AZure](../develop/quickstart-create-new-tenant.md) 
* Uma conta de utilizador em Azure AD com [permissão](../roles/permissions-reference.md) para configurar o provisionamento (por exemplo, Administrador de Aplicação, Administrador de Aplicação cloud, Proprietário de Aplicações ou Administrador Global). 
* Uma conta de utilizador no BlogIn com função de Administrador.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passo 1. Planear a sua implementação de aprovisionamento
1. Saiba [como funciona o serviço de aprovisionamento](../app-provisioning/user-provisioning.md).
2. Determine quem vai estar no [âmbito do aprovisionamento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determine quais os dados a [mapear entre Azure AD e BlogIn](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-blogin-to-support-provisioning-with-azure-ad"></a>Passo 2. Configure BlogIn para apoiar o provisionamento com Azure AD

Para configurar o fornecimento de utilizadores no **BlogIn,** faça login na sua conta BlogIn e siga estes passos:

1. Navegar para **definições**  >  **Configurações Configurações Configuração**  >  **SSO & fornecimento do utilizador**.
2. Mude para o **separador de provisionamento do Utilizador** e altere o estado de provisionamento do utilizador para **On**.
3. Clique no botão **Guardar alterações.** Após a primeira salvação, o **símbolo secreto (Portador)** será gerado.
4. Copiar **base (inquilino) URL** e **valores simbólicos secretos (portador).** Estes valores serão introduzidos nos campos URL e Secret Token no separador Provisioning da sua aplicação BlogIn no portal Azure.

Para obter uma explicação mais detalhada da configuração do provisionamento do utilizador no BlogIn, consulte [Configurar o Provisionamento do Utilizador via SCIM](https://blogin.co/blog/set-up-user-provisioning-via-scim-254/). Por favor contacte a [equipa de suporte do BlogIn](mailto:support@blogin.co) se tiver alguma dúvida ou precisar de ajuda.

## <a name="step-3-add-blogin-from-the-azure-ad-application-gallery"></a>Passo 3. Adicione BlogIn da galeria de aplicações AD Azure

Adicione o BlogIn da galeria de aplicações AZure AD para começar a gerir o fornecimento ao BlogIn. Se tiver configurado previamente o BlogIn para SSO, pode utilizar a mesma aplicação. No entanto, é recomendável criar uma aplicação separada ao testar a integração inicialmente. Saiba mais sobre como adicionar uma aplicação a partir da galeria [aqui](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passo 4: Determinar quem vai estar no âmbito do aprovisionamento 

O serviço de aprovisionamento do Azure AD permite-lhe determinar quem vai ser aprovisionado com base na atribuição à aplicação e/ou com base em atributos do utilizador/grupo. Se optar por determinar quem vai ser aprovisionado na sua aplicação com base na atribuição, pode utilizar os seguintes [passos](../manage-apps/assign-user-or-group-access-portal.md) para atribuir utilizadores e grupos à aplicação. Se escolher determinar quem vai ser aprovisionado com base apenas em atributos do utilizador ou grupo, pode utilizar um filtro de âmbito conforme descrito [aqui](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Ao atribuir utilizadores e grupos ao BlogIn, tem de selecionar outra função que não o **Acesso Predefinido.** Os utilizadores com a função Acesso Predefinido são excluídos do aprovisionamento e marcados como não autorizados de forma efetiva nos registos de aprovisionamento. Se a única função disponível na aplicação for a função de acesso predefinido, pode [atualizar o manifesto de aplicação](../develop/howto-add-app-roles-in-azure-ad-apps.md) para adicionar funções adicionais. 

* Comece pequeno. Teste com um pequeno conjunto de utilizadores e grupos antes de implementar para todos. Quando o âmbito do aprovisionamento está definido para os utilizadores e os grupos atribuídos, pode controlar isto ao atribuir um ou dois utilizadores ou grupos à aplicação. Quando o âmbito está definido para todos os utilizadores e grupos, pode especificar um [filtro de âmbito baseado em atributos](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-blogin"></a>Passo 5. Configure o fornecimento automático de utilizadores ao BlogIn 

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos no TestApp com base em atribuições de utilizador e/ou grupo em Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-blogin-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para blogIn em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais** e, em seguida, **Todas as aplicações**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **BlogIn**.

    ![O link BlogIn na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Aprovisionamento**.

    ![Separador Aprovisionamento](common/provisioning.png)

4. Defina o **Modo de Aprovisionamento** como **Automático**.

    ![Separador de provisionamento automático](common/provisioning-automatic.png)

5. Na secção **Credenciais de Administração,** insira o seu BLOGIn Tenant URL e Secret Token. Clique em **Testar a Ligação** para garantir que o Azure AD pode ligar-se ao Clarizen. Se a ligação falhar, certifique-se de que a sua conta Clarizen tem permissões de Administração e tente novamente.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **E-mail de Notificação**, introduza o endereço de e-mail de uma pessoa ou um grupo que deve receber as notificações de erro de aprovisionamento e marque a caixa de verificação **Enviar uma notificação de e-mail quando ocorre uma falha**.

    ![E-mail de Notificação](common/provisioning-notification-email.png)

7. Selecione **Guardar**.

8. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users para o BlogIn**.

9. Reveja os atributos do utilizador que são sincronizados de Azure AD para BlogIn na secção **De mapeamento de atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas de utilizador no BlogIn para operações de atualização. Se optar por alterar o [atributo de alvo correspondente,](../app-provisioning/customize-application-attributes.md)terá de garantir que a API do BlogIn suporta a filtragem dos utilizadores com base nesse atributo. Selecione o botão **Guardar** para escoar quaisquer alterações.

   |Atributo|Tipo|
   |---|---|
   |userName|String|
   |active|Booleano|
   |título|String|
   |emails[type eq "work"].value|String|
   |name.givenName|String|
   |name.familyName|String|
   |nome.formatado|String|
   |phoneNumbers[type eq "work"].value|String|

10. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Groups para BlogIn**.

11. Reveja os atributos do grupo que são sincronizados de Azure AD para BlogIn na secção **Attribute-Mapping.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar com os grupos no BlogIn para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

      |Atributo|Tipo|
      |---|---|
      |displayName|String|
      |membros|Referência|

12. Para configurar filtros de âmbito, veja as instruções seguintes disponibilizadas no [Tutorial de filtro de âmbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para ativar o serviço de prestação de Ad Azure para o BlogIn, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

    ![Estado do Aprovisionamento Ativado](common/provisioning-toggle-on.png)

14. Defina os utilizadores e/ou grupos que deseja prestar ao BlogIn, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

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