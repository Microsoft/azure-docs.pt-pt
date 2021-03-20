---
title: Visão geral das fotos de partilha para Azure Files | Microsoft Docs
description: Um instantâneo de partilha é uma versão apenas de leitura de uma partilha de Ficheiros Azure que é tirada num ponto do tempo, como uma forma de apoiar a partilha.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 01/17/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: d415ef165da18312a458d7d14fba18acd1bf44cf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "84235606"
---
# <a name="overview-of-share-snapshots-for-azure-files"></a>Descrição geral de partilha de instantâneos de ficheiros do Azure

Os Ficheiros do Azure permitem tirar instantâneos das partilhas de ficheiros. Os instantâneos da partilha capturam o estado da partilha nessa altura. Neste artigo, descrevemos quais as capacidades que os instantâneos de partilha disponibilizam e como pode aproveitá-las no seu caso de utilização personalizada.

## <a name="when-to-use-share-snapshots"></a>Quando utilizar os instantâneos de partilha

### <a name="protection-against-application-error-and-data-corruption"></a>Proteção contra erro na aplicação e danos em dados

As aplicações que utilizam partilhas de ficheiros realizam operações como escrita, leitura, armazenamento, transmissão e processamento. Se uma aplicação estiver mal configurada ou se introduzir um erro não intencional, poderá ocorrer uma substituição acidental ou danos em alguns blocos. Para ajudar a proteger contra estes cenários, pode obter um instantâneo de partilha antes de implementar o novo código da aplicação. Se for introduzido um erro ou um erro da aplicação com a nova implementação, poderá voltar a uma versão anterior dos dados nessa partilha de ficheiros. 

### <a name="protection-against-accidental-deletions-or-unintended-changes"></a>Proteção contra eliminações acidentais ou alterações não intencionais

Imagine que está a trabalhar num ficheiro de texto numa partilha de ficheiros. Depois de o ficheiro de texto estar fechado, perde a capacidade de anular as alterações. Nestes casos, precisa de recuperar uma versão anterior do ficheiro. Poderá utilizar instantâneos de partilha para recuperar versões anteriores do ficheiro se for eliminado ou o nome tiver sido mudado de forma acidental.

### <a name="general-backup-purposes"></a>Finalidades da cópia de segurança geral

Depois de criar uma partilha de ficheiros, pode criar periodicamente um instantâneo da partilha de ficheiros para utilizar para a cópia de segurança de dados. Um instantâneo de partilha, quando criado periodicamente, ajuda a manter versões anteriores de dados que podem servir para futuros requisitos de auditoria ou recuperação após desastre. Recomendamos a utilização de [backup de partilha de ficheiros Azure](../../backup/azure-file-share-backup-overview.md) como solução de backup para tirar e gerir instantâneos. Também pode tirar e gerir as fotos por si mesmo, utilizando cli ou PowerShell.

## <a name="capabilities"></a>Capacidades

Um instantâneo de partilha é uma cópia pontual e só de leitura dos seus dados. Pode criar, excluir e gerir instantâneos utilizando a API REST. As mesmas capacidades também estão disponíveis na biblioteca de clientes, Azure CLI e portal Azure. 

Pode ver instantâneos de uma partilha utilizando tanto a API REST como a SMB. Pode recuperar a lista de versões do diretório ou ficheiro, e pode montar uma versão específica diretamente como unidade (apenas disponível no Windows - ver [Limites).](#limits) 

Após a criação de uma snapshot partilhada, pode ser lida, copiada ou eliminada, mas não modificada. Não pode copiar uma foto inteira para outra conta de armazenamento. Tem de fazer esse ficheiro por ficheiro, utilizando a AzCopy ou outros mecanismos de cópia.

A capacidade de partilha de instantâneos é fornecida ao nível da partilha de ficheiros. A recuperação é fornecida a nível de ficheiros individuais, para permitir a restauração de ficheiros individuais. Pode restaurar uma partilha completa de ficheiros utilizando a API REST, o portal, a biblioteca do cliente ou a ferramenta PowerShell/CLI.

Uma imagem de partilha de uma partilha de ficheiros é idêntica à sua parte de ficheiro base. A única diferença é que um valor **DateTime** é anexado ao URI de ações para indicar o momento em que a snapshot da partilha foi tirada. Por exemplo, se uma URI de partilha de ficheiros for http: \/ /storagesample.core.file.windows.net/myshare, o snapshot de partilha URI é semelhante a:
```
http://storagesample.core.file.windows.net/myshare?snapshot=2011-03-09T01:42:34.9360000Z
```

As imagens de partilha persistem até serem explicitamente eliminadas. Um instantâneo de partilha não pode sobreviver à sua parte de ficheiro base. Pode enumerar as imagens associadas à partilha de ficheiros base para rastrear as suas imagens atuais. 

Quando cria uma imagem de partilha de uma partilha de ficheiros, os ficheiros nas propriedades do sistema da partilha são copiados para o instantâneo de partilha com os mesmos valores. Os ficheiros base e os metadados da partilha de ficheiros também são copiados para o instantâneo de partilha, a menos que especifique metadados separados para a imagem de partilha quando o criar.

Não é possível excluir uma ação que tenha partilha de instantâneos a menos que apague primeiro todas as imagens de partilha.

## <a name="space-usage"></a>Utilização do espaço

As fotos partilhadas são incrementais na natureza. Apenas os dados que mudaram após a sua mais recente partilha instantânea são guardados. Isto minimiza o tempo necessário para criar o instantâneo de partilha e poupa nos custos de armazenamento. Qualquer operação de escrita para o objeto ou operação de atualização de propriedade ou metadados é contada para "conteúdo alterado" e é armazenada no instantâneo de partilha. 

Para conservar o espaço, pode apagar a imagem de partilha para o período em que a agitação foi mais alta.

Apesar de as imagens de partilha serem guardadas de forma incremental, é necessário reter apenas o instantâneo de partilha mais recente para restaurar a partilha. Quando elimina uma imagem partilhada, apenas os dados exclusivos dessa partilha são removidos. As imagens ativas contêm todas as informações necessárias para navegar e restaurar os seus dados (a partir do momento em que a fotografia de partilha foi tirada) para o local original ou para uma localização alternativa. Pode restaurar ao nível do item.

As fotos não contam para o seu limite de 5-TB. Não há limite para a quantidade de imagens de partilha de espaço que ocupam no total. Os limites da conta de armazenamento ainda se aplicam.

## <a name="limits"></a>Limites

O número máximo de imagens de partilha que o Azure Files permite hoje é de 200. Depois de 200 fotos partilhadas, tem de eliminar imagens de partilha mais antigas para criar novas. 

Não há limite para as chamadas simultâneas para a criação de instantâneos de partilha. Não há limite para a quantidade de espaço que partilhe instantâneos de uma determinada partilha de ficheiros que podem consumir. 

Hoje em dia, não é possível montar fotos partilhadas no Linux. Isto porque o cliente Linux SMB não suporta a montagem de instantâneos como o Windows.

## <a name="copying-data-back-to-a-share-from-share-snapshot"></a>Copiar dados de volta para uma partilha a partir do instantâneo de partilha

As operações de cópia que envolvem instantâneos de partilha e ficheiros seguem estas regras:

Pode copiar ficheiros individuais num instantâneo de partilha de ficheiros para a partilha base ou para qualquer outra localização. Pode restaurar uma versão anterior de um ficheiro ou restaurar a partilha de ficheiros completa ao copiar ficheiro a ficheiro a partir do instantâneo de partilha. O instantâneo de partilha não é promovido para a partilha base. 

O instantâneo de partilha permanece intacto após a cópia, mas a partilha de ficheiros base é substituída por uma cópia dos dados que estavam disponíveis no instantâneo de partilha. Todos os ficheiros restaurados contam para o “conteúdo alterado”.

Pode copiar um ficheiro num instantâneo de partilha para um destino diferente com um nome diferente. O ficheiro de destino resultante é um ficheiro gravável e não um instantâneo de partilha. Neste caso, a partilha de ficheiros base permanecerá intacta.

Quando um ficheiro de destino é substituído por uma cópia, todos os instantâneos de partilha associados ao ficheiro de destino original permanecem intactos.

## <a name="general-best-practices"></a>Melhores práticas gerais

Recomendamos a utilização de [backup de partilha de ficheiros Azure](../../backup/azure-file-share-backup-overview.md) como solução de backup para automatizar fotografias, bem como gerir instantâneos. Quando estiver a executar infraestruturas no Azure, automatize cópias de segurança para recuperação de dados sempre que possível. As ações automatizadas são mais fiáveis do que os processos manuais, ajudando a melhorar a proteção e a recuperabilidade de dados. Pode utilizar a cópia de segurança da partilha de ficheiros Azure, a API REST, a SDK do Cliente ou a script para automação.

Antes de implementar o programador de instantâneos de partilha, considere cuidadosamente as definições de frequência e retenção de imagens partilhadas para evitar incorrer em cargas desnecessárias.

As imagens partilhadas fornecem apenas proteção ao nível do ficheiro. As imagens partilhadas não impedem a eliminação de dedos gordos numa parte de ficheiro ou numa conta de armazenamento. Para ajudar a proteger uma conta de armazenamento de eliminações acidentais, pode [ativar a eliminação suave,](storage-files-prevent-file-share-deletion.md)ou bloquear a conta de armazenamento e/ou o grupo de recursos.

## <a name="next-steps"></a>Passos seguintes
- Trabalhando com fotos partilhadas em:
    - [Backup de partilha de ficheiros Azure](../../backup/azure-file-share-backup-overview.md)
    - [PowerShell](storage-how-to-use-files-powershell.md)
    - [CLI](storage-how-to-use-files-cli.md)
    - [Windows](storage-how-to-use-files-windows.md#accessing-share-snapshots-from-windows)
    - [Partilhar foto instantânea FAQ](storage-files-faq.md#share-snapshots)