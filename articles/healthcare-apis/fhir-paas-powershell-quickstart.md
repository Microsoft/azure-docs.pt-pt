---
title: 'Quickstart: Implementar API Azure para FHIR utilizando PowerShell'
description: Neste arranque rápido, você aprenderá a implementar Azure API para FHIR usando PowerShell.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 10/15/2019
ms.author: cavoeg
ms.openlocfilehash: 2cb50f2ae98dab1e64e01498e0913d1932e455cd
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92339412"
---
# <a name="quickstart-deploy-azure-api-for-fhir-using-powershell"></a>Quickstart: Implementar API Azure para FHIR utilizando PowerShell

Neste arranque rápido, você aprenderá a implementar Azure API para FHIR usando PowerShell.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="register-the-azure-api-for-fhir-resource-provider"></a>Registe a AZure API para o fornecedor de recursos FHIR

Se o `Microsoft.HealthcareApis` fornecedor de recursos ainda não estiver registado para a sua subscrição, pode registá-lo com:

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.HealthcareApis
```

## <a name="create-azure-resource-group"></a>Criar grupo de recursos Azure

```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroupName" -Location westus2
```

## <a name="deploy-azure-api-for-fhir"></a>Implementar o Azure API for FHIR

```azurepowershell-interactive
New-AzHealthcareApisService -Name nameoffhirservice -ResourceGroupName myResourceGroupName -Location westus2 -Kind fhir-R4
```

> [!NOTE]
> Dependendo da versão do `Az` módulo PowerShell que instalou, o servidor FHIR forifado pode ser configurado para utilizar [o RBAC local](configure-local-rbac.md) e ter o atualmente assinado no utilizador PowerShell definido na lista de IDs de objetos de identidade permitidos para o serviço FHIR implantado. Para a frente, recomendamos que [utilize o Azure RBAC](configure-azure-rbac.md) para atribuir funções de plano de dados e poderá ter de eliminar este ID de objeto de utilizadores após a implementação para ativar o modo Azure RBAC.


## <a name="fetch-capability-statement"></a>Declaração de capacidade de busca

Poderá validar que a conta Azure API para a conta FHIR está a funcionar através da obtenção de uma declaração de capacidade do FHIR:

```azurepowershell-interactive
$metadata = Invoke-WebRequest -Uri "https://nameoffhirservice.azurehealthcareapis.com/metadata"
$metadata.RawContent
```

## <a name="clean-up-resources"></a>Limpar os recursos

Se não continuar a utilizar esta aplicação, elimine o grupo de recursos com os seguintes passos:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupName
```

## <a name="next-steps"></a>Passos seguintes

Neste guia de arranque rápido, colocou a API Azure para fHIR na sua subscrição. Para definir definições adicionais na sua API Azure para FHIR, proceda às definições adicionais como orientar. Se estiver pronto para começar a usar a API Azure para FHIR, leia mais sobre como registar as aplicações.

>[!div class="nextstepaction"]
>[Definições adicionais em API Azure para FHIR](azure-api-for-fhir-additional-settings.md)

>[!div class="nextstepaction"]
>[Visão geral das candidaturas do registo](fhir-app-registration.md)
