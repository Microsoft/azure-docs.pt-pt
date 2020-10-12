---
title: Utilizar a CLI do Azure Digital Twins
titleSuffix: Azure Digital Twins
description: Veja como começar e usar o CLI das Gémeas Digitais Azure.
author: baanders
ms.author: baanders
ms.date: 05/25/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 2c642b2441d1f30c31e707a237732e028f548ac5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89298186"
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

Os comandos Azure Digital Twins fazem parte da [extensão Azure IoT para Azure CLI](https://github.com/Azure/azure-iot-cli-extension). Pode ver a documentação de referência para estes comandos como parte do conjunto de `az iot` comandos: [az dt](https://docs.microsoft.com/cli/azure/ext/azure-iot/dt?view=azure-cli-latest).

Pode certificar-se de que tem a versão mais recente da extensão com estes passos. Pode executar estes comandos na [Azure Cloud Shell](../cloud-shell/overview.md) ou num [Azure CLI local.](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)

[!INCLUDE [digital-twins-cloud-shell-extensions.md](../../includes/digital-twins-cloud-shell-extensions.md)]

## <a name="next-steps"></a>Passos seguintes

Para uma alternativa aos comandos CLI, consulte como gerir uma instância Azure Digital Twins usando APIs e SDKs:
* [*Como fazer: Use as APIs e SDKs de gémeos digitais Azure*](how-to-use-apis-sdks.md)
