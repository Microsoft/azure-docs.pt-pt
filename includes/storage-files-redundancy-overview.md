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
ms.openlocfilehash: b7c097306ba46d4f0024aecc55994508e2d8a090
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96011725"
---
Para proteger os dados nas suas ações de ficheiroS Azure contra perda de dados ou corrupção, todas as ações de ficheiros Azure armazenam várias cópias de cada ficheiro à medida que estão escritas. Dependendo dos requisitos da sua carga de trabalho, pode selecionar graus adicionais de redundância. A Azure Files suporta atualmente as seguintes opções de redundância de dados:

- **Localmente redundante**: O armazenamento localmente redundante, muitas vezes referido como LRS, significa que cada ficheiro é armazenado três vezes dentro de um cluster de armazenamento Azure. Isto protege contra a perda de dados devido a falhas no hardware, como uma má unidade de disco.
- **Zona redundante**: O armazenamento redundante da zona, muitas vezes referido como ZRS, significa que cada ficheiro é armazenado três vezes em três aglomerados distintos de armazenamento Azure. Tal como acontece com o armazenamento localmente redundante, a redundância de zona dá-lhe três cópias de cada ficheiro, no entanto estas cópias estão fisicamente isoladas em três aglomerados de armazenamento distintos em *diferentes zonas de disponibilidade* de Azure. As zonas de disponibilidade são localizações físicas únicas dentro de uma região de Azure. Cada zona é composta por um ou mais datacenters equipados com energia, refrigeração e rede independentes. Uma escrita para armazenamento não é aceite até que seja escrita para os clusters de armazenamento em todas as três zonas de disponibilidade. 
- **Geo-redundante**: O armazenamento geo-redundante, muitas vezes referido como GRS, é como um armazenamento localmente redundante, na medida em que um ficheiro é armazenado três vezes dentro de um aglomerado de armazenamento Azure na região primária. Todas as escritas são então assíncronamente replicadas para uma região secundária definida pela Microsoft. O armazenamento geo-redundante fornece 6 cópias dos seus dados distribuídos entre duas regiões de Azure. Em caso de grande desastre, como a perda permanente de uma região de Azure devido a um desastre natural ou outro evento similar, a Microsoft realizará um failover para que o secundário em vigor se torne o principal, servindo todas as operações. Uma vez que a replicação entre as regiões primária e secundária é assíncrona, em caso de catástrofe grave, perder-se-ão ainda dados ainda não replicados na região secundária. Também pode efetuar uma falha manual de uma conta de armazenamento geo-redundante.
- **Geo-zona redundante**: O armazenamento redundante geo-zona, muitas vezes referido como GZRS, é como uma zona de armazenamento redundante, na medida em que um ficheiro é armazenado três vezes em três aglomerados de armazenamento distintos na região primária. Todas as escritas são então assíncronamente replicadas para uma região secundária definida pela Microsoft. O processo de failover para o armazenamento de zonas geo-redundantes funciona da mesma forma que para o armazenamento geo-redundante.

As ações de ficheiros Standard Azure suportam todos os quatro tipos de despedimentos, enquanto as ações de ficheiros Premium Azure apenas suportam armazenamento redundante localmente e zona redundante.

As contas de armazenamento da versão geral 2 (GPv2) fornecem duas opções adicionais de despedimento que não são suportadas por Ficheiros Azure: ler armazenamento geo-redundante acessível, muitas vezes referido como RA-GRS, e ler armazenamento geo-zona-redundante acessível, muitas vezes referido como RA-GZRS. Pode providenciar ações de ficheiros Azure em contas de armazenamento com estas opções definidas, no entanto, a Azure Files não suporta a leitura da região secundária. As ações de ficheiros Azure implantadas em contas de armazenamento redundantes de geo ou geo-zona acessíveis serão faturadas como armazenamento de geo-redundantes ou de geo-zona, respectivamente.