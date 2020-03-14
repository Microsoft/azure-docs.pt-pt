---
title: Redundância de dados
titleSuffix: Azure Storage
description: Os dados na sua conta de Armazenamento Microsoft Azure são replicados para durabilidade e elevada disponibilidade. As configurações de despedimento incluem armazenamento localmente redundante (LRS), armazenamento redundante em zona (ZRS), armazenamento geo-redundante (GRS), armazenamento geo-redundante de acesso de leitura (RA-GRS), armazenamento geo-zona redundante (GZRS) (pré-visualização) e acesso à leitura armazenamento geo-zona-redundante (RA-GZRS) (pré-visualização).
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 7ae5f59a1bd96362d5466b2f6363185ba168d942
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79255265"
---
# <a name="azure-storage-redundancy"></a>Redundância de armazenamento azure

O Azure Storage armazena sempre várias cópias dos seus dados para que esteja protegido contra eventos planeados e não planeados, incluindo falhas de hardware transitórios, falhas de rede ou de energia e desastres naturais maciços. O despedimento garante que a sua conta de armazenamento cumpre o [Acordo de Nível de Serviço (SLA) para o Armazenamento Azure](https://azure.microsoft.com/support/legal/sla/storage/) mesmo face a falhas.

Ao decidir qual a opção de despedimento que é melhor para o seu cenário, considere as trocas entre custos mais baixos e maior disponibilidade e durabilidade. Os fatores que ajudam a determinar que opção de despedimento deve escolher incluem:  

- Como os seus dados são replicados na região primária
- Se os seus dados são replicados para uma segunda localização geograficamente distante da região primária, para proteger contra desastres regionais
- Se a sua aplicação requer acesso lido aos dados replicados na região secundária se a região primária ficar indisponível por qualquer motivo

## <a name="redundancy-in-the-primary-region"></a>Redundância na região primária

Os dados de uma conta de Armazenamento Azure são sempre replicados três vezes na região primária. O Azure Storage oferece duas opções para a forma como os seus dados são replicados na região primária:

- **O armazenamento redundante localmente (LRS)** copia os seus dados sincronizadamente três vezes dentro de uma única localização física na região primária. O LRS é a opção de replicação menos dispendiosa, mas não é recomendado para aplicações que requerem elevada disponibilidade.
- O **armazenamento redundante em zonas (ZRS)** copia os seus dados sincronizadamente em três zonas de disponibilidade Azure na região primária. Para aplicações que requerem elevada disponibilidade, a Microsoft recomenda a utilização de ZRS na região primária e também a replicação para uma região secundária.

### <a name="locally-redundant-storage"></a>Armazenamento localmente redundante

O armazenamento redundante localmente (LRS) replica os seus dados três vezes dentro de uma única localização física na região primária. O LRS proporciona pelo menos 99.999999999% (11 noves) durabilidade de objetos durante um determinado ano.

O LRS é a opção de redundância mais barata e oferece a menor durabilidade em comparação com outras opções. O LRS protege os seus dados contra falhas na cremalheira do servidor e nas falhas de condução. No entanto, se ocorrer um desastre como incêndio ou inundação dentro do centro de dados, todas as réplicas de uma conta de armazenamento utilizando LRS podem ser perdidas ou irrecuperáveis. Para mitigar este risco, a Microsoft recomenda a utilização de [armazenamento redundante](#zone-redundant-storage) em zonas (ZRS), [armazenamento geo-redundante](#geo-redundant-storage) (GRS) ou [armazenamento redundante geo-zona (pré-visualização)](#geo-zone-redundant-storage-preview) (GZRS).

Um pedido de escrita para uma conta de armazenamento que está a usar LRS acontece sincronizadamente. A operação de escrita regressa com sucesso apenas depois de os dados terem sido escritos às três réplicas.

LRS é uma boa escolha para os seguintes cenários:

- Se a sua aplicação armazenar dados que podem ser facilmente reconstruídos se ocorrer perda de dados, poderá optar por LRS.
- Se a sua aplicação estiver restrita à replicação de dados apenas dentro de um país ou região devido aos requisitos de governação de dados, poderá optar por LRS. Em alguns casos, as regiões emparelhadas em que os dados são geo-replicados podem estar noutro país ou região. Para obter mais informações sobre regiões emparelhadas, consulte [as regiões de Azure.](https://azure.microsoft.com/regions/)

### <a name="zone-redundant-storage"></a>Armazenamento com redundância entre zonas

O armazenamento redundante em zona (ZRS) replica os seus dados de Armazenamento Azure sincronizadamente em três zonas de disponibilidade Azure na região primária. Cada zona de disponibilidade é uma localização física separada com potência independente, arrefecimento e networking. A ZRS oferece durabilidade para objetos de dados de armazenamento azure de pelo menos 99.99999999999% (12 9's) durante um determinado ano.

Com o ZRS, os seus dados ainda estão acessíveis para operações de leitura e escrita, mesmo que uma zona fique indisponível. Se uma zona ficar indisponível, o Azure realiza atualizações de rede, como a recolocação de DNS. Estas atualizações podem afetar a sua aplicação se aceder a dados antes de as atualizações terem sido concluídas. Ao conceber aplicações para zRS, siga as práticas de manipulação transitória de falhas, incluindo a implementação de políticas de retry com back-off exponencial.

Um pedido de escrita para uma conta de armazenamento que está a usar ZRS acontece sincronizadamente. A operação de escrita regressa com sucesso apenas depois de os dados forem escritos a todas as réplicas nas três zonas de disponibilidade.

A Microsoft recomenda a utilização de ZRS na região primária para cenários que requerem consistência, durabilidade e elevada disponibilidade. O ZRS proporciona um excelente desempenho, baixa latência e resiliência para os seus dados se ficar temporariamente indisponível. No entanto, o ZRS por si só não pode proteger os seus dados contra uma catástrofe regional onde várias zonas são permanentemente afetadas. Para proteção contra desastres regionais, a Microsoft recomenda a utilização de [armazenamento geo-zona-redundante](#geo-zone-redundant-storage-preview) (GZRS), que utiliza ZRS na região primária e também geo-replica os seus dados para uma região secundária.

O quadro seguinte mostra quais os tipos de contas de armazenamento que suportam o ZRS em que as regiões:

|    Tipo de conta de armazenamento    |    Regiões suportadas    |    Serviços suportados    |
|----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------|
|    Geral v2<sup>1</sup>    | Sudeste Asiático<br /> Leste da Austrália<br /> Norte da Europa<br />  Europa Ocidental<br /> França Central<br /> Leste do Japão<br /> África do Sul Norte<br /> Sul do Reino Unido<br /> E.U.A. Central<br /> E.U.A Leste<br /> E.U.A. Leste 2<br /> E.U.A. Oeste 2    |    Blobs de bloco<br /> Bolhas de página<sup>2</sup><br /> Ações de ficheiros (standard)<br /> Tabelas<br /> Filas<br /> |
|    BlockBlobStorage<sup>1</sup>    | Europa Ocidental<br /> E.U.A Leste    |    Bloco somente bolhas    |
|    FileStorage    | Europa Ocidental<br /> E.U.A Leste    |    Apenas ficheiros Azure    |

<sup>1</sup> O nível de arquivo não é atualmente suportado para contas ZRS.<br />
<sup>2</sup> As contas de armazenamento que contêm discos geridos pelo Azure para máquinas virtuais usam sempre LRS. Os discos não geridos azure também devem utilizar LRS. É possível criar uma conta de armazenamento para discos não geridos Azure que usam GRS, mas não é recomendado devido a potenciais problemas com consistência sobre geo-replicação assíncrona. Nem discos geridos nem não geridos suportam ZRS ou GZRS. Para obter mais informações sobre discos geridos, consulte [Preços para discos geridos](https://azure.microsoft.com/pricing/details/managed-disks/)pelo Azure .

Para obter informações sobre quais as regiões que suportam o ZRS, consulte **o suporte de serviços por região** em [Que São As Zonas de Disponibilidade do Azure?](../../availability-zones/az-overview.md)

## <a name="redundancy-in-a-secondary-region"></a>Redundância numa região secundária

Para aplicações que requerem elevada disponibilidade, pode optar por copiar adicionalmente os dados da sua conta de armazenamento para uma região secundária que fica a centenas de quilómetros da região primária. Se a sua conta de armazenamento for copiada para uma região secundária, então os seus dados são duráveis mesmo no caso de uma paragem regional completa ou de um desastre em que a região primária não é recuperável.

Quando cria uma conta de armazenamento, seleciona a região primária para a conta. A região secundária emparelhada é determinada com base na região primária, e não pode ser alterada. Para obter mais informações sobre as regiões apoiadas pelo Azure, consulte [as regiões de Azure.](https://azure.microsoft.com/global-infrastructure/regions/)

O Azure Storage oferece duas opções para copiar os seus dados para uma região secundária:

- **O armazenamento geo-redundante (GRS)** copia os seus dados sincronizadamente três vezes dentro de uma única localização física na região primária usando LRS. Em seguida, copia os seus dados assincronicamente para uma única localização física na região secundária.
- **O armazenamento geo-zona redundante (GZRS)** (pré-visualização) copia os seus dados sincronizadamente em três zonas de disponibilidade azure na região primária utilizando ZRS. Em seguida, copia os seus dados assincronicamente para uma única localização física na região secundária.

A principal diferença entre GRS e GZRS é a forma como os dados são replicados na região primária. Dentro da localização secundária, os dados são sempre replicados sincronizadamente três vezes usando LRS.

Com GRS ou GZRS, os dados no local secundário não estão disponíveis para leitura ou acesso de escrita, a menos que haja uma falha na região secundária. Para ler o acesso à localização secundária, configure a sua conta de armazenamento para utilizar armazenamento geo-redundante de acesso de leitura (RA-GRS) ou armazenamento geo-zona-redundante de acesso à leitura (RA-GZRS). Para mais informações, consulte [Ler acesso aos dados da região secundária](#read-access-to-data-in-the-secondary-region).

Se a região primária ficar indisponível, pode optar por falhar na região secundária (pré-visualização). Após a falha ter terminado, a região secundária torna-se a região primária, e pode ler e escrever dados novamente. Para obter mais informações sobre a recuperação de desastres e aprender a falhar na região secundária, consulte a recuperação de desastres e a falha da [conta (pré-visualização)](storage-disaster-recovery-guidance.md).

> [!IMPORTANT]
> Uma vez que os dados são replicados para a região secundária assincronicamente, uma falha que afeta a região primária pode resultar em perda de dados se a região primária não puder ser recuperada. O intervalo entre as mais recentes escritas para a região primária e a última escrita para a região secundária é conhecido como o objetivo do ponto de recuperação (RPO). O RPO indica o ponto no tempo para o qual os dados podem ser recuperados. O Armazenamento Azure normalmente tem um RPO de menos de 15 minutos, embora não exista atualmente SLA sobre o tempo que leva para replicar dados para a região secundária.

### <a name="geo-redundant-storage"></a>Armazenamento georredundante

O armazenamento geo-redundante (GRS) copia os seus dados sincronizadamente três vezes dentro de uma única localização física na região primária usando LRS. Em seguida, copia os seus dados sincronicamente para um único local físico numa região secundária que fica a centenas de milhas da região primária. GrS oferece durabilidade para objetos de dados de armazenamento azure de pelo menos 99.99999999999999% (16 9's) durante um determinado ano.

Uma operação de escrita é inicialmente comprometida com a localização primária e replicada usando LRS. A atualização é então replicada assincronicamente para a região secundária. Quando os dados são escritos para a localização secundária, também é replicado dentro desse local usando LRS.

### <a name="geo-zone-redundant-storage-preview"></a>Armazenamento geo-zona redundante (pré-visualização)

O armazenamento geo-zona-redundante (GZRS) (pré-visualização) combina a elevada disponibilidade proporcionada pelo despedimento em zonas de disponibilidade com proteção contra interrupções regionais fornecidas por geo-replicação. Os dados de uma conta de armazenamento GZRS são copiados em três zonas de [disponibilidade do Azure](../../availability-zones/az-overview.md) na região primária e também são replicados para uma região geográfica secundária para proteção contra desastres regionais. A Microsoft recomenda a utilização de GZRS para aplicações que requerem a máxima consistência, durabilidade e disponibilidade, excelente desempenho e resiliência para a recuperação de desastres.

Com uma conta de armazenamento GZRS, pode continuar a ler e escrever dados se uma zona de disponibilidade ficar indisponível ou não for recuperável. Além disso, os seus dados também são duráveis no caso de uma paragem regional completa ou de um desastre em que a região primária não é recuperável. A GZRS foi concebida para fornecer pelo menos 99.9999999999999999999999999% (16 9's) durabilidade de objetos durante um determinado ano.

Apenas as contas de armazenamento v2 de uso geral suportam GZRS e RA-GZRS. Para obter mais informações sobre os tipos de conta de armazenamento, consulte a visão geral da conta de [armazenamento do Azure.](storage-account-overview.md) Bolhas de bloco de suporte GZRS e RA-GZRS, bolhas de página (com exceção dos discos VHD), ficheiros, tabelas e filas.

GZRS e RA-GZRS estão atualmente disponíveis para pré-visualização nas seguintes regiões:

- Sudeste Asiático
- Norte da Europa
- Europa Ocidental
- Leste do Japão
- Sul do Reino Unido
- E.U.A Leste
- E.U.A. Leste 2
- E.U.A. Central
- E.U.A. Oeste 2

A Microsoft continua a permitir gZRS e RA-GZRS em regiões azure adicionais. Consulte regularmente a página de Atualizações de [Serviços Azure](https://azure.microsoft.com/updates/) para obter informações sobre regiões apoiadas.

Para obter informações sobre os preços de pré-visualização, consulte os preços de pré-visualização da GZRS para [Blobs,](https://azure.microsoft.com/pricing/details/storage/blobs) [Files,](https://azure.microsoft.com/pricing/details/storage/files/)Filas e [Tabelas](https://azure.microsoft.com/pricing/details/storage/queues/). [](https://azure.microsoft.com/pricing/details/storage/tables/)

> [!IMPORTANT]
> A Microsoft recomenda não utilizar funcionalidades de pré-visualização para cargas de trabalho de produção.

## <a name="read-access-to-data-in-the-secondary-region"></a>Ler acesso aos dados na região secundária

O armazenamento geo-redundante (com GRS ou GZRS) replica os seus dados para outro local físico na região secundária para proteger contra interrupções regionais. No entanto, esses dados só estão disponíveis para serem lidos se o cliente ou a Microsoft iniciarem uma falha da região primária para a região secundária. Quando permite ler o acesso à região secundária, os seus dados estão disponíveis para serem lidos se a região primária ficar indisponível. Para ler o acesso à região secundária, permita o armazenamento geo-redundante de acesso à leitura (RA-GRS) ou o armazenamento geo-zona-redundante de acesso à leitura (RA-GZRS).

### <a name="design-your-applications-for-read-access-to-the-secondary"></a>Desenhe as suas aplicações para ler o acesso ao secundário

Se a sua conta de armazenamento estiver configurada para ler o acesso à região secundária, então pode projetar as suas aplicações para mudar perfeitamente para dados de leitura da região secundária se a região primária ficar indisponível por qualquer motivo. A região secundária está sempre disponível para acesso à leitura, para que possa testar a sua aplicação para se certificar de que será lida a partir do secundário em caso de paragem. Para obter mais informações sobre como projetar as suas aplicações para alta disponibilidade, consulte A conceção de [aplicações altamente disponíveis utilizando armazenamento geo-redundante de acesso de leitura.](storage-designing-ha-apps-with-ragrs.md)

Ao ler o acesso ao secundário está ativado, os seus dados podem ser lidos a partir do ponto final secundário, bem como a partir do ponto final primário para a sua conta de armazenamento. O ponto final secundário afixa o sufixo *– secundário* ao nome da conta. Por exemplo, se o seu ponto final primário para o armazenamento blob for `myaccount.blob.core.windows.net`, então o ponto final secundário é `myaccount-secondary.blob.core.windows.net`. As chaves de acesso à conta para a sua conta de armazenamento são as mesmas para os pontos finais primários e secundários.

### <a name="check-the-last-sync-time-property"></a>Consulte a propriedade Last Sync Time

Uma vez que os dados são replicados para a região secundária assincronicamente, a região secundária está muitas vezes atrás da região primária. Se um fracasso acontecer na região primária, é provável que todos os escritos para as primárias ainda não tenham sido replicados para o secundário.

Para determinar quais as operações de escrita que foram replicadas para a região secundária, a sua aplicação pode verificar a propriedade **Do Último Tempo sincronizado** para a sua conta de armazenamento. Todas as operações escritas escritas para a região primária antes do último tempo de sincronização foram replicadas com sucesso para a região secundária, o que significa que estão disponíveis para serem lidas a partir do secundário. Quaisquer operações escritas escritas escritas para a região primária após o último tempo de sincronização podem ou não ter sido replicadas para a região secundária, o que significa que podem não estar disponíveis para operações de leitura.

Pode consultar o valor da propriedade **Last Sync Time** utilizando o Azure PowerShell, o Azure CLI ou uma das bibliotecas de clientes do Azure Storage. A propriedade **Last Sync Time** é um valor GMT data/hora. Para mais informações, [consulte a propriedade Last Sync Time para obter uma conta](last-sync-time-get.md)de armazenamento .

## <a name="summary-of-redundancy-options"></a>Resumo das opções de despedimento

O quadro seguinte mostra o quão duradouros e disponíveis os seus dados estão num dado cenário, dependendo do tipo de redundância que está em vigor para a sua conta de armazenamento:

| Cenário                                                                                                 | LRS                             | ZRS                              | GRS/RA-GRS                                  | GZRS/RA-GZrs (pré-visualização)                              |
| :------------------------------------------------------------------------------------------------------- | :------------------------------ | :------------------------------- | :----------------------------------- | :----------------------------------- |
| Um nó dentro de um centro de dados torna-se indisponível                                                                 | Sim                             | Sim                              | Sim                                  | Sim                                  |
| Um centro de dados inteiro (zonal ou não zonal) fica indisponível                                           | Não                              | Sim                              | Sim                                  | Sim                                  |
| Ocorre uma paralisação em toda a região                                                                                     | Não                              | Não                               | Sim                                  | Sim                                  |
| Leia o acesso aos dados na região secundária se a região primária ficar indisponível | Não                              | Não                               | Sim (com RA-GRS)                                   | Sim (com RA-GZRS)                                 |
| Por cento de durabilidade de objetos ao longo de um dado ano<sup>1</sup>                                          | pelo menos 99.999999999% (11 9's) | pelo menos 99.99999999999% (12 9's) | pelo menos 99.9999999999999999999 % (16 9's) | pelo menos 99.9999999999999999999 % (16 9's) |
| Conta de armazenamento suportada tipos<sup>2</sup>                                                                   | GPv2, GPv1, BlockBlobStorage, BlobStorage, FileStorage                | GPv2, BlockBlobStorage, FileStorage                             | GPv2, GPv1, BlobStorage                     | GPv2                     |
| Disponibilidade SLA para pedidos de leitura<sup>1</sup>  | Pelo menos 99,9% (99% para o nível de acesso cool) | Pelo menos 99,9% (99% para o nível de acesso cool) | Pelo menos 99,9% (99% para o nível de acesso cool) para GRS<br /><br />Pelo menos 99,99% (99,9% para o nível de acesso cool) para RA-GRS | Pelo menos 99,9% (99% para o nível de acesso cool) para GZRS<br /><br />Pelo menos 99,99% (99,9% para o nível de acesso cool) para RA-GZRS |
| Disponibilidade SLA para pedidos de escrita<sup>1</sup>  | Pelo menos 99,9% (99% para o nível de acesso cool) | Pelo menos 99,9% (99% para o nível de acesso cool) | Pelo menos 99,9% (99% para o nível de acesso cool) | Pelo menos 99,9% (99% para o nível de acesso cool) |

<sup>1</sup> Para obter informações sobre as garantias de armazenamento do Azure para durabilidade e disponibilidade, consulte o [Azure Storage SLA](https://azure.microsoft.com/support/legal/sla/storage/).

<sup>2</sup> Para obter informações sobre os tipos de conta de armazenamento, consulte a [visão geral da conta](storage-account-overview.md)de armazenamento .

Todos os dados relativos a todos os tipos de contas de armazenamento são copiados de acordo com a opção de despedimento para a conta de armazenamento. Objetos incluindo bolhas de blocos, bolhas de apêndice, bolhas de página, filas, mesas e ficheiros são copiados.

Para obter informações sobre preços para cada opção de despedimento, consulte o preço do [Armazenamento Azure](https://azure.microsoft.com/pricing/details/storage/).

> [!NOTE]
> O Armazenamento de Discos Premium Azure suporta atualmente apenas armazenamento redundante localmente (LRS). As contas de armazenamento de blocos blob suportam armazenamento localmente redundante (LRS) e armazenamento redundante de zona (ZRS) em certas regiões.

## <a name="data-integrity"></a>Integridade dos dados

O Armazenamento Azure verifica regularmente a integridade dos dados armazenados com controlos cíclicos de despedimento (CRCs). Se for detetada corrupção de dados, é reparado utilizando dados redundantes. O Azure Storage também calcula verificações em todo o tráfego da rede para detetar corrupção de pacotes de dados ao armazenar ou recuperar dados.

## <a name="see-also"></a>Consulte também

- [Verifique a propriedade Do Último Tempo sincronizado para obter uma conta de armazenamento](last-sync-time-get.md)
- [Alterar a opção de despedimento para uma conta de armazenamento](redundancy-migration.md)
- [Conceber aplicações altamente disponíveis utilizando o Armazenamento RA-GRS](../storage-designing-ha-apps-with-ragrs.md)
- [Recuperação de desastres e falha na conta (pré-visualização)](storage-disaster-recovery-guidance.md)
