---
title: 'Tutorial: Configurar Zoom para fornecimento automático de utilizadores com Azure Ative Directory | Microsoft Docs'
description: Saiba como provisão e desavisagem automática de contas de utilizadores do Azure AD ao Zoom.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/3/2019
ms.author: Zhchia
ms.openlocfilehash: c87c3692d7e1106e0952b85791da663e03c6001d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96182483"
---
# <a name="tutorial-configure-zoom-for-automatic-user-provisioning"></a>Tutorial: Configure Zoom para o fornecimento automático de utilizadores

Este tutorial descreve os passos necessários para realizar tanto no Zoom como no Azure Ative Directory (Azure AD) para configurar o fornecimento automático do utilizador. Quando configurado, a Azure AD fornece automaticamente e desescvisões utilizadores e grupos para [Zoom](https://zoom.us/pricing/) usando o serviço de provisionamento Azure AD. Para obter detalhes importantes sobre o que este serviço faz, como funciona e perguntas frequentes, veja [Automatizar o aprovisionamento e desaprovisionamento de utilizadores em aplicações SaaS no Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Capacidades suportadas
> [!div class="checklist"]
> * Criar utilizadores em Zoom
> * Remova os utilizadores no Zoom quando já não necessitam de acesso
> * Mantenha os atributos do utilizador sincronizados entre Azure AD e Zoom
> * [Único sinal de zoom](./zoom-tutorial.md) (recomendado)

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* [Um inquilino do Azure AD](../develop/quickstart-create-new-tenant.md).
* Uma conta de utilizador no Azure AD com [permissão](../roles/permissions-reference.md) para configurar o aprovisionamento (por ex., Administrador de Aplicações, Administrador de Aplicações de Cloud, Proprietário da Aplicação ou Administrador Global). 
* [Um inquilino de Zoom.](https://zoom.us/pricing)
* Uma conta de utilizador em Zoom com permissões de Administrador.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passo 1. Planear a sua implementação de aprovisionamento
1. Saiba [como funciona o serviço de aprovisionamento](../app-provisioning/user-provisioning.md).
2. Determine quem vai estar no [âmbito do aprovisionamento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determine quais os dados a [mapear entre Azure AD e Zoom](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-zoom-to-support-provisioning-with-azure-ad"></a>Passo 2. Configure Zoom para apoiar o provisionamento com a Azure AD

1. Inscreva-se na sua [Consola de Administração de Zoom](https://zoom.us/signin). Navegue para **o Mercado de Aplicações Advanced >** no painel de navegação à esquerda.

    ![Integrações de Zoom](media/zoom-provisioning-tutorial/zoom01.png)

2. Navegue para **Gerir** no canto superior direito da página. 

    ![Screenshot do Zoom App Marketplace com a opção Manage called.](media/zoom-provisioning-tutorial/zoom02.png)

3. Navegue para a sua aplicação AD Azure criada. 
    
    ![Screenshot da secção De Aplicações Criadas com a aplicação Azure A D chamada.](media/zoom-provisioning-tutorial/zoom03.png)

4. Selecione **Credenciais de Aplicação** no painel de navegação esquerdo.

    ![Screenshot do painel de navegação esquerdo com a opção De Credenciais de Aplicação em destaque.](media/zoom-provisioning-tutorial/zoom04.png)

5. Copie e guarde o **Token JWT**. Este valor será introduzido no campo **Secret Token** no separador Provisioning da sua aplicação Zoom no portal Azure. Se precisar de um novo token não expirado, terá de reconfigurar o tempo de validade que irá gerar um novo token. 

    ![Screenshot da página De Credenciais de Aplicação.](media/zoom-provisioning-tutorial/zoom05.png)

## <a name="step-3-add-zoom-from-the-azure-ad-application-gallery"></a>Passo 3. Adicione Zoom da galeria de aplicações AZure AD

Adicione Zoom da galeria de aplicações AZure AD para começar a gerir o provisionamento ao Zoom. Se tiver configurado previamente Zoom para SSO, pode utilizar a mesma aplicação. No entanto, é recomendável criar uma aplicação separada ao testar a integração inicialmente. Saiba mais sobre como adicionar uma aplicação a partir da galeria [aqui](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passo 4: Determinar quem vai estar no âmbito do aprovisionamento 

O serviço de aprovisionamento do Azure AD permite-lhe determinar quem vai ser aprovisionado com base na atribuição à aplicação e/ou com base em atributos do utilizador/grupo. Se optar por determinar quem vai ser aprovisionado na sua aplicação com base na atribuição, pode utilizar os seguintes [passos](../manage-apps/assign-user-or-group-access-portal.md) para atribuir utilizadores e grupos à aplicação. Se escolher determinar quem vai ser aprovisionado com base apenas em atributos do utilizador ou grupo, pode utilizar um filtro de âmbito conforme descrito [aqui](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Ao atribuir utilizadores e grupos ao Zoom, tem de selecionar outra função que não **o Acesso Predefinido**. Os utilizadores com a função Acesso Predefinido são excluídos do aprovisionamento e marcados como não autorizados de forma efetiva nos registos de aprovisionamento. Se a única função disponível na aplicação for a função de acesso predefinido, pode [atualizar o manifesto de aplicação](../develop/howto-add-app-roles-in-azure-ad-apps.md) para adicionar funções adicionais. 

* Comece pequeno. Teste com um pequeno conjunto de utilizadores e grupos antes de implementar para todos. Quando o âmbito do aprovisionamento está definido para os utilizadores e os grupos atribuídos, pode controlar isto ao atribuir um ou dois utilizadores ou grupos à aplicação. Quando o âmbito está definido para todos os utilizadores e grupos, pode especificar um [filtro de âmbito baseado em atributos](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-zoom"></a>Passo 5. Configure o fornecimento automático do utilizador ao Zoom 

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos no TestApp com base em atribuições de utilizador e/ou grupo em Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-zoom-in-azure-ad"></a>Para configurar o fornecimento automático do utilizador para zoom em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais** e, em seguida, **Todas as aplicações**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Zoom**.

    ![O link Zoom na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Aprovisionamento**.

    ![Screenshot das opções De gestão com a opção Provisioning chamada.](common/provisioning.png)

4. Defina o **Modo de Aprovisionamento** como **Automático**.

    ![Screenshot da lista de retirada do modo de provisionamento com a opção Automática chamada.](common/provisioning-automatic.png)

5. Sob a secção **de Credenciais de Administração,** `https://api.zoom.us/scim` insira em **URL de inquilino.** Insira o valor **JWT Token** recuperado anteriormente em **Secret Token**. Clique em **Testar a Ligação** para garantir que o Azure AD pode ligar-se ao Zoom. Se a ligação falhar, certifique-se de que a sua conta Zoom tem permissões de Administração e tente novamente.

    ![Provisão de zoom](./media/zoom-provisioning-tutorial/provisioning.png)

6. No campo **E-mail de Notificação**, introduza o endereço de e-mail de uma pessoa ou um grupo que deve receber as notificações de erro de aprovisionamento e marque a caixa de verificação **Enviar uma notificação de e-mail quando ocorre uma falha**.

    ![E-mail de Notificação](common/provisioning-notification-email.png)

7. Selecione **Guardar**.

8. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users para Zoom**.

9. Reveja os atributos do utilizador que são sincronizados de AD AD a Zoom na secção **De mapeamento de atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas do utilizador no Zoom para operações de atualização. Se optar por alterar o [atributo de alvo correspondente,](../app-provisioning/customize-application-attributes.md)terá de garantir que a API de Zoom suporta utilizadores filtrantes com base nesse atributo. Selecione o botão **Guardar** para escoar quaisquer alterações.

   |Atributo|Tipo|
   |---|---|
   |userName|String|
   |active|Booleano|
   |name.givenName|String|
   |name.familyName|String|
   |e-mails[tipo eq "work"]|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String|

10. Para configurar filtros de âmbito, veja as instruções seguintes disponibilizadas no [Tutorial de filtro de âmbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para ativar o serviço de provisionamento Azure AD para Zoom, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

    ![Estado do Aprovisionamento Ativado](common/provisioning-toggle-on.png)

12. Defina os utilizadores e/ou grupos que deseja providenciar ao Zoom, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de Aprovisionamento](common/provisioning-scope.png)

13. Quando estiver pronto para aprovisionar, clique em **Guardar**.

    ![Guardar Configuração de Aprovisionamento](common/provisioning-configuration-save.png)

Esta operação inicia o ciclo de sincronização inicial de todos os utilizadores e grupos definidos no **Âmbito** na secção **Definições**. O ciclo inicial leva mais tempo a ser executado do que os ciclos subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de aprovisionamento do Azure AD esteja em execução. 

## <a name="step-6-monitor-your-deployment"></a>Passo 6. Monitorizar a implementação
Depois de configurar o aprovisionamento, utilize os seguintes recursos para monitorizar a sua implementação:

1. Utilize os [registos de aprovisionamento](../reports-monitoring/concept-provisioning-logs.md) para determinar quais os utilizadores que foram aprovisionados com ou sem êxito
2. Verifique a [barra de progresso](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) para ver o estado do ciclo de aprovisionamento e quão próximo está da conclusão
3. Se a configuração de aprovisionamento parecer estar num mau estado de funcionamento, a aplicação vai entrar em quarentena. Saiba mais sobre os estados de quarentena [aqui](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="connector-limitations"></a>Limitações do conector
* O Zoom só permite um máximo de 9.999 utilizadores básicos hoje em dia.

## <a name="change-log"></a>Change log
* 05/14/2020 - Apoio às operações de ATUALIZAÇÃO adicionadas para e-mails[tipo eq "trabalho"] atribuem.
* 10/20/2020 - Apoio adicional para duas novas funções "Licenciada" e "On-Prem" para substituir as funções existentes "Pro" e "Corp". O apoio às funções "Pro" e "Corp" será removido no futuro.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento de contas de utilizador para Aplicações Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como analisar os registos e obter relatórios sobre a atividade de aprovisionamento](../app-provisioning/check-status-user-account-provisioning.md)