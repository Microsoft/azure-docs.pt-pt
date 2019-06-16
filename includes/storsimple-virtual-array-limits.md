---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 658fd9178495f14274c85eab2129c9dcd3be7693
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66143283"
---
| **Identificador de limite** | **Limite** | **Comentários** |
| --- | --- | --- |
| Capacidade total (incluindo na nuvem) |Até 64 TB por dispositivo virtual |Pode efetuar a ativação através de uma matriz Virtual StorSimple completa para outra matriz vazia. Se tentar restaurar para o mesmo dispositivo, certifique-se de que tem espaço suficiente no dispositivo para concluir esta operação. Após ter excedido 32 TB, não é possível restaurar para o mesmo dispositivo. |
| Número máximo de credenciais da conta de armazenamento por dispositivo |1 | |
| Número máximo de volumes/partilhas |16 | |
| Tamanho mínimo de uma partilha em camadas |500 GB | |
| Tamanho mínimo de um volume em camadas |500 GB | |
| Tamanho máximo de uma partilha em camadas |20 TB | |
| Tamanho máximo de um volume em camadas |5 TB | |
| Tamanho mínimo de uma partilha de afixado localmente |50 GB | |
| Tamanho mínimo de um volume afixado localmente |50 GB | |
| Tamanho máximo de uma partilha de afixado localmente |2 TB | |
| Tamanho máximo de um volume afixado localmente |200 GB | |
| Número máximo de ligações de iSCSI de iniciadores |512 | |
| Número máximo de registos de controlo de acesso por dispositivo |64 | |
| Número máximo de cópias de segurança retidas pelo dispositivo virtual no *. backups* pasta para o servidor de ficheiros |5 |Isto inclui o mais recente agendada (gerado pela política de cópia de segurança padrão) e as cópias de segurança manuais. |
| Número máximo de cópias de segurança agendadas mantidos pelo dispositivo |55 |30 cópias de segurança diárias<br>12 cópias de segurança mensais<br>13 cópias de segurança anuais |
| Número máximo de cópias de segurança manuais retidas por parte do dispositivo |45 | |
| Número máximo de ficheiros por partilha para um servidor de ficheiros |1 milhão |Ao realizar uma restauração de dispositivo, os tempos de restauro são proporcionais ao número de ficheiros em todas as partilhas no dispositivo. |
| Número máximo de ficheiros por volume para um servidor de iSCSI |1 milhão | |
| Número máximo de ficheiros por matriz virtual |4 milhões | |
| Restaurar o tempo de recuperação |Restauro rápido |O restauro baseia-se no mapa térmico e depende do tamanho do volume.<br>Operações de cópia de segurança podem ocorrer enquanto uma operação de restauro está em curso. |

