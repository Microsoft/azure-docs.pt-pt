---
title: Gerir e analisar registos de fluxo do grupo de segurança de rede com o observador de rede e a pilha elástica | Documentos da Microsoft
description: Gerir e analisar a rede grupo fluxo registos de segurança no Azure com o observador de rede e o Elastic Stack.
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
ms.openlocfilehash: 7361eff0f76271564fd5a0e9b8a18221ec4138e3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60860121"
---
# <a name="visualize-azure-network-watcher-nsg-flow-logs-using-open-source-tools"></a>Visualizar registos de fluxo de NSG de observador de rede do Azure utilizando ferramentas open source

Os registos de fluxo do grupo de segurança de rede fornecem informações que podem ser utilizadas compreender o tráfego IP de entrada e de saída nos grupos de segurança de rede. Estes registos de fluxo mostram fluxos de saída e entrados numa base por regra, o fluxo de mensagens em fila aplica-se a NIC, 5 de cadeias de identificação informações sobre o fluxo (IP de origem/destino, porta de origem/destino, protocolo) e se o tráfego foi permitido ou negado.

Estes registos de fluxo podem ser difíceis de analisar e obter informações a partir de manualmente. No entanto, existem várias ferramentas de código-fonte aberto que podem ajudar a visualizar estes dados. Este artigo fornece uma solução para visualizar estes registos com a pilha elástica, que irá permitir-lhe rapidamente de índice e visualizar o fluxo faz logon num dashboard do Kibana.

> [!Warning]  
> Os seguintes passos funcionam com a versão de registos de fluxo 1. Para obter detalhes, consulte [introdução ao registo do fluxo para grupos de segurança de rede](network-watcher-nsg-flow-logging-overview.md). As instruções seguintes não irão funcionar com a versão 2 dos ficheiros de registo, sem modificações.

## <a name="scenario"></a>Cenário

Neste artigo, vamos configurar uma solução que irá permitir-lhe visualizar registos de fluxo do grupo de segurança de rede com a pilha elástica.  Um plug-in de entrada de Logstash irá obter os registos de fluxo diretamente a partir do blob de armazenamento configurado para que contém os registos de fluxo. Em seguida, usando a pilha elástica, os registos de fluxo serão indexados e utilizados para criar um dashboard do Kibana para visualizar as informações.

![cenário][scenario]

## <a name="steps"></a>Passos

### <a name="enable-network-security-group-flow-logging"></a>Registo do fluxo do grupo de segurança de rede ativar
Para este cenário, tem de ter a rede segurança grupo fluxo o registo ativado em, pelo menos, um grupo de segurança de rede na sua conta. Para obter instruções sobre como ativar os registos de fluxo de segurança de rede, consulte o seguinte artigo [introdução ao registo do fluxo para grupos de segurança de rede](network-watcher-nsg-flow-logging-overview.md).

### <a name="set-up-the-elastic-stack"></a>Configurar a pilha elástica
Ao ligar os registos de fluxo do NSG com a pilha elástica, podemos criar um dashboard do Kibana o que permite a pesquisa, criar um gráfico, analisar e obter informações a partir de nossos logs.

#### <a name="install-elasticsearch"></a>Instale o Elasticsearch

1. A pilha elástica da versão 5.0 e superior requer Java 8. Execute o comando `java -version` para verificar a sua versão. Se não tiver instalado de java, consulte a documentação sobre o [Azure-suppored JDKs](https://aka.ms/azure-jdks).
2. Transferir o pacote de binário correto para o seu sistema:

   ```bash
   curl -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.2.0.deb
   sudo dpkg -i elasticsearch-5.2.0.deb
   sudo /etc/init.d/elasticsearch start
   ```

   Outros métodos de instalação podem ser encontrados em [instalação Elasticsearch](https://www.elastic.co/guide/en/beats/libbeat/5.2/elasticsearch-installation.html)

3. Certifique-se de que o Elasticsearch está em execução com o comando:

    ```bash
    curl http://127.0.0.1:9200
    ```

    Deverá ver uma resposta semelhante ao seguinte:

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

Para obter mais instruções na pesquisa de elástica de instalação, consulte [instruções de instalação](https://www.elastic.co/guide/en/elasticsearch/reference/5.2/_installation.html).

### <a name="install-logstash"></a>Instalar o Logstash

1. Para instalar o Logstash, execute os seguintes comandos:

    ```bash
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```
2. Em seguida, precisamos configurar o Logstash para aceder e analisar os registos de fluxo. Crie um ficheiro de logstash.conf com:

    ```bash
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

3. Adicione o seguinte conteúdo para o ficheiro:

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

### <a name="install-the-logstash-input-plugin-for-azure-blob-storage"></a>Instalar o Logstash entrado Plug-in para o armazenamento de Blobs do Azure

Este plug-in do Logstash permitirá que acessar diretamente os registos de fluxo a partir da respetiva conta de armazenamento designada. Para instalar este plug-in, do diretório de instalação do Logstash padrão (em /usr/share/logstash/bin neste caso), execute o comando:

```bash
logstash-plugin install logstash-input-azureblob
```

Para iniciar o Logstash, execute o comando:

```bash
sudo /etc/init.d/logstash start
```

Para obter mais informações sobre este plug-in, consulte a [documentação](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob).

### <a name="install-kibana"></a>Instalar o Kibana

1. Execute os seguintes comandos para instalar o Kibana:

   ```bash
   curl -L -O https://artifacts.elastic.co/downloads/kibana/kibana-5.2.0-linux-x86_64.tar.gz
   tar xzvf kibana-5.2.0-linux-x86_64.tar.gz
   ```

2. Para executar os comandos de utilização do Kibana:

   ```bash
   cd kibana-5.2.0-linux-x86_64/
   ./bin/kibana
   ```

3. Para ver a sua interface de web do Kibana, navegue para `http://localhost:5601`
4. Para este cenário, o padrão de índice usado para os registos de fluxo é "registos de fluxo de nsg". Pode alterar o padrão de índice na secção "saída" do seu ficheiro logstash.conf.
5. Se pretender ver o dashboard do Kibana remotamente, criar uma regra NSG de entrada que permite o acesso ao **porta 5601**.

### <a name="create-a-kibana-dashboard"></a>Criar um dashboard do Kibana

Um dashboard de exemplo para ver detalhes e as tendências em seus alertas é mostrado na imagem seguinte:

![figura 1][1]

Transfira o [ficheiro de dashboard](https://aka.ms/networkwatchernsgflowlogdashboard), o [ficheiros de visualização](https://aka.ms/networkwatchernsgflowlogvisualizations)e o [guardado o ficheiro de pesquisa](https://aka.ms/networkwatchernsgflowlogsearch).

Sob o **Management** separador do Kibana, navegue até à **objetos guardados** e importar todos os três arquivos. Em seguida, a partir da **Dashboard** separador, pode abrir e carregue o dashboard de exemplo.

Também pode criar suas próprias visualizações e dashboards adaptadas para métricas de seu interesse. Saiba mais sobre a criação de visualizações do Kibana a partir do Kibana [documentação oficial](https://www.elastic.co/guide/en/kibana/current/visualize.html).

### <a name="visualize-nsg-flow-logs"></a>Visualizar registos de fluxo NSG

O dashboard de exemplo fornece várias visualizações dos registos de fluxo:

1. Fluxos por decisão/direção ao longo do tempo - gráficos de séries de tempo que mostra o número de fluxos ao longo do período de tempo. Pode editar a unidade de tempo e span de ambas estas visualizações. Fluxos por decisão mostra a proporção de permitir ou negar as decisões tomadas, enquanto fluxos por direção mostra a proporção de tráfego de entrada e saída. Com estes elementos visuais pode examinar as tendências de tráfego ao longo do tempo e procurar qualquer picos ou padrões invulgares.

   ![figura 2][2]

2. Fluxos por porta de destino/origem – gráficos de pizza que mostra a divisão de fluxos para seus respectivos portas. Com esta vista, pode ver as portas utilizadas com mais frequência. Se clicar numa porta específica no gráfico circular, o restante do dashboard irá filtrar apenas fluxos dessa porta.

   ![3][3]

3. Número de fluxos e a primeira hora de Log – métricas que mostra o número de fluxos registadas e a data do registo mais antigo capturado.

   ![figura 4][4]

4. Fluxos por NSG e regra – um gráfico de barras que mostra a distribuição de fluxos dentro de cada NSG, bem como a distribuição de regras dentro de cada NSG. Aqui pode ver quais regras de NSG e gerar mais tráfego.

   ![figura 5][5]

5. Principais 10 origem/destino IPs – gráficos de barras que mostra a origem de 10 principais e de destino IPs. Pode ajustar estes gráficos para mostrar os IPs mais ou menos superior. Aqui pode ver os mais frequentemente ocorrência IPs, bem como a decisão de tráfego (permitir ou negar) que estão sendo feitas para cada IP.

   ![figura 6][6]

6. As Tuplas de fluxo – esta tabela mostra as informações contidas dentro de cada tupla de fluxo, bem como seu correspondente NGS e regra.

   ![7][7]

Com a barra de consulta na parte superior do dashboard, pode filtrar o dashboard com base em qualquer parâmetro de fluxos, como o ID de subscrição, grupos de recursos, regra ou qualquer outra variável de interesse. Para mais informações sobre consultas e filtros do Kibana, consulte o [documentação oficial](https://www.elastic.co/guide/en/beats/packetbeat/current/kibana-queries-filters.html)

## <a name="conclusion"></a>Conclusão

Ao combinar os registos de fluxo do grupo de segurança de rede com a pilha elástica, nós já percorremos cópia de segurança com uma forma poderosa e personalizável para visualizar o nosso tráfego de rede. Estes dashboards permitem-lhe obter rapidamente e partilhar informações sobre o tráfego de rede, bem como o filtro para baixo e investigar quaisquer anomalias potenciais. Utilização do Kibana, pode personalizar estes dashboards e criar visualizações específicas para satisfazer quaisquer necessidades de segurança, auditoria e conformidade.

## <a name="next-steps"></a>Passos Seguintes

Saiba como visualizar os seus registos de fluxo NSG com o Power BI, visite a página [registos com o Power BI de fluxos NSG visualizar](network-watcher-visualize-nsg-flow-logs-power-bi.md)

<!--Image references-->

[scenario]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/scenario.png
[1]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure1.png
[2]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure2.png
[3]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure3.png
[4]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure4.png
[5]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure5.png
[6]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure6.png
[7]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure7.png
