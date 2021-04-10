---
title: Utilizar a CLI do Azure Digital Twins
titleSuffix: Azure Digital Twins
description: Veja como começar e usar o CLI das Gémeas Digitais Azure.
author: baanders
ms.author: baanders
ms.date: 05/25/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 261dceb70a6059c76dbe3bd1d7636eee5d9d77bc
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105936296"
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

O CLI Azure irá automaticamente pedir-lhe para instalar a extensão na primeira utilização de um comando que o exija.

Em alternativa, pode utilizar o seguinte comando para instalar a extensão a qualquer momento (ou atualizá-la se se verificar que já tem uma versão mais antiga). O comando pode ser executado na [Azure Cloud Shell](../cloud-shell/overview.md) ou num [Azure CLI local.](/cli/azure/install-azure-cli)

```azurecli-interactive
az extension add --upgrade -n azure-iot
```

## <a name="next-steps"></a>Passos seguintes

Explore o CLI e o seu conjunto completo de comandos através dos documentos de referência:
* [referência de comando *az dt*](/cli/azure/ext/azure-iot/dt)