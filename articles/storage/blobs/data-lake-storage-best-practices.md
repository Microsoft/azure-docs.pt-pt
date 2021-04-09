---
title: Melhores práticas para a utilização do Azure Data Lake Storage Gen2 | Microsoft Docs
description: Conheça as melhores práticas sobre a ingestão de dados, segurança de datas e desempenho relacionado com a utilização do Azure Data Lake Storage Gen2 (anteriormente conhecido como Azure Data Lake Store)
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: normesta
ms.reviewer: sachins
ms.openlocfilehash: 937048ce14b9b05b55cd8d76e7a8c1fd67c63e4d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105933713"
---
# <a name="best-practices-for-using-azure-data-lake-storage-gen2"></a>Melhores práticas para a utilização do Azure Data Lake Storage Gen2

Neste artigo, você aprende sobre as melhores práticas e considerações para trabalhar com a Azure Data Lake Storage Gen2. Este artigo fornece informações em torno da segurança, desempenho, resiliência e monitorização para data lake storage gen2. Antes do Data Lake Storage Gen2, trabalhar com dados verdadeiramente grandes em serviços como o Azure HDInsight era complexo. Teve de obter dados em várias contas de armazenamento Blob para que o armazenamento de petabytes e o desempenho ideal nessa escala pudessem ser alcançados. Data Lake Storage Gen2 suporta tamanhos individuais de ficheiros até 190.7 TiB e a maioria dos limites rígidos para o desempenho foram removidos. No entanto, ainda existem algumas considerações que este artigo cobre para que você possa obter o melhor desempenho com Data Lake Storage Gen2.

## <a name="security-considerations"></a>Considerações de segurança

A Azure Data Lake Storage Gen2 oferece controlos de acesso POSIX para utilizadores, grupos e diretores de serviçoS Azure Ative(Azure AD). Estes controlos de acesso podem ser definidos para ficheiros e diretórios existentes. Os controlos de acesso também podem ser utilizados para criar permissões padrão que podem ser aplicadas automaticamente a novos ficheiros ou diretórios. Mais detalhes sobre data lake storage Gen2 ACLs estão disponíveis no [controle de acesso em Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md).

### <a name="use-security-groups-versus-individual-users"></a>Utilize grupos de segurança contra utilizadores individuais

Ao trabalhar com big data na Data Lake Storage Gen2, é provável que um diretor de serviço seja usado para permitir que serviços como o Azure HDInsight trabalhem com os dados. No entanto, pode haver casos em que os utilizadores individuais também precisem de ter acesso aos dados. Em todos os casos, considere fortemente a utilização de [grupos](../common/storage-auth-aad.md) de segurança do Azure Ative Directory em vez de atribuir utilizadores individuais a diretórios e ficheiros.

Uma vez que um grupo de segurança é atribuído permissões, adicionar ou remover utilizadores do grupo não requer quaisquer atualizações para data lake storage Gen2. Isto também ajuda a garantir que não excede o número máximo de entradas de controlo de acesso por lista de controlo de acesso (ACL). Atualmente, esse número é de 32, (incluindo os quatro ACLs de estilo POSIX que estão sempre associados a cada ficheiro e diretório): o utilizador próprio, o grupo de propriedade, a máscara, e outros. Cada diretório pode ter dois tipos de ACL, o acesso ACL e o ACL predefinido, para um total de 64 entradas de controlo de acesso. Para obter mais informações sobre estes ACLs, consulte [o controlo de acesso no Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="security-for-groups"></a>Segurança para grupos

Quando você ou os seus utilizadores precisam de acesso a dados numa conta de armazenamento com espaço hierárquico habilitado, o melhor é usar grupos de segurança do Azure Ative Directory. Alguns grupos recomendados para começar podem ser **ReadOnlyUsers**, **WriteAccessUsers** e **FullAccessUsers** para a raiz do recipiente, e até mesmo separados para subdiretas chave. Se houver outros grupos de utilizadores antecipados que possam ser adicionados mais tarde, mas que ainda não foram identificados, poderá considerar a criação de grupos de segurança falsos que tenham acesso a determinadas pastas. A utilização do grupo de segurança garante que pode evitar longos tempos de processamento ao atribuir novas permissões a milhares de ficheiros.

### <a name="security-for-service-principals"></a>Segurança para os diretores de serviços

Os principais do serviço Azure Ative Directory são normalmente utilizados por serviços como o Azure Databricks para aceder a dados na Data Lake Storage Gen2. Para muitos clientes, um único diretor de serviço Azure Ative Directory pode ser adequado, e pode ter permissões completas na raiz do contentor Data Lake Storage Gen2. Outros clientes podem necessitar de vários clusters com diferentes princípios de serviço onde um cluster tem acesso total aos dados, e outro cluster com apenas acesso de leitura. 

### <a name="enable-the-data-lake-storage-gen2-firewall-with-azure-service-access"></a>Ativar a firewall gen2 de armazenamento de data lake com acesso ao serviço Azure

Data Lake Storage Gen2 suporta a opção de ligar uma firewall e limitar o acesso apenas aos serviços Azure, que é recomendado para limitar o vetor de ataques externos. Firewall pode ser ativado numa conta de armazenamento no portal Azure através do **Firewall**  >  **Enable Firewall (ON)**  >  Permitir o acesso às opções de **serviços Azure.**

Para aceder à sua conta de armazenamento a partir de Azure Databricks, insaça a Azure Databricks na sua rede virtual e, em seguida, adicione essa rede virtual à sua firewall. Consulte as firewalls de [armazenamento Configure Azure e redes virtuais](../common/storage-network-security.md).

## <a name="resiliency-considerations"></a>Considerações de resiliência

Ao armazenar um sistema com Data Lake Storage Gen2 ou qualquer serviço na nuvem, deve considerar os seus requisitos de disponibilidade e como responder a possíveis interrupções no serviço. Uma questão poderia ser localizada em caso específico ou mesmo em toda a região, pelo que é importante ter um plano para ambos. Dependendo do objetivo do tempo de recuperação e do objetivo do ponto de recuperação SLAs para a sua carga de trabalho, poderá escolher uma estratégia mais ou menos agressiva para alta disponibilidade e recuperação de desastres.

### <a name="high-availability-and-disaster-recovery"></a>Elevada disponibilidade e recuperação após desastre

A elevada disponibilidade (HA) e a recuperação de desastres (DR) podem por vezes ser combinadas, embora cada uma tenha uma estratégia ligeiramente diferente, especialmente quando se trata de dados. Data Lake Storage Gen2 já lida com a replicação 3x sob o capot para proteger contra falhas de hardware localizadas. Adicionalmente, outras opções de replicação, tais como ZRS ou GZRS, melhoram ha, enquanto GRS & RA-GRS melhoram DR. Ao construir um plano para ha, em caso de interrupção de serviço, a carga de trabalho necessita de acesso aos dados mais recentes o mais rapidamente possível, mudando para um caso replicado separadamente localmente ou em uma nova região.

Numa estratégia dr, para se preparar para o caso improvável de uma falha catastrófica de uma região, também é importante ter dados replicados para uma região diferente usando a replicação GRS ou RA-GRS. Também deve considerar os seus requisitos para casos de vantagem, como corrupção de dados, onde poderá querer criar instantâneos periódicos para recorrer. Dependendo da importância e do tamanho dos dados, considere os instantâneos delta rolantes de períodos de 1, 6 e 24 horas, de acordo com as tolerâncias de risco.

Para a resiliência de dados com data lake storage gen2, é aconselhável geo-replicar os seus dados através de GRS ou RA-GRS que satisfaça os seus requisitos de HA/DR. Além disso, deve considerar formas de a aplicação utilizando a Data Lake Storage Gen2 falhar automaticamente na região secundária através da monitorização de gatilhos ou duração de tentativas falhadas, ou pelo menos enviar uma notificação aos administradores para intervenção manual. Tenha em mente que há uma troca de falhas em relação à espera que um serviço volte a funcionar.

### <a name="use-distcp-for-data-movement-between-two-locations"></a>Use distcp para movimento de dados entre dois locais

Abreviado para cópia distribuída, o DistCp é uma ferramenta de linha de comando Linux que vem com Hadoop e fornece movimento de dados distribuído entre dois locais. Os dois locais podem ser Data Lake Storage Gen2, HDFS ou S3. Esta ferramenta utiliza trabalhos mapReduce num cluster Hadoop (por exemplo, HDInsight) para escalar em todos os nós. A Distcp é considerada a forma mais rápida de mover grandes dados sem aparelhos especiais de compressão da rede. A Distcp também oferece uma opção para atualizar apenas deltas entre dois locais, lidar com as retrações automáticas, bem como a escala dinâmica do cálculo. Esta abordagem é incrivelmente eficiente quando se trata de replicar coisas como as tabelas Hive/Spark que podem ter muitos ficheiros grandes num único diretório e você só quer copiar sobre os dados modificados. Por estas razões, a Distcp é a ferramenta mais recomendada para copiar dados entre grandes lojas de dados.

Os trabalhos de cópia podem ser desencadeados por fluxos de trabalho Apache Oozie usando frequência ou gatilhos de dados, bem como trabalhos de cron linux. Para trabalhos intensivos de replicação, é recomendado girar um cluster hdinsight hadoop separado que pode ser afinado e dimensionado especificamente para os trabalhos de cópia. Isto garante que os trabalhos de cópia não interferem em trabalhos críticos. Se executar a replicação numa frequência suficientemente larga, o cluster pode mesmo ser retirado entre cada trabalho. Se não conseguir chegar à região secundária, certifique-se de que outro cluster também é girado na região secundária para replicar novos dados de volta para a conta primária de Data Lake Storage Gen2 uma vez que volta a subir. Por exemplo, utilizando o Distcp, consulte [use Distcp para copiar dados entre as bolhas de armazenamento Azure e a Data Lake Storage Gen2](../blobs/data-lake-storage-use-distcp.md).

### <a name="use-azure-data-factory-to-schedule-copy-jobs"></a>Use a Azure Data Factory para agendar trabalhos de cópia

A Azure Data Factory também pode ser usada para agendar trabalhos de cópia usando uma Atividade de Cópia, e pode até ser configurado numa frequência através do Copy Wizard. Tenha em mente que a Azure Data Factory tem um limite de unidades de movimento de dados em nuvem (DMUs), e eventualmente cobre o rendimento/computação para grandes cargas de trabalho de dados. Além disso, a Azure Data Factory não oferece atualmente atualizações delta entre as contas da Data Lake Storage Gen2, pelo que diretórios como as tabelas de Hive exigiriam uma cópia completa para replicar. Consulte o artigo da [fábrica de dados](../../data-factory/load-azure-data-lake-storage-gen2.md) para obter mais informações sobre a cópia com a Data Factory.

## <a name="monitoring-considerations"></a>Considerações de monitorização

Data Lake Storage Gen2 fornece métricas no portal Azure sob a conta de Data Lake Storage Gen2 e no Azure Monitor. A disponibilidade de Data Lake Storage Gen2 é apresentada no portal Azure. Para obter a disponibilidade mais atualizada de uma conta Gen2 de armazenamento de data, você deve executar seus próprios testes sintéticos para validar a disponibilidade. Outras métricas, tais como a utilização total do armazenamento, pedidos de leitura/escrita e ingresss/saídas estão disponíveis para serem alavancadas por aplicações de monitorização e também podem desencadear alertas quando os limiares (por exemplo, latência média ou # de erros por minuto) são ultrapassados.

## <a name="directory-layout-considerations"></a>Considerações de layout de diretório

Ao aterrar dados num lago de dados, é importante pré-planear a estrutura dos dados para que a segurança, a partilha e o processamento possam ser utilizados de forma eficaz. Muitas das seguintes recomendações são aplicáveis para todas as cargas de trabalho de big data. Cada carga de trabalho tem requisitos diferentes sobre como os dados são consumidos, mas abaixo estão alguns layouts comuns a considerar quando se trabalha com IoT e cenários de lote.

### <a name="iot-structure"></a>Estrutura IoT

Nas cargas de trabalho ioT, pode haver uma grande quantidade de dados a serem desembarcados na loja de dados que se estende por inúmeros produtos, dispositivos, organizações e clientes. É importante pré-planear o layout do diretório para organização, segurança e processamento eficiente dos dados para consumidores em fluxo. Um modelo geral a considerar pode ser o seguinte layout:

*{Region}/{SubjectMatter(s)}/{yyyy}/{mm}/{dd}/{hh}/*

Por exemplo, a telemetria de aterragem para um motor de avião no Reino Unido pode parecer a seguinte estrutura:

*REINO UNIDO/Aviões/BA1293/Motor1/2017/08/11/12/*

Há uma razão importante para colocar a data no final da estrutura do diretório. Se pretender bloquear determinadas regiões ou sujeitar assuntos a utilizadores/grupos, então pode facilmente fazê-lo com as permissões POSIX. Caso contrário, se houvesse a necessidade de restringir um determinado grupo de segurança à visualização apenas dos dados do Reino Unido ou de certos aviões, com a estrutura da data à frente seria necessária uma autorização separada para numerosos diretórios ao abrigo de cada diretório de hora. Além disso, ter a estrutura da data à frente aumentaria exponencialmente o número de diretórios à medida que o tempo passava.

### <a name="batch-jobs-structure"></a>Estrutura de empregos em lotes

A partir de um nível elevado, uma abordagem comumente utilizada no processamento de lotes é a de obter dados de terra num diretório "in". Em seguida, uma vez que os dados são processados, coloque os novos dados num diretório "out" para os processos a jusante para consumir. Esta estrutura de diretório é vista por vezes para trabalhos que requerem processamento em ficheiros individuais e podem não exigir um processamento massivamente paralelo sobre grandes conjuntos de dados. Tal como a estrutura IoT acima recomendada, uma boa estrutura de diretórios tem os diretórios de nível de pais para coisas como questões de região e assuntos (por exemplo, organização, produto/produtor). Esta estrutura ajuda a garantir os dados em toda a sua organização e a uma melhor gestão dos dados nas suas cargas de trabalho. Além disso, considere a data e a hora na estrutura para permitir uma melhor organização, pesquisas filtradas, segurança e automação no processamento. O nível de granularidade para a estrutura da data é determinado pelo intervalo em que os dados são carregados ou tratados, tais como hora, dia ou mesmo mensal.

Por vezes, o processamento de ficheiros não é bem sucedido devido à corrupção de dados ou a formatos inesperados. Nesses casos, a estrutura do diretório pode beneficiar de uma pasta **/má** para mover os ficheiros para uma inspeção posterior. O trabalho em lote também pode tratar do relatório ou notificação destes ficheiros *maus* para intervenção manual. Considere a seguinte estrutura do modelo:

*{Region}/{SubjectMatter(s)}/In/{yyyy}/{mm}/{dd}/{hh}/*\
*{Region}/{SubjectMatter(s)}/out/{yyyy}/{mm}/{dd}/{hh}/*\
*{Region}/{SubjectMatter(s)}/Bad/{yyyy}/{mm}/{dd}/{hh}/*

Por exemplo, uma empresa de marketing recebe extratos diários de dados de atualizações de clientes dos seus clientes na América do Norte. Pode parecer o seguinte corte antes e depois de ser processado:

*NA/Extratos/ACMEPaperCo/In/2017/08/14/updates_08142017.csv*\
*NA/Extratos/ACMEPaperCo/out/2017/08/14/processed_updates_08142017.csv*

No caso comum de os dados dos lotes serem tratados diretamente em bases de dados como a Hive ou as bases de dados tradicionais do SQL, não há necessidade de uma pasta **/dentro** **ou/fora,** uma vez que a saída já vai para uma pasta separada para a tabela Hive ou base de dados externa. Por exemplo, extratos diários de clientes aterrariam nas respetivas pastas, e a orquestração por algo como Azure Data Factory, Apache Oozie ou Apache Airflow desencadearia um trabalho diário de Hive ou Spark para processar e escrever os dados numa tabela da Colmeia.
