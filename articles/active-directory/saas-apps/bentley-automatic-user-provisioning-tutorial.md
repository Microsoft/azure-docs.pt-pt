---
title: 'Tutorial: Configure Bentley - Fornecimento automático de utilizadores para fornecimento automático de utilizadores com Azure Ative Directory | Microsoft Docs'
description: Saiba como provisão e desa provisionamento automático de contas de utilizador de Azure AD para Bentley - Provisioning automático do utilizador.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 08778fff-f252-45c2-95d4-cc640c288af3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/13/2021
ms.author: Zhchia
ms.openlocfilehash: 0d5aaee56606abcc886310049ad5a087f2aedb5e
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/17/2021
ms.locfileid: "107590323"
---
# <a name="tutorial-configure-bentley---automatic-user-provisioning-for-automatic-user-provisioning"></a>Tutorial: Configure Bentley - Fornecimento automático de utilizadores para fornecimento automático de utilizadores

Este tutorial descreve os passos necessários para realizar tanto no Bentley - Fornecimento Automático de Utilizador como no Diretório Ativo Azure (Azure AD) para configurar o fornecimento automático do utilizador. Quando configurado, a Azure AD fornece automaticamente e desescvisões utilizadores e grupos para [Bentley - Fornecimento automático de utilizadores](https://www.bentley.com) utilizando o serviço de provisionamento Azure AD. Para obter detalhes importantes sobre o que este serviço faz, como funciona e perguntas frequentes, veja [Automatizar o aprovisionamento e desaprovisionamento de utilizadores em aplicações SaaS no Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Capacidades suportadas
> [!div class="checklist"]
> * Criar utilizadores em Bentley - Provisionamento Automático do Utilizador
> * Remover os utilizadores no Bentley - Fornecimento automático de utilizadores quando já não necessitam de acesso
> * Mantenha os atributos do utilizador sincronizados entre Azure AD e Bentley - Fornecimento automático de utilizadores
> * Grupos de provisão e membros do grupo na Bentley - Provisioning automático do utilizador

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* [Um inquilino da AD AZure](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Uma conta de utilizador em Azure AD com [permissão](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) para configurar o provisionamento (por exemplo, Administrador de Aplicação, Administrador de Aplicação cloud, Proprietário de Aplicações ou Administrador Global). 
* Uma conta federada com bentley IMS.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passo 1. Planear a sua implementação de aprovisionamento
1. Saiba [como funciona o serviço de aprovisionamento](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Determine quem vai estar no [âmbito do aprovisionamento](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Determine quais os dados a [mapear entre Azure AD e Bentley - Fornecimento Automático de Utilizadores](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-bentley---automatic-user-provisioning-to-support-provisioning-with-azure-ad"></a>Passo 2. Configure Bentley - Fornecimento automático de utilizadores para apoiar o provisionamento com Azure AD

Contacte a equipa de [apoio](https://communities.bentley.com/communities/other_communities/licensing_cloud_and_web_services/w/wiki/52836/microsoft-azure-ad-automatic-user-provisioning-configuration) ao fornecimento de utilizadores da Bentley para o INQUILINO URL e Secret Token. Estes valores serão introduzidos no separador Provisioning da aplicação Bentley no portal Azure.

## <a name="step-3-add-bentley---automatic-user-provisioning-from-the-azure-ad-application-gallery"></a>Passo 3. Adicionar Bentley - Provisionamento automático do utilizador a partir da galeria de aplicações Azure AD

Adicione Bentley - Fornecimento automático de utilizadores da galeria de aplicações Azure AD para começar a gerir o fornecimento à Bentley - Provisioning automático do utilizador. Saiba mais sobre como adicionar uma aplicação a partir da galeria [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passo 4: Determinar quem vai estar no âmbito do aprovisionamento 

O serviço de aprovisionamento do Azure AD permite-lhe determinar quem vai ser aprovisionado com base na atribuição à aplicação e/ou com base em atributos do utilizador/grupo. Se optar por determinar quem vai ser aprovisionado na sua aplicação com base na atribuição, pode utilizar os seguintes [passos](../manage-apps/assign-user-or-group-access-portal.md) para atribuir utilizadores e grupos à aplicação. Se escolher determinar quem vai ser aprovisionado com base apenas em atributos do utilizador ou grupo, pode utilizar um filtro de âmbito conforme descrito [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Ao atribuir utilizadores e grupos à Bentley - Provisioning automática do utilizador, tem de selecionar outra função que não o **Acesso Predefinido**. Os utilizadores com a função Acesso Predefinido são excluídos do aprovisionamento e marcados como não autorizados de forma efetiva nos registos de aprovisionamento. Se a única função disponível na aplicação for a função de acesso predefinido, pode [atualizar o manifesto de aplicação](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) para adicionar funções adicionais. 

* Comece pequeno. Teste com um pequeno conjunto de utilizadores e grupos antes de implementar para todos. Quando o âmbito do aprovisionamento está definido para os utilizadores e os grupos atribuídos, pode controlar isto ao atribuir um ou dois utilizadores ou grupos à aplicação. Quando o âmbito está definido para todos os utilizadores e grupos, pode especificar um [filtro de âmbito baseado em atributos](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-bentley---automatic-user-provisioning"></a>Passo 5. Configure o fornecimento automático do utilizador à Bentley - Provisioning automática do utilizador 

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos no TestApp com base em atribuições de utilizador e/ou grupo em Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-bentley---automatic-user-provisioning-in-azure-ad"></a>Para configurar o provisionamento automático do utilizador para a Bentley - Provisioning automática do utilizador em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais** e, em seguida, **Todas as aplicações**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Bentley - Fornecimento automático do utilizador**.

    ![O Link Bentley - Fornecimento automático de utilizadores na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Aprovisionamento**.

    ![Separador Aprovisionamento](common/provisioning.png)

4. Defina o **Modo de Aprovisionamento** como **Automático**.

    ![Separador de provisionamento automático](common/provisioning-automatic.png)

5. Na secção **Credenciais de Administração,** insira o seu Bentley - URL de Inquilino De Provisionamento automático do Utilizador e Token Secreto. Clique em **'Testar' Ligação** para garantir que o Azure AD pode ligar-se ao Bentley - Provisioning Automático do Utilizador. Se a ligação falhar, certifique-se de que a sua conta Bentley - Automatic User Provisioning tem permissões de administração e tente novamente.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **E-mail de Notificação**, introduza o endereço de e-mail de uma pessoa ou um grupo que deve receber as notificações de erro de aprovisionamento e marque a caixa de verificação **Enviar uma notificação de e-mail quando ocorre uma falha**.

    ![E-mail de Notificação](common/provisioning-notification-email.png)

7. Selecione **Guardar**.

8. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users to Bentley - Automatic User Provisioning**.

9. Reveja os atributos do utilizador que são sincronizados de Azure AD para Bentley - Provisionamento automático do utilizador na secção **De mapeamento de atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para corresponder às contas do utilizador no Bentley - Fornecimento automático do utilizador para operações de atualização. Se optar por alterar o [atributo alvo correspondente,](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)terá de garantir que o Bentley - API de Provisionamento Automático do Utilizador suporta utilizadores filtrantes com base nesse atributo. Selecione o botão **Guardar** para escoar quaisquer alterações.

   |Atributo|Tipo|Suportado para filtragem|
   |---|---|---|
   |userName|String|&check;|
   |título|String|
   |emails[type eq "work"].value|String|
   |preferiuLanguage|String|
   |name.givenName|String|
   |name.familyName|String|
   |endereços[tipo eq "work"].streetAddress|String|
   |endereços[tipo eq "work"].localidade|String|
   |endereços[tipo eq "work"].região|String|
   |endereços[tipo eq "work"].postalCode|String|
   |endereços[tipo eq "work"].país|String|
   |phoneNumbers[type eq "work"].value|String|
   |externalId|String|
   |urn:ietf:params:scim:schemas:extension:Bentley:2.0:User:isSoftDeleted|String|

10. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Groups para Bentley - Provisioning Automático do Utilizador**.

11. Reveja os atributos do grupo que são sincronizados de Azure AD para Bentley - Provisioning automático do utilizador na secção **De mapeamento de atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para corresponder aos grupos em Bentley - Fornecimento automático do utilizador para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

      |Atributo|Tipo|Suportado para filtragem|
      |---|---|---|
      |displayName|String|&check;|
      |externalId|String|
      |membros|Referência|
      |urn:ietf:params:scim:schemas:extensão:Bentley:2.0:Grupo:descrição|String|

12. Para configurar filtros de âmbito, veja as instruções seguintes disponibilizadas no [Tutorial de filtro de âmbito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para ativar o serviço de prestação de Ad Azure para a Bentley - Provisioning Automática do Utilizador, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

    ![Estado do Aprovisionamento Ativado](common/provisioning-toggle-on.png)

14. Defina os utilizadores e/ou grupos que deseja prestar à Bentley - Provisioning automática do utilizador, escolhendo os valores pretendidos no **Âmbito** na secção **Definições.**

    ![Âmbito de Aprovisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para aprovisionar, clique em **Guardar**.

    ![Guardar Configuração de Aprovisionamento](common/provisioning-configuration-save.png)

Esta operação inicia o ciclo de sincronização inicial de todos os utilizadores e grupos definidos no **Âmbito** na secção **Definições**. O ciclo inicial leva mais tempo a ser executado do que os ciclos subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de aprovisionamento do Azure AD esteja em execução. 

## <a name="step-6-monitor-your-deployment"></a>Passo 6. Monitorizar a implementação
Depois de configurar o aprovisionamento, utilize os seguintes recursos para monitorizar a sua implementação:

1. Utilize os [registos de aprovisionamento](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) para determinar quais os utilizadores que foram aprovisionados com ou sem êxito
2. Verifique a [barra de progresso](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) para ver o estado do ciclo de aprovisionamento e quão próximo está da conclusão
3. Se a configuração de aprovisionamento parecer estar num mau estado de funcionamento, a aplicação vai entrar em quarentena. Saiba mais sobre os estados de quarentena [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="connector-limitations"></a>Limitações do conector
* O atributo de extensão da empresa "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager" não é suportado e será removido.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento de contas de utilizador para Aplicações Empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como analisar os registos e obter relatórios sobre a atividade de aprovisionamento](../manage-apps/check-status-user-account-provisioning.md)
