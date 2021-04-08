---
title: Boas práticas para a utilização do Azure Data Lake Storage Gen1 | Microsoft Docs
description: Conheça as melhores práticas sobre a ingestão de dados, segurança de datas e desempenho relacionado com a utilização da Azure Data Lake Storage Gen1 (anteriormente conhecida como Azure Data Lake Store)
services: data-lake-store
documentationcenter: ''
author: sachinsbigdata
manager: mtillman
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 06/27/2018
ms.author: sachins
ms.openlocfilehash: 9a5c5f9a4033b70a664071d6077a69f38c905093
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96452216"
---
# <a name="best-practices-for-using-azure-data-lake-storage-gen1"></a>Melhores práticas para a utilização do Azure Data Lake Storage Gen1

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Neste artigo, você aprende sobre as melhores práticas e considerações para trabalhar com a Azure Data Lake Storage Gen1. Este artigo fornece informações em torno da segurança, desempenho, resiliência e monitorização para data lake storage gen1. Antes do Data Lake Storage Gen1, trabalhar com dados verdadeiramente grandes em serviços como o Azure HDInsight era complexo. Teve de obter dados em várias contas de armazenamento Blob para que o armazenamento de petabytes e o desempenho ideal nessa escala pudessem ser alcançados. Com a Data Lake Storage Gen1, a maioria dos limites rígidos para o tamanho e desempenho são removidos. No entanto, ainda existem algumas considerações que este artigo cobre para que você possa obter o melhor desempenho com Data Lake Storage Gen1.

## <a name="security-considerations"></a>Considerações de segurança

A Azure Data Lake Storage Gen1 oferece controlos de acesso POSIX e auditoria detalhada para utilizadores, grupos e diretores de serviço azure Ative. Estes controlos de acesso podem ser definidos para ficheiros e pastas existentes. Os controlos de acesso também podem ser utilizados para criar predefinições que podem ser aplicadas a novos ficheiros ou pastas. Quando as permissões são definidas para pastas existentes e objetos infantis, as permissões devem ser propagadas novamente em cada objeto. Se houver um grande número de ficheiros, a propagação das permissões pode demorar muito tempo. O tempo desemtivos pode variar entre 30-50 objetos processados por segundo. Por isso, planeie a estrutura da pasta e os grupos de utilizadores adequadamente. Caso contrário, pode causar atrasos e problemas inesperados quando trabalha com os seus dados.

Assuma que tem uma pasta com 100.000 objetos infantis. Se pegar no limite inferior de 30 objetos processados por segundo, para atualizar a permissão para toda a pasta pode demorar uma hora. Mais detalhes sobre data lake storage Gen1 ACLs estão disponíveis no [controle de acesso em Azure Data Lake Storage Gen1](data-lake-store-access-control.md). Para um melhor desempenho na atribuição de ACLs de forma recursiva, pode utilizar a Ferramenta Command-Line do Lago de Dados Azure. A ferramenta cria múltiplos fios e lógica de navegação recursiva para aplicar rapidamente ACLs em milhões de ficheiros. A ferramenta está disponível para Linux e Windows, e a [documentação](https://github.com/Azure/data-lake-adlstool) e downloads para esta ferramenta podem ser [encontrados](https://aka.ms/adlstool-download) no GitHub. Estas mesmas melhorias de desempenho podem ser ativadas pelas suas próprias ferramentas escritas com os SDKs de armazenamento de data lake [e](data-lake-store-data-operations-net-sdk.md) [java.](data-lake-store-get-started-java-sdk.md)

### <a name="use-security-groups-versus-individual-users"></a>Utilize grupos de segurança contra utilizadores individuais

Ao trabalhar com big data na Data Lake Storage Gen1, muito provavelmente um diretor de serviço é usado para permitir que serviços como Azure HDInsight trabalhem com os dados. No entanto, pode haver casos em que os utilizadores individuais também precisem de ter acesso aos dados. Nesses casos, deve utilizar [grupos](data-lake-store-secure-data.md#create-security-groups-in-azure-active-directory) de segurança do Azure Ative Directory em vez de atribuir utilizadores individuais a pastas e ficheiros.

Uma vez que um grupo de segurança é atribuído permissões, adicionar ou remover utilizadores do grupo não requer quaisquer atualizações para data lake storage Gen1. Isto também ajuda a garantir que não exceda o limite de [32 ACLs de acesso e predefinidos](../azure-resource-manager/management/azure-subscription-service-limits.md#data-lake-storage-limits) (isto inclui os quatro ACLs de estilo POSIX que estão sempre associados a todos os ficheiros e pastas: [o utilizador próprio,](data-lake-store-access-control.md#the-owning-user) [o grupo de propriedade,](data-lake-store-access-control.md#the-owning-group) [a máscara,](data-lake-store-access-control.md#the-mask)e outros).

### <a name="security-for-groups"></a>Segurança para grupos

Como discutido, quando os utilizadores precisam de acesso à Data Lake Storage Gen1, o melhor é usar grupos de segurança do Azure Ative Directory. Alguns grupos recomendados para começar podem ser **ReadOnlyUsers**, **WriteAccessUsers** e **FullAccessUsers** para a raiz da conta, e até mesmo os separados para subfolders chave. Se houver outros grupos de utilizadores antecipados que possam ser adicionados mais tarde, mas que ainda não foram identificados, poderá considerar a criação de grupos de segurança falsos que tenham acesso a determinadas pastas. A utilização do grupo de segurança garante que mais tarde não precisa de muito tempo de processamento para atribuir novas permissões a milhares de ficheiros.

### <a name="security-for-service-principals"></a>Segurança para os diretores de serviços

Os principais do serviço Azure Ative Directory são normalmente utilizados por serviços como o Azure HDInsight para aceder a dados na Data Lake Storage Gen1. Dependendo dos requisitos de acesso em várias cargas de trabalho, pode haver algumas considerações para garantir a segurança dentro e fora da organização. Para muitos clientes, um único diretor de serviço Azure Ative Directory pode ser adequado, e pode ter permissões completas na raiz da conta Desejamento de Data Lake Gen1. Outros clientes podem necessitar de vários clusters com diferentes princípios de serviço onde um cluster tem acesso total aos dados, e outro cluster com apenas acesso de leitura. Tal como acontece com os grupos de segurança, poderá considerar fazer um principal de serviço para cada cenário previsto (leia, escreva, na íntegra) uma vez que uma conta Gen1 de armazenamento de data lake é criada.

### <a name="enable-the-data-lake-storage-gen1-firewall-with-azure-service-access"></a>Ativar a firewall gen1 de armazenamento de data lake com acesso ao serviço Azure

Data Lake Storage Gen1 suporta a opção de ligar uma firewall e limitar o acesso apenas aos serviços Azure, que é recomendado para um vetor de ataque menor de intrusões externas. Firewall pode ser ativado na conta Desejamento de Armazenamento de Dados Gen1 no portal Azure através do **Firewall**  >  **Enable Firewall (ON)**  >  Permitir o acesso às opções de **serviços Azure.**

![Definições de firewall na Data Lake Storage Gen1](./media/data-lake-store-best-practices/data-lake-store-firewall-setting.png "Definições de firewall na Data Lake Storage Gen1")

Uma vez ativada a firewall, apenas os serviços Azure tais como HDInsight, Data Factory, Azure Synapse Analytics, etc. têm acesso à Data Lake Storage Gen1. Devido à tradução interna do endereço de rede utilizada pelo Azure, a firewall da Data Lake Storage Gen1 não suporta a restrição de serviços específicos por IP e destina-se apenas a restrições de pontos finais fora de Azure, como no local.

## <a name="performance-and-scale-considerations"></a>Considerações de desempenho e escala

Uma das características mais poderosas da Data Lake Storage Gen1 é que remove os limites rígidos na produção de dados. A eliminação dos limites permite que os clientes aumentem o seu tamanho de dados e acompanhem os requisitos de desempenho sem necessidade de fragmentos dos dados. Uma das considerações mais importantes para otimizar o desempenho da Data Lake Storage Gen1 é que ele tem o melhor desempenho quando dado o paralelismo.

### <a name="improve-throughput-with-parallelism"></a>Melhorar a produção com paralelismo

Considere dar 8-12 fios por núcleo para a produção de leitura/escrita mais ideal. Isto deve-se ao bloqueio de leituras/escritos num único fio, e mais fios podem permitir uma maior concordância no VM. Para garantir que os níveis são saudáveis e o paralelismo pode ser aumentado, certifique-se de monitorizar a utilização do CPU do VM.

### <a name="avoid-small-file-sizes"></a>Evite tamanhos de ficheiros pequenos

As permissões posix e a auditoria em Data Lake Storage Gen1 vem com uma sobrecarga que se torna evidente quando se trabalha com inúmeros pequenos ficheiros. Como uma boa prática, você deve emarnar os seus dados em ficheiros maiores versus escrever milhares ou milhões de pequenos ficheiros para data lake storage gen1. Evitar tamanhos de ficheiros pequenos pode ter múltiplos benefícios, tais como:

* Redução das verificações de autenticação em vários ficheiros
* Ligações de ficheiros abertos reduzidas
* Cópia/replicação mais rápida
* Menos ficheiros para processar ao atualizar permissões de Data Lake Storage Gen1 POSIX

Dependendo dos serviços e cargas de trabalho que estão a usar os dados, um bom tamanho a considerar para ficheiros é de 256 MB ou superior. Se os tamanhos do ficheiro não puderem ser lotados ao aterrar em Data Lake Storage Gen1, pode ter um trabalho de compactação separado que combina estes ficheiros em outros maiores. Para obter mais informações e recomendações sobre os tamanhos dos ficheiros e organizar os dados na Data Lake Storage Gen1, consulte [Estruturar o seu conjunto de dados.](data-lake-store-performance-tuning-guidance.md#structure-your-data-set)

### <a name="large-file-sizes-and-potential-performance-impact"></a>Grandes tamanhos de ficheiros e impacto potencial de desempenho

Embora a Data Lake Storage Gen1 suporte ficheiros grandes até petabytes de tamanho, para um desempenho ótimo e dependendo da leitura do processo, pode não ser ideal ir acima de 2 GB em média. Por exemplo, ao utilizar **o Distcp** para copiar dados entre locais ou diferentes contas de armazenamento, os ficheiros são o melhor nível de granularidade utilizado para determinar as tarefas do mapa. Portanto, se estiver a copiar 10 ficheiros que são 1 TB cada, no máximo 10 mappers são atribuídos. Além disso, se tiver muitos ficheiros com mappers atribuídos, inicialmente os mappers trabalham em paralelo para mover ficheiros grandes. No entanto, à medida que o trabalho começa a acabar, apenas alguns mappers permanecem alocados e você pode ficar preso com um único mapper atribuído a um grande arquivo. A Microsoft apresentou melhorias ao Distcp para resolver este problema em futuras versões Hadoop.

Outro exemplo a considerar é quando se utiliza a Azure Data Lake Analytics com data lake storage gen1. Dependendo do processamento feito pelo exaustor, alguns ficheiros que não podem ser divididos (por exemplo, XML, JSON) podem sofrer em desempenho superior a 2 GB. Nos casos em que os ficheiros podem ser divididos por um extrator (por exemplo, CSV), são preferidos ficheiros grandes.

### <a name="capacity-plan-for-your-workload"></a>Plano de capacidade para a sua carga de trabalho

A azure Data Lake Storage Gen1 remove os limites de estrangulamento de IO que são colocados nas contas de armazenamento blob. No entanto, ainda há limites suaves que precisam de ser considerados. Os limites de estrangulamento por incumprimento de entrada/saída satisfazem as necessidades da maioria dos cenários. Se a sua carga de trabalho precisar de aumentar os limites, trabalhe com o suporte da Microsoft. Além disso, olhe para os limites durante a fase de prova de conceito para que os limites de estrangulamento do IO não sejam atingidos durante a produção. Se isso acontecer, poderá exigir um aumento manual da equipa de engenharia da Microsoft. Se ocorrer estrangulamento de IO, a Azure Data Lake Storage Gen1 devolve um código de erro de 429, e idealmente deve ser novamente julgado com uma política de backoff exponencial apropriada.

### <a name="optimize-writes-with-the-data-lake-storage-gen1-driver-buffer"></a>Otimizar "writes" com o tampão de condutor da Data Lake Storage Gen1

Para otimizar o desempenho e reduzir o IOPS ao escrever para data lake storage gen1 de Hadoop, execute operações de escrita o mais próximo possível do tamanho do buffer do condutor do Data Lake Storage Gen1. Tente não exceder o tamanho do tampão antes da lavagem, como quando estiver a transmitir utilizando cargas de trabalho de streaming Apache Storm ou Spark. Ao escrever para Data Lake Storage Gen1 da HDInsight/Hadoop, é importante saber que a Data Lake Storage Gen1 tem um motorista com um tampão de 4-MB. Como muitos controladores do sistema de ficheiros, este tampão pode ser lavado manualmente antes de atingir o tamanho de 4-MB. Caso contrário, é imediatamente lavado para armazenamento se a próxima escrita exceder o tamanho máximo do tampão. Sempre que possível, deve evitar uma sobrecarga ou uma subexedição significativa do tampão ao sincronizar/lavar a política por contagem ou janela de tempo.

## <a name="resiliency-considerations"></a>Considerações de resiliência

Ao armazenar um sistema com data lake storage gen1 ou qualquer serviço na nuvem, deve considerar os seus requisitos de disponibilidade e como responder a possíveis interrupções no serviço. Uma questão poderia ser localizada em caso específico ou mesmo em toda a região, pelo que é importante ter um plano para ambos. Dependendo do objetivo do **tempo de recuperação** e do objetivo do ponto de **recuperação** SLAs para a sua carga de trabalho, poderá escolher uma estratégia mais ou menos agressiva para alta disponibilidade e recuperação de desastres.

### <a name="high-availability-and-disaster-recovery"></a>Elevada disponibilidade e recuperação após desastre

A elevada disponibilidade (HA) e a recuperação de desastres (DR) podem por vezes ser combinadas, embora cada uma tenha uma estratégia ligeiramente diferente, especialmente quando se trata de dados. Data Lake Storage Gen1 já lida com a replicação 3x sob o capot para proteger contra falhas de hardware localizadas. No entanto, uma vez que a replicação em todas as regiões não está incorporada, você deve gerir isso por si mesmo. Ao construir um plano para ha, em caso de interrupção de serviço, a carga de trabalho necessita de acesso aos dados mais recentes o mais rapidamente possível, mudando para um caso replicado separadamente localmente ou em uma nova região.

Numa estratégia dr, para se preparar para o caso improvável de uma falha catastrófica de uma região, também é importante ter dados replicados para uma região diferente. Estes dados podem inicialmente ser os mesmos que os dados de HA replicados. No entanto, também deve considerar os seus requisitos para casos de vantagem, como a corrupção de dados, onde poderá querer criar instantâneos periódicos para recorrer. Dependendo da importância e dimensão dos dados, considere os instantâneos delta rolantes de períodos de 1, 6 e 24 horas na loja local e/ou secundária, de acordo com as tolerâncias de risco.

Para a resiliência de dados com data lake storage gen1, é aconselhável geo-replicar os seus dados para uma região separada com uma frequência que satisfaça os seus requisitos de HA/DR, idealmente a cada hora. Esta frequência de replicação minimiza movimentos massivos de dados que podem ter necessidades de produção concorrentes com o sistema principal e um melhor objetivo de ponto de recuperação (RPO). Além disso, deve considerar formas de a aplicação utilizando a Data Lake Storage Gen1 falhar automaticamente na conta secundária através da monitorização de gatilhos ou duração de tentativas falhadas, ou pelo menos enviar uma notificação aos administradores para intervenção manual. Tenha em mente que há uma troca de falhas em relação à espera que um serviço volte a funcionar. Se os dados não terminarem a replicação, uma falha pode causar perda de dados potenciais, inconsistência ou fusão complexa dos dados.

Abaixo estão as três principais opções recomendadas para orquestrar a replicação entre as contas da Data Lake Storage Gen1, e as principais diferenças entre cada uma delas.

|  |Distcp  |Azure Data Factory  |AdlCopy  |
|---------|---------|---------|---------|
|**Limites de escala**     | Delimitada por nódoas operárias        | Limitado por unidades de Movimento de Dados de Nuvem Max        | Ligado por unidades de Analytics        |
|**Suporta a cópia de deltas**     |   Yes      | No         | No         |
|**Orquestração embuta**     |  Não (use Oozie Airflow ou cron jobs)       | Yes        | Não (Use Azure Automation ou Windows Task Scheduler)         |
|**Sistemas de ficheiros suportados**     | ADL, HDFS, WASB, S3, GS, CFS        |Numerosos, consulte [conectores.](../data-factory/connector-azure-blob-storage.md)         | ADL a ADL, WASB a ADL (apenas na mesma região)        |
|**Suporte ao OS**     |Qualquer os que executa o Hadoop         | N/D          | Windows 10         |

### <a name="use-distcp-for-data-movement-between-two-locations"></a>Use distcp para movimento de dados entre dois locais

Abreviado para cópia distribuída, o Distcp é uma ferramenta de linha de comando Linux que vem com Hadoop e fornece movimento de dados distribuído entre dois locais. Os dois locais podem ser Data Lake Storage Gen1, HDFS, WASB ou S3. Esta ferramenta utiliza trabalhos mapReduce num cluster Hadoop (por exemplo, HDInsight) para escalar em todos os nós. A Distcp é considerada a forma mais rápida de mover grandes dados sem aparelhos especiais de compressão da rede. A Distcp também oferece uma opção para atualizar apenas deltas entre dois locais, lidar com as retrações automáticas, bem como a escala dinâmica do cálculo. Esta abordagem é incrivelmente eficiente quando se trata de replicar coisas como as tabelas Hive/Spark que podem ter muitos ficheiros grandes num único diretório e você só quer copiar sobre os dados modificados. Por estas razões, a Distcp é a ferramenta mais recomendada para copiar dados entre grandes lojas de dados.

Os trabalhos de cópia podem ser desencadeados por fluxos de trabalho Apache Oozie usando frequência ou gatilhos de dados, bem como trabalhos de cron linux. Para trabalhos intensivos de replicação, é recomendado girar um cluster hdinsight hadoop separado que pode ser afinado e dimensionado especificamente para os trabalhos de cópia. Isto garante que os trabalhos de cópia não interferem em trabalhos críticos. Se executar a replicação numa frequência suficientemente larga, o cluster pode mesmo ser retirado entre cada trabalho. Se não conseguir chegar à região secundária, certifique-se de que outro cluster também é girado na região secundária para replicar novos dados de volta para a conta primária de Data Lake Storage Gen1 uma vez que volta a subir. Por exemplo, utilizando o Distcp, consulte [use Distcp para copiar dados entre as bolhas de armazenamento Azure e a Data Lake Storage Gen1](data-lake-store-copy-data-wasb-distcp.md).

### <a name="use-azure-data-factory-to-schedule-copy-jobs"></a>Use a Azure Data Factory para agendar trabalhos de cópia

A Azure Data Factory também pode ser usada para agendar trabalhos de cópia usando uma **Atividade de Cópia**, e pode até ser configurado numa frequência através do Copy **Wizard**. Tenha em mente que a Azure Data Factory tem um limite de unidades de movimento de dados em nuvem (DMUs), e eventualmente cobre o rendimento/computação para grandes cargas de trabalho de dados. Além disso, a Azure Data Factory não oferece atualmente atualizações delta entre as contas da Data Lake Storage Gen1, pelo que pastas como as tabelas hive exigiriam uma cópia completa para replicar. Consulte o guia de [afinação da Atividade de Cópias](../data-factory/copy-activity-performance.md) para obter mais informações sobre a cópia com a Data Factory.

### <a name="adlcopy"></a>AdlCopy

O AdlCopy é uma ferramenta de linha de comando do Windows que permite copiar dados entre duas contas gen1 de armazenamento de dados apenas dentro da mesma região. A ferramenta AdlCopy fornece uma opção autónoma ou a opção de usar uma conta Azure Data Lake Analytics para executar o seu trabalho de cópia. Embora tenha sido originalmente construído para cópias a pedido em oposição a uma replicação robusta, ele fornece outra opção para fazer cópia distribuída através de contas de Data Lake Storage Gen1 na mesma região. Para fiabilidade, é recomendado usar a opção premium Data Lake Analytics para qualquer carga de trabalho de produção. A versão autónoma pode devolver respostas ocupadas e tem escala e monitorização limitadas.

Tal como o Distcp, o AdlCopy precisa de ser orquestrado por algo como Azure Automation ou Windows Task Scheduler. Tal como acontece com a Data Factory, a AdlCopy não suporta a cópia apenas de ficheiros atualizados, mas sim recopia e substitui os ficheiros existentes. Para obter mais informações e exemplos de utilização do AdlCopy, consulte [os dados da Cópia das Bolhas de Armazenamento Azure para a Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md).

## <a name="monitoring-considerations"></a>Considerações de monitorização

Data Lake Storage Gen1 fornece registos de diagnóstico detalhados e auditoria. Data Lake Storage Gen1 fornece algumas métricas básicas no portal Azure sob a conta de Data Lake Storage Gen1 e no Azure Monitor. A disponibilidade de Data Lake Storage Gen1 é apresentada no portal Azure. No entanto, esta métrica é atualizada a cada sete minutos e não pode ser questionada através de uma API exposta publicamente. Para obter a disponibilidade mais atualizada de uma conta Gen1 de armazenamento de dados, você deve executar seus próprios testes sintéticos para validar a disponibilidade. Outras métricas, tais como a utilização total do armazenamento, os pedidos de leitura/escrita e a entrada/saída podem demorar até 24 horas para se refrescar. Assim, métricas mais atualizadas devem ser calculadas manualmente através de ferramentas de linha de comando Hadoop ou agregação de informações de registo. A forma mais rápida de obter a utilização mais recente do armazenamento é executar este comando HDFS a partir de um nó de cluster Hadoop (por exemplo, nó de cabeça):

```console
hdfs dfs -du -s -h adl://<adlsg1_account_name>.azuredatalakestore.net:443/
```

### <a name="export-data-lake-storage-gen1-diagnostics"></a>Diagnósticos gen1 de armazenamento de dados de exportação

Uma das formas mais rápidas de obter acesso a registos pes pesjáveis a partir da Data Lake Storage Gen1 é permitir o envio de **registos** para **Registar Analytics** sob a lâmina de Diagnóstico para a conta de Data Lake Storage Gen1. Isto proporciona acesso imediato aos registos de entrada com filtros de tempo e conteúdo, juntamente com opções de alerta (email/webhook) ativadas dentro de intervalos de 15 minutos. Para obter instruções, consulte [aceder aos registos de diagnóstico do Azure Data Lake Storage Gen1](data-lake-store-diagnostic-logs.md).

Para um maior alerta em tempo real e mais controlo sobre onde aterrar os registos, considere a exportação de registos para o Azure EventHub, onde o conteúdo pode ser analisado individualmente ou ao longo de uma janela de tempo, a fim de enviar notificações em tempo real para uma fila. Uma aplicação separada, como uma [App Lógica,](../connectors/connectors-create-api-azure-event-hubs.md) pode então consumir e comunicar os alertas para o canal apropriado, bem como submeter métricas a ferramentas de monitorização como NewRelic, Datadog ou AppDynamics. Alternativamente, se estiver a utilizar uma ferramenta de terceiros, como o ElasticSearch, pode exportar os registos para o Blob Storage e utilizar o [plugin Azure Logstash](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob) para consumir os dados na sua pilha de Elasticsearch, Kibana e Logstash (ELK).

### <a name="turn-on-debug-level-logging-in-hdinsight"></a>Ligue a registar-se ao nível do depurg em HDInsight

Se o envio de registos gen1 de armazenamento de dados não estiver ligado, o Azure HDInsight também fornece uma maneira de ligar o [registo do lado do cliente para data lake storage Gen1](data-lake-store-performance-tuning-mapreduce.md) via log4j. Você deve definir a seguinte propriedade em configurações **Ambari**  >  **YARN**  >  **Config**  >  **Advanced yarn-log4j**:

`log4j.logger.com.microsoft.azure.datalake.store=DEBUG`

Uma vez que a propriedade é definida e os nós são reiniciados, os diagnósticos da Gen1 de armazenamento de dados são escritos nos registos YARN nos nós (/tmp/ \<user\> /fios.log), e detalhes importantes como erros ou estrangulamento (código de erro HTTP 429) podem ser monitorizados. Estas mesmas informações também podem ser monitorizadas nos registos do Azure Monitor ou onde quer que os registos sejam enviados para a lâmina de diagnóstico da conta Desíduo de Armazenamento de [Dados](data-lake-store-diagnostic-logs.md) Gen1. Recomenda-se, pelo menos, que o registo do lado do cliente seja ligado ou utilize a opção de envio de registos com a Data Lake Storage Gen1 para visibilidade operacional e depuração mais fácil.

### <a name="run-synthetic-transactions"></a>Executar transações sintéticas

Atualmente, a métrica de disponibilidade de serviço para data lake storage Gen1 no portal Azure tem janela de atualização de 7 minutos. Além disso, não pode ser questionado usando uma API exposta publicamente. Assim, é recomendado construir uma aplicação básica que faça transações sintéticas para data lake storage gen1 que pode fornecer até o minuto de disponibilidade. Um exemplo pode ser criar uma WebJob, Logic App ou Azure Function App para executar uma leitura, criação e atualização contra a Data Lake Storage Gen1 e enviar os resultados para a sua solução de monitorização. As operações podem ser efetuadas numa pasta temporária e depois eliminadas após o teste, que pode ser executada a cada 30-60 segundos, dependendo dos requisitos.

## <a name="directory-layout-considerations"></a>Considerações de layout de diretório

Ao aterrar dados num lago de dados, é importante pré-planear a estrutura dos dados para que a segurança, a partilha e o processamento possam ser utilizados de forma eficaz. Muitas das seguintes recomendações podem ser usadas quer seja com Azure Data Lake Storage Gen1, Blob Storage ou HDFS. Cada carga de trabalho tem requisitos diferentes sobre como os dados são consumidos, mas abaixo estão alguns layouts comuns a considerar quando se trabalha com IoT e cenários de lote.

### <a name="iot-structure"></a>Estrutura IoT

Nas cargas de trabalho ioT, pode haver uma grande quantidade de dados a serem desembarcados na loja de dados que se estende por inúmeros produtos, dispositivos, organizações e clientes. É importante pré-planear o layout do diretório para organização, segurança e processamento eficiente dos dados para consumidores em fluxo. Um modelo geral a considerar pode ser o seguinte layout:

```console
{Region}/{SubjectMatter(s)}/{yyyy}/{mm}/{dd}/{hh}/
```

Por exemplo, a telemetria de aterragem para um motor de avião no Reino Unido pode parecer a seguinte estrutura:

```console
UK/Planes/BA1293/Engine1/2017/08/11/12/
```

Há uma razão importante para colocar a data no final da estrutura da pasta. Se pretender bloquear determinadas regiões ou sujeitar assuntos a utilizadores/grupos, então pode facilmente fazê-lo com as permissões POSIX. Caso contrário, se houvesse a necessidade de restringir um determinado grupo de segurança à visualização apenas dos dados do Reino Unido ou de certos aviões, com a estrutura da data à frente seria necessária uma autorização separada para numerosas pastas sob cada hora pasta. Além disso, ter a estrutura da data à frente aumentaria exponencialmente o número de pastas à medida que o tempo passava.

### <a name="batch-jobs-structure"></a>Estrutura de empregos em lotes

A partir de um nível elevado, uma abordagem comumente usada no processamento de lotes é obter dados de terra numa pasta "in". Em seguida, uma vez que os dados são processados, coloque os novos dados numa pasta "out" para os processos a jusante consumirem. Esta estrutura de diretório é vista por vezes para trabalhos que requerem processamento em ficheiros individuais e podem não exigir um processamento massivamente paralelo sobre grandes conjuntos de dados. Tal como a estrutura IoT acima recomendada, uma boa estrutura de diretórios tem as pastas ao nível dos pais para coisas como questões de região e assuntos (por exemplo, organização, produto/produtor). Esta estrutura ajuda a garantir os dados em toda a sua organização e a uma melhor gestão dos dados nas suas cargas de trabalho. Além disso, considere a data e a hora na estrutura para permitir uma melhor organização, pesquisas filtradas, segurança e automação no processamento. O nível de granularidade para a estrutura da data é determinado pelo intervalo em que os dados são carregados ou tratados, tais como hora, dia ou mesmo mensal.

Por vezes, o processamento de ficheiros não é bem sucedido devido à corrupção de dados ou a formatos inesperados. Nesses casos, a estrutura do diretório pode beneficiar de uma pasta **/má** para mover os ficheiros para uma inspeção posterior. O trabalho em lote também pode tratar do relatório ou notificação destes ficheiros *maus* para intervenção manual. Considere a seguinte estrutura do modelo:

```console
{Region}/{SubjectMatter(s)}/In/{yyyy}/{mm}/{dd}/{hh}/
{Region}/{SubjectMatter(s)}/Out/{yyyy}/{mm}/{dd}/{hh}/
{Region}/{SubjectMatter(s)}/Bad/{yyyy}/{mm}/{dd}/{hh}/
```

Por exemplo, uma empresa de marketing recebe extratos diários de dados de atualizações de clientes dos seus clientes na América do Norte. Pode parecer o seguinte corte antes e depois de ser processado:

```console
NA/Extracts/ACMEPaperCo/In/2017/08/14/updates_08142017.csv
NA/Extracts/ACMEPaperCo/Out/2017/08/14/processed_updates_08142017.csv
```

No caso comum de os dados dos lotes serem tratados diretamente em bases de dados como a Hive ou as bases de dados tradicionais do SQL, não há necessidade de uma pasta **/dentro** **ou/fora,** uma vez que a saída já vai para uma pasta separada para a tabela Hive ou base de dados externa. Por exemplo, extratos diários de clientes aterrariam nas respetivas pastas, e a orquestração por algo como Azure Data Factory, Apache Oozie ou Apache Airflow desencadearia um trabalho diário de Hive ou Spark para processar e escrever os dados numa tabela da Colmeia.

## <a name="next-steps"></a>Passos seguintes

* [Visão geral do Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Controlo de acessos em Azure Data Lake Storage Gen1](data-lake-store-access-control.md)
* [Segurança em Azure Data Lake Storage Gen1](data-lake-store-security-overview.md)
* [Afinação Azure Data Lake Storage Gen1 para desempenho](data-lake-store-performance-tuning-guidance.md)
* [Orientação de afinação de desempenho para usar HDInsight Spark com Azure Data Lake Storage Gen1](data-lake-store-performance-tuning-spark.md)
* [Orientação de afinação de desempenho para usar colmeia HDInsight com Azure Data Lake Storage Gen1](data-lake-store-performance-tuning-hive.md)
* [Criar clusters HDInsight com Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
