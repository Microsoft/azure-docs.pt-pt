---
title: Registar uma ligação de pré-visualização do serviço de peering utilizando o CLI Azure
description: Saiba como registar uma ligação de Serviço de Peering utilizando o CLI Azure
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/2/2020
ms.author: derekol
ms.openlocfilehash: e7b696ba052b2aca9e14628327c07275845607ad
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94540591"
---
# <a name="register-a-peering-service-connection-by-using-the-azure-cli"></a>Registar uma ligação de Serviço de Peering utilizando o CLI Azure

O Azure Peering Service é um serviço de networking que melhora a conectividade do cliente com os serviços na nuvem da Microsoft, tais como o Microsoft 365, Dynamics 365, software como serviço (SaaS), Azure ou quaisquer serviços da Microsoft acessíveis através da internet pública. Neste artigo, você aprenderá a registar uma ligação de Serviço de Peering utilizando o CLI Azure.

- Este artigo requer a versão 2.0.28 ou posterior do Azure CLI. Execute o comando [az version](/cli/azure/reference-index#az_version) para localizar a versão e as bibliotecas dependentes instaladas. Para atualizar para a versão mais recente, execute o comando [az upgrade](/cli/azure/reference-index#az_upgrade).

## <a name="prerequisites"></a>Pré-requisitos 

Deve ter o seguinte:

### <a name="azure-account"></a>Conta do Azure

Deve ter uma conta Microsoft Azure válida e ativa. Esta conta é necessária para configurar a ligação serviço de peering. O Serviço de Observação é um recurso dentro das subscrições Azure.

### <a name="connectivity-provider"></a>Fornecedor de conectividade

Pode trabalhar com um fornecedor de serviços de internet ou um parceiro de intercâmbio de internet para obter o Peering Service para ligar a sua rede à rede Microsoft.

Certifique-se de que os fornecedores de conectividade são parceiros com a Microsoft.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

- Este artigo requer a versão 2.0.28 ou posterior do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada.

### <a name="1-select-your-subscription"></a>1. Selecione a sua subscrição

Selecione a subscrição para a qual pretende registar a ligação Serviço de Peering.

```azurecli-interactive
az account set --subscription "<subscription ID>"
```

Se ainda não tiver um grupo de recursos, tem de criar um antes de registar a sua ligação serviço de peering. Pode criar um grupo de recursos executando o seguinte comando:

```azurecli-interactive
az group create -n MyResourceGroup -l "West US"
```

### <a name="2-register-your-subscription-with-the-resource-provider-and-feature-flag"></a>2. Registe a sua subscrição junto do fornecedor de recursos e da bandeira de recurso

Antes de avançar para os passos de registo da ligação do Serviço de Pares utilizando o CLI Azure, registe a sua subscrição com o fornecedor de recursos e bandeira de recurso utilizando o CLI Azure. Os comandos Azure CLI são especificados aqui:

```azurecli-interactive

az feature register --namespace Microsoft.Peering --name AllowPeeringService

```

### <a name="3-register-the-peering-service-connection"></a>3. Registar a ligação do Serviço de Observação

Registe a ligação de Serviço de Pares utilizando o seguinte conjunto de comandos através do Azure CLI. Este exemplo regista a ligação peering Service chamada myPeeringService.

```azurecli-interactive
az peering service create : Create peering service\
  --location -l \
  --name myPeeringService\
  --resource-group -g MyResourceGroup\
  --peering-service-location\
  --peering-service-provider\
  --tags
```

### <a name="4-register-the-prefix"></a>4. Registar o prefixo

Registe o prefixo fornecido pelo fornecedor de conectividade executando os seguintes comandos através do Azure CLI. Este exemplo regista o prefixo chamado myPrefix.

```azurecli-interactive
az peering service prefix create \
  --name  myPrefix\
  --peering-service-name myPeeringService\
  --resource-group  -g myResourceGroup\
```

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre a ligação do Serviço de Peering, consulte [a ligação do Serviço de Peering](connection.md).
- Para saber mais sobre a telemetria de conexão peering Service, consulte [a telemetria de conexão peering service](connection-telemetry.md).
- Para medir a telemetria, consulte [a telemetria de ligação medida](measure-connection-telemetry.md).
- Para registar a ligação utilizando a Azure PowerShell, consulte [registar uma ligação de Serviço de Pares - Azure PowerShell](powershell.md).
- Para registar a ligação utilizando o portal Azure, consulte [registar uma ligação de Serviço de Pares - portal Azure](azure-portal.md).
