---
title: 'Tutorial: Configure SuccessFactors a entrar no Azure Ative Directory / Microsoft Docs'
description: Saiba como configurar o provisionamento de entrada de SuccessFactors para Azure AD
services: active-directory
author: cmmdesai
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.topic: tutorial
ms.workload: identity
ms.date: 08/05/2020
ms.author: chmutali
ms.openlocfilehash: a62943c1a808424ded1a5e46ed115cda332bf7d5
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96020760"
---
# <a name="tutorial-configure-sap-successfactors-to-azure-ad-user-provisioning"></a>Tutorial: Configurar os sap successFactors para o fornecimento de utilizadores Azure AD
O objetivo deste tutorial é mostrar os passos necessários para a disponibilização de dados dos trabalhadores da SuccessFactors Employee Central para o Azure Ative Directory, com a inscrição opcional do endereço de e-mail para o SuccessFactors. 

>[!NOTE]
>Utilize este tutorial se os utilizadores que pretende obter do SuccessFactors são utilizadores apenas na nuvem que não precisam de uma conta AD no local. Se os utilizadores necessitarem apenas de conta AD no local ou tanto da conta AD como da AZure AD, consulte o tutorial sobre [configuração SAP SuccessFactors para o](sap-successfactors-inbound-provisioning-tutorial.md#overview) provisionamento do utilizador do Ative Directory. 

## <a name="overview"></a>Descrição geral

O [serviço de prestação de utilizadores do Azure Ative Directory](../app-provisioning/user-provisioning.md) integra-se com a Centro de [Trabalhadores do SuccessFactors](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html) de forma a gerir o ciclo de vida identitária dos utilizadores. 

O serviço de fornecimento de utilizadores SuccessFactors suportado pelo serviço de fornecimento de utilizadores Azure AD permite automatizar os seguintes recursos humanos e cenários de gestão do ciclo de vida da identidade:

* **Contratação de novos colaboradores** - Quando um novo empregado é adicionado aos SuccessFactors, uma conta de utilizador é criada automaticamente no Azure Ative Directory e opcionalmente microsoft 365 e [outras aplicações SaaS suportadas pela Azure AD,](../app-provisioning/user-provisioning.md)com a desativação do endereço de e-mail para SuccessFactors.

* **Atribuição de colaboradores e atualizações de perfis** - Quando um registo de empregados é atualizado em SuccessFactors (como o seu nome, título ou gestor), a sua conta de utilizador será automaticamente atualizada Azure Ative Directory e opcionalmente Microsoft 365 e [outras aplicações SaaS suportadas pela Azure AD](../app-provisioning/user-provisioning.md).

* **Rescisões de empregados** - Quando um empregado é encerrado em SuccessFactors, a sua conta de utilizador é automaticamente desativada no Azure Ative Directory e opcionalmente microsoft 365 e [outras aplicações SaaS suportadas pela Azure AD](../app-provisioning/user-provisioning.md).

* **Recontrações de empregados** - Quando um empregado é recontratado em SuccessFactors, a sua conta antiga pode ser automaticamente reativada ou re provisionada (dependendo da sua preferência) para o Azure Ative Directory e opcionalmente microsoft 365 e [outras aplicações SaaS apoiadas pela Azure AD](../app-provisioning/user-provisioning.md).

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Para quem é a solução de provisionamento do utilizador mais adequada?

Esta solução de provisionamento de utilizadores do Azure Ative Directory é ideal para:

* Organizações que desejam uma solução pré-construída e baseada em nuvem para o fornecimento de utilizadores do SuccessFactors

* Organizações que exigem fornecimento direto de utilizadores de SuccessFactors para Azure Ative Directory

* Organizações que exigem que os utilizadores sejam aprovisionados utilizando dados obtidos a partir da [Central de Trabalhadores do SuccessFactors (CE)](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html)

* Organizações que usam o Microsoft 365 para e-mail

## <a name="solution-architecture"></a>Arquitetura de Soluções

Esta secção descreve a arquitetura de solução de atenção de utilizadores de ponta a ponta para utilizadores apenas na nuvem. Existem dois fluxos relacionados:

* **Fluxo de dados hr autoritários – de SuccessFactors a Azure Ative Directory:** Neste evento de trabalhadores de fluxo (como Novas Contratações, Transferências, Rescisões) ocorrem primeiro na nuvem SuccessFactors Employee Central e, em seguida, os dados do evento fluem para o Diretório Ativo Azure. Dependendo do evento, pode levar a operações de criação/atualização/ativação/desativação em Azure AD.
* **Email Writeback Flow – do Diretório Ativo para o SuccessFactors:** Uma vez que a criação da conta esteja completa no Azure Ative Directory, o valor de atributo de e-mail ou UPN gerado em Azure AD pode ser rectado aos SuccessFactors.

  ![Descrição geral](./media/sap-successfactors-inbound-provisioning/sf2aad-overview.png)

### <a name="end-to-end-user-data-flow"></a>Fluxo de dados de utilizador de ponta a ponta

1. A equipa de RH realiza transações de trabalhadores (Joiners/Movers/Leavers ou New Hires/Transfers/Terminations) em SuccessFactors Employee Central
2. O Serviço de Provisionamento AZURE AD executa sincronizações programadas de identidades dos SuccessFactors CE e identifica alterações que precisam de ser processadas para sincronização com o Diretório Ativo no local.
3. O Serviço de Provisionamento AZure AD determina a alteração e invoca a criação/atualização/ativação/desativação/desativação para o utilizador em Azure AD.
4. Se a [aplicação SuccessFactors Writeback](sap-successfactors-writeback-tutorial.md) estiver configurada, então o endereço de e-mail do utilizador é recuperado a partir de Azure AD. 
5. O serviço de fornecimento de AD AZure escreve o atributo de e-mail de volta aos SuccessFactors, com base no atributo correspondente utilizado.

## <a name="planning-your-deployment"></a>Planejando a sua implantação

Configurar o fornecimento de utilizadores orientados pela Cloud HR de SuccessFactors para Azure AD requer um planeamento considerável cobrindo diferentes aspetos, tais como:

* Determinação do ID correspondente 
* Mapeamento do atributo
* Transformação de atributos 
* Filtros de âmbito

Consulte o [plano de implementação de RH em nuvem](../app-provisioning/plan-cloud-hr-provision.md) para obter orientações abrangentes em torno destes tópicos. Consulte a referência de [integração sap SuccessFactors](../app-provisioning/sap-successfactors-integration-reference.md) para saber sobre as entidades apoiadas, processamento de detalhes e como personalizar a integração para diferentes cenários de RH. 

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
* Procure por *Gerir funções de permissões* e, em seguida, **selecione 'Gerir funções'** a partir dos resultados da pesquisa.
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

* No Centro de Administração SuccessFactors, procure *grupos de permissões de gestão* e, em seguida, selecione **'Gerir grupos de permissão'** a partir dos resultados da pesquisa.
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

## <a name="configuring-user-provisioning-from-successfactors-to-azure-ad"></a>Configurar o fornecimento de utilizadores de SuccessFactors para Azure AD

Esta secção fornece passos para o fornecimento de conta de utilizador de SuccessFactors a Azure AD.

* [Adicione a app de conector de provisionamento e configuure a conectividade aos SuccessFactors](#part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors)
* [Configurar mapeamentos de atributos](#part-2-configure-attribute-mappings)
* [Permitir e lançar o fornecimento de utilizadores](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors"></a>Parte 1: Adicione a app de conector de provisionamento e configuure a conectividade aos SuccessFactors

**Para configurar o fornecimento de SuccessFactors à Azure AD:**

1. Ir para <https://portal.azure.com>

2. Na barra de navegação à esquerda, selecione **Azure Ative Directory**

3. Selecione **Aplicações empresariais,** em seguida, **todas as aplicações**.

4. **Selecione Adicione uma aplicação** e selecione a categoria **'Todos'.**

5. Procure por **SuccessFactors para Azure Ative Directory User Provisioning**, e adicione essa aplicação na galeria.

6. Depois de adicionar a app e o ecrã de detalhes da aplicação ser mostrado, selecione **Provisioning**

7. Alterar o **modo** **de provisionamento** para **automático**

8. Preencha a secção **credenciais de administração** da seguinte forma:

   * **Nome de Utilizador Admin** – Introduza o nome de utilizador da conta de utilizador da API successFactors, com o ID da empresa anexado. Tem o formato: **\@ username companyID**

   * **Senha de administração –** Introduza a palavra-passe da conta de utilizador da API do SuccessFactors. 

   * **URL do inquilino –** Insira o nome do ponto final dos serviços OData API dos SuccessFactors. Apenas introduza o nome de anfitrião do servidor sem https ou https. Este valor deve parecer: **api-server-name.successfactors.com**.

   * **E-mail de notificação –** Insira o seu endereço de e-mail e verifique a caixa de verificação "enviar e-mail se ocorrer falha".
    > [!NOTE]
    > O Serviço de Provisionamento Azure AD envia uma notificação por e-mail se o trabalho de provisionamento entrar em estado de [quarentena.](../app-provisioning/application-provisioning-quarantine-status.md)

   * Clique no botão **De Ligação de Teste.** Se o teste de ligação for bem sucedido, clique no botão **Guardar** na parte superior. Se falhar, verifique duas vezes se as credenciais e URL do SuccessFactors são válidos.
    >[!div class="mx-imgBorder"]
    >![Portal do Azure](./media/sap-successfactors-inbound-provisioning/sf2aad-provisioning-creds.png)

   * Uma vez guardadas as credenciais com sucesso, a secção **mappings** apresentará o mapeamento padrão **Sincronizar Os Utilizadores do SuccessFactors para o Azure Ative Directory**

### <a name="part-2-configure-attribute-mappings"></a>Parte 2: Configurar mapeamentos de atributos

Nesta secção, irá configurar como os dados dos utilizadores fluem de SuccessFactors para Ative Directory.

1. No separador De Provisionamento em **Mappings,** clique em **Sincronizar Utilizadores de Sucessofactors para Azure Ative Directory**.

1. No campo **Origem Object Scope,** pode selecionar quais os conjuntos de utilizadores em SuccessFactors que devem estar em possibilidade de provisão para Azure AD, definindo um conjunto de filtros baseados em atributos. O âmbito padrão é "todos os utilizadores em SuccessFactors". Filtros de exemplo:

   * Exemplo: Âmbito para utilizadores com personIdExternal entre 10000000 e 20000000 (excluindo 2000000)

      * Atributo: personIdExternal

      * Operador: REGEX Match

      * Valor: (1[0-9][0-9][0-9][0-9][0-9][0-9][0-9])

   * Exemplo: Apenas trabalhadores e não trabalhadores contingentes

      * Atributo: EmployeeID

      * Operador: NÃO É NULO

   > [!TIP]
   > Quando estiver a configurar a app de provisionamento pela primeira vez, terá de testar e verificar os mapeamentos e expressões do seu atributo para se certificar de que está a dar-lhe o resultado desejado. A Microsoft recomenda a utilização dos filtros de deteção no **âmbito do objeto de origem** para testar os seus mapeamentos com alguns utilizadores de teste de SuccessFactors. Depois de verificar que os mapeamentos funcionam, pode remover o filtro ou expandi-lo gradualmente para incluir mais utilizadores.

   > [!CAUTION] 
   > O comportamento predefinido do motor de provisionamento é desativar/eliminar utilizadores que ficam fora de alcance. Isto pode não ser desejável nos seus SuccessFactors para a integração AD Azure. Para anular este comportamento padrão consulte o artigo [Ignorar a eliminação das contas de utilizador que ficam fora de alcance](../app-provisioning/skip-out-of-scope-deletions.md)
  
1. No campo **Target Object Actions,** pode filtrar globalmente as ações que são realizadas no Ative Directory. **Criar** e **Atualizar** são mais comuns.

1. Na secção **de mapeamentos do Atributo,** pode definir como os SuccessFactors individuais atribuem o mapa aos atributos ative directory.

  >[!NOTE]
  >Para a lista completa de atributos SuccessFactors suportados pela aplicação, consulte a [Referência de Atributos SuccessFactors](../app-provisioning/sap-successfactors-attribute-reference.md)


1. Clique num mapeamento de atributos existente para atualizá-lo ou clique em **Adicionar novo mapeamento** na parte inferior do ecrã para adicionar novos mapeamentos. Um mapeamento de atributos individuais suporta estas propriedades:

      * **Tipo de Mapeamento**

         * **Direto** – Escreve o valor dos SuccessFactors atribuem ao atributo AD, sem alterações

         * **Constante** - Escreva um valor estático e constante de cordas para o atributo AD

         * **Expressão** – Permite-lhe escrever um valor personalizado para o atributo AD, com base num ou mais atributos SuccessFactors. [Para mais informações, consulte este artigo sobre expressões.](../app-provisioning/functions-for-customizing-application-data.md)

      * **Atributo de origem** - O atributo do utilizador dos SuccessFactors

      * **Valor predefinido** – Opcional. Se o atributo de origem tiver um valor vazio, o mapeamento escreverá este valor.
            A configuração mais comum é deixar este em branco.

      * **Atributo-alvo** - O atributo do utilizador no Ative Directory.

      * **Combine objetos usando este atributo** – Se este mapeamento deve ou não ser usado para identificar exclusivamente os utilizadores entre SuccessFactors e Ative Directory. Este valor é tipicamente definido no campo de ID do Trabalhador para SuccessFactors, que é tipicamente mapeado para um dos atributos de ID do Empregado no Ative Directory.

      * **Precedência correspondente** - Podem ser definidos vários atributos correspondentes. Quando há múltiplos, são avaliados na ordem definida por este campo. Assim que um fósforo é encontrado, não são avaliados mais atributos correspondentes.

      * **Aplique este mapeamento**

         * **Always** – Aplicar este mapeamento tanto na criação como nas ações de atualização do utilizador

         * **Apenas durante a criação** - Aplique este mapeamento apenas em ações de criação de utilizadores

1. Para guardar os seus mapeamentos, clique em **Guardar** na parte superior da secção Attribute-Mapping.

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

## <a name="next-steps"></a>Passos seguintes

* [Saiba mais sobre atributos de Sucessos suportados para o provisionamento de entrada](../app-provisioning/sap-successfactors-attribute-reference.md)
* [Saiba como configurar a gravação de email para o SuccessFactors](sap-successfactors-writeback-tutorial.md)
* [Saiba como analisar os registos e obter relatórios sobre a atividade de aprovisionamento](../app-provisioning/check-status-user-account-provisioning.md)
* [Saiba como configurar um único sign-on entre SuccessFactors e Azure Ative Directory](successfactors-tutorial.md)
* [Saiba como integrar outras aplicações saaS com o Azure Ative Directory](tutorial-list.md)
* [Saiba como exportar e importar as suas configurações de provisionamento](../app-provisioning/export-import-provisioning-configuration.md)