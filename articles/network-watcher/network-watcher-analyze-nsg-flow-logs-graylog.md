---
title: Analisar registos de fluxo de grupo de segurança de rede do Azure - o Graylog | Documentos da Microsoft
description: Saiba como gerir e analisar a rede registos de fluxo do grupo de segurança no Azure com o observador de rede e o Graylog.
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
ms.date: 09/19/2017
ms.author: mareat
ms.openlocfilehash: a5fadcfce154740a79a8764f44f08b21ad18f4d8
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57879944"
---
# <a name="manage-and-analyze-network-security-group-flow-logs-in-azure-using-network-watcher-and-graylog"></a>Gerir e analisar a rede registos de fluxo do grupo de segurança no Azure com o observador de rede e o Graylog

[Os registos de fluxo do grupo de segurança de rede](network-watcher-nsg-flow-logging-overview.md) fornecem informações que pode utilizar para compreender o tráfego IP de entrada e saída para interfaces de rede do Azure. Os registos de fluxo mostram fluxos de saída e entrados num por base de regra de grupo de segurança de rede, a interface de rede o fluxo se aplica, informações de 5 cadeias de identificação (IP de origem/destino, porta de origem/destino, protocolo) sobre o fluxo, e se o tráfego foi permitido ou negado .

Pode ter vários grupos de segurança de rede na sua rede com o registo de fluxo ativado. Vários grupos de segurança de rede com o registo de fluxo ativado podem tornar complicado para analisar e obter informações a partir dos seus registos. Este artigo fornece uma solução para gerir centralmente estes registos de fluxo do grupo do rede segurança usando o Graylog, uma gestão de registos de código-fonte aberto e ferramenta de análise e Logstash, um pipeline de processamento de dados do lado do servidor do código-fonte aberto.

> [!Warning]
> Os seguintes passos funcionam com a versão de registos de fluxo 1. Para obter detalhes, consulte [introdução ao registo do fluxo para grupos de segurança de rede](network-watcher-nsg-flow-logging-overview.md). As instruções seguintes não irão funcionar com a versão 2 dos ficheiros de registo, sem modificações.

## <a name="scenario"></a>Cenário

Registos de fluxo de grupo de segurança de rede estão ativados com o observador de rede. Registos de fluxo fluxo no armazenamento de Blobs do Azure. Um plug-in do Logstash é utilizado para ligar e processar registos de fluxo do armazenamento de BLOBs e enviá-los para o Graylog. Depois dos registos de fluxo são armazenados no Graylog, podem ser analisados e visualizados em dashboards personalizados.

![Fluxo de trabalho o Graylog](./media/network-watcher-analyze-nsg-flow-logs-graylog/workflow.png)

## <a name="installation-steps"></a>Passos de Instalação

### <a name="enable-network-security-group-flow-logging"></a>Ativar o registo de fluxo de grupo de segurança de rede

Para este cenário, tem de ter ativado no grupo de segurança de rede, pelo menos, um na sua conta dos registo de fluxo de grupo para segurança rede. Para obter instruções sobre como ativar os registos de fluxo do grupo de segurança de rede, consulte o seguinte artigo [introdução ao registo do fluxo para grupos de segurança de rede](network-watcher-nsg-flow-logging-overview.md).

### <a name="setting-up-graylog"></a>Como configurar o Graylog

Neste exemplo, o Graylog e Logstash estão configurados num servidor do Ubuntu 14.04, implementado no Azure.

- Consulte a [documentação](https://docs.graylog.org/en/2.2/pages/installation/os/ubuntu.html) do Graylog, para obter instruções passo a passo sobre como instalar o Ubuntu.
- Confirme que também configura a interface da web o Graylog ao seguir a [documentação](https://docs.graylog.org/en/2.2/pages/configuration/web_interface.html#configuring-webif).

Este exemplo utiliza a mínimo (ou seja, a configuração o Graylog uma única instância de um Graylog), mas o Graylog pode ser arquitetado para dimensionar todos os recursos, dependendo do seu sistema e de produção necessidades. Para obter mais informações sobre considerações de arquitetura ou um guia de arquitetura aprofundada, veja do Graylog [documentação](https://docs.graylog.org/en/2.2/pages/architecture.html) e [guia de arquitetura](https://www.slideshare.net/Graylog/graylog-engineering-design-your-architecture).

O Graylog pode ser instalado de várias maneiras, dependendo das suas plataformas e preferências. Para obter uma lista completa dos métodos de instalação possíveis, consulte oficial do Graylog [documentação](https://docs.graylog.org/en/2.2/pages/installation.html). A aplicação de servidor o Graylog é executado em distribuições do Linux e tem os seguintes pré-requisitos:

-  Java SE 8 ou posterior – [documentação Azul Azure JDK](https://aka.ms/azure-jdks)
-  Elástico pesquisar 2.x (2.1.0 ou posterior)- [documentação de instalação do Elasticsearch](https://www.elastic.co/guide/en/elasticsearch/reference/2.4/_installation.html)
-  MongoDB 2,4 ou posterior – [documentação de instalação do MongoDB](https://docs.mongodb.com/manual/administration/install-on-linux/)

### <a name="install-logstash"></a>Instalar o Logstash

Logstash é utilizado para nivelamento os registos de fluxo de formatados em JSON para um nível de cadeias de identificação de fluxo. Mesclar os registos de fluxo facilita os registos organizar e procurar o Graylog.

1. Para instalar o Logstash, execute os seguintes comandos:

   ```bash
   curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
   sudo dpkg -i logstash-5.2.0.deb
   ```

2. Configure o Logstash para analisar os registos de fluxo e enviá-los para o Graylog. Crie um ficheiro de Logstash.conf:

   ```bash
   sudo touch /etc/logstash/conf.d/logstash.conf
   ```

3. Adicione o seguinte conteúdo para o ficheiro. Altere os valores realçados para refletir os detalhes da conta de armazenamento:

   ```
    input {
        azureblob
        {
            storage_account_name => "mystorageaccount"
            storage_access_key => "NrUZmx7pJSKaRJzvQbeiZWi5nBRWOTr7Wwr9DrvK7YtDBrADYxT1y0oEExtSlkDnGRt7qcRiZzEBCCyRYND8SxSt"
            container => "insights-logs-networksecuritygroupflowevent"
            registry_create_policy => "start_over"
            codec => "json"
            file_head_bytes => 21
            file_tail_bytes => 9
            # Possible options: `do_not_break`, `with_head_tail`, `without_head_tail`
            break_json_down_policy  => 'with_head_tail'
            break_json_batch_count => 2
            interval => 5
        }
    }
    
    filter {
        split { field => "[records]" }
        split { field => "[records][properties][flows]"}
        split { field => "[records][properties][flows][flows]"}
        split { field => "[records][properties][flows][flows][flowTuples]"
    }
    
     mutate {
        split => { "[records][resourceId]" => "/"}
        add_field =>{
                    "Subscription" => "%{[records][resourceId][2]}"
                    "ResourceGroup" => "%{[records][resourceId][4]}"
                    "NetworkSecurityGroup" => "%{[records][resourceId][8]}"
        }
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
        udp {
            host => "127.0.0.1"
            port => 12201
        }
    }
    ```
   O ficheiro de configuração do Logstash fornecido é composto por três partes: a entrada, o filtro e a saída. A secção de entrada designa a origem de entrada dos registos que irá processar o Logstash – nesse caso, vai utilizar um blogue do Azure entrada Plug-in (instalado nos passos seguintes) que permite-nos acessar o fluxo de grupo de segurança de rede ficheiros JSON armazenados no armazenamento de BLOBs de registo.

A secção de filtro nivela, em seguida, cada ficheiro de registo de fluxo para que cada tupla de fluxo individuais e as respetivas propriedades associadas torna-se um evento separado do Logstash.

Por fim, a secção de saída encaminha cada evento do Logstash para o servidor o Graylog. De acordo com seu específica precisa, modificar o ficheiro de configuração do Logstash, conforme necessário.

   > [!NOTE]
   > O ficheiro de configuração anterior pressupõe que o servidor o Graylog foi configurado no endereço IP de loopback de anfitrião local 127.0.0.1. Caso contrário, não se esqueça de alterar o parâmetro de anfitrião na secção de saída para o endereço IP correto.

Para obter mais instruções sobre como instalar o Logstash, consulte o Logstash [documentação](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html).

### <a name="install-the-logstash-input-plug-in-for-azure-blob-storage"></a>Instalar a entrada do Logstash Plug-in para o armazenamento de Blobs do Azure

O plug-in do Logstash permite que acessar diretamente os registos de fluxo a partir da respetiva conta de armazenamento de blob designado. Para instalar o plug-in do diretório de instalação do Logstash padrão (em /usr/share/logstash/bin neste caso), execute o seguinte comando:

```bash
cd /usr/share/logstash/bin
sudo ./logstash-plugin install logstash-input-azureblob
```

Para obter mais informações sobre este plug-in, consulte a [documentação](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob).

### <a name="set-up-connection-from-logstash-to-graylog"></a>Configure a ligação do Logstash para o Graylog

Agora que tem de estabelecer uma ligação para os registos de fluxo com o Logstash e configurar o servidor o Graylog, terá de configurar o Graylog para aceitar os ficheiros de registo de entrada.

1. Navegue até à sua interface do web Server o Graylog utilizando o URL que configurou para ela. Pode acessar a interface ao direcionar o browser para `http://<graylog-server-ip>:9000/`

2. Para navegar para a página de configuração, selecione o **System** menu pendente na parte superior da navegação barra à direita e, em seguida, clique em **entradas**.
   Em alternativa, navegue para `http://<graylog-server-ip>:9000/system/inputs`

   ![Introdução](./media/network-watcher-analyze-nsg-flow-logs-graylog/getting-started.png)

3. Para iniciar a nova entrada, selecione *GELF UDP* no **selecione a entrada** baixo e, em seguida, preencha o formulário. GELF significa o Graylog formato de registo expandido. O formato GELF é desenvolvido pelo Graylog. Para saber mais sobre suas vantagens, consulte o Graylog [documentação](https://docs.graylog.org/en/2.2/pages/gelf.html).

   Certifique-se de vincular a entrada para o IP que tiver configurado o seu servidor o Graylog na. O endereço IP deve corresponder a **anfitrião** campo da saída do ficheiro de configuração do Logstash UDP. A porta predefinida deve ser *12201*. Certifique-se de que a porta corresponde a **porta** campo no UDP de saída designados no arquivo de configuração do Logstash.

   ![Entradas](./media/network-watcher-analyze-nsg-flow-logs-graylog/inputs.png)

   Uma vez iniciada a entrada, verá que são apresentados no **entradas locais** secção, conforme mostrado na imagem seguinte:

   ![](./media/network-watcher-analyze-nsg-flow-logs-graylog/local-inputs.png)

   Para saber mais sobre o Graylog entradas de mensagem, consulte a [documentação](https://docs.graylog.org/en/2.2/pages/sending_data.html#what-are-graylog-message-inputs).

4. Depois destas configurações foram feitas, pode iniciar o Logstash para iniciar a leitura nos registos de fluxo com o seguinte comando: `sudo systemctl start logstash.service`.

### <a name="search-through-graylog-messages"></a>Pesquisar por meio de mensagens o Graylog

Depois de permitir que algum tempo para o seu servidor o Graylog recolher mensagens, é possível pesquisar as mensagens. Para verificar as mensagens sendo enviadas ao seu servidor o Graylog, pelo **entradas** clique da página de configuração a "**Show recebeu mensagens**" botão do GELF UDP de entrada que criou. Será direcionado para uma tela semelhante à imagem seguinte: 

![Histograma](./media/network-watcher-analyze-nsg-flow-logs-graylog/histogram.png)

Clicar no link azul "% {Message}" expande a cada mensagem para mostrar os parâmetros de cada tupla de fluxo, conforme mostrado na imagem seguinte:

![Mensagens](./media/network-watcher-analyze-nsg-flow-logs-graylog/messages.png)

Por predefinição, todos os campos de mensagem estão incluídos na pesquisa, se não selecionar um campo de mensagem específica para procurar. Se pretender pesquisar mensagens específicas (ou seja – IP de origem do fluxo cadeias de identificação de uma específica) pode usar a linguagem de consulta de pesquisa o Graylog como [documentado](https://docs.graylog.org/en/2.2/pages/queries.html)

## <a name="analyze-network-security-group-flow-logs-using-graylog"></a>Analisar com o Graylog dos registos de fluxo de grupo para segurança rede

Agora que o Graylog-configurada em execução, pode utilizar algumas das funcionalidades para compreender melhor os seus dados de registo do fluxo. É uma forma através de dashboards para criar vistas específicas dos seus dados.

### <a name="create-a-dashboard"></a>Criar um dashboard

1. Na barra de navegação superior, selecione **Dashboards** ou navegue para `http://<graylog-server-ip>:9000/dashboards/`

2. A partir daí, clique no verde **criar dashboard** botão e preencha o formulário curto com o título e descrição do dashboard. Acertar a **guardar** botão para criar o novo dashboard. Verá um dashboard semelhante à imagem seguinte:

    ![Dashboards](./media/network-watcher-analyze-nsg-flow-logs-graylog/dashboards.png)

### <a name="add-widgets"></a>Adicionar widgets

Pode clicar o título do dashboard para vê-lo, mas agora está vazio, uma vez que ainda não adicionamos qualquer widgets. São um widget de tipo simples e útil para adicionar ao dashboard **valores rápida** gráficos, que exibe uma lista de valores do campo selecionado e a distribuição dos mesmos.

1. Navegue de volta para os resultados da pesquisa da entrada UDP que está a receber os registos de fluxo, selecionando **pesquisa** da barra de navegação superior.

2. Sob o **resultado de pesquisa** painel no lado esquerdo do ecrã, encontre o **campos** guia, que lista os vários campos de cada mensagem de cadeias de identificação de fluxo recebida.

3. Selecione qualquer parâmetro pretendido na qual pretende visualizar (neste exemplo, o IP de origem está selecionado). Para mostrar a lista de possíveis widgets, clique na seta pendente azul à esquerda do campo, em seguida, selecione **valores rápidas** para gerar o widget. Deverá ver algo semelhante à imagem seguinte:

   ![IP de origem](./media/network-watcher-analyze-nsg-flow-logs-graylog/srcip.png)

4. A partir daí, pode selecionar o **adicionar ao dashboard** no canto superior direito do widget e selecione o dashboard correspondente para adicionar.

5. Navegue de volta para o dashboard para ver o widget que acabou de adicionar.

   Pode adicionar uma variedade de outras widgets como histogramas e contagens ao seu dashboard para controlar as métricas importantes, como o dashboard de exemplo mostrado na imagem seguinte:

   ![Dashboard de Flowlogs](./media/network-watcher-analyze-nsg-flow-logs-graylog/flowlogs-dashboard.png)

    Para obter mais explicações sobre os outros tipos de widgets e dashboards, consulte o do Graylog [documentação](https://docs.graylog.org/en/2.2/pages/dashboards.html).

Ao integrar o observador de rede com o Graylog, agora tem uma forma conveniente e centralizada para gerir e visualizar registos de fluxo de grupo de segurança de rede. O Graylog tem uma série de outros recursos poderosos, tais como fluxos e os alertas que também podem ser utilizadas para gerir os registos de fluxo ainda mais e compreender melhor o seu tráfego de rede. Agora que tem o Graylog configurado e ligado ao Azure, não hesite em continuar a explorar as outras funcionalidades que oferece.

## <a name="next-steps"></a>Passos Seguintes

Saiba como visualizar os seus registos de fluxo do grupo de segurança rede com o Power BI ao visitar [registos com o Power BI de fluxos do grupo de segurança de rede de visualizar](network-watcher-visualize-nsg-flow-logs-power-bi.md).
