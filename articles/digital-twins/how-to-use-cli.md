---
title: Utilizar a CLI do Azure Digital Twins
titleSuffix: Azure Digital Twins
description: Veja como começar e usar o CLI das Gémeas Digitais Azure.
author: baanders
ms.author: baanders
ms.date: 05/25/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 53b20ded8e4b4a003beff1ef8489ecd9ff3451ac
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84725307"
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

Os comandos Azure Digital Twins fazem parte da [extensão Azure IoT para Azure CLI](https://github.com/Azure/azure-iot-cli-extension). Pode ver a documentação de referência para estes comandos como parte do conjunto de `az iot` comandos: [az dt](https://docs.microsoft.com/cli/azure/ext/azure-iot/dt?view=azure-cli-latest).

## <a name="deploy-and-validate"></a>Implementar e validar

Além de gerir geralmente o seu caso, o CLI é também uma ferramenta útil para implementação e validação.
* Os comandos do plano de controlo podem ser utilizados para tornar a implantação de uma nova instância repetível ou automatizada.
* Os comandos do plano de dados podem ser usados para verificar rapidamente os valores no seu caso, e verificar se as operações foram concluídas como esperado.

## <a name="next-steps"></a>Próximos passos

Para uma alternativa aos comandos CLI, consulte como gerir uma instância Azure Digital Twins usando APIs e SDKs:
* [Como fazer: Use as APIs e SDKs de gémeos digitais Azure](how-to-use-apis-sdks.md)
