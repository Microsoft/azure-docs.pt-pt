---
title: Escolha as políticas de nivelamento em nuvem de sincronização de ficheiros Azure | Microsoft Docs
description: Detalhes sobre o que deve ter em mente ao escolher as políticas de nivelamento em nuvem Azure File Sync.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: f0bf41e1a847335a99b3e8f2e9ecbac504c3179e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107797389"
---
# <a name="choose-cloud-tiering-policies"></a>Escolha políticas de tiering de nuvem

Este artigo fornece orientações para os utilizadores que estão a selecionar e ajustar as suas políticas de tiering em nuvem. Antes de ler este artigo, certifique-se de que compreende como funciona o tiering da nuvem. Para os fundamentos de nivelamento de nuvens, consulte o nível da nuvem de [sincronização de ficheiros Azure.](file-sync-cloud-tiering-overview.md) Para obter uma explicação aprofundada das políticas de nivelamento em nuvem com exemplos, consulte [as políticas de nivelamento da nuvem Azure File Sync](file-sync-cloud-tiering-policy.md).

## <a name="limitations"></a>Limitações
- O tiering em nuvem não é suportado no volume do sistema Windows.

- Ainda pode ativar o tiering de nuvem se tiver uma quota de FSRM de nível de volume. Uma vez definida uma quota FSRM, as APIs de consulta de espaço livre que são chamadas reportam automaticamente o espaço livre no volume de acordo com a definição de quota. 

### <a name="minimum-file-size-for-a-file-to-tier"></a>Tamanho mínimo do ficheiro para um ficheiro para tier

Para as versões 9 e mais recentes, o tamanho mínimo do ficheiro para um ficheiro é baseado no tamanho do cluster do sistema de ficheiros. O tamanho mínimo do ficheiro elegível para o tiering da nuvem é calculado em 2x o tamanho do cluster e no mínimo 8 KB. A tabela a seguir ilustra os tamanhos mínimos de ficheiro que podem ser nivelados, com base no tamanho do cluster de volume:

|Tamanho do cluster de volume (Bytes) |Arquivos deste tamanho ou maior podem ser hierarquizados  |
|----------------------------|---------|
|4 KB ou menor (4096)      | 8 KB    |
|8 KB (8192)                 | 16 KB   |
|16 KB (16384)               | 32 KB   |
|32 KB (32768)               | 64 KB   |
|64 KB (65536)    | 128 KB  |
|128 KB (131072) | 256 KB |
|256 KB (262144) | 512 KB |
|512 KB (524288) | 1 MB |
|1 MB (1048576) | 2 MB |
|2 MB (2097152) | 4 MB |

Os tamanhos de cluster até 2 MB são suportados com a versão 12 do agente Azure File Sync mas, para tamanhos maiores, o nível de nuvem não funciona.

Todos os sistemas de ficheiros utilizados pelo Windows, organizem o seu disco rígido com base no tamanho do cluster (também conhecido como tamanho da unidade de atribuição). O tamanho do cluster representa a menor quantidade de espaço em disco que pode ser usado para segurar um ficheiro. Quando os tamanhos dos ficheiros não saem para um múltiplo do tamanho do cluster, deve ser usado espaço adicional para segurar o ficheiro - até ao próximo múltiplo do tamanho do cluster.

O Azure File Sync é suportado em volumes NTFS com Windows Server 2012 R2 e mais recentes. A tabela seguinte descreve os tamanhos de cluster predefinidos quando cria um novo volume NTFS com o Windows Server 2019.

|Tamanho do volume    |Windows Server 2019             |
|---------------|--------------------------------|
|7 MB - 16 TB   | 4 KB                |
|16TB - 32 TB   | 8 KB                |
|32TB - 64 TB   | 16 KB               |
|64TB - 128 TB  | 32 KB               |
|128TB - 256 TB | 64 KB (anterior máx) |
|256 TB – 512 TB| 128 KB              |
|512 TB – 1 PB  | 256 KB              |
|1 PB - 2 PB    | 512 KB              |
|2 TB – 4 PB    | 1024 KB             |
|4 TB - 8 TB    | 2048 KB (tamanho máximo)  |
|> 8 TB         | não apoiado       |

É possível que, após a criação do volume, formatesse manualmente o volume com um tamanho de cluster diferente. Se o seu volume provém de uma versão mais antiga do Windows, os tamanhos de cluster padrão também podem ser diferentes. [Este artigo tem mais detalhes sobre os tamanhos de cluster padrão.](https://support.microsoft.com/help/140365/default-cluster-size-for-ntfs-fat-and-exfat) Mesmo que escolha um tamanho de cluster inferior a 4 KB, um limite de 8 KB como o menor tamanho de ficheiro que pode ser nivelado, ainda se aplica. (Mesmo que tecnicamente o tamanho do cluster de 2x equivalesse a menos de 8 KB.)

A razão para o mínimo absoluto encontra-se na forma como o NTFS armazena ficheiros extremamente pequenos - 1 KB a 4 ficheiros de tamanho KB. Dependendo de outros parâmetros do volume, é possível que os pequenos ficheiros não sejam armazenados num cluster no disco. É possivelmente mais eficiente armazenar tais ficheiros diretamente na Master File Table do volume ou "MFT record". O ponto de reparse de nivelamento de nuvens é sempre armazenado no disco e ocupa exatamente um cluster. A nivelação de ficheiros tão pequenos pode acabar sem poupanças de espaço. Casos extremos podem até acabar por usar mais espaço com o tiering de nuvens ativado. Para evitar isso, o menor tamanho de um ficheiro que o nível da nuvem irá tiering, é de 8 KB em um tamanho de cluster de 4 KB ou menor. 

## <a name="selecting-your-initial-policies"></a>Selecionando as suas políticas iniciais

Geralmente, quando ativa o tiering da nuvem num ponto final do servidor, deve criar uma unidade virtual local para cada ponto final do servidor individual. Isolar o ponto final do servidor facilita a compreensão de como funciona o tiering da nuvem e ajusta as suas políticas em conformidade. No entanto, o Azure File Sync funciona mesmo que tenha vários pontos finais do servidor na mesma unidade, para obter detalhes ver os [pontos finais do servidor Múltiplo na](file-sync-cloud-tiering-policy.md#multiple-server-endpoints-on-a-local-volume) secção de volume local. Recomendamos também que, quando ativar pela primeira vez o tiering em nuvem, mantenha a política de data desativada e a política de espaço livre de volume seja de cerca de 10% a 20%. Para a maioria dos volumes de servidores de ficheiros, 20% de espaço livre de volume é normalmente a melhor opção.

Para simplificar e ter uma compreensão clara de como os itens serão nivelados, recomendamos que ajuste principalmente a sua política de espaço livre de volume e mantenha a sua política de data desativada, a menos que seja necessário. Recomendamos isto porque a maioria dos clientes acha valioso encher a cache local com o maior número possível de ficheiros quentes e tier o resto para a nuvem. No entanto, a política de data pode ser benéfica se quiser libertar proactivamente o espaço do disco local e conhecer ficheiros nesse ponto final do servidor acedidos após o número de dias especificados na sua política de data não precisar de ser mantido localmente. A definição da política de data liberta capacidade valiosa do disco local para outros pontos finais no mesmo volume para cache mais dos seus ficheiros.

Depois de definir as suas políticas, monitorize a saída e ajuste ambas as políticas em conformidade. Recomendamos especificamente olhar para o tamanho de **recolha de nível** de nuvem e **tamanho de recolha de nível de nuvem por** métricas de aplicação no Azure Monitor. Para aprender a monitorizar a saída, consulte [o nível da nuvem do Monitor](file-sync-monitor-cloud-tiering.md).

## <a name="adjusting-your-policies"></a>Ajustar as suas políticas

Se a quantidade de ficheiros constantemente recolhida do Azure for maior do que deseja, poderá ter mais ficheiros quentes do que o espaço para os guardar no volume do servidor local. Aumente o tamanho do volume local, se possível, e/ou diminua a percentagem de política de espaço livre de volume em pequenos incrementos. A diminuição da percentagem de espaço livre de volume também pode ter consequências negativas. Uma maior agitação no seu conjunto de dados requer mais espaço livre - para novos ficheiros e recordação de ficheiros "frios". O tiering começa com um atraso de até uma hora e depois precisa de tempo de processamento, razão pela qual deve ter sempre amplo espaço livre no seu volume.

Manter mais dados locais significa custos de saída mais baixos, uma vez que menos ficheiros serão recolhidos do Azure, mas também requer uma maior quantidade de armazenamento no local, que vem a seu custo próprio. 

Ao ajustar a sua política de espaço livre de volume, a quantidade de dados que deve manter local é determinada pelos seguintes fatores: a largura de banda, o padrão de acesso do conjunto de dados e o orçamento. Com uma ligação de baixa largura de banda, pode querer mais dados locais, para garantir o mínimo de lag para os utilizadores. Caso contrário, pode baseá-la na taxa de churn durante um determinado período. Como exemplo, se souber que 10% do conjunto de dados de 1-TB muda ou é acedido ativamente todos os meses, então pode querer manter 100 GB locais para que não esteja frequentemente a recordar ficheiros. Se o seu volume for de 2 TB, então você gostaria de manter 5% (ou 100 GB) local, o que significa que os restantes 95% são a sua percentagem de espaço livre de volume. No entanto, deve adicionar um tampão para períodos de maior agitação – por outras palavras, começando com uma maior percentagem de espaço livre de volume e, em seguida, ajustando-o se necessário mais tarde.

## <a name="standard-operating-procedures"></a>Procedimentos operacionais padrão

- Ao migrar pela primeira vez para Azure Files via Azure File Sync, o tiering em nuvem depende do upload inicial
- O tiering da nuvem verifica o cumprimento das políticas de espaço e data livres de volume a cada sessenta minutos
- A utilização do interruptor /LFSM no Robocopy quando os ficheiros migradores permitirão que os ficheiros sincronizem e o tiering da nuvem produza espaço durante o upload inicial 
- Se o nível ocorrer antes da formação de um mapa de calor, os ficheiros serão nivelados pela última vez modificada do tempo

## <a name="next-steps"></a>Passos seguintes

* [Planear uma implementação do Azure File Sync](file-sync-planning.md)
