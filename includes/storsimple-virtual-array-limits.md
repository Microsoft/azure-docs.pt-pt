---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 658fd9178495f14274c85eab2129c9dcd3be7693
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "67184290"
---
| **Identificador de limite** | **Limite** | **Comentários** |
| --- | --- | --- |
| Capacidade total (incluindo nuvem) |Até 64 TB por dispositivo virtual |Pode falhar sobre um Conjunto Virtual StorSimple completo para outra matriz vazia. Se tentar restaurar o mesmo dispositivo, certifique-se de que dispõe de espaço suficiente para completar esta operação. Depois de ter ultrapassado 32 TB, não poderá restaurar o mesmo dispositivo. |
| Número máximo de credenciais de conta de armazenamento por dispositivo |1 | |
| Número máximo de volumes/ações |16 | |
| Tamanho mínimo de uma parte hierárquico |500 GB | |
| Tamanho mínimo de um volume hierárquico |500 GB | |
| Tamanho máximo de uma parte hierárquico |20 TB | |
| Tamanho máximo de um volume hierárquico |5 TB | |
| Tamanho mínimo de uma parte fixa local |50 GB | |
| Tamanho mínimo de um volume fixado localmente |50 GB | |
| Tamanho máximo de uma parte fixa local |2 TB | |
| Tamanho máximo de um volume fixado localmente |200 GB | |
| Número máximo de ligações iSCSI de iniciadores |512 | |
| Número máximo de registos de controlo de acesso por dispositivo |64 | |
| Número máximo de cópias de segurança retidas pelo dispositivo virtual na pasta *.backups* para servidor de ficheiros |5 |Isto inclui os mais recentes programados (gerados pela política de backup predefinido) e cópias de segurança manuais. |
| Número máximo de backups programados retidos pelo dispositivo |55 |30 backups diários<br>12 backups mensais<br>13 backups mensais |
| Número máximo de cópias de segurança manuais retidas pelo dispositivo |45 | |
| Número máximo de ficheiros por ação para um servidor de ficheiros |1 milhão |Ao executar uma restauração do dispositivo, os tempos de restauro são proporcionais ao número de ficheiros em todas as partilhas do dispositivo. |
| Número máximo de ficheiros por volume para um servidor iSCSI |1 milhão | |
| Número máximo de ficheiros por matriz virtual |4 milhões | |
| Restaurar o tempo de recuperação |Restauro rápido |A restauração baseia-se no mapa de calor e depende do tamanho do volume.<br>Podem ocorrer operações de backup enquanto uma operação de restauro está em curso. |

