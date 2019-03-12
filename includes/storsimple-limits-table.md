---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 93f4f74d435cc14130668da102d1246c5fad5872
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553826"
---
| Identificador de limite | Limite | Comentários |
| --- | --- | --- |
| Número máximo de credenciais da conta de armazenamento |64 | |
| Número máximo de contentores de volumes |64 | |
| Número máximo de volumes |255 | |
| Número máximo de agendas por modelo de largura de banda |168 |Uma agenda para cada hora, todos os dias da semana. |
| Tamanho máximo de um volume em camadas em dispositivos físicos |64 TB para o StorSimple 8100 e StorSimple 8600 |StorSimple 8100 e StorSimple 8600 são dispositivos físicos. |
| Tamanho máximo de um volume em camadas sobre dispositivos virtuais no Azure |30 TB para o StorSimple 8010 <br></br> 64 TB para o StorSimple 8020 |O StorSimple 8010 e StorSimple 8020 são dispositivos virtuais no Azure que utilizam o armazenamento Standard e o armazenamento Premium, respectivamente. |
| Tamanho máximo de um volume localmente afixado em dispositivos físicos |9 TB para o StorSimple 8100 <br></br> 24 TB para o StorSimple 8600 |StorSimple 8100 e StorSimple 8600 são dispositivos físicos. |
| Número máximo de ligações de iSCSI |512 | |
| Número máximo de ligações de iSCSI de iniciadores |512 | |
| Número máximo de registos de controlo de acesso por dispositivo |64 | |
| Número máximo de volumes de acordo com a política de cópia de segurança |24 | |
| Número máximo de cópias de segurança retidas de acordo com a política de cópia de segurança |64 | |
| Número máximo de agendas de acordo com a política de cópia de segurança |10 | |
| Número máximo de instantâneos de qualquer tipo que podem ser mantidos por volume |256 |Este valor inclui os instantâneos locais e instantâneos da cloud. |
| Número máximo de instantâneos que podem estar presentes em qualquer dispositivo |10,000 | |
| Número máximo de volumes que podem ser processados em paralelo para cópia de segurança, restauro ou clone |16 |<ul><li>Se existirem mais de 16 volumes, eles são processados sequencialmente como blocos de processamento de se tornarem disponíveis.</li><li>Novas cópias de segurança de um clonado ou um volume em camadas restaurado não pode ocorrer até que a operação esteja concluída. Para um volume local, as cópias de segurança são permitidas depois do volume está online.</li></ul> |
| Restaurar e clonar o tempo de recuperação de volumes em camadas |< 2 minutos |<ul><li>O volume é disponibilizado em dois minutos de uma operação de clonagem, independentemente do tamanho do volume ou o restauro.</li><li>O desempenho de volume inicialmente pode ser mais lento do que o normal, como a maioria dos dados e metadados ainda reside na cloud. Desempenho poderá aumentar como fluxos de dados a partir da cloud para o dispositivo StorSimple.</li><li>O tempo total para transferir os metadados depende do tamanho do volume alocado. Metadados automaticamente é colocado no dispositivo em segundo plano numa taxa de cinco minutos por TB de dados de volume alocado. Essa taxa poderão ser afetada pela largura de banda de Internet para a cloud.</li><li>O restauro ou a operação de clonagem está concluída quando todos os metadados são no dispositivo.</li><li>Operações de cópia de segurança não não possível efetuar até o restauro ou operação de clonagem é totalmente concluída. |
| Restaurar o tempo de recuperação para volumes afixados localmente |< 2 minutos |<ul><li>O volume é disponibilizado em dois minutos da operação de restauro, independentemente do tamanho do volume.</li><li>O desempenho de volume inicialmente pode ser mais lento do que o normal, como a maioria dos dados e metadados ainda reside na cloud. Desempenho poderá aumentar como fluxos de dados a partir da cloud para o dispositivo StorSimple.</li><li>O tempo total para transferir os metadados depende do tamanho do volume alocado. Metadados automaticamente é colocado no dispositivo em segundo plano numa taxa de cinco minutos por TB de dados de volume alocado. Essa taxa poderão ser afetada pela largura de banda de Internet para a cloud.</li><li>Ao contrário dos volumes em camadas, se existirem volumes afixados localmente, o volume de dados também é transferido localmente no dispositivo. A operação de restauro está concluída quando todos os dados de volume tenha sido colocada no dispositivo.</li><li>As operações de restauro poderão ser longo e o tempo total para concluir o restauro dependerá do tamanho do volume aprovisionado local, a largura de banda de Internet e os dados existentes no dispositivo. Operações de cópia de segurança no volume afixado localmente são permitidas enquanto a operação de restauro está em curso. |
| Disponibilidade de restauro anterior |Última ativação pós-falha | |
| Débito de leitura/escrita de máximo de clientes, quando atendidos a partir da camada SSD * |920/720 MB/seg, com uma única interface de rede de Ethernet de 10 gigabit |Até duas vezes com MPIO e duas interfaces de rede. |
| Débito de leitura/escrita de máximo de clientes, quando atendidos a partir da camada HDD * |120/250 MB/sec | |
| Débito de leitura/escrita de máximo de clientes, quando atendidos a partir da camada de cloud * |11/41 MB/seg |Débito de leitura depende dos clientes a gerar e a manutenção de profundidade de fila de e/s suficiente. |

&#42;Débito máximo por tipo de e/s foi medido com a leitura de 100 por cento e cenários de escrita de 100 por cento. Débito real pode ser mais baixo e depende de e/s misturar e condições de rede.

