---
title: 'Azure CLI: Implementar servidor FHIR de código aberto para Azure - Azure API para Azure'
description: Este quickstart explica como implementar o servidor FHIR do Microsoft Open Source para o Azure.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 02/07/2019
ms.author: matjazl
ms.custom: devx-track-azurecli
ms.openlocfilehash: 10af71afd8843e75d5df3be57c909c56a7abca01
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87843594"
---
# <a name="quickstart-deploy-open-source-fhir-server-using-azure-cli"></a>Quickstart: Implementar o servidor FHIR open source usando O Azure CLI

Neste arranque rápido, você aprenderá a implementar um servidor FHIR Open Source &reg; em Azure usando o Azure CLI.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-resource-group"></a>Criar grupo de recursos

Escolha um nome para o grupo de recursos que contenha os recursos aprovisionados e crie-o:

```azurecli-interactive
servicename="myfhirservice"
az group create --name $servicename --location westus2
```

## <a name="deploy-template"></a>Implementar o modelo

O Microsoft FHIR Server for Azure [GitHub Repository](https://github.com/Microsoft/fhir-server) contém um modelo que irá implementar todos os recursos necessários. Desdobre-o com:

```azurecli-interactive
az group deployment create -g $servicename --template-uri https://raw.githubusercontent.com/Microsoft/fhir-server/master/samples/templates/default-azuredeploy.json --parameters serviceName=$servicename
```

## <a name="verify-fhir-server-is-running"></a>Verifique se o servidor FHIR está em execução

Obtenha uma declaração de capacidade do servidor FHIR com:

```azurecli-interactive
metadataurl="https://${servicename}.azurewebsites.net/metadata"
curl --url $metadataurl
```

Levará um minuto ou mais para o servidor responder pela primeira vez.

## <a name="clean-up-resources"></a>Limpar os recursos

Se não continuar a utilizar esta aplicação, elimine o grupo de recursos com os seguintes passos:

```azurecli-interactive
az group delete --name $servicename
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, implementou o Microsoft Open Source FHIR Server para Azure na sua subscrição. Para aprender a aceder à API FHIR usando o Carteiro, dirija-se ao tutorial do Carteiro.
 
>[!div class="nextstepaction"]
>[Acesso FHIR API usando Carteiro](access-fhir-postman-tutorial.md)
