---
title: Recolher fontes de dados de desempenho do Windows e do Linux com o agente Log Analytics no Azure Monitor
description: Os contadores de desempenho são recolhidos pelo Azure Monitor para analisar o desempenho nos agentes Windows e Linux.  Este artigo descreve como configurar a recolha de contadores de desempenho para agentes Windows e Linux, detalhes dos mesmos armazenados no espaço de trabalho e como analisá-los no portal Azure.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/21/2020
ms.openlocfilehash: 533d4a83ea73b98e26a57febc077a607bcb25465
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96532312"
---
# <a name="collect-windows-and-linux-performance-data-sources-with-log-analytics-agent"></a>Recolher fontes de dados de desempenho do Windows e do Linux com o agente Log Analytics
Os contadores de desempenho no Windows e linux fornecem informações sobre o desempenho de componentes de hardware, sistemas operativos e aplicações.  O Azure Monitor pode recolher contadores de desempenho de agentes do Log Analytics em intervalos frequentes para análises de Tempo Real Próximo (NRT), além de agregar dados de desempenho para análise e reporte a longo prazo.

> [!IMPORTANT]
> Este artigo abrange a recolha de dados de desempenho com o [agente Log Analytics,](log-analytics-agent.md) que é um dos agentes utilizados pelo Azure Monitor. Outros agentes recolhem dados diferentes e são configurados de forma diferente. Consulte [a visão geral dos agentes do Azure Monitor](agents-overview.md) para obter uma lista dos agentes disponíveis e os dados que podem recolher.

![Contadores de desempenho](media/data-sources-performance-counters/overview.png)

## <a name="configuring-performance-counters"></a>Configurar contadores de desempenho
Configure os contadores de desempenho do [menu De dados em Definições Avançadas](agent-data-sources.md#configuring-data-sources) para o espaço de trabalho Do Log Analytics.

Quando configurar pela primeira vez os contadores Windows ou Linux Performance para um novo espaço de trabalho, é-lhe dada a opção de criar rapidamente vários contadores comuns.  São listados com uma caixa de verificação junto a cada um.  Certifique-se de que quaisquer contadores que pretende criar são verificados e, em seguida, clique em **Adicionar os contadores de desempenho selecionados**.

Para os contadores de desempenho do Windows, pode escolher uma instância específica para cada contador de desempenho. Para os contadores de desempenho do Linux, a instância de cada contador que escolher aplica-se a todos os contadores de crianças do contador dos pais. A tabela a seguir mostra as instâncias comuns disponíveis tanto para os contadores de desempenho Linux como para o Windows.

| Nome da instância | Descrição |
| --- | --- |
| \_Total |Total de todas as instâncias |
| \* |Todas as instâncias |
| (/&#124;/var) |Corresponde a instâncias nomeadas: / ou /var |

### <a name="windows-performance-counters"></a>Contadores de desempenho do Windows

![Configure balcões de desempenho do Windows](media/data-sources-performance-counters/configure-windows.png)

Siga este procedimento para adicionar um novo contador de desempenho do Windows para recolher. Por favor, note que os contadores de desempenho V2 Windows não são suportados.

1. Digite o nome do contador na caixa de texto no *objeto de formato(instância)\contador*.  Quando começa a escrever, é-lhe apresentada uma lista de contadores comuns.  Pode selecionar um contador da lista ou escrever num dos seus.  Também pode devolver todas as instâncias para um determinado contador especificando *o contador de objetos.*  

    Ao recolher contadores de desempenho do SQL Server a partir de instâncias nomeadas, todos os contadores de instâncias nomeados começam com *MSSQL$* e seguidos pelo nome da instância.  Por exemplo, para recolher o contador de registos Cache Hit Ratio para todas as bases de dados do objeto de desempenho da Base de Dados para o chamado SQL instância INST2, especifique `MSSQL$INST2:Databases(*)\Log Cache Hit Ratio` .

2. Clique **+** ou prima **Enter** para adicionar o contador à lista.
3. Quando adiciona um contador, utiliza o padrão de 10 segundos para o intervalo **de amostra .**  Pode alterá-lo para um valor mais elevado até 1800 segundos (30 minutos) se quiser reduzir os requisitos de armazenamento dos dados de desempenho recolhidos.
4. Quando terminar de adicionar contadores, clique no botão **Guardar** na parte superior do ecrã para guardar a configuração.

### <a name="linux-performance-counters"></a>Balcões de desempenho linux

![Configures balcões de desempenho linux](media/data-sources-performance-counters/configure-linux-1.png)

Siga este procedimento para adicionar um novo contador de desempenho Linux para recolher.

1. Digite o nome do contador na caixa de texto no *objeto de formato(instância)\contador*.  Quando começa a escrever, é-lhe apresentada uma lista de contadores comuns.  Pode selecionar um contador da lista ou escrever num dos seus.  
1. Clique **+** ou prima **Enter** para adicionar o contador à lista de outros contadores para o objeto.
1. Todos os contadores para um objeto usam o mesmo **Intervalo de Amostra.**  A predefinição é de 10 segundos.  Altere isto para um valor mais elevado de até 1800 segundos (30 minutos) se quiser reduzir os requisitos de armazenamento dos dados de desempenho recolhidos.
1. Quando terminar de adicionar contadores, clique no botão **Guardar** na parte superior do ecrã para guardar a configuração.

#### <a name="configure-linux-performance-counters-in-configuration-file"></a>Configurar contadores de desempenho do Linux no ficheiro de configuração
Em vez de configurar os contadores de desempenho do Linux utilizando o portal Azure, tem a opção de editar ficheiros de configuração no agente Linux.  As métricas de desempenho a recolher são controladas pela configuração em **/etc/opt/microsoft/omsagent/ \<workspace id\> /conf/omsagent.conf**.

Cada objeto, ou categoria, das métricas de desempenho a recolher deve ser definido no ficheiro de configuração como um único `<source>` elemento. A sintaxe segue o padrão abaixo.

```xml
<source>
    type oms_omi  
    object_name "Processor"
    instance_regex ".*"
    counter_name_regex ".*"
    interval 30s
</source>
```


Os parâmetros deste elemento são descritos na tabela seguinte.

| Parâmetros | Descrição |
|:--|:--|
| \_nome objeto | Nome do objeto para a coleção. |
| instância \_ regex |  Uma *expressão regular* que define quais as instâncias a recolher. O valor: `.*` especifica todas as instâncias. Para recolher as métricas do processador apenas para a \_ instância Total, pode especificar `_Total` . Para recolher métricas de processo apenas para as instâncias crond ou sshd, pode especificar: `(crond\|sshd)` . |
| contador \_ nome \_ regex | Uma *expressão regular* que define quais os contadores (para o objeto) a recolher. Para recolher todos os contadores para o objeto, especifique: `.*` . Para recolher apenas contadores de espaço para o objeto de memória, por exemplo, pode especificar: `.+Swap.+` |
| interval | Frequência na qual os contadores do objeto são recolhidos. |


A tabela que se segue lista os objetos e contadores que pode especificar no ficheiro de configuração.  Existem contadores adicionais disponíveis para determinadas aplicações, conforme descrito nos [balcões de desempenho da Collect para aplicações Linux no Azure Monitor](data-sources-linux-applications.md).

| Nome do objeto | Nome do Contador |
|:--|:--|
| Disco lógico | % Inodes grátis |
| Disco lógico | % Espaço Livre |
| Disco lógico | % Inodes usados |
| Disco lógico | % Espaço Usado |
| Disco lógico | Discos ler Bytes/seg |
| Disco lógico | Leituras/seg de disco |
| Disco lógico | Transferências de discos/seg |
| Disco lógico | Discos De Escrita Bytes/seg |
| Disco lógico | Escritas/seg de disco |
| Disco lógico | Megabytes grátis |
| Disco lógico | Bytes/seg de disco lógico |
| Memória | % Memória Disponível |
| Memória | % Espaço de troca disponível |
| Memória | % Memória Usada |
| Memória | % Espaço de troca usado |
| Memória | Memória MBytes disponível |
| Memória | Troca de MBytes disponível |
| Memória | Leituras de página/seg |
| Memória | Escritas/seg de página |
| Memória | Páginas/seg |
| Memória | Espaço de troca de MBytes usado |
| Memória | MBytes de memória usados |
| Rede | Total de bytes transmitidos |
| Rede | Total de bytes recebidos |
| Rede | Total Bytes |
| Rede | Total de pacotes transmitidos |
| Rede | Total de pacotes recebidos |
| Rede | Erros Rx totais |
| Rede | Erros Tx totais |
| Rede | Colisões totais |
| Disco Físico | Avg. Disco seg/Ler |
| Disco Físico | Avg. Disco seg/Transferência |
| Disco Físico | Avg. Disco seg/Write |
| Disco Físico | Bytes/seg de disco físico |
| Processo | Pct Tempo Privilegiado |
| Processo | Tempo de utilizador do Pct |
| Processo | KBytes de Memória Usados |
| Processo | Memória partilhada virtual |
| Processador | % tempo DPC |
| Processador | % de Tempo Inativo |
| Processador | % Tempo de interrupção |
| Processador | % IO Tempo de Espera |
| Processador | % Tempo agradável |
| Processador | % Tempo Privilegiado |
| Processador | % de Tempo do Processador |
| Processador | % Tempo de Utilização |
| Sistema | Memória Física Gratuita |
| Sistema | Espaço gratuito em ficheiros de paging |
| Sistema | Memória Virtual Gratuita |
| Sistema | Processos |
| Sistema | Tamanho armazenado em ficheiros de paging |
| Sistema | Tempo de atividade |
| Sistema | Utilizadores |


Segue-se a configuração padrão para métricas de desempenho.

```xml
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
    instance_regex ".*"
    counter_name_regex ".*"
    interval 5m
</source>

<source>
    type oms_omi
    object_name "Processor"
    instance_regex ".*"
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
```

## <a name="data-collection"></a>Recolha de dados
O Azure Monitor recolhe todos os contadores de desempenho especificados no intervalo de amostra especificado em todos os agentes que tenham o contador instalado.  Os dados não são agregados, e os dados brutos estão disponíveis em todas as vistas de consulta de registo para a duração especificada pelo seu espaço de trabalho de análise de log.

## <a name="performance-record-properties"></a>Propriedades de registo de desempenho
Os registos de desempenho têm um tipo de **Perf** e têm as propriedades na tabela seguinte.

| Propriedade | Descrição |
|:--- |:--- |
| Computador |Computador de onde o evento foi recolhido. |
| Contra-Nome |Nome do balcão de performance |
| CounterPath |Percurso completo do contador no contador de \\ \\ \<Computer> \\ objetos de \\ formulário(instância). |
| ContraValue |Valor numérico do balcão. |
| Nome de exemplo |O nome da instância do evento.  Vazio, se não houver caso. |
| ObjectName |Nome do objeto de desempenho |
| SourceSystem |Tipo de agente de onde os dados foram recolhidos. <br><br>OpsManager - Agente Windows, ou ligação direta ou SCOM <br> Linux - Todos os agentes Linux  <br> AzureStorage – Azure Diagnostics |
| TimeGenerated |Data e hora os dados foram amostrados. |

## <a name="sizing-estimates"></a>Estimativas de dimensionamento
 Uma estimativa aproximada para a recolha de um determinado contador em intervalos de 10 segundos é de cerca de 1 MB por dia por exemplo.  Pode estimar os requisitos de armazenamento de um determinado contador com a seguinte fórmula.

> 1 MB x (número de contadores) x (número de agentes) x (número de instâncias)

## <a name="log-queries-with-performance-records"></a>Consultas de registo com registos de desempenho
A tabela seguinte fornece diferentes exemplos de consultas de registo que recuperam registos de desempenho.

| Consulta | Descrição |
|:--- |:--- |
| Des |Todos os dados de desempenho |
| Perf &#124; onde computador == "MyComputer" |Todos os dados de desempenho de um determinado computador |
| Perf &#124; onde contra-Natal == "Comprimento atual da fila do disco" |Todos os dados de desempenho de um balcão específico |
| Perf &#124; onde ObjectName == "Processador" e CounterName == "% Tempo do processador" e Nome de InstânciaName == "_Total" &#124; resumir AVGCPU = avg (CounterValue) por Computador |Utilização média do CPU em todos os computadores |
| Perf &#124; onde o CounterName == "% Tempo do processador" &#124; resumir AgregadValue = max (CounterValue) por Computador |Utilização máxima do CPU em todos os computadores |
| Perf &#124; onde ObjectName == "LogicalDisk" e CounterName == "Atual comprimento da fila do disco" e computador == "MyComputerName" &#124; resumir AggregatedValue = avg (CounterValue) by InstanceName |Comprimento médio da fila do disco corrente em todos os casos de um determinado computador |
| Perf &#124; onde o ContraName == "Transferências de Disco/seg" &#124; resumir AgregadoValue = percentil (CounterValue, 95) por Computador |Percentil 95 de transferências de disco/sec em todos os computadores |
| Perf &#124; onde o ContraName == "% Tempo do Processador" e "InstanceName= "_Total" &#124; resumir AgregadodValue = avg (CounterValue) por bin (TimeGenerated, 1h), Computador |Média horária de utilização do CPU em todos os computadores |
| Perf &#124; onde o Computador == "MyComputer" e CounterName startswith_cs "%" e InstanceName == "_Total" &#124; resumir AgregadoValue = percentil (CounterValue, 70) by bin (TimeGenerated, 1h), CounterName | Por hora 70 percentil de cada % contador por cento para um determinado computador |
| Perf &#124; onde o Contra-Natal == "% Tempo do Processador" e "InstanceName= "_Total" e "Computador= "MyComputer" &#124; resumir ["min(CounterValue)"] = min(CounterValue), ["avg(CounterValue)"] = avg (CounterValue), ["percentile75 (CounterValue)"] = 75), ["max(CounterValue)"] = max (CounterValue) por bin (TimeGenerated, 1h), Computador |Média horária, mínima, máxima e utilização de CPU de 75% para um computador específico |
| Perf &#124; onde ObjectName == "MSSQL$INST2:Bases de dados" e ExemploName == "mestre" | Todos os dados de desempenho do objeto de desempenho da base de dados para a base de dados principal a partir da chamada SQL Server instance INST2.  




## <a name="next-steps"></a>Passos seguintes
* [Recolha contadores de desempenho a partir de aplicações Linux,](data-sources-linux-applications.md) incluindo MySQL e Apache HTTP Server.
* Saiba mais [sobre consultas de registo](../log-query/log-query-overview.md) para analisar os dados recolhidos a partir de fontes de dados e soluções.  
* Exportação de dados recolhidos para [Power BI](powerbi.md) para visualizações e análises adicionais.
