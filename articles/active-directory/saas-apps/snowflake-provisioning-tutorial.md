---
title: 'Tutorial: Configurar floco de neve para fornecimento automático de utilizadores com Azure Ative Directory | Microsoft Docs'
description: Aprenda a configurar o Azure Ative Directory para provisá-lo e desprovisionar automaticamente as contas dos utilizadores ao Snowflake.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 06f11763498e3e8393d688a71e1c37b466be3f6f
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2021
ms.locfileid: "99539540"
---
# <a name="tutorial-configure-snowflake-for-automatic-user-provisioning"></a>Tutorial: Configure Snowflake para o fornecimento automático de utilizadores

Este tutorial demonstra os passos que executa no Snowflake e no Azure Ative Directory (AZure AD) para configurar a Azure AD para provisão automática e desprovisionar utilizadores e grupos para [Snowflake](https://www.Snowflake.com/pricing/). Para detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte [O que é o fornecimento automatizado de utilizadores de aplicações SaaS em Azure AD?](../app-provisioning/user-provisioning.md) 

> [!NOTE]
> Este conector encontra-se atualmente em visualização pública. Para obter informações sobre os termos de utilização, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="capabilities-supported"></a>Capacidades suportadas
> [!div class="checklist"]
> * Criar utilizadores em Snowflake
> * Remova os utilizadores em Snowflake quando já não necessitam de acesso
> * Mantenha os atributos do utilizador sincronizados entre Azure AD e Snowflake
> * Grupos de provisão e membros do grupo em Snowflake
> * Permitir [um único sinal de](./snowflake-tutorial.md) snowflake (recomendado)

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* [Um inquilino da AD AZure](../develop/quickstart-create-new-tenant.md)
* Uma conta de utilizador em Azure AD com [permissão](../roles/permissions-reference.md) para configurar o provisionamento (Administrador de Aplicação, Administrador de Aplicação cloud, Proprietário de Aplicações ou Administrador Global)
* [Um inquilino de Snowflake](https://www.Snowflake.com/pricing/)
* Uma conta de utilizador em Snowflake com permissões de administração

## <a name="step-1-plan-your-provisioning-deployment"></a>Passo 1: Planeie a sua implantação de provisionamento
1. Saiba [como funciona o serviço de aprovisionamento](../app-provisioning/user-provisioning.md).
2. Determine quem vai estar no [âmbito do aprovisionamento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determine quais os dados a [mapear entre Azure AD e Snowflake](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-snowflake-to-support-provisioning-with-azure-ad"></a>Passo 2: Configure floco de neve para apoiar o provisionamento com Azure AD

Antes de configurar snowflake para o fornecimento automático de utilizadores com Azure AD, você precisa ativar o fornecimento de Sistema de Gestão de Identidade de Domínio Cruzado (SCIM) em Floco de Neve.

1. Inscreva-se na sua consola de administração snowflake. Introduza a seguinte consulta na folha de cálculo realçada e, em seguida, selecione **Executar**.

    ![Screenshot da consola de administração Snowflake com botão de consulta e executar.](media/Snowflake-provisioning-tutorial/image00.png)

2.  Um símbolo de acesso SCIM é gerado para o seu inquilino snowflake. Para recuperá-lo, selecione o link realçado na imagem seguinte.

    ![Screenshot de uma folha de cálculo no Snowflake U I com o token de acesso S C I M chamado.](media/Snowflake-provisioning-tutorial/image01.png)

3. Copie o valor simbólico gerado e selecione **Feito**. Este valor é introduzido na caixa **Secret Token** no **separador Provisioning** da sua aplicação Snowflake no portal Azure.

    ![Screenshot da secção Detalhes, mostrando o token copiado para o campo de texto e a opção 'Fazer' foi chamada.](media/Snowflake-provisioning-tutorial/image02.png)

## <a name="step-3-add-snowflake-from-the-azure-ad-application-gallery"></a>Passo 3: Adicione floco de neve da galeria de aplicações AZure AD

Adicione Snowflake da galeria de aplicações AZure AD para começar a gerir o fornecimento a Snowflake. Se configurar previamente o Snowflake para um único sinal de sso, pode utilizar a mesma aplicação. No entanto, recomendamos que crie uma aplicação separada quando está inicialmente a testar a integração. [Saiba mais sobre a adição de uma aplicação na galeria.](../manage-apps/add-application-portal.md) 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passo 4: Definir quem estará no âmbito do provisionamento 

O serviço de prestação de Ad Azure permite-lhe atear o âmbito de aplicação de quem será a provisionado com base na atribuição à aplicação, ou com base em atributos do utilizador ou grupo. Se optar por escolher quem será aprovisionado na sua app com base na atribuição, pode utilizar os [passos para atribuir utilizadores e grupos à aplicação.](../manage-apps/assign-user-or-group-access-portal.md) Se optar por escolher o âmbito de aplicação de quem será a provisionado apenas com base em atributos do utilizador ou grupo, pode [utilizar um filtro de deteção](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

Tenha estas dicas em mente:

* Ao atribuir utilizadores e grupos a Snowflake, tem de selecionar outra função que não o Acesso Predefinido. Os utilizadores com a função Acesso Predefinido são excluídos do aprovisionamento e marcados como não autorizados de forma efetiva nos registos de aprovisionamento. Se a única função disponível na aplicação for a função Acesso Predefinido, pode [atualizar o manifesto de aplicação](../develop/howto-add-app-roles-in-azure-ad-apps.md) para adicionar mais funções. 

* Comece pequeno. Teste com um pequeno conjunto de utilizadores e grupos antes de implementar para todos. Quando o âmbito de provisionamento é definido para utilizadores e grupos atribuídos, você pode controlá-lo atribuindo um ou dois utilizadores ou grupos à app. Quando o âmbito é definido para todos os utilizadores e grupos, pode especificar um [filtro de deteção baseado em atributos](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-snowflake"></a>Passo 5: Configurar o fornecimento automático do utilizador ao Floco de Neve 

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores e grupos em Snowflake. Pode basear a configuração nas atribuições de utilizador e grupo em Azure AD.

Para configurar o fornecimento automático de utilizadores para flocos de neve em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **aplicações da Empresa**  >  **Todas as aplicações**.

    ![Screenshot que mostra o painel de aplicações da Enterprise.](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Snowflake**.

    ![Screenshot que mostra uma lista de aplicações.](common/all-applications.png)

3. Selecione o separador **Aprovisionamento**.

    ![Screenshot das opções De gestão com a opção Provisioning chamada.](common/provisioning.png)

4. Desa ajuste **o modo de provisionamento** para **automático**.

    ![Screenshot da lista de abandono do modo de provisionamento com a opção Automática chamada.](common/provisioning-automatic.png)

5. Na secção **Credenciais Admin,** insira o URL base SCIM 2.0 e o símbolo de autenticação que recuperou anteriormente nas caixas URL e **Secret Token** do **Arrendatário,** respectivamente. 

   Selecione **a Ligação** de Teste para garantir que o Azure AD pode ligar-se ao Floco de Neve. Se a ligação falhar, certifique-se de que a sua conta snowflake tem permissões de administração e tente novamente.

    ![Screenshot que mostra caixas para o inquilino U R L e ficha secreta, juntamente com o botão de Ligação de Teste.](common/provisioning-testconnection-tenanturltoken.png)

6. Na caixa de **e-mail de notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento. Em seguida, selecione a **notificação enviar uma notificação por e-mail quando ocorrer uma falha** na caixa de verificação.

    ![Screenshot que mostra caixas para e-mail de notificação.](common/provisioning-notification-email.png)

7. Selecione **Guardar**.

8. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users to Snowflake**.

9. Reveja os atributos do utilizador que são sincronizados de Azure AD a Snowflake na secção **De Mapeamento de Atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas de utilizador em Snowflake para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

   |Atributo|Tipo|
   |---|---|
   |active|Booleano|
   |displayName|String|
   |emails[type eq "work"].value|String|
   |userName|String|
   |name.givenName|String|
   |name.familyName|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:defaultRole|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:defaultWarehouse|String|

10. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Groups to Snowflake**.

11. Reveja os atributos do grupo que são sincronizados de Azure AD a Snowflake na secção **De Mapeamento de Atributos.** Os atributos selecionados como propriedades **de correspondência** são usados para combinar com os grupos em Snowflake para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

    |Atributo|Tipo|
    |---|---|
    |displayName|String|
    |membros|Referência|

12. Para configurar filtros de deteção, consulte as instruções no tutorial do [filtro de escotagem](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para ativar o serviço de fornecimento de Ad Azure para Floco de Neve, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

     ![Screenshot que mostra o Estado de Provisionamento ligado.](common/provisioning-toggle-on.png)

14. Defina os utilizadores e grupos que pretende providenciar ao Floco de Neve, escolhendo os valores pretendidos no **Âmbito** na secção **Definições.** 

    Se esta opção não estiver disponível, configura os campos necessários sob **as Credenciais de Administração**, selecione **Save** e refresque a página. 

     ![Screenshot que mostra escolhas para o âmbito de provisionamento.](common/provisioning-scope.png)

15. Quando estiver pronto para a provisão, **selecione Save**.

     ![Screenshot do botão para guardar uma configuração de provisionamento.](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser efetua do que as sincronizações subsequentes. As sincronizações subsequentes ocorrem a cada 40 minutos, desde que o serviço de fornecimento AZure AD esteja em execução.

## <a name="step-6-monitor-your-deployment"></a>Passo 6: Monitorize a sua implantação
Depois de configurar o provisionamento, use os seguintes recursos para monitorizar a sua implantação:

- Utilize os [registos de provisionamento](../reports-monitoring/concept-provisioning-logs.md) para determinar quais os utilizadores que foram a provisionados com sucesso ou sem sucesso.
- Verifique a barra de [progresso](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) para ver o estado do ciclo de provisionamento e o quão perto está de ser concluído.
- Se a configuração de aprovisionamento parecer estar num mau estado de funcionamento, a aplicação vai entrar em quarentena. [Saiba mais sobre estados de quarentena.](../app-provisioning/application-provisioning-quarantine-status.md)  

## <a name="connector-limitations"></a>Limitações do conector

As fichas SCIM geradas por flocos de neve expiram em 6 meses. Esteja ciente de que precisa de atualizar estes tokens antes de expirarem, para permitir que as sincronizações de provisionamento continuem a funcionar. 

## <a name="troubleshooting-tips"></a>Sugestões de resolução de problemas

O serviço de prestação de AD Azure opera atualmente ao abrigo de [gamas IP específicas](../app-provisioning/use-scim-to-provision-users-and-groups.md#ip-ranges). Se necessário, pode restringir outras gamas de IP e adicionar estes intervalos IP específicos à lista de admissões. Esta técnica permitirá o fluxo de tráfego do serviço de fornecimento Azure AD para a sua aplicação.

## <a name="change-log"></a>Change log

* 07/21/2020: Ativado soft-delete para todos os utilizadores (através do atributo ativo).

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do provisionamento de conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que são acesso a aplicações e um único acesso com o Azure Ative Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passos seguintes
* [Saiba como analisar os registos e obter relatórios sobre a atividade de aprovisionamento](../app-provisioning/check-status-user-account-provisioning.md)
