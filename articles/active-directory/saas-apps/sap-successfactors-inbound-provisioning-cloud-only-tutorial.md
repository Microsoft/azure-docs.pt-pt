---
title: 'Tutorial: Configurar o provisionamento de sucessoFactors no Diretório Ativo Azure [ Microsoft Docs'
description: Saiba como configurar o fornecimento de entrada de SuccessFactors para AD Azure
services: active-directory
author: cmmdesai
documentationcenter: na
manager: jodadzie
ms.assetid: fac4f61e-d942-4429-a298-9ba74db95077
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2019
ms.author: chmutali
ms.openlocfilehash: 09501a80d6ddcbbc9fa6cc08e36f47beb13d1663
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77063227"
---
# <a name="tutorial-configure-sap-successfactors-to-azure-ad-user-provisioning-preview"></a>Tutorial: Configure SAP SuccessFactors para o fornecimento de utilizadores da AD Azure (Pré-visualização)
O objetivo deste tutorial é mostrar os passos necessários para fornecer dados dos trabalhadores da SuccessFactors Employee Central para o Diretório Ativo Azure, com reprodução opcional de endereço de e-mail para SuccessFactors. Esta integração encontra-se na pré-visualização pública e suporta a recuperação de mais de [70 atributos](../app-provisioning/sap-successfactors-attribute-reference.md) de utilizadores da SuccessFactors Employee Central. 

>[!NOTE]
>Utilize este tutorial se os utilizadores que pretende fornecer a partir de SuccessFactors forem utilizadores apenas na nuvem que não necessitam de uma conta AD no local. Se os utilizadores necessitarem apenas de uma conta AD no local ou da conta AD e Azure AD, consulte o tutorial sobre configurar o [SAP SuccessFactors para](sap-successfactors-inbound-provisioning-tutorial.md#overview) o fornecimento de utilizadores de Diretório Ativo. 

## <a name="overview"></a>Descrição geral

O serviço de prestação de [utilizadores do Azure Ative Directory](../app-provisioning/user-provisioning.md) integra-se com a [Central de Colaboradores SuccessFactors](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html) de forma a gerir o ciclo de vida de identidade dos utilizadores. 

Os fluxos de trabalho de fornecimento de utilizadores SuccessFactors suportados pelo serviço de prestação de utilizadores da AD Azure permitem a automatização dos seguintes recursos humanos e cenários de gestão do ciclo de vida identitário:

* **Contratação** de novos colaboradores - Quando um novo colaborador é adicionado ao SuccessFactors, uma conta de utilizador é automaticamente criada no Diretório Ativo do Azure e opcionalmente office 365 e [outras aplicações SaaS suportadas pela Azure AD,](../app-provisioning/user-provisioning.md)com redação do endereço de e-mail para SuccessFactors.

* **Atributo seletiva e atualizações** de perfil - Quando um registo de empregados é atualizado em SuccessFactors (como o seu nome, título ou gestor), a sua conta de utilizador será automaticamente atualizada do Azure Ative Directory e opcionalmente office 365 e [outras aplicações SaaS suportadas pela Azure AD](../app-provisioning/user-provisioning.md).

* **Rescisões** de funcionários - Quando um empregado é despedido em SuccessFactors, a sua conta de utilizador é automaticamente desativada no Diretório Ativo Azure e opcionalmente office 365 e [outras aplicações SaaS suportadas pela Azure AD](../app-provisioning/user-provisioning.md).

* **Recontratações** de empregados - Quando um empregado é recontratado em SuccessFactors, a sua conta antiga pode ser automaticamente reativada ou reaprovisionada (dependendo da sua preferência) para o Diretório Ativo Azure e opcionalmente office 365 e [outras aplicações SaaS suportadas pela Azure AD](../app-provisioning/user-provisioning.md).

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Para quem é esta solução de fornecimento de utilizadores mais adequada?

Esta solução de provisionamento de utilizadores de Diretórios Ativos de Sucesso é ideal para:

* Organizações que desejem uma solução pré-construída e baseada na nuvem para o fornecimento de utilizadores SuccessFactors

* Organizações que exigem o fornecimento direto de utilizadores de SuccessFactors para O Diretório Ativo Azure

* Organizações que exigem que os utilizadores sejam aprovisionados utilizando dados obtidos a partir da Central de Empregados de [SucessoFactors (CE)](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html)

* Organizações que usam o Office 365 para e-mail

## <a name="solution-architecture"></a>Arquitetura de Soluções

Esta secção descreve a arquitetura de solução de solução de fornecimento de utilizadores de ponta a ponta para utilizadores apenas na nuvem. Existem dois fluxos relacionados:

* **Fluxo de dados de RH autoritário – de SuccessFactors a Diretório Ativo Azure:** Neste fluxo os eventos de trabalhadores (tais como Novas Contratações, Transferências, Rescisões) ocorrem primeiro na nuvem SuccessFactors Employee Central e, em seguida, os dados do evento fluem para o Diretório Ativo Azure. Dependendo do evento, pode levar a operações de criação/atualização/ativação/desativação em Azure AD.
* **Fluxo de Writeback por e-mail – do Diretório Ativo no local para SuccessFactors:** Uma vez que a criação da conta esteja completa no Diretório Ativo Do Azure, o valor do atributo de e-mail ou UPN gerado em Azure AD pode ser reescrito para SuccessFactors.

  ![Descrição geral](./media/sap-successfactors-inbound-provisioning/sf2aad-overview.png)

### <a name="end-to-end-user-data-flow"></a>Fluxo de dados de utilizador de ponta a ponta

1. A equipa de RH realiza transações de trabalhadores (Joiners/Movers/Leavers ou New Hires/Transfers/Terminations) na SuccessFactors Employee Central
2. O Serviço de Provisionamento de AD Azure executa sincronizações programadas de identidades da SuccessFactors EC e identifica alterações que precisam de ser processadas para sincronização com o Diretório Ativo no local.
3. O Serviço de Provisionamento de AD Azure determina a alteração e invoca a criação/atualização/ativação/desativação do funcionamento do utilizador em Azure AD.
4. Se a [aplicação SuccessFactors Writeback](sap-successfactors-writeback-tutorial.md) estiver configurada, o endereço de e-mail do utilizador é recuperado a partir de Azure AD. 
5. O serviço de provisionamento de AD Azure escreve atributo de e-mail para SuccessFactors, com base no atributo correspondente utilizado.

## <a name="planning-your-deployment"></a>Planejando a sua implantação

Configurar o fornecimento de utilizadores conduzidos pela Cloud HR de SuccessFactors para AD Azure requer um planeamento considerável que abranja diferentes aspetos, tais como:

* Determinação do ID correspondente 
* Mapeamento do atributo
* Transformação do atributo 
* Filtros de âmbito

Consulte o plano de [implantação](../app-provisioning/plan-cloud-hr-provision.md) de RH em nuvem para diretrizes abrangentes em torno destes tópicos. 

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

## <a name="configuring-user-provisioning-from-successfactors-to-azure-ad"></a>Configurar o fornecimento de utilizadores de SuccessFactors para Azure AD

Esta secção fornece passos para o fornecimento de conta de utilizador de SuccessFactors para Azure AD.

* [Adicione a aplicação de conector de provisionamento e configure a conectividade aos SuccessFactors](#part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors)
* [Configure mapeamentos de atributos](#part-2-configure-attribute-mappings)
* [Ativar e lançar o fornecimento de utilizadores](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors"></a>Parte 1: Adicione a aplicação de conector de provisionamento e configure a conectividade aos SuccessFactors

**Para configurar o fornecimento de SuccessFactors para AD Azure:**

1. Ir para <https://portal.azure.com>

2. Na barra de navegação esquerda, **selecione Azure Ative Diretório**

3. Selecione **Aplicações Empresariais,** em **seguida, todas as aplicações**.

4. Selecione **Adicionar uma aplicação**e selecione a categoria **All.**

5. Procure **SuccessFactors para Azure Ative Directory User Provisioning**, e adicione essa aplicação a partir da galeria.

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
    >![Portal do Azure](./media/sap-successfactors-inbound-provisioning/sf2aad-provisioning-creds.png)

   * Uma vez que as credenciais são guardadas com sucesso, a secção **De Mapeamentoapresentará** o mapeamento padrão **Synchronize SuccessFactors Users to Azure Ative Directory**

### <a name="part-2-configure-attribute-mappings"></a>Parte 2: Configurar mapeamentos de atributos

Nesta secção, irá configurar como os dados dos utilizadores fluem de SuccessFactors para Ative Directory.

1. No separador provisionamento em **Mapeamentos,** clique em **Synchronize SuccessFactors Users to Azure Ative Directory**.

1. No campo **Source Object Scope,** pode selecionar quais os conjuntos de utilizadores em SuccessFactors que devem estar disponíveis para o fornecimento ao Azure AD, definindo um conjunto de filtros baseados em atributos. O âmbito padrão é "todos os utilizadores em SuccessFactors". Filtros de exemplo:

   * Exemplo: Âmbito para utilizadores com personalidadeExternal entre 10000000 e 20000000 (excluindo 20000000)

      * Atributo: personIdExternal

      * Operador: Regex Match

      * Valor: (1[0-9][0-9][0-9][0-9][0-9][0-9]]]]

   * Exemplo: Apenas trabalhadores e não trabalhadores contingentes

      * Atributo: EmployeeID

      * Operador: NÃO É NULO

   > [!TIP]
   > Quando estiver a configurar a aplicação de provisionamento pela primeira vez, terá de testar e verificar os seus mapeamentos e expressões de atributos para se certificar de que está a dar-lhe o resultado desejado. A Microsoft recomenda a utilização dos filtros de deteção no âmbito do Âmbito do **Objeto Fonte** para testar os seus mapeamentos com alguns utilizadores de teste do SuccessFactors. Uma vez verificado que os mapeamentos funcionam, então pode remover o filtro ou expandi-lo gradualmente para incluir mais utilizadores.

   > [!CAUTION] 
   > O comportamento predefinido do motor de provisionamento é desativar/eliminar utilizadores que desapertem o seu alcance. Isto pode não ser desejável nos seus SuccessFactors para a integração de Anúncios Azure. Para anular este comportamento padrão, consulte o artigo [Sem a eliminação das contas dos utilizadores que saem do âmbito](../app-provisioning/skip-out-of-scope-deletions.md)
  
1. No campo **Target Object Actions,** pode filtrar globalmente quais as ações que são realizadas no Diretório Ativo. **Criar** e **Atualizar** são mais comuns.

1. Na secção de **mapeamento sacar,** pode definir como os SuccessFactors individuais atribuem mapa aos atributos do Diretório Ativo.

  >[!NOTE]
  >Para a lista completa de Atributos De SucessoSuportados pela aplicação, consulte a Referência do [Atributo SuccessFactors](../app-provisioning/sap-successfactors-attribute-reference.md)


1. Clique num mapeamento de atributoexistente para atualizá-lo, ou clique **Em adicionar novo mapeamento** na parte inferior do ecrã para adicionar novos mapeamentos. Um mapeamento individual de atributos suporta estas propriedades:

      * **Tipo de mapeamento**

         * **Direto** – Escreve o valor dos SuccessFactors atribuiao atributo aD, sem alterações

         * **Constante** - Escreva um valor de corda estático e constante para o atributo AD

         * **Expressão** – Permite-lhe escrever um valor personalizado para o atributo AD, com base em um ou mais atributos SuccessFactors. [Para mais informações, consulte este artigo sobre expressões.](../app-provisioning/functions-for-customizing-application-data.md)

      * **Atributo de origem** - O atributo do utilizador dos SuccessFactors

      * **Valor predefinido** – Opcional. Se o atributo de origem tiver um valor vazio, o mapeamento escreverá este valor.
            A configuração mais comum é deixar isto em branco.

      * **Atributo-alvo** – O atributo do utilizador no Diretório Ativo.

      * **Combine objetos usando este atributo** – Se este mapeamento deve ou não ser usado para identificar exclusivamente os utilizadores entre SuccessFactors e Ative Directy. Este valor é tipicamente definido no campo id do trabalhador para SuccessFactors, que é tipicamente mapeado para um dos atributos de ID do empregado em Diretório Ativo.

      * **Precedência correspondente** – Podem ser definidos múltiplos atributos correspondentes. Quando existem múltiplos, são avaliados na ordem definida por este campo. Assim que um jogo é encontrado, não são avaliados mais atributos correspondentes.

      * **Aplique este mapeamento**

         * **Sempre** – Aplique este mapeamento tanto na criação do utilizador como nas ações de atualização

         * **Apenas durante** a criação - Aplicar este mapeamento apenas em ações de criação de utilizadores

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

* [Saiba mais sobre os Atributos de Sucesso suportados para o fornecimento de entrada](../app-provisioning/sap-successfactors-attribute-reference.md)
* [Saiba como configurar a redação de e-mail para SuccessFactors](sap-successfactors-writeback-tutorial.md)
* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
* [Saiba como configurar um único sign-on entre SuccessFactors e Azure Ative Directory](successfactors-tutorial.md)
* [Saiba como integrar outras aplicações do SaaS com o Diretório Ativo Azure](tutorial-list.md)
* [Saiba como exportar e importar as suas configurações de provisionamento](../app-provisioning/export-import-provisioning-configuration.md)


