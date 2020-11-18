---
title: 'Quickstart: Implementar AZure API para FHIR utilizando O Azure CLI'
description: Neste arranque rápido, você aprenderá a implementar Azure API para FHIR em Azure usando o Azure CLI.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 10/15/2019
ms.author: cavoeg
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3340cd0a39b0f5311487fec5b05d37e3376d433a
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659256"
---
# <a name="quickstart-deploy-azure-api-for-fhir-using-azure-cli"></a>Quickstart: Implementar AZure API para FHIR utilizando O Azure CLI

Neste arranque rápido, você aprenderá a implementar Azure API para FHIR em Azure usando o Azure CLI.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="add-healthcareapis-extension"></a>Adicionar extensão HealthcareAPIs

```azurecli-interactive
az extension add --name healthcareapis
```

Obtenha uma lista de comandos para HealthcareAPIs:

```azurecli-interactive
az healthcareapis --help
```

## <a name="create-azure-resource-group"></a>Criar Grupo de Recursos Azure

Escolha um nome para o grupo de recursos que conterá a API Azure para FHIR e crie-o:

```azurecli-interactive
az group create --name "myResourceGroup" --location westus2
```

## <a name="deploy-the-azure-api-for-fhir"></a>Implementar a API Azure para fHIR

```azurecli-interactive
az healthcareapis create --resource-group myResourceGroup --name nameoffhiraccount --kind fhir-r4 --location westus2 
```

## <a name="fetch-fhir-api-capability-statement"></a>Obter declaração de capacidade da API FHIR

Obtenha uma declaração de capacidade da API FHIR com:

```azurecli-interactive
curl --url "https://nameoffhiraccount.azurehealthcareapis.com/metadata"
```

## <a name="clean-up-resources"></a>Limpar os recursos

Se não continuar a utilizar esta aplicação, elimine o grupo de recursos com os seguintes passos:

```azurecli-interactive
az group delete --name "myResourceGroup"
```

## <a name="next-steps"></a>Próximos passos

Neste guia de arranque rápido, colocou a API Azure para fHIR na sua subscrição. Para definir definições adicionais na sua API Azure para FHIR, proceda às definições adicionais como orientar. Se estiver pronto para começar a usar a API Azure para FHIR, leia mais sobre como registar as aplicações.

>[!div class="nextstepaction"]
>[Definições adicionais em API Azure para FHIR](azure-api-for-fhir-additional-settings.md)

>[!div class="nextstepaction"]
>[Visão geral das candidaturas do registo](fhir-app-registration.md)
