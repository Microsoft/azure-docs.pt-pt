---
title: Práticas recomendadas para usar o Azure Data Lake Storage Gen1 | Microsoft Docs
description: Conheça as práticas recomendadas sobre ingestão de dados, segurança de data e desempenho relacionados ao uso de Azure Data Lake Storage Gen1 (anteriormente conhecido como Azure Data Lake Store)
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
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75638940"
---
# <a name="best-practices-for-using-azure-data-lake-storage-gen1"></a>Práticas recomendadas para usar o Azure Data Lake Storage Gen1

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Neste artigo, você aprenderá sobre as práticas recomendadas e as considerações para trabalhar com Azure Data Lake Storage Gen1. Este artigo fornece informações sobre segurança, desempenho, resiliência e monitoramento para Data Lake Storage Gen1. Antes de Data Lake Storage Gen1, trabalhar com verdadeiramente Big Data em serviços como o Azure HDInsight era complexo. Você precisava fragmentar dados em várias contas de armazenamento de BLOBs para que o armazenamento de petabyte e o desempenho ideal nessa escala pudessem ser atingidos. Com Data Lake Storage Gen1, a maioria dos limites rígidos de tamanho e desempenho são removidos. No entanto, ainda há algumas considerações que este artigo aborda para que você possa obter o melhor desempenho com Data Lake Storage Gen1.

## <a name="security-considerations"></a>Considerações de segurança

O Azure Data Lake Storage Gen1 oferece controles de acesso POSIX e auditoria detalhada para usuários, grupos e entidades de serviço do Azure Active Directory (Azure AD). Esses controles de acesso podem ser definidos para arquivos e pastas existentes. Os controles de acesso também podem ser usados para criar padrões que podem ser aplicados a novos arquivos ou pastas. Quando as permissões são definidas para pastas e objetos filho existentes, as permissões precisam ser propagadas recursivamente em cada objeto. Se houver um grande número de arquivos, a propagação das permissões poderá levar muito tempo. O tempo gasto pode variar entre 30-50 objetos processados por segundo. Portanto, planeje a estrutura de pastas e os grupos de usuários adequadamente. Caso contrário, isso pode causar atrasos e problemas imprevistos quando você trabalha com seus dados.

Suponha que você tenha uma pasta com 100.000 objetos filho. Se você assumir o limite inferior de 30 objetos processados por segundo, para atualizar a permissão para a pasta inteira poderá levar uma hora. Mais detalhes sobre Data Lake Storage Gen1 ACLs estão disponíveis no [controle de acesso no Azure data Lake Storage Gen1](data-lake-store-access-control.md). Para melhorar o desempenho na atribuição de ACLs recursivamente, você pode usar a ferramenta de linha de comando Azure Data Lake. A ferramenta cria vários threads e lógica de navegação recursiva para aplicar ACLs rapidamente a milhões de arquivos. A ferramenta está disponível para Linux e Windows, e a [documentação](https://github.com/Azure/data-lake-adlstool) e os [downloads](https://aka.ms/adlstool-download) para essa ferramenta podem ser encontrados no github. Essas mesmas melhorias de desempenho podem ser habilitadas por suas próprias ferramentas escritas com os SDKs Data Lake Storage Gen1 [.net](data-lake-store-data-operations-net-sdk.md) e [Java](data-lake-store-get-started-java-sdk.md) .

### <a name="use-security-groups-versus-individual-users"></a>Usar grupos de segurança versus usuários individuais

Ao trabalhar com Big Data no Data Lake Storage Gen1, provavelmente uma entidade de serviço é usada para permitir que serviços como o Azure HDInsight trabalhem com os dados. No entanto, pode haver casos em que os usuários individuais precisam acessar os dados também. Nesses casos, você deve usar Azure Active Directory [grupos de segurança](data-lake-store-secure-data.md#create-security-groups-in-azure-active-directory) em vez de atribuir usuários individuais a pastas e arquivos.

Depois que um grupo de segurança recebe permissões, adicionar ou remover usuários do grupo não exige nenhuma atualização para Data Lake Storage Gen1. Isso também ajuda a garantir que você não exceda o limite de [acesso 32 e ACLs padrão](../azure-resource-manager/management/azure-subscription-service-limits.md#data-lake-store-limits) (isso inclui as quatro ACLs de estilo POSIX que estão sempre associadas a cada arquivo e pasta: [o usuário proprietário](data-lake-store-access-control.md#the-owning-user), [o grupo proprietário](data-lake-store-access-control.md#the-owning-group), [a máscara](data-lake-store-access-control.md#the-mask)e outros).

### <a name="security-for-groups"></a>Segurança para grupos

Conforme discutido, quando os usuários precisam de acesso ao Data Lake Storage Gen1, é melhor usar Azure Active Directory grupos de segurança. Alguns grupos recomendados para começar podem ser **ReadOnlyUsers**, **WriteAccessUsers**e **FullAccessUsers** para a raiz da conta e até mesmo separá-los para subpastas de chave. Se houver outros grupos de usuários previstos que possam ser adicionados posteriormente, mas ainda não tiverem sido identificados, você poderá considerar a criação de grupos de segurança fictícios que tenham acesso a determinadas pastas. O uso do grupo de segurança garante que, mais tarde, você não precise de um tempo de processamento longo para atribuir novas permissões a milhares de arquivos.

### <a name="security-for-service-principals"></a>Segurança para entidades de serviço

Azure Active Directory entidades de serviço geralmente são usadas por serviços como o Azure HDInsight para acessar dados no Data Lake Storage Gen1. Dependendo dos requisitos de acesso em várias cargas de trabalho, pode haver algumas considerações para garantir a segurança dentro e fora da organização. Para muitos clientes, uma única entidade de serviço de Azure Active Directory pode ser adequada e pode ter permissões totais na raiz da conta de Data Lake Storage Gen1. Outros clientes podem exigir vários clusters com entidades de serviço diferentes, em que um cluster tem acesso completo aos dados e outro cluster com acesso de leitura. Assim como nos grupos de segurança, você pode considerar tornar uma entidade de serviço para cada cenário antecipado (leitura, gravação, total) quando uma conta de Data Lake Storage Gen1 for criada.

### <a name="enable-the-data-lake-storage-gen1-firewall-with-azure-service-access"></a>Habilitar o Data Lake Storage Gen1 firewall com acesso de serviço do Azure

Data Lake Storage Gen1 dá suporte à opção de ativar um firewall e limitar o acesso somente aos serviços do Azure, o que é recomendado para um vetor de ataque menor de intrusões externas. O firewall pode ser habilitado na conta de Data Lake Storage Gen1 no portal do Azure por meio do **firewall** > **habilitar o firewall (ativado)**  > **permitir acesso às opções de serviços do Azure** .

![Configurações de firewall no Data Lake Storage Gen1](./media/data-lake-store-best-practices/data-lake-store-firewall-setting.png "Configurações de firewall no Data Lake Storage Gen1")

Quando o firewall está habilitado, somente os serviços do Azure, como HDInsight, Data Factory, SQL Data Warehouse, etc. têm acesso ao Data Lake Storage Gen1. Devido à conversão de endereços de rede interna usada pelo Azure, o firewall de Data Lake Storage Gen1 não dá suporte à restrição de serviços específicos por IP e destina-se apenas a restrições de pontos de extremidade fora do Azure, como no local.

## <a name="performance-and-scale-considerations"></a>Considerações sobre desempenho e escala

Um dos recursos mais poderosos do Data Lake Storage Gen1 é que ele remove os limites rígidos de taxa de transferência de dados. A remoção dos limites permite que os clientes aumentem seu tamanho de dados e acompanham os requisitos de desempenho sem a necessidade de fragmentar os dados. Uma das considerações mais importantes para otimizar o desempenho de Data Lake Storage Gen1 é que ele executa o melhor quando determinado paralelismo.

### <a name="improve-throughput-with-parallelism"></a>Melhorar a taxa de transferência com paralelismo

Considere fornecer 8-12 threads por núcleo para obter a taxa de transferência de leitura/gravação ideal. Isso se deve ao bloqueio de leituras/gravações em um único thread, e mais threads podem permitir maior simultaneidade na VM. Para garantir que os níveis sejam íntegros e o paralelismo possa ser aumentado, certifique-se de monitorar a utilização da CPU da VM.

### <a name="avoid-small-file-sizes"></a>Evitar pequenos tamanhos de arquivo

As permissões de POSIX e a auditoria no Data Lake Storage Gen1 vêm com uma sobrecarga que se torna aparente ao trabalhar com vários arquivos pequenos. Como prática recomendada, você deve armazenar em lote seus dados em arquivos maiores em vez de escrever milhares ou milhões de arquivos pequenos para Data Lake Storage Gen1. Evitar tamanhos de arquivos pequenos pode ter vários benefícios, como:

* Reduzindo as verificações de autenticação em vários arquivos
* Conexões de arquivo aberto reduzidas
* Cópia/replicação mais rápida
* Menos arquivos a serem processados ao atualizar Data Lake Storage Gen1 permissões POSIX

Dependendo de quais serviços e cargas de trabalho estão usando os dados, um bom tamanho a ser levado em consideração para os arquivos é de 256 MB ou mais. Se os tamanhos de arquivo não puderem ser agrupados em lote quando o patamar for Data Lake Storage Gen1, você poderá ter um trabalho de compactação separado que combine esses arquivos em maiores. Para obter mais informações e recomendações sobre tamanhos de arquivo e organizar os dados em Data Lake Storage Gen1, consulte [estruturar seu conjunto de dados](data-lake-store-performance-tuning-guidance.md#structure-your-data-set).

### <a name="large-file-sizes-and-potential-performance-impact"></a>Tamanhos de arquivos grandes e impacto no desempenho potencial

Embora Data Lake Storage Gen1 dê suporte a arquivos grandes de até petabytes de tamanho, para obter um desempenho ideal e, dependendo do processo que lê os dados, pode não ser ideal ir acima de 2 GB em média. Por exemplo, ao usar **Distcp** para copiar dados entre locais ou contas de armazenamento diferentes, os arquivos são o nível mais refinado de granularidade usado para determinar as tarefas de mapa. Portanto, se você estiver copiando 10 arquivos que são de 1 TB cada, no máximo 10 Mapeadores serão alocados. Além disso, se você tiver muitos arquivos com Mapeadores atribuídos, inicialmente os Mapeadores funcionarão em paralelo para mover arquivos grandes. No entanto, à medida que o trabalho começa a fazer o vento, apenas alguns Mapeadores permanecem alocados e você pode ficar preso a um único mapeador atribuído a um arquivo grande. A Microsoft enviou melhorias ao Distcp para resolver esse problema em versões futuras do Hadoop.

Outro exemplo a ser considerado é ao usar Azure Data Lake Analytics com Data Lake Storage Gen1. Dependendo do processamento feito pelo extrator, alguns arquivos que não podem ser divididos (por exemplo, XML, JSON) podem sofrer desempenho quando maior que 2 GB. Nos casos em que os arquivos podem ser divididos por um extrator (por exemplo, CSV), os arquivos grandes são preferenciais.

### <a name="capacity-plan-for-your-workload"></a>Plano de capacidade para sua carga de trabalho

Azure Data Lake Storage Gen1 remove os limites de limitação de e/s rígidos que são colocados em contas de armazenamento de BLOBs. No entanto, ainda há limites flexíveis que precisam ser considerados. Os limites de limitação de entrada/saída padrão atendem às necessidades da maioria dos cenários. Se sua carga de trabalho precisar ter os limites aumentados, trabalhe com o suporte da Microsoft. Além disso, examine os limites durante o estágio de prova de conceito para que os limites de limitação de e/s não sejam atingidos durante a produção. Se isso acontecer, pode ser necessário aguardar um aumento manual da equipe de engenharia da Microsoft. Se a limitação de e/s ocorrer, Azure Data Lake Storage Gen1 retornará um código de erro 429 e, idealmente, deverá ser repetida com uma política de retirada exponencial apropriada.

### <a name="optimize-writes-with-the-data-lake-storage-gen1-driver-buffer"></a>Otimizar "gravações" com o buffer do driver de Data Lake Storage Gen1

Para otimizar o desempenho e reduzir o IOPS ao gravar em Data Lake Storage Gen1 do Hadoop, execute operações de gravação o mais próximo possível do tamanho do buffer do driver Data Lake Storage Gen1. Tente não exceder o tamanho do buffer antes de liberar, como ao transmitir usando Apache Storm ou cargas de trabalho de streaming do Spark. Ao gravar em Data Lake Storage Gen1 do HDInsight/Hadoop, é importante saber que Data Lake Storage Gen1 tem um driver com um buffer de 4 MB. Como muitos drivers de sistema de arquivos, esse buffer pode ser liberado manualmente antes de atingir o tamanho de 4 MB. Caso contrário, ele será liberado imediatamente para armazenamento se a próxima gravação exceder o tamanho máximo do buffer. Sempre que possível, você deve evitar uma saturação ou uma insuficiência significativa do buffer ao sincronizar/liberar a política por contagem ou janela de tempo.

## <a name="resiliency-considerations"></a>Considerações de resiliência

Ao arquitetar um sistema com Data Lake Storage Gen1 ou qualquer serviço de nuvem, você deve considerar seus requisitos de disponibilidade e como responder a possíveis interrupções no serviço. Um problema pode ser localizado na instância específica ou até mesmo em toda a região, então ter um plano para ambos é importante. Dependendo do objetivo do **tempo de recuperação** e dos SLAs de **objetivo de ponto de recuperação** para sua carga de trabalho, você pode escolher uma estratégia mais ou menos agressiva para alta disponibilidade e recuperação de desastres.

### <a name="high-availability-and-disaster-recovery"></a>Elevada disponibilidade e recuperação após desastre

A HA (alta disponibilidade) e a DR (recuperação de desastre) às vezes podem ser combinadas em conjunto, embora cada uma tenha uma estratégia um pouco diferente, especialmente quando se trata de dados. O Data Lake Storage Gen1 já manipula a replicação 3x nos bastidores para proteger contra falhas de hardware localizadas. No entanto, como a replicação entre regiões não é interna, você mesmo deve gerenciá-la. Ao criar um plano de HA, no caso de uma interrupção de serviço, a carga de trabalho precisa ter acesso aos dados mais recentes o mais rápido possível, alternando para uma instância replicada separada localmente ou em uma nova região.

Em uma estratégia de DR, para se preparar para o evento improvável de uma falha catastrófica de uma região, também é importante ter dados replicados em uma região diferente. Esses dados podem inicialmente ser iguais aos dados de HA replicados. No entanto, você também deve considerar seus requisitos para casos de borda, como corrupção de dados, em que você pode desejar criar instantâneos periódicos para o qual fazer fallback. Dependendo da importância e do tamanho dos dados, considere a possibilidade de reverter instantâneos Delta de períodos de 1, 6 e 24 horas no repositório local e/ou secundário, de acordo com as tolerâncias de risco.

Para resiliência de dados com Data Lake Storage Gen1, é recomendável replicar geograficamente seus dados para uma região separada com uma frequência que atenda aos seus requisitos de HA/DR, idealmente a cada hora. Essa frequência de replicação minimiza movimentos de dados maciços que podem ter necessidades de produtividade concorrentes com o sistema principal e um RPO (objetivo de ponto de recuperação) melhor. Além disso, você deve considerar as maneiras para o aplicativo usar Data Lake Storage Gen1 para fazer failover automaticamente na conta secundária por meio de gatilhos de monitoramento ou comprimento de tentativas com falha, ou pelo menos enviar uma notificação aos administradores para intervenção manual. Lembre-se de que há uma compensação de failover em comparação com a espera de um serviço voltar a ficar online. Se os dados não terminarem de replicar, um failover poderá causar potencial perda de dados, inconsistência ou mesclagem complexa dos dados.

Abaixo estão as três principais opções recomendadas para orquestrar a replicação entre contas de Data Lake Storage Gen1 e as principais diferenças entre cada uma delas.

|  |Distcp  |Azure Data Factory  |AdlCopy  |
|---------|---------|---------|---------|
|**Limites de escala**     | Limitado por nós de trabalho        | Limitado por unidades máximas de movimentação de dados na nuvem        | Associado por unidades de análise        |
|**Dá suporte à cópia de deltas**     |   Sim      | Não         | Não         |
|**Orquestração interna**     |  Não (use trabalhos de fluxo de ar Oozie ou cron)       | Sim        | Não (use a automação do Azure ou o Windows Agendador de Tarefas)         |
|**Sistemas de arquivos com suporte**     | ADL, HDFS, WASB, S3, GS, CFS        |Vários, consulte [conectores](../data-factory/connector-azure-blob-storage.md).         | ADL para ADL, WASB para ADL (somente a mesma região)        |
|**Suporte do so**     |Qualquer sistema operacional que esteja executando o Hadoop         | N/A          | Windows 10         |

### <a name="use-distcp-for-data-movement-between-two-locations"></a>Usar Distcp para movimentação de dados entre dois locais

Abreviação de cópia distribuída, o Distcp é uma ferramenta de linha de comando do Linux que vem com o Hadoop e fornece movimentação de dados distribuída entre dois locais. Os dois locais podem ser Data Lake Storage Gen1, HDFS, WASB ou S3. Essa ferramenta usa trabalhos MapReduce em um cluster Hadoop (por exemplo, HDInsight) para escalar horizontalmente em todos os nós. O Distcp é considerado a maneira mais rápida de mover Big Data sem dispositivos de compactação de rede especiais. O Distcp também fornece uma opção para atualizar somente os deltas entre dois locais, lida com tentativas automáticas, bem como o dimensionamento dinâmico da computação. Essa abordagem é incrivelmente eficiente quando se trata de replicar coisas como tabelas de Hive/Spark que podem ter muitos arquivos grandes em um único diretório e você só deseja copiar sobre os dados modificados. Por esses motivos, o Distcp é a ferramenta mais recomendada para copiar dados entre Big Data repositórios.

Os trabalhos de cópia podem ser disparados por fluxos de trabalho do Apache Oozie usando frequência ou gatilhos de dados, bem como trabalhos do Linux cron. Para trabalhos de replicação com uso intensivo, é recomendável criar um cluster HDInsight Hadoop separado que possa ser ajustado e dimensionado especificamente para os trabalhos de cópia. Isso garante que os trabalhos de cópia não interfiram em trabalhos críticos. Se estiver executando a replicação em uma frequência suficientemente ampla, o cluster poderá até ser retirado entre cada trabalho. Se houver failover na região secundária, certifique-se de que outro cluster também seja girado na região secundária para replicar novos dados de volta para a conta de Data Lake Storage Gen1 primária após a realização do backup. Para obter exemplos de como usar Distcp, consulte [usar o Distcp para copiar dados entre os blobs de armazenamento do Azure e data Lake Storage Gen1](data-lake-store-copy-data-wasb-distcp.md).

### <a name="use-azure-data-factory-to-schedule-copy-jobs"></a>Usar Azure Data Factory para agendar trabalhos de cópia

Azure Data Factory também pode ser usado para agendar trabalhos de cópia usando uma **atividade de cópia**e pode até mesmo ser configurado em uma frequência por meio do **Assistente de cópia**. Tenha em mente que Azure Data Factory tem um limite de DMUs (unidades de movimentação de dados de nuvem) e, eventualmente, limita a taxa de transferência/computação para cargas de trabalho de dados grandes. Além disso, Azure Data Factory atualmente não oferece atualizações Delta entre contas de Data Lake Storage Gen1, portanto, pastas como tabelas de Hive exigirão uma cópia completa para replicar. Consulte o [Guia de ajuste da atividade de cópia](../data-factory/copy-activity-performance.md) para obter mais informações sobre como copiar com data Factory.

### <a name="adlcopy"></a>AdlCopy

AdlCopy é uma ferramenta de linha de comando do Windows que permite copiar dados entre duas contas de Data Lake Storage Gen1 apenas na mesma região. A ferramenta AdlCopy fornece uma opção autônoma ou a opção de usar uma conta de Azure Data Lake Analytics para executar o trabalho de cópia. Embora ele tenha sido originalmente compilado para cópias sob demanda em vez de uma replicação robusta, ele fornece outra opção para fazer a cópia distribuída entre Data Lake Storage Gen1 contas na mesma região. Para confiabilidade, é recomendável usar a opção de Data Lake Analytics Premium para qualquer carga de trabalho de produção. A versão autônoma pode retornar respostas ocupadas e tem escala e monitoramento limitados.

Como Distcp, o AdlCopy precisa ser orquestrado por algo como a automação do Azure ou o Windows Agendador de Tarefas. Assim como ocorre com Data Factory, AdlCopy não dá suporte à cópia somente de arquivos atualizados, mas recopia e substitui arquivos existentes. Para obter mais informações e exemplos de como usar AdlCopy, consulte [copiar dados de blobs de armazenamento do Azure para data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md).

## <a name="monitoring-considerations"></a>Considerações sobre a monitorização

Data Lake Storage Gen1 fornece auditoria e logs de diagnóstico detalhados. Data Lake Storage Gen1 fornece algumas métricas básicas no portal do Azure na conta do Data Lake Storage Gen1 e no Azure Monitor. A disponibilidade de Data Lake Storage Gen1 é exibida no portal do Azure. No entanto, essa métrica é atualizada a cada sete minutos e não pode ser consultada por meio de uma API exposta publicamente. Para obter a disponibilidade mais atualizada de uma conta de Data Lake Storage Gen1, você deve executar seus próprios testes sintéticos para validar a disponibilidade. Outras métricas, como a utilização total do armazenamento, as solicitações de leitura/gravação e a entrada/saída podem levar até 24 horas para serem atualizadas. Assim, as métricas mais atualizadas devem ser calculadas manualmente por meio de ferramentas de linha de comando do Hadoop ou agregando informações de log. A maneira mais rápida de obter a utilização de armazenamento mais recente é executar esse comando HDFS em um nó de cluster Hadoop (por exemplo, nó principal):

    hdfs dfs -du -s -h adl://<adlsg1_account_name>.azuredatalakestore.net:443/

### <a name="export-data-lake-storage-gen1-diagnostics"></a>Exportar Data Lake Storage Gen1 diagnóstico

Uma das maneiras mais rápidas de obter acesso aos logs pesquisáveis de Data Lake Storage Gen1 é habilitar o envio de logs para **log Analytics** na folha **diagnóstico** para a conta de data Lake Storage Gen1. Isso fornece acesso imediato aos logs de entrada com filtros de tempo e conteúdo, juntamente com as opções de alerta (email/webhook) disparadas em intervalos de 15 minutos. Para obter instruções, consulte [acessando logs de diagnóstico para Azure data Lake Storage Gen1](data-lake-store-diagnostic-logs.md).

Para obter mais alertas em tempo real e mais controle sobre onde colocar os logs, considere exportar logs para o EventHub do Azure, onde o conteúdo pode ser analisado individualmente ou em uma janela de tempo para enviar notificações em tempo real para uma fila. Um aplicativo separado, como um [aplicativo lógico](../connectors/connectors-create-api-azure-event-hubs.md) , pode consumir e comunicar os alertas para o canal apropriado, bem como enviar métricas para ferramentas de monitoramento como NewRelic, Datadog ou AppDynamics. Como alternativa, se você estiver usando uma ferramenta de terceiros, como ElasticSearch, poderá exportar os logs para o armazenamento de BLOBs e usar o [plug-in do Logstash do Azure](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob) para consumir os dados em sua pilha ElasticSearch, Kibana e Logstash (Elk).

### <a name="turn-on-debug-level-logging-in-hdinsight"></a>Ativar o log em nível de depuração no HDInsight

Se Data Lake Storage Gen1 envio de logs não estiver ativado, o Azure HDInsight também fornecerá uma maneira de ativar o [registro em log do lado do cliente para data Lake Storage Gen1](data-lake-store-performance-tuning-mapreduce.md) via Log4J. Você deve definir a seguinte propriedade em **Ambari** > **YARN** > **config** > **Configurações avançadas de yarn-Log4J**:

    log4j.logger.com.microsoft.azure.datalake.store=DEBUG

Depois que a propriedade é definida e os nós são reiniciados, Data Lake Storage Gen1 diagnóstico é gravado nos logs do YARN nos nós (/tmp/\<usuário\>/yarn.log) e detalhes importantes como erros ou limitação (código de erro HTTP 429) podem ser monitorados. Essas mesmas informações também podem ser monitoradas nos logs de Azure Monitor ou onde os logs são enviados para a folha [diagnóstico](data-lake-store-diagnostic-logs.md) da conta de data Lake Storage Gen1. É recomendável que pelo menos tenha o log do lado do cliente ativado ou utilize a opção de envio de logs com Data Lake Storage Gen1 para visibilidade operacional e depuração mais fácil.

### <a name="run-synthetic-transactions"></a>Executar transações sintéticas

Atualmente, a métrica de disponibilidade de serviço para Data Lake Storage Gen1 no portal do Azure tem uma janela de atualização de 7 minutos. Além disso, ele não pode ser consultado usando uma API exposta publicamente. Portanto, é recomendável criar um aplicativo básico que faça transações sintéticas para Data Lake Storage Gen1 que possa fornecer até a disponibilidade de minutos. Um exemplo pode estar criando um WebJob, um aplicativo lógico ou Aplicativo de funções do Azure para executar uma leitura, criação e atualização em Data Lake Storage Gen1 e enviar os resultados para sua solução de monitoramento. As operações podem ser feitas em uma pasta temporária e, em seguida, excluídas após o teste, que podem ser executadas a cada 30-60 segundos, dependendo dos requisitos.

## <a name="directory-layout-considerations"></a>Considerações sobre layout de diretório

Quando dados de aterrissagem estão em um data Lake, é importante planejar previamente a estrutura dos dados para que a segurança, o particionamento e o processamento possam ser utilizados com eficiência. Muitas das recomendações a seguir podem ser usadas, seja com Azure Data Lake Storage Gen1, armazenamento de BLOBs ou HDFS. Cada carga de trabalho tem requisitos diferentes de como os dados são consumidos, mas abaixo estão alguns layouts comuns a serem considerados ao trabalhar com cenários de lote e de IoT.

### <a name="iot-structure"></a>Estrutura de IoT

Em cargas de trabalho de IoT, pode haver um grande volume de dados sendo descarregou no armazenamento de dados que abrange vários produtos, dispositivos, organizações e clientes. É importante planejar previamente o layout do diretório para organização, segurança e processamento eficiente dos dados para consumidores de fluxo contínuo. Um modelo geral a ser considerado pode ser o seguinte layout:

    {Region}/{SubjectMatter(s)}/{yyyy}/{mm}/{dd}/{hh}/

Por exemplo, a telemetria de aterrissagem para um motor de avião no Reino Unido pode se parecer com a seguinte estrutura:

    UK/Planes/BA1293/Engine1/2017/08/11/12/

Há um motivo importante para colocar a data no final da estrutura de pastas. Se você quiser bloquear determinadas regiões ou assuntos de assunto para usuários/grupos, poderá facilmente fazer isso com as permissões de POSIX. Caso contrário, se houvesse a necessidade de restringir um determinado grupo de segurança para exibir apenas os dados do Reino Unido ou determinados planos, com a estrutura de data na frente, uma permissão separada seria necessária para várias pastas em cada pasta de hora. Além disso, ter a estrutura de data na frente aumentaria exponencialmente o número de pastas à medida que o tempo passou.

### <a name="batch-jobs-structure"></a>Estrutura de trabalhos em lotes

De um alto nível, uma abordagem comumente usada no processamento em lotes é o terreno de dados em uma pasta "in". Em seguida, depois que os dados são processados, coloque os novos dados em uma pasta "out" para que os processos downstream sejam consumidos. Essa estrutura de diretório é vista às vezes para trabalhos que exigem processamento em arquivos individuais e podem não exigir processamento maciçomente paralelo em grandes conjuntos de dados. Como a estrutura de IoT recomendada acima, uma boa estrutura de diretório tem as pastas de nível pai para coisas como assuntos de região e assunto (por exemplo, organização, produto/produtor). Essa estrutura ajuda a proteger os dados em sua organização e o melhor gerenciamento dos dados em suas cargas de trabalho. Além disso, considere a data e a hora na estrutura para permitir melhor organização, pesquisas filtradas, segurança e automação no processamento. O nível de granularidade da estrutura de data é determinado pelo intervalo no qual os dados são carregados ou processados, como por hora, diariamente ou até mesmo mensalmente.

Às vezes, o processamento de arquivos não é bem-sucedido devido a dados corrompidos ou formatos inesperados. Nesses casos, a estrutura de diretório pode se beneficiar de uma pasta **/Bad** para mover os arquivos para para uma inspeção mais detalhada. O trabalho em lotes também pode manipular o relatório ou a notificação desses arquivos *inválidos* para intervenção manual. Considere a seguinte estrutura de modelo:

    {Region}/{SubjectMatter(s)}/In/{yyyy}/{mm}/{dd}/{hh}/
    {Region}/{SubjectMatter(s)}/Out/{yyyy}/{mm}/{dd}/{hh}/
    {Region}/{SubjectMatter(s)}/Bad/{yyyy}/{mm}/{dd}/{hh}/

Por exemplo, uma empresa de marketing recebe extratos de dados diários de atualizações do cliente de seus clientes no América do Norte. Ele pode parecer com o seguinte trecho antes e depois de ser processado:

    NA/Extracts/ACMEPaperCo/In/2017/08/14/updates_08142017.csv
    NA/Extracts/ACMEPaperCo/Out/2017/08/14/processed_updates_08142017.csv

No caso comum de dados do lote sendo processados diretamente em bancos de dados como o hive ou bancos de dado SQL tradicionais, não há necessidade de uma pasta **/in** ou **/out** , pois a saída já entra em uma pasta separada para a tabela Hive ou o banco de dados externo. Por exemplo, os extratos diários de clientes seriam inseridos em suas respectivas pastas e a orquestração por algo como Azure Data Factory, Apache Oozie ou Apache enflow dispararia um trabalho diário de Hive ou Spark para processar e gravar os dados em uma tabela Hive.

## <a name="next-steps"></a>Passos seguintes

* [Descrição geral do Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Controle de acesso no Azure Data Lake Storage Gen1](data-lake-store-access-control.md)
* [Segurança em Azure Data Lake Storage Gen1](data-lake-store-security-overview.md)
* [Ajustando Azure Data Lake Storage Gen1 para desempenho](data-lake-store-performance-tuning-guidance.md)
* [Diretrizes de ajuste de desempenho para usar o HDInsight Spark com o Azure Data Lake Storage Gen1](data-lake-store-performance-tuning-spark.md)
* [Diretrizes de ajuste de desempenho para usar o hive do HDInsight com Azure Data Lake Storage Gen1](data-lake-store-performance-tuning-hive.md)
* [Criar clusters HDInsight com Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
