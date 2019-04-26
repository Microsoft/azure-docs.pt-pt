---
title: Limites de sistema de série 8000 do StorSimple | Documentos da Microsoft
description: Descreve os limites de sistema e os tamanhos recomendados para ligações e componentes de série StorSimple 8000.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: c7392678-0924-46c6-9c59-1665cb9b6586
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 03/28/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a0053f950b36351b06d08630cbf9977f53f2ed47
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60319569"
---
# <a name="what-are-storsimple-8000-series-system-limits"></a>Quais são os limites de sistema de série 8000 do StorSimple?

## <a name="overview"></a>Descrição geral

StorSimple fornece armazenamento dimensionável e flexível para o seu datacenter. No entanto, existem alguns limites que deve ter em mente como planejar, implantar e operar a solução StorSimple. A tabela seguinte descreve estes limites e fornece algumas recomendações para que pode obter o máximo proveito de sua solução StorSimple.

| Identificador de limite | Limite | Comentários |
| --- | --- | --- |
| Número máximo de credenciais da conta de armazenamento |64 | |
| Número máximo de contentores de volumes |64 | |
| Número máximo de volumes |255 | |
| Número máximo de volumes localmente afixados |32 | |
| Número máximo de agendas por modelo de largura de banda |168 |Uma agenda para cada hora, todos os dias da semana (24 * 7). |
| Tamanho máximo de um volume em camadas em dispositivos físicos |64 TB para 8100 e 8600 |8100 e 8600 são dispositivos físicos. |
| Tamanho máximo de um volume em camadas sobre dispositivos virtuais no Azure |30 TB para 8010 <br></br> 64 TB para o 8020 |dispositivos 8010 e 8020 são dispositivos virtuais no Azure que utilizam o armazenamento Standard e o armazenamento Premium, respetivamente. |
| Tamanho máximo de um volume localmente afixado em dispositivos físicos |8,5 TB para 8100 <br></br> 22,5 TB para 8600 |8100 e 8600 são dispositivos físicos. |
| Número máximo de ligações de iSCSI |512 | |
| Número máximo de ligações de iSCSI de iniciadores |512 | |
| Número máximo de registos de controlo de acesso por dispositivo |64 | |
| Número máximo de volumes de acordo com a política de cópia de segurança |20 | |
| Número máximo de cópias de segurança retidas por agenda (numa política de cópia de segurança) |64 | |
| Número máximo de agendas de acordo com a política de cópia de segurança |10 | |
| Número máximo de instantâneos de qualquer tipo que podem ser mantidos por volume |256 |Este número inclui os instantâneos locais e instantâneos da cloud. |
| Número máximo de instantâneos que podem estar presentes em qualquer dispositivo |10,000 | |
| Número máximo de volumes que podem ser processados em paralelo para cópia de segurança, restauro ou clone |16 |<ul><li>Se existirem mais de 16 volumes, eles são processados sequencialmente como blocos de processamento de se tornarem disponíveis.</li><li>Novas cópias de segurança de um clonado ou um volume em camadas restaurado não pode ocorrer até que a operação esteja concluída. No entanto, para um volume local, as cópias de segurança são permitidas depois do volume está online.</li></ul> |
| Restaurar e clonar o tempo de recuperação de volumes em camadas |< a 2 minutos |<ul><li>O volume é disponibilizado em dois minutos da operação de restauro ou um clone, independentemente do tamanho do volume.</li><li>O desempenho do volume inicialmente pode ser mais lento do que o normal, como a maioria dos dados e metadados ainda reside na cloud. Pode aumentar o desempenho como fluxos de dados a partir da cloud para o dispositivo StorSimple.</li><li>O tempo total para transferir os metadados depende do tamanho do volume alocado. Metadados automaticamente é colocado no dispositivo em segundo plano numa taxa de cinco minutos por TB de dados de volume alocado. Essa taxa pode ser afetada pela largura de banda de Internet para a cloud.</li><li>O restauro ou a operação de clonagem está concluída quando todos os metadados são no dispositivo.</li><li>Operações de cópia de segurança não não possível efetuar até o restauro ou operação de clonagem é totalmente concluída. |
| Restaurar o tempo de recuperação para volumes afixados localmente |< a 2 minutos |<ul><li>O volume é disponibilizado em dois minutos da operação de restauro, independentemente do tamanho do volume.</li><li>O desempenho do volume inicialmente pode ser mais lento do que o normal, como a maioria dos dados e metadados ainda reside na cloud. Pode aumentar o desempenho como fluxos de dados a partir da cloud para o dispositivo StorSimple.</li><li>O tempo total para transferir os metadados depende do tamanho do volume alocado. Metadados automaticamente é colocado no dispositivo em segundo plano numa taxa de cinco minutos por TB de dados de volume alocado. Essa taxa pode ser afetada pela largura de banda de Internet para a cloud.</li><li>Ao contrário dos volumes em camadas, para volumes afixados localmente, o volume de dados também é transferido localmente no dispositivo. A operação de restauro está concluída quando todos os dados de volume tenha sido colocada no dispositivo.</li><li>As operações de restauro poderão ser longas. O tempo total para concluir o restauro depende do tamanho do volume aprovisionado local, a largura de banda de Internet e os dados existentes no dispositivo. Operações de cópia de segurança no volume afixado localmente são permitidas enquanto a operação de restauro está em curso. |
| Taxa de processamento de instantâneos de cloud |15 minutos/TB |<ul><li>Está pronto para o upload, por TB de dados de volume alocado na cópia de segurança de instantâneos de tempo mínimo para tornar a cloud. </li><li> Hora do instantâneo de total cloud é calculada através da adição desta vez para o tempo de carregamento do instantâneo, o que é afetado pela largura de banda de Internet para a cloud. |
| Débito de leitura/escrita de máximo de clientes (quando servidos a partir da camada SSD) * |920/720 MB/s com uma única interface de rede 10 GbE |Até 2 x com MPIO e duas interfaces de rede. |
| Débito de leitura/escrita de máximo de clientes (quando atendidos a partir da camada HDD) * |120/250 MB/s | |
| Débito de leitura/escrita de máximo de clientes (quando atendidos a partir da camada de cloud) * para Update 3 e posterior * * |40/60 MB/s para volumes de em camadas<br><br>60/80 MB/s para volumes em camadas com a opção de arquivo selecionada durante a criação do volume |Débito de leitura depende dos clientes a gerar e a manutenção de profundidade de fila de e/s suficiente. <br><br>Velocidade obtida depende da velocidade da conta de armazenamento subjacente utilizada. |

&#42;Débito máximo por tipo de e/s foi medido com a leitura de 100 por cento e cenários de escrita de 100 por cento. Débito real pode ser mais baixo e depende de e/s misturar e condições de rede.

&#42;&#42;Números de desempenho antes da atualização 3 podem ser mais baixos.

## <a name="next-steps"></a>Passos Seguintes
Reveja os [requisitos do sistema StorSimple](storsimple-8000-system-requirements.md).

