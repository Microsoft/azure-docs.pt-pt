---
title: CLI do Azure Service Fabric-sfctl Store | Microsoft Docs
description: Descreve os comandos da Store sfctl da CLI do Service Fabric.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: ccd9e8323f6e0de7b81c7600e7828e4858c51201
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035854"
---
# <a name="sfctl-store"></a>sfctl store
Execute operações básicas de nível de arquivo no repositório de imagens do cluster.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| eliminar | Exclui o conteúdo do repositório de imagens existente. |
| raiz-informações | Obtém as informações de conteúdo na raiz do repositório de imagens. |
| stat | Obtém as informações de conteúdo do repositório de imagens. |

## <a name="sfctl-store-delete"></a>exclusão da loja sfctl
Exclui o conteúdo do repositório de imagens existente.

Exclui o conteúdo do repositório de imagens existente que está sendo encontrado no caminho relativo do repositório de imagens fornecido. Esse comando pode ser usado para excluir pacotes de aplicativos carregados depois que eles são provisionados.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --content-Path [obrigatório] | Caminho relativo para o arquivo ou a pasta no repositório de imagens de sua raiz. |
| --Timeout-t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores\: permitidos JSON, jsonc, Table, TSV.  JSON\: padrão. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-store-root-info"></a>raiz da sfctl Store-informações
Obtém as informações de conteúdo na raiz do repositório de imagens.

Retorna as informações sobre o conteúdo do repositório de imagens na raiz do repositório de imagens.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --Timeout-t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores\: permitidos JSON, jsonc, Table, TSV.  JSON\: padrão. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-store-stat"></a>stat da sfctl Store
Obtém as informações de conteúdo do repositório de imagens.

Retorna as informações sobre o conteúdo do repositório de imagens no contentPath especificado. O contentPath é relativo à raiz do repositório de imagens.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --content-Path [obrigatório] | Caminho relativo para o arquivo ou a pasta no repositório de imagens de sua raiz. |
| --Timeout-t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores\: permitidos JSON, jsonc, Table, TSV.  JSON\: padrão. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |


## <a name="next-steps"></a>Passos Seguintes
- [Configure](service-fabric-cli.md) a CLI do Service Fabric.
- Saiba como usar a CLI do Service Fabric usando os [scripts de exemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).