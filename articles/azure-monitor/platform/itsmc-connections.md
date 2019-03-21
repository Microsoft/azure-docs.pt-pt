---
title: Suporte a conexões com o conector de gestão do serviço de TI no Log Analytics do Azure | Documentos da Microsoft
description: Este artigo fornece informações sobre como ligar seus produtos/serviços ITSM com o conector de TI de gestão de serviço (ITSMC) no Azure Monitor para monitorizar e gerir os itens de trabalho ITSM centralmente.
documentationcenter: ''
author: jyothirmaisuri
manager: riyazp
editor: ''
ms.assetid: 8231b7ce-d67f-4237-afbf-465e2e397105
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/24/2018
ms.author: v-jysur
ms.openlocfilehash: 64a4e7a181f7bd24e305ef5ee8d3d6657c3f394b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58081292"
---
# <a name="connect-itsm-productsservices-with-it-service-management-connector"></a>Ligar produtos/serviços de ITSM com o conector de gestão do serviço de TI
Este artigo fornece informações sobre como configurar a conexão entre seus produtos/serviços ITSM e o conector de gestão de serviço de TI (ITSMC) no Log Analytics para gerenciar de forma centralizada seus itens de trabalho. Para obter mais informações sobre ITSMC, consulte [descrição geral](../../azure-monitor/platform/itsmc-overview.md).

Os seguintes produtos/serviços ITSM são suportados. Selecione o produto para ver informações detalhadas sobre como ligar o produto ao ITSMC.

- [System Center Service Manager](#connect-system-center-service-manager-to-it-service-management-connector-in-azure)
- [ServiceNow](#connect-servicenow-to-it-service-management-connector-in-azure)
- [Provance](#connect-provance-to-it-service-management-connector-in-azure)
- [Cherwell](#connect-cherwell-to-it-service-management-connector-in-azure)

> [!NOTE]
> 
> Conector ITSM só pode ligar a instâncias do ServiceNow com base na cloud. Instâncias de ServiceNow no local não são atualmente suportadas.

## <a name="connect-system-center-service-manager-to-it-service-management-connector-in-azure"></a>Ligar o System Center Service Manager para o serviço de TI conector de gestão no Azure

As secções seguintes fornecem detalhes sobre como ligar o seu produto System Center Service Manager para ITSMC no Azure.

### <a name="prerequisites"></a>Pré-requisitos

Certifique-se de que são cumpridos os seguintes pré-requisitos:

- ITSMC instalado. Mais informações: [Adicionar o IT a solução de conector de gestão de serviço](../../azure-monitor/platform/itsmc-overview.md#adding-the-it-service-management-connector-solution).
- A aplicação Web do Service Manager (aplicação Web) é implementada e configurada. Informações sobre a aplicação Web estão [aqui](#create-and-deploy-service-manager-web-app-service).
- Ligação híbrida criada e configurada. Mais informações: [Configurar a ligação de híbrida](#configure-the-hybrid-connection).
- Versões suportadas do Service Manager:  2012 R2 ou 2016.
- Função de utilizador:  [Operador avançado](https://technet.microsoft.com/library/ff461054.aspx).

### <a name="connection-procedure"></a>Procedimento de ligação

Utilize o procedimento seguinte para ligar a sua instância de System Center Service Manager para ITSMC:

1. No portal do Azure, aceda a **todos os recursos** e procure **ServiceDesk(YourWorkspaceName)**

2.  Sob **ORIGENS de dados de área de trabalho** clique em **ligações de ITSM**.

    ![Nova ligação](media/itsmc-connections/add-new-itsm-connection.png)

3. Na parte superior do painel direito, clique em **adicionar**.

4. Forneça as informações, conforme descrito na tabela a seguir e clique em **OK** para criar a ligação.

> [!NOTE]
> 
> Todos esses parâmetros são obrigatórios.

| **Campo** | **Descrição** |
| --- | --- |
| **Nome da Ligação**   | Escreva um nome para a instância de System Center Service Manager que pretende ligar-se com ITSMC.  Utilizar este nome mais tarde quando configurar os itens de trabalho nesta instância / ver detalhadas do log analytics. |
| **Tipo de parceiro**   | Selecione **System Center Service Manager**. |
| **URL do servidor**   | Escreva o URL da aplicação Web do Service Manager. Obter mais informações sobre as aplicações Web do Service Manager estão [aqui](#create-and-deploy-service-manager-web-app-service).
| **ID de cliente**   | Escreva o ID de cliente que gerou (usando o script automático) para autenticar a aplicação Web. Obter mais informações sobre o script automatizado são [aqui.](../../azure-monitor/platform/itsmc-service-manager-script.md)|
| **Segredo do cliente**   | Escreva o segredo de cliente gerado para este ID.   |
| **Âmbito de sincronização de dados**   | Selecione os itens de trabalho do Service Manager que pretende sincronizar através do ITSMC.  Estes itens são importados para o Log Analytics de trabalho. **Opções:**  Incidentes, pedidos de alteração.|
| **Sincronizar dados** | Escreva o número de dias decorridos que pretende que os dados do. **Limite máximo**: 120 dias. |
| **Criar novo item de configuração na solução ITSM** | Selecione esta opção se pretender criar os itens de configuração no produto ITSM. Quando selecionada, o Log Analytics cria os CIs afetados como itens de configuração (em caso de não existente CIs) no sistema ITSM suportado. **Predefinido**: desativado. |

![Ligação do Service manager](media/itsmc-connections/service-manager-connection.png)

**Quando ligado com êxito e sincronizados**:

- Itens de trabalho selecionado do Service Manager são importados para o Azure **do Log Analytics.** Pode ver o resumo esses itens de trabalho no mosaico do conector de gestão do serviço de TI.

- Pode criar incidentes de alertas do Log Analytics ou de registros de log ou de alertas do Azure nesta instância do Service Manager.


Saiba mais: [Criar itens de trabalho ITSM de alertas do Azure](../../azure-monitor/platform/itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

### <a name="create-and-deploy-service-manager-web-app-service"></a>Criar e implementar o serviço de aplicações web do Service Manager

Para ligar o Gestor de serviços no local com ITSMC no Azure, Microsoft criou uma aplicação Web do Service Manager no GitHub.

Para configurar a aplicação Web de ITSM para o Service Manager, faça o seguinte:

- **Implementar a aplicação Web** – implementar a aplicação Web, defina as propriedades e autenticar com o Azure AD. Pode implementar a aplicação web ao utilizar o [automatizada de script](../../azure-monitor/platform/itsmc-service-manager-script.md) que a Microsoft forneceu.
- **Configurar a ligação híbrida** - [configurar esta ligação](#configure-the-hybrid-connection), manualmente.

#### <a name="deploy-the-web-app"></a>Implementar a aplicação web
Utilizar o automatizadas [script](../../azure-monitor/platform/itsmc-service-manager-script.md) para implementar a aplicação Web, defina as propriedades e autenticar com o Azure AD.

Execute o script, fornecendo os seguintes detalhes necessários:

- Detalhes da subscrição do Azure
- Nome do grupo de recursos
- Localização
- Detalhes do servidor do Service Manager (nome do servidor, domínio, nome de utilizador e palavra-passe)
- Prefixo do nome do site para a sua aplicação Web
- Espaço de nomes do ServiceBus.

O script cria uma aplicação Web com o nome que especificou (juntamente com algumas cadeias de caracteres adicionais para o tornar único). Ele gera os **URL de aplicação Web**, **ID de cliente** e **segredo do cliente**.

Guarde os valores, usá-los quando cria uma ligação com ITSMC.

**Verificar a instalação da aplicação Web**

1. Aceda a **portal do Azure** > **recursos**.
2. Selecione a aplicação Web, clique em **configurações** > **configurações de aplicativo**.
3. Certifique-se as informações sobre a instância do Service Manager que indicou no momento da implementação da aplicação através do script.

### <a name="configure-the-hybrid-connection"></a>Configurar a ligação híbrida

Utilize o procedimento seguinte para configurar a ligação híbrida que se liga a instância do Service Manager com ITSMC no Azure.

1. Encontre a aplicação Web do Service Manager, em **recursos do Azure**.
2. Clique em **configurações** > **redes**.
3. Sob **ligações híbridas**, clique em **configurar os pontos de extremidade de ligação híbrida**.

    ![Rede de ligação híbrida](media/itsmc-connections/itsmc-hybrid-connection-networking-and-end-points.png)
4. Na **ligações híbridas** painel, clique em **adicionar ligação híbrida**.

    ![Adicionar ligação híbrida](media/itsmc-connections/itsmc-new-hybrid-connection-add.png)

5. Na **adicionar as ligações híbridas** painel, clique em **híbrida de criar nova ligação**.

    ![Nova ligação híbrida](media/itsmc-connections/itsmc-create-new-hybrid-connection.png)

6. Escreva os seguintes valores:

   - **Nome do ponto final**: Especifique um nome para a nova ligação híbrida.
   - **EndPoint Host**: FQDN do servidor de gestão do Service Manager.
   - **EndPoint Port**: Tipo de 5724
   - **Espaço de nomes do Servicebus**: Utilizar um espaço de nomes do servicebus existente ou criar um novo.
   - **Localização**: selecione a localização.
   - **Nome**: Especifique um nome para o servicebus, se estiver a criar.

     ![Valores de ligação híbrida](media/itsmc-connections/itsmc-new-hybrid-connection-values.png)
6. Clique em **OK** para fechar a **criar ligação híbrida** painel e começa a criar a ligação híbrida.

    Depois da ligação híbrida criada, é apresentado no painel de.

7. Depois de criar a ligação híbrida, selecione a ligação e clique em **selecionado de adicionar ligação híbrida**.

    ![Nova ligação híbrida](media/itsmc-connections/itsmc-new-hybrid-connection-added.png)

#### <a name="configure-the-listener-setup"></a>Configurar o programa de configuração do serviço de escuta

Utilize o procedimento seguinte para configurar o programa de configuração do serviço de escuta para a ligação híbrida.

1. Na **ligações híbridas** painel, clique em **transfira o Gerenciador de conexões** e instalá-lo no computador onde a instância de System Center Service Manager está em execução.

    Assim que a instalação estiver concluída, **IU do Gestor de ligações híbridas** opção está disponível no **iniciar** menu.

2. Clique em **IU do Gestor de ligações híbridas** , será solicitado a informar suas credenciais do Azure.

3. Início de sessão com as suas credenciais do Azure e selecione a sua subscrição em que foi criada a ligação híbrida.

4. Clique em **Guardar**.

A ligação híbrida é ligada com êxito.

![ligação híbrida com êxito](media/itsmc-connections/itsmc-hybrid-connection-listener-set-up-successful.png)
> [!NOTE]
> 
> Após a híbrida é criada a ligação, certifique-se e testar a ligação ao visitar a aplicação Web do Service Manager implementada. Certifique-se de que a ligação é efetuada com êxito antes de tentar ligar ao ITSMC no Azure.

A imagem de exemplo seguinte mostra os detalhes de uma ligação com êxito:

![Teste de ligação híbrida](media/itsmc-connections/itsmc-hybrid-connection-test.png)

## <a name="connect-servicenow-to-it-service-management-connector-in-azure"></a>Ligar o ServiceNow ao serviço de TI conector de gestão no Azure

As secções seguintes fornecem detalhes sobre como ligar o seu produto do ServiceNow para ITSMC no Azure.

### <a name="prerequisites"></a>Pré-requisitos
Certifique-se de que são cumpridos os seguintes pré-requisitos:
- ITSMC instalado. Mais informações: [Adicionar o IT a solução de conector de gestão de serviço](../../azure-monitor/platform/itsmc-overview.md#adding-the-it-service-management-connector-solution).
- Versões suportadas do ServiceNow: Kingston, Jacarta, Istambul, Helsínquia, Geneva.

**Os administradores do ServiceNow necessário efetuar o seguinte na sua instância do ServiceNow**:
- Gere ID de cliente e segredo do cliente para o produto do ServiceNow. Para obter informações sobre como gerar ID de cliente e o segredo, consulte as seguintes informações conforme necessário:

    - [Configurar o OAuth para Kingston](https://docs.servicenow.com/bundle/kingston-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Configurar o OAuth para Jacarta](https://docs.servicenow.com/bundle/jakarta-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Configurar o OAuth para Istambul](https://docs.servicenow.com/bundle/istanbul-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Configurar o OAuth para Helsínquia](https://docs.servicenow.com/bundle/helsinki-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Configurar o OAuth para Geneva](https://docs.servicenow.com/bundle/geneva-servicenow-platform/page/administer/security/task/t_SettingUpOAuth.html)


- Instale a aplicação de utilizador para a integração do Microsoft Log Analytics (aplicação ServiceNow). [Saiba mais](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1 ).
- Crie função de utilizador de integração para a aplicação de utilizador instalada. Há informações sobre como criar a função de utilizador de integração [aqui](#create-integration-user-role-in-servicenow-app).

### <a name="connection-procedure"></a>**Procedimento de ligação**
Utilize o procedimento seguinte para criar uma ligação do ServiceNow:


1. No portal do Azure, aceda a **todos os recursos** e procure **ServiceDesk(YourWorkspaceName)**

2.  Sob **ORIGENS de dados de área de trabalho** clique em **ligações de ITSM**.
    ![Nova ligação](media/itsmc-connections/add-new-itsm-connection.png)

3. Na parte superior do painel direito, clique em **adicionar**.

4. Forneça as informações, conforme descrito na tabela a seguir e clique em **OK** para criar a ligação.


> [!NOTE]
> Todos esses parâmetros são obrigatórios.

| **Campo** | **Descrição** |
| --- | --- |
| **Nome da Ligação**   | Escreva um nome para a instância do ServiceNow que queira se conectar com ITSMC.  Utilizar este nome posteriormente no Log Analytics quando configurar os itens de trabalho neste ITSM / ver detalhadas do log analytics. |
| **Tipo de parceiro**   | Selecione **ServiceNow**. |
| **Nome de Utilizador**   | Escreva o nome de utilizador de integração que criou na aplicação do ServiceNow para oferecer suporte a ligação ao ITSMC. Mais informações: [Criar função de utilizador do ServiceNow aplicação](#create-integration-user-role-in-servicenow-app).|
| **Palavra-passe**   | Escreva a palavra-passe associada com este nome de utilizador. **Nota**: Nome de utilizador e palavra-passe são utilizados para gerar tokens de autenticação apenas e não são armazenadas em qualquer lugar no âmbito do serviço ITSMC.  |
| **URL do servidor**   | Escreva o URL da instância do ServiceNow que deseja se conectar ITSMC. |
| **ID de cliente**   | Escreva o ID de cliente que pretende utilizar para a autenticação OAuth2, o que gerou anteriormente.  Obter mais informações sobre a geração de ID de cliente e segredo:   [Configuração de OAuth](https://wiki.servicenow.com/index.php?title=OAuth_Setup). |
| **Segredo do cliente**   | Escreva o segredo de cliente gerado para este ID.   |
| **Âmbito de sincronização de dados**   | Selecione os itens de trabalho do ServiceNow que pretende sincronizar com o Azure Log Analytics, através do ITSMC.  Os valores selecionados são importados para o log analytics.   **Opções:**  Incidentes e pedidos de alteração.|
| **Sincronizar dados** | Escreva o número de dias decorridos que pretende que os dados do. **Limite máximo**: 120 dias. |
| **Criar novo item de configuração na solução ITSM** | Selecione esta opção se pretender criar os itens de configuração no produto ITSM. Quando selecionada, ITSMC cria os CIs afetados como itens de configuração (em caso de não existente CIs) no sistema ITSM suportado. **Predefinido**: desativado. |

![Ligação do ServiceNow](media/itsmc-connections/itsm-connection-servicenow-connection-latest.png)

**Quando ligado com êxito e sincronizados**:

- Itens de trabalho selecionado da instância do ServiceNow são importados para o Azure **do Log Analytics.** Pode ver o resumo esses itens de trabalho no mosaico do conector de gestão do serviço de TI.

- Pode criar incidentes de alertas do Log Analytics ou de registros de log ou de alertas do Azure nesta instância do ServiceNow.

Saiba mais: [Criar itens de trabalho ITSM de alertas do Azure](../../azure-monitor/platform/itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

### <a name="create-integration-user-role-in-servicenow-app"></a>Criar função de utilizador de integração na aplicação do ServiceNow

Utilizador o seguinte procedimento:

1. Visite o [ServiceNow store](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1) e instale o **aplicação de utilizador para o ServiceNow e integração do Microsoft OMS** em sua instância do ServiceNow.
   
   >[!NOTE]
   >Como parte da transição em curso do Microsoft Operations Management Suite (OMS) para o Azure Monitor, OMS agora é referido como o Log Analytics.     
2. Após a instalação, visite a barra de navegação à esquerda da instância do ServiceNow, pesquisa e selecione integrador de Microsoft OMS.  
3. Clique em **lista de verificação de instalação**.

   O estado é apresentado como **não concluir** se a função de utilizador ainda está a ser criada.

4. Nas caixas de texto, junto a **criar o utilizador de integração**, introduza o nome de utilizador para o utilizador que possam ligar à ITSMC no Azure.
5. Introduza a palavra-passe para este utilizador e clique em **OK**.  

> [!NOTE]
> 
> Utilize estas credenciais para fazer a conexão de ServiceNow no Azure.

O utilizador recentemente criada será apresentado com as funções padrão atribuídas.

**Predefinido funções**:
- personalize_choices
- import_transformer
-   x_mioms_microsoft.user
-   itil
-   template_editor
-   view_changer

Assim que o utilizador é criado com êxito, o estado dos **Verifique a lista de verificação de instalação** move como concluído, lista os detalhes da função de utilizador criados para a aplicação.

> [!NOTE]
> 
> Conector ITSM pode enviar incidentes ao ServiceNow sem quaisquer outros módulos instalados na sua instância do ServiceNow. Se estiver a utilizar o módulo de EventManagement na sua instância do ServiceNow e deseja criar eventos ou alertas no ServiceNow através do conector, adicione as seguintes funções para o utilizador de integração:
> 
>    - evt_mgmt_integration
>    - evt_mgmt_operator  


## <a name="connect-provance-to-it-service-management-connector-in-azure"></a>Ligar Provance ao serviço de TI conector de gestão no Azure

As secções seguintes fornecem detalhes sobre como ligar o seu produto Provance a ITSMC no Azure.


### <a name="prerequisites"></a>Pré-requisitos

Certifique-se de que são cumpridos os seguintes pré-requisitos:


- ITSMC instalado. Mais informações: [Adicionar o IT a solução de conector de gestão de serviço](../../azure-monitor/platform/itsmc-overview.md#adding-the-it-service-management-connector-solution).
- Aplicação de provance deve ser registada no Azure AD – e ID de cliente é disponibilizado. Para obter informações detalhadas, consulte [como configurar a autenticação do Active Directory](../../app-service/configure-authentication-provider-aad.md).

- Função de utilizador:  Administrador.

### <a name="connection-procedure"></a>Procedimento de ligação

Utilize o procedimento seguinte para criar uma ligação de Provance:

1. No portal do Azure, aceda a **todos os recursos** e procure **ServiceDesk(YourWorkspaceName)**

2.  Sob **ORIGENS de dados de área de trabalho** clique em **ligações de ITSM**.
    ![Nova ligação](media/itsmc-connections/add-new-itsm-connection.png)

3. Na parte superior do painel direito, clique em **adicionar**.

4. Forneça as informações, conforme descrito na tabela a seguir e clique em **OK** para criar a ligação.

> [!NOTE]
> 
> Todos esses parâmetros são obrigatórios.

| **Campo** | **Descrição** |
| --- | --- |
| **Nome da Ligação**   | Escreva um nome para a instância de Provance que queira se conectar com ITSMC.  Utilizar este nome mais tarde quando configurar os itens de trabalho neste ITSM / ver detalhadas do log analytics. |
| **Tipo de parceiro**   | Selecione **Provance**. |
| **Nome de Utilizador**   | Escreva o nome de utilizador que possam ligar à ITSMC.    |
| **Palavra-passe**   | Escreva a palavra-passe associada com este nome de utilizador. **Nota:** Nome de utilizador e palavra-passe são utilizados para gerar tokens de autenticação apenas e não são armazenadas em qualquer lugar no âmbito do serviço ITSMC. _|
| **URL do servidor**   | Escreva o URL da sua instância de Provance que pretende ligar a ITSMC. |
| **ID de cliente**   | Escreva o ID de cliente para autenticar esta ligação, que gerou na sua instância de Provance.  Obter mais informações sobre o ID de cliente, consulte [como configurar a autenticação do Active Directory](../../app-service/configure-authentication-provider-aad.md). |
| **Âmbito de sincronização de dados**   | Selecione os itens de trabalho de Provance que pretende sincronizar com o Azure Log Analytics, através do ITSMC.  Estes itens são importados para o log analytics de trabalho.   **Opções:**   Incidentes, pedidos de alteração.|
| **Sincronizar dados** | Escreva o número de dias decorridos que pretende que os dados do. **Limite máximo**: 120 dias. |
| **Criar novo item de configuração na solução ITSM** | Selecione esta opção se pretender criar os itens de configuração no produto ITSM. Quando selecionada, ITSMC cria os CIs afetados como itens de configuração (em caso de não existente CIs) no sistema ITSM suportado. **Predefinido**: desativado.|

![Ligação de provance](media/itsmc-connections/itsm-connections-provance-latest.png)

**Quando ligado com êxito e sincronizados**:

- Itens de trabalho selecionado desta instância Provance são importados para o Azure **do Log Analytics.** Pode ver o resumo esses itens de trabalho no mosaico do conector de gestão do serviço de TI.

- Pode criar incidentes de alertas do Log Analytics ou de registros de log ou de alertas do Azure nesta instância Provance.

Saiba mais: [Criar itens de trabalho ITSM de alertas do Azure](../../azure-monitor/platform/itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

## <a name="connect-cherwell-to-it-service-management-connector-in-azure"></a>Ligar o Cherwell ao serviço de TI conector de gestão no Azure

As secções seguintes fornecem detalhes sobre como ligar o seu produto Cherwell a ITSMC no Azure.

### <a name="prerequisites"></a>Pré-requisitos

Certifique-se de que são cumpridos os seguintes pré-requisitos:

- ITSMC instalado. Mais informações: [Adicionar o IT a solução de conector de gestão de serviço](../../azure-monitor/platform/itsmc-overview.md#adding-the-it-service-management-connector-solution).
- ID de cliente gerado. Mais informações: [Gerar ID de cliente para o Cherwell](#generate-client-id-for-cherwell).
- Função de utilizador:  Administrador.

### <a name="connection-procedure"></a>Procedimento de ligação

Utilize o procedimento seguinte para criar uma ligação de Provance:

1. No portal do Azure, aceda a **todos os recursos** e procure **ServiceDesk(YourWorkspaceName)**

2.  Sob **ORIGENS de dados de área de trabalho** clique em **ligações de ITSM**.
    ![Nova ligação](media/itsmc-connections/add-new-itsm-connection.png)

3. Na parte superior do painel direito, clique em **adicionar**.

4. Forneça as informações, conforme descrito na tabela a seguir e clique em **OK** para criar a ligação.

> [!NOTE]
> 
> Todos esses parâmetros são obrigatórios.

| **Campo** | **Descrição** |
| --- | --- |
| **Nome da Ligação**   | Escreva um nome para a instância de Cherwell que pretende ligar a ITSMC.  Utilizar este nome mais tarde quando configurar os itens de trabalho neste ITSM / ver detalhadas do log analytics. |
| **Tipo de parceiro**   | Selecione **Cherwell.** |
| **Nome de Utilizador**   | Escreva o nome de utilizador de Cherwell que possam ligar à ITSMC. |
| **Palavra-passe**   | Escreva a palavra-passe associada com este nome de utilizador. **Nota:** Nome de utilizador e palavra-passe são utilizados para gerar tokens de autenticação apenas e não são armazenadas em qualquer lugar no âmbito do serviço ITSMC.|
| **URL do servidor**   | Escreva o URL da sua instância de Cherwell que pretende ligar a ITSMC. |
| **ID de cliente**   | Escreva o ID de cliente para autenticar esta ligação, que gerou na sua instância de Cherwell.   |
| **Âmbito de sincronização de dados**   | Selecione os itens de trabalho de Cherwell que pretende sincronizar através do ITSMC.  Estes itens são importados para o log analytics de trabalho.   **Opções:**  Incidentes, pedidos de alteração. |
| **Sincronizar dados** | Escreva o número de dias decorridos que pretende que os dados do. **Limite máximo**: 120 dias. |
| **Criar novo item de configuração na solução ITSM** | Selecione esta opção se pretender criar os itens de configuração no produto ITSM. Quando selecionada, ITSMC cria os CIs afetados como itens de configuração (em caso de não existente CIs) no sistema ITSM suportado. **Predefinido**: desativado. |


![Ligação de provance](media/itsmc-connections/itsm-connections-cherwell-latest.png)

**Quando ligado com êxito e sincronizados**:

- Itens de trabalho selecionado desta instância Cherwell são importados para o Azure **do Log Analytics.** Pode ver o resumo esses itens de trabalho no mosaico do conector de gestão do serviço de TI.

- Pode criar incidentes de alertas do Log Analytics ou de registros de log ou de alertas do Azure nesta instância Cherwell.

Saiba mais: [Criar itens de trabalho ITSM de alertas do Azure](../../azure-monitor/platform/itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

### <a name="generate-client-id-for-cherwell"></a>Gerar ID de cliente para o Cherwell

Para gerar o chave do ID de cliente para o Cherwell, utilize o seguinte procedimento:

1. Inicie sessão sua instância de Cherwell como administrador.
2. Clique em **Security** > **definições de cliente de API do REST de editar**.
3. Selecione **criar novo cliente** > **segredo do cliente**.

    ![Id de utilizador Cherwell](media/itsmc-connections/itsmc-cherwell-client-id.png)


## <a name="next-steps"></a>Passos Seguintes
 - [Criar itens de trabalho ITSM de alertas do Azure](../../azure-monitor/platform/itsmc-overview.md#create-itsm-work-items-from-azure-alerts)
