---
title: Compreenda a faturação dos Ficheiros Azure | Microsoft Docs
description: Saiba como interpretar os modelos de faturação a provisionados e pay-as-you-go para ações de ficheiros Azure.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 01/27/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 11d22fd83106bb1802514d0c7d5f67724664464d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788390"
---
# <a name="understand-azure-files-billing"></a>Compreenda a faturação dos Ficheiros Azure
A Azure Files fornece dois modelos de faturação distintos: provisionados e pay-as-you-go. O modelo provisionado só está disponível para ações de ficheiros premium, que são ações de ficheiros implantadas no tipo de conta de armazenamento **FileStorage.** O modelo pay-as-you-go só está disponível para ações de ficheiros padrão, que são ações de ficheiros implantadas no tipo de conta de armazenamento **versão 2 (GPv2) para fins gerais.** Este artigo explica como ambos os modelos funcionam de forma a ajudá-lo a entender a sua conta mensal de Ficheiros Azure.

Para obter informações sobre preços dos ficheiros Azure, consulte [a página de preços do Azure Files](https://azure.microsoft.com/pricing/details/storage/files/).

## <a name="storage-units"></a>Unidades de armazenamento    
A Azure Files utiliza unidades de medição base-2 para representar a capacidade de armazenamento: KiB, MiB, GiB e TiB. O seu sistema operativo pode ou não utilizar a mesma unidade de medição ou sistema de contagem.

### <a name="windows"></a>Windows

Tanto o sistema operativo Windows como o Azure Files medem a capacidade de armazenamento utilizando o sistema de contagem base-2, mas há uma diferença na rotulagem das unidades. A Azure Files rotula a sua capacidade de armazenamento com unidades de medição base-2, enquanto o Windows rotula a sua capacidade de armazenamento em unidades de medição base-10. Ao reportar a capacidade de armazenamento, o Windows não converte a sua capacidade de armazenamento de base-2 para base-10.

|Acrónimo  |Definição  |Unidade  |Windows apresenta como  |
|---------|---------|---------|---------|
|KiB     |1.024 bytes         |kibibyte         |KB (kilobyte)         |
|MiB     |1.024 KiB (1.048.576 bytes)         |mebibyte         |MB (megabyte)         |
|GiB     |1024 MiB (1.073.741.824 bytes)         |gibibyte         |GB (gigabyte)         |
|TiB     |1024 GiB (1.099.511.627.776 bytes)         |tebibyte         |Tuberculose (terabyte)         |

### <a name="macos"></a>macOS

Veja [como o iOS e o macOS reportam a capacidade de armazenamento](https://support.apple.com/HT201402) no site da Apple para determinar qual o sistema de contagem usado.

### <a name="linux"></a>Linux

Um sistema de contagem diferente poderia ser usado por cada sistema operativo ou peça de software individual. Consulte a documentação deles para determinar como reportam a capacidade de armazenamento.

## <a name="provisioned-model"></a>Modelo provisionado
A Azure Files utiliza um modelo provisionado para ações de ficheiros premium. Num modelo de negócio a provisionado, especifica proativamente ao serviço Azure Files quais são os seus requisitos de armazenamento, em vez de ser faturado com base no que utiliza. Isto é semelhante à compra de hardware no local, na medida em que quando fornece uma parte de ficheiro Azure com uma certa quantidade de armazenamento, você paga por esse armazenamento independentemente de usá-lo ou não, assim como você não começa a pagar os custos de meios físicos no local quando você começa a usar o espaço. Ao contrário da compra de meios físicos no local, as ações de ficheiros a provisionadas podem ser dinamicamente dimensionadas para cima ou para baixo, dependendo do seu armazenamento e características de desempenho de IO.

Ao providenciar uma parte de ficheiro premium, especifica quantos GiBs a sua carga de trabalho requer. Cada GiB que fornece dá-lhe direito a IOPS adicional e produção numa relação fixa. Além do IOPS de base para o qual está garantido, cada partilha de ficheiros premium suporta rebentar com uma base de esforço melhor. As fórmulas para iops e produção são as seguintes:

- IOPS de base = 400 + 1 * GiB provisionado. (Até um máximo de 100.000 IOPS).
- Limite de explosão = MAX (4000, 3 * PIOS de base).
- Taxa de Egress = 60 MiB/s + 0,06 * GiB a provisionado.
- Taxa de entrada = 40 MiB/s + 0,04 * GiB a provisionado.

O tamanho previsto da parte do ficheiro pode ser aumentado a qualquer momento, mas só pode ser diminuído após 24 horas desde o último aumento. Depois de esperar 24 horas sem um aumento de quota, pode diminuir a quota de ações quantas vezes quiser, até que a aumente novamente. As alterações na escala iops/de produção serão eficazes dentro de poucos minutos após a alteração do tamanho previsto.

É possível diminuir o tamanho da sua parte abaixo do seu GiB usado. Se o fizer, não perderá dados mas, ainda será cobrado pelo tamanho utilizado e receberá o desempenho (IOPS de base, produção e IOPS rebentado) da parte atada, e não do tamanho utilizado.

A tabela a seguir ilustra alguns exemplos destas fórmulas para as dimensões das ações previstas:

|Capacidade (GiB) | IOPS de linha de base | IOPS de explosão | Egress (MiB/s) | Ingress (MiB/s) |
|---------|---------|---------|---------|---------|
|100         | 500     | Até 4.000     | 66   | 44   |
|500         | 900     | Até 4.000  | 90   | 60   |
|1,024       | 1,424   | Até 4.000   | 122   | 81   |
|5,120       | 5,520   | Até 15.360  | 368   | 245   |
|10,240      | 10,640  | Até 30.720  | 675   | 450   |
|33,792      | 34,192  | Até 100.000 | 2,088 | 1,392   |
|51,200      | 51,600  | Até 100.000 | 3,132 | 2,088   |
|102,400     | 100.000 | Até 100.000 | 6,204 | 4,136   |

O desempenho efetivo da partilha de ficheiros está sujeito a limites de rede de máquinas, largura de banda de rede disponível, tamanhos de IO, paralelismo, entre muitos outros fatores. Por exemplo, com base em testes internos com tamanhos IO de leitura/escrita de 8 KiB, uma única máquina virtual Windows sem O Multicanal SMB ativada, *Standard F16s_v2,* ligada à partilha de ficheiros premium sobre SMB poderia alcançar 20K ler IOPS e 15K escrever IOPS. Com 512 tamanhos de IO de leitura/escrita MiB, o mesmo VM poderia alcançar 1.1 GiB/s e 370 MiB/s ingress produção. O mesmo cliente pode alcançar até \~ 3x de desempenho se o SMB Multichannel estiver ativado nas ações premium. Para atingir a escala máxima de desempenho, [ative o SMB Multicanal](storage-files-enable-smb-multichannel.md) e espalhe a carga por vários VMs. Consulte o [guia de desempenho multicanal](storage-files-smb-multichannel-performance.md) e [resolução de problemas](storage-troubleshooting-files-performance.md) SMB para alguns problemas de desempenho comuns e soluções alternativas.

### <a name="bursting"></a>Estourando
Se a sua carga de trabalho necessitar do desempenho extra para satisfazer a procura máxima, a sua parte pode usar créditos de rutura para ir acima do limite de IOPS de linha de base para oferecer o desempenho de partilha que precisa para satisfazer a procura. As ações de ficheiros premium podem rebentar o seu IOPS até 4.000 ou até um fator de três, o que for um valor mais elevado. A explosão é automatizada e funciona com base num sistema de crédito. A explosão funciona com uma melhor base de esforço e o limite de explosão não é uma garantia, as ações de ficheiro podem rebentar *até ao* limite por uma duração máxima de 60 minutos.

Os créditos acumulam-se num balde de rutura sempre que o tráfego para a sua parte de ficheiro está abaixo do IOPS de base. Por exemplo, uma quota de 100 GiB tem 500 IOPS de base. Se o tráfego real na parte foi de 100 IOPS para um intervalo específico de 1 segundo, então os 400 IOPS não reutilizados são creditados a um balde de explosão. Da mesma forma, uma quota de TiB ociosa 1, acumula crédito rebentado em 1.424 IOPS. Estes créditos serão então utilizados mais tarde quando as operações excederem o IOPS de base.

Sempre que uma ação exceder o IOPS de base e tiver créditos num balde de rutura, irá rebentar na taxa máxima de pico permitida. As ações podem continuar a rebentar enquanto os créditos permanecerem, até ao máximo de 60 minutos de duração, mas isso baseia-se no número de créditos acumulados. Cada IO para além do IOPS de base consome um crédito e uma vez consumidos todos os créditos, a parte regressaria ao IOPS de base.

Os créditos de ações têm três estados:

- Acumulando, quando a parte do ficheiro está a usar menos do que o IOPS de base.
- Em declínio, quando a partilha de ficheiros está a utilizar mais do que o IOPS de base e no modo de rebentamento.
- Constante, quando a partilha de ficheiros está a usar exatamente o IOPS de base, ou não existem créditos acumulados ou utilizados.

As novas ações de ficheiros começam com o número total de créditos no seu balde de rebentamento. Os créditos de rutura não serão acumulados se o IOPS de ações cair abaixo do IOPS de base devido ao estrangulamento pelo servidor.

## <a name="pay-as-you-go-model"></a>Modelo pay-as-you-go
A Azure Files usa um modelo de negócio pay-as-you-go para ações de ficheiros padrão. Num modelo de negócio pay-as-you-go, o valor que paga é determinado pelo valor que realmente utiliza, em vez de se basear num valor provisitado. A um nível elevado, você paga um custo pela quantidade de dados armazenados no disco, e, em seguida, um conjunto adicional de transações com base no seu uso desses dados. Um modelo pay-as-you-go pode ser rentável, porque não precisa de uma visão excessiva para ter em conta os requisitos futuros de crescimento ou desempenho ou desprovisionamento se a sua carga de trabalho for a pegada de dados varia ao longo do tempo. Por outro lado, um modelo pay-as-you-go também pode ser difícil de planear como parte de um processo de orçamentação, porque o modelo de faturação pay-as-you-go é impulsionado pelo consumo de utilizador final.

### <a name="differences-in-standard-tiers"></a>Diferenças nos níveis padrão
Quando cria uma partilha de ficheiros padrão, escolhe entre os níveis otimizados, quentes e frescos da transação. Todos os três níveis são armazenados no mesmo hardware de armazenamento padrão. A principal diferença para estes três níveis são os seus preços de armazenamento de dados no descanso, que são mais baixos nos níveis mais frios, e os preços de transação, que são mais elevados nos escalões mais frios. Isto significa:

- A transação otimizada, como o nome indica, otimiza o preço das elevadas cargas de trabalho de transação. A transação otimizada tem os dados mais elevados no preço de armazenamento de repouso, mas os preços de transação mais baixos.
- Hot é para cargas de trabalho ativas que não envolvem um grande número de transações, e tem um preço de armazenamento de dados ligeiramente mais baixo no descanso, mas preços de transação ligeiramente mais elevados em comparação com transações otimizadas. Pense nisso como o meio termo entre os níveis otimizados e frescos da transação.
- Cool otimiza o preço para cargas de trabalho que não têm muita atividade, oferecendo os dados mais baixos no preço de armazenamento de repouso, mas os preços de transação mais elevados.

Se colocar uma carga de trabalho pouco frequentemente acedida no nível otimizado de transações, não pagará quase nada pelas poucas vezes num mês que efetua transações contra a sua parte, mas pagará um valor elevado pelos custos de armazenamento de dados. Se você movesse esta mesma parte para o nível cool, você ainda pagaria quase nada pelos custos de transação, simplesmente porque você está fazendo transações muito raramente para esta carga de trabalho, mas o nível legal tem um preço de armazenamento de dados muito mais barato. A seleção do nível adequado para o seu caso de utilização permite-lhe reduzir consideravelmente os seus custos. A seleção do nível adequado para o seu caso de utilização permite-lhe reduzir consideravelmente os seus custos.

Da mesma forma, se colocar uma carga de trabalho altamente acedida no nível legal, pagará muito mais em custos de transação, mas menos pelos custos de armazenamento de dados. Isto pode levar a uma situação em que o aumento dos custos dos preços de transação supera as poupanças do preço de armazenamento de dados diminuído, levando-o a pagar mais dinheiro em cool do que teria em transações otimizadas. É possível para alguns níveis de utilização que, embora o nível quente seja o nível mais eficiente em termos de custos, o nível cool será mais caro do que a transação otimizada.

A sua carga de trabalho e nível de atividade determinará o nível mais eficiente em termos de custos para a sua quota de ficheiro padrão. Na prática, a melhor forma de escolher o nível mais eficiente em termos de custos passa por olhar para o consumo real de recursos da parte (dados armazenados, transações de escrita, etc.).

### <a name="what-are-transactions"></a>O que são transações?
As transações são operações ou pedidos contra ficheiros Azure para carregar, transferir ou manipular o conteúdo da partilha de ficheiros. Todas as ações tomadas numa ação de ficheiro traduzem-se numa ou mais transações, e em ações padrão que utilizam o modelo de faturação pay-as-you-go, que se traduz em custos de transação.

Existem cinco categorias básicas de transações: escrever, listar, ler, outras, e excluir. Todas as operações efetuadas através da API ou SMB rest são colocadas numa destas 4 categorias da seguinte forma:

| Tipo de operação | Escrever transações | Lista de transações | Ler transações | Outras transações | Eliminar transações |
|-|-|-|-|-|-|
| Operações de gestão | <ul><li>`CreateShare`</li><li>`SetFileServiceProperties`</li><li>`SetShareMetadata`</li><li>`SetShareProperties`</li></ul> | <ul><li>`ListShares`</li></ul> | <ul><li>`GetFileServiceProperties`</li><li>`GetShareAcl`</li><li>`GetShareMetadata`</li><li>`GetShareProperties`</li><li>`GetShareStats`</li></ul> | | <ul><li>`DeleteShare`</li></ul> |
| Operações de dados | <ul><li>`CopyFile`</li><li>`Create`</li><li>`CreateDirectory`</li><li>`CreateFile`</li><li>`PutRange`</li><li>`PutRangeFromURL`</li><li>`SetDirectoryMetadata`</li><li>`SetFileMetadata`</li><li>`SetFileProperties`</li><li>`SetInfo`</li><li>`SetShareACL`</li><li>`Write`</li><li>`PutFilePermission`</li></ul> | <ul><li>`ListFileRanges`</li><li>`ListFiles`</li><li>`ListHandles`</li></ul>  | <ul><li>`FilePreflightRequest`</li><li>`GetDirectoryMetadata`</li><li>`GetDirectoryProperties`</li><li>`GetFile`</li><li>`GetFileCopyInformation`</li><li>`GetFileMetadata`</li><li>`GetFileProperties`</li><li>`QueryDirectory`</li><li>`QueryInfo`</li><li>`Read`</li><li>`GetFilePermission`</li></ul> | <ul><li>`AbortCopyFile`</li><li>`Cancel`</li><li>`ChangeNotify`</li><li>`Close`</li><li>`Echo`</li><li>`Ioctl`</li><li>`Lock`</li><li>`Logoff`</li><li>`Negotiate`</li><li>`OplockBreak`</li><li>`SessionSetup`</li><li>`TreeConnect`</li><li>`TreeDisconnect`</li><li>`CloseHandles`</li><li>`AcquireFileLease`</li><li>`BreakFileLease`</li><li>`ChangeFileLease`</li><li>`ReleaseFileLease`</li></ul> | <ul><li>`ClearRange`</li><li>`DeleteDirectory`</li></li>`DeleteFile`</li></ul> |

> [!Note]  
> O NFS 4.1 só está disponível para ações de ficheiros premium, que utilizam o modelo de faturação previsto, as transações não afetam a faturação das ações de ficheiros premium.

## <a name="see-also"></a>Ver também
- [Página de preços do Azure Files](https://azure.microsoft.com/pricing/details/storage/files/).
- Planejando uma implementação e planeamento [de ficheiros Azure](storage-files-planning.md) [para uma implementação de Azure File Sync](../file-sync/file-sync-planning.md).
- [Crie uma partilha de ficheiros](storage-how-to-create-file-share.md) e [implemente o Sync de Ficheiros Azure](../file-sync/file-sync-deployment-guide.md).