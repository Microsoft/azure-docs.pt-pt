---
title: Exceções comuns do FabricClient geradas | Documentos da Microsoft
description: Descreve as exceções e erros que podem ser gerados pelas APIs FabricClient ao executar o aplicativo e de operações de gestão de cluster comuns.
services: service-fabric
documentationcenter: .net
author: oanapl
manager: chackdan
editor: ''
ms.assetid: bb821313-b221-479f-b08e-36cf07e60a07
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/20/2018
ms.author: oanapl
ms.openlocfilehash: 5bf17f4ced6bb01d8b62b6fa40ed1aeffe6f712f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60946551"
---
# <a name="common-exceptions-and-errors-when-working-with-the-fabricclient-apis"></a>Exceções e erros ao trabalhar com as APIs FabricClient comuns
O [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) APIs permitem que os administradores de cluster e a aplicação efetuar tarefas administrativas num aplicativo, serviço ou cluster do Service Fabric. Por exemplo, implantação de aplicativos, atualização e remoção, a verificar o estado de funcionamento um cluster ou um serviço de teste. Os desenvolvedores de aplicativos e administradores de cluster podem utilizar as APIs FabricClient desenvolver ferramentas para gerir o cluster do Service Fabric e aplicações.

Existem muitos tipos diferentes de operações que podem ser realizadas usando FabricClient.  Cada método pode lançar exceções para os problemas de infraestrutura transitório, erros de runtime ou erros devido a entrada incorreta.  Consulte a documentação de referência de API para obter as exceções são geradas por um método específico. Existem algumas exceções, no entanto, que podem ser geradas por muitos diferentes [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) APIs. A tabela seguinte lista as exceções que são comuns em FabricClient APIs.

| Exceção | Lançada quando |
| --- |:--- |
| [System.Fabric.FabricObjectClosedException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricobjectclosedexception) |O [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) objeto está num estado fechado. Descartar a [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) objeto estiver a utilizar e criar uma instância de uma nova [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) objeto. |
| [System.TimeoutException](https://docs.microsoft.com/dotnet/core/api/system.timeoutexception) |A operação excedeu o tempo limite. [OperationTimedOut](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode) é retornado quando a operação de um pouco mais complicado MaxOperationTimeout para concluir. |
| [System.UnauthorizedAccessException](https://docs.microsoft.com/dotnet/core/api/system.unauthorizedaccessexception) |A verificação de acesso para a operação falhou. E_ACCESSDENIED é devolvido. |
| [System.Fabric.FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception) |Ocorreu um erro de tempo de execução ao executar a operação. Qualquer um dos métodos FabricClient potencialmente pode emitir [FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception), o [ErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception.ErrorCode) propriedade indica a causa exata da exceção. Códigos de erro são definidos na [FabricErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode) enumeração. |
| [System.Fabric.FabricTransientException](https://docs.microsoft.com/dotnet/api/system.fabric.fabrictransientexception) |A operação falhou devido a uma condição de erro transitório de algum tipo. Por exemplo, uma operação pode falhar porque um quórum de réplicas temporariamente não está acessível. Exceções transitórias correspondem às operações com falhas que podem ser repetidas. |

Alguns comum [FabricErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode) erros que podem ser retornados numa [FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception):

| Erro | Condição |
| --- |:--- |
| CommunicationError |Um erro de comunicação causou a operação falhar, repita a operação. |
| InvalidCredentialType |O tipo de credencial é inválido. |
| InvalidX509FindType |O X509FindType é inválido. |
| InvalidX509StoreLocation |O X509 localização do arquivo é inválida. |
| InvalidX509StoreName |O X509 nome de arquivo é inválido. |
| InvalidX509Thumbprint |O X509 cadeia de thumbprint do certificado é inválida. |
| InvalidProtectionLevel |O nível de proteção é inválido. |
| InvalidX509Store |Não é possível abrir o arquivo de certificados X509. |
| InvalidSubjectName |O nome do requerente é inválido. |
| InvalidAllowedCommonNameList |O formato de cadeia da lista Nome comum é inválido. Deve ser uma lista separada por vírgulas. |

