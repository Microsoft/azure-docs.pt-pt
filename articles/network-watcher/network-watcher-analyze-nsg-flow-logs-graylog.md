---
title: Analise os registos de fluxo do grupo de segurança da rede Azure - Graylog [ Graylog ] Microsoft Docs
description: Aprenda a gerir e analisar os registos de fluxo de grupos de segurança da rede em Azure usando o Network Watcher e o Graylog.
services: network-watcher
documentationcenter: na
author: damendo
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/19/2017
ms.author: damendo
ms.openlocfilehash: 1e597a81967a8fb6be2959d53e65ad01135e5e25
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76842908"
---
# <a name="manage-and-analyze-network-security-group-flow-logs-in-azure-using-network-watcher-and-graylog"></a>Gerir e analisar registos de fluxo de grupos de segurança de rede em Azure usando o Network Watcher e o Graylog

[Os registos](network-watcher-nsg-flow-logging-overview.md) de fluxo do grupo de segurança da rede fornecem informações que pode usar para entender o tráfego IP para interfaces de rede Azure. Os registos de fluxo saem dos fluxos de saída e de entrada numa base de regra do grupo de segurança da rede, a interface de rede a que o fluxo se aplica, informações de 5-tuple (Source/Destination IP, Source/Destination Port, Protocol) sobre o fluxo, e se o tráfego foi permitido ou negado.

Pode ter muitos grupos de segurança de rede na sua rede com o registo de fluxos ativado. Vários grupos de segurança de rede com registo de fluxo habilitado podem tornar complicado analisar e obter informações dos seus registos. Este artigo fornece uma solução para gerir centralmente estes registos de fluxo de grupos de segurança da rede usando graylog, uma ferramenta de gestão e análise de registo de código aberto, e Logstash, um pipeline de processamento de dados do lado do servidor de código aberto.

> [!Warning]
> Os passos seguintes funcionam com os registos de fluxo versão 1. Para mais detalhes, consulte [Introdução à exploração de fluxos de registo para grupos de segurança](network-watcher-nsg-flow-logging-overview.md)da rede . As seguintes instruções não funcionarão com a versão 2 dos ficheiros de registo, sem modificação.

## <a name="scenario"></a>Cenário

Os registos de fluxo do grupo de segurança da rede estão ativados utilizando o Observador da Rede. Os registos de fluxo fluem para o armazenamento de bolhas Azure. Um plugin logstash é usado para ligar e processar registos de fluxo do armazenamento blob e enviá-los para Graylog. Uma vez armazenados os registos de fluxo em Graylog, podem ser analisados e visualizados em dashboards personalizados.

![Fluxo de trabalho de graylog](./media/network-watcher-analyze-nsg-flow-logs-graylog/workflow.png)

## <a name="installation-steps"></a>Etapas de instalação

### <a name="enable-network-security-group-flow-logging"></a>Ativar o fluxo de fluxo do grupo de segurança da rede

Para este cenário, deve ter o fluxo de fluxo de fluxo do grupo de segurança da rede ativado em pelo menos um grupo de segurança de rede na sua conta. Para obter instruções sobre a ativação de registos de fluxo de grupos de segurança da rede, consulte o seguinte artigo [Introdução à exploração de fluxos de registo para grupos](network-watcher-nsg-flow-logging-overview.md)de segurança da rede .

### <a name="setting-up-graylog"></a>Configuração de Graylog

Neste exemplo, tanto graylog como Logstash estão configurados num Servidor Ubuntu 14.04, implantado em Azure.

- Consulte a [documentação](https://docs.graylog.org/en/2.2/pages/installation/os/ubuntu.html) da Graylog, para obter instruções passo a passo sobre como instalar em Ubuntu.
- Certifique-se de configurar também a interface web Graylog seguindo a [documentação](https://docs.graylog.org/en/2.2/pages/configuration/web_interface.html#configuring-webif).

Este exemplo utiliza a configuração mínima de Graylog (isto é, uma única instância de um Graylog), mas graylog pode ser arquitetado para escalar recursos dependendo do seu sistema e necessidades de produção. Para mais informações sobre considerações arquitetónicas ou um guia arquitetónico profundo, consulte a [documentação](https://docs.graylog.org/en/2.2/pages/architecture.html) e [o guia arquitetónico](https://www.slideshare.net/Graylog/graylog-engineering-design-your-architecture)da Graylog.

O Graylog pode ser instalado de muitas formas, dependendo da sua plataforma e preferências. Para obter uma lista completa dos possíveis métodos de instalação, consulte a [documentação](https://docs.graylog.org/en/2.2/pages/installation.html)oficial da Graylog. A aplicação do servidor Graylog funciona nas distribuições do Linux e tem os seguintes pré-requisitos:

-  Java SE 8 ou mais tarde - [Documentação Azul Azure JDK](https://aka.ms/azure-jdks)
-  Pesquisa Elástica 2.x (2.1.0 ou mais tarde) - Documentação de [instalação de pesquisa elástica](https://www.elastic.co/guide/en/elasticsearch/reference/2.4/_installation.html)
-  MongoDB 2.4 ou mais tarde – Documentação de [instalação do MongoDB](https://docs.mongodb.com/manual/administration/install-on-linux/)

### <a name="install-logstash"></a>Instalar logstash

O logstash é usado para aplainar os troncos de fluxo formatadoJON para um nível de tuple de fluxo. Aplainar os troncos de fluxo facilita a organização e a pesquisa em Graylog.

1. Para instalar o Logstash, execute os seguintes comandos:

   ```bash
   curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
   sudo dpkg -i logstash-5.2.0.deb
   ```

2. Configure o Logstash para analisar os registos de fluxo e envie-os para graylog. Crie um ficheiro Logstash.conf:

   ```bash
   sudo touch /etc/logstash/conf.d/logstash.conf
   ```

3. Adicione o seguinte conteúdo ao ficheiro. Altere os valores destacados para refletir os detalhes da sua conta de armazenamento:

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
   O ficheiro config Logstash fornecido é composto por três partes: a entrada, filtro e saída. A secção de entrada designa a fonte de entrada dos registos que o Logstash irá processar – neste caso, vai utilizar um plugin de entrada de blog Azure (instalado nos próximos passos) que nos permite aceder aos ficheiros JSON de registo de fluxo do grupo de segurança da rede armazenados no armazenamento de blob.

A secção do filtro achata cada ficheiro de registo de fluxo para que cada estple de fluxo individual e as suas propriedades associadas se tornem um evento de Logstash separado.

Finalmente, a secção de saída encaminha cada evento de Logstash para o servidor Graylog. Para atender às suas necessidades específicas, modifique o ficheiro logstash config, conforme necessário.

   > [!NOTE]
   > O ficheiro config anterior pressupõe que o servidor Graylog foi configurado no endereço IP de loopback local 127.0.0.1. Caso contrário, certifique-se de que altera o parâmetro do hospedeiro na secção de saída para o endereço IP correto.

Para obter mais instruções sobre a instalação de Logstash, consulte a [documentação](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html)do Logstash .

### <a name="install-the-logstash-input-plug-in-for-azure-blob-storage"></a>Instale o plug-in de entrada Logstash para armazenamento de blob Azure

O plugin Logstash permite-lhe aceder diretamente aos registos de fluxo da sua conta de armazenamento de blob designada. Para instalar o plug-in, a partir do diretório de instalação predefinido do Logstash (neste caso/usr/share/logstash/bin), execute o seguinte comando:

```bash
cd /usr/share/logstash/bin
sudo ./logstash-plugin install logstash-input-azureblob
```

Para mais informações sobre esta ficha, consulte a [documentação](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob).

### <a name="set-up-connection-from-logstash-to-graylog"></a>Configurar a ligação de Logstash a Graylog

Agora que estabeleceu uma ligação aos registos de fluxo utilizando o Logstash e configurar o servidor Graylog, precisa de configurar o Graylog para aceitar os ficheiros de registo que entram.

1. Navegue para a interface web do Graylog Server utilizando o URL configurado para o mesmo. Você pode aceder à interface direcionando o seu navegador para`http://<graylog-server-ip>:9000/`

2. Para navegar na página de configuração, selecione o menu de drop-down do **Sistema** na barra de navegação superior à direita e, em seguida, clique em **Inputs**.
   Alternativamente, navegar para`http://<graylog-server-ip>:9000/system/inputs`

   ![Introdução](./media/network-watcher-analyze-nsg-flow-logs-graylog/getting-started.png)

3. Para lançar a nova entrada, selecione *GELF UDP* na entrada **Select** drop-down e, em seguida, preencha o formulário. GELF significa Graylog Extended Log Format. O formato GELF é desenvolvido pela Graylog. Para saber mais sobre as suas vantagens, consulte a [documentação](https://docs.graylog.org/en/2.2/pages/gelf.html)Graylog.

   Certifique-se de ligar a entrada ao IP em que configurao o servidor Graylog. O endereço IP deve corresponder ao campo **de acolhimento** da saída UDP do ficheiro de configuração Logstash. A porta padrão deve ser *12201*. Certifique-se de que a porta corresponde ao campo **portuário** da saída UDP designada no ficheiro logstash config.

   ![Entradas](./media/network-watcher-analyze-nsg-flow-logs-graylog/inputs.png)

   Assim que lançar a entrada, deve vê-la aparecer na secção de **inputs locais,** como mostra a seguinte imagem:

   ![](./media/network-watcher-analyze-nsg-flow-logs-graylog/local-inputs.png)

   Para saber mais sobre as inputs da mensagem Graylog, consulte a [documentação](https://docs.graylog.org/en/2.2/pages/sending_data.html#what-are-graylog-message-inputs).

4. Uma vez feitas estas configurações, pode iniciar o Logstash para começar `sudo systemctl start logstash.service`a ler em registos de fluxo com o seguinte comando: .

### <a name="search-through-graylog-messages"></a>Pesquisar através de mensagens Graylog

Depois de ter dado algum tempo ao seu servidor Graylog para recolher mensagens, é possível pesquisar através das mensagens. Para verificar as mensagens enviadas para o seu servidor Graylog, a partir da página de configuração de **Inputs** clique no botão "**Mostrar recebeu mensagens**" da entrada DALU GELF que criou. Você é direcionado para um ecrã que se parece com a seguinte imagem: 

![Histograma](./media/network-watcher-analyze-nsg-flow-logs-graylog/histogram.png)

Clicar no link azul "%{Message}" expande cada mensagem para mostrar os parâmetros de cada túnica de fluxo, como mostra a seguinte imagem:

![Mensagens](./media/network-watcher-analyze-nsg-flow-logs-graylog/messages.png)

Por predefinição, todos os campos de mensagens estão incluídos na pesquisa se não selecionar um campo de mensagem específico para procurar. Se quiser pesquisar mensagens específicas (ou seja, – fluxo de tuples de uma fonte específica IP) pode utilizar a linguagem de consulta de pesquisa Graylog como [documentado](https://docs.graylog.org/en/2.2/pages/queries.html)

## <a name="analyze-network-security-group-flow-logs-using-graylog"></a>Analisar os registos de fluxo do grupo de segurança da rede usando o Graylog

Agora que o Graylog configurao em funcionamento, pode utilizar algumas das suas funcionalidades para entender melhor os seus dados de registo de fluxo. Uma dessas formas é usando dashboards para criar visões específicas dos seus dados.

### <a name="create-a-dashboard"></a>Create a dashboard (Criar um dashboard)

1. Na barra de navegação superior, selecione **Dashboards** ou navegue para`http://<graylog-server-ip>:9000/dashboards/`

2. A partir daí, clique no botão verde Criar o **dashboard** e preencher o formulário curto com o título e descrição do seu painel de instrumentos. Acerte no botão **Guardar** para criar o novo painel de instrumentos. Vê-se um painel semelhante à seguinte imagem:

    ![Dashboards](./media/network-watcher-analyze-nsg-flow-logs-graylog/dashboards.png)

### <a name="add-widgets"></a>Adicionar widgets

Pode clicar no título do painel de instrumentos para o ver, mas neste momento está vazio, já que não adicionámos widgets. Um widget de tipo fácil e útil para adicionar ao painel de instrumentos são gráficos **Quick Values,** que apresentam uma lista de valores do campo selecionado, e sua distribuição.

1. Navegue de volta para os resultados de pesquisa da entrada UDP que está a receber registos de fluxo selecionando **Search** a partir da barra de navegação superior.

2. Sob o painel de **resultados** da Pesquisa para o lado esquerdo do ecrã, encontre o separador **Campos,** que lista os vários campos de cada mensagem de fluxo de entrada.

3. Selecione qualquer parâmetro desejado para visualizar (neste exemplo, a fonte IP é selecionada). Para mostrar a lista de possíveis widgets, clique na seta azul para baixo à esquerda do campo e, em seguida, selecione **valores Quick** para gerar o widget. Devia ver algo semelhante à seguinte imagem:

   ![IP de origem](./media/network-watcher-analyze-nsg-flow-logs-graylog/srcip.png)

4. A partir daí, pode selecionar o botão Adicionar ao painel de **instrumentos** no canto superior direito do widget e selecionar o painel correspondente para adicionar.

5. Navegue de volta ao tablier para ver o widget que acabou de adicionar.

   Pode adicionar uma variedade de outros widgets, como histogramas e contagens ao seu painel de instrumentos para acompanhar métricas importantes, como o painel de dados da amostra mostrado na imagem seguinte:

   ![Dashboard flowlogs](./media/network-watcher-analyze-nsg-flow-logs-graylog/flowlogs-dashboard.png)

    Para mais explicações sobre os dashboards e os outros tipos de widgets, consulte a [documentação](https://docs.graylog.org/en/2.2/pages/dashboards.html)da Graylog.

Ao integrar o Network Watcher com o Graylog, tem agora uma forma conveniente e centralizada de gerir e visualizar os registos de fluxo do grupo de segurança da rede. Graylog tem uma série de outras funcionalidades poderosas, tais como streams e alertas que também podem ser usados para gerir ainda mais os registos de fluxo e entender melhor o tráfego da sua rede. Agora que tem o Graylog configurado e ligado ao Azure, sinta-se livre para continuar a explorar a outra funcionalidade que oferece.

## <a name="next-steps"></a>Passos seguintes

Saiba como visualizar os registos de fluxo do grupo de segurança da rede com o Power BI, visitando registos de fluxos de fluxos de grupo de [rede Visualize com Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md).
