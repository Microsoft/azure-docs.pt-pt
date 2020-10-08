---
title: 'PowerShell: Implementar o servidor FHIR para a Azure – AZure API para FHIR'
description: Este quickstart explica como implementar o servidor FHIR do Microsoft Open Source utilizando o PowerShell.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: c6c1a7e21f0a1554c67c7f1860a2bd3382c941f5
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/07/2020
ms.locfileid: "91817959"
---
# <a name="quickstart-deploy-open-source-fhir-server-using-powershell"></a>Quickstart: Implementar o servidor FHIR open source usando PowerShell

Neste arranque rápido, aprenda a implementar o servidor FHIR do Microsoft Open Source para o Azure Using PowerShell.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Escolha um nome para o grupo de recursos que contenha os recursos aprovisionados e crie-o:

```azurepowershell-interactive
$fhirServiceName = "MyFhirService"
$rg = New-AzResourceGroup -Name $fhirServiceName -Location westus2
```

## <a name="deploy-the-fhir-server-template"></a>Implemente o modelo de servidor FHIR

O Microsoft FHIR Server for Azure [GitHub Repository](https://github.com/Microsoft/fhir-server) contém um modelo que irá implementar todos os recursos necessários. O processo de implantação demora vários minutos, conforme os recursos necessários do Azure são criados e configurados. Desdobre-o com:

```azurepowershell-interactive
New-AzResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Microsoft/fhir-server/master/samples/templates/default-azuredeploy.json -ResourceGroupName $rg.ResourceGroupName -serviceName $fhirServiceName
```

> [!NOTE]
> Se ainda não tiver iniciado sessão, faça primeiro os seguintes comandos.

```azurepowershell-interactive
Connect-AzAccount
get-azsubscription
Set-AzContext -SubscriptionId yoursubscriptionid
```

Para utilizar um grupo de recursos existente, altere $rg valores na linha de definição variável $rg e na linha de comando de implantação do modelo Azure ARM, como indicado no código.

```azurepowershell-interactive
$fhirServiceName = "MyFhirService"
$rg = "MyExistingResourceGroupName"
New-AzResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Microsoft/fhir-server/master/samples/templates/default-azuredeploy.json -ResourceGroupName $rg -serviceName $fhirServiceName
```

## <a name="verify-fhir-server-is-running"></a>Verifique se o servidor FHIR está em execução

```azurepowershell-interactive
$metadataUrl = "https://" + $fhirServiceName + ".azurewebsites.net/metadata" 
$metadata = Invoke-WebRequest -Uri $metadataUrl
$metadata.RawContent
```

Levará um minuto ou mais para o servidor responder pela primeira vez.

## <a name="clean-up-resources"></a>Limpar os recursos

Se não continuar a utilizar esta aplicação, elimine o grupo de recursos com os seguintes passos:

```azurepowershell-interactive
Remove-AzResourceGroup -Name $rg.ResourceGroupName
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, implementou o Microsoft Open Source FHIR Server para Azure na sua subscrição. Para aprender a aceder à API FHIR usando o Carteiro, dirija-se ao tutorial do Carteiro.
 
>[!div class="nextstepaction"]
>[Acesso FHIR API usando Carteiro](access-fhir-postman-tutorial.md)
