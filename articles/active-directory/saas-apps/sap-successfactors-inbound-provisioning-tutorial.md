---
title: 'Tutorial: configurar o provisionamento de entrada do SuccessFactors no Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o provisionamento de entrada do SuccessFactors
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
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77060232"
---
# <a name="tutorial-configure-sap-successfactors-to-active-directory-user-provisioning-preview"></a>Tutorial: configurar o SAP SuccessFactors para Active Directory provisionamento de usuário (versão prévia)
O objetivo deste tutorial é mostrar as etapas que você precisa executar para provisionar usuários do SuccessFactors Employee central no Active Directory (AD) e no Azure AD, com write-back opcional de endereço de email para SuccessFactors. Esta integração encontra-se na pré-visualização pública e suporta a recuperação de mais de [70 atributos](../app-provisioning/sap-successfactors-attribute-reference.md) de utilizadores da SuccessFactors Employee Central.

>[!NOTE]
>Use este tutorial se os usuários que você deseja provisionar do SuccessFactors precisarem de uma conta do AD local e, opcionalmente, uma conta do Azure AD. Se os utilizadores do SuccessFactors apenas precisarem de conta AD Azure (utilizadores apenas na nuvem), consulte o tutorial sobre [configurar o SAP SuccessFactors para](sap-successfactors-inbound-provisioning-cloud-only-tutorial.md) o fornecimento de utilizadores da AD Azure. 


## <a name="overview"></a>Descrição geral

O serviço de prestação de [utilizadores do Azure Ative Directory](../app-provisioning/user-provisioning.md) integra-se com a [Central de Colaboradores SuccessFactors](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html) de forma a gerir o ciclo de vida de identidade dos utilizadores. 

Os fluxos de trabalho de provisionamento de usuário SuccessFactors com suporte do serviço de provisionamento de usuário do Azure AD habilitam a automação dos seguintes cenários de gerenciamento de ciclo de vida de identidade e recursos humanos:

* **Contratação** de novos colaboradores - Quando um novo colaborador é adicionado ao SuccessFactors, uma conta de utilizador é automaticamente criada em Ative Directory, Azure Ative Directory, e opcionalmente office 365 e [outras aplicações SaaS suportadas pela Azure AD,](../app-provisioning/user-provisioning.md)com redação do endereço de e-mail para SuccessFactors.

* **Atributos e atualizações** de perfis dos colaboradores - Quando um registo de empregados é atualizado em SuccessFactors (como o seu nome, título ou gestor), a sua conta de utilizador será automaticamente atualizada em Diretório Ativo, Diretório Ativo Azure e opcionalmente Office 365 e [outras aplicações SaaS suportadas pela Azure AD](../app-provisioning/user-provisioning.md).

* **Rescisões** de funcionários - Quando um empregado é despedido em SuccessFactors, a sua conta de utilizador é automaticamente desativada em Diretório Ativo, Diretório Ativo Azure e opcionalmente Office 365 e [outras aplicações SaaS suportadas pela Azure AD.](../app-provisioning/user-provisioning.md)

* **Recontratações** de colaboradores - Quando um empregado é recontratado em SuccessFactors, a sua conta antiga pode ser automaticamente reativada ou represtada (dependendo da sua preferência) ao Ative Directory, Ao Diretório Ativo azure e opcionalmente ao Office 365 e [outras aplicações SaaS apoiadas pela Azure AD.](../app-provisioning/user-provisioning.md)

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>A qual esta solução de provisionamento de usuário mais adequada?

Esse SuccessFactors para Active Directory solução de provisionamento de usuário é ideal para:

* Organizações que desejam uma solução predefinida baseada em nuvem para provisionamento de usuário SuccessFactors

* Organizações que exigem o provisionamento direto de usuários do SuccessFactors para o Active Directory

* Organizações que exigem que os utilizadores sejam aprovisionados utilizando dados obtidos a partir da Central de Empregados de [SucessoFactors (CE)](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html)

* Organizações que exigem a adesão, mudança e deixando os utilizadores sincronizados com uma ou mais Florestas de Diretórios Ativos, Domínios e OUs com base apenas em informações de mudança detetadas na [SuccessFactors Employee Central (CE)](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html)

* Organizações que usam o Office 365 para email

## <a name="solution-architecture"></a>Arquitetura de Soluções

Esta seção descreve a arquitetura da solução de provisionamento de usuário de ponta a ponta para ambientes híbridos comuns. Há dois fluxos relacionados:

* **Fluxo de dados de RH autoritário – desde SuccessFactors até diretório ativo no local:** Neste fluxo de eventos de trabalhadores (tais como Novas Contratações, Transferências, Rescisões) ocorrem primeiro na nuvem SuccessFactors Employee Central e, em seguida, os dados do evento fluem para o diretório ativo no local através da Azure AD e do Agente de Provisionamento. Dependendo do evento, ele pode levar a criar/atualizar/habilitar/desabilitar operações no AD.
* **Fluxo de Writeback por e-mail – do Diretório Ativo no local para SuccessFactors:** Uma vez que a criação da conta esteja completa no Ative Directory, é sincronizada com o Azure AD AD sync e o atributo de e-mail pode ser reescrito para SuccessFactors.

  ![Descrição geral](./media/sap-successfactors-inbound-provisioning/sf2ad-overview.png)

### <a name="end-to-end-user-data-flow"></a>Fluxo de dados do usuário de ponta a ponta

1. A equipe de RH executa transações de trabalho (junções/movimentadores/pertraçãos ou novas contratações/transferências/encerramentos) no SuccessFactors Employee central
2. O serviço de provisionamento do Azure AD executa sincronizações agendadas de identidades do SuccessFactors EC e identifica as alterações que precisam ser processadas para sincronização com Active Directory locais.
3. O serviço de provisionamento do Azure AD invoca o agente de provisionamento local Azure AD Connect com uma carga de solicitação que contém as operações criar/atualizar/habilitar/desabilitar conta do AD.
4. O agente de provisionamento do Azure AD Connect usa uma conta de serviço para adicionar/atualizar dados da conta do AD.
5. O mecanismo de sincronização Azure AD Connect executa a sincronização Delta para efetuar pull de atualizações no AD.
6. As atualizações de Active Directory são sincronizadas com Azure Active Directory.
7. Se a [aplicação SuccessFactors Writeback](sap-successfactors-writeback-tutorial.md) estiver configurada, escreve atributo de e-mail de volta para SuccessFactors, com base no atributo correspondente utilizado.

## <a name="planning-your-deployment"></a>Planejando a implantação

A configuração do provisionamento de usuário controlado por RH na nuvem do SuccessFactors para o AD requer um planejamento considerável que abrange diferentes aspectos, como:
* Instalação do agente de provisionamento do Azure AD Connect 
* Número de SuccessFactors para aplicativos de provisionamento de usuário do AD a serem implantados
* ID de correspondência, mapeamento de atributo, transformação e filtros de escopo

Consulte o plano de [implantação](../app-provisioning/plan-cloud-hr-provision.md) de RH em nuvem para diretrizes abrangentes em torno destes tópicos. 

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

  >[!NOTE]
  >Para a lista completa de atributos recuperados por esta app de provisionamento, consulte a Referência do [Atributo SuccessFactors](../app-provisioning/sap-successfactors-attribute-reference.md)

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

## <a name="configuring-user-provisioning-from-successfactors-to-active-directory"></a>Configurando o provisionamento de usuário do SuccessFactors para o Active Directory

Esta seção fornece etapas para o provisionamento de contas de usuário do SuccessFactors para cada domínio Active Directory dentro do escopo da sua integração.

* [Adicione a aplicação de conector de provisionamento e descarregue o Agente de Provisionamento](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent)
* [Instalar e configurar no local agente de provisionamento(s)](#part-2-install-and-configure-on-premises-provisioning-agents)
* [Configure conectividade com SuccessFactors e Diretório Ativo](#part-3-in-the-provisioning-app-configure-connectivity-to-successfactors-and-active-directory)
* [Configure mapeamentos de atributos](#part-4-configure-attribute-mappings)
* [Ativar e lançar o fornecimento de utilizadores](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent"></a>Parte 1: Adicionar o aplicativo do conector de provisionamento e baixar o agente de provisionamento

**Para configurar SuccessFactors para o fornecimento de DirectórioActivo Ativo:**

1. Ir para <https://portal.azure.com>

2. Na barra de navegação esquerda, **selecione Azure Ative Diretório**

3. Selecione **Aplicações Empresariais,** em **seguida, todas as aplicações**.

4. Selecione **Adicionar uma aplicação**e selecione a categoria **All.**

5. Procure **SuccessFactors para o fornecimento de utilizadores de diretórios ativos**e adicione essa aplicação a partir da galeria.

6. Depois da aplicação ser adicionada e o ecrã de detalhes da aplicação ser mostrado, selecione **Provisioning**

7. Alterar o **modo** **de provisionamento** para **automático**

8. Clique na faixa de informações exibida para baixar o agente de provisionamento. 
   > [!div class="mx-imgBorder"]
   > ![Agente de descarregamento](./media/sap-successfactors-inbound-provisioning/download-pa-agent.png "Tela de download do agente")


### <a name="part-2-install-and-configure-on-premises-provisioning-agents"></a>Parte 2: instalar e configurar agente (s) de provisionamento local

Para provisionar para Active Directory local, o agente de provisionamento deve ser instalado em um servidor que tenha o .NET 4.7.1 + estrutura e acesso de rede aos domínios de Active Directory desejados.

> [!TIP]
> Pode verificar a versão da estrutura .NET no seu servidor utilizando as instruções [aqui](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed)fornecidas .
> Se o servidor não tiver .NET 4.7.1 ou mais instalado, pode descarregá-lo a partir [daqui](https://support.microsoft.com/help/4033342/the-net-framework-4-7-1-offline-installer-for-windows).  

Transfira o instalador do agente baixado para o host do servidor e siga as etapas fornecidas abaixo para concluir a configuração do agente.

1. Entre no Windows Server em que você deseja instalar o novo agente.

1. Lance o instalador do Agente de Provisionamento, concorde com os termos e clique no botão **Instalar.**

   ![Instalar ecrã](./media/workday-inbound-tutorial/pa_install_screen_1.png "Tela de instalação")
   
1. Após a instalação estar concluída, o assistente será lançado e verá o ecrã **AD Connect Azure.** Clique no botão **Authenticate** para ligar à sua instância Azure AD.

   ![Ligar AD Azure](./media/workday-inbound-tutorial/pa_install_screen_2.png "Ligar ao Azure AD")
   
1. Autentique em sua instância do Azure AD usando credenciais de administrador global.

   ![Administrador Auth](./media/workday-inbound-tutorial/pa_install_screen_3.png "Autenticação de administrador")

   > [!NOTE]
   > As credenciais de administrador do Azure AD são usadas somente para se conectar ao seu locatário do Azure AD. O agente não armazena as credenciais localmente no servidor.

1. Após autenticação bem sucedida com a AD Azure, verá o ecrã connect **Ative Directy.** Neste passo, introduza o nome de domínio AD e clique no botão **Adicionar Diretório.**

   ![Adicionar Diretório](./media/workday-inbound-tutorial/pa_install_screen_4.png "Adicionar diretório")
  
1. Agora, você será solicitado a inserir as credenciais necessárias para se conectar ao domínio do AD. No mesmo ecrã, pode utilizar a prioridade do controlador de **domínio Select** para especificar os controladores de domínio que o agente deve utilizar para enviar pedidos de fornecimento.

   ![Credenciais de domínio](./media/workday-inbound-tutorial/pa_install_screen_5.png)
   
1. Depois de configurar o domínio, o instalador exibirá uma lista de domínios configurados. Neste ecrã, pode repetir passo #5 e #6 para adicionar mais domínios ou clicar em **Next** para proceder ao registo do agente.

   ![Domínios configurados](./media/workday-inbound-tutorial/pa_install_screen_6.png "Domínios configurados")

   > [!NOTE]
   > Se você tiver vários domínios do AD (por exemplo, na.contoso.com, emea.contoso.com), adicione cada domínio individualmente à lista.
   > Somente a adição do domínio pai (por exemplo, contoso.com) não é suficiente. Você deve registrar cada domínio filho com o agente.
   
1. Reveja os detalhes da configuração e clique em **Confirmar** para registar o agente.
  
   ![Confirmar tela](./media/workday-inbound-tutorial/pa_install_screen_7.png "Confirmar tela")
   
1. O assistente de configuração exibe o progresso do registro do agente.
  
   ![Registo do Agente](./media/workday-inbound-tutorial/pa_install_screen_8.png "Registro do agente")
   
1. Assim que o registo do agente for bem sucedido, pode clicar na **Saída** para sair do Assistente.
  
   ![Ecrã de saída](./media/workday-inbound-tutorial/pa_install_screen_9.png "Sair da tela")
   
1. Verifique a instalação do agente e verifique se ele está em execução abrindo o snap-in "serviços" e procure o serviço chamado "agente de provisionamento do Microsoft Azure AD Connect"
  
   ![Serviços](./media/workday-inbound-tutorial/services.png)

### <a name="part-3-in-the-provisioning-app-configure-connectivity-to-successfactors-and-active-directory"></a>Parte 3: no aplicativo de provisionamento, configure a conectividade com o SuccessFactors e o Active Directory
Nesta etapa, estabelecemos a conectividade com SuccessFactors e Active Directory no portal do Azure. 

1. No portal Azure, volte aos SuccessFactors para a App de Provisionamento de Utilizadores de Diretório Ativo criada na [Parte 1](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent)

1. Complete a secção **de Credenciais de Administrador** da seguinte forma:

   * Nome de **utilizador da Administração** – Introduza o nome de utilizador da conta de utilizador da API SuccessFactors, com o ID da empresa anexado. Tem o formato: **username\@companyID**

   * **Senha de administração –** Introduza a palavra-passe da conta de utilizador da API SuccessFactors. 

   * **URL do inquilino –** Insira o nome dos serviços SuccessFactors OData API endpoint. Insira apenas o nome do host do servidor sem http ou HTTPS. Este valor deve parecer: **<api-server-name>.successfactors.com**.

   * Floresta de **Diretório Ativo -** O "Nome" do seu domínio de Diretório Ativo, registado no agente. Use a lista suspensa para selecionar o domínio de destino para provisionamento. Este valor é tipicamente uma corda como: *contoso.com*

   * **Recipiente de Diretório Ativo -** Introduza o DN do recipiente onde o agente deve criar contas de utilizador por padrão.
        Exemplo: *OU=Utilizadores,DC=contoso,DC=com*
        > [!NOTE]
        > Esta definição só entra em jogo para criações de conta de utilizador se o atributo *parentDistinguishedName* não estiver configurado nos mapeamentos do atributo. Essa configuração não é usada para operações de pesquisa ou atualização de usuário. A subárvore de domínio inteira cai no escopo da operação de pesquisa.

   * **Email de notificação –** Insira o seu endereço de e-mail e verifique a caixa de verificação "enviar e-mail se ocorrer falha".
    > [!NOTE]
    > O Serviço de Provisionamento de AD Azure envia notificação por e-mail se o trabalho de provisionamento entrar em estado de [quarentena.](/azure/active-directory/manage-apps/application-provisioning-quarantine-status)

   * Clique no botão **de ligação** de teste. Se o teste de ligação for bem sucedido, clique no botão **Guardar** na parte superior. Se ele falhar, verifique se as credenciais do SuccessFactors e as credenciais do AD configuradas na instalação do agente são válidas.
    >[!div class="mx-imgBorder"]
    >![Portal do Azure](./media/sap-successfactors-inbound-provisioning/sf2ad-provisioning-creds.png)

   * Uma vez que as credenciais são guardadas com sucesso, a secção **De Mapeamentos** apresentará o mapeamento padrão **Synchronize SuccessFactors Users to On Premises Ative Directory**

### <a name="part-4-configure-attribute-mappings"></a>Parte 4: configurar mapeamentos de atributo

Nesta seção, você configurará como os dados do usuário fluem do SuccessFactors para o Active Directory.

1. No separador provisionamento em **Mapeamentos,** clique em **Synchronize SuccessFactors Users to On Premises Ative Directory**.

1. No campo **Source Object Scope,** pode selecionar quais os conjuntos de utilizadores em SuccessFactors que devem estar disponíveis para o fornecimento de AD, definindo um conjunto de filtros baseados em atributos. O escopo padrão é "todos os usuários em SuccessFactors". Filtros de exemplo:

   * Exemplo: escopo para usuários com personIdExternal entre 1 milhão e 2 milhões (exceto 2 milhões)

      * Atributo: personIdExternal

      * Operador: correspondência de REGEX

      * Valor: (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * Exemplo: somente funcionários e não usuários contingentes

      * Atributo: EmployeeID

      * Operador: não é nulo

   > [!TIP]
   > Quando você estiver configurando o aplicativo de provisionamento pela primeira vez, será necessário testar e verificar os mapeamentos e as expressões de atributo para garantir que ele esteja dando o resultado desejado. A Microsoft recomenda a utilização dos filtros de deteção no âmbito do Âmbito do **Objeto Fonte** para testar os seus mapeamentos com alguns utilizadores de teste do SuccessFactors. Depois de verificar se os mapeamentos funcionam, você pode remover o filtro ou expandi-lo gradualmente para incluir mais usuários.

   > [!CAUTION] 
   > O comportamento padrão do mecanismo de provisionamento é desabilitar/excluir usuários que saem do escopo. Isso pode não ser desejável em sua integração do SuccessFactors com o AD. Para anular este comportamento padrão, consulte o artigo [Sem a eliminação das contas dos utilizadores que saem do âmbito](../app-provisioning/skip-out-of-scope-deletions.md)
  
1. No campo **Target Object Actions,** pode filtrar globalmente quais as ações que são realizadas no Diretório Ativo. **Criar** e **Atualizar** são mais comuns.

1. Na secção de **mapeamento sacar,** pode definir como os SuccessFactors individuais atribuem mapa aos atributos do Diretório Ativo.

  >[!NOTE]
  >Para a lista completa de Atributos De SucessoSuportados pela aplicação, consulte a Referência do [Atributo SuccessFactors](../app-provisioning/sap-successfactors-attribute-reference.md)


1. Clique num mapeamento de atributoexistente para atualizá-lo, ou clique **Em adicionar novo mapeamento** na parte inferior do ecrã para adicionar novos mapeamentos. Um mapeamento de atributo individual oferece suporte a essas propriedades:

      * **Tipo de mapeamento**

         * **Direto** – Escreve o valor dos SuccessFactors atribuiao atributo aD, sem alterações

         * **Constante** - Escreva um valor de corda estático e constante para o atributo AD

         * **Expressão** – Permite-lhe escrever um valor personalizado para o atributo AD, com base em um ou mais atributos SuccessFactors. [Para mais informações, consulte este artigo sobre expressões.](../app-provisioning/functions-for-customizing-application-data.md)

      * **Atributo de origem** - O atributo do utilizador dos SuccessFactors

      * **Valor predefinido** – Opcional. Se o atributo de origem tiver um valor vazio, o mapeamento gravará esse valor em vez disso.
            A configuração mais comum é deixar em branco.

      * **Atributo-alvo** – O atributo do utilizador no Diretório Ativo.

      * **Combine objetos usando este atributo** – Se este mapeamento deve ou não ser usado para identificar exclusivamente os utilizadores entre SuccessFactors e Ative Directy. Esse valor é normalmente definido no campo ID de trabalho para SuccessFactors, que normalmente é mapeado para um dos atributos de ID de funcionário em Active Directory.

      * **Precedência correspondente** – Podem ser definidos múltiplos atributos correspondentes. Quando há vários, eles são avaliados na ordem definida por esse campo. Assim que uma correspondência for encontrada, nenhum atributo correspondente será avaliado.

      * **Aplique este mapeamento**

         * **Sempre** – Aplique este mapeamento tanto na criação do utilizador como nas ações de atualização

         * **Apenas durante** a criação - Aplicar este mapeamento apenas em ações de criação de utilizadores

1. Para guardar os seus mapeamentos, clique em **Guardar** no topo da secção Attribute-Mapping.

Uma vez concluída a configuração de mapeamento do atributo, pode agora [ativar e lançar o serviço](#enable-and-launch-user-provisioning)de fornecimento do utilizador .

## <a name="enable-and-launch-user-provisioning"></a>Habilitar e iniciar o provisionamento de usuário

Depois que as configurações do aplicativo de provisionamento do SuccessFactors tiverem sido concluídas, você poderá ativar o serviço de provisionamento no portal do Azure.

> [!TIP]
> Por padrão, quando você ativa o serviço de provisionamento, ele inicia as operações de provisionamento para todos os usuários no escopo. Se houver erros no mapeamento ou problemas de dados de SuccessFactors, o trabalho de provisionamento poderá falhar e entrar no estado de quarentena. Para evitar isto, como uma boa prática, recomendamos configurar o filtro **Source Object Scope** e testar os seus mapeamentos de atributos com alguns utilizadores de teste antes de lançar a sincronização completa para todos os utilizadores. Depois de verificar se os mapeamentos funcionam e estão fornecendo os resultados desejados, você pode remover o filtro ou expandi-lo gradualmente para incluir mais usuários.

1. No separador **provisionamento,** coloque o estado de **provisionamento** **ligado**.

2. Clique em **Guardar**.

3. Esta operação iniciará a sincronização inicial, que pode usar um número variável de horas dependendo de quantos usuários estiverem no locatário SuccessFactors. Você pode verificar a barra de progresso para acompanhar o progresso do ciclo de sincronização. 

4. A qualquer momento, verifique o separador de **registos de auditoria** no portal Azure para ver que ações o serviço de provisionamento realizou. Os logs de auditoria listam todos os eventos de sincronização individuais executados pelo serviço de provisionamento, como quais usuários estão sendo lidos de SuccessFactors e, posteriormente, adicionados ou atualizados para Active Directory. 

5. Uma vez concluída a sincronização inicial, escreverá um relatório resumo de auditoria no separador **Provisioning,** como mostrado abaixo.

   > [!div class="mx-imgBorder"]
   > ![provisionamento de](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png) de progresso

## <a name="next-steps"></a>Passos seguintes

* [Saiba mais sobre os Atributos de Sucesso suportados para o fornecimento de entrada](../app-provisioning/sap-successfactors-attribute-reference.md)
* [Saiba como configurar a redação de e-mail para SuccessFactors](sap-successfactors-writeback-tutorial.md)
* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
* [Saiba como configurar um único sign-on entre SuccessFactors e Azure Ative Directory](successfactors-tutorial.md)
* [Saiba como integrar outras aplicações do SaaS com o Diretório Ativo Azure](tutorial-list.md)
* [Saiba como exportar e importar as suas configurações de provisionamento](../app-provisioning/export-import-provisioning-configuration.md)
