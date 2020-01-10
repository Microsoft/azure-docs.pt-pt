---
title: Coletar e analisar contadores de desempenho no Azure Monitor | Microsoft Docs
description: Os contadores de desempenho são coletados pelo Azure Monitor para analisar o desempenho em agentes do Windows e do Linux.  Este artigo descreve como configurar a coleta de contadores de desempenho para agentes do Windows e do Linux, os detalhes deles são armazenados no espaço de trabalho e como analisá-los no portal do Azure.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/28/2018
ms.openlocfilehash: 624996c86423bf486111fde8743117ea888862e7
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75363834"
---
# <a name="windows-and-linux-performance-data-sources-in-azure-monitor"></a>Fontes de dados de desempenho do Windows e do Linux no Azure Monitor
Os contadores de desempenho no Windows e no Linux fornecem informações sobre o desempenho de componentes de hardware, sistemas operacionais e aplicativos.  Azure Monitor pode coletar contadores de desempenho em intervalos frequentes para análise NRT (quase em tempo real), além de agregar dados de desempenho para análise e relatórios de longo prazo.

![Contadores de desempenho](media/data-sources-performance-counters/overview.png)

## <a name="configuring-performance-counters"></a>Configurando contadores de desempenho
Configure contadores de desempenho no [menu dados em configurações avançadas](agent-data-sources.md#configuring-data-sources).

Ao configurar os contadores de desempenho do Windows ou do Linux pela primeira vez para um novo espaço de trabalho, você terá a opção de criar rapidamente vários contadores comuns.  São listados com uma caixa de verificação junto a cada um.  Verifique se todos os contadores que você deseja criar inicialmente estão marcados e clique em **adicionar os contadores de desempenho selecionados**.

Para contadores de desempenho do Windows, você pode escolher uma instância específica para cada contador de desempenho. Para contadores de desempenho do Linux, a instância de cada contador que você escolhe aplica-se a todos os contadores filho do contador pai. A tabela a seguir mostra as instâncias comuns disponíveis para os contadores de desempenho do Linux e do Windows.

| Nome da Instância | Descrição |
| --- | --- |
| Total de \_ |Total de todas as instâncias |
| \* |Todas as instâncias |
| (/&#124;/var) |Corresponde a instâncias nomeadas:/ou/var |

### <a name="windows-performance-counters"></a>Contadores de desempenho do Windows

![Configurar contadores de desempenho do Windows](media/data-sources-performance-counters/configure-windows.png)

Siga este procedimento para adicionar um novo contador de desempenho do Windows a ser coletado.

1. Digite o nome do contador na caixa de texto no formato *objeto (instância) \counter*.  Quando você começar a digitar, verá uma lista correspondente de contadores comuns.  Você pode selecionar um contador na lista ou digitar um do seu próprio.  Você também pode retornar todas as instâncias para um determinado contador especificando *objeto\contador*.  

    Ao coletar SQL Server contadores de desempenho de instâncias nomeadas, todos os contadores de instância nomeados começam com *MSSQL $* e seguidos pelo nome da instância.  Por exemplo, para coletar o contador de taxa de acertos do cache de log para todos os bancos de dados do objeto de desempenho do Database para a instância SQL nomeada INST2, especifique `MSSQL$INST2:Databases(*)\Log Cache Hit Ratio`.

2. Clique em **+** ou pressione **Enter** para adicionar o contador à lista.
3. Quando você adiciona um contador, ele usa o padrão de 10 segundos para seu **intervalo de amostragem**.  Você pode alterar isso para um valor mais alto de até 1800 segundos (30 minutos) se desejar reduzir os requisitos de armazenamento dos dados de desempenho coletados.
4. Quando você terminar de adicionar contadores, clique no botão **salvar** na parte superior da tela para salvar a configuração.

### <a name="linux-performance-counters"></a>Contadores de desempenho do Linux

![Configurar contadores de desempenho do Linux](media/data-sources-performance-counters/configure-linux.png)

Siga este procedimento para adicionar um novo contador de desempenho do Linux a ser coletado.

1. Por predefinição, todas as alterações de configuração são automaticamente enviados por push para todos os agentes.  Para agentes do Linux, um arquivo de configuração é enviado para o coletor de dados Fluentd.  Se você quiser modificar esse arquivo manualmente em cada agente do Linux, desmarque a caixa *aplicar configuração abaixo a meus computadores Linux* e siga as orientações abaixo.
2. Digite o nome do contador na caixa de texto no formato *objeto (instância) \counter*.  Quando você começar a digitar, verá uma lista correspondente de contadores comuns.  Você pode selecionar um contador na lista ou digitar um do seu próprio.  
3. Clique em **+** ou pressione **Enter** para adicionar o contador à lista de outros contadores para o objeto.
4. Todos os contadores de um objeto usam o mesmo **intervalo de amostragem**.  A predefinição é 10 segundos.  Altere isso para um valor mais alto de até 1800 segundos (30 minutos) se você quiser reduzir os requisitos de armazenamento dos dados de desempenho coletados.
5. Quando você terminar de adicionar contadores, clique no botão **salvar** na parte superior da tela para salvar a configuração.

#### <a name="configure-linux-performance-counters-in-configuration-file"></a>Configurar contadores de desempenho do Linux no arquivo de configuração
Em vez de configurar contadores de desempenho do Linux usando o portal do Azure, você tem a opção de editar arquivos de configuração no agente do Linux.  As métricas de desempenho a serem coletadas são controladas pela configuração em **/etc/opt/microsoft/omsagent/\<ID do espaço de trabalho\>/conf/omsagent.conf**.

Cada objeto, ou categoria, das métricas de desempenho a serem coletadas deve ser definido no arquivo de configuração como um único elemento de `<source>`. A sintaxe segue o padrão abaixo.

    <source>
      type oms_omi  
      object_name "Processor"
      instance_regex ".*"
      counter_name_regex ".*"
      interval 30s
    </source>


Os parâmetros neste elemento são descritos na tabela a seguir.

| Parâmetros | Descrição |
|:--|:--|
| nome do\_de objeto | Nome do objeto da coleção. |
| Regex de\_de instância |  Uma *expressão regular* que define quais instâncias coletar. O valor: `.*` especifica todas as instâncias. Para coletar métricas do processador somente para a instância total do \_, você pode especificar `_Total`. Para coletar métricas de processo somente para as instâncias crond ou sshd, você pode especificar: `(crond\|sshd)`. |
| counter\_name\_regex | Uma *expressão regular* que define os contadores (para o objeto) a serem coletados. Para coletar todos os contadores do objeto, especifique: `.*`. Para coletar somente contadores de espaço de permuta para o objeto de memória, por exemplo, você pode especificar: `.+Swap.+` |
| intervalo | Frequência em que os contadores do objeto são coletados. |


A tabela a seguir lista os objetos e contadores que você pode especificar no arquivo de configuração.  Há contadores adicionais disponíveis para determinados aplicativos, conforme descrito em [coletar contadores de desempenho para aplicativos do Linux em Azure monitor](data-sources-linux-applications.md).

| Nome do Objeto | Nome do contador |
|:--|:--|
| Disco lógico | % De inodes livres |
| Disco lógico | % De espaço livre |
| Disco lógico | % De inodes usados |
| Disco lógico | % De espaço utilizado |
| Disco lógico | Bytes Lidos de Disco/seg |
| Disco lógico | Leituras de disco/seg |
| Disco lógico | As transferências de disco/seg |
| Disco lógico | Bytes Escritos em Disco/seg |
| Disco lógico | Escritas de disco/seg |
| Disco lógico | Megabytes livres |
| Disco lógico | Bytes de disco lógico/seg |
| Memória | % De memória disponível |
| Memória | % De espaço de permuta disponível |
| Memória | % De memória usada |
| Memória | % De espaço de permuta utilizado |
| Memória | MBytes de memória disponíveis |
| Memória | Troca de MBytes disponíveis |
| Memória | Leituras de Paginações/seg |
| Memória | Escritas de Paginações/seg |
| Memória | Páginas/seg |
| Memória | Espaço de permuta usado em Mbytes |
| Memória | Mbytes de memória usados |
| Rede | Total de Bytes transmitidos |
| Rede | Total de Bytes recebidos |
| Rede | Total de bytes |
| Rede | Total de pacotes transmitidos |
| Rede | Total de pacotes recebidos |
| Rede | Total de erros de RX |
| Rede | Total de erros TX |
| Rede | Total de colisões |
| Disco Físico | Média de disco s/leitura |
| Disco Físico | Média de disco s/transferência |
| Disco Físico | Média de disco s/gravação |
| Disco Físico | Bytes de disco físico/s |
| Processo | Percentual de tempo privilegiado |
| Processo | Tempo de usuário do PCT |
| Processo | KBytes de memória usada |
| Processo | Memória compartilhada virtual |
| Processador | % De tempo de DPC |
| Processador | % de Tempo Inativo |
| Processador | % De tempo de interrupção |
| Processador | % De tempo de espera de e/s |
| Processador | % De tempo adequado |
| Processador | % De tempo privilegiado |
| Processador | % Tempo do processador |
| Processador | % De tempo do usuário |
| Sistema | Memória física livre |
| Sistema | Espaço livre em arquivos de paginação |
| Sistema | Memória virtual livre |
| Sistema | Processos |
| Sistema | Tamanho armazenado em arquivos de paginação |
| Sistema | Tempo de atividade |
| Sistema | Utilizadores |


A seguir está a configuração padrão para métricas de desempenho.

    <source>
      type oms_omi
      object_name "Physical Disk"
      instance_regex ".*"
      counter_name_regex ".*"
      interval 5m
    </source>

    <source>
      type oms_omi
      object_name "Logical Disk"
      instance_regex ".*
      counter_name_regex ".*"
      interval 5m
    </source>

    <source>
      type oms_omi
      object_name "Processor"
      instance_regex ".*
      counter_name_regex ".*"
      interval 30s
    </source>

    <source>
      type oms_omi
      object_name "Memory"
      instance_regex ".*"
      counter_name_regex ".*"
      interval 30s
    </source>

## <a name="data-collection"></a>Recolha de dados
Azure Monitor coleta todos os contadores de desempenho especificados em seu intervalo de amostragem especificado em todos os agentes que têm esse contador instalado.  Os dados não são agregados e os dados brutos estão disponíveis em todas as exibições de consulta de log durante a duração especificada pela sua assinatura.

## <a name="performance-record-properties"></a>Propriedades do registro de desempenho
Os registros de desempenho têm um **tipo de desempenho** e têm as propriedades na tabela a seguir.

| Propriedade | Descrição |
|:--- |:--- |
| Computador |Computador que o evento foi recolhido a partir de. |
| CounterName |Nome do contador de desempenho |
| CounterPath |Caminho completo do contador no formulário \\\\\<computador >\\objeto (instância)\\contador. |
| CounterValue |Valor numérico do contador. |
| InstanceName |Nome da instância do evento.  Vazio se não houver nenhuma instância. |
| ObjectName |Nome do objeto de desempenho |
| SourceSystem |Tipo de agente do qual os dados foram coletados. <br><br>OpsManager – agente do Windows, conexão direta ou SCOM <br> Linux – todos os agentes do Linux  <br> AzureStorage – Diagnóstico do Azure |
| TimeGenerated |Data e hora em que os dados foram amostrados. |

## <a name="sizing-estimates"></a>Estimativas de dimensionamento
 Uma estimativa aproximada para a coleta de um determinado contador em intervalos de 10 segundos é de cerca de 1 MB por dia por instância.  Você pode estimar os requisitos de armazenamento de um determinado contador com a fórmula a seguir.

    1 MB x (number of counters) x (number of agents) x (number of instances)

## <a name="log-queries-with-performance-records"></a>Registrar consultas com registros de desempenho
A tabela a seguir fornece diferentes exemplos de consultas de log que recuperam registros de desempenho.

| Consulta | Descrição |
|:--- |:--- |
| Desempenho |Todos os dados de desempenho |
| Perf &#124; , em que Computer = = "MyComputer" |Todos os dados de desempenho de um computador específico |
| Desempenho &#124; em que CounterName = = "comprimento da fila de disco atual" |Todos os dados de desempenho de um determinado contador |
| Perf &#124; , em que ObjectName = = "Processor" e CounterName = = "% Processor Time" e InstanceName = = " &#124; _Total" resume AVGCPU = AVG (Comvalue) por computador |Utilização média da CPU em todos os computadores |
| Desempenho &#124; em que CounterName = = "% Processor Time &#124; " resume AggregatedValue = Max (Comvalue) por computador |Utilização máxima da CPU em todos os computadores |
| Perf &#124; , em que ObjectName = = "LogicalDisk" e CounterName = = "comprimento atual da fila do disco" e computador = = " &#124; mycomputername" resume AggregatedValue = AVG (Comvalue) por InstanceName |Comprimento médio da fila de disco atual em todas as instâncias de um determinado computador |
| Desempenho &#124; em que CounterName = = "transferências de disco/ &#124; s" resume AggregatedValue = percentil (comvalue, 95) por computador |95 º percentil de transferências de disco/s em todos os computadores |
| Desempenho &#124; em que CounterName = = "% Processor Time" e InstanceName = = "_Total &#124; " resume AggregatedValue = AVG (comvalue) por bin (TimeGenerated, 1h), Computer |Média horária de uso da CPU em todos os computadores |
| Perf &#124; , em que Computer = = "MyComputer" e CounterName startswith_cs "%" e InstanceName = = &#124; "_Total" resume AggregatedValue = percentil (myValue, 70) por bin (TimeGenerated, 1h), CounterName | A cada hora 70 percentil de cada contador de% por cento para um determinado computador |
| Perf &#124; = = "% Processor Time" e InstanceName = = "_Total" e Computer = = "MyComputer" &#124; resume ["min (myValue)"] = min (comvalue), ["AVG (comvalue)"] = AVG (comvalue), ["percentile75 (myValue)"] = percentil (comvalue, 75), ["Max (comvalue)"] = Max (comvalue) por bin (TimeGenerated, 1h), Computer |Média por hora, mínimo, máximo e 75-percentil de uso da CPU para um computador específico |
| Perf &#124; , em que ObjectName = = "MSSQL $ INST2: Databases" e InstanceName = = "Master" | Todos os dados de desempenho do objeto de desempenho Database para o banco de dados mestre da instância SQL Server nomeada INST2.  




## <a name="next-steps"></a>Passos seguintes
* [Colete contadores de desempenho de aplicativos Linux](data-sources-linux-applications.md) , incluindo MySQL e Apache HTTP Server.
* Saiba mais sobre [registar as consultas](../log-query/log-query-overview.md) para analisar os dados recolhidos a partir de origens de dados e soluções.  
* Exporte os dados coletados para [Power bi](powerbi.md) para análise e visualizações adicionais.
