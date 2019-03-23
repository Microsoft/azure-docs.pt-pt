---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 03/23/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e3d2466f05028a963256dbcc052c46650857836d
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58372718"
---
### <a name="premium-performance-block-blob-storage"></a>Armazenamento de BLOBs de blocos de desempenho Premium

Uma conta de armazenamento de BLOBs de bloco de desempenho de premium é otimizada para aplicações que utilizam o menor, intervalo de kilobytes, objetos. É ideal para aplicativos que exigem velocidades de transação muito alto ou armazenamento de baixa latência consistente. Armazenamento de BLOBs de blocos de desempenho Premium foi concebido para escalar com as suas aplicações. Se planeia implementar aplicações que necessitam de centenas de milhares de pedidos por segundo ou petabytes de capacidade de armazenamento, contacte-ao submeter um pedido de suporte no [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="premium-performance-page-blob-storage"></a>Armazenamento de BLOBs de página de desempenho Premium

Desempenho de Premium, contas de armazenamento para fins gerais v1 ou v2 têm os seguintes destinos de escalabilidade:

| Capacidade de conta total                            | Largura de banda total de uma conta de armazenamento localmente redundante                     |
| ------------------------------------------------- | --------------------------------------------------------------------------- |
| Capacidade do disco: 35 TB <br>Capacidade do instantâneo: 10 TB | Cópia de segurança para 50 gigabits por segundo de entrada<sup>1</sup> + saída<sup>2</sup> |

<sup>1</sup> todos os dados (pedidos), que são enviados para uma conta de armazenamento

<sup>2</sup> todos os dados (respostas) que são recebidos a partir de uma conta de armazenamento

Se estiver a utilizar contas de armazenamento de desempenho premium para discos não geridos e seu aplicativo excede os destinos de escalabilidade de uma única conta de armazenamento, pode querer migrar para discos geridos. Se não quiser migrar para discos geridos, crie a sua aplicação para utilizar várias contas de armazenamento. Em seguida, particione os dados entre essas contas de armazenamento. Por exemplo, se quiser anexar discos de 51-TB em várias VMs, espalhá-los entre duas contas de armazenamento. 35 TB é o limite para uma conta de armazenamento premium única. Certifique-se de que uma conta de armazenamento de desempenho premium única nunca tem mais de 35 TB de discos aprovisionados.