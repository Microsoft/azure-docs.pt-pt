---
title: Visualizar os registos de fluxo nsg - Stack Elástica
titleSuffix: Azure Network Watcher
description: Gerir e analisar os registos de fluxo do grupo de segurança da rede em Azure utilizando o Observador de Rede e a Stack Elástica.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: e567994038fb4f71ef86dc577760ecf4699a0b1d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840643"
---
# <a name="visualize-azure-network-watcher-nsg-flow-logs-using-open-source-tools"></a>Visualizar registos do fluxo do NSG do Observador de Rede do Azure com ferramentas open-source

Os registos de fluxo do Grupo de Segurança da Rede fornecem informações que podem ser usadas, compreendem o tráfego IP em Grupos de Segurança da Rede. Estes registos de fluxo mostram fluxos de saída e de entrada por regra, o NIC a que o fluxo se aplica, 5 informações de tuple sobre o fluxo (Source/Destination IP, Source/Destination Port, Protocol), e se o tráfego foi permitido ou negado.

Estes registos de fluxo podem ser difíceis de analisar manualmente e obter insights de. No entanto, existem várias ferramentas de código aberto que podem ajudar a visualizar estes dados. Este artigo fornecerá uma solução para visualizar estes troncos usando a Stack Elástica, que lhe permitirá indexar e visualizar rapidamente os seus registos de fluxo num dashboard kibana.

> [!Warning]  
> Os passos seguintes funcionam com os registos de fluxo versão 1. Para mais detalhes, consulte [Introdução à exploração de fluxos de registo para grupos de segurança](network-watcher-nsg-flow-logging-overview.md)da rede . As seguintes instruções não funcionarão com a versão 2 dos ficheiros de registo, sem modificação.

## <a name="scenario"></a>Cenário

Neste artigo, vamos criar uma solução que lhe permitirá visualizar os registos de fluxo do Network Security Group utilizando a Stack Elástica.  Um plugin de entrada logstash obterá os registos de fluxo diretamente a partir da bolha de armazenamento configurada para conter os registos de fluxo. Em seguida, utilizando a Stack Elástica, os registos de fluxo serão indexados e utilizados para criar um dashboard Kibana para visualizar a informação.

![cenário][scenario]

## <a name="steps"></a>Passos

### <a name="enable-network-security-group-flow-logging"></a>Ativar a exploração de fluxo do Grupo de Segurança da Rede
Para este cenário, deve ter o Fluxo de Registo do Grupo de Segurança da Rede ativado em pelo menos um Grupo de Segurança de Rede na sua conta. Para obter instruções sobre a ativação de registos de fluxo de segurança da rede, consulte o seguinte artigo [Introdução ao fluxo de registo sinuoso para grupos](network-watcher-nsg-flow-logging-overview.md)de segurança da rede .

### <a name="set-up-the-elastic-stack"></a>Configurar a pilha elástica
Ao ligar os registos de fluxo nsg com a Stack Elástica, podemos criar um dashboard Kibana que nos permite pesquisar, graph, analisar e obter insights dos nossos registos.

#### <a name="install-elasticsearch"></a>Instalar pesquisa elástica

1. A Stack Elástica da versão 5.0 e acima requer Java 8. Execute `java -version` o comando para verificar a sua versão. Se não tiver java instalado, consulte a documentação sobre os [JDKs suppored Azure](https://aka.ms/azure-jdks).
2. Descarregue o pacote binário correto para o seu sistema:

   ```bash
   curl -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.2.0.deb
   sudo dpkg -i elasticsearch-5.2.0.deb
   sudo /etc/init.d/elasticsearch start
   ```

   Outros métodos de instalação podem ser encontrados na [Instalação Elasticsearch](https://www.elastic.co/guide/en/beats/libbeat/5.2/elasticsearch-installation.html)

3. Verifique se a Elasticsearch está a decorrer com o comando:

    ```bash
    curl http://127.0.0.1:9200
    ```

    Devia ver uma resposta semelhante a esta:

    ```json
    {
    "name" : "Angela Del Toro",
    "cluster_name" : "elasticsearch",
    "version" : {
        "number" : "5.2.0",
        "build_hash" : "8ff36d139e16f8720f2947ef62c8167a888992fe",
        "build_timestamp" : "2016-01-27T13:32:39Z",
        "build_snapshot" : false,
        "lucene_version" : "6.1.0"
    },
    "tagline" : "You Know, for Search"
    }
    ```

Para mais instruções sobre a instalação de procura elástica, consulte as instruções de [instalação](https://www.elastic.co/guide/en/elasticsearch/reference/5.2/_installation.html).

### <a name="install-logstash"></a>Instalar logstash

1. Para instalar o Logstash executar os seguintes comandos:

    ```bash
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```
2. Em seguida, precisamos configurar logstash para aceder e analisar os registos de fluxo. Criar um ficheiro logstash.conf utilizando:

    ```bash
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

3. Adicione o seguinte conteúdo ao ficheiro:

   ```
   input {
      azureblob
        {
            storage_account_name => "mystorageaccount"
            storage_access_key => "VGhpcyBpcyBhIGZha2Uga2V5Lg=="
            container => "insights-logs-networksecuritygroupflowevent"
            codec => "json"
            # Refer https://docs.microsoft.com/azure/network-watcher/network-watcher-read-nsg-flow-logs
            # Typical numbers could be 21/9 or 12/2 depends on the nsg log file types
            file_head_bytes => 12
            file_tail_bytes => 2
            # Enable / tweak these settings when event is too big for codec to handle.
            # break_json_down_policy => "with_head_tail"
            # break_json_batch_count => 2
        }
      }

      filter {
        split { field => "[records]" }
        split { field => "[records][properties][flows]"}
        split { field => "[records][properties][flows][flows]"}
        split { field => "[records][properties][flows][flows][flowTuples]"}

     mutate{
      split => { "[records][resourceId]" => "/"}
      add_field => {"Subscription" => "%{[records][resourceId][2]}"
                    "ResourceGroup" => "%{[records][resourceId][4]}"
                    "NetworkSecurityGroup" => "%{[records][resourceId][8]}"}
      convert => {"Subscription" => "string"}
      convert => {"ResourceGroup" => "string"}
      convert => {"NetworkSecurityGroup" => "string"}
      split => { "[records][properties][flows][flows][flowTuples]" => ","}
      add_field => {
                  "unixtimestamp" => "%{[records][properties][flows][flows][flowTuples][0]}"
                  "srcIp" => "%{[records][properties][flows][flows][flowTuples][1]}"
                  "destIp" => "%{[records][properties][flows][flows][flowTuples][2]}"
                  "srcPort" => "%{[records][properties][flows][flows][flowTuples][3]}"
                  "destPort" => "%{[records][properties][flows][flows][flowTuples][4]}"
                  "protocol" => "%{[records][properties][flows][flows][flowTuples][5]}"
                  "trafficflow" => "%{[records][properties][flows][flows][flowTuples][6]}"
                  "traffic" => "%{[records][properties][flows][flows][flowTuples][7]}"
                   }
      convert => {"unixtimestamp" => "integer"}
      convert => {"srcPort" => "integer"}
      convert => {"destPort" => "integer"}        
     }

     date{
       match => ["unixtimestamp" , "UNIX"]
     }
    }
   output {
     stdout { codec => rubydebug }
     elasticsearch {
       hosts => "localhost"
       index => "nsg-flow-logs"
     }
   }  
   ```

Para mais instruções sobre a instalação do Logstash, consulte a [documentação oficial](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html).

### <a name="install-the-logstash-input-plugin-for-azure-blob-storage"></a>Instale o plugin de entrada Logstash para armazenamento de blob Azure

Este plugin logstash permitir-lhe-á aceder diretamente aos registos de fluxo da sua conta de armazenamento designada. Para instalar este plugin, a partir do diretório de instalação de Logstash predefinido (neste caso/usr/share/logstash/bin) executar o comando:

```bash
logstash-plugin install logstash-input-azureblob
```

Para iniciar o Logstash executar o comando:

```bash
sudo /etc/init.d/logstash start
```

Para mais informações sobre este plugin, consulte a [documentação](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob).

### <a name="install-kibana"></a>Instalar Kibana

1. Executar os seguintes comandos para instalar kibana:

   ```bash
   curl -L -O https://artifacts.elastic.co/downloads/kibana/kibana-5.2.0-linux-x86_64.tar.gz
   tar xzvf kibana-5.2.0-linux-x86_64.tar.gz
   ```

2. Para executar Kibana, use os comandos:

   ```bash
   cd kibana-5.2.0-linux-x86_64/
   ./bin/kibana
   ```

3. Para ver a sua interface web Kibana, navegue para`http://localhost:5601`
4. Para este cenário, o padrão de índice utilizado para os registos de fluxo é "nsg-flow-logs". Pode alterar o padrão de índice na secção "output" do seu ficheiro logstash.conf.
5. Se quiser ver o dashboard kibana remotamente, crie uma regra NSG de entrada que permita o acesso à **porta 5601**.

### <a name="create-a-kibana-dashboard"></a>Crie um dashboard Kibana

Um painel de amostras para visualizar tendências e detalhes nos seus alertas é mostrado na imagem seguinte:

![figura 1][1]

Descarregue o ficheiro do painel de [instrumentos,](https://aka.ms/networkwatchernsgflowlogdashboard)o ficheiro de [visualização](https://aka.ms/networkwatchernsgflowlogvisualizations)e o [ficheiro de pesquisa guardado.](https://aka.ms/networkwatchernsgflowlogsearch)

Sob o separador **Gestão** de Kibana, navegue para **Saved Objects** e importe os três ficheiros. Em seguida, a partir do **separador Dashboard** pode abrir e carregar o painel de dados da amostra.

Também pode criar as suas próprias visualizações e dashboards adaptados para métricas do seu próprio interesse. Leia mais sobre a criação de visualizações kibana a partir da [documentação oficial](https://www.elastic.co/guide/en/kibana/current/visualize.html)de Kibana.

### <a name="visualize-nsg-flow-logs"></a>Visualizar os registos de fluxo nsg

O painel de amostras fornece várias visualizações dos registos de fluxo:

1. Flows by Decision/Direction Over Time - gráficos da série de tempo que mostram o número de fluxos durante o período de tempo. Pode editar a unidade de tempo e o tempo de ambas estas visualizações. Os fluxos por decisão mostram a proporção de decisões de permitir ou negar tomadas, enquanto os Fluxos por Direção mostram a proporção de tráfego de entrada e saída. Com estes visuais pode examinar as tendências de tráfego ao longo do tempo e procurar por quaisquer picos ou padrões incomuns.

   ![figura2][2]

2. Fluxos por Destination/Source Port – gráficos de tartes que mostram a quebra de fluxos para as respetivas portas. Com esta vista pode ver as portas mais usadas. Se clicar numa porta específica dentro do gráfico de tartes, o resto do painel de instrumentos filtrar-se-á até aos fluxos dessa porta.

   ![figura3][3]

3. Número de Fluxos e Tempo de Registo Mais Antigo – métricas que mostram o número de fluxos registados e a data do primeiro registo capturado.

   ![figura4][4]

4. Flui por NSG e Regra – um gráfico de barras que mostra a distribuição de fluxos dentro de cada NSG, bem como a distribuição de regras dentro de cada NSG. A partir daqui você pode ver quais NSG e regras geraram mais tráfego.

   ![figura5][5]

5. Top 10 Source/Destination IPs – gráficos de barras que mostram os 10 melhores IPs de origem e destino. Pode ajustar estes gráficos para mostrar iPs mais ou menos topo. A partir daqui pode ver os IPs mais comuns, bem como a decisão de tráfego (permitir ou negar) ser tomada em direção a cada IP.

   ![figura6][6]

6. Flow Tuples – esta tabela mostra-lhe as informações contidas em cada túnica de fluxo, bem como a sua correspondente NGS e regra.

   ![figura7][7]

Utilizando a barra de consulta na parte superior do painel de instrumentos, pode filtrar o painel de instrumentos com base em qualquer parâmetro dos fluxos, tais como ID de subscrição, grupos de recursos, regra ou qualquer outra variável de interesse. Para mais informações sobre as consultas e filtros de Kibana, consulte a [documentação oficial](https://www.elastic.co/guide/en/beats/packetbeat/current/kibana-queries-filters.html)

## <a name="conclusion"></a>Conclusão

Ao combinar os registos de fluxo do Grupo de Segurança da Rede com a Stack Elástica, temos uma forma poderosa e personalizável de visualizar o nosso tráfego de rede. Estes dashboards permitem-lhe obter rapidamente e partilhar informações sobre o tráfego da sua rede, bem como filtrar e investigar eventuais anomalias. Utilizando a Kibana, pode adaptar estes dashboards e criar visualizações específicas para atender a quaisquer necessidades de segurança, auditoria e conformidade.

## <a name="next-steps"></a>Passos seguintes

Saiba como visualizar os seus registos de fluxo NSG com Power BI visitando visualize os [registos de fluxos NSG com Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)

<!--Image references-->

[scenario]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/scenario.png
[1]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure1.png
[2]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure2.png
[3]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure3.png
[4]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure4.png
[5]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure5.png
[6]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure6.png
[7]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure7.png
