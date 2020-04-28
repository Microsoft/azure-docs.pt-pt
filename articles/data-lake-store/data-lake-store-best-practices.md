---
title: Boas práticas para a utilização do Azure Data Lake Storage Gen1 [ Microsoft Docs
description: Conheça as melhores práticas sobre ingestão de dados, segurança de datas e desempenho relacionado com a utilização de Azure Data Lake Storage Gen1 (anteriormente conhecido como Azure Data Lake Store)
services: data-lake-store
documentationcenter: ''
author: sachinsbigdata
manager: mtillman
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 06/27/2018
ms.author: sachins
ms.openlocfilehash: a8ca67d1ff3100aee02ed473c9cc2180de3973b8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75638940"
---
# <a name="best-practices-for-using-azure-data-lake-storage-gen1"></a>Boas práticas para usar O Armazenamento de Lagos Azure Gen1

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Neste artigo, você aprende sobre as melhores práticas e considerações para trabalhar com Azure Data Lake Storage Gen1. Este artigo fornece informações em torno da segurança, desempenho, resiliência e monitorização para Data Lake Storage Gen1. Antes do Data Lake Storage Gen1, trabalhar com dados verdadeiramente grandes em serviços como o Azure HDInsight era complexo. Você teve que esnsos dados em várias contas de armazenamento Blob para que o armazenamento de petabyte e o desempenho ideal a essa escala pudessem ser alcançados. Com data Lake Storage Gen1, a maioria dos limites rígidos para o tamanho e desempenho são removidos. No entanto, existem ainda algumas considerações que este artigo cobre para que possa obter o melhor desempenho com Data Lake Storage Gen1.

## <a name="security-considerations"></a>Considerações de segurança

O Azure Data Lake Storage Gen1 oferece controlos de acesso POSIX e auditoria detalhada para utilizadores, grupos e diretores de serviço sinuosos do Azure Ative Directory (Azure AD). Estes controlos de acesso podem ser definidos para ficheiros e pastas existentes. Os controlos de acesso também podem ser utilizados para criar predefinições que podem ser aplicadas a novos ficheiros ou pastas. Quando as permissões são definidas para pastas e objetos infantis existentes, as permissões devem ser propagadas recursivamente em cada objeto. Se houver um grande número de ficheiros, a propagação das permissões pode demorar muito tempo. O tempo devida pode variar entre 30-50 objetos processados por segundo. Assim, planeie a estrutura da pasta e os grupos de utilizadores adequadamente. Caso contrário, pode causar atrasos e problemas inesperados quando trabalha com os seus dados.

Assuma que tem uma pasta com 100.000 objetos infantis. Se tomar o limite inferior de 30 objetos processados por segundo, para atualizar a permissão para toda a pasta pode demorar uma hora. Mais detalhes sobre data Lake Storage Gen1 ACLs estão disponíveis no controle de [acesso em Azure Data Lake Storage Gen1](data-lake-store-access-control.md). Para um melhor desempenho na atribuição de ACLs de forma recursiva, pode utilizar a Ferramenta de Linha de Comando do Lago de Dados Azure. A ferramenta cria vários fios e lógica de navegação recursiva para aplicar rapidamente ACLs a milhões de ficheiros. A ferramenta está disponível para Linux e Windows, e a [documentação](https://github.com/Azure/data-lake-adlstool) e [downloads](https://aka.ms/adlstool-download) para esta ferramenta podem ser encontrados no GitHub. Estas mesmas melhorias de desempenho podem ser ativadas pelas suas próprias ferramentas escritas com os Data Lake Storage Gen1 [.NET](data-lake-store-data-operations-net-sdk.md) e [Java](data-lake-store-get-started-java-sdk.md) SDKs.

### <a name="use-security-groups-versus-individual-users"></a>Utilize grupos de segurança contra utilizadores individuais

Ao trabalhar com big data em Data Lake Storage Gen1, muito provavelmente um diretor de serviço é usado para permitir que serviços como o Azure HDInsight trabalhem com os dados. No entanto, pode haver casos em que os utilizadores individuais também precisam de ter acesso aos dados. Nesses casos, deve utilizar [grupos](data-lake-store-secure-data.md#create-security-groups-in-azure-active-directory) de segurança do Diretório Ativo Azure em vez de atribuir utilizadores individuais a pastas e ficheiros.

Uma vez que um grupo de segurança é atribuído permissões, adicionar ou remover utilizadores do grupo não requer quaisquer atualizações para Data Lake Storage Gen1. Isto também ajuda a garantir que não excede o limite de [32 ACLs](../azure-resource-manager/management/azure-subscription-service-limits.md#data-lake-store-limits) de Acesso e Predefinido (isto inclui os quatro ACLs estilo POSIX que estão sempre associados a cada ficheiro e pasta: [o utilizador próprio](data-lake-store-access-control.md#the-owning-user), o grupo [próprio,](data-lake-store-access-control.md#the-owning-group) [a máscara](data-lake-store-access-control.md#the-mask), e outros).

### <a name="security-for-groups"></a>Segurança para grupos

Como discutido, quando os utilizadores precisam de acesso ao Data Lake Storage Gen1, o melhor é usar os grupos de segurança do Azure Ative Directory. Alguns grupos recomendados para começar podem ser **ReadOnlyUsers,** **WriteAccessUsers**e **FullAccessUsers** para a raiz da conta, e até mesmo separar os para subpastas chave. Se houver outros grupos de utilizadores antecipados que possam ser adicionados mais tarde, mas que ainda não foram identificados, poderá considerar a criação de grupos de segurança manequim que tenham acesso a determinadas pastas. A utilização do grupo de segurança garante que mais tarde não precisa de um longo tempo de processamento para atribuir novas permissões a milhares de ficheiros.

### <a name="security-for-service-principals"></a>Segurança para os diretores de serviço

Os principais diretores de serviços do Azure Ative Directory são normalmente utilizados por serviços como o Azure HDInsight para aceder a dados em Data Lake Storage Gen1. Dependendo dos requisitos de acesso em várias cargas de trabalho, pode haver algumas considerações para garantir a segurança dentro e fora da organização. Para muitos clientes, um único diretor de serviço azure ative diretório pode ser adequado, e pode ter permissões completas na raiz da conta Data Lake Storage Gen1. Outros clientes podem exigir vários clusters com diferentes diretores de serviço onde um cluster tem acesso total aos dados, e outro cluster com apenas acesso lido. Tal como acontece com os grupos de segurança, pode considerar fazer um principal de serviço para cada cenário previsto (ler, escrever, cheio) assim que for criada uma conta Data Lake Storage Gen1.

### <a name="enable-the-data-lake-storage-gen1-firewall-with-azure-service-access"></a>Ativar a firewall Gen1 de armazenamento de data Lake com acesso ao serviço Azure

Data Lake Storage Gen1 suporta a opção de ligar uma firewall e limitar o acesso apenas aos serviços Azure, que é recomendado para um vetor de ataque menor de intrusões externas. A firewall pode ser ativada na conta Data Lake Storage Gen1 no portal Azure através do **Firewall Enable Firewall** > **(ON)** > Permitir o acesso às opções de**serviços Azure.**

![Definições de firewall em Data Lake Storage Gen1](./media/data-lake-store-best-practices/data-lake-store-firewall-setting.png "Definições de firewall em Data Lake Storage Gen1")

Uma vez ativada a firewall, apenas os serviços Azure, tais como HDInsight, Data Factory, SQL Data Warehouse, etc. têm acesso ao Data Lake Storage Gen1. Devido à tradução interna de endereços de rede utilizada pelo Azure, a firewall Data Lake Storage Gen1 não suporta restringir serviços específicos por IP e destina-se apenas a restrições de pontos finais fora do Azure, como no local.

## <a name="performance-and-scale-considerations"></a>Considerações de desempenho e escala

Uma das características mais poderosas do Data Lake Storage Gen1 é que remove os limites rígidos na entrada de dados. A eliminação dos limites permite que os clientes aumentem o seu tamanho de dados e acompanhem os requisitos de desempenho sem necessidade de fragmentos dos dados. Uma das considerações mais importantes para otimizar o desempenho do Data Lake Storage Gen1 é que ele tem o melhor desempenho quando dado o paralelismo.

### <a name="improve-throughput-with-parallelism"></a>Melhorar a entrada com paralelismo

Considere dar 8-12 fios por núcleo para a melhor leitura/escrita. Isto deve-se ao bloqueio de leituras/escritos num único fio, e mais fios podem permitir uma maior conmoedação no VM. Para garantir que os níveis são saudáveis e o paralelismo pode ser aumentado, certifique-se de monitorizar a utilização do CpU do VM.

### <a name="avoid-small-file-sizes"></a>Evite pequenos tamanhos de ficheiros

As permissões posix e auditoria sintetidade no Data Lake Storage Gen1 vêm com uma sobrecarga que se torna evidente ao trabalhar com numerosos pequenos ficheiros. Como uma boa prática, você deve colocar os seus dados em ficheiros maiores versus escrever milhares ou milhões de pequenos ficheiros para Data Lake Storage Gen1. Evitar tamanhos de ficheiros pequenos pode ter múltiplos benefícios, tais como:

* Reduzir as verificações de autenticação em vários ficheiros
* Ligações de ficheiros abertos reduzidos
* Cópia/replicação mais rápida
* Menos ficheiros para processar ao atualizar permissões de Armazenamento de Data Lake Gen1 POSIX

Dependendo dos serviços e cargas de trabalho que estão a utilizar os dados, uma boa dimensão a considerar para ficheiros é de 256 MB ou superior. Se os tamanhos dos ficheiros não puderem ser loteados ao aterrar em Data Lake Storage Gen1, pode ter um trabalho de compactação separado que combina estes ficheiros em maiores. Para obter mais informações e recomendações sobre o tamanho dos ficheiros e organização dos dados em Data Lake Storage Gen1, consulte [Estruturação do seu conjunto de dados](data-lake-store-performance-tuning-guidance.md#structure-your-data-set).

### <a name="large-file-sizes-and-potential-performance-impact"></a>Grandes tamanhos de ficheiros e impacto potencial de desempenho

Embora data Lake Storage Gen1 suporte grandes ficheiros até petabytes em tamanho, para um desempenho ótimo e dependendo do processo de leitura dos dados, pode não ser ideal ir acima de 2 GB em média. Por exemplo, ao utilizar o **Distcp** para copiar dados entre locais ou diferentes contas de armazenamento, os ficheiros são o melhor nível de granularidade utilizado para determinar as tarefas do mapa. Portanto, se estiver a copiar 10 ficheiros que são 1 TB cada, no máximo 10 mappers são atribuídos. Além disso, se tiver muitos ficheiros com mappers atribuídos, inicialmente os mappers funcionam em paralelo para mover ficheiros grandes. No entanto, à medida que o trabalho começa a acabar, restam apenas alguns mappers e você pode ficar preso com um único mapper atribuído a um grande arquivo. A Microsoft submeteu melhorias à Distcp para abordar este problema em futuras versões Hadoop.

Outro exemplo a ter em conta é quando se utiliza o Azure Data Lake Analytics com data lake storage Gen1. Dependendo do processamento feito pelo extrator, alguns ficheiros que não podem ser divididos (por exemplo, XML, JSON) podem sofrer no desempenho quando superiores a 2 GB. Nos casos em que os ficheiros podem ser divididos por um extrator (por exemplo, CSV), os ficheiros grandes são preferidos.

### <a name="capacity-plan-for-your-workload"></a>Plano de capacidade para a sua carga de trabalho

Azure Data Lake Storage Gen1 remove os rígidos limites de estrangulamento de IO que são colocados nas contas de armazenamento blob. No entanto, ainda há limites suaves que devem ser considerados. Os limites de estrangulamento/egress padrão satisfazem as necessidades da maioria dos cenários. Se a sua carga de trabalho precisar de aumentar os limites, trabalhe com o suporte da Microsoft. Além disso, olhe para os limites durante a fase de prova de conceito para que os limites de estrangulamento da OI não sejam atingidos durante a produção. Se isso acontecer, poderá ser necessário esperar por um aumento manual da equipa de engenharia da Microsoft. Se ocorrer estrangulamento da IO, o Azure Data Lake Storage Gen1 devolve um código de erro de 429, e idealmente deve ser novamente julgado com uma política de backoff exponencial apropriada.

### <a name="optimize-writes-with-the-data-lake-storage-gen1-driver-buffer"></a>Otimizar "escreve" com o tampão de condutor gen1 de armazenamento de data Lake

Para otimizar o desempenho e reduzir o IOPS ao escrever para data Lake Storage Gen1 de Hadoop, execute operações de escrita o mais próximo possível do tamanho do tampão do condutor do Data Lake Storage Gen1. Tente não exceder o tamanho do tampão antes de descarregar, como ao transmitir com cargas de trabalho de streaming Apache Storm ou Spark. Ao escrever para Data Lake Storage Gen1 da HDInsight/Hadoop, é importante saber que data Lake Storage Gen1 tem um motorista com um tampão de 4 MB. Como muitos controladores do sistema de ficheiros, este tampão pode ser lavado manualmente antes de atingir o tamanho de 4 MB. Caso contrário, é imediatamente lavado para armazenamento se a próxima escrita exceder o tamanho máximo do tampão. Sempre que possível, deve evitar uma sobrerdição ou uma subuma significativa do tampão ao sincronizar/limpar a política por contagem ou janela de tempo.

## <a name="resiliency-considerations"></a>Considerações de resiliência

Ao arquitetar um sistema com Data Lake Storage Gen1 ou qualquer serviço na nuvem, você deve considerar os seus requisitos de disponibilidade e como responder a possíveis interrupções no serviço. Uma questão poderia ser localizada para o caso específico ou mesmo para toda a região, pelo que ter um plano para ambos é importante. Dependendo do objetivo de tempo de **recuperação** e do objetivo de **recuperação** sLAs para a sua carga de trabalho, você pode escolher uma estratégia mais ou menos agressiva para alta disponibilidade e recuperação de desastres.

### <a name="high-availability-and-disaster-recovery"></a>Elevada disponibilidade e recuperação após desastre

A elevada disponibilidade (HA) e a recuperação de desastres (DR) podem por vezes ser combinadas em conjunto, embora cada um tenha uma estratégia ligeiramente diferente, especialmente quando se trata de dados. Data Lake Storage Gen1 já lida com replicação 3x sob o capot para proteger contra falhas de hardware localizadas. No entanto, uma vez que a replicação entre regiões não está incorporada, deve gerir isto por si mesmo. Ao construir um plano para ha, em caso de interrupção do serviço, a carga de trabalho precisa de ter acesso aos dados mais recentes o mais rapidamente possível, passando para um caso separado replicado localmente ou numa nova região.

Numa estratégia de DR, para se preparar para o caso improvável de uma falha catastrófica de uma região, também é importante que os dados sejam replicados para uma região diferente. Estes dados podem inicialmente ser os mesmos que os dados de HA replicados. No entanto, também deve considerar os seus requisitos para casos de borda, como a corrupção de dados, onde pode querer criar instantâneos periódicos para voltar atrás. Dependendo da importância e dimensão dos dados, considere instantâneos delta rolantes de períodos de 1, 6 e 24 horas na loja local e/ou secundária, de acordo com as tolerâncias de risco.

Para a resiliência de dados com data Lake Storage Gen1, recomenda-se geo-replicar os seus dados para uma região separada com uma frequência que satisfaça os seus requisitos de HA/DR, idealmente a cada hora. Esta frequência de replicação minimiza movimentos maciços de dados que podem ter necessidades de produção concorrentes com o sistema principal e um melhor objetivo de ponto de recuperação (RPO). Além disso, deve considerar formas de a aplicação utilizando data Lake Storage Gen1 falhar automaticamente na conta secundária através de disparos ou comprimento de tentativas falhadas, ou pelo menos enviar uma notificação aos administradores para intervenção manual. Tenha em mente que há trocas de falhas contra esperar que um serviço volte a funcionar. Se os dados não terminarem a replicação, uma falha pode causar perda de dados, inconsistência ou fusão complexa dos dados.

Abaixo estão as três opções recomendadas para orquestrar a replicação entre as contas da Data Lake Storage Gen1, e as principais diferenças entre cada uma delas.

|  |Distcp  |Azure Data Factory  |AdlCopy  |
|---------|---------|---------|---------|
|**Limites de escala**     | Delimitado por nódosos operários        | Limitado por unidades de Movimento de Dados Max Cloud        | Vinculado por unidades de Analytics        |
|**Suporta copiar deltas**     |   Sim      | Não         | Não         |
|**Orquestração embutida**     |  Não (use Oozie Airflow ou trabalhos de sarjeta)       | Sim        | Não (Utilizar automação azure ou programador de tarefas do Windows)         |
|**Sistemas de ficheiros suportados**     | ADL, HDFS, WASB, S3, GS, CFS        |Numerosos, ver [Conectores.](../data-factory/connector-azure-blob-storage.md)         | ADL para ADL, WASB para ADL (apenas na mesma região)        |
|**Apoio ao OS**     |Qualquer Sistema operativo Hadoop         | N/D          | Windows 10         |

### <a name="use-distcp-for-data-movement-between-two-locations"></a>Utilizar a Distcp para a circulação de dados entre dois locais

Abreviada para cópia distribuída, distcp é uma ferramenta de linha de comando Linux que vem com Hadoop e fornece movimento de dados distribuídos entre dois locais. As duas localizações podem ser Data Lake Storage Gen1, HDFS, WASB ou S3. Esta ferramenta utiliza mapReduce jobs num cluster Hadoop (por exemplo, HDInsight) para escalar todos os nós. A Distcp é considerada a forma mais rápida de mover big data sem aparelhos especiais de compressão de rede. A Distcp também oferece uma opção para apenas atualizar deltas entre dois locais, lidar com repetições automáticas, bem como escala dinâmica de computação. Esta abordagem é incrivelmente eficiente quando se trata de replicar coisas como as tabelas Hive/Spark que podem ter muitos ficheiros grandes num único diretório e só quer copiar sobre os dados modificados. Por estas razões, a Distcp é a ferramenta mais recomendada para copiar dados entre grandes lojas de dados.

Os trabalhos de cópia podem ser desencadeados por fluxos de trabalho apache Oozie usando freqüência ou gatilhos de dados, bem como empregos de cron. Para trabalhos de replicação intensiva, recomenda-se que se elegire um cluster de Hadoop HDInsight separado que possa ser ajustado e dimensionado especificamente para os trabalhos de cópia. Isto garante que os trabalhos de cópia não interferem com empregos críticos. Se executar a replicação numa frequência suficientemente larga, o cluster pode mesmo ser retirado entre cada trabalho. Se falhar na região secundária, certifique-se de que outro cluster também é criado na região secundária para replicar novos dados de volta à conta principal de Data Lake Storage Gen1 assim que voltar a subir. Por exemplo, na utilização do Distcp, consulte [Use Distcp para copiar dados entre as Bolhas](data-lake-store-copy-data-wasb-distcp.md)de Armazenamento de Azure e o Data Lake Storage Gen1 .

### <a name="use-azure-data-factory-to-schedule-copy-jobs"></a>Use a Fábrica de Dados Azure para agendar trabalhos de cópia

A Azure Data Factory também pode ser usada para agendar trabalhos de cópia utilizando uma Atividade de **Cópia,** e pode até ser configurada numa frequência através do **Assistente de Cópia**. Tenha em mente que a Azure Data Factory tem um limite de unidades de movimento de dados em nuvem (DMUs), e eventualmente cobre a produção/computação para grandes cargas de trabalho de dados. Além disso, a Azure Data Factory atualmente não oferece atualizações delta entre contas data Lake Storage Gen1, por isso pastas como as tabelas da Hive exigiriam uma cópia completa para replicar. Consulte o guia de [afinação](../data-factory/copy-activity-performance.md) da Atividade de Cópia para obter mais informações sobre a cópia com a Data Factory.

### <a name="adlcopy"></a>AdlCopy

O AdlCopy é uma ferramenta de linha de comando do Windows que permite copiar dados entre duas contas de Data Lake Storage Gen1 apenas dentro da mesma região. A ferramenta AdlCopy fornece uma opção autónoma ou a opção de usar uma conta Azure Data Lake Analytics para executar o seu trabalho de cópia. Embora tenha sido originalmente construído para cópias a pedido em oposição a uma replicação robusta, fornece outra opção para fazer cópias distribuídas através de contas de Data Lake Storage Gen1 dentro da mesma região. Para fiabilidade, é aconselhável utilizar a opção premium Data Lake Analytics para qualquer carga de trabalho de produção. A versão autónoma pode devolver respostas movimentadas e tem escala e monitorização limitadas.

Tal como a Distcp, o AdlCopy precisa de ser orquestrado por algo como o Azure Automation ou o Windows Task Scheduler. Tal como acontece com a Data Factory, a AdlCopy não suporta copiar apenas ficheiros atualizados, mas recopia e substitui ficheiros existentes. Para obter mais informações e exemplos de utilização do AdlCopy, consulte [os dados de cópia sinuosos do Azure Storage Blobs para data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md).

## <a name="monitoring-considerations"></a>Considerações de acompanhamento

Data Lake Storage Gen1 fornece registos de diagnóstico detalhados e auditoria. Data Lake Storage Gen1 fornece algumas métricas básicas no portal Azure sob a conta Data Lake Storage Gen1 e no Azure Monitor. A disponibilidade de Data Lake Storage Gen1 é exibida no portal Azure. No entanto, esta métrica é refrescada a cada sete minutos e não pode ser consultada através de uma API publicamente exposta. Para obter a disponibilidade mais atualizada de uma conta Gen1 de Armazenamento de Data Lake, você deve executar seus próprios testes sintéticos para validar a disponibilidade. Outras métricas, tais como a utilização total do armazenamento, pedidos de leitura/escrita e entradas/saídas podem demorar até 24 horas a refrescar-se. Assim, métricas mais atualizadas devem ser calculadas manualmente através de ferramentas de linha de comando Hadoop ou de agregação de informações de registo. A forma mais rápida de obter a mais recente utilização de armazenamento é executar este comando HDFS a partir de um nó de aglomerado hadoop (por exemplo, nó de cabeça):

    hdfs dfs -du -s -h adl://<adlsg1_account_name>.azuredatalakestore.net:443/

### <a name="export-data-lake-storage-gen1-diagnostics"></a>Diagnósticos de Armazenamento de Lago de Exportação Gen1

Uma das formas mais rápidas de ter acesso a registos pesquisáveis a partir do Data Lake Storage Gen1 é permitir o envio de log **para Log Analytics** sob a lâmina de **diagnóstico** para a conta Data Lake Storage Gen1. Isto fornece acesso imediato a registos de entrada com filtros de tempo e conteúdo, juntamente com opções de alerta (e-mail/webhook) desencadeadas dentro de intervalos de 15 minutos. Para obter instruções, consulte [o acesso aos registos de diagnóstico do Azure Data Lake Storage Gen1](data-lake-store-diagnostic-logs.md).

Para alertar mais em tempo real e mais controlo sobre onde aterrar os registos, considere exportar registos para o Azure EventHub, onde o conteúdo pode ser analisado individualmente ou ao longo de uma janela temporal para submeter notificações em tempo real a uma fila. Uma aplicação separada, como uma [App Lógica,](../connectors/connectors-create-api-azure-event-hubs.md) pode então consumir e comunicar os alertas para o canal apropriado, bem como submeter métricas a ferramentas de monitorização como NewRelic, Datadog ou AppDynamics. Em alternativa, se estiver a utilizar uma ferramenta de terceiros, como o ElasticSearch, pode exportar os registos para o Blob Storage e utilizar o [plugin De Logstash Azure](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob) para consumir os dados na sua stack Elasticsearch, Kibana e Logstash (ELK).

### <a name="turn-on-debug-level-logging-in-hdinsight"></a>Ligue a exploração madeireira de nível de depuração no HDInsight

Se o envio de registo slog slog Da Data Lake Storage Gen1 não estiver ligado, o Azure HDInsight também fornece uma forma de ativar a [exploração do lado do cliente para data Lake Storage Gen1](data-lake-store-performance-tuning-mapreduce.md) via log4j. Deve definir a seguinte propriedade em**Configurações avançadas de fios-log4j** **ambari** > **YARN** > **Config:** > 

    log4j.logger.com.microsoft.azure.datalake.store=DEBUG

Uma vez que a propriedade é definida e os nós são reiniciados, os diagnósticos data Lake Storage Gen1\<\>são escritos para os registos de Arn nos nós (/tmp/ user /yarn.log), e detalhes importantes como erros ou estrangulamento (código de erro HTTP 429) podem ser monitorizados. Estas mesmas informações também podem ser monitorizadas nos registos do Monitor Azure ou onde os registos são enviados na lâmina de [diagnóstico](data-lake-store-diagnostic-logs.md) da conta Data Lake Storage Gen1. Recomenda-se que pelo menos o registo do lado do cliente esteja ligado ou utilize a opção de envio de registo slog com data lake Storage Gen1 para visibilidade operacional e depuração mais fácil.

### <a name="run-synthetic-transactions"></a>Executar transações sintéticas

Atualmente, a métrica de disponibilidade de serviço para Data Lake Storage Gen1 no portal Azure tem janela de atualização de 7 minutos. Além disso, não pode ser consultado usando uma API publicamente exposta. Assim, recomenda-se a construção de uma aplicação básica que faça transações sintéticas para data Lake Storage Gen1 que pode fornecer até a disponibilidade minúscula. Um exemplo pode ser criar uma Aplicação WebJob, Logic app ou Azure Function App para realizar uma leitura, criação e atualização contra data Lake Storage Gen1 e enviar os resultados para a sua solução de monitorização. As operações podem ser efetuadas numa pasta temporária e depois eliminadas após o teste, que pode ser executada a cada 30-60 segundos, dependendo dos requisitos.

## <a name="directory-layout-considerations"></a>Considerações de layout de diretório

Ao aterrar dados num lago de dados, é importante pré-planear a estrutura dos dados para que a segurança, a divisão e o processamento possam ser utilizados de forma eficaz. Muitas das seguintes recomendações podem ser usadas quer seja com Azure Data Lake Storage Gen1, Blob Storage ou HDFS. Cada carga de trabalho tem diferentes requisitos sobre como os dados são consumidos, mas abaixo estão alguns layouts comuns a considerar quando se trabalha com cenários de IoT e lote.

### <a name="iot-structure"></a>Estrutura ioT

Nas cargas de trabalho do IoT, pode haver uma grande quantidade de dados a serem desembarcados na loja de dados que se estende por inúmeros produtos, dispositivos, organizações e clientes. É importante pré-planear o layout do diretório para organização, segurança e processamento eficiente dos dados para consumidores de streaming. Um modelo geral a considerar pode ser o seguinte layout:

    {Region}/{SubjectMatter(s)}/{yyyy}/{mm}/{dd}/{hh}/

Por exemplo, a telemetria de aterragem de um motor de avião no Reino Unido pode parecer a seguinte estrutura:

    UK/Planes/BA1293/Engine1/2017/08/11/12/

Há uma razão importante para colocar a data no final da estrutura da pasta. Se quiser bloquear certas regiões ou sujeitar assuntos a utilizadores/grupos, então pode fazê-lo facilmente com as permissões POSIX. Caso contrário, se houvesse a necessidade de restringir um determinado grupo de segurança à visualização apenas dos dados do Reino Unido ou de certos aviões, seria necessária uma autorização separada para numerosas pastas sob cada pasta de hora a hora. Além disso, ter a estrutura da data à frente aumentaria exponencialmente o número de pastas à medida que o tempo passava.

### <a name="batch-jobs-structure"></a>Estrutura de trabalhos de lote

A partir de um nível elevado, uma abordagem comumente usada no processamento de lotes é para aterrar dados numa pasta "in". Em seguida, uma vez que os dados são processados, coloque os novos dados numa pasta "out" para os processos a jusante consumirem. Esta estrutura de diretório é vista, por vezes, para trabalhos que exigem o processamento de ficheiros individuais e podem não exigir um processamento maciço paralelo sobre grandes conjuntos de dados. Tal como a estrutura ioT recomendada acima, uma boa estrutura de diretório tem as pastas de nível dos pais para coisas como a região e assuntos (por exemplo, organização, produto/produtor). Esta estrutura ajuda a garantir os dados em toda a sua organização e a uma melhor gestão dos dados nas suas cargas de trabalho. Além disso, considere a data e a hora na estrutura para permitir uma melhor organização, pesquisas filtradas, segurança e automação no processamento. O nível de granularidade para a estrutura da data é determinado pelo intervalo em que os dados são carregados ou processados, como por hora, diariamente ou mesmo mensalmente.

Por vezes, o processamento de ficheiros não é bem sucedido devido à corrupção de dados ou formatos inesperados. Nesses casos, a estrutura do diretório pode beneficiar de uma pasta **/mau** para mover os ficheiros para posterior inspeção. O trabalho do lote também pode tratar do relatório ou notificação destes *ficheiros maus* para intervenção manual. Considere a seguinte estrutura do modelo:

    {Region}/{SubjectMatter(s)}/In/{yyyy}/{mm}/{dd}/{hh}/
    {Region}/{SubjectMatter(s)}/Out/{yyyy}/{mm}/{dd}/{hh}/
    {Region}/{SubjectMatter(s)}/Bad/{yyyy}/{mm}/{dd}/{hh}/

Por exemplo, uma empresa de marketing recebe diariamente extratos de dados de atualizações de clientes dos seus clientes na América do Norte. Pode parecer o seguinte corte antes e depois de ser processado:

    NA/Extracts/ACMEPaperCo/In/2017/08/14/updates_08142017.csv
    NA/Extracts/ACMEPaperCo/Out/2017/08/14/processed_updates_08142017.csv

No caso comum de os dados do lote serem processados diretamente em bases de dados como a Hive ou as bases de dados tradicionais sQL, não há necessidade de uma pasta **/dentro** ou **fora,** uma vez que a saída já vai para uma pasta separada para a tabela da Colmeia ou bases de dados externas. Por exemplo, extratos diários de clientes aterrariam nas suas respetivas pastas, e a orquestração por algo como a Azure Data Factory, Apache Oozie ou Apache Airflow desencadearia um trabalho diário de Hive ou Spark para processar e escrever os dados numa tabela da Colmeia.

## <a name="next-steps"></a>Passos seguintes

* [Visão geral do Armazenamento de Lagos De Dados Azure Gen1](data-lake-store-overview.md)
* [Controlo de Acesso em Azure Data Lake Storage Gen1](data-lake-store-access-control.md)
* [Segurança em Azure Data Lake Storage Gen1](data-lake-store-security-overview.md)
* [Tuning Azure Data Lake Storage Gen1 para desempenho](data-lake-store-performance-tuning-guidance.md)
* [Orientação de afinação de desempenho para a utilização de HDInsight Spark com Azure Data Lake Storage Gen1](data-lake-store-performance-tuning-spark.md)
* [Orientação de afinação de desempenho para a utilização da Hive HDInsight com O Armazenamento de Lagos Azure Gen1](data-lake-store-performance-tuning-hive.md)
* [Crie clusters HDInsight com Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
