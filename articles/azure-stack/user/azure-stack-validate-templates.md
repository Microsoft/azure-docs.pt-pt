---
title: Utilize uma ferramenta de validação de modelo para verificar modelos para o Azure Stack | Documentos da Microsoft
description: Modelos de verificação para a implementação para o Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: d9e6aee1-4cba-4df5-b5a3-6f38da9627a3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/08/2018
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 12/27/2018
ms.openlocfilehash: 650b868762299725927623134039e87bbee9f4c2
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59277515"
---
# <a name="check-your-templates-for-azure-stack-with-the-template-validation-tool"></a>Verifique os seus modelos para o Azure Stack com a ferramenta de validação de modelo

*Aplica-se a Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Pode usar a ferramenta de validação de modelo para verificar se seu Gerenciador de recursos do Azure [modelos](azure-stack-arm-templates.md) está pronto para implementar no Azure Stack. A ferramenta de validação de modelo está disponível como parte das ferramentas do Azure Stack. Baixe as ferramentas do Azure Stack, utilizando os passos descritos na [baixe as ferramentas do GitHub](azure-stack-powershell-download.md) artigo.

## <a name="overview"></a>Descrição geral

Para validar um modelo, tem de criar primeiro um arquivo de recursos de nuvem e, em seguida, execute a ferramenta de validação. Utilize os seguintes módulos do PowerShell a partir das ferramentas do Azure Stack:

- Na **CloudCapabilities** pasta: `AzureRM.CloudCapabilities.psm1` cria um ficheiro JSON de capacidades de cloud que representa os serviços e as versões numa cloud do Azure Stack.
- Na **TemplateValidator** pasta: `AzureRM.TemplateValidator.psm1` utiliza um ficheiro JSON de capacidades de cloud para testar modelos para a implementação no Azure Stack.

## <a name="build-the-cloud-capabilities-file"></a>Compilar o arquivo de recursos de cloud

Antes de utilizar o validador de modelo, execute o **AzureRM.CloudCapabilities** módulo do PowerShell para criar um ficheiro JSON.

>[!NOTE]
> Se atualizar o sistema integrado, ou adicionar quaisquer novos serviços ou as extensões de virtual, deve executar novamente este módulo.

1. Certifique-se de que tem conectividade ao Azure Stack. Estes passos podem ser executados a partir do anfitrião de kit de desenvolvimento do Azure Stack ou, pode utilizar um [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) para ligar a partir da sua estação de trabalho.
2. Importar os **AzureRM.CloudCapabilities** módulo do PowerShell:

    ```powershell
    Import-Module .\CloudCapabilities\AzureRM.CloudCapabilities.psm1
    ```

3. Utilize o `Get-CloudCapabilities` cmdlet para obter versões de serviço e criar um ficheiro JSON de capacidades de cloud. Se não especificar **- OutputPath**, o arquivo AzureCloudCapabilities.Json é criado no diretório atual. Utilize a localização do Azure real:

    ```powershell
    Get-AzureRMCloudCapability -Location <your location> -Verbose
    ```

## <a name="validate-templates"></a>Validar modelos

Utilize estes passos para validar os modelos utilizando o **AzureRM.TemplateValidator** módulo do PowerShell. Pode utilizar os seus próprios modelos ou validar a [modelos de início rápido do Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates).

1. Importar os **AzureRM.TemplateValidator.psm1** módulo do PowerShell:

    ```powershell
    cd "c:\AzureStack-Tools-master\TemplateValidator"
    Import-Module .\AzureRM.TemplateValidator.psm1
    ```

2. Execute a validação do modelo:

    ```powershell
    Test-AzureRMTemplate -TemplatePath <path to template.json or template folder> `
    -CapabilitiesPath <path to cloudcapabilities.json> `
    -Verbose
    ```

Erros ou avisos de validação de modelo, são apresentados na consola do PowerShell e escritos para um ficheiro HTML com o diretório de origem. Captura de ecrã seguinte é um exemplo de um relatório de validação:

![Relatório de validação de modelo](./media/azure-stack-validate-templates/image1.png)

### <a name="parameters"></a>Parâmetros

A validação do modelo suporta os seguintes parâmetros.

| Parâmetro | Descrição | Necessário |
| ----- | -----| ----- |
| TemplatePath | Especifica o caminho para recursivamente encontrar modelos Azure Resource Manager. | Sim |
| TemplatePattern | Especifica o nome dos arquivos de modelo para fazer corresponder. | Não |
| CapabilitiesPath | Especifica o caminho para o ficheiro JSON de capacidades de cloud. | Sim |
| IncludeComputeCapabilities | Inclui avaliação dos recursos de IaaS, como tamanhos de VM e extensões de VM. | Não |
| IncludeStorageCapabilities | Inclui avaliação de recursos de armazenamento, como tipos SKU. | Não |
| Relatório | Especifica o nome do relatório HTML gerado. | Não |
| Verboso | Registos de erros e avisos para a consola. | Não|

### <a name="examples"></a>Exemplos

Neste exemplo valida todos os [modelos de início rápido do Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates) transferido para o armazenamento local. O exemplo também valida os tamanhos de máquinas virtuais e extensões de recursos do Kit de desenvolvimento do Azure Stack:

```powershell
test-AzureRMTemplate -TemplatePath C:\AzureStack-Quickstart-Templates `
-CapabilitiesPath .\TemplateValidator\AzureStackCloudCapabilities_with_AddOns_20170627.json `
-TemplatePattern MyStandardTemplateName.json `
-IncludeComputeCapabilities `
-Report TemplateReport.html
```

## <a name="next-steps"></a>Passos Seguintes

- [Implementar modelos para o Azure Stack](azure-stack-arm-templates.md)
- [Desenvolver modelos para o Azure Stack](azure-stack-develop-templates.md)
