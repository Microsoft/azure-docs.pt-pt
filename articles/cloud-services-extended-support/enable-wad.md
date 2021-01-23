---
title: Aplicar a extensão de diagnóstico do Windows Azure nos Serviços cloud (suporte alargado)
description: Aplicar a extensão de diagnóstico do Windows Azure para serviços cloud (suporte alargado)
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 4511ad979312d58e0a1b9cce9b1280e9ca059007
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98744824"
---
# <a name="apply-the-windows-azure-diagnostics-extension-in-cloud-services-extended-support"></a>Aplicar a extensão de diagnóstico do Windows Azure nos Serviços cloud (suporte alargado) 
Pode monitorizar as principais métricas de desempenho para qualquer serviço na nuvem. Cada função de serviço na nuvem recolhe dados mínimos: utilização do CPU, utilização da rede e utilização do disco. Se o serviço de nuvem tiver a extensão Microsoft.Azure.Diagnostics aplicada a uma função, essa função pode recolher pontos adicionais de dados. Para mais informações, consulte [a visão geral das extensões](extensions.md)

A extensão do Windows Azure Diagnostics pode ser ativada para serviços cloud (suporte alargado) através do modelo [PowerShell](deploy-powershell.md) ou [ARM](deploy-template.md)

## <a name="apply-windows-azure-diagnostics-extension-using-powershell"></a>Aplicar extensão de diagnóstico do Windows Azure usando PowerShell

```powershell
# Create WAD extension object
$wadExtension = New-AzCloudServiceDiagnosticsExtension -Name "WADExtension" -ResourceGroupName "ContosOrg" -CloudServiceName "ContosCS" -StorageAccountName "ContosSA" -StorageAccountKey $storageAccountKey[0].Value -DiagnosticsConfigurationPath $configFile -TypeHandlerVersion "1.5" -AutoUpgradeMinorVersion $true 
$extensionProfile = @{extension = @($rdpExtension, $wadExtension)} 

# Get existing Cloud Service
$cloudService = Get-AzCloudService -ResourceGroup "ContosOrg" -CloudServiceName "ContosoCS"

# Add WAD extension to existing Cloud Service extension object
$cloudService.ExtensionProfileExtension = $cloudService.ExtensionProfileExtension + $wadExtension

# Update Cloud Service
$cloudService | Update-AzCloudService
```

## <a name="apply-windows-azure-diagnostics-extension-using-arm-template"></a>Aplicar extensão de diagnóstico do Windows Azure usando o modelo ARM
```json
"extensionProfile": { 
          "extensions": [ 
            { 
              "name": "Microsoft.Insights.VMDiagnosticsSettings_WebRole1", 
              "properties": { 
                "autoUpgradeMinorVersion": true, 
                "publisher": "Microsoft.Azure.Diagnostics", 
                "type": "PaaSDiagnostics", 
                "typeHandlerVersion": "1.5", 
                "settings": "Include PublicConfig XML as a raw string", 
                "protectedSettings": "Include PrivateConfig XML as a raw string”", 
                "rolesAppliedTo": [ 
                  "WebRole1" 
                ] 
              } 
            }
          ]
        },

```

## <a name="next-steps"></a>Próximos passos 
- Reveja os [pré-requisitos](deploy-prerequisite.md) de implantação para serviços em nuvem (suporte alargado).
- Reveja [perguntas frequentes](faq.md) para serviços cloud (suporte alargado).
- Implementar um Serviço de Cloud (suporte alargado) utilizando o [portal Azure](deploy-portal.md), [PowerShell,](deploy-powershell.md) [Modelo](deploy-template.md) ou [Estúdio Visual](deploy-visual-studio.md).