---
title: Melhores práticas para a utilização de geração 2 de armazenamento do Azure Data Lake | Documentos da Microsoft
description: Conheça as práticas recomendadas sobre a ingestão de dados, segurança de data e desempenho relacionados ao uso do Azure Data Lake Storage Gen2 (anteriormente conhecido como o Azure Data Lake Store)
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: article
ms.date: 12/06/2018
ms.author: normesta
ms.reviewer: sachins
ms.openlocfilehash: 7cfe19614b2107161dcce9c80690333212162045
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67061323"
---
# <a name="best-practices-for-using-azure-data-lake-storage-gen2"></a>Melhores práticas para utilizar a geração 2 de armazenamento do Azure Data Lake

Neste artigo, ficará a conhecer as melhores práticas e considerações para trabalhar com a geração 2 de armazenamento do Azure Data Lake. Este artigo fornece informações de segurança, desempenho, resiliência e monitoramento de geração 2 de armazenamento do Data Lake. Antes de geração 2 de armazenamento do Data Lake, trabalhar com macrodados verdadeiramente em serviços, como o Azure HDInsight era complexo. Precisava dividir os dados em várias contas de armazenamento de BLOBs, de modo a que podem ser conseguidos petabytes de armazenamento e um desempenho ideal nessa escala. Com a geração 2 de armazenamento do Data Lake, a maioria dos limites fixos de tamanho e desempenho é removida. No entanto, ainda existem algumas considerações que este artigo aborda para que pode obter o melhor desempenho com geração 2 de armazenamento do Data Lake.

## <a name="security-considerations"></a>Considerações de segurança

Geração de armazenamento 2 do Azure Data Lake oferece controlos de acesso POSIX para utilizadores, grupos e principais de serviço do Azure Active Directory (Azure AD). Esses controles de acesso podem ser definidos como existentes ficheiros e diretórios. Os controlos de acesso também podem ser utilizados para criar as permissões predefinidas que podem ser aplicadas automaticamente para novos ficheiros ou diretórios. Obter mais detalhes sobre as ACLs de geração 2 do Data Lake armazenamento estão disponíveis em [controlo de acesso no Azure Data Lake Storage Gen2](storage-data-lake-storage-access-control.md).

### <a name="use-security-groups-versus-individual-users"></a>Utilizar grupos de segurança em comparação com utilizadores individuais

Ao trabalhar com macrodados na geração 2 de armazenamento do Data Lake, é provável que um principal de serviço é utilizado para permitir que os serviços, como o Azure HDInsight para trabalhar com os dados. No entanto, pode haver casos em que usuários individuais têm acesso aos dados também. Em todos os casos, considerar a utilização do Azure Active Directory [grupos de segurança](../common/storage-auth-aad.md) em vez de atribuir utilizadores individuais para diretórios e arquivos.

Depois de um grupo de segurança é atribuído permissões, adicionar ou remover os utilizadores do grupo não necessita de atualizações de geração 2 de armazenamento do Data Lake. Isto também ajuda a assegurar a que não exceder o número máximo de entradas de controlo de acesso por lista de controlo de acesso (ACL). Atualmente, o número é 32, (incluindo as ACLs de estilo POSIX quatro que sempre estão associadas a cada ficheiro e de diretório): o utilizador proprietário, o grupo proprietário, a máscara e outras. Cada diretório pode ter dois tipos de ACL, o ACL de acesso e a predefinição ACL, num total de 64 entradas de controlo de acesso. Para obter mais informações sobre estas ACLs, consulte [controlo de acesso no Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="security-for-groups"></a>Segurança para grupos

Quando ou os seus utilizadores precisam de acesso a dados numa conta de armazenamento com espaço de nomes hierárquico ativado, é melhor usar grupos de segurança do Azure Active Directory. Alguns recomendado de grupos para começar seria **ReadOnlyUsers**, **WriteAccessUsers**, e **FullAccessUsers** para a raiz do sistema de arquivos e até mesmo separar aqueles para chave subdiretórios. Se houver alguma outra previstos grupos de utilizadores que podem ser adicionados mais tarde, mas que não tenham sido identificados, porém, podem considerar a criação de grupos de segurança fictício que têm acesso a determinadas pastas. Utilizar o grupo de segurança garante que pode evitar o tempo de processamento muito tempo ao atribuir novas permissões para milhares de arquivos.

### <a name="security-for-service-principals"></a>Segurança para principais de serviço

Principais de serviço do Azure Active Directory são normalmente utilizados por serviços, como o Azure Databricks para aceder a dados na geração 2 de armazenamento do Data Lake. Para muitos clientes, um principal de serviço do Azure Active Directory único pode ser adequado e pode ter todas as permissões na raiz do sistema de arquivos de geração 2 de armazenamento do Data Lake. Outros clientes poderão precisar de vários clusters com principais de serviço diferentes em que um cluster tem acesso total aos dados e o outro cluster com acesso só de leitura. 

### <a name="enable-the-data-lake-storage-gen2-firewall-with-azure-service-access"></a>Ativar a firewall de geração 2 de armazenamento do Data Lake com acesso de serviço do Azure

Geração 2 de armazenamento do Data Lake suporta a opção de ativar um firewall e limitar o acesso apenas aos serviços do Azure, o que é recomendado para limitar o vetor de ataques externos. Firewall pode ser ativada numa conta de armazenamento no portal do Azure através da **Firewall** > **ativar Firewall (ON)**  > **permitir o acesso aos serviços do Azure** opções.

Adicionar um clusters do Azure Databricks para uma rede virtual que poderá ter permissão para aceder através da Firewall de armazenamento requer a utilização de uma funcionalidade de pré-visualização do Databricks. Para ativar esta funcionalidade, coloque um pedido de suporte.

## <a name="resiliency-considerations"></a>Considerações de resiliência

Ao desenvolver a arquitetura de um sistema com geração 2 de armazenamento do Data Lake ou qualquer serviço de nuvem, deve considerar os requisitos de disponibilidade e de como responder a potenciais interrupções no serviço. Um problema poderia ser localizado para a instância específica ou até mesmo toda a região, por isso, ter um plano para ambos é importante. Consoante o objetivo de tempo de recuperação e a recuperação do ponto de objetivo SLAs para a sua carga de trabalho, pode escolher uma estratégia mais ou menos agressiva para elevada disponibilidade e recuperação após desastre.

### <a name="high-availability-and-disaster-recovery"></a>Elevada disponibilidade e recuperação após desastre

Elevada disponibilidade (HA) e a recuperação após desastre (DR) podem às vezes, ser combinados, embora cada um tem uma estratégia um pouco diferente, especialmente quando se trata de dados. Geração 2 de armazenamento do Data Lake já suporta a replicação de x 3, nos bastidores para proteger contra falhas de hardware localizadas. Além disso, outras opções de replicação, como ZRS melhorar HA ao GRS e RA-GRS melhorar o DR. Ao criar um plano para HA, em caso de uma interrupção do serviço a carga de trabalho precisa de aceder aos dados mais recentes mais rapidamente possível pela mudança para uma instância separadamente replicada localmente ou numa nova região.

Uma estratégia de DR, para se preparar para improvável eventualidade de uma falha catastrófica de uma região, também é importante ter dados replicados noutra região a utilizar os replicação GRS ou RA-GRS. Também deve considerar os requisitos de casos extremos, como Corrupção de dados em que pode querer criar instantâneos periódicos para reverter para. Dependendo da importância e o tamanho dos dados, considere implementar instantâneos de delta de 1 - 6- e períodos de 24 horas, de acordo com as tolerâncias de risco.

Para resiliência de dados com a geração 2 de armazenamento do Data Lake, recomenda-se de georreplicá-seus dados através de GRS ou RA-GRS que satisfaça os requisitos de HA/DR. Além disso, deve considerar maneiras para o aplicativo usando a geração 2 de armazenamento do Data Lake para automaticamente a ativação pós-falha para a região secundária através de acionadores de monitorização ou comprimento de tentativas falhadas ou, pelo menos, enviar uma notificação para os administradores intervenção manual. Tenha em atenção que não há compromisso de ativação pós-falha em relação à espera de um serviço esteja novamente online.

### <a name="use-distcp-for-data-movement-between-two-locations"></a>Utilizar o Distcp para movimento de dados entre duas localizações

Abreviação de cópia distribuída, DistCp é uma ferramenta de linha de comandos do Linux que vem com o Hadoop e fornece o movimento de dados distribuídos entre duas localizações. Os dois locais podem ser de geração 2 de armazenamento do Data Lake, o HDFS ou o S3. Essa ferramenta utiliza tarefas de MapReduce num cluster do Hadoop (por exemplo, o HDInsight) para aumentar horizontalmente em todos os nós. Distcp é considerado a forma mais rápida para mover grandes quantidades de dados sem aplicações de compressão de rede especiais. Distcp também fornece uma opção para atualizar apenas deltas entre duas localizações, tentativas automáticas de identificadores, bem como dimensionamento dinâmico de computação. Essa abordagem é incrivelmente eficiente quando trata coisas como tabelas de ramo de registo/Spark que podem ter muitos ficheiros grandes num único diretório a replicar e que apenas pretende copiar os dados modificados. Por esses motivos, Distcp é a ferramenta mais recomendada para copiar dados entre arquivos de macrodados.

Tarefas de cópia podem ser acionadas por fluxos de trabalho Apache Oozie com frequência ou gatilhos de dados, bem como tarefas de cron do Linux. Para tarefas de replicação intensivos, é recomendado para acelerar um cluster de HDInsight Hadoop separado que pode ser ajustado e dimensionado especificamente para as tarefas de cópia. Isto garante que as tarefas de cópia não interferem com tarefas críticas. Se a executar os replicação numa frequência grande o suficiente, o cluster pode, até mesmo, desativado entre cada tarefa. Se efetuar a ativação pós-falha para a região secundária, certifique-se de que outro cluster também é rotacionado na região secundária para replicar dados de novo para a conta de geração 2 de armazenamento do Data Lake principal assim que ele é reativado. Para obter exemplos de utilizar o Distcp, consulte [utilizar Distcp para copiar dados entre os Blobs de armazenamento do Azure e de geração 2 de armazenamento do Data Lake](../blobs/data-lake-storage-use-distcp.md).

### <a name="use-azure-data-factory-to-schedule-copy-jobs"></a>Utilize o Azure Data Factory para agendar tarefas de cópia

O Azure Data Factory também pode ser utilizado para agendar tarefas de cópia com uma atividade de cópia e até mesmo pode ser configurado numa frequência através do Assistente de cópia. Tenha em atenção que o Azure Data Factory tem um limite de unidades de movimento de dados na cloud (DMUs) e, eventualmente caps a computação/débito para cargas de trabalho de dados grandes. Além disso, atualmente do Azure Data Factory não oferece atualizações de delta entre contas de geração 2 de armazenamento do Data Lake, portanto, diretórios, como tabelas do Hive exigiria uma cópia completa para replicar. Consulte a [artigo de fábrica de dados](../../data-factory/load-azure-data-lake-storage-gen2.md) para obter mais informações sobre a cópia com o Data Factory.

## <a name="monitoring-considerations"></a>Considerações sobre a monitorização

Geração 2 de armazenamento do Data Lake fornece métricas no portal do Azure sob a conta de geração 2 de armazenamento do Data Lake e no Azure Monitor. Disponibilidade de geração 2 de armazenamento do Data Lake é apresentada no portal do Azure. Para obter a disponibilidade mais recente de uma conta de geração 2 de armazenamento do Data Lake, tem de executar seus próprios testes sintéticos para validar a disponibilidade. Outras métricas, como a utilização de armazenamento total, leitura/escrita pedidos e de entrada/saída estão disponíveis para ser alavancada com monitorização de aplicações e também podem acionar alertas quando os limiares (por exemplo, a latência média ou n. º de erros por minuto) for excedidos.

## <a name="directory-layout-considerations"></a>Considerações de esquema de diretório

Ao colocar dados num data lake, é importante planear previamente a estrutura dos dados para que a segurança, criação de partições e o processamento podem ser utilizados com eficiência. Muitas das recomendações seguintes são aplicáveis para todas as cargas de trabalho de grandes volumes de dados. Cada carga de trabalho tem requisitos diferentes na forma como os dados são consumidos, mas abaixo estão alguns layouts comuns a serem consideradas ao trabalhar com o IoT e cenários do batch.

### <a name="iot-structure"></a>Estrutura de IoT

Em cargas de trabalho de IoT, pode haver uma grande quantidade de dados que está a ser foi colocadas no arquivo de dados que abrange vários produtos, dispositivos, organizações e clientes. É importante planear previamente o esquema de diretório para a organização, segurança e processamento eficiente dos dados para os consumidores de fluxo de baixo. Um modelo geral a serem considerados pode ser o seguinte layout:

    {Region}/{SubjectMatter(s)}/{yyyy}/{mm}/{dd}/{hh}/

Por exemplo, a telemetria de um motor de avião no Reino Unido de destino pode ser, como a seguinte estrutura:

    UK/Planes/BA1293/Engine1/2017/08/11/12/

Há um motivo importante para colocar a data no final da estrutura de diretório. Se quiser bloquear determinadas regiões ou assuntos de assunto para os utilizadores/grupos, em seguida, pode facilmente fazê com as permissões de acesso POSIX. Caso contrário, se houvesse uma necessidade para restringir um determinado grupo de segurança para exibir apenas os dados do Reino Unido ou determinados planos, com a estrutura de data na frente uma permissão separada seria necessária para vários diretórios em cada diretório de hora. Além disso, ter a estrutura de data na frente seria exponencialmente aumentar o número de diretórios como tempo aconteceu o trabalho.

### <a name="batch-jobs-structure"></a>Estrutura de tarefas do batch

De forma geral, uma abordagem mais usada no processamento de lotes é direcionado para dados num diretório "em". Em seguida, depois dos dados são processados, coloca os novos dados num diretório "de saída" a jusante processos a consumir. Esta estrutura de diretório é vista, às vezes, para as tarefas que precisam de processamento em ficheiros individuais e poderão não requerer o processamento paralelo em massa ao longo de grandes conjuntos de dados. Como a estrutura de IoT recomendada acima, uma estrutura de diretório boa tem os diretórios de nível de principal para tarefas como a região e assuntos de assunto (por exemplo, organização, produto/produtor). Esta estrutura ajuda a proteger os dados em sua organização e um melhor gerenciamento de dados em suas cargas de trabalho. Além disso, considere a data e hora na estrutura para permitir uma melhor organização, pesquisas filtradas, segurança e Automação no processamento. O nível de granularidade para a estrutura de data é determinado pelo intervalo no qual os dados são carregados ou processados, como a hora a hora, diária, ou até mesmo mensal.

Por vezes, de ficheiros processamento for concluído com êxito devido a danos em dados ou formatos inesperados. Nesses casos, a estrutura do diretório poderá se beneficiar de um **/ruim** pasta para mover os ficheiros para obter mais inspeção. A tarefa de lote também poderá lidar com a geração de relatórios ou a notificação esses *ruim* ficheiros para uma intervenção manual. Considere a seguinte estrutura de modelo:

    {Region}/{SubjectMatter(s)}/In/{yyyy}/{mm}/{dd}/{hh}/
    {Region}/{SubjectMatter(s)}/Out/{yyyy}/{mm}/{dd}/{hh}/
    {Region}/{SubjectMatter(s)}/Bad/{yyyy}/{mm}/{dd}/{hh}/

Por exemplo, uma empresa de marketing recebe as extrações de dados diária de atualizações de cliente dos respetivos clientes na América do Norte. Ela poderia ser o seguinte fragmento antes e depois a ser processado:

    NA/Extracts/ACMEPaperCo/In/2017/08/14/updates_08142017.csv
    NA/Extracts/ACMEPaperCo/Out/2017/08/14/processed_updates_08142017.csv

No caso comum de dados de lote que está a ser processados diretamente em bases de dados como o Hive ou bases de dados SQL tradicionais, não uma necessidade de um **/in** ou **/fora** pasta, uma vez que o resultado já entra num separar a pasta para a tabela do Hive ou a base de dados externa. Por exemplo, seriam direcionado para extrações diárias de clientes em suas respectivas pastas e orquestração por algo como o Azure Data Factory, Apache Oozie, ou Apache ventilação dispararia uma tarefa diária de Hive ou Spark para processar e escrever os dados para uma tabela do Hive.
