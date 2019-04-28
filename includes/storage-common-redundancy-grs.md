---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/26/2018
ms.author: jeking
ms.custom: include file
ms.openlocfilehash: efa593d0ff0043d81574b67192deed30933e1e40
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60516254"
---
Armazenamento georredundante (GRS) foi concebido para proporcionar, pelo menos, 99,99999999999999% (16 9 s) a durabilidade dos objetos ao longo de um determinado ano ao replicar os seus dados para uma região secundária que está situada a centenas de quilómetros de distância da região primária. Se a sua conta de armazenamento tiver GRS ativada, em seguida, seus dados são duráveis mesmo em caso de uma falha regional completa ou um desastre no qual a região primária não é recuperável.

Se optar por GRS, tem duas opções relacionadas à sua escolha:

* GRS replica os dados para outro centro de dados numa região secundária, mas que os dados estão disponíveis para ser só de leitura se a Microsoft iniciará uma ativação pós-falha dos principais para a região secundária. 
* Armazenamento georredundante com acesso de leitura (RA-GRS) baseia-se em GRS. RA-GRS replica os dados para outro centro de dados numa região secundária e também fornece-lhe a opção de ler a partir da região secundária. Com o RA-GRS, pode ler a partir da região secundária, independentemente se a Microsoft inicie uma ativação pós-falha dos principais para a região secundária. 

Para uma conta de armazenamento com o GRS ou RA-GRS ativada, todos os dados pela primeira vez é replicado com armazenamento localmente redundante (LRS). Uma atualização em primeiro lugar tem o compromisso para a localização primária e replicados utiliza o LRS. A atualização, em seguida, é replicada de forma assíncrona para a região secundária com GRS. Quando os dados são escritos para a localização secundária, também são replicado dentro desse local utiliza o LRS. 

Regiões primárias e secundárias gerir réplicas em domínios de falha e domínios dentro de uma unidade de escala de armazenamento de atualização. A unidade de escala de armazenamento é a unidade de replicação básica no Centro de dados. A replicação a este nível é fornecida pela LRS; Para obter mais informações, consulte [armazenamento localmente redundante (LRS): Redundância de dados de baixo custo do armazenamento do Azure](../articles/storage/common/storage-redundancy-lrs.md).

Tenha em consideração esses pontos ao decidir que opção de replicação a utilizar:

* Armazenamento com redundância de zona (ZRS) fornece elevada disponibilidade com a replicação síncrona e pode ser uma opção melhor para alguns cenários que o GRS ou RA-GRS. Para obter mais informações sobre ZRS, veja [ZRS](../articles/storage/common/storage-redundancy-zrs.md).
* Replicação assíncrona envolve um atraso a partir do momento em que dados são escritos para a região primária, quando ele é replicado para a região secundária. Em caso de desastre regional, as alterações que ainda não tiverem sido replicadas para a região secundária podem ser perdidas se esses dados não podem ser recuperados da região primária.
* Com a GRS, a réplica não está disponível para leitura ou acesso de escrita, a menos que a Microsoft inicie uma ativação pós-falha para a região secundária. No caso de uma ativação pós-falha, vai ter de leitura e escrita acesso a esses dados após a ativação pós-falha foi concluída. Para obter mais informações, consulte [orientações sobre a recuperação após desastre](../articles/storage/common/storage-disaster-recovery-guidance.md).
* Se seu aplicativo precisar ler a partir da região secundária, ative o RA-GRS.