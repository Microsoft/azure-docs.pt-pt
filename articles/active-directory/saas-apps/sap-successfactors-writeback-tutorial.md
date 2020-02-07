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
ms.openlocfilehash: 2de0cdd32428884170f549afacdbd52c3a10c93f
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77060053"
---
# <a name="tutorial-configure-attribute-writeback-from-azure-ad-to-sap-successfactors-preview"></a>Tutorial: configurar o Write-back de atributo do Azure AD para o SAP SuccessFactors (versão prévia)
O objetivo deste tutorial é mostrar as etapas que você precisa executar para os atributos de write-back do Azure AD para o SuccessFactors Employee central. O único atributo com suporte no momento para Write-back é o atributo de email. 

## <a name="overview"></a>Descrição geral

Depois de configurar a integração de fornecimento de entrada utilizando a App de fornecimento de [Anúncios No local](sap-successfactors-inbound-provisioning-tutorial.md) ou a aplicação de provisionamento De [AD Para Azure,](sap-successfactors-inbound-provisioning-cloud-only-tutorial.md) pode configurar opcionalmente a app SuccessFactors Writeback para escrever o endereço de e-mail de volta para SuccessFactors. 

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>A qual esta solução de provisionamento de usuário mais adequada?

Essa solução de provisionamento de usuário SuccessFactors write-back é ideal para:

* Organizações que usam o Office 365 que desejam fazer write-back de atributos autoritativos gerenciados por ele (como endereço de email) de volta para SuccessFactors

## <a name="configuring-successfactors-for-the-integration"></a>Configurando o SuccessFactors para a integração

Um requisito comum de todos os conectores de provisionamento do SuccessFactors é que eles exigem credenciais de uma conta SuccessFactors com as permissões corretas para invocar as APIs OData do SuccessFactors. Esta seção descreve as etapas para criar a conta de serviço no SuccessFactors e conceder as permissões apropriadas. 

* [Criar/identificar conta de utilizador da API em SuccessFactors](#createidentify-api-user-account-in-successfactors)
* [Criar um papel de permissões DaPI](#create-an-api-permissions-role)
* [Criar um Grupo de Permissão para o utilizador da API](#create-a-permission-group-for-the-api-user)
* [Papel de permissão de concessão para o Grupo de Permissão](#grant-permission-role-to-the-permission-group)

### <a name="createidentify-api-user-account-in-successfactors"></a>Criar/identificar conta de usuário de API no SuccessFactors
Trabalhe com sua equipe de administração do SuccessFactors ou com o parceiro de implementação para criar ou identificar uma conta de usuário no SuccessFactors que será usada para invocar as APIs OData. As credenciais de nome de usuário e senha desta conta serão necessárias ao configurar os aplicativos de provisionamento no Azure AD. 

### <a name="create-an-api-permissions-role"></a>Criar uma função de permissões de API

* Faça logon no SAP SuccessFactors com uma conta de usuário que tenha acesso ao centro de administração.
* Procure *funções*de permissão de gestão e, em seguida, selecione **'Gerir funções de permissão'** a partir dos resultados da pesquisa.
  ![gerir papéis de permissão](./media/sap-successfactors-inbound-provisioning/manage-permission-roles.png)
* A partir da Lista de Papéis de Permissão, clique em **Criar Novo**.
  > [!div class="mx-imgBorder"]
  > ![criar novos](./media/sap-successfactors-inbound-provisioning/create-new-permission-role-1.png) de permissão
* Adicione um **nome** e **descrição** para o novo papel de permissão. O nome e a descrição devem indicar que a função é para permissões de uso da API.
  > [!div class="mx-imgBorder"]
  > ![permissão detalhe](./media/sap-successfactors-inbound-provisioning/permission-role-detail.png)
* Sob as definições de Permissão, clique em **Permissão...** em seguida, percorra a lista de permissão e clique em **Gerir Ferramentas**de Integração . Verifique a caixa para permitir que o **Administrador aceda à API OData através**da autenticação básica .
  > [!div class="mx-imgBorder"]
  > ![Gerir ferramentas de integração](./media/sap-successfactors-inbound-provisioning/manage-integration-tools.png)
* Desloque-se para baixo na mesma caixa e selecione **API Central do Empregado**. Adicione permissões conforme mostrado abaixo para ler usando a API do ODATA e editar usando a API do ODATA. Selecione a opção Editar se você planeja usar a mesma conta para o SuccessFactors cenário de write-back. 
  > [!div class="mx-imgBorder"]
  > ![Ler permissões](./media/sap-successfactors-inbound-provisioning/odata-read-write-perm.png)
* Clique em **Done**. Clique em **Guardar Alterações**.

### <a name="create-a-permission-group-for-the-api-user"></a>Criar um grupo de permissões para o usuário da API

* No Centro de Administração SuccessFactors, procure grupos de *permissão de gestão*e, em seguida, selecione **Manage Permission Groups** a partir dos resultados da pesquisa.
  > [!div class="mx-imgBorder"]
  > ![Gerir grupos de permissão](./media/sap-successfactors-inbound-provisioning/manage-permission-groups.png)
* A partir da janela 'Gerir grupos de permissão', clique em **Criar Novo**.
  > [!div class="mx-imgBorder"]
  > ![Adicionar novos](./media/sap-successfactors-inbound-provisioning/create-new-group.png) de grupo
* Adicione um nome de grupo para o novo grupo. O nome do grupo deve indicar que o grupo é para usuários de API.
  > [!div class="mx-imgBorder"]
  > ![nome do grupo de permissão](./media/sap-successfactors-inbound-provisioning/permission-group-name.png)
* Adicione membros ao grupo. Por exemplo, pode selecionar o **username** do menu de entrega do People Pool e, em seguida, introduzir o nome de utilizador da conta API que será usada para a integração. 
  > [!div class="mx-imgBorder"]
  > ![Adicionar membros do grupo](./media/sap-successfactors-inbound-provisioning/add-group-members.png)
* Clique em **Feito** para terminar de criar o Grupo de Permissões.

### <a name="grant-permission-role-to-the-permission-group"></a>Conceder a função de permissão ao grupo de permissões

* No SuccessFactors Admin Center, procure funções de permissão de *gestão*e, em seguida, selecione **'Gerir funções de permissão'** a partir dos resultados da pesquisa.
* A partir da Lista de Papéis de **Permissão,** selecione o papel que criou para permissões de utilização da API.
* Sob **o Grant este papel para...** , clique em **Adicionar...** botão.
* Selecione **Grupo de Permissões...** a partir do menu suspenso e, em seguida, clique em **Select...** para abrir a janela Grupos para pesquisar e selecionar o grupo acima criado. 
  > [!div class="mx-imgBorder"]
  > ![Adicionar](./media/sap-successfactors-inbound-provisioning/add-permission-group.png) de grupo de permissão
* Examine a concessão da função de permissão para o grupo de permissões. 
  > [!div class="mx-imgBorder"]
  > ![Papel de Permissão e](./media/sap-successfactors-inbound-provisioning/permission-role-group.png) de pormenor do Grupo
* Clique em **Guardar Alterações**.

## <a name="configuring-successfactors-writeback"></a>Configurando o Write-back SuccessFactors

Esta seção fornece as etapas para 

* [Adicione a aplicação de conector de provisionamento e configure a conectividade aos SuccessFactors](#part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors)
* [Configure mapeamentos de atributos](#part-2-configure-attribute-mappings)
* [Ativar e lançar o fornecimento de utilizadores](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors"></a>Parte 1: Adicionar o aplicativo do conector de provisionamento e configurar a conectividade com o SuccessFactors

**Para configurar successFactors Writeback:**

1. Ir para <https://portal.azure.com>

2. Na barra de navegação esquerda, **selecione Azure Ative Diretório**

3. Selecione **Aplicações Empresariais,** em **seguida, todas as aplicações**.

4. Selecione **Adicionar uma aplicação**e selecione a categoria **All.**

5. Procure **successFactors Writeback**e adicione essa aplicação na galeria.

6. Depois da aplicação ser adicionada e o ecrã de detalhes da aplicação ser mostrado, selecione **Provisioning**

7. Alterar o **modo** **de provisionamento** para **automático**

8. Complete a secção **de Credenciais de Administrador** da seguinte forma:

   * Nome de **utilizador da Administração** – Introduza o nome de utilizador da conta de utilizador da API SuccessFactors, com o ID da empresa anexado. Tem o formato: **username\@companyID**

   * **Senha de administração –** Introduza a palavra-passe da conta de utilizador da API SuccessFactors. 

   * **URL do inquilino –** Insira o nome dos serviços SuccessFactors OData API endpoint. Insira apenas o nome do host do servidor sem http ou HTTPS. Este valor deve parecer: **api-server-name.successfactors.com.**

   * **Email de notificação –** Insira o seu endereço de e-mail e verifique a caixa de verificação "enviar e-mail se ocorrer falha".
    > [!NOTE]
    > O Serviço de Provisionamento de AD Azure envia notificação por e-mail se o trabalho de provisionamento entrar em estado de [quarentena.](/azure/active-directory/manage-apps/application-provisioning-quarantine-status)

   * Clique no botão **de ligação** de teste. Se o teste de ligação for bem sucedido, clique no botão **Guardar** na parte superior. Se falhar, verifique se as credenciais e a URL do SuccessFactors são válidas.
    >[!div class="mx-imgBorder"]
    >![Portal do Azure](./media/sap-successfactors-inbound-provisioning/sfwb-provisioning-creds.png)

   * Uma vez que as credenciais são guardadas com sucesso, a secção **De Mapeamentoapresentará** o mapeamento padrão **Synchronize Azure Ative Directory Users to SuccessFactors**

### <a name="part-2-configure-attribute-mappings"></a>Parte 2: configurar mapeamentos de atributo

Nesta seção, você configurará como os dados do usuário fluem do SuccessFactors para o Active Directory.

1. No separador Provisioning em **Mapeamentos,** clique em **Synchronize Azure Ative Directory Users to SuccessFactors**.

1. No campo **Source Object Scope,** pode selecionar quais os conjuntos de utilizadores em AD Azure devem ser considerados para writeback, definindo um conjunto de filtros baseados em atributos. O escopo padrão é "todos os usuários no Azure AD". 
   > [!TIP]
   > Quando você estiver configurando o aplicativo de provisionamento pela primeira vez, será necessário testar e verificar os mapeamentos e as expressões de atributo para garantir que ele esteja dando o resultado desejado. A Microsoft recomenda a utilização dos filtros de deteção no âmbito do Âmbito do **Objeto Fonte** para testar os seus mapeamentos com alguns utilizadores de teste da AD Azure. Depois de verificar se os mapeamentos funcionam, você pode remover o filtro ou expandi-lo gradualmente para incluir mais usuários.

1. O campo **target object Actions** apenas suporta a operação **Update.**

1. Na secção de **mapeamento** sacar, só é possível alterar o ID correspondente que é usado para ligar um perfil de utilizador successFactors com o utilizador DaD Azure e que atributo em Azure AD serve como fonte de e-mail. 
    >[!div class="mx-imgBorder"]
    >![Portal do Azure](./media/sap-successfactors-inbound-provisioning/sfwb-attribute-mapping.png)

   >[!NOTE]
   >O Write-back SuccessFactors só dá suporte ao atributo email. Por favor, não utilize **adicionar novo mapeamento** para adicionar novos atributos. 

1. Para guardar os seus mapeamentos, clique em **Guardar** no topo da secção Attribute-Mapping.

Uma vez concluída a configuração de mapeamento do atributo, pode agora [ativar e lançar o serviço](#enable-and-launch-user-provisioning)de fornecimento do utilizador .

## <a name="enable-and-launch-user-provisioning"></a>Habilitar e iniciar o provisionamento de usuário

Depois que as configurações do aplicativo de provisionamento do SuccessFactors tiverem sido concluídas, você poderá ativar o serviço de provisionamento no portal do Azure.

> [!TIP]
> Por padrão, quando você ativa o serviço de provisionamento, ele inicia as operações de provisionamento para todos os usuários no escopo. Se houver erros no mapeamento ou nos problemas de dados do workday, o trabalho de provisionamento poderá falhar e entrar no estado de quarentena. Para evitar isto, como uma boa prática, recomendamos configurar o filtro **Source Object Scope** e testar os seus mapeamentos de atributos com alguns utilizadores de teste antes de lançar a sincronização completa para todos os utilizadores. Depois de verificar se os mapeamentos funcionam e estão fornecendo os resultados desejados, você pode remover o filtro ou expandi-lo gradualmente para incluir mais usuários.

1. No separador **provisionamento,** coloque o estado de **provisionamento** **ligado**.

2. Clique em **Guardar**.

3. Esta operação iniciará a sincronização inicial, que pode usar um número variável de horas dependendo de quantos usuários estiverem no locatário SuccessFactors. Você pode verificar a barra de progresso para acompanhar o progresso do ciclo de sincronização. 

4. A qualquer momento, verifique o separador de **registos de auditoria** no portal Azure para ver que ações o serviço de provisionamento realizou. Os logs de auditoria listam todos os eventos de sincronização individuais executados pelo serviço de provisionamento, como quais usuários estão sendo lidos do workday e, posteriormente, adicionados ou atualizados para Active Directory. 

5. Uma vez concluída a sincronização inicial, escreverá um relatório resumo de auditoria no separador **Provisioning,** como mostrado abaixo.

   > [!div class="mx-imgBorder"]
   > ![provisionamento de](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png) de progresso

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
* [Saiba como configurar um único sign-on entre SuccessFactors e Azure Ative Directory](successfactors-tutorial.md)
* [Saiba como integrar outras aplicações do SaaS com o Diretório Ativo Azure](tutorial-list.md)
* [Saiba como exportar e importar as suas configurações de provisionamento](../app-provisioning/export-import-provisioning-configuration.md)

