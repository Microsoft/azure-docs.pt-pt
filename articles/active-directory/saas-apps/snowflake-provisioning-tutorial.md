---
title: 'Tutorial: Configure Snowflake para fornecimento automático de utilizadores com Diretório Ativo Azure / Microsoft Docs'
description: Aprenda a configurar o Azure Ative Directory para provisões automáticas e desavisagem de contas de utilizadores ao Snowflake.
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
ms.openlocfilehash: cc88712dd686983baed858de47e11dfa44e85350
ms.sourcegitcommit: 19ffdad48bc4caca8f93c3b067d1cf29234fef47
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/06/2021
ms.locfileid: "97955523"
---
# <a name="tutorial-configure-snowflake-for-automatic-user-provisioning"></a>Tutorial: Configure Snowflake para o fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados no Snowflake e no Azure Ative Directory (Azure AD) para configurar a Azure AD para provisão automática e desa provisionamento de utilizadores e/ou grupos para [Snowflake](https://www.Snowflake.com/pricing/). Para obter detalhes importantes sobre o que este serviço faz, como funciona e perguntas frequentes, veja [Automatizar o aprovisionamento e desaprovisionamento de utilizadores em aplicações SaaS no Azure Active Directory](../app-provisioning/user-provisioning.md). 


> [!NOTE]
> Este conector encontra-se atualmente em Visualização Pública. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="capabilities-supported"></a>Capacidades suportadas
> [!div class="checklist"]
> * Criar utilizadores em Snowflake
> * Remova os utilizadores em Snowflake quando já não necessitam de acesso
> * Mantenha os atributos do utilizador sincronizados entre Azure AD e Snowflake
> * Grupos de provisão e membros do grupo em Snowflake
> * [Único sinal de](./snowflake-tutorial.md) Snowflake (recomendado)

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* [Um inquilino do Azure AD](../develop/quickstart-create-new-tenant.md).
* Uma conta de utilizador no Azure AD com [permissão](../roles/permissions-reference.md) para configurar o aprovisionamento (por ex., Administrador de Aplicações, Administrador de Aplicações de Cloud, Proprietário da Aplicação ou Administrador Global).
* [Um inquilino de Snowflake.](https://www.Snowflake.com/pricing/)
* Uma conta de utilizador em Snowflake com permissões de administrador.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passo 1. Planear a sua implementação de aprovisionamento
1. Saiba [como funciona o serviço de aprovisionamento](../app-provisioning/user-provisioning.md).
2. Determine quem vai estar no [âmbito do aprovisionamento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determine quais os dados a [mapear entre Azure AD e Snowflake](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-snowflake-to-support-provisioning-with-azure-ad"></a>Passo 2. Configure floco de neve para apoiar o provisionamento com Azure AD

Antes de configurar o Snowflake para o fornecimento automático do utilizador com Azure AD, terá de permitir o fornecimento scim em Snowflake.

1. Inscreva-se na sua Consola de Administração snowflake. Introduza a consulta abaixo mostrada na folha de cálculo realçada e clique em **Executar**.

    ![Consola de administração de flocos de neve](media/Snowflake-provisioning-tutorial/image00.png)

2.  Um scim Access Token será gerado para o seu inquilino snowflake. Para recuperá-lo, clique no link realçado abaixo.

    ![Screenshot de uma folha de cálculo no Snowflake U I com o token S C I M Access chamado.](media/Snowflake-provisioning-tutorial/image01.png)

3. Copie o valor simbólico gerado e clique em **Fazer**. Este valor será introduzido no campo **Secret Token** no separador Provisioning da sua aplicação Snowflake no portal Azure.

    ![Screenshot da secção Detalhes mostrando o token copiado para o campo de texto e a opção 'Feito' foi chamada.](media/Snowflake-provisioning-tutorial/image02.png)

## <a name="step-3-add-snowflake-from-the-azure-ad-application-gallery"></a>Passo 3. Adicione Floco de Neve da galeria de aplicações AZure AD

Adicione Snowflake da galeria de aplicações AZure AD para começar a gerir o fornecimento a Snowflake. Se já configurar snowflake para SSO, pode utilizar a mesma aplicação. No entanto, é recomendável criar uma aplicação separada ao testar a integração inicialmente. Saiba mais sobre como adicionar uma aplicação a partir da galeria [aqui](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passo 4: Determinar quem vai estar no âmbito do aprovisionamento 

O serviço de aprovisionamento do Azure AD permite-lhe determinar quem vai ser aprovisionado com base na atribuição à aplicação e/ou com base em atributos do utilizador/grupo. Se optar por determinar quem vai ser aprovisionado na sua aplicação com base na atribuição, pode utilizar os seguintes [passos](../manage-apps/assign-user-or-group-access-portal.md) para atribuir utilizadores e grupos à aplicação. Se escolher determinar quem vai ser aprovisionado com base apenas em atributos do utilizador ou grupo, pode utilizar um filtro de âmbito conforme descrito [aqui](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Ao atribuir utilizadores e grupos a Snowflake, deve selecionar outra função que não o **Acesso Predefinido**. Os utilizadores com a função Acesso Predefinido são excluídos do aprovisionamento e marcados como não autorizados de forma efetiva nos registos de aprovisionamento. Se a única função disponível na aplicação for a função de acesso predefinido, pode [atualizar o manifesto de aplicação](../develop/howto-add-app-roles-in-azure-ad-apps.md) para adicionar funções adicionais. 

* Comece pequeno. Teste com um pequeno conjunto de utilizadores e grupos antes de implementar para todos. Quando o âmbito do aprovisionamento está definido para os utilizadores e os grupos atribuídos, pode controlar isto ao atribuir um ou dois utilizadores ou grupos à aplicação. Quando o âmbito está definido para todos os utilizadores e grupos, pode especificar um [filtro de âmbito baseado em atributos](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-snowflake"></a>Passo 5. Configure o fornecimento automático de utilizadores ao Floco de Neve 

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos em Snowflake com base em atribuições de utilizador e/ou grupo em Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-snowflake-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para flocos de neve em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais** e, em seguida, **Todas as aplicações**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Snowflake**.

    ![O link Snowflake na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Aprovisionamento**.

    ![Screenshot das opções De gestão com a opção Provisioning chamada.](common/provisioning.png)

4. Defina o **Modo de Aprovisionamento** como **Automático**.

    ![Screenshot da lista de retirada do modo de provisionamento com a opção Automática chamada.](common/provisioning-automatic.png)

5. Sob a secção Credenciais de Administração, insira os valores **de URL base SCIM 2.0 e Token de autenticação** recuperados anteriormente nos campos URL e **Secret Token** do **arrendatário,** respectivamente. Clique em **Testar a Ligação** para garantir que o Azure AD pode ligar-se ao Floco de Neve. Se a ligação falhar, certifique-se de que a sua conta Snowflake tem permissões de administração e tente novamente.

    ![INQUILINO URL + Token](common/provisioning-testconnection-tenanturltoken.png)

7. No campo **'Email' de Notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e verifique a caixa de verificação - **Envie uma notificação de e-mail quando ocorrer uma falha**.

    ![E-mail de Notificação](common/provisioning-notification-email.png)

8. Clique em **Guardar**.

9. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users to Snowflake**.

10. Reveja os atributos do utilizador que são sincronizados de Azure AD a Snowflake na secção **De Mapeamento de Atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas de utilizador em Snowflake para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

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

11. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Groups to Snowflake**.

12. Reveja os atributos do grupo que são sincronizados de Azure AD a Snowflake na secção **De Mapeamento de Atributos.** Os atributos selecionados como propriedades **de correspondência** são usados para combinar com os grupos em Snowflake para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

      |Atributo|Tipo|
      |---|---|
      |displayName|String|
      |membros|Referência|

13. Para configurar filtros de âmbito, veja as instruções seguintes disponibilizadas no [Tutorial de filtro de âmbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

14. Para ativar o serviço de prestação de Ad Azure para o Floco de Neve, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

    ![Estado do Aprovisionamento Ativado](common/provisioning-toggle-on.png)

15. Defina os utilizadores e/ou grupos que deseja providenciar ao Snowflake, escolhendo os valores desejados no **Âmbito** na secção **Definições.** Se esta opção não estiver disponível, por favor configuure os campos necessários sob as Credenciais de Administração, Clique **em Guardar** e atualize a página. 

    ![Âmbito de Aprovisionamento](common/provisioning-scope.png)

16. Quando estiver pronto para aprovisionar, clique em **Guardar**.

    ![Guardar Configuração de Aprovisionamento](common/provisioning-configuration-save.png)

    Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de fornecimento AZure AD esteja em execução.

## <a name="step-6-monitor-your-deployment"></a>Passo 6. Monitorizar a implementação
Depois de configurar o aprovisionamento, utilize os seguintes recursos para monitorizar a sua implementação:

1. Utilize os [registos de aprovisionamento](../reports-monitoring/concept-provisioning-logs.md) para determinar quais os utilizadores que foram aprovisionados com ou sem êxito
2. Verifique a [barra de progresso](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) para ver o estado do ciclo de aprovisionamento e quão próximo está da conclusão
3. Se a configuração de aprovisionamento parecer estar num mau estado de funcionamento, a aplicação vai entrar em quarentena. Saiba mais sobre os estados de quarentena [aqui](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="connector-limitations"></a>Limitações do conector

* Os tokens do SCIM gerados pelo floco de neve expiram em 6 meses. Esteja ciente de que estes devem ser atualizados antes de expirarem para permitir que as sincronizações de provisionamento continuem a funcionar. 

## <a name="troubleshooting-tips"></a>Sugestões de Resolução de Problemas

* **Gamas IP** 

   O serviço de prestação de AD Azure opera atualmente ao abrigo de uma determinada gama de IP. Assim, se necessário, pode restringir outras gamas de IP e adicionar estes intervalos IP específicos à lista de admissões da sua aplicação para permitir o fluxo de tráfego do serviço de fornecimento de Azure AD à sua aplicação. Consulte a documentação em [Gamas IP](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#ip-ranges).

## <a name="change-log"></a>Alterar Registo

* 07/21/2020 - Ativado soft-delete para todos os utilizadores (através do atributo ativo).

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do fornecimento de conta de utilizador para apps empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes
* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md).