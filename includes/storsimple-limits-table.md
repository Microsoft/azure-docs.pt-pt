---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 93f4f74d435cc14130668da102d1246c5fad5872
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "67184657"
---
| Identificador de limite | Limite | Comentários |
| --- | --- | --- |
| Número máximo de credenciais de conta de armazenamento |64 | |
| Número máximo de contentores de volume |64 | |
| Número máximo de volumes |255 | |
| Número máximo de horários por modelo de largura de banda |168 |Um horário para cada hora, todos os dias da semana. |
| Tamanho máximo de um volume hierárquico em dispositivos físicos |64 TB para StorSimple 8100 e StorSimple 8600 |StorSimple 8100 e StorSimple 8600 são dispositivos físicos. |
| Tamanho máximo de um volume hierárquico em dispositivos virtuais em Azure |30 TB para StorSimple 8010 <br></br> 64 TB para StorSimple 8020 |StorSimple 8010 e StorSimple 8020 são dispositivos virtuais em Azure que utilizam armazenamento Standard e Armazenamento Premium, respectivamente. |
| Tamanho máximo de um volume fixado localmente em dispositivos físicos |9 TB para StorSimple 8100 <br></br> 24 TB para StorSimple 8600 |StorSimple 8100 e StorSimple 8600 são dispositivos físicos. |
| Número máximo de ligações iSCSI |512 | |
| Número máximo de ligações iSCSI de iniciadores |512 | |
| Número máximo de registos de controlo de acesso por dispositivo |64 | |
| Número máximo de volumes por política de backup |24 | |
| Número máximo de backups retidos por política de backup |64 | |
| Número máximo de horários por política de backup |10 | |
| Número máximo de instantâneos de qualquer tipo que possa ser mantido por volume |256 |Este valor inclui instantâneos locais e instantâneos em nuvem. |
| Número máximo de instantâneos que podem estar presentes em qualquer dispositivo |10,000 | |
| Número máximo de volumes que podem ser processados em paralelo para backup, restauro ou clone |16 |<ul><li>Se houver mais de 16 volumes, são processados sequencialmente à medida que as faixas de processamento ficam disponíveis.</li><li>Não podem ocorrer novas cópias de segurança de um volume clonado ou de um nível restaurado até que a operação esteja concluída. Para um volume local, as cópias de segurança são permitidas depois de o volume estar online.</li></ul> |
| Restaurar e clonar recuperar tempo para volumes hierárquicos |<2 minutos |<ul><li>O volume é disponibilizado no prazo de 2 minutos após uma operação de restauro ou clone, independentemente do tamanho do volume.</li><li>O desempenho do volume pode inicialmente ser mais lento do que o normal, uma vez que a maioria dos dados e metadados ainda residem na nuvem. O desempenho pode aumentar à medida que os dados fluem da nuvem para o dispositivo StorSimple.</li><li>O tempo total para descarregar metadados depende do tamanho do volume atribuído. Os metadados são automaticamente colocados no dispositivo em segundo plano à taxa de 5 minutos por TB dos dados de volume atribuídos. Esta taxa pode ser afetada pela largura de banda da Internet para a nuvem.</li><li>A operação de restauro ou clone está completa quando todos os metadados estiverem no dispositivo.</li><li>As operações de backup não podem ser realizadas até que a operação de restauro ou clone esteja completa. |
| Restaurar o tempo de recuperação para volumes fixados localmente |<2 minutos |<ul><li>O volume é disponibilizado no prazo de 2 minutos após a operação de restauro, independentemente do tamanho do volume.</li><li>O desempenho do volume pode inicialmente ser mais lento do que o normal, uma vez que a maioria dos dados e metadados ainda residem na nuvem. O desempenho pode aumentar à medida que os dados fluem da nuvem para o dispositivo StorSimple.</li><li>O tempo total para descarregar metadados depende do tamanho do volume atribuído. Os metadados são automaticamente colocados no dispositivo em segundo plano à taxa de 5 minutos por TB dos dados de volume atribuídos. Esta taxa pode ser afetada pela largura de banda da Internet para a nuvem.</li><li>Ao contrário dos volumes hierárquicos, se existirem volumes fixados localmente, os dados de volume também são descarregados localmente no dispositivo. A operação de restauro fica completa quando todos os dados de volume foram trazidos para o dispositivo.</li><li>As operações de restauro podem ser longas e o tempo total para concluir a restauração dependerá do tamanho do volume local, da largura de banda da Internet e dos dados existentes no dispositivo. As operações de backup no volume fixado localmente são permitidas enquanto a operação de restauro está em curso. |
| Disponibilidade de restauração fina |Última falha | |
| Produção máxima de leitura/escrita do cliente, quando servido a partir do nível SSD* |920/720 MB/seg com uma interface de rede Ethernet de 10 gigabit |Até duas vezes com MPIO e duas interfaces de rede. |
| Produção máxima de leitura/escrita do cliente, quando servido a partir do nível HDD* |120/250 MB/seg | |
| Produção máxima de leitura/escrita do cliente, quando servido a partir do nível de nuvem* |11/41 MB/seg |A produção de leitura depende dos clientes que geram e mantêm a profundidade de fila de E/O suficiente. |

&#42;produção máxima por tipo de E/S foi medida com 100% de leitura e 100% de cenários de escrita. A produção real pode ser menor e depende da mistura de E/S e das condições de rede.

