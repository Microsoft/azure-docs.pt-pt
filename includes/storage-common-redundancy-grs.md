---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 06/28/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 28b53864abc37a880a9573cc9657231aff862336
ms.sourcegitcommit: df7942ba1f28903ff7bef640ecef894e95f7f335
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/14/2019
ms.locfileid: "69029847"
---
O GRS (armazenamento com redundância geográfica) foi projetado para fornecer pelo menos a durabilidade da 99.99999999999999% (16 9) de objetos em um determinado ano, replicando seus dados para uma região secundária que está a centenas de quilômetros de distância da região primária. Se sua conta de armazenamento tiver o GRS habilitado, seus dados serão duráveis mesmo no caso de uma interrupção regional completa ou um desastre no qual a região primária não seja recuperável.

Se você optar por GRS, terá duas opções relacionadas para escolher:

* O GRS Replica seus dados para outro data center em uma região secundária, mas esses dados estarão disponíveis para serem lidos somente se a Microsoft iniciar um failover da região primária para a secundária.
* O armazenamento com redundância geográfica com acesso de leitura (RA-GRS) é baseado em GRS. O RA-GRS Replica seus dados para outro data center em uma região secundária e também fornece a opção de ler a partir da região secundária. Com o RA-GRS, você pode ler a partir da região secundária, independentemente se a Microsoft inicia um failover da região primária para a secundária.

Para uma conta de armazenamento com GRS ou RA-GRS habilitada, todos os dados são replicados primeiro com o armazenamento com redundância local (LRS). Uma atualização é confirmada primeiro no local principal e replicada usando LRS. Em seguida, a atualização é replicada assincronamente para a região secundária usando GRS. Quando os dados são gravados no local secundário, eles também são replicados nesse local usando LRS.

As regiões primária e secundária gerenciam réplicas em domínios de falha e domínios de atualização separados em uma unidade de escala de armazenamento. A unidade de escala de armazenamento é a unidade de replicação básica no datacenter. A replicação nesse nível é fornecida por LRS; para obter mais informações, [consulte LRS (armazenamento com redundância local): Redundância de dados de baixo custo para o armazenamento](../articles/storage/common/storage-redundancy-lrs.md)do Azure.

Tenha esses pontos em mente ao decidir qual opção de replicação usar:

* O armazenamento com redundância de zona geográfica (GZRS) (visualização) fornece alta disponibilidade junto com a durabilidade máxima, replicando os dados de forma síncrona em três zonas de disponibilidade do Azure e, em seguida, replicando os dados de maneira assíncrona para a região secundária. Você também pode habilitar o acesso de leitura para a região secundária. O GZRS foi projetado para fornecer pelo menos a durabilidade de objetos de 99.99999999999999% (16 9) em um determinado ano. Para obter mais informações sobre o GZRS, consulte [armazenamento com redundância de zona geográfica para alta disponibilidade e durabilidade máxima (versão prévia)](../articles/storage/common/storage-redundancy-gzrs.md).
* O ZRS (armazenamento com redundância de zona) fornece alta disponibilidade com replicação síncrona em três zonas de disponibilidade do Azure e pode ser uma opção melhor para alguns cenários do que GRS ou RA-GRS. Para obter mais informações sobre o ZRS, consulte [ZRS](../articles/storage/common/storage-redundancy-zrs.md).
* A replicação assíncrona envolve um atraso desde o momento em que os dados são gravados na região primária, até quando eles são replicados para a região secundária. No caso de um desastre regional, as alterações que ainda não foram replicadas para a região secundária poderão ser perdidas se esses dados não puderem ser recuperados da região primária.
* Com o GRS, a réplica não está disponível para acesso de leitura ou gravação, a menos que a Microsoft inicie um failover para a região secundária. No caso de um failover, você terá acesso de leitura e gravação a esses dados após a conclusão do failover. Para obter mais informações, consulte [diretrizes de recuperação de desastre](../articles/storage/common/storage-disaster-recovery-guidance.md).
* Se seu aplicativo precisar ler a partir da região secundária, habilite RA-GRS ou RA-GZRS (versão prévia).
