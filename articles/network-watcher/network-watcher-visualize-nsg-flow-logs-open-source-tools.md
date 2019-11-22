---
title: Visualizar logs de fluxo NSG – pilha elástica
titleSuffix: Azure Network Watcher
description: Gerencie e analise os logs de fluxo do grupo de segurança de rede no Azure usando o observador de rede e a pilha elástica.
services: network-watcher
documentationcenter: na
author: mattreatMSFT
manager: vitinnan
editor: ''
ms.assetid: e9b2dcad-4da4-4d6b-aee2-6d0afade0cb8
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: mareat
ms.openlocfilehash: 53cbfe08d310f7244134e1ae31b18644a83c63d3
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277751"
---
# <a name="visualize-azure-network-watcher-nsg-flow-logs-using-open-source-tools"></a>Visualizar logs de fluxo do NSG do observador de rede do Azure usando ferramentas de código aberto

Os logs de fluxo do grupo de segurança de rede fornecem informações que podem ser usadas para entender o tráfego IP de entrada e saída em grupos de segurança de rede. Esses logs de fluxo mostram os fluxos de entrada e saída por regra, a NIC à qual o fluxo se aplica, 5 informações de tupla sobre o fluxo (IP de origem/destino, porta de origem/destino, protocolo) e se o tráfego foi permitido ou negado.

Esses logs de fluxo podem ser difíceis de analisar manualmente e obter informações do. No entanto, há várias ferramentas de código-fonte aberto que podem ajudar a visualizar esses dados. Este artigo fornecerá uma solução para visualizar esses logs usando a pilha elástica, que permitirá que você indexe e visualize rapidamente seus logs de fluxo em um painel do Kibana.

> [!Warning]  
> As etapas a seguir funcionam com os logs de fluxo versão 1. Para obter detalhes, consulte [introdução ao log de fluxo para grupos de segurança de rede](network-watcher-nsg-flow-logging-overview.md). As instruções a seguir não funcionarão com a versão 2 dos arquivos de log, sem modificação.

## <a name="scenario"></a>Cenário

Neste artigo, configuraremos uma solução que permitirá que você visualize os logs de fluxo do grupo de segurança de rede usando a pilha elástica.  Um plug-in de entrada Logstash obterá os logs de fluxo diretamente do blob de armazenamento configurado para conter os logs de fluxo. Em seguida, usando a pilha elástica, os logs de fluxo serão indexados e usados para criar um painel do Kibana para visualizar as informações.

![cenário][scenario]

## <a name="steps"></a>Passos

### <a name="enable-network-security-group-flow-logging"></a>Habilitar o log de fluxo do grupo de segurança de rede
Para este cenário, você deve ter o log de fluxo do grupo de segurança de rede habilitado em pelo menos um grupo de segurança de rede em sua conta. Para obter instruções sobre como habilitar os logs de fluxo de segurança de rede, consulte o seguinte artigo [introdução ao log de fluxo para grupos de segurança de rede](network-watcher-nsg-flow-logging-overview.md).

### <a name="set-up-the-elastic-stack"></a>Configurar a pilha elástica
Conectando os logs de fluxo do NSG com a pilha elástica, podemos criar um painel do Kibana o que nos permite pesquisar, grafar, analisar e derivar informações de nossos logs.

#### <a name="install-elasticsearch"></a>Instalar o Elasticsearch

1. A pilha elástica da versão 5,0 e superior requer o Java 8. Execute o comando `java -version` para verificar sua versão. Se você não tiver o Java instalado, consulte a documentação sobre o [Azure-há suporte para JDKs](https://aka.ms/azure-jdks).
2. Baixe o pacote binário correto para seu sistema:

   ```bash
   curl -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.2.0.deb
   sudo dpkg -i elasticsearch-5.2.0.deb
   sudo /etc/init.d/elasticsearch start
   ```

   Outros métodos de instalação podem ser encontrados em [instalação do Elasticsearch](https://www.elastic.co/guide/en/beats/libbeat/5.2/elasticsearch-installation.html)

3. Verifique se Elasticsearch está em execução com o comando:

    ```bash
    curl http://127.0.0.1:9200
    ```

    Você deverá ver uma resposta semelhante a esta:

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

Para obter mais instruções sobre como instalar a pesquisa elástica, consulte [as instruções de instalação](https://www.elastic.co/guide/en/elasticsearch/reference/5.2/_installation.html).

### <a name="install-logstash"></a>Instalar o Logstash

1. Para instalar o Logstash, execute os seguintes comandos:

    ```bash
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```
2. Em seguida, precisamos configurar o Logstash para acessar e analisar os logs de fluxo. Crie um arquivo logstash. conf usando:

    ```bash
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

3. Adicione o seguinte conteúdo ao arquivo:

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

Para obter mais instruções sobre como instalar o Logstash, consulte a [documentação oficial](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html).

### <a name="install-the-logstash-input-plugin-for-azure-blob-storage"></a>Instalar o plug-in de entrada Logstash para o armazenamento de BLOBs do Azure

Esse plug-in Logstash permitirá que você acesse diretamente os logs de fluxo de sua conta de armazenamento designada. Para instalar esse plug-in, no diretório de instalação padrão do Logstash (neste caso,,/usr/share/logstash/bin), execute o comando:

```bash
logstash-plugin install logstash-input-azureblob
```

Para iniciar o Logstash, execute o comando:

```bash
sudo /etc/init.d/logstash start
```

Para obter mais informações sobre esse plug-in, consulte a [documentação](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob).

### <a name="install-kibana"></a>Instalar o Kibana

1. Execute os seguintes comandos para instalar o Kibana:

   ```bash
   curl -L -O https://artifacts.elastic.co/downloads/kibana/kibana-5.2.0-linux-x86_64.tar.gz
   tar xzvf kibana-5.2.0-linux-x86_64.tar.gz
   ```

2. Para executar o Kibana, use os comandos:

   ```bash
   cd kibana-5.2.0-linux-x86_64/
   ./bin/kibana
   ```

3. Para exibir a interface da Web do Kibana, navegue até `http://localhost:5601`
4. Para esse cenário, o padrão de índice usado para os logs de fluxo é "NSG-Flow-logs". Você pode alterar o padrão de índice na seção "saída" do seu arquivo logstash. conf.
5. Se você quiser exibir o painel do Kibana remotamente, crie uma regra de NSG de entrada permitindo o acesso à **porta 5601**.

### <a name="create-a-kibana-dashboard"></a>Criar um painel do Kibana

Um painel de exemplo para exibir tendências e detalhes em seus alertas é mostrado na figura a seguir:

![figura 1][1]

Baixe o [arquivo do painel](https://aka.ms/networkwatchernsgflowlogdashboard), o [arquivo de visualização](https://aka.ms/networkwatchernsgflowlogvisualizations)e o [arquivo de pesquisa salvo](https://aka.ms/networkwatchernsgflowlogsearch).

Na guia **Gerenciamento** do Kibana, navegue até **objetos salvos** e importe todos os três arquivos. Em seguida, na guia **painel** , você pode abrir e carregar o painel de exemplo.

Você também pode criar suas próprias visualizações e painéis personalizados para métricas de seus próprios interesses. Leia mais sobre como criar visualizações Kibana da [documentação oficial](https://www.elastic.co/guide/en/kibana/current/visualize.html)do Kibana.

### <a name="visualize-nsg-flow-logs"></a>Visualizar logs de fluxo de NSG

O painel de exemplo fornece várias visualizações dos logs de fluxo:

1. Fluxos por decisão/direção em gráficos de série temporal que mostram o número de fluxos no período de tempo. Você pode editar a unidade de tempo e o alcance dessas duas visualizações. Fluxos por decisão mostram a proporção de decisões permitidas ou negadas feitas, enquanto os fluxos por direção mostram a proporção do tráfego de entrada e de saída. Com esses elementos visuais, você pode examinar as tendências de tráfego ao longo do tempo e procurar por picos ou padrões incomuns.

   ![Figura 2][2]

2. Fluxos por porta de destino/origem – gráficos de pizza que mostram a divisão de fluxos para suas respectivas portas. Com essa exibição, você pode ver as portas usadas com mais frequência. Se você clicar em uma porta específica dentro do gráfico de pizza, o restante do painel será filtrado para os fluxos dessa porta.

   ![figure3][3]

3. Número de fluxos e o tempo de log mais antigo – as métricas mostram o número de fluxos registrados e a data do log mais antigo capturado.

   ![figure4][4]

4. Flui por NSG e regra – um grafo de barras mostrando a distribuição de fluxos em cada NSG, bem como a distribuição de regras em cada NSG. A partir daqui, você pode ver quais NSG e regras geraram mais tráfego.

   ![figure5][5]

5. 10 principais IPs de origem/destino – gráficos de barras mostrando os 10 principais IPs de origem e de destino. Você pode ajustar esses gráficos para mostrar mais ou menos IPs principais. A partir daqui, você pode ver os IPs que ocorrem com mais frequência, bem como a decisão de tráfego (permitir ou negar) sendo feita em direção a cada IP.

   ![figure6][6]

6. Tuplas de fluxo – esta tabela mostra as informações contidas em cada tupla de fluxo, bem como sua NGS e regra correspondentes.

   ![figure7][7]

Usando a barra de consulta na parte superior do painel, você pode filtrar o painel com base em qualquer parâmetro dos fluxos, como ID de assinatura, grupos de recursos, regra ou qualquer outra variável de interesse. Para obter mais informações sobre filtros e consultas do Kibana, consulte a [documentação oficial](https://www.elastic.co/guide/en/beats/packetbeat/current/kibana-queries-filters.html)

## <a name="conclusion"></a>Conclusão

Ao combinar os logs de fluxo do grupo de segurança de rede com a pilha elástica, criamos uma maneira avançada e personalizável de Visualizar nosso tráfego de rede. Esses painéis permitem que você possa obter e compartilhar informações rapidamente sobre o tráfego de rede, bem como filtrar e investigar todas as anomalias em potencial. Usando o Kibana, você pode personalizar esses painéis e criar visualizações específicas para atender às necessidades de segurança, auditoria e conformidade.

## <a name="next-steps"></a>Passos seguintes

Saiba como visualizar seus logs de fluxo do NSG com Power BI visitando [Visualizar logs do NSG fluxos com Power bi](network-watcher-visualize-nsg-flow-logs-power-bi.md)

<!--Image references-->

[scenario]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/scenario.png
[1]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure1.png
[2]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure2.png
[3]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure3.png
[4]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure4.png
[5]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure5.png
[6]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure6.png
[7]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure7.png
