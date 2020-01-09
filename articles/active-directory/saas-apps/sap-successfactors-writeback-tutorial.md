---
title: 'Tutorial: configurar o Write-back SuccessFactors no Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Write-back de atributo para SuccessFactors do Azure AD
services: active-directory
author: cmmdesai
documentationcenter: na
manager: jodadzie
ms.assetid: ad255bd4-9e50-43a1-a92b-359215868b6b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2019
ms.author: chmutali
ms.openlocfilehash: 84ab5da993541012fd2199a30d03f5c69e88bf2c
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/28/2019
ms.locfileid: "75530039"
---
# <a name="tutorial-configure-attribute-writeback-from-azure-ad-to-sap-successfactors-preview"></a>Tutorial: configurar o Write-back de atributo do Azure AD para o SAP SuccessFactors (versão prévia)
O objetivo deste tutorial é mostrar as etapas que você precisa executar para os atributos de write-back do Azure AD para o SuccessFactors Employee central. O único atributo com suporte no momento para Write-back é o atributo de email. 

## <a name="overview"></a>Visão geral

Depois de configurar a integração de provisionamento de entrada usando o [SuccessFactors para o aplicativo de provisionamento do AD local](sap-successfactors-inbound-provisioning-tutorial.md) ou o SuccessFactors para o aplicativo de provisionamento [do Azure ad](sap-successfactors-inbound-provisioning-cloud-only-tutorial.md) , você pode, opcionalmente, configurar o aplicativo SuccessFactors write-back para gravar o endereço de email de volta no SuccessFactors. 

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>A qual esta solução de provisionamento de usuário mais adequada?

Essa solução de provisionamento de usuário SuccessFactors write-back é ideal para:

* Organizações que usam o Office 365 que desejam fazer write-back de atributos autoritativos gerenciados por ele (como endereço de email) de volta para SuccessFactors

## <a name="configuring-successfactors-for-the-integration"></a>Configurando o SuccessFactors para a integração

Um requisito comum de todos os conectores de provisionamento do SuccessFactors é que eles exigem credenciais de uma conta SuccessFactors com as permissões corretas para invocar as APIs OData do SuccessFactors. Esta seção descreve as etapas para criar a conta de serviço no SuccessFactors e conceder as permissões apropriadas. 

* [Criar/identificar conta de usuário de API no SuccessFactors](#createidentify-api-user-account-in-successfactors)
* [Criar uma função de permissões de API](#create-an-api-permissions-role)
* [Criar um grupo de permissões para o usuário da API](#create-a-permission-group-for-the-api-user)
* [Conceder a função de permissão ao grupo de permissões](#grant-permission-role-to-the-permission-group)

### <a name="createidentify-api-user-account-in-successfactors"></a>Criar/identificar conta de usuário de API no SuccessFactors
Trabalhe com sua equipe de administração do SuccessFactors ou com o parceiro de implementação para criar ou identificar uma conta de usuário no SuccessFactors que será usada para invocar as APIs OData. As credenciais de nome de usuário e senha desta conta serão necessárias ao configurar os aplicativos de provisionamento no Azure AD. 

### <a name="create-an-api-permissions-role"></a>Criar uma função de permissões de API

* Faça logon no SAP SuccessFactors com uma conta de usuário que tenha acesso ao centro de administração.
* Procure *gerenciar funções de permissão*e, em seguida, selecione **gerenciar funções de permissão** nos resultados da pesquisa.
  ![gerenciar funções de permissão](./media/sap-successfactors-inbound-provisioning/manage-permission-roles.png)
* Na lista função de permissão, clique em **criar novo**.
  > [!div class="mx-imgBorder"]
  > ![criar uma nova função de permissão](./media/sap-successfactors-inbound-provisioning/create-new-permission-role-1.png)
* Adicione um **nome de função** e uma **Descrição** para a nova função de permissão. O nome e a descrição devem indicar que a função é para permissões de uso da API.
  > [!div class="mx-imgBorder"]
  > detalhes da função de permissão ![](./media/sap-successfactors-inbound-provisioning/permission-role-detail.png)
* Em configurações de permissão, clique em **permissão...** , em seguida, role para baixo na lista de permissões e clique em **gerenciar ferramentas de integração**. Marque a caixa **permitir que o administrador acesse a API OData por meio da autenticação básica**.
  > [!div class="mx-imgBorder"]
  > ![gerenciar ferramentas de integração](./media/sap-successfactors-inbound-provisioning/manage-integration-tools.png)
* Role para baixo na mesma caixa e selecione **API central do funcionário**. Adicione permissões conforme mostrado abaixo para ler usando a API do ODATA e editar usando a API do ODATA. Selecione a opção Editar se você planeja usar a mesma conta para o SuccessFactors cenário de write-back. 
  > [!div class="mx-imgBorder"]
  > ![permissões de gravação de leitura](./media/sap-successfactors-inbound-provisioning/odata-read-write-perm.png)
* Clique em **concluído**. Clique em **Guardar Alterações**.

### <a name="create-a-permission-group-for-the-api-user"></a>Criar um grupo de permissões para o usuário da API

* No centro de administração do SuccessFactors, procure *gerenciar grupos de permissões*e, em seguida, selecione **gerenciar grupos de permissões** nos resultados da pesquisa.
  > [!div class="mx-imgBorder"]
  > ![gerenciar grupos de permissões](./media/sap-successfactors-inbound-provisioning/manage-permission-groups.png)
* Na janela gerenciar grupos de permissões, clique em **criar novo**.
  > [!div class="mx-imgBorder"]
  > ![adicionar novo grupo](./media/sap-successfactors-inbound-provisioning/create-new-group.png)
* Adicione um nome de grupo para o novo grupo. O nome do grupo deve indicar que o grupo é para usuários de API.
  > [!div class="mx-imgBorder"]
  > ![nome do grupo de permissões](./media/sap-successfactors-inbound-provisioning/permission-group-name.png)
* Adicione membros ao grupo. Por exemplo, você pode selecionar **nome de usuário** no menu suspenso pool de pessoas e, em seguida, inserir o nome de usuário da conta de API que será usada para a integração. 
  > [!div class="mx-imgBorder"]
  > ![Adicionar membros do grupo](./media/sap-successfactors-inbound-provisioning/add-group-members.png)
* Clique em **concluído** para concluir a criação do grupo de permissões.

### <a name="grant-permission-role-to-the-permission-group"></a>Conceder a função de permissão ao grupo de permissões

* No centro de administração do SuccessFactors, procure *gerenciar funções de permissão*e, em seguida, selecione **gerenciar funções de permissão** nos resultados da pesquisa.
* Na **lista função de permissão**, selecione a função que você criou para as permissões de uso da API.
* Em **conceder esta função para...** , clique no botão **Adicionar...** .
* Selecione **grupo de permissões...** no menu suspenso e, em seguida, clique em **selecionar...** para abrir a janela grupos para pesquisar e selecionar o grupo criado acima. 
  > [!div class="mx-imgBorder"]
  > ![Adicionar grupo de permissões](./media/sap-successfactors-inbound-provisioning/add-permission-group.png)
* Examine a concessão da função de permissão para o grupo de permissões. 
  > [!div class="mx-imgBorder"]
  > ![a função de permissão e os detalhes do grupo](./media/sap-successfactors-inbound-provisioning/permission-role-group.png)
* Clique em **Guardar Alterações**.

## <a name="configuring-successfactors-writeback"></a>Configurando o Write-back SuccessFactors

Esta seção fornece as etapas para 

* [Adicionar o aplicativo do conector de provisionamento e configurar a conectividade com o SuccessFactors](#part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors)
* [Configurar mapeamentos de atributo](#part-2-configure-attribute-mappings)
* [Habilitar e iniciar o provisionamento de usuário](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors"></a>Parte 1: Adicionar o aplicativo do conector de provisionamento e configurar a conectividade com o SuccessFactors

**Para configurar o Write-back SuccessFactors:**

1. Ir para <https://portal.azure.com>

2. Na barra de navegação à esquerda, selecione **Azure Active Directory**

3. Selecione **aplicativos empresariais**e **todos os aplicativos**.

4. Selecione **Adicionar um aplicativo**e selecione a categoria **tudo** .

5. Pesquise por **write-back de SuccessFactors**e adicione esse aplicativo da galeria.

6. Depois que o aplicativo for adicionado e a tela de detalhes do aplicativo for exibida, selecione **provisionamento**

7. Alterar o **modo** de provisionamento para **automático**

8. Conclua a seção de **credenciais de administrador** da seguinte maneira:

   * **Nome** de usuário do administrador – insira o nome de usu of the SuccessFactors API User Account, com a ID da empresa anexada. Ele tem o formato: **username\@CompanyID**

   * **Senha de administrador –** Insira a senha da conta de usuário da API do SuccessFactors. 

   * **URL do locatário –** Insira o nome do ponto de extremidade dos serviços de API OData do SuccessFactors. Insira apenas o nome do host do servidor sem http ou HTTPS. Esse valor deve ser semelhante a: **API-Server-Name.successfactors.com**.

   * **Email de notificação –** Insira seu endereço de email e marque a caixa de seleção "enviar email se ocorrer falha".
    > [!NOTE]
    > O serviço de provisionamento do Azure AD enviará uma notificação por email se o trabalho de provisionamento entrar em um estado de [quarentena](/azure/active-directory/manage-apps/application-provisioning-quarantine-status) .

   * Clique no botão **testar conexão** . Se o teste de conexão tiver sucesso, clique no botão **salvar** na parte superior. Se falhar, verifique se as credenciais e a URL do SuccessFactors são válidas.
    >[!div class="mx-imgBorder"]
    >![Portal do Azure](./media/sap-successfactors-inbound-provisioning/sfwb-provisioning-creds.png)

   * Depois que as credenciais forem salvas com êxito, a seção **mapeamentos** exibirá o mapeamento padrão **sincronizar Azure Active Directory usuários para SuccessFactors**

### <a name="part-2-configure-attribute-mappings"></a>Parte 2: configurar mapeamentos de atributo

Nesta seção, você configurará como os dados do usuário fluem do SuccessFactors para o Active Directory.

1. Na guia provisionamento em **mapeamentos**, clique em **sincronizar Azure Active Directory usuários para SuccessFactors**.

1. No campo **escopo do objeto de origem** , você pode selecionar quais conjuntos de usuários no Azure ad devem ser considerados para Write-back, definindo um conjunto de filtros baseados em atributo. O escopo padrão é "todos os usuários no Azure AD". 
   > [!TIP]
   > Quando você estiver configurando o aplicativo de provisionamento pela primeira vez, será necessário testar e verificar os mapeamentos e as expressões de atributo para garantir que ele esteja dando o resultado desejado. A Microsoft recomenda usar os filtros de escopo no **escopo do objeto de origem** para testar seus mapeamentos com alguns usuários de teste do Azure AD. Depois de verificar se os mapeamentos funcionam, você pode remover o filtro ou expandi-lo gradualmente para incluir mais usuários.

1. O campo de **ações do objeto de destino** só dá suporte à operação de **atualização** .

1. Na seção **mapeamentos de atributo** , você só pode alterar a ID correspondente usada para vincular um perfil de usuário SuccessFactors com o usuário do Azure AD e qual atributo no Azure ad serve como a fonte de email. 
    >[!div class="mx-imgBorder"]
    >![Portal do Azure](./media/sap-successfactors-inbound-provisioning/sfwb-attribute-mapping.png)

   >[!NOTE]
   >O Write-back SuccessFactors só dá suporte ao atributo email. Não use **Adicionar novo mapeamento** para adicionar novos atributos. 

1. Para salvar seus mapeamentos, clique em **salvar** na parte superior da seção de mapeamento de atributos.

Depois que a configuração de mapeamento de atributo for concluída, agora você poderá [habilitar e iniciar o serviço de provisionamento de usuário](#enable-and-launch-user-provisioning).

## <a name="enable-and-launch-user-provisioning"></a>Habilitar e iniciar o provisionamento de usuário

Depois que as configurações do aplicativo de provisionamento do SuccessFactors tiverem sido concluídas, você poderá ativar o serviço de provisionamento no portal do Azure.

> [!TIP]
> Por padrão, quando você ativa o serviço de provisionamento, ele inicia as operações de provisionamento para todos os usuários no escopo. Se houver erros no mapeamento ou nos problemas de dados do workday, o trabalho de provisionamento poderá falhar e entrar no estado de quarentena. Para evitar isso, como uma prática recomendada, é recomendável configurar o filtro de **escopo do objeto de origem** e testar os mapeamentos de atributo com alguns usuários de teste antes de iniciar a sincronização completa para todos os usuários. Depois de verificar se os mapeamentos funcionam e estão fornecendo os resultados desejados, você pode remover o filtro ou expandi-lo gradualmente para incluir mais usuários.

1. Na guia **provisionamento** , defina o **status de provisionamento** como **ativado**.

2. Clique em **Guardar**.

3. Esta operação iniciará a sincronização inicial, que pode usar um número variável de horas dependendo de quantos usuários estiverem no locatário SuccessFactors. Você pode verificar a barra de progresso para acompanhar o progresso do ciclo de sincronização. 

4. A qualquer momento, verifique a guia **logs de auditoria** no portal do Azure para ver quais ações o serviço de provisionamento executou. Os logs de auditoria listam todos os eventos de sincronização individuais executados pelo serviço de provisionamento, como quais usuários estão sendo lidos do workday e, posteriormente, adicionados ou atualizados para Active Directory. 

5. Depois que a sincronização inicial for concluída, ela gravará um relatório de Resumo de auditoria na guia **provisionamento** , conforme mostrado abaixo.

   > [!div class="mx-imgBorder"]
   > ![barra de progresso de provisionamento](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como examinar os logs e obter relatórios sobre a atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)
* [Saiba como configurar o logon único entre o SuccessFactors e o Azure Active Directory](successfactors-tutorial.md)
* [Saiba como integrar outros aplicativos SaaS com o Azure Active Directory](tutorial-list.md)
* [Saiba como exportar e importar as configurações de provisionamento](../manage-apps/export-import-provisioning-configuration.md)

