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
ms.openlocfilehash: 10bc0ac3a61ddba22dea46dfdd47a0305e2d9149
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103019703"
---
# <a name="quickstart-deploy-azure-api-for-fhir-using-azure-cli"></a>Quickstart: Implementar AZure API para FHIR utilizando O Azure CLI

Neste arranque rápido, você aprenderá a implementar Azure API para FHIR em Azure usando o Azure CLI.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

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

## <a name="next-steps"></a>Passos seguintes

Neste guia de arranque rápido, colocou a API Azure para fHIR na sua subscrição. Para definir definições adicionais na sua API Azure para FHIR, proceda às definições adicionais como orientar. Se estiver pronto para começar a usar a API Azure para FHIR, leia mais sobre como registar as aplicações.

>[!div class="nextstepaction"]
>[Definições adicionais em API Azure para FHIR](azure-api-for-fhir-additional-settings.md)

>[!div class="nextstepaction"]
>[Visão geral das candidaturas do registo](fhir-app-registration.md)
