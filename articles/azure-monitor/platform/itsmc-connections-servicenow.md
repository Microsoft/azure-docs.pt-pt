---
title: Conecte o ServiceNow com o conector de gestão de serviços de TI
description: Este artigo fornece informações sobre como servirNow com o It Service Management Connector (ITSMC) em Azure Monitor para monitorizar e gerir centralmente os itens de trabalho ITSM.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/21/2020
ms.openlocfilehash: 662c36e4f0082c376a6e250e9a0885f0cd225964
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/22/2020
ms.locfileid: "97729722"
---
# <a name="connect-servicenow-with-it-service-management-connector"></a>Conecte o ServiceNow com o conector de gestão de serviços de TI

Este artigo fornece informações sobre como configurar a ligação entre a sua instância ServiceNow e o It Service Management Connector (ITSMC) no Log Analytics para gerir centralmente os seus itens de trabalho.

As seguintes secções fornecem detalhes sobre como ligar o seu produto ServiceNow ao ITSMC em Azure.

## <a name="prerequisites"></a>Pré-requisitos
Assegurar que os seguintes pré-requisitos sejam cumpridos:
- ITSMC instalado. Mais informações: [Adicionar a Solução de Conector de Gestão de Serviços de TI](./itsmc-definition.md#add-it-service-management-connector).
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
>     1. [Complete um processo de sincronização manual para a configuração do conector ITSM](./itsmc-resync-servicenow.md)
>     2. Revogue o velho token de atualização, uma vez que não é recomendado manter as chaves antigas por razões de segurança. Na pesquisa da lâmina ServiceNow para OAuth do sistema do que selecionar Gerir Tokens. Escolha o símbolo antigo da lista de acordo com o nome OAuth e data de validade.
> ![Definição de OAuth do sistema SNOW](media/itsmc-connections/snow-system-oauth.png)
>     3. Clique no Acesso De Revogar e do que no Revogar.

- Instale a Aplicação do Utilizador para integração do Microsoft Log Analytics (app ServiceNow). [Saiba mais](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1 ).
> [!NOTE]
> O ITSMC suporta apenas a aplicação oficial do utilizador para a integração do Microsoft Log Analytics que é descarregada a partir da loja ServiceNow. O ITSMC não suporta qualquer ingestão de códigos no lado do ServiceNow ou na aplicação que não faça parte da solução oficial do ServiceNow. 
- Crie uma função de utilizador de integração para a aplicação do utilizador instalada. Informações sobre como criar o papel de utilizador de integração [estão aqui.](#create-integration-user-role-in-servicenow-app)

## <a name="connection-procedure"></a>**Procedimento de ligação**
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
| **ID de Cliente**   | Digite o ID do cliente que pretende utilizar para a Autenticação OAuth2, que gerou anteriormente.  Mais informações sobre a geração de identificação do cliente e segredo:   [OAuth Setup](https://wiki.servicenow.com/index.php?title=OAuth_Setup). |
| **Segredo do Cliente**   | Digite o segredo do cliente, gerado para esta identificação.   |
| **Âmbito de sincronização de dados**   | Selecione os itens de trabalho ServiceNow que pretende sincronizar com o Azure Log Analytics, através do ITSMC.  Os valores selecionados são importados para análise de registos.   **Opções:**  Incidentes e Pedidos de Alteração.|
| **Dados de Sincronização** | Digite o número de dias passados de onde deseja os dados. **Limite máximo:** 120 dias. |
| **Criar novo item de configuração na solução ITSM** | Selecione esta opção se pretender criar os itens de configuração no produto ITSM. Quando selecionado, o ITSMC cria os TEC afetados como itens de configuração (em caso de TEC não existentes) no sistema ITSM suportado. **Predefinição:** desativado. |

![Ligação ServiceNow](media/itsmc-connections/itsm-connection-servicenow-connection-latest.png)

**Quando conectado com sucesso, e sincronizado:**

- Artigos de trabalho selecionados da instância ServiceNow são importados para a Azure **Log Analytics.** Pode ver o resumo destes artigos de trabalho no azulejo do conector de gestão de serviços de TI.

- Pode criar incidentes a partir de alertas de Log Analytics ou de registos de registos, ou a partir de alertas Azure neste caso ServiceNow.

> [!NOTE]
> No ServiceNow existe um limite de taxa para pedidos por hora. Para configurar o limite, utilize-o definindo "Taxa de API de entrada de entrada" na instância ServiceNow.

## <a name="create-integration-user-role-in-servicenow-app"></a>Criar papel de utilizador de integração na aplicação ServiceNow

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
> O CONECTOR ITSM pode enviar incidentes para o ServiceNow sem quaisquer outros módulos instalados na sua instância ServiceNow. Se estiver a utilizar o módulo EventManagement na sua instância ServiceNow e pretender criar Eventos ou Alertas no ServiceNow utilizando o conector, adicione as seguintes funções ao utilizador de integração:
> 
>    - evt_mgmt_integration
>    - evt_mgmt_operator  


## <a name="next-steps"></a>Passos seguintes

* [Visão geral do conector ITSM](itsmc-overview.md)
* [Crie itens de trabalho ITSM a partir de alertas Azure](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts)
* [Resolver problemas do Conector do ITSM](./itsmc-resync-servicenow.md)