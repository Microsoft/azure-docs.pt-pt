---
title: 'Tutorial: Configurar fortes change cloud para fornecimento automático de utilizadores com Azure Ative Directory | Microsoft Docs'
description: Saiba como advisionar e desavisar automaticamente as contas de utilizadores do Azure AD para o Fortes Change Cloud.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: ef9a8f5e-0bf0-46d6-8e17-3bcf1a5b0a6b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2021
ms.author: Zhchia
ms.openlocfilehash: 43b783d9462205b01d3ac4de0c5779fdc9864470
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99550463"
---
# <a name="tutorial-configure-fortes-change-cloud-for-automatic-user-provisioning"></a>Tutorial: Configurar fortes alterar nuvem para fornecimento automático de utilizadores

Este tutorial descreve os passos necessários para realizar tanto na Nuvem de Mudança de Fortes como no Azure Ative Directory (AZure AD) para configurar o fornecimento automático de utilizadores. Quando configurado, o Azure AD fornece automaticamente e desnegri os utilizadores e grupos para [Fortes Change Cloud](https://fortesglobal.com/) utilizando o serviço de provisionamento Azure AD. Para obter detalhes importantes sobre o que este serviço faz, como funciona e perguntas frequentes, veja [Automatizar o aprovisionamento e desaprovisionamento de utilizadores em aplicações SaaS no Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Capacidades Suportadas
> [!div class="checklist"]
> * Criar utilizadores em Fortes Change Cloud
> * Remova os utilizadores na Nuvem de Mudança de Fortes quando já não necessitam de acesso
> * Mantenha os atributos do utilizador sincronizados entre AZure AD e Fortes Change Cloud
> * [Único sinal de "Fortes](fortes-change-cloud-tutorial.md) Change Cloud" (recomendado)

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* [Um inquilino da AD AZure](../develop/quickstart-create-new-tenant.md) 
* Uma conta de utilizador em Azure AD com [permissão](../roles/permissions-reference.md) para configurar o provisionamento (por exemplo, Administrador de Aplicação, Administrador de Aplicação cloud, Proprietário de Aplicações ou Administrador Global). 
* Um inquilino da Nuvem De Fortes Change.
* Uma conta de utilizador em Fortes Change Cloud com permissões de Administração.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passo 1. Planear a sua implementação de aprovisionamento
1. Saiba [como funciona o serviço de aprovisionamento](../app-provisioning/user-provisioning.md).
2. Determine quem vai estar no [âmbito do aprovisionamento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determine quais os dados a [mapear entre Azure AD e Fortes Change Cloud](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-fortes-change-cloud-to-support-provisioning-with-azure-ad"></a>Passo 2. Configure Fortes Change Cloud para apoiar o provisionamento com Azure AD

1. Faça login com a sua conta de administração para Fortes Change Cloud. Clique no **ícone Definições** e, em seguida, navegue para o **Provisionamento do Utilizador (SCIM)**.

    [![O cenário de CLOUD SCIM de alterações ](media/fortes-change-cloud-provisioning-tutorial/scim-settings.png) de fortes](media/fortes-change-cloud-provisioning-tutorial/scim-settings.png#lightbox)

2. Na nova janela, copie e guarde a URL do **Inquilino** e o **Token Primário.** O URL do inquilino será inserido no campo **DE INQUILINO URL** * e o token primário será inserido no campo **Secret** * Token no separador Provisioning da sua aplicação Fortes Change Cloud no portal Azure.
     
      [![O símbolo primário da nuvem de mudança de fortes](media/fortes-change-cloud-provisioning-tutorial/primary-token.png)](media/fortes-change-cloud-provisioning-tutorial/primary-token.png#lightbox)

## <a name="step-3-add-fortes-change-cloud-from-the-azure-ad-application-gallery"></a>Passo 3. Adicione a Nuvem de Mudança de Fortes da galeria de aplicações AZure AD

Adicione o Fortes Change Cloud da galeria de aplicações AZure AD para começar a gerir o fornecimento à Nuvem de Mudança de Fortes. Se já configurar o Fortes Change Cloud para SSO, pode utilizar a mesma aplicação. No entanto, é recomendável criar uma aplicação separada ao testar a integração inicialmente. Saiba mais sobre como adicionar uma aplicação a partir da galeria [aqui](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passo 4: Determinar quem vai estar no âmbito do aprovisionamento 

O serviço de aprovisionamento do Azure AD permite-lhe determinar quem vai ser aprovisionado com base na atribuição à aplicação e/ou com base em atributos do utilizador/grupo. Se optar por determinar quem vai ser aprovisionado na sua aplicação com base na atribuição, pode utilizar os seguintes [passos](../manage-apps/assign-user-or-group-access-portal.md) para atribuir utilizadores e grupos à aplicação. Se escolher determinar quem vai ser aprovisionado com base apenas em atributos do utilizador ou grupo, pode utilizar um filtro de âmbito conforme descrito [aqui](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Ao atribuir utilizadores e grupos a Fortes Change Cloud, tem de selecionar outra função que não o **Acesso Predefinido.** Os utilizadores com a função Acesso Predefinido são excluídos do aprovisionamento e marcados como não autorizados de forma efetiva nos registos de aprovisionamento. Se a única função disponível na aplicação for a função de acesso predefinido, pode [atualizar o manifesto de aplicação](../develop/howto-add-app-roles-in-azure-ad-apps.md) para adicionar funções adicionais. 

* Comece pequeno. Teste com um pequeno conjunto de utilizadores e grupos antes de implementar para todos. Quando o âmbito do aprovisionamento está definido para os utilizadores e os grupos atribuídos, pode controlar isto ao atribuir um ou dois utilizadores ou grupos à aplicação. Quando o âmbito está definido para todos os utilizadores e grupos, pode especificar um [filtro de âmbito baseado em atributos](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-fortes-change-cloud"></a>Passo 5. Configure o fornecimento automático de utilizadores para a Nuvem de Mudança de Fortes 

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos no TestApp com base em atribuições de utilizador e/ou grupo em Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-fortes-change-cloud-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para fortes change cloud em Ad Azure:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais** e, em seguida, **Todas as aplicações**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Fortes Change Cloud**.

    ![O link De Alteração de Fortes na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Aprovisionamento**.

    ![Separador Aprovisionamento](common/provisioning.png)

4. Defina o **Modo de Aprovisionamento** como **Automático**.

    ![Separador de provisionamento automático](common/provisioning-automatic.png)

5. Sob a secção **de Credenciais de Administração,** insira o URL do Inquilino De Nuvem De Alteração de Fortes e o Token Secreto. Clique em **Testar A Ligação** para garantir que o Azure AD pode ligar-se à Nuvem de Alteração de Fortes. Se a ligação falhar, certifique-se de que a sua conta Desinsumin a Cloud tem permissões de Administração e tente novamente.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **E-mail de Notificação**, introduza o endereço de e-mail de uma pessoa ou um grupo que deve receber as notificações de erro de aprovisionamento e marque a caixa de verificação **Enviar uma notificação de e-mail quando ocorre uma falha**.

    ![E-mail de Notificação](common/provisioning-notification-email.png)

7. Selecione **Guardar**.

8. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users to Fortes Change Cloud**.

9. Reveja os atributos do utilizador que são sincronizados de AD AD a Fortes Change Cloud na secção **De mapeamento de Atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas de utilizador em Fortes Change Cloud para operações de atualização. Se optar por alterar o [atributo de alvo correspondente,](../app-provisioning/customize-application-attributes.md)terá de garantir que a API da Cloud Descosusa de Fortes Change suporta utilizadores filtrantes com base nesse atributo. Selecione o botão **Guardar** para escoar quaisquer alterações.

   |Atributo|Tipo|Suportado para filtragem|
   |---|---|---|
   |userName|String|&check;|
   |active|Booleano|
   |emails[type eq "work"].value|String|
   |name.givenName|String|
   |name.familyName|String|
   |nome.formatado|String|
   |externalId|String|
   |urn:ietf:params:scim:schemas:extension:fcc:2.0:User:administrator|Booleano|
   |urn:ietf:params:scim:schemas:extension:fcc:2.0:User:loginDisabled|Booleano|

  

10. Para configurar filtros de âmbito, veja as instruções seguintes disponibilizadas no [Tutorial de filtro de âmbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para ativar o serviço de prestação de Ad Azure para a Nuvem de Alteração de Fortes, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

    ![Estado do Aprovisionamento Ativado](common/provisioning-toggle-on.png)

12. Defina os utilizadores e/ou grupos que deseja prestar à Nuvem de Alteração de Fortes, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de Aprovisionamento](common/provisioning-scope.png)

13. Quando estiver pronto para aprovisionar, clique em **Guardar**.

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