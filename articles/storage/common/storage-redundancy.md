---
title: Redundância de dados no armazenamento do Azure | Documentos da Microsoft
description: Dados da sua conta de armazenamento do Microsoft Azure são replicados para durabilidade e elevada disponibilidade. Opções de redundância incluem armazenamento localmente redundante (LRS), o armazenamento com redundância de zona (ZRS), o armazenamento georredundante (GRS) e o armazenamento georredundante com acesso de leitura (RA-GRS).
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 01/18/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 347ae6dbdbab866b6d82d64bec4e668689078429
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57791244"
---
# <a name="azure-storage-redundancy"></a>Redundância de armazenamento do Azure

Os dados na sua conta de armazenamento do Microsoft Azure são sempre replicados para garantir a durabilidade e a elevada disponibilidade. O armazenamento do Azure copia os dados para que ele está protegido contra eventos planeados e imprevistos, incluindo falhas de hardware transitórias, rede ou falhas de energia e enormes desastres naturais. Pode optar por replicar os seus dados no mesmo centro de dados, em centros de dados zonal dentro da mesma região ou em várias regiões geograficamente dispersas.

A replicação garante que a sua conta de armazenamento cumpre o [Contrato de Nível de Serviço (SLA) para o Armazenamento](https://azure.microsoft.com/support/legal/sla/storage/) mesmo em caso de falhas. Leia o SLA para obter mais informações sobre as garantias do Armazenamento do Azure quanto à durabilidade e à disponibilidade.

## <a name="choosing-a-redundancy-option"></a>Escolher uma opção de redundância

Quando cria uma conta de armazenamento, pode selecionar uma das seguintes opções de redundância:

* [Armazenamento localmente redundante (LRS)](storage-redundancy-lrs.md)
* [Armazenamento com redundância entre zonas (ZRS)](storage-redundancy-zrs.md)
* [Armazenamento georredundante (GRS)](storage-redundancy-grs.md)
* [Armazenamento georredundante com acesso de leitura (RA-GRS)](storage-redundancy-grs.md#read-access-geo-redundant-storage)

A tabela seguinte fornece uma rápida visão geral do escopo da durabilidade e disponibilidade cada estratégia de replicação irá fornecer-lhe para um determinado tipo de evento (ou um evento de impacto semelhante).

| Cenário                                                                                                 | LRS                             | ZRS                              | GRS                                  | RA-GRS                               |
| :------------------------------------------------------------------------------------------------------- | :------------------------------ | :------------------------------- | :----------------------------------- | :----------------------------------- |
| Indisponibilidade de nó dentro de um centro de dados                                                                 | Sim                             | Sim                              | Sim                                  | Sim                                  |
| Um Datacenter inteiro (zonal ou não zonal) fica indisponível                                           | Não                              | Sim                              | Sim                                  | Sim                                  |
| Uma interrupção de toda a região                                                                                     | Não                              | Não                               | Sim                                  | Sim                                  |
| Acesso de leitura aos seus dados (numa região remota, georreplicado) em caso de indisponibilidade de toda a região | Não                              | Não                               | Não                                   | Sim                                  |
| Concebido para fornecer \_ \_ durabilidade dos objetos ao longo de um determinado ano                                          | pelo menos, 99,999999999% (11 9 s) | pelo menos, 99.9999999999% (12 9 s) | pelo menos 99,99999999999999% (16 9 s) | pelo menos 99,99999999999999% (16 9 s) |
| Tipos de conta de armazenamento suportadas                                                                   | GPv2, GPv1, Blob                | GPv2                             | GPv2, GPv1, Blob                     | GPv2, GPv1, Blob                     |
| SLA de disponibilidade para solicitações de leitura | Pelo menos, 99,9% (99% para o escalão de acesso esporádico) | Pelo menos, 99,9% (99% para o escalão de acesso esporádico) | Pelo menos, 99,9% (99% para o escalão de acesso esporádico) | Pelo menos, 99,99% (99,9% para o escalão de acesso esporádico) |
| SLA de disponibilidade para pedidos de escrita | Pelo menos, 99,9% (99% para o escalão de acesso esporádico) | Pelo menos, 99,9% (99% para o escalão de acesso esporádico) | Pelo menos, 99,9% (99% para o escalão de acesso esporádico) | Pelo menos, 99,9% (99% para o escalão de acesso esporádico) |

Para obter informações sobre cada opção de redundância de preços, consulte [preços de armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/). 

Para obter informações sobre o armazenamento do Azure garante-se para durabilidade e disponibilidade, consulte a [SLA de armazenamento do Azure](https://azure.microsoft.com/support/legal/sla/storage/).

> [!NOTE]
> O armazenamento Premium suporta o armazenamento apenas localmente redundante (LRS).

## <a name="changing-replication-strategy"></a>Alterar a estratégia de replicação
Pode alterar a estratégia de replicação da conta de armazenamento utilizando o [portal do Azure](https://portal.azure.com/), [Azure Powershell](storage-powershell-guide-full.md), [da CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest), ou uma do [cliente do Azure bibliotecas](https://docs.microsoft.com/azure/index?view=azure-dotnet#pivot=sdkstools). Alterar o tipo de replicação da conta de armazenamento não resulta num período de indisponibilidade.

   > [!NOTE]
   > Atualmente, não é possível utilizar o Portal ou a API para converter a sua conta ZRS. Se pretender converter a replicação da sua conta ZRS, veja [armazenamentoredundância de zona (ZRS)](storage-redundancy-zrs.md) para obter detalhes.
    
### <a name="are-there-any-costs-to-changing-my-accounts-replication-strategy"></a>Existem custos para alterar a estratégia de replicação da minha conta?
Depende de seu caminho de conversão. Ordenação de mais barato da oferta de redundância mais caras temos LRS, ZRS, GRS e RA-GRS. Por exemplo, irá *partir* LRS a nada serão aplicados custos adicionais, uma vez que vai para um nível de redundância mais sofisticado. Vai *para* GRS ou RA-GRS será cobrado um custo de largura de banda de saída porque os dados (em sua região primária) está a ser replicados para a região secundária remoto. Este é um custo único na configuração inicial. Depois dos dados são copiados, não existem mais conversão custos. Apenas serão cobrados para replicar qualquer novo ou de atualizações nos dados existentes. Para obter detalhes sobre os custos de largura de banda, consulte [página de preços de armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/blobs/).

Se converter sua conta de armazenamento de GRS para LRS, não existe nenhum custo adicional, mas os dados replicados são eliminados da localização secundária.

Se converter sua conta de armazenamento de RA-GRS para GRS ou LRS, essa conta é faturada como RA-GRS para 30 dias adicionais para além da data em que foi convertido.

## <a name="see-also"></a>Consulte também

- [Armazenamento localmente redundante (LRS): Redundância de dados de baixo custo do armazenamento do Azure](storage-redundancy-lrs.md)
- [Armazenamento com redundância de zona (ZRS): Aplicações de armazenamento do Azure de elevada disponibilidade](storage-redundancy-zrs.md)
- [Armazenamento georredundante (GRS): Replicação de inter-regional do armazenamento do Azure](storage-redundancy-grs.md)
- [Azure destinos de escalabilidade e desempenho de armazenamento](storage-scalability-targets.md)
- [Conceber aplicações de elevada disponibilidade com o armazenamento RA-GRS](../storage-designing-ha-apps-with-ragrs.md)
- [Armazenamento do Microsoft Azure redundância acesso de leitura e de opções de armazenamento georredundante](https://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx)
- [Artigo SOSP – Storage do Azure: Um serviço de armazenamento de cloud de elevada disponibilidade com consistência forte](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)
