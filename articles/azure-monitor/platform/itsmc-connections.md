---
title: Conector de gestão de serviços de TI no Monitor Azure
description: Este artigo fornece informações sobre como ligar os seus produtos/serviços ITSM com o It Service Management Connector (ITSMC) em Azure Monitor para monitorizar e gerir centralmente os itens de trabalho do ITSM.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/12/2020
ms.openlocfilehash: a0ef8d9f28f90db77686c3139c8b45eccfb669aa
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2020
ms.locfileid: "96938822"
---
# <a name="connect-itsm-productsservices-with-it-service-management-connector"></a>Ligar os produtos/serviços do ITSM ao Conector de Gestão de Serviços de TI
Este artigo fornece informações sobre como configurar a ligação entre o seu produto/serviço ITSM e o It Service Management Connector (ITSMC) no Log Analytics para gerir centralmente os seus itens de trabalho. Para obter mais informações sobre o ITSMC, consulte [a Visão Geral.](./itsmc-overview.md)

São suportados os seguintes produtos/serviços ITSM. Selecione o produto para ver informações detalhadas sobre como ligar o produto ao ITSMC.

- [Gestor de serviços do Centro de Sistema](#connect-system-center-service-manager-to-it-service-management-connector-in-azure)
- [ServiceNow](#connect-servicenow-to-it-service-management-connector-in-azure)
- [Provance](#connect-provance-to-it-service-management-connector-in-azure)
- [Cherwell](#connect-cherwell-to-it-service-management-connector-in-azure)

> [!NOTE]
> 
> Propomos aos nossos clientes Cherwell e Provance que utilizem a [ação Webhook](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#webhook) para cherwell e provance como outra solução para a integração.

## <a name="connect-system-center-service-manager-to-it-service-management-connector-in-azure"></a>Connect System Center Service Manager ao It Service Management Connector em Azure

As secções seguintes fornecem detalhes sobre como ligar o seu produto System Center Service Manager ao ITSMC em Azure.

### <a name="prerequisites"></a>Pré-requisitos

Assegurar que os seguintes pré-requisitos sejam cumpridos:

- ITSMC instalado. Mais informações: [Adicionar a Solução de Conector de Gestão de Serviços de TI](./itsmc-overview.md#add-it-service-management-connector).
- A aplicação Web do Gestor de Serviços (web app) está implantada e configurada. A informação sobre a aplicação Web está [aqui.](#create-and-deploy-service-manager-web-app-service)
- Ligação híbrida criada e configurada. Mais informações: [Configurar a Ligação Híbrida](#configure-the-hybrid-connection).
- Versões suportadas do Service Manager: 2012 R2 ou 2016.
- Função de utilizador:  [Operador avançado](/previous-versions/system-center/service-manager-2010-sp1/ff461054(v=technet.10)).
- Hoje os alertas enviados pelo Azure Monitor podem criar em Incidentes do Gestor de Serviços do System Center.

> [!NOTE]
> 
> - O Conector ITSM só pode ligar-se a instâncias de ServiceNow baseadas na nuvem. As instâncias do ServiceNow no local não são suportadas atualmente.
> - Para utilizar [modelos personalizados](./itsmc-overview.md#template-definitions) como parte das ações, o parâmetro "ProjectionType" no modelo SCSM deve ser mapeado para "IncidentManagement! System.WorkItem.Incident.ProjectionType"

### <a name="connection-procedure"></a>Procedimento de ligação

Utilize o seguinte procedimento para ligar a sua instância do Gestor de Serviços do System Center ao ITSMC:

1. No portal Azure, vá a **Todos os Recursos** e procure o **ServiceDesk (Seu Nome De Espaço-Trabalho)**

2.  No **âmbito do WORKSPACE DATA SOURCES** clique em **ItsM Connections**.

    ![Nova ligação](media/itsmc-connections/add-new-itsm-connection.png)

3. Na parte superior do painel direito, clique em **Adicionar**.

4. Forneça as informações como descrito na tabela seguinte e clique **em OK** para criar a ligação.

> [!NOTE]
> 
> Todos estes parâmetros são obrigatórios.

| **Campo** | **Descrição** |
| --- | --- |
| **Nome de conexão**   | Digite um nome para a instância do Gestor de Serviço do Centro de Sistema que pretende ligar com o ITSMC.  Utilize este nome mais tarde quando configurar itens de trabalho neste caso/ ver análises detalhadas de registos. |
| **Tipo de parceiro**   | Selecione **System Center Service Manager**. |
| **URL do servidor**   | Digite o URL da aplicação Web do Gestor de Serviço. Mais informações sobre a aplicação Web do Service Manager [estão aqui.](#create-and-deploy-service-manager-web-app-service)
| **ID do cliente**   | Digite o ID do cliente que gerou (utilizando o script automático) para autenticar a aplicação Web. Mais informações sobre o script automatizado [estão aqui.](./itsmc-service-manager-script.md)|
| **Segredo do Cliente**   | Digite o segredo do cliente, gerado para esta identificação.   |
| **Dados de Sincronização**   | Selecione os itens de trabalho do Gestor de Serviço que pretende sincronizar através do ITSMC.  Estes artigos de trabalho são importados para o Log Analytics. **Opções:**  Incidentes, Pedidos de Alteração.|
| **Âmbito de sincronização de dados** | Digite o número de dias passados de onde deseja os dados. **Limite máximo:** 120 dias. |
| **Criar novo item de configuração na solução ITSM** | Selecione esta opção se pretender criar os itens de configuração no produto ITSM. Quando selecionado, o Log Analytics cria os CIs afetados como itens de configuração (em caso de TEC não existentes) no sistema ITSM suportado. **Predefinição:** desativado. |

![Conexão de gestor de serviço](media/itsmc-connections/service-manager-connection.png)

**Quando conectado com sucesso, e sincronizado:**

- Os artigos de trabalho selecionados do Service Manager são importados para a Azure **Log Analytics.** Pode ver o resumo destes artigos de trabalho no azulejo do conector de gestão de serviços de TI.

- Pode criar incidentes a partir de alertas de Log Analytics ou de registos de registos, ou a partir de alertas Azure nesta instância do Service Manager.


Saiba mais: [Crie itens de trabalho ITSM a partir de alertas Azure](./itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

### <a name="create-and-deploy-service-manager-web-app-service"></a>Criar e implementar serviço de aplicações web do Service Manager

Para ligar o Gestor de Serviços no local com o ITSMC em Azure, a Microsoft criou uma aplicação Web do Service Manager no GitHub.

Para configurar a aplicação WEB ITSM para o seu Gestor de Serviços, faça o seguinte:

- **Implementar a aplicação Web** – Implementar a aplicação Web, definir as propriedades e autenticar com Azure AD. Pode implementar a aplicação web utilizando o [script automatizado](./itsmc-service-manager-script.md) que a Microsoft lhe forneceu.
- **Configure a ligação**  -  híbrida [Configure esta ligação,](#configure-the-hybrid-connection)manualmente.

#### <a name="deploy-the-web-app"></a>Implementar a aplicação web
Utilize o [script](./itsmc-service-manager-script.md) automatizado para implementar a aplicação Web, definir as propriedades e autenticar com Azure AD.

Execute o script fornecendo os seguintes detalhes necessários:

- Detalhes da subscrição do Azure
- Nome do grupo de recursos
- Localização
- Detalhes do servidor do Gestor de Serviço (nome do servidor, domínio, nome de utilizador e palavra-passe)
- Prefixo de nome do site para a sua aplicação Web
- Espaço de nomes ServiceBus.

O script cria a aplicação Web usando o nome especificado (juntamente com algumas cordas adicionais para torná-la única). Gera o URL da **aplicação Web,** **iD do cliente** e **segredo do cliente.**

Guarde os valores, utilize-os quando criar uma ligação com o ITSMC.

**Verifique a instalação da aplicação Web**

1. Ir para o **portal Azure**  >  **Recursos**.
2. Selecione a aplicação Web, clique em  >  **Definições de Aplicação** de Definições .
3. Confirme as informações sobre a instância do Service Manager que forneceu no momento da implementação da aplicação através do script.

### <a name="configure-the-hybrid-connection"></a>Configure a ligação híbrida

Utilize o seguinte procedimento para configurar a ligação híbrida que liga a instância do Gestor de Serviço com o ITSMC em Azure.

1. Encontre a aplicação Web do Gestor de Serviços, ao abrigo **da Azure Resources.**
2. Clique em **Redes de**  >  **Definições**.
3. Em **Conexões Híbridas,** clique em **Configurar os seus pontos finais de ligação híbrida**.

    ![Rede de ligação híbrida](media/itsmc-connections/itsmc-hybrid-connection-networking-and-end-points.png)
4. Na lâmina **De Ligações Híbridas,** clique **em Adicionar ligação híbrida**.

    ![Adicionar ligação híbrida](media/itsmc-connections/itsmc-new-hybrid-connection-add.png)

5. Na lâmina **Add Hybrid Connections,** clique em **Criar uma nova ligação híbrida**.

    ![Nova ligação híbrida](media/itsmc-connections/itsmc-create-new-hybrid-connection.png)

6. Digite os seguintes valores:

   - **Nome do Ponto final**: Especifique um nome para a nova ligação Híbrida.
   - **EndPoint Host**: FQDN do servidor de gestão do Gestor de Serviços.
   - **Porta EndPoint**: Tipo 5724
   - **Espaço de nome servicebus**: Utilize um espaço de nome de servicebus existente ou crie um novo.
   - **Localização**: selecione a localização.
   - **Nome**: Especifique um nome para obus de serviço se estiver a criá-lo.

     ![Valores de ligação híbrida](media/itsmc-connections/itsmc-new-hybrid-connection-values.png)
6. Clique **em OK** para fechar a lâmina de **ligação híbrida Create** e comece a criar a ligação híbrida.

    Uma vez criada a ligação Híbrida, é exibida debaixo da lâmina.

7. Depois da ligação híbrida ser criada, selecione a ligação e clique **Em Adicionar a ligação híbrida selecionada**.

    ![Nova ligação híbrida](media/itsmc-connections/itsmc-new-hybrid-connection-added.png)

#### <a name="configure-the-listener-setup"></a>Configure a configuração do ouvinte

Utilize o seguinte procedimento para configurar a configuração do ouvinte para a ligação híbrida.

1. Na lâmina **De Ligações Híbridas,** clique em **Baixar o Gestor de Ligação** e instale-o na máquina onde está a decorrer a instância do Gestor de Serviços do Centro de Sistema.

    Uma vez concluída a instalação, a opção **UI do Gestor de Ligação Híbrida** está disponível no menu **Iniciar.**

2. Clique **em Hybrid Connection Manager UI,** será solicitado para as suas credenciais Azure.

3. Faça login com as suas credenciais Azure e selecione a sua subscrição onde foi criada a ligação Híbrida.

4. Clique em **Guardar**.

A sua ligação híbrida está ligada com sucesso.

![conexão híbrida de sucesso](media/itsmc-connections/itsmc-hybrid-connection-listener-set-up-successful.png)
> [!NOTE]
> 
> Após a criação da ligação híbrida, verifique e teste a ligação visitando a aplicação Web do Service Manager implementada. Certifique-se de que a ligação é bem sucedida antes de tentar ligar-se ao ITSMC em Azure.

A imagem da amostra que se segue mostra os detalhes de uma ligação bem sucedida:

![Teste de conexão híbrido](media/itsmc-connections/itsmc-hybrid-connection-test.png)

## <a name="connect-servicenow-to-it-service-management-connector-in-azure"></a>Conecte o Conector de Gestão de Serviços de TI em Azure

As seguintes secções fornecem detalhes sobre como ligar o seu produto ServiceNow ao ITSMC em Azure.

### <a name="prerequisites"></a>Pré-requisitos
Assegurar que os seguintes pré-requisitos sejam cumpridos:
- ITSMC instalado. Mais informações: [Adicionar a Solução de Conector de Gestão de Serviços de TI](./itsmc-overview.md#add-it-service-management-connector).
- Versões suportadas pelo ServiceNow: Orlando, Nova Iorque, Madrid, Londres, Kingston, Jacarta, Istambul, Helsínquia, Genebra.
- Hoje os alertas enviados pelo Azure Monitor podem criar no ServiceNow um dos seguintes elementos: Eventos, Incidentes ou Alertas.
> [!NOTE]
> A ITSMC suporta apenas a oferta oficial do SaaS do Service Now. As implementações privadas do Serviço Agora não são suportadas. 

**Os Administradores ServiceNow devem fazer o seguinte no seu caso ServiceNow:**
- Gerei o ID do cliente e o segredo do cliente para o produto ServiceNow. Para obter informações sobre como gerar iD do cliente e segredo, consulte as seguintes informações conforme necessário:

    - [Configurar o OAuth para Orlando](https://docs.servicenow.com/bundle/orlando-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Configurar o OAuth para Nova Iorque](https://docs.servicenow.com/bundle/newyork-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Configurar o OAuth para Madrid](https://docs.servicenow.com/bundle/madrid-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Configurar o OAuth para Londres](https://docs.servicenow.com/bundle/london-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Configurar o OAuth para Kingston](https://docs.servicenow.com/bundle/kingston-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Configurar o OAuth para Jacarta](https://docs.servicenow.com/bundle/jakarta-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Configurar o OAuth para Istambul](https://docs.servicenow.com/bundle/istanbul-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Configurar o OAuth para Helsínquia](https://docs.servicenow.com/bundle/helsinki-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Configurar o OAuth para Genebra](https://docs.servicenow.com/bundle/geneva-servicenow-platform/page/administer/security/task/t_SettingUpOAuth.html)
> [!NOTE]
> Como parte da definição do "Up OAuth" recomendamos:
>
> 1) **Atualizar a duração do token de atualização para 90 dias (7.776.000 segundos):** Como parte da [Configuração OAuth](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.servicenow.com%2Fbundle%2Fnewyork-platform-administration%2Fpage%2Fadminister%2Fsecurity%2Ftask%2Ft_SettingUpOAuth.html&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C2c6812e429a549e71cdd08d7d1b148d8%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637208431696739125&sdata=Q7mF6Ej8MCupKaEJpabTM56EDZ1T8vFVyihhoM594aA%3D&reserved=0) na fase 2: [Criar um ponto final para os clientes acederem à instância](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.servicenow.com%2Fbundle%2Fnewyork-platform-administration%2Fpage%2Fadminister%2Fsecurity%2Ftask%2Ft_CreateEndpointforExternalClients.html&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C2c6812e429a549e71cdd08d7d1b148d8%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637208431696749123&sdata=hoAJHJAFgUeszYCX1Q%2FXr4N%2FAKiFcm5WV7mwR2UqeWA%3D&reserved=0) Após a definição do ponto final, in ServiceNow pesquisa de lâminas para OAuth do que selecionar Registo de Aplicações. Escolha o nome do OAuth que foi definido e atualize o campo de Refresh token Lifespan para 7.776.000 (90 dias em segundos).
> No final, clique em atualização.
> 2) **Recomendamos estabelecer um procedimento interno para garantir que a ligação permanece viva:** De acordo com o Refresh Token Lifespan para refrescar o token. Certifique-se de realizar as seguintes operações antes do prazo de validade esperado do token (dois dias antes do prazo de vida do Refresh Token expirar recomendamos):
>
> 1. [Complete um processo de sincronização manual para a configuração do conector ITSM](./itsmc-resync-servicenow.md)
> 2. Revogue o velho token de atualização, uma vez que não é recomendado manter as chaves antigas por razões de segurança. Na pesquisa da lâmina ServiceNow para OAuth do sistema do que selecionar Gerir Tokens. Escolha o símbolo antigo da lista de acordo com o nome OAuth e data de validade.
> ![Definição de OAuth do sistema SNOW](media/itsmc-connections/snow-system-oauth.png)
> 3. Clique no Acesso De Revogar e do que no Revogar.

- Instale a Aplicação do Utilizador para integração do Microsoft Log Analytics (app ServiceNow). [Saiba mais](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1 ).
> [!NOTE]
> O ITSMC suporta apenas a aplicação oficial do utilizador para a integração do Microsoft Log Analytics que é descarregada a partir da loja ServiceNow. O ITSMC não suporta qualquer ingestão de códigos no lado do ServiceNow ou na aplicação que não faça parte da solução oficial do ServiceNow. 
- Crie uma função de utilizador de integração para a aplicação do utilizador instalada. Informações sobre como criar o papel de utilizador de integração [estão aqui.](#create-integration-user-role-in-servicenow-app)

### <a name="connection-procedure"></a>**Procedimento de ligação**
Utilize o seguinte procedimento para criar uma ligação ServiceNow:


1. No portal Azure, vá a **Todos os Recursos** e procure o **ServiceDesk (Seu Nome De Espaço-Trabalho)**

2.  No **âmbito do WORKSPACE DATA SOURCES** clique em **ItsM Connections**.
    ![Nova ligação](media/itsmc-connections/add-new-itsm-connection.png)

3. Na parte superior do painel direito, clique em **Adicionar**.

4. Forneça as informações como descrito na tabela seguinte e clique **em OK** para criar a ligação.


> [!NOTE]
> Todos estes parâmetros são obrigatórios.

| **Campo** | **Descrição** |
| --- | --- |
| **Nome de conexão**   | Digite um nome para a instância ServiceNow que pretende ligar com o ITSMC.  Utilize este nome mais tarde no Log Analytics quando configurar itens de trabalho neste ITSM/ ver análises detalhadas de registos. |
| **Tipo de parceiro**   | Selecione **ServiceNow**. |
| **Nome de Utilizador**   | Digite o nome de utilizador de integração que criou na aplicação ServiceNow para suportar a ligação ao ITSMC. Mais informações: [Criar função de utilizador de aplicações ServiceNow](#create-integration-user-role-in-servicenow-app).|
| **Palavra-passe**   | Digite a palavra-passe associada a este nome de utilizador. **Nota:** O nome de utilizador e a palavra-passe são utilizados apenas para gerar fichas de autenticação e não são armazenados em qualquer parte dentro do serviço ITSMC.  |
| **URL do servidor**   | Digite o URL da instância ServiceNow que pretende ligar ao ITSMC. O URL deve apontar para uma versão SaaS suportada com sufixo ".servicenow.com".|
| **ID do cliente**   | Digite o ID do cliente que pretende utilizar para a Autenticação OAuth2, que gerou anteriormente.  Mais informações sobre a geração de identificação do cliente e segredo:   [OAuth Setup](https://wiki.servicenow.com/index.php?title=OAuth_Setup). |
| **Segredo do Cliente**   | Digite o segredo do cliente, gerado para esta identificação.   |
| **Âmbito de sincronização de dados**   | Selecione os itens de trabalho ServiceNow que pretende sincronizar com o Azure Log Analytics, através do ITSMC.  Os valores selecionados são importados para análise de registos.   **Opções:**  Incidentes e Pedidos de Alteração.|
| **Dados de Sincronização** | Digite o número de dias passados de onde deseja os dados. **Limite máximo:** 120 dias. |
| **Criar novo item de configuração na solução ITSM** | Selecione esta opção se pretender criar os itens de configuração no produto ITSM. Quando selecionado, o ITSMC cria os TEC afetados como itens de configuração (em caso de TEC não existentes) no sistema ITSM suportado. **Predefinição:** desativado. |

![Ligação ServiceNow](media/itsmc-connections/itsm-connection-servicenow-connection-latest.png)

**Quando conectado com sucesso, e sincronizado:**

- Artigos de trabalho selecionados da instância ServiceNow são importados para a Azure **Log Analytics.** Pode ver o resumo destes artigos de trabalho no azulejo do conector de gestão de serviços de TI.

- Pode criar incidentes a partir de alertas de Log Analytics ou de registos de registos, ou a partir de alertas Azure neste caso ServiceNow.

Saiba mais: [Crie itens de trabalho ITSM a partir de alertas Azure](./itsmc-overview.md#create-itsm-work-items-from-azure-alerts).


> [!NOTE]
> No ServiceNow existe um limite de taxa para pedidos por hora. Para configurar o limite, utilize-o definindo "Taxa de API de entrada de entrada" na instância ServiceNow.

### <a name="create-integration-user-role-in-servicenow-app"></a>Criar papel de utilizador de integração na aplicação ServiceNow

Utilizador do seguinte procedimento:

1. Visite a [loja ServiceNow](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1) e instale a **App utilizador para o ServiceNow e a Integração OMS do Microsoft** na sua Instância De Conhecimento de Serviço.
   
   >[!NOTE]
   >Como parte da transição em curso do Microsoft Operations Management Suite (OMS) para o Azure Monitor, o OMS é agora referido como Log Analytics.     
2. Após a instalação, visite a barra de navegação esquerda da instância ServiceNow, procure e selecione o integrador Microsoft OMS.  
3. Clique **na lista de verificação de instalações**.

   O estado é apresentado como  **Não completo** se a função do utilizador ainda estiver por criar.

4. Nas caixas de texto, junto ao **Utilizador de Integração Create,** insira o nome de utilizador para o utilizador que pode ligar-se ao ITSMC em Azure.
5. Introduza a palavra-passe para este utilizador e clique **em OK**.  

> [!NOTE]
> 
> Utiliza estas credenciais para fazer a ligação ServiceNow em Azure.

O utilizador recém-criado é apresentado com as funções predefinidas atribuídas.

**Funções predefinidos:**
- personalize_choices
- import_transformer
-   x_mioms_microsoft.user
-   itil
-   template_editor
-   view_changer

Uma vez criado com sucesso o utilizador, o estado da Lista de Verificação de **Instalações** passa para Concluído, enumerando os detalhes da função de utilizador criada para a aplicação.

> [!NOTE]
> 
> O CONECTOR ITSM pode enviar incidentes para o ServiceNow sem quaisquer outros módulos instalados na sua instância ServiceNow. Se estiver a utilizar o módulo EventManagement na sua instância ServiceNow e pretender criar Eventos ou Alertas no ServiceNow utilizando o conector, adicione as seguintes funções ao utilizador de integração:
> 
>    - evt_mgmt_integration
>    - evt_mgmt_operator  


## <a name="connect-provance-to-it-service-management-connector-in-azure"></a>Ligue provance ao conector de gestão de serviços de TI em Azure

As seguintes secções fornecem detalhes sobre como ligar o seu produto Provance ao ITSMC em Azure.

> [!NOTE]
> 
> Propomos aos nossos clientes provance que utilizem a [ação Webhook](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#webhook) para cherwell e provance como outra solução para a integração.

### <a name="prerequisites"></a>Pré-requisitos

Assegurar que os seguintes pré-requisitos sejam cumpridos:


- ITSMC instalado. Mais informações: [Adicionar a Solução de Conector de Gestão de Serviços de TI](./itsmc-overview.md#add-it-service-management-connector).
- A App Provance deve ser registada no Azure AD - e o ID do cliente está disponível. Para obter informações [detalhadas, consulte como configurar a autenticação ativa do diretório](../../app-service/configure-authentication-provider-aad.md).

- Função de utilizador: Administrador.

### <a name="connection-procedure"></a>Procedimento de ligação

Utilize o seguinte procedimento para criar uma ligação à Provance:

1. No portal Azure, vá a **Todos os Recursos** e procure o **ServiceDesk (Seu Nome De Espaço-Trabalho)**

2.  No **âmbito do WORKSPACE DATA SOURCES** clique em **ItsM Connections**.
    ![Nova ligação](media/itsmc-connections/add-new-itsm-connection.png)

3. Na parte superior do painel direito, clique em **Adicionar**.

4. Forneça as informações como descrito na tabela seguinte e clique **em OK** para criar a ligação.

> [!NOTE]
> 
> Todos estes parâmetros são obrigatórios.

| **Campo** | **Descrição** |
| --- | --- |
| **Nome de conexão**   | Digite um nome para a instância Provance que pretende ligar com o ITSMC.  Utilize este nome mais tarde quando configurar itens de trabalho neste ITSM/ ver análises detalhadas de registo. |
| **Tipo de parceiro**   | Selecione **Provance**. |
| **Nome de Utilizador**   | Digite o nome de utilizador que pode ligar-se ao ITSMC.    |
| **Palavra-passe**   | Digite a palavra-passe associada a este nome de utilizador. **Nota:** O nome de utilizador e a palavra-passe são utilizados apenas para gerar fichas de autenticação e não são armazenados em qualquer parte dentro do ITSMC service._|
| **URL do servidor**   | Digite o URL da sua instância provance que pretende ligar ao ITSMC. |
| **ID do cliente**   | Digite o ID do cliente para autenticar esta ligação, que gerou no seu caso Provance.  Mais informações sobre o ID do cliente, veja [como configurar a autenticação ativa do diretório.](../../app-service/configure-authentication-provider-aad.md) |
| **Âmbito de sincronização de dados**   | Selecione os itens de trabalho provance que pretende sincronizar com o Azure Log Analytics, através do ITSMC.  Estes artigos de trabalho são importados para análise de registos.   **Opções:**   Incidentes, Pedidos de Alteração.|
| **Dados de Sincronização** | Digite o número de dias passados de onde deseja os dados. **Limite máximo:** 120 dias. |
| **Criar novo item de configuração na solução ITSM** | Selecione esta opção se pretender criar os itens de configuração no produto ITSM. Quando selecionado, o ITSMC cria os TEC afetados como itens de configuração (em caso de TEC não existentes) no sistema ITSM suportado. **Predefinição:** desativado.|

![Screenshot que realça as listas de Nome de Ligação e Tipo de Parceiro.](media/itsmc-connections/itsm-connections-provance-latest.png)

**Quando conectado com sucesso, e sincronizado:**

- Os artigos de trabalho selecionados desta instância provance são importados para a Azure **Log Analytics.** Pode ver o resumo destes artigos de trabalho no azulejo do conector de gestão de serviços de TI.

- Pode criar incidentes a partir de alertas de Log Analytics ou de registos de registos, ou a partir de alertas Azure neste caso provance.

Saiba mais: [Crie itens de trabalho ITSM a partir de alertas Azure](./itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

## <a name="connect-cherwell-to-it-service-management-connector-in-azure"></a>Ligue cherwell ao conector de gestão de serviços de TI em Azure

As seguintes secções fornecem detalhes sobre como ligar o seu produto Cherwell ao ITSMC em Azure.

> [!NOTE]
> 
> Propomos aos nossos clientes Cherwell que utilizem a [ação Webhook](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#webhook) para cherwell e provance como outra solução para a integração.

### <a name="prerequisites"></a>Pré-requisitos

Assegurar que os seguintes pré-requisitos sejam cumpridos:

- ITSMC instalado. Mais informações: [Adicionar a Solução de Conector de Gestão de Serviços de TI](./itsmc-overview.md#add-it-service-management-connector).
- Identificação do cliente gerada. Mais informações: [Gere a identificação do cliente para a Cherwell.](#generate-client-id-for-cherwell)
- Função de utilizador: Administrador.

### <a name="connection-procedure"></a>Procedimento de ligação

Utilize o seguinte procedimento para criar uma ligação Cherwell:

1. No portal Azure, vá a **Todos os Recursos** e procure o **ServiceDesk (Seu Nome De Espaço-Trabalho)**

2.  No **âmbito do WORKSPACE DATA SOURCES** clique em **ItsM Connections**.
    ![Nova ligação](media/itsmc-connections/add-new-itsm-connection.png)

3. Na parte superior do painel direito, clique em **Adicionar**.

4. Forneça as informações como descrito na tabela seguinte e clique **em OK** para criar a ligação.

> [!NOTE]
> 
> Todos estes parâmetros são obrigatórios.

| **Campo** | **Descrição** |
| --- | --- |
| **Nome de conexão**   | Digite um nome para a instância Cherwell que pretende ligar ao ITSMC.  Utilize este nome mais tarde quando configurar itens de trabalho neste ITSM/ ver análises detalhadas de registo. |
| **Tipo de parceiro**   | Selecione **Cherwell.** |
| **Nome de Utilizador**   | Digite o nome de utilizador Cherwell que pode ligar-se ao ITSMC. |
| **Palavra-passe**   | Digite a palavra-passe associada a este nome de utilizador. **Nota:** O nome de utilizador e a palavra-passe são utilizados apenas para gerar fichas de autenticação e não são armazenados em qualquer parte dentro do serviço ITSMC.|
| **URL do servidor**   | Digite o URL da sua instância Cherwell que pretende ligar ao ITSMC. |
| **ID do cliente**   | Digite o ID do cliente para autenticar esta ligação, que gerou no seu caso Cherwell.   |
| **Âmbito de sincronização de dados**   | Selecione os itens de trabalho cherwell que pretende sincronizar através do ITSMC.  Estes artigos de trabalho são importados para análise de registos.   **Opções:**  Incidentes, Pedidos de Alteração. |
| **Dados de Sincronização** | Digite o número de dias passados de onde deseja os dados. **Limite máximo:** 120 dias. |
| **Criar novo item de configuração na solução ITSM** | Selecione esta opção se pretender criar os itens de configuração no produto ITSM. Quando selecionado, o ITSMC cria os TEC afetados como itens de configuração (em caso de TEC não existentes) no sistema ITSM suportado. **Predefinição:** desativado. |


![Conexão provance](media/itsmc-connections/itsm-connections-cherwell-latest.png)

**Quando conectado com sucesso, e sincronizado:**

- Os artigos de trabalho selecionados desta instância cherwell são importados para a Azure **Log Analytics.** Pode ver o resumo destes artigos de trabalho no azulejo do conector de gestão de serviços de TI.

- Pode criar incidentes a partir de alertas de Log Analytics ou de registos de registos, ou a partir de alertas Azure neste caso Cherwell.

Saiba mais: [Crie itens de trabalho ITSM a partir de alertas Azure](./itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

### <a name="generate-client-id-for-cherwell"></a>Gerar iD de cliente para Cherwell

Para gerar o ID/chave do cliente para Cherwell, utilize o seguinte procedimento:

1. Faça login no seu caso Cherwell como administrador.
2. Clique   >  **nas definições do cliente API de Edição de** Segurança .
3. **Selecione Criar novo** segredo do  >  **cliente** cliente.

    ![Id de utilizador cherwell](media/itsmc-connections/itsmc-cherwell-client-id.png)


## <a name="next-steps"></a>Passos seguintes
 - [Crie itens de trabalho ITSM a partir de alertas Azure](./itsmc-overview.md#create-itsm-work-items-from-azure-alerts)
