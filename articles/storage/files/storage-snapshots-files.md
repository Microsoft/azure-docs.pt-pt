---
title: Visão geral das fotos partilhadas para ficheiros Azure / Microsoft Docs
description: Um instantâneo de partilha é uma versão apenas de uma partilha do Azure Files que é tirada num momento, como forma de apoiar a partilha.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 01/17/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: c05b79d2f1da8076b507ca9ee7a06504de21d5ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72333180"
---
# <a name="overview-of-share-snapshots-for-azure-files"></a>Descrição geral de partilha de instantâneos de ficheiros do Azure 
O Azure Files fornece a capacidade de tirar fotos de partilhas de ações de ficheiros. As imagens de partilha captam o estado de partilha nessa altura. Neste artigo, descrevemos quais as capacidades que as imagens de partilha fornecem e como pode tirar partido delas no seu caso de uso personalizado.

## <a name="when-to-use-share-snapshots"></a>Quando usar instantâneos de partilha

### <a name="protection-against-application-error-and-data-corruption"></a>Proteção contra erro de aplicação e corrupção de dados
As aplicações que utilizam ações de ficheiros realizam operações como escrita, leitura, armazenamento, transmissão e processamento. Se uma aplicação estiver mal configurada ou for introduzida uma falha não intencional, a sobreescrita acidental ou danos podem acontecer a alguns quarteirões. Para ajudar a proteger-se contra estes cenários, pode tirar uma fotografia de partilha antes de implementar um novo código de aplicação. Se for introduzido um erro de erro de erro de erro ou de aplicação com a nova implementação, pode voltar a uma versão anterior dos seus dados nessa partilha de ficheiros. 

### <a name="protection-against-accidental-deletions-or-unintended-changes"></a>Proteção contra supressões acidentais ou alterações não intencionais
Imagina que estás a trabalhar num ficheiro de texto numa partilha de ficheiros. Depois de o ficheiro de texto estar fechado, perde a capacidade de desfazer as suas alterações. Nestes casos, é necessário recuperar uma versão anterior do ficheiro. Pode utilizar imagens de partilha para recuperar versões anteriores do ficheiro se for acidentalmente renomeado ou eliminado.

### <a name="general-backup-purposes"></a>Finalidades gerais de backup
Depois de criar uma partilha de ficheiros, pode criar periodicamente uma imagem de partilha da partilha de ficheiros para a utilizar para cópia de segurança de dados. Um instantâneo de partilha, quando tomado periodicamente, ajuda a manter versões anteriores de dados que podem ser usados para futuros requisitos de auditoria ou recuperação de desastres.

## <a name="capabilities"></a>Capacidades
Uma imagem de partilha é uma cópia pontual dos seus dados. Pode criar, eliminar e gerir instantâneos utilizando a API REST. As mesmas capacidades também estão disponíveis na biblioteca de clientes, Azure CLI e portal Azure. 

Pode ver instantâneos de uma parte utilizando tanto a API REST Como a SMB. Pode recuperar a lista de versões do diretório ou ficheiro, e pode montar uma versão específica diretamente como uma unidade (apenas disponível no Windows - ver [Limites](#limits)). 

Após a criação de um instantâneo de partilha, pode ser lido, copiado ou eliminado, mas não modificado. Não pode copiar uma foto inteira para outra conta de armazenamento. Tem de fazer esse ficheiro por ficheiro, utilizando a AzCopy ou outros mecanismos de cópia.

A capacidade de partilha de instantâneos é fornecida ao nível da partilha de ficheiros. A recuperação é fornecida a nível individual de ficheiros, para permitir a restauração de ficheiros individuais. Pode restaurar uma partilha completa de ficheiros utilizando a SMB, a API REST, o portal, a biblioteca do cliente ou a ferramenta PowerShell/CLI.

Uma imagem de partilha de uma parte de ficheiro é idêntica à sua parte base de ficheiros. A única diferença é que um valor **DateTime** é anexado à ação URI para indicar o momento em que o instantâneo de partilha foi tirado. Por exemplo, se uma http://storagesample.core.file.windows.net/mysharepartilha de ficheiros URI for, o snapshot de partilha URI é semelhante a:
```
http://storagesample.core.file.windows.net/myshare?snapshot=2011-03-09T01:42:34.9360000Z
```

As imagens de partilha persistem até serem explicitamente eliminadas. Um instantâneo de partilha não pode sobreviver à sua parte base de ficheiros. Pode enumerar as imagens associadas à partilha de ficheiros base para rastrear as suas imagens atuais. 

Quando cria uma imagem de partilha de uma partilha de ficheiros, os ficheiros nas propriedades do sistema da partilha são copiados para o instantâneo de partilha com os mesmos valores. Os ficheiros base e os metadados da partilha de ficheiros também são copiados para o instantâneo da partilha, a menos que especifique metadados separados para o instantâneo de partilha quando o criar.

Não é possível eliminar uma parte que tenha imagens partilhadas a não ser que apague primeiro todas as imagens de partilha.

## <a name="space-usage"></a>Uso do espaço 
Partilhar fotos são de natureza incremental. Apenas os dados que mudaram após o seu mais recente instantâneo de partilha são guardados. Isto minimiza o tempo necessário para criar o instantâneo de partilha e poupa nos custos de armazenamento. Qualquer operação de escrita para o objeto ou propriedade ou operação de atualização de metadados é contada para "conteúdo alterado" e é armazenada no instantâneo de partilha. 

Para conservar o espaço, pode eliminar o instantâneo de partilha para o período em que o churn foi mais alto.

Mesmo que as imagens de partilha sejam guardadas de forma incremental, você precisa reter apenas o mais recente instantâneo de partilha para restaurar a parte. Quando elimina um instantâneo de partilha, apenas os dados exclusivos desse instantâneo de partilha são removidos. Os instantâneos ativos contêm toda a informação que precisa para navegar e restaurar os seus dados (a partir do momento em que o instantâneo de partilha foi levado) para a localização original ou para um local alternativo. Pode restaurar ao nível do item.

As fotos não contam para o seu limite de 5 TB. Não há limite para quanto soque fotos de partilha de espaço ocupam no total. Os limites da conta de armazenamento ainda se aplicam.

## <a name="limits"></a>Limites
O número máximo de fotos de partilha que o Azure Files permite hoje é de 200. Depois de 200 fotos partilhadas, tem de eliminar fotografias de partilha mais antigas para criar novas. 

Não há limite para os pedidos simultâneos de criação de imagens partilhadas. Não há limite para a quantidade de espaço que partilhe instantâneos de uma determinada partilha de ficheiros pode consumir. 

Hoje, não é possível montar fotos de partilha no Linux. Isto porque o cliente Linux SMB não suporta imagens de montagem como o Windows.

## <a name="copying-data-back-to-a-share-from-share-snapshot"></a>Copiar dados de volta a uma parte do share snapshot
As operações de cópia que envolvem ficheiros e partilhem instantâneos seguem estas regras:

Pode copiar ficheiros individuais num instantâneo de partilha de ficheiros para a sua parte base ou qualquer outro local. Pode restaurar uma versão anterior de um ficheiro ou restaurar a parte completa do ficheiro copiando o ficheiro por ficheiro a partir do instantâneo da partilha. O instantâneo de partilha não é promovido a parte base. 

O instantâneo da partilha permanece intacto após a cópia, mas a parte do ficheiro base é substituída com uma cópia dos dados disponíveis no instantâneo da partilha. Todos os ficheiros restaurados contam para "conteúdo alterado".

Pode copiar um ficheiro numa fotografia partilhada para um destino diferente com um nome diferente. O ficheiro de destino resultante é um ficheiro writável e não um instantâneo de partilha. Neste caso, a sua parte de ficheiro base permanecerá intacta.

Quando um ficheiro de destino é substituído com uma cópia, quaisquer instantâneos de partilha associados ao ficheiro de destino original permanecem intactos.

## <a name="general-best-practices"></a>Melhores práticas gerais 
Quando estiver a executar infraestruturas no Azure, mate cópias de segurança para recuperação de dados sempre que possível. As ações automatizadas são mais fiáveis do que os processos manuais, ajudando a melhorar a proteção de dados e a recuperabilidade. Pode utilizar a API REST, o Client SDK ou o scripting para automação.

Antes de implementar o programador de instantâneos de partilha, considere cuidadosamente a frequência de instantâneo e as definições de retenção para evitar incorrer em cargas desnecessárias.

As imagens de partilha fornecem apenas proteção ao nível do ficheiro. As fotos partilhadas não impedem a eliminação do dedo gordo numa conta de partilha de ficheiros ou armazenamento. Para ajudar a proteger uma conta de armazenamento de supressões acidentais, pode bloquear a conta de armazenamento ou o grupo de recursos.

## <a name="next-steps"></a>Passos seguintes
- Trabalhar com fotos partilhadas em:
    - [PowerShell](storage-how-to-use-files-powershell.md)
    - [CLI](storage-how-to-use-files-cli.md)
    - [Windows](storage-how-to-use-files-windows.md#accessing-share-snapshots-from-windows)
    - [Partilhar faQ snapshot](storage-files-faq.md#share-snapshots)
