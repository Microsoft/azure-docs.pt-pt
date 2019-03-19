---
title: Gerir segurança grupo fluxo registos de rede com o observador de rede e o Grafana | Documentos da Microsoft
description: Gerir e analisar a rede grupo fluxo registos de segurança no Azure com o observador de rede e o Grafana.
services: network-watcher
documentationcenter: na
author: mattreatMSFT
manager: vitinnan
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/15/2017
ms.author: mareat
ms.openlocfilehash: 73173c144f979d4a10b90a16aec783fe51a3f90e
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58000394"
---
# <a name="manage-and-analyze-network-security-group-flow-logs-using-network-watcher-and-grafana"></a>Gerir e analisar registos de fluxo do grupo de segurança de rede com o observador de rede e o Grafana

[Os registos de fluxo do grupo de segurança (NSG) de rede](network-watcher-nsg-flow-logging-overview.md) fornecem informações que podem ser utilizadas para compreender o tráfego IP de entrada e saída em interfaces de rede. Estes registos de fluxo mostram fluxos de saída e entrados num por base de regra NSG, o NIC o fluxo se aplica, informações de 5 cadeias de identificação sobre o fluxo (IP de origem/destino, porta de origem/destino, protocolo), e se o tráfego foi permitido ou negado.

> [!Warning]  
> Os seguintes passos funcionam com a versão de registos de fluxo 1. Para obter detalhes, consulte [introdução ao registo do fluxo para grupos de segurança de rede](network-watcher-nsg-flow-logging-overview.md). As instruções seguintes não irão funcionar com a versão 2 dos ficheiros de registo, sem modificações.

Pode ter muitos NSGs na sua rede com o registo de fluxo ativado. Esta quantidade de dados de registo torna complicado para analisar e obter informações a partir dos seus registos. Este artigo fornece uma solução para gerir centralmente estes registos de fluxo NSG com o Grafana, um gráfico de ferramenta, o ElasticSearch, um pesquisa distribuída e o motor de análise e o Logstash, que é um pipeline de processamento de dados do lado do servidor do código-fonte aberto de código aberto.  

## <a name="scenario"></a>Cenário

Os registos de fluxo NSG estão ativados com o observador de rede e são armazenados no armazenamento de Blobs do Azure. Um plug-in do Logstash é utilizado para ligar e processar registos de fluxo do armazenamento de BLOBs e enviá-los para o ElasticSearch.  Depois dos registos de fluxo são armazenados no ElasticSearch, podem ser analisados e visualizados em dashboards personalizados no Grafana.

![Grafana de observador de rede do NSG](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig1.png)

## <a name="installation-steps"></a>Passos de instalação

### <a name="enable-network-security-group-flow-logging"></a>Registo do fluxo do grupo de segurança de rede ativar

Para este cenário, tem de ter a rede segurança grupo fluxo o registo ativado em, pelo menos, um grupo de segurança de rede na sua conta. Para obter instruções sobre como ativar os registos de fluxo de segurança de rede, consulte o seguinte artigo [introdução ao registo do fluxo para grupos de segurança de rede](network-watcher-nsg-flow-logging-overview.md).

### <a name="setup-considerations"></a>Considerações de configuração

Neste exemplo o Grafana, ElasticSearch e Logstash são configuradas num Ubuntu 16.04 LTS Server implementado no Azure. Esta configuração mínima é utilizada para a execução de todos os três componentes – eles estão todos em execução na mesma VM. Esta configuração só deve ser utilizada para cargas de trabalho não críticas e de testes. Logstash, Elasticsearch e o Grafana podem todos ser arquitetado para dimensionar de forma independente em muitas instâncias. Para obter mais informações, consulte a documentação para cada um desses componentes.

### <a name="install-logstash"></a>Instalar o Logstash

Utilize o Logstash aplanar os registos de fluxo de formatados em JSON para um nível de cadeias de identificação de fluxo.

1. Para instalar o Logstash, execute os seguintes comandos:

    ```bash
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```

2. Configure o Logstash para analisar os registos de fluxo e enviá-los para o ElasticSearch. Crie um ficheiro de Logstash.conf com:

    ```bash
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

3. Adicione o seguinte conteúdo para o ficheiro. Altere a chave de acesso e o nome conta de armazenamento para refletir os detalhes da conta de armazenamento:

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

O ficheiro de configuração do Logstash fornecido é composto por três partes: a entrada, o filtro e a saída.
A secção de entrada designa a origem de entrada dos registos que irá processar o Logstash – nesse caso, vamos utilizar um "azureblob" entrada Plug-in (instalado nos passos seguintes) que irá permitir-nos acessar os arquivos de log fluxo do NSG JSON armazenados no armazenamento de Blobs. 

A secção de filtro nivela, em seguida, cada ficheiro de registo de fluxo para que cada tupla de fluxo individuais e as respetivas propriedades associadas torna-se um evento separado do Logstash.

Por fim, a secção de saída encaminha cada evento do Logstash para o servidor de ElasticSearch. Pode modificar o ficheiro de configuração do Logstash para se adequar às suas necessidades específicas.

### <a name="install-the-logstash-input-plugin-for-azure-blob-storage"></a>Instalar o Logstash entrado Plug-in para o armazenamento de Blobs do Azure

Este plug-in do Logstash permite que acessar diretamente os registos de fluxo a partir da respetiva conta de armazenamento de blob designado. Para instalar este plug-in, a partir do diretório de instalação de Logstash padrão (em /usr/share/logstash/bin neste caso) execute o comando:

```bash
cd /usr/share/logstash/bin
sudo ./logstash-plugin install logstash-input-azureblob
```

Para obter mais informações sobre este plug-in, consulte [Logstash Plug-in entrada para o Azure Storage Blobs](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob).

### <a name="install-elasticsearch"></a>Instale o ElasticSearch

Pode utilizar o seguinte script para instalar o ElasticSearch. Para obter informações sobre como instalar o ElasticSearch, consulte [Elastic Stack](https://www.elastic.co/guide/en/elastic-stack/current/index.html).

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

### <a name="install-grafana"></a>Instalar o Grafana

Para instalar e executar o Grafana, execute os seguintes comandos:

```bash
wget https://s3-us-west-2.amazonaws.com/grafana-releases/release/grafana_4.5.1_amd64.deb
sudo apt-get install -y adduser libfontconfig
sudo dpkg -i grafana_4.5.1_amd64.deb
sudo service grafana-server start
```

Para informações de instalação adicionais, consulte [instalar no Debian / Ubuntu](https://docs.grafana.org/installation/debian/).

#### <a name="add-the-elasticsearch-server-as-a-data-source"></a>Adicione o servidor do ElasticSearch como uma origem de dados

Em seguida, terá de adicionar o índice de ElasticSearch que contém os registos de fluxo como uma origem de dados. Pode adicionar uma origem de dados, selecionando **Adicionar origem de dados** e concluir o formulário com as informações relevantes. Um exemplo desta configuração pode ser encontrado na seguinte captura de ecrã:

![Adicionar origem de dados](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig2.png)

#### <a name="create-a-dashboard"></a>Criar um dashboard

Agora que configurou com êxito o Grafana para ler a partir do índice de ElasticSearch que contém os registos de fluxo do NSG, pode criar e personalizar a dashboards. Para criar um novo dashboard, selecione **criar seu primeiro dashboard**. A configuração de gráfico de exemplo seguinte mostra os fluxos segmentados por regra NSG:

![Gráfico de dashboard](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig3.png)

Captura de ecrã seguinte mostra um gráfico e o gráfico que mostra os principais fluxos e a frequência das mesmas. Fluxos também são mostrados por regra NSG e fluxos por de decisão. O Grafana é altamente personalizável, portanto, é recomendado que crie dashboards para se adequar às suas necessidades de monitorização específicas. O exemplo seguinte mostra um dashboard típico:

![Gráfico de dashboard](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig4.png)

## <a name="conclusion"></a>Conclusão

Ao integrar o observador de rede com o ElasticSearch e o Grafana, agora tem uma forma conveniente e centralizada para gerir e visualizar registos de fluxo NSG, bem como outros dados. O Grafana tem uma série de outros recursos de gráficas poderosos que também podem ser utilizadas para gerir ainda mais os registos de fluxo e compreender melhor o seu tráfego de rede. Agora que tem uma instância da Grafana configurado e ligado ao Azure, pode-se de que continua a explorar as outras funcionalidades que oferece.

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre como utilizar [observador de rede](network-watcher-monitoring-overview.md).

