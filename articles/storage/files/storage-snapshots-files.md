---
title: Visão geral de instantâneos de compartilhamento para arquivos do Azure | Microsoft Docs
description: Um instantâneo de compartilhamento é uma versão somente leitura de um compartilhamento de arquivos do Azure que é realizada em um ponto no tempo, como uma maneira de fazer backup do compartilhamento.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 01/17/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: f3cbf740016a4c162c63343be4cb9cd577f85935
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68699357"
---
# <a name="overview-of-share-snapshots-for-azure-files"></a>Visão geral de instantâneos de compartilhamento para arquivos do Azure 
Os arquivos do Azure fornecem a capacidade de fazer instantâneos de compartilhamento de compartilhamentos de arquivos. Os instantâneos de compartilhamento capturam o estado de compartilhamento nesse momento. Neste artigo, descrevemos quais recursos os instantâneos de compartilhamento fornecem e como você pode aproveitá-los em seu caso de uso personalizado.

## <a name="when-to-use-share-snapshots"></a>Quando usar instantâneos de compartilhamento

### <a name="protection-against-application-error-and-data-corruption"></a>Proteção contra erros de aplicativo e corrupção de dados
Os aplicativos que usam compartilhamentos de arquivos executam operações como gravação, leitura, armazenamento, transmissão e processamento. Se um aplicativo estiver configurado incorretamente ou se um bug não intencional for introduzido, a substituição acidental ou o dano poderá ocorrer em alguns blocos. Para ajudar a se proteger contra esses cenários, você pode tirar um instantâneo de compartilhamento antes de implantar o novo código do aplicativo. Se um erro de bug ou aplicativo for introduzido com a nova implantação, você poderá voltar para uma versão anterior dos seus dados no compartilhamento de arquivos. 

### <a name="protection-against-accidental-deletions-or-unintended-changes"></a>Proteção contra exclusões acidentais ou alterações indesejadas
Imagine que você esteja trabalhando em um arquivo de texto em um compartilhamento de arquivos. Depois que o arquivo de texto for fechado, você perderá a capacidade de desfazer suas alterações. Nesses casos, você precisará recuperar uma versão anterior do arquivo. Você pode usar instantâneos de compartilhamento para recuperar versões anteriores do arquivo se ele for acidentalmente renomeado ou excluído.

### <a name="general-backup-purposes"></a>Fins de backup geral
Depois de criar um compartilhamento de arquivos, você pode criar periodicamente um instantâneo de compartilhamento do compartilhamento de arquivos para usá-lo para o backup de dados. Um instantâneo de compartilhamento, quando realizado periodicamente, ajuda a manter versões anteriores de dados que podem ser usadas para futuros requisitos de auditoria ou recuperação de desastres.

## <a name="capabilities"></a>Capacidades
Um instantâneo de compartilhamento é uma cópia point-in-time e somente leitura dos seus dados. Você pode criar, excluir e gerenciar instantâneos usando a API REST. Os mesmos recursos também estão disponíveis na biblioteca do cliente, CLI do Azure e portal do Azure. 

Você pode exibir instantâneos de um compartilhamento usando a API REST e o SMB. Você pode recuperar a lista de versões do diretório ou arquivo, e pode montar uma versão específica diretamente como uma unidade (disponível somente no Windows-consulte [limites](#limits)). 

Depois que um instantâneo de compartilhamento é criado, ele pode ser lido, copiado ou excluído, mas não modificado. Você não pode copiar um instantâneo de compartilhamento inteiro para outra conta de armazenamento. Você precisa fazer esse arquivo por arquivo, usando AzCopy ou outros mecanismos de cópia.

O recurso de instantâneo de compartilhamento é fornecido no nível de compartilhamento de arquivo. A recuperação é fornecida em nível de arquivo individual, para permitir a restauração de arquivos individuais. Você pode restaurar um compartilhamento de arquivos completo usando SMB, a API REST, o portal, a biblioteca de cliente ou as ferramentas do PowerShell/CLI.

Um instantâneo de compartilhamento de um compartilhamento de arquivos é idêntico ao seu compartilhamento de arquivos base. A única diferença é que um valor **DateTime** é acrescentado ao URI de compartilhamento para indicar a hora em que o instantâneo de compartilhamento foi tirado. Por exemplo, se um URI de compartilhamento de http://storagesample.core.file.windows.net/myshare arquivos for, o URI do instantâneo de compartilhamento será semelhante a:
```
http://storagesample.core.file.windows.net/myshare?snapshot=2011-03-09T01:42:34.9360000Z
```

Os instantâneos de compartilhamento persistem até que sejam explicitamente excluídos. Um instantâneo de compartilhamento não pode sobreviver além seu compartilhamento de arquivos base. Você pode enumerar os instantâneos associados ao compartilhamento de arquivos base para controlar seus instantâneos atuais. 

Quando você cria um instantâneo de compartilhamento de um compartilhamento de arquivos, os arquivos nas propriedades do sistema do compartilhamento são copiados para o instantâneo de compartilhamento com os mesmos valores. Os arquivos base e os metadados do compartilhamento de arquivos também são copiados para o instantâneo de compartilhamento, a menos que você especifique metadados separados para o instantâneo de compartilhamento ao criá-lo.

Você não pode excluir um compartilhamento que tem instantâneos de compartilhamento, a menos que você exclua todos os instantâneos de compartilhamento primeiro.

## <a name="space-usage"></a>Uso de espaço 
Os instantâneos de compartilhamento são incrementais por natureza. Somente os dados que foram alterados após o instantâneo de compartilhamento mais recente são salvos. Isso minimiza o tempo necessário para criar o instantâneo de compartilhamento e economiza os custos de armazenamento. Qualquer operação de gravação no objeto ou na operação de atualização de metadados ou de propriedade é contada em relação ao "conteúdo alterado" e é armazenada no instantâneo de compartilhamento. 

Para conservar o espaço, você pode excluir o instantâneo de compartilhamento para o período em que a rotatividade foi mais alta.

Embora os instantâneos de compartilhamento sejam salvos incrementalmente, você precisa reter apenas o instantâneo de compartilhamento mais recente para restaurar o compartilhamento. Quando você exclui um instantâneo de compartilhamento, somente os dados exclusivos desse instantâneo de compartilhamento são removidos. Os instantâneos ativos contêm todas as informações de que você precisa para procurar e restaurar seus dados (desde o momento em que o instantâneo de compartilhamento foi tirado) até o local original ou um local alternativo. Você pode restaurar no nível do item.

Os instantâneos não contam para o limite de compartilhamento de 5 TB. Não há nenhum limite para a quantidade de espaço que os instantâneos de compartilhamento ocupam no total. Limites de conta de armazenamento ainda se aplicam.

## <a name="limits"></a>Limites
O número máximo de instantâneos de compartilhamento que os arquivos do Azure permite hoje é de 200. Após 200 instantâneos de compartilhamento, você precisa excluir instantâneos de compartilhamento mais antigos para criar novos. 

Não há nenhum limite para as chamadas simultâneas para a criação de instantâneos de compartilhamento. Não há nenhum limite para a quantidade de espaço que os instantâneos de compartilhamento de um determinado compartilhamento de arquivos podem consumir. 

Hoje, não é possível montar instantâneos de compartilhamento no Linux. Isso ocorre porque o cliente do Linux SMB não oferece suporte à montagem de instantâneos como o Windows.

## <a name="copying-data-back-to-a-share-from-share-snapshot"></a>Copiando dados de volta para um compartilhamento do instantâneo de compartilhamento
As operações de cópia que envolvem arquivos e instantâneos de compartilhamento seguem estas regras:

Você pode copiar arquivos individuais em um instantâneo de compartilhamento de arquivo para seu compartilhamento base ou qualquer outro local. Você pode restaurar uma versão anterior de um arquivo ou restaurar o compartilhamento de arquivos completo copiando arquivo por arquivo do instantâneo de compartilhamento. O instantâneo de compartilhamento não é promovido para o compartilhamento base. 

O instantâneo de compartilhamento permanece intacto após a cópia, mas o compartilhamento de arquivos base é substituído por uma cópia dos dados que estavam disponíveis no instantâneo de compartilhamento. Todos os arquivos restaurados contam para "conteúdo alterado".

Você pode copiar um arquivo em um instantâneo de compartilhamento para um destino com um nome diferente. O arquivo de destino resultante é um arquivo gravável e não um instantâneo de compartilhamento.

Quando um arquivo de destino é substituído por uma cópia, os instantâneos de compartilhamento associados ao arquivo de destino original permanecem intactos.

## <a name="general-best-practices"></a>Práticas recomendadas gerais 
Quando você estiver executando a infraestrutura no Azure, automatize os backups para recuperação de dados sempre que possível. Ações automatizadas são mais confiáveis do que processos manuais, ajudando a melhorar a proteção e a capacidade de recuperação de dados. Você pode usar a API REST, o SDK do cliente ou o script para automação.

Antes de implantar o Agendador de instantâneos de compartilhamento, considere cuidadosamente suas configurações de frequência e retenção de instantâneo de compartilhamento para evitar incorrer em encargos desnecessários.

Os instantâneos de compartilhamento fornecem apenas proteção em nível de arquivo. Os instantâneos de compartilhamento não impedem exclusões de Fat-Finger em um compartilhamento de arquivos ou conta de armazenamento. Para ajudar a proteger uma conta de armazenamento contra exclusões acidentais, você pode bloquear a conta de armazenamento ou o grupo de recursos.

## <a name="next-steps"></a>Passos Seguintes
- Trabalhando com instantâneos de compartilhamento em:
    - [PowerShell](storage-how-to-use-files-powershell.md)
    - [CLI](storage-how-to-use-files-cli.md)
    - [Windows](storage-how-to-use-files-windows.md#accessing-share-snapshots-from-windows)
    - [Perguntas frequentes de compartilhamento de instantâneo](storage-files-faq.md#share-snapshots)
