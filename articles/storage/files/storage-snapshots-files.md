---
title: Descrição geral de instantâneos de partilha de ficheiros do Azure | Documentos da Microsoft
description: Um instantâneo de partilha é uma versão só de leitura de uma partilha de ficheiros do Azure que está a ser utilizada num ponto no tempo, como uma forma de fazer cópias de segurança da partilha.
services: storage
author: RenaShahMSFT
ms.service: storage
ms.topic: article
ms.date: 01/17/2018
ms.author: renash
ms.subservice: files
ms.openlocfilehash: 8d6bca12ae1df0cab7be3e1c54467d32063bacaf
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57195312"
---
# <a name="overview-of-share-snapshots-for-azure-files"></a>Descrição geral de instantâneos de partilha de ficheiros do Azure 
Os ficheiros do Azure fornece a capacidade para tirar instantâneos de partilha de partilhas de ficheiros. Partilhe instantâneos captura o estado de partilha nesse ponto no tempo. Neste artigo, descrevemos de que fornecem instantâneos de partilha de capacidades e como pode tirar partido dos mesmos no seu caso de utilização personalizado.

## <a name="when-to-use-share-snapshots"></a>Quando utilizar instantâneos de partilha

### <a name="protection-against-application-error-and-data-corruption"></a>Proteção contra Corrupção de dados e de erro de aplicação
Aplicações que utilizam partilhas de ficheiros efetuar operações como escrever, leitura, armazenamento, transmissão e processamento. Se um aplicativo está configurado incorretamente ou é apresentado um erro não intencional, uma substituição acidental ou danos em que podem ter alguns blocos. Para ajudar a proteger contra estes cenários, pode tirar um instantâneo de partilha antes de implementar o novo código de aplicação. Se um erro de bug ou aplicação é introduzido com a nova implementação, pode voltar atrás para uma versão anterior dos seus dados em que a partilha de ficheiros. 

### <a name="protection-against-accidental-deletions-or-unintended-changes"></a>Proteção contra alterações indesejadas ou de eliminações acidentais
Imagine que estiver trabalhando num arquivo de texto numa partilha de ficheiros. Depois de fechar o ficheiro de texto, perde a capacidade de desfazer as alterações. Nestes casos, em seguida, tem de recuperar uma versão anterior do arquivo. Pode utilizar instantâneos de partilha para recuperar versões anteriores do ficheiro se acidentalmente tem mudado ou eliminado.

### <a name="general-backup-purposes"></a>Fins gerais de cópia de segurança
Depois de criar uma partilha de ficheiros, pode criar um instantâneo de partilha de partilha de ficheiros para utilizá-lo para cópia de segurança de dados periodicamente. Uma partilha de instantâneos, quando executada periodicamente, ajuda a manter versões anteriores dos dados que podem ser utilizadas para requisitos de auditoria futuros ou recuperação após desastre.

## <a name="capabilities"></a>Capacidades
Um instantâneo de partilha é uma cópia de ponto no tempo, só de leitura dos seus dados. Pode criar, eliminar e gerir instantâneos com a API REST. Mesmas capacidades também estão disponíveis no portal do Azure, CLI do Azure e biblioteca de cliente. 

Pode ver instantâneos de uma partilha com o API do REST e o SMB. Pode obter a lista de versões do diretório ou arquivo, e pode montar uma versão específica diretamente como uma unidade (disponível apenas no Windows - consulte [limites](#limits)). 

Depois de cria um instantâneo de partilha, ele possa ser lido, copiado, ou eliminado, mas não modificado. Não é possível copiar um instantâneo de partilha completa para outra conta de armazenamento. Precisa fazer nesse arquivo por arquivo, com o AzCopy ou de outros mecanismos de cópia.

Capacidade de instantâneo de partilha é fornecida ao nível de partilha de ficheiros. Obtenção está disponível no nível de arquivo individual, para permitir o restauro de ficheiros individuais. Pode restaurar uma partilha de ficheiros completa através de SMB, a API REST, o portal, a biblioteca de cliente ou as ferramentas do PowerShell/CLI.

Um instantâneo de partilha de uma partilha de ficheiros é idêntico à sua partilha de ficheiros de base. A única diferença é que uma **DateTime** valor é acrescentado ao compartilhamento de URI para indicar a hora em que o instantâneo de partilha foi criado. Por exemplo, se o URI de partilhar um ficheiro é http://storagesample.core.file.windows.net/myshare, o instantâneo de partilha de URI é semelhante a:
```
http://storagesample.file.core.windows.net/myshare?snapshot=2011-03-09T01:42:34.9360000Z
```

Instantâneos de partilha persistirem até que eles são eliminados de forma explícita. Um instantâneo de partilha não é possível sobreviverem às sua partilha de ficheiros de base. Pode enumerar os instantâneos associados a partilha de ficheiros de base para controlar seu instantâneos atuais. 

Quando cria um instantâneo de partilha de uma partilha de ficheiros, os ficheiros nas propriedades do sistema do compartilhamento de são copiados para o instantâneo de partilha com os mesmos valores. Os ficheiros de bases e os metadados da partilha de ficheiros também são copiados para o instantâneo de partilha, a menos que especifique metadados separado para a partilha de instantâneo quando a criar.

Não é possível eliminar uma partilha que tenha instantâneos de partilha, a menos que elimine primeiro todos os instantâneos de partilha.

## <a name="space-usage"></a>Utilização de espaço 
Instantâneos de partilha são incrementais por natureza. Apenas os dados que foram alterados após a sua mais recente instantâneo de partilha é guardado. Isto minimiza o tempo necessário para criar o instantâneo de partilha e guarda nos custos de armazenamento. Qualquer operação de escrita para o objeto ou operação de atualização de propriedade ou metadados é computada como "conteúdo alterado" e está armazenada no instantâneo de partilha. 

Para conservar espaço, pode eliminar o instantâneo de partilha para o período de quando o volume de alterações foi mais alta.

Apesar de instantâneos de partilha são guardados de forma incremental, terá de manter apenas o instantâneo de partilha de mais recente para restaurar a partilha. Quando elimina um instantâneo de partilha, apenas os dados exclusivos a esse instantâneo de partilha são removidos. Instantâneos do Active Directory contenham todas as informações de que precisa para procurar e restaurar os dados (desde o momento em que o instantâneo de partilha foi criado) para a localização original ou uma localização alternativa. Pode restaurar ao nível do item.

Os instantâneos não contam para o limite de partilha de 5 TB. Não existe nenhum limite para quanto os instantâneos de partilha de espaço ocupam no total. Limites de conta de armazenamento ainda se aplicam.

## <a name="limits"></a>Limites
O número máximo de instantâneos de partilha de ficheiros do Azure permite que hoje é 200. Depois de instantâneos de partilha de 200, terá de eliminar os instantâneos de partilha mais antigos para criar novos. 

Não existe nenhum limite para as chamadas simultâneas para a criação de instantâneos de partilha. Não existe nenhum limite à quantidade de espaço que a partilha de instantâneos de partilha de ficheiros específica podem consumir. 

Atualmente, não é possível montar os instantâneos de partilha no Linux. Isto acontece porque o cliente Linux SMB não suporta instantâneos de montagem, como o Windows faz.

## <a name="copying-data-back-to-a-share-from-share-snapshot"></a>Copiar dados para uma partilha de instantâneo de partilha
Operações de cópia que envolvem ficheiros e instantâneos de partilha, siga estas regras:

Pode copiar ficheiros individuais num instantâneo de partilha de ficheiros através de sua partilha de base ou qualquer outra localização. Pode restaurar uma versão anterior de um ficheiro ou restaurar a partilha de ficheiros concluído ao copiar o arquivo por arquivo a partir do instantâneo de partilha. O instantâneo de partilha não é promovido para partilha de base. 

O instantâneo de partilha permanece intacto depois de copiar, mas a partilha de ficheiros de base será substituída com uma cópia dos dados que estavam disponíveis no instantâneo de partilha. Todos os a contagem de ficheiros restaurados na direção de "conteúdo alterado."

Pode copiar um arquivo num instantâneo de partilha para um destino com um nome diferente. O ficheiro de destino resultante é um ficheiro gravável e não um instantâneo de partilha.

Quando um ficheiro de destino será substituído com uma cópia, quaisquer instantâneos de partilha associados ao ficheiro de destino original permanecem intactos.

## <a name="general-best-practices"></a>Melhores práticas gerais 
Quando estiver executando a infraestrutura no Azure, automatize cópias de segurança para a recuperação de dados sempre que possível. Ações automáticas são mais fiáveis do que os processos manuais, ajuda a melhorar a proteção de dados e capacidade de recuperação. Pode utilizar a API REST, o SDK do cliente ou de scripts para automação.

Antes de implementar o agendador de instantâneo de partilha, tenha em atenção a frequência de instantâneos de partilha e definições de retenção para evitar incorrer em custos desnecessários.

Instantâneos de partilha fornecem apenas uma proteção ao nível do ficheiro. Instantâneos de partilha não impedem eliminações de dedo fat numa conta de armazenamento ou de partilha de ficheiros. Para ajudar a proteger uma conta de armazenamento de eliminações acidentais, pode bloquear a conta de armazenamento ou o grupo de recursos.

## <a name="next-steps"></a>Passos Seguintes
- Trabalhar com instantâneos de partilha no:
    - [PowerShell](storage-how-to-use-files-powershell.md)
    - [CLI](storage-how-to-use-files-cli.md)
    - [Windows](storage-how-to-use-files-windows.md#accessing-share-snapshots-from-windows)
    - [FAQ de instantâneo de partilha](storage-files-faq.md#share-snapshots)
