---
title: Executar a detecção de intrusão de rede com ferramentas de código aberto
titleSuffix: Azure Network Watcher
description: Este artigo descreve como usar o observador de rede do Azure e ferramentas de software livre para executar a detecção de invasão de rede
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 0f043f08-19e1-4125-98b0-3e335ba69681
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: 8a0b4ff4fc985355d8dc76f2f3fd7fb35da55ec0
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74275925"
---
# <a name="perform-network-intrusion-detection-with-network-watcher-and-open-source-tools"></a>Executar a detecção de intrusão de rede com o observador de rede e ferramentas de código aberto

As capturas de pacote são um componente-chave para implementar os sistemas de detecção de intrusão de rede (IDS) e executar o monitoramento de segurança de rede (NSM). Há várias ferramentas de IDS de software livre que processam capturas de pacote e procuram assinaturas de possíveis invasões de rede e atividades mal-intencionadas. Usando as capturas de pacote fornecidas pelo observador de rede, você pode analisar sua rede em busca de intrusões ou vulnerabilidades prejudiciais.

Uma dessas ferramentas de software livre é Suricata, um mecanismo de IDS que usa RuleSets para monitorar o tráfego de rede e dispara alertas sempre que eventos suspeitos ocorrem. O Suricata oferece um mecanismo multi-threaded, o que significa que ele pode executar a análise de tráfego de rede com maior velocidade e eficiência. Para obter mais detalhes sobre o Suricata e seus recursos, visite o site em https://suricata-ids.org/.

## <a name="scenario"></a>Cenário

Este artigo explica como configurar seu ambiente para executar a detecção de invasão de rede usando o observador de rede, o Suricata e a pilha elástica. O observador de rede fornece as capturas de pacote usadas para executar a detecção de intrusão na rede. O Suricata processa as capturas de pacotes e aciona alertas com base em pacotes que correspondem a seu conjunto de regras de ameaças. Esses alertas são armazenados em um arquivo de log em seu computador local. Usando a pilha elástica, os logs gerados pelo Suricata podem ser indexados e usados para criar um painel do Kibana, fornecendo uma representação visual dos logs e um meio de obter rapidamente informações sobre possíveis vulnerabilidades de rede.  

![cenário de aplicativo Web simples][1]

Ambas as ferramentas de software livre podem ser configuradas em uma VM do Azure, permitindo que você execute essa análise em seu próprio ambiente de rede do Azure.

## <a name="steps"></a>Passos

### <a name="install-suricata"></a>Instalar o Suricata

Para todos os outros métodos de instalação, visite https://suricata.readthedocs.io/en/latest/install.html

1. No terminal de linha de comando de sua VM, execute os seguintes comandos:

    ```
    sudo add-apt-repository ppa:oisf/suricata-stable
    sudo apt-get update
    sudo sudo apt-get install suricata
    ```

1. Para verificar sua instalação, execute o comando `suricata -h` para ver a lista completa de comandos.

### <a name="download-the-emerging-threats-ruleset"></a>Baixe o conjunto de regras de ameaças emergentes

Neste estágio, não temos nenhuma regra para a execução do Suricata. Você pode criar suas próprias regras se houver ameaças específicas à sua rede que deseja detectar ou se você também pode usar conjuntos de regras desenvolvidos de vários provedores, como ameaças emergentes ou regras de VRT do snort. Usamos o conjunto de regras de ameaças emergentes acessíveis gratuitamente aqui:

Baixe o conjunto de regras e copie-o para o diretório:

```
wget https://rules.emergingthreats.net/open/suricata/emerging.rules.tar.gz
tar zxf emerging.rules.tar.gz
sudo cp -r rules /etc/suricata/
```

### <a name="process-packet-captures-with-suricata"></a>Processar capturas de pacote com Suricata

Para processar capturas de pacote usando o Suricata, execute o seguinte comando:

```
sudo suricata -c /etc/suricata/suricata.yaml -r <location_of_pcapfile>
```
Para verificar os alertas resultantes, leia o arquivo Fast. log:
```
tail -f /var/log/suricata/fast.log
```

### <a name="set-up-the-elastic-stack"></a>Configurar a pilha elástica

Embora os logs que o Suricata produz contenham informações valiosas sobre o que está acontecendo em nossa rede, esses arquivos de log não são os mais fáceis de ler e entender. Conectando o Suricata à pilha elástica, podemos criar um painel do Kibana o que nos permite pesquisar, grafar, analisar e derivar informações de nossos logs.

#### <a name="install-elasticsearch"></a>Instalar o Elasticsearch

1. A pilha elástica da versão 5,0 e superior requer o Java 8. Execute o comando `java -version` para verificar sua versão. Se você não tiver o Java instalado, consulte a documentação sobre o [Azure-há suporte para JDKs](https://aka.ms/azure-jdks).

1. Baixe o pacote binário correto para seu sistema:

    ```
    curl -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.2.0.deb
    sudo dpkg -i elasticsearch-5.2.0.deb
    sudo /etc/init.d/elasticsearch start
    ```

    Outros métodos de instalação podem ser encontrados em [instalação do Elasticsearch](https://www.elastic.co/guide/en/beats/libbeat/5.2/elasticsearch-installation.html)

1. Verifique se Elasticsearch está em execução com o comando:

    ```
    curl http://127.0.0.1:9200
    ```

    Você deverá ver uma resposta semelhante a esta:

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

Para obter mais instruções sobre como instalar a pesquisa elástica, consulte a página [instalação](https://www.elastic.co/guide/en/elasticsearch/reference/5.2/_installation.html)

### <a name="install-logstash"></a>Instalar o Logstash

1. Para instalar o Logstash, execute os seguintes comandos:

    ```
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```
1. Em seguida, precisamos configurar o Logstash para ler a partir da saída do arquivo véspera. JSON. Crie um arquivo logstash. conf usando:

    ```
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

1. Adicione o seguinte conteúdo ao arquivo (verifique se o caminho para o arquivo véspera. JSON está correto):

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

1. Certifique-se de conceder as permissões corretas ao arquivo véspera. JSON para que Logstash possa ingerir o arquivo.
    
    ```
    sudo chmod 775 /var/log/suricata/eve.json
    ```

1. Para iniciar o Logstash, execute o comando:

    ```
    sudo /etc/init.d/logstash start
    ```

Para obter mais instruções sobre como instalar o Logstash, consulte a [documentação oficial](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html)

### <a name="install-kibana"></a>Instalar o Kibana

1. Execute os seguintes comandos para instalar o Kibana:

    ```
    curl -L -O https://artifacts.elastic.co/downloads/kibana/kibana-5.2.0-linux-x86_64.tar.gz
    tar xzvf kibana-5.2.0-linux-x86_64.tar.gz

    ```
1. Para executar o Kibana, use os comandos:

    ```
    cd kibana-5.2.0-linux-x86_64/
    ./bin/kibana
    ```

1. Para exibir a interface da Web do Kibana, navegue até `http://localhost:5601`
1. Para esse cenário, o padrão de índice usado para os logs de Suricata é "logstash-*"

1. Se você quiser exibir o painel do Kibana remotamente, crie uma regra de NSG de entrada permitindo o acesso à **porta 5601**.

### <a name="create-a-kibana-dashboard"></a>Criar um painel do Kibana

Para este artigo, fornecemos um painel de exemplo para que você exiba tendências e detalhes em seus alertas.

1. Baixe o arquivo do painel [aqui](https://aka.ms/networkwatchersuricatadashboard), o arquivo de visualização [aqui](https://aka.ms/networkwatchersuricatavisualization)e o arquivo de pesquisa salvo [aqui](https://aka.ms/networkwatchersuricatasavedsearch).

1. Na guia **Gerenciamento** do Kibana, navegue até **objetos salvos** e importe todos os três arquivos. Em seguida, na guia **painel** , você pode abrir e carregar o painel de exemplo.

Você também pode criar suas próprias visualizações e painéis personalizados para métricas de seus próprios interesses. Leia mais sobre como criar visualizações Kibana da [documentação oficial](https://www.elastic.co/guide/en/kibana/current/visualize.html)do Kibana.

![painel do kibana][2]

### <a name="visualize-ids-alert-logs"></a>Visualizar logs de alerta de IDS

O painel de exemplo fornece várias visualizações dos logs de alerta do Suricata:

1. Alertas por GeoIP – um mapa que mostra a distribuição de alertas por seu país/região de origem com base no local geográfico (determinado pelo IP)

    ![IP geográfico][3]

1. 10 principais alertas – um resumo dos 10 alertas disparados mais frequentes e sua descrição. Clicar em um alerta individual filtra o painel para as informações referentes a esse alerta específico.

    ![imagem 4][4]

1. Número de alertas – a contagem total de alertas disparados pelo conjunto de regras

    ![imagem 5][5]

1. 20 principais IPs/portas de origem/destino-gráficos de pizza mostrando os 20 principais IPs e portas em que os alertas foram acionados. Você pode filtrar por IPs/portas específicas para ver quantos e quais tipos de alertas estão sendo disparados.

    ![imagem 6][6]

1. Resumo do alerta – uma tabela que resume detalhes específicos de cada alerta individual. Você pode personalizar essa tabela para mostrar outros parâmetros de interesse de cada alerta.

    ![imagem 7][7]

Para obter mais documentação sobre a criação de painéis e visualizações personalizadas, consulte a [documentação oficial do Kibana](https://www.elastic.co/guide/en/kibana/current/introduction.html).

## <a name="conclusion"></a>Conclusão

Combinando as capturas de pacote fornecidas pelo observador de rede e ferramentas de IDS de software livre, como o Suricata, você pode executar a detecção de invasão de rede para uma ampla gama de ameaças. Esses painéis permitem identificar rapidamente tendências e anomalias em sua rede, bem como se aprofundar nos dados para descobrir as causas raiz de alertas, como agentes de usuários mal-intencionados ou portas vulneráveis. Com esses dados extraídos, você pode tomar decisões informadas sobre como reagir e proteger sua rede contra tentativas de invasão prejudiciais e criar regras para evitar invasões futuras à sua rede.

## <a name="next-steps"></a>Passos seguintes

Saiba como disparar capturas de pacote com base em alertas visitando [usar a captura de pacote para fazer o monitoramento de rede proativo com o Azure Functions](network-watcher-alert-triggered-packet-capture.md)

Saiba como visualizar seus logs de fluxo do NSG com Power BI visitando [Visualizar logs do NSG fluxos com Power bi](network-watcher-visualize-nsg-flow-logs-power-bi.md)



<!-- images -->
[1]: ./media/network-watcher-intrusion-detection-open-source-tools/figure1.png
[2]: ./media/network-watcher-intrusion-detection-open-source-tools/figure2.png
[3]: ./media/network-watcher-intrusion-detection-open-source-tools/figure3.png
[4]: ./media/network-watcher-intrusion-detection-open-source-tools/figure4.png
[5]: ./media/network-watcher-intrusion-detection-open-source-tools/figure5.png
[6]: ./media/network-watcher-intrusion-detection-open-source-tools/figure6.png
[7]: ./media/network-watcher-intrusion-detection-open-source-tools/figure7.png
