---
title: Mapear uma estrutura de pasta para uma topologia de sincronização de ficheiros Azure
description: Mapear uma estrutura de ficheiros e pastas existente para as partilhas de ficheiros Azure para utilização com o Azure File Sync. Um bloco de texto comum, partilhado entre documentos de migração.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 44fb95de88cc86c802e5ba72f0b5379b8708c506
ms.sourcegitcommit: 5192c04feaa3d1bd564efe957f200b7b1a93a381
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2020
ms.locfileid: "78209561"
---
Neste passo, está a avaliar quantas ações de ficheiro si precisa. Um único Servidor do Windows (ou cluster) pode sincronizar até 30 partilhas de ficheiros Azure.

Pode ter mais pastas no seu StorSimple que partilha atualmente localmente como uma partilha smb para os seus utilizadores e aplicações. O mais fácil seria prever uma partilha no local para mapear 1:1 para uma partilha de ficheiros Azure. Se este número for gerível, o que significa menos de 30 para um único Servidor Windows, ou planeia ter dois Servidores Windows (60) e assim por diante, então recomenda-se um mapeamento 1:1.

Se tiver mais ações do que 30, muitas vezes é desnecessário mapear uma partilha no local 1:1 para uma partilha de ficheiros Azure.
considere as seguintes opções:

#### <a name="share-grouping"></a>Agrupamento de ações

Por exemplo, se o seu departamento de RH tiver um total de 15 ações, então pode considerar armazenar todos os dados de RH numa única partilha de ficheiros Azure. Armazenar várias ações no local numa única partilha de ficheiros Azure não o impede de criar as habituais 15 ações SMB no seu Servidor Windows local. Significa apenas que organiza as pastas-raiz destas 15 ações como subpastas sob uma pasta comum. Em seguida, sincroniza esta pasta comum com uma partilha de ficheiros Azure. Desta forma, apenas uma única parte de ficheiro Sino Azure na nuvem é necessária para este grupo de ações no local.

#### <a name="volume-sync"></a>Sincronização de volume

O Azure File Sync suporta sincronizar a raiz de um volume numa partilha de ficheiros Azure.
Se sincronizar a pasta raiz, todas as subpastas e ficheiros acabarão na mesma partilha de ficheiros Azure.

#### <a name="other-best-practices-to-consider"></a>Outras boas práticas a considerar

Além do limite de sincronização de partilha de ficheiros 30 Azure por servidor, a principal consideração é a eficiência da sincronização.

Quando existem várias partilhas no seu servidor cada uma sincronizando com a sua própria partilha de ficheiros Azure, a sincronização pode funcionar paralelamente para todas elas. O vetor de escala não é do tamanho de todos os ficheiros num âmbito de sincronização. É o número de itens (ficheiros e pastas) que precisam de ser processados.

Uma única partilha de ficheiros Azure pode ter até 100 TiB.
O Azure File Sync suporta sincronizar até 100 milhões de itens por partilha de ficheiros Azure.

Sincronizar o volume de raiz nem sempre será a melhor resposta. Existem benefícios na sincronização de vários locais, ao fazê-lo ajuda a manter o número de itens mais baixo por âmbito de sincronização. Configurar a sincronização de ficheiros Azure com um número mais baixo de itens não é apenas importante para sincronização, mas também beneficia a restauração do lado da nuvem a partir de backups, bem como ajudar a velocidade de recuperação de desastres no caso de perder o servidor e fornecer um novo que se conecta à mesma fil Azure e partilhas.

Utilize uma combinação dos conceitos acima para ajudar a determinar quantas partilhas de ficheiros Azure precisa e quais as partes dos seus dados StorSimple existentes que acabarão em que o ficheiro Azure partilha.

Crie uma lista que registre os seus pensamentos, de modo a que possa encaminhá-la no próximo passo. Manter-se organizado aqui é importante, pois pode ser fácil perder detalhes do seu plano de mapeamento ao fornecer muitos recursos ao mesmo tempo.
