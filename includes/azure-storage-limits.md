---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/11/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 01d84914682d40b97c3d480a753c8b966cf61acc
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553970"
---
A tabela seguinte descreve os limites predefinidos para o armazenamento do Azure. O *entrada* limite refere-se a todos os dados de pedidos que são enviados para uma conta de armazenamento. O *saída* limite refere-se a todos os dados de respostas que são recebidas a partir de uma conta de armazenamento.

| Recurso | Limite predefinido |
| --- | --- |
| Número de contas de armazenamento por região por subscrição, que inclui contas Standard e Premium | 250 |
| Capacidade de conta de armazenamento máximo | 2 PB para E.U.A. e Europa, 500 TB para todas as outras regiões, que inclui o Reino Unido |
| Número máximo de contentores de BLOBs, blobs, partilhas de ficheiros, tabelas, filas, entidades ou mensagens por conta de armazenamento | Sem limite |
| Taxa máxima de pedidos<sup>1</sup> por conta de armazenamento | 20 000 pedidos por segundo |
| Máxima de entrada<sup>1</sup> por conta de armazenamento (regiões dos E.U.A.) | 10 Gbps, se o RA-GRS/GRS estiver ativado, 20 Gbps para LRS/ZRS<sup>2</sup> |
| Máxima de entrada<sup>1</sup> por conta de armazenamento (regiões fora dos E.U.A.) | 5 Gbps, se o RA-GRS/GRS estiver ativado, 10 Gbps para LRS/ZRS<sup>2</sup> |
| Saída máxima para fins gerais v2 e contas de armazenamento de BLOBs (todas as regiões) | 50 Gbps |
| Saída máxima para as contas de armazenamento para fins gerais v1 (regiões dos E.U.A.) | 20 Gbps se o RA-GRS/GRS estiver ativado, 30 Gbps para LRS/ZRS<sup>2</sup> |
| Saída máxima para as contas de armazenamento para fins gerais v1 (regiões fora dos E.U.A.) | 10 Gbps, se o RA-GRS/GRS estiver ativado, 15 Gbps para LRS/ZRS<sup>2</sup> |

<sup>1</sup>as contas de armazenamento padrão do azure suportam limites mais elevados para entrada por pedido. Para pedir um aumento de limites de conta para entrada, contacte [suporte do Azure](https://azure.microsoft.com/support/faq/).

<sup>2</sup> [replicação de armazenamento do azure](https://docs.microsoft.com/azure/storage/common/storage-redundancy) as opções incluem:
* **RA-GRS**: Armazenamento georredundante com acesso de leitura. Se o RA-GRS for ativado, os destinos de saída para a localização secundária são idênticos para a localização principal.
* **GRS**: Armazenamento georredundante. 
* **ZRS**: Armazenamento com redundância de zona.
* **LRS**: Armazenamento localmente redundante. 

> [!NOTE]
> Recomendamos que utilize uma conta de armazenamento para fins gerais v2 para a maioria dos cenários. Pode facilmente atualizar uma para fins gerais v1 ou uma conta de armazenamento de Blobs do Azure para uma conta para fins gerais v2, sem períodos de indisponibilidade e sem a necessidade de copiar dados.
>
> Para obter mais informações sobre contas de armazenamento do Azure, consulte [descrição geral da conta de armazenamento](../articles/storage/common/storage-account-overview.md). 

Se as necessidades da sua aplicação excederem os destinos de escalabilidade de uma conta de armazenamento única, pode criar a sua aplicação para utilizar várias contas de armazenamento. Em seguida, pode particionar seus objetos de dados entre essas contas de armazenamento. Para obter informações sobre os preços de volume, consulte [preços de armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/).

Todas as contas de armazenamento executam numa topologia de rede simples e suportam os destinos de escalabilidade e desempenho descritos neste artigo, independentemente de quando foram criadas. Para obter mais informações sobre a arquitetura de rede simples de armazenamento do Azure e na escalabilidade, consulte [armazenamento do Microsoft Azure: Um serviço de armazenamento de Cloud de elevada disponibilidade com consistência forte](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).

