---
title: Metas de escalabilidade e desempenho de Ficheiros do Azure
description: Conheça os objetivos de escalabilidade e desempenho para os Ficheiros Azure, incluindo a capacidade, taxa de pedido e limites de largura de banda de entrada e saída.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 0ecfbb9053fde4ff332cbbcb6e14a84a5bbeb99a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104593157"
---
# <a name="azure-files-scalability-and-performance-targets"></a>Metas de escalabilidade e desempenho de Ficheiros do Azure
[O Azure Files](storage-files-introduction.md) oferece ações de ficheiros totalmente geridas na nuvem que são acessíveis através dos protocolos do sistema de ficheiros SMB e NFS. Este artigo discute os objetivos de escalabilidade e desempenho para Azure Files e Azure File Sync.

Os alvos de escalabilidade e desempenho listados aqui são alvos de alta qualidade, mas podem ser afetados por outras variáveis na sua implantação. Por exemplo, a produção de um ficheiro também pode ser limitada pela largura de banda de rede disponível, e não apenas pelos servidores que hospedam as suas ações de ficheiroS Azure. Recomendamos vivamente testar o seu padrão de utilização para determinar se a escalabilidade e desempenho dos Ficheiros Azure satisfazem os seus requisitos. Estamos também empenhados em aumentar estes limites ao longo do tempo. 

## <a name="azure-files-scale-targets"></a>Metas de dimensionamento dos Ficheiros do Azure
As ações de ficheiros Azure são implantadas em contas de armazenamento, que são objetos de alto nível que representam um conjunto partilhado de armazenamento. Este conjunto de armazenamento pode ser usado para implantar várias partilhas de ficheiros. Há, portanto, três categorias a ter em conta: contas de armazenamento, ações de ficheiros Azure e ficheiros.

### <a name="storage-account-scale-targets"></a>Metas de escala de conta de armazenamento
O Azure suporta vários tipos de contas de armazenamento para diferentes cenários de armazenamento que os clientes podem ter, mas existem dois tipos principais de contas de armazenamento para ficheiros Azure. Que tipo de conta de armazenamento precisa de criar depende se pretende criar uma parte de ficheiro padrão ou uma partilha de ficheiros premium: 

- **Contas de armazenamento versão geral 2 (GPv2): As** contas de armazenamento GPv2 permitem-lhe implantar ações de ficheiros Azure em hardware standard/hard disk (baseado em HDD). Além de armazenar ações de ficheiros Azure, as contas de armazenamento GPv2 podem armazenar outros recursos de armazenamento, tais como recipientes blob, filas ou mesas. As ações de ficheiro podem ser implantadas nos níveis otimizados (predefinidos), quentes ou frescos.

- **Contas de armazenamento de filestorage**: As contas de armazenamento de filestorage permitem-lhe implantar ações de ficheiros Azure em hardware baseado em discos de estado premium/sólido (baseado em SSD). As contas de arquiteação de ficheiros só podem ser utilizadas para armazenar ações de ficheiros Azure; nenhum outros recursos de armazenamento (recipientes blob, filas, mesas, etc.) podem ser implantados numa conta de arquiteta.

| Atributo | Contas de armazenamento GPv2 (padrão) | Contas de armazenamento de arquiteação de ficheiros (premium) |
|-|-|-|
| Número de contas de armazenamento por região por subscrição | 250 | 250 |
| Capacidade máxima da conta de armazenamento | 5 PiB<sup>1</sup> | 100 TiB (a provisionado) |
| Número máximo de ações de ficheiros | Ilimitado | O tamanho total de todas as ações deve ser inferior ao máximo do que a capacidade máxima da conta de armazenamento |
| Taxa máxima de pedido simultânea | 20.000 IOPS<sup>1</sup> | 100.000 OPS |
| Entrada máxima | <ul><li>EUA/Europa: 10 Gbp/sec<sup>1</sup></li><li>Outras regiões (LRS/ZRS): 10 Gbp/sec<sup>1</sup></li><li>Outras regiões (GRS): 5 Gbp/sec<sup>1</sup></li></ul> | 4,136 MiB/seg |
| Saída máxima | 50 Gbp/seg<sup>1</sup> | 6,204 MiB/seg |
| Número máximo de regras de rede virtual | 200 | 200 |
| Número máximo de regras de endereço IP | 200 | 200 |
| Operações de leitura de gestão | 800 por 5 minutos | 800 por 5 minutos |
| Operações de escrita de gestão | 10 por segundo/1200 por hora | 10 por segundo/1200 por hora |
| Operações de lista de gestão | 100 por 5 minutos | 100 por 5 minutos |

<sup>1 As</sup> contas de armazenamento da versão geral 2 suportam limites de capacidade mais elevados e limites mais elevados para a entrada por pedido. Para solicitar um aumento dos limites de conta, contacte [o Suporte Azure](https://azure.microsoft.com/support/faq/).

### <a name="azure-file-share-scale-targets"></a>Alvos de escala de partilha de ficheiros Azure
| Atributo | Ações de ficheiros<sup>padrão 1</sup> | Ações de ficheiros premium |
|-|-|-|
| Tamanho mínimo de uma partilha de ficheiros | Sem mínimo | 100 GiB (a provisionado) |
| Unidade de aumento/diminuição de tamanhos previstos | N/D | 1 GiB |
| Tamanho máximo de uma partilha de ficheiros | <ul><li>100 TiB, com grande funcionalidade de partilha de ficheiros ativada<sup>2</sup></li><li>5 TiB, padrão</li></ul> | 100 TiB |
| Número máximo de ficheiros numa partilha de ficheiros | Sem limite | Sem limite |
| Taxa máxima de pedido (Max IOPS) | <ul><li>10.000, com grande funcionalidade de partilha de ficheiros ativada<sup>2</sup></li><li>1.000 ou 100 pedidos por 100 ms, padrão</li></ul> | <ul><li>IOPS de base: 400 + 1 IOPS por GiB, até 100.000</li><li>IOPS rebentando: Max (4000,3x IOPS por GiB), até 100.000</li></ul> |
| Entrada máxima para uma única partilha de ficheiros | <ul><li>Até 300 MiB/seg, com grande funcionalidade de partilha de ficheiros ativada<sup>2</sup></li><li>Até 60 MiB/seg, padrão</li></ul> | 40 MiB/s + 0,04 * GiB a provisionado |
| Saída máxima para uma única partilha de ficheiros | <ul><li>Até 300 MiB/seg, com grande funcionalidade de partilha de ficheiros ativada<sup>2</sup></li><li>Até 60 MiB/seg, padrão</li></ul> | 60 MiB/s + 0,06 * GiB a provisionado |
| Número máximo de instantâneos de partilha | 200 instantâneos | 200 instantâneos |
| Comprimento máximo do nome do objeto (diretórios e ficheiros) | 2.048 caracteres | 2.048 caracteres |
| Componente de nome de pathname máximo (no caminho \A\B\C\D, cada letra é um componente) | 255 caracteres | 255 caracteres |
| Limite de ligação rígido (apenas NFS) | N/D | 178 |
| Número máximo de canais multicanais SMB | N/D | 4 |
| Número máximo de políticas de acesso armazenadas por partilha de ficheiros | 5 | 5 |

<sup>1</sup> Os limites das ações de ficheiros padrão aplicam-se aos três níveis disponíveis para as ações de ficheiros padrão: transação otimizada, quente e fria.

<sup>2</sup> O predefinição nas ações de ficheiros padrão é de 5 TiB, ver [Enable e criar grandes ações de ficheiros](./storage-files-how-to-create-large-file-share.md) para os detalhes sobre como aumentar a escala de ações de ficheiros padrão até 100 TiB.

### <a name="file-scale-targets"></a>Alvos de escala de ficheiros
| Atributo | Ficheiros em ações de ficheiros padrão  | Ficheiros em ações de ficheiros premium  |
|-|-|-|
| Tamanho máximo do ficheiro | 4 TiB | 4 TiB |
| Taxa máxima de pedido simultânea | 1.000 OPS | Até 8.000<sup>1</sup> |
| Entrada máxima para um ficheiro | 60 MiB/seg | 200 MiB/seg (Até 1 GiB/s com pré-visualização SMB Multicanal)<sup>2</sup>|
| Saída máxima para um ficheiro | 60 MiB/seg | 300 MiB/seg (Até 1 GiB/s com pré-visualização SMB Multicanal)<sup>2</sup> |
| Pegas simultâneas máximas | 2.000 pegas | 2.000 pegas  |

<sup>1 Aplica-se à leitura e à escrita de IOs (tamanhos de IO tipicamente menores ou iguais a 64 KiB). As operações de metadados, para além de leituras e escritos, podem ser mais baixas.</sup> 
 <sup>2 Sujeito a limites de rede de máquinas, largura de banda disponível, tamanhos de IO, profundidade de fila e outros fatores. Para mais detalhes consulte o [desempenho do SMB Multicannel](./storage-files-smb-multichannel-performance.md).</sup>

## <a name="azure-file-sync-scale-targets"></a>Alvos de dimensionamento do Azure File Sync
O quadro seguinte indica os limites dos testes da Microsoft e indica também quais os alvos que são limites rígidos:

| Recurso | Destino | Limite rígido |
|----------|--------------|------------|
| Serviços de Sincronização de Armazenamento por região | 100 Serviços de Sincronização de Armazenamento | Yes |
| Grupos sincronizados por serviço de sincronização de armazenamento | 200 grupos de sincronização | Yes |
| Servidores registados por Serviço de Sincronização de Armazenamento | 99 servidores | Yes |
| Pontos finais da nuvem por grupo de sincronização | 1 ponto final de nuvem | Yes |
| Pontos finais do servidor por grupo de sincronização | 100 pontos finais do servidor | Yes |
| Pontos finais do servidor por servidor | 30 pontos finais do servidor | Yes |
| Objetos do sistema de ficheiros (diretórios e ficheiros) por grupo de sincronização | 100 milhões de objetos | No |
| Número máximo de objetos do sistema de ficheiros (diretórios e ficheiros) num diretório | 5 milhões de objetos | Yes |
| Tamanho máximo do descritor de segurança de objetos (diretórios e ficheiros) | 64 KiB | Yes |
| Tamanho dos ficheiros | 100 GiB | No |
| Tamanho mínimo do ficheiro para um ficheiro a ser nivelado | V9 e mais recente: Baseado no tamanho do cluster do sistema de ficheiros (tamanho do cluster do sistema de ficheiros duplos). Por exemplo, se o tamanho do cluster do sistema de ficheiros for de 4 KiB, o tamanho mínimo do ficheiro será de 8 KiB.<br> V8 e mais velho: 64 KiB  | Yes |

> [!Note]  
> Um ponto final Azure File Sync pode escalar até ao tamanho de uma partilha de ficheiros Azure. Se o limite de tamanho da ação do ficheiro Azure for atingido, a sincronização não poderá funcionar.

### <a name="azure-file-sync-performance-metrics"></a>Métricas de desempenho do Azure File Sync
Uma vez que o agente Azure File Sync funciona numa máquina do Windows Server que se conecta às partilhas de ficheiros Azure, o desempenho sincronizado eficaz depende de uma série de fatores na sua infraestrutura: Windows Server e a configuração subjacente do disco, largura de banda de rede entre o servidor e o armazenamento do Azure, tamanho do ficheiro, tamanho total do conjunto de dados e a atividade no conjunto de dados. Uma vez que o Azure File Sync funciona ao nível do ficheiro, as características de desempenho de uma solução baseada em Azure File Sync são melhor medidas no número de objetos (ficheiros e diretórios) processados por segundo.

Para Azure File Sync, o desempenho é fundamental em duas fases:

1. **Provisão inicial:** Para otimizar o desempenho no provisionamento inicial, consulte o [Onboarding com Azure File Sync](storage-sync-files-deployment-guide.md#onboarding-with-azure-file-sync) para obter os detalhes de implementação ideais.
2. **Sincronização em curso**: Depois de os dados terem sido inicialmente semeados nas ações de ficheiros Azure, o Azure File Sync mantém vários pontos finais sincronizados.

Para ajudá-lo a planear a sua implantação para cada uma das fases, abaixo estão os resultados observados durante os testes internos num sistema com uma config

| Configuração do sistema | Detalhes |
|-|-|
| CPU | 64 Núcleos Virtuais com cache MiB L3 64 |
| Memória | 128 GiB |
| Disco | Discos SAS com RAID 10 com cache apoiado por bateria |
| Rede | Rede 1 Gbps |
| Carga de trabalho | Servidor de ficheiros de finalidade geral|

| Provisão única inicial  | Detalhes |
|-|-|
| Número de objetos | 25 milhões de objetos |
| Tamanho do conjunto de dados| ~4.7 TiB |
| Tamanho médio do ficheiro | ~200 KiB (Maior Arquivo: 100 GiB) |
| Enumeração inicial da alteração da nuvem | 20 objetos por segundo  |
| Envio de produção | 20 objetos por segundo por grupo de sincronização |
| Transfer para o Espaço Nome | 400 objetos por segundo |

### <a name="initial-one-time-provisioning"></a>Provisão única inicial

**Enumeração inicial da mudança de nuvem**: Quando um novo grupo de sincronização é criado, a enumeração inicial da mudança de nuvem é o primeiro passo que irá executar. Neste processo, o sistema irá enumerar todos os itens na Azure File Share. Durante este processo, não haverá atividade sincronizada, ou seja, nenhum itens será descarregado do ponto final da nuvem para o ponto final do servidor e nenhum itens será carregado do ponto final do servidor para o ponto final da nuvem. A atividade de sincronização será retomada assim que a enumeração inicial da alteração da nuvem estiver concluída.
A taxa de desempenho é de 20 objetos por segundo. Os clientes podem estimar o tempo que levará para completar a enumeração inicial da alteração da nuvem, determinando o número de itens na partilha de nuvem e usando as seguintes fórmulas para obter o tempo em dias. 

   **Tempo (em dias) para a enumeração inicial da nuvem = (Número de objetos no ponto final da nuvem)/((20 * 60 * 60 * 24)**

**A sincronização inicial de dados do Windows Server para a Azure File share**:Muitas implementações de Azure File Sync começam com uma partilha de ficheiros Azure vazia porque todos os dados estão no Servidor do Windows. Nestes casos, a enumeração inicial da mudança de nuvem é rápida e a maior parte do tempo será gasto sincronizando alterações do Servidor do Windows para a partilha de ficheiros Azure. 

Embora o sync carrequiva os dados para a partilha de ficheiros Azure, não há tempo de inatividade no servidor de ficheiros local, e os administradores podem [configurar limites](./storage-sync-files-server-registration.md#set-azure-file-sync-network-limits) de rede para restringir a quantidade de largura de banda utilizada para o upload de dados de fundo.

A sincronização inicial é tipicamente limitada pela taxa inicial de upload de 20 ficheiros por segundo por grupo de sincronização. Os clientes podem estimar o momento de enviar todos os seus dados para a Azure usando as seguintes fórmulas para obter tempo em dias:  

   **Tempo (em dias) para o upload de ficheiros para um grupo de sincronização = (Número de objetos no ponto final do servidor)/((20 * 60 * 60 * 24)**

Dividir os seus dados em vários pontos finais do servidor e grupos de sincronização pode acelerar este upload inicial de dados, porque o upload pode ser feito em paralelo para vários grupos de sincronização a uma taxa de 20 itens por segundo cada. Assim, dois grupos de sincronização estariam a funcionar a uma taxa combinada de 40 itens por segundo. O tempo total para completar seria a estimativa de tempo para o grupo de sincronização com a maioria dos ficheiros sincronizar.

**Produção de download de namespace** Quando um novo ponto final do servidor é adicionado a um grupo de sincronização existente, o agente Azure File Sync não descarrega nenhum dos conteúdos do ficheiro a partir do ponto final da nuvem. Primeiro sincroniza o espaço de nome completo e, em seguida, desencadeia a chamada de fundo para descarregar os ficheiros, quer na sua totalidade, quer, se o tiering da nuvem estiver ativado, para a política de tiering de nuvem definida no ponto final do servidor.

| Sincronização em curso  | Detalhes  |
|-|--|
| Número de objetos sincronizados| 125.000 objetos (~1% churn) |
| Tamanho do conjunto de dados| 50 GiB |
| Tamanho médio do ficheiro | ~500 KiB |
| Envio de produção | 20 objetos por segundo por grupo de sincronização |
| Download Completo Do Rendimento* | 60 objetos por segundo |

*Se o tiering da nuvem estiver ativado, é provável que observe um melhor desempenho, uma vez que apenas alguns dos dados do ficheiro são descarregados. O Azure File Sync apenas descarrega os dados dos ficheiros em cache quando são alterados em qualquer um dos pontos finais. Para quaisquer ficheiros hierárquicos ou recém-criados, o agente não descarrega os dados do ficheiro e, em vez disso, apenas sincroniza o espaço de nomes em todos os pontos finais do servidor. O agente também suporta downloads parciais de ficheiros hierárquicos à medida que são acedidos pelo utilizador. 

> [!Note]  
> Os números acima não são uma indicação do desempenho que irá experimentar. O desempenho real dependerá de múltiplos fatores, tal como descritos no início desta secção.

Como guia geral para a sua implantação, deve ter em mente algumas coisas:

- O produto do objeto escala aproximadamente proporcionalmente ao número de grupos de sincronização no servidor. Dividir dados em vários grupos de sincronização num servidor produz melhor produção, que também é limitada pelo servidor e pela rede.
- A produção do objeto é inversamente proporcional à produção mib por segundo. Para ficheiros mais pequenos, experimentará uma produção mais elevada em termos do número de objetos processados por segundo, mas menor MiB por segundo. Inversamente, para ficheiros maiores, obterá menos objetos processados por segundo, mas mais alto MiB por segundo. A produção miB por segundo é limitada pelos alvos de escala de Ficheiros Azure.

## <a name="see-also"></a>Ver também
- [Planear uma implementação de Ficheiros do Azure](storage-files-planning.md)
- [Planear uma implementação do Azure File Sync](storage-sync-files-planning.md)