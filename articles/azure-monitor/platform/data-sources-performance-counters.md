---
title: Recolher e analisar balcões de desempenho no Monitor Do Azure Microsoft Docs
description: Os contadores de desempenho são recolhidos pelo Azure Monitor para analisar o desempenho dos agentes Windows e Linux.  Este artigo descreve como configurar a recolha de balcões de Desempenho tanto para os agentes Windows como para os linux, detalhes dos mesmos estão armazenados no espaço de trabalho e como analisá-los no portal Azure.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/28/2018
ms.openlocfilehash: d1a972a1d89066b961f2dcc28fba830e3a04ebc1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274765"
---
# <a name="windows-and-linux-performance-data-sources-in-azure-monitor"></a>Fontes de dados de desempenho do Windows e Linux no Monitor Azure
Os contadores de desempenho no Windows e no Linux fornecem informações sobre o desempenho de componentes de hardware, sistemas operativos e aplicações.  O Azure Monitor pode recolher contadores de desempenho em intervalos frequentes para análise de tempo próximo em tempo real (NRT), além de agregar dados de desempenho para análise e reporte a longo prazo.

![Contadores de desempenho](media/data-sources-performance-counters/overview.png)

## <a name="configuring-performance-counters"></a>Configurar contadores de desempenho
Configure os contadores de desempenho do [menu Dados em Definições Avançadas](agent-data-sources.md#configuring-data-sources).

Quando configura os contadores Windows ou Linux Performance para um novo espaço de trabalho, é-lhe dada a opção de criar rapidamente vários contadores comuns.  São listados com uma caixa de verificação junto a cada um.  Certifique-se de que os contadores que deseja criar inicialmente são verificados e, em seguida, clique em Adicionar os contadores de **desempenho selecionados**.

Para os contadores de desempenho do Windows, pode escolher uma instância específica para cada contador de desempenho. Para os contadores de desempenho linux, a instância de cada contador que escolher aplica-se a todos os balcões de crianças do contador principal. A tabela que se segue mostra as instâncias comuns disponíveis tanto para os balcões de desempenho do Linux como para o Windows.

| Nome da instância | Descrição |
| --- | --- |
| \_Total |Total de todas as instâncias |
| \* |Todas as instâncias |
| (/&#124;/var) |Casos de correspondência nomeados: / ou /var |

### <a name="windows-performance-counters"></a>Contadores de desempenho do Windows

![Configure os contadores de desempenho do Windows](media/data-sources-performance-counters/configure-windows.png)

Siga este procedimento para adicionar um novo contador de desempenho do Windows para recolher.

1. Digite o nome do contador na caixa de texto no objeto de formato *(por exemplo)\contador*.  Quando começa a dactilografar, é-lhe apresentada uma lista de contadores comuns.  Pode selecionar um contador da lista ou escrever num dos seus.  Também pode devolver todas as instâncias para um contador específico, especificando *o contador de objetos.counter*.  

    Ao recolher contadores de desempenho do SQL Server de instâncias nomeadas, todos os contadores de instâncias nomeados começam com *MSSQL$* e seguidos pelo nome da instância.  Por exemplo, recolher o contador de rácios de impacto de cache de registo para todas `MSSQL$INST2:Databases(*)\Log Cache Hit Ratio`as bases de dados do objeto de desempenho da Base de Dados para a chamada instância SQL INST2, especificar .

2. Clique **+** ou prima **Introduza** para adicionar o contador à lista.
3. Quando adiciona um contador, utiliza o padrão de 10 segundos para o intervalo da **amostra**.  Pode alterar isto para um valor superior a 1800 segundos (30 minutos) se pretender reduzir os requisitos de armazenamento dos dados de desempenho recolhidos.
4. Quando terminar de adicionar contadores, clique no botão **Guardar** na parte superior do ecrã para guardar a configuração.

### <a name="linux-performance-counters"></a>Contadores de desempenho linux

![Configure contadores de desempenho linux](media/data-sources-performance-counters/configure-linux.png)

Siga este procedimento para adicionar um novo contador de desempenho Linux para recolher.

1. Por predefinição, todas as alterações de configuração são automaticamente empurradas para todos os agentes.  Para os agentes Linux, um ficheiro de configuração é enviado para o coletor de dados Fluentd.  Se desejar modificar este ficheiro manualmente em cada agente Linux, desverifique a *configuração abaixo da caixa Aplique abaixo a configuração nas minhas máquinas Linux* e siga as orientações abaixo.
2. Digite o nome do contador na caixa de texto no objeto de formato *(por exemplo)\contador*.  Quando começa a dactilografar, é-lhe apresentada uma lista de contadores comuns.  Pode selecionar um contador da lista ou escrever num dos seus.  
3. Clique **+** ou prima **Introduza** para adicionar o contador à lista de outros contadores para o objeto.
4. Todos os contadores para um objeto usam o mesmo **intervalo de amostra**.  A predefinição é de 10 segundos.  Muda-o para um valor superior a 1800 segundos (30 minutos) se quiser reduzir os requisitos de armazenamento dos dados de desempenho recolhidos.
5. Quando terminar de adicionar contadores, clique no botão **Guardar** na parte superior do ecrã para guardar a configuração.

#### <a name="configure-linux-performance-counters-in-configuration-file"></a>Configure os contadores de desempenho do Linux no ficheiro de configuração
Em vez de configurar os contadores de desempenho do Linux utilizando o portal Azure, tem a opção de editar ficheiros de configuração no agente Linux.  As métricas de desempenho a recolher são controladas pela configuração em **/etc/opt/microsoft/omsagent/\<workspace id\>/conf/omsagent.conf**.

Cada objeto, ou categoria, de métricas de desempenho a recolher `<source>` deve ser definido no ficheiro de configuração como um único elemento. A sintaxe segue o padrão abaixo.

    <source>
      type oms_omi  
      object_name "Processor"
      instance_regex ".*"
      counter_name_regex ".*"
      interval 30s
    </source>


Os parâmetros deste elemento são descritos na tabela seguinte.

| Parâmetros | Descrição |
|:--|:--|
| nome\_do objeto | Nome do objeto para a coleção. |
| caso\_regex |  Uma *expressão regular* que define quais as instâncias a recolher. O valor: `.*` especifica todas as instâncias. Para recolher métricas do \_processador apenas `_Total`para a instância Total, pode especificar . Para recolher métricas de processo apenas para os casos de `(crond\|sshd)`compadrio ou sshd, pode especificar: . |
| contra\_\_nome regex | Uma *expressão regular* que define quais os contadores (para o objeto) recolher. Para recolher todos os contadores para `.*`o objeto, especifique: . Para recolher apenas trocar contadores de espaço para o objeto de memória, por exemplo, pode especificar:`.+Swap.+` |
| intervalo | Frequência na qual os balcões do objeto são recolhidos. |


A tabela que se segue lista os objetos e balcões que pode especificar no ficheiro de configuração.  Existem contadores adicionais disponíveis para determinadas aplicações, conforme descrito nos balcões de [desempenho da Collect para aplicações Linux no Monitor Azure](data-sources-linux-applications.md).

| Nome do objeto | Contra nome |
|:--|:--|
| Disco Lógico | % Inodes livres |
| Disco Lógico | % Espaço Livre |
| Disco Lógico | % Inodos Usados |
| Disco Lógico | % Espaço Usado |
| Disco Lógico | Discos Ler Bytes/seg |
| Disco Lógico | Leituras/seg de disco |
| Disco Lógico | Transferências de disco/seg |
| Disco Lógico | Discos Write Bytes/seg |
| Disco Lógico | Escritas de Disco/seg |
| Disco Lógico | Megabytes grátis |
| Disco Lógico | Bytes/sede de disco lógico |
| Memória | % Memória Disponível |
| Memória | % Espaço de Troca Disponível % |
| Memória | % Memória Usada |
| Memória | % Espaço de troca usado % |
| Memória | Memória MBytes disponível |
| Memória | Troca de MBytes disponíveis |
| Memória | Leituras da página/seg |
| Memória | Página Escritas/seg |
| Memória | Páginas/seg |
| Memória | Espaço de troca de MBytes usado |
| Memória | MBytes de memória usada |
| Rede | Total bytes transmitidos |
| Rede | Total de Bytes Recebidos |
| Rede | Bytes Totais |
| Rede | Pacotes totais transmitidos |
| Rede | Total de pacotes recebidos |
| Rede | Total de Erros Rx |
| Rede | Total de erros Tx |
| Rede | Colisões totais |
| Disco Físico | Avg. Disk sec/Read |
| Disco Físico | Avg. Disco sec/Transferência |
| Disco Físico | Avg. Disk sec/Write |
| Disco Físico | Bytes/sede de disco físico |
| Processo | Tempo Privilegiado do Pct |
| Processo | Tempo de utilizador do Pct |
| Processo | KBytes de memória usada |
| Processo | Memória Partilhada Virtual |
| Processador | % Tempo dPC |
| Processador | % de Tempo Inativo |
| Processador | % tempo de interrupção |
| Processador | % Io Tempo de Espera |
| Processador | % Bom Tempo |
| Processador | % Tempo Privilegiado |
| Processador | % de Tempo do Processador |
| Processador | % Tempo de utilizador |
| Sistema | Memória Física Gratuita |
| Sistema | Espaço gratuito em arquivos de paging |
| Sistema | Memória Virtual Gratuita |
| Sistema | Processos |
| Sistema | Tamanho armazenado em arquivos de paging |
| Sistema | Tempo de subida |
| Sistema | Utilizadores |


Segue-se a configuração padrão para métricas de desempenho.

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
O Monitor Azure recolhe todos os contadores de desempenho especificados no intervalo de amostra especificado em todos os agentes que tenham esse contador instalado.  Os dados não são agregados e os dados brutos estão disponíveis em todas as vistas de consulta de registo durante a duração especificada pela sua subscrição.

## <a name="performance-record-properties"></a>Propriedades de recorde de desempenho
Os registos de desempenho têm um tipo de **Perf** e têm as propriedades na tabela seguinte.

| Propriedade | Descrição |
|:--- |:--- |
| Computador |Computador de que o evento foi recolhido. |
| Contranome |Nome do contador de desempenho |
| Contra-caminho |Caminho completo do contador \\ \\ \<sob \\a forma\\Computer>contador (por exemplo) do objeto. |
| ContraValor |Valor numérico do balcão. |
| Nome de instância |Nome da instância do evento.  Vazio se não houver instância. |
| ObjectName |Nome do objeto de desempenho |
| SourceSystem |Tipo de agente de que os dados foram recolhidos. <br><br>OpsManager – Agente Windows, ligue diretamente ou SCOM <br> Linux - Todos os agentes linux  <br> AzureStorage – Diagnósticos Azure |
| TimeGenerated |Data e hora os dados foram amostrados. |

## <a name="sizing-estimates"></a>Estimativas de dimensionamento
 Uma estimativa aproximada para a recolha de um contador em intervalos de 10 segundos é de cerca de 1 MB por dia por exemplo.  Pode estimar os requisitos de armazenamento de um contador específico com a seguinte fórmula.

    1 MB x (number of counters) x (number of agents) x (number of instances)

## <a name="log-queries-with-performance-records"></a>Consultas de log com registos de desempenho
A tabela seguinte fornece diferentes exemplos de consultas de registo que recuperam registos de Desempenho.

| Consulta | Descrição |
|:--- |:--- |
| Des |Todos os dados de desempenho |
| Perf &#124; onde computador == "MyComputer" |Todos os dados de desempenho de um determinado computador |
| Perf &#124; onde contranome == "Comprimento da fila do disco atual" |Todos os dados de desempenho para um contador específico |
| Perf &#124; onde objectname == "Processador" e ContraNome == "% Tempo do Processador" e Nome de Instância == "_Total" &#124; resumir AVGCPU = avg(ContraValue) por Computador |Utilização média do CPU em todos os computadores |
| Perf &#124; onde o contranome == "% tempo do processador" &#124; resumir AgregadaValue = max (ContraValue) por Computador |Utilização máxima do CPU em todos os computadores |
| Perf &#124; onde objectname == "LogicalDisk" e ContraNome == "Comprimento da fila do disco atual" e computador == "MyComputerName" &#124; resumir AgregadaValue = avg(ContraValue) por ExemploName |Comprimento médio da fila do disco atual em todas as instâncias de um determinado computador |
| Perf &#124; onde o contranome == "Transferências de discos/seg" &#124; resumir Valor Agregado = percentil (ContraValue, 95) por Computador |Percentil 95 das Transferências de Discos/Sec em todos os computadores |
| Perf &#124; onde contranome == "% tempo de processador" e nome de exemplo == "_Total" &#124; resumir AgregadaValue = avg(ContraValue) por bin (TimeGenerated, 1h), Computer |Média horária de utilização de CPU em todos os computadores |
| Perf &#124; onde o computador == "MyComputer" e contranome startswith_cs "%" e O Nome de Exemplo == "_Total" &#124; resumir AgregadValue = percentil (ContraValue, 70) por bin (TimeGenerated, 1h), CounterName | Por hora 70 percentil de cada balcão de % para um determinado computador |
| Perf &#124; onde o ContraNome == "% Tempo do Processador" e o Nome de Exemplo == "_Total" e Computador == "MyComputer" &#124; resumir ["min(ContraValue)"] = min(CounterValue), ["avg(ContraValue)"] = avg(CounterValue), ["percentil75(ContraValue)"] = percentil(ContraValor), = percentil(ContraValor), = percentil(ContraValor), = percentil(ContraValor), = %g(ContraValue, = 75), ["max(ContraValue)"] = máx(ContraValor) por bin (TimeGenerated, 1h), Computador |Média horária, mínima, máxima e utilização de CPU de 75 por cento para um computador específico |
| Perf &#124; onde objectname == "MSSQL$INST2:Bases de dados" e Exemplo == "mestre" | Todos os dados de Desempenho do objeto de desempenho da Base de Dados para a base de dados principal da chamada instância do Servidor SQL INST2.  




## <a name="next-steps"></a>Passos seguintes
* [Colete contadores de desempenho de aplicações Linux,](data-sources-linux-applications.md) incluindo MySQL e Apache HTTP Server.
* Saiba mais sobre consultas de [registo](../log-query/log-query-overview.md) para analisar os dados recolhidos a partir de fontes e soluções de dados.  
* Exportação de dados recolhidos para [O Power BI](powerbi.md) para visualizações e análises adicionais.
