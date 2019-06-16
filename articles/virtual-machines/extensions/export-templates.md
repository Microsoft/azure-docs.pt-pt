---
title: Exportar grupos de recursos do Azure que contêm as extensões de VM | Documentos da Microsoft
description: Exporte modelos do Resource Manager, que incluem as extensões de máquina virtual.
services: virtual-machines-windows
documentationcenter: ''
author: roiyz-msft
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 7f4e2ca6-f1c7-4f59-a2cc-8f63132de279
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/05/2016
ms.author: roiyz
ms.openlocfilehash: f56cfeeede393dbdb9632ea4120d3a81e89f3f7c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61484054"
---
# <a name="exporting-resource-groups-that-contain-vm-extensions"></a>Exportar grupos de recursos que contêm as extensões de VM

Grupos de recursos do Azure podem ser exportados para um novo modelo do Resource Manager que, em seguida, pode ser reimplantado. O processo de exportação interpreta os recursos existentes e cria um modelo do Resource Manager que quando implementado resulta num grupo de recursos semelhantes. Quando utilizar a opção de exportação do grupo de recursos em relação a um grupo de recursos que contém as extensões de Máquina Virtual, vários itens precisam de ser considerados como compatibilidade de extensão e definições protegido.

Este detalhes do documento como funciona o o processo de exportação do grupo de recursos sobre extensões de máquina virtual, incluindo uma lista de extensões de suporte e protegidas de detalhes sobre a manipulação de dados.

## <a name="supported-virtual-machine-extensions"></a>Extensões de Máquina Virtual suportadas

Várias extensões de Máquina Virtual estão disponíveis. Nem todas as extensões podem ser exportadas para um modelo do Resource Manager através da funcionalidade de "Script de automatização". Se uma extensão de máquina virtual não é suportada, ele precisa ser colocado manualmente novamente para o modelo exportado.

As seguintes extensões podem ser exportadas com a funcionalidade de script de automatização.

| Extensão ||||
|---|---|---|---|
| Cópia de segurança Acronis | Agente do Windows o Datadog | Aplicação de patches para Linux do SO | Linux de instantâneo VM
| Cópia de segurança Acronis Linux | Extensão do docker | Agente de puppet |
| Informações de BG | Extensão DSC | Informações do site 24x7 Apm |
| BMC CTM agente Linux | Dynatrace Linux | 24x7 Linux servidor do site |
| Windows de agente CTM de BMC | Dynatrace Windows | 24x7 Windows servidor do site |
| Cliente do chef | Defender de aplicação de segurança HPE | DSA Micro de tendência |
| Script Personalizado | IaaS Antimalware | Trend Micro DSA Linux |
| Extensão de Script Personalizado | Diagnóstico de IaaS | Acesso VM para Linux |
| Script personalizado para Linux | Cliente de Chef do Linux | Acesso VM para Linux |
| Agente do Linux o Datadog | Diagnóstico do Linux | Instantâneo de VM |

## <a name="export-the-resource-group"></a>Exportar o grupo de recursos

Para exportar um grupo de recursos num modelo reutilizável, conclua os seguintes passos:

1. Iniciar sessão no portal do Azure
2. No Hub Menu, clique em grupos de recursos
3. Selecione o grupo de recursos de destino na lista
4. No painel do grupo de recursos, clique em Script de automação

![Exportação do modelo](./media/export-templates/template-export.png)

O script do Azure Resource Manager automatizações produz um modelo do Resource Manager, um ficheiro de parâmetros e vários scripts de implementação de exemplo, como o PowerShell e CLI do Azure. Neste momento, o modelo exportado pode ser baixado com o botão de transferência, adicionado como um novo modelo para a biblioteca de modelos ou implantados novamente com o botão implementar.

## <a name="configure-protected-settings"></a>Configurar definições protegidas

Várias extensões de máquina virtual do Azure incluem uma configuração de definições protegidas, que criptografa dados confidenciais, como credenciais e cadeias de caracteres de configuração. Definições protegidas não são exportadas com o script de automação. Se precisam de definições necessárias, protegidas seja reinserida no exportado baseadas num modelo.

### <a name="step-1---remove-template-parameter"></a>Passo 1 – remover o parâmetro de modelo

Quando o grupo de recursos é exportado, é criado um parâmetro de modelo único de fornecer um valor às definições protegidas exportadas. Este parâmetro pode ser removido. Para remover o parâmetro, examine a lista de parâmetros e eliminar o parâmetro que é semelhante a este exemplo JSON.

```json
"extensions_extensionname_protectedSettings": {
    "defaultValue": null,
    "type": "SecureObject"
}
```

### <a name="step-2---get-protected-settings-properties"></a>Passo 2 – Get protegidos propriedades das definições

Uma vez que cada definição protegido tem um conjunto de propriedades necessárias, uma lista destas propriedades precisam de ser reunidas. Cada um dos parâmetros da configuração de definições protegidas pode ser encontrado no [esquema de Gestor de recursos do Azure no GitHub](https://raw.githubusercontent.com/Azure/azure-resource-manager-schemas/master/schemas/2015-08-01/Microsoft.Compute.json). Esse esquema inclui apenas os conjuntos de parâmetros para as extensões listadas na seção de visão geral deste documento. 

De dentro do repositório de esquema, procure a extensão desejada, para este exemplo `IaaSDiagnostics`. Uma vez as extensões `protectedSettings` objeto ser localizado, tome nota de cada parâmetro. No exemplo do `IaasDiagnostic` extensão, a exigir parâmetros estão `storageAccountName`, `storageAccountKey`, e `storageAccountEndPoint`.

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

### <a name="step-3---re-create-the-protected-configuration"></a>Passo 3 – voltar a criar a configuração protegida

No modelo exportado, procure `protectedSettings` e substituir o objeto de definição protegido exportado com um novo que inclui os parâmetros de extensão necessária e um valor para cada um deles.

No exemplo do `IaasDiagnostic` extensão, a nova configuração de definição protegido teria uma aparência semelhante ao seguinte exemplo:

```json
"protectedSettings": {
    "storageAccountName": "[parameters('storageAccountName')]",
    "storageAccountKey": "[parameters('storageAccountKey')]",
    "storageAccountEndPoint": "https://core.windows.net"
}
```

O recurso de extensão final será semelhante ao seguinte exemplo de JSON:

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

Se utilizar parâmetros do modelo para fornecer valores de propriedade, estes têm de ser criado. Ao criar parâmetros de modelo para definir valores de protegido, certifique-se de usar o `SecureString` o tipo de parâmetro para que os valores confidenciais estão protegidos. Para obter mais informações sobre como utilizar parâmetros, consulte [modelos Authoring Azure Resource Manager](../../resource-group-authoring-templates.md).

No exemplo do `IaasDiagnostic` extensão, seriam possível criar os seguintes parâmetros na secção de parâmetros do modelo do Resource Manager.

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

Neste momento, o modelo pode ser implementado com qualquer método de implementação do modelo.
