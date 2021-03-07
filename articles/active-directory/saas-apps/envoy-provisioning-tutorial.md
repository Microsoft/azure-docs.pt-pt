---
title: 'Tutorial: Configure Envoy para o fornecimento automático de utilizadores com Azure Ative Directory | Microsoft Docs'
description: Saiba como providenciar e desa provisionar automaticamente as contas de utilizadores do Azure AD ao Enviado.
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
ms.openlocfilehash: b18ec2531b26574357f56c11558f2afd84467495
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102437694"
---
# <a name="tutorial-configure-envoy-for-automatic-user-provisioning"></a>Tutorial: Enviado de configuração para o fornecimento automático de utilizadores

Este tutorial descreve os passos necessários para realizar tanto no Envoy como no Azure Ative Directory (Azure AD) para configurar o fornecimento automático do utilizador. Quando configurado, a Azure AD fornece automaticamente e desescvisões utilizadores e grupos para [enviado](https://envoy.com/pricing/) usando o serviço de provisionamento Azure AD. Para obter detalhes importantes sobre o que este serviço faz, como funciona e perguntas frequentes, veja [Automatizar o aprovisionamento e desaprovisionamento de utilizadores em aplicações SaaS no Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Capacidades suportadas
> [!div class="checklist"]
> * Criar utilizadores em Enviado
> * Remova os utilizadores no Enviado quando já não necessitam de acesso
> * Mantenha os atributos do utilizador sincronizados entre Azure AD e Envoy
> * Grupos de provisão e membros do grupo no Enviado
> * [Único sinal de aprovação](./envoy-tutorial.md) para o enviado (recomendado)

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* [Um inquilino da AD AZure](../develop/quickstart-create-new-tenant.md) 
* Uma conta de utilizador no Azure AD com [permissão](../roles/permissions-reference.md) para configurar o aprovisionamento (por ex., Administrador de Aplicações, Administrador de Aplicações de Cloud, Proprietário da Aplicação ou Administrador Global). 
* [Um inquilino enviado.](https://envoy.com/pricing/)
* Uma conta de utilizador em Enviado com permissões de Administração.

> [!NOTE]
> Esta integração também está disponível para usar a partir do ambiente cloud do governo dos EUA Azure AD. Você pode encontrar esta aplicação na Azure AD US Government Cloud Application Gallery e configurá-la da mesma forma que você faz a partir de nuvem pública.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passo 1. Planear a sua implementação de aprovisionamento
1. Saiba [como funciona o serviço de aprovisionamento](../app-provisioning/user-provisioning.md).
2. Determine quem vai estar no [âmbito do aprovisionamento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determinar quais os dados a [mapear entre a Azure AD e o Enviado.](../app-provisioning/customize-application-attributes.md) 

## <a name="step-2-configure-envoy-to-support-provisioning-with-azure-ad"></a>Passo 2. Configure enviado para apoiar o provisionamento com a Azure AD

1. Inscreva-se na sua [Consola de Administração enviada.](https://dashboard.envoy.com/login) Clique em **Integrações**.

    ![Integrações do Enviado](media/envoy-provisioning-tutorial/envoy01.png)

2. Clique em **Instalar** para a **integração do Microsoft Azure SCIM**.

    ![Instalação do enviado](media/envoy-provisioning-tutorial/envoy02.png)

3. Clique em **Guardar** para **Sincronizar todos os utilizadores.** 

    ![Enviado Salvar](media/envoy-provisioning-tutorial/envoy03.png)

4. Copie **o TOKEN DO PORTADOR DAAUTH**. Este valor será introduzido no campo **Secret Token** no separador de provisionamento da sua aplicação envoy no portal Azure.
    
    ![Enviado OAUTH](media/envoy-provisioning-tutorial/envoy04.png)

## <a name="step-3-add-envoy-from-the-azure-ad-application-gallery"></a>Passo 3. Adicione enviado da galeria de aplicações AZure AD

Adicione o enviado da galeria de aplicações Azure AD para começar a gerir o fornecimento ao Enviado. Se já configurar o Enviado para SSO, pode utilizar a mesma aplicação. No entanto, é recomendável criar uma aplicação separada ao testar a integração inicialmente. Saiba mais sobre como adicionar uma aplicação a partir da galeria [aqui](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passo 4: Determinar quem vai estar no âmbito do aprovisionamento 

O serviço de aprovisionamento do Azure AD permite-lhe determinar quem vai ser aprovisionado com base na atribuição à aplicação e/ou com base em atributos do utilizador/grupo. Se optar por determinar quem vai ser aprovisionado na sua aplicação com base na atribuição, pode utilizar os seguintes [passos](../manage-apps/assign-user-or-group-access-portal.md) para atribuir utilizadores e grupos à aplicação. Se escolher determinar quem vai ser aprovisionado com base apenas em atributos do utilizador ou grupo, pode utilizar um filtro de âmbito conforme descrito [aqui](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Ao atribuir utilizadores e grupos ao Enviado, deve selecionar outra função que não **o Acesso Predefinido**. Os utilizadores com a função Acesso Predefinido são excluídos do aprovisionamento e marcados como não autorizados de forma efetiva nos registos de aprovisionamento. Se a única função disponível na aplicação for a função de acesso predefinido, pode [atualizar o manifesto de aplicação](../develop/howto-add-app-roles-in-azure-ad-apps.md) para adicionar funções adicionais. 

* Comece pequeno. Teste com um pequeno conjunto de utilizadores e grupos antes de implementar para todos. Quando o âmbito do aprovisionamento está definido para os utilizadores e os grupos atribuídos, pode controlar isto ao atribuir um ou dois utilizadores ou grupos à aplicação. Quando o âmbito está definido para todos os utilizadores e grupos, pode especificar um [filtro de âmbito baseado em atributos](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-envoy"></a>Passo 5. Configure o fornecimento automático de utilizadores ao Enviado 

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos no TestApp com base em atribuições de utilizador e/ou grupo em Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-envoy-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para o Enviado em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais** e, em seguida, **Todas as aplicações**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Envoy.**

    ![O link do enviado na lista de candidaturas](common/all-applications.png)

3. Selecione o separador **Aprovisionamento**.

    ![Screenshot das opções De gestão com a opção Provisioning chamada.](common/provisioning.png)

4. Defina o **Modo de Aprovisionamento** como **Automático**.

    ![Screenshot da lista de retirada do modo de provisionamento com a opção Automática chamada.](common/provisioning-automatic.png)

5. Sob a secção **de Credenciais de Administração,** entrada `https://app.envoy.com/scim/v2` na URL do **inquilino.** Insira o valor **TOKEN do PORTADOR DAAUTH** recuperado anteriormente em **Secret Token**. Clique em **'Testar' a Ligação** para garantir que o Azure AD pode ligar-se ao Enviado. Se a ligação falhar, certifique-se de que a sua conta de Administrador tem permissões de administração e tente novamente.

   ![A screenshot mostra a caixa de diálogo de Admin Credentials, onde pode inserir o seu Inquilino U R L e Secret Token.](./media/envoy-tutorial/provisioning.png)

6. No campo **E-mail de Notificação**, introduza o endereço de e-mail de uma pessoa ou um grupo que deve receber as notificações de erro de aprovisionamento e marque a caixa de verificação **Enviar uma notificação de e-mail quando ocorre uma falha**.

    ![E-mail de Notificação](common/provisioning-notification-email.png)

7. Selecione **Guardar**.

8. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users to Envoy**.

9. Reveja os atributos do utilizador que são sincronizados de Azure AD para Enviado na secção **De mapeamento de Atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para corresponder às contas do utilizador no Enviado para operações de atualização. Se optar por alterar o [atributo de alvo correspondente,](../app-provisioning/customize-application-attributes.md)terá de garantir que a API do Enviado suporta a filtragem dos utilizadores com base nesse atributo. Selecione o botão **Guardar** para escoar quaisquer alterações.

   |Atributo|Tipo|
   |---|---|
   |userName|String|
   |externalId|String|
   |displayName|String|
   |título|String|
   |emails[type eq "work"].value|String|
   |preferiuLanguage|String|
   |departamento|String|
   |endereços[tipo eq "work"].país|String|
   |endereços[tipo eq "work"].localidade|String|
   |endereços[tipo eq "work"].região|String|
   |endereços[tipo eq "work"].postalCode|String|
   |endereços[tipo eq "work"].formatado|String|
   |endereços[tipo eq "work"].streetAddress|String|
   |name.givenName|String|
   |name.familyName|String|
   |nome.formatado|String|
   |phoneNumbers[type eq "mobile"].value|String|
   |phoneNumbers[type eq "work"].value|String|
   |região|String|

10. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Groups to Envoy**.

11. Reveja os atributos do grupo que são sincronizados de Azure AD a Enviado na secção **De Atribuição-Mapeamento.** Os atributos selecionados como propriedades **de correspondência** são usados para combinar com os grupos em Envoy para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

      |Atributo|Tipo|
      |---|---|
      |displayName|String|
      |externalId|String|
      |membros|Referência|

12. Para configurar filtros de âmbito, veja as instruções seguintes disponibilizadas no [Tutorial de filtro de âmbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para ativar o serviço de prestação de Ad Azure para enviado, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

    ![Estado do Aprovisionamento Ativado](common/provisioning-toggle-on.png)

14. Defina os utilizadores e/ou grupos que deseja prestar ao Enviado, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

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