---
title: 'Tutorial: Configurar InVision para fornecimento automático de utilizadores com Diretório Ativo Azure / Microsoft Docs'
description: Saiba como provisão e desprovisionamento automática de contas de utilizadores do Azure AD para o InVision.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 72518dda-d485-45c8-849e-6b27ee09d9a8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/25/2020
ms.author: Zhchia
ms.openlocfilehash: c58a3d9f07b62e92d1e71096ae9dd3fc85515843
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92459888"
---
# <a name="tutorial-configure-invision-for-automatic-user-provisioning"></a>Tutorial: Configurar InVision para fornecimento automático de utilizadores

Este tutorial descreve os passos necessários para realizar tanto no InVision como no Azure Ative Directory (AZure AD) para configurar o fornecimento automático do utilizador. Quando configurado, o Azure AD fornece automaticamente e desproteja utilizadores e grupos para [o InVision](https://www.invisionapp.com/) utilizando o serviço de provisionamento Azure AD. Para obter detalhes importantes sobre o que este serviço faz, como funciona e perguntas frequentes, veja [Automatizar o aprovisionamento e desaprovisionamento de utilizadores em aplicações SaaS no Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Capacidades suportadas
> [!div class="checklist"]
> * Criar utilizadores no InVision
> * Remova os utilizadores no InVision quando já não necessitam de acesso
> * Mantenha os atributos do utilizador sincronizados entre Azure AD e InVision
> * [Único sinal de insusição](./invision-tutorial.md) no InVision (obrigatório)

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* [Um inquilino da AD AZure](../develop/quickstart-create-new-tenant.md) 
* Uma conta de utilizador no Azure AD com [permissão](../users-groups-roles/directory-assign-admin-roles.md) para configurar o aprovisionamento (por ex., Administrador de Aplicações, Administrador de Aplicações de Cloud, Proprietário da Aplicação ou Administrador Global). 
* Uma [conta InVision Enterprise](https://www.invisionapp.com/enterprise) com SSO ativada.
* Uma conta de utilizador no InVision com permissões de Administração.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passo 1. Planear a sua implementação de aprovisionamento
1. Saiba [como funciona o serviço de aprovisionamento](../app-provisioning/user-provisioning.md).
2. Determine quem vai estar no [âmbito do aprovisionamento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determine quais os dados a [mapear entre AZure AD e InVision](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-invision-to-support-provisioning-with-azure-ad"></a>Passo 2. Configure a InVision para apoiar o provisionamento com a Azure AD

1. Inscreva-se na sua [conta InVision Enterprise](https://www.invisionapp.com/enterprise) como Administrador ou Proprietário. Abra a gaveta **de definições** de equipa na parte inferior esquerda e selecione **Definições**.

   ![Configuração de configuração SCIM](./media/invision-provisioning-tutorial/invision-scim-settings.png)

2. Selecione **Alterar** o Provisionamento do Utilizador com a definição **SCIM.**

   ![Definições de provisionamento SCIM](./media/invision-provisioning-tutorial/invision-provisioning-settings.png)

3. Selecione o toggle para ativar o fornecimento SCIM. Note que deve ter SSO configurado primeiro para poder ativar o SCIM:

   ![SCIM permite o provisionamento](./media/invision-provisioning-tutorial/enable-scim-provisioning.png)

4. Copie o **URL da API scim** e apenda-se `/scim/v2` ao URL. Copie o **símbolo de autenticação**. Guarde estes valores para mais tarde utilizar nos campos **URL** e **Secret Token** no separador Provisioning da sua aplicação InVision no portal Azure.

   ![Ficha de acesso SCIM](./media/invision-provisioning-tutorial/invision-access-token.png)


## <a name="step-3-add-invision-from-the-azure-ad-application-gallery"></a>Passo 3. Adicionar InVision a partir da galeria de aplicações AD Azure

Adicione InVision a partir da galeria de aplicações AD Azure para começar a gerir o fornecimento ao InVision. Se tiver configurado previamente o InVision para SSO, pode utilizar a mesma aplicação. No entanto, é recomendável criar uma aplicação separada ao testar a integração inicialmente. Saiba mais sobre como adicionar uma aplicação a partir da galeria [aqui](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passo 4: Determinar quem vai estar no âmbito do aprovisionamento 

O serviço de aprovisionamento do Azure AD permite-lhe determinar quem vai ser aprovisionado com base na atribuição à aplicação e/ou com base em atributos do utilizador/grupo. Se optar por determinar quem vai ser aprovisionado na sua aplicação com base na atribuição, pode utilizar os seguintes [passos](../manage-apps/assign-user-or-group-access-portal.md) para atribuir utilizadores e grupos à aplicação. Se escolher determinar quem vai ser aprovisionado com base apenas em atributos do utilizador ou grupo, pode utilizar um filtro de âmbito conforme descrito [aqui](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Ao atribuir utilizadores e grupos ao InVision, tem de selecionar outra função que não o **Acesso Predefinido**. Os utilizadores com a função Acesso Predefinido são excluídos do aprovisionamento e marcados como não autorizados de forma efetiva nos registos de aprovisionamento. Se a única função disponível na aplicação for a função de acesso predefinido, pode [atualizar o manifesto de aplicação](../develop/howto-add-app-roles-in-azure-ad-apps.md) para adicionar funções adicionais. 

* Comece pequeno. Teste com um pequeno conjunto de utilizadores e grupos antes de implementar para todos. Quando o âmbito do aprovisionamento está definido para os utilizadores e os grupos atribuídos, pode controlar isto ao atribuir um ou dois utilizadores ou grupos à aplicação. Quando o âmbito está definido para todos os utilizadores e grupos, pode especificar um [filtro de âmbito baseado em atributos](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-invision"></a>Passo 5. Configure o fornecimento automático de utilizadores ao InVision 

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos no TestApp com base em atribuições de utilizador e/ou grupo em Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-invision-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para o InVision em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais** e, em seguida, **Todas as aplicações**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **InVision**.

    ![O link InVision na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Aprovisionamento**.

    ![Separador Aprovisionamento](common/provisioning.png)

4. Defina o **Modo de Aprovisionamento** como **Automático**.

    ![Modo de aprovisionamento](common/provisioning-automatic.png)

5. Sob a secção de Credenciais de **Administração,** insira o valor URL SCIM API recuperado anteriormente no **URL do inquilino.** Insira o valor simbólico de autenticação recuperado anteriormente em **Secret Token**. Clique em **'Testar' Ligação** para garantir que o Azure AD pode ligar-se ao InVision. Se a ligação falhar, certifique-se de que a sua conta InVision tem permissões de Administração e tente novamente.

    ![Credenciais de administrador](./media/inVision-provisioning-tutorial/provisioning.png)

6. No campo **E-mail de Notificação**, introduza o endereço de e-mail de uma pessoa ou um grupo que deve receber as notificações de erro de aprovisionamento e marque a caixa de verificação **Enviar uma notificação de e-mail quando ocorre uma falha**.

    ![E-mail de Notificação](common/provisioning-notification-email.png)

7. Selecione **Guardar**.

8. Na secção **Mappings,** selecione **Provision Azure Ative Directory Users**.

9. Reveja os atributos do utilizador que são sincronizados de AD AD a InVision na secção **De mapeamento de atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas de utilizador no InVision para operações de atualização. Se optar por alterar o [atributo de alvo correspondente,](../app-provisioning/customize-application-attributes.md)terá de garantir que a API do InVision suporta a filtragem dos utilizadores com base nesse atributo. Selecione o botão **Guardar** para escoar quaisquer alterações.

   |Atributo|Tipo|Suportado para filtragem|
   |---|---|---|
   |userName|String|&check;|
   |active|Booleano|
   |emails[type eq "work"].value|String|
   |name.givenName|String|
   |name.familyName|String|

10. Para configurar filtros de âmbito, veja as instruções seguintes disponibilizadas no [Tutorial de filtro de âmbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para ativar o serviço de prestação de Ad Azure para o InVision, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

    ![Estado do Aprovisionamento Ativado](common/provisioning-toggle-on.png)

12. Defina os utilizadores e/ou grupos que deseja prestar ao InVision, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de Aprovisionamento](common/provisioning-scope.png)

13. Quando estiver pronto para aprovisionar, clique em **Guardar**.

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