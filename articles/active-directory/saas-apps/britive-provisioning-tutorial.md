---
title: 'Tutorial: Configurar britive para fornecimento automático de utilizadores com Azure Ative Directory | Microsoft Docs'
description: Saiba como provisão e desavisagem automática de contas de utilizadores de Azure AD a Britive.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 622688b3-9d20-482e-aab9-ce2a1f01e747
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/05/2021
ms.author: Zhchia
ms.openlocfilehash: 8bebcb49bc7bf31614a161c08d33d5910679b614
ms.sourcegitcommit: 94c3c1be6bc17403adbb2bab6bbaf4a717a66009
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2021
ms.locfileid: "103225768"
---
# <a name="tutorial-configure-britive-for-automatic-user-provisioning"></a>Tutorial: Configurar britive para fornecimento automático de utilizadores

Este tutorial descreve os passos necessários para realizar tanto no Diretório Britive como no Azure Ative (Azure AD) para configurar o fornecimento automático do utilizador. Quando configurado, a Azure AD fornece automaticamente e desescvisões utilizadores e grupos para [Britive](https://www.britive.com/) usando o serviço de provisionamento Azure AD. Para obter detalhes importantes sobre o que este serviço faz, como funciona e perguntas frequentes, veja [Automatizar o aprovisionamento e desaprovisionamento de utilizadores em aplicações SaaS no Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Capacidades Suportadas
> [!div class="checklist"]
> * Criar utilizadores em Britive
> * Remova os utilizadores em Britive quando já não necessitam de acesso
> * Mantenha os atributos do utilizador sincronizados entre Azure AD e Britive
> * Grupos de provisão e membros do grupo em Britive
> * [Único sinal de](britive-tutorial.md) Britive (recomendado)

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* [Um inquilino da AD AZure](../develop/quickstart-create-new-tenant.md) 
* Uma conta de utilizador em Azure AD com [permissão](../roles/permissions-reference.md) para configurar o provisionamento (por exemplo, Administrador de Aplicação, Administrador de Aplicação cloud, Proprietário de Aplicações ou Administrador Global). 
* Um inquilino [britânico.](https://www.britive.com/)
* Uma conta de utilizador em Britive com permissões de Administração.


## <a name="step-1-plan-your-provisioning-deployment"></a>Passo 1. Planear a sua implementação de aprovisionamento
1. Saiba [como funciona o serviço de aprovisionamento](../app-provisioning/user-provisioning.md).
1. Determine quem vai estar no [âmbito do aprovisionamento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
1. Determine quais os dados a [mapear entre Azure AD e Britive](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-britive-to-support-provisioning-with-azure-ad"></a>Passo 2. Configure britive para apoiar o provisionamento com Azure AD

A aplicação terá de ser configurada manualmente utilizando os passos abaixo fornecidos:
1. Iniciar sessão para aplicação Britive com privilégios de administrador
1. Clique em **Fornecedores de Identidade de administração de >de utilizadores >administrador**
1. Clique no **Add Identity Provider**. Insira o nome e descrição. Clique no botão Adicionar Fornecedor de Identidade.

    ![Fornecedor de Identidade](media/britive-provisioning-tutorial/identity.png)

1. Será mostrada uma página de configuração semelhante a uma apresentada abaixo.

    ![Página de configuração](media/britive-provisioning-tutorial/configuration.png)

1. Clique no **separador SCIM.** Mude o fornecedor SCIM de Genérico para Azure e guarde as alterações. Copie o URL SCIM e anote-o. Estes valores serão introduzidos nas caixas URL do **Arrendatário** no separador Provisioning da sua aplicação Britive no portal Azure.

    ![Página SCIM](media/britive-provisioning-tutorial/scim.png)

1. Clique em **Criar Token**. Selecione a validade do token conforme necessário e clique no botão Criar Token.

    ![Criar Ficha](media/britive-provisioning-tutorial/create-token.png)

1. Copie o símbolo gerado e anote-o. Clique em OK. Por favor, note que o utilizador não poderá voltar a ver o token. Clique no botão Re-Create para gerar um novo token, se necessário. Estes valores serão introduzidos nas caixas URL **secret Token** e Tenant no separador Provisioning da sua aplicação getAbstract no portal Azure.

    ![Ficha de cópia](media/britive-provisioning-tutorial/copy-token.png) 


## <a name="step-3-add-britive-from-the-azure-ad-application-gallery"></a>Passo 3. Adicione Britive da galeria de aplicações AZure AD

Adicione Britive da galeria de aplicações AZure AD para começar a gerir o fornecimento à Britive. Se já configurar anteriormente Britive para SSO, pode utilizar a mesma aplicação. No entanto, é recomendável criar uma aplicação separada ao testar a integração inicialmente. Saiba mais sobre como adicionar uma aplicação a partir da galeria [aqui](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passo 4: Determinar quem vai estar no âmbito do aprovisionamento 

O serviço de aprovisionamento do Azure AD permite-lhe determinar quem vai ser aprovisionado com base na atribuição à aplicação e/ou com base em atributos do utilizador/grupo. Se optar por determinar quem vai ser aprovisionado na sua aplicação com base na atribuição, pode utilizar os seguintes [passos](../manage-apps/assign-user-or-group-access-portal.md) para atribuir utilizadores e grupos à aplicação. Se escolher determinar quem vai ser aprovisionado com base apenas em atributos do utilizador ou grupo, pode utilizar um filtro de âmbito conforme descrito [aqui](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Ao atribuir utilizadores e grupos a Britive, deve selecionar uma outra função que não **o Acesso Predefinido**. Os utilizadores com a função Acesso Predefinido são excluídos do aprovisionamento e marcados como não autorizados de forma efetiva nos registos de aprovisionamento. Se a única função disponível na aplicação for a função de acesso predefinido, pode [atualizar o manifesto de aplicação](../develop/howto-add-app-roles-in-azure-ad-apps.md) para adicionar funções adicionais. 

* Comece pequeno. Teste com um pequeno conjunto de utilizadores e grupos antes de implementar para todos. Quando o âmbito do aprovisionamento está definido para os utilizadores e os grupos atribuídos, pode controlar isto ao atribuir um ou dois utilizadores ou grupos à aplicação. Quando o âmbito está definido para todos os utilizadores e grupos, pode especificar um [filtro de âmbito baseado em atributos](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-britive"></a>Passo 5. Configure o fornecimento automático de utilizadores a Britive 

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos em Britive com base em atribuições de utilizador e/ou grupo em Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-britive-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para britive em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais** e, em seguida, **Todas as aplicações**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

1. Na lista de candidaturas, selecione **Britive**.

    ![O link britive na lista de Aplicações](common/all-applications.png)

1. Selecione o separador **Aprovisionamento**.

    ![Separador Aprovisionamento](common/provisioning.png)

1. Defina o **Modo de Aprovisionamento** como **Automático**.

    ![Separador de provisionamento automático](common/provisioning-automatic.png)

1. Sob a secção **de Credenciais de Administração,** insira o URL do inquilino britive e o Token Secreto. Clique em **Testar A Ligação** para garantir que o Azure AD pode ligar-se à Britive. Se a ligação falhar, certifique-se de que a sua conta Britive tem permissões de Administração e tente novamente.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

1. No campo **E-mail de Notificação**, introduza o endereço de e-mail de uma pessoa ou um grupo que deve receber as notificações de erro de aprovisionamento e marque a caixa de verificação **Enviar uma notificação de e-mail quando ocorre uma falha**.

    ![E-mail de Notificação](common/provisioning-notification-email.png)

1. Selecione **Guardar**.

1. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users to Britive**.

1. Reveja os atributos do utilizador que são sincronizados de Azure AD a Britive na secção **De mapeamento de Atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas de utilizador em Britive para operações de atualização. Se optar por alterar o [atributo de alvo correspondente,](../app-provisioning/customize-application-attributes.md)terá de garantir que a API Britive suporta utilizadores filtrantes com base nesse atributo. Selecione o botão **Guardar** para escoar quaisquer alterações.

   |Atributo|Tipo|Suportado para filtragem|
   |---|---|---|
   |userName|String|&check;
   |active|Booleano|
   |displayName|String|
   |título|String|
   |externalId|String|
   |preferiuLanguage|String|
   |name.givenName|String|
   |name.familyName|String|
   |nickName|String|
   |userType|String|
   |região|String|
   |timezone|String|
   |e-mails[tipo eq "casa"].valor|String|
   |e-mails[tipo eq "outros"].valor|String|
   |emails[type eq "work"].value|String|
   |números de telefone[tipo eq "home"].value|String|
   |phoneNumbers[tipo eq "outro"].value|String|
   |números de telefone[tipo eq "pager"].valor|String|
   |phoneNumbers[type eq "work"].value|String|
   |phoneNumbers[type eq "mobile"].value|String|
   |números de telefone[tipo eq "fax"].valor|String|
   |endereços[tipo eq "work"].formatado|String|
   |endereços[tipo eq "work"].streetAddress|String|
   |endereços[tipo eq "work"].localidade|String|
   |endereços[tipo eq "work"].região|String|
   |endereços[tipo eq "work"].postalCode|String|
   |endereços[tipo eq "work"].país|String|
   |endereços[tipo eq "home"].formatado|String|
   |endereços[tipo eq "home"].streetAddress|String|
   |endereços[tipo eq "home"].localidade|String|
   |endereços[tipo eq "home"].região|String|
   |endereços[tipo eq "home"].postalCode|String|
   |endereços[tipo eq "home"].país|String|
   |endereços[tipo eq "outros"].formatados|String|
   |endereços[tipo eq "outros"].streetAddress|String|
   |endereços[tipo eq "outros"].localidade|String|
   |endereços[tipo eq "outros"].região|String|
   |endereços[tipo eq "outros"].postalCode|String|
   |endereços[tipo eq "outros"].país|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:organization|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|Referência|


1. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Groups to Britive**.

1. Reveja os atributos do grupo que são sincronizados de Azure AD a Britive na secção **De mapeamento de Atributos.** Os atributos selecionados como propriedades **de correspondência** são usados para combinar os grupos em Britive para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

      |Atributo|Tipo|Suportado para filtragem|
      |---|---|---|
      |displayName|String|&check;
      |externalId|String|
      |membros|Referência|

1. Para configurar filtros de âmbito, veja as instruções seguintes disponibilizadas no [Tutorial de filtro de âmbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Para ativar o serviço de prestação de Ad Azure para britive, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

    ![Estado do Aprovisionamento Ativado](common/provisioning-toggle-on.png)

1. Defina os utilizadores e/ou grupos que deseja prestar à Britive, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de Aprovisionamento](common/provisioning-scope.png)

1. Quando estiver pronto para aprovisionar, clique em **Guardar**.

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