---
title: Azure Service Fabric CLI-sfctl store
description: Conheça o sfctl, a interface de linha de comando Azure Service Fabric. Inclui uma lista de comandos para realizar operações de nível de ficheiro na loja de imagem cluster.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 75c62b54ff3aa7f3af344aa3e1ca81d431ae0ab2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76905619"
---
# <a name="sfctl-store"></a>sfctl store
Efetue operações básicas de nível de ficheiro na loja de imagens do cluster.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| delete | Elimina o conteúdo existente da loja de imagens. |
| raiz-info | Obtém a informação de conteúdo na raiz da loja de imagens. |
| estação estando | Obtém a informação sobre o conteúdo da loja de imagens. |

## <a name="sfctl-store-delete"></a>sfctl loja apagar
Elimina o conteúdo existente da loja de imagens.

Elimina o conteúdo existente da loja de imagens que está a ser encontrado dentro do caminho relativo da loja de imagens. Este comando pode ser utilizado para eliminar pacotes de aplicação carregados uma vez que são provisionados.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --content-path [Obrigatório] | Caminho relativo para arquivar ou pasta na loja de imagens a partir da sua raiz. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-store-root-info"></a>sfctl loja root-info
Obtém a informação de conteúdo na raiz da loja de imagens.

Devolve a informação sobre o conteúdo da loja de imagens na raiz da loja de imagens.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-store-stat"></a>sfctl loja stat
Obtém a informação sobre o conteúdo da loja de imagens.

Devolve as informações sobre o conteúdo da loja de imagens no conteúdo especificadoPath. O contentPath é relativo à raiz da loja de imagens.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --content-path [Obrigatório] | Caminho relativo para arquivar ou pasta na loja de imagens a partir da sua raiz. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |


## <a name="next-steps"></a>Passos seguintes
- [Configurar](service-fabric-cli.md) o CLI de tecido de serviço.
- Aprenda a utilizar o CLI de tecido de serviço utilizando as [scripts de amostra](/azure/service-fabric/scripts/sfctl-upgrade-application).