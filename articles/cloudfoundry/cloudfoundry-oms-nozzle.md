---
title: Implemente o bocal de análise de log azure para monitorização da foundry em nuvem
description: Orientação passo a passo na implementação do bocal de loggregator Cloud Foundry para Azure Log Analytics. Utilize o Bocal para monitorizar as métricas de saúde e desempenho do sistema Cloud Foundry.
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
ms.openlocfilehash: bf6691310ec964a1d6293f3a60c151e3d6f8e641
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76277362"
---
# <a name="deploy-azure-log-analytics-nozzle-for-cloud-foundry-system-monitoring"></a>Implemente o bocal de análise de log azure para monitorização do sistema de foundry em nuvem

[O Azure Monitor](https://azure.microsoft.com/services/log-analytics/) é um serviço em Azure. Ajuda-o a recolher e analisar dados que são gerados a partir da sua nuvem e ambientes no local.

O bocal de log analytics (o bocal) é um componente cloud foundry (CF), que encaminha as métricas da mangueira de [loggregator cloud Foundry](https://docs.cloudfoundry.org/loggregator/architecture.html) para os registos do Monitor Azure. Com o Bocal, pode recolher, visualizar e analisar as métricas de saúde e desempenho do seu sistema CF, através de múltiplas implementações.

Neste documento, aprende-se a implantar o Bocal para o seu ambiente CF e, em seguida, acede aos dados da consola de logs Do Monitor Azure.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Pré-requisitos

Os seguintes passos são pré-requisitos para a colocação do bocal.

### <a name="1-deploy-a-cf-or-pivotal-cloud-foundry-environment-in-azure"></a>1. Implementar um ambiente de sedição de nuvem de nuvem em Azure

Pode utilizar o Bocal com uma implantação CF de código aberto ou uma implementação de Fundição de Nuvem Pivotal (PCF).

* [Implementar o Cloud Foundry no Azure](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/blob/master/docs/guidance.md)

* [Implementar a fundação de nuvem fundamental em Azure](https://docs.pivotal.io/pivotalcf/1-11/customizing/azure.html)

### <a name="2-install-the-cf-command-line-tools-for-deploying-the-nozzle"></a>2. Instale as ferramentas de linha de comando CF para a implantação do bocal

O Bocal funciona como uma aplicação em ambiente CF. Precisa do CF CLI para implementar a aplicação.

O Bocal também precisa de autorização de acesso para a mangueira de incêndio do loggregator e para o Controlador de Nuvem. Para criar e configurar o utilizador, necessita do serviço de Conta utilizador e Autenticação (UAA).

* [Instalar Cloud Foundry CLI](https://docs.cloudfoundry.org/cf-cli/install-go-cli.html)

* [Instale o cliente da linha de comando Da Cloud Foundry UAA](https://github.com/cloudfoundry/cf-uaac/blob/master/README.md)

Antes de configurar o cliente da Linha de Comando UAA, certifique-se de que a RubyGems está instalada.

### <a name="3-create-a-log-analytics-workspace-in-azure"></a>3. Criar um espaço de trabalho de Log Analytics em Azure

Pode criar manualmente o espaço de trabalho log Analytics ou utilizando um modelo. O modelo irá implementar uma configuração de visões e alertas kPI pré-configurados para a consola de logs Do Monitor Azure. 

#### <a name="to-create-the-workspace-manually"></a>Para criar o espaço de trabalho manualmente:

1. No portal Azure, procure na lista de serviços no Azure Marketplace e, em seguida, selecione espaços de trabalho log Analytics.
2. Selecione **Criar**e, em seguida, selecione escolhas para os seguintes itens:

   * **Log Analytics espaço de trabalho**: Digite um nome para o seu espaço de trabalho.
   * **Subscrição**: Se tiver várias subscrições, escolha a que é a mesma que a sua implementação cf.
   * **Grupo de recursos:** Pode criar um novo grupo de recursos ou utilizar o mesmo com a sua implementação de CF.
   * **Localização**: Insira o local.
   * **Nível de preços:** Selecione **OK** para completar.

Para mais informações, consulte [Iniciar com registos do Monitor Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started).

#### <a name="to-create-the-log-analytics-workspace-through-the-monitoring-template-from-azure-market-place"></a>Para criar o espaço de trabalho log Analytics através do modelo de monitorização do mercado azure:

1. Abre o Portal do Azure.
1. Clique no sinal "+" ou "Criar um recurso" no canto superior esquerdo.
1. Digite "Cloud Foundry" na janela de pesquisa, selecione "Cloud Foundry Monitoring Solution".
1. A primeira página do modelo de solução de monitorização Cloud Foundry está carregada, clique em "Criar" para lançar a lâmina do modelo.
1. Introduza os parâmetros necessários:
    * **Subscrição**: Selecione uma subscrição Azure para o espaço de trabalho Log Analytics, normalmente o mesmo com a implementação da Cloud Foundry.
    * **Grupo de recursos**: Selecione um grupo de recursos existente ou crie um novo para o espaço de trabalho log Analytics.
    * **Localização**do Grupo de Recursos : Selecione a localização do grupo de recursos.
    * **OMS_Workspace_Name**: Introduza um nome de espaço de trabalho, se o espaço de trabalho não existir, o modelo criará um novo.
    * **OMS_Workspace_Region:** Selecione a localização para o espaço de trabalho.
    * **OMS_Workspace_Pricing_Tier:** Selecione o espaço de trabalho Log Analytics SKU. Consulte a [orientação](https://azure.microsoft.com/pricing/details/log-analytics/) de preços para referência.
    * **Termos legais**: Clique em termos legais e clique em "Criar" para aceitar o termo legal.
1. Depois de especificar todos os parâmetros, clique em "Criar" para implementar o modelo. Quando a implementação estiver concluída, o estado aparecerá no separador de notificação.


## <a name="deploy-the-nozzle"></a>Implementar o bocal

Existem algumas formas diferentes de implantar o Bocal: como um azulejo PCF ou como uma aplicação CF.

### <a name="deploy-the-nozzle-as-a-pcf-ops-manager-tile"></a>Implante o Bocal como um azulejo PCF Ops Manager

Siga os passos para [instalar e configurar o bocal](https://docs.pivotal.io/partners/azure-log-analytics-nozzle/installing.html)de análise de log azure para PCF . Esta é a abordagem simplificada, o azulejo do gestor de OPS PCF configurará automaticamente e empurrará o bocal. 

### <a name="deploy-the-nozzle-manually-as-a-cf-application"></a>Implementar o Bocal manualmente como aplicação CF

Se não estiver a utilizar o PCF Ops Manager, implante o Bocal como aplicação. As seguintes secções descrevem este processo.

#### <a name="sign-in-to-your-cf-deployment-as-an-admin-through-cf-cli"></a>Inscreva-se na sua implantação cf como administrador através do CF CLI

Execute o seguinte comando:
```
cf login -a https://api.${SYSTEM_DOMAIN} -u ${CF_USER} --skip-ssl-validation
```

"SYSTEM_DOMAIN" é o seu nome de domínio CF. Pode recuperá-lo pesquisando o "SYSTEM_DOMAIN" no seu ficheiro manifesto de implantação do CF. 

"CF_User" é o nome da administração do CF. Pode recuperar o nome e a palavra-passe pesquisando a secção "scim", procurando o nome e o "cf_admin_password" no seu ficheiro manifesto de implementação de CF.

#### <a name="create-a-cf-user-and-grant-required-privileges"></a>Criar um utilizador cf e conceder privilégios necessários

Execute os seguintes comandos:
```
uaac target https://uaa.${SYSTEM_DOMAIN} --skip-ssl-validation
uaac token client get admin
cf create-user ${FIREHOSE_USER} ${FIREHOSE_USER_PASSWORD}
uaac member add cloud_controller.admin ${FIREHOSE_USER}
uaac member add doppler.firehose ${FIREHOSE_USER}
```

"SYSTEM_DOMAIN" é o seu nome de domínio CF. Pode recuperá-lo pesquisando o "SYSTEM_DOMAIN" no seu ficheiro manifesto de implantação do CF.

#### <a name="download-the-latest-log-analytics-nozzle-release"></a>Descarregue o mais recente lançamento do Log Analytics Nozzle

Execute o seguinte comando:
```
git clone https://github.com/Azure/oms-log-analytics-firehose-nozzle.git
cd oms-log-analytics-firehose-nozzle
```

#### <a name="set-environment-variables"></a>Definir variáveis de ambiente

Agora pode definir variáveis ambientais no ficheiro manifesto.yml no seu diretório atual. O seguinte mostra o manifesto da aplicação para o Bocal. Substitua os valores por informações específicas sobre o espaço de trabalho log Analytics.

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

### <a name="push-the-application-from-your-development-computer"></a>Empurre a aplicação do seu computador de desenvolvimento

Certifique-se de que está sob a pasta oms-log-analytics-firehose-bocazzle. Execute o seguinte comando:
```
cf push
```

## <a name="validate-the-nozzle-installation"></a>Validar a instalação do bocal

### <a name="from-apps-manager-for-pcf"></a>De Gestor de Aplicativos (para PCF)

1. Inscreva-se no Ops Manager e certifique-se de que o azulejo está exposto no painel de instalação.
2. Inscreva-se no Gestor de Apps, certifique-se de que o espaço que criou para o Bocal está listado no relatório de utilização e confirme que o estado está normal.

### <a name="from-your-development-computer"></a>Do seu computador de desenvolvimento

Na janela CF CLI, escreva:
```
cf apps
```
Certifique-se de que a aplicação OMS Nozzle está em execução.

## <a name="view-the-data-in-the-azure-portal"></a>Ver os dados no portal Azure

Se implementou a solução de monitorização através do modelo de local de mercado, vá ao portal Azure e localize a solução. Pode encontrar a solução no grupo de recursos especificado no modelo. Clique na solução, navegue na "consola de análise de registos", as vistas pré-configuradas estão listadas, com KPIs do sistema de memória de nuvem, dados de aplicações, alertas e métricas de saúde VM. 

Se criou manualmente o espaço de trabalho log Analytics, siga os passos abaixo para criar as vistas e alertas:

### <a name="1-import-the-oms-view"></a>1. Importar a visão oms

A partir do portal OMS, navegue para **ver o Designer** > **Import** > **Browse,** e selecione um dos ficheiros omsview. Por exemplo, selecione *Cloud Foundry.omsview*, e guarde a vista. Agora é exibido um azulejo na página **overview.** Selecione-o para ver métricas visualizadas.

Pode personalizar estas vistas ou criar novas vistas através do **View Designer.**

O *"Cloud Foundry.omsview"* é uma versão de pré-visualização do modelo de visualização Cloud Foundry OMS. Este é um modelo totalmente configurado, padrão. Se tiver sugestões ou feedback sobre o modelo, envie-as para a secção de [emissão](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues).

### <a name="2-create-alert-rules"></a>2. Criar regras de alerta

Pode [criar os alertas](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts)e personalizar as consultas e os valores limiares conforme necessário. Recomendam-se os seguintes alertas:

| Consulta de pesquisa                                                                  | Gerar alerta com base em | Descrição                                                                       |
| ----------------------------------------------------------------------------- | ----------------------- | --------------------------------------------------------------------------------- |
| Type=CF_ValueMetric_CL Origin_s=bbs Name_s="Domain.cf-apps"                   | Número de resultados < 1   | **bbs. Domain.cf-apps** indica se o Domínio das aplicações cf-apps está atualizado. Isto significa que os pedidos da Aplicação CF do Cloud Controller são sincronizados para bbs. LRPsDesired (AIs desejado por Diego) para execução. Nenhum dado recebido significa que o domínio das aplicações cf-apps não está atualizado na janela de tempo especificada. |
| Type=CF_ValueMetric_CL Origin_s=representante Name_s=UnhealthyCell Value_d>1            | Número de resultados > 0   | Para as células Diego, 0 significa saudável, e 1 significa insalubre. Detete o alerta se várias células Diego não saudáveis forem detetadas na janela de tempo especificada. |
| Type=CF_ValueMetric_CL Origin_s="bosh-hm-forwarder" Name_s="system.healthy" Value_d=0 | Número de resultados > 0 | 1 significa que o sistema é saudável, e 0 significa que o sistema não é saudável. |
| Type=CF_ValueMetric_CL Origin_s=route_emitter Name_s=ConsulDownMode Value_d>0 | Número de resultados > 0   | O cônsul emite periodicamente o seu estado de saúde. 0 significa que o sistema é saudável, e 1 significa que o emissor de rota deteta que o Cônsul está em baixo. |
| Type=CF_CounterEvent_CL Origin_s=DopplerServer (Name_s="TruncatingBuffer.DropMessages" ou Name_s="doppler.shedEnvelopes") Delta_d>0 | Número de resultados > 0 | O número delta de mensagens intencionalmente largadas pelo Doppler devido à pressão nas costas. |
| Tipo=CF_LogMessage_CL SourceType_s=LGR MessageType_s=ERR                      | Número de resultados > 0   | O loggregator emite **LGR** para indicar problemas com o processo de exploração madeireira. Um exemplo de tal problema é quando a saída da mensagem de registo é demasiado elevada. |
| Tipo=CF_ValueMetric_CL Name_s=slowConsumerAlert                               | Número de resultados > 0   | Quando o Bocal recebe um alerta lento do consumidor do loggregator, envia o **slowConsumerAlert** ValueMetric para os registos do Monitor Azure. |
| Tipo=CF_CounterEvent_CL Job_s=bocal Name_s=eventosPerdida Delta_d>0              | Número de resultados > 0   | Se o número delta de eventos perdidos atingir um limiar, significa que o Bocal pode ter um problema em funcionamento. |

## <a name="scale"></a>Escala

Pode escalar o bocal e o gregador.

### <a name="scale-the-nozzle"></a>Escalar o bocal

Deviacomeçar com pelo menos duas instâncias do bocal. A mangueira de fogo distribui a carga de trabalho em todos os casos do Bocal.
Para se certificar de que o Bocal consegue acompanhar o tráfego de dados da mangueira de incêndio, instale o alerta **slowConsumerAlert** (listado na secção anterior, "Criar regras de alerta"). Depois de ter sido alertado, siga as orientações para o [bocal lento](https://docs.pivotal.io/pivotalcf/1-11/loggregator/log-ops-guide.html#slow-noz) para determinar se é necessário escalar.
Para aumentar o bocal, utilize o Gestor de Aplicações ou o CLI CF para aumentar os números de instância ou os recursos de memória ou disco para o Bocal.

### <a name="scale-the-loggregator"></a>Escala o loggregator

O loggregator envia uma mensagem de registo **LGR** para indicar problemas com o processo de registo. Pode monitorizar o alerta para determinar se o loggregator precisa de ser aumentado.
Para aumentar o loggregator, aumente o tamanho do tampão Doppler ou adicione instâncias adicionais do servidor Doppler no manifesto CF. Para mais informações, consulte [a orientação para escalonar o loggregator](https://docs.cloudfoundry.org/running/managing-cf/logging-config.html#scaling).

## <a name="update"></a>Atualizar

Para atualizar o Bocal com uma versão mais recente, descarregue o novo lançamento do Bocal, siga os passos na secção anterior "Implementar o Bocal" e empurre a aplicação novamente.

### <a name="remove-the-nozzle-from-ops-manager"></a>Remova o bocal do Gestor de Operações

1. Inscreva-se no Gerente de Operações.
2. Localize o bocal de log analytics do **Microsoft Azure para azulejos PCF.**
3. Selecione o ícone do lixo e confirme a eliminação.

### <a name="remove-the-nozzle-from-your-development-computer"></a>Retire o bocal do seu computador de desenvolvimento

Na sua janela CF CLI, escreva:
```
cf delete <App Name> -r
```

Se remover o bocal, os dados no portal OMS não são removidos automaticamente. Expira com base na definição de retenção de registos do Monitor Azure.

## <a name="support-and-feedback"></a>Suporte e comentários

O bocal azure Log Analytics é de origem aberta. Envie as suas perguntas e feedback para a [secção GitHub](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues). Para abrir um pedido de suporte Azure, escolha "Máquina Virtual executando Cloud Foundry" como categoria de serviço. 

## <a name="next-step"></a>Passo seguinte

A partir de PCF2.0, as métricas de desempenho vM são transferidas para o bocal Azure Log Analytics por System Metrics Forwarder, e integradas no espaço de trabalho log Analytics. Já não precisa do agente Log Analytics para as métricas de desempenho vm. No entanto, ainda pode utilizar o agente Log Analytics para recolher informações sobre o Syslog. O agente Log Analytics está instalado como um add-on Bosh para os seus VMs CF. 

Para mais detalhes, consulte [o agente Desloque o Registo Analytics para a sua implementação](https://github.com/Azure/oms-agent-for-linux-boshrelease)de Cloud Foundry .
