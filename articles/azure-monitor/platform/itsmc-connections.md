---
title: Ligações suportadas com conector de gestão de serviços de TI no Azure Log Analytics  Microsoft Docs
description: Este artigo fornece informações sobre como ligar os seus produtos/serviços ITSM ao Conector de Gestão de Serviços de TI (ITSMC) no Monitor Azure para monitorizar e gerir centralmente os itens de trabalho ITSM.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/24/2018
ms.openlocfilehash: c6cad29b6cc392746a2e56323302521302835b2f
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77665872"
---
# <a name="connect-itsm-productsservices-with-it-service-management-connector"></a>Ligue os produtos/serviços ITSM com o Conector de Gestão de Serviços de TI
Este artigo fornece informações sobre como configurar a ligação entre o seu produto/serviço ITSM e o Conector de Gestão de Serviços de TI (ITSMC) no Log Analytics para gerir centralmente os seus itens de trabalho. Para mais informações sobre o ITSMC, consulte [a visão geral](../../azure-monitor/platform/itsmc-overview.md).

São apoiados os seguintes produtos/serviços ITSM. Selecione o produto para ver informações detalhadas sobre como ligar o produto ao ITSMC.

- [Gestor de serviços do Centro de Sistema](#connect-system-center-service-manager-to-it-service-management-connector-in-azure)
- [ServiceNow](#connect-servicenow-to-it-service-management-connector-in-azure)
- [Provance](#connect-provance-to-it-service-management-connector-in-azure)
- [Rio Cherwell](#connect-cherwell-to-it-service-management-connector-in-azure)

> [!NOTE]
> 
> O Conector ITSM só pode ligar-se a instâncias de ServiceNow baseadas em nuvem. No local, as instâncias serviceNow não são atualmente suportadas.

## <a name="connect-system-center-service-manager-to-it-service-management-connector-in-azure"></a>Connect System Center Service Manager para o Conector de Gestão de Serviços de TI em Azure

As seguintes secções fornecem detalhes sobre como ligar o seu produto System Center Service Manager ao ITSMC em Azure.

### <a name="prerequisites"></a>Pré-requisitos

Certifique-se de que os seguintes pré-requisitos são cumpridos:

- ITSMC instalado. Mais informações: Adicionar a solução de [conector](../../azure-monitor/platform/itsmc-overview.md#adding-the-it-service-management-connector-solution)de gestão de serviços de TI .
- A aplicação Web do Gestor de Serviços (aplicação Web) é implementada e configurada. A informação sobre a aplicação Web está [aqui.](#create-and-deploy-service-manager-web-app-service)
- Ligação híbrida criada e configurada. Mais informações: [Configure a ligação híbrida](#configure-the-hybrid-connection).
- Versões suportadas do Gestor de Serviços: 2012 R2 ou 2016.
- Função do utilizador: [Operador avançado](https://technet.microsoft.com/library/ff461054.aspx).

### <a name="connection-procedure"></a>Procedimento de ligação

Utilize o seguinte procedimento para ligar a sua instância de Gestor de Serviço sicionalado ao ITSMC:

1. No portal Azure, vá a **Todos os Recursos** e procure **ServiceDesk (YourWorkspaceName)**

2.  Em **fontes** de dados do ESPAÇO DE TRABALHO clique em **conexões ITSM**.

    ![Nova ligação](media/itsmc-connections/add-new-itsm-connection.png)

3. Na parte superior do painel direito, clique em **Adicionar**.

4. Forneça as informações descritas na tabela seguinte e clique em **OK** para criar a ligação.

> [!NOTE]
> 
> Todos estes parâmetros são obrigatórios.

| **Campo** | **Descrição** |
| --- | --- |
| **Nome da Ligação**   | Digite um nome para a instância do Gestor de Serviço sinuoso que pretende ligar ao ITSMC.  Usa este nome mais tarde quando configurar itens de trabalho neste caso/visualiza risada de registos detalhados. |
| **Tipo de parceiro**   | Selecione **System Center Service Manager**. |
| **URL do servidor**   | Digite o URL da aplicação Web do Gestor de Serviços. Mais informações sobre a aplicação Web do Gestor de Serviços estão [aqui.](#create-and-deploy-service-manager-web-app-service)
| **ID do cliente**   | Digite o ID do cliente que gerou (utilizando o script automático) para autenticar a aplicação Web. Mais informações sobre o guião automatizado estão [aqui.](../../azure-monitor/platform/itsmc-service-manager-script.md)|
| **Segredo de Cliente**   | Digite o segredo do cliente, gerado para esta identificação.   |
| **Dados de Sincronização**   | Selecione os itens de trabalho do Gestor de Serviço que pretende sincronizar através do ITSMC.  Estes artigos de trabalho são importados para o Log Analytics. **Opções:**  Incidentes, Pedidos de Mudança.|
| **Âmbito de sincronização de dados** | Digite o número de dias anteriores de que quer os dados. **Limite máximo**: 120 dias. |
| **Criar novo item de configuração na solução ITSM** | Selecione esta opção se pretender criar os itens de configuração no produto ITSM. Quando selecionado, o Log Analytics cria os CIs afetados como itens de configuração (em caso de IC não existentes) no sistema ITSM suportado. **Predefinição:** desativado. |

![Conexão de gestor de serviços](media/itsmc-connections/service-manager-connection.png)

**Quando conectado com sucesso e sincronizado:**

- Os itens de trabalho selecionados do Gestor de Serviços são importados para o Azure **Log Analytics.** Pode ver o resumo destes itens de trabalho no azulejo do Cone de Gestão de Serviços de TI.

- Pode criar incidentes a partir de alertas de Log Analytics ou de registos de registos, ou de alertas Azure nesta instância do Gestor de Serviços.


Saiba mais: Crie itens de [trabalho ITSM a partir de alertas Azure](../../azure-monitor/platform/itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

### <a name="create-and-deploy-service-manager-web-app-service"></a>Criar e implementar serviço de aplicações web do Gestor de Serviços

Para ligar o Gestor de Serviços no local com o ITSMC em Azure, a Microsoft criou uma aplicação Web do Service Manager no GitHub.

Para configurar a aplicação ITSM Web para o seu Gestor de Serviços, faça o seguinte:

- **Implementar a aplicação Web** – Implementar a aplicação Web, definir as propriedades e autenticar com a AD Azure. Pode implementar a aplicação web utilizando o [script automatizado](../../azure-monitor/platform/itsmc-service-manager-script.md) que a Microsoft lhe forneceu.
- **Configure a ligação híbrida** - [Configure esta ligação](#configure-the-hybrid-connection)manualmente.

#### <a name="deploy-the-web-app"></a>Implementar a aplicação web
Utilize o [script](../../azure-monitor/platform/itsmc-service-manager-script.md) automatizado para implementar a aplicação Web, definir as propriedades e autenticar com a AD Azure.

Executar o script fornecendo os seguintes detalhes necessários:

- Detalhes da subscrição do Azure
- Nome do grupo de recursos
- Localização
- Detalhes do servidor do Gestor de Serviços (nome do servidor, domínio, nome do utilizador e palavra-passe)
- Prefixo de nome do site para a sua aplicação Web
- ServiceBus Namespace.

O script cria a aplicação Web utilizando o nome que especificou (juntamente com algumas cordas adicionais para torná-la única). Gera o URL da **aplicação Web,** **o ID do cliente** e o segredo do **cliente.**

Guarde os valores, use-os quando criar uma ligação com o ITSMC.

**Verifique a instalação da aplicação Web**

1. Vá ao **portal Azure** > **Recursos.**
2. Selecione a aplicação Web, clique em **Definições** de > **aplicação**.
3. Confirme as informações sobre a instância do Gestor de Serviços que forneceu no momento da implementação da aplicação através do script.

### <a name="configure-the-hybrid-connection"></a>Configure a ligação híbrida

Utilize o seguinte procedimento para configurar a ligação híbrida que liga a instância do Gestor de Assistência ao ITSMC em Azure.

1. Encontre a aplicação Web do Gestor de Serviços, no âmbito do **Azure Resources.**
2. Clique em **Definições** > **Networking**.
3. Em **conexões híbridas,** clique **em configurar os seus pontos finais de ligação híbrido**.

    ![Ligação híbrida em rede](media/itsmc-connections/itsmc-hybrid-connection-networking-and-end-points.png)
4. Na lâmina **ligações híbridas,** clique em **Adicionar ligação híbrida**.

    ![Ligação híbrida adicionar](media/itsmc-connections/itsmc-new-hybrid-connection-add.png)

5. Na lâmina **Add Hybrid Connections,** clique em **Criar uma nova ligação híbrida**.

    ![Nova ligação híbrida](media/itsmc-connections/itsmc-create-new-hybrid-connection.png)

6. Digite os seguintes valores:

   - **Nome endpoint**: Especifique um nome para a nova ligação Híbrida.
   - **EndPoint Host**: FQDN do servidor de gestão do Gestor de Serviços.
   - **Porta endpoint**: Tipo 5724
   - **Espaço de nome servicebus**: Utilize um espaço de nome de serviço existente ou crie um novo.
   - **Localização**: selecione a localização.
   - **Nome**: Especifique um nome para o serviço se estiver a criá-lo.

     ![Valores de ligação híbrida](media/itsmc-connections/itsmc-new-hybrid-connection-values.png)
6. Clique **em OK** para fechar a lâmina de **ligação híbrida Create** e comece a criar a ligação híbrida.

    Uma vez criada a ligação Híbrida, é exibida sob a lâmina.

7. Depois da ligação híbrida ser criada, selecione a ligação e clique **Em adicionar ligação híbrida selecionada**.

    ![Nova ligação híbrida](media/itsmc-connections/itsmc-new-hybrid-connection-added.png)

#### <a name="configure-the-listener-setup"></a>Configure a configuração do ouvinte

Utilize o seguinte procedimento para configurar a configuração do ouvinte para a ligação híbrida.

1. Na lâmina de **Ligações Híbridas,** clique em **Baixar o Gestor de Ligações** e instalá-lo na máquina onde a instância do Gestor de Serviço sinuoso do System Center está em execução.

    Uma vez concluída a instalação, a opção **UI do Hybrid Connection Manager** está disponível no menu **Iniciar.**

2. Clique em **Hybrid Connection Manager UI,** será solicitado para as suas credenciais Azure.

3. Faça login com as suas credenciais Azure e selecione a sua subscrição onde foi criada a ligação Híbrida.

4. Clique em **Guardar**.

A sua ligação híbrida está ligada com sucesso.

![conexão híbrida bem sucedida](media/itsmc-connections/itsmc-hybrid-connection-listener-set-up-successful.png)
> [!NOTE]
> 
> Após a criação da ligação híbrida, verifique e teste a ligação visitando a aplicação Web do Gestor de Serviços implementada. Certifique-se de que a ligação tem sucesso antes de tentar ligar-se ao ITSMC em Azure.

A imagem da amostra que se segue mostra os detalhes de uma ligação bem sucedida:

![Teste de ligação híbrida](media/itsmc-connections/itsmc-hybrid-connection-test.png)

## <a name="connect-servicenow-to-it-service-management-connector-in-azure"></a>Ligue o Serviço Agora ao Conector de Gestão de Serviços de TI em Azure

As seguintes secções fornecem detalhes sobre como ligar o seu produto ServiceNow ao ITSMC em Azure.

### <a name="prerequisites"></a>Pré-requisitos
Certifique-se de que os seguintes pré-requisitos são cumpridos:
- ITSMC instalado. Mais informações: Adicionar a solução de [conector](../../azure-monitor/platform/itsmc-overview.md#adding-the-it-service-management-connector-solution)de gestão de serviços de TI .
- Versões suportadas por ServiceNow: Nova Iorque, Madrid, Londres, Kingston, Jacarta, Istambul, Helsínquia, Genebra.

**ServiceNow Admins deve fazer o seguinte na sua instância ServiceNow:**
- Gere o ID do cliente e o segredo do cliente para o produto ServiceNow. Para obter informações sobre como gerar identificação e segredo do cliente, consulte as seguintes informações conforme necessário:

    - [Instale o OAuth para Nova Iorque](https://docs.servicenow.com/bundle/newyork-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Instale OAuth para Madrid](https://docs.servicenow.com/bundle/madrid-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Instale o OAuth para Londres](https://docs.servicenow.com/bundle/london-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Instale OAuth para Kingston](https://docs.servicenow.com/bundle/kingston-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Instale o OAuth para Jacarta](https://docs.servicenow.com/bundle/jakarta-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Instale o OAuth para Istambul](https://docs.servicenow.com/bundle/istanbul-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Instale OAuth para Helsínquia](https://docs.servicenow.com/bundle/helsinki-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Instale o OAuth para Genebra](https://docs.servicenow.com/bundle/geneva-servicenow-platform/page/administer/security/task/t_SettingUpOAuth.html)


- Instale a aplicação de utilizador para integração do Microsoft Log Analytics (aplicação ServiceNow). [Saiba mais](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1 ).
- Criar a função de utilizador de integração para a aplicação do utilizador instalada. Informação sobre como criar o papel do utilizador de integração está [aqui.](#create-integration-user-role-in-servicenow-app)

### <a name="connection-procedure"></a>**Procedimento de ligação**
Utilize o seguinte procedimento para criar uma ligação ServiceNow:


1. No portal Azure, vá a **Todos os Recursos** e procure **ServiceDesk (YourWorkspaceName)**

2.  Em **fontes** de dados do ESPAÇO DE TRABALHO clique em **conexões ITSM**.
    ![nova ligação](media/itsmc-connections/add-new-itsm-connection.png)

3. Na parte superior do painel direito, clique em **Adicionar**.

4. Forneça as informações descritas na tabela seguinte e clique em **OK** para criar a ligação.


> [!NOTE]
> Todos estes parâmetros são obrigatórios.

| **Campo** | **Descrição** |
| --- | --- |
| **Nome da Ligação**   | Digite um nome para a instância ServiceNow que pretende ligar ao ITSMC.  Usa este nome mais tarde no Log Analytics quando configurar itens de trabalho neste ITSM/visualiza risada aanálise de registos detalhadas. |
| **Tipo de parceiro**   | Selecione **ServiceNow**. |
| **Nome de Utilizador**   | Digite o nome de utilizador de integração que criou na aplicação ServiceNow para suportar a ligação ao ITSMC. Mais informações: Criar a função de [utilizador da aplicação ServiceNow](#create-integration-user-role-in-servicenow-app).|
| **Palavra-passe**   | Digite a palavra-passe associada a este nome de utilizador. **Nota:** O nome do utilizador e a palavra-passe são utilizados apenas para gerar fichas de autenticação e não são armazenados em nenhum lugar dentro do serviço ITSMC.  |
| **URL do servidor**   | Digite o URL da instância ServiceNow que pretende ligar ao ITSMC. |
| **ID do cliente**   | Digite o ID do cliente que pretende utilizar para autenticação OAuth2, que gerou anteriormente.  Mais informações sobre a geração de ID e segredo do cliente: [Configuração OAuth](https://wiki.servicenow.com/index.php?title=OAuth_Setup). |
| **Segredo de Cliente**   | Digite o segredo do cliente, gerado para esta identificação.   |
| **Âmbito de sincronização de dados**   | Selecione os itens de trabalho ServiceNow que pretende sincronizar com o Azure Log Analytics, através do ITSMC.  Os valores selecionados são importados para aanálise de registos.   **Opções:**  Incidentes e Pedidos de Mudança.|
| **Dados de Sincronização** | Digite o número de dias anteriores de que quer os dados. **Limite máximo**: 120 dias. |
| **Criar novo item de configuração na solução ITSM** | Selecione esta opção se pretender criar os itens de configuração no produto ITSM. Quando selecionado, o ITSMC cria os IC afetados como itens de configuração (em caso de IC não existentes) no sistema ITSM suportado. **Predefinição:** desativado. |

![Conexão ServiceNow](media/itsmc-connections/itsm-connection-servicenow-connection-latest.png)

**Quando conectado com sucesso e sincronizado:**

- Os itens de trabalho selecionados da instância ServiceNow são importados para o Azure **Log Analytics.** Pode ver o resumo destes itens de trabalho no azulejo do Cone de Gestão de Serviços de TI.

- Pode criar incidentes a partir de alertas de Log Analytics ou de registos de registos, ou de alertas Azure neste caso ServiceNow.

Saiba mais: Crie itens de [trabalho ITSM a partir de alertas Azure](../../azure-monitor/platform/itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

### <a name="create-integration-user-role-in-servicenow-app"></a>Criar a função do utilizador de integração na aplicação ServiceNow

Utilizador, o seguinte procedimento:

1. Visite a [loja ServiceNow](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1) e instale a **App de Utilizador para O Serviço Agora e Integração Microsoft OMS** na sua Instância ServiceNow.
   
   >[!NOTE]
   >Como parte da transição em curso da Microsoft Operations Management Suite (OMS) para o Monitor Azure, a OMS é agora referida como Log Analytics.     
2. Após a instalação, visite a barra de navegação esquerda da instância ServiceNow, procure e selecione o integrador Microsoft OMS.  
3. Clique na **lista de verificação de instalação**.

   O estado é apresentado como **Não completo** se a função do utilizador ainda estiver por criar.

4. Nas caixas de texto, junto ao Utilizador de **Integração Create,** introduza o nome de utilizador para o utilizador que pode ligar-se ao ITSMC em Azure.
5. Introduza a palavra-passe para este utilizador e clique em **OK**.  

> [!NOTE]
> 
> Usa estas credenciais para fazer a ligação ServiceNow em Azure.

O utilizador recém-criado é apresentado com as funções predefinidas atribuídas.

**Funções predefinidas:**
- personalize_choices
- import_transformer
-   x_mioms_microsoft.user
-   itil
-   template_editor
-   view_changer

Uma vez criado com sucesso o utilizador, o estado da **Check Installation Checklist** passa para Concluído, listando os detalhes da função de utilizador criado para a aplicação.

> [!NOTE]
> 
> O Conector ITSM pode enviar incidentes para o ServiceNow sem quaisquer outros módulos instalados na sua instância ServiceNow. Se estiver a utilizar o módulo DeGestão de Eventos na sua instância ServiceNow e pretender criar Eventos ou Alertas no ServiceNow utilizando o conector, adicione as seguintes funções ao utilizador de integração:
> 
>    - evt_mgmt_integration
>    - evt_mgmt_operator  


## <a name="connect-provance-to-it-service-management-connector-in-azure"></a>Ligue provance ao conector de gestão de serviços de TI em Azure

As seguintes secções fornecem detalhes sobre como ligar o seu produto Provance ao ITSMC em Azure.


### <a name="prerequisites"></a>Pré-requisitos

Certifique-se de que os seguintes pré-requisitos são cumpridos:


- ITSMC instalado. Mais informações: Adicionar a solução de [conector](../../azure-monitor/platform/itsmc-overview.md#adding-the-it-service-management-connector-solution)de gestão de serviços de TI .
- A Provance App deve ser registada com a Azure AD - e o ID do cliente é disponibilizado. Para obter informações detalhadas, consulte [como configurar a autenticação ativa](../../app-service/configure-authentication-provider-aad.md)do diretório .

- Função do utilizador: Administrador.

### <a name="connection-procedure"></a>Procedimento de Ligação

Utilize o seguinte procedimento para criar uma ligação à Provance:

1. No portal Azure, vá a **Todos os Recursos** e procure **ServiceDesk (YourWorkspaceName)**

2.  Em **fontes** de dados do ESPAÇO DE TRABALHO clique em **conexões ITSM**.
    ![nova ligação](media/itsmc-connections/add-new-itsm-connection.png)

3. Na parte superior do painel direito, clique em **Adicionar**.

4. Forneça as informações descritas na tabela seguinte e clique em **OK** para criar a ligação.

> [!NOTE]
> 
> Todos estes parâmetros são obrigatórios.

| **Campo** | **Descrição** |
| --- | --- |
| **Nome da Ligação**   | Digite um nome para a instância Provance que pretende ligar ao ITSMC.  Usa este nome mais tarde quando configurar itens de trabalho neste ITSM/visualiza a análise detalhada do registo. |
| **Tipo de parceiro**   | Selecione **Provance**. |
| **Nome de Utilizador**   | Digite o nome de utilizador que pode ligar ao ITSMC.    |
| **Palavra-passe**   | Digite a palavra-passe associada a este nome de utilizador. **Nota:** O nome e a palavra-passe do utilizador são utilizados apenas para gerar fichas de autenticação e não são armazenados em nenhum lugar dentro do serviço ITSMC._|
| **URL do servidor**   | Digite o URL da sua instância de Provance que pretende ligar ao ITSMC. |
| **ID do cliente**   | Digite o ID do cliente para autenticar esta ligação, que gerou na sua instância Provance.  Mais informações sobre id cliente, veja [como configurar a autenticação de diretório ativo](../../app-service/configure-authentication-provider-aad.md). |
| **Âmbito de sincronização de dados**   | Selecione os itens de trabalho da Provance que pretende sincronizar com o Azure Log Analytics, através do ITSMC.  Estes artigos de trabalho são importados para aanálise de registos.   **Opções:**   Incidentes, Pedidos de Mudança.|
| **Dados de Sincronização** | Digite o número de dias anteriores de que quer os dados. **Limite máximo**: 120 dias. |
| **Criar novo item de configuração na solução ITSM** | Selecione esta opção se pretender criar os itens de configuração no produto ITSM. Quando selecionado, o ITSMC cria os IC afetados como itens de configuração (em caso de IC não existentes) no sistema ITSM suportado. **Predefinição:** desativado.|

![Ligação provance](media/itsmc-connections/itsm-connections-provance-latest.png)

**Quando conectado com sucesso e sincronizado:**

- Os itens de trabalho selecionados desta instância de Provance são importados para o Azure **Log Analytics.** Pode ver o resumo destes itens de trabalho no azulejo do Cone de Gestão de Serviços de TI.

- Pode criar incidentes a partir de alertas de Log Analytics ou de registos de registos, ou de alertas Azure neste caso Provance.

Saiba mais: Crie itens de [trabalho ITSM a partir de alertas Azure](../../azure-monitor/platform/itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

## <a name="connect-cherwell-to-it-service-management-connector-in-azure"></a>Ligue Cherwell ao Conector de Gestão de Serviços de TI em Azure

As seguintes secções fornecem detalhes sobre como ligar o seu produto Cherwell ao ITSMC em Azure.

### <a name="prerequisites"></a>Pré-requisitos

Certifique-se de que os seguintes pré-requisitos são cumpridos:

- ITSMC instalado. Mais informações: Adicionar a solução de [conector](../../azure-monitor/platform/itsmc-overview.md#adding-the-it-service-management-connector-solution)de gestão de serviços de TI .
- Id do cliente gerado. Mais informações: Gere o [ID do cliente para cherwell](#generate-client-id-for-cherwell).
- Função do utilizador: Administrador.

### <a name="connection-procedure"></a>Procedimento de Ligação

Utilize o seguinte procedimento para criar uma ligação à Provance:

1. No portal Azure, vá a **Todos os Recursos** e procure **ServiceDesk (YourWorkspaceName)**

2.  Em **fontes** de dados do ESPAÇO DE TRABALHO clique em **conexões ITSM**.
    ![nova ligação](media/itsmc-connections/add-new-itsm-connection.png)

3. Na parte superior do painel direito, clique em **Adicionar**.

4. Forneça as informações descritas na tabela seguinte e clique em **OK** para criar a ligação.

> [!NOTE]
> 
> Todos estes parâmetros são obrigatórios.

| **Campo** | **Descrição** |
| --- | --- |
| **Nome da Ligação**   | Digite um nome para a instância de Cherwell que pretende ligar ao ITSMC.  Usa este nome mais tarde quando configurar itens de trabalho neste ITSM/visualiza a análise detalhada do registo. |
| **Tipo de parceiro**   | Selecione **Cherwell.** |
| **Nome de Utilizador**   | Digite o nome de utilizador Cherwell que pode ligar-se ao ITSMC. |
| **Palavra-passe**   | Digite a palavra-passe associada a este nome de utilizador. **Nota:** O nome e a palavra-passe do utilizador são utilizados apenas para gerar fichas de autenticação e não são armazenados em nenhum lugar dentro do serviço ITSMC.|
| **URL do servidor**   | Digite o URL da sua instância de Cherwell que pretende ligar ao ITSMC. |
| **ID do cliente**   | Digite o ID do cliente para autenticar esta ligação, que gerou no seu caso Cherwell.   |
| **Âmbito de sincronização de dados**   | Selecione os itens de trabalho cherwell que pretende sincronizar através do ITSMC.  Estes artigos de trabalho são importados para aanálise de registos.   **Opções:**  Incidentes, Pedidos de Mudança. |
| **Dados de Sincronização** | Digite o número de dias anteriores de que quer os dados. **Limite máximo**: 120 dias. |
| **Criar novo item de configuração na solução ITSM** | Selecione esta opção se pretender criar os itens de configuração no produto ITSM. Quando selecionado, o ITSMC cria os IC afetados como itens de configuração (em caso de IC não existentes) no sistema ITSM suportado. **Predefinição:** desativado. |


![Ligação provance](media/itsmc-connections/itsm-connections-cherwell-latest.png)

**Quando conectado com sucesso e sincronizado:**

- Os itens de trabalho selecionados desta instância de Cherwell são importados para o Azure **Log Analytics.** Pode ver o resumo destes itens de trabalho no azulejo do Cone de Gestão de Serviços de TI.

- Pode criar incidentes a partir de alertas de Log Analytics ou de registos de registos, ou de alertas Azure neste caso cherwell.

Saiba mais: Crie itens de [trabalho ITSM a partir de alertas Azure](../../azure-monitor/platform/itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

### <a name="generate-client-id-for-cherwell"></a>Gerar ID do cliente para Cherwell

Para gerar o ID/chave do cliente para Cherwell, utilize o seguinte procedimento:

1. Faça login na sua instância cherwell como administrador.
2. Clique em **Segurança** > Editar as definições do **cliente REST API**.
3. Selecione **Criar um novo cliente** > segredo do **cliente**.

    ![Id do utilizador cherwell](media/itsmc-connections/itsmc-cherwell-client-id.png)


## <a name="next-steps"></a>Passos seguintes
 - [Criar itens de trabalho ITSM a partir de alertas Azure](../../azure-monitor/platform/itsmc-overview.md#create-itsm-work-items-from-azure-alerts)
