---
title: Realizar a deteção de intrusão de rede com ferramentas de código aberto
titleSuffix: Azure Network Watcher
description: Este artigo descreve como usar o Azure Network Watcher e ferramentas de código aberto para realizar a deteção de intrusões de rede
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: 0f043f08-19e1-4125-98b0-3e335ba69681
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 781f3788c9001276315a2baed7060450fa00d77a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76845028"
---
# <a name="perform-network-intrusion-detection-with-network-watcher-and-open-source-tools"></a>Realizar a deteção de intrusão de rede com o Observador de Rede e ferramentas de código aberto

As capturas de pacotes são um componente chave para implementar sistemas de deteção de intrusões de rede (IDS) e realizar monitorização de segurança de rede (NSM). Existem várias ferramentas IDS de código aberto que processam capturas de pacotes e procuram assinaturas de possíveis intrusões de rede e atividade maliciosa. Utilizando as capturas de pacotes fornecidas pelo Network Watcher, pode analisar a sua rede para eventuais intrusões ou vulnerabilidades nocivas.

Uma dessas ferramentas de código aberto é a Suricata, um motor IDS que usa conjuntos de regras para monitorizar o tráfego da rede e dispara alertas sempre que ocorrerem eventos suspeitos. A Suricata oferece um motor multi-roscado, o que significa que pode realizar análises de tráfego de rede com maior rapidez e eficiência. Para mais detalhes sobre a Suricata e https://suricata-ids.org/as suas capacidades, visite o seu website em .

## <a name="scenario"></a>Cenário

Este artigo explica como configurar o seu ambiente para realizar a deteção de intrusões de rede usando o Network Watcher, Suricata e a Stack Elástica. O Network Watcher fornece-lhe as capturas de pacotes utilizadas para realizar a deteção de intrusões de rede. A Suricata processa as capturas do pacote e desencadeia alertas baseados em pacotes que correspondem ao seu conjunto de ameaças. Estes alertas são armazenados num ficheiro de registo na sua máquina local. Utilizando a Stack Elástica, os registos gerados pela Suricata podem ser indexados e utilizados para criar um dashboard Kibana, proporcionando-lhe uma representação visual dos registos e um meio para obter rapidamente insights sobre potenciais vulnerabilidades da rede.  

![cenário simples de aplicação web][1]

Ambas as ferramentas de código aberto podem ser configuradas num VM Azure, permitindo-lhe realizar esta análise dentro do seu próprio ambiente de rede Azure.

## <a name="steps"></a>Passos

### <a name="install-suricata"></a>Instalar Suricata

Para todos os outros métodos de instalação, visitehttps://suricata.readthedocs.io/en/latest/install.html

1. No terminal de linha de comando do seu VM executar os seguintes comandos:

    ```
    sudo add-apt-repository ppa:oisf/suricata-stable
    sudo apt-get update
    sudo sudo apt-get install suricata
    ```

1. Para verificar a sua `suricata -h` instalação, execute o comando para ver a lista completa de comandos.

### <a name="download-the-emerging-threats-ruleset"></a>Descarregue as regras das Ameaças Emergentes

Nesta fase, não temos regras para a Suricata concorrer. Pode criar as suas próprias regras se houver ameaças específicas à sua rede que gostaria de detetar, ou também pode utilizar conjuntos de regras desenvolvidos de vários fornecedores, tais como Ameaças Emergentes ou regras vrt da Snort. Utilizamos aqui as regras de ameaças emergentes livremente acessíveis:

Descarregue o conjunto de regras e copie-os para o diretório:

```
wget https://rules.emergingthreats.net/open/suricata/emerging.rules.tar.gz
tar zxf emerging.rules.tar.gz
sudo cp -r rules /etc/suricata/
```

### <a name="process-packet-captures-with-suricata"></a>Capturas de pacotes de processo com Suricata

Para processar capturas de pacotes usando Suricata, executar o seguinte comando:

```
sudo suricata -c /etc/suricata/suricata.yaml -r <location_of_pcapfile>
```
Para verificar os alertas resultantes, leia o ficheiro fast.log:
```
tail -f /var/log/suricata/fast.log
```

### <a name="set-up-the-elastic-stack"></a>Configurar a pilha elástica

Enquanto os registos que a Suricata produz contêm informações valiosas sobre o que está a acontecer na nossa rede, estes ficheiros de registo não são os mais fáceis de ler e entender. Ao ligar a Suricata à Stack Elástica, podemos criar um dashboard Kibana que nos permite pesquisar, graphar, analisar e obter insights dos nossos registos.

#### <a name="install-elasticsearch"></a>Instalar pesquisa elástica

1. A Stack Elástica da versão 5.0 e acima requer Java 8. Execute `java -version` o comando para verificar a sua versão. Se não tiver java instalado, consulte a documentação sobre os [JDKs suppored Azure](https://aka.ms/azure-jdks).

1. Descarregue o pacote binário correto para o seu sistema:

    ```
    curl -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.2.0.deb
    sudo dpkg -i elasticsearch-5.2.0.deb
    sudo /etc/init.d/elasticsearch start
    ```

    Outros métodos de instalação podem ser encontrados na [Instalação Elasticsearch](https://www.elastic.co/guide/en/beats/libbeat/5.2/elasticsearch-installation.html)

1. Verifique se a Elasticsearch está a decorrer com o comando:

    ```
    curl http://127.0.0.1:9200
    ```

    Devia ver uma resposta semelhante a esta:

    ```
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

Para mais instruções sobre a instalação da pesquisa elástica, consulte a página [Instalação](https://www.elastic.co/guide/en/elasticsearch/reference/5.2/_installation.html)

### <a name="install-logstash"></a>Instalar logstash

1. Para instalar o Logstash executar os seguintes comandos:

    ```
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```
1. Em seguida, precisamos configurar logstash para ler a partir da saída do ficheiro eve.json. Criar um ficheiro logstash.conf utilizando:

    ```
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

1. Adicione o seguinte conteúdo ao ficheiro (certifique-se de que o caminho para o ficheiro eve.json está correto):

    ```ruby
    input {
    file {
        path => ["/var/log/suricata/eve.json"]
        codec =>  "json"
        type => "SuricataIDPS"
    }

    }

    filter {
    if [type] == "SuricataIDPS" {
        date {
        match => [ "timestamp", "ISO8601" ]
        }
        ruby {
        code => "
            if event.get('[event_type]') == 'fileinfo'
            event.set('[fileinfo][type]', event.get('[fileinfo][magic]').to_s.split(',')[0])
            end
        "
        }

        ruby{
        code => "
            if event.get('[event_type]') == 'alert'
            sp = event.get('[alert][signature]').to_s.split(' group ')
            if (sp.length == 2) and /\A\d+\z/.match(sp[1])
                event.set('[alert][signature]', sp[0])
            end
            end
            "
        }
    }

    if [src_ip]  {
        geoip {
        source => "src_ip"
        target => "geoip"
        #database => "/opt/logstash/vendor/geoip/GeoLiteCity.dat"
        add_field => [ "[geoip][coordinates]", "%{[geoip][longitude]}" ]
        add_field => [ "[geoip][coordinates]", "%{[geoip][latitude]}"  ]
        }
        mutate {
        convert => [ "[geoip][coordinates]", "float" ]
        }
        if ![geoip.ip] {
        if [dest_ip]  {
            geoip {
            source => "dest_ip"
            target => "geoip"
            #database => "/opt/logstash/vendor/geoip/GeoLiteCity.dat"
            add_field => [ "[geoip][coordinates]", "%{[geoip][longitude]}" ]
            add_field => [ "[geoip][coordinates]", "%{[geoip][latitude]}"  ]
            }
            mutate {
            convert => [ "[geoip][coordinates]", "float" ]
            }
        }
        }
    }
    }

    output {
    elasticsearch {
        hosts => "localhost"
    }
    }
    ```

1. Certifique-se de que dá as permissões corretas ao ficheiro eve.json para que o Logstash possa ingerir o ficheiro.
    
    ```
    sudo chmod 775 /var/log/suricata/eve.json
    ```

1. Para iniciar o Logstash executar o comando:

    ```
    sudo /etc/init.d/logstash start
    ```

Para mais instruções sobre a instalação de Logstash, consulte a [documentação oficial](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html)

### <a name="install-kibana"></a>Instalar Kibana

1. Executar os seguintes comandos para instalar kibana:

    ```
    curl -L -O https://artifacts.elastic.co/downloads/kibana/kibana-5.2.0-linux-x86_64.tar.gz
    tar xzvf kibana-5.2.0-linux-x86_64.tar.gz

    ```
1. Para executar Kibana, use os comandos:

    ```
    cd kibana-5.2.0-linux-x86_64/
    ./bin/kibana
    ```

1. Para ver a sua interface web Kibana, navegue para`http://localhost:5601`
1. Para este cenário, o padrão de índice utilizado para os troncos suricata é "logstash-*"

1. Se quiser ver o dashboard kibana remotamente, crie uma regra NSG de entrada que permita o acesso à **porta 5601**.

### <a name="create-a-kibana-dashboard"></a>Crie um dashboard Kibana

Para este artigo, fornecemos um painel de amostras para que possa ver tendências e detalhes nos seus alertas.

1. Descarregue o ficheiro do dashboard [aqui,](https://aka.ms/networkwatchersuricatadashboard)o ficheiro de visualização [aqui,](https://aka.ms/networkwatchersuricatavisualization)e o ficheiro de pesquisa guardado [aqui](https://aka.ms/networkwatchersuricatasavedsearch).

1. Sob o separador **Gestão** de Kibana, navegue para **Saved Objects** e importe os três ficheiros. Em seguida, a partir do **separador Dashboard** pode abrir e carregar o painel de dados da amostra.

Também pode criar as suas próprias visualizações e dashboards adaptados para métricas do seu próprio interesse. Leia mais sobre a criação de visualizações kibana a partir da [documentação oficial](https://www.elastic.co/guide/en/kibana/current/visualize.html)de Kibana.

![tablier kibana][2]

### <a name="visualize-ids-alert-logs"></a>Visualizar registos de alerta IDS

O painel de amostras fornece várias visualizações dos registos de alerta Suricata:

1. Alertas por GeoIP – mapa que mostra a distribuição de alertas pelo seu país/região de origem com base na localização geográfica (determinada por IP)

    ![geo ip][3]

1. Top 10 Alertas – um resumo dos 10 alertas mais frequentes desencadeados e a sua descrição. Clicar num alerta individual filtra o painel de instrumentos para obter as informações relativas a esse alerta específico.

    ![imagem 4][4]

1. Número de Alertas – a contagem total de alertas desencadeados pelo conjunto de regras

    ![imagem 5][5]

1. Top 20 Source/Destination IPs/Ports - gráficos de tartes mostrando os 20 melhores IPs e portas em que os alertas foram desencadeados. Pode filtrar em IPs/portas específicas para ver quantos e que tipo de alertas estão a ser desencadeados.

    ![imagem 6][6]

1. Resumo do alerta – uma tabela que resume detalhes específicos de cada alerta individual. Pode personalizar esta tabela para mostrar outros parâmetros de interesse para cada alerta.

    ![imagem 7][7]

Para obter mais documentação sobre a criação de visualizações personalizadas e dashboards, consulte a [documentação oficial de Kibana.](https://www.elastic.co/guide/en/kibana/current/introduction.html)

## <a name="conclusion"></a>Conclusão

Ao combinar capturas de pacotes fornecidas pelo Network Watcher e ferramentas IDS de código aberto, como a Suricata, pode realizar a deteção de intrusões de rede para uma ampla gama de ameaças. Estes dashboards permitem-lhe detetar rapidamente tendências e anomalias dentro da sua rede, bem como investigar os dados para descobrir causas fundamentais de alertas como agentes de utilizador maliciosos ou portas vulneráveis. Com estes dados extraídos, pode tomar decisões informadas sobre como reagir e proteger a sua rede de quaisquer tentativas de intrusão prejudiciais e criar regras para evitar futuras intrusões na sua rede.

## <a name="next-steps"></a>Passos seguintes

Saiba como desencadear capturas de pacotes com base em alertas visitando a captura de [pacotes de uso para fazer monitorização proativa da rede com funções Azure](network-watcher-alert-triggered-packet-capture.md)

Saiba como visualizar os seus registos de fluxo NSG com Power BI visitando visualize os [registos de fluxos NSG com Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)



<!-- images -->
[1]: ./media/network-watcher-intrusion-detection-open-source-tools/figure1.png
[2]: ./media/network-watcher-intrusion-detection-open-source-tools/figure2.png
[3]: ./media/network-watcher-intrusion-detection-open-source-tools/figure3.png
[4]: ./media/network-watcher-intrusion-detection-open-source-tools/figure4.png
[5]: ./media/network-watcher-intrusion-detection-open-source-tools/figure5.png
[6]: ./media/network-watcher-intrusion-detection-open-source-tools/figure6.png
[7]: ./media/network-watcher-intrusion-detection-open-source-tools/figure7.png
