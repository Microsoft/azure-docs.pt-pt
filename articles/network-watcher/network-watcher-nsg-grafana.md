---
title: Gerenciar logs de fluxo do NSG usando o Grafana
titleSuffix: Azure Network Watcher
description: Gerencie e analise os logs de fluxo do grupo de segurança de rede no Azure usando o observador de rede e o Grafana.
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
ms.openlocfilehash: e92f4db575e4b318af8dc7bf3ba2eb4b69a3a2d9
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277948"
---
# <a name="manage-and-analyze-network-security-group-flow-logs-using-network-watcher-and-grafana"></a>Gerenciar e analisar logs de fluxo do grupo de segurança de rede usando o observador de rede e o Grafana

[Os logs de fluxo do NSG (grupo de segurança de rede)](network-watcher-nsg-flow-logging-overview.md) fornecem informações que podem ser usadas para entender o tráfego IP de entrada e saída em interfaces de rede. Esses logs de fluxo mostram os fluxos de entrada e saída em uma base de regra por NSG, a NIC à qual o fluxo se aplica, informações de 5 tuplas sobre o fluxo (IP de origem/destino, porta de origem/destino, protocolo) e se o tráfego foi permitido ou negado.

> [!Warning]  
> As etapas a seguir funcionam com os logs de fluxo versão 1. Para obter detalhes, consulte [introdução ao log de fluxo para grupos de segurança de rede](network-watcher-nsg-flow-logging-overview.md). As instruções a seguir não funcionarão com a versão 2 dos arquivos de log, sem modificação.

Você pode ter muitos NSGs em sua rede com o log de fluxo habilitado. Essa quantidade de dados de registro em log torna difícil analisar e obter informações de seus logs. Este artigo fornece uma solução para gerenciar centralmente esses logs de fluxo do NSG usando o Grafana, uma ferramenta de gráfico de software livre, o ElasticSearch, um mecanismo de pesquisa e análise distribuída e Logstash, que é um pipeline de processamento de dados do lado do servidor de software livre.  

## <a name="scenario"></a>Cenário

Os logs de fluxo NSG são habilitados usando o observador de rede e são armazenados no armazenamento de BLOBs do Azure. Um plug-in Logstash é usado para conectar e processar logs de fluxo do armazenamento de BLOBs e enviá-los ao ElasticSearch.  Depois que os logs de fluxo são armazenados em ElasticSearch, eles podem ser analisados e visualizados em painéis personalizados no Grafana.

![Grafana do observador de rede NSG](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig1.png)

## <a name="installation-steps"></a>Etapas de instalação

### <a name="enable-network-security-group-flow-logging"></a>Habilitar o log de fluxo do grupo de segurança de rede

Para este cenário, você deve ter o log de fluxo do grupo de segurança de rede habilitado em pelo menos um grupo de segurança de rede em sua conta. Para obter instruções sobre como habilitar os logs de fluxo de segurança de rede, consulte o seguinte artigo [introdução ao log de fluxo para grupos de segurança de rede](network-watcher-nsg-flow-logging-overview.md).

### <a name="setup-considerations"></a>Considerações sobre a instalação

Neste exemplo, Grafana, ElasticSearch e Logstash são configurados em um servidor Ubuntu 16, 4 LTS implantado no Azure. Essa configuração mínima é usada para executar todos os três componentes – todos eles estão em execução na mesma VM. Essa configuração só deve ser usada para cargas de trabalho de teste e não críticas. Logstash, Elasticsearch e Grafana podem ser arquitetados para dimensionar de forma independente em várias instâncias. Para obter mais informações, consulte a documentação para cada um desses componentes.

### <a name="install-logstash"></a>Instalar o Logstash

Use Logstash para mesclar os logs de fluxo formatados em JSON para um nível de tupla de fluxo.

1. Para instalar o Logstash, execute os seguintes comandos:

    ```bash
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```

2. Configure o Logstash para analisar os logs de fluxo e enviá-los para o ElasticSearch. Crie um arquivo Logstash. conf usando:

    ```bash
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

3. Adicione o seguinte conteúdo ao arquivo. Altere o nome da conta de armazenamento e a chave de acesso para refletir os detalhes da sua conta de armazenamento:

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

O arquivo de configuração Logstash fornecido é composto por três partes: a entrada, o filtro e a saída.
A seção de entrada designa a fonte de entrada dos logs que o Logstash processará – nesse caso, usaremos um plug-in de entrada "azureblob" (instalado nas próximas etapas) que nos permitirá acessar os arquivos JSON do log de fluxo NSG armazenados no armazenamento de BLOBs. 

Em seguida, a seção de filtro mescla cada arquivo de log de fluxo para que cada tupla de fluxo individual e suas propriedades associadas se tornem um evento Logstash separado.

Por fim, a seção de saída encaminha cada evento Logstash para o servidor ElasticSearch. Fique à vontade para modificar o arquivo de configuração Logstash de acordo com suas necessidades específicas.

### <a name="install-the-logstash-input-plugin-for-azure-blob-storage"></a>Instalar o plug-in de entrada Logstash para o armazenamento de BLOBs do Azure

Esse plug-in Logstash permite que você acesse diretamente os logs de fluxo de sua conta de armazenamento de BLOBs designada. Para instalar esse plug-in, no diretório de instalação padrão do Logstash (neste caso,,/usr/share/logstash/bin), execute o comando:

```bash
cd /usr/share/logstash/bin
sudo ./logstash-plugin install logstash-input-azureblob
```

Para obter mais informações sobre esse plug-in, consulte [plug-in de entrada do Logstash para BLOBs de armazenamento do Azure](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob).

### <a name="install-elasticsearch"></a>Instalar o ElasticSearch

Você pode usar o script a seguir para instalar o ElasticSearch. Para obter informações sobre como instalar o ElasticSearch, consulte [pilha elástica](https://www.elastic.co/guide/en/elastic-stack/current/index.html).

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

Para obter informações adicionais de instalação, consulte [instalando no Debian/Ubuntu](https://docs.grafana.org/installation/debian/).

#### <a name="add-the-elasticsearch-server-as-a-data-source"></a>Adicionar o servidor ElasticSearch como uma fonte de dados

Em seguida, você precisa adicionar o índice ElasticSearch que contém os logs de fluxo como uma fonte de dados. Você pode adicionar uma fonte de dados selecionando **Adicionar fonte de dados** e concluindo o formulário com as informações relevantes. Uma amostra dessa configuração pode ser encontrada na seguinte captura de tela:

![Adicionar origem de dados](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig2.png)

#### <a name="create-a-dashboard"></a>Criar um painel

Agora que você configurou com êxito o Grafana para ler do índice ElasticSearch que contém os logs de fluxo do NSG, você pode criar e personalizar painéis. Para criar um novo painel, selecione **criar seu primeiro painel**. A configuração de gráfico de exemplo a seguir mostra os fluxos segmentados pela regra NSG:

![Grafo de painel](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig3.png)

A captura de tela a seguir descreve um grafo e um gráfico mostrando os principais fluxos e sua frequência. Os fluxos também são mostrados pela regra e pelos fluxos do NSG por decisão. O Grafana é altamente personalizável, portanto, é aconselhável que você crie painéis para atender às suas necessidades específicas de monitoramento. O exemplo a seguir mostra um painel típico:

![Grafo de painel](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig4.png)

## <a name="conclusion"></a>Conclusão

Ao integrar o observador de rede com o ElasticSearch e o Grafana, agora você tem uma maneira centralizada e conveniente de gerenciar e visualizar os logs de fluxo de NSG, bem como outros dados. O Grafana tem vários outros recursos avançados de grafação que também podem ser usados para gerenciar ainda mais os logs de fluxo e entender melhor o tráfego de rede. Agora que você tem uma instância Grafana configurada e conectada ao Azure, sinta-se à vontade para continuar a explorar as outras funcionalidades que ele oferece.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre como usar o [observador de rede](network-watcher-monitoring-overview.md).

