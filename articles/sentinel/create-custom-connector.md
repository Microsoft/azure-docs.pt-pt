---
title: Recursos para criar conectores personalizados Azure Sentinel | Microsoft Docs
description: Saiba mais sobre os recursos disponíveis para criar conectores personalizados para o Azure Sentinel. Os métodos incluem o agente Log Analytics e API, Logstash, Logic Apps, PowerShell e Azure Functions.
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/09/2021
ms.author: bagol
ms.openlocfilehash: 90646339ef41d0629a4d1ce8efed4b50427d3b2b
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100418315"
---
# <a name="resources-for-creating-azure-sentinel-custom-connectors"></a>Recursos para criar conectores personalizados Azure Sentinel

O Azure Sentinel fornece uma vasta gama de [conectores incorporados para serviços Azure e soluções externas,](connect-data-sources.md)e também suporta a ingestão de dados de algumas fontes sem um conector dedicado.

Se não conseguir ligar a sua fonte de dados ao Azure Sentinel utilizando qualquer uma das soluções existentes, considere criar o seu próprio conector de fonte de dados.

Para obter uma lista completa de conectores suportados, consulte o [Azure Sentinel: The connectors grand (CEF, Syslog, Direct, Agent, Custom, e muito mais)](https://techcommunity.microsoft.com/t5/azure-sentinel/azure-sentinel-the-connectors-grand-cef-syslog-direct-agent/ba-p/803891) blog post.

## <a name="compare-custom-connector-methods"></a>Compare métodos de conector personalizados

A tabela a seguir compara detalhes essenciais sobre cada método para a criação de conectores personalizados descritos neste artigo. Selecione os links na tabela para obter mais detalhes sobre cada método.

|Descrição do método  |Funcionalidade | Sem servidor    |Complexidade  |
|---------|---------|---------|---------|
|**[Agente de registo de análise](#connect-with-the-log-analytics-agent)** <br>O melhor para a recolha de ficheiros a partir de fontes no local e iaaS   | Apenas recolha de ficheiros  |   No      |Baixo         |
|**[Logstash](#connect-with-logstash)** <br>O melhor para fontes no local e iaaS, qualquer fonte para a qual um plugin está disponível, e organizações já familiarizadas com Logstash  | Os plugins disponíveis, além de plugin personalizados, as capacidades proporcionam uma flexibilidade significativa.   |   Não, não. requer um cluster VM ou VM para executar           |   Baixo; suporta muitos cenários com plugins      |
|**[Logic Apps](#connect-with-logic-apps)** <br>Custo elevado; evitar dados de grande volume <br>O melhor para fontes de nuvem de baixo volume  | A programação sem código permite uma flexibilidade limitada, sem suporte para a implementação de algoritmos.<br><br> Se nenhuma ação disponível já suportar os seus requisitos, criar uma ação personalizada pode acrescentar complexidade.    |    Yes         |   Baixo; desenvolvimento simples e sem código      |
|**[PowerShell](#connect-with-powershell)** <br>O melhor para prototipagem e uploads periódicos de ficheiros | Suporte direto para recolha de ficheiros. <br><br>O PowerShell pode ser usado para recolher mais fontes, mas requer codificação e configuração do script como um serviço.      |No               |  Baixo       |
|**[Log Analytics API](#connect-with-the-log-analytics-api)** <br>O melhor para os ISV implementando a integração, e para requisitos únicos de recolha   | Suporta todas as capacidades disponíveis com o código.  | Depende da implementação           |     Alto    |
|**[Funções Azure](#connect-with-azure-functions)** O melhor para fontes de nuvem de alto volume e para requisitos de recolha únicos  | Suporta todas as capacidades disponíveis com o código.  |  Yes             |     Alto; requer conhecimento de programação    |
|     |         |                |

> [!TIP]
> Para comparações de utilização de Apps Lógicas e Funções Azure para o mesmo conector, consulte:
> 
> - [Ingeste rapidamente web application firewall entra em Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/ingest-fastly-web-application-firewall-logs-into-azure-sentinel/ba-p/1238804)
> - Office 365 (comunidade Azure Sentinel GitHub): [Conector de função de aplicação lógica](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Get-O365Data)  |  [Azure](https://github.com/Azure/Azure-Sentinel/tree/master/DataConnectors/O365%20Data)
> 

## <a name="connect-with-the-log-analytics-agent"></a>Conecte-se com o agente Log Analytics

Se a sua fonte de dados entregar eventos em ficheiros, recomendamos que utilize o agente Azure Monitor Log Analytics para criar o seu conector personalizado.

- Para obter mais informações, consulte [a recolha de registos personalizados no Azure Monitor](/azure/azure-monitor/platform/data-sources-custom-logs).

- Para um exemplo deste método, consulte [recolher fontes de dados JSON personalizadas com o agente Log Analytics para Linux no Azure Monitor](/azure/azure-monitor/platform/data-sources-json).

## <a name="connect-with-logstash"></a>Conecte-se com Logstash

Se estiver familiarizado com [o Logstash,](https://www.elastic.co/logstash)pode querer utilizar o Logstash com o [plug-in de saída logstash para o Azure Sentinel](connect-logstash.md) para criar o seu conector personalizado.

Com o plugin de saída de logstash Azure Sentinel, pode utilizar qualquer entrada de Logstash e plugins de filtragem, e configurar o Azure Sentinel como saída para um gasoduto Logstash. O Logstash tem uma grande biblioteca de plugins que permitem a entrada de várias fontes, tais como Centros de Eventos, Apache Kafka, Ficheiros, Bases de Dados e Serviços cloud. Utilize plug-ins de filtragem para analisar eventos, filtrar eventos desnecessários, valores obfuscados e muito mais.

Por exemplo, utilizar o Logstash como conector personalizado, consulte:

- [Caça ao Capital One Breach TTPs em registos AWS usando Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/hunting-for-capital-one-breach-ttps-in-aws-logs-using-azure/ba-p/1019767) (blog)
- [Guia de implementação radware Azure Sentinel](https://support.radware.com/ci/okcsFattach/get/1025459_3)

Para exemplos de plugins úteis logstash, consulte:

- [Plugin de entrada cloudwatch](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-cloudwatch.html)
- [Plugin Azure Event Hubs](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-azure_event_hubs.html)
- [Plugin de entrada de armazenamento de nuvem do Google](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-google_cloud_storage.html)
- [plugin de entrada Google_pubsub](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-google_pubsub.html)

> [!TIP]
> O logstash também permite a recolha de dados em escala utilizando um cluster. Para obter mais informações, consulte [utilizando um VM de logstash equilibrado em carga à escala](https://techcommunity.microsoft.com/t5/azure-sentinel/scaling-up-syslog-cef-collection/ba-p/1185854).
>

## <a name="connect-with-logic-apps"></a>Conecte-se com apps lógicas

Utilize uma [App Azure Logic](/azure/logic-apps/) para criar um conector personalizado e sem servidor para o Azure Sentinel.

> [!NOTE]
> Embora a criação de conectores sem servidor que utilizem Apps Lógicas possa ser conveniente, a utilização de Aplicações Lógicas para os seus conectores pode ser dispendiosa para grandes volumes de dados.
>
> Recomendamos que utilize este método apenas para fontes de dados de baixo volume ou para enriquecer os seus uploads de dados.
>

1. **Utilize um dos seguintes gatilhos para iniciar as suas Aplicações Lógicas**:

    |Acionador  |Description  |
    |---------|---------|
    |**Uma tarefa recorrente**     |   Por exemplo, agende a sua App Lógica para recuperar dados regularmente a partir de ficheiros específicos, bases de dados ou APIs externos. <br>Para obter mais informações, consulte [Criar, programar e executar tarefas e fluxos de trabalho recorrentes em Azure Logic Apps](/azure/connectors/connectors-native-recurrence).      |
    |**Desencadeamento a pedido**     | Execute a sua Aplicação Lógica a pedido para recolha e teste manual de dados. <br>Para obter mais informações, consulte  [aplicações lógicas Call, Trigger ou Nest logic utilizando pontos finais HTTPS](/azure/logic-apps/logic-apps-http-endpoint).        |
    |**Ponto final HTTP/S**     |  Recomendado para o streaming, e se o sistema de origem pode iniciar a transferência de dados. <br>Para obter mais informações, consulte [os pontos finais do serviço de chamada em HTTP ou HTTPs](/azure/connectors/connectors-native-http).       |
    |     |         |

1. **Utilize qualquer um dos conectores Logic App que lêem informações para obter os seus eventos.** Por exemplo:

    - [Ligue-se a uma API REST](/connectors/custom-connectors/)
    - [Ligue-se a um servidor SQL](/connectors/sql/)
    - [Ligar-se a um sistema de ficheiros](/connectors/filesystem/)

    > [!TIP]
    > Os conectores personalizados para REST APIs, SQL Servers e sistemas de ficheiros também suportam a obtenção de dados de fontes de dados no local. Para obter mais informações, consulte [instalar documentação de gateway de dados no local.](/connectors/filesystem/)
    >

1. **Prepare as informações que pretende obter.**

    Por exemplo, utilize a [ação parse JSON](/azure/logic-apps/logic-apps-perform-data-operations#parse-json-action) para aceder a propriedades em conteúdo JSON, permitindo-lhe selecionar essas propriedades a partir da lista de conteúdos dinâmicos quando especificar entradas para a sua App Lógica.

    Para obter mais informações, consulte [Executar operações de dados em Azure Logic Apps](/azure/logic-apps/logic-apps-perform-data-operations).

1. **Escreva os dados para Registar Analytics.**

    Para mais informações, consulte a documentação do [Azure Log Analytics Data Collector.](/connectors/azureloganalyticsdatacollector/)

Por exemplo, como pode criar um conector personalizado para o Azure Sentinel utilizando aplicações lógicas, consulte:

- [Criar um pipeline de dados com a API do Colecionador de Dados](/connectors/azureloganalyticsdatacollector/)
- [Conector Palo Alto Prisma Logic App usando um webhook](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Ingest-Prisma) (comunidade Azure Sentinel GitHub)
- [Proteja as suas chamadas microsoft Teams com ativação programada](https://techcommunity.microsoft.com/t5/azure-sentinel/secure-your-calls-monitoring-microsoft-teams-callrecords/ba-p/1574600) (blog)
- [Ingerir indicadores de ameaça AlienVault OTX em Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/ingesting-alien-vault-otx-threat-indicators-into-azure-sentinel/ba-p/1086566) (blog)
- [Envio de registos tap de ponto de prova para Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/sending-proofpoint-tap-logs-to-azure-sentinel/ba-p/767727) (blog)


## <a name="connect-with-powershell"></a>Ligar com o PowerShell

O [script Upload-AzMonitorLog PowerShell](https://www.powershellgallery.com/packages/Upload-AzMonitorLog/) permite-lhe utilizar o PowerShell para transmitir eventos ou informações de contexto para Azure Sentinel a partir da linha de comando. Este streaming cria efetivamente um conector personalizado entre a sua fonte de dados e o Azure Sentinel.

Por exemplo, o seguinte script envia um ficheiro CSV para Azure Sentinel:

``` PowerShell
Import-Csv .\testcsv.csv
| .\Upload-AzMonitorLog.ps1
-WorkspaceId '69f7ec3e-cae3-458d-b4ea-6975385-6e426'
-WorkspaceKey $WSKey
-LogTypeName 'MyNewCSV'
-AddComputerName
-AdditionalDataTaggingName "MyAdditionalField"
-AdditionalDataTaggingValue "Foo"
```

O script [de scripts Upload-AzMonitorLog PowerShell](https://www.powershellgallery.com/packages/Upload-AzMonitorLog/) utiliza os seguintes parâmetros:

|Parâmetro  |Descrição  |
|---------|---------|
|**WorkspaceId**     |   O seu espaço de trabalho Azure Sentinel, onde vai armazenar os seus dados.  [Encontre o seu ID e a chave do seu espaço de trabalho.](#find-your-workspace-id-and-key)  |
|**Espaço de trabalhoKey**     |   A chave primária ou secundária para o espaço de trabalho Azure Sentinel onde você estará armazenando os seus dados. [Encontre o seu ID e a chave do seu espaço de trabalho.](#find-your-workspace-id-and-key)  |
|**Nome de Registo**     |    O nome da tabela de registo personalizado onde pretende armazenar os dados. Um sufixo de **_CL** será automaticamente adicionado ao final do seu nome de mesa.  |
|**AddComputerName**     |   Quando este parâmetro existe, o script adiciona o nome do computador atual a cada registo de registo, num campo chamado **Computador**.      |
|**TaggedAzureResourceId**     | Quando este parâmetro existe, o script associa todos os registos de registos carregados com o recurso Azure especificado. <br><br>Esta associação permite os registos de registos carregados para consultas de contexto de recursos, e adere ao controlo de acesso centrado em recursos e baseado em funções.       |
|**Nome adicional doDataTagging**     |      Quando este parâmetro existe, o script adiciona outro campo a cada registo de registo, com o nome configurado, e o valor configurado para o parâmetro **AdicionalDataTaggingValue.** <br><br>Neste caso, **o AdicionalDataTaggingValue** não deve estar vazio. |
|**AdicionalDataTaggingValue**     |   Quando este parâmetro existe, o script adiciona outro campo a cada registo de registo, com o valor configurado, e o nome de campo configurado para o parâmetro **AdicionalDataTaggingName.** <br><br>Se o parâmetro **AdicionalDataTaggingName** estiver vazio, mas um valor estiver configurado, o nome de campo predefinido é **DataTagging**.       |
|     |         |

### <a name="find-your-workspace-id-and-key"></a>Encontre o seu ID e chave do seu espaço de trabalho

Encontre os detalhes dos parâmetros **WorkspaceID** e **WorkspaceKey** em Azure Sentinel:

1. Em Azure Sentinel, selecione **Definições** à esquerda e, em seguida, selecione o **separador definições do Espaço de Trabalho.**

1. Under **Get start with Log Analytics**  >  **1 Connect a data source**, select Windows and **Linux agents management**.

1. Encontre o seu ID do espaço de trabalho, a chave primária e a chave secundária nos separadores dos servidores do **Windows.**
## <a name="connect-with-the-log-analytics-api"></a>Conecte-se com a API de API de Log Analytics

Pode transmitir eventos para Azure Sentinel utilizando a API do Colecionador de Dados do Log Analytics para ligar diretamente para um ponto final RESTful.

Ao chamar diretamente um ponto final RESTful requer mais programação, também proporciona mais flexibilidade.

Para obter mais informações, consulte a [API do coletor de dados do Log Analytics,](/azure/azure-monitor/platform/data-collector-api)especialmente os seguintes exemplos:

- [C#](https://docs.microsoft.com/azure/azure-monitor/platform/data-collector-api#c-sample)
- [Python 2](https://docs.microsoft.com/azure/azure-monitor/platform/data-collector-api#python-2-sample)

## <a name="connect-with-azure-functions"></a>Conecte-se com funções Azure

Utilize funções Azure juntamente com uma API RESTful e várias línguas de codificação, como [o PowerShell,](/azure/azure-functions/functions-reference-powershell)para criar um conector personalizado sem servidor.

Por exemplo, este método, consulte:

- [Ligue o seu VMware Carbon Black Cloud Endpoint Standard ao Azure Sentinel com a função Azure](connect-vmware-carbon-black.md)
- [Ligue o seu Sign-On Single Okta ao Azure Sentinel com a Função Azure](connect-okta-single-sign-on.md)
- [Ligue o seu Ponto de Revisão TAP ao Azure Sentinel com a Função Azure](connect-proofpoint-tap.md)
- [Ligue o seu VM Qualys ao Azure Sentinel com a Função Azure](connect-qualys-vm.md)
- [Ingerir XML, CSV ou outros formatos de dados](/azure/azure-monitor/platform/create-pipeline-datacollector-api#ingesting-xml-csv-or-other-formats-of-data)
- [Monitorar Zoom com Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/monitoring-zoom-with-azure-sentinel/ba-p/1341516) (blog)
- [Implementar uma App de Função para obter dados da API de gestão do Office 365 em Azure Sentinel](https://github.com/Azure/Azure-Sentinel/tree/master/DataConnectors/O365%20Data) (comunidade Azure Sentinel GitHub)

## <a name="parse-your-custom-connector-data"></a>Analise os dados do conector personalizado

Pode utilizar a técnica de análise incorporada do seu conector personalizado para extrair as informações relevantes e preencher os campos relevantes em Azure Sentinel.

Por exemplo:

- **Se tiver utilizado o Logstash,** utilize o plugin do filtro [Grok](https://www.elastic.co/guide/en/logstash/current/plugins-filters-grok.html) para analisar os seus dados.
- **Se tiver usado uma função Azure,** analise os seus dados com código. Para mais informações, consulte [Parsers.](normalization.md#parsers)

Azure Sentinel suporta a análise na hora da consulta. A análise no tempo de consulta permite-lhe empurrar os dados no formato original e, em seguida, analisar a pedido, quando necessário.

A análise na hora da consulta também significa que não precisa de saber a estrutura exata dos seus dados com antecedência, quando cria o seu conector personalizado, ou mesmo a informação que precisa para extrair. Em vez disso, analise os seus dados a qualquer momento, mesmo durante uma investigação.

> [!NOTE]
> A atualização do seu parser também se aplica a dados que já ingeriu no Azure Sentinel.
> 
## <a name="next-steps"></a>Passos seguintes

Utilize os dados ingeridos no Azure Sentinel para garantir o seu ambiente com qualquer um dos seguintes processos:

- [Obter visibilidade sobre os alertas](quickstart-get-visibility.md)
- [Visualizar e monitorizar os seus dados](tutorial-monitor-your-data.md)
- [Investigar incidentes](tutorial-investigate-cases.md)
- [Detetar ameaças](tutorial-detect-threats-built-in.md)
- [Automatizar a prevenção de ameaças](tutorial-respond-threats-playbook.md)
- [Investigar ameaças](hunting.md)
