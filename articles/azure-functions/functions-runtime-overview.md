---
title: Visão geral de Azure Functions Runtime | Microsoft Docs
description: Visão geral da visualização de Azure Functions Runtime
services: functions
author: apwestgarth
manager: stefsch
ms.assetid: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: anwestg
ms.openlocfilehash: e67041ca78ba328fad132cc556b12d780eb9b318
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70095984"
---
# <a name="azure-functions-runtime-overview-preview"></a>Visão geral de Azure Functions Runtime (visualização)

[!INCLUDE [intro](../../includes/functions-runtime-preview-note.md)]

A Azure Functions Runtime (versão prévia) fornece uma nova maneira de aproveitar a simplicidade e a flexibilidade do modelo de programação de Azure Functions local. Criado com base nas mesmas raízes de código aberto que Azure Functions, Azure Functions Runtime é implantado localmente para fornecer uma experiência de desenvolvimento quase idêntica como o serviço de nuvem.

![Azure Functions Runtime o portal de visualização][1]

O Azure Functions Runtime fornece uma maneira de experimentar Azure Functions antes de confirmar a nuvem. Dessa forma, os ativos de código que você criar poderão ser levados com você para a nuvem quando você migrar.  O tempo de execução também abre novas opções para você, como usar o poder de computação sobressalente de seus computadores locais para executar processos em lotes durante a noite. Você também pode usar dispositivos em sua organização para enviar dados condicionalmente para outros sistemas, tanto localmente quanto na nuvem.

O Azure Functions Runtime consiste em duas partes:

* Função de gerenciamento de Azure Functions Runtime
* Azure Functions Runtime função de trabalho

## <a name="azure-functions-management-role"></a>Função de gerenciamento de Azure Functions

A função de gerenciamento de Azure Functions fornece um host para o gerenciamento de suas funções locais. Essa função executa as seguintes tarefas:

* Hospedagem do Portal de Gerenciamento de Azure Functions, que é o mesmo que você vê no [portal do Azure](https://portal.azure.com). O portal fornece uma experiência consistente que permite desenvolver suas funções da mesma maneira como você faria no portal do Azure.
* Distribuindo funções em vários trabalhos do functions.
* Fornecer um ponto de extremidade de publicação para que você possa publicar suas funções diretamente do Microsoft Visual Studio baixando e importando o perfil de publicação.

## <a name="azure-functions-worker-role"></a>Azure Functions função de trabalho

As funções de trabalho Azure Functions são implantadas em contêineres do Windows e são onde o código de função é executado.  Você pode implantar várias funções de trabalho em toda a sua organização e essa opção é uma maneira importante na qual os clientes podem fazer uso de capacidade de computação sobressalente.  Um exemplo de onde a computação sobressalente existe em muitas organizações é que as máquinas são ativadas constantemente, mas não estão sendo usadas por longos períodos de tempo.

## <a name="minimum-requirements"></a>Requisitos mínimos

Para começar a usar o Azure Functions Runtime, você deve ter um computador com a atualização do Windows Server 2016 ou do Windows 10 para criadores com acesso a uma instância do SQL Server.

## <a name="next-steps"></a>Próximos Passos

Instalar a versão [prévia do Azure Functions Runtime](https://aka.ms/azafrdoc)

<!--Image references-->
[1]: ./media/functions-runtime-overview/AzureFunctionsRuntime_Portal.png
