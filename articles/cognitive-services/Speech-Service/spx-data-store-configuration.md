---
title: Opções de configuração do CLI do discurso - Serviço de fala
titleSuffix: Azure Cognitive Services
description: Saiba como criar e gerir ficheiros de configuração para utilização com o CLI do Discurso Azure.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 01/13/2021
ms.author: erhopf
ms.openlocfilehash: aa8551e49c8ef16984783c4e9735c987174b180a
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/17/2021
ms.locfileid: "98540062"
---
# <a name="speech-cli-configuration-options"></a>Opções de configuração do CLI do discurso

O comportamento do CLI da fala pode depender de definições em ficheiros de configuração, aos quais pode consultar um `@` símbolo. O Discurso CLI salva um novo cenário num novo `./spx/data` subdiretório que é criado no atual diretório de trabalho para o CLI do Discurso. Ao procurar um valor de configuração, o CLI do Discurso procura o seu diretório de trabalho atual, depois na loja de dados `./spx/data` em , e depois em outras datastores, incluindo uma loja de dados apenas de leitura final no `spx` binário. 

No início rápido do Speech CLI, utilizou a datastore para guardar os seus `@key` `@region` valores, pelo que não precisou de os especificar com cada `spx` comando. Tenha em mente que pode utilizar ficheiros de configuração para armazenar as suas próprias definições de configuração, ou até mesmo usá-los para passar URLs ou outros conteúdos dinâmicos gerados em tempo de execução.

## <a name="create-and-manage-configuration-files-in-the-datastore"></a>Criar e gerir ficheiros de configuração na datastore

Esta secção mostra como utilizar um ficheiro de configuração na loja de dados local para armazenar e obter definições de comando utilizando `spx config` , e armazenar a saída do Speech CLI utilizando a `--output` opção.

O exemplo a seguir limpa o `@my.defaults` ficheiro de configuração, adiciona pares de valor-chave para **chave** e **região** no ficheiro e utiliza a configuração numa chamada para `spx recognize` .

```console
spx config @my.defaults --clear
spx config @my.defaults --add key 000072626F6E20697320636F6F6C0000
spx config @my.defaults --add region westus

spx config @my.defaults

spx recognize --nodefaults @my.defaults --file hello.wav
```

Também pode escrever conteúdo dinâmico num ficheiro de configuração. Por exemplo, o seguinte comando cria um modelo de fala personalizado e armazena o URL do novo modelo num ficheiro de configuração. O comando seguinte aguarda até que o modelo dessa URL esteja pronto para ser utilizado antes de regressar.

```console
spx csr model create --name "Example 4" --datasets @my.datasets.txt --output url @my.model.txt
spx csr model status --model @my.model.txt --wait
```

O exemplo a seguir escreve dois URLs no `@my.datasets.txt` ficheiro de configuração. Neste cenário, `--output` pode incluir uma palavra-chave de **adicionar** opcional para criar um ficheiro de configuração ou anexar ao existente.


```console
spx csr dataset create --name "LM" --kind Language --content https://crbn.us/data.txt --output url @my.datasets.txt
spx csr dataset create --name "AM" --kind Acoustic --content https://crbn.us/audio.zip --output add url @my.datasets.txt

spx config @my.datasets.txt
```

Para obter mais detalhes sobre ficheiros de datastore, incluindo a utilização de ficheiros de configuração predefinidos `@spx.default` `@default.config` (, , e `@*.default.config` para definições padrão específicas do comando), insira este comando:

```console
spx help advanced setup
```

## <a name="next-steps"></a>Passos seguintes 

* [Operações de lote com o CLI do Discurso](./spx-batch-operations.md)