---
title: Azure Service Fabric CLI sfctl loja | Documentos da Microsoft
description: Descreve os comandos do CLI do Service Fabric sfctl store.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: 65dcceb2e55ec0927630b32670d2f915a01903bf
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60303168"
---
# <a name="sfctl-store"></a>sfctl store
Efetue operações de nível de arquivo básico no armazenamento de imagens do cluster.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| delete | Elimina o conteúdo do arquivo de imagem de existente. |
| root-info | Obtém as informações dos conteúdos na raiz do arquivo de imagem. |
| stat | Obtém as informações de conteúdo de arquivo de imagem. |

## <a name="sfctl-store-delete"></a>o sfctl store delete
Elimina o conteúdo do arquivo de imagem de existente.

Eliminações existente conteúdo de arquivo de imagem que está a ser encontrado dentro da imagem de determinado armazenam o caminho relativo. Este comando pode ser utilizado para eliminar pacotes de aplicações carregadas depois de terem sido aprovisionados.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| – caminho de conteúdo [necessário] | Caminho relativo para o ficheiro ou pasta no arquivo de imagem de sua raiz. |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-store-root-info"></a>informações de raiz do arquivo sfctl
Obtém as informações dos conteúdos na raiz do arquivo de imagem.

Devolve as informações sobre armazenamento de imagens do conteúdo na raiz do arquivo de imagem.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-store-stat"></a>o sfctl store stat
Obtém as informações de conteúdo de arquivo de imagem.

Devolve as informações sobre o conteúdo do arquivo de imagem no contentPath especificado. O contentPath é relativo à raiz do arquivo de imagem.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| – caminho de conteúdo [necessário] | Caminho relativo para o ficheiro ou pasta no arquivo de imagem de sua raiz. |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |


## <a name="next-steps"></a>Passos Seguintes
- [Configuração](service-fabric-cli.md) a CLI do Service Fabric.
- Saiba como utilizar a CLI do Service Fabric utilizando o [scripts de exemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).