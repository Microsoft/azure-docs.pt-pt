---
title: Utilizar a CLI do Azure Digital Twins
titleSuffix: Azure Digital Twins
description: Veja como começar e usar o CLI das Gémeas Digitais Azure.
author: baanders
ms.author: baanders
ms.date: 05/25/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: a24b8b18dd109f1d8ed5acaa7de55ce5a3cc1eb9
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102201112"
---
# <a name="use-the-azure-digital-twins-cli"></a>Utilizar a CLI do Azure Digital Twins

Além de gerir a sua instância Azure Digital Twins no portal Azure, a Azure Digital Twins tem um **conjunto de comando para o [CLI Azure](/cli/azure/what-is-azure-cli)** que pode utilizar para realizar a maioria das principais ações com o serviço, incluindo:
* Gerir um exemplo de Gémeos Digitais Azure
* Gestão de modelos
* Gerir gémeos digitais
* Gerir relações gémeas
* Pontos finais configurados
* Gerir [rotas](concepts-route-events.md)
* Configurar a [segurança](concepts-security.md) através do controlo de acesso baseado em funções Azure (Azure RBAC)

O conjunto de comandos é chamado **az dt,** e faz parte da [extensão Azure IoT para Azure CLI](https://github.com/Azure/azure-iot-cli-extension). Pode ver a lista completa de comandos e a sua utilização como parte da documentação de referência para o conjunto de `az iot` comandos: referência de comando [ *az dt*](/cli/azure/ext/azure-iot/dt).

## <a name="uses-deploy-and-validate"></a>Utilizações (implantar e validar)

Além de gerir geralmente o seu caso, o CLI é também uma ferramenta útil para implementação e validação.
* Os comandos do plano de controlo podem ser utilizados para tornar a implantação de uma nova instância repetível ou automatizada.
* Os comandos do plano de dados podem ser usados para verificar rapidamente os valores no seu caso, e verificar se as operações foram concluídas como esperado.

## <a name="get-the-command-set"></a>Obter o conjunto de comando

Os comandos Azure Digital Twins fazem parte da [extensão Azure IoT para Azure CLI (azure-iot)](https://github.com/Azure/azure-iot-cli-extension)– por isso siga estes passos para se certificar de que tem a mais recente `azure-iot` extensão com os comandos **az dt.**

### <a name="cli-version-requirements"></a>Requisitos da versão CLI

Se estiver a utilizar o Azure CLI com PowerShell, o pacote de extensão requer que a sua versão Azure CLI seja **2.3.1** ou superior.

Pode consultar a versão do seu Azure CLI com este comando CLI:
```azurecli
az --version
```

Para obter instruções sobre como instalar ou atualizar o CLI Azure para uma versão mais recente, consulte [*instalar o Azure CLI*](/cli/azure/install-azure-cli).

### <a name="get-the-extension"></a>Obtenha a extensão

Pode certificar-se de que tem a versão mais recente da `azure-iot` extensão com estes passos. Pode executar estes comandos na [Azure Cloud Shell](../cloud-shell/overview.md) ou num [Azure CLI local.](/cli/azure/install-azure-cli)

[!INCLUDE [digital-twins-cloud-shell-extensions.md](../../includes/digital-twins-cloud-shell-extensions.md)]

## <a name="next-steps"></a>Passos seguintes

Explore o CLI e o seu conjunto completo de comandos através dos documentos de referência:
* [referência de comando *az dt*](/cli/azure/ext/azure-iot/dt)