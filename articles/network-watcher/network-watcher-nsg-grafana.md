---
title: Gerir registos de fluxo NSG usando Grafana
titleSuffix: Azure Network Watcher
description: Gerir e analisar os Registos de Fluxo do Grupo de Segurança de Rede em Azure utilizando o Network Watcher e o Grafana.
services: network-watcher
documentationcenter: na
author: damendo
tags: azure-resource-manager
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/15/2017
ms.author: damendo
ms.openlocfilehash: d522d305c70214009b8aa2886d07d2d5403dd2b1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97656313"
---
# <a name="manage-and-analyze-network-security-group-flow-logs-using-network-watcher-and-grafana"></a>Gerir e analisar os registos de fluxo do Grupo de Segurança de Rede utilizando o Network Watcher e o Grafana

[Os registos de fluxo do Grupo de Segurança da Rede (NSG)](network-watcher-nsg-flow-logging-overview.md) fornecem informações que podem ser usadas para compreender o tráfego IP de entrada e saída nas interfaces de rede. Estes registos de fluxo mostram fluxos de saída e de entrada numa base de regra de NSG, o NIC o fluxo aplica-se a, 5-tuple informações sobre o fluxo (Source/Destination IP, Source/Destination Port, Protocol), e se o tráfego foi permitido ou negado.

Pode ter muitos NSGs na sua rede com registo de fluxo ativado. Esta quantidade de dados de registo torna difícil analisar e obter informações dos seus registos. Este artigo fornece uma solução para gerir centralmente estes registos de fluxo NSG usando Grafana, uma ferramenta de gráfico de código aberto, ElasticSearch, um motor de pesquisa e análise distribuído, e Logstash, que é um pipeline de processamento de dados do lado do servidor de código aberto.  

## <a name="scenario"></a>Scenario

Os registos de fluxo NSG são ativados utilizando o Network Watcher e são armazenados no armazenamento de bolhas Azure. Um plugin logstash é utilizado para ligar e processar registos de fluxo a partir do armazenamento de bolhas e enviá-los para o ElasticSearch.  Uma vez que os registos de fluxo são armazenados em ElasticSearch, eles podem ser analisados e visualizados em dashboards personalizados em Grafana.

![NSG Network Watcher Grafana](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig1.png)

## <a name="installation-steps"></a>Passos de instalação

### <a name="enable-network-security-group-flow-logging"></a>Ativar a registo de fluxo do Grupo de Segurança de Rede

Para este cenário, deve ter o Fluxo de Fluxo de Rede ativado em pelo menos um Grupo de Segurança de Rede na sua conta. Para obter instruções sobre a ativação dos registos de fluxo de segurança da rede, consulte o seguinte artigo [Introdução à registo de fluxo para grupos de segurança da rede](network-watcher-nsg-flow-logging-overview.md).

### <a name="setup-considerations"></a>Considerações sobre configuração

Neste exemplo, Grafana, ElasticSearch e Logstash estão configurados num Servidor LTS Ubuntu 16.04 implantado em Azure. Esta configuração mínima é usada para executar os três componentes – todos eles estão a funcionar no mesmo VM. Esta configuração só deve ser utilizada para testes e cargas de trabalho não críticas. Logstash, Elasticsearch e Grafana podem ser arquitetos para escalar independentemente em muitos casos. Para mais informações, consulte a documentação de cada um destes componentes.

### <a name="install-logstash"></a>Instalar Logstash

Utilize o Logstash para achatar os registos de fluxo formatados JSON para um nível de tuple de fluxo.

1. Para instalar o Logstash, execute os seguintes comandos:

    ```bash
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```

2. Configure o Logstash para analisar os registos de fluxo e enviá-los para o ElasticSearch. Criar um ficheiro Logstash.conf utilizando:

    ```bash
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

3. Adicione o seguinte conteúdo ao ficheiro. Altere o nome da conta de armazenamento e a chave de acesso para refletir os detalhes da sua conta de armazenamento:

   ```bash
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

      mutate {
        split => { "[records][resourceId]" => "/"}
        add_field => { "Subscription" => "%{[records][resourceId][2]}"
          "ResourceGroup" => "%{[records][resourceId][4]}"
          "NetworkSecurityGroup" => "%{[records][resourceId][8]}" 
        }
        convert => {"Subscription" => "string"}
        convert => {"ResourceGroup" => "string"}
        convert => {"NetworkSecurityGroup" => "string"}
        split => { "[records][properties][flows][flows][flowTuples]" => "," }
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
        add_field => {
          "time" => "%{[records][time]}"
          "systemId" => "%{[records][systemId]}"
          "category" => "%{[records][category]}"
          "resourceId" => "%{[records][resourceId]}"
          "operationName" => "%{[records][operationName}}"
          "Version" => "%{[records][properties][Version}}"
          "rule" => "%{[records][properties][flows][rule]}"
          "mac" => "%{[records][properties][flows][flows][mac]}"
        }
        convert => {"unixtimestamp" => "integer"}
        convert => {"srcPort" => "integer"}
        convert => {"destPort" => "integer"}
        add_field => { "message" => "%{Message}" }        
      }
 
      date {
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

O ficheiro config logstash fornecido é composto por três partes: a entrada, o filtro e a saída.
A secção de entrada designa a fonte de entrada dos registos que o Logstash irá processar – neste caso, vamos utilizar um plugin de entrada "azureblob" (instalado nos próximos passos) que nos permitirá aceder aos ficheiros JSON do registo de fluxo NSG armazenados no armazenamento de bolhas. 

Em seguida, a secção do filtro achata cada ficheiro de registo de fluxo para que cada tuple de fluxo individual e as suas propriedades associadas se tornem um evento separado de Logstash.

Finalmente, a secção de saída encaminha cada evento de Logstash para o servidor ElasticSearch. Sinta-se livre para modificar o ficheiroconfig logstash para atender às suas necessidades específicas.

### <a name="install-the-logstash-input-plugin-for-azure-blob-storage"></a>Instale o plugin de entrada Logstash para armazenamento Azure Blob

Este plugin Logstash permite-lhe aceder diretamente aos registos de fluxo a partir da sua conta de armazenamento de bolhas designada. Para instalar esta ficha, a partir do diretório de instalação de Logstash predefinido (neste caso /usr/share/logstash/bin) executar o comando:

```bash
cd /usr/share/logstash/bin
sudo ./logstash-plugin install logstash-input-azureblob
```

Para obter mais informações sobre esta ficha, consulte [o plugin de entrada logstash para Azure Storage Blobs](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob).

### <a name="install-elasticsearch"></a>Instalar ElasticSearch

Pode utilizar o seguinte script para instalar o ElasticSearch. Para obter informações sobre a instalação do ElasticSearch, consulte [a Pilha Elástica.](https://www.elastic.co/guide/en/elastic-stack/current/index.html)

```bash
apt-get install apt-transport-https openjdk-8-jre-headless uuid-runtime pwgen -y
wget -qO - https://packages.elastic.co/GPG-KEY-elasticsearch | apt-key add -
echo "deb https://packages.elastic.co/elasticsearch/5.x/debian stable main" | tee -a /etc/apt/sources.list.d/elasticsearch-5.x.list
apt-get update && apt-get install elasticsearch
sed -i s/#cluster.name:.*/cluster.name:\ grafana/ /etc/elasticsearch/elasticsearch.yml
systemctl daemon-reload
systemctl enable elasticsearch.service
systemctl start elasticsearch.service
```

### <a name="install-grafana"></a>Instalar Grafana

Para instalar e executar Grafana, executar os seguintes comandos:

```bash
wget https://s3-us-west-2.amazonaws.com/grafana-releases/release/grafana_4.5.1_amd64.deb
sudo apt-get install -y adduser libfontconfig
sudo dpkg -i grafana_4.5.1_amd64.deb
sudo service grafana-server start
```

Para obter informações adicionais sobre a instalação [em Debian/ Ubuntu](https://docs.grafana.org/installation/debian/).

#### <a name="add-the-elasticsearch-server-as-a-data-source"></a>Adicione o servidor ElasticSearch como fonte de dados

Em seguida, é necessário adicionar o índice ElasticSearch contendo registos de fluxo como fonte de dados. Pode adicionar uma fonte de dados selecionando Adicionar fonte de **dados** e completar o formulário com as informações relevantes. Uma amostra desta configuração pode ser encontrada na seguinte imagem:

![Adicionar origem de dados](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig2.png)

#### <a name="create-a-dashboard"></a>Criar um dashboard

Agora que configuraste com sucesso a Grafana para ler a partir do índice ElasticSearch que contém registos de fluxo NSG, podes criar e personalizar dashboards. Para criar um novo painel de instrumentos, selecione **Criar o seu primeiro dashboard**. A seguinte configuração do gráfico de amostra mostra fluxos segmentados pela regra NSG:

![Gráfico de tablier](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig3.png)

A imagem que se segue mostra um gráfico e gráfico que mostra os fluxos superiores e a sua frequência. Os fluxos também são mostrados pela regra e fluxos NSG por decisão. Grafana é altamente personalizável, por isso é aconselhável que crie dashboards de acordo com as suas necessidades específicas de monitorização. O exemplo a seguir mostra um painel típico:

![Screenshot que mostra a configuração do gráfico de amostra com fluxos segmentados pela regra NSG.](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig4.png)

## <a name="conclusion"></a>Conclusão

Ao integrar o Network Watcher com ElasticSearch e Grafana, tem agora uma forma conveniente e centralizada de gerir e visualizar registos de fluxo NSG, bem como outros dados. Grafana tem uma série de outras funcionalidades de gráficos poderosos que também podem ser usadas para gerir ainda mais os registos de fluxo e entender melhor o tráfego da sua rede. Agora que tem um caso Grafana configurado e ligado ao Azure, sinta-se livre para continuar a explorar a outra funcionalidade que oferece.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre a utilização [do Network Watcher.](network-watcher-monitoring-overview.md)

