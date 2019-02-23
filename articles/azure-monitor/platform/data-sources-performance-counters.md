---
title: Recolher e analisar os contadores de desempenho no Azure Monitor | Documentos da Microsoft
description: Contadores de desempenho são recolhidos pelo Azure Monitor para analisar o desempenho em agentes Windows e Linux.  Este artigo descreve como configurar a recolha de contadores de desempenho para ambos os Windows e agentes do Linux, detalhes de que eles são armazenados na área de trabalho e como analisá-los no portal do Azure.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: 20e145e4-2ace-4cd9-b252-71fb4f94099e
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2018
ms.author: magoedte
ms.openlocfilehash: 93f47529e3be44ff1db4e089bdcdca3eb1b4dea3
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56728357"
---
# <a name="windows-and-linux-performance-data-sources-in-azure-monitor"></a>Windows e Linux origens de dados de desempenho no Azure Monitor
Contadores de desempenho no Windows e no Linux fornecem informações sobre o desempenho de componentes de hardware, sistemas operacionais e aplicativos.  O Azure Monitor pode recolher contadores de desempenho em intervalos frequentes para análise quase em Tempo Real (NRT), além de agregar dados de desempenho para análise de termo mais tempo e relatórios.

![Contadores de desempenho](media/data-sources-performance-counters/overview.png)

## <a name="configuring-performance-counters"></a>Configurar contadores de desempenho
Configurar contadores de desempenho a partir do [menu de dados nas definições avançadas](agent-data-sources.md#configuring-data-sources).

Quando configurar os contadores de Windows ou o desempenho do Linux para uma nova área de trabalho em primeiro lugar, é-lhe dada a opção para criar rapidamente vários contadores comuns.  São listados com uma caixa de verificação junto a cada um.  Certifique-se de que quaisquer contadores que pretende criar inicialmente são verificados e, em seguida, clique em **adicionar os contadores de desempenho selecionados**.

Para contadores de desempenho do Windows, pode escolher uma instância específica de cada contador de desempenho. Para contadores de desempenho do Linux, a instância de cada contador que escolher aplica-se a todos os contadores de subordinados do contador principal. A tabela seguinte mostra as instâncias comuns disponíveis para os contadores de desempenho do Linux e Windows.

| Nome da Instância | Descrição |
| --- | --- |
| \_Total |Total de todas as instâncias |
| \* |Todas as instâncias |
| (/&#124;/var) |Corresponde a instâncias com o nome: / ou /var |

### <a name="windows-performance-counters"></a>Contadores de desempenho do Windows

![Configurar os contadores de desempenho do Windows](media/data-sources-performance-counters/configure-windows.png)

Siga este procedimento para adicionar um novo contador de desempenho do Windows para recolher.

1. Escreva o nome do contador na caixa de texto no formato *objeto (instância) \counter*.  Quando começa a digitar, é apresentada uma lista de correspondência de contadores comuns.  Pode selecionar um contador da lista ou tipo em seu próprio dashboard.  Também podem retornar todas as instâncias de um contador específico, especificando *object\counter*.  

    Quando a recolha de contadores de desempenho do SQL Server a partir de instâncias nomeadas, tudo com o nome início de contadores de instância com *MSSQL$* e seguido do nome da instância.  Por exemplo, para recolher o contador da taxa de acessos de Cache em Log todas as bases de dados do objeto de desempenho da base de dados para o SQL com o nome a instância INST2, especificar `MSSQL$INST2:Databases(*)\Log Cache Hit Ratio`.

2. Clique em **+** ou prima **Enter** para adicionar o contador à lista.
3. Quando adiciona um contador, ele usa o padrão de 10 segundos para seus **intervalo de amostragem**.  Pode alterar isto para um valor mais alto de até como 1800 segundos (30 minutos) para reduzir os requisitos de armazenamento dos dados de desempenho coletados.
4. Quando tiver terminado a adição de contadores, clique nas **guardar** botão na parte superior do ecrã, para guardar a configuração.

### <a name="linux-performance-counters"></a>Contadores de desempenho do Linux

![Configurar os contadores de desempenho do Linux](media/data-sources-performance-counters/configure-linux.png)

Siga este procedimento para adicionar um novo contador de desempenho do Linux para recolher.

1. Por predefinição, todas as alterações de configuração são automaticamente enviados por push para todos os agentes.  Para agentes do Linux, um ficheiro de configuração é enviado para o recoletor de dados Fluentd.  Se deseja modificar esse arquivo manualmente em cada agente do Linux, em seguida, desmarque a caixa *aplicar configuração abaixo aos meus computadores Linux* e siga as orientações abaixo.
2. Escreva o nome do contador na caixa de texto no formato *objeto (instância) \counter*.  Quando começa a digitar, é apresentada uma lista de correspondência de contadores comuns.  Pode selecionar um contador da lista ou tipo em seu próprio dashboard.  
3. Clique em **+** ou prima **Enter** para adicionar o contador para a lista de outros contadores para o objeto.
4. Todos os contadores de um objeto de utilizar a mesma **intervalo de amostragem**.  A predefinição é 10 segundos.  Alterar isso para um valor mais alto de até como 1800 segundos (30 minutos) para reduzir os requisitos de armazenamento dos dados de desempenho coletados.
5. Quando tiver terminado a adição de contadores, clique nas **guardar** botão na parte superior do ecrã, para guardar a configuração.

#### <a name="configure-linux-performance-counters-in-configuration-file"></a>Configurar contadores de desempenho do Linux no ficheiro de configuração
Em vez de configurar contadores de desempenho do Linux no portal do Azure, tem a opção de editar ficheiros de configuração no agente Linux.  Métricas de desempenho para recolher são controladas mediante a configuração no **/etc/optar ativamente por participar/microsoft/omsagent/\<id da área de trabalho\>/conf/omsagent.conf**.

Cada objeto ou categoria, das métricas de desempenho para recolher deve ser definidos no ficheiro de configuração como um único `<source>` elemento. A sintaxe segue o padrão abaixo.

    <source>
      type oms_omi  
      object_name "Processor"
      instance_regex ".*"
      counter_name_regex ".*"
      interval 30s
    </source>


Os parâmetros neste elemento são descritos na tabela seguinte.

| Parâmetros | Descrição |
|:--|:--|
| object\_name | Nome do objeto para a coleção. |
| instance\_regex |  R *expressão regular* definir quais as instâncias para recolher. O valor: `.*` Especifica todas as instâncias. Para recolher métricas de processador para apenas os \_instância Total, poderia especificar `_Total`. Para recolher métricas de processo para apenas as instâncias crond ou sshd, pode especificar: `(crond\|sshd)`. |
| counter\_name\_regex | R *expressão regular* definir quais contadores (para o objeto) para recolher. Para recolher todos os contadores para o objeto, especifique: `.*`. Para recolher contadores para espaço de comutação apenas para o objeto de memória, por exemplo, pode especificar: `.+Swap.+` |
| intervalo | Frequência em que os contadores do objeto são recolhidos. |


A tabela seguinte lista os objetos e os contadores que pode especificar no arquivo de configuração.  Há contadores adicionais disponíveis para determinadas aplicações conforme descrito em [recolher contadores de desempenho de aplicações do Linux no Azure Monitor](data-sources-linux-applications.md).

| Nome do Objeto | Nome do Contador |
|:--|:--|
| Disco lógico | % De Inodes livres |
| Disco lógico | % De espaço livre |
| Disco lógico | % De Inodes utilizados |
| Disco lógico | % De espaço utilizado |
| Disco lógico | Bytes Lidos de Disco/seg |
| Disco lógico | Leituras de disco/seg |
| Disco lógico | As transferências de disco/seg |
| Disco lógico | Bytes Escritos em Disco/seg |
| Disco lógico | Escritas de disco/seg |
| Disco lógico | Megabytes livres |
| Disco lógico | Bytes de disco lógico/seg |
| Memória | % De memória disponível |
| Memória | % De espaço de comutação disponível |
| Memória | % De memória utilizada |
| Memória | % De espaço de comutação utilizado |
| Memória | MBytes de memória disponíveis |
| Memória | Comutação em MBytes disponíveis |
| Memória | Leituras de paginações/seg |
| Memória | Páginas escritas/seg |
| Memória | Páginas/seg |
| Memória | Espaço de comutação \ MBytes disponíveis |
| Memória | Memória utilizada em MBytes |
| Rede | Total de Bytes transmitidos |
| Rede | Total de Bytes recebidos |
| Rede | Total de Bytes |
| Rede | Total de pacotes transmitidos |
| Rede | Total de pacotes recebidos |
| Rede | Total Rx de erros |
| Rede | Erros de total Tx |
| Rede | Colisões total |
| Disco físico | Média Disco seg/leitura |
| Disco físico | Média Disco seg/transferência |
| Disco físico | Média Disco seg/escritas |
| Disco físico | Bytes de disco físico/seg |
| Processo | Tempo de PCT privilegiado |
| Processo | Tempo de utilizador de PCT |
| Processo | KBytes de memória utilizada |
| Processo | Memória compartilhada virtual |
| Processador | % De tempo DPC |
| Processador | % De tempo inativo |
| Processador | % De tempo de interrupção |
| Processador | % De tempo de espera de e/s |
| Processador | % De tempo nice |
| Processador | % De tempo privilegiado |
| Processador | % Tempo do processador |
| Processador | % Tempo de utilizador |
| Sistema | Memória física livre |
| Sistema | Espaço livre em ficheiros de paginação |
| Sistema | Memória Virtual livre |
| Sistema | Processos |
| Sistema | Tamanho armazenado em ficheiros de paginação |
| Sistema | Tempo de atividade |
| Sistema | Utilizadores |


Segue-se a configuração predefinida para as métricas de desempenho.

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
Monitor do Azure recolhe todos os contadores de desempenho especificado no respetivo intervalo de amostragem especificado em todos os agentes que tenham que contador instalado.  Os dados não são agregados e os dados não processados estão disponíveis em todas as vistas de consulta de registo durante o período especificado pela sua subscrição.

## <a name="performance-record-properties"></a>Propriedades de registo de desempenho
Registos de desempenho têm um tipo de **Perf** e ter as propriedades na tabela seguinte.

| Propriedade | Descrição |
|:--- |:--- |
| Computador |Computador que o evento foi recolhido a partir de. |
| CounterName |Nome do contador de desempenho |
| CounterPath |Caminho completo do contador na forma \\ \\ \<computador >\\object(instance)\\contador. |
| CounterValue |Valor numérico do contador. |
| InstanceName |Nome da instância do evento.  Vazia se nenhuma instância. |
| ObjectName |Nome do objeto de desempenho |
| SourceSystem |Tipo de agente que foram recolhidos os dados de. <br><br>Ligar OpsManager – agente de Windows, direta ou SCOM <br> Linux – todos os agentes do Linux  <br> AzureStorage – diagnósticos do Azure |
| TimeGenerated |Data e hora que de dados foi o objeto de amostragem. |

## <a name="sizing-estimates"></a>As estimativas de dimensionamento
 Uma estimativa aproximada de coleção de um contador específico em intervalos de 10 segundos é cerca de 1 MB por dia, por instância.  Pode estimar as necessidades de armazenamento de um contador específico com a fórmula seguinte.

    1 MB x (number of counters) x (number of agents) x (number of instances)

## <a name="log-queries-with-performance-records"></a>Consultas de registo com registos de desempenho
A tabela seguinte fornece exemplos diferentes de consultas de registo que obter registos de desempenho.

| Consulta | Descrição |
|:--- |:--- |
| Desempenho |Todos os dados de desempenho |
| Desempenho &#124; onde computador = = "MyComputer" |Todos os dados de desempenho de um computador específico |
| Desempenho &#124; onde CounterName = = "Comprimento de fila de disco atual" |Todos os dados de desempenho de um contador específico |
| Desempenho &#124; onde ObjectName = = "Processador" e CounterName = = "% de tempo do processador" e InstanceName = = total" &#124; resumir AVGCPU = avg(Average) por computador |Utilização média da CPU em todos os computadores |
| Desempenho &#124; onde CounterName = = "% de tempo do processador" &#124; resumir AggregatedValue = max(Max) por computador |Máximo de utilização da CPU em todos os computadores |
| Perf &#124; where ObjectName == "LogicalDisk" and CounterName == "Current Disk Queue Length" and Computer == "MyComputerName" &#124; summarize AggregatedValue = avg(Average) by InstanceName |Duração média da fila de disco atual em todas as instâncias de um determinado computador |
| Desempenho &#124; onde CounterName = = "DiskTransfers/seg" &#124; resumir AggregatedValue = percentil (médio, 95) por computador |95 º percentil de transferência de disco/seg em todos os computadores |
| Desempenho &#124; onde CounterName = = "% de tempo do processador" e InstanceName = = total" &#124; resumir AggregatedValue = avg(CounterValue) por bin (TimeGenerated, 1 hora), computador |Média por hora de utilização da CPU em todos os computadores |
| Desempenho &#124; onde computador = = "MyComputer" e CounterName startswith_cs "%" e InstanceName = = total" &#124; resumir AggregatedValue = percentil (CounterValue, 70) por bin (TimeGenerated, 1 hora), CounterName | Percentil de 70 por hora de cada contador % de percentagem de um computador específico |
| Desempenho &#124; onde CounterName = = "% de tempo do processador" e InstanceName = = , total"e o computador = ="MyComputer" &#124; resumir ["min(CounterValue)"] = min(CounterValue), ["avg(CounterValue)"] = avg(CounterValue), ["percentile75(CounterValue)"] = percentil (CounterValue, 75), ["max(CounterValue)"] = max(CounterValue) por bin (TimeGenerated, 1 hora), computador |Por hora média, mínimo, máxima e 75 percentil utilização da CPU para um computador específico |
| Desempenho &#124; onde ObjectName = = "MSSQL$ INST2: bases de dados" e InstanceName = = "principal" | Todos os dados de desempenho do objeto de desempenho da base de dados para o banco de dados mestra a instância nomeada do SQL Server INST2.  




## <a name="next-steps"></a>Passos Seguintes
* [Recolher contadores de desempenho de aplicações do Linux](data-sources-linux-applications.md) incluindo MySQL e Apache HTTP Server.
* Saiba mais sobre [registar as consultas](../log-query/log-query-overview.md) para analisar os dados recolhidos a partir de origens de dados e soluções.  
* Exportar os dados recolhidos para [Power BI](powerbi.md) para análise e visualizações adicionais.
