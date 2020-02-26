---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/27/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 87457bb103f49be4ca3e7bf9f463c5bf63f3a119
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77597842"
---
Para proteger os dados do seu ficheiro Azure, partilha contra perda de dados ou corrupção, todas as ações de ficheiro sinuosa do Azure armazenam várias cópias de cada ficheiro à medida que são escritas. Dependendo dos requisitos da sua carga de trabalho, pode selecionar graus adicionais de redundância. Atualmente, o Azure Files suporta as seguintes opções de redundância de dados:

- **Localmente redundante**: Armazenamento localmente redundante, muitas vezes referido como LRS, significa que cada ficheiro é armazenado três vezes dentro de um cluster de armazenamento Azure. Isto protege contra a perda de dados devido a falhas de hardware, tais como uma má unidade de disco.
- **Zona redundante**: Armazenamento redundante da zona, muitas vezes referido como ZRS, significa que cada ficheiro é armazenado três vezes em três aglomerados de armazenamento Azure distintos. Os três diferentes clusters de armazenamento Azure armazenam o ficheiro três vezes, tal como com armazenamento localmente redundante, e estão fisicamente isolados em *diferentes zonas*de disponibilidade do Azure. As zonas de disponibilidade são locais físicos únicos dentro de uma região de Azure. Cada zona é composta por um ou mais datacenters equipados com energia, refrigeração e rede independentes. Uma escrita para armazenamento não é aceite até que seja escrita para os clusters de armazenamento em todas as três zonas de disponibilidade. 
- **Geo-redundante**: O armazenamento geo-redundante, muitas vezes referido como GRS, é como um armazenamento localmente redundante, na medida em que um ficheiro é armazenado três vezes dentro de um cluster de armazenamento Azure na região primária. Todas as escritas são então assíncronamente replicadas para uma região secundária definida pela Microsoft. O armazenamento geo-redundante fornece 6 cópias dos seus dados espalhados entre duas regiões azure. Em caso de um grande desastre, como a perda permanente de uma região de Azure devido a um desastre natural ou outro evento semelhante, a Microsoft realizará uma falha para que o secundário se torne o principal, servindo todas as operações. Uma vez que a replicação entre as regiões primárias e secundárias é assíncrona, em caso de catástrofe grave, perder-se-ão dados ainda não replicados para a região secundária. Também pode executar uma falha manual de uma conta de armazenamento geo-redundante.
- **Geo-zona redundante**: O armazenamento redundante da geozona, muitas vezes referido como GZRS, é como um armazenamento redundante da zona, na medida em que um ficheiro é armazenado nove vezes em 3 aglomerados de armazenamento distintos na região primária. Todas as escritas são então assíncronamente replicadas para uma região secundária definida pela Microsoft. O processo de failover para armazenamento geo-zona redundante funciona da mesma forma que para armazenamento geo-redundante.

As ações de ficheiro standard Azure suportam todos os quatro tipos de despedimentos, enquanto as ações de ficheiros Premium Azure apenas suportam armazenamento redundante localmente redundante e de zona redundante.

As contas de armazenamento da versão 2 (GPv2) fornecem duas opções adicionais de despedimento que não são suportadas pelo Azure Files: ler armazenamento geo-redundante acessível, muitas vezes referido como RA-GRS, e ler armazenamento acessível geozona-redundante, muitas vezes referido como RA-GZRS. Pode fornecer ações de ficheiros Azure em contas de armazenamento com estas opções definidas, no entanto o Azure Files não suporta a leitura da região secundária. As ações de ficheiros Azure implantadas em contas de armazenamento redundantes de geoou geozona acessíveis serão contabilizadas como armazenamento geo-redundante ou geo-zona redundante, respectivamente.