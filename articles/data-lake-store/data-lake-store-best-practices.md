---
title: Melhores práticas para a utilização de geração 1 de armazenamento do Azure Data Lake | Documentos da Microsoft
description: Conheça as práticas recomendadas sobre a ingestão de dados, segurança de data e desempenho relacionados ao uso do Azure Data Lake Storage Gen1 (anteriormente conhecido como o Azure Data Lake Store)
services: data-lake-store
documentationcenter: ''
author: sachinsbigdata
manager: jhubbard
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 06/27/2018
ms.author: sachins
ms.openlocfilehash: 53af7ff840f9d04f0e09010b72e9eefc32a8eadd
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56961895"
---
# <a name="best-practices-for-using-azure-data-lake-storage-gen1"></a>Melhores práticas para utilizar a geração 1 de armazenamento do Azure Data Lake

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Neste artigo, ficará a conhecer as melhores práticas e considerações para trabalhar com a geração 1 de armazenamento do Azure Data Lake. Este artigo fornece informações de segurança, desempenho, resiliência e monitoramento de geração 1 de armazenamento do Data Lake. Antes de geração 1 de armazenamento do Data Lake, trabalhar com macrodados verdadeiramente em serviços, como o Azure HDInsight era complexo. Precisava dividir os dados em várias contas de armazenamento de BLOBs, de modo a que podem ser conseguidos petabytes de armazenamento e um desempenho ideal nessa escala. Com a geração 1 de armazenamento do Data Lake, a maioria dos limites fixos de tamanho e desempenho é removida. No entanto, ainda existem algumas considerações que este artigo aborda para que pode obter o melhor desempenho com geração 1 de armazenamento do Data Lake.

## <a name="security-considerations"></a>Considerações de segurança

Ofertas de geração 1 de armazenamento do Azure Data Lake POSIX controlos de acesso e detalhadas de auditoria para os utilizadores do Azure Active Directory (Azure AD), grupos e principais de serviço. Esses controles de acesso podem ser definidos para ficheiros e pastas existentes. Os controlos de acesso também podem ser utilizados para criar padrões que podem ser aplicados a novos ficheiros ou pastas. Quando as permissões estão definidas para pastas existentes e os objetos subordinados, as permissões têm de ser propagada recursivamente em cada objeto. Se houver um grande número de ficheiros, propagar as permissões pode demorar muito tempo. O tempo necessário pode variar entre objetos de 30 a 50 processados por segundo. Por conseguinte, adequadamente a planear os grupos de utilizador e a estrutura de pasta. Caso contrário, pode causar problemas e atrasos inesperados quando trabalha com os seus dados.

Suponha que tem uma pasta com 100 000 objetos filho. Se analisar o limite inferior do 30 objetos processados por segundo atualizar a permissão para a pasta inteira pode demorar uma hora. Obter mais detalhes sobre as ACLs de geração 1 do Data Lake armazenamento estão disponíveis em [controlo de acesso no Azure Data Lake Storage Gen1](data-lake-store-access-control.md). Para um melhor desempenho em atribuir recursivamente ACLs, pode usar a ferramenta de linha de comandos do Azure Data Lake. A ferramenta cria vários threads e lógica de navegação de recursiva aplicarem rapidamente as ACLs para milhões de ficheiros. A ferramenta está disponível para Linux e Windows e o [documentação](https://github.com/Azure/data-lake-adlstool) e [downloads](https://aka.ms/adlstool-download) para esta ferramenta pode ser encontrada no GitHub. Esses aprimoramentos de desempenho mesmo podem ser ativados por suas próprias ferramentas escritas com a geração de 1 de armazenamento do Data Lake [.NET](data-lake-store-data-operations-net-sdk.md) e [Java](data-lake-store-get-started-java-sdk.md) SDKs.

### <a name="use-security-groups-versus-individual-users"></a>Utilizar grupos de segurança em comparação com utilizadores individuais

Ao trabalhar com macrodados na geração 1 de armazenamento do Data Lake, é muito provável que um principal de serviço é utilizado para permitir que os serviços, como o Azure HDInsight para trabalhar com os dados. No entanto, pode haver casos em que usuários individuais têm acesso aos dados também. Nesses casos, tem de utilizar o Azure Active Directory [grupos de segurança](data-lake-store-secure-data.md#create-security-groups-in-azure-active-directory) em vez de atribuir utilizadores individuais em pastas e ficheiros.

Depois de um grupo de segurança é atribuído permissões, adicionar ou remover os utilizadores do grupo não necessita de atualizações de geração 1 de armazenamento do Data Lake. Isto também ajuda a assegurar a não exceder o limite de [acesso de 32 e de ACLs predefinidas](../azure-subscription-service-limits.md#data-lake-store-limits) (Isto inclui as ACLs de estilo POSIX quatro que sempre estão associadas a todos os ficheiros e pastas: [o utilizador proprietário](data-lake-store-access-control.md#the-owning-user), [o grupo proprietário](data-lake-store-access-control.md#the-owning-group), [a máscara](data-lake-store-access-control.md#the-mask)e outros).

### <a name="security-for-groups"></a>Segurança para grupos

Como discutido, quando os utilizadores têm acesso ao Data Lake Storage Gen1, é melhor usar grupos de segurança do Azure Active Directory. Alguns recomendado de grupos para começar seria **ReadOnlyUsers**, **WriteAccessUsers**, e **FullAccessUsers** para a raiz da conta e até mesmo separar aqueles para a chave subpastas. Se houver alguma outra previstos grupos de utilizadores que podem ser adicionados mais tarde, mas que não tenham sido identificados, porém, podem considerar a criação de grupos de segurança fictício que têm acesso a determinadas pastas. Utilizar o grupo de segurança garante que mais tarde não é necessário um longo tempo de processamento para atribuição de novas permissões para milhares de arquivos.

### <a name="security-for-service-principals"></a>Segurança para principais de serviço

Principais de serviço do Azure Active Directory são normalmente utilizados por serviços, como o Azure HDInsight para aceder a dados na geração 1 de armazenamento do Data Lake. Consoante os requisitos de acesso em várias cargas de trabalho, poderão existir algumas considerações para garantir a segurança dentro e fora da organização. Para muitos clientes, um principal de serviço do Azure Active Directory único pode ser adequado e pode ter todas as permissões na raiz da conta do Data Lake Storage Gen1. Outros clientes poderão precisar de vários clusters com principais de serviço diferentes em que um cluster tem acesso total aos dados e o outro cluster com acesso só de leitura. Tal como acontece com os grupos de segurança, pode optar por tornar um principal de serviço para cada previsto cenário (leitura, escrita, total) quando é criada uma conta de geração 1 de armazenamento do Data Lake.

### <a name="enable-the-data-lake-storage-gen1-firewall-with-azure-service-access"></a>Ativar a firewall de geração 1 de armazenamento do Data Lake com acesso de serviço do Azure

Geração 1 de armazenamento do Data Lake suporta a opção de ativar um firewall e limitar o acesso apenas aos serviços do Azure, o que é recomendado para um vetor de ataque menor de intrusões externos. Firewall pode ser ativada na conta de geração 1 do Data Lake Storage no portal do Azure através da **Firewall** > **ativar Firewall (ON)** > **permitir o acesso ao Azure serviços** opções.

![Definições de geração 1 de armazenamento do Data Lake da firewall](./media/data-lake-store-best-practices/data-lake-store-firewall-setting.png "definições na geração 1 de armazenamento do Data Lake da Firewall")

Assim que a firewall está ativados, apenas o Azure serviços como o HDInsight, o Data Factory, o SQL Data Warehouse, etc. têm acesso a geração 1 de armazenamento do Data Lake. Devido a tradução de endereços de rede interna utilizada pelo Azure, o firewall de geração 1 de armazenamento do Data Lake não suporta a restrição de serviços específicos por IP e destina-se apenas para restrições de pontos de extremidade fora do Azure, como no local.

## <a name="performance-and-scale-considerations"></a>Considerações sobre desempenho e dimensionamento

Um dos recursos mais avançados de geração 1 de armazenamento do Data Lake é que isso elimina os limites fixos no débito de dados. Remover os limites permite aos clientes aumentar seu tamanho de dados e acompanhado requisitos de desempenho sem a necessidade de dividir os dados. Um dos mais importante considerações sobre a otimização de desempenho de geração 1 de armazenamento do Data Lake é que ele executa o melhor quando devido a paralelismo.

### <a name="improve-throughput-with-parallelism"></a>Melhorar o débito com paralelismo

Pense em 12 de 8 threads por núcleo para o débito de leitura/escrita ideal. Isso se deve bloquear leituras/escritas num único thread e, mais threads podem permitir que uma simultaneidade mais elevada na VM. Para garantir que os níveis estão em bom Estados e paralelismo pode ser aumentado, certifique-se de que monitorizar a utilização de CPU da VM.

### <a name="avoid-small-file-sizes"></a>Evitar a tamanhos de ficheiro pequeno

Permissões de POSIX e auditoria na geração 1 de armazenamento do Data Lake é fornecido com uma sobrecarga que se torna aparente quando trabalhar com vários arquivos pequenos. Como melhor prática, tem de lote seus dados em arquivos maiores em relação à gravação milhares ou milhões de ficheiros pequenos para geração 1 de armazenamento do Data Lake. Evitar tamanhos de ficheiro pequeno pode ter vários benefícios, tais como:

* Reduzir as verificações de autenticação em vários arquivos
* Ligações de abrir o ficheiro de reduzido
* Copiar mais rápida/replicação.
* Ficheiros menos a processar ao atualizar as permissões do Data Lake Storage Gen1 POSIX

Dependendo de quais serviços e cargas de trabalho estiver a utilizar os dados, um bom tamanho a ter em consideração para os ficheiros é de 256 MB ou superior. Se os tamanhos de ficheiro não podem ser loteados quando o destino numa geração 1 de armazenamento do Data Lake, pode ter uma tarefa de compactação separado que combina esses arquivos maiores. Para obter mais informações e recomendações sobre tamanhos de ficheiro e organizar os dados na geração 1 de armazenamento do Data Lake, veja [estruturar seu conjunto de dados](data-lake-store-performance-tuning-guidance.md#structure-your-data-set).

### <a name="large-file-sizes-and-potential-performance-impact"></a>Tamanhos de ficheiros grandes e o possível impacto de desempenho

Apesar de geração 1 de armazenamento do Data Lake suporta ficheiros grandes até petabytes de tamanho, para otimizar o desempenho e, dependendo do processo de leitura de dados, pode não ser ideal para passar em média de 2 GB. Por exemplo, ao usar **Distcp** para copiar dados entre localizações ou contas de armazenamento diferentes, os arquivos são o melhores nível de granularidade utilizado para determinar as tarefas de mapa. Então, se estiver a copiar 10 ficheiros que estão a 1 TB, no máximo 10 mapeadores são alocados. Além disso, se tiver muitos ficheiros com mapeadores atribuídos, inicialmente os mapeadores trabalham em paralelo para mover ficheiros grandes. No entanto, como a tarefa é iniciada terminar apenas alguns mapeadores permanecem alocadas e pode estar preso a um mapeador único atribuído a um arquivo grande. Microsoft submeteu melhorias para Distcp para resolver este problema nas futuras versões do Hadoop.

Outro exemplo, a considerar é ao utilizar o Azure Data Lake Analytics com a geração 1 de armazenamento do Data Lake. Dependendo do processamento feito com o extrator, alguns ficheiros que não podem ser divididos (por exemplo, XML, JSON) poderiam ser prejudicado no desempenho quando mais de 2 GB. Em casos em que os ficheiros podem ser divididos por um extrator (por exemplo, CSV), ficheiros grandes são preferenciais.

### <a name="capacity-plan-for-your-workload"></a>Planear a capacidade de sua carga de trabalho

Geração de armazenamento 1 do Azure Data Lake remove a e/s de disco rígida limitação de limites que são colocadas em contas de armazenamento de Blobs. No entanto, existem limites ainda flexíveis que precisam ser consideradas. Os limites de limitação de entrada/saída predefinidos satisfazer as necessidades da maioria dos cenários. Se a sua carga de trabalho tem de ter de aumentar os limites, trabalhe com o suporte da Microsoft. Além disso, observe os limites durante a fase de prova de conceito, de modo a que os limites de limitação de e/s não será ativado durante a produção. Se isto acontecer, exijam a espera para um aumento manual da equipa de engenharia da Microsoft. Se ocorrer a limitação de e/s, Gen1 de armazenamento do Azure Data Lake devolve um código de erro de 429 e o ideal é que deve ser repetida com uma política de término exponencial apropriado.

### <a name="optimize-writes-with-the-data-lake-storage-gen1-driver-buffer"></a>Otimizar o "escreve" com o buffer de driver de geração 1 de armazenamento do Data Lake

Para otimizar o desempenho e reduzir IOPS de escrita em Gen1 de armazenamento do Data Lake do Hadoop, execute operações de escrita mais próximo o tamanho de memória intermédia de driver de geração 1 de armazenamento do Data Lake possível. Tente não exceder o tamanho do buffer antes liberá-lo, por exemplo, quando a transmissão em fluxo com o Apache Storm ou Spark cargas de trabalho de transmissão em fluxo. Ao escrever para a geração 1 de armazenamento do Data Lake do HDInsight/Hadoop, é importante saber que a geração 1 de armazenamento do Data Lake tem um driver com uma memória intermédia de 4 MB. Como muitos controladores de sistema de ficheiros, esse buffer possam ser esvaziado manualmente antes de atingir o tamanho de 4 MB. Caso contrário, imediatamente liberada para o armazenamento se a próxima gravação excede o tamanho do buffer máximo. Sempre que possível, deve evitar uma saturação ou um underrun significativa da memória intermédia quando a sincronização/liberação da política de por janela contagem ou tempo.

## <a name="resiliency-considerations"></a>Considerações de resiliência

Ao desenvolver a arquitetura de um sistema com geração 1 de armazenamento do Data Lake ou qualquer serviço de nuvem, deve considerar os requisitos de disponibilidade e de como responder a potenciais interrupções no serviço. Um problema poderia ser localizado para a instância específica ou até mesmo toda a região, por isso, ter um plano para ambos é importante. Consoante a **objetivo de tempo de recuperação** e o **objetivo de ponto de recuperação** SLAs para sua carga de trabalho, pode escolher uma estratégia mais ou menos agressiva para elevada disponibilidade e recuperação após desastre.

### <a name="high-availability-and-disaster-recovery"></a>Elevada disponibilidade e recuperação após desastre

Elevada disponibilidade (HA) e a recuperação após desastre (DR) podem às vezes, ser combinados, embora cada um tem uma estratégia um pouco diferente, especialmente quando se trata de dados. Geração 1 de armazenamento do Data Lake já suporta a replicação de x 3, nos bastidores para proteger contra falhas de hardware localizadas. No entanto, uma vez que a replicação entre regiões não é integrada, tem de geri-la manualmente. Ao criar um plano para HA, em caso de uma interrupção do serviço a carga de trabalho precisa de aceder aos dados mais recentes mais rapidamente possível pela mudança para uma instância separadamente replicada localmente ou numa nova região.

Uma estratégia de DR, para se preparar para improvável eventualidade de uma falha catastrófica de uma região, também é importante ter dados replicados para uma região diferente. Estes dados inicialmente podem ser o mesmo que os dados replicados de HA. No entanto, também deve considerar os requisitos de casos extremos, como Corrupção de dados em que pode querer criar instantâneos periódicos para reverter para. Dependendo da importância e o tamanho dos dados, considere sem interrupção de instantâneos de delta de 1 - 6- e períodos de 24 horas no armazenamento local e/ou secundário, de acordo com as tolerâncias de risco.

Para resiliência de dados com o Data Lake Storage Gen1, recomenda-se de georreplicá-seus dados para uma região separada com uma frequência que satisfaça os requisitos de HA/DR, o ideal é que cada hora. Esta frequência de replicação minimiza os movimentos de grandes quantidades de dados que podem ter débito concorrente precisa com o sistema principal e um melhor objetivo de ponto de recuperação (RPO). Além disso, deve considerar maneiras para o aplicativo usando a geração 1 de armazenamento do Data Lake para automaticamente a ativação pós-falha para a conta secundária através de acionadores de monitorização ou comprimento de tentativas falhadas ou, pelo menos, enviar uma notificação para os administradores intervenção manual. Tenha em atenção que não há compromisso de ativação pós-falha em relação à espera de um serviço esteja novamente online. Se os dados não foi concluída a replicar, uma ativação pós-falha pode causar potencial perda de dados, a inconsistência ou a mesclagem complexa dos dados.

Seguem-se as principais três opções recomendadas para a orquestração da replicação entre contas de geração 1 de armazenamento do Data Lake e principais diferenças entre cada um deles.

|  |Distcp  |Azure Data Factory  |AdlCopy  |
|---------|---------|---------|---------|
|**Limites de dimensionamento**     | Vinculado de acordo com nós de trabalho        | Limitado pelas unidades de movimento de dados de Cloud máx.        | Vinculado por unidades de análise        |
|**Suporta deltas a copiar**     |   Sim      | Não         | Não         |
|**Orquestração interna**     |  Não (utilizar o Oozie ventilação ou cron tarefas)       | Sim        | Não (utilizar a automatização do Azure ou o agendador de tarefas do Windows)         |
|**Sistemas de ficheiros suportados**     | ADL, HDFS, WASB, S3, GS, CFS        |Inúmeros, consulte [conectores](../data-factory/connector-azure-blob-storage.md).         | ADL para ADL, WASB para ADL (mesma região apenas)        |
|**Suporte do sistema operacional**     |Qualquer sistema operacional com o Hadoop         | N/A          | Windows 10         |

### <a name="use-distcp-for-data-movement-between-two-locations"></a>Utilizar o Distcp para movimento de dados entre duas localizações

Abreviação de cópia distribuída, Distcp é uma ferramenta de linha de comandos do Linux que vem com o Hadoop e fornece o movimento de dados distribuídos entre duas localizações. Os dois locais podem ser Gen1 de armazenamento do Data Lake, HDFS, WASB ou S3. Essa ferramenta utiliza tarefas de MapReduce num cluster do Hadoop (por exemplo, o HDInsight) para aumentar horizontalmente em todos os nós. Distcp é considerado a forma mais rápida para mover grandes quantidades de dados sem aplicações de compressão de rede especiais. Distcp também fornece uma opção para atualizar apenas deltas entre duas localizações, tentativas automáticas de identificadores, bem como dimensionamento dinâmico de computação. Essa abordagem é incrivelmente eficiente quando trata coisas como tabelas de ramo de registo/Spark que podem ter muitos ficheiros grandes num único diretório a replicar e que apenas pretende copiar os dados modificados. Por esses motivos, Distcp é a ferramenta mais recomendada para copiar dados entre arquivos de macrodados.

Tarefas de cópia podem ser acionadas por fluxos de trabalho Apache Oozie com frequência ou gatilhos de dados, bem como tarefas de cron do Linux. Para tarefas de replicação intensivos, é recomendado para acelerar um cluster de HDInsight Hadoop separado que pode ser ajustado e dimensionado especificamente para as tarefas de cópia. Isto garante que as tarefas de cópia não interferem com tarefas críticas. Se a executar os replicação numa frequência grande o suficiente, o cluster pode, até mesmo, desativado entre cada tarefa. Se efetuar a ativação pós-falha para a região secundária, certifique-se de que outro cluster também é rotacionado na região secundária para replicar novos dados de volta para a conta de geração 1 do Data Lake armazenamento primária depois que ele é reativado. Para obter exemplos de utilizar o Distcp, consulte [utilizar Distcp para copiar dados entre os Blobs de armazenamento do Azure e de geração 1 de armazenamento do Data Lake](data-lake-store-copy-data-wasb-distcp.md).

### <a name="use-azure-data-factory-to-schedule-copy-jobs"></a>Utilize o Azure Data Factory para agendar tarefas de cópia

O Azure Data Factory também pode ser utilizado para agendar tarefas de cópia com um **atividade de cópia**e até mesmo pode ser configurado numa frequência por meio da **Assistente de cópia**. Tenha em atenção que o Azure Data Factory tem um limite de unidades de movimento de dados na cloud (DMUs) e, eventualmente caps a computação/débito para cargas de trabalho de dados grandes. Além disso, atualmente do Azure Data Factory não oferece atualizações de delta entre contas de geração 1 de armazenamento do Data Lake, portanto, requerem uma cópia completa para replicar pastas, como tabelas do Hive. Consulte a [guia de ajuste de atividade de cópia](../data-factory/copy-activity-performance.md) para obter mais informações sobre a cópia com o Data Factory.

### <a name="adlcopy"></a>AdlCopy

AdlCopy é uma ferramenta de linha de comandos do Windows que permite copiar dados entre duas contas de geração 1 de armazenamento do Data Lake apenas dentro da mesma região. A ferramenta de AdlCopy fornece uma opção autônoma ou a opção para utilizar uma conta do Azure Data Lake Analytics para executar a tarefa de cópia. Embora ele foi originalmente criado para cópias de sob demanda em vez de uma replicação robusta, ele fornece outra opção para fazer a cópia distribuída em várias contas de geração 1 do Data Lake Storage na mesma região. Para fiabilidade, é recomendado para utilizar a opção de Data Lake Analytics de premium para qualquer carga de trabalho de produção. A versão autónoma do pode devolver respostas ocupadas e limitou o dimensionamento e monitorização.

Como o Distcp, o AdlCopy tem de ser orquestradas por algo como o automatização do Azure ou o agendador de tarefas do Windows. Tal como acontece com o Data Factory, AdlCopy não suporta a cópia apenas ficheiros atualizados, mas voltará a copiar e substituir ficheiros existentes. Para obter mais informações e exemplos de como utilizar o AdlCopy, consulte [copiar dados dos Blobs de armazenamento do Azure para a geração 1 de armazenamento do Data Lake](data-lake-store-copy-data-azure-storage-blob.md).

## <a name="monitoring-considerations"></a>Considerações sobre a monitorização

Geração 1 de armazenamento do Data Lake fornece auditoria e registos de diagnóstico detalhados. Geração 1 de armazenamento do Data Lake fornece algumas métricas básicas no portal do Azure sob a conta de geração 1 de armazenamento do Data Lake e no Azure Monitor. Disponibilidade de geração 1 de armazenamento do Data Lake é apresentada no portal do Azure. No entanto, esta métrica é atualizada a cada sete minutos e não pode ser consultada através de uma API exposta publicamente. Para obter a disponibilidade mais recente de uma conta do Data Lake Storage Gen1, tem de executar seus próprios testes sintéticos para validar a disponibilidade. Outras métricas, como a utilização do armazenamento total, pedidos de leitura/escrita e entrada/saída podem demorar até 24 horas para atualizar. Por isso, mais métricas atualizadas devem ser calculadas manualmente por meio de ferramentas de linha de comandos do Hadoop ou informações de registo agregada. A maneira mais rápida de obter a utilização de armazenamento mais recente é executar este comando HDFS a partir de um nó de cluster do Hadoop (por exemplo, nó principal):

    hdfs dfs -du -s -h adl://<adlsg1_account_name>.azuredatalakestore.net:443/

### <a name="export-data-lake-storage-gen1-diagnostics"></a>Exportar diagnóstico de geração 1 de armazenamento do Data Lake

Uma das formas mais rápidas para aceder a registos pesquisáveis de geração 1 de armazenamento do Data Lake é permitir o envio de log **do Log Analytics** sob a **diagnóstico** painel para a conta de geração 1 de armazenamento do Data Lake. Isto fornece acesso imediato aos registos de entrada com o tempo e filtros de conteúdo, juntamente com as opções (e-mail/webhook) de alerta acionado em intervalos de 15 minutos. Para obter instruções, consulte [aceder a registos de diagnóstico para a geração 1 de armazenamento do Azure Data Lake](data-lake-store-diagnostic-logs.md).

Para alertas em tempo real mais e mais controle sobre onde direcionado para os registos, considere a exportar registos ao Azure EventHub onde conteúdo pode ser analisado individualmente ou através de uma janela de tempo para enviar notificações em tempo real a uma fila. Um aplicativo separado, como um [aplicação lógica](../connectors/connectors-create-api-azure-event-hubs.md) pode, em seguida, consumir e comunicar os alertas para o canal apropriado, bem como enviar métricas para ferramentas como NewRelic, Datadog ou AppDynamics de monitorização. Em alternativa, se estiver a utilizar uma ferramenta de terceiros como o ElasticSearch, pode exportar os registos para o armazenamento de BLOBs e utilizar o [Plug-in do Azure Logstash](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob) consumir os dados à sua pilha Elasticsearch, Kibana e o Logstash (ELK).

### <a name="turn-on-debug-level-logging-in-hdinsight"></a>Ativar o registo de nível de depuração no HDInsight

Se Data Lake armazenamento geração 1 não está ativado o envio de log, Azure HDInsight também fornece uma forma de ativar [lado do cliente de registo para a geração 1 de armazenamento do Data Lake](data-lake-store-performance-tuning-mapreduce.md) via log4j. Tem de definir a propriedade seguinte **Ambari** > **YARN** > **Config** > **Advanced log4j do yarn configurações**:

    log4j.logger.com.microsoft.azure.datalake.store=DEBUG

Depois da propriedade é definida e os nós são reiniciados, diagnóstico de geração 1 de armazenamento do Data Lake é escrito nos registos do YARN em nós (/tmp/\<utilizador\>/yarn.log) e importante detalhes como erros ou (código de erro HTTP 429) a limitação pode ser monitorizado. Estas informações também podem ser monitorizadas no Azure Monitor registos ou onde quer que os registos são fornecidos na [diagnóstico](data-lake-store-diagnostic-logs.md) painel da conta do Data Lake Storage Gen1. Recomenda-se para, pelo menos, tem de ativar o registo de cliente ou utilizar a opção com a geração 1 de armazenamento do Data Lake para visibilidade operacional e a depuração mais fácil de envio de registos.

### <a name="run-synthetic-transactions"></a>Executar transações sintéticas

A métrica de disponibilidade do serviço de geração 1 do Data Lake Storage no portal do Azure tem atualmente, a janela de atualização de 7 minutos. Além disso, não pode ser consultado com uma API exposta publicamente. Por conseguinte, é recomendado para criar uma aplicação básica que faça transações sintéticas para o Data Lake Storage Gen1 que pode fornecer até a disponibilidade de minuto. Um exemplo poderá ser criar um trabalho Web, aplicação lógica ou aplicação de funções do Azure para efetuar uma leitura, criar e atualizar em relação a geração 1 de armazenamento do Data Lake e enviar os resultados para sua solução de monitorização. As operações podem ser feitas numa pasta temporária e, em seguida, eliminadas após o teste, o que poderá ser executado a cada 30 a 60 segundos, dependendo dos requisitos.

## <a name="directory-layout-considerations"></a>Considerações de esquema de diretório

Ao colocar dados num data lake, é importante planear previamente a estrutura dos dados para que a segurança, criação de partições e o processamento podem ser utilizados com eficiência. Muitas das recomendações seguintes podem ser utilizadas, seja com Gen1 de armazenamento do Azure Data Lake, o armazenamento de BLOBs ou HDFS. Cada carga de trabalho tem requisitos diferentes na forma como os dados são consumidos, mas abaixo estão alguns layouts comuns a serem consideradas ao trabalhar com o IoT e cenários do batch.

### <a name="iot-structure"></a>Estrutura de IoT

Em cargas de trabalho de IoT, pode haver uma grande quantidade de dados que está a ser foi colocadas no arquivo de dados que abrange vários produtos, dispositivos, organizações e clientes. É importante planear previamente o esquema de diretório para a organização, segurança e processamento eficiente dos dados para os consumidores de fluxo de baixo. Um modelo geral a serem considerados pode ser o seguinte layout:

    {Region}/{SubjectMatter(s)}/{yyyy}/{mm}/{dd}/{hh}/

Por exemplo, a telemetria de um motor de avião no Reino Unido de destino pode ser, como a seguinte estrutura:

    UK/Planes/BA1293/Engine1/2017/08/11/12/

Há um motivo importante para colocar a data no final da estrutura de pastas. Se quiser bloquear determinadas regiões ou assuntos de assunto para os utilizadores/grupos, em seguida, pode facilmente fazê com as permissões de acesso POSIX. Caso contrário, se houvesse uma necessidade para restringir um determinado grupo de segurança para exibir apenas os dados do Reino Unido ou determinados planos, com a estrutura de data na frente uma permissão separada seria necessária para diversas pastas na pasta de cada hora. Além disso, ter a estrutura de data na frente seria exponencialmente aumentar o número de pastas como tempo aconteceu o trabalho.

### <a name="batch-jobs-structure"></a>Estrutura de tarefas do batch

De forma geral, uma abordagem mais usada no processamento de lotes é direcionado para dados numa pasta "em". Em seguida, depois dos dados são processados, coloca os dados de novo numa pasta "de saída" a jusante processos a consumir. Esta estrutura de diretório é vista, às vezes, para as tarefas que precisam de processamento em ficheiros individuais e poderão não requerer o processamento paralelo em massa ao longo de grandes conjuntos de dados. Como a estrutura de IoT recomendada acima, uma estrutura de diretório boa tem as pastas de nível de principal para tarefas como a região e assuntos de assunto (por exemplo, organização, produto/produtor). Esta estrutura ajuda a proteger os dados em sua organização e um melhor gerenciamento de dados em suas cargas de trabalho. Além disso, considere a data e hora na estrutura para permitir uma melhor organização, pesquisas filtradas, segurança e Automação no processamento. O nível de granularidade para a estrutura de data é determinado pelo intervalo no qual os dados são carregados ou processados, como a hora a hora, diária, ou até mesmo mensal.

Por vezes, de ficheiros processamento for concluído com êxito devido a danos em dados ou formatos inesperados. Nesses casos, a estrutura do diretório poderá se beneficiar de um **/ruim** pasta para mover os ficheiros para obter mais inspeção. A tarefa de lote também poderá lidar com a geração de relatórios ou a notificação esses *ruim* ficheiros para uma intervenção manual. Considere a seguinte estrutura de modelo:

    {Region}/{SubjectMatter(s)}/In/{yyyy}/{mm}/{dd}/{hh}/
    {Region}/{SubjectMatter(s)}/Out/{yyyy}/{mm}/{dd}/{hh}/
    {Region}/{SubjectMatter(s)}/Bad/{yyyy}/{mm}/{dd}/{hh}/

Por exemplo, uma empresa de marketing recebe as extrações de dados diária de atualizações de cliente dos respetivos clientes na América do Norte. Ela poderia ser o seguinte fragmento antes e depois a ser processado:

    NA/Extracts/ACMEPaperCo/In/2017/08/14/updates_08142017.csv
    NA/Extracts/ACMEPaperCo/Out/2017/08/14/processed_updates_08142017.csv

No caso comum de dados de lote que está a ser processados diretamente em bases de dados como o Hive ou bases de dados SQL tradicionais, não uma necessidade de um **/in** ou **/fora** pasta, uma vez que o resultado já entra num separar a pasta para a tabela do Hive ou a base de dados externa. Por exemplo, seriam direcionado para extrações diárias de clientes em suas respectivas pastas e orquestração por algo como o Azure Data Factory, Apache Oozie, ou Apache ventilação dispararia uma tarefa diária de Hive ou Spark para processar e escrever os dados para uma tabela do Hive.

## <a name="next-steps"></a>Passos Seguintes

* [Descrição geral do Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Controlo de acesso no Azure Data Lake Storage Gen1](data-lake-store-access-control.md)
* [Segurança no Azure Data Lake Storage Gen1](data-lake-store-security-overview.md)
* [Otimização de geração 1 de armazenamento do Azure Data Lake para o desempenho](data-lake-store-performance-tuning-guidance.md)
* [Guia para utilizar o HDInsight Spark com Gen1 de armazenamento do Azure Data Lake de sintonização de desempenho](data-lake-store-performance-tuning-spark.md)
* [Guia para utilizar o Hive do HDInsight com Gen1 de armazenamento do Azure Data Lake de sintonização de desempenho](data-lake-store-performance-tuning-hive.md)
* [Orquestração de dados com o Azure Data Factory para geração 1 de armazenamento do Azure Data Lake](https://mix.office.com/watch/1oa7le7t2u4ka)
* [Criar clusters do HDInsight com Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
