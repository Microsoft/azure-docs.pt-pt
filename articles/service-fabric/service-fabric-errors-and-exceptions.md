---
title: Exceções de Tecidos Comuns lançadas
description: Descreve as exceções e erros comuns que podem ser lançados pelas APIs doNcimient Ao executar operações de aplicação e gestão de clusters.
author: georgewallace
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: gwallace
ms.openlocfilehash: d22184d6d275adb95932fb29338768210547c199
ms.sourcegitcommit: b56226271541e1393a4b85d23c07fd495a4f644d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/26/2020
ms.locfileid: "85392629"
---
# <a name="common-exceptions-and-errors-when-working-with-the-fabricclient-apis"></a>Exceções e erros comuns ao trabalhar com as APIs FabricClient
As APIs [DoNcims FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) permitem que os administradores de cluster e aplicação executem tarefas administrativas numa aplicação, serviço ou cluster de Tecidos de Serviço. Por exemplo, implementação de aplicações, upgrade e remoção, verificando a saúde de um cluster, ou testando um serviço. Os desenvolvedores de aplicações e os administradores de cluster podem usar as APIs do FabricClient para desenvolver ferramentas para gerir o cluster e aplicações do Tecido de Serviço.

Existem muitos tipos diferentes de operações que podem ser realizadas usando o FabricClient.  Cada método pode lançar exceções para erros devido a entradas incorretas, erros de tempo de execução ou problemas de infraestrutura transitórios.  Consulte a documentação de referência da API para descobrir quais as exceções que são lançadas por um método específico. Existem algumas exceções, no entanto, que podem ser lançadas por muitas APIs [denciências de Tecidos](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) diferentes. A tabela a seguir enumera as exceções comuns em todas as APIs doNcimient.

| Exceção | Jogado quando |
| --- |:--- |
| [System.Fabric.FabricObjectClosedException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricobjectclosedexception) |O objeto [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) está em estado fechado. Elimine o objeto [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) que está a usar e instantaneamente um novo objeto [FabricClient.](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) |
| [Sistema.TimeoutExcepção](https://docs.microsoft.com/dotnet/core/api/system.timeoutexception) |A operação foi cronometrada. [OperationTimedOut](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode) é devolvido quando a operação leva mais do que MaxOperationTimeout para ser concluída. |
| [Sistema.União DesacessExcepção](https://docs.microsoft.com/dotnet/core/api/system.unauthorizedaccessexception) |A verificação de acesso à operação falhou. E_ACCESSDENIED é devolvido. |
| [System.Fabric.FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception) |Ocorreu um erro de tempo de execução durante a operação. Qualquer um dos métodos FabricClient pode potencialmente lançar [FabricException,](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception)a propriedade [ErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception.ErrorCode) indica a causa exata da exceção. Os códigos de erro são definidos na enumeração [FabricErrorCode.](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode) |
| [System.Fabric.FabricTransientExcepção](https://docs.microsoft.com/dotnet/api/system.fabric.fabrictransientexception) |A operação falhou devido a algum tipo de erro transitório. Por exemplo, uma operação pode falhar porque um quórum de réplicas não é temporariamente alcançável. As exceções transitórias correspondem a operações falhadas que podem ser novamente julgadas. |

Alguns erros comuns [do FabricErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode) que podem ser devolvidos numa [FabricException:](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception)

| Erro | Condição |
| --- |:--- |
| Comunicadoror |Um erro de comunicação fez com que a operação falhasse, relemissou a operação. |
| InvalidCredentialType |O tipo de credencial é inválido. |
| InvalidX509FindType |O X509FindType é inválido. |
| InvalidX509StoreLocation |A localização da loja X509 é inválida. |
| InvalidX509StoreName |O nome da loja X509 é inválido. |
| Impressão invalidX509Thumbprint |A cadeia de impressão digital do certificado X509 é inválida. |
| InvalidProtectionLevel |O nível de proteção é inválido. |
| Loja InvalidX509 |A loja de certificados X509 não pode ser aberta. |
| InvalidSubjectName |O nome do sujeito é inválido. |
| InvalidAllowedCommonNameList |O formato da cadeia de lista de nomes comuns é inválido. Deve ser uma lista separada por vírgulas. |

