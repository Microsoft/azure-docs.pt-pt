---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 658fd9178495f14274c85eab2129c9dcd3be7693
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "67184290"
---
| **Identificador de limite** | **Limite** | **Comentários** |
| --- | --- | --- |
| Capacidade total (incluindo nuvem) |Até 64 TB por dispositivo virtual |Você pode falhar sobre um StorSimple Virtual Array completo para outra matriz vazia. Se tentar restaurar o mesmo dispositivo, certifique-se de que tem espaço suficiente no dispositivo para completar esta operação. Depois de ter ultrapassado os 32 TB, não é possível restaurar o mesmo dispositivo. |
| Número máximo de credenciais de conta de armazenamento por dispositivo |1 | |
| Número máximo de volumes/ações |16 | |
| Tamanho mínimo de uma parte hierárquico |500 GB | |
| Tamanho mínimo de um volume hierárquico |500 GB | |
| Tamanho máximo de uma parte hierárquico |20 TB | |
| Tamanho máximo de um volume hierárquico |5 TB | |
| Tamanho mínimo de uma parte fixada localmente |50 GB | |
| Tamanho mínimo de um volume fixado localmente |50 GB | |
| Tamanho máximo de uma parte fixada localmente |2 TB | |
| Tamanho máximo de um volume fixado localmente |200 GB | |
| Número máximo de ligações iSCSI de iniciadores |512 | |
| Número máximo de registos de controlo de acesso por dispositivo |64 | |
| Número máximo de backups retidos pelo dispositivo virtual na pasta *.backups* para servidor de ficheiros |5 |Isto inclui as mais recentes agendadas (geradas pela política de backup padrão) e cópias de segurança manuais. |
| Número máximo de cópias de segurança programadas retidas pelo dispositivo |55 |30 backups diários<br>12 backups mensais<br>13 backups anuais |
| Número máximo de cópias de segurança manuais retidas pelo dispositivo |45 | |
| Número máximo de ficheiros por ação para um servidor de ficheiros |1 milhão |Ao realizar uma restauração do dispositivo, os tempos de restauro são proporcionais ao número de ficheiros em todas as partes do dispositivo. |
| Número máximo de ficheiros por volume para um servidor iSCSI |1 milhão | |
| Número máximo de ficheiros por matriz virtual |4 milhões | |
| Restaurar o tempo de recuperação |Restauro rápido |O restauro baseia-se no mapa de calor e depende do tamanho do volume.<br>As operações de backup podem ocorrer enquanto uma operação de restauro está em andamento. |

