---
title: 'Tutorial: Configurar a Formação de Sensibilização para a Segurança da Webroot para o fornecimento automático de utilizadores com o Azure Ative Directory | Microsoft Docs'
description: Saiba como providenciar e desincêrenciar automaticamente contas de utilizadores do Azure AD à Webroot Security Awareness Training.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 455f4396-930e-4db5-a167-d3ea6a860a17
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/06/2020
ms.author: Zhchia
ms.openlocfilehash: f015718c59178b8094f535280edadd641d22a806
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98741932"
---
# <a name="tutorial-configure-webroot-security-awareness-training-for-automatic-user-provisioning"></a>Tutorial: Configurar a Formação de Sensibilização para a Segurança da Webroot para o fornecimento automático de utilizadores

Este tutorial descreve os passos que você precisa realizar tanto no Webroot Security Awareness Training como no Azure Ative Directory (Azure AD) para configurar o fornecimento automático do utilizador. Quando configurado, a Azure AD fornece automaticamente e desescvisões utilizadores e grupos para [a Webroot Security Awareness Training](https://www.webroot.com/) utilizando o serviço de provisionamento Azure AD. Para obter detalhes importantes sobre o que este serviço faz, como funciona e perguntas frequentes, veja [Automatizar o aprovisionamento e desaprovisionamento de utilizadores em aplicações SaaS no Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Capacidades suportadas
> [!div class="checklist"]
> * Criar utilizadores em Formação de Sensibilização para a Segurança da Webroot
> * Remova os utilizadores no Treino de Sensibilização para a Segurança da Webroot quando já não necessitam de acesso
> * Mantenha os atributos do utilizador sincronizados entre Azure AD e Webroot Security Awareness Training
> * Grupos de fornecimento e membros do grupo na Formação de Sensibilização para a Segurança da Webroot

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* [Um inquilino da AD AZure](../develop/quickstart-create-new-tenant.md) 
* Uma conta de utilizador em Azure AD com [permissão](../roles/permissions-reference.md) para configurar o provisionamento (por exemplo, Administrador de Aplicação, Administrador de Aplicação cloud, Proprietário de Aplicações ou Administrador Global).
* Uma consola de fornecedor de serviços gerido com treino de sensibilização para a segurança da Webroot habilitado para pelo menos um dos seus sites.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passo 1. Planear a sua implementação de aprovisionamento
1. Saiba [como funciona o serviço de aprovisionamento](../app-provisioning/user-provisioning.md).
2. Determine quem vai estar no [âmbito do aprovisionamento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determine quais os dados a [mapear entre Azure AD e Webroot Security Awareness Training](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-webroot-security-awareness-training-to-support-provisioning-with-azure-ad"></a>Passo 2. Configure a Formação de Sensibilização para a Segurança da Webroot para apoiar o provisionamento com a Azure AD

### <a name="obtain-a-secret-token"></a>Obtenha um símbolo secreto

Para ligar o seu site ao Azure AD, terá de obter um **Token Secreto** para esse site na consola de gestão Webroot.

1. Inscreva-se na sua [consola de gestão Webroot](https://identity.webrootanywhere.com/v1/Account/login#tab_customers)

2. A partir do separador **Sites,** clique no ícone de engrenagem na coluna Desconseção de Segurança para o site que deseja ligar com Azure AD.

    ![Ícone de engrenagem](./media/webroot-security-awareness-training-provisioning-tutorial/gear-icon.png)

3. Clique no botão para **configurar a integração ad Ad Azure**.

    ![Configurar integração ad azure](./media/webroot-security-awareness-training-provisioning-tutorial/configure-azure-ad-integration.png)

4. Copie e guarde o **Token Secreto.** Este valor será introduzido no campo Secret Token no separador Provisioning da sua aplicação webroot Security Awareness Training no portal Azure.

5. Clique em **Concluído**.

    ![Cópia Segredo Token](./media/webroot-security-awareness-training-provisioning-tutorial/copy-secret-token.png)

## <a name="step-3-add-webroot-security-awareness-training-from-the-azure-ad-application-gallery"></a>Passo 3. Adicione a Webroot Security Awareness Training da galeria de aplicações Azure AD

Adicione a Webroot Security Awareness Training a partir da galeria de aplicações Azure AD para começar a gerir o fornecimento à Formação de Sensibilização para a Segurança da Webroot. Saiba mais sobre como adicionar uma aplicação a partir da galeria [aqui](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passo 4: Determinar quem vai estar no âmbito do aprovisionamento 

O serviço de aprovisionamento do Azure AD permite-lhe determinar quem vai ser aprovisionado com base na atribuição à aplicação e/ou com base em atributos do utilizador/grupo. Se optar por determinar quem vai ser aprovisionado na sua aplicação com base na atribuição, pode utilizar os seguintes [passos](../manage-apps/assign-user-or-group-access-portal.md) para atribuir utilizadores e grupos à aplicação. Se escolher determinar quem vai ser aprovisionado com base apenas em atributos do utilizador ou grupo, pode utilizar um filtro de âmbito conforme descrito [aqui](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Ao atribuir utilizadores e grupos à Formação de Sensibilização para a Segurança da Webroot, tem de selecionar outra função que não o **Acesso Padrão**. Os utilizadores com a função Acesso Predefinido são excluídos do aprovisionamento e marcados como não autorizados de forma efetiva nos registos de aprovisionamento. Se a única função disponível na aplicação for a função de acesso predefinido, pode [atualizar o manifesto de aplicação](../develop/howto-add-app-roles-in-azure-ad-apps.md) para adicionar funções adicionais. 

* Comece pequeno. Teste com um pequeno conjunto de utilizadores e grupos antes de implementar para todos. Quando o âmbito do aprovisionamento está definido para os utilizadores e os grupos atribuídos, pode controlar isto ao atribuir um ou dois utilizadores ou grupos à aplicação. Quando o âmbito está definido para todos os utilizadores e grupos, pode especificar um [filtro de âmbito baseado em atributos](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-webroot-security-awareness-training"></a>Passo 5. Configure o fornecimento automático de utilizadores à Formação de Sensibilização para a Segurança da Webroot 

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos no TestApp com base em atribuições de utilizador e/ou grupo em Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-webroot-security-awareness-training-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para a Formação de Sensibilização para a Segurança da Webroot em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais** e, em seguida, **Todas as aplicações**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Webroot Security Awareness Training**.

    ![O link webroot Security Awareness Training na lista de aplicações](common/all-applications.png)

3. Selecione o separador **Aprovisionamento**.

    ![Screenshot das opções De gestão com a opção Provisioning chamada.](common/provisioning.png)

4. Defina o **Modo de Aprovisionamento** como **Automático**.

    ![Screenshot da lista de retirada do modo de provisionamento com a opção Automática chamada.](common/provisioning-automatic.png)

5. Sob a secção **de Credenciais de Administração,** entrada `https://awarenessapi.webrootanywhere.com/api/v2/scim` na URL do **inquilino.** Insira o valor simbólico secreto recuperado anteriormente em **Secret Token**. Clique em **Test Connection** para garantir que a Azure AD pode ligar-se ao Treino de Sensibilização para a Segurança webroot. Se a ligação falhar, certifique-se de que a sua conta de Formação de Sensibilização para a Segurança da Webroot tem permissões de administração e tente novamente.

    ![A screenshot mostra a caixa de diálogo de Admin Credentials, onde pode inserir o seu Inquilino U R L e Secret Token.](./media/webroot-security-awareness-training-provisioning-tutorial/provisioning.png)

6. No campo **E-mail de Notificação**, introduza o endereço de e-mail de uma pessoa ou um grupo que deve receber as notificações de erro de aprovisionamento e marque a caixa de verificação **Enviar uma notificação de e-mail quando ocorre uma falha**.

    ![E-mail de Notificação](common/provisioning-notification-email.png)

7. Selecione **Guardar**.

8. Na secção **Mappings,** selecione **Provision Azure Ative Directory Users**.

9. Reveja os atributos do utilizador que são sincronizados de Azure AD a Webroot Security Awareness Training na secção **De mapeamento de atributos.** Os atributos selecionados como propriedades **de correspondência** são usados para combinar as contas de utilizador no Webroot Security Awareness Training para operações de atualização. Se optar por alterar o [atributo de alvo correspondente,](../app-provisioning/customize-application-attributes.md)terá de garantir que a API de Formação de Sensibilização para a Segurança da Webroot suporta utilizadores filtrantes com base nesse atributo. Selecione o botão **Guardar** para escoar quaisquer alterações.

   |Atributo|Tipo|Suportado para filtragem|
   |---|---|---|
   |userName|String|&check;|
   |externalId|String|
   |name.givenName|String|
   |name.familyName|String|
   |emails[type eq "work"].value|String|

10. Na secção **Mappings,** selecione **Provision Azure Ative Directory Groups**.

11. Reveja os atributos do grupo que são sincronizados de Azure AD a Webroot Security Awareness Training na secção **De mapeamento de atributos.** Os atributos selecionados como propriedades **correspondentes** são usados para combinar com os grupos no Webroot Security Awareness Training para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

      |Atributo|Tipo|Suportado para filtragem|
      |---|---|---|
      |displayName|String|&check;|
      |membros|Referência|
      |externalId|String|

12. Para configurar filtros de âmbito, veja as instruções seguintes disponibilizadas no [Tutorial de filtro de âmbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para permitir o serviço de prestação de Ad Azure para a Formação de Sensibilização para a Segurança da Webroot, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

    ![Estado do Aprovisionamento Ativado](common/provisioning-toggle-on.png)

14. Defina os utilizadores e/ou grupos que deseja prestar à Webroot Security Awareness Training, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de Aprovisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para aprovisionar, clique em **Guardar**.

    ![Guardar Configuração de Aprovisionamento](common/provisioning-configuration-save.png)

Esta operação inicia o ciclo de sincronização inicial de todos os utilizadores e grupos definidos no **Âmbito** na secção **Definições**. O ciclo inicial leva mais tempo a ser executado do que os ciclos subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de aprovisionamento do Azure AD esteja em execução. 

## <a name="step-6-monitor-your-deployment"></a>Passo 6. Monitorizar a implementação
Depois de configurar o aprovisionamento, utilize os seguintes recursos para monitorizar a sua implementação:

1. Utilize os [registos de aprovisionamento](../reports-monitoring/concept-provisioning-logs.md) para determinar quais os utilizadores que foram aprovisionados com ou sem êxito
2. Verifique a [barra de progresso](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) para ver o estado do ciclo de aprovisionamento e quão próximo está da conclusão
3. Se a configuração de aprovisionamento parecer estar num mau estado de funcionamento, a aplicação vai entrar em quarentena. Saiba mais sobre os estados de quarentena [aqui](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="change-log"></a>Change log
* 1/21/2021 - Suporte adicional para atributo principal "userName" para os utilizadores.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento de contas de utilizador para Aplicações Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como analisar os registos e obter relatórios sobre a atividade de aprovisionamento](../app-provisioning/check-status-user-account-provisioning.md)