---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 93f4f74d435cc14130668da102d1246c5fad5872
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2019
ms.locfileid: "67184657"
---
| Identificador de limite | Limite | Comentários |
| --- | --- | --- |
| Número máximo de credenciais da conta de armazenamento |64 | |
| Número máximo de contêineres de volume |64 | |
| Número máximo de volumes |255 | |
| Número máximo de agendas por modelo de largura de banda |168 |Uma agenda para cada hora, todos os dias da semana. |
| Tamanho máximo de um volume em camadas em dispositivos físicos |64 TB para StorSimple 8100 e StorSimple 8600 |O StorSimple 8100 e o StorSimple 8600 são dispositivos físicos. |
| Tamanho máximo de um volume em camadas em dispositivos virtuais no Azure |30 TB para o StorSimple 8010 <br></br> 64 TB para StorSimple 8020 |O StorSimple 8010 e o StorSimple 8020 são dispositivos virtuais no Azure que usam armazenamento Standard e armazenamento Premium, respectivamente. |
| Tamanho máximo de um volume fixado localmente em dispositivos físicos |9 TB para o StorSimple 8100 <br></br> 24 TB para o StorSimple 8600 |O StorSimple 8100 e o StorSimple 8600 são dispositivos físicos. |
| Número máximo de conexões iSCSI |512 | |
| Número máximo de conexões iSCSI de iniciadores |512 | |
| Número máximo de registros de controle de acesso por dispositivo |64 | |
| Número máximo de volumes por política de backup |24 | |
| Número máximo de backups retidos por política de backup |64 | |
| Número máximo de agendas por política de backup |10 | |
| Número máximo de instantâneos de qualquer tipo que podem ser retidos por volume |256 |Esse valor inclui instantâneos locais e instantâneos de nuvem. |
| Número máximo de instantâneos que podem estar presentes em qualquer dispositivo |10,000 | |
| Número máximo de volumes que podem ser processados em paralelo para backup, restauração ou clonagem |16 |<ul><li>Se houver mais de 16 volumes, eles serão processados sequencialmente conforme os slots de processamento se tornarem disponíveis.</li><li>Novos backups de um volume clonado ou restaurado em camadas não podem ocorrer até que a operação seja concluída. Para um volume local, os backups são permitidos depois que o volume está online.</li></ul> |
| Restauração e clonagem do tempo de recuperação para volumes em camadas |< 2 minutos |<ul><li>O volume é disponibilizado em 2 minutos após uma operação de restauração ou de clonagem, independentemente do tamanho do volume.</li><li>O desempenho do volume inicialmente pode ser mais lento do que o normal, pois a maioria dos dados e metadados ainda residem na nuvem. O desempenho pode aumentar conforme os fluxos de dados da nuvem para o dispositivo StorSimple.</li><li>O tempo total para baixar metadados depende do tamanho do volume alocado. Os metadados são automaticamente colocados no dispositivo em segundo plano na taxa de 5 minutos por TB de dados de volume alocados. Essa taxa pode ser afetada pela largura de banda da Internet para a nuvem.</li><li>A operação de restauração ou clonagem é concluída quando todos os metadados estão no dispositivo.</li><li>As operações de backup não podem ser executadas até que a operação de restauração ou clonagem esteja totalmente concluída. |
| Tempo de recuperação de restauração para volumes fixados localmente |< 2 minutos |<ul><li>O volume é disponibilizado dentro de 2 minutos da operação de restauração, independentemente do tamanho do volume.</li><li>O desempenho do volume inicialmente pode ser mais lento do que o normal, pois a maioria dos dados e metadados ainda residem na nuvem. O desempenho pode aumentar conforme os fluxos de dados da nuvem para o dispositivo StorSimple.</li><li>O tempo total para baixar metadados depende do tamanho do volume alocado. Os metadados são automaticamente colocados no dispositivo em segundo plano na taxa de 5 minutos por TB de dados de volume alocados. Essa taxa pode ser afetada pela largura de banda da Internet para a nuvem.</li><li>Ao contrário dos volumes em camadas, se houver volumes fixados localmente, os dados do volume também serão baixados localmente no dispositivo. A operação de restauração é concluída quando todos os dados de volume foram trazidos para o dispositivo.</li><li>As operações de restauração podem ser longas e o tempo total para concluir a restauração dependerá do tamanho do volume local provisionado, da largura de banda da Internet e dos dados existentes no dispositivo. As operações de backup no volume fixado localmente são permitidas enquanto a operação de restauração está em andamento. |
| Disponibilidade de restauração fina |Último failover | |
| Taxa de transferência máxima de leitura/gravação do cliente, quando servida da camada SSD * |920/720 MB/s com uma única interface de rede Ethernet de 10 gigabits |Até duas vezes com o MPIO e duas interfaces de rede. |
| Taxa de transferência máxima de leitura/gravação do cliente, quando servida da camada de HDD * |120/250 MB/s | |
| Taxa de transferência máxima de leitura/gravação do cliente, quando servida da camada de nuvem * |11/41 MB/s |A taxa de transferência de leitura depende de clientes gerando e mantendo profundidade de fila de e/s suficiente. |

&#42;A taxa de transferência máxima por tipo de e/s foi medida com 100% de leitura e 100% de cenários de gravação. A taxa de transferência real pode ser menor e depende da combinação de e/s e das condições de rede.

