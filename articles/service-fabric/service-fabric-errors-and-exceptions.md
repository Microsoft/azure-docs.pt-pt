---
title: Exceções do Common FabricClient lançadas
description: Descreve as exceções e erros comuns que podem ser lançados pelas APIs do FabricClient enquanto executam as operações de aplicação e gestão de clusters.
author: oanapl
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: oanapl
ms.openlocfilehash: 9ad3097a490d4728e05ea90652c17c24b79cac2c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75457928"
---
# <a name="common-exceptions-and-errors-when-working-with-the-fabricclient-apis"></a>Exceções e erros comuns ao trabalhar com as APIs FabricClient
As APIs [do FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) permitem que os administradores de cluster e aplicação executem tarefas administrativas numa aplicação, serviço ou cluster de Tecido de Serviço. Por exemplo, implementação de aplicações, atualização e remoção, verificação da saúde de um cluster, ou testar um serviço. Os desenvolvedores de aplicações e administradores de cluster podem usar as APIs do FabricClient para desenvolver ferramentas para gerir o cluster e aplicações do Tecido de Serviço.

Existem muitos tipos diferentes de operações que podem ser realizadas usando o FabricClient.  Cada método pode lançar exceções para erros devido a inserção incorreta, erros de tempo de execução ou problemas de infraestrutura transitórios.  Consulte a documentação de referência da API para saber quais as exceções que são lançadas por um método específico. Existem algumas exceções, no entanto, que podem ser lançadas por muitas APIs [de FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) diferentes. A tabela seguinte enumera as exceções que são comuns em todas as APIs do FabricClient.

| Exceção | Jogado quando |
| --- |:--- |
| [System.Fabric.FabricObjectClosedException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricobjectclosedexception) |O objeto [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) está em estado fechado. Elimine o objeto [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) que está a utilizar e instanteie um novo objeto [FabricClient.](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) |
| [System.TimeoutException](https://docs.microsoft.com/dotnet/core/api/system.timeoutexception) |A operação está esgotada. [OperationTimedOut](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode) é devolvido quando a operação leva mais do que maxOperationTimeout para completar. |
| [Sistema.Não autorizadoAccessException](https://docs.microsoft.com/dotnet/core/api/system.unauthorizedaccessexception) |A verificação de acesso à operação falhou. E_ACCESSDENIED é devolvido. |
| [System.Fabric.Fabric.FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception) |Ocorreu um erro de tempo de execução durante a execução da operação. Qualquer um dos métodos FabricClient pode potencialmente lançar [A Exceção do Tecido,](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception)a propriedade [ErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception.ErrorCode) indica a causa exata da exceção. Os códigos de erro são definidos na enumeração [do Código do Erro do Tecido.](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode) |
| [System.Fabric.FabricTransientException](https://docs.microsoft.com/dotnet/api/system.fabric.fabrictransientexception) |A operação falhou devido a algum tipo de condição de erro transitório. Por exemplo, uma operação pode falhar porque um quórum de réplicas não é temporariamente alcançável. As exceções transitórias correspondem a operações falhadas que podem ser novamente tentadas. |

Alguns erros comuns do [ErrorErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode) que podem ser devolvidos numa Violação do [Tecido:](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception)

| Erro | Condição |
| --- |:--- |
| Erro de Comunicação |Um erro de comunicação fez com que a operação falhasse, retentar a operação. |
| InvalidCredentialType |O tipo de credencial é inválido. |
| InvalidX509FindType |O X509FindType é inválido. |
| InvalidX509StoreLocation |A localização da loja X509 é inválida. |
| Nome de loja InvalidX509 |O nome da loja X509 é inválido. |
| Impressão de polegar InvalidX509 |A cadeia de impressão digital do certificado X509 é inválida. |
| Nível de Proteção Inválido |O nível de proteção é inválido. |
| Loja InvalidX509 |A loja de certificados X509 não pode ser aberta. |
| Nome de assunto inválido |O nome do assunto é inválido. |
| Lista de Nomes Comuns Inválidos |O formato de cadeia de lista de nomes comuns é inválido. Deve ser uma lista separada de vírinas. |

