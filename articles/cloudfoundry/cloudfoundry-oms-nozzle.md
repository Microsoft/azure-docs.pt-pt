---
title: Implantar o bocal do Azure Log Analytics para monitoramento de Cloud Foundry
description: Orientações passo a passo sobre como implantar o Cloud Foundry bocal agregador para o Log Analytics do Azure. Use o bocal para monitorar as métricas de desempenho e integridade do sistema Cloud Foundry.
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
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76277362"
---
# <a name="deploy-azure-log-analytics-nozzle-for-cloud-foundry-system-monitoring"></a>Implantar o bocal do Azure Log Analytics para monitoramento do sistema Cloud Foundry

[Azure monitor](https://azure.microsoft.com/services/log-analytics/) é um serviço no Azure. Ele ajuda a coletar e analisar dados que são gerados de seus ambientes de nuvem e locais.

O bocal de Log Analytics (o bocal) é um componente de Cloud Foundry (CF), que encaminha as métricas da [Cloud Foundry agregador](https://docs.cloudfoundry.org/loggregator/architecture.html) firehose para logs de Azure monitor. Com o bocal, você pode coletar, exibir e analisar as métricas de desempenho e integridade do sistema do CF em várias implantações.

Neste documento, você aprenderá a implantar o bocal no ambiente do CF e, em seguida, acessar os dados no console de logs de Azure Monitor.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Pré-requisitos

As etapas a seguir são pré-requisitos para implantar o bocal.

### <a name="1-deploy-a-cf-or-pivotal-cloud-foundry-environment-in-azure"></a>1. implantar um ambiente de Cloud Foundry do CF ou Pivotal no Azure

Você pode usar o bocal com uma implantação de CF de software livre ou uma implantação do PCF (Cloud Foundry Pivotal).

* [Implantar Cloud Foundry no Azure](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/blob/master/docs/guidance.md)

* [Implantar Cloud Foundry dinâmicos no Azure](https://docs.pivotal.io/pivotalcf/1-11/customizing/azure.html)

### <a name="2-install-the-cf-command-line-tools-for-deploying-the-nozzle"></a>2. instalar as ferramentas de linha de comando do CF para implantar o bocal

O bocal é executado como um aplicativo no ambiente do CF. Você precisa da CLI do CF para implantar o aplicativo.

O bocal também precisa de permissão de acesso para o fIREHOSE agregador e o controlador de nuvem. Para criar e configurar o usuário, você precisa do serviço de autenticação e conta de usuário (UAA).

* [Instalar Cloud Foundry CLI](https://docs.cloudfoundry.org/cf-cli/install-go-cli.html)

* [Instalar Cloud Foundry cliente de linha de comando UAA](https://github.com/cloudfoundry/cf-uaac/blob/master/README.md)

Antes de configurar o cliente de linha de comando UAA, verifique se o RubyGems está instalado.

### <a name="3-create-a-log-analytics-workspace-in-azure"></a>3. criar um espaço de trabalho Log Analytics no Azure

Você pode criar o espaço de trabalho Log Analytics manualmente ou usando um modelo. O modelo implantará uma configuração de exibições de KPI pré-configuradas e alertas para o console de logs de Azure Monitor. 

#### <a name="to-create-the-workspace-manually"></a>Para criar o espaço de trabalho manualmente:

1. Na portal do Azure, pesquise a lista de serviços no Azure Marketplace e, em seguida, selecione Log Analytics espaços de trabalho.
2. Selecione **criar**e, em seguida, selecione as opções para os seguintes itens:

   * **Espaço de trabalho log Analytics**: digite um nome para seu espaço de trabalho.
   * **Assinatura**: se você tiver várias assinaturas, escolha aquela que seja a mesma que a sua implantação do CF.
   * **Grupo de recursos**: você pode criar um novo grupo de recursos ou usar o mesmo com a implantação do CF.
   * **Local**: Insira o local.
   * **Tipo de preço**: selecione **OK** para concluir.

Para obter mais informações, consulte Introdução [aos logs de Azure monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started).

#### <a name="to-create-the-log-analytics-workspace-through-the-monitoring-template-from-azure-market-place"></a>Para criar o espaço de trabalho Log Analytics por meio do modelo de monitoramento do Azure Market Place:

1. Abra portal do Azure.
1. Clique no sinal "+" ou em "criar um recurso" no canto superior esquerdo.
1. Digite "Cloud Foundry" na janela de pesquisa, selecione "solução de monitoramento de Cloud Foundry".
1. A página frontal do modelo de solução de monitoramento de Cloud Foundry está carregada, clique em "criar" para iniciar a folha do modelo.
1. Insira os parâmetros necessários:
    * **Assinatura**: selecione uma assinatura do Azure para o espaço de trabalho log Analytics, geralmente o mesmo com Cloud Foundry implantação.
    * **Grupo de recursos**: selecione um grupo de recursos existente ou crie um novo para o espaço de trabalho log Analytics.
    * **Local do grupo de recursos**: selecione o local do grupo de recursos.
    * **OMS_Workspace_Name**: Insira um nome de espaço de trabalho, se o espaço de trabalho não existir, o modelo criará um novo.
    * **OMS_Workspace_Region**: selecione o local para o espaço de trabalho.
    * **OMS_Workspace_Pricing_Tier**: selecione o SKU do espaço de trabalho log Analytics. Consulte as [diretrizes de preços](https://azure.microsoft.com/pricing/details/log-analytics/) para referência.
    * **Termos legais**: clique em termos legais e, em seguida, clique em "criar" para aceitar o termo legal.
1. Depois que todos os parâmetros forem especificados, clique em "criar" para implantar o modelo. Quando a implantação for concluída, o status aparecerá na guia notificação.


## <a name="deploy-the-nozzle"></a>Implantar o bocal

Há algumas maneiras diferentes de implantar o bocal: como um bloco PCF ou como um aplicativo CF.

### <a name="deploy-the-nozzle-as-a-pcf-ops-manager-tile"></a>Implantar o bocal como um bloco do Gerenciador de operações do PCF

Siga as etapas para [instalar e configurar o bocal do Azure log Analytics para PCF](https://docs.pivotal.io/partners/azure-log-analytics-nozzle/installing.html). Essa é a abordagem simplificada, o bloco do Gerenciador de ops do PCF configurará e enviará automaticamente o bocal. 

### <a name="deploy-the-nozzle-manually-as-a-cf-application"></a>Implantar o bocal manualmente como um aplicativo CF

Se você não estiver usando o PCF Ops Manager, implante o bocal como um aplicativo. As seções a seguir descrevem esse processo.

#### <a name="sign-in-to-your-cf-deployment-as-an-admin-through-cf-cli"></a>Entre em sua implantação do CF como um administrador por meio da CLI do CF

Execute o seguinte comando:
```
cf login -a https://api.${SYSTEM_DOMAIN} -u ${CF_USER} --skip-ssl-validation
```

"SYSTEM_DOMAIN" é o nome de domínio do CF. Você pode recuperá-lo pesquisando o "SYSTEM_DOMAIN" em seu arquivo de manifesto de implantação do CF. 

"CF_User" é o nome do administrador do CF. Você pode recuperar o nome e a senha pesquisando a seção "SCIM", procurando o nome e o "cf_admin_password" em seu arquivo de manifesto de implantação do CF.

#### <a name="create-a-cf-user-and-grant-required-privileges"></a>Criar um usuário do CF e conceder os privilégios necessários

Execute os seguintes comandos:
```
uaac target https://uaa.${SYSTEM_DOMAIN} --skip-ssl-validation
uaac token client get admin
cf create-user ${FIREHOSE_USER} ${FIREHOSE_USER_PASSWORD}
uaac member add cloud_controller.admin ${FIREHOSE_USER}
uaac member add doppler.firehose ${FIREHOSE_USER}
```

"SYSTEM_DOMAIN" é o nome de domínio do CF. Você pode recuperá-lo pesquisando o "SYSTEM_DOMAIN" em seu arquivo de manifesto de implantação do CF.

#### <a name="download-the-latest-log-analytics-nozzle-release"></a>Baixe a versão mais recente do bocal Log Analytics

Execute o seguinte comando:
```
git clone https://github.com/Azure/oms-log-analytics-firehose-nozzle.git
cd oms-log-analytics-firehose-nozzle
```

#### <a name="set-environment-variables"></a>Definir variáveis de ambiente

Agora você pode definir variáveis de ambiente no arquivo manifest. yml em seu diretório atual. O seguinte mostra o manifesto do aplicativo para o bocal. Substitua os valores pelas informações específicas do espaço de trabalho Log Analytics.

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

### <a name="push-the-application-from-your-development-computer"></a>Enviar por push o aplicativo do seu computador de desenvolvimento

Verifique se você está na pasta OMS-log-Analytics-firehose-bocal. Execute o seguinte comando:
```
cf push
```

## <a name="validate-the-nozzle-installation"></a>Validar a instalação do bocal

### <a name="from-apps-manager-for-pcf"></a>Do Gerenciador de aplicativos (para PCF)

1. Entre no Ops Manager e verifique se o bloco é exibido no painel de instalação.
2. Entre no Gerenciador de aplicativos, verifique se o espaço que você criou para o bocal está listado no relatório de uso e confirme se o status é normal.

### <a name="from-your-development-computer"></a>Do seu computador de desenvolvimento

Na janela da CLI do CF, digite:
```
cf apps
```
Verifique se o aplicativo bocal do OMS está em execução.

## <a name="view-the-data-in-the-azure-portal"></a>Exibir os dados no portal do Azure

Se você tiver implantado a solução de monitoramento por meio do modelo do mercado, vá para portal do Azure e localize a solução. Você pode encontrar a solução no grupo de recursos especificado no modelo. Clique na solução, navegue até o "console do log Analytics", as exibições pré-configuradas são listadas, com os principais KPIs do sistema Cloud Foundry, dados do aplicativo, alertas e métricas de integridade da VM. 

Se você criou o espaço de trabalho Log Analytics manualmente, siga as etapas abaixo para criar os modos de exibição e os alertas:

### <a name="1-import-the-oms-view"></a>1. importar a exibição do OMS

No portal do OMS, navegue até o **Designer de exibição** > **importar** > **procurar**e selecione um dos arquivos omsview. Por exemplo, selecione *Cloud Foundry. omsview*e salve o modo de exibição. Agora, um bloco é exibido na página **visão geral** . Selecione-o para ver as métricas visualizadas.

Você pode personalizar essas exibições ou criar novas exibições por meio do **Designer de exibição**.

O *"Cloud Foundry. omsview"* é uma versão de visualização do modelo de exibição do Cloud Foundry OMS. Esse é um modelo padrão totalmente configurado. Se você tiver sugestões ou comentários sobre o modelo, envie-os para a [seção de problemas](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues).

### <a name="2-create-alert-rules"></a>2. criar regras de alerta

Você pode [criar os alertas](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts)e personalizar as consultas e os valores de limite conforme necessário. Estes são os alertas recomendados:

| Consulta de pesquisa                                                                  | Gerar alerta com base em | Descrição                                                                       |
| ----------------------------------------------------------------------------- | ----------------------- | --------------------------------------------------------------------------------- |
| Type=CF_ValueMetric_CL Origin_s=bbs Name_s="Domain.cf-apps"                   | Número de resultados < 1   | **BBS. Domain.cf-apps** indica se o domínio CF-apps está atualizado. Isso significa que as solicitações de aplicativo do CF do controlador de nuvem são sincronizadas com o BBS. LRPsDesired (AIs de Diego desejado) para execução. Nenhum dado recebido significa que o domínio CF-apps não está atualizado na janela de tempo especificada. |
| Type=CF_ValueMetric_CL Origin_s=rep Name_s=UnhealthyCell Value_d>1            | Número de resultados > 0   | Para células Diego, 0 significa íntegro e 1 significa não íntegro. Defina o alerta se várias células de Diego não íntegros forem detectadas na janela de tempo especificada. |
| Type=CF_ValueMetric_CL Origin_s="bosh-hm-forwarder" Name_s="system.healthy" Value_d=0 | Número de resultados > 0 | 1 significa que o sistema está íntegro e 0 significa que o sistema não está íntegro. |
| Type = CF_ValueMetric_CL Origin_s = route_emitter Name_s = ConsulDownMode Value_d > 0 | Número de resultados > 0   | O Consul emite seu status de integridade periodicamente. 0 significa que o sistema está íntegro e 1 significa que o emissor de rota detecta que o Consul está inoperante. |
| Type = CF_CounterEvent_CL Origin_s = DopplerServer (Name_s = "TruncatingBuffer. DroppedMessages" ou Name_s = "Doppler. shedEnvelopes") Delta_d > 0 | Número de resultados > 0 | O número Delta de mensagens descartadas intencionalmente por Doppler devido à pressão de retorno. |
| Type=CF_LogMessage_CL SourceType_s=LGR MessageType_s=ERR                      | Número de resultados > 0   | Agregador emite **LGR** para indicar problemas com o processo de log. Um exemplo desse problema é quando a saída da mensagem de log é muito alta. |
| Type=CF_ValueMetric_CL Name_s=slowConsumerAlert                               | Número de resultados > 0   | Quando o bocal recebe um alerta de consumidor lento do agregador, ele envia o **valuemetric slowconsumeralert** ao para logs de Azure monitor. |
| Type=CF_CounterEvent_CL Job_s=nozzle Name_s=eventsLost Delta_d>0              | Número de resultados > 0   | Se o número Delta de eventos perdidos atingir um limite, isso significará que o bocal pode ter um problema em execução. |

## <a name="scale"></a>Escalabilidade

Você pode dimensionar o bocal e o agregador.

### <a name="scale-the-nozzle"></a>Dimensionar o bocal

Você deve começar com pelo menos duas instâncias do bocal. O fIREHOSE distribui a carga de trabalho em todas as instâncias do bocal.
Para garantir que o bocal possa acompanhar o tráfego de dados do fIREHOSE, configure o alerta **valuemetric slowconsumeralert** (listado na seção anterior, "criar regras de alerta"). Depois de ter sido alertado, siga as [diretrizes para bocal lenta](https://docs.pivotal.io/pivotalcf/1-11/loggregator/log-ops-guide.html#slow-noz) para determinar se o dimensionamento é necessário.
Para escalar verticalmente o bocal, use o Gerenciador de aplicativos ou a CLI do CF para aumentar os números de instância ou a memória ou os recursos de disco para o bocal.

### <a name="scale-the-loggregator"></a>Dimensionar o agregador

Agregador envia uma mensagem de log **LGR** para indicar problemas com o processo de log. Você pode monitorar o alerta para determinar se o agregador precisa ser escalado verticalmente.
Para escalar verticalmente o agregador, aumente o tamanho do buffer Doppler ou adicione instâncias de servidor Doppler adicionais no manifesto do CF. Para obter mais informações, consulte [a orientação para dimensionar o agregador](https://docs.cloudfoundry.org/running/managing-cf/logging-config.html#scaling).

## <a name="update"></a>Atualizar

Para atualizar o bocal com uma versão mais recente, baixe a nova versão do bocal, siga as etapas na seção "implantar o bocal" anterior e envie o aplicativo novamente.

### <a name="remove-the-nozzle-from-ops-manager"></a>Remover o bocal do Ops Manager

1. Entre no Ops Manager.
2. Localize o **Microsoft Azure log Analytics bocal para** o bloco PCF.
3. Selecione o ícone de lixo e confirme a exclusão.

### <a name="remove-the-nozzle-from-your-development-computer"></a>Remover o bocal do seu computador de desenvolvimento

Na janela da CLI do CF, digite:
```
cf delete <App Name> -r
```

Se você remover o bocal, os dados no portal do OMS não serão removidos automaticamente. Ele expira com base na configuração de retenção de logs de Azure Monitor.

## <a name="support-and-feedback"></a>Suporte e comentários

O bocal do Azure Log Analytics está em código aberto. Envie suas perguntas e seus comentários para a [seção do GitHub](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues). Para abrir uma solicitação de suporte do Azure, escolha "máquina virtual em execução Cloud Foundry" como a categoria de serviço. 

## <a name="next-step"></a>Passo seguinte

No PCF 2.0, as métricas de desempenho da VM são transferidas para o Azure Log Analytics bocal pelo encaminhador de métricas do sistema e integradas ao espaço de trabalho Log Analytics. Você não precisa mais do agente de Log Analytics para as métricas de desempenho da VM. No entanto, você ainda pode usar o agente de Log Analytics para coletar informações de syslog. O agente de Log Analytics é instalado como um complemento Bosh em suas VMs do CF. 

Para obter detalhes, consulte [implantar log Analytics Agent na sua implantação do Cloud Foundry](https://github.com/Azure/oms-agent-for-linux-boshrelease).
