---
title: Opções de linha de comando do Azure Storage Explorer | Microsoft Docs
description: Documentação das opções de linha de comando do Azure Storage Explorer
services: storage
author: chuye
ms.service: storage
ms.topic: article
ms.date: 02/24/2021
ms.author: chuye
ms.openlocfilehash: 0d588d85852f798542c5d52658e8dae3b9e57949
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101746408"
---
# <a name="azure-storage-explorer-command-line-options"></a>Opções de linha de comando do Azure Storage Explorer

O Microsoft Azure Storage Explorer tem um conjunto de opções de linha de comando que podem ser adicionadas ao iniciar a aplicação. A maioria das opções de linha de comando são para depuração ou resolução de problemas.

## <a name="command-line-options"></a>Opções da linha de comandos
Opção  | Descrição
:------- | :-----------
`--debug`/`--prod`  | Inicie a aplicação em modo de depuramento ou de produção. No modo de depuração, os dados de anexo locais serão armazenados no armazenamento local da aplicação e não serão encriptados. As propriedades ocultas serão apresentadas no painel Propriedades para nós de recursos selecionados. O nível de verbosidade do registo será definido para imprimir mensagens de depuração revelando a lógica interna de configuração do Storage Explorer. O valor predefinido é `--prod`.
`--lang`  | Inicie a aplicação com uma determinada língua. Por exemplo, `--lang="zh-Hans"`.
`--disable-gpu` | Inicie a aplicação sem aceleração da GPU.
`--auto-open-dev-tools` | Deixe a aplicação abrir a janela das ferramentas do desenvolvedor assim que a janela do navegador aparecer. Esta opção é útil quando pretende atingir um ponto de rutura numa linha no código de arranque da janela do navegador.
`--verbosity` | Deslovide o nível de verbosidade da exploração madeireira do Explorador de Armazenamento. Os níveis de verbosidade suportados `debug` incluem, , , , e `verbose` `info` `warn` `error` `silent` . Por exemplo, `--verbosity=verbose`. Ao correr em modo de produção, o nível de verbosidade padrão é `info` . Ao correr em modo de depurado, o nível de verbosidade do log será sempre `debug` .
`--log-dir` | Desa estale o diretório para guardar ficheiros de registo. Por exemplo, `--log-dir=path_to_a_directory`.

Um exemplo de iniciar o Storage Explorer com opções de linha de comando personalizadas

```shell
./MicrosoftAzureStorageExplorer --lang=en --auto-open-dev-tools
```

> [!NOTE]
> Estas opções de linha de comando podem mudar em novas versões do Storage Explorer.

## <a name="when-to-use-command-line-options"></a>Quando utilizar opções de linha de comando

Algumas opções de linha de comando podem ser usadas para personalizar o Storage Explorer. Para as opções que têm as definições correspondentes do utilizador, tais como `--lang` . Recomendamos a utilização das definições do utilizador em vez de utilizar a opção de linha de comando. 

As outras opções de linha de comando podem ser úteis para depuração e resolução de problemas. Se encontrar um problema no Storage Explorer, reproduzir o problema no modo de depuração pode ajudar-nos a obter informações mais detalhadas para investigar.