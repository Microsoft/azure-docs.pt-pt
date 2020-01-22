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
ms.openlocfilehash: c2a699a9fafdba60fb2a938fd4691c291562fbc5
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2020
ms.locfileid: "76292523"
---
# <a name="tutorial-configure-sap-successfactors-to-active-directory-user-provisioning-preview"></a>Tutorial: configurar o SAP SuccessFactors para Active Directory provisionamento de usuário (versão prévia)
O objetivo deste tutorial é mostrar as etapas que você precisa executar para provisionar usuários do SuccessFactors Employee central no Active Directory (AD) e no Azure AD, com write-back opcional de endereço de email para SuccessFactors. Essa integração está em visualização pública e dá suporte à recuperação de mais de [70 atributos de usuário](../manage-apps/sap-successfactors-attribute-reference.md) do SuccessFactors Employee central.

>[!NOTE]
>Use este tutorial se os usuários que você deseja provisionar do SuccessFactors precisarem de uma conta do AD local e, opcionalmente, uma conta do Azure AD. Se os usuários de SuccessFactors precisarem apenas de uma conta do Azure AD (usuários somente em nuvem), consulte o tutorial em [Configurar o SAP SuccessFactors para o provisionamento de usuário do Azure ad](sap-successfactors-inbound-provisioning-cloud-only-tutorial.md) . 


## <a name="overview"></a>Visão geral

O [serviço de provisionamento de usuário Azure Active Directory](../manage-apps/user-provisioning.md) se integra ao [SuccessFactors Employee central](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html) para gerenciar o ciclo de vida da identidade dos usuários. 

Os fluxos de trabalho de provisionamento de usuário SuccessFactors com suporte do serviço de provisionamento de usuário do Azure AD habilitam a automação dos seguintes cenários de gerenciamento de ciclo de vida de identidade e recursos humanos:

* **Contratando novos funcionários** – quando um novo funcionário é adicionado ao SuccessFactors, uma conta de usuário é criada automaticamente em Active Directory, Azure Active Directory e, opcionalmente, o Office 365 e [outros aplicativos SaaS com suporte do Azure ad](../manage-apps/user-provisioning.md), com write-back do endereço de email para SuccessFactors.

* **Atualizações de perfil e atributo de funcionário** – quando um registro de funcionário é atualizado em SuccessFactors (como seu nome, título ou gerente), sua conta de usuário será atualizada automaticamente em Active Directory, Azure Active Directory e, opcionalmente, no Office 365 e em [outros aplicativos SaaS com suporte do Azure ad](../manage-apps/user-provisioning.md).

* **Encerramentos de funcionários** – quando um funcionário é encerrado no SuccessFactors, sua conta de usuário é automaticamente desabilitada em Active Directory, Azure Active Directory e, opcionalmente, o Office 365 e [outros aplicativos SaaS com suporte do Azure ad](../manage-apps/user-provisioning.md).

* **Recontratação de funcionários** -quando um funcionário é recontratado no SuccessFactors, sua conta antiga pode ser reativada automaticamente ou reprovisionada (dependendo da sua preferência) para Active Directory, Azure Active Directory e, opcionalmente, o Office 365 e [outros aplicativos SaaS com suporte do Azure ad](../manage-apps/user-provisioning.md).

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>A qual esta solução de provisionamento de usuário mais adequada?

Esse SuccessFactors para Active Directory solução de provisionamento de usuário é ideal para:

* Organizações que desejam uma solução predefinida baseada em nuvem para provisionamento de usuário SuccessFactors

* Organizações que exigem o provisionamento direto de usuários do SuccessFactors para o Active Directory

* Organizações que exigem que os usuários sejam provisionados usando os dados obtidos do [SuccessFactors Employee central (EC)](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html)

* Organizações que exigem ingressar, mover e deixar os usuários serem sincronizados com um ou mais Active Directory florestas, domínios e UOs com base apenas nas informações de alteração detectadas no [SuccessFactors Employee central (EC)](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html)

* Organizações que usam o Office 365 para email

## <a name="solution-architecture"></a>Arquitetura da Solução

Esta seção descreve a arquitetura da solução de provisionamento de usuário de ponta a ponta para ambientes híbridos comuns. Há dois fluxos relacionados:

* **Fluxo de dados de RH autoritativo – de SuccessFactors para local Active Directory:** Nesse fluxo, eventos de trabalho (como novas contratações, transferências, terminações) ocorrem pela primeira vez no Cloud SuccessFactors funcionário central e, em seguida, os dados de evento fluem para Active Directory locais por meio do Azure AD e do agente de provisionamento. Dependendo do evento, ele pode levar a criar/atualizar/habilitar/desabilitar operações no AD.
* **Fluxo de write-back de email – do Active Directory local para SuccessFactors:** Depois que a criação da conta for concluída em Active Directory, ela será sincronizada com o Azure AD por meio do Azure AD Connect sincronização e o atributo de email poderá ser gravado no SuccessFactors.

  ![Visão geral](./media/sap-successfactors-inbound-provisioning/sf2ad-overview.png)

### <a name="end-to-end-user-data-flow"></a>Fluxo de dados do usuário de ponta a ponta

1. A equipe de RH executa transações de trabalho (junções/movimentadores/pertraçãos ou novas contratações/transferências/encerramentos) no SuccessFactors Employee central
2. O serviço de provisionamento do Azure AD executa sincronizações agendadas de identidades do SuccessFactors EC e identifica as alterações que precisam ser processadas para sincronização com Active Directory locais.
3. O serviço de provisionamento do Azure AD invoca o agente de provisionamento local Azure AD Connect com uma carga de solicitação que contém as operações criar/atualizar/habilitar/desabilitar conta do AD.
4. O agente de provisionamento do Azure AD Connect usa uma conta de serviço para adicionar/atualizar dados da conta do AD.
5. O mecanismo de sincronização Azure AD Connect executa a sincronização Delta para efetuar pull de atualizações no AD.
6. As atualizações de Active Directory são sincronizadas com Azure Active Directory.
7. Se o [aplicativo SuccessFactors write-back](sap-successfactors-writeback-tutorial.md) estiver configurado, ele gravará o atributo de email novamente em SuccessFactors, com base no atributo correspondente usado.

## <a name="planning-your-deployment"></a>Planejando a implantação

A configuração do provisionamento de usuário controlado por RH na nuvem do SuccessFactors para o AD requer um planejamento considerável que abrange diferentes aspectos, como:
* Instalação do agente de provisionamento do Azure AD Connect 
* Número de SuccessFactors para aplicativos de provisionamento de usuário do AD a serem implantados
* ID de correspondência, mapeamento de atributo, transformação e filtros de escopo

Consulte o plano de [implantação de RH de nuvem](../manage-apps/plan-cloud-hr-provision.md) para obter diretrizes abrangentes sobre esses tópicos. 

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

  >[!NOTE]
  >Para obter a lista completa de atributos recuperados por este aplicativo de provisionamento, consulte a [referência de atributo SuccessFactors](../manage-apps/sap-successfactors-attribute-reference.md)

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

## <a name="configuring-user-provisioning-from-successfactors-to-active-directory"></a>Configurando o provisionamento de usuário do SuccessFactors para o Active Directory

Esta seção fornece etapas para o provisionamento de contas de usuário do SuccessFactors para cada domínio Active Directory dentro do escopo da sua integração.

* [Adicionar o aplicativo do conector de provisionamento e baixar o agente de provisionamento](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent)
* [Instalar e configurar agente (s) de provisionamento local](#part-2-install-and-configure-on-premises-provisioning-agents)
* [Configurar a conectividade com o SuccessFactors e o Active Directory](#part-3-in-the-provisioning-app-configure-connectivity-to-successfactors-and-active-directory)
* [Configurar mapeamentos de atributo](#part-4-configure-attribute-mappings)
* [Habilitar e iniciar o provisionamento de usuário](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent"></a>Parte 1: Adicionar o aplicativo do conector de provisionamento e baixar o agente de provisionamento

**Para configurar o SuccessFactors para Active Directory provisionamento:**

1. Ir para <https://portal.azure.com>

2. Na barra de navegação à esquerda, selecione **Azure Active Directory**

3. Selecione **aplicativos empresariais**e **todos os aplicativos**.

4. Selecione **Adicionar um aplicativo**e selecione a categoria **tudo** .

5. Pesquise **SuccessFactors para Active Directory provisionamento de usuário**e adicione esse aplicativo da galeria.

6. Depois que o aplicativo for adicionado e a tela de detalhes do aplicativo for exibida, selecione **provisionamento**

7. Alterar o **modo** de provisionamento para **automático**

8. Clique na faixa de informações exibida para baixar o agente de provisionamento. 
   > [!div class="mx-imgBorder"]
   > ![Agente de download](./media/sap-successfactors-inbound-provisioning/download-pa-agent.png "Tela de download do agente")


### <a name="part-2-install-and-configure-on-premises-provisioning-agents"></a>Parte 2: instalar e configurar agente (s) de provisionamento local

Para provisionar para Active Directory local, o agente de provisionamento deve ser instalado em um servidor que tenha o .NET 4.7.1 + estrutura e acesso de rede aos domínios de Active Directory desejados.

> [!TIP]
> Você pode verificar a versão do .NET Framework no seu servidor usando as instruções fornecidas [aqui](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed).
> Se o servidor não tiver o .NET 4.7.1 ou superior instalado, você poderá baixá-lo [aqui](https://support.microsoft.com/help/4033342/the-net-framework-4-7-1-offline-installer-for-windows).  

Transfira o instalador do agente baixado para o host do servidor e siga as etapas fornecidas abaixo para concluir a configuração do agente.

1. Entre no Windows Server em que você deseja instalar o novo agente.

1. Inicie o instalador do agente de provisionamento, concorde com os termos e clique no botão **instalar** .

   ![Tela de instalação](./media/workday-inbound-tutorial/pa_install_screen_1.png "Tela de instalação")
   
1. Após a conclusão da instalação, o assistente será iniciado e você verá a tela **conectar o Azure ad** . Clique no botão **autenticar** para se conectar à sua instância do Azure AD.

   ![Conectar o Azure AD](./media/workday-inbound-tutorial/pa_install_screen_2.png "Ligar ao Azure AD")
   
1. Autentique em sua instância do Azure AD usando credenciais de administrador global.

   ![Autenticação de administrador](./media/workday-inbound-tutorial/pa_install_screen_3.png "Autenticação de administrador")

   > [!NOTE]
   > As credenciais de administrador do Azure AD são usadas somente para se conectar ao seu locatário do Azure AD. O agente não armazena as credenciais localmente no servidor.

1. Após a autenticação bem-sucedida com o Azure AD, você verá a tela **conectar Active Directory** . Nesta etapa, insira o nome de domínio do AD e clique no botão **Adicionar diretório** .

   ![Adicionar diretório](./media/workday-inbound-tutorial/pa_install_screen_4.png "Adicionar diretório")
  
1. Agora, você será solicitado a inserir as credenciais necessárias para se conectar ao domínio do AD. Na mesma tela, você pode usar a **prioridade selecionar controlador de domínio** para especificar os controladores de domínio que o agente deve usar para enviar solicitações de provisionamento.

   ![Credenciais de domínio](./media/workday-inbound-tutorial/pa_install_screen_5.png)
   
1. Depois de configurar o domínio, o instalador exibirá uma lista de domínios configurados. Nessa tela, você pode repetir a etapa #5 e #6 para adicionar mais domínios ou clicar em **Avançar** para prosseguir para o registro do agente.

   ![Domínios configurados](./media/workday-inbound-tutorial/pa_install_screen_6.png "Domínios configurados")

   > [!NOTE]
   > Se você tiver vários domínios do AD (por exemplo, na.contoso.com, emea.contoso.com), adicione cada domínio individualmente à lista.
   > Somente a adição do domínio pai (por exemplo, contoso.com) não é suficiente. Você deve registrar cada domínio filho com o agente.
   
1. Examine os detalhes de configuração e clique em **confirmar** para registrar o agente.
  
   ![Confirmar tela](./media/workday-inbound-tutorial/pa_install_screen_7.png "Confirmar tela")
   
1. O assistente de configuração exibe o progresso do registro do agente.
  
   ![Registro do agente](./media/workday-inbound-tutorial/pa_install_screen_8.png "Registro do agente")
   
1. Quando o registro do agente for bem-sucedido, você poderá clicar em **sair** para sair do assistente.
  
   ![Sair da tela](./media/workday-inbound-tutorial/pa_install_screen_9.png "Sair da tela")
   
1. Verifique a instalação do agente e verifique se ele está em execução abrindo o snap-in "serviços" e procure o serviço chamado "agente de provisionamento do Microsoft Azure AD Connect"
  
   ![Serviços](./media/workday-inbound-tutorial/services.png)

### <a name="part-3-in-the-provisioning-app-configure-connectivity-to-successfactors-and-active-directory"></a>Parte 3: no aplicativo de provisionamento, configure a conectividade com o SuccessFactors e o Active Directory
Nesta etapa, estabelecemos a conectividade com SuccessFactors e Active Directory no portal do Azure. 

1. Na portal do Azure, volte para o SuccessFactors para Active Directory aplicativo de provisionamento de usuário criado na [parte 1](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent)

1. Conclua a seção de **credenciais de administrador** da seguinte maneira:

   * **Nome** de usuário do administrador – insira o nome de usu of the SuccessFactors API User Account, com a ID da empresa anexada. Ele tem o formato: **username\@CompanyID**

   * **Senha de administrador –** Insira a senha da conta de usuário da API do SuccessFactors. 

   * **URL do locatário –** Insira o nome do ponto de extremidade dos serviços de API OData do SuccessFactors. Insira apenas o nome do host do servidor sem http ou HTTPS. Esse valor deve ser semelhante a: **< API-Server-name >. successfactors. com**.

   * **Floresta Active Directory** O "nome" de seu domínio de Active Directory, conforme registrado com o agente. Use a lista suspensa para selecionar o domínio de destino para provisionamento. Esse valor normalmente é uma cadeia de caracteres como: *contoso.com*

   * **Contêiner de Active Directory-** Insira o DN do contêiner em que o agente deve criar contas de usuário por padrão.
        Exemplo: *ou = Users, DC = contoso, DC = com*
        > [!NOTE]
        > Essa configuração só entra em cena para criações de conta de usuário se o atributo *parentDistinguishedName* não estiver configurado nos mapeamentos de atributo. Essa configuração não é usada para operações de pesquisa ou atualização de usuário. A subárvore de domínio inteira cai no escopo da operação de pesquisa.

   * **Email de notificação –** Insira seu endereço de email e marque a caixa de seleção "enviar email se ocorrer falha".
    > [!NOTE]
    > O serviço de provisionamento do Azure AD enviará uma notificação por email se o trabalho de provisionamento entrar em um estado de [quarentena](/azure/active-directory/manage-apps/application-provisioning-quarantine-status) .

   * Clique no botão **testar conexão** . Se o teste de conexão tiver sucesso, clique no botão **salvar** na parte superior. Se ele falhar, verifique se as credenciais do SuccessFactors e as credenciais do AD configuradas na instalação do agente são válidas.
    >[!div class="mx-imgBorder"]
    >![Portal do Azure](./media/sap-successfactors-inbound-provisioning/sf2ad-provisioning-creds.png)

   * Depois que as credenciais forem salvas com êxito, a seção **mapeamentos** exibirá o mapeamento padrão **sincronizar usuários do SuccessFactors no local Active Directory**

### <a name="part-4-configure-attribute-mappings"></a>Parte 4: configurar mapeamentos de atributo

Nesta seção, você configurará como os dados do usuário fluem do SuccessFactors para o Active Directory.

1. Na guia provisionamento, em **mapeamentos**, clique em **sincronizar usuários do SuccessFactors para o local Active Directory**.

1. No campo **escopo do objeto de origem** , você pode selecionar quais conjuntos de usuários em SuccessFactors devem estar no escopo para provisionamento no AD, definindo um conjunto de filtros baseados em atributo. O escopo padrão é "todos os usuários em SuccessFactors". Filtros de exemplo:

   * Exemplo: escopo para usuários com personIdExternal entre 1 milhão e 2 milhões (exceto 2 milhões)

      * Atributo: personIdExternal

      * Operador: correspondência de REGEX

      * Valor: (1 [0-9] [0-9] [0-9] [0-9] [0-9] [0-9])

   * Exemplo: somente funcionários e não usuários contingentes

      * Atributo: EmployeeID

      * Operador: não é nulo

   > [!TIP]
   > Quando você estiver configurando o aplicativo de provisionamento pela primeira vez, será necessário testar e verificar os mapeamentos e as expressões de atributo para garantir que ele esteja dando o resultado desejado. A Microsoft recomenda usar os filtros de escopo no **escopo do objeto de origem** para testar seus mapeamentos com alguns usuários de teste do SuccessFactors. Depois de verificar se os mapeamentos funcionam, você pode remover o filtro ou expandi-lo gradualmente para incluir mais usuários.

   > [!CAUTION] 
   > O comportamento padrão do mecanismo de provisionamento é desabilitar/excluir usuários que saem do escopo. Isso pode não ser desejável em sua integração do SuccessFactors com o AD. Para substituir esse comportamento padrão, consulte o artigo [ignorar a exclusão de contas de usuário que saem do escopo](../manage-apps/skip-out-of-scope-deletions.md)
  
1. No campo **ações do objeto de destino** , você pode filtrar globalmente quais ações são executadas em Active Directory. **Criar** e **Atualizar** são os mais comuns.

1. Na seção **mapeamentos de atributo** , você pode definir como os atributos de SuccessFactors individuais são mapeados para atributos de Active Directory.

  >[!NOTE]
  >Para obter a lista completa do atributo SuccessFactors com suporte pelo aplicativo, consulte a [referência de atributo SuccessFactors](../manage-apps/sap-successfactors-attribute-reference.md)


1. Clique em um mapeamento de atributo existente para atualizá-lo ou clique em **Adicionar novo mapeamento** na parte inferior da tela para adicionar novos mapeamentos. Um mapeamento de atributo individual oferece suporte a essas propriedades:

      * **Tipo de mapeamento**

         * **Direto** – grava o valor do atributo SuccessFactors para o atributo do AD, sem alterações

         * **Constante** -escreva um valor de cadeia de caracteres constante e estático para o atributo do AD

         * **Expression** – permite que você grave um valor personalizado para o atributo do AD, com base em um ou mais atributos SuccessFactors. [Para obter mais informações, consulte este artigo sobre expressões](../manage-apps/functions-for-customizing-application-data.md).

      * **Atributo de origem** -o atributo de usuário de SuccessFactors

      * **Valor padrão** – opcional. Se o atributo de origem tiver um valor vazio, o mapeamento gravará esse valor em vez disso.
            A configuração mais comum é deixar em branco.

      * **Atributo de destino** – o atributo de usuário em Active Directory.

      * **Corresponder objetos usando este atributo** – se este mapeamento deve ou não ser usado para identificar exclusivamente os usuários entre SuccessFactors e Active Directory. Esse valor é normalmente definido no campo ID de trabalho para SuccessFactors, que normalmente é mapeado para um dos atributos de ID de funcionário em Active Directory.

      * **Precedência de correspondência** – vários atributos correspondentes podem ser definidos. Quando há vários, eles são avaliados na ordem definida por esse campo. Assim que uma correspondência for encontrada, nenhum atributo correspondente será avaliado.

      * **Aplicar este mapeamento**

         * **Sempre** – aplicar esse mapeamento nas ações de criação e atualização do usuário

         * **Somente durante a criação** -aplique este mapeamento somente em ações de criação de usuário

1. Para salvar seus mapeamentos, clique em **salvar** na parte superior da seção de mapeamento de atributos.

Depois que a configuração de mapeamento de atributo for concluída, agora você poderá [habilitar e iniciar o serviço de provisionamento de usuário](#enable-and-launch-user-provisioning).

## <a name="enable-and-launch-user-provisioning"></a>Habilitar e iniciar o provisionamento de usuário

Depois que as configurações do aplicativo de provisionamento do SuccessFactors tiverem sido concluídas, você poderá ativar o serviço de provisionamento no portal do Azure.

> [!TIP]
> Por padrão, quando você ativa o serviço de provisionamento, ele inicia as operações de provisionamento para todos os usuários no escopo. Se houver erros no mapeamento ou problemas de dados de SuccessFactors, o trabalho de provisionamento poderá falhar e entrar no estado de quarentena. Para evitar isso, como uma prática recomendada, é recomendável configurar o filtro de **escopo do objeto de origem** e testar os mapeamentos de atributo com alguns usuários de teste antes de iniciar a sincronização completa para todos os usuários. Depois de verificar se os mapeamentos funcionam e estão fornecendo os resultados desejados, você pode remover o filtro ou expandi-lo gradualmente para incluir mais usuários.

1. Na guia **provisionamento** , defina o **status de provisionamento** como **ativado**.

2. Clique em **Guardar**.

3. Esta operação iniciará a sincronização inicial, que pode usar um número variável de horas dependendo de quantos usuários estiverem no locatário SuccessFactors. Você pode verificar a barra de progresso para acompanhar o progresso do ciclo de sincronização. 

4. A qualquer momento, verifique a guia **logs de auditoria** no portal do Azure para ver quais ações o serviço de provisionamento executou. Os logs de auditoria listam todos os eventos de sincronização individuais executados pelo serviço de provisionamento, como quais usuários estão sendo lidos de SuccessFactors e, posteriormente, adicionados ou atualizados para Active Directory. 

5. Depois que a sincronização inicial for concluída, ela gravará um relatório de Resumo de auditoria na guia **provisionamento** , conforme mostrado abaixo.

   > [!div class="mx-imgBorder"]
   > ![barra de progresso de provisionamento](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="next-steps"></a>Passos seguintes

* [Saiba mais sobre os atributos SuccessFactors com suporte para o provisionamento de entrada](../manage-apps/sap-successfactors-attribute-reference.md)
* [Saiba como configurar o Write-back de email para SuccessFactors](sap-successfactors-writeback-tutorial.md)
* [Saiba como examinar os logs e obter relatórios sobre a atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)
* [Saiba como configurar o logon único entre o SuccessFactors e o Azure Active Directory](successfactors-tutorial.md)
* [Saiba como integrar outros aplicativos SaaS com o Azure Active Directory](tutorial-list.md)
* [Saiba como exportar e importar as configurações de provisionamento](../manage-apps/export-import-provisioning-configuration.md)
