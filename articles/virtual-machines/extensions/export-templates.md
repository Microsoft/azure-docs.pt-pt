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
ms.openlocfilehash: 79991dad96742109817d579b951082d1a30e3951
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79253913"
---
# <a name="exporting-resource-groups-that-contain-vm-extensions"></a>Grupos de recursos exportadores que contêm extensões VM

Os Grupos de Recursos Azure podem ser exportados para um novo modelo de Gestor de Recursos que pode ser redistribuído. O processo de exportação interpreta os recursos existentes, e cria um modelo de Gestor de Recursos que, quando implementado resulta num Grupo de Recursos semelhante. Ao utilizar a opção de exportação do Grupo de Recursos contra um Grupo de Recursos que contém extensões de máquinavirtual, vários itens devem ser considerados como compatibilidade de extensão e configurações protegidas.

Este documento detalha como funciona o processo de exportação do Grupo de Recursos relativamente às extensões de máquinas virtuais, incluindo uma lista de extensões suportadas e detalhes sobre o manuseamento de dados seguros.

## <a name="supported-virtual-machine-extensions"></a>Extensões de máquinavirtual suportadas

Muitas extensões de Máquina Virtual estão disponíveis. Nem todas as extensões podem ser exportadas para um modelo de Gestor de Recursos utilizando a funcionalidade "Automation Script". Se uma extensão virtual da máquina não for suportada, deve ser colocada manualmente de volta no modelo exportado.

As seguintes extensões podem ser exportadas com a funcionalidade de script de automação.

| Extensão ||||
|---|---|---|---|
| Apoio acronis | Agente datadog Windows | Os Patching Para Linux | VM Snapshot Linux
| Acronis Backup Linux | Extensão do Estivador | Agente de Marionetas |
| Informação Bg | Extensão DSC | Site 24x7 Apm Insight |
| BMC CTM Agent Linux | Dynatrace Linux | Site 24x7 Linux Server |
| Janelas do agente BMC CTM | Janelas Dynatrace | Servidor windows do site 24x7 |
| Chef Cliente | HPE Security Application Defender | Tendência Micro DSA |
| Script Personalizado | IaaS Antimalware | Tendência Micro DSA Linux |
| Extensão de Script Personalizado | Diagnósticos IaaS | Acesso VM para Linux |
| Script personalizado para Linux | Cliente do Chef Linux | Acesso VM para Linux |
| Agente Linux Datadog | Diagnóstico linux | Instantâneo de VM |

## <a name="export-the-resource-group"></a>Exportar o Grupo de Recursos

Para exportar um Grupo de Recursos para um modelo reutilizável, complete os seguintes passos:

1. Iniciar sessão no portal do Azure
2. No menu Hub, clique em Grupos de Recursos
3. Selecione o grupo de recursos-alvo da lista
4. Na lâmina do Grupo de Recursos, clique no Script de Automação

![Exportação de Modelos](./media/export-templates/template-export.png)

O script de automações do Gestor de Recursos Azure produz um modelo de Gestor de Recursos, um ficheiro de parâmetros e vários scripts de implementação de amostras, tais como PowerShell e Azure CLI. Neste ponto, o modelo exportado pode ser descarregado usando o botão de descarregamento, adicionado como um novo modelo para a biblioteca do modelo, ou reimplantado usando o botão de implantação.

## <a name="configure-protected-settings"></a>Configurar definições protegidas

Muitas extensões de máquinas virtuais Azure incluem uma configuração de configurações protegida, que encripta dados sensíveis, como credenciais e cadeias de configuração. As definições protegidas não são exportadas com o script de automação. Se necessário, as definições protegidas devem ser reinseridas no modelo exportado.

### <a name="step-1---remove-template-parameter"></a>Passo 1 - Remover parâmetro de modelo

Quando o Grupo de Recursos é exportado, é criado um único parâmetro de modelo para fornecer um valor às configurações protegidas exportadas. Este parâmetro pode ser removido. Para remover o parâmetro, olhe através da lista de parâmetros e elimine o parâmetro que se parece com este exemplo JSON.

```json
"extensions_extensionname_protectedSettings": {
    "defaultValue": null,
    "type": "SecureObject"
}
```

### <a name="step-2---get-protected-settings-properties"></a>Passo 2 - Obtenha propriedades de configurações protegidas

Como cada configuração protegida tem um conjunto de propriedades necessárias, uma lista destas propriedades precisa de ser recolhida. Cada parâmetro da configuração de configurações protegidas pode ser encontrado no esquema do Gestor de [Recursos Azure no GitHub](https://raw.githubusercontent.com/Azure/azure-resource-manager-schemas/master/schemas/2015-08-01/Microsoft.Compute.json). Este esquema inclui apenas os conjuntos de parâmetros para as extensões enumeradas na secção geral deste documento. 

De dentro do repositório de esquemas, procure a extensão desejada, por exemplo `IaaSDiagnostics`. Uma vez localizadas as extensões `protectedSettings` objeto, tome nota de cada parâmetro. No exemplo da extensão `IaasDiagnostic`, os parâmetros necessários são `storageAccountName`, `storageAccountKey`e `storageAccountEndPoint`.

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

No modelo exportado, procure `protectedSettings` e substitua o objeto de fixação protegido exportado por um novo que inclua os parâmetros de extensão necessários e um valor para cada um.

No exemplo da extensão `IaasDiagnostic`, a nova configuração de definição protegida seria o seguinte exemplo:

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

Se utilizar parâmetros de modelo para fornecer valores de propriedade, estes precisam de ser criados. Ao criar parâmetros de modelo para valores de regulação protegidos, certifique-se de que utiliza o tipo de parâmetro `SecureString` para que os valores sensíveis sejam fixados. Para obter mais informações sobre a utilização de parâmetros, consulte [os modelos de Gestor de Recursos Do Azure.](../../resource-group-authoring-templates.md)

No exemplo da extensão `IaasDiagnostic`, os seguintes parâmetros seriam criados na secção de parâmetros do modelo do Gestor de Recursos.

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

Neste ponto, o modelo pode ser implantado usando qualquer método de implementação do modelo.
