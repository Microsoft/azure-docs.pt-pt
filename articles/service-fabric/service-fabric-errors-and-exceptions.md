---
title: Exceções FabricClient comuns geradas
description: Descreve as exceções e erros comuns que podem ser lançados pelas APIs do FabricClient ao executar operações de gerenciamento de aplicativo e cluster.
author: oanapl
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: oanapl
ms.openlocfilehash: 9ad3097a490d4728e05ea90652c17c24b79cac2c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75457928"
---
# <a name="common-exceptions-and-errors-when-working-with-the-fabricclient-apis"></a>Erros e exceções comuns ao trabalhar com as APIs do FabricClient
As APIs do [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) permitem que os administradores de cluster e de aplicativos executem tarefas administrativas em um Service Fabric aplicativo, serviço ou cluster. Por exemplo, implantação, atualização e remoção de aplicativos, verificação da integridade de um cluster ou teste de um serviço. Os desenvolvedores de aplicativos e os administradores de cluster podem usar as APIs do FabricClient para desenvolver ferramentas para gerenciar o cluster de Service Fabric e os aplicativos.

Há muitos tipos diferentes de operações que podem ser executadas usando FabricClient.  Cada método pode gerar exceções para erros devido à entrada incorreta, erros de tempo de execução ou problemas de infraestrutura transitórios.  Consulte a documentação de referência da API para descobrir quais exceções são lançadas por um método específico. No entanto, há algumas exceções que podem ser geradas por muitas APIs [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) diferentes. A tabela a seguir lista as exceções que são comuns em todas as APIs do FabricClient.

| Exceção | Gerado quando |
| --- |:--- |
| [System.Fabric.FabricObjectClosedException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricobjectclosedexception) |O objeto [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) está em um estado fechado. Descarte o objeto [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) que você está usando e crie uma instância de um novo objeto [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) . |
| [System.TimeoutException](https://docs.microsoft.com/dotnet/core/api/system.timeoutexception) |O tempo limite da operação foi atingido. [OperationTimedOut](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode) é retornado quando a operação leva mais de MaxOperationTimeout para ser concluída. |
| [System.UnauthorizedAccessException](https://docs.microsoft.com/dotnet/core/api/system.unauthorizedaccessexception) |Falha na verificação de acesso para a operação. E_ACCESSDENIED é retornado. |
| [System.Fabric.FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception) |Ocorreu um erro de tempo de execução ao executar a operação. Qualquer um dos métodos FabricClient potencialmente pode lançar [fabricexception](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception), a propriedade [ErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception.ErrorCode) indica a causa exata da exceção. Os códigos de erro são definidos na enumeração [FabricErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode) . |
| [System.Fabric.FabricTransientException](https://docs.microsoft.com/dotnet/api/system.fabric.fabrictransientexception) |A operação falhou devido a uma condição de erro transitória de algum tipo. Por exemplo, uma operação pode falhar porque um quorum de réplicas está temporariamente inacessível. As exceções transitórias correspondem às operações com falha que podem ser repetidas. |

Alguns erros comuns de [FabricErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode) que podem ser retornados em uma [fabricexception](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception):

| Erro | Condição |
| --- |:--- |
| CommunicationError |Um erro de comunicação fez com que a operação falhasse, repita a operação. |
| InvalidCredentialType |O tipo de credencial é inválido. |
| InvalidX509FindType |O X509FindType é inválido. |
| InvalidX509StoreLocation |O local do repositório X509 é inválido. |
| InvalidX509StoreName |O nome do repositório X509 é inválido. |
| InvalidX509Thumbprint |A cadeia de impressão digital do certificado X509 é inválida. |
| InvalidProtectionLevel |O nível de proteção é inválido. |
| InvalidX509Store |Não é possível abrir o repositório de certificados X509. |
| InvalidSubjectName |O nome da entidade é inválido. |
| InvalidAllowedCommonNameList |O formato da cadeia de caracteres da lista de nomes comuns é inválido. Deve ser uma lista separada por vírgulas. |

