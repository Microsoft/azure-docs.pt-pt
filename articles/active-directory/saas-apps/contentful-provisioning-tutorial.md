---
title: 'Tutorial: Configurar Conteúdo para fornecimento automático de utilizadores com Diretório Ativo Azure'
description: Aprenda a providenciar e desprovisionar automaticamente as contas dos utilizadores do Azure Ative Directory (Azure AD) ao Contentful.
services: active-directory
documentationcenter: ''
author: zchia
manager: beatrizd
ms.assetid: 3b761984-a9a0-4519-b23e-563438978de5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/11/2020
ms.author: zhchia
ms.openlocfilehash: c9d19624d90b1228b2a44caeff7d103af3172ed9
ms.sourcegitcommit: 66479d7e55449b78ee587df14babb6321f7d1757
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/15/2020
ms.locfileid: "97516343"
---
# <a name="tutorial-configure-contentful-for-automatic-user-provisioning"></a>Tutorial: Configurar Conteúdo para o fornecimento automático de utilizadores

Este artigo descreve os passos necessários para completar no Contentful e no Azure Ative Directory (Azure AD) para configurar o fornecimento automático do utilizador. Quando configurado, o Azure AD fornece automaticamente e desprovisiona os utilizadores e grupos para [Contentful](https://www.contentful.com/) utilizando o serviço de fornecimento AZure AD. Para obter detalhes importantes sobre o que este serviço faz e como funciona, e para perguntas frequentes, consulte [o fornecimento e desprovisionamento de utilizadores da Automamate para aplicações SaaS com o Azure Ative Directory](../app-provisioning/user-provisioning.md). 

## <a name="capabilities-supported"></a>Capacidades suportadas

> [!div class="checklist"]
> * Criar utilizadores em Conteúdo
> * Remova os utilizadores em Contentful quando já não necessitam de acesso
> * Mantenha os atributos do utilizador sincronizados entre AZure AD e Contentful
> * Grupos de provisão e membros do grupo em Contentful
> * [Único sinal de contentamento](contentful-tutorial.md) (recomendado)

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* [Um inquilino do Azure AD](../develop/quickstart-create-new-tenant.md). 
* Uma conta de utilizador em Azure AD que tem [permissão](../roles/permissions-reference.md) para configurar o provisionamento (por exemplo, Administrador de Aplicação, Administrador de Aplicação cloud, Proprietário de Aplicações ou Administrador Global). 
* Uma conta de organização contentada que tem uma subscrição que suporta o fornecimento de Sistema de Gestão de Identidade de Domínio Cruzado (SCIM). Se tiver dúvidas sobre a subscrição da sua organização, contacte [o Contentful Support](mailto:support@contentful.com).
 
## <a name="plan-your-provisioning-deployment"></a>Planear a sua implementação de aprovisionamento

1. Saiba [como funciona o serviço de aprovisionamento](../app-provisioning/user-provisioning.md).
2. Determine quem vai estar no [âmbito do aprovisionamento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determine quais os dados a [mapear entre AZure AD e Contentful](../app-provisioning/customize-application-attributes.md). 

## <a name="configure-contentful-to-support-provisioning-with-azure-ad"></a>Configure Contentamento satisfeito para apoiar o provisionamento com Azure AD

1. No Contentful, crie uma conta **de Utilizador de Serviço.** Todas as permissões de provisionamento para a Azure são fornecidas através desta conta. Recomendamos que escolha **o Proprietário** como o papel de organização para esta conta.

2. Inscreva-se no Contentful como **Utilizador de Serviço.**

3. No menu esquerdo, selecione **Organização para configurar** o fornecimento do utilizador de  >  **ferramentas** de acesso  >  .

   ![Screenshot do menu de definições da Organização em Contentful, com o fornecimento do Utilizador em destaque nas Ferramentas de Acesso.](media/contentful-provisioning-tutorial/access.png)

4. Copie e guarde o **URL SCIM**. Você introduzirá este valor no portal Azure, no **separador Provisioning** da sua aplicação Contentful.

5. **Selecione Gerar ficha de acesso pessoal**.

    ![url](media/contentful-provisioning-tutorial/generate.png)

6. Na janela modal, insira um nome para o seu token de acesso pessoal e, em seguida, **selecione Gerar**.

7. A URL SCIM e o símbolo secreto são gerados. Copiar e guardar estes valores. Você introduzirá estes valores no **separador Provisioning** da sua aplicação Contentful no portal Azure.

    ![Screenshot do painel de token de acesso pessoal, com C F P A T e o nome de espaço reservado simbólico em destaque.](media/contentful-provisioning-tutorial/token.png)


Se tiver dúvidas enquanto configurar o fornecimento na consola de administração Contentful, contacte [o Contentful Support](mailto:support@contentful.com).

## <a name="add-contentful-from-the-azure-ad-application-gallery"></a>Adicione Conteúdo da galeria de aplicações AZure AD

Para gerir o fornecimento ao Contentful, adicione Contentful da galeria de aplicações AZure AD. Se tiver configurado contentful para um único sôs-on, pode utilizar a mesma aplicação. No entanto, recomendamos que crie uma app separada para testar inicialmente a integração. Saiba como [adicionar uma aplicação na galeria.](../manage-apps/add-application-portal.md) 

## <a name="define-who-will-be-in-scope-for-provisioning"></a>Determinar quem vai estar no âmbito do aprovisionamento 

Pode utilizar o serviço de fornecimento Azure AD para o âmbito de aplicação que será prestado com base na atribuição à aplicação ou com base em atributos do utilizador ou grupo. 

Se optar por escolher o âmbito de aplicação a quem será aprovisionado na sua app com base na atribuição, preencha os passos para [atribuir utilizadores e grupos à aplicação.](../manage-apps/assign-user-or-group-access-portal.md)

Se optar por escolher o âmbito de aplicação de quem será provisionado apenas com base em atributos do utilizador ou grupo, utilize um filtro de deteção para [definir regras condicionais para o provisionamento das contas dos utilizadores](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Quando atribui utilizadores e grupos ao Contentful, tem de selecionar outra função que não o **Acesso Predefinido**. Os utilizadores que tenham a função de Acesso Predefinido estão excluídos do provisionamento e são indicados nos registos de provisionamento como não tendo direito eficazmente. Se a única função disponível na aplicação for a função Acesso Predefinido, pode [atualizar o manifesto de aplicação](../develop/howto-add-app-roles-in-azure-ad-apps.md) para adicionar mais funções. 
* Comece pequeno. Teste com um pequeno conjunto de utilizadores e grupos antes de chegar a todos. Quando o âmbito de provisionamento é definido para utilizadores e grupos atribuídos, você pode controlar o âmbito atribuindo um ou dois utilizadores ou grupos à app. Quando o âmbito é definido para todos os utilizadores e grupos, pode especificar um [filtro de deteção baseado em atributos](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

## <a name="configure-automatic-user-provisioning-to-contentful"></a>Configure o fornecimento automático de utilizadores ao Contentful 

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores e grupos numa aplicação de teste baseada em atribuições de utilizador ou grupo em Azure AD.

### <a name="configure-automatic-user-provisioning-for-contentful-in-azure-ad"></a>Configure o fornecimento automático de utilizadores para o Contentful em AZure AD

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.

   ![Screenshot que mostra o menu de aplicações da Enterprise no portal Azure, com todas as aplicações em destaque.](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Contentful**.

   ![Screenshot que mostra os primeiros 20 resultados devolvidos na lista de Aplicações.](common/all-applications.png)

3. Selecione o separador **Aprovisionamento**.

   ![Screenshot do separador Provisioning realçado na secção Gerir o menu esquerdo.](common/provisioning.png)

4. Desa ajuste **o modo de provisionamento** para **automático**.

   ![Screenshot que mostra as opções do Modo de Provisionamento, com o Automático em destaque.](common/provisioning-automatic.png)

5. Na secção **Credenciais Admin,** insira o seu URL de inquilino satisfeito e um símbolo secreto. Para garantir que o Azure AD pode ligar-se ao Contentful, selecione **a Ligação de Teste**. Se a ligação falhar, certifique-se de que a sua conta Contentful tem permissões de Administração e, em seguida, tente novamente.

   ![Screenshot que mostra as caixas de texto Do Inquilino U R L e Secret Token, com o botão de Ligação de Teste realçado.](common/provisioning-testconnection-tenanturltoken.png)

6. No **E-mail de Notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e, em seguida, selecione a **notificação enviar uma notificação por e-mail quando ocorrer uma falha** na caixa de verificação.

   ![Screenshot que mostra a caixa de texto de Email de Notificação.](common/provisioning-notification-email.png)

7. Selecione **Guardar**.

8. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users to Contentful**.

9. Na secção **Atributo-Mapeamento,** reveja os atributos do utilizador que são sincronizados de AZure AD a Contentful. Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas do utilizador em Contentful para operações de atualização. Se optar por alterar o [atributo de alvo correspondente,](../app-provisioning/customize-application-attributes.md)deve certificar-se de que a API contentativa suporta a filtragem dos utilizadores com base nesse atributo. Selecione o botão **Guardar** para escoar quaisquer alterações.

   |Atributo|Tipo|Suportado para filtragem|
   |---|---|---|
   |userName|String|&check;|
   |name.givenName|String|
   |name.familyName|String|

10. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Groups to Contentful**.

11. Na secção **Atributo-Mapping,** reveja os atributos do grupo que são sincronizados de AZure AD a Contentful. Os atributos selecionados como propriedades **de correspondência** são utilizados para corresponder aos grupos em Contentful para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

    |Atributo|Tipo|Suportado para filtragem|
    |---|---|---|
    |displayName|String|&check;|
    |membros|Referência|

12. Para configurar filtros de deteção, complete os passos descritos no tutorial do [filtro de escotagem](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para ativar o serviço de prestação de AD Azure para Contentful, na secção **Definições,** para Estado **de Provisionamento**, selecione **On**.

    ![Screenshot que mostra o estado de provisionamento ligado e desligado.](common/provisioning-toggle-on.png)

14. Para definir os utilizadores ou grupos que pretende apresentar ao Contentful, na secção **Definições,** para **Scope,** selecione a opção relevante.

    ![Screenshot que mostra opções que pode selecionar no painel Scope.](common/provisioning-scope.png)

15. Quando estiver pronto para a provisão, **selecione Save**.

    ![Screenshot que mostra o botão Guardar e o botão Cancelar.](common/provisioning-configuration-save.png)

Esta operação inicia o ciclo de sincronização inicial de todos os utilizadores e grupos definidos no **Âmbito** em **Definições**. O ciclo inicial leva mais tempo a ser executado do que os ciclos subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de aprovisionamento do Azure AD esteja em execução. 

## <a name="monitor-your-deployment"></a>Monitorizar a implementação

Depois de configurar o fornecimento, utilize os seguintes recursos para monitorizar a sua implantação:

* Para determinar quais os utilizadores que foram a provisionados com sucesso ou sem sucesso, consulte os [registos de provisionamento](../reports-monitoring/concept-provisioning-logs.md).
* Para ver o estado do ciclo de provisionamento e a sua proximidade com o acabamento, verifique a [barra de progresso.](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)
* Se a configuração do provisionamento parece estar num estado pouco saudável, a aplicação entra em quarentena. Saiba mais sobre [estados de quarentena.](../app-provisioning/application-provisioning-quarantine-status.md)  

## <a name="next-steps"></a>Passos seguintes

* [Saiba como analisar os registos e obter relatórios sobre a atividade de aprovisionamento](../app-provisioning/check-status-user-account-provisioning.md)
* [Gerir o fornecimento de conta de utilizador para apps empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)
