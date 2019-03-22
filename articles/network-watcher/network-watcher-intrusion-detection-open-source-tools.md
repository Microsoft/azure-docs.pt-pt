---
title: Executar a deteção de intrusão de rede com o observador de rede do Azure e ferramentas de código aberto | Documentos da Microsoft
description: Este artigo descreve como utilizar o observador de rede do Azure e abrir ferramentas de código para executar a deteção de intrusão de rede
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 0f043f08-19e1-4125-98b0-3e335ba69681
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 41b039c6598334d49b9ddb0608d7ad2ae045b223
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57901275"
---
# <a name="perform-network-intrusion-detection-with-network-watcher-and-open-source-tools"></a>Executar a deteção de intrusão de rede com o observador de rede e ferramentas de código aberto

Capturas de pacotes são um componente-chave para implementar sistemas de detecção de intrusão de rede (IDS) e efetuar a monitorização de segurança de rede (NSM). Existem várias ferramentas de IDS de código-fonte aberto que processam capturas de pacotes e procurar por assinaturas de invasões de rede possível e atividades maliciosas. Utilizar o pacote captura fornecido por observador de rede, pode analisar sua rede para qualquer intrusões prejudiciais ou vulnerabilidades.

Uma dessas ferramentas de código-fonte aberto é Suricata, um motor de IDS que utiliza conjuntos de regras para monitorizar o tráfego de rede e aciona alertas sempre que ocorrem eventos suspeitos. Suricata oferece um mecanismo de vários threads, o que significa que ele pode realizar a análise de tráfego de rede com maior velocidade e eficiência. Para obter mais detalhes sobre Suricata e as respetivas capacidades, visite o site em https://suricata-ids.org/.

## <a name="scenario"></a>Cenário

Este artigo explica como configurar o ambiente para executar a deteção de intrusão de rede com o observador de rede, Suricata e a pilha elástica. Observador de rede fornece capturas de pacotes utilizadas para executar a deteção de intrusão de rede. Suricata processa as capturas de pacotes e acionar alertas com base em pacotes que correspondem ao seu determinado conjunto de regras de ameaças. Estes alertas são armazenados num ficheiro de registo no seu computador local. Com a pilha elástica, os registos gerados pelo Suricata podem ser indexados e utilizados para criar um dashboard do Kibana, dando-lhe uma representação visual dos registos e um meio para obter rapidamente informações para potenciais vulnerabilidades de rede.  

![cenário de aplicação web simples][1]

Ambas as ferramentas de código-fonte aberto podem ser definidas numa VM do Azure, permitindo que execute esta análise na seu próprio ambiente de rede do Azure.

## <a name="steps"></a>Passos

### <a name="install-suricata"></a>Instalar Suricata

Para todos os outros métodos de instalação, visite https://suricata.readthedocs.io/en/latest/install.html

1. No terminal da linha de comandos da sua VM, execute os seguintes comandos:

    ```
    sudo add-apt-repository ppa:oisf/suricata-stable
    sudo apt-get update
    sudo sudo apt-get install suricata
    ```

1. Para verificar sua instalação, execute o comando `suricata -h` para ver a lista completa de comandos.

### <a name="download-the-emerging-threats-ruleset"></a>Transferir o conjunto de regras de ameaças emergentes

Nesta fase, mas não tem quaisquer regras para Suricata executar. Pode criar suas próprias regras se existem ameaças específicas à sua rede que pretende detetar, ou pode também conjuntos de regras de utilização, desenvolvido de vários fornecedores, como ameaças emergentes ou regras de VRT do Snort. Aqui usamos o conjunto de regras de ameaças emergentes livremente acessível:

Transferir o conjunto de regras e copiá-los para o diretório:

```
wget https://rules.emergingthreats.net/open/suricata/emerging.rules.tar.gz
tar zxf emerging.rules.tar.gz
sudo cp -r rules /etc/suricata/
```

### <a name="process-packet-captures-with-suricata"></a>Capturas de pacotes de processo com Suricata

Processar pacote captura usando Suricata, execute o seguinte comando:

```
sudo suricata -c /etc/suricata/suricata.yaml -r <location_of_pcapfile>
```
Para verificar os alertas resultantes, ler o ficheiro de fast.log:
```
tail -f /var/log/suricata/fast.log
```

### <a name="set-up-the-elastic-stack"></a>Configurar a pilha elástica

Embora os registos que produz Suricata contêm informações valiosas sobre o que acontece na nossa rede, estes ficheiros de registo não são mais fácil de ler e compreender. Ao ligar Suricata com a pilha elástica, podemos criar um dashboard do Kibana o que permite a pesquisa, criar um gráfico, analisar e obter informações a partir de nossos logs.

#### <a name="install-elasticsearch"></a>Instale o Elasticsearch

1. A pilha elástica da versão 5.0 e superior requer Java 8. Execute o comando `java -version` para verificar a sua versão. Se não tiver instalado de java, consulte a documentação sobre o [Azure-suppored JDKs](https://aka.ms/azure-jdks).

1. Transferir o pacote de binário correto para o seu sistema:

    ```
    curl -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.2.0.deb
    sudo dpkg -i elasticsearch-5.2.0.deb
    sudo /etc/init.d/elasticsearch start
    ```

    Outros métodos de instalação podem ser encontrados em [instalação Elasticsearch](https://www.elastic.co/guide/en/beats/libbeat/5.2/elasticsearch-installation.html)

1. Certifique-se de que o Elasticsearch está em execução com o comando:

    ```
    curl http://127.0.0.1:9200
    ```

    Deverá ver uma resposta semelhante ao seguinte:

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

Para obter mais instruções na pesquisa de elástica de instalação, consulte a página [instalação](https://www.elastic.co/guide/en/elasticsearch/reference/5.2/_installation.html)

### <a name="install-logstash"></a>Instalar o Logstash

1. Para instalar o Logstash, execute os seguintes comandos:

    ```
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```
1. Em seguida, precisamos configurar o Logstash para ler a partir da saída do ficheiro de eve.json. Crie um ficheiro de logstash.conf com:

    ```
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

1. Adicione o seguinte conteúdo para o ficheiro (Certifique-se de que o caminho para o ficheiro de eve.json está correto):

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

1. Certifique-se conceder as permissões corretas para o ficheiro de eve.json para que o Logstash pode ingerir o ficheiro.
    
    ```
    sudo chmod 775 /var/log/suricata/eve.json
    ```

1. Para iniciar o Logstash, execute o comando:

    ```
    sudo /etc/init.d/logstash start
    ```

Para obter mais instruções sobre como instalar o Logstash, consulte o [documentação oficial](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html)

### <a name="install-kibana"></a>Instalar o Kibana

1. Execute os seguintes comandos para instalar o Kibana:

    ```
    curl -L -O https://artifacts.elastic.co/downloads/kibana/kibana-5.2.0-linux-x86_64.tar.gz
    tar xzvf kibana-5.2.0-linux-x86_64.tar.gz

    ```
1. Para executar os comandos de utilização do Kibana:

    ```
    cd kibana-5.2.0-linux-x86_64/
    ./bin/kibana
    ```

1. Para ver a sua interface de web do Kibana, navegue para `http://localhost:5601`
1. Para este cenário, o padrão de índice utilizado para os registos de Suricata é "logstash-*"

1. Se pretender ver o dashboard do Kibana remotamente, criar uma regra NSG de entrada que permite o acesso ao **porta 5601**.

### <a name="create-a-kibana-dashboard"></a>Criar um dashboard do Kibana

Neste artigo, nós fornecemos um dashboard de exemplo para ver detalhes e as tendências em seus alertas.

1. Transfira o ficheiro de dashboard [aqui](https://aka.ms/networkwatchersuricatadashboard), o ficheiro de visualização [aqui](https://aka.ms/networkwatchersuricatavisualization)e o arquivo de pesquisa guardada [aqui](https://aka.ms/networkwatchersuricatasavedsearch).

1. Sob o **Management** separador do Kibana, navegue até à **objetos guardados** e importar todos os três arquivos. Em seguida, a partir da **Dashboard** separador, pode abrir e carregue o dashboard de exemplo.

Também pode criar suas próprias visualizações e dashboards adaptadas para métricas de seu interesse. Saiba mais sobre a criação de visualizações do Kibana a partir do Kibana [documentação oficial](https://www.elastic.co/guide/en/kibana/current/visualize.html).

![dashboard do kibana][2]

### <a name="visualize-ids-alert-logs"></a>Visualizar registos de alerta de IDS

O dashboard de exemplo fornece várias visualizações dos registos de alerta do Suricata:

1. Alertas por GeoIP – um mapa que mostra a distribuição de alertas por país/região de origem com base na localização geográfica (determinada pelo IP)

    ![ip de geo][3]

1. Alertas de 10 principais – um resumo dos 10 mais frequente acionados alertas e sua descrição. Clicar num alerta individual filtra para baixo do dashboard para as informações relativas a esse alerta específica.

    ![imagem de 4][4]

1. Número de alertas – a contagem total de alertas acionados pelo conjunto de regras

    ![imagem de 5][5]

1. Principais 20 origem/destino IPs/portas - gráficos de pizza que mostra os principais 20 IPs e as portas que alertas foram acionadas no. Pode filtrar para baixo em IPs/portas específicas para ver o número e o tipo de alertas estão sendo acionado.

    ![imagem 6][6]

1. Resumo de alerta – uma tabela que resume os detalhes específicos de cada alerta individual. Pode personalizar esta tabela para mostrar outros parâmetros de interesse para cada alerta.

    ![imagem 7][7]

Para obter mais documentação sobre como criar visualizações personalizadas e dashboards, veja [documentação oficial do Kibana](https://www.elastic.co/guide/en/kibana/current/introduction.html).

## <a name="conclusion"></a>Conclusão

Ao combinar o pacote captura desde que o observador de rede e ferramentas de IDS de código-fonte aberto como Suricata, pode realizar a detecção de intrusão de rede para uma ampla variedade de ameaças. Estes dashboards permitem-lhe detetar rapidamente tendências e anomalias na sua rede, como dig bem os dados para descobrir a causa de raiz de alertas, como agentes de utilizador mal intencionado ou portas vulneráveis. Com esses dados extraídos, pode tomar decisões informadas sobre como reagir a e proteger sua rede contra quaisquer tentativas de intrusão prejudicial e criar regras para impedir as intrusões futuras à sua rede.

## <a name="next-steps"></a>Passos Seguintes

Saiba como acionar as capturas de pacotes com base em alertas ao visitar [utilizar a captura de pacotes para efetuar a monitorização de rede proativa com as funções do Azure](network-watcher-alert-triggered-packet-capture.md)

Saiba como visualizar os seus registos de fluxo NSG com o Power BI, visite a página [registos com o Power BI de fluxos NSG visualizar](network-watcher-visualize-nsg-flow-logs-power-bi.md)



<!-- images -->
[1]: ./media/network-watcher-intrusion-detection-open-source-tools/figure1.png
[2]: ./media/network-watcher-intrusion-detection-open-source-tools/figure2.png
[3]: ./media/network-watcher-intrusion-detection-open-source-tools/figure3.png
[4]: ./media/network-watcher-intrusion-detection-open-source-tools/figure4.png
[5]: ./media/network-watcher-intrusion-detection-open-source-tools/figure5.png
[6]: ./media/network-watcher-intrusion-detection-open-source-tools/figure6.png
[7]: ./media/network-watcher-intrusion-detection-open-source-tools/figure7.png
