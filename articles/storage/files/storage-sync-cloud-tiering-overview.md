---
title: Compreenda o nível de nivelamento da nuvem de sincronização de ficheiros Azure | Microsoft Docs
description: Compreenda o tiering de nuvem, uma funcionalidade opcional de Sincronização de Ficheiros Azure. Os ficheiros acedidos frequentemente são cached localmente no servidor; outros são hierarquizados para Ficheiros Azure.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 1/4/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: dcd58e966da7ca596a14ca1b2839cbeb6399a855
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104576463"
---
# <a name="cloud-tiering-overview"></a>Visão geral do tiering da nuvem
O tiering em nuvem, uma característica opcional do Azure File Sync, diminui a quantidade de armazenamento local necessária mantendo o desempenho de um servidor de ficheiros no local.

Quando ativado, esta funcionalidade armazena apenas frequentemente ficheiros (quentes) no servidor local. Os ficheiros pouco frequentemente acedidos (cool) são divididos em espaço de nome (estrutura de ficheiros e pastas) e conteúdos de ficheiros. O espaço de nome é armazenado localmente e o conteúdo do ficheiro armazenado numa partilha de ficheiros Azure na nuvem. 

Quando um utilizador abre um ficheiro hierárquico, o Azure File Sync recorda perfeitamente os dados do ficheiro da partilha de ficheiros no Azure.

## <a name="how-cloud-tiering-works"></a>Como funciona o tiering de nuvens

### <a name="cloud-tiering-policies"></a>Políticas de nivelamento em nuvem
Quando ativa o tiering de nuvens, existem duas políticas que pode definir para informar o Azure File Sync quando deve preencher ficheiros cool: a **política de espaço livre** de volume e a política de **datas**. 

#### <a name="volume-free-space-policy"></a>Política de espaço livre de volume
A **política de espaço livre** de volume diz ao Azure File Sync para nivelar os ficheiros para a nuvem quando uma certa quantidade de espaço é ocupado no seu disco local. 

Por exemplo, se a sua capacidade de disco local for de 200 GB e quiser que pelo menos 40 GB da sua capacidade de disco local permaneça sempre livre, deve definir a política de espaço livre de volume para 20%. O espaço livre de volume aplica-se ao nível do volume e não ao nível de diretórios individuais ou pontos finais do servidor. 

Para saber como a política de espaço livre de volume afeta os ficheiros inicialmente descarregados quando um novo ponto final do servidor é adicionado (ver as políticas de Sincronização que afetam o [tiering da nuvem).](#sync-policies-that-affect-cloud-tiering)

#### <a name="date-policy"></a>Política de data
Com a política de **data,** os ficheiros cool são hierárquicos para a nuvem se não tiverem sido acedidos (isto é, lidos ou escritos) durante x número de dias. Por exemplo, se notou que os ficheiros que passaram mais de 15 dias sem serem acedidos são normalmente ficheiros de arquivo, deverá definir a sua política de data para 15 dias. 

Para obter mais exemplos sobre como a política de data e a política de espaço livre de volume funcionam em conjunto, consulte [as políticas de tiering de nuvem de sincronização de ficheiros Azure.](storage-sync-choose-cloud-tiering-policies.md)

### <a name="windows-server-data-deduplication"></a>Deduplica de dados do Windows Server
A desduplicação de dados é suportada em volumes que tenham o tiering de nuvem ativado a partir do Windows Server 2016. Para mais detalhes, consulte [o Planeamento para uma implementação do Azure File Sync](./storage-sync-files-planning.md#data-deduplication).

### <a name="cloud-tiering-heatmap"></a>Mapa de calor de camadas de nuvem
O Azure File Sync monitoriza o acesso ao ficheiro (ler e escrever operações) ao longo do tempo e, com base na frequência e no acesso recente, atribui uma pontuação de calor a cada ficheiro. Utiliza estas pontuações para construir um "mapa de calor" do seu espaço de identificação em cada ponto final do servidor. Este mapa de calor é uma lista de todos os ficheiros sincronizados num local com nivelamento de nuvem ativado, ordenado pela sua pontuação de calor. Os ficheiros frequentemente acedidos que foram recentemente abertos são considerados quentes, enquanto ficheiros que mal foram tocados e que não foram acedidos há algum tempo são considerados fixes. 

Para determinar a posição relativa de um ficheiro individual nesse mapa térmico, o sistema utiliza o máximo dos seus relógios, na seguinte ordem: MAX (Última Hora de Acesso, Última Hora Modificada, Tempo de Criação). 

Normalmente, o último tempo de acesso é rastreado e disponível. No entanto, quando um novo ponto final do servidor é criado, com o tiering da nuvem ativado, não passou tempo suficiente para observar o acesso ao ficheiro. Se não houver uma última vez de acesso válida, o último tempo modificado é utilizado para avaliar a posição relativa no mapa de calor.  

A política da data funciona da mesma forma. Sem uma última hora de acesso, a política de datas atuará no último tempo modificado. Se isso não estiver disponível, voltará a ser criado tempo de um ficheiro. Com o tempo, o sistema irá observar cada vez mais pedidos de acesso a ficheiros e automaticamente começar a utilizar a última vez de acesso auto-rastreado.

> [!Note]
> O tiering em nuvem não depende da funcionalidade NTFS para rastrear a última vez de acesso. Esta funcionalidade NTFS está desligada por padrão e devido a considerações de desempenho, não recomendamos que ative manualmente esta funcionalidade. Faixas de nivelamento de nuvem última vez de acesso separadamente.

### <a name="sync-policies-that-affect-cloud-tiering"></a>Sync políticas que afetam o tiering da nuvem

Com a versão 11 do agente Azure File Sync, existem duas políticas adicionais de Azure File Sync que pode definir que afetam o tiering da nuvem: **download inicial** e **recolha proativa**.

#### <a name="initial-download"></a>Download inicial

Quando um servidor está a ligar-se a uma partilha de ficheiros Azure com ficheiros nele, pode agora decidir como pretende que o servidor descarregue inicialmente os dados da partilha de ficheiros. Quando o nível da nuvem está ativado, tem duas opções. 

![Uma imagem de download inicial quando o tiering da nuvem está ativado](media/storage-sync-cloud-tiering-overview/cloud-tiering-overview-3.png)  

A primeira opção é recordar primeiro o espaço de nome e, em seguida, recordar o conteúdo do ficheiro por última marca de tempo modificada, até que a capacidade do disco local seja alcançada. Se tiver espaço suficiente em disco e souber que os ficheiros que são modificados pela última vez devem ser em cache localmente, esta opção é ideal. Os ficheiros que não possam ser armazenados localmente devido à falta de espaço em disco serão hierárquicos.        

A segunda opção é, inicialmente, recordar apenas o espaço de nome e recordar o conteúdo do ficheiro apenas quando acedido. Esta opção é melhor se pretender minimizar a capacidade utilizada no disco local e pretender que os utilizadores decidam quais os ficheiros que devem ser em cache localmente. Todos os ficheiros começarão como ficheiros hierárquicos com esta opção.

![Uma imagem de download inicial quando o tiering da nuvem é desativado](media/storage-sync-cloud-tiering-overview/cloud-tiering-overview-4.png)

Quando o nível da nuvem é desativado, tem uma terceira opção, que é evitar ficheiros hierárquicos todos juntos. Nesta situação, os ficheiros só aparecerão no servidor uma vez descarregados na totalidade. Se tiver aplicações que exijam a presença de ficheiros completos e que não possam tolerar ficheiros hierárquicos no seu espaço de nome, este é o ideal.      

#### <a name="proactive-recalling"></a>Recordação proativa

Quando um ficheiro é criado ou modificado, pode relembrativamente um ficheiro para servidores que especifique. Isto torna o ficheiro novo ou modificado prontamente disponível para consumo em cada servidor especificado. 

Por exemplo, uma empresa globalmente distribuída tem sucursais nos EUA e na Índia. De manhã (hora dos EUA) os trabalhadores da informação criam uma nova pasta e novos ficheiros para um novo projeto e trabalham o dia todo nele. O Azure File Sync sincronizará pastas e ficheiros para a partilha de ficheiros Azure (ponto final da nuvem). Os trabalhadores da informação na Índia continuarão a trabalhar no projeto no seu timezone. Quando chegam de manhã, o servidor local Azure File Sync, na Índia, precisa de ter estes novos ficheiros disponíveis localmente, de modo a que a equipa da Índia possa trabalhar eficientemente a partir de uma cache local. Permitir este modo impede que o acesso inicial do ficheiro seja mais lento devido à recolha a pedido e permite que o servidor relembrá-lo de forma proactiva assim que estes foram alterados ou criados na partilha de ficheiros Azure.

Se os ficheiros recolhidos para o servidor não forem realmente necessários localmente, então a recolha desnecessária pode aumentar o tráfego e os custos da sua saída. Portanto, apenas permitem a recolha proativa quando se sabe que pré-povoar a cache de um servidor com alterações recentes da nuvem terá um efeito positivo nos utilizadores ou aplicações que utilizam os ficheiros desse servidor. 

Permitir a recolha proactiva também pode resultar num maior uso da largura de banda no servidor e pode fazer com que outros conteúdos relativamente novos no servidor local sejam agressivamente nivelados devido ao aumento dos ficheiros serem recolhidos. Por sua vez, isto pode levar a mais chamadas se os ficheiros que estão a ser nivelados forem considerados quentes pelos servidores. 

:::image type="content" source="media/storage-sync-files-deployment-guide/proactive-download.png" alt-text="Uma imagem que mostra o comportamento de descarregamento de ficheiros Azure para um ponto final do servidor atualmente em vigor e um botão para abrir um menu que permite alterá-lo.":::

Para obter mais detalhes sobre a recolha proativa, consulte [implementar o Sync de ficheiros Azure](storage-sync-files-deployment-guide.md#proactively-recall-new-and-changed-files-from-an-azure-file-share).

## <a name="tiered-vs-locally-cached-file-behavior"></a>Tiered vs. comportamento de ficheiros em cache local

O tiering em nuvem é a separação entre o espaço de nome (a hierarquia do ficheiro e da pasta, bem como as propriedades dos ficheiros) e o conteúdo do ficheiro. 

#### <a name="tiered-file"></a>Arquivo hierárquico

Para ficheiros hierárquicos, o tamanho do disco é zero, uma vez que o conteúdo do ficheiro em si não está a ser armazenado localmente. Quando um ficheiro é hierarquizado, o filtro do sistema de ficheiros Azure File Sync (StorageSync.sys) substitui o ficheiro localmente por um ponteiro (ponto de reparse). O ponto de reparse representa um URL para o ficheiro na partilha de ficheiros Azure. Um ficheiro hierárquico tem o atributo "offline" e o atributo FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS definido no NTFS para que as aplicações de terceiros possam identificar de forma segura ficheiros hierárquicos.   

![Uma imagem das propriedades de um ficheiro quando é tiered - apenas espaço de nome.](media/storage-sync-cloud-tiering-overview/cloud-tiering-overview-2.png)    

#### <a name="locally-cached-file"></a>Arquivo em cache local

Por outro lado, para um ficheiro armazenado num servidor de ficheiros no local, o tamanho do disco é quase igual ao tamanho lógico do ficheiro, uma vez que todo o ficheiro (atributos de ficheiro + conteúdo de ficheiro) é armazenado localmente.     

![Uma imagem das propriedades de um ficheiro quando não está nivelada - namespace + conteúdo de ficheiro.](media/storage-sync-cloud-tiering-overview/cloud-tiering-overview-1.png) 

Também é possível que um ficheiro seja parcialmente hierarquizado (ou parcialmente recolhido). Num ficheiro parcialmente hierárquico, parte do ficheiro está no disco. Isto pode ocorrer quando os ficheiros são parcialmente lidos por aplicações como jogadores multimédia ou utilitários zip que suportam o acesso ao streaming de ficheiros. O Azure File Sync é inteligente e recorda apenas as informações solicitadas da partilha de ficheiros Azure conectada.

> [!NOTE]
> O tamanho representa o tamanho lógico do ficheiro. O tamanho do disco representa o tamanho físico do fluxo de ficheiros que está armazenado no disco.

## <a name="next-steps"></a>Passos seguintes
* [Escolha as políticas de tiering de nuvem de sincronização de ficheiros Azure](storage-sync-choose-cloud-tiering-policies.md)
* [Planear uma implementação do Azure File Sync](storage-sync-files-planning.md)