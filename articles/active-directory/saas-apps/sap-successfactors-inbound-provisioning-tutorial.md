---
title: 'Tutorial: Configurar o fornecimento de sucessos de sucessos no fornecimento de AD e Azure AD | Microsoft Docs'
description: Saiba como configurar o provisionamento de entrada de successfactors
services: active-directory
author: cmmdesai
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.topic: tutorial
ms.workload: identity
ms.date: 01/19/2021
ms.author: chmutali
ms.openlocfilehash: ce48d87c6e04e6c349b681e953647feb5e7ddda5
ms.sourcegitcommit: 9d9221ba4bfdf8d8294cf56e12344ed05be82843
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2021
ms.locfileid: "98570121"
---
# <a name="tutorial-configure-sap-successfactors-to-active-directory-user-provisioning"></a>Tutorial: Configurar os sap successFactors para o provisionamento do utilizador do Ative Directory 
O objetivo deste tutorial é mostrar os passos que você precisa para executar para os utilizadores de SuccessFactors Employee Central em Ative Directory (AD) e Azure AD, com a write-back opcional de endereço de e-mail para SuccessFactors. 

>[!NOTE]
>Utilize este tutorial se os utilizadores que pretender obter do SuccessFactors necessitarem de uma conta AD no local e opcionalmente uma conta AD AZure. Se os utilizadores do SuccessFactors apenas precisarem da conta AD Azure (utilizadores apenas na nuvem), consulte o tutorial sobre configurar o fornecimento de [utilizadores SAP SuccessFactors para o Azure AD.](sap-successfactors-inbound-provisioning-cloud-only-tutorial.md) 


## <a name="overview"></a>Descrição Geral

O [serviço de prestação de utilizadores do Azure Ative Directory](../app-provisioning/user-provisioning.md) integra-se com a Centro de [Trabalhadores do SuccessFactors](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html) de forma a gerir o ciclo de vida identitária dos utilizadores. 

O serviço de fornecimento de utilizadores SuccessFactors suportado pelo serviço de fornecimento de utilizadores Azure AD permite automatizar os seguintes recursos humanos e cenários de gestão do ciclo de vida da identidade:

* **Contratação de novos colaboradores** - Quando um novo empregado é adicionado ao SuccessFactors, uma conta de utilizador é criada automaticamente em Ative Directory, Azure Ative Directory, e opcionalmente Microsoft 365 e [outras aplicações SaaS suportadas pela Azure AD,](../app-provisioning/user-provisioning.md)com a desativação do endereço de e-mail para SuccessFactors.

* **Atributos dos colaboradores e atualizações de perfis** - Quando um registo de empregados é atualizado em SuccessFactors (como o seu nome, título ou gestor), a sua conta de utilizador será automaticamente atualizada no Ative Directory, Azure Ative Directory e opcionalmente microsoft 365 e [outras aplicações SaaS suportadas pela Azure AD](../app-provisioning/user-provisioning.md).

* **Rescisões de empregados** - Quando um empregado é encerrado em SuccessFactors, a sua conta de utilizador é automaticamente desativada em Ative Directory, Azure Ative Directory, e opcionalmente Microsoft 365 e [outras aplicações SaaS suportadas pela Azure AD](../app-provisioning/user-provisioning.md).

* **Recontrações de empregados** - Quando um empregado é recontratado em SuccessFactors, a sua conta antiga pode ser automaticamente reativada ou re provisionada (dependendo da sua preferência) para o Ative Directory, Azure Ative Directory, e opcionalmente Microsoft 365 e [outras aplicações SaaS apoiadas pela Azure AD](../app-provisioning/user-provisioning.md).

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Para quem é a solução de provisionamento do utilizador mais adequada?

Esta solução de provisionamento de utilizadores do Ative Directory é ideal para:

* Organizações que desejam uma solução pré-construída e baseada em nuvem para o fornecimento de utilizadores do SuccessFactors

* Organizações que exigem fornecimento direto de utilizadores de SuccessFactors para Ative Directory

* Organizações que exigem que os utilizadores sejam aprovisionados utilizando dados obtidos a partir da [Central de Trabalhadores do SuccessFactors (CE)](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html)

* Organizações que exigem a adesão, mudança e deixar os utilizadores sincronizados com uma ou mais Florestas de Diretórios Ativos, Domínios e OUs com base apenas em informações de alteração detetadas no [SuccessFactors Employee Central (CE)](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html)

* Organizações que usam o Microsoft 365 para e-mail

## <a name="solution-architecture"></a>Arquitetura da Solução

Esta secção descreve a arquitetura de solução de aprovisionamento de utilizadores de ponta a ponta para ambientes híbridos comuns. Existem dois fluxos relacionados:

* **Fluxo de dados de RH autoritário – de SuccessFactors a Ative Directory:** Neste evento de trabalhadores de fluxo (como Novas Contratações, Transferências, Rescisões) ocorrem primeiro na nuvem SuccessFactors Employee Central e, em seguida, os dados do evento fluem para o Diretório Ativo no local através da Azure AD e do Agente Provisionante. Dependendo do evento, pode levar a operações de criação/atualização/ativação/desativação em AD.
* **Email Writeback Flow – do Diretório Ativo para o SuccessFactors:** Uma vez que a criação de conta esteja completa no Ative Directory, é sincronizado com Azure AD através da sincronização Azure AD Connect e o atributo de e-mail pode ser escrito de volta para o SuccessFactors.

  ![Descrição Geral](./media/sap-successfactors-inbound-provisioning/sf2ad-overview.png)

### <a name="end-to-end-user-data-flow"></a>Fluxo de dados de utilizador de ponta a ponta

1. A equipa de RH realiza transações de trabalhadores (Joiners/Movers/Leavers ou New Hires/Transfers/Terminations) em SuccessFactors Employee Central
2. O Serviço de Provisionamento AZURE AD executa sincronizações programadas de identidades dos SuccessFactors CE e identifica alterações que precisam de ser processadas para sincronização com o Diretório Ativo no local.
3. O Serviço de Provisionamento Azure AD invoca o agente de provisionamento Azure AD Connect Provisioning Com um pedido de carga útil contendo a conta AD criar/atualizar/ativar/desativar as operações.
4. O Agente de Provisionamento AD AD Ad Azure utiliza uma conta de serviço para adicionar/atualizar dados da conta AD.
5. O motor Azure AD Connect Sync funciona com sincronização delta para puxar atualizações em AD.
6. As atualizações do Ative Directory estão sincronizadas com o Azure Ative Directory.
7. Se a [aplicação SuccessFactors Writeback](sap-successfactors-writeback-tutorial.md) estiver configurada, escreve o atributo de e-mail de volta aos SuccessFactors, com base no atributo correspondente utilizado.

## <a name="planning-your-deployment"></a>Planejando a sua implantação

Configurar o fornecimento de utilizadores orientados pela Cloud HR de SuccessFactors para AD requer um planeamento considerável cobrindo diferentes aspetos, tais como:
* Configuração do agente de provisionamento Azure AD Connect 
* Número de successFactors para aplicações de fornecimento de utilizadores AD para implementar
* ID correspondente, mapeamento de atributos, transformação e filtros de scoping

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

1. Faça login no SAP SuccessFactors com uma conta de utilizador que tenha acesso ao Centro de Administração.
1. Procure por *Gerir funções de permissões* e, em seguida, **selecione 'Gerir funções'** a partir dos resultados da pesquisa.
  ![Gerir funções de permissões](./media/sap-successfactors-inbound-provisioning/manage-permission-roles.png)
1. A partir da Lista de Funções de Permissão, clique em **Criar Novo**.
    > [!div class="mx-imgBorder"]
    > ![Criar nova função de permissão](./media/sap-successfactors-inbound-provisioning/create-new-permission-role-1.png)
1. Adicione um nome de **papel** e **descrição** para o novo papel de permissão. O nome e a descrição devem indicar que a função é para permissões de utilização da API.
    > [!div class="mx-imgBorder"]
    > ![Detalhe de função de permissão](./media/sap-successfactors-inbound-provisioning/permission-role-detail.png)
1. Nas definições de Permissão, clique em **Permissão...** e, em seguida, desloque a lista de permissões e clique em **Gerir Ferramentas de Integração**. Verifique a caixa para **permitir o acesso do Administrador à API OData através da Autenticação Básica.**
    > [!div class="mx-imgBorder"]
    > ![Gerir ferramentas de integração](./media/sap-successfactors-inbound-provisioning/manage-integration-tools.png)
1. Desloque-se na mesma caixa e selecione **API Central do Empregado**. Adicione permissões como mostrado abaixo para ler usando a API ODATA e edite usando a API ODATA. Selecione a opção de edição se pretender utilizar a mesma conta para o cenário De Writeback to SuccessFactors. 
    > [!div class="mx-imgBorder"]
    > ![Ler permissões de escrita](./media/sap-successfactors-inbound-provisioning/odata-read-write-perm.png)

1. Na mesma caixa de permissões, vá a Permissões de **Utilizador -> Dados do Empregado** e reveja os atributos que a conta de serviço pode ler do inquilino successFactors. Por exemplo, para recuperar o atributo *Username* dos SuccessFactors, certifique-se de que a permissão "Ver" é concedida para este atributo. Reveja igualmente cada atributo para obter a permissão de visualização. 

    > [!div class="mx-imgBorder"]
    > ![Permissões de dados dos colaboradores](./media/sap-successfactors-inbound-provisioning/review-employee-data-permissions.png)
   

    >[!NOTE]
    >Para a lista completa de atributos recuperados por esta app de provisionamento, consulte a [Referência de Atributos SuccessFactors](../app-provisioning/sap-successfactors-attribute-reference.md)

1. Clique em **'Feito'.** Clique em **Guardar Alterações**.

### <a name="create-a-permission-group-for-the-api-user"></a>Criar um Grupo de Permissão para o utilizador API

1. No Centro de Administração SuccessFactors, procure *grupos de permissões de gestão* e, em seguida, selecione **'Gerir grupos de permissão'** a partir dos resultados da pesquisa.
    > [!div class="mx-imgBorder"]
    > ![Gerir grupos de permissões](./media/sap-successfactors-inbound-provisioning/manage-permission-groups.png)
1. A partir da janela 'Gerir grupos' de permissão, clique em **Criar Novo**.
    > [!div class="mx-imgBorder"]
    > ![Adicionar novo grupo](./media/sap-successfactors-inbound-provisioning/create-new-group.png)
1. Adicione um nome de grupo para o novo grupo. O nome do grupo deve indicar que o grupo é para utilizadores de API.
    > [!div class="mx-imgBorder"]
    > ![Nome do grupo de permissão](./media/sap-successfactors-inbound-provisioning/permission-group-name.png)
1. Adicione membros ao grupo. Por exemplo, pode selecionar o nome de **utilizador** do menu de entrega do People Pool e, em seguida, introduzir o nome de utilizador da conta API que será utilizada para a integração. 
    > [!div class="mx-imgBorder"]
    > ![Adicionar membros do grupo](./media/sap-successfactors-inbound-provisioning/add-group-members.png)
1. Clique **em Fazer** para terminar a criação do Grupo de Permissões.

### <a name="grant-permission-role-to-the-permission-group"></a>Atribuir papel de permissão ao Grupo de Permissão

1. No SuccessFactors Admin Center, procure por *Manage Permission Roles*, em seguida, **selecione Gerir Funções** de Permisse a partir dos resultados da pesquisa.
1. A partir da **Lista de Funções de Permisse,** selecione a função que criou para permissões de utilização da API.
1. Sob **o Comando de Grant, este papel para...** clique em **Adicionar...** botão.
1. Selecione **Grupo de Permissões...** a partir do menu suspenso, em seguida, clique em **Selecionar...** para abrir a janela Grupos para procurar e selecionar o grupo criado acima. 
    > [!div class="mx-imgBorder"]
    > ![Adicionar grupo de permissões](./media/sap-successfactors-inbound-provisioning/add-permission-group.png)
1. Reveja a concessão de função de permissão ao Grupo de Permissão. 
    > [!div class="mx-imgBorder"]
    > ![Papel de Permissão e detalhe de grupo](./media/sap-successfactors-inbound-provisioning/permission-role-group.png)
1. Clique em **Guardar Alterações**.

## <a name="configuring-user-provisioning-from-successfactors-to-active-directory"></a>Configurar o fornecimento de utilizadores de SuccessFactors para Ative Directory

Esta secção fornece passos para o fornecimento de conta de utilizador de SuccessFactors a cada domínio ative Directory no âmbito da sua integração.

* [Adicione a app de conector de provisionamento e descarregue o Agente De provisionador](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent)
* [Instalação e configuração no local Agente de provisionamento](#part-2-install-and-configure-on-premises-provisioning-agents)
* [Configure a conectividade com os SuccessFactors e o Ative Directory](#part-3-in-the-provisioning-app-configure-connectivity-to-successfactors-and-active-directory)
* [Configurar mapeamentos de atributos](#part-4-configure-attribute-mappings)
* [Permitir e lançar o fornecimento de utilizadores](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent"></a>Parte 1: Adicione a app de conector de provisionamento e descarregue o Agente De provisionador

**Para configurar o fornecimento de SuccessFactors ao Ative Directory:**

1. Ir para <https://portal.azure.com>

2. Na barra de navegação à esquerda, selecione **Azure Ative Directory**

3. Selecione **Aplicações empresariais,** em seguida, **todas as aplicações**.

4. **Selecione Adicione uma aplicação** e selecione a categoria **'Todos'.**

5. Procure por **SuccessFactors para Ative Directory User Provisioning**, e adicione essa aplicação na galeria.

6. Depois de adicionar a app e o ecrã de detalhes da aplicação ser mostrado, selecione **Provisioning**

7. Alterar o **modo** **de provisionamento** para **automático**

8. Clique no banner de informações exibido para descarregar o Agente Provisioning. 
   >[!div class="mx-imgBorder"]
   >![Agente de descarregamento](./media/workday-inbound-tutorial/pa-download-agent.png "Download Agent Screen")

### <a name="part-2-install-and-configure-on-premises-provisioning-agents"></a>Parte 2: Instalar e configurar no local agentes de provisionamento

Para o fornecimento ao Ative Directory no local, o agente Provisioning deve ser instalado num servidor ligado a domínios que tenha acesso à rede ao ou dos domínios do Diretório Ativo pretendidos.

Transfira o instalador de agente descarregado para o anfitrião do servidor e siga os passos listados [na secção do agente de instalação](../cloud-provisioning/how-to-install.md) para completar a configuração do agente.

### <a name="part-3-in-the-provisioning-app-configure-connectivity-to-successfactors-and-active-directory"></a>Parte 3: Na app de provisionamento, configurar a conectividade com os SuccessFactors e o Ative Directory
Neste passo, estabelecemos conectividade com o SuccessFactors e Diretório Ativo no portal Azure. 

1. No portal Azure, volte aos SuccessFactors para a App de Provisionamento de Utilizadores de Diretório Ativo criada na [Parte 1](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent)

1. Preencha a secção **credenciais de administração** da seguinte forma:

   * **Nome de Utilizador Admin** – Introduza o nome de utilizador da conta de utilizador da API successFactors, com o ID da empresa anexado. Tem o formato: **\@ username companyID**

   * **Senha de administração –** Introduza a palavra-passe da conta de utilizador da API do SuccessFactors. 

   * **URL do inquilino –** Insira o nome do ponto final dos serviços OData API dos SuccessFactors. Apenas introduza o nome de anfitrião do servidor sem https ou https. Este valor deve ser como: **<nome api-servidor>.successfactors.com**.

   * **Floresta de Diretório Ativo -** O "Nome" do seu domínio ative directory, registado no agente. Utilize o dropdown para selecionar o domínio alvo para o provisionamento. Este valor é tipicamente uma cadeia como: *contoso.com*

   * **Contentor de Diretório Ativo -** Introduza o contentor DN onde o agente deve criar contas de utilizador por padrão.
        Exemplo: *OU=Utilizadores,DC=contoso,DC=com*
        > [!NOTE]
        > Esta definição só entra em jogo para criações de conta de utilizador se o atributo *nome parental* não estiver configurado nos mapeamentos do atributo. Esta definição não é utilizada para operações de pesquisa ou atualização do utilizador. Toda a sub-árvore de domínio cai no âmbito da operação de busca.

   * **E-mail de notificação –** Insira o seu endereço de e-mail e verifique a caixa de verificação "enviar e-mail se ocorrer falha".
    > [!NOTE]
    > O Serviço de Provisionamento Azure AD envia uma notificação por e-mail se o trabalho de provisionamento entrar em estado de [quarentena.](../app-provisioning/application-provisioning-quarantine-status.md)

   * Clique no botão **De Ligação de Teste.** Se o teste de ligação for bem sucedido, clique no botão **Guardar** na parte superior. Se falhar, verifique duas vezes se as credenciais do SuccessFactors e as credenciais de AD configuradas na configuração do agente são válidas.
    >[!div class="mx-imgBorder"]
    >![Portal do Azure](./media/sap-successfactors-inbound-provisioning/sf2ad-provisioning-creds.png)

   * Uma vez guardadas as credenciais com sucesso, a secção **mappings** apresentará o mapeamento padrão sincronizar os **utilizadores do SuccessFactors para o Diretório Ativo das Instalações**

### <a name="part-4-configure-attribute-mappings"></a>Parte 4: Configurar mapeamentos de atributos

Nesta secção, irá configurar como os dados dos utilizadores fluem de SuccessFactors para Ative Directory.

1. No separador De Provisionamento em **Mappings,** clique em **Sincronizar Utilizadores de Sucesso para No Diretório Ativo das Instalações**.

1. No campo **'Âmbito do Objeto fonte',** pode selecionar quais os conjuntos de utilizadores em SuccessFactors que devem estar em possibilidade de provisão para AD, definindo um conjunto de filtros baseados em atributos. O âmbito padrão é "todos os utilizadores em SuccessFactors". Filtros de exemplo:

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
   > O comportamento predefinido do motor de provisionamento é desativar/eliminar utilizadores que ficam fora de alcance. Isto pode não ser desejável nos seus SuccessFactors para a integração da AD. Para anular este comportamento padrão consulte o artigo [Ignorar a eliminação das contas de utilizador que ficam fora de alcance](../app-provisioning/skip-out-of-scope-deletions.md)
  
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

Uma vez concluída a configuração de mapeamento do seu atributo, pode testar o fornecimento de um único utilizador utilizando [o fornecimento a pedido](../app-provisioning/provision-on-demand.md) e, em seguida, [ativar e lançar o serviço de fornecimento do utilizador](#enable-and-launch-user-provisioning).

## <a name="enable-and-launch-user-provisioning"></a>Permitir e lançar o fornecimento de utilizadores

Uma vez concluídas as configurações de aplicações do SuccessFactors e verificando a provisão para um único utilizador com [provisão a pedido,](../app-provisioning/provision-on-demand.md)pode ligar o serviço de fornecimento no portal Azure.

> [!TIP]
> Por padrão, quando ligar o serviço de fornecimento, iniciará operações de provisionamento para todos os utilizadores no âmbito. Se houver erros nas questões de dados do mapeamento ou do SuccessFactors, então o trabalho de provisionamento pode falhar e entrar no estado de quarentena. Para evitar isto, como uma boa prática, recomendamos configurar o filtro **Source Object Scope** e testar os mapeamentos do seu atributo com alguns utilizadores de teste que usam o fornecimento a pedido antes de lançar [a](../app-provisioning/provision-on-demand.md) sincronização completa para todos os utilizadores. Uma vez verificado que os mapeamentos funcionam e lhe estão a dar os resultados desejados, então pode remover o filtro ou expandi-lo gradualmente para incluir mais utilizadores.

1. Vá à lâmina **de provisionamento** e clique no **provisionamento iniciar**.

1. Esta operação iniciará a sincronização inicial, que pode demorar um número variável de horas dependendo de quantos utilizadores estão no inquilino successFactors. Pode verificar a barra de progresso para acompanhar o progresso do ciclo de sincronização. 

1. A qualquer momento, consulte o **separador de registos** de auditoria no portal Azure para ver que ações o serviço de prestação de serviços executou. Os registos de auditoria listam todos os eventos de sincronização individuais realizados pelo serviço de fornecimento, tais como os utilizadores que estão a ser lidos a partir de SuccessFactors e posteriormente adicionados ou atualizados ao Ative Directory. 

1. Uma vez concluída a sincronização inicial, escreverá um relatório de resumo de auditoria no **separador Provisioning,** como mostrado abaixo.

   > [!div class="mx-imgBorder"]
   > ![Provisão de barras de progresso](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="next-steps"></a>Passos seguintes

* [Saiba mais sobre atributos de Sucessos suportados para o provisionamento de entrada](../app-provisioning/sap-successfactors-attribute-reference.md)
* [Saiba como configurar a gravação de email para o SuccessFactors](sap-successfactors-writeback-tutorial.md)
* [Saiba como analisar os registos e obter relatórios sobre a atividade de aprovisionamento](../app-provisioning/check-status-user-account-provisioning.md)
* [Saiba como configurar um único sign-on entre SuccessFactors e Azure Ative Directory](successfactors-tutorial.md)
* [Saiba como integrar outras aplicações saaS com o Azure Ative Directory](tutorial-list.md)
* [Saiba como exportar e importar as suas configurações de provisionamento](../app-provisioning/export-import-provisioning-configuration.md)