---
title: 'Tutorial: Configurar o provisionamento de sucessoFactors no Diretório Ativo Azure  Microsoft Docs'
description: Saiba como configurar o fornecimento de entrada a partir de SuccessFactors
services: active-directory
author: cmmdesai
documentationcenter: na
manager: jodadzie
ms.assetid: 1ff90231-1312-463e-8949-7d976e433fc3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/05/2019
ms.author: chmutali
ms.openlocfilehash: d9317a68c8967fbe0728e8c47e59dd33367c6163
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78374780"
---
# <a name="tutorial-configure-sap-successfactors-to-active-directory-user-provisioning-preview"></a>Tutorial: Configure SAP SuccessFactors para o fornecimento de utilizadores de directórioactivo ativo (Pré-visualização)
O objetivo deste tutorial é mostrar os passos que precisa de executar para fornecer aos utilizadores da SuccessFactors Employee Central em Ative Directory (AD) e Azure AD, com redação opcional de endereço de e-mail para SuccessFactors. Esta integração encontra-se na pré-visualização pública e suporta a recuperação de mais de [70 atributos](../app-provisioning/sap-successfactors-attribute-reference.md) de utilizadores da SuccessFactors Employee Central.

>[!NOTE]
>Utilize este tutorial se os utilizadores que pretende fornecer a partir de SuccessFactors precisarem de uma conta AD no local e opcionalmente uma conta Azure AD. Se os utilizadores do SuccessFactors apenas precisarem de conta AD Azure (utilizadores apenas na nuvem), consulte o tutorial sobre [configurar o SAP SuccessFactors para](sap-successfactors-inbound-provisioning-cloud-only-tutorial.md) o fornecimento de utilizadores da AD Azure. 


## <a name="overview"></a>Descrição geral

O serviço de prestação de [utilizadores do Azure Ative Directory](../app-provisioning/user-provisioning.md) integra-se com a [Central de Colaboradores SuccessFactors](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html) de forma a gerir o ciclo de vida de identidade dos utilizadores. 

Os fluxos de trabalho de fornecimento de utilizadores SuccessFactors suportados pelo serviço de prestação de utilizadores da AD Azure permitem a automatização dos seguintes recursos humanos e cenários de gestão do ciclo de vida identitário:

* **Contratação** de novos colaboradores - Quando um novo colaborador é adicionado ao SuccessFactors, uma conta de utilizador é automaticamente criada em Ative Directory, Azure Ative Directory, e opcionalmente office 365 e [outras aplicações SaaS suportadas pela Azure AD,](../app-provisioning/user-provisioning.md)com redação do endereço de e-mail para SuccessFactors.

* **Atributos e atualizações** de perfis dos colaboradores - Quando um registo de empregados é atualizado em SuccessFactors (como o seu nome, título ou gestor), a sua conta de utilizador será automaticamente atualizada em Diretório Ativo, Diretório Ativo Azure e opcionalmente Office 365 e [outras aplicações SaaS suportadas pela Azure AD](../app-provisioning/user-provisioning.md).

* **Rescisões** de funcionários - Quando um empregado é despedido em SuccessFactors, a sua conta de utilizador é automaticamente desativada em Diretório Ativo, Diretório Ativo Azure e opcionalmente Office 365 e [outras aplicações SaaS suportadas pela Azure AD.](../app-provisioning/user-provisioning.md)

* **Recontratações** de colaboradores - Quando um empregado é recontratado em SuccessFactors, a sua conta antiga pode ser automaticamente reativada ou represtada (dependendo da sua preferência) ao Ative Directory, Ao Diretório Ativo azure e opcionalmente ao Office 365 e [outras aplicações SaaS apoiadas pela Azure AD.](../app-provisioning/user-provisioning.md)

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Para quem é esta solução de fornecimento de utilizadores mais adequada?

Esta solução de provisionamento de utilizadores de Diretórios Ativos é ideal para:

* Organizações que desejem uma solução pré-construída e baseada na nuvem para o fornecimento de utilizadores SuccessFactors

* Organizações que exigem o fornecimento direto de utilizadores de SuccessFactors para Diretório Ativo

* Organizações que exigem que os utilizadores sejam aprovisionados utilizando dados obtidos a partir da Central de Empregados de [SucessoFactors (CE)](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html)

* Organizações que exigem a adesão, mudança e deixando os utilizadores sincronizados com uma ou mais Florestas de Diretórios Ativos, Domínios e OUs com base apenas em informações de mudança detetadas na [SuccessFactors Employee Central (CE)](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html)

* Organizações que usam o Office 365 para e-mail

## <a name="solution-architecture"></a>Arquitetura de Soluções

Esta secção descreve a arquitetura de solução de solução de fornecimento de utilizadores de ponta a ponta para ambientes híbridos comuns. Existem dois fluxos relacionados:

* **Fluxo de dados de RH autoritário – desde SuccessFactors até diretório ativo no local:** Neste fluxo de eventos de trabalhadores (tais como Novas Contratações, Transferências, Rescisões) ocorrem primeiro na nuvem SuccessFactors Employee Central e, em seguida, os dados do evento fluem para o diretório ativo no local através da Azure AD e do Agente de Provisionamento. Dependendo do evento, pode levar a operações de criação/atualização/ativação/desativação em AD.
* **Fluxo de Writeback por e-mail – do Diretório Ativo no local para SuccessFactors:** Uma vez que a criação da conta esteja completa no Ative Directory, é sincronizada com o Azure AD AD sync e o atributo de e-mail pode ser reescrito para SuccessFactors.

  ![Descrição geral](./media/sap-successfactors-inbound-provisioning/sf2ad-overview.png)

### <a name="end-to-end-user-data-flow"></a>Fluxo de dados de utilizador de ponta a ponta

1. A equipa de RH realiza transações de trabalhadores (Joiners/Movers/Leavers ou New Hires/Transfers/Terminations) na SuccessFactors Employee Central
2. O Serviço de Provisionamento de AD Azure executa sincronizações programadas de identidades da SuccessFactors EC e identifica alterações que precisam de ser processadas para sincronização com o Diretório Ativo no local.
3. O Serviço de Provisionamento de AD Azure invoca o agente de provisionamento de ligação Azure AD no local com uma carga útil de pedido contendo operações de criação/atualização/ativação/desativação da conta AD.
4. O Agente de Provisionamento de Ligação AD Azure utiliza uma conta de serviço para adicionar/atualizar dados da conta AD.
5. O motor Azure AD Connect Sync executa a delta sync para puxar atualizações em AD.
6. As atualizações do Diretório Ativo estão sincronizadas com o Diretório Ativo azure.
7. Se a [aplicação SuccessFactors Writeback](sap-successfactors-writeback-tutorial.md) estiver configurada, escreve atributo de e-mail de volta para SuccessFactors, com base no atributo correspondente utilizado.

## <a name="planning-your-deployment"></a>Planejando a sua implantação

Configurar o fornecimento de utilizadores conduzidos pela Cloud HR de SuccessFactors para AD requer um planeamento considerável que abranja diferentes aspetos, tais como:
* Configuração do agente de provisionamento Azure AD Connect 
* Número de SuccessFactors para aplicações de fornecimento de utilizadores aD para implementar
* Id correspondente, mapeamento de atributos, transformação e filtros de deteção

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
  ![gerir papéis de permissão](./media/sap-successfactors-inbound-provisioning/manage-permission-roles.png)
* A partir da Lista de Papéis de Permissão, clique em **Criar Novo**.
  > [!div class="mx-imgBorder"]
  > ![criar novos](./media/sap-successfactors-inbound-provisioning/create-new-permission-role-1.png) de permissão
* Adicione um **nome** e **descrição** para o novo papel de permissão. O nome e a descrição devem indicar que o papel é para permissões de utilização da API.
  > [!div class="mx-imgBorder"]
  > ![permissão detalhe](./media/sap-successfactors-inbound-provisioning/permission-role-detail.png)
* Sob as definições de Permissão, clique em **Permissão...** em seguida, percorra a lista de permissão e clique em **Gerir Ferramentas**de Integração . Verifique a caixa para permitir que o **Administrador aceda à API OData através**da autenticação básica .
  > [!div class="mx-imgBorder"]
  > ![Gerir ferramentas de integração](./media/sap-successfactors-inbound-provisioning/manage-integration-tools.png)
* Desloque-se para baixo na mesma caixa e selecione **API Central do Empregado**. Adicione permissões como mostrado abaixo para ler usando ODATA API e edite usando API ODATA. Selecione a opção de edição se planeia utilizar a mesma conta para o cenário Writeback to SuccessFactors. 
  > [!div class="mx-imgBorder"]
  > ![Ler permissões](./media/sap-successfactors-inbound-provisioning/odata-read-write-perm.png)

  >[!NOTE]
  >Para a lista completa de atributos recuperados por esta app de provisionamento, consulte a Referência do [Atributo SuccessFactors](../app-provisioning/sap-successfactors-attribute-reference.md)

* Clique em **Done**. Clique em **Guardar Alterações**.

### <a name="create-a-permission-group-for-the-api-user"></a>Criar um Grupo de Permissão para o utilizador da API

* No Centro de Administração SuccessFactors, procure grupos de *permissão de gestão*e, em seguida, selecione **Manage Permission Groups** a partir dos resultados da pesquisa.
  > [!div class="mx-imgBorder"]
  > ![Gerir grupos de permissão](./media/sap-successfactors-inbound-provisioning/manage-permission-groups.png)
* A partir da janela 'Gerir grupos de permissão', clique em **Criar Novo**.
  > [!div class="mx-imgBorder"]
  > ![Adicionar novos](./media/sap-successfactors-inbound-provisioning/create-new-group.png) de grupo
* Adicione um Nome de Grupo para o novo grupo. O nome do grupo deve indicar que o grupo é para utilizadores de API.
  > [!div class="mx-imgBorder"]
  > ![nome do grupo de permissão](./media/sap-successfactors-inbound-provisioning/permission-group-name.png)
* Adicione membros ao grupo. Por exemplo, pode selecionar o **username** do menu de entrega do People Pool e, em seguida, introduzir o nome de utilizador da conta API que será usada para a integração. 
  > [!div class="mx-imgBorder"]
  > ![Adicionar membros do grupo](./media/sap-successfactors-inbound-provisioning/add-group-members.png)
* Clique em **Feito** para terminar de criar o Grupo de Permissões.

### <a name="grant-permission-role-to-the-permission-group"></a>Papel de permissão de concessão para o Grupo de Permissão

* No SuccessFactors Admin Center, procure funções de permissão de *gestão*e, em seguida, selecione **'Gerir funções de permissão'** a partir dos resultados da pesquisa.
* A partir da Lista de Papéis de **Permissão,** selecione o papel que criou para permissões de utilização da API.
* Sob **o Grant este papel para...** , clique em **Adicionar...** botão.
* Selecione **Grupo de Permissões...** a partir do menu suspenso e, em seguida, clique em **Select...** para abrir a janela Grupos para pesquisar e selecionar o grupo acima criado. 
  > [!div class="mx-imgBorder"]
  > ![Adicionar](./media/sap-successfactors-inbound-provisioning/add-permission-group.png) de grupo de permissão
* Reveja a concessão de funções de permissão ao Grupo de Permissão. 
  > [!div class="mx-imgBorder"]
  > ![Papel de Permissão e](./media/sap-successfactors-inbound-provisioning/permission-role-group.png) de pormenor do Grupo
* Clique em **Guardar Alterações**.

## <a name="configuring-user-provisioning-from-successfactors-to-active-directory"></a>Configurar o fornecimento de utilizadores de SuccessFactors para Diretório Ativo

Esta secção fornece passos para o fornecimento de conta de utilizador de SuccessFactors para cada domínio de Diretório Ativo no âmbito da sua integração.

* [Adicione a aplicação de conector de provisionamento e descarregue o Agente de Provisionamento](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent)
* [Instalar e configurar no local agente de provisionamento(s)](#part-2-install-and-configure-on-premises-provisioning-agents)
* [Configure conectividade com SuccessFactors e Diretório Ativo](#part-3-in-the-provisioning-app-configure-connectivity-to-successfactors-and-active-directory)
* [Configure mapeamentos de atributos](#part-4-configure-attribute-mappings)
* [Ativar e lançar o fornecimento de utilizadores](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent"></a>Parte 1: Adicione a aplicação de conector de provisionamento e descarregue o Agente de Provisionamento

**Para configurar SuccessFactors para o fornecimento de DirectórioActivo Ativo:**

1. Ir para <https://portal.azure.com>

2. Na barra de navegação esquerda, **selecione Azure Ative Diretório**

3. Selecione **Aplicações Empresariais,** em **seguida, todas as aplicações**.

4. Selecione **Adicionar uma aplicação**e selecione a categoria **All.**

5. Procure **SuccessFactors para o fornecimento de utilizadores de diretórios ativos**e adicione essa aplicação a partir da galeria.

6. Depois da aplicação ser adicionada e o ecrã de detalhes da aplicação ser mostrado, selecione **Provisioning**

7. Alterar o **modo** **de provisionamento** para **automático**

8. Clique no banner de informação apresentado para descarregar o Agente de Provisionamento. 
   > [!div class="mx-imgBorder"]
   > ![Agente de descarregamento](./media/sap-successfactors-inbound-provisioning/download-pa-agent.png "Descarregue o ecrã do agente")


### <a name="part-2-install-and-configure-on-premises-provisioning-agents"></a>Parte 2: Instalar e configurar no local agente de provisionamento ou s

Para fornecer ao Diretório Ativo no local, o agente de provisionamento deve ser instalado num servidor que tenha .NET 4.7.1+ Enquadramento e acesso à rede ao domínio de Diretório Ativo pretendido.

> [!TIP]
> Pode verificar a versão da estrutura .NET no seu servidor utilizando as instruções [aqui](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed)fornecidas .
> Se o servidor não tiver .NET 4.7.1 ou mais instalado, pode descarregá-lo a partir [daqui](https://support.microsoft.com/help/4033342/the-net-framework-4-7-1-offline-installer-for-windows).  

Transfira o instalador de agente descarregado para o anfitrião do servidor e siga os passos indicados abaixo para completar a configuração do agente.

1. Faça sessão no Windows Server onde pretende instalar o novo agente.

1. Lance o instalador do Agente de Provisionamento, concorde com os termos e clique no botão **Instalar.**

   ![Instalar ecrã](./media/workday-inbound-tutorial/pa_install_screen_1.png "Instalar ecrã")
   
1. Após a instalação estar concluída, o assistente será lançado e verá o ecrã **AD Connect Azure.** Clique no botão **Authenticate** para ligar à sua instância Azure AD.

   ![Ligar AD Azure](./media/workday-inbound-tutorial/pa_install_screen_2.png "Ligar ao Azure AD")
   
1. Autenticar a sua instância De AD Azure usando credenciais de administrador global.

   ![Administrador Auth](./media/workday-inbound-tutorial/pa_install_screen_3.png "Administrador Auth")

   > [!NOTE]
   > As credenciais de administração da Azure AD são usadas apenas para se conectarem ao seu inquilino Azure AD. O agente não armazena as credenciais localmente no servidor.

1. Após autenticação bem sucedida com a AD Azure, verá o ecrã connect **Ative Directy.** Neste passo, introduza o nome de domínio AD e clique no botão **Adicionar Diretório.**

   ![Adicionar Diretório](./media/workday-inbound-tutorial/pa_install_screen_4.png "Adicionar Diretório")
  
1. Será agora solicitado a introduzir as credenciais necessárias para se ligar ao Domínio AD. No mesmo ecrã, pode utilizar a prioridade do controlador de **domínio Select** para especificar os controladores de domínio que o agente deve utilizar para enviar pedidos de fornecimento.

   ![Credenciais de domínio](./media/workday-inbound-tutorial/pa_install_screen_5.png)
   
1. Depois de configurar o domínio, o instalador apresenta uma lista de domínios configurados. Neste ecrã, pode repetir passo #5 e #6 para adicionar mais domínios ou clicar em **Next** para proceder ao registo do agente.

   ![Domínios configurados](./media/workday-inbound-tutorial/pa_install_screen_6.png "Domínios configurados")

   > [!NOTE]
   > Se tiver vários domínios ad (por exemplo, na.contoso.com, emea.contoso.com), adicione cada domínio individualmente à lista.
   > Apenas a adição do domínio dos pais (por exemplo, contoso.com) não é suficiente. Deve registar cada domínio infantil com o agente.
   
1. Reveja os detalhes da configuração e clique em **Confirmar** para registar o agente.
  
   ![Confirmar tela](./media/workday-inbound-tutorial/pa_install_screen_7.png "Confirmar tela")
   
1. O assistente de configuração mostra o progresso do registo do agente.
  
   ![Registo do Agente](./media/workday-inbound-tutorial/pa_install_screen_8.png "Registo do Agente")
   
1. Assim que o registo do agente for bem sucedido, pode clicar na **Saída** para sair do Assistente.
  
   ![Ecrã de saída](./media/workday-inbound-tutorial/pa_install_screen_9.png "Ecrã de saída")
   
1. Verifique a instalação do Agente e certifique-se de que está a funcionar abrindo o Snap-In "Services" e procure o Serviço denominado "Microsoft Azure AD Connect Provisioning Agent"
  
   ![Serviços](./media/workday-inbound-tutorial/services.png)

### <a name="part-3-in-the-provisioning-app-configure-connectivity-to-successfactors-and-active-directory"></a>Parte 3: Na aplicação de provisionamento, configure a conectividade com SuccessFactors e Diretório Ativo
Neste passo, estabelecemos conectividade com SuccessFactors e Ative Directy no portal Azure. 

1. No portal Azure, volte aos SuccessFactors para a App de Provisionamento de Utilizadores de Diretório Ativo criada na [Parte 1](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent)

1. Complete a secção **de Credenciais de Administrador** da seguinte forma:

   * Nome de **utilizador da Administração** – Introduza o nome de utilizador da conta de utilizador da API SuccessFactors, com o ID da empresa anexado. Tem o formato: **username\@companyID**

   * **Senha de administração –** Introduza a palavra-passe da conta de utilizador da API SuccessFactors. 

   * **URL do inquilino –** Insira o nome dos serviços SuccessFactors OData API endpoint. Basta introduzir o nome de anfitrião do servidor sem http ou https. Este valor deve parecer: **<api-server-name>.successfactors.com**.

   * Floresta de **Diretório Ativo -** O "Nome" do seu domínio de Diretório Ativo, registado no agente. Utilize a queda para selecionar o domínio alvo para o fornecimento. Este valor é tipicamente uma corda como: *contoso.com*

   * **Recipiente de Diretório Ativo -** Introduza o DN do recipiente onde o agente deve criar contas de utilizador por padrão.
        Exemplo: *OU=Utilizadores,DC=contoso,DC=com*
        > [!NOTE]
        > Esta definição só entra em jogo para criações de conta de utilizador se o atributo *parentDistinguishedName* não estiver configurado nos mapeamentos do atributo. Esta definição não é utilizada para operações de pesquisa ou atualização do utilizador. Toda a subárvore de domínio cai no âmbito da operação de busca.

   * **Email de notificação –** Insira o seu endereço de e-mail e verifique a caixa de verificação "enviar e-mail se ocorrer falha".
    > [!NOTE]
    > O Serviço de Provisionamento de AD Azure envia notificação por e-mail se o trabalho de provisionamento entrar em estado de [quarentena.](/azure/active-directory/manage-apps/application-provisioning-quarantine-status)

   * Clique no botão **de ligação** de teste. Se o teste de ligação for bem sucedido, clique no botão **Guardar** na parte superior. Se falhar, verifique duas vezes se as credenciais SuccessFactors e as credenciais AD configuradas na configuração do agente são válidas.
    >[!div class="mx-imgBorder"]
    >![Portal do Azure](./media/sap-successfactors-inbound-provisioning/sf2ad-provisioning-creds.png)

   * Uma vez que as credenciais são guardadas com sucesso, a secção **De Mapeamentos** apresentará o mapeamento padrão **Synchronize SuccessFactors Users to On Premises Ative Directory**

### <a name="part-4-configure-attribute-mappings"></a>Parte 4: Configurar mapeamentos de atributos

Nesta secção, irá configurar como os dados dos utilizadores fluem de SuccessFactors para Ative Directory.

1. No separador provisionamento em **Mapeamentos,** clique em **Synchronize SuccessFactors Users to On Premises Ative Directory**.

1. No campo **Source Object Scope,** pode selecionar quais os conjuntos de utilizadores em SuccessFactors que devem estar disponíveis para o fornecimento de AD, definindo um conjunto de filtros baseados em atributos. O âmbito padrão é "todos os utilizadores em SuccessFactors". Filtros de exemplo:

   * Exemplo: Âmbito para utilizadores com personalidadeExternal entre 10000000 e 20000000 (excluindo 20000000)

      * Atributo: personIdExternal

      * Operador: Regex Match

      * Valor: (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * Exemplo: Apenas trabalhadores e não trabalhadores contingentes

      * Atributo: EmployeeID

      * Operador: NÃO É NULO

   > [!TIP]
   > Quando estiver a configurar a aplicação de provisionamento pela primeira vez, terá de testar e verificar os seus mapeamentos e expressões de atributos para se certificar de que está a dar-lhe o resultado desejado. A Microsoft recomenda a utilização dos filtros de deteção no âmbito do Âmbito do **Objeto Fonte** para testar os seus mapeamentos com alguns utilizadores de teste do SuccessFactors. Uma vez verificado que os mapeamentos funcionam, então pode remover o filtro ou expandi-lo gradualmente para incluir mais utilizadores.

   > [!CAUTION] 
   > O comportamento predefinido do motor de provisionamento é desativar/eliminar utilizadores que desapertem o seu alcance. Isto pode não ser desejável nos seus SuccessFactors para a integração de Anúncios. Para anular este comportamento padrão, consulte o artigo [Sem a eliminação das contas dos utilizadores que saem do âmbito](../app-provisioning/skip-out-of-scope-deletions.md)
  
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
> Por predefinição, quando ligar o serviço de provisionamento, iniciará operações de provisionamento para todos os utilizadores no âmbito. Se houver erros nos problemas de dados de mapeamento ou SuccessFactors, então o trabalho de provisionamento pode falhar e ir para o estado de quarentena. Para evitar isto, como uma boa prática, recomendamos configurar o filtro **Source Object Scope** e testar os seus mapeamentos de atributos com alguns utilizadores de teste antes de lançar a sincronização completa para todos os utilizadores. Uma vez verificado que os mapeamentos funcionam e estão a dar-lhe os resultados desejados, então pode remover o filtro ou expandi-lo gradualmente para incluir mais utilizadores.

1. No separador **provisionamento,** coloque o estado de **provisionamento** **ligado**.

2. Clique em **Guardar**.

3. Esta operação iniciará a sincronização inicial, que pode demorar um número variável de horas dependendo de quantos utilizadores estão no inquilino SuccessFactors. Pode verificar a barra de progresso para acompanhar o progresso do ciclo de sincronização. 

4. A qualquer momento, verifique o separador de **registos de auditoria** no portal Azure para ver que ações o serviço de provisionamento realizou. Os registos de auditoria listam todos os eventos de sincronização individuais realizados pelo serviço de provisionamento, tais como os utilizadores que estão a ser lidos fora dos SuccessFactors e, posteriormente, adicionados ou atualizados para o Ative Directory. 

5. Uma vez concluída a sincronização inicial, escreverá um relatório resumo de auditoria no separador **Provisioning,** como mostrado abaixo.

   > [!div class="mx-imgBorder"]
   > ![provisionamento de](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png) de progresso

## <a name="next-steps"></a>Passos Seguintes

* [Saiba mais sobre os Atributos de Sucesso suportados para o fornecimento de entrada](../app-provisioning/sap-successfactors-attribute-reference.md)
* [Saiba como configurar a redação de e-mail para SuccessFactors](sap-successfactors-writeback-tutorial.md)
* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
* [Saiba como configurar um único sign-on entre SuccessFactors e Azure Ative Directory](successfactors-tutorial.md)
* [Saiba como integrar outras aplicações do SaaS com o Diretório Ativo Azure](tutorial-list.md)
* [Saiba como exportar e importar as suas configurações de provisionamento](../app-provisioning/export-import-provisioning-configuration.md)
