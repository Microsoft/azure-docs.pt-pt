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
ms.topic: article
ms.date: 01/13/2020
ms.author: Zhchia
ms.openlocfilehash: 0941c95ee6215a710ebb1bbc2fba9fae09ccf16d
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/16/2020
ms.locfileid: "90707271"
---
# <a name="tutorial-configure-solarwinds-service-desk-previously-samanage-for-automatic-user-provisioning"></a>Tutorial: Configure o Balcão de Serviço SolarWinds (anteriormente Samanage) para o fornecimento automático de utilizadores

Este tutorial descreve os passos que precisa de executar tanto no SolarWinds Service Desk (anteriormente Samanage) como no Azure Ative Directory (Azure AD) para configurar o fornecimento automático de utilizadores. Quando configurado, a Azure AD fornece automaticamente e desprovisiona utilizadores e grupos para [o SolarWinds Service Desk](https://www.samanage.com/pricing/) utilizando o serviço de provisionamento Azure AD. Para obter detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte [automatizar o fornecimento e desprovisionamento de aplicações saaS com diretório Azure Ative.](../manage-apps/user-provisioning.md)

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
> * [Único sinal de inscrição no](https://docs.microsoft.com/azure/active-directory/saas-apps/samanage-tutorial) Balcão de Serviço SolarWinds (recomendado)

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* [Um inquilino da AD AZure](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Uma conta de utilizador em Azure AD com [permissão](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) para configurar o provisionamento (por exemplo, Administrador de Aplicação, Administrador de Aplicação cloud, Proprietário de Aplicações ou Administrador Global). 
* Um [inquilino do Balcão de Serviço SolarWinds](https://www.samanage.com/pricing/) com o pacote Profissional.
* Uma conta de utilizador no SolarWinds Service Desk com permissões de administração.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passo 1. Planeie a sua implantação de provisionamento
1. Saiba [como funciona o serviço de prestação de serviços.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)
2. Determinar quem estará no [âmbito do provisionamento](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Determine quais os dados a [mapear entre o Azure AD e o SolarWinds Service Desk](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-solarwinds-service-desk-to-support-provisioning-with-azure-ad"></a>Passo 2. Configure o Balcão de Serviço SolarWinds para apoiar o fornecimento com Azure AD

Para gerar um símbolo secreto para a autenticação, consulte [a autenticação de tokens tutoriais para integração da API.](https://help.samanage.com/s/article/Tutorial-Tokens-Authentication-for-API-Integration-1536721557657)

## <a name="step-3-add-solarwinds-service-desk-from-the-azure-ad-application-gallery"></a>Passo 3. Adicionar Balcão de Serviço SolarWinds da galeria de aplicações AZure AD

Adicione o Balcão de Serviço SolarWinds da galeria de aplicações AZure AD para começar a gerir o fornecimento ao SolarWinds Service Desk. Se já configurar o SolarWinds Service Desk para SSO, pode utilizar a mesma aplicação. No entanto, recomenda-se que crie uma aplicação separada ao testar inicialmente a integração. Saiba mais sobre a adição de uma aplicação na galeria [aqui.](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passo 4: Definir quem estará no âmbito do provisionamento 

O serviço de prestação de Ad Azure permite-lhe atear âmbito a quem será a provisionado com base na atribuição à aplicação e ou com base em atributos do utilizador/grupo. Se optar por escolher o âmbito de aplicação de quem será aprovisionado na sua aplicação com base na atribuição, pode utilizar os [seguintes passos](../manage-apps/assign-user-or-group-access-portal.md) para atribuir utilizadores e grupos à aplicação. Se optar por escolher o âmbito de aplicação de quem será a provisionado apenas com base em atributos do utilizador ou grupo, pode utilizar um filtro de deteção como descrito [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Ao atribuir utilizadores e grupos ao Balcão de Serviço SolarWinds, tem de selecionar outra função que não o **Acesso Predefinido**. Os utilizadores com a função De Acesso Predefinido estão excluídos do provisionamento e serão marcados como não efetivamente intitulados nos registos de provisionamento. Se a única função disponível na aplicação for a função de acesso predefinido, pode [atualizar o manifesto de aplicação](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) para adicionar funções adicionais. 

* Comece minúsculo. Teste com um pequeno conjunto de utilizadores e grupos antes de rolar para todos. Quando o âmbito de provisão é definido para utilizadores e grupos atribuídos, pode controlá-lo atribuindo um ou dois utilizadores ou grupos à aplicação. Quando o âmbito é definido para todos os utilizadores e grupos, pode especificar um [filtro de deteção baseado no atributo](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-solarwinds-service-desk"></a>Passo 5. Configure o fornecimento automático de utilizadores ao Balcão de Serviço SolarWinds 

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos no TestApp com base em atribuições de utilizador e/ou grupo em Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-solarwinds-service-desk-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para o SolarWinds Service Desk em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **SolarWinds Service Desk**.

3. Selecione o **separador Provisioning.**

    ![Screenshot que mostra o separador Provisioning selecionado.](common/provisioning.png)

4. Desa ajuste o **modo de provisionamento** para **automático**.

    ![Screenshot que mostra o modo de provisionamento definido para Automático.](common/provisioning-automatic.png)

5. Sob a secção **de Credenciais de Administração,** entrada `https://api.samanage.com` na URL do **inquilino.**  Insira o valor simbólico secreto recuperado anteriormente em **Secret Token**. Selecione **a Ligação de Teste** para garantir que o Azure AD pode ligar-se ao Balcão de Assistência SolarWinds. Se a ligação falhar, certifique-se de que a sua conta de Balcão de Serviço SolarWinds tem permissões de administração e tente novamente.

    ![Screenshot que mostra o botão de ligação de teste selecionado.](./media/samanage-provisioning-tutorial/provisioning.png)

6. No campo **'Email' de Notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e selecione a **notificação enviar uma notificação por e-mail quando ocorrer uma falha** na caixa de verificação.

    ![E-mail de notificação](common/provisioning-notification-email.png)

7. Selecione **Guardar**.

8. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users to SolarWinds Service Desk**.

9. Reveja os atributos do utilizador que são sincronizados do AD AD a SolarWinds Service Desk na secção **De mapeamento de Atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas de utilizador no Balcão de Serviço SolarWinds para operações de atualização. Se optar por alterar o [atributo de alvo correspondente,](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)terá de garantir que a API do Balcão de Serviços SolarWinds suporta utilizadores filtrantes com base nesse atributo. Selecione o botão **Guardar** para escoar quaisquer alterações.

      ![Mapeamentos do utilizador de Samange](./media/samanage-provisioning-tutorial/user-attributes.png)

10. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Groups to SolarWinds Service Desk**.

11. Reveja os atributos do grupo que são sincronizados do AD AD a SolarWinds Service Desk na secção **De mapeamento de Atributos.** Os atributos selecionados como propriedades **de correspondência** são usados para combinar com os grupos no Balcão de Serviço SolarWinds para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

      ![Mapeamentos do Grupo Samange](./media/samanage-provisioning-tutorial/group-attributes.png)

12. Para configurar filtros de deteção, consulte as seguintes instruções fornecidas no tutorial do [filtro de escotagem](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para ativar o serviço de prestação de Ad Azure para o Balcão de Serviço SolarWinds, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

    ![Estatuto de Provisionamento Toggled On](common/provisioning-toggle-on.png)

14. Defina os utilizadores e/ou grupos que deseja prestar ao Balcão de Serviços SolarWinds, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para a provisão, **selecione Save**.

    ![Configuração de provisionamento de poupança](common/provisioning-configuration-save.png)

Esta operação inicia o ciclo inicial de sincronização de todos os utilizadores e grupos definidos no **Âmbito** na secção **Definições.** O ciclo inicial demora mais tempo a ser efetuado do que os ciclos subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de fornecimento AZure AD esteja em funcionamento. 

## <a name="step-6-monitor-your-deployment"></a>Passo 6. Monitorizar a implementação
Depois de configurar o provisionamento, utilize os seguintes recursos para monitorizar a sua implantação:

1. Utilize os [registos de provisionamento](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) para determinar quais os utilizadores que foram a provisionados com sucesso ou sem sucesso
2. Verifique a [barra de progresso](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) para ver o estado do ciclo de provisionamento e quão perto está da sua conclusão
3. Se a configuração do provisionamento parecer estar num estado pouco saudável, a aplicação entrará em quarentena. Saiba mais sobre estados de quarentena [aqui.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)

## <a name="connector-limitations"></a>Limitações do conector

Se selecionar a opção **Sync de todos os utilizadores e grupos** e configurar um valor para as **funções** do Balcão de Serviço SolarWinds, o valor sob o **valor Predefinido se a caixa de nulo (é opcional)** tiver de ser expressa no seguinte formato:

- {"displayName":"role"}, onde a função é o valor padrão que deseja.

## <a name="change-log"></a>Change log

* 09/14/2020 - Alterou o nome da empresa em dois tutoriais SaaS de Samanage para SolarWinds Service Desk (anteriormente Samanage) por https://github.com/ravitmorales .
* 04/22/2020 - Método de autorização atualizado do auth básico ao símbolo secreto de longa duração.

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do fornecimento de conta de utilizador para apps empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)
