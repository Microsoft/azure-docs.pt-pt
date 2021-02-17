---
title: Implementar o Azure Log Analytics Nozzle para monitorização de Cloud Foundry
description: Orientação passo a passo sobre a implementação do bocal do loggregator Cloud Foundry para Azure Log Analytics. Utilize o Bocal para monitorizar as métricas de saúde e desempenho do sistema Cloud Foundry.
services: virtual-machines-linux
author: ningk
tags: Cloud-Foundry
ms.assetid: 00c76c49-3738-494b-b70d-344d8efc0853
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/22/2017
ms.author: ningk
ms.openlocfilehash: 9fafa9bd014a44fdd0098ef2364375c3f9672bea
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100571069"
---
# <a name="deploy-azure-log-analytics-nozzle-for-cloud-foundry-system-monitoring"></a>Implementar o Azure Log Analytics Nozzle para monitorização do sistema Cloud Foundry

[Azure Monitor](https://azure.microsoft.com/services/log-analytics/) é um serviço em Azure. Ajuda-o a recolher e analisar dados que são gerados a partir dos ambientes da sua nuvem e no local.

O Bocal De Analítica de Log (o Bocal) é um componente cloud foundry (CF), que encaminha métricas da mangueira de incêndio do [loggregator cloud Foundry](https://docs.cloudfoundry.org/loggregator/architecture.html) para os registos do Monitor Azure. Com o Bocal, pode recolher, visualizar e analisar as métricas de saúde e desempenho do seu sistema CF, através de múltiplas implementações.

Neste documento, aprende-se a implantar o Nozzle para o seu ambiente CF e, em seguida, acede aos dados a partir da consola de registos Azure Monitor.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Pré-requisitos

Os seguintes passos são pré-requisitos para a implantação do Bocal.

### <a name="1-deploy-a-cf-or-pivotal-cloud-foundry-environment-in-azure"></a>1. Implementar um ambiente de fundimento de nuvens cf ou pivotal em Azure

Pode utilizar o Bocal com uma implementação CF de código aberto ou uma implementação de Fundição de Nuvem Pivotal (PCF).

* [Implementar o Cloud Foundry no Azure](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/blob/master/docs/guidance.md)

* [Implementar a Base De Nuvem Pivotal em Azure](https://docs.pivotal.io/pivotalcf/1-11/customizing/azure.html)

### <a name="2-install-the-cf-command-line-tools-for-deploying-the-nozzle"></a>2. Instale as ferramentas da linha de comando CF para a implantação do Bocal

O Bocal funciona como uma aplicação em ambiente CF. Precisa do CF CLI para implementar a aplicação.

O Bocal também precisa de autorização de acesso à mangueira de incêndio do loggregator e ao Controlador de Nuvem. Para criar e configurar o utilizador, precisa do serviço de Conta de Utilizador e Autenticação (UAA).

* [Instalar Cloud Foundry CLI](https://docs.cloudfoundry.org/cf-cli/install-go-cli.html)

* [Instalar o cliente da linha de comando Cloud Foundry UAA](https://github.com/cloudfoundry/cf-uaac/blob/master/README.md)

Antes de configurar o cliente da Linha de Comando da UAA, certifique-se de que a RubyGems está instalada.

### <a name="3-create-a-log-analytics-workspace-in-azure"></a>3. Criar um espaço de trabalho log Analytics em Azure

Pode criar manualmente o espaço de trabalho Log Analytics ou utilizando um modelo. O modelo irá implementar uma configuração de visualizações e alertas KPI pré-configurados para a consola de registos Azure Monitor. 

#### <a name="to-create-the-workspace-manually"></a>Para criar o espaço de trabalho manualmente:

1. No portal Azure, procure a lista de serviços no Azure Marketplace e, em seguida, selecione log analytics espaços de trabalho.
2. Selecione **Criar** e, em seguida, selecione escolhas para os seguintes itens:

   * **Log Analytics workspace**: Digite um nome para o seu espaço de trabalho.
   * **Subscrição**: Se tiver várias subscrições, escolha a que é igual à sua implementação de CF.
   * **Grupo de recursos**: Pode criar um novo grupo de recursos ou utilizar o mesmo com a sua implementação de CF.
   * **Localização**: Insira o local.
   * **Nível de preços**: Selecione **OK** para completar.

Para obter mais informações, consulte [Começar com os registos do Azure Monitor](../azure-monitor/overview.md).

#### <a name="to-create-the-log-analytics-workspace-through-the-monitoring-template-from-azure-market-place"></a>Para criar o espaço de trabalho Log Analytics através do modelo de monitorização a partir do mercado Azure:

1. Abre o Portal do Azure.
1. Clique no sinal "+" ou "Criar um recurso" no canto superior esquerdo.
1. Digite "Cloud Foundry" na janela de pesquisa, selecione "Cloud Foundry Monitoring Solution".
1. A primeira página do modelo de solução de monitorização Cloud Foundry está carregada, clique em "Criar" para lançar a lâmina do modelo.
1. Introduza os parâmetros necessários:
    * **Subscrição**: Selecione uma subscrição Azure para o espaço de trabalho Log Analytics, normalmente o mesmo com a implementação de Cloud Foundry.
    * **Grupo de recursos**: Selecione um grupo de recursos existente ou crie um novo para o espaço de trabalho Log Analytics.
    * **Localização do Grupo de Recursos**: Selecione a localização do grupo de recursos.
    * **OMS_Workspace_Name**: Introduza um nome de espaço de trabalho, se o espaço de trabalho não existir, o modelo criará um novo.
    * **OMS_Workspace_Region**: Selecione a localização para o espaço de trabalho.
    * **OMS_Workspace_Pricing_Tier**: Selecione o espaço de trabalho Log Analytics SKU. Consulte [a orientação de preços](https://azure.microsoft.com/pricing/details/log-analytics/) para referência.
    * **Termos legais**: Clique em termos legais e, em seguida, clique em "Criar" para aceitar o termo legal.
1. Depois de especificados todos os parâmetros, clique em "Criar" para implementar o modelo. Quando a implementação estiver concluída, o estado aparecerá no separador de notificação.


## <a name="deploy-the-nozzle"></a>Implementar o Bocal

Existem algumas maneiras diferentes de implantar o Bocal: como um azulejo pcf ou como uma aplicação CF.

### <a name="deploy-the-nozzle-as-a-pcf-ops-manager-tile"></a>Implementar o Bocal como azulejo do PcF Ops Manager

Siga os passos para [instalar e configurar o Bocal Azure Log Analytics para PCF](https://docs.pivotal.io/partners/azure-log-analytics-nozzle/installing.html). Esta é a abordagem simplificada, o azulejo do gerente do PCF Ops configurará automaticamente e empurrará o bocal. 

### <a name="deploy-the-nozzle-manually-as-a-cf-application"></a>Implemente o Bocal manualmente como uma aplicação CF

Se não estiver a utilizar o PcF Ops Manager, insaça o Nozzle como uma aplicação. As seguintes secções descrevem este processo.

#### <a name="sign-in-to-your-cf-deployment-as-an-admin-through-cf-cli"></a>Inscreva-se na sua implantação de CF como administrador através do CF CLI

Execute o seguinte comando:
```
cf login -a https://api.${SYSTEM_DOMAIN} -u ${CF_USER} --skip-ssl-validation
```

"SYSTEM_DOMAIN" é o seu nome de domínio CF. Pode recuperá-lo procurando no "SYSTEM_DOMAIN" no seu ficheiro manifesto de implantação de CF. 

"CF_User" é o nome de administrador do CF. Pode recuperar o nome e a palavra-passe procurando na secção "scim", procurando o nome e o "cf_admin_password" no seu ficheiro manifesto de implantação de CF.

#### <a name="create-a-cf-user-and-grant-required-privileges"></a>Criar um utilizador cf e conceder privilégios necessários

Execute os seguintes comandos:
```
uaac target https://uaa.${SYSTEM_DOMAIN} --skip-ssl-validation
uaac token client get admin
cf create-user ${FIREHOSE_USER} ${FIREHOSE_USER_PASSWORD}
uaac member add cloud_controller.admin ${FIREHOSE_USER}
uaac member add doppler.firehose ${FIREHOSE_USER}
```

"SYSTEM_DOMAIN" é o seu nome de domínio CF. Pode recuperá-lo procurando no "SYSTEM_DOMAIN" no seu ficheiro manifesto de implantação de CF.

#### <a name="download-the-latest-log-analytics-nozzle-release"></a>Descarregue o mais recente lançamento do Log Analytics Nozzle

Execute o seguinte comando:
```
git clone https://github.com/Azure/oms-log-analytics-firehose-nozzle.git
cd oms-log-analytics-firehose-nozzle
```

#### <a name="set-environment-variables"></a>Definir variáveis de ambiente

Agora pode definir variáveis ambientais no ficheiro manifesto.yml no seu diretório atual. O seguinte mostra o manifesto da aplicação para o Nozzle. Substitua os valores pela informação específica do espaço de trabalho do Log Analytics.

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

### <a name="push-the-application-from-your-development-computer"></a>Empurre a aplicação a partir do seu computador de desenvolvimento

Certifique-se de que está sob a pasta oms-log-analytics-firehose-nozzle. Execute o seguinte comando:
```
cf push
```

## <a name="validate-the-nozzle-installation"></a>Validar a instalação no bocal

### <a name="from-apps-manager-for-pcf"></a>Do Gestor de Aplicações (para PCF)

1. Inscreva-se no Gestor de Ops e certifique-se de que o azulejo está no painel de instrumentos de instalação.
2. Inscreva-se no Gestor de Aplicações, certifique-se de que o espaço criado para o Nozzle está listado no relatório de utilização e confirme que o estado está normal.

### <a name="from-your-development-computer"></a>Do seu computador de desenvolvimento

Na janela CF CLI, escreva:
```
cf apps
```
Certifique-se de que a aplicação OMS Nozzle está em funcionamento.

## <a name="view-the-data-in-the-azure-portal"></a>Ver os dados no portal Azure

Se implementou a solução de monitorização através do modelo de mercado, vá ao portal Azure e localize a solução. Pode encontrar a solução no grupo de recursos especificado no modelo. Clique na solução, navegue na "consola de análise de registos", as vistas pré-configuradas estão listadas, com KPI's de topo do sistema Cloud Foundry, dados de aplicações, alertas e métricas de saúde VM. 

Se criou manualmente o espaço de trabalho Log Analytics, siga os passos abaixo para criar as vistas e alertas:

### <a name="1-import-the-oms-view"></a>1. Importar a vista OMS

A partir do portal OMS, navegue para **Ver Designer**  >  **Import**  >  **Browse**, e selecione um dos ficheiros omsview. Por exemplo, selecione *Cloud Foundry.omsview* e guarde a vista. Agora é apresentado um azulejo na página **'Vista Geral'.** Selecione-o para ver métricas visualizadas.

Pode personalizar estas vistas ou criar novas vistas através do **View Designer.**

O *"Cloud Foundry.omsview"* é uma versão de pré-visualização do modelo de visualização cloud Foundry OMS. Este é um modelo totalmente configurado, padrão. Se tiver sugestões ou feedback sobre o modelo, envie-as para a [secção de emissão](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues).

### <a name="2-create-alert-rules"></a>2. Criar regras de alerta

Pode [criar os alertas](../azure-monitor/alerts/alerts-overview.md)e personalizar as consultas e valores limiares conforme necessário. São recomendados os seguintes alertas:

| Consulta de pesquisa                                                                  | Gerar alerta com base em | Description                                                                       |
| ----------------------------------------------------------------------------- | ----------------------- | --------------------------------------------------------------------------------- |
| Type=CF_ValueMetric_CL Origin_s=bbs Name_s="Domain.cf-apps"                   | Número de resultados < 1   | **bbs. Domain.cf-apps** indicam se o domínio das aplicações de cf está atualizado. Isto significa que os pedidos de aplicações CF do Cloud Controller são sincronizados para bbs. LRPsDesired (AIs desejado por Diego) para execução. Nenhum dado recebido significa que o domínio das aplicações de cf não está atualizado na janela de tempo especificada. |
| Type=CF_ValueMetric_CL Origin_s=rep Name_s=UnhealthyCell Value_d>1            | Número de resultados > 0   | Para as células Diego, 0 significa saudável, e 1 significa insalubre. Desafie o alerta se várias células Diego não saudáveis forem detetadas na janela de tempo especificada. |
| Type=CF_ValueMetric_CL Origin_s="bosh-hm-forwarder" Name_s="system.healthy" Value_d=0 | Número de resultados > 0 | 1 significa que o sistema é saudável, e 0 significa que o sistema não é saudável. |
| Type=CF_ValueMetric_CL Origin_s=route_emitter Name_s=ConsulDownMode Value_d>0 | Número de resultados > 0   | O cônsul emite periodicamente o seu estado de saúde. 0 significa que o sistema é saudável, e 1 significa que o emissor de rota deteta que o Cônsul está em baixo. |
| Type=CF_CounterEvent_CL Origin_s=DopplerServer (Name_s="TruncatingBuffer.DroppedMessages" ou Name_s="doppler.shedEnvelopes") Delta_d>0 | Número de resultados > 0 | O número delta de mensagens intencionalmente deixadas por Doppler devido à pressão nas costas. |
| Type=CF_LogMessage_CL SourceType_s=LGR MessageType_s=ERR                      | Número de resultados > 0   | Loggregator emite **LGR** para indicar problemas com o processo de registo. Um exemplo de tal problema é quando a saída da mensagem de registo é demasiado alta. |
| Type=CF_ValueMetric_CL Name_s=slowConsumerAlert                               | Número de resultados > 0   | Quando o Bocal recebe um alerta lento do consumidor do loggregator, envia os **registos slowConsumerAlert** ValueMetric para Azure Monitor. |
| Type=CF_CounterEvent_CL Job_s=bico Name_s=eventosLost Delta_d>0              | Número de resultados > 0   | Se o número delta de eventos perdidos atingir um limiar, significa que o Bocal pode ter um problema em funcionamento. |

## <a name="scale"></a>Escala

Pode escalar o Bocal e o loggregator.

### <a name="scale-the-nozzle"></a>Escalar o bocal

Deve começar com pelo menos duas instâncias do Bocal. A mangueira distribui a carga de trabalho em todos os casos do Bocal.
Para se certificar de que o Bocal pode acompanhar o tráfego de dados a partir da mangueira de incêndio, instale o alerta **slowConsumerAlert** (listado na secção anterior, "Criar regras de alerta"). Depois de ter sido alertado, siga as orientações para o [Nozzle lento](https://docs.pivotal.io/pivotalcf/1-11/loggregator/log-ops-guide.html#slow-noz) para determinar se a escala é necessária.
Para aumentar o Bocal, utilize o Gestor de Aplicações ou o CLI do CF para aumentar os números de instância ou os recursos de memória ou disco para o Bocal.

### <a name="scale-the-loggregator"></a>Escalar o loggregator

Loggregator envia uma mensagem de registo **LGR** para indicar problemas com o processo de registo. Pode monitorizar o alerta para determinar se o loggregator precisa de ser aumentado.
Para aumentar o loggregator, aumente o tamanho do tampão Doppler ou adicione instâncias adicionais do servidor Doppler no manifesto CF. Para obter mais informações, consulte [as orientações para a escala do loggregator](https://docs.cloudfoundry.org/running/managing-cf/logging-config.html#scaling).

## <a name="update"></a>Atualizar

Para atualizar o Nozzle com uma versão mais recente, descarregue o novo lançamento do Nozzle, siga os passos na secção anterior "Implementar o Bocal" e empurre novamente a aplicação.

### <a name="remove-the-nozzle-from-ops-manager"></a>Retire o bocal do Gestor de Ops

1. Inscreva-se no Gestor de Operações.
2. Localize o **Bocal de Análise de Registos microsoft Azure para** o azulejo PCF.
3. Selecione o ícone do lixo e confirme a eliminação.

### <a name="remove-the-nozzle-from-your-development-computer"></a>Retire o Bocal do seu computador de desenvolvimento

Na sua janela CF CLI, escreva:
```
cf delete <App Name> -r
```

Se remover o Bocal, os dados no portal OMS não são removidos automaticamente. Expira com base na definição de retenção de registos do Monitor Azure.

## <a name="support-and-feedback"></a>Suporte e comentários

O Azure Log Analytics Nozzle está aberto. Envie as suas perguntas e feedback para a [secção GitHub.](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues) Para abrir um pedido de suporte Azure, escolha "Máquina Virtual que executa cloud foundry" como categoria de serviço. 

## <a name="next-step"></a>Passo seguinte

A partir de PCF2.0, as métricas de desempenho de VM são transferidas para o bocal Azure Log Analytics pelo System Metrics Forwarder, e integradas no espaço de trabalho Log Analytics. Já não precisa do agente Log Analytics para as métricas de desempenho em VM. No entanto, ainda pode utilizar o agente Log Analytics para recolher informações do Syslog. O agente Log Analytics é instalado como um suplemento Bosh aos seus VMs CF. 

Para mais detalhes, consulte [o agente Implementar Log Analytics para a sua implementação cloud foundry](https://github.com/Azure/oms-agent-for-linux-boshrelease).