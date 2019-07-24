---
title: Práticas recomendadas para usar o Azure Data Lake Storage Gen2 | Microsoft Docs
description: Conheça as práticas recomendadas sobre ingestão de dados, segurança de data e desempenho relacionados ao uso de Azure Data Lake Storage Gen2 (anteriormente conhecido como Azure Data Lake Store)
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: article
ms.date: 12/06/2018
ms.author: normesta
ms.reviewer: sachins
ms.openlocfilehash: c1a298584b2444d52f84c0e599462bc26c63a898
ms.sourcegitcommit: af58483a9c574a10edc546f2737939a93af87b73
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/17/2019
ms.locfileid: "68302626"
---
# <a name="best-practices-for-using-azure-data-lake-storage-gen2"></a>Práticas recomendadas para usar o Azure Data Lake Storage Gen2

Neste artigo, você aprenderá sobre as práticas recomendadas e as considerações para trabalhar com Azure Data Lake Storage Gen2. Este artigo fornece informações sobre segurança, desempenho, resiliência e monitoramento para Data Lake Storage Gen2. Antes de Data Lake Storage Gen2, trabalhar com verdadeiramente Big Data em serviços como o Azure HDInsight era complexo. Você precisava fragmentar dados em várias contas de armazenamento de BLOBs para que o armazenamento de petabyte e o desempenho ideal nessa escala pudessem ser atingidos. Com Data Lake Storage Gen2, a maioria dos limites rígidos de tamanho e desempenho são removidos. No entanto, ainda há algumas considerações que este artigo aborda para que você possa obter o melhor desempenho com Data Lake Storage Gen2.

## <a name="security-considerations"></a>Considerações de segurança

O Azure Data Lake Storage Gen2 oferece controles de acesso POSIX para usuários, grupos e entidades de serviço do Azure Active Directory (AD do Azure). Esses controles de acesso podem ser definidos para arquivos e diretórios existentes. Os controles de acesso também podem ser usados para criar permissões padrão que podem ser aplicadas automaticamente a novos arquivos ou diretórios. Mais detalhes sobre Data Lake Storage Gen2 ACLs estão disponíveis no [controle de acesso no Azure data Lake Storage Gen2](storage-data-lake-storage-access-control.md).

### <a name="use-security-groups-versus-individual-users"></a>Usar grupos de segurança versus usuários individuais

Ao trabalhar com Big Data no Data Lake Storage Gen2, é provável que uma entidade de serviço seja usada para permitir que serviços como o Azure HDInsight trabalhem com os dados. No entanto, pode haver casos em que os usuários individuais precisam acessar os dados também. Em todos os casos, é altamente recomendável usar Azure Active Directory [grupos de segurança](../common/storage-auth-aad.md) em vez de atribuir usuários individuais a diretórios e arquivos.

Depois que um grupo de segurança recebe permissões, adicionar ou remover usuários do grupo não exige nenhuma atualização para Data Lake Storage Gen2. Isso também ajuda a garantir que você não exceda o número máximo de entradas de controle de acesso por ACL (lista de controle de acesso). Atualmente, esse número é 32, (incluindo as quatro ACLs de estilo POSIX que estão sempre associadas a todos os arquivos e diretórios): o usuário proprietário, o grupo proprietário, a máscara e outros. Cada diretório pode ter dois tipos de ACL, a ACL de acesso e a ACL padrão, para um total de 64 entradas de controle de acesso. Para obter mais informações sobre essas ACLs, consulte [controle de acesso em Azure data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="security-for-groups"></a>Segurança para grupos

Quando você ou seus usuários precisam de acesso a dados em uma conta de armazenamento com o namespace hierárquico habilitado, é melhor usar Azure Active Directory grupos de segurança. Alguns grupos recomendados para começar podem ser **ReadOnlyUsers**, **WriteAccessUsers**e **FullAccessUsers** para a raiz do sistema de arquivos e até mesmo separá-los para subdiretórios de chave. Se houver outros grupos de usuários previstos que possam ser adicionados posteriormente, mas ainda não tiverem sido identificados, você poderá considerar a criação de grupos de segurança fictícios que tenham acesso a determinadas pastas. O uso do grupo de segurança garante que você pode evitar tempo de processamento longo ao atribuir novas permissões a milhares de arquivos.

### <a name="security-for-service-principals"></a>Segurança para entidades de serviço

Azure Active Directory entidades de serviço geralmente são usadas por serviços como Azure Databricks para acessar dados no Data Lake Storage Gen2. Para muitos clientes, uma única entidade de serviço de Azure Active Directory pode ser adequada e pode ter permissões completas na raiz do sistema de arquivos de Data Lake Storage Gen2. Outros clientes podem exigir vários clusters com entidades de serviço diferentes, em que um cluster tem acesso completo aos dados e outro cluster com acesso de leitura. 

### <a name="enable-the-data-lake-storage-gen2-firewall-with-azure-service-access"></a>Habilitar o Data Lake Storage Gen2 firewall com acesso de serviço do Azure

Data Lake Storage Gen2 dá suporte à opção de ativar um firewall e limitar o acesso somente aos serviços do Azure, o que é recomendado para limitar o vetor de ataques externos. O firewall pode ser habilitado em uma conta de armazenamento no portal do Azure por meio do **Firewall** > **habilitar o firewall (ativado)**  > **permitir acesso às opções de serviços do Azure** .

Para acessar sua conta de armazenamento do Azure Databricks, implante Azure Databricks em sua rede virtual e, em seguida, adicione essa rede virtual ao firewall. Consulte [configurar redes virtuais e firewalls de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-network-security).

## <a name="resiliency-considerations"></a>Considerações de resiliência

Ao arquitetar um sistema com Data Lake Storage Gen2 ou qualquer serviço de nuvem, você deve considerar seus requisitos de disponibilidade e como responder a possíveis interrupções no serviço. Um problema pode ser localizado na instância específica ou até mesmo em toda a região, então ter um plano para ambos é importante. Dependendo do objetivo do tempo de recuperação e dos SLAs de objetivo de ponto de recuperação para sua carga de trabalho, você pode escolher uma estratégia mais ou menos agressiva para alta disponibilidade e recuperação de desastres.

### <a name="high-availability-and-disaster-recovery"></a>Elevada disponibilidade e recuperação após desastre

A HA (alta disponibilidade) e a DR (recuperação de desastre) às vezes podem ser combinadas em conjunto, embora cada uma tenha uma estratégia um pouco diferente, especialmente quando se trata de dados. O Data Lake Storage Gen2 já manipula a replicação 3x nos bastidores para proteger contra falhas de hardware localizadas. Além disso, outras opções de replicação, como ZRS, melhoram a HA enquanto GRS & RA-GRS a melhorar a recuperação de desastres. Ao criar um plano de HA, no caso de uma interrupção de serviço, a carga de trabalho precisa ter acesso aos dados mais recentes o mais rápido possível, alternando para uma instância replicada separada localmente ou em uma nova região.

Em uma estratégia de DR, para se preparar para o evento improvável de uma falha catastrófica de uma região, também é importante ter dados replicados em uma região diferente usando a replicação GRS ou RA-GRS. Você também deve considerar seus requisitos para casos de borda, como corrupção de dados, em que você pode desejar criar instantâneos periódicos para o qual fazer fallback. Dependendo da importância e do tamanho dos dados, considere a possibilidade de reverter instantâneos Delta de períodos de 1, 6 e 24 horas, de acordo com as tolerâncias de risco.

Para resiliência de dados com Data Lake Storage Gen2, é recomendável replicar geograficamente seus dados por meio de GRS ou RA-GRS que atenda aos seus requisitos de HA/DR. Além disso, você deve considerar as maneiras para o aplicativo usar Data Lake Storage Gen2 para fazer failover automaticamente na região secundária por meio de gatilhos de monitoramento ou comprimento de tentativas com falha, ou pelo menos enviar uma notificação aos administradores para intervenção manual. Lembre-se de que há uma compensação de failover em comparação com a espera de um serviço voltar a ficar online.

### <a name="use-distcp-for-data-movement-between-two-locations"></a>Usar Distcp para movimentação de dados entre dois locais

Abreviação de cópia distribuída, o DistCp é uma ferramenta de linha de comando do Linux que vem com o Hadoop e fornece movimentação de dados distribuída entre dois locais. Os dois locais podem ser Data Lake Storage Gen2, HDFS ou S3. Essa ferramenta usa trabalhos MapReduce em um cluster Hadoop (por exemplo, HDInsight) para escalar horizontalmente em todos os nós. O Distcp é considerado a maneira mais rápida de mover Big Data sem dispositivos de compactação de rede especiais. O Distcp também fornece uma opção para atualizar somente os deltas entre dois locais, lida com tentativas automáticas, bem como o dimensionamento dinâmico da computação. Essa abordagem é incrivelmente eficiente quando se trata de replicar coisas como tabelas de Hive/Spark que podem ter muitos arquivos grandes em um único diretório e você só deseja copiar sobre os dados modificados. Por esses motivos, o Distcp é a ferramenta mais recomendada para copiar dados entre Big Data repositórios.

Os trabalhos de cópia podem ser disparados por fluxos de trabalho do Apache Oozie usando frequência ou gatilhos de dados, bem como trabalhos do Linux cron. Para trabalhos de replicação com uso intensivo, é recomendável criar um cluster HDInsight Hadoop separado que possa ser ajustado e dimensionado especificamente para os trabalhos de cópia. Isso garante que os trabalhos de cópia não interfiram em trabalhos críticos. Se estiver executando a replicação em uma frequência suficientemente ampla, o cluster poderá até ser retirado entre cada trabalho. Se houver failover na região secundária, certifique-se de que outro cluster também seja girado na região secundária para replicar novos dados de volta para a conta de Data Lake Storage Gen2 primária após a realização do backup. Para obter exemplos de como usar Distcp, consulte [usar o Distcp para copiar dados entre os blobs de armazenamento do Azure e data Lake Storage Gen2](../blobs/data-lake-storage-use-distcp.md).

### <a name="use-azure-data-factory-to-schedule-copy-jobs"></a>Usar Azure Data Factory para agendar trabalhos de cópia

Azure Data Factory também pode ser usado para agendar trabalhos de cópia usando uma atividade de cópia e pode até mesmo ser configurado em uma frequência por meio do assistente de cópia. Tenha em mente que Azure Data Factory tem um limite de DMUs (unidades de movimentação de dados de nuvem) e, eventualmente, limita a taxa de transferência/computação para cargas de trabalho de dados grandes. Além disso, Azure Data Factory atualmente não oferece atualizações Delta entre contas de Data Lake Storage Gen2, portanto, diretórios como tabelas Hive exigirão uma cópia completa para replicar. Consulte o [artigo data Factory](../../data-factory/load-azure-data-lake-storage-gen2.md) para obter mais informações sobre como copiar com data Factory.

## <a name="monitoring-considerations"></a>Considerações sobre a monitorização

Data Lake Storage Gen2 fornece métricas no portal do Azure na conta de Data Lake Storage Gen2 e no Azure Monitor. A disponibilidade de Data Lake Storage Gen2 é exibida no portal do Azure. Para obter a disponibilidade mais atualizada de uma conta de Data Lake Storage Gen2, você deve executar seus próprios testes sintéticos para validar a disponibilidade. Outras métricas, como a utilização total do armazenamento, as solicitações de leitura/gravação e a entrada/saída, estão disponíveis para serem aproveitadas pelo monitoramento de aplicativos e também podem disparar alertas quando os limites (por exemplo, latência média ou número de erros por minuto) são excedidos.

## <a name="directory-layout-considerations"></a>Considerações sobre layout de diretório

Quando dados de aterrissagem estão em um data Lake, é importante planejar previamente a estrutura dos dados para que a segurança, o particionamento e o processamento possam ser utilizados com eficiência. Muitas das recomendações a seguir são aplicáveis a todas as cargas de trabalho do Big Data. Cada carga de trabalho tem requisitos diferentes de como os dados são consumidos, mas abaixo estão alguns layouts comuns a serem considerados ao trabalhar com cenários de lote e de IoT.

### <a name="iot-structure"></a>Estrutura de IoT

Em cargas de trabalho de IoT, pode haver um grande volume de dados sendo descarregou no armazenamento de dados que abrange vários produtos, dispositivos, organizações e clientes. É importante planejar previamente o layout do diretório para organização, segurança e processamento eficiente dos dados para consumidores de fluxo contínuo. Um modelo geral a ser considerado pode ser o seguinte layout:

    {Region}/{SubjectMatter(s)}/{yyyy}/{mm}/{dd}/{hh}/

Por exemplo, a telemetria de aterrissagem para um motor de avião no Reino Unido pode se parecer com a seguinte estrutura:

    UK/Planes/BA1293/Engine1/2017/08/11/12/

Há um motivo importante para colocar a data no final da estrutura do diretório. Se você quiser bloquear determinadas regiões ou assuntos de assunto para usuários/grupos, poderá facilmente fazer isso com as permissões de POSIX. Caso contrário, se houvesse a necessidade de restringir um determinado grupo de segurança para exibir apenas os dados do Reino Unido ou determinados planos, com a estrutura de data na frente, uma permissão separada seria necessária para vários diretórios em cada diretório de hora. Além disso, ter a estrutura de data na frente aumentaria exponencialmente o número de diretórios à medida que o tempo passou.

### <a name="batch-jobs-structure"></a>Estrutura de trabalhos em lotes

De um alto nível, uma abordagem comumente usada no processamento em lotes é colocar os dados em um diretório "in". Em seguida, depois que os dados são processados, coloque os novos dados em um diretório "out" para que os processos downstream sejam consumidos. Essa estrutura de diretório é vista às vezes para trabalhos que exigem processamento em arquivos individuais e podem não exigir processamento maciçomente paralelo em grandes conjuntos de dados. Como a estrutura de IoT recomendada acima, uma boa estrutura de diretório tem os diretórios de nível pai para coisas como assuntos de região e assunto (por exemplo, organização, produto/produtor). Essa estrutura ajuda a proteger os dados em sua organização e o melhor gerenciamento dos dados em suas cargas de trabalho. Além disso, considere a data e a hora na estrutura para permitir melhor organização, pesquisas filtradas, segurança e automação no processamento. O nível de granularidade da estrutura de data é determinado pelo intervalo no qual os dados são carregados ou processados, como por hora, diariamente ou até mesmo mensalmente.

Às vezes, o processamento de arquivos não é bem-sucedido devido a dados corrompidos ou formatos inesperados. Nesses casos, a estrutura de diretório pode se beneficiar de uma pasta **/Bad** para mover os arquivos para para uma inspeção mais detalhada. O trabalho em lotes também pode manipular o relatório ou a notificação  desses arquivos inválidos para intervenção manual. Considere a seguinte estrutura de modelo:

    {Region}/{SubjectMatter(s)}/In/{yyyy}/{mm}/{dd}/{hh}/
    {Region}/{SubjectMatter(s)}/Out/{yyyy}/{mm}/{dd}/{hh}/
    {Region}/{SubjectMatter(s)}/Bad/{yyyy}/{mm}/{dd}/{hh}/

Por exemplo, uma empresa de marketing recebe extratos de dados diários de atualizações do cliente de seus clientes no América do Norte. Ele pode parecer com o seguinte trecho antes e depois de ser processado:

    NA/Extracts/ACMEPaperCo/In/2017/08/14/updates_08142017.csv
    NA/Extracts/ACMEPaperCo/Out/2017/08/14/processed_updates_08142017.csv

No caso comum de dados do lote que estão sendo processados diretamente em bancos de dado como o hive ou bancos de dados SQL tradicionais, não há necessidade de uma pasta **/in** ou **/out** , pois a saída já entra em uma pasta separada para a tabela de Hive ou externa banco. Por exemplo, os extratos diários de clientes seriam inseridos em suas respectivas pastas e a orquestração por algo como Azure Data Factory, Apache Oozie ou Apache enflow dispararia um trabalho diário de Hive ou Spark para processar e gravar os dados em uma tabela Hive.
