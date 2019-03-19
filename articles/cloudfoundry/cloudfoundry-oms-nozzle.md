---
title: Implementar Nozzle do Azure Log Analytics para a Cloud Foundry monitorização | Documentos da Microsoft
description: Orientações passo a passo sobre como implementar o Cloud Foundry loggregator Nozzle para o Azure Log Analytics. Utilize o Nozzle para monitorizar as métricas de estado de funcionamento e desempenho de sistema do Cloud Foundry.
services: virtual-machines-linux
author: ningk
manager: jeconnoc
tags: Cloud-Foundry
ms.assetid: 00c76c49-3738-494b-b70d-344d8efc0853
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/22/2017
ms.author: ningk
ms.openlocfilehash: 6220aebdef6970f3d5f7017e4ae48f6f409ae0ce
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58111468"
---
# <a name="deploy-azure-log-analytics-nozzle-for-cloud-foundry-system-monitoring"></a>Implementar Nozzle do Azure Log Analytics para o monitoramento de sistema do Cloud Foundry

[O Azure Monitor](https://azure.microsoft.com/services/log-analytics/) é um serviço no Azure. Ele ajuda a recolher e analisar os dados que são gerados a partir da nuvem e de ambientes no local.

O Log Analytics Nozzle (o Nozzle) é um componente de Cloud Foundry (CF), que encaminha as métricas do [Cloud Foundry loggregator](https://docs.cloudfoundry.org/loggregator/architecture.html) firehose aos registos do Azure Monitor. Com o Nozzle do utilizador, pode recolher, ver e analisar seu CF estado de funcionamento e desempenho as métricas do sistema, entre múltiplas implementações.

Neste documento, saiba como implementar o Nozzle do seu ambiente de CF e, em seguida, acessar os dados a partir da consola de registos do Azure Monitor.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Pré-requisitos

Os seguintes passos são pré-requisitos para implementar o Nozzle do utilizador.

### <a name="1-deploy-a-cf-or-pivotal-cloud-foundry-environment-in-azure"></a>1. Implementar um ambiente CF ou Pivotal Cloud Foundry no Azure

Pode usar o Nozzle com uma implementação de CF de código-fonte aberto ou uma implementação da Pivotal Cloud Foundry (PCF).

* [Implementar o Cloud Foundry no Azure](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/blob/master/docs/guidance.md)

* [Implementar a Pivotal Cloud Foundry no Azure](https://docs.pivotal.io/pivotalcf/1-11/customizing/azure.html)

### <a name="2-install-the-cf-command-line-tools-for-deploying-the-nozzle"></a>2. Instalar as ferramentas de linha de comando CF para implementar o Nozzle do

O Nozzle é executado como uma aplicação no ambiente de CF. Precisa CF CLI para implementar a aplicação.

O Nozzle também necessita da permissão de acesso para o firehose loggregator e o controlador de Cloud. Para criar e configurar o utilizador, terá do serviço de conta de utilizador e autenticação (UAA).

* [Instalar a CLI do Cloud Foundry](https://docs.cloudfoundry.org/cf-cli/install-go-cli.html)

* [Instalar o cliente de linha de comandos do Cloud Foundry UAA](https://github.com/cloudfoundry/cf-uaac/blob/master/README.md)

Antes de configurar o cliente de linha de comando UAA, certifique-se de que o RubyGems está instalada.

### <a name="3-create-a-log-analytics-workspace-in-azure"></a>3. Criar uma área de trabalho do Log Analytics no Azure

É possível criar a área de trabalho do Log Analytics manualmente ou utilizando um modelo. O modelo implementa uma configuração de pré-configurado de KPI de vistas e alertas para a consola de registos do Azure Monitor. 

#### <a name="to-create-the-workspace-manually"></a>Para criar manualmente a área de trabalho:

1. No portal do Azure, pesquise a lista de serviços no Azure Marketplace e, em seguida, selecione as áreas de trabalho do Log Analytics.
2. Selecione **criar**e, em seguida, selecione opções para os seguintes itens:

   * **Área de trabalho de análise de registo**: Escreva um nome para a área de trabalho.
   * **Subscrição**: Se tiver várias subscrições, selecione aquele que é o mesmo que a implementação de CF.
   * **Grupo de recursos**: Pode criar um novo grupo de recursos ou utilize a mesma com a sua implementação CF.
   * **Localização**: Introduza a localização.
   * **Escalão de preço**: Selecione **OK** para concluir.

Para obter mais informações, consulte [começar a utilizar com os registos do Azure Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started).

#### <a name="to-create-the-log-analytics-workspace-through-the-monitoring-template-from-azure-market-place"></a>Para criar a área de trabalho do Log Analytics através do modelo de monitorização no lugar de mercado do Azure:

1. Abra o portal do Azure.
1. Clique no sinal "+" ou "Criar um recurso" no canto superior esquerdo.
1. Escreva "Cloud Foundry" na janela de pesquisa, selecione "Cloud Foundry monitorização solução".
1. O Cloud Foundry monitorização solução modelo front-página é carregada, clique em "Criar" para iniciar o painel de modelo.
1. Introduza os parâmetros necessários:
    * **Subscrição**: Selecione uma subscrição do Azure para a área de trabalho do Log Analytics, normalmente, o mesmo com a implementação de Cloud Foundry.
    * **Grupo de recursos**: Selecione um grupo de recursos existente ou crie um novo para a área de trabalho do Log Analytics.
    * **Localização do grupo de recursos**: Selecione a localização do grupo de recursos.
    * **OMS_Workspace_Name**: Introduza um nome de área de trabalho, se a área de trabalho não existir, o modelo irá criar um novo.
    * **OMS_Workspace_Region**: Selecione a localização para a área de trabalho.
    * **OMS_Workspace_Pricing_Tier**: Selecione a área de trabalho do Log Analytics SKU. Consulte a [preços orientações](https://azure.microsoft.com/pricing/details/log-analytics/) para referência.
    * **Termos legais**: Clique em termos legais e, em seguida, clique em "Criar" para aceitar o termo legal.
1. Depois de todos os parâmetros forem especificados, clique em "Criar" para implementar o modelo. Quando a implementação estiver concluída, o estado aparecerá no separador de notificação.


## <a name="deploy-the-nozzle"></a>Implementar Nozzle do

Existem duas formas diferentes de implementar o Nozzle: como um mosaico PCF ou como uma aplicação CF.

### <a name="deploy-the-nozzle-as-a-pcf-ops-manager-tile"></a>Implementar Nozzle do como um mosaico de PCF Ops Manager

Siga os passos para [instalar e configurar o Nozzle do Azure Log Analytics para o PCF](https://docs.pivotal.io/partners/azure-log-analytics-nozzle/installing.html). Essa é a abordagem simplificada, o mosaico do Gestor de operações do PCF irá configurar e enviar por push o nozzle do automaticamente. 

### <a name="deploy-the-nozzle-manually-as-a-cf-application"></a>Implementar Nozzle do manualmente como uma aplicação de CF

Se não estiver a utilizar o PCF Ops Manager, implemente o Nozzle como uma aplicação. As secções seguintes descrevem este processo.

#### <a name="sign-in-to-your-cf-deployment-as-an-admin-through-cf-cli"></a>Inicie sessão na sua implementação CF como um administrador através do CF CLI

Execute o seguinte comando:
```
cf login -a https://api.${SYSTEM_DOMAIN} -u ${CF_USER} --skip-ssl-validation
```

"SYSTEM_DOMAIN" é o seu nome de domínio CF. Pode recuperá-lo ao pesquisar "SYSTEM_DOMAIN" em seu arquivo de manifesto de implantação CF. 

"CF_User" é o nome de administrador CF. Pode obter o nome e a palavra-passe ao pesquisar a seção "scim", procurando o nome e o "cf_admin_password" em seu arquivo de manifesto de implantação CF.

#### <a name="create-a-cf-user-and-grant-required-privileges"></a>Criar um utilizador CF e conceder privilégios necessários

Execute os seguintes comandos:
```
uaac target https://uaa.${SYSTEM_DOMAIN} --skip-ssl-validation
uaac token client get admin
cf create-user ${FIREHOSE_USER} ${FIREHOSE_USER_PASSWORD}
uaac member add cloud_controller.admin ${FIREHOSE_USER}
uaac member add doppler.firehose ${FIREHOSE_USER}
```

"SYSTEM_DOMAIN" é o seu nome de domínio CF. Pode recuperá-lo ao pesquisar "SYSTEM_DOMAIN" em seu arquivo de manifesto de implantação CF.

#### <a name="download-the-latest-log-analytics-nozzle-release"></a>Baixe a versão mais recente do Nozzle do Log Analytics

Execute o seguinte comando:
```
git clone https://github.com/Azure/oms-log-analytics-firehose-nozzle.git
cd oms-log-analytics-firehose-nozzle
```

#### <a name="set-environment-variables"></a>Definir variáveis de ambiente

Agora pode definir variáveis de ambiente no ficheiro manifest.yml no seu diretório atual. O código a seguir mostra o manifesto da aplicação para o Nozzle do utilizador. Substitua os valores com as suas informações de área de trabalho do Log Analytics específicas.

```
OMS_WORKSPACE             : Log Analytics workspace ID: Open your Log Analytics workspace in the Azure portal, select **Advanced settings**, select **Connected Sources**, and select **Windows Servers**.
OMS_KEY                   : OMS key: Open your Log Analytics workspace in the Azure portal, select **Advanced settings**, select **Connected Sources**, and select **Windows Servers**.
OMS_POST_TIMEOUT          : HTTP post timeout for sending events to Azure Monitor logs. The default is 10 seconds.
OMS_BATCH_TIME            : Interval for posting a batch to Azure Monitor logs. The default is 10 seconds.
OMS_MAX_MSG_NUM_PER_BATCH : The maximum number of messages in a batch to Azure Monitor logs. The default is 1000.
API_ADDR                  : The API URL of the CF environment. For more information, see the preceding section, "Sign in to your CF deployment as an admin through CF CLI."
DOPPLER_ADDR              : Loggregator's traffic controller URL. For more information, see the preceding section, "Sign in to your CF deployment as an admin through CF CLI."
FIREHOSE_USER             : CF user you created in the preceding section, "Create a CF user and grant required privileges." This user has firehose and Cloud Controller admin access.
FIREHOSE_USER_PASSWORD    : Password of the CF user above.
EVENT_FILTER              : Event types to be filtered out. The format is a comma-separated list. Valid event types are METRIC, LOG, and HTTP.
SKIP_SSL_VALIDATION       : If true, allows insecure connections to the UAA and the traffic controller.
CF_ENVIRONMENT            : Enter any string value for identifying logs and metrics from different CF environments.
IDLE_TIMEOUT              : The Keep Alive duration for the firehose consumer. The default is 60 seconds.
LOG_LEVEL                 : The logging level of the Nozzle. Valid levels are DEBUG, INFO, and ERROR.
LOG_EVENT_COUNT           : If true, the total count of events that the Nozzle has received and sent are logged to Azure Monitor logs as CounterEvents.
LOG_EVENT_COUNT_INTERVAL  : The time interval of the logging event count to Azure Monitor logs. The default is 60 seconds.
```

### <a name="push-the-application-from-your-development-computer"></a>Envie a aplicação do seu computador de desenvolvimento

Certifique-se de que está sob a pasta log-analytics-firehose-nozzle do oms. Execute o seguinte comando:
```
cf push
```

## <a name="validate-the-nozzle-installation"></a>Validar a instalação de Nozzle

### <a name="from-apps-manager-for-pcf"></a>A partir do Gestor de aplicações (para o PCF)

1. Inicie sessão no Ops Manager e certifique-se de que o mosaico é apresentado no dashboard de instalação.
2. Inicie sessão no Gestor de aplicações, certifique-se de que o espaço que criou para o Nozzle está listado no relatório de utilização e confirme que o estado é normal.

### <a name="from-your-development-computer"></a>Do seu computador de desenvolvimento

Na janela da CLI de CF, escreva:
```
cf apps
```
Certificar-se de que o aplicativo Nozzle do OMS está em execução.

## <a name="view-the-data-in-the-azure-portal"></a>Ver os dados no portal do Azure

Se tiver implementado a solução de monitorização através do modelo do mercado, aceda ao portal do Azure e localize a solução. Pode encontrar a solução no grupo de recursos que especificou no modelo. Clique a solução, navegue para "log analytics consola", as vistas pré-configuradas estão listadas, com KPIs de sistema do Cloud Foundry superior, os dados da aplicação, alertas e métricas de estado de funcionamento da VM. 

Se tiver criado a área de trabalho do Log Analytics manualmente, siga os passos abaixo para criar as vistas e alertas:

### <a name="1-import-the-oms-view"></a>1. Importar o modo de exibição do OMS

No portal do OMS, navegue até **estruturador de vistas** > **importação** > **procurar**e selecione um dos ficheiros omsview. Por exemplo, seleccione *Cloud Foundry.omsview*e guarde o modo de exibição. Agora um mosaico é apresentado no **descrição geral** página. Selecione-o para ver as métricas de visualizados.

Pode personalizar estas vistas ou criar novas vistas através de **estruturador de vistas**.

O *"Cloud Foundry.omsview"* é uma versão de pré-visualização do modelo de exibição do Cloud Foundry OMS. Este é um modelo padrão totalmente configurado. Se tiver sugestões ou comentários sobre o modelo, enviá-las para o [emitir secção](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues).

### <a name="2-create-alert-rules"></a>2. Criar regras de alertas

Pode [criar os alertas](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts)e personalizar as consultas e os valores de limiar conforme necessário. A seguir são apresentadas alertas:

| Consulta de pesquisa                                                                  | Gerar alerta com base em | Descrição                                                                       |
| ----------------------------------------------------------------------------- | ----------------------- | --------------------------------------------------------------------------------- |
| Type=CF_ValueMetric_CL Origin_s=bbs Name_s="Domain.cf-apps"                   | Número de resultados < 1   | **BBS. Aplicações de Domain.cf** indica se o domínio de aplicativos está atualizado. Isso significa que os pedidos de aplicação CF do controlador de Cloud são sincronizados com bbs. LRPsDesired (AIs Diego desejada) para execução. Não foram recebidos dados significa que domínio cf-aplicações não está atualizado na janela de tempo especificado. |
| Type=CF_ValueMetric_CL Origin_s=rep Name_s=UnhealthyCell Value_d>1            | Número de resultados > 0   | Para as células Diego, 0 significa bom estado de funcionamento e 1 significa mau estado de funcionamento. Defina o alerta se várias células Diego mau estado de funcionamento são detetadas na janela de tempo especificado. |
| Type=CF_ValueMetric_CL Origin_s="bosh-hm-forwarder" Name_s="system.healthy" Value_d=0 | Número de resultados > 0 | 1 significa que o sistema está em bom estado e 0 significa que o sistema não está em bom estado. |
| Type=CF_ValueMetric_CL Origin_s=route_emitter Name_s=ConsulDownMode Value_d>0 | Número de resultados > 0   | Consul emite periodicamente o estado de funcionamento. 0 significa que o sistema está em bom estado e 1 significa que o emissor de rota Deteta que Consul está desativado. |
| Tipo = CF_CounterEvent_CL Origin_s = Delta_d DopplerServer (Name_s="TruncatingBuffer.DroppedMessages" ou Name_s="doppler.shedEnvelopes") > 0 | Número de resultados > 0 | O número de delta de mensagens caiu intencionalmente Doppler devido a pressão. |
| Type=CF_LogMessage_CL SourceType_s=LGR MessageType_s=ERR                      | Número de resultados > 0   | Loggregator emite **LGR** para indicar problemas com o processo de Registro em log. Um exemplo desse problema é quando o resultado de mensagem do registo é demasiado elevado. |
| Type=CF_ValueMetric_CL Name_s=slowConsumerAlert                               | Número de resultados > 0   | Quando o Nozzle recebe um alerta de consumo lentos de loggregator, envia os **slowConsumerAlert** regista ValueMetric para o Azure Monitor. |
| Type=CF_CounterEvent_CL Job_s=nozzle Name_s=eventsLost Delta_d>0              | Número de resultados > 0   | Se o número de delta de eventos perdidos atinge um limiar, significa que o Nozzle pode ter um problema ao executar. |

## <a name="scale"></a>Escala

Pode dimensionar o Nozzle do utilizador e a loggregator.

### <a name="scale-the-nozzle"></a>Dimensionar o Nozzle do

Deve começar com, pelo menos, duas instâncias do Nozzle do utilizador. O firehose distribui a carga de trabalho em todas as instâncias do Nozzle do utilizador.
Para certificar-se de que o Nozzle pode acompanhar o tráfego de dados do firehose, configure as **slowConsumerAlert** alerta (listada na secção anterior, "Criar regras de alerta"). Depois de ter sido alertado, siga os [orientações para Nozzle lenta](https://docs.pivotal.io/pivotalcf/1-11/loggregator/log-ops-guide.html#slow-noz) para determinar se o dimensionamento é necessário.
Para aumentar verticalmente o bocal, utilize o Gestor de aplicações ou a CLI CF para aumentar os números de instância ou os recursos de memória ou disco para o Nozzle do utilizador.

### <a name="scale-the-loggregator"></a>Dimensionar o loggregator

Loggregator envia uma **LGR** mensagem do registo para indicar problemas com o processo de Registro em log. Pode monitorizar o alerta para determinar se o loggregator tem de ser aumentado.
Para aumentar verticalmente o loggregator, aumentar o tamanho do Doppler buffer ou adicionar as instâncias de servidor Doppler adicional no manifesto CF. Para obter mais informações, consulte [as orientações para dimensionar o loggregator](https://docs.cloudfoundry.org/running/managing-cf/logging-config.html#scaling).

## <a name="update"></a>Atualizar

Para atualizar o Nozzle com uma versão mais recente, transferir a nova versão de Nozzle, siga os passos na secção "Implementar Nozzle do" anterior e enviar por push o aplicativo novamente.

### <a name="remove-the-nozzle-from-ops-manager"></a>Remover o Nozzle do Ops Manager

1. Inicie sessão no Ops Manager.
2. Localize a **Nozzle do Microsoft Azure Log Analytics para o PCF** mosaico.
3. Selecione o ícone de lixo e confirme a eliminação.

### <a name="remove-the-nozzle-from-your-development-computer"></a>Remover o Nozzle do seu computador de desenvolvimento

Na janela da CLI de CF, escreva:
```
cf delete <App Name> -r
```

Se remover o bocal, os dados no portal do OMS não são removidos automaticamente. Data de expiração com base na sua configuração de retenção do registos do Azure Monitor.

## <a name="support-and-feedback"></a>Suporte e comentários

Nozzle de análise de registo do Azure é de código fonte aberto. Envie suas dúvidas e comentários para o [secção do GitHub](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues). Para abrir um pedido de suporte do Azure, escolha "Executar fundição de nuvem de Máquina Virtual" como a categoria de serviço. 

## <a name="next-step"></a>Passo seguinte

A partir de PCF2.0, métricas de desempenho da VM são transferidas para nozzle do Log Analytics do Azure, o reencaminhador de métricas do sistema e integradas para a área de trabalho do Log Analytics. Já não tem o agente Log Analytics para as métricas de desempenho da VM. No entanto pode ainda utilizar o agente Log Analytics para recolher informações do Syslog. O agente Log Analytics é instalado como um complemento Bosh às suas VMs CF. 

Para obter detalhes, consulte [agente de implementar o Log Analytics para a implementação de Cloud Foundry](https://github.com/Azure/oms-agent-for-linux-boshrelease).
