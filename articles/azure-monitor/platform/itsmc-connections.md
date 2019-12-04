---
title: Conexões com suporte com Conector de Gerenciamento de Serviços de TI no Azure Log Analytics | Microsoft Docs
description: Este artigo fornece informações sobre como conectar seus produtos/serviços de ITSM com o Conector de Gerenciamento de Serviços de TI (ITSMC) em Azure Monitor para monitorar e gerenciar de forma centralizada os itens de trabalho de ITSM.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: JYOTHIRMAISURI
ms.author: v-jysur
ms.date: 05/24/2018
ms.openlocfilehash: bd9f659589b222350fe688ee4da876801a65226a
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74769970"
---
# <a name="connect-itsm-productsservices-with-it-service-management-connector"></a>Conectar produtos/serviços de ITSM com o Conector de Gerenciamento de Serviços de TI
Este artigo fornece informações sobre como configurar a conexão entre seu produto/serviço de ITSM e o Conector de Gerenciamento de Serviços de TI (ITSMC) em Log Analytics para gerenciar centralmente seus itens de trabalho. Para obter mais informações sobre ITSMC, consulte [visão geral](../../azure-monitor/platform/itsmc-overview.md).

Há suporte para os seguintes produtos/serviços de ITSM. Selecione o produto para exibir informações detalhadas sobre como conectar o produto ao ITSMC.

- [System Center Service Manager](#connect-system-center-service-manager-to-it-service-management-connector-in-azure)
- [ServiceNow](#connect-servicenow-to-it-service-management-connector-in-azure)
- [Provance](#connect-provance-to-it-service-management-connector-in-azure)
- [Cherwell](#connect-cherwell-to-it-service-management-connector-in-azure)

> [!NOTE]
> 
> Conector ITSM só pode se conectar a instâncias do ServiceNow baseadas em nuvem. Atualmente, não há suporte para instâncias locais do ServiceNow.

## <a name="connect-system-center-service-manager-to-it-service-management-connector-in-azure"></a>Conectar System Center Service Manager ao Conector de Gerenciamento de Serviços de TI no Azure

As seções a seguir fornecem detalhes sobre como conectar seu System Center Service Manager produto ao ITSMC no Azure.

### <a name="prerequisites"></a>Pré-requisitos

Verifique se os seguintes pré-requisitos foram atendidos:

- ITSMC instalado. Mais informações: [adicionando a solução de conector de gerenciamento de serviços de ti](../../azure-monitor/platform/itsmc-overview.md#adding-the-it-service-management-connector-solution).
- O aplicativo Web Service Manager (aplicativo Web) é implantado e configurado. As informações sobre o aplicativo Web estão [aqui](#create-and-deploy-service-manager-web-app-service).
- Conexão híbrida criada e configurada. Mais informações: [Configurar a conexão híbrida](#configure-the-hybrid-connection).
- Versões com suporte do Service Manager: 2012 R2 ou 2016.
- Função de usuário: [operador avançado](https://technet.microsoft.com/library/ff461054.aspx).

### <a name="connection-procedure"></a>Procedimento de conexão

Use o procedimento a seguir para conectar sua instância do System Center Service Manager ao ITSMC:

1. Em portal do Azure, vá para **todos os recursos** e procure **YourWorkspaceName)**

2.  Em **fontes de dados do espaço de trabalho** , clique em **conexões de ITSM**.

    ![Nova ligação](media/itsmc-connections/add-new-itsm-connection.png)

3. Na parte superior do painel direito, clique em **Adicionar**.

4. Forneça as informações conforme descrito na tabela a seguir e clique em **OK** para criar a conexão.

> [!NOTE]
> 
> Todos esses parâmetros são obrigatórios.

| **Campo** | **Descrição** |
| --- | --- |
| **Nome da Ligação**   | Digite um nome para a instância de System Center Service Manager que você deseja conectar com ITSMC.  Você usará esse nome posteriormente ao configurar itens de trabalho nesta instância/exibir o log Analytics detalhado. |
| **Tipo de parceiro**   | Selecione **System Center Service Manager**. |
| **URL do servidor**   | Digite a URL do aplicativo Web Service Manager. Mais informações sobre o Service Manager aplicativo Web está [aqui](#create-and-deploy-service-manager-web-app-service).
| **ID do cliente**   | Digite a ID do cliente que você gerou (usando o script automático) para autenticar o aplicativo Web. Mais informações sobre o script automatizado estão [aqui.](../../azure-monitor/platform/itsmc-service-manager-script.md)|
| **Segredo do cliente**   | Digite o segredo do cliente, gerado para essa ID.   |
| **Sincronizar dados**   | Selecione o Service Manager itens de trabalho que você deseja sincronizar por meio de ITSMC.  Esses itens de trabalho são importados para o Log Analytics. **Opções:**  Incidentes, solicitações de alteração.|
| **Escopo de sincronização de dados** | Digite o número de dias anteriores dos quais você deseja os dados. **Limite máximo**: 120 dias. |
| **Criar novo item de configuração na solução de ITSM** | Selecione esta opção se desejar criar os itens de configuração no produto de ITSM. Quando selecionado, Log Analytics cria o CIs afetado como itens de configuração (no caso de CIs não existente) no sistema de ITSM com suporte. **Padrão**: desabilitado. |

![Conexão do Service Manager](media/itsmc-connections/service-manager-connection.png)

**Quando conectado e sincronizado com êxito**:

- Os itens de trabalho selecionados de Service Manager são importados para o Log Analytics do Azure **.** Você pode exibir o resumo desses itens de trabalho no bloco Conector de Gerenciamento de Serviços de TI.

- Você pode criar incidentes de Log Analytics alertas ou de registros de log ou de alertas do Azure nesta instância de Service Manager.


Saiba mais: [criar itens de trabalho de ITSM de alertas do Azure](../../azure-monitor/platform/itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

### <a name="create-and-deploy-service-manager-web-app-service"></a>Criar e implantar Service Manager serviço de aplicativo Web

Para conectar a Service Manager local com o ITSMC no Azure, a Microsoft criou um aplicativo Web Service Manager no GitHub.

Para configurar o aplicativo Web de ITSM para seu Service Manager, faça o seguinte:

- **Implantar o aplicativo Web** – implante o aplicativo Web, defina as propriedades e autentique com o Azure AD. Você pode implantar o aplicativo Web usando o [script automatizado](../../azure-monitor/platform/itsmc-service-manager-script.md) que a Microsoft forneceu a você.
- **Configure a conexão híbrida** - [configurar essa conexão](#configure-the-hybrid-connection)manualmente.

#### <a name="deploy-the-web-app"></a>Implantar o aplicativo Web
Use o [script](../../azure-monitor/platform/itsmc-service-manager-script.md) automatizado para implantar o aplicativo Web, definir as propriedades e autenticar com o Azure AD.

Execute o script fornecendo os seguintes detalhes necessários:

- Detalhes da assinatura do Azure
- Nome do grupo de recursos
- Localização
- Detalhes do Service Manager Server (nome do servidor, domínio, nome de usuário e senha)
- Prefixo do nome do site para seu aplicativo Web
- Namespace do ServiceBus.

O script cria o aplicativo Web usando o nome que você especificou (juntamente com algumas cadeias de caracteres adicionais para torná-lo exclusivo). Ele gera a **URL do aplicativo Web**, a **ID do cliente** e o **segredo do cliente**.

Salve os valores, use-os quando você criar uma conexão com o ITSMC.

**Verificar a instalação do aplicativo Web**

1. Vá para **portal do Azure** > **recursos**.
2. Selecione o aplicativo Web, clique em **configurações** > **configurações do aplicativo**.
3. Confirme as informações sobre a instância de Service Manager que você forneceu no momento da implantação do aplicativo por meio do script.

### <a name="configure-the-hybrid-connection"></a>Configurar a conexão híbrida

Use o procedimento a seguir para configurar a conexão híbrida que conecta a instância de Service Manager com o ITSMC no Azure.

1. Localize o aplicativo Web Service Manager, em **recursos do Azure**.
2. Clique em **configurações** > **rede**.
3. Em **conexões híbridas**, clique em **configurar seus pontos de extremidade de conexão híbrida**.

    ![Rede de conexão híbrida](media/itsmc-connections/itsmc-hybrid-connection-networking-and-end-points.png)
4. Na folha **conexões híbridas** , clique em **Adicionar conexão híbrida**.

    ![Adição de conexão híbrida](media/itsmc-connections/itsmc-new-hybrid-connection-add.png)

5. Na folha **adicionar conexões híbridas** , clique em **criar nova conexão híbrida**.

    ![Nova conexão híbrida](media/itsmc-connections/itsmc-create-new-hybrid-connection.png)

6. Digite os seguintes valores:

   - **Nome do ponto de extremidade**: especifique um nome para a nova conexão híbrida.
   - **Host de ponto de extremidade**: FQDN do servidor de gerenciamento de Service Manager.
   - **Porta do ponto de extremidade**: tipo 5724
   - **Namespace do ServiceBus**: Use um namespace do ServiceBus existente ou crie um novo.
   - **Local**: selecione o local.
   - **Nome**: especifique um nome para o ServiceBus se você o estiver criando.

     ![Valores de conexão híbrida](media/itsmc-connections/itsmc-new-hybrid-connection-values.png)
6. Clique em **OK** para fechar a folha **criar conexão híbrida** e começar a criar a conexão híbrida.

    Depois que a conexão híbrida é criada, ela é exibida na folha.

7. Depois que a conexão híbrida for criada, selecione a conexão e clique em **Adicionar conexão híbrida selecionada**.

    ![Nova conexão híbrida](media/itsmc-connections/itsmc-new-hybrid-connection-added.png)

#### <a name="configure-the-listener-setup"></a>Configurar a instalação do ouvinte

Use o procedimento a seguir para configurar a configuração do ouvinte para a conexão híbrida.

1. Na folha **conexões híbridas** , clique em **baixar o Gerenciador de conexões** e instale-o no computador em que System Center Service Manager instância está em execução.

    Quando a instalação for concluída, **Gerenciador de conexões híbridas opção interface do usuário** estará disponível no menu **Iniciar** .

2. Clique em **Gerenciador de conexões híbridas interface do usuário** , suas credenciais do Azure serão solicitadas.

3. Faça logon com suas credenciais do Azure e selecione sua assinatura na qual a conexão híbrida foi criada.

4. Clique em **Guardar**.

Sua conexão híbrida foi conectada com êxito.

![conexão híbrida bem-sucedida](media/itsmc-connections/itsmc-hybrid-connection-listener-set-up-successful.png)
> [!NOTE]
> 
> Depois que a conexão híbrida for criada, verifique e teste a conexão visitando o aplicativo Web Service Manager implantado. Verifique se a conexão foi bem-sucedida antes de tentar se conectar ao ITSMC no Azure.

A imagem de exemplo a seguir mostra os detalhes de uma conexão bem-sucedida:

![Teste de conexão híbrida](media/itsmc-connections/itsmc-hybrid-connection-test.png)

## <a name="connect-servicenow-to-it-service-management-connector-in-azure"></a>Conectar o ServiceNow ao Conector de Gerenciamento de Serviços de TI no Azure

As seções a seguir fornecem detalhes sobre como conectar seu produto ServiceNow ao ITSMC no Azure.

### <a name="prerequisites"></a>Pré-requisitos
Verifique se os seguintes pré-requisitos foram atendidos:
- ITSMC instalado. Mais informações: [adicionando a solução de conector de gerenciamento de serviços de ti](../../azure-monitor/platform/itsmc-overview.md#adding-the-it-service-management-connector-solution).
- Versões com suporte do ServiceNow: Madri, Londres, Kingston, Jacarta, Istambul, Helsinque, Geneva.

**Os administradores do servicenow devem fazer o seguinte em sua instância do servicenow**:
- Gere a ID do cliente e o segredo do cliente para o produto ServiceNow. Para obter informações sobre como gerar a ID e o segredo do cliente, consulte as seguintes informações, conforme necessário:

    - [Configurar o OAuth para Madri](https://docs.servicenow.com/bundle/madrid-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Configurar o OAuth para Londres](https://docs.servicenow.com/bundle/london-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Configurar o OAuth para Kingston](https://docs.servicenow.com/bundle/kingston-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Configurar o OAuth para Jacarta](https://docs.servicenow.com/bundle/jakarta-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Configurar o OAuth para Istambul](https://docs.servicenow.com/bundle/istanbul-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Configurar o OAuth para Helsinque](https://docs.servicenow.com/bundle/helsinki-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Configurar o OAuth para Geneva](https://docs.servicenow.com/bundle/geneva-servicenow-platform/page/administer/security/task/t_SettingUpOAuth.html)
    - [Configurar o OAuth para Nova York](https://docs.servicenow.com/bundle/newyork-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)

- Instale o aplicativo de usuário para integração do Microsoft Log Analytics (aplicativo ServiceNow). [Saiba mais](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1 ).
- Criar função de usuário de integração para o aplicativo de usuário instalado. Informações sobre como criar a função de usuário de integração estão [aqui](#create-integration-user-role-in-servicenow-app).

### <a name="connection-procedure"></a>**Procedimento de conexão**
Use o procedimento a seguir para criar uma conexão do ServiceNow:


1. Em portal do Azure, vá para **todos os recursos** e procure **YourWorkspaceName)**

2.  Em **fontes de dados do espaço de trabalho** , clique em **conexões de ITSM**.
    ![nova conexão](media/itsmc-connections/add-new-itsm-connection.png)

3. Na parte superior do painel direito, clique em **Adicionar**.

4. Forneça as informações conforme descrito na tabela a seguir e clique em **OK** para criar a conexão.


> [!NOTE]
> Todos esses parâmetros são obrigatórios.

| **Campo** | **Descrição** |
| --- | --- |
| **Nome da Ligação**   | Digite um nome para a instância do ServiceNow que você deseja conectar com ITSMC.  Você usará esse nome posteriormente em Log Analytics ao configurar itens de trabalho neste ITSM/exibir o log Analytics detalhado. |
| **Tipo de parceiro**   | Selecione **ServiceNow**. |
| **Nome de Utilizador**   | Digite o nome de usuário de integração que você criou no aplicativo ServiceNow para dar suporte à conexão com o ITSMC. Mais informações: [criar função de usuário de aplicativo ServiceNow](#create-integration-user-role-in-servicenow-app).|
| **Palavra-passe**   | Digite a senha associada a este nome de usuário. **Observação**: o nome de usuário e a senha são usados para gerar somente tokens de autenticação e não são armazenados em nenhum lugar no serviço ITSMC.  |
| **URL do servidor**   | Digite a URL da instância do ServiceNow que você deseja conectar ao ITSMC. |
| **ID do cliente**   | Digite a ID do cliente que você deseja usar para autenticação OAuth2, que você gerou anteriormente.  Mais informações sobre como gerar a ID e o segredo do cliente: [instalação do OAuth](https://wiki.servicenow.com/index.php?title=OAuth_Setup). |
| **Segredo do cliente**   | Digite o segredo do cliente, gerado para essa ID.   |
| **Escopo de sincronização de dados**   | Selecione os itens de trabalho do ServiceNow que você deseja sincronizar com o Azure Log Analytics, por meio do ITSMC.  Os valores selecionados são importados para o log Analytics.   **Opções:**  Incidentes e solicitações de alteração.|
| **Sincronizar dados** | Digite o número de dias anteriores dos quais você deseja os dados. **Limite máximo**: 120 dias. |
| **Criar novo item de configuração na solução de ITSM** | Selecione esta opção se desejar criar os itens de configuração no produto de ITSM. Quando selecionado, o ITSMC cria o CIs afetado como itens de configuração (no caso de CIs não existente) no sistema de ITSM com suporte. **Padrão**: desabilitado. |

![Conexão do ServiceNow](media/itsmc-connections/itsm-connection-servicenow-connection-latest.png)

**Quando conectado e sincronizado com êxito**:

- Os itens de trabalho selecionados da instância do ServiceNow são importados para o Azure **log Analytics.** Você pode exibir o resumo desses itens de trabalho no bloco Conector de Gerenciamento de Serviços de TI.

- Você pode criar incidentes de Log Analytics alertas ou de registros de log ou de alertas do Azure nesta instância do ServiceNow.

Saiba mais: [criar itens de trabalho de ITSM de alertas do Azure](../../azure-monitor/platform/itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

### <a name="create-integration-user-role-in-servicenow-app"></a>Criar função de usuário de integração no aplicativo ServiceNow

Usuário o seguinte procedimento:

1. Visite a [loja do servicenow](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1) e instale o **aplicativo de usuário para a integração do Servicenow e do Microsoft OMS** em sua instância do servicenow.
   
   >[!NOTE]
   >Como parte da transição contínua do Microsoft Operations Management Suite (OMS) para Azure Monitor, o OMS agora é chamado de Log Analytics.     
2. Após a instalação, visite a barra de navegação à esquerda da instância do ServiceNow, pesquise e selecione Microsoft OMS Integrator.  
3. Clique na **lista de verificação de instalação**.

   O status será exibido como **não concluído** se a função de usuário ainda não for criada.

4. Nas caixas de texto, ao lado de **criar usuário de integração**, insira o nome de usuário para o usuário que pode se conectar ao ITSMC no Azure.
5. Insira a senha para esse usuário e clique em **OK**.  

> [!NOTE]
> 
> Use essas credenciais para fazer a conexão do ServiceNow no Azure.

O usuário recém-criado é exibido com as funções padrão atribuídas.

**Funções padrão**:
- personalize_choices
- import_transformer
-   x_mioms_microsoft. User
-   ITIL
-   template_editor
-   view_changer

Depois que o usuário for criado com êxito, o status da **lista de verificação de instalação de verificação** será movido para concluído, listando os detalhes da função de usuário criada para o aplicativo.

> [!NOTE]
> 
> Conector ITSM pode enviar incidentes para o ServiceNow sem nenhum outro módulo instalado em sua instância do ServiceNow. Se você estiver usando o módulo EventManagement em sua instância do ServiceNow e quiser criar eventos ou alertas no ServiceNow usando o conector, adicione as seguintes funções ao usuário de integração:
> 
>    - evt_mgmt_integration
>    - evt_mgmt_operator  


## <a name="connect-provance-to-it-service-management-connector-in-azure"></a>Conectar o Provance ao Conector de Gerenciamento de Serviços de TI no Azure

As seções a seguir fornecem detalhes sobre como conectar seu produto Provance ao ITSMC no Azure.


### <a name="prerequisites"></a>Pré-requisitos

Verifique se os seguintes pré-requisitos foram atendidos:


- ITSMC instalado. Mais informações: [adicionando a solução de conector de gerenciamento de serviços de ti](../../azure-monitor/platform/itsmc-overview.md#adding-the-it-service-management-connector-solution).
- O aplicativo Provance deve ser registrado com o Azure AD e a ID do cliente é disponibilizada. Para obter informações detalhadas, consulte [como configurar a autenticação do Active Directory](../../app-service/configure-authentication-provider-aad.md).

- Função de usuário: administrador.

### <a name="connection-procedure"></a>Procedimento de conexão

Use o procedimento a seguir para criar uma conexão Provance:

1. Em portal do Azure, vá para **todos os recursos** e procure **YourWorkspaceName)**

2.  Em **fontes de dados do espaço de trabalho** , clique em **conexões de ITSM**.
    ![nova conexão](media/itsmc-connections/add-new-itsm-connection.png)

3. Na parte superior do painel direito, clique em **Adicionar**.

4. Forneça as informações conforme descrito na tabela a seguir e clique em **OK** para criar a conexão.

> [!NOTE]
> 
> Todos esses parâmetros são obrigatórios.

| **Campo** | **Descrição** |
| --- | --- |
| **Nome da Ligação**   | Digite um nome para a instância de Provance que você deseja conectar com ITSMC.  Você usará esse nome posteriormente ao configurar itens de trabalho neste ITSM/exibir log Analytics detalhado. |
| **Tipo de parceiro**   | Selecione **Provance**. |
| **Nome de Utilizador**   | Digite o nome de usuário que pode se conectar ao ITSMC.    |
| **Palavra-passe**   | Digite a senha associada a este nome de usuário. **Observação:** O nome de usuário e a senha são usados para gerar somente tokens de autenticação e não são armazenados em nenhum lugar no serviço ITSMC. _|
| **URL do servidor**   | Digite a URL da instância do Provance que você deseja conectar ao ITSMC. |
| **ID do cliente**   | Digite a ID do cliente para autenticar essa conexão, que você gerou em sua instância do Provance.  Para obter mais informações sobre a ID do cliente, consulte [como configurar a autenticação do Active Directory](../../app-service/configure-authentication-provider-aad.md). |
| **Escopo de sincronização de dados**   | Selecione os itens de trabalho do Provance que você deseja sincronizar com o Azure Log Analytics, por meio de ITSMC.  Esses itens de trabalho são importados para o log Analytics.   **Opções:**   Incidentes, solicitações de alteração.|
| **Sincronizar dados** | Digite o número de dias anteriores dos quais você deseja os dados. **Limite máximo**: 120 dias. |
| **Criar novo item de configuração na solução de ITSM** | Selecione esta opção se desejar criar os itens de configuração no produto de ITSM. Quando selecionado, o ITSMC cria o CIs afetado como itens de configuração (no caso de CIs não existente) no sistema de ITSM com suporte. **Padrão**: desabilitado.|

![Conexão Provance](media/itsmc-connections/itsm-connections-provance-latest.png)

**Quando conectado e sincronizado com êxito**:

- Os itens de trabalho selecionados dessa instância do Provance são importados para o Azure **log Analytics.** Você pode exibir o resumo desses itens de trabalho no bloco Conector de Gerenciamento de Serviços de TI.

- Você pode criar incidentes de Log Analytics alertas ou de registros de log ou de alertas do Azure nesta instância do Provance.

Saiba mais: [criar itens de trabalho de ITSM de alertas do Azure](../../azure-monitor/platform/itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

## <a name="connect-cherwell-to-it-service-management-connector-in-azure"></a>Conectar o Cherwell ao Conector de Gerenciamento de Serviços de TI no Azure

As seções a seguir fornecem detalhes sobre como conectar seu produto Cherwell ao ITSMC no Azure.

### <a name="prerequisites"></a>Pré-requisitos

Verifique se os seguintes pré-requisitos foram atendidos:

- ITSMC instalado. Mais informações: [adicionando a solução de conector de gerenciamento de serviços de ti](../../azure-monitor/platform/itsmc-overview.md#adding-the-it-service-management-connector-solution).
- ID do cliente gerada. Mais informações: [gerar a ID do cliente para Cherwell](#generate-client-id-for-cherwell).
- Função de usuário: administrador.

### <a name="connection-procedure"></a>Procedimento de conexão

Use o procedimento a seguir para criar uma conexão Provance:

1. Em portal do Azure, vá para **todos os recursos** e procure **YourWorkspaceName)**

2.  Em **fontes de dados do espaço de trabalho** , clique em **conexões de ITSM**.
    ![nova conexão](media/itsmc-connections/add-new-itsm-connection.png)

3. Na parte superior do painel direito, clique em **Adicionar**.

4. Forneça as informações conforme descrito na tabela a seguir e clique em **OK** para criar a conexão.

> [!NOTE]
> 
> Todos esses parâmetros são obrigatórios.

| **Campo** | **Descrição** |
| --- | --- |
| **Nome da Ligação**   | Digite um nome para a instância de Cherwell que você deseja conectar ao ITSMC.  Você usará esse nome posteriormente ao configurar itens de trabalho neste ITSM/exibir log Analytics detalhado. |
| **Tipo de parceiro**   | Selecione **Cherwell.** |
| **Nome de Utilizador**   | Digite o nome de usuário Cherwell que pode se conectar ao ITSMC. |
| **Palavra-passe**   | Digite a senha associada a este nome de usuário. **Observação:** O nome de usuário e a senha são usados para gerar somente tokens de autenticação e não são armazenados em nenhum lugar no serviço ITSMC.|
| **URL do servidor**   | Digite a URL da instância do Cherwell que você deseja conectar ao ITSMC. |
| **ID do cliente**   | Digite a ID do cliente para autenticar essa conexão, que você gerou em sua instância do Cherwell.   |
| **Escopo de sincronização de dados**   | Selecione os itens de trabalho do Cherwell que você deseja sincronizar por meio de ITSMC.  Esses itens de trabalho são importados para o log Analytics.   **Opções:**  Incidentes, solicitações de alteração. |
| **Sincronizar dados** | Digite o número de dias anteriores dos quais você deseja os dados. **Limite máximo**: 120 dias. |
| **Criar novo item de configuração na solução de ITSM** | Selecione esta opção se desejar criar os itens de configuração no produto de ITSM. Quando selecionado, o ITSMC cria o CIs afetado como itens de configuração (no caso de CIs não existente) no sistema de ITSM com suporte. **Padrão**: desabilitado. |


![Conexão Provance](media/itsmc-connections/itsm-connections-cherwell-latest.png)

**Quando conectado e sincronizado com êxito**:

- Os itens de trabalho selecionados dessa instância do Cherwell são importados para o Azure **log Analytics.** Você pode exibir o resumo desses itens de trabalho no bloco Conector de Gerenciamento de Serviços de TI.

- Você pode criar incidentes de Log Analytics alertas ou de registros de log ou de alertas do Azure nesta instância do Cherwell.

Saiba mais: [criar itens de trabalho de ITSM de alertas do Azure](../../azure-monitor/platform/itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

### <a name="generate-client-id-for-cherwell"></a>Gerar ID do cliente para Cherwell

Para gerar a ID/chave do cliente para Cherwell, use o seguinte procedimento:

1. Faça logon em sua instância do Cherwell como administrador.
2. Clique em **segurança** > **Editar configurações do cliente da API REST**.
3. Selecione **criar novo cliente** > **segredo do cliente**.

    ![ID de usuário do Cherwell](media/itsmc-connections/itsmc-cherwell-client-id.png)


## <a name="next-steps"></a>Passos seguintes
 - [Criar itens de trabalho de ITSM de alertas do Azure](../../azure-monitor/platform/itsmc-overview.md#create-itsm-work-items-from-azure-alerts)
