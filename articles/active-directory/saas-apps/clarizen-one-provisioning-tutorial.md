---
title: 'Tutorial: Configure Clarizen One para fornecimento automático de utilizadores com Diretório Ativo Azure / Microsoft Docs'
description: Saiba como providenciar e desa provisionar automaticamente as contas de utilizadores do Azure AD para Clarizen One.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: d8021105-eb5b-4a20-8739-f02e0e22c147
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2020
ms.author: Zhchia
ms.openlocfilehash: 133e12450f01e14f2204810f0d24cd28b5f948f8
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92795375"
---
# <a name="tutorial-configure-clarizen-one-for-automatic-user-provisioning"></a>Tutorial: Configure Clarizen One para fornecimento automático de utilizadores

Este tutorial descreve os passos necessários para realizar tanto no Clarizen One como no Azure Ative Directory (Azure AD) para configurar o fornecimento automático do utilizador. Quando configurado, a Azure AD fornece automaticamente e desescvisões utilizadores e grupos para [Clarizen One](https://www.clarizen.com/) usando o serviço de provisionamento Azure AD. Para obter detalhes importantes sobre o que este serviço faz, como funciona e perguntas frequentes, veja [Automatizar o aprovisionamento e desaprovisionamento de utilizadores em aplicações SaaS no Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Capacidades Suportadas
> [!div class="checklist"]
> * Criar utilizadores em Clarizen One
> * Remova os utilizadores em Clarizen One quando já não necessitam de acesso
> * Mantenha os atributos do utilizador sincronizados entre Azure AD e Clarizen One
> * Grupos de provisão e membros do grupo na Clarizen One
> * [Único sinal de](https://docs.microsoft.com/azure/active-directory/saas-apps/clarizen-tutorial) Clarizen One (recomendado)

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* [Um inquilino da AD AZure](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Uma conta de utilizador em Azure AD com [permissão](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) para configurar o provisionamento (por exemplo, Administrador de Aplicação, Administrador de Aplicação cloud, Proprietário de Aplicações ou Administrador Global). 
* Uma conta de utilizador em Clarizen One com [permissões](https://success.clarizen.com/hc/articles/360011833079-API-Keys-Support) **de Integração user** e **Lite Admin** .

## <a name="step-1-plan-your-provisioning-deployment"></a>Passo 1. Planear a sua implementação de aprovisionamento
1. Saiba [como funciona o serviço de aprovisionamento](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Determine quem vai estar no [âmbito do aprovisionamento](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Determine quais os dados a [mapear entre Azure AD e Clarizen One](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-clarizen-one-to-support-provisioning-with-azure-ad"></a>Passo 2. Configure Clarizen One para apoiar o provisionamento com Azure AD
1. Selecione um dos quatro URLs de inquilinos seguintes de acordo com o seu ambiente Clarizen One e centro de dados:
      * Centro de dados de produção dos EUA: https://servicesapp2.clarizen.com/scim/v2
      * Centro de dados de produção da UE: https://serviceseu1.clarizen.com/scim/v2
      * Centro de dados da Caixa de Areia dos EUA: https://servicesapp.clarizentb.com/scim/v2
      * Centro de dados da CAIXA de areia da UE: https://serviceseu.clarizentb.com/scim/v2

2. Gerar uma [chave API](https://success.clarizen.com/hc/articles/360011833079-API-Keys-Support). Este valor será introduzido no campo **Secret Token** no separador Provisioning da sua aplicação Clarizen One no portal Azure.

## <a name="step-3-add-clarizen-one-from-the-azure-ad-application-gallery"></a>Passo 3. Adicione Clarizen One da galeria de aplicações AD Azure

Adicione Clarizen One da galeria de aplicações AZure AD para começar a gerir o fornecimento ao Clarizen One. Se já configurar clarizen One para SSO, pode utilizar a mesma aplicação. No entanto, é recomendável criar uma aplicação separada ao testar a integração inicialmente. Saiba mais sobre como adicionar uma aplicação a partir da galeria [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passo 4: Determinar quem vai estar no âmbito do aprovisionamento 

O serviço de aprovisionamento do Azure AD permite-lhe determinar quem vai ser aprovisionado com base na atribuição à aplicação e/ou com base em atributos do utilizador/grupo. Se optar por determinar quem vai ser aprovisionado na sua aplicação com base na atribuição, pode utilizar os seguintes [passos](../manage-apps/assign-user-or-group-access-portal.md) para atribuir utilizadores e grupos à aplicação. Se escolher determinar quem vai ser aprovisionado com base apenas em atributos do utilizador ou grupo, pode utilizar um filtro de âmbito conforme descrito [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Ao atribuir utilizadores e grupos ao Clarizen One, tem de selecionar outra função que não o **Acesso Predefinido** . Os utilizadores com a função Acesso Predefinido são excluídos do aprovisionamento e marcados como não autorizados de forma efetiva nos registos de aprovisionamento. Se a única função disponível na aplicação for a função de acesso predefinido, pode [atualizar o manifesto de aplicação](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) para adicionar funções adicionais. 

* Comece pequeno. Teste com um pequeno conjunto de utilizadores e grupos antes de implementar para todos. Quando o âmbito do aprovisionamento está definido para os utilizadores e os grupos atribuídos, pode controlar isto ao atribuir um ou dois utilizadores ou grupos à aplicação. Quando o âmbito está definido para todos os utilizadores e grupos, pode especificar um [filtro de âmbito baseado em atributos](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-clarizen-one"></a>Passo 5. Configure o fornecimento automático de utilizadores ao Clarizen One 

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos no TestApp com base em atribuições de utilizador e/ou grupo em Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-clarizen-one-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para clarizen One em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais** e, em seguida, **Todas as aplicações** .

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Clarizen One** .

    ![O link Clarizen One na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Aprovisionamento** .

    ![Separador Aprovisionamento](common/provisioning.png)

4. Defina o **Modo de Aprovisionamento** como **Automático** .

    ![Separador de provisionamento automático](common/provisioning-automatic.png)

5. Sob a secção **de Credenciais de Administração,** insira o seu URL de inquilino Clarizen One e o Token Secreto. Clique em **Testar A Ligação** para garantir que o Azure AD pode ligar-se ao Clarizen One. Se a ligação falhar, certifique-se de que a sua conta Clarizen One tem permissões de Administração e tente novamente.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **E-mail de Notificação** , introduza o endereço de e-mail de uma pessoa ou um grupo que deve receber as notificações de erro de aprovisionamento e marque a caixa de verificação **Enviar uma notificação de e-mail quando ocorre uma falha** .

    ![E-mail de Notificação](common/provisioning-notification-email.png)

7. Selecione **Guardar** .

8. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users to Clarizen One** .

9. Reveja os atributos do utilizador que são sincronizados de Azure AD a Clarizen One na secção **De mapeamento de Atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas de utilizador em Clarizen One para operações de atualização. Se optar por alterar o [atributo de alvo correspondente,](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)terá de garantir que a API Clarizen One suporta utilizadores filtrantes com base nesse atributo. Selecione o botão **Guardar** para escoar quaisquer alterações.

   |Atributo|Tipo|
   |---|---|
   |userName|String|
   |displayName|String|
   |active|Booleano|
   |título|String|
   |emails[type eq "work"].value|String|
   |e-mails[tipo eq "casa"].valor|String|
   |e-mails[tipo eq "outros"].valor|String|
   |preferiuLanguage|String|
   |name.givenName|String|
   |name.familyName|String|
   |nome.formatado|String|
   |name.honorificPrefix|String|
   |nome.honorificS suléix|String|
   |endereços[tipo eq "outros"].formatados|String|
   |endereços[tipo eq "work"].formatado|String|
   |endereços[tipo eq "work"].país|String|
   |endereços[tipo eq "work"].região|String|
   |endereços[tipo eq "work"].localidade|String|
   |endereços[tipo eq "work"].postalCode|String|
   |endereços[tipo eq "work"].streetAddress|String|
   |phoneNumbers[type eq "work"].value|String|
   |phoneNumbers[type eq "mobile"].value|String|
   |números de telefone[tipo eq "fax"].valor|String|
   |números de telefone[tipo eq "home"].value|String|
   |phoneNumbers[tipo eq "outro"].value|String|
   |números de telefone[tipo eq "pager"].valor|String|
   |externalId|String|
   |nickName|String|
   |região|String|
   |papéis[eq primário "True".type]|String|
   |papéis[eq primário "True".value]|String|
   |timezone|String|
   |userType|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|Referência|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|String|

10. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Groups to Clarizen One** .

11. Reveja os atributos do grupo que são sincronizados de Azure AD a Clarizen One na secção **De mapeamento de Atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar com os grupos em Clarizen One para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

      |Atributo|Tipo|
      |---|---|
      |displayName|String|
      |externalId|String|
      |membros|Referência|

12. Para configurar filtros de âmbito, veja as instruções seguintes disponibilizadas no [Tutorial de filtro de âmbito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para ativar o serviço de prestação de Ad Azure para Clarizen One, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

    ![Estado do Aprovisionamento Ativado](common/provisioning-toggle-on.png)

14. Defina os utilizadores e/ou grupos que deseja prestar à Clarizen One, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de Aprovisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para aprovisionar, clique em **Guardar** .

    ![Guardar Configuração de Aprovisionamento](common/provisioning-configuration-save.png)

Esta operação inicia o ciclo de sincronização inicial de todos os utilizadores e grupos definidos no **Âmbito** na secção **Definições** . O ciclo inicial leva mais tempo a ser executado do que os ciclos subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de aprovisionamento do Azure AD esteja em execução. 

## <a name="step-6-monitor-your-deployment"></a>Passo 6. Monitorizar a implementação
Depois de configurar o aprovisionamento, utilize os seguintes recursos para monitorizar a sua implementação:

1. Utilize os [registos de aprovisionamento](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) para determinar quais os utilizadores que foram aprovisionados com ou sem êxito
2. Verifique a [barra de progresso](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) para ver o estado do ciclo de aprovisionamento e quão próximo está da conclusão
3. Se a configuração de aprovisionamento parecer estar num mau estado de funcionamento, a aplicação vai entrar em quarentena. Saiba mais sobre os estados de quarentena [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## <a name="troubleshooting-tips"></a>Sugestões de Resolução de Problemas
Ao atribuir um Utilizador à aplicação clarizen One, escolha apenas a função **de Utilizador.** As funções abaixo mostradas são inválidas.

* Administrador (Administrador)
* Utilizador de relatórios de e-mail
* Utilizador externo
* Utilizador Financeiro
* Utilizador Social
* Super Utilizador
* Utilizador de despesas & tempo



## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento de contas de utilizador para Aplicações Empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como analisar os registos e obter relatórios sobre a atividade de aprovisionamento](../manage-apps/check-status-user-account-provisioning.md)
