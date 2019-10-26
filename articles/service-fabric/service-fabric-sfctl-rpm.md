---
title: CLI do Azure Service Fabric-sfctl rpm | Microsoft Docs
description: Descreve os comandos do sfctl rpm do Service Fabric CLI.
services: service-fabric
documentationcenter: na
author: jeffj6123
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: 9c37dc8131f14a3b35e68b3e88502c91f96810f6
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901024"
---
# <a name="sfctl-rpm"></a>sfctl rpm
Consultar e enviar comandos para o serviço do Gerenciador de reparo.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| aprovar-forçar | Força a aprovação da tarefa de reparo específica. |
| delete | Exclui uma tarefa de reparo concluída. |
| list | Obtém uma lista de tarefas de reparo que correspondem aos filtros fornecidos. |

## <a name="sfctl-rpm-approve-force"></a>sfctl rpm aprovar-forçar
Força a aprovação da tarefa de reparo específica.

Esta API dá suporte à plataforma Service Fabric; Ele não deve ser usado diretamente do seu código.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --Task-ID [obrigatório] | A ID da tarefa de reparo. |
| --versão | O número da versão atual da tarefa de reparo. Se for diferente de zero, a solicitação só terá sucesso se esse valor corresponder à versão real atual da tarefa de reparo. Se for zero, nenhuma verificação de versão será executada. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores permitidos\: JSON, jsonc, Table, TSV.  Padrão\: JSON. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-rpm-delete"></a>sfctl rpm excluir
Exclui uma tarefa de reparo concluída.

Esta API dá suporte à plataforma Service Fabric; Ele não deve ser usado diretamente do seu código.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --Task-ID [obrigatório] | A ID da tarefa de reparo concluída a ser excluída. |
| --versão | O número da versão atual da tarefa de reparo. Se for diferente de zero, a solicitação só terá sucesso se esse valor corresponder à versão real atual da tarefa de reparo. Se for zero, nenhuma verificação de versão será executada. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores permitidos\: JSON, jsonc, Table, TSV.  Padrão\: JSON. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-rpm-list"></a>lista de RPM sfctl
Obtém uma lista de tarefas de reparo que correspondem aos filtros fornecidos.

Esta API dá suporte à plataforma Service Fabric; Ele não deve ser usado diretamente do seu código.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --executor-filtro | O nome do executor de reparo cujas tarefas reivindicadas devem ser incluídas na lista. |
| --Estado-filtro | Um bit ou dos valores a seguir, especificando quais Estados de tarefa devem ser incluídos na lista de resultados. <br> 1-criado <br>2-declarado  <br>4-preparando  <br>8-aprovado  <br>16-executando  <br>32-restaurando  <br>64-concluído |
| --ID-da-tarefa-filtro | O prefixo da ID da tarefa de reparo a ser correspondido. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores permitidos\: JSON, jsonc, Table, TSV.  Padrão\: JSON. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |


## <a name="next-steps"></a>Passos seguintes
- [Configure](service-fabric-cli.md) a CLI do Service Fabric.
- Saiba como usar a CLI do Service Fabric usando os [scripts de exemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).