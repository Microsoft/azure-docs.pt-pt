---
title: 'Tutorial: Configure SuccessFactors writeback in Azure Ative Directory / Microsoft Docs'
description: Saiba como configurar a gravação do atributo para sucessoFactors da Azure AD
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "77060053"
---
# <a name="tutorial-configure-attribute-writeback-from-azure-ad-to-sap-successfactors-preview"></a>Tutorial: Configurar a writeback do Azure AD para SAP SuccessFactors (Preview)
O objetivo deste tutorial é mostrar os passos que você precisa para executar para escrever atributos de Azure AD a SuccessFactors Employee Central. O único atributo atualmente suportado para writeback é o atributo de e-mail. 

## <a name="overview"></a>Descrição geral

Depois de configurar a integração de provisionamento de entrada usando quer [o SuccessFactors para](sap-successfactors-inbound-provisioning-tutorial.md) a app de provisionamento de AD no local ou para o SuccessFactors para a app de provisionamento [AZure AD,](sap-successfactors-inbound-provisioning-cloud-only-tutorial.md) pode configurar opcionalmente a app SuccessFactors Writeback para escrever o endereço de e-mail de volta para o SuccessFactors. 

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Para quem é a solução de provisionamento do utilizador mais adequada?

Esta solução de provisionamento do utilizador SuccessFactors Writeback é ideal para:

* Organizações que usam o Office 365 que desejam escrever atributos autoritários geridos por TI (como endereço de e-mail) de volta aos SuccessFactors

## <a name="configuring-successfactors-for-the-integration"></a>Configurar o SucessoFactors para a integração

Um requisito comum de todos os conectadores successFactors que aprovisionam é que eles requerem credenciais de uma conta SuccessFactors com as permissões certas para invocar as APIs OData dos OData. Esta secção descreve passos para criar a conta de serviço em SuccessFactors e conceder permissões apropriadas. 

* [Criar/identificar conta de utilizador da API em SuccessFactors](#createidentify-api-user-account-in-successfactors)
* [Criar um papel de permissões da API](#create-an-api-permissions-role)
* [Criar um Grupo de Permissão para o utilizador API](#create-a-permission-group-for-the-api-user)
* [Atribuir papel de permissão ao Grupo de Permissão](#grant-permission-role-to-the-permission-group)

### <a name="createidentify-api-user-account-in-successfactors"></a>Criar/identificar conta de utilizador da API em SuccessFactors
Trabalhe com a sua equipa de administradores successFactors ou parceiro de implementação para criar ou identificar uma conta de utilizador em SuccessFactors que será usada para invocar as APIs OData. O nome de utilizador e as credenciais de senha desta conta serão necessárias ao configurar as aplicações de provisionamento em Azure AD. 

### <a name="create-an-api-permissions-role"></a>Criar um papel de permissões da API

* Faça login no SAP SuccessFactors com uma conta de utilizador que tenha acesso ao Centro de Administração.
* Procure por *Gerir funções de permissões*e, em seguida, **selecione 'Gerir funções'** a partir dos resultados da pesquisa.
  ![Gerir funções de permissões](./media/sap-successfactors-inbound-provisioning/manage-permission-roles.png)
* A partir da Lista de Funções de Permissão, clique em **Criar Novo**.
  > [!div class="mx-imgBorder"]
  > ![Criar nova função de permissão](./media/sap-successfactors-inbound-provisioning/create-new-permission-role-1.png)
* Adicione um nome de **papel** e **descrição** para o novo papel de permissão. O nome e a descrição devem indicar que a função é para permissões de utilização da API.
  > [!div class="mx-imgBorder"]
  > ![Detalhe de função de permissão](./media/sap-successfactors-inbound-provisioning/permission-role-detail.png)
* Nas definições de Permissão, clique em **Permissão...** e, em seguida, desloque a lista de permissões e clique em **Gerir Ferramentas de Integração**. Verifique a caixa para **permitir o acesso do Administrador à API OData através da Autenticação Básica.**
  > [!div class="mx-imgBorder"]
  > ![Gerir ferramentas de integração](./media/sap-successfactors-inbound-provisioning/manage-integration-tools.png)
* Desloque-se na mesma caixa e selecione **API Central do Empregado**. Adicione permissões como mostrado abaixo para ler usando a API ODATA e edite usando a API ODATA. Selecione a opção de edição se pretender utilizar a mesma conta para o cenário De Writeback to SuccessFactors. 
  > [!div class="mx-imgBorder"]
  > ![Ler permissões de escrita](./media/sap-successfactors-inbound-provisioning/odata-read-write-perm.png)
* Clique em **'Feito'.** Clique em **Guardar Alterações**.

### <a name="create-a-permission-group-for-the-api-user"></a>Criar um Grupo de Permissão para o utilizador API

* No Centro de Administração SuccessFactors, procure *grupos de permissões de gestão*e, em seguida, selecione **'Gerir grupos de permissão'** a partir dos resultados da pesquisa.
  > [!div class="mx-imgBorder"]
  > ![Gerir grupos de permissões](./media/sap-successfactors-inbound-provisioning/manage-permission-groups.png)
* A partir da janela 'Gerir grupos' de permissão, clique em **Criar Novo**.
  > [!div class="mx-imgBorder"]
  > ![Adicionar novo grupo](./media/sap-successfactors-inbound-provisioning/create-new-group.png)
* Adicione um nome de grupo para o novo grupo. O nome do grupo deve indicar que o grupo é para utilizadores de API.
  > [!div class="mx-imgBorder"]
  > ![Nome do grupo de permissão](./media/sap-successfactors-inbound-provisioning/permission-group-name.png)
* Adicione membros ao grupo. Por exemplo, pode selecionar o nome de **utilizador** do menu de entrega do People Pool e, em seguida, introduzir o nome de utilizador da conta API que será utilizada para a integração. 
  > [!div class="mx-imgBorder"]
  > ![Adicionar membros do grupo](./media/sap-successfactors-inbound-provisioning/add-group-members.png)
* Clique **em Fazer** para terminar a criação do Grupo de Permissões.

### <a name="grant-permission-role-to-the-permission-group"></a>Atribuir papel de permissão ao Grupo de Permissão

* No SuccessFactors Admin Center, procure por *Manage Permission Roles*, em seguida, **selecione Gerir Funções** de Permisse a partir dos resultados da pesquisa.
* A partir da **Lista de Funções de Permisse,** selecione a função que criou para permissões de utilização da API.
* Sob **o Comando de Grant, este papel para...** clique em **Adicionar...** botão.
* Selecione **Grupo de Permissões...** a partir do menu suspenso, em seguida, clique em **Selecionar...** para abrir a janela Grupos para procurar e selecionar o grupo criado acima. 
  > [!div class="mx-imgBorder"]
  > ![Adicionar grupo de permissões](./media/sap-successfactors-inbound-provisioning/add-permission-group.png)
* Reveja a concessão de função de permissão ao Grupo de Permissão. 
  > [!div class="mx-imgBorder"]
  > ![Papel de Permissão e detalhe de grupo](./media/sap-successfactors-inbound-provisioning/permission-role-group.png)
* Clique em **Guardar Alterações**.

## <a name="configuring-successfactors-writeback"></a>Configuração do Sucesso Confirmação de Histórias

Esta secção fornece passos para 

* [Adicione a app de conector de provisionamento e configuure a conectividade aos SuccessFactors](#part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors)
* [Configurar mapeamentos de atributos](#part-2-configure-attribute-mappings)
* [Permitir e lançar o fornecimento de utilizadores](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors"></a>Parte 1: Adicione a app de conector de provisionamento e configuure a conectividade aos SuccessFactors

**Para configurar a gravação do SuccessFactors:**

1. Ir para <https://portal.azure.com>

2. Na barra de navegação à esquerda, selecione **Azure Ative Directory**

3. Selecione **Aplicações empresariais,** em seguida, **todas as aplicações**.

4. **Selecione Adicione uma aplicação**e selecione a categoria **'Todos'.**

5. Procure por **SuccessFactors Writeback**e adicione a aplicação na galeria.

6. Depois de adicionar a app e o ecrã de detalhes da aplicação ser mostrado, selecione **Provisioning**

7. Alterar o **modo** **de provisionamento** para **automático**

8. Preencha a secção **credenciais de administração** da seguinte forma:

   * **Nome de Utilizador Admin** – Introduza o nome de utilizador da conta de utilizador da API successFactors, com o ID da empresa anexado. Tem o formato: ** \@ username companyID**

   * **Senha de administração –** Introduza a palavra-passe da conta de utilizador da API do SuccessFactors. 

   * **URL do inquilino –** Insira o nome do ponto final dos serviços OData API dos SuccessFactors. Apenas introduza o nome de anfitrião do servidor sem https ou https. Este valor deve parecer: **api-server-name.successfactors.com**.

   * **E-mail de notificação –** Insira o seu endereço de e-mail e verifique a caixa de verificação "enviar e-mail se ocorrer falha".
    > [!NOTE]
    > O Serviço de Provisionamento Azure AD envia uma notificação por e-mail se o trabalho de provisionamento entrar em estado de [quarentena.](/azure/active-directory/manage-apps/application-provisioning-quarantine-status)

   * Clique no botão **De Ligação de Teste.** Se o teste de ligação for bem sucedido, clique no botão **Guardar** na parte superior. Se falhar, verifique duas vezes se as credenciais e URL do SuccessFactors são válidos.
    >[!div class="mx-imgBorder"]
    >![Portal do Azure](./media/sap-successfactors-inbound-provisioning/sfwb-provisioning-creds.png)

   * Uma vez guardadas as credenciais com sucesso, a secção **mappings** apresentará o mapeamento padrão **synchronize Azure Ative Directory Users to SuccessFactors**

### <a name="part-2-configure-attribute-mappings"></a>Parte 2: Configurar mapeamentos de atributos

Nesta secção, irá configurar como os dados dos utilizadores fluem de SuccessFactors para Ative Directory.

1. No separador De Provisionamento em **Mappings,** clique em **Synchronize Azure Ative Directory Users to SuccessFactors**.

1. No campo **'Âmbito do Objeto fonte',** pode selecionar quais os conjuntos de utilizadores em Azure AD que devem ser considerados para Writeback, definindo um conjunto de filtros baseados em atributos. O âmbito padrão é "todos os utilizadores em Azure AD". 
   > [!TIP]
   > Quando estiver a configurar a app de provisionamento pela primeira vez, terá de testar e verificar os mapeamentos e expressões do seu atributo para se certificar de que está a dar-lhe o resultado desejado. A Microsoft recomenda a utilização dos filtros de deteção no **âmbito do objeto de origem** para testar os seus mapeamentos com alguns utilizadores de teste do Azure AD. Depois de verificar que os mapeamentos funcionam, pode remover o filtro ou expandi-lo gradualmente para incluir mais utilizadores.

1. O campo **"Ações de Objecto-Alvo"** suporta apenas a operação **Atualização.**

1. Na secção **de mapeamentos do Attribute,** só é possível alterar o ID correspondente que é utilizado para ligar um perfil de utilizador do SuccessFactors ao utilizador AZure AD e que o atributo Azure AD serve como fonte de e-mail. 
    >[!div class="mx-imgBorder"]
    >![Portal do Azure](./media/sap-successfactors-inbound-provisioning/sfwb-attribute-mapping.png)

   >[!NOTE]
   >A writeback SuccessFactors apenas suporta o atributo de e-mail. Por favor, não utilize **adicionar Add New Mapping** para adicionar novos atributos. 

1. Para guardar os seus mapeamentos, clique em **Guardar** na parte superior da secção De mapeamento de Atributos.

Uma vez concluída a configuração de mapeamento do seu atributo, pode agora [ativar e lançar o serviço de fornecimento de utilizadores](#enable-and-launch-user-provisioning).

## <a name="enable-and-launch-user-provisioning"></a>Permitir e lançar o fornecimento de utilizadores

Uma vez concluídas as configurações de aplicações do SuccessFactors, pode ligar o serviço de prestação no portal Azure.

> [!TIP]
> Por padrão, quando ligar o serviço de fornecimento, iniciará operações de provisionamento para todos os utilizadores no âmbito. Se houver erros no mapeamento ou problemas de dados do Dia de Trabalho, então o trabalho de provisionamento pode falhar e entrar no estado de quarentena. Para evitar isto, como uma boa prática, recomendamos configurar o filtro **Source Object Scope** e testar os mapeamentos do seu atributo com alguns utilizadores de teste antes de lançar a sincronização completa para todos os utilizadores. Uma vez verificado que os mapeamentos funcionam e lhe estão a dar os resultados desejados, então pode remover o filtro ou expandi-lo gradualmente para incluir mais utilizadores.

1. No **separador Provisioning,** desa fixação do **Estado de Provisionamento** para **On**.

2. Clique em **Guardar**.

3. Esta operação iniciará a sincronização inicial, que pode demorar um número variável de horas dependendo de quantos utilizadores estão no inquilino successFactors. Pode verificar a barra de progresso para acompanhar o progresso do ciclo de sincronização. 

4. A qualquer momento, consulte o **separador de registos** de auditoria no portal Azure para ver que ações o serviço de prestação de serviços executou. Os registos de auditoria listam todos os eventos de sincronização individuais realizados pelo serviço de fornecimento, tais como os utilizadores que estão a ser lidos fora do Workday e posteriormente adicionados ou atualizados ao Ative Directory. 

5. Uma vez concluída a sincronização inicial, escreverá um relatório de resumo de auditoria no **separador Provisioning,** como mostrado abaixo.

   > [!div class="mx-imgBorder"]
   > ![Provisão de barras de progresso](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="next-steps"></a>Próximos passos

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
* [Saiba como configurar um único sign-on entre SuccessFactors e Azure Ative Directory](successfactors-tutorial.md)
* [Saiba como integrar outras aplicações saaS com o Azure Ative Directory](tutorial-list.md)
* [Saiba como exportar e importar as suas configurações de provisionamento](../app-provisioning/export-import-provisioning-configuration.md)

