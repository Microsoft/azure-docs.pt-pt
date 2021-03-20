---
title: Visualizar os registos de fluxo NSG - Pilha Elástica
titleSuffix: Azure Network Watcher
description: Gerir e analisar os Registos de Fluxo do Grupo de Segurança de Rede em Azure utilizando o Observador de Rede e a Pilha Elástica.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: aca8c75f262e472cbc770c052b86d6e760ee449a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "95026476"
---
# <a name="visualize-azure-network-watcher-nsg-flow-logs-using-open-source-tools"></a>Visualizar registos do fluxo do NSG do Observador de Rede do Azure com ferramentas open-source

Os registos de fluxo do Grupo de Segurança de Rede fornecem informações que podem ser utilizadas compreendem o tráfego IP de entrada e saída em Grupos de Segurança de Rede. Estes registos de fluxo mostram fluxos de saída e de entrada numa base de regra, o NIC o fluxo aplica-se a 5 informações sobre o fluxo (Source/Destination IP, Source/Destination Port, Protocol), e se o tráfego foi permitido ou negado.

Estes registos de fluxo podem ser difíceis de analisar manualmente e obter insights de. No entanto, existem várias ferramentas de código aberto que podem ajudar a visualizar estes dados. Este artigo fornecerá uma solução para visualizar estes troncos usando a Pilha Elástica, que lhe permitirá indexar e visualizar rapidamente os seus registos de fluxo num dashboard kibana.

## <a name="scenario"></a>Scenario

Neste artigo, vamos criar uma solução que lhe permitirá visualizar os registos de fluxo do Grupo de Segurança de Rede utilizando a Pilha Elástica.  Um plugin de entrada logstash obtém os registos de fluxo diretamente da bolha de armazenamento configurada para conter os registos de fluxo. Em seguida, usando a Pilha Elástica, os registos de fluxo serão indexados e usados para criar um dashboard Kibana para visualizar a informação.

![O diagrama mostra um cenário que permite visualizar os registos de fluxo do Grupo de Segurança de Rede utilizando a Pilha Elástica.][scenario]

## <a name="steps"></a>Passos

### <a name="enable-network-security-group-flow-logging"></a>Ativar a registo de fluxo do Grupo de Segurança de Rede
Para este cenário, deve ter o Fluxo de Fluxo de Rede ativado em pelo menos um Grupo de Segurança de Rede na sua conta. Para obter instruções sobre a ativação dos registos de fluxo de segurança da rede, consulte o seguinte artigo [Introdução à registo de fluxo para grupos de segurança da rede](network-watcher-nsg-flow-logging-overview.md).

### <a name="set-up-the-elastic-stack"></a>Configurar a Pilha Elástica
Ao ligar os registos de fluxo NSG com a Pilha Elástica, podemos criar um dashboard Kibana que nos permite pesquisar, grafar, analisar e obter insights dos nossos registos.

#### <a name="install-elasticsearch"></a>Instalar o elástico

1. A Pilha Elástica da versão 5.0 ou superior requer Java 8. Verifique o comando `java -version` para verificar a sua versão. Se não tiver java instalado, consulte a documentação sobre os [JDKs suposos do Azure.](/azure/developer/java/fundamentals/java-jdk-long-term-support)
2. Descarregue o pacote binário correto para o seu sistema:

   ```bash
   curl -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.2.0.deb
   sudo dpkg -i elasticsearch-5.2.0.deb
   sudo /etc/init.d/elasticsearch start
   ```

   Outros métodos de instalação podem ser encontrados na [Instalação Elasticsearch](https://www.elastic.co/guide/en/beats/libbeat/5.2/elasticsearch-installation.html)

3. Verifique se o elasticsearch está a funcionar com o comando:

    ```bash
    curl http://127.0.0.1:9200
    ```

    Deve ver uma resposta semelhante a esta:

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

Para obter mais instruções sobre a instalação da busca elástica, consulte [as instruções de instalação](https://www.elastic.co/guide/en/elasticsearch/reference/5.2/_installation.html).

### <a name="install-logstash"></a>Instalar Logstash

1. Para instalar o Logstash, executar os seguintes comandos:

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
                  "flowstate" => "%{[records][properties][flows][flows][flowTuples][8]}"
                   "packetsSourceToDest" => "%{[records][properties][flows][flows][flowTuples][9]}"
                   "bytesSentSourceToDest" => "%{[records][properties][flows][flows][flowTuples][10]}"
                   "packetsDestToSource" => "%{[records][properties][flows][flows][flowTuples][11]}"
                   "bytesSentDestToSource" => "%{[records][properties][flows][flows][flowTuples][12]}"
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

Para obter mais instruções sobre a instalação do Logstash, consulte a [documentação oficial](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html).

### <a name="install-the-logstash-input-plugin-for-azure-blob-storage"></a>Instale o plugin de entrada Logstash para armazenamento de bolhas Azure

Este plugin Logstash permitir-lhe-á aceder diretamente aos registos de fluxo da sua conta de armazenamento designada. Para instalar este plugin, a partir do diretório de instalação de Logstash predefinido (neste caso /usr/share/logstash/bin) executar o comando:

```bash
logstash-plugin install logstash-input-azureblob
```

Para iniciar o Logstash, executar o comando:

```bash
sudo /etc/init.d/logstash start
```

Para obter mais informações sobre este plugin, consulte a [documentação](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob).

### <a name="install-kibana"></a>Instalar o Kibana

1. Executar os seguintes comandos para instalar Kibana:

   ```bash
   curl -L -O https://artifacts.elastic.co/downloads/kibana/kibana-5.2.0-linux-x86_64.tar.gz
   tar xzvf kibana-5.2.0-linux-x86_64.tar.gz
   ```

2. Para executar Kibana use os comandos:

   ```bash
   cd kibana-5.2.0-linux-x86_64/
   ./bin/kibana
   ```

3. Para ver a sua interface web Kibana, navegue para `http://localhost:5601`
4. Para este cenário, o padrão de índice utilizado para os registos de fluxo é "nsg-flow-logs". Pode alterar o padrão de índice na secção "saída" do seu ficheiro logstash.conf.
5. Se quiser ver o painel kibana remotamente, crie uma regra NSG de entrada que permite o acesso à **porta 5601**.

### <a name="create-a-kibana-dashboard"></a>Criar um painel kibana

Um painel de amostras para visualizar tendências e detalhes nos seus alertas é mostrado na seguinte imagem:

![figura 1][1]

Descarregue o [ficheiro do dashboard,](https://aka.ms/networkwatchernsgflowlogdashboard)o [ficheiro de visualização](https://aka.ms/networkwatchernsgflowlogvisualizations)e o [ficheiro de pesquisa guardado](https://aka.ms/networkwatchernsgflowlogsearch).

Sob o **separador Gestão** de Kibana, navegue para **Objetos Guardados** e importe os três ficheiros. Em seguida, a partir do **separador Dashboard** pode abrir e carregar o painel de amostras.

Também pode criar as suas próprias visualizações e dashboards adaptados para métricas do seu próprio interesse. Leia mais sobre a criação de visualizações de Kibana a partir da [documentação oficial](https://www.tutorialspoint.com/kibana/kibana_create_visualization.htm)de Kibana .

### <a name="visualize-nsg-flow-logs"></a>Visualizar registos de fluxo NSG

O painel de amostras fornece várias visualizações dos registos de fluxo:

1. Fluxos por Decisão/Direção Ao longo do tempo - gráficos de séries de tempo que mostram o número de fluxos ao longo do período de tempo. Pode editar a unidade de tempo e de extensão de ambas as visualizações. Os fluxos por decisão mostram a proporção de permitir ou negar decisões tomadas, enquanto flows by Direction mostra a proporção de tráfego de entrada e saída. Com estes visuais pode examinar as tendências de tráfego ao longo do tempo e procurar quaisquer picos ou padrões incomuns.

   ![A screenshot mostra um painel de amostras com fluxos por decisão e direção ao longo do tempo.][2]

2. Flows by Destination/Source Port – gráficos de tartes que mostram a repartição dos fluxos para as respetivas portas. Com esta vista pode ver os seus portos mais utilizados. Se clicar numa porta específica dentro do gráfico de tartes, o resto do painel filtrar-se-á até aos fluxos dessa porta.

   ![A screenshot mostra um painel de amostras com fluxos por destino e porta de origem.][3]

3. Número de Fluxos e Início do Tempo de Registo – métricas que mostram o número de fluxos registados e a data do primeiro registo capturado.

   ![A screenshot mostra um painel de amostras com o número de fluxos e a hora de início do registo.][4]

4. Flows by NSG and Rule – um gráfico de barras que mostra a distribuição dos fluxos dentro de cada NSG, bem como a distribuição de regras dentro de cada NSG. A partir daqui você pode ver quais NSG e regras geraram mais tráfego.

   ![A screenshot mostra um painel de amostras com fluxos por N S G e regra.][5]

5. Top 10 IPs de Origem/Destino – gráficos de barras que mostram os 10 melhores IPs de origem e destino. Pode ajustar estes gráficos para mostrar mais ou menos iPs de topo. A partir daqui pode ver os IPs mais comumente ocorrentes, bem como a decisão de tráfego (permitir ou negar) sendo tomada em relação a cada IP.

   ![A screenshot mostra um painel de amostras com fluxos pelos dez principais endereços de origem e destino I P.][6]

6. Flow Tuples – esta tabela mostra-lhe as informações contidas em cada tuple de fluxo, bem como as respetivas NGS e regra.

   ![A screenshot mostra tuples de fluxo numa mesa.][7]

Utilizando a barra de consulta na parte superior do painel de instrumentos, pode filtrar o painel de instrumentos com base em qualquer parâmetro dos fluxos, tais como ID de subscrição, grupos de recursos, regra ou qualquer outra variável de interesse. Para saber mais sobre as consultas e filtros de Kibana, consulte a [documentação oficial](https://www.elastic.co/guide/en/beats/packetbeat/current/kibana-queries-filters.html)

## <a name="conclusion"></a>Conclusão

Ao combinar os registos de fluxo do Grupo de Segurança de Rede com a Pilha Elástica, criámos uma forma poderosa e personalizável de visualizar o tráfego da nossa rede. Estes dashboards permitem-lhe obter e partilhar rapidamente informações sobre o tráfego da sua rede, bem como filtrar e investigar quaisquer anomalias potenciais. Usando Kibana, você pode personalizar estes dashboards e criar visualizações específicas para atender a quaisquer necessidades de segurança, auditoria e conformidade.

## <a name="next-steps"></a>Passos seguintes

Saiba como visualizar os seus registos de fluxo NSG com Power BI visitando [registos de fluxos NSG do Visualize com Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)

<!--Image references-->

[scenario]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/scenario.png
[1]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure1.png
[2]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure2.png
[3]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure3.png
[4]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure4.png
[5]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure5.png
[6]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure6.png
[7]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure7.png