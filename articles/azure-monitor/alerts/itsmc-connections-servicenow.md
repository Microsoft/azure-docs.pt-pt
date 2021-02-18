---
title: Conecte o ServiceNow com o conector de gestão de serviços de TI
description: Saiba como ligar o ServiceNow ao It Service Management Connector (ITSMC) no Azure Monitor para monitorizar e gerir centralmente os itens de trabalho DO ITSM.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/21/2020
ms.openlocfilehash: daaaf25bc1739b57d4696dc8978a330b00b08f70
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100617951"
---
# <a name="connect-servicenow-with-it-service-management-connector"></a>Conecte o ServiceNow com o conector de gestão de serviços de TI

Este artigo mostra-lhe como configurar a ligação entre uma instância ServiceNow e o It Service Management Connector (ITSMC) em Log Analytics, para que possa gerir centralmente os seus itens de trabalho de Gestão de Serviços de TI (ITSM).

## <a name="prerequisites"></a>Pré-requisitos
Certifique-se de que cumpre os seguintes pré-requisitos para a ligação.

### <a name="itsmc-installation"></a>Instalação ITSMC

Para obter informações sobre a instalação do ITSMC, consulte [adicionar a solução de conector de gestão de serviços de TI.](./itsmc-definition.md#add-it-service-management-connector)

> [!NOTE]
> A ITSMC suporta apenas o software oficial como uma oferta de serviço (SaaS) do ServiceNow. As missões privadas do ServiceNow não são suportadas.

### <a name="oauth-setup"></a>Configuração OAuth

As versões suportadas pelo ServiceNow incluem Paris, Orlando, Nova Iorque, Madrid, Londres, Kingston, Jacarta, Istambul, Helsínquia e Genebra.

Os administradores do ServiceNow devem gerar uma identificação do cliente e um segredo de cliente para o seu caso ServiceNow. Consulte as seguintes informações conforme necessário:

- [Configurar o OAuth para Paris](https://docs.servicenow.com/bundle/paris-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [Configurar o OAuth para Orlando](https://docs.servicenow.com/bundle/orlando-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [Configurar o OAuth para Nova Iorque](https://docs.servicenow.com/bundle/newyork-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [Configurar o OAuth para Madrid](https://docs.servicenow.com/bundle/madrid-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [Configurar o OAuth para Londres](https://docs.servicenow.com/bundle/london-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [Configurar o OAuth para Kingston](https://docs.servicenow.com/bundle/kingston-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [Configurar o OAuth para Jacarta](https://docs.servicenow.com/bundle/jakarta-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [Configurar o OAuth para Istambul](https://docs.servicenow.com/bundle/istanbul-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [Configurar o OAuth para Helsínquia](https://docs.servicenow.com/bundle/helsinki-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [Configurar o OAuth para Genebra](https://docs.servicenow.com/bundle/geneva-servicenow-platform/page/administer/security/task/t_SettingUpOAuth.html)

Como parte da criação da OAuth, recomendamos:

1. [Criar um ponto final para os clientes acederem ao caso.](https://docs.servicenow.com/bundle/newyork-platform-administration/page/administer/security/task/t_CreateEndpointforExternalClients.html)

1. Atualizar o tempo de vida útil do token de atualização:

   1. No painel **ServiceNow,** procure **por OAuth do Sistema** e, em seguida, selecione Registo de **Aplicações**. 
   1. Selecione o nome do OAuth que foi definido e **altere Refresh Token Lifespan** para **7.776.000 segundos** (90 dias). 
   1. Selecione **Atualizar**. 

1. Estabeleça um procedimento interno para garantir que a ligação permaneça viva. Alguns dias antes da expiração prevista da duração do token de atualização, efetuar as seguintes operações:

   1. [Preencha um processo de sincronização manual para a configuração do conector ITSM](./itsmc-resync-servicenow.md).

   1. Revogue para o velho token refresh. Não recomendamos guardar chaves velhas por razões de segurança. 
   
      1. No painel **ServiceNow,** procure **por OAuth do Sistema** e, em seguida, selecione Gerir **Tokens**. 
      
      1. Selecione o token antigo da lista de acordo com o nome OAuth e data de validade.

         ![Screenshot que mostra uma lista de fichas para OAuth.](media/itsmc-connections-servicenow/snow-system-oauth.png)
      1. **Selecione Revogar o Acesso**  >  **Revogado**.

## <a name="install-the-user-app-and-create-the-user-role"></a>Instale a aplicação do utilizador e crie a função de utilizador

Utilize o seguinte procedimento para instalar a aplicação do utilizador Service Now e criar a função de utilizador de integração para a empresa. Você usará estas credenciais para fazer a ligação ServiceNow em Azure.

> [!NOTE]
> O ITSMC suporta apenas a aplicação oficial do utilizador para a integração do Microsoft Log Analytics que é descarregada a partir da loja ServiceNow. O ITSMC não suporta qualquer ingestão de códigos no lado do ServiceNow ou qualquer aplicação que não faça parte da solução oficial do ServiceNow. 

1. Visite a [loja ServiceNow](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1) e instale **a App do Utilizador para o ServiceNow e a Integração OMS do Microsoft** no seu caso ServiceNow.
   
   >[!NOTE]
   >Como parte da transição em curso do Microsoft Operations Management Suite (OMS) para a Azure Monitor, o OMS chama-se Log Analytics.     
2. Após a instalação, vá à barra de navegação esquerda da instância ServiceNow e, em seguida, procure e selecione **o integrador Microsoft OMS**.  
3. Selecione **a lista de verificação de instalações**.

   O estado é apresentado como  **Não completo** porque a função do utilizador ainda não foi criada.

4. Na caixa de texto ao lado **do Utilizador de integração Create,** insira o nome para o utilizador que pode ligar-se ao ITSMC em Azure.
5. Introduza a palavra-passe para este utilizador e, em seguida, selecione **OK**.  

O utilizador recém-criado é apresentado com as funções predefinidas atribuídas:

- personalize_choices
- import_transformer
- x_mioms_microsoft.user
- itil
- template_editor
- view_changer

Depois de criar com sucesso o utilizador, o estado da Lista de Verificação de **Instalações** move-se para **Complete e** lista os detalhes da função de utilizador criada para a aplicação.

> [!NOTE]
> O ITSMC pode enviar incidentes para o ServiceNow sem quaisquer outros módulos instalados na sua instância ServiceNow. Se estiver a utilizar o módulo EventManagement na sua instância ServiceNow e pretender criar eventos ou alertas no ServiceNow utilizando o conector, adicione as seguintes funções ao utilizador de integração:
> 
> - evt_mgmt_integration
> - evt_mgmt_operator  

## <a name="create-a-connection"></a>Criar uma ligação
Utilize o seguinte procedimento para criar uma ligação ServiceNow.

> [!NOTE]
> Os alertas enviados pelo Azure Monitor podem criar um dos seguintes elementos no ServiceNow: eventos, incidentes ou alertas. 

1. No portal Azure, vá a **Todos os Recursos** e procure o **ServiceDesk (YourWorkspaceName)**.

2. No **âmbito das Fontes de Dados do Espaço de Trabalho,** selecione **itsm connections**.

   ![Screenshot que mostra a seleção de uma fonte de dados.](media/itsmc-overview/add-new-itsm-connection.png)

3. Na parte superior do painel direito, **selecione Adicionar**.

4. Forneça as informações descritas na tabela seguinte e, em seguida, selecione **OK**.

   | **Campo** | **Descrição** |
   | --- | --- |
   | **Nome de conexão**   | Insira um nome para o caso ServiceNow que pretende ligar-se ao ITSMC. Utilize este nome mais tarde no Log Analytics quando configurar itens de trabalho ITSM e ver análises detalhadas. |
   | **Tipo de Parceiro**   | Selecione **ServiceNow**. |
   | **URL do Servidor**   | Introduza o URL da instância ServiceNow que pretende ligar ao ITSMC. O URL deve apontar para uma versão SaaS suportada com o *sufixo .servicenow.com* (por `https://XXXXX.service-now.com/` exemplo).|
   | **Nome de Utilizador**   | Introduza o nome de utilizador de integração que criou na app ServiceNow para suportar a ligação ao ITSMC.|
   | **Palavra-passe**   | Introduza a palavra-passe associada a este nome de utilizador. **Nota:** O nome de utilizador e a palavra-passe são utilizados apenas para gerar fichas de autenticação. Não estão guardados em nenhum lugar dentro do serviço ITSMC.  |
   | **ID do cliente**   | Insira o ID do cliente que pretende utilizar para a autenticação OAuth2, que gerou anteriormente. Para obter mais informações sobre a geração de um ID do cliente e um segredo, consulte [Configurar OAuth](https://old.wiki/index.php/OAuth_Setup). |
   | **Segredo do Cliente**   | Insira o segredo do cliente gerado para esta identificação.   |
   | **Âmbito de sincronização de dados (em dias)** | Insira o número de dias passados de onde deseja os dados. O limite é de 120 dias. |
   | **Artigos de trabalho para sincronizar**   | Selecione os itens de trabalho ServiceNow que pretende sincronizar com o Azure Log Analytics, através do ITSMC. Os valores selecionados são importados para o Log Analytics. As opções são incidentes e pedidos de alteração.|
   | **Criar novo item de configuração no produto ITSM** | Selecione esta opção se pretender criar os itens de configuração no produto ITSM. Quando é selecionado, o ITSMC cria itens de configuração (se não existir nenhum) no sistema ITSM suportado. É desativado por defeito. |

![Screenshot de caixas e opções para adicionar uma ligação ServiceNow.](media/itsmc-connections-servicenow/itsm-connection-servicenow-connection-latest.png)

Quando estiver ligado e sincronizado com sucesso:

- Os itens de trabalho selecionados da instância ServiceNow são importados para o Log Analytics. Pode ver o resumo destes artigos de trabalho no azulejo do conector de gestão de serviços de **TI.**

- Pode criar incidentes a partir de alertas de Log Analytics ou registos de registos, ou a partir de alertas Azure neste caso ServiceNow.

> [!NOTE]
> O ServiceNow tem um limite de tarifa para pedidos por hora. Para configurar o limite, defina **a taxa de API de entrada REST limitando** na instância ServiceNow.

## <a name="next-steps"></a>Passos seguintes

* [Visão geral do conector ITSM](itsmc-overview.md)
* [Crie itens de trabalho ITSM a partir de alertas Azure](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts)
* [Problemas de resolução de problemas no conector ITSM](./itsmc-resync-servicenow.md)
