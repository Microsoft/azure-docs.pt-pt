---
title: Analise os registos de fluxo do grupo de segurança da rede Azure - Graylog Microsoft Docs
description: Aprenda a gerir e analisar os registos de fluxo de grupos de segurança de rede em Azure usando o Network Watcher e o Graylog.
services: network-watcher
documentationcenter: na
author: damendo
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/19/2017
ms.author: damendo
ms.openlocfilehash: 8b363d90d05e95b017c3a655b57dbabc3712a155
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94965549"
---
# <a name="manage-and-analyze-network-security-group-flow-logs-in-azure-using-network-watcher-and-graylog"></a>Gerir e analisar registos de fluxo de grupos de segurança de rede em Azure usando Network Watcher e Graylog

[Os registos de fluxo do grupo de segurança da rede](network-watcher-nsg-flow-logging-overview.md) fornecem informações que pode usar para entender o tráfego IP de entrada e saída para interfaces de rede Azure. Os registos de fluxo mostram fluxos de saída e de entrada numa base de regra de grupo de segurança de rede, a interface de rede a que o fluxo se aplica, informações de 5 tuple (Source/Destination IP, Source/Destination Port, Protocol) sobre o fluxo, e se o tráfego foi permitido ou negado.

Pode ter muitos grupos de segurança de rede na sua rede com registo de fluxos ativados. Vários grupos de segurança de rede com registo de fluxo habilitado podem tornar difícil analisar e obter informações dos seus registos. Este artigo fornece uma solução para gerir centralmente estes registos de fluxo de grupo de segurança de rede usando graylog, uma ferramenta de gestão e análise de registos de código aberto, e Logstash, um pipeline de processamento de dados do lado do servidor de código aberto.

> [!Warning]
> Os passos seguintes funcionam com os registos de fluxo versão 1. Para mais informações, consulte [Introdução à registo de fluxo para grupos de segurança da rede](network-watcher-nsg-flow-logging-overview.md). As seguintes instruções não funcionarão com a versão 2 dos ficheiros de registo, sem modificação.

## <a name="scenario"></a>Cenário

Os registos de fluxo do grupo de segurança da rede são ativados utilizando o Network Watcher. Os registos de fluxo fluem para o armazenamento de bolhas Azure. Um plugin logstash é utilizado para ligar e processar registos de fluxo a partir do armazenamento de bolhas e enviá-los para Graylog. Uma vez que os registos de fluxo são armazenados em Graylog, podem ser analisados e visualizados em dashboards personalizados.

![Fluxo de trabalho graylog](./media/network-watcher-analyze-nsg-flow-logs-graylog/workflow.png)

## <a name="installation-steps"></a>Etapas de Instalação

### <a name="enable-network-security-group-flow-logging"></a>Ativar a exploração de fluxo de grupo de segurança de rede

Para este cenário, deve ter a registo de fluxo de fluxo de grupo de segurança de rede ativada em pelo menos um grupo de segurança de rede na sua conta. Para obter instruções sobre a ativação dos registos de fluxo do grupo de segurança da rede, consulte o seguinte artigo [Introdução à registo de fluxo para grupos de segurança da rede](network-watcher-nsg-flow-logging-overview.md).

### <a name="setting-up-graylog"></a>Criação de Graylog

Neste exemplo, tanto graylog como Logstash são configurados num Servidor Ubuntu 14.04, implantado em Azure.

- Consulte a [documentação](https://docs.graylog.org/en/2.2/pages/installation/os/ubuntu.html) da Graylog, para obter instruções passo a passo sobre como instalar em Ubuntu.
- Certifique-se também de configurar a interface web Graylog seguindo a [documentação](https://docs.graylog.org/en/2.2/pages/configuration/web_interface.html#configuring-webif).

Este exemplo utiliza a configuração mínima graylog (i.e) uma única instância de um Graylog), mas Graylog pode ser projetado para escalar recursos dependendo do seu sistema e necessidades de produção. Para obter mais informações sobre considerações arquitetónicas ou um guia arquitetónico profundo, consulte [a documentação](https://docs.graylog.org/en/2.2/pages/architecture.html) e [o guia arquitetónico](https://www.slideshare.net/Graylog/graylog-engineering-design-your-architecture)de Graylog.

O Graylog pode ser instalado de várias maneiras, dependendo da sua plataforma e preferências. Para obter uma lista completa dos possíveis métodos de instalação, consulte a [documentação](https://docs.graylog.org/en/2.2/pages/installation.html)oficial da Graylog . A aplicação do servidor Graylog funciona nas distribuições do Linux e tem os seguintes pré-requisitos:

-  Java SE 8 ou mais tarde - [Azul Azure JDK documentação](/azure/developer/java/fundamentals/java-jdk-long-term-support)
-  Pesquisa elástica 2.x (2.1.0 ou mais tarde) - [Documentação de instalação de elasticsearch](https://www.elastic.co/guide/en/elasticsearch/reference/2.4/_installation.html)
-  MongoDB 2.4 ou posteriormente - [Documentação de instalação mongoDB](https://docs.mongodb.com/manual/administration/install-on-linux/)

### <a name="install-logstash"></a>Instalar Logstash

O logstash é utilizado para achatar os registos de fluxo formatados JSON para um nível de tuple de fluxo. Aplanar os registos de fluxo torna os troncos mais fáceis de organizar e pesquisar em Graylog.

1. Para instalar o Logstash, execute os seguintes comandos:

   ```bash
   curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
   sudo dpkg -i logstash-5.2.0.deb
   ```

2. Configure o Logstash para analisar os registos de fluxo e enviá-los para Graylog. Criar um ficheiro Logstash.conf:

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
   O ficheiro config logstash fornecido é composto por três partes: a entrada, o filtro e a saída. A secção de entrada designa a fonte de entrada dos registos que o Logstash irá processar – neste caso, irá utilizar um plugin de entrada de blog Azure (instalado nos próximos passos) que nos permite aceder aos ficheiros JSON do fluxo de fluxo do grupo de segurança de rede armazenados no armazenamento de bolhas.

Em seguida, a secção do filtro achata cada ficheiro de registo de fluxo para que cada tuple de fluxo individual e as suas propriedades associadas se tornem um evento separado de Logstash.

Finalmente, a secção de saída encaminha cada evento de Logstash para o servidor Graylog. Para atender às suas necessidades específicas, modifique o ficheiroconfig Logstash, conforme necessário.

   > [!NOTE]
   > O ficheiro configurado anterior pressupõe que o servidor Graylog foi configurado no endereço IP do anfitrião local 127.0.0.1. Caso contrário, certifique-se de que altera o parâmetro do anfitrião na secção de saída para o endereço IP correto.

Para obter mais instruções sobre a instalação do Logstash, consulte a [documentação](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html)do Logstash .

### <a name="install-the-logstash-input-plug-in-for-azure-blob-storage"></a>Instale o plug-in de entrada logstash para armazenamento de bolhas Azure

O plugin Logstash permite-lhe aceder diretamente aos registos de fluxo a partir da sua conta de armazenamento de bolhas designada. Para instalar o plug-in, a partir do diretório de instalação de Logstash predefinido (neste caso /usr/share/logstash/bin), executar o seguinte comando:

```bash
cd /usr/share/logstash/bin
sudo ./logstash-plugin install logstash-input-azureblob
```

Para obter mais informações sobre esta ficha, consulte a [documentação.](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob)

### <a name="set-up-connection-from-logstash-to-graylog"></a>Configurar a ligação de Logstash a Graylog

Agora que estabeleceu uma ligação aos registos de fluxo utilizando o Logstash e criou o servidor Graylog, tem de configurar o Graylog para aceitar os ficheiros de registo de entrada.

1. Navegue para a interface web do Graylog Server utilizando o URL configurado para o mesmo. Pode aceder à interface direcionando o seu navegador para `http://<graylog-server-ip>:9000/`

2. Para navegar na página de configuração, selecione o menu de abandono **do Sistema** na barra de navegação superior à direita e, em seguida, clique em **Entradas**.
   Em alternativa, navegue para `http://<graylog-server-ip>:9000/system/inputs`

   ![Introdução](./media/network-watcher-analyze-nsg-flow-logs-graylog/getting-started.png)

3. Para lançar a nova entrada, selecione *GELF UDP* no drop-down de **entrada Select** e, em seguida, preencha o formulário. GELF significa Formato de Registo Estendido Graylog. O formato GELF é desenvolvido pela Graylog. Para saber mais sobre as suas vantagens, consulte a [documentação](https://docs.graylog.org/en/2.2/pages/gelf.html)graylog.

   Certifique-se de que liga a entrada ao IP em que configura o servidor Graylog. O endereço IP deve corresponder ao campo **anfitrião** da saída UDP do ficheiro de configuração Logstash. A porta predefinido deve ser *12201*. Certifique-se de que a porta corresponde ao campo **de portas** na saída UDP designada no ficheiroconfig logstash.

   ![O Screenshot mostra as Entradas Graylog, com opções para lançar e encontrar entradas.](./media/network-watcher-analyze-nsg-flow-logs-graylog/inputs.png)

   Uma vez lançada a entrada, deve vê-la aparecer na secção **de entradas Local,** como mostra a seguinte imagem:

   ![Screenshot que mostra a secção de entradas local que contém a entrada que lançou.](./media/network-watcher-analyze-nsg-flow-logs-graylog/local-inputs.png)

   Para saber mais sobre as entradas de mensagens Graylog, consulte a [documentação](https://docs.graylog.org/en/2.2/pages/sending_data.html#what-are-graylog-message-inputs).

4. Uma vez feitas estas configurações, pode iniciar o Logstash para começar a ler em registos de fluxo com o seguinte comando: `sudo systemctl start logstash.service` .

### <a name="search-through-graylog-messages"></a>Pesquisar através de mensagens Graylog

Depois de ter dado algum tempo para que o seu servidor Graylog recolhesse mensagens, é capaz de pesquisar através das mensagens. Para verificar as mensagens **enviadas** para o seu servidor Graylog, a partir da página de configuração de Entradas clique no botão "**Mostrar mensagens recebidas**" da entrada GELF UDP que criou. É direcionado para um ecrã que se parece com a seguinte imagem: 

![A screenshot mostra o servidor Graylog que exibe o resultado de Pesquisa, Histograma e Mensagens.](./media/network-watcher-analyze-nsg-flow-logs-graylog/histogram.png)

Clicar no link azul "%{Message}" expande cada mensagem para mostrar os parâmetros de cada tuple de fluxo, como mostra a seguinte imagem:

![A screenshot mostra o detalhe da mensagem do servidor Graylog.](./media/network-watcher-analyze-nsg-flow-logs-graylog/messages.png)

Por predefinição, todos os campos de mensagens estão incluídos na pesquisa se não selecionar um campo de mensagens específico para procurar. Se quiser procurar mensagens específicas (i.e) – os tuples de fluxo a partir de um IP de origem específica) você pode usar o idioma de consulta de pesquisa Graylog como [documentado](https://docs.graylog.org/en/2.2/pages/queries.html)

## <a name="analyze-network-security-group-flow-logs-using-graylog"></a>Analise os registos de fluxo do grupo de segurança da rede usando o Graylog

Agora que o Graylog se instalou em funcionamento, pode utilizar algumas das suas funcionalidades para entender melhor os seus dados de registo de fluxo. Uma dessas formas é utilizar dashboards para criar visões específicas dos seus dados.

### <a name="create-a-dashboard"></a>Criar um dashboard

1. Na barra de navegação superior, selecione **Dashboards** ou navegue para `http://<graylog-server-ip>:9000/dashboards/`

2. A partir daí, clique no botão verde **Criar painel de instrumentos** e preencha o formulário curto com o título e descrição do seu painel de instrumentos. Assem no botão **Guardar** para criar o novo painel de instrumentos. Vê-se um painel semelhante à seguinte imagem:

    ![O Screenshot mostra os dashboards do servidor Graylog, com as opções para criar e editar dashboards.](./media/network-watcher-analyze-nsg-flow-logs-graylog/dashboards.png)

### <a name="add-widgets"></a>Adicionar widgets

Pode clicar no título do painel para vê-lo, mas neste momento está vazio, uma vez que não adicionámos widgets. Um widget tipo fácil e útil para adicionar ao dashboard são **gráficos quick values,** que apresentam uma lista de valores do campo selecionado, e sua distribuição.

1. Volte para os resultados de pesquisa da entrada UDP que está a receber registos de fluxo selecionando **Search** a partir da barra de navegação superior.

2. No painel de **resultados** da Pesquisa para o lado esquerdo do ecrã, encontre o **separador Campos,** que lista os vários campos de cada mensagem de tuple de fluxo de entrada.

3. Selecione qualquer parâmetro desejado para visualizar (neste exemplo, a fonte IP é selecionada). Para mostrar a lista de widgets possíveis, clique na seta azul para baixo à esquerda do campo e, em seguida, selecione **Valores Rápidos** para gerar o widget. Deve ver algo semelhante à seguinte imagem:

   ![IP de origem](./media/network-watcher-analyze-nsg-flow-logs-graylog/srcip.png)

4. A partir daí, pode selecionar o botão Adicionar ao painel de **instrumentos** no canto superior direito do widget e selecionar o painel de instrumentos correspondente para adicionar.

5. Volte para o painel de instrumentos para ver o widget que acabou de adicionar.

   Pode adicionar uma variedade de outros widgets, tais como histogramas e conta ao seu painel de instrumentos para acompanhar métricas importantes, como o painel de amostras mostrado na seguinte imagem:

   ![Painel de fluxologs](./media/network-watcher-analyze-nsg-flow-logs-graylog/flowlogs-dashboard.png)

    Para obter mais informações sobre os dashboards e os outros tipos de widgets, consulte a [documentação](https://docs.graylog.org/en/2.2/pages/dashboards.html)da Graylog .

Ao integrar o Network Watcher com o Graylog, tem agora uma forma conveniente e centralizada de gerir e visualizar os registos de fluxo de grupos de segurança de rede. Graylog tem uma série de outras funcionalidades poderosas, tais como streams e alertas que também podem ser usados para gerir ainda mais os registos de fluxo e entender melhor o tráfego da sua rede. Agora que tem o Graylog configurado e ligado ao Azure, sinta-se à vontade para continuar a explorar a outra funcionalidade que oferece.

## <a name="next-steps"></a>Passos seguintes

Saiba como visualizar os registos de fluxo do seu grupo de segurança de rede com o Power BI visitando [os fluxos de fluxos de grupo de segurança de rede visualize com Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md).