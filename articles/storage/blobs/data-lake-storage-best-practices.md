---
title: Boas práticas para a utilização do Azure Data Lake Storage Gen2 [ Microsoft Docs
description: Conheça as melhores práticas sobre ingestão de dados, segurança de datas e desempenho relacionado com a utilização de Azure Data Lake Storage Gen2 (anteriormente conhecido como Azure Data Lake Store)
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: normesta
ms.reviewer: sachins
ms.openlocfilehash: ac4e126c7ecbd1fc781db74e5b19635b273bbb34
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72299669"
---
# <a name="best-practices-for-using-azure-data-lake-storage-gen2"></a>Boas práticas para usar o Azure Data Lake Storage Gen2

Neste artigo, você aprende sobre as melhores práticas e considerações para trabalhar com azure Data Lake Storage Gen2. Este artigo fornece informações em torno da segurança, desempenho, resiliência e monitorização para Data Lake Storage Gen2. Antes do Data Lake Storage Gen2, trabalhar com dados verdadeiramente grandes em serviços como o Azure HDInsight era complexo. Você teve que esnsos dados em várias contas de armazenamento Blob para que o armazenamento de petabyte e o desempenho ideal a essa escala pudessem ser alcançados. Data Lake Storage Gen2 suporta tamanhos individuais de ficheiros até 5TB e a maioria dos limites rígidos para o desempenho foram removidos. No entanto, existem ainda algumas considerações que este artigo cobre para que possa obter o melhor desempenho com Data Lake Storage Gen2.

## <a name="security-considerations"></a>Considerações de segurança

O Azure Data Lake Storage Gen2 oferece controlos de acesso POSIX para utilizadores, grupos e diretores de serviço sinuosos do Azure Ative Directory (Azure AD). Estes controlos de acesso podem ser definidos para ficheiros e diretórios existentes. Os controlos de acesso também podem ser utilizados para criar permissões predefinidas que podem ser aplicadas automaticamente a novos ficheiros ou diretórios. Mais detalhes sobre data Lake Storage Gen2 ACLs estão disponíveis no controle de [acesso em Azure Data Lake Storage Gen2](storage-data-lake-storage-access-control.md).

### <a name="use-security-groups-versus-individual-users"></a>Utilize grupos de segurança contra utilizadores individuais

Ao trabalhar com big data em Data Lake Storage Gen2, é provável que um diretor de serviço seja usado para permitir que serviços como o Azure HDInsight trabalhem com os dados. No entanto, pode haver casos em que os utilizadores individuais também precisam de ter acesso aos dados. Em todos os casos, considere fortemente a utilização de [grupos](../common/storage-auth-aad.md) de segurança do Azure Ative Directory em vez de atribuir utilizadores individuais a diretórios e ficheiros.

Uma vez que um grupo de segurança é atribuído permissões, adicionar ou remover utilizadores do grupo não requer quaisquer atualizações para Data Lake Storage Gen2. Isto também ajuda a garantir que não excede o número máximo de entradas de controlo de acesso por lista de controlo de acesso (ACL). Atualmente, esse número é de 32, (incluindo os quatro ACLs estilo POSIX que estão sempre associados a cada ficheiro e diretório): o utilizador próprio, o grupo próprio, a máscara, e outros. Cada diretório pode ter dois tipos de ACL, o ACL de acesso e o ACL padrão, num total de 64 entradas de controlo de acesso. Para obter mais informações sobre estes ACLs, consulte [o controlo de acesso em Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="security-for-groups"></a>Segurança para grupos

Quando você ou os seus utilizadores precisam de acesso a dados numa conta de armazenamento com espaço de nome hierárquico ativado, o melhor é utilizar grupos de segurança do Diretório Ativo Azure. Alguns grupos recomendados para começar podem ser **ReadOnlyUsers,** **WriteAccessUsers**e **FullAccessUsers** para a raiz do recipiente, e até mesmo separar os para subdirectios chave. Se houver outros grupos de utilizadores antecipados que possam ser adicionados mais tarde, mas que ainda não foram identificados, poderá considerar a criação de grupos de segurança manequim que tenham acesso a determinadas pastas. A utilização do grupo de segurança garante que pode evitar longos tempos de processamento ao atribuir novas permissões a milhares de ficheiros.

### <a name="security-for-service-principals"></a>Segurança para os diretores de serviço

Os principais diretores de serviços de Diretório Ativo Azure são normalmente utilizados por serviços como o Azure Databricks para aceder a dados em Data Lake Storage Gen2. Para muitos clientes, um único diretor de serviço azure ative diretório pode ser adequado, e pode ter permissões completas na raiz do recipiente Data Lake Storage Gen2. Outros clientes podem exigir vários clusters com diferentes diretores de serviço onde um cluster tem acesso total aos dados, e outro cluster com apenas acesso lido. 

### <a name="enable-the-data-lake-storage-gen2-firewall-with-azure-service-access"></a>Ativar a firewall Gen2 de armazenamento de data Lake com acesso ao serviço Azure

Data Lake Storage Gen2 suporta a opção de ligar uma firewall e limitar o acesso apenas aos serviços Azure, que é recomendado para limitar o vetor de ataques externos. A firewall pode ser ativada numa conta de armazenamento no portal Azure através do **Firewall Enable Firewall** > **(ON)** > Permitir o acesso às opções de**serviços Do Azure.**

Para aceder à sua conta de armazenamento a partir de Azure Databricks, implemente os Azure Databricks na sua rede virtual e, em seguida, adicione essa rede virtual à sua firewall. Consulte as firewalls de [armazenamento Configure Azure e redes virtuais.](https://docs.microsoft.com/azure/storage/common/storage-network-security)

## <a name="resiliency-considerations"></a>Considerações de resiliência

Ao arquitetar um sistema com Data Lake Storage Gen2 ou qualquer serviço na nuvem, você deve considerar os seus requisitos de disponibilidade e como responder a possíveis interrupções no serviço. Uma questão poderia ser localizada para o caso específico ou mesmo para toda a região, pelo que ter um plano para ambos é importante. Dependendo do objetivo de tempo de recuperação e do objetivo de recuperação sLAs para a sua carga de trabalho, você pode escolher uma estratégia mais ou menos agressiva para alta disponibilidade e recuperação de desastres.

### <a name="high-availability-and-disaster-recovery"></a>Elevada disponibilidade e recuperação após desastre

A elevada disponibilidade (HA) e a recuperação de desastres (DR) podem por vezes ser combinadas em conjunto, embora cada um tenha uma estratégia ligeiramente diferente, especialmente quando se trata de dados. Data Lake Storage Gen2 já lida com replicação 3x sob o capot para proteger contra falhas de hardware localizadas. Adicionalmente, outras opções de replicação, tais como ZRS ou GZRS (pré-visualização), melhoram ha, enquanto GRS & RA-GRS melhorar DR. Ao construir um plano para ha, em caso de interrupção do serviço, a carga de trabalho precisa de ter acesso aos dados mais recentes o mais rapidamente possível, passando para um caso separado replicado localmente ou numa nova região.

Numa estratégia de DR, para se preparar para o caso improvável de uma falha catastrófica de uma região, também é importante que os dados sejam replicados para uma região diferente utilizando a replicação GRS ou RA-GRS. Deve também considerar os seus requisitos para casos de borda, como a corrupção de dados, onde pode querer criar instantâneos periódicos para voltar atrás. Dependendo da importância e dimensão dos dados, considere instantâneos delta rolantes de períodos de 1, 6 e 24 horas, de acordo com tolerâncias de risco.

Para a resiliência de dados com data Lake Storage Gen2, recomenda-se geo-replicar os seus dados através de GRS ou RA-GRS que satisfaça os seus requisitos de HA/DR. Além disso, deve considerar formas de a aplicação utilizando data Lake Storage Gen2 falhar automaticamente na região secundária através de disparos ou comprimento de tentativas falhadas, ou pelo menos enviar uma notificação aos administradores para intervenção manual. Tenha em mente que há trocas de falhas contra esperar que um serviço volte a funcionar.

### <a name="use-distcp-for-data-movement-between-two-locations"></a>Utilizar a Distcp para a circulação de dados entre dois locais

Abreviada para cópia distribuída, distCp é uma ferramenta de linha de comando Linux que vem com Hadoop e fornece movimento de dados distribuídos entre dois locais. As duas localizações podem ser Data Lake Storage Gen2, HDFS ou S3. Esta ferramenta utiliza mapReduce jobs num cluster Hadoop (por exemplo, HDInsight) para escalar todos os nós. A Distcp é considerada a forma mais rápida de mover big data sem aparelhos especiais de compressão de rede. A Distcp também oferece uma opção para apenas atualizar deltas entre dois locais, lidar com repetições automáticas, bem como escala dinâmica de computação. Esta abordagem é incrivelmente eficiente quando se trata de replicar coisas como as tabelas Hive/Spark que podem ter muitos ficheiros grandes num único diretório e só quer copiar sobre os dados modificados. Por estas razões, a Distcp é a ferramenta mais recomendada para copiar dados entre grandes lojas de dados.

Os trabalhos de cópia podem ser desencadeados por fluxos de trabalho apache Oozie usando freqüência ou gatilhos de dados, bem como empregos de cron. Para trabalhos de replicação intensiva, recomenda-se que se elegire um cluster de Hadoop HDInsight separado que possa ser ajustado e dimensionado especificamente para os trabalhos de cópia. Isto garante que os trabalhos de cópia não interferem com empregos críticos. Se executar a replicação numa frequência suficientemente larga, o cluster pode mesmo ser retirado entre cada trabalho. Se falhar na região secundária, certifique-se de que outro cluster também é criado na região secundária para replicar novos dados de volta à conta principal de Data Lake Storage Gen2 assim que voltar a subir. Por exemplo, na utilização do Distcp, consulte [Use Distcp para copiar dados entre as Bolhas](../blobs/data-lake-storage-use-distcp.md)de Armazenamento de Azure e o Data Lake Storage Gen2 .

### <a name="use-azure-data-factory-to-schedule-copy-jobs"></a>Use a Fábrica de Dados Azure para agendar trabalhos de cópia

A Azure Data Factory também pode ser usada para agendar trabalhos de cópia utilizando uma Atividade de Cópia, e pode até ser configurada numa frequência através do Copy Wizard. Tenha em mente que a Azure Data Factory tem um limite de unidades de movimento de dados em nuvem (DMUs), e eventualmente cobre a produção/computação para grandes cargas de trabalho de dados. Além disso, a Azure Data Factory não oferece atualmente atualizações delta entre as contas data Lake Storage Gen2, pelo que diretórios como as tabelas da Hive exigiriam uma cópia completa para se replicarem. Consulte o [artigo da fábrica](../../data-factory/load-azure-data-lake-storage-gen2.md) de dados para obter mais informações sobre a cópia com data factory.

## <a name="monitoring-considerations"></a>Considerações de acompanhamento

Data Lake Storage Gen2 fornece métricas no portal Azure sob a conta Data Lake Storage Gen2 e no Azure Monitor. A disponibilidade de Data Lake Storage Gen2 é exibida no portal Azure. Para obter a disponibilidade mais atualizada de uma conta Gen2 de Armazenamento de Data Lake, você deve executar seus próprios testes sintéticos para validar a disponibilidade. Outras métricas, tais como a utilização total do armazenamento, pedidos de leitura/escrita e entradas/saídas estão disponíveis para serem alavancadas através de aplicações de monitorização e também podem desencadear alertas quando os limiares (por exemplo, latência média ou # de erros por minuto) são ultrapassados.

## <a name="directory-layout-considerations"></a>Considerações de layout de diretório

Ao aterrar dados num lago de dados, é importante pré-planear a estrutura dos dados para que a segurança, a divisão e o processamento possam ser utilizados de forma eficaz. Muitas das seguintes recomendações são aplicáveis a todas as cargas de trabalho de big data. Cada carga de trabalho tem diferentes requisitos sobre como os dados são consumidos, mas abaixo estão alguns layouts comuns a considerar quando se trabalha com cenários de IoT e lote.

### <a name="iot-structure"></a>Estrutura ioT

Nas cargas de trabalho do IoT, pode haver uma grande quantidade de dados a serem desembarcados na loja de dados que se estende por inúmeros produtos, dispositivos, organizações e clientes. É importante pré-planear o layout do diretório para organização, segurança e processamento eficiente dos dados para consumidores de streaming. Um modelo geral a considerar pode ser o seguinte layout:

    {Region}/{SubjectMatter(s)}/{yyyy}/{mm}/{dd}/{hh}/

Por exemplo, a telemetria de aterragem de um motor de avião no Reino Unido pode parecer a seguinte estrutura:

    UK/Planes/BA1293/Engine1/2017/08/11/12/

Há uma razão importante para colocar a data no fim da estrutura do diretório. Se quiser bloquear certas regiões ou sujeitar assuntos a utilizadores/grupos, então pode fazê-lo facilmente com as permissões POSIX. Caso contrário, se houvesse necessidade de restringir um determinado grupo de segurança à visualização apenas dos dados do Reino Unido ou de certos aviões, seria necessária uma autorização separada para numerosos diretórios ao abrigo de cada hora. Além disso, ter a estrutura da data à frente aumentaria exponencialmente o número de diretórios à medida que o tempo passava.

### <a name="batch-jobs-structure"></a>Estrutura de trabalhos de lote

De um alto nível, uma abordagem comumente usada no processamento de lotes é para obter dados em um diretório "in". Em seguida, uma vez que os dados são processados, coloque os novos dados num diretório "out" para os processos a jusante consumirem. Esta estrutura de diretório é vista, por vezes, para trabalhos que exigem o processamento de ficheiros individuais e podem não exigir um processamento maciço paralelo sobre grandes conjuntos de dados. Tal como a estrutura ioT acima recomendada, uma boa estrutura de diretório tem os diretórios de nível parental para coisas como a região e assuntos (por exemplo, organização, produto/produtor). Esta estrutura ajuda a garantir os dados em toda a sua organização e a uma melhor gestão dos dados nas suas cargas de trabalho. Além disso, considere a data e a hora na estrutura para permitir uma melhor organização, pesquisas filtradas, segurança e automação no processamento. O nível de granularidade para a estrutura da data é determinado pelo intervalo em que os dados são carregados ou processados, como por hora, diariamente ou mesmo mensalmente.

Por vezes, o processamento de ficheiros não é bem sucedido devido à corrupção de dados ou formatos inesperados. Nesses casos, a estrutura do diretório pode beneficiar de uma pasta **/mau** para mover os ficheiros para posterior inspeção. O trabalho do lote também pode tratar do relatório ou notificação destes *ficheiros maus* para intervenção manual. Considere a seguinte estrutura do modelo:

    {Region}/{SubjectMatter(s)}/In/{yyyy}/{mm}/{dd}/{hh}/
    {Region}/{SubjectMatter(s)}/Out/{yyyy}/{mm}/{dd}/{hh}/
    {Region}/{SubjectMatter(s)}/Bad/{yyyy}/{mm}/{dd}/{hh}/

Por exemplo, uma empresa de marketing recebe diariamente extratos de dados de atualizações de clientes dos seus clientes na América do Norte. Pode parecer o seguinte corte antes e depois de ser processado:

    NA/Extracts/ACMEPaperCo/In/2017/08/14/updates_08142017.csv
    NA/Extracts/ACMEPaperCo/Out/2017/08/14/processed_updates_08142017.csv

No caso comum de os dados do lote serem processados diretamente em bases de dados como a Hive ou as bases de dados tradicionais sQL, não há necessidade de uma pasta **/dentro** ou **fora,** uma vez que a saída já vai para uma pasta separada para a tabela da Colmeia ou bases de dados externas. Por exemplo, extratos diários de clientes aterrariam nas suas respetivas pastas, e a orquestração por algo como a Azure Data Factory, Apache Oozie ou Apache Airflow desencadearia um trabalho diário de Hive ou Spark para processar e escrever os dados numa tabela da Colmeia.
