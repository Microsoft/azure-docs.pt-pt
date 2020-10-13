---
title: Exportação de Grupos de Recursos Azure que contêm extensões VM
description: Modelos de Gestor de Recursos de Exportação que incluem extensões de máquinas virtuais.
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
ms.openlocfilehash: 529024256b8b3f6da7146787d3a37c59a13b5bd3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86494756"
---
# <a name="exporting-resource-groups-that-contain-vm-extensions"></a>Grupos de recursos exportadores que contêm extensões VM

Os Grupos de Recursos Azure podem ser exportados para um novo modelo de Gestor de Recursos que pode ser redistribuído. O processo de exportação interpreta os recursos existentes e cria um modelo de Gestor de Recursos que, quando implementado, resulta num Grupo de Recursos semelhante. Ao utilizar a opção de exportação do Grupo de Recursos contra um Grupo de Recursos que contenha extensões de Máquina Virtual, vários itens devem ser considerados como compatibilidade de extensão e definições protegidas.

Este documento detalha como funciona o processo de exportação do Grupo de Recursos no que diz respeito a extensões de máquinas virtuais, incluindo uma lista de extensões apoiadas e detalhes sobre o tratamento de dados seguros.

## <a name="supported-virtual-machine-extensions"></a>Extensões de máquinas virtuais suportadas

Muitas extensões da Máquina Virtual estão disponíveis. Nem todas as extensões podem ser exportadas para um modelo de Gestor de Recursos utilizando a função "Script de Automação". Se uma extensão de máquina virtual não for suportada, ela precisa de ser colocada manualmente de volta no modelo exportado.

As seguintes extensões podem ser exportadas com a função de script de automatização.

> Acronis Backup, Acronis Backup Linux, Bg Info, BMC CTM Agent Linux, BMC CTM Agent Windows, Chef Client, Script personalizado, Extensão de Script Personalizado, Script Personalizado para Linux, Agente Linux de Datadog, Agente windows de datadog, extensão de Docker, Extensão DSC, Dynatrace Linux, Dynatrace Windows, HPE Security Application Defender, IaaS Antimalware, IaaS Diagnostic, Linux Chef Client, Linux Diagnostic, OS Patching For Linux, Puppet Agent, Site 24x , Site 24x7 Linux Server, Site 24x7 Windows Server, Trend Micro DSA, Trend Micro DSA Linux, VM Access For Linux, VM Access For Linux, VM Snapshot, VM Snapshot Linux

## <a name="export-the-resource-group"></a>Exportar o Grupo de Recursos

Para exportar um Grupo de Recursos para um modelo reutilizável, complete os seguintes passos:

1. Iniciar sessão no portal do Azure
2. No Menu Hub, clique em Grupos de Recursos
3. Selecione o grupo de recursos-alvo da lista
4. Na lâmina do Grupo de Recursos, clique em Script Automation

![Exportação de modelo](./media/export-templates/template-export.png)

O script de automatização do Gestor de Recursos Azure produz um modelo de Gestor de Recursos, um ficheiro de parâmetros e vários scripts de implementação de amostras como PowerShell e Azure CLI. Neste ponto, o modelo exportado pode ser descarregado usando o botão de descarregamento, adicionado como um novo modelo à biblioteca do modelo, ou reenviado usando o botão de implementação.

## <a name="configure-protected-settings"></a>Configurar configurações protegidas

Muitas extensões de máquinas virtuais Azure incluem uma configuração de configurações protegidas, que encripta dados sensíveis como credenciais e cadeias de configuração. As definições protegidas não são exportadas com o script de automatização. Se necessário, as definições protegidas devem ser reinseridas no modelo exportado.

### <a name="step-1---remove-template-parameter"></a>Passo 1 - Remover o parâmetro do modelo

Quando o Grupo de Recursos é exportado, é criado um único parâmetro de modelo para fornecer um valor às definições protegidas exportadas. Este parâmetro pode ser removido. Para remover o parâmetro, procure através da lista de parâmetros e elimine o parâmetro que se parece com este exemplo JSON.

```json
"extensions_extensionname_protectedSettings": {
    "defaultValue": null,
    "type": "SecureObject"
}
```

### <a name="step-2---get-protected-settings-properties"></a>Passo 2 - Obtenha propriedades de configurações protegidas

Como cada definição protegida tem um conjunto de propriedades necessárias, uma lista destas propriedades precisa de ser recolhida. Cada parâmetro da configuração de definições protegidas pode ser encontrado no esquema do [Gestor de Recursos Azure no GitHub](https://raw.githubusercontent.com/Azure/azure-resource-manager-schemas/master/schemas/2015-08-01/Microsoft.Compute.json). Este esquema inclui apenas os parâmetros para as extensões listadas na secção geral deste documento. 

De dentro do repositório de esquemas, procure a extensão desejada, para este `IaaSDiagnostics` exemplo. Uma vez localizado o `protectedSettings` objeto das extensões, tome nota de cada parâmetro. No exemplo da `IaasDiagnostic` extensão, os parâmetros requeridos são `storageAccountName` , e `storageAccountKey` `storageAccountEndPoint` .

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

### <a name="step-3---re-create-the-protected-configuration"></a>Passo 3 - Recriar a configuração protegida

No modelo exportado, procure `protectedSettings` e substitua o objeto de definição protegido exportado por um novo que inclua os parâmetros de extensão necessários e um valor para cada um.

No exemplo da `IaasDiagnostic` extensão, a nova configuração de definição protegida seria o seguinte exemplo:

```json
"protectedSettings": {
    "storageAccountName": "[parameters('storageAccountName')]",
    "storageAccountKey": "[parameters('storageAccountKey')]",
    "storageAccountEndPoint": "https://core.windows.net"
}
```

O recurso de extensão final é semelhante ao seguinte exemplo JSON:

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

Se utilizar parâmetros de modelo para fornecer valores de propriedade, estes precisam de ser criados. Ao criar parâmetros de modelo para valores de regulação protegidos, certifique-se de que utiliza o `SecureString` tipo de parâmetro para que os valores sensíveis sejam fixados. Para obter mais informações sobre a utilização de parâmetros, consulte [os modelos do Gestor de Recursos Azure.](../../azure-resource-manager/templates/template-syntax.md)

No exemplo da `IaasDiagnostic` extensão, os seguintes parâmetros seriam criados na secção de parâmetros do modelo de Gestor de Recursos.

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

Neste ponto, o modelo pode ser implementado usando qualquer método de implementação do modelo.
