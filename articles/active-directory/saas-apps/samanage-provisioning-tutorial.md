---
title: 'Tutorial: Configure SolarWinds Service Desk (anteriormente Samanage) para fornecimento automático de utilizadores com Diretório Ativo Azure / Microsoft Docs'
description: Saiba como providenciar e desaprovisionar automaticamente as contas de utilizadores do Azure AD ao SolarWinds Service Desk (anteriormente Samanage).
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/13/2020
ms.author: Zhchia
ms.openlocfilehash: 5cdc36c20cbba148bb68bda700f5fdccbc593caf
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2020
ms.locfileid: "94353004"
---
# <a name="tutorial-configure-solarwinds-service-desk-previously-samanage-for-automatic-user-provisioning"></a>Tutorial: Configure o Balcão de Serviço SolarWinds (anteriormente Samanage) para o fornecimento automático de utilizadores

Este tutorial descreve os passos que precisa de executar tanto no SolarWinds Service Desk (anteriormente Samanage) como no Azure Ative Directory (Azure AD) para configurar o fornecimento automático de utilizadores. Quando configurado, a Azure AD fornece automaticamente e desprovisiona utilizadores e grupos para [o SolarWinds Service Desk](https://www.samanage.com/pricing/) utilizando o serviço de provisionamento Azure AD. Para obter detalhes importantes sobre o que este serviço faz, como funciona e perguntas frequentes, veja [Automatizar o aprovisionamento e desaprovisionamento de utilizadores em aplicações SaaS no Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="migrate-to-the-new-solarwinds-service-desk-application"></a>Migrar para a nova aplicação do Balcão de Serviço SolarWinds

Se tiver uma integração existente com o SolarWinds Service Desk, consulte a seguinte secção sobre as próximas alterações. Se estiver a configurar o SolarWinds Service Desk pela primeira vez, pode saltar esta secção e mudar-se para **capacidades suportadas**.

#### <a name="whats-changing"></a>O que está a mudar?

* Alterações do lado Azure AD: O método de autorização para a prestação de utilizadores em Samange tem sido historicamente **auth básico**. Em breve verá o método de autorização alterado para o símbolo **secreto de Long lived.**


#### <a name="what-do-i-need-to-do-to-migrate-my-existing-custom-integration-to-the-new-application"></a>O que preciso de fazer para migrar a minha integração personalizada existente para a nova aplicação?

Se tiver uma integração existente do Balcão de Serviço SolarWinds com credenciais de administração válidas, **não é necessária qualquer ação**. Migramos automaticamente os clientes para a nova aplicação. Este processo é feito completamente nos bastidores. Se as credenciais existentes expirarem, ou se precisar de autorizar o acesso à aplicação novamente, precisa de gerar um sinal secreto de longa duração. Para gerar um novo símbolo, consulte o passo 2 deste artigo.


#### <a name="how-can-i-tell-if-my-application-has-been-migrated"></a>Como posso saber se o meu pedido foi migrado? 

Quando a sua aplicação é migrada, na secção **Credenciais de Administrador,** os campos **de Nome de Utilizador admin** e Password **Admin** serão substituídos por um único campo **Secreto Token.**

## <a name="capabilities-supported"></a>Capacidades suportadas

> [!div class="checklist"]
> * Criar utilizadores no Balcão de Serviço SolarWinds
> * Remova os utilizadores no Balcão de Serviço SolarWinds quando já não necessitam de acesso
> * Mantenha os atributos do utilizador sincronizados entre o Azure AD e o SolarWinds Service Desk
> * Grupos de fornecimento e membros do grupo no Balcão de Serviço SolarWinds
> * [Único sinal de inscrição no](./samanage-tutorial.md) Balcão de Serviço SolarWinds (recomendado)

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* [Um inquilino da AD AZure](../develop/quickstart-create-new-tenant.md) 
* Uma conta de utilizador em Azure AD com [permissão](../users-groups-roles/directory-assign-admin-roles.md) para configurar o provisionamento (por exemplo, Administrador de Aplicação, Administrador de Aplicação cloud, Proprietário de Aplicações ou Administrador Global). 
* Um [inquilino do Balcão de Serviço SolarWinds](https://www.samanage.com/pricing/) com o pacote Profissional.
* Uma conta de utilizador no SolarWinds Service Desk com permissões de administração.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passo 1. Planear a sua implementação de aprovisionamento
1. Saiba [como funciona o serviço de aprovisionamento](../app-provisioning/user-provisioning.md).
2. Determine quem vai estar no [âmbito do aprovisionamento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determine quais os dados a [mapear entre o Azure AD e o SolarWinds Service Desk](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-solarwinds-service-desk-to-support-provisioning-with-azure-ad"></a>Passo 2. Configure o Balcão de Serviço SolarWinds para apoiar o fornecimento com Azure AD

Para gerar um símbolo secreto para a autenticação, consulte [a autenticação de tokens tutoriais para integração da API.](https://help.samanage.com/s/article/Tutorial-Tokens-Authentication-for-API-Integration-1536721557657)

## <a name="step-3-add-solarwinds-service-desk-from-the-azure-ad-application-gallery"></a>Passo 3. Adicionar Balcão de Serviço SolarWinds da galeria de aplicações AZure AD

Adicione o Balcão de Serviço SolarWinds da galeria de aplicações AZure AD para começar a gerir o fornecimento ao SolarWinds Service Desk. Se já configurar o SolarWinds Service Desk para SSO, pode utilizar a mesma aplicação. No entanto, é recomendável criar uma aplicação separada ao testar a integração inicialmente. Saiba mais sobre como adicionar uma aplicação a partir da galeria [aqui](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passo 4: Determinar quem vai estar no âmbito do aprovisionamento 

O serviço de aprovisionamento do Azure AD permite-lhe determinar quem vai ser aprovisionado com base na atribuição à aplicação e/ou com base em atributos do utilizador/grupo. Se optar por determinar quem vai ser aprovisionado na sua aplicação com base na atribuição, pode utilizar os seguintes [passos](../manage-apps/assign-user-or-group-access-portal.md) para atribuir utilizadores e grupos à aplicação. Se escolher determinar quem vai ser aprovisionado com base apenas em atributos do utilizador ou grupo, pode utilizar um filtro de âmbito conforme descrito [aqui](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Ao atribuir utilizadores e grupos ao Balcão de Serviço SolarWinds, tem de selecionar outra função que não o **Acesso Predefinido**. Os utilizadores com a função Acesso Predefinido são excluídos do aprovisionamento e marcados como não autorizados de forma efetiva nos registos de aprovisionamento. Se a única função disponível na aplicação for a função de acesso predefinido, pode [atualizar o manifesto de aplicação](../develop/howto-add-app-roles-in-azure-ad-apps.md) para adicionar funções adicionais. 

* Comece pequeno. Teste com um pequeno conjunto de utilizadores e grupos antes de implementar para todos. Quando o âmbito do aprovisionamento está definido para os utilizadores e os grupos atribuídos, pode controlar isto ao atribuir um ou dois utilizadores ou grupos à aplicação. Quando o âmbito está definido para todos os utilizadores e grupos, pode especificar um [filtro de âmbito baseado em atributos](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-solarwinds-service-desk"></a>Passo 5. Configure o fornecimento automático de utilizadores ao Balcão de Serviço SolarWinds 

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos no TestApp com base em atribuições de utilizador e/ou grupo em Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-solarwinds-service-desk-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para o SolarWinds Service Desk em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais** e, em seguida, **Todas as aplicações**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **SolarWinds Service Desk**.

3. Selecione o separador **Aprovisionamento**.

    ![Screenshot que mostra o separador Provisioning selecionado.](common/provisioning.png)

4. Defina o **Modo de Aprovisionamento** como **Automático**.

    ![Screenshot que mostra o modo de provisionamento definido para Automático.](common/provisioning-automatic.png)

5. Sob a secção **de Credenciais de Administração,** entrada `https://api.samanage.com` na URL do **inquilino.**  Insira o valor simbólico secreto recuperado anteriormente em **Secret Token**. Selecione **a Ligação de Teste** para garantir que o Azure AD pode ligar-se ao Balcão de Assistência SolarWinds. Se a ligação falhar, certifique-se de que a sua conta de Balcão de Serviço SolarWinds tem permissões de administração e tente novamente.

    ![Screenshot que mostra o botão de ligação de teste selecionado.](./media/samanage-provisioning-tutorial/provisioning.png)

6. No campo **E-mail de Notificação** , introduza o endereço de e-mail de uma pessoa ou um grupo que deve receber as notificações de erro de aprovisionamento e marque a caixa de verificação **Enviar uma notificação de e-mail quando ocorre uma falha**.

    ![E-mail de Notificação](common/provisioning-notification-email.png)

7. Selecione **Guardar**.

8. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users to SolarWinds Service Desk**.

9. Reveja os atributos do utilizador que são sincronizados do AD AD a SolarWinds Service Desk na secção **De mapeamento de Atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas de utilizador no Balcão de Serviço SolarWinds para operações de atualização. Se optar por alterar o [atributo de alvo correspondente,](../app-provisioning/customize-application-attributes.md)terá de garantir que a API do Balcão de Serviços SolarWinds suporta utilizadores filtrantes com base nesse atributo. Selecione o botão **Guardar** para escoar quaisquer alterações.

      ![Mapeamentos do utilizador de Samange](./media/samanage-provisioning-tutorial/user-attributes.png)

10. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Groups to SolarWinds Service Desk**.

11. Reveja os atributos do grupo que são sincronizados do AD AD a SolarWinds Service Desk na secção **De mapeamento de Atributos.** Os atributos selecionados como propriedades **de correspondência** são usados para combinar com os grupos no Balcão de Serviço SolarWinds para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

      ![Mapeamentos do Grupo Samange](./media/samanage-provisioning-tutorial/group-attributes.png)

12. Para configurar filtros de âmbito, veja as instruções seguintes disponibilizadas no [Tutorial de filtro de âmbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para ativar o serviço de prestação de Ad Azure para o Balcão de Serviço SolarWinds, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

    ![Estado do Aprovisionamento Ativado](common/provisioning-toggle-on.png)

14. Defina os utilizadores e/ou grupos que deseja prestar ao Balcão de Serviços SolarWinds, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de Aprovisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para a provisão, **selecione Save**.

    ![Guardar Configuração de Aprovisionamento](common/provisioning-configuration-save.png)

Esta operação inicia o ciclo de sincronização inicial de todos os utilizadores e grupos definidos no **Âmbito** na secção **Definições**. O ciclo inicial leva mais tempo a ser executado do que os ciclos subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de aprovisionamento do Azure AD esteja em execução. 

## <a name="step-6-monitor-your-deployment"></a>Passo 6. Monitorizar a implementação
Depois de configurar o aprovisionamento, utilize os seguintes recursos para monitorizar a sua implementação:

1. Utilize os [registos de aprovisionamento](../reports-monitoring/concept-provisioning-logs.md) para determinar quais os utilizadores que foram aprovisionados com ou sem êxito
2. Verifique a [barra de progresso](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) para ver o estado do ciclo de aprovisionamento e quão próximo está da conclusão
3. Se a configuração de aprovisionamento parecer estar num mau estado de funcionamento, a aplicação vai entrar em quarentena. Saiba mais sobre os estados de quarentena [aqui](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="connector-limitations"></a>Limitações do conector

Se selecionar a opção **Sync de todos os utilizadores e grupos** e configurar um valor para as **funções** do Balcão de Serviço SolarWinds, o valor sob o **valor Predefinido se a caixa de nulo (é opcional)** tiver de ser expressa no seguinte formato:

- {"displayName":"role"}, onde a função é o valor padrão que deseja.

## <a name="change-log"></a>Change log

* 09/14/2020 - Alterou o nome da empresa em dois tutoriais SaaS de Samanage para SolarWinds Service Desk (anteriormente Samanage) por https://github.com/ravitmorales .
* 04/22/2020 - Método de autorização atualizado do auth básico ao símbolo secreto de longa duração.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento de contas de utilizador para Aplicações Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como analisar os registos e obter relatórios sobre a atividade de aprovisionamento](../app-provisioning/check-status-user-account-provisioning.md)