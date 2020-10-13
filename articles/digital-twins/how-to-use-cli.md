---
title: Utilizar a CLI do Azure Digital Twins
titleSuffix: Azure Digital Twins
description: Veja como começar e usar o CLI das Gémeas Digitais Azure.
author: baanders
ms.author: baanders
ms.date: 05/25/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 65462937db4711066bdb8b31cc22de508321d701
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/12/2020
ms.locfileid: "91950912"
---
# <a name="use-the-azure-digital-twins-cli"></a>Utilizar a CLI do Azure Digital Twins

Além de gerir a sua instância Azure Digital Twins no portal Azure, a Azure Digital Twins tem uma **interface de linha de comando (CLI)** que pode utilizar para realizar a maioria das principais ações com o serviço, incluindo:
* Gerir um exemplo de Gémeos Digitais Azure
* Gestão de modelos
* Gerir gémeos digitais
* Gerir relações gémeas
* Pontos finais configurados
* Gerir [rotas](concepts-route-events.md)
* Configurar a [segurança](concepts-security.md) através do controlo de acesso baseado em funções (RBAC)

[!INCLUDE [digital-twins-known-issue-cloud-shell](../../includes/digital-twins-known-issue-cloud-shell.md)]

## <a name="uses-deploy-and-validate"></a>Utilizações (implantar e validar)

Além de gerir geralmente o seu caso, o CLI é também uma ferramenta útil para implementação e validação.
* Os comandos do plano de controlo podem ser utilizados para tornar a implantação de uma nova instância repetível ou automatizada.
* Os comandos do plano de dados podem ser usados para verificar rapidamente os valores no seu caso, e verificar se as operações foram concluídas como esperado.

## <a name="get-the-extension"></a>Obtenha a extensão

Os comandos Azure Digital Twins fazem parte da [extensão Azure IoT para Azure CLI](https://github.com/Azure/azure-iot-cli-extension). Pode ver a lista completa de comandos e a sua utilização como parte da documentação de referência para o conjunto de `az iot` comandos: referência de comando [ *az dt* ](https://docs.microsoft.com/cli/azure/ext/azure-iot/dt?view=azure-cli-latest&preserve-view=true).

Pode certificar-se de que tem a versão mais recente da extensão com estes passos. Pode executar estes comandos na [Azure Cloud Shell](../cloud-shell/overview.md) ou num [Azure CLI local.](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true)

[!INCLUDE [digital-twins-cloud-shell-extensions.md](../../includes/digital-twins-cloud-shell-extensions.md)]

## <a name="next-steps"></a>Passos seguintes

Explore o CLI e o seu conjunto completo de comandos através dos documentos de referência:
* [referência de comando *az dt*](https://docs.microsoft.com/cli/azure/ext/azure-iot/dt?view=azure-cli-latest&preserve-view=true)
