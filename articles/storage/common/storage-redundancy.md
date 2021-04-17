---
title: Redundância de dados
titleSuffix: Azure Storage
description: Compreenda a redundância de dados no Azure Storage. Os dados na sua conta de Armazenamento Microsoft Azure são replicados para durabilidade e alta disponibilidade.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 04/16/2021
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 3665421ddbdd9cf079ff4aab9377fc9164a1599c
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575365"
---
# <a name="azure-storage-redundancy"></a>Redundância do Armazenamento do Azure

O Azure Storage armazena sempre várias cópias dos seus dados para que esteja protegido contra eventos planeados e não planeados, incluindo falhas de hardware transitórios, falhas de rede ou de energia e desastres naturais maciços. A redundância garante que a sua conta de armazenamento cumpre os seus objetivos de disponibilidade e durabilidade mesmo face a falhas.

Ao decidir qual a opção de despedimento melhor para o seu cenário, considere as trocas entre custos mais baixos e maior disponibilidade. Os fatores que ajudam a determinar qual a opção de redundância que deve escolher incluem:  

- Como os seus dados são replicados na região primária
- Se os seus dados são replicados para uma segunda região que está geograficamente distante da região primária, para proteger contra desastres regionais
- Se a sua aplicação requer acesso lido aos dados replicados na região secundária se a região primária ficar indisponível por qualquer motivo

## <a name="redundancy-in-the-primary-region"></a>Redundância na região primária

Os dados de uma conta de Armazenamento Azure são sempre replicados três vezes na região primária. O Armazenamento do Microsoft Azure oferece duas opções para a forma como os dados são replicados na região primária:

- **O armazenamento localmente redundante (LRS)** copia os seus dados de forma sincronizada três vezes num único local físico na região primária. O LRS é a opção de replicação menos dispendiosa, mas não é recomendado para aplicações que exijam elevada disponibilidade.
- **O armazenamento redundante de zona (ZRS)** copia os seus dados de forma sincronizada em três zonas de disponibilidade de Azure na região primária. Para aplicações que requerem elevada disponibilidade, a Microsoft recomenda a utilização de ZRS na região primária e também a replicação para uma região secundária.

> [!NOTE]
> A Microsoft recomenda a utilização de ZRS na região primária para o Azure Data Lake Storage Gen2.

### <a name="locally-redundant-storage"></a>Armazenamento localmente redundante

O armazenamento localmente redundante (LRS) replica os seus dados três vezes dentro de um único centro de dados na região primária. O LRS proporciona pelo menos 99.99999999999999999 % (11 noves) de durabilidade de objetos durante um determinado ano.

O LRS é a opção de despedimento mais barata e oferece a menor durabilidade em comparação com outras opções. O LRS protege os seus dados contra as falhas de suporte do servidor e da unidade. No entanto, se ocorrer uma catástrofe como incêndio ou inundação dentro do centro de dados, todas as réplicas de uma conta de armazenamento utilizando LRS podem ser perdidas ou irrecuperáveis. Para mitigar este risco, a Microsoft recomenda a utilização [de armazenamento redundante de zona](#zone-redundant-storage) (ZRS), armazenamento [geo-redundante](#geo-redundant-storage) (GRS) ou [armazenamento de zonas geo-redundantes](#geo-zone-redundant-storage) (GZRS).

Um pedido de escrita para uma conta de armazenamento que está a usar LRS acontece sincronizadamente. A operação de escrita só regressa com sucesso depois de os dados terem sido escritos às três réplicas.

O diagrama a seguir mostra como os seus dados são replicados num único centro de dados com LRS:

:::image type="content" source="media/storage-redundancy/locally-redundant-storage.png" alt-text="Diagrama mostrando como os dados são replicados num único centro de dados com LRS":::

LRS é uma boa escolha para os seguintes cenários:

- Se a sua aplicação armazena dados que podem ser facilmente reconstruídos se ocorrer perda de dados, poderá optar pelo LRS.
- Se a sua aplicação se limitar a replicar dados apenas dentro de um país ou região devido aos requisitos de governação de dados, poderá optar pelo LRS. Em alguns casos, as regiões emparelhadas em que os dados são geo-replicados podem estar noutro país ou região. Para obter mais informações sobre as regiões emparelhadas, consulte [as regiões de Azure.](https://azure.microsoft.com/regions/)

### <a name="zone-redundant-storage"></a>Armazenamento com redundância entre zonas

O armazenamento redundante de zona (ZRS) replica os seus dados de armazenamento Azure sincronizados em três zonas de disponibilidade de Azure na região primária. Cada zona de disponibilidade é uma localização física separada com energia, refrigeração e rede independentes, A ZRS oferece durabilidade para objetos de dados de armazenamento Azure de pelo menos 99.9999999999999999999999999999 (12 9's) durante um determinado ano.

Com o ZRS, os seus dados ainda estão acessíveis tanto para operações de leitura como para escrever, mesmo que uma zona fique indisponível. Se uma zona ficar indisponível, o Azure realiza atualizações de rede, como a re-apontamento de DNS. Estas atualizações podem afetar a sua aplicação se aceder a dados antes de as atualizações terem sido concluídas. Ao conceber aplicações para ZRS, siga práticas para o manuseamento transitório de falhas, incluindo implementar políticas de retrip com recuo exponencial.

Um pedido de escrita para uma conta de armazenamento que está a usar o ZRS acontece sincronizadamente. A operação de escrita só regressa com sucesso depois de os dados forem escritos a todas as réplicas nas três zonas de disponibilidade.

A Microsoft recomenda a utilização de ZRS na região primária para cenários que exijam consistência, durabilidade e alta disponibilidade. O ZRS também é recomendado para restringir a replicação de dados a um país ou região para satisfazer os requisitos de governação de dados.

O seguinte diagrama mostra como os seus dados são replicados em zonas de disponibilidade na região primária com ZRS:

:::image type="content" source="media/storage-redundancy/zone-redundant-storage.png" alt-text="Diagrama mostrando como os dados são replicados na região primária com ZRS":::

O ZRS proporciona um excelente desempenho, baixa latência e resiliência para os seus dados se ficar temporariamente indisponível. No entanto, o ZRS por si só pode não proteger os seus dados contra um desastre regional onde várias zonas são permanentemente afetadas. Para proteção contra desastres regionais, a Microsoft recomenda a utilização [de armazenamento redundante de geo-zona](#geo-zone-redundant-storage) (GZRS), que utiliza ZRS na região primária e também geo-replica os seus dados para uma região secundária.

O quadro que se segue mostra quais os tipos de contas de armazenamento que suportam o ZRS em que regiões:

| Tipo de conta de armazenamento | Regiões suportadas | Serviços suportados |
|--|--|--|
| Finalidade geral v2<sup>1</sup> | (África) África do Sul Norte<br /> (Ásia-Pacífico) Sudeste Asiático<br /> (Ásia-Pacífico) Austrália Leste<br /> Leste do Japão (Ásia-Pacífico) <br /> (Canadá) Central do Canadá<br /> (Europa) Norte da Europa<br /> (Europa) Europa Ocidental<br /> (Europa) França Central<br /> (Europa) Alemanha Centro-Oeste<br /> (Europa) Reino Unido Sul<br /> (América do Sul) Brasil Sul<br /> (EUA) Centro dos EUA<br /> (EUA) E.U.A Leste<br /> (EUA) Leste dos EUA 2<br /> (EUA) Centro-Sul dos EUA<br /> (EUA) Eua Ocidentais<br /> (EUA) Oeste DOS EUA 2 | Blobs de blocos<br /> Bolhas de página<sup>2</sup><br /> Ações de ficheiros (padrão)<br /> Tables<br /> Filas<br /> |
| BlockBlobStorage<sup>1</sup> | Sudeste Asiático<br /> Leste da Austrália<br /> Norte da Europa<br /> Europa Ocidental<br /> França Central <br /> Leste do Japão<br /> Sul do Reino Unido <br /> E.U.A Leste <br /> E.U.A. Leste 2 <br /> E.U.A. Oeste 2| Bolhas de bloco premium apenas |
| Arquitorage de arquivos | Sudeste Asiático<br /> Leste da Austrália<br /> Norte da Europa<br /> Europa Ocidental<br /> França Central <br /> Leste do Japão<br /> Sul do Reino Unido <br /> E.U.A Leste <br /> E.U.A. Leste 2 <br /> E.U.A. Oeste 2 | Os ficheiros premium partilham apenas |

<sup>1</sup> O nível de arquivo não é suportado atualmente para contas ZRS.<br />
<sup>2</sup> As contas de armazenamento que contêm discos geridos Azure para máquinas virtuais usam sempre LRS. Os discos não geridos Azure também devem utilizar LRS. É possível criar uma conta de armazenamento para discos não geridos Azure que usam GRS, mas não é recomendado devido a potenciais problemas com consistência sobre a geo-replicação assíncrona. Nem discos geridos nem não geridos suportam ZRS ou GZRS. Para obter mais informações sobre discos geridos, consulte [preços para discos geridos Azure](https://azure.microsoft.com/pricing/details/managed-disks/).

Para obter informações sobre quais as regiões que apoiam o ZRS, consulte **o apoio de serviços por região** em [Quais são as Zonas de Disponibilidade Azure?](../../availability-zones/az-overview.md)

## <a name="redundancy-in-a-secondary-region"></a>Redundância numa região secundária

Para aplicações que exijam elevada disponibilidade, pode optar por copiar adicionalmente os dados da sua conta de armazenamento para uma região secundária que fica a centenas de quilómetros da região primária. Se a sua conta de armazenamento for copiada para uma região secundária, então os seus dados são duráveis mesmo no caso de uma paragem regional completa ou de um desastre em que a região primária não é recuperável.

Quando cria uma conta de armazenamento, seleciona a região primária para a conta. A região secundária emparelhada é determinada com base na região primária, e não pode ser alterada. Para obter mais informações sobre as regiões apoiadas pelo Azure, consulte as [regiões de Azure.](https://azure.microsoft.com/global-infrastructure/regions/)

O Azure Storage oferece duas opções para copiar os seus dados para uma região secundária:

- O **armazenamento georredundante (GRS)** copia os dados de forma síncrona três vezes numa única localização física na região primária através do LRS. Em seguida, copia os dados de forma assíncrona para uma única localização física na região secundária. Dentro da região secundária, os seus dados são copiados sincronizadamente três vezes utilizando LRS.
- **O armazenamento de zonas-redundantes geo-zona (GZRS)** copia os seus dados sincronizadamente em três zonas de disponibilidade de Azure na região primária utilizando zRS. Em seguida, copia os dados de forma assíncrona para uma única localização física na região secundária. Dentro da região secundária, os seus dados são copiados sincronizadamente três vezes utilizando LRS.

> [!NOTE]
> A principal diferença entre GRS e GZRS é a forma como os dados são replicados na região primária. Dentro da região secundária, os dados são sempre replicados sincronizadamente três vezes utilizando LRS. O LRS na região secundária protege os seus dados contra falhas de hardware.

Com GRS ou GZRS, os dados na região secundária não estão disponíveis para ler ou escrever acesso, a menos que haja uma falha na região secundária. Para ler o acesso à região secundária, configuure a sua conta de armazenamento para utilizar o armazenamento geo-redundante de acesso à leitura (RA-GRS) ou o armazenamento de zonas de acesso de leitura redundantes (RA-GZRS). Para mais informações, consulte [Ler o acesso aos dados na região secundária.](#read-access-to-data-in-the-secondary-region)

Se a região primária ficar indisponível, pode optar por falhar na região secundária. Após a conclusão do failover, a região secundária torna-se a região primária, e pode voltar a ler e escrever dados. Para obter mais informações sobre a recuperação de desastres e para aprender a falhar na região secundária, consulte a [recuperação de desastres e a conta de armazenamento falhada.](storage-disaster-recovery-guidance.md)

> [!IMPORTANT]
> Uma vez que os dados são replicados para a região secundária de forma assíncrona, uma falha que afeta a região primária pode resultar em perda de dados se a região primária não puder ser recuperada. O intervalo entre os mais recentes escreve para a região primária e a última escrita para a região secundária é conhecido como o objetivo do ponto de recuperação (RPO). O RPO indica o ponto no tempo para que os dados podem ser recuperados. O Azure Storage normalmente tem um RPO inferior a 15 minutos, embora não exista atualmente nenhum SLA sobre o tempo que leva para replicar dados para a região secundária.

### <a name="geo-redundant-storage"></a>Armazenamento georredundante

O armazenamento georredundante (GRS) copia os dados de forma síncrona três vezes numa única localização física na região primária através do LRS. Em seguida, copia os seus dados assíncronamente para um único local físico numa região secundária que fica a centenas de milhas da região primária. A GRS oferece durabilidade para objetos de dados de armazenamento Azure de pelo menos 99.9999999999999999999999999999999999999999999 (16 9's) durante um determinado ano.

Uma operação de escrita é inicialmente comprometida com a localização primária e replicada usando LRS. A atualização é então replicada assíncronamente para a região secundária. Quando os dados são escritos para a localização secundária, também é replicado dentro desse local usando LRS.

O seguinte diagrama mostra como os seus dados são replicados com GRS ou RA-GRS:

:::image type="content" source="media/storage-redundancy/geo-redundant-storage.png" alt-text="Diagrama que mostra como os dados são replicados com GRS ou RA-GRS":::

### <a name="geo-zone-redundant-storage"></a>Armazenamento georredundante com redundância entre zonas

O armazenamento de zonas geo-redundantes (GZRS) combina a elevada disponibilidade proporcionada pela redundância através das zonas de disponibilidade com proteção contra interrupções regionais fornecidas por geo-replicação. Os dados de uma conta de armazenamento GZRS são copiados em três [zonas de disponibilidade de Azure](../../availability-zones/az-overview.md) na região primária e também são replicados numa região geográfica secundária para proteção contra desastres regionais. A Microsoft recomenda a utilização de GZRS para aplicações que exijam a máxima consistência, durabilidade e disponibilidade, excelente desempenho e resiliência para a recuperação de desastres.

Com uma conta de armazenamento GZRS, pode continuar a ler e escrever dados se uma zona de disponibilidade ficar indisponível ou se não for recuperável. Além disso, os seus dados também são duráveis em caso de uma paralisação regional completa ou de um desastre em que a região primária não é recuperável. O GZRS foi concebido para proporcionar pelo menos 99.99999999999999999999999999999999 (16 9's) de durabilidade de objetos durante um determinado ano.

O seguinte diagrama mostra como os seus dados são replicados com GZRS ou RA-GZRS:

:::image type="content" source="media/storage-redundancy/geo-zone-redundant-storage.png" alt-text="Diagrama que mostra como os dados são replicados com GZRS ou RA-GZRS":::

Apenas as contas de armazenamento v2 de uso geral suportam GZRS e RA-GZRS. Para obter mais informações sobre tipos de conta de armazenamento, consulte [a visão geral da conta de armazenamento Azure](storage-account-overview.md). GZRS e RA-GZRS suportam blocos de blocos, bolhas de página (exceto discos VHD), ficheiros, tabelas e filas.

Os GZRS e RA-GZRS são apoiados nas seguintes regiões:

- (África) África do Sul Norte
- (Ásia-Pacífico) Ásia Oriental
- (Ásia-Pacífico) Sudeste Asiático
- (Ásia-Pacífico) Austrália Leste
- (Ásia-Pacífico) Índia Central
- Leste do Japão (Ásia-Pacífico) 
- (Ásia-Pacífico) Coreia Central
- (Canadá) Central do Canadá
- (Europa) Norte da Europa
- (Europa) Europa Ocidental
- (Europa) França Central
- (Europa) Alemanha Centro-Oeste
- (Europa) Leste da Noruega
- (Europa) Suíça Norte
- (Europa) Reino Unido Sul
- (Médio Oriente) Uae Norte
- (América do Sul) Brasil Sul
- (EUA) Centro dos EUA
- (EUA) E.U.A Leste
- (EUA) Leste dos EUA 2
- (EUA) Centro-Norte dos EUA
- (EUA) Centro-Sul dos EUA
- (EUA) Eua Ocidentais
- (EUA) Oeste DOS EUA 2

Para obter informações sobre preços, consulte os [](https://azure.microsoft.com/pricing/details/storage/files/)detalhes dos preços das [bolhas, ficheiros,](https://azure.microsoft.com/pricing/details/storage/blobs)filas e [tabelas.](https://azure.microsoft.com/pricing/details/storage/queues/) [](https://azure.microsoft.com/pricing/details/storage/tables/)

## <a name="read-access-to-data-in-the-secondary-region"></a>Ler acesso aos dados na região secundária

O armazenamento geo-redundante (com GRS ou GZRS) replica os seus dados para outra localização física na região secundária para proteger contra interrupções regionais. No entanto, esses dados só estão disponíveis para serem lidos se o cliente ou a Microsoft iniciarem uma falha da região primária para a região secundária. Quando permite o acesso de leitura à região secundária, os seus dados estão disponíveis para serem lidos a todo o momento, incluindo numa situação em que a região primária fica indisponível. Para ler o acesso à região secundária, permitir o armazenamento geo-redundante de acesso à leitura (RA-GRS) ou o armazenamento de zonas de acesso à leitura (RA-GZRS).

> [!NOTE]
> A Azure Files não suporta o armazenamento geo-redundante de acesso à leitura (RA-GRS) e o armazenamento de geo-zonas redundantes de acesso à leitura (RA-GZRS).

### <a name="design-your-applications-for-read-access-to-the-secondary"></a>Desenhe as suas aplicações para ler o acesso ao secundário

Se a sua conta de armazenamento estiver configurada para o acesso lido à região secundária, então pode projetar as suas aplicações para mudar sem problemas para os dados de leitura da região secundária se a região primária ficar indisponível por qualquer motivo. 

A região secundária está disponível para acesso à leitura depois de ativar RA-GRS ou RA-GZRS, para que possa testar a sua aplicação com antecedência para se certificar de que será lida corretamente a partir do secundário em caso de interrupção. Para obter mais informações sobre como projetar as suas aplicações para uma elevada disponibilidade, consulte [Use geo-redundância para desenhar aplicações altamente disponíveis.](geo-redundant-design.md)

Quando se lê o acesso ao secundário, a sua aplicação pode ser lida a partir do ponto final secundário, bem como a partir do ponto final primário. O ponto final secundário apêndice o *sufixo ( secundário* ao nome da conta. Por exemplo, se o seu principal ponto final para o armazenamento blob for `myaccount.blob.core.windows.net` , então o ponto final secundário é `myaccount-secondary.blob.core.windows.net` . As chaves de acesso à conta da sua conta de armazenamento são as mesmas tanto para os pontos finais primários como secundários.

### <a name="check-the-last-sync-time-property"></a>Consulte a propriedade Last Sync Time

Como os dados são replicados para a região secundária de forma assíncrona, a região secundária está muitas vezes por trás da região primária. Se um falhanço acontecer na região primária, é provável que todos os escritos para as primárias ainda não tenham sido replicados para o secundário.

Para determinar quais as operações de escrita que foram replicadas na região secundária, a sua aplicação pode verificar a propriedade **Last Sync Time** para a sua conta de armazenamento. Todas as operações de escrita escrita escrita para a região primária antes da última sincronização foram replicadas com sucesso para a região secundária, o que significa que estão disponíveis para serem lidas a partir do secundário. Quaisquer operações de escrita escrita escrita para a região primária após a última hora de sincronização podem ou não ter sido replicadas na região secundária, o que significa que podem não estar disponíveis para operações de leitura.

Pode consultar o valor da propriedade **Last Sync Time** utilizando a Azure PowerShell, Azure CLI ou uma das bibliotecas do cliente do Azure Storage. A propriedade **Last Sync Time** é um valor de data/hora GMT. Para mais informações, [consulte a propriedade Da Última Hora de Sincronização para obter uma conta de armazenamento.](last-sync-time-get.md)

## <a name="summary-of-redundancy-options"></a>Resumo das opções de despedimento

As tabelas nas secções seguintes resumem as opções de despedimento disponíveis para o Armazenamento Azure

### <a name="durability-and-availability-parameters"></a>Parâmetros de durabilidade e disponibilidade

A tabela a seguir descreve parâmetros-chave para cada opção de redundância:

| Parâmetro | LRS | ZRS | GRS/RA-GRS | GZRS/RA-GZRS |
|:-|:-|:-|:-|:-|
| Durabilidade por cento de objetos ao longo de um determinado ano | pelo menos 99.9999999999999999 % (11 9's) | pelo menos 99.9999999999999999999 % (12 9's) | pelo menos 99.999999999999999999999999999999999 (16 9's) | pelo menos 99.999999999999999999999999999999999 (16 9's) |
| Disponibilidade para pedidos de leitura | Pelo menos 99,9% (99% para o nível de acesso fresco) | Pelo menos 99,9% (99% para o nível de acesso fresco) | Pelo menos 99,9% (99% para nível de acesso fresco) para GRS<br /><br />Pelo menos 99,99% (99,9% para o nível de acesso cool) para RA-GRS | Pelo menos 99,9% (99% para nível de acesso fresco) para GZRS<br /><br />Pelo menos 99,99% (99,9% para o nível de acesso cool) para RA-GZRS |
| Disponibilidade para pedidos de escrita | Pelo menos 99,9% (99% para o nível de acesso fresco) | Pelo menos 99,9% (99% para o nível de acesso fresco) | Pelo menos 99,9% (99% para o nível de acesso fresco) | Pelo menos 99,9% (99% para o nível de acesso fresco) |
| Número de cópias de dados mantidos em nós separados | Três cópias dentro de uma única região | Três cópias em zonas de disponibilidade separadas dentro de uma única região | Total de seis exemplares, incluindo três na região primária e três na região secundária | Seis exemplares no total, incluindo três em zonas de disponibilidade separadas na região primária e três cópias redundantes locais na região secundária |

### <a name="durability-and-availability-by-outage-scenario"></a>Durabilidade e disponibilidade por cenário de paralisação

A tabela a seguir indica se os seus dados são duráveis e disponíveis num determinado cenário, dependendo do tipo de redundância em vigor para a sua conta de armazenamento:

| Cenário de paralisação | LRS | ZRS | GRS/RA-GRS | GZRS/RA-GZRS |
|:-|:-|:-|:-|:-|
| Um nó dentro de um centro de dados torna-se indisponível | Yes | Yes | Yes | Yes |
| Um centro de dados inteiro (zonal ou não-zonal) torna-se indisponível | No | Yes | Sim<sup>1</sup> | Yes |
| Uma paralisação em toda a região ocorre na região primária | No | No | Sim<sup>1</sup> | Sim<sup>1</sup> |
| Leia o acesso à região secundária disponível se a região primária ficar indisponível | No | No | Sim (com RA-GRS) | Sim (com RA-GZRS) |

<sup>1</sup> O failover da conta é necessário para restaurar a disponibilidade de escrita se a região primária ficar indisponível. Para obter mais informações, consulte [a recuperação de desastres e a falha da conta de armazenamento.](storage-disaster-recovery-guidance.md)

### <a name="supported-azure-storage-services"></a>Serviços de armazenamento suportados da Azure

A tabela que se segue mostra quais as opções de despedimento suportadas por cada serviço de Armazenamento Azure.

| LRS | ZRS | GRS/RA-GRS | GZRS/RA-GZRS |
|:-|:-|:-|:-|
| Armazenamento de blobs<br />Armazenamento de filas<br />Table Storage<br />Ficheiros do Azure<br />Discos geridos Azure | Armazenamento de blobs<br />Armazenamento de filas<br />Table Storage<br />Ficheiros do Azure | Armazenamento de blobs<br />Armazenamento de filas<br />Table Storage<br />Ficheiros do Azure<br /> | Armazenamento de blobs<br />Armazenamento de filas<br />Table Storage<br />Ficheiros do Azure<br /> |

### <a name="supported-storage-account-types"></a>Tipos de conta de armazenamento suportados

O quadro seguinte mostra quais as opções de despedimento suportadas por cada tipo de conta de armazenamento. Para obter informações sobre tipos de conta de armazenamento, consulte [a visão geral da conta de armazenamento](storage-account-overview.md).

| LRS | ZRS | GRS/RA-GRS | GZRS/RA-GZRS |
|:-|:-|:-|:-|
| Fins gerais v2<br /> Fins gerais v1<br /> BlockBlobStorage<br /> BlobStorage<br /> Arquitorage de arquivos | Fins gerais v2<br /> BlockBlobStorage<br /> Arquitorage de arquivos | Fins gerais v2<br /> Fins gerais v1<br /> BlobStorage | Fins gerais v2 |

Todos os dados relativos a todas as contas de armazenamento são copiados de acordo com a opção de despedimento para a conta de armazenamento. Objetos que incluem bolhas de blocos, bolhas de apêndice, bolhas de página, filas, tabelas e ficheiros são copiados. Os dados em todos os níveis, incluindo o nível de arquivo, são copiados. Para obter mais informações sobre os níveis de blob, consulte [o armazenamento Azure Blob: níveis de acesso quentes, frescos e de arquivo](../blobs/storage-blob-storage-tiers.md).

Para obter informações sobre preços para cada opção de redundância, consulte [os preços de armazenamento Azure](https://azure.microsoft.com/pricing/details/storage/).

> [!NOTE]
> A Azure Premium Disk Storage suporta atualmente apenas armazenamento localmente redundante (LRS). As contas de armazenamento de blob bloqueiam o armazenamento localmente redundante (LRS) e o armazenamento redundante de zona (ZRS) em certas regiões.

## <a name="data-integrity"></a>Integridade dos dados

O Azure Storage verifica regularmente a integridade dos dados armazenados através de verificações cíclicas de redundância (CRCs). Se for detetada corrupção de dados, é reparada com dados redundantes. O Azure Storage também calcula os dados de verificação em todo o tráfego de rede para detetar a corrupção de pacotes de dados ao armazenar ou recuperar dados.

## <a name="see-also"></a>Ver também

- [Verifique a propriedade da Última Hora do Sincronização para obter uma conta de armazenamento](last-sync-time-get.md)
- [Alterar a opção de despedimento para uma conta de armazenamento](redundancy-migration.md)
- [Use geo-redundância para projetar aplicações altamente disponíveis](geo-redundant-design.md)
- [Recuperação após desastre e ativação pós-falha de contas de armazenamento](storage-disaster-recovery-guidance.md)
