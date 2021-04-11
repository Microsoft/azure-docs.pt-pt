---
title: 'Tutorial: Configure Clarizen One para fornecimento automático de utilizadores com Azure Ative Directory | Microsoft Docs'
description: Saiba como provisão e desprovisionar automaticamente as contas dos utilizadores do Azure AD para Clarizen One.
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
ms.topic: tutorial
ms.date: 10/01/2020
ms.author: Zhchia
ms.openlocfilehash: f3a19d3c3bf3e4340bb36fd683453541fa15eb6c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101650838"
---
# <a name="tutorial-configure-clarizen-one-for-automatic-user-provisioning"></a>Tutorial: Configure Clarizen One para fornecimento automático de utilizadores

Este tutorial descreve os passos necessários para realizar tanto no Clarizen One como no Azure Ative Directory (Azure AD) para configurar o fornecimento automático do utilizador. Quando configurado, o Azure AD fornece automaticamente e desprovisiona utilizadores e grupos para [Clarizen One](https://www.clarizen.com/) utilizando o serviço de fornecimento AZure AD. Para obter informações sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte [automatizar o fornecimento e desprovisionamento do utilizador para software como aplicações de serviço (SaaS) com Azure AD](../app-provisioning/user-provisioning.md).

## <a name="capabilities-supported"></a>Capacidades suportadas

> [!div class="checklist"]
> * Crie utilizadores em Clarizen One.
> * Remova os utilizadores em Clarizen One quando já não necessitam de acesso.
> * Mantenha os atributos do utilizador sincronizados entre Azure AD e Clarizen One.
> * Grupos de provisão e membros do grupo no Clarizen One.
> * Recomenda-se um [único sinal (SSO)](./clarizen-tutorial.md) a Clarizen One.

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* [Um inquilino do Azure AD](../develop/quickstart-create-new-tenant.md).
* Uma conta de utilizador em Azure AD com [permissão](../roles/permissions-reference.md) para configurar o provisionamento. Exemplos são administrador de aplicação, administrador de aplicação cloud, proprietário de aplicações ou administrador global.
* Uma conta de utilizador em Clarizen One com [permissões](https://success.clarizen.com/hc/articles/360011833079-API-Keys-Support) **de Integração user** e **Lite Admin** .

## <a name="step-1-plan-your-provisioning-deployment"></a>Passo 1. Planear a sua implementação de aprovisionamento

1. Saiba [como funciona o serviço de aprovisionamento](../app-provisioning/user-provisioning.md).
1. Determine quem vai estar no [âmbito do aprovisionamento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
1. Determine quais os dados a [mapear entre Azure AD e Clarizen One](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-clarizen-one-to-support-provisioning-with-azure-ad"></a>Passo 2. Configure Clarizen One para apoiar o provisionamento com Azure AD

1. Selecione um dos quatro URLs de inquilinos seguintes de acordo com o seu ambiente Clarizen One e centro de dados:
      * Centro de dados de produção dos EUA: https://servicesapp2.clarizen.com/scim/v2
      * Centro de dados de produção da UE: https://serviceseu1.clarizen.com/scim/v2
      * Centro de dados da Caixa de Areia dos EUA: https://servicesapp.clarizentb.com/scim/v2
      * Centro de dados da CAIXA de areia da UE: https://serviceseu.clarizentb.com/scim/v2

1. Gerar uma [chave API](https://success.clarizen.com/hc/articles/360011833079-API-Keys-Support). Este valor será introduzido na caixa **Secret Token** no **separador Provisioning** da sua aplicação Clarizen One no portal Azure.

## <a name="step-3-add-clarizen-one-from-the-azure-ad-application-gallery"></a>Passo 3. Adicione Clarizen One da galeria de aplicações AD Azure

Adicione Clarizen One da galeria de aplicações AZure AD para começar a gerir o fornecimento ao Clarizen One. Se já configurar clarizen One para SSO, pode utilizar a mesma aplicação. Quando testar a integração inicialmente, crie uma aplicação separada. Para saber mais sobre como adicionar uma aplicação na galeria, consulte [adicionar uma aplicação ao seu inquilino AZure AD.](../manage-apps/add-application-portal.md)

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passo 4: Determinar quem vai estar no âmbito do aprovisionamento

Com o serviço de prestação de Ad Azure, pode ser aparida quem será a provisionado com base na atribuição à aplicação ou com base em atributos do utilizador ou grupo. Se optar por escolher o âmbito de aplicação de quem será aprovisionado na sua app com base na atribuição, siga os passos na atribuição do [utilizador gerir uma aplicação no Azure Ative Directory](../manage-apps/assign-user-or-group-access-portal.md) para atribuir utilizadores e grupos à aplicação. Se optar por escolher o âmbito de aplicação de quem será provisionado apenas com base em atributos do utilizador ou grupo, utilize um filtro de escoamento, conforme descrito no [provisionamento de aplicações baseados em Atributos com filtros de escoamento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

* Quando atribui utilizadores e grupos ao Clarizen One, tem de selecionar outra função que não o **Acesso Predefinido**. Os utilizadores com a função de acesso predefinido estão excluídos do provisionamento e serão marcados como não efetivamente intitulados nos registos de provisionamento. Se a única função disponível na aplicação for a função de acesso predefinido, pode [atualizar o manifesto de aplicação](../develop/howto-add-app-roles-in-azure-ad-apps.md) para adicionar mais funções.
* Comece pequeno. Teste com um pequeno conjunto de utilizadores e grupos antes de chegar a todos. Quando o âmbito de provisão é definido para utilizadores e grupos atribuídos, pode manter o controlo atribuindo um ou dois utilizadores ou grupos à aplicação. Quando o âmbito é definido para todos os utilizadores e grupos, pode especificar um [filtro de deteção baseado em atributos](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

## <a name="step-5-configure-automatic-user-provisioning-to-clarizen-one"></a>Passo 5. Configure o fornecimento automático de utilizadores ao Clarizen One

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores ou grupos no TestApp com base em atribuições de utilizador ou grupo em Ad Azure.

### <a name="configure-automatic-user-provisioning-for-clarizen-one-in-azure-ad"></a>Configure o fornecimento automático de utilizadores para clarizen One em Azure AD

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **aplicações da Empresa**  >  **Todas as aplicações**.

      ![Screenshot que mostra o painel de aplicações da Enterprise.](common/enterprise-applications.png)

1. Na lista de candidaturas, selecione **Clarizen One**.

      ![Screenshot que mostra o link Clarizen One na lista de Aplicações.](common/all-applications.png)

1. Selecione o separador **Aprovisionamento**.

      ![Screenshot que mostra o separador Provisioning.](common/provisioning.png)

1. Desa ajuste **o modo de provisionamento** para **automático**.

      ![Screenshot que mostra a opção de provisionamento Automático.](common/provisioning-automatic.png)

1. Sob a secção **de Credenciais de Administração,** insira o seu URL de **Inquilino** Clarizen One e **o Token Secreto.** Selecione **a Ligação** de Teste para garantir que o Azure AD pode ligar-se ao Clarizen One. Se a ligação falhar, certifique-se de que a sua conta Clarizen One tem permissões de administração e tente novamente.

    ![Screenshot que mostra a caixa do Token Secreto.](common/provisioning-testconnection-tenanturltoken.png)

1. Na caixa de **e-mail de notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento. Selecione a **notificação Enviar uma notificação por e-mail quando ocorrer uma falha** na caixa de verificação.

    ![Screenshot que mostra a caixa de e-mail de notificação.](common/provisioning-notification-email.png)

1. Selecione **Guardar**.

1. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users to Clarizen One**.

1. Reveja os atributos do utilizador que são sincronizados de Azure AD a Clarizen One na secção **De mapeamento de Atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas de utilizador em Clarizen One para operações de atualização. Se alterar o [atributo de alvo correspondente,](../app-provisioning/customize-application-attributes.md)deve certificar-se de que a API Clarizen One suporta utilizadores filtrantes com base nesse atributo. Selecione o botão **Guardar** para escoar quaisquer alterações.

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

1. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Groups to Clarizen One**.

1. Reveja os atributos do grupo que são sincronizados de Azure AD a Clarizen One na secção **De mapeamento de Atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar com os grupos em Clarizen One para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

      |Atributo|Tipo|
      |---|---|
      |displayName|String|
      |externalId|String|
      |membros|Referência|

1. Para configurar filtros de deteção, consulte as instruções no tutorial do  [filtro de escotagem](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Para ativar o serviço de prestação de Ad Azure para Clarizen One, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

      ![Screenshot que mostra o Estado de Provisionamento alternado.](common/provisioning-toggle-on.png)

1. Defina os utilizadores ou grupos que pretende prever ao Clarizen One selecionando os valores pretendidos no **Âmbito** na secção **Definições.**

      ![Screenshot que mostra o âmbito de provisão.](common/provisioning-scope.png)

1. Quando estiver pronto para a provisão, **selecione Save**.

      ![Screenshot que mostra a poupança da configuração de provisionamento.](common/provisioning-configuration-save.png)

Esta operação inicia o ciclo de sincronização inicial de todos os utilizadores e grupos definidos no **Âmbito** na secção **Definições**. O ciclo inicial leva mais tempo a ser executado do que os ciclos subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de aprovisionamento do Azure AD esteja em execução.

## <a name="step-6-monitor-your-deployment"></a>Passo 6. Monitorizar a implementação

Depois de configurar o fornecimento, use os seguintes recursos para monitorizar a sua implantação.

1. Utilize os [registos de provisionamento](../reports-monitoring/concept-provisioning-logs.md) para determinar quais os utilizadores que foram a provisionados com sucesso ou sem sucesso.
1. Verifique a barra de [progresso](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) para ver o estado do ciclo de provisionamento e o quão perto está de ser concluído.
1. Se a configuração de aprovisionamento parecer estar num mau estado de funcionamento, a aplicação vai entrar em quarentena. Para saber mais sobre estados de quarentena, consulte [o provisionamento da aplicação em estado de quarentena](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="troubleshooting-tips"></a>Sugestões de resolução de problemas

Quando atribuir um utilizador à aplicação clarizen One, selecione apenas a função **Utilizador.** As seguintes funções são inválidas:

* Administrador (administrador)
* Utilizador de relatório de e-mail
* Utilizador externo
* Utilizador financeiro
* Utilizador social
* Superuser
* Tempo & Utilizador de Despesas

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do provisionamento de conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como analisar os registos e obter relatórios sobre a atividade de aprovisionamento](../app-provisioning/check-status-user-account-provisioning.md)