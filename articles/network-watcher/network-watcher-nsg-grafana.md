---
title: Gerir os registos de fluxo nsg usando Grafana
titleSuffix: Azure Network Watcher
description: Gerir e analisar os registos de fluxo do grupo de segurança da rede em Azure utilizando o Network Watcher e o Grafana.
services: network-watcher
documentationcenter: na
author: damendo
tags: azure-resource-manager
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/15/2017
ms.author: damendo
ms.openlocfilehash: c48d5a02cdb8ef63904642c6c2c76cb5d61e1f9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840915"
---
# <a name="manage-and-analyze-network-security-group-flow-logs-using-network-watcher-and-grafana"></a>Gerir e analisar registos de fluxo do Grupo de Segurança da Rede usando o Network Watcher e o Grafana

Os registos de fluxo do [Network Security Group (NSG)](network-watcher-nsg-flow-logging-overview.md) fornecem informações que podem ser usadas para compreender o tráfego IP nas interfaces da rede. Estes registos de fluxo mostram fluxos de saída e de entrada numa base de regra NSG, o NIC a que o fluxo se aplica a, 5-tuple informações sobre o fluxo (Source/Destination IP, Source/Destination Port, Protocol), e se o tráfego foi permitido ou negado.

> [!Warning]  
> Os passos seguintes funcionam com os registos de fluxo versão 1. Para mais detalhes, consulte [Introdução à exploração de fluxos de registo para grupos de segurança](network-watcher-nsg-flow-logging-overview.md)da rede . As seguintes instruções não funcionarão com a versão 2 dos ficheiros de registo, sem modificação.

Pode ter muitos NSGs na sua rede com o registo de fluxos ativado. Esta quantidade de dados de registo torna complicado analisar e obter informações dos seus registos. Este artigo fornece uma solução para gerir centralmente estes registos de fluxo NSG usando grafana, uma ferramenta de grafismo de código aberto, ElasticSearch, um motor de pesquisa e análise distribuído, e Logstash, que é um pipeline de processamento de dados do lado do servidor de fonte aberta.  

## <a name="scenario"></a>Cenário

Os registos de fluxo NSG são ativados utilizando o Network Watcher e são armazenados no armazenamento de blob Azure. Um plugin logstash é usado para ligar e processar registos de fluxo do armazenamento blob e enviá-los para ElasticSearch.  Uma vez armazenados os registos de fluxo em ElasticSearch, podem ser analisados e visualizados em dashboards personalizados em Grafana.

![NSG Network Watcher Grafana](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig1.png)

## <a name="installation-steps"></a>Passos de instalação

### <a name="enable-network-security-group-flow-logging"></a>Ativar a exploração de fluxo do Grupo de Segurança da Rede

Para este cenário, deve ter o Fluxo de Registo do Grupo de Segurança da Rede ativado em pelo menos um Grupo de Segurança de Rede na sua conta. Para obter instruções sobre a ativação de registos de fluxo de segurança da rede, consulte o seguinte artigo [Introdução ao fluxo de registo sinuoso para grupos](network-watcher-nsg-flow-logging-overview.md)de segurança da rede .

### <a name="setup-considerations"></a>Considerações sobre configuração

Neste exemplo Grafana, ElasticSearch e Logstash estão configurados num Ubuntu 16.04 LTS Server implantado em Azure. Esta configuração mínima é utilizada para executar os três componentes – todos eles estão a funcionar no mesmo VM. Esta configuração só deve ser utilizada para testes e cargas de trabalho não críticas. Logstash, Elasticsearch e Grafana podem ser arquitetados para escalar independentemente em muitos casos. Para mais informações, consulte a documentação para cada um destes componentes.

### <a name="install-logstash"></a>Instalar logstash

Utilize logstash para aplainar os troncos de fluxo formatadoJOn para um nível de fluxo de tuple.

1. Para instalar o Logstash, execute os seguintes comandos:

    ```bash
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```

2. Configure o Logstash para analisar os registos de fluxo e envie-os para o ElásticoSearch. Criar um ficheiro Logstash.conf utilizando:

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

O ficheiro config Logstash fornecido é composto por três partes: a entrada, filtro e saída.
A secção de entrada designa a fonte de entrada dos registos que o Logstash irá processar – neste caso, vamos utilizar um plugin de entrada "azureblob" (instalado nos próximos passos) que nos permitirá aceder aos ficheiros JSON de log de fluxo NSG armazenados no armazenamento de blob. 

A secção do filtro achata cada ficheiro de registo de fluxo para que cada estple de fluxo individual e as suas propriedades associadas se tornem um evento de Logstash separado.

Finalmente, a secção de saída reencaminha cada evento de Logstash para o servidor ElasticSearch. Sinta-se à vontade para modificar o ficheiro config Logstash de acordo com as suas necessidades específicas.

### <a name="install-the-logstash-input-plugin-for-azure-blob-storage"></a>Instale o plugin de entrada Logstash para armazenamento de Blob Azure

Este plugin logstash permite-lhe aceder diretamente aos registos de fluxo da sua conta de armazenamento de blob designada. Para instalar esta ficha, a partir do diretório de instalação de Logstash predefinido (neste caso/usr/share/logstash/bin) executa o comando:

```bash
cd /usr/share/logstash/bin
sudo ./logstash-plugin install logstash-input-azureblob
```

Para mais informações sobre esta ficha, consulte plugin de [entrada Logstash para Blobs de Armazenamento Azure](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob).

### <a name="install-elasticsearch"></a>Instalar ElásticoSearch

Pode utilizar o seguinte script para instalar o ElasticSearch. Para obter informações sobre a instalação de ElasticSearch, consulte [Elastic Stack](https://www.elastic.co/guide/en/elastic-stack/current/index.html).

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

Para instalar e executar grafana, executar os seguintes comandos:

```bash
wget https://s3-us-west-2.amazonaws.com/grafana-releases/release/grafana_4.5.1_amd64.deb
sudo apt-get install -y adduser libfontconfig
sudo dpkg -i grafana_4.5.1_amd64.deb
sudo service grafana-server start
```

Para obter informações adicionais sobre a instalação, consulte [a instalação em Debian / Ubuntu](https://docs.grafana.org/installation/debian/).

#### <a name="add-the-elasticsearch-server-as-a-data-source"></a>Adicione o servidor ElasticSearch como fonte de dados

Em seguida, é necessário adicionar o índice ElasticSearch contendo registos de fluxo como fonte de dados. Pode adicionar uma fonte de dados selecionando **adicionar fonte** de dados e preenchendo o formulário com as informações relevantes. Uma amostra desta configuração pode ser encontrada na seguinte imagem:

![Adicionar origem de dados](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig2.png)

#### <a name="create-a-dashboard"></a>Create a dashboard (Criar um dashboard)

Agora que configuraste grafana com sucesso para ler a partir do índice ElasticSearch contendo registos de fluxo NSG, pode criar e personalizar dashboards. Para criar um novo dashboard, selecione **Criar o seu primeiro dashboard**. A configuração do gráfico de amostra seguem mostrar fluxos segmentados pela regra NSG:

![Gráfico do painel de instrumentos](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig3.png)

A imagem que se segue mostra um gráfico e gráfico mostrando os fluxos superiores e a sua frequência. Os fluxos também são mostrados pela regra do NSG e fluxos por decisão. Grafana é altamente personalizável, por isso é aconselhável criar dashboards para atender às suas necessidades específicas de monitorização. O exemplo que se segue mostra um dashboard típico:

![Gráfico do painel de instrumentos](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig4.png)

## <a name="conclusion"></a>Conclusão

Ao integrar o Network Watcher com ElasticSearch e Grafana, tem agora uma forma conveniente e centralizada de gerir e visualizar registos de fluxo nsg, bem como outros dados. Grafana tem uma série de outras funcionalidades de grafismo poderosas que também podem ser usadas para gerir ainda mais os registos de fluxo e entender melhor o tráfego da sua rede. Agora que tem uma instância grafana configurada e ligada ao Azure, sinta-se livre para continuar a explorar a outra funcionalidade que oferece.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre a utilização do Observador de [Rede](network-watcher-monitoring-overview.md).

