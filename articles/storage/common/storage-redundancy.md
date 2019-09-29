---
title: Redundância de dados no armazenamento do Azure | Microsoft Docs
description: Os dados em sua conta de Armazenamento do Microsoft Azure são replicados quanto à durabilidade e alta disponibilidade. As opções de redundância incluem LRS (armazenamento com redundância local), ZRS (armazenamento com redundância de zona), GRS (armazenamento com redundância geográfica), armazenamento com redundância geográfica com acesso de leitura (RA-GRS), armazenamento com redundância de zona geográfica (GZRS) e com acesso de leitura com redundância de zona geográfica armazenamento (RA-GZRS) (visualização).
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 8025228275afeb3f23268db759eb7659b9887132
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/29/2019
ms.locfileid: "71670787"
---
# <a name="azure-storage-redundancy"></a>Redundância de armazenamento do Azure

Os dados na sua conta de armazenamento do Microsoft Azure são sempre replicados para garantir a durabilidade e a elevada disponibilidade. O armazenamento do Azure copia seus dados para que eles sejam protegidos contra eventos planejados e não planejados, incluindo falhas transitórias de hardware, interrupções de rede ou energia e desastres naturais maciços. Você pode optar por replicar seus dados dentro do mesmo data center, entre data centers zonais na mesma região ou em regiões geograficamente separadas.

A replicação garante que a sua conta de armazenamento cumpre o [Contrato de Nível de Serviço (SLA) para o Armazenamento](https://azure.microsoft.com/support/legal/sla/storage/) mesmo em caso de falhas. Leia o SLA para obter mais informações sobre as garantias do Armazenamento do Azure quanto à durabilidade e à disponibilidade.

O armazenamento do Azure verifica regularmente a integridade dos dados armazenados usando verificações de redundância cíclica (CRCs). Se a corrupção de dados for detectada, ela será reparada usando dados redundantes. O armazenamento do Azure também calcula somas de verificação em todo o tráfego de rede para detectar corrupção de pacotes de dados ao armazenar ou recuperar dados.

## <a name="choosing-a-redundancy-option"></a>Escolhendo uma opção de redundância

Ao criar uma conta de armazenamento, você pode selecionar uma das seguintes opções de redundância:

[!INCLUDE [azure-storage-redundancy](../../../includes/azure-storage-redundancy.md)]

A tabela a seguir fornece uma visão geral rápida do escopo de durabilidade e disponibilidade que cada estratégia de replicação fornecerá a você para um determinado tipo de evento (ou evento de impacto semelhante).

| Cenário                                                                                                 | LRS                             | ZRS                              | GRS/RA-GRS                                  | GZRS/RA-GZRS (visualização)                              |
| :------------------------------------------------------------------------------------------------------- | :------------------------------ | :------------------------------- | :----------------------------------- | :----------------------------------- |
| Indisponibilidade de nó dentro de um data center                                                                 | Sim                             | Sim                              | Sim                                  | Sim                                  |
| Um data center inteiro (zonal ou não zonal) fica indisponível                                           | Não                              | Sim                              | Sim                                  | Sim                                  |
| Uma interrupção em toda a região                                                                                     | Não                              | Não                               | Sim                                  | Sim                                  |
| Acesso de leitura aos seus dados (em uma região remota de replicação geográfica) no caso de indisponibilidade em toda a região | Não                              | Não                               | Sim (com RA-GRS)                                   | Sim (com RA-GZRS)                                 |
| Projetado para fornecer \_ \_ durabilidade de objetos em um determinado ano                                          | pelo menos 99,999999999% (11 9 ' s) | pelo menos 99,9999999999% (12 9 ' s) | pelo menos 99.99999999999999% (16 9 ' s) | pelo menos 99.99999999999999% (16 9 ' s) |
| Tipos de conta de armazenamento com suporte                                                                   | GPv2, GPv1, Blob                | GPv2                             | GPv2, GPv1, Blob                     | GPv2                     |
| SLA de disponibilidade para solicitações de leitura | Pelo menos 99,9% (99% para a camada de acesso fria) | Pelo menos 99,9% (99% para a camada de acesso fria) | Pelo menos 99,9% (99% para a camada de acesso fria) para GRS<br /><br />Pelo menos 99,99% (99,9% para a camada de acesso fria) para RA-GRS | Pelo menos 99,9% (99% para a camada de acesso fria) para GZRS<br /><br />Pelo menos 99,99% (99,9% para a camada de acesso fria) para RA-GZRS |
| SLA de disponibilidade para solicitações de gravação | Pelo menos 99,9% (99% para a camada de acesso fria) | Pelo menos 99,9% (99% para a camada de acesso fria) | Pelo menos 99,9% (99% para a camada de acesso fria) | Pelo menos 99,9% (99% para a camada de acesso fria) |

Todos os dados em sua conta de armazenamento são replicados, incluindo BLOBs de blocos e blobs de acréscimo, blobs de páginas, filas, tabelas e arquivos. Todos os tipos de contas de armazenamento são replicados, embora ZRS exija uma conta de armazenamento v2 de uso geral.

Para obter informações sobre preços para cada opção de redundância, consulte [preços do armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/). 

Para obter informações sobre as garantias de armazenamento do Azure quanto à durabilidade e disponibilidade, consulte o [SLA do armazenamento do Azure](https://azure.microsoft.com/support/legal/sla/storage/).

> [!NOTE]
> Atualmente, o armazenamento Premium do Azure dá suporte apenas ao LRS (armazenamento com redundância local).

## <a name="changing-replication-strategy"></a>Alterando a estratégia de replicação

Você pode alterar a estratégia de replicação da sua conta de armazenamento usando o [portal do Azure](https://portal.azure.com/), o [Azure PowerShell](storage-powershell-guide-full.md), [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)ou uma das bibliotecas de cliente de [armazenamento do Azure](https://docs.microsoft.com/azure/index#pivot=sdkstools). A alteração do tipo de replicação da sua conta de armazenamento não resulta em tempo de inatividade.

> [!NOTE]
> No momento, você não pode usar as bibliotecas de cliente de armazenamento do portal do Azure ou do Azure para converter sua conta em ZRS, GZRS ou RA-GZRS. Para migrar sua conta para o ZRS, consulte [armazenamento com redundância de zona (ZRS) para criar aplicativos de armazenamento do Azure altamente disponíveis](storage-redundancy-zrs.md) para obter detalhes. Para migrar GZRS ou RA-GZRS, consulte [armazenamento com redundância de zona geográfica para alta disponibilidade e durabilidade máxima (versão prévia)](storage-redundancy-zrs.md) para obter detalhes.

### <a name="are-there-any-costs-to-changing-my-accounts-replication-strategy"></a>Há algum custo para alterar a estratégia de replicação da minha conta?

Depende do seu caminho de conversão. Ordenando da menor para a mais cara, as ofertas de redundância de armazenamento do Azure LRS, ZRS, GRS, RA-GRS, GZRS e RA-GZRS. Por exemplo, passar *de* LRS para qualquer outro tipo de replicação incorrerá em encargos adicionais, pois você está mudando para um nível de redundância mais sofisticado. Migrar *para* grs ou ra-grs incorrerá em um encargo de largura de banda de saída, pois seus dados (em sua região primária) estão sendo replicados para sua região secundária remota. Essa cobrança é um custo único na configuração inicial. Depois que os dados são copiados, não há nenhuma cobrança adicional de migração. Você é cobrado somente pela replicação de novas ou atualizações nos dados existentes. Para obter detalhes sobre cobranças de largura de banda, consulte a [página de preços do armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/blobs/)

Se você migrar sua conta de armazenamento de GRS para LRS, não haverá nenhum custo adicional, mas os dados replicados serão excluídos do local secundário.

Se você migrar sua conta de armazenamento de RA-GRS para GRS ou LRS, essa conta será cobrada como RA-GRS por um adicional 30 dias além da data em que foi convertida.

## <a name="see-also"></a>Consulte também

- [LRS (armazenamento com redundância local): Redundância de dados de baixo custo para o armazenamento do Azure](storage-redundancy-lrs.md)
- [Armazenamento com redundância de zona (ZRS): Aplicativos de armazenamento do Azure altamente disponíveis](storage-redundancy-zrs.md)
- [Armazenamento com redundância geográfica (GRS): Replicação entre regiões para o armazenamento do Azure](storage-redundancy-grs.md)
- [Armazenamento com redundância de zona geográfica (GZRS) para alta disponibilidade e durabilidade máxima (versão prévia)](storage-redundancy-gzrs.md)
- [Escalabilidade e metas de desempenho do armazenamento do Azure](storage-scalability-targets.md)
- [Criando aplicativos altamente disponíveis usando o armazenamento RA-GRS](../storage-designing-ha-apps-with-ragrs.md)
- [Opções de redundância Armazenamento do Microsoft Azure e armazenamento com redundância geográfica com acesso de leitura](https://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx)
- [SOSP Paper – armazenamento do Azure: Um serviço de armazenamento em nuvem altamente disponível com consistência forte](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)
