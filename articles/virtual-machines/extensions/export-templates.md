---
title: Exportando grupos de recursos do Azure que contêm extensões de VM
description: Exportar modelos do Resource Manager que incluem extensões de máquina virtual.
services: virtual-machines-windows
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 7f4e2ca6-f1c7-4f59-a2cc-8f63132de279
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/05/2016
ms.author: akjosh
ms.openlocfilehash: 79991dad96742109817d579b951082d1a30e3951
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74073135"
---
# <a name="exporting-resource-groups-that-contain-vm-extensions"></a>Exportando grupos de recursos que contêm extensões de VM

Os grupos de recursos do Azure podem ser exportados para um novo modelo do Resource Manager que pode ser reimplantado. O processo de exportação interpreta os recursos existentes e cria um modelo do Resource Manager que, quando implantado, resulta em um grupo de recursos semelhante. Ao usar a opção de exportação do grupo de recursos em um grupo de recursos que contém extensões de máquina virtual, vários itens precisam ser considerados como compatibilidade de extensão e configurações protegidas.

Este documento detalha como o processo de exportação do grupo de recursos funciona em relação às extensões de máquina virtual, incluindo uma lista de extensões com suporte e detalhes sobre como lidar com dados protegidos.

## <a name="supported-virtual-machine-extensions"></a>Extensões de máquina virtual com suporte

Muitas extensões de máquina virtual estão disponíveis. Nem todas as extensões podem ser exportadas para um modelo do Resource Manager usando o recurso "script de automação". Se não houver suporte para uma extensão de máquina virtual, ela precisará ser colocada de volta no modelo exportado manualmente.

As extensões a seguir podem ser exportadas com o recurso de script de automação.

| Extensão ||||
|---|---|---|---|
| Backup do Acronis | Agente do Windows Datadog | Aplicação de patch de so para Linux | Instantâneo de VM Linux
| Acronis Backup Linux | Extensão do Docker | Agente Puppet |
| Informações de BG | Extensão DSC | Insight de APM do site 24x7 |
| BMC CTM Agent Linux | Dynatrace Linux | Site do servidor Linux 24x7 |
| Janelas do agente CTM do BMC | Janelas dynaTrace | Site 24x7 do Windows Server |
| Cliente chefe | Aplicativo HPE Security defender | DSA da Trend Micro |
| Script Personalizado | AntiMalware de IaaS | Trend Micro DSA Linux |
| Extensão de Script Personalizado | Diagnóstico de IaaS | Acesso à VM para Linux |
| Script personalizado para Linux | Cliente do Linux chefe | Acesso à VM para Linux |
| Agente do Datadog Linux | Diagnóstico do Linux | Instantâneo de VM |

## <a name="export-the-resource-group"></a>Exportar o grupo de recursos

Para exportar um grupo de recursos para um modelo reutilizável, conclua as seguintes etapas:

1. Iniciar sessão no portal do Azure
2. No menu Hub, clique em grupos de recursos
3. Selecione o grupo de recursos de destino na lista
4. Na folha do grupo de recursos, clique em script de automação

![Exportação de modelo](./media/export-templates/template-export.png)

O script Azure Resource Manager automations produz um modelo do Resource Manager, um arquivo de parâmetros e vários exemplos de scripts de implantação, como o PowerShell e o CLI do Azure. Neste ponto, o modelo exportado pode ser baixado usando o botão baixar, adicionado como um novo modelo à biblioteca de modelos ou reimplantado usando o botão implantar.

## <a name="configure-protected-settings"></a>Definir configurações protegidas

Muitas extensões de máquina virtual do Azure incluem uma configuração de configurações protegidas, que criptografa dados confidenciais, como credenciais e cadeias de caracteres de configuração. As configurações protegidas não são exportadas com o script de automação. Se necessário, as configurações protegidas precisam ser reinseridas no modelo exportado.

### <a name="step-1---remove-template-parameter"></a>Etapa 1 – remover parâmetro de modelo

Quando o grupo de recursos é exportado, um único parâmetro de modelo é criado para fornecer um valor às configurações protegidas exportadas. Esse parâmetro pode ser removido. Para remover o parâmetro, examine a lista de parâmetros e exclua o parâmetro parecido com este exemplo de JSON.

```json
"extensions_extensionname_protectedSettings": {
    "defaultValue": null,
    "type": "SecureObject"
}
```

### <a name="step-2---get-protected-settings-properties"></a>Etapa 2 – obter propriedades de configurações protegidas

Como cada configuração protegida tem um conjunto de propriedades necessárias, uma lista dessas propriedades precisa ser coletada. Cada parâmetro da configuração de configurações protegidas pode ser encontrado no [esquema de Azure Resource Manager no GitHub](https://raw.githubusercontent.com/Azure/azure-resource-manager-schemas/master/schemas/2015-08-01/Microsoft.Compute.json). Esse esquema inclui apenas os conjuntos de parâmetros para as extensões listadas na seção visão geral deste documento. 

No repositório do esquema, procure a extensão desejada para este exemplo `IaaSDiagnostics`. Depois que as extensões `protectedSettings` objeto tiver sido localizado, anote cada parâmetro. No exemplo da extensão de `IaasDiagnostic`, os parâmetros require são `storageAccountName`, `storageAccountKey`e `storageAccountEndPoint`.

```json
"protectedSettings": {
    "type": "object",
    "properties": {
        "storageAccountName": {
            "type": "string"
        },
        "storageAccountKey": {
            "type": "string"
        },
        "storageAccountEndPoint": {
            "type": "string"
        }
    },
    "required": [
        "storageAccountName",
        "storageAccountKey",
        "storageAccountEndPoint"
    ]
}
```

### <a name="step-3---re-create-the-protected-configuration"></a>Etapa 3: criar novamente a configuração protegida

No modelo exportado, pesquise `protectedSettings` e substitua o objeto de configuração protegida exportado por um novo que inclua os parâmetros de extensão necessários e um valor para cada um.

No exemplo da extensão de `IaasDiagnostic`, a nova configuração de configuração protegida seria semelhante ao exemplo a seguir:

```json
"protectedSettings": {
    "storageAccountName": "[parameters('storageAccountName')]",
    "storageAccountKey": "[parameters('storageAccountKey')]",
    "storageAccountEndPoint": "https://core.windows.net"
}
```

O recurso de extensão final é semelhante ao seguinte exemplo de JSON:

```json
{
    "name": "Microsoft.Insights.VMDiagnosticsSettings",
    "type": "extensions",
    "location": "[resourceGroup().location]",
    "apiVersion": "[variables('apiVersion')]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "tags": {
        "displayName": "AzureDiagnostics"
    },
    "properties": {
        "publisher": "Microsoft.Azure.Diagnostics",
        "type": "IaaSDiagnostics",
        "typeHandlerVersion": "1.5",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), variables('vmName'), variables('wadcfgxend')))]",
            "storageAccount": "[parameters('existingdiagnosticsStorageAccountName')]"
        },
        "protectedSettings": {
            "storageAccountName": "[parameters('storageAccountName')]",
            "storageAccountKey": "[parameters('storageAccountKey')]",
            "storageAccountEndPoint": "https://core.windows.net"
        }
    }
}
```

Se você estiver usando parâmetros de modelo para fornecer valores de propriedade, eles precisam ser criados. Ao criar parâmetros de modelo para valores de configuração protegidos, certifique-se de usar o tipo de parâmetro `SecureString` para que os valores confidenciais sejam protegidos. Para obter mais informações sobre como usar parâmetros, consulte [criando modelos de Azure Resource Manager](../../resource-group-authoring-templates.md).

No exemplo da extensão de `IaasDiagnostic`, os seguintes parâmetros seriam criados na seção de parâmetros do modelo do Resource Manager.

```json
"storageAccountName": {
    "defaultValue": null,
    "type": "SecureString"
},
"storageAccountKey": {
    "defaultValue": null,
    "type": "SecureString"
}
```

Neste ponto, o modelo pode ser implantado usando qualquer método de implantação de modelo.
