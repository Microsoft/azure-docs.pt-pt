---
title: 'Tutorial: Configure SuccessFactors reescreve no Diretório Ativo do Azure [ Microsoft Docs'
description: Saiba como configurar a atribuição de reemputações a SuccessFactors da Azure AD
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77060053"
---
# <a name="tutorial-configure-attribute-writeback-from-azure-ad-to-sap-successfactors-preview"></a>Tutorial: Configure atributo redação de Azure AD para SAP SuccessFactors (Pré-visualização)
O objetivo deste tutorial é mostrar os passos que precisa de executar para reescrever atributos de Azure AD para SuccessFactors Employee Central. O único atributo atualmente suportado para redevolução é o atributo de e-mail. 

## <a name="overview"></a>Descrição geral

Depois de configurar a integração de fornecimento de entrada utilizando a App de fornecimento de [Anúncios No local](sap-successfactors-inbound-provisioning-tutorial.md) ou a aplicação de provisionamento De [AD Para Azure,](sap-successfactors-inbound-provisioning-cloud-only-tutorial.md) pode configurar opcionalmente a app SuccessFactors Writeback para escrever o endereço de e-mail de volta para SuccessFactors. 

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Para quem é esta solução de fornecimento de utilizadores mais adequada?

Esta solução de provisionamento de utilizadores Writeback SuccessFactors é ideal para:

* Organizações que usam o Office 365 que desejam reescrever atributos autoritários geridos por TI (como endereço de e-mail) de volta ao SuccessFactors

## <a name="configuring-successfactors-for-the-integration"></a>Configurar Fatores de Sucesso para a integração

Um requisito comum de todos os conectores de fornecimento de SuccessFactors é que eles requerem credenciais de uma conta SuccessFactors com as permissões certas para invocar as APIs OData SuccessFactors. Esta secção descreve passos para criar a conta de serviço em SuccessFactors e conceder permissões apropriadas. 

* [Criar/identificar conta de utilizador da API em SuccessFactors](#createidentify-api-user-account-in-successfactors)
* [Criar um papel de permissões DaPI](#create-an-api-permissions-role)
* [Criar um Grupo de Permissão para o utilizador da API](#create-a-permission-group-for-the-api-user)
* [Papel de permissão de concessão para o Grupo de Permissão](#grant-permission-role-to-the-permission-group)

### <a name="createidentify-api-user-account-in-successfactors"></a>Criar/identificar conta de utilizador da API em SuccessFactors
Trabalhe com a sua equipa de administração do SuccessFactors ou parceiro de implementação para criar ou identificar uma conta de utilizador em SuccessFactors que será usada para invocar as APIs oData. O nome de utilizador e as credenciais de senha desta conta serão necessários ao configurar as aplicações de provisionamento em Azure AD. 

### <a name="create-an-api-permissions-role"></a>Criar um papel de permissões DaPI

* Inicie sessão no SAP SuccessFactors com uma conta de utilizador que tenha acesso ao Centro De Administração.
* Procure *funções*de permissão de gestão e, em seguida, selecione **'Gerir funções de permissão'** a partir dos resultados da pesquisa.
  ![Gerir funções de permissão](./media/sap-successfactors-inbound-provisioning/manage-permission-roles.png)
* A partir da Lista de Papéis de Permissão, clique em **Criar Novo**.
  > [!div class="mx-imgBorder"]
  > ![Criar novo papel de permissão](./media/sap-successfactors-inbound-provisioning/create-new-permission-role-1.png)
* Adicione um **nome** e **descrição** para o novo papel de permissão. O nome e a descrição devem indicar que o papel é para permissões de utilização da API.
  > [!div class="mx-imgBorder"]
  > ![Detalhe de papel de permissão](./media/sap-successfactors-inbound-provisioning/permission-role-detail.png)
* Sob as definições de Permissão, clique em **Permissão...** em seguida, percorra a lista de permissão e clique em **Gerir Ferramentas**de Integração . Verifique a caixa para permitir que o **Administrador aceda à API OData através**da autenticação básica .
  > [!div class="mx-imgBorder"]
  > ![Gerir ferramentas de integração](./media/sap-successfactors-inbound-provisioning/manage-integration-tools.png)
* Desloque-se para baixo na mesma caixa e selecione **API Central do Empregado**. Adicione permissões como mostrado abaixo para ler usando ODATA API e edite usando API ODATA. Selecione a opção de edição se planeia utilizar a mesma conta para o cenário Writeback to SuccessFactors. 
  > [!div class="mx-imgBorder"]
  > ![Ler permissões de escrita](./media/sap-successfactors-inbound-provisioning/odata-read-write-perm.png)
* Clique em **Done**. Clique em **Guardar Alterações**.

### <a name="create-a-permission-group-for-the-api-user"></a>Criar um Grupo de Permissão para o utilizador da API

* No Centro de Administração SuccessFactors, procure grupos de *permissão de gestão*e, em seguida, selecione **Manage Permission Groups** a partir dos resultados da pesquisa.
  > [!div class="mx-imgBorder"]
  > ![Gerir grupos de permissão](./media/sap-successfactors-inbound-provisioning/manage-permission-groups.png)
* A partir da janela 'Gerir grupos de permissão', clique em **Criar Novo**.
  > [!div class="mx-imgBorder"]
  > ![Adicionar novo grupo](./media/sap-successfactors-inbound-provisioning/create-new-group.png)
* Adicione um Nome de Grupo para o novo grupo. O nome do grupo deve indicar que o grupo é para utilizadores de API.
  > [!div class="mx-imgBorder"]
  > ![Nome de grupo de permissão](./media/sap-successfactors-inbound-provisioning/permission-group-name.png)
* Adicione membros ao grupo. Por exemplo, pode selecionar o **username** do menu de entrega do People Pool e, em seguida, introduzir o nome de utilizador da conta API que será usada para a integração. 
  > [!div class="mx-imgBorder"]
  > ![Adicionar membros do grupo](./media/sap-successfactors-inbound-provisioning/add-group-members.png)
* Clique em **Feito** para terminar de criar o Grupo de Permissões.

### <a name="grant-permission-role-to-the-permission-group"></a>Papel de permissão de concessão para o Grupo de Permissão

* No SuccessFactors Admin Center, procure funções de permissão de *gestão*e, em seguida, selecione **'Gerir funções de permissão'** a partir dos resultados da pesquisa.
* A partir da Lista de Papéis de **Permissão,** selecione o papel que criou para permissões de utilização da API.
* Sob **o Grant este papel para...**, clique em **Adicionar...** botão.
* Selecione **Grupo de Permissões...** a partir do menu suspenso e, em seguida, clique em **Select...** para abrir a janela Grupos para pesquisar e selecionar o grupo acima criado. 
  > [!div class="mx-imgBorder"]
  > ![Adicionar grupo de permissão](./media/sap-successfactors-inbound-provisioning/add-permission-group.png)
* Reveja a concessão de funções de permissão ao Grupo de Permissão. 
  > [!div class="mx-imgBorder"]
  > ![Papel de permissão e detalhe do grupo](./media/sap-successfactors-inbound-provisioning/permission-role-group.png)
* Clique em **Guardar Alterações**.

## <a name="configuring-successfactors-writeback"></a>Configurar a writeback de SuccessFactors

Esta secção fornece passos para 

* [Adicione a aplicação de conector de provisionamento e configure a conectividade aos SuccessFactors](#part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors)
* [Configure mapeamentos de atributos](#part-2-configure-attribute-mappings)
* [Ativar e lançar o fornecimento de utilizadores](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors"></a>Parte 1: Adicione a aplicação de conector de provisionamento e configure a conectividade aos SuccessFactors

**Para configurar successFactors Writeback:**

1. Ir para <https://portal.azure.com>

2. Na barra de navegação esquerda, **selecione Azure Ative Diretório**

3. Selecione **Aplicações Empresariais,** em **seguida, todas as aplicações**.

4. Selecione **Adicionar uma aplicação**e selecione a categoria **All.**

5. Procure **successFactors Writeback**e adicione essa aplicação na galeria.

6. Depois da aplicação ser adicionada e o ecrã de detalhes da aplicação ser mostrado, selecione **Provisioning**

7. Alterar o **modo** **de provisionamento** para **automático**

8. Complete a secção **de Credenciais de Administrador** da seguinte forma:

   * Nome de **utilizador da Administração** – Introduza o nome de utilizador da conta de utilizador da API SuccessFactors, com o ID da empresa anexado. Tem o formato: **\@username companyID**

   * **Senha de administração –** Introduza a palavra-passe da conta de utilizador da API SuccessFactors. 

   * **URL do inquilino –** Insira o nome dos serviços SuccessFactors OData API endpoint. Basta introduzir o nome de anfitrião do servidor sem http ou https. Este valor deve parecer: **api-server-name.successfactors.com.**

   * **Email de notificação –** Insira o seu endereço de e-mail e verifique a caixa de verificação "enviar e-mail se ocorrer falha".
    > [!NOTE]
    > O Serviço de Provisionamento de AD Azure envia notificação por e-mail se o trabalho de provisionamento entrar em estado de [quarentena.](/azure/active-directory/manage-apps/application-provisioning-quarantine-status)

   * Clique no botão **de ligação** de teste. Se o teste de ligação for bem sucedido, clique no botão **Guardar** na parte superior. Se falhar, verifique duas vezes se as credenciais successFactors e URL são válidas.
    >[!div class="mx-imgBorder"]
    >![Portal do Azure](./media/sap-successfactors-inbound-provisioning/sfwb-provisioning-creds.png)

   * Uma vez que as credenciais são guardadas com sucesso, a secção **De Mapeamentoapresentará** o mapeamento padrão **Synchronize Azure Ative Directory Users to SuccessFactors**

### <a name="part-2-configure-attribute-mappings"></a>Parte 2: Configurar mapeamentos de atributos

Nesta secção, irá configurar como os dados dos utilizadores fluem de SuccessFactors para Ative Directory.

1. No separador Provisioning em **Mapeamentos,** clique em **Synchronize Azure Ative Directory Users to SuccessFactors**.

1. No campo **Source Object Scope,** pode selecionar quais os conjuntos de utilizadores em AD Azure devem ser considerados para writeback, definindo um conjunto de filtros baseados em atributos. O âmbito padrão é "todos os utilizadores em Azure AD". 
   > [!TIP]
   > Quando estiver a configurar a aplicação de provisionamento pela primeira vez, terá de testar e verificar os seus mapeamentos e expressões de atributos para se certificar de que está a dar-lhe o resultado desejado. A Microsoft recomenda a utilização dos filtros de deteção no âmbito do Âmbito do **Objeto Fonte** para testar os seus mapeamentos com alguns utilizadores de teste da AD Azure. Uma vez verificado que os mapeamentos funcionam, então pode remover o filtro ou expandi-lo gradualmente para incluir mais utilizadores.

1. O campo **target object Actions** apenas suporta a operação **Update.**

1. Na secção de **mapeamento** sacar, só é possível alterar o ID correspondente que é usado para ligar um perfil de utilizador successFactors com o utilizador DaD Azure e que atributo em Azure AD serve como fonte de e-mail. 
    >[!div class="mx-imgBorder"]
    >![Portal do Azure](./media/sap-successfactors-inbound-provisioning/sfwb-attribute-mapping.png)

   >[!NOTE]
   >O Writeback SuccessFactors apenas suporta o atributo de e-mail. Por favor, não utilize **adicionar novo mapeamento** para adicionar novos atributos. 

1. Para guardar os seus mapeamentos, clique em **Guardar** no topo da secção Attribute-Mapping.

Uma vez concluída a configuração de mapeamento do atributo, pode agora [ativar e lançar o serviço](#enable-and-launch-user-provisioning)de fornecimento do utilizador .

## <a name="enable-and-launch-user-provisioning"></a>Ativar e lançar o fornecimento de utilizadores

Uma vez concluídas as configurações de aplicações de provisionamento SuccessFactors, pode ligar o serviço de provisionamento no portal Azure.

> [!TIP]
> Por predefinição, quando ligar o serviço de provisionamento, iniciará operações de provisionamento para todos os utilizadores no âmbito. Se houver erros nas questões de mapeamento ou dados do Dia do Trabalho, então o trabalho de provisionamento pode falhar e ir para o estado de quarentena. Para evitar isto, como uma boa prática, recomendamos configurar o filtro **Source Object Scope** e testar os seus mapeamentos de atributos com alguns utilizadores de teste antes de lançar a sincronização completa para todos os utilizadores. Uma vez verificado que os mapeamentos funcionam e estão a dar-lhe os resultados desejados, então pode remover o filtro ou expandi-lo gradualmente para incluir mais utilizadores.

1. No separador **provisionamento,** coloque o estado de **provisionamento** **ligado**.

2. Clique em **Guardar**.

3. Esta operação iniciará a sincronização inicial, que pode demorar um número variável de horas dependendo de quantos utilizadores estão no inquilino SuccessFactors. Pode verificar a barra de progresso para acompanhar o progresso do ciclo de sincronização. 

4. A qualquer momento, verifique o separador de **registos de auditoria** no portal Azure para ver que ações o serviço de provisionamento realizou. Os registos de auditoria listam todos os eventos de sincronização individuais realizados pelo serviço de provisionamento, tais como os utilizadores que estão a ser lidos fora do Workday e, posteriormente, adicionados ou atualizados para o Ative Directory. 

5. Uma vez concluída a sincronização inicial, escreverá um relatório resumo de auditoria no separador **Provisioning,** como mostrado abaixo.

   > [!div class="mx-imgBorder"]
   > ![Fornecimento de barra de progresso](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
* [Saiba como configurar um único sign-on entre SuccessFactors e Azure Ative Directory](successfactors-tutorial.md)
* [Saiba como integrar outras aplicações do SaaS com o Diretório Ativo Azure](tutorial-list.md)
* [Saiba como exportar e importar as suas configurações de provisionamento](../app-provisioning/export-import-provisioning-configuration.md)

