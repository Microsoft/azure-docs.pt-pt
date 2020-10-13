---
title: 'Tutorial: Configure Nova Relíquia por Organização para o fornecimento automático de utilizadores com diretório ativo Azure / Microsoft Docs'
description: Saiba como provisão e desavisagem automática de contas de utilizadores de Azure AD a New Relic by Organization.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 04/14/2020
ms.author: Zhchia
ms.openlocfilehash: c8934e1344ff2d7df97c3854f4e025f19fb37f1e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91361624"
---
# <a name="tutorial-configure-new-relic-by-organization-for-automatic-user-provisioning"></a>Tutorial: Configure nova relíquia por organização para o fornecimento automático de utilizadores

Este tutorial descreve os passos necessários para realizar tanto em New Relic by Organization como no Azure Ative Directory (Azure AD) para configurar o fornecimento automático de utilizadores. Quando configurado, a Azure AD fornece automaticamente e desnegri os utilizadores e grupos para [Nova Relíquia por Organização](https://newrelic.com/) utilizando o serviço de provisionamento Azure AD. Para obter detalhes importantes sobre o que este serviço faz, como funciona e perguntas frequentes, veja [Automatizar o aprovisionamento e desaprovisionamento de utilizadores em aplicações SaaS no Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Capacidades Suportadas
> [!div class="checklist"]
> * Criar utilizadores em Nova Relíquia por Organização
> * Remova os utilizadores em Nova Relíquia por Organização quando já não necessitam de acesso
> * Mantenha os atributos do utilizador sincronizados entre Azure AD e New Relic by Organization
> * Grupos de provisão e membros do grupo em Nova Relíquia por Organização
> * [Único sinal de](https://docs.microsoft.com/azure/active-directory/saas-apps/new-relic-limited-release-tutorial) nova relíquia por organização (recomendado)

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* [Um inquilino da AD AZure](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Uma conta de utilizador no Azure AD com [permissão](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) para configurar o aprovisionamento (por ex., Administrador de Aplicações, Administrador de Aplicações de Cloud, Proprietário da Aplicação ou Administrador Global). 
* Uma ou mais contas na New Relic by Organization a que gostaria que os seus utilizadores tivessem acesso. 

## <a name="step-1-plan-your-provisioning-deployment"></a>Passo 1. Planear a sua implementação de aprovisionamento
1. Saiba [como funciona o serviço de aprovisionamento](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Determine quem vai estar no [âmbito do aprovisionamento](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Determine quais os dados a [mapear entre Azure AD e New Relic by Organization](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-new-relic-by-organization-to-support-provisioning-with-azure-ad"></a>Passo 2. Configure nova relíquia por organização para apoiar o provisionamento com a Azure AD

Trabalhe com o representante da sua conta ou obtenha apoio na support.newrelic.com para configurar o SCIM e o SSO para a sua organização. Terá de fornecer o seu representante da conta com:

- O nome da sua organização
- Lista de IDs de conta Nova Relíquia para associar à organização

Com esta informação, o representante da sua conta cria um registo de organização para si no nosso novo sistema e associa as suas contas à organização.

O seu representante de conta fornece-lhe as seguintes informações que necessitará para configurar o novo pedido de SCIM/SSO da Nova Relíquia para o seu fornecedor de identidade:

- Ponto final SCIM (URL do inquilino)
- Símbolo do portador do SCIM (Token Secreto)

O símbolo do portador do SCIM permite o fornecimento dos seus utilizadores na New Relic, por isso, por favor, mantenha o valor seguro. O seu representante da conta transferirá o símbolo do portador do SCIM para si de forma segura.

## <a name="step-3-add-new-relic-by-organization-from-the-azure-ad-application-gallery"></a>Passo 3. Adicione nova relíquia por organização da galeria de aplicações Azure AD

Adicione nova relíquia por organização da galeria de aplicações Azure AD para começar a gerir o fornecimento de Nova Relíquia por Organização. Se já configurar New Relic by Organization for SSO, pode utilizar a mesma aplicação. No entanto, é recomendável criar uma aplicação separada ao testar a integração inicialmente. Saiba mais sobre como adicionar uma aplicação a partir da galeria [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passo 4: Determinar quem vai estar no âmbito do aprovisionamento 

O serviço de aprovisionamento do Azure AD permite-lhe determinar quem vai ser aprovisionado com base na atribuição à aplicação e/ou com base em atributos do utilizador/grupo. Se optar por determinar quem vai ser aprovisionado na sua aplicação com base na atribuição, pode utilizar os seguintes [passos](../manage-apps/assign-user-or-group-access-portal.md) para atribuir utilizadores e grupos à aplicação. Se escolher determinar quem vai ser aprovisionado com base apenas em atributos do utilizador ou grupo, pode utilizar um filtro de âmbito conforme descrito [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Ao atribuir utilizadores e grupos a Nova Relíquia por Organização, tem de selecionar outra função que não o **Acesso Predefinido**. Os utilizadores com a função Acesso Predefinido são excluídos do aprovisionamento e marcados como não autorizados de forma efetiva nos registos de aprovisionamento. Se a única função disponível na aplicação for a função de acesso predefinido, pode [atualizar o manifesto de aplicação](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) para adicionar funções adicionais. 

* Comece pequeno. Teste com um pequeno conjunto de utilizadores e grupos antes de implementar para todos. Quando o âmbito do aprovisionamento está definido para os utilizadores e os grupos atribuídos, pode controlar isto ao atribuir um ou dois utilizadores ou grupos à aplicação. Quando o âmbito está definido para todos os utilizadores e grupos, pode especificar um [filtro de âmbito baseado em atributos](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-new-relic-by-organization"></a>Passo 5. Configure o fornecimento automático de utilizadores a Nova Relíquia por Organização 

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos no TestApp com base em atribuições de utilizador e/ou grupo em Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-new-relic-by-organization-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para Nova Relíquia por Organização em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais** e, em seguida, **Todas as aplicações**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Nova Relíquia por Organização.**

    ![O novo link relíquia na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Aprovisionamento**.

    ![Screenshot das opções De gestão com a opção Provisioning chamada.](common/provisioning.png)

4. Defina o **Modo de Aprovisionamento** como **Automático**.

    ![Screenshot da lista de retirada do modo de provisionamento com a opção Automática chamada.](common/provisioning-automatic.png)

5. Sob a secção **de Credenciais de Administração,** entrada `https://scim-provisioning.service.newrelic.com/scim/v2` na URL do inquilino. Insira o valor simbólico de autenticação SCIM recuperado anteriormente em **Secret Token**. Clique em **Testar a Ligação** para garantir que o Azure AD pode ligar-se a Nova Relíquia. Se a ligação falhar, certifique-se de que a sua conta New Relic tem permissões de administração e tente novamente.

    ![A screenshot mostra a caixa de diálogo de Admin Credentials, onde pode inserir o seu Inquilino U R L e Secret Token.](./media/new-relic-by-organization-provisioning-tutorial/provisioning.png)

6. No campo **E-mail de Notificação**, introduza o endereço de e-mail de uma pessoa ou um grupo que deve receber as notificações de erro de aprovisionamento e marque a caixa de verificação **Enviar uma notificação de e-mail quando ocorre uma falha**.

    ![E-mail de Notificação](common/provisioning-notification-email.png)

7. Selecione **Guardar**.

8. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users to New Relic by Organization**.

9. Reveja os atributos do utilizador que são sincronizados de Azure AD a Nova Relíquia por Organização na secção **De Mapeamento de Atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas de utilizador em New Relic by Organization para operações de atualização. Se optar por alterar o [atributo de alvo correspondente,](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)terá de garantir que a Nova Relíquia da Organização API suporta utilizadores filtrantes com base nesse atributo. Selecione o botão **Guardar** para escoar quaisquer alterações.

   |Atributo|Tipo|
   |---|---|
   |userName|String|
   |externalId|String|
   |active|Booleano|
   |emails[type eq "work"].value|String|
   |name.givenName|String|
   |nome.formatado|String|
   |timezone|String|

10. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Groups to New Relic by Organization**.

11. Reveja os atributos do grupo que são sincronizados de Azure AD a New Relic by Organization na secção **De mapeamento de atributos.** Os atributos selecionados como propriedades **de correspondência** são usados para combinar os grupos em New Relic by Organization para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

      |Atributo|Tipo|
      |---|---|
      |displayName|String|
      |externalId|String|
      |membros|Referência|

12. Para configurar filtros de âmbito, veja as instruções seguintes disponibilizadas no [Tutorial de filtro de âmbito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para ativar o serviço de fornecimento de Ad Azure para Nova Relíquia por Organização, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

    ![Estado do Aprovisionamento Ativado](common/provisioning-toggle-on.png)

14. Defina os utilizadores e/ou grupos que deseja prestar à Nova Relíquia por Organização, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de Aprovisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para aprovisionar, clique em **Guardar**.

    ![Guardar Configuração de Aprovisionamento](common/provisioning-configuration-save.png)

Esta operação inicia o ciclo de sincronização inicial de todos os utilizadores e grupos definidos no **Âmbito** na secção **Definições**. O ciclo inicial leva mais tempo a ser executado do que os ciclos subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de aprovisionamento do Azure AD esteja em execução. 

## <a name="step-6-monitor-your-deployment"></a>Passo 6. Monitorizar a implementação
Depois de configurar o aprovisionamento, utilize os seguintes recursos para monitorizar a sua implementação:

* Utilize os [registos de aprovisionamento](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) para determinar quais os utilizadores que foram aprovisionados com ou sem êxito
* Verifique a [barra de progresso](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) para ver o estado do ciclo de aprovisionamento e quão próximo está da conclusão
* Se a configuração de aprovisionamento parecer estar num mau estado de funcionamento, a aplicação vai entrar em quarentena. Saiba mais sobre os estados de quarentena [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  


## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento de contas de utilizador para Aplicações Empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como analisar os registos e obter relatórios sobre a atividade de aprovisionamento](../manage-apps/check-status-user-account-provisioning.md)
