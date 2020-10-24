---
title: 'Tutorial: Configurar smartsheet para fornecimento automático de utilizadores com Diretório Ativo Azure Microsoft Docs'
description: Saiba como configurar o Azure Ative Directory para provisão automática e desa provisionar contas de utilizadores ao Smartsheet.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 06/07/2019
ms.author: jeedes
ms.openlocfilehash: a3c2567cf1799bca5750e90fbe5d89f6da952ff5
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92514897"
---
# <a name="tutorial-configure-smartsheet-for-automatic-user-provisioning"></a>Tutorial: Configurar smartsheet para fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados no Smartsheet e no Azure Ative Directory (Azure AD) para configurar a Azure AD para fornecimento e desavisagem automática de utilizadores e/ou grupos ao [Smartsheet.](https://www.smartsheet.com/pricing) Para obter detalhes importantes sobre o que este serviço faz, como funciona e perguntas frequentes, veja [Automatizar o aprovisionamento e desaprovisionamento de utilizadores em aplicações SaaS no Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Capacidades suportadas
> [!div class="checklist"]
> * Criar utilizadores no Smartsheet
> * Remova os utilizadores no Smartsheet quando já não necessitam de acesso
> * Mantenha os atributos do utilizador sincronizados entre Azure AD e Smartsheet
> * Único sinal de sação no Smartsheet (recomendado)

> [!NOTE]
> Este conector encontra-se atualmente em Visualização Pública. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* [Um inquilino do Azure AD](../develop/quickstart-create-new-tenant.md).
* Uma conta de utilizador no Azure AD com [permissão](../users-groups-roles/directory-assign-admin-roles.md) para configurar o aprovisionamento (por ex., Administrador de Aplicações, Administrador de Aplicações de Cloud, Proprietário da Aplicação ou Administrador Global).
* [Um inquilino da Smartsheet.](https://www.smartsheet.com/pricing)
* Uma conta de utilizador num plano Smartsheet Enterprise ou Enterprise Premier com permissões de Administrador de Sistema.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passo 1. Planear a sua implementação de aprovisionamento
1. Saiba [como funciona o serviço de aprovisionamento](../app-provisioning/user-provisioning.md).
2. Determine quem vai estar no [âmbito do aprovisionamento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determine quais os dados a [mapear entre Azure AD e Smartsheet](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-smartsheet-to-support-provisioning-with-azure-ad"></a>Passo 2. Configure smartsheet para apoiar o provisionamento com Azure AD

Antes de configurar o Smartsheet para o fornecimento automático do utilizador com AZure AD, terá de ativar o fornecimento scim no Smartsheet.

1. Inscreva-se como **SysAdmin** no **[portal Smartsheet](https://app.smartsheet.com/b/home)** e navegue para o **Administrador de Conta**.

    ![Administração de Conta smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-accountadmin.png)

2. Aceda aos **controlos de segurança > edição automática de > de fornecimento de utilizadores.**

    ![Controlos de Segurança smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-securitycontrols.png)

3. Adicione e valide os domínios de e-mail para os utilizadores que planeia providenciar de Azure AD a Smartsheet. Escolha **Não Habilitado** para garantir que todas as ações de provisionamento só são originárias do Azure AD, e também para garantir que a sua lista de utilizadores da Smartsheet está sincronizada com as atribuições AZure AD.

    ![Fornecimento de utilizadores de smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-userprovisioning.png)

4. Uma vez concluída a validação, terá de ativar o domínio. 

    ![Smartsheet Ativar Domínio](media/smartsheet-provisioning-tutorial/smartsheet-activatedomain.png)

5. Gere o **Token Secreto** necessário para configurar o fornecimento automático de utilizadores com Azure AD navegando para **Apps e Integrações.**

    ![Screenshot da página smartsheet Admin com o avatar do utilizador e a opção de Integrações & apps chamadas.](media/smartsheet-provisioning-tutorial/Smartsheet05.png)

6. Escolha **o acesso da API.** Clique **Em Gerar novo token de acesso**.

    ![Screenshot da caixa de diálogo de Definições Pessoais com o Acesso API e Gere novas opções de token de acesso chamadas.](media/smartsheet-provisioning-tutorial/Smartsheet06.png)

7. Defina o nome do Token de Acesso da API. Clique em **OK**.

    ![Screenshot do Passo 1 de 2: Gere o Token de Acesso API com a opção OK chamada.](media/smartsheet-provisioning-tutorial/Smartsheet07.png)

8. Copie o Token de Acesso API e guarde-o pois esta será a única altura em que poderá vê-lo. Isto é necessário no campo **Secret Token** em Azure AD.

    ![Ficha de folha inteligente](media/smartsheet-provisioning-tutorial/Smartsheet08.png)

## <a name="step-3-add-smartsheet-from-the-azure-ad-application-gallery"></a>Passo 3. Adicione smartsheet da galeria de aplicações AD Azure

Adicione smartsheet da galeria de aplicações AZure AD para começar a gerir o fornecimento ao Smartsheet. Se tiver configurado anteriormente o Smartsheet para SSO, pode utilizar a mesma aplicação. No entanto, é recomendável criar uma aplicação separada ao testar a integração inicialmente. Saiba mais sobre como adicionar uma aplicação a partir da galeria [aqui](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passo 4: Determinar quem vai estar no âmbito do aprovisionamento 

O serviço de aprovisionamento do Azure AD permite-lhe determinar quem vai ser aprovisionado com base na atribuição à aplicação e/ou com base em atributos do utilizador/grupo. Se optar por determinar quem vai ser aprovisionado na sua aplicação com base na atribuição, pode utilizar os seguintes [passos](../manage-apps/assign-user-or-group-access-portal.md) para atribuir utilizadores e grupos à aplicação. Se escolher determinar quem vai ser aprovisionado com base apenas em atributos do utilizador ou grupo, pode utilizar um filtro de âmbito conforme descrito [aqui](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Ao atribuir utilizadores e grupos ao Smartsheet, deve selecionar outra função que não o **Acesso Predefinido**. Os utilizadores com a função Acesso Predefinido são excluídos do aprovisionamento e marcados como não autorizados de forma efetiva nos registos de aprovisionamento. Se a única função disponível na aplicação for a função de acesso predefinido, pode [atualizar o manifesto de aplicação](../develop/howto-add-app-roles-in-azure-ad-apps.md) para adicionar funções adicionais. 

* Para garantir a paridade nas atribuições de funções de utilizador entre smartsheet e Ad Azure, recomenda-se utilizar as mesmas atribuições de funções preenchidas na lista completa de utilizadores da Smartsheet. Para obter esta lista de utilizadores do Smartsheet, navegue para **o Administrador > Gestão de Utilizadores > Mais Ações > Baixar Lista de Utilizadores (cSV)**.

* Para aceder a determinadas funcionalidades na aplicação, o Smartsheet exige que um utilizador tenha várias funções. Para saber mais sobre tipos e permissões de utilizador no Smartsheet, aceda a [Tipos e Permissões do Utilizador](https://help.smartsheet.com/learning-track/shared-users/user-types-and-permissions).

*  Se um utilizador tiver múltiplas funções atribuídas no Smartsheet, **deve** certificar-se de que estas atribuições de funções são replicadas em AD Azure para evitar um cenário em que os utilizadores possam perder o acesso permanente aos objetos da Smartsheet. Cada papel único no Smartsheet **MUST** é atribuído a um grupo diferente em Azure AD. O utilizador **DEVE** então ser adicionado a cada um dos grupos correspondentes às funções desejadas. 

* Comece pequeno. Teste com um pequeno conjunto de utilizadores e grupos antes de implementar para todos. Quando o âmbito do aprovisionamento está definido para os utilizadores e os grupos atribuídos, pode controlar isto ao atribuir um ou dois utilizadores ou grupos à aplicação. Quando o âmbito está definido para todos os utilizadores e grupos, pode especificar um [filtro de âmbito baseado em atributos](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

## <a name="step-5-configure-automatic-user-provisioning-to-smartsheet"></a>Passo 5. Configure o fornecimento automático de utilizadores ao Smartsheet 

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos em Smartsheet com base em atribuições de utilizador e/ou grupo em Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-smartsheet-in-azure-ad"></a>Para configurar o fornecimento automático do utilizador para o Smartsheet em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais** e, em seguida, **Todas as aplicações**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Smartsheet**.

    ![O link Smartsheet na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Aprovisionamento**.

    ![Screenshot das opções De gestão com a opção Provisioning chamada.](common/provisioning.png)

4. Defina o **Modo de Aprovisionamento** como **Automático**.

    ![Screenshot da lista de retirada do modo de provisionamento com a opção Automática chamada.](common/provisioning-automatic.png)

5. Sob a secção **Credenciais de Administração,** insira os valores **DE URL base SCIM 2.0 e Access Token** recuperados anteriormente da Smartsheet em **USSA** e **Secret Token,** respectivamente. Clique em **Testar a Ligação** para garantir que o Azure AD pode ligar-se ao Smartsheet. Se a ligação falhar, certifique-se de que a sua conta Smartsheet tem permissões SysAdmin e tente novamente.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **'Email' de Notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e verifique a caixa de verificação - **Envie uma notificação de e-mail quando ocorrer uma falha**.

    ![E-mail de Notificação](common/provisioning-notification-email.png)

7. Clique em **Guardar**.

8. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users para smartsheet**.

9. Reveja os atributos do utilizador que são sincronizados de AD AD a Smartsheet na secção **De Mapeamento de Atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas do utilizador no Smartsheet para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

   |Atributo|Tipo|
   |---|---|
   |active|Booleano|
   |título|String|
   |userName|String|
   |name.givenName|String|
   |name.familyName|String|
   |phoneNumbers[type eq "work"].value|String|
   |phoneNumbers[type eq "mobile"].value|String|
   |números de telefone[tipo eq "fax"].valor|String|
   |externalId|String|
   |papéis[eq primário "True"].display|String|
   |papéis[eq primário "Verdadeiro"].tipo|String|
   |roles[primary eq "True"].value|String|
   |funções|String|
   urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|String|


10. Para configurar filtros de âmbito, veja as instruções seguintes disponibilizadas no [Tutorial de filtro de âmbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para ativar o serviço de fornecimento de Ad Azure para smartsheet, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

    ![Estado do Aprovisionamento Ativado](common/provisioning-toggle-on.png)

12. Defina os utilizadores e/ou grupos que deseja prestar ao Smartsheet, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de Aprovisionamento](common/provisioning-scope.png)

13. Quando estiver pronto para aprovisionar, clique em **Guardar**.

    ![Guardar Configuração de Aprovisionamento](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de fornecimento AZure AD esteja em execução. 

## <a name="step-6-monitor-your-deployment"></a>Passo 6. Monitorizar a implementação
Depois de configurar o aprovisionamento, utilize os seguintes recursos para monitorizar a sua implementação:

1. Utilize os [registos de aprovisionamento](../reports-monitoring/concept-provisioning-logs.md) para determinar quais os utilizadores que foram aprovisionados com ou sem êxito
2. Verifique a [barra de progresso](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) para ver o estado do ciclo de aprovisionamento e quão próximo está da conclusão
3. Se a configuração de aprovisionamento parecer estar num mau estado de funcionamento, a aplicação vai entrar em quarentena. Saiba mais sobre os estados de quarentena [aqui](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="connector-limitations"></a>Limitações do conector

* O Smartsheet não suporta eliminações suaves. Quando o atributo **ativo** de um utilizador é definido como Falso, o Smartsheet elimina o utilizador permanentemente.

## <a name="change-log"></a>Change log

* 06/16/2020 - Apoio adicional aos atributos de extensão da empresa "Cost Center", "Division", "Manager" e "Department" para os utilizadores.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento de contas de utilizador para Aplicações Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como analisar os registos e obter relatórios sobre a atividade de aprovisionamento](../app-provisioning/check-status-user-account-provisioning.md)