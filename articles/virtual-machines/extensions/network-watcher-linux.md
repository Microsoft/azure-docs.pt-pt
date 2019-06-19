---
title: Extensão de máquina virtual do agente do observador de rede do Azure para Linux | Documentos da Microsoft
description: Implemente o agente do observador de rede na máquina virtual Linux utilizando uma extensão de máquina virtual.
services: virtual-machines-linux
documentationcenter: ''
author: gurudennis
manager: amku
editor: ''
tags: azure-resource-manager
ms.assetid: 5c81e94c-e127-4dd2-ae83-a236c4512345
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/14/2017
ms.author: dennisg
ms.openlocfilehash: 5ed5e791cd6e611218769650115c78afd1869f67
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60743434"
---
# <a name="network-watcher-agent-virtual-machine-extension-for-linux"></a>Extensão de máquina virtual do agente do observador de rede para Linux

## <a name="overview"></a>Descrição geral

[O observador de rede do Azure](/azure/network-watcher/) é um serviço de monitorização, diagnóstico e análise de desempenho de rede que permite a monitorização de redes do Azure. A extensão de máquina virtual (VM) do agente do observador de rede é um requisito para algumas das funcionalidades do observador de rede em VMs do Azure, como a captura de tráfego de rede a pedido e outras funcionalidades avançadas.

Este artigo detalha as opções de implementação para a extensão de VM de agente do observador de rede para Linux e as plataformas suportadas. Instalação do agente não interromper ou exigir uma reinicialização, da VM. Pode implementar a extensão em máquinas virtuais que implementar. Se a máquina virtual é implementada por um serviço do Azure, consulte a documentação para o serviço para determinar se é ou não permite a instalação de extensões na máquina virtual.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="operating-system"></a>Sistema operativo

A extensão de agente do observador de rede pode ser configurada para as distribuições de Linux seguintes:

| Distribuição | Version |
|---|---|
| Ubuntu | 12+ |
| Debian | 7 e 8 |
| Red Hat | 6 e 7 |
| Oracle Linux | 6.8+ e 7 |
| SUSE Linux Enterprise Server | 11 e 12 |
| OpenSUSE Leap | 42.3+ |
| CentOS | 6.5+ e 7 |
| CoreOS | 899.17.0+ |


### <a name="internet-connectivity"></a>Conectividade Internet

Algumas das funcionalidades do agente do observador de rede requer que a VM está ligada à Internet. Sem a capacidade de estabelecer ligações de saída, alguns dos recursos de agente do observador de rede podem funcione incorretamente ou fique indisponível. Para obter mais informações sobre a funcionalidade de observador de rede que requer que o agente, consulte a[documentação do observador de rede](/azure/network-watcher/).

## <a name="extension-schema"></a>Esquema de extensão

O JSON seguinte mostra o esquema para a extensão de agente do observador de rede. A extensão não exigir ou suportar, quaisquer definições fornecido pelo usuário. A extensão baseia-se na configuração predefinida.

```json
{
    "type": "extensions",
    "name": "Microsoft.Azure.NetworkWatcher",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.NetworkWatcher",
        "type": "NetworkWatcherAgentLinux",
        "typeHandlerVersion": "1.4",
        "autoUpgradeMinorVersion": true
    }
}
```

### <a name="property-values"></a>Valores de propriedade

| Nome | Valor / exemplo |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| publicador | Microsoft.Azure.NetworkWatcher |
| type | NetworkWatcherAgentLinux |
| typeHandlerVersion | 1.4 |

## <a name="template-deployment"></a>Implementação de modelos

É possível implementar extensões de VM do Azure com um modelo Azure Resource Manager. Para implementar a extensão de agente do observador de rede, utilize o esquema json anterior no seu modelo.

## <a name="azure-classic-cli-deployment"></a>Implementação de CLI clássica do Azure

O exemplo seguinte implementa a extensão de VM de agente do observador de rede a uma VM existente implementada por meio do modelo de implementação clássica:

```azurecli
azure config mode asm
azure vm extension set myVM1 NetworkWatcherAgentLinux Microsoft.Azure.NetworkWatcher 1.4
```

## <a name="azure-cli-deployment"></a>Implementação de CLI do Azure

O exemplo seguinte implementa a extensão de VM de agente do observador de rede a uma VM existente implementada através do Resource Manager:

```azurecli
az vm extension set --resource-group myResourceGroup1 --vm-name myVM1 --name NetworkWatcherAgentLinux --publisher Microsoft.Azure.NetworkWatcher --version 1.4
```

## <a name="troubleshooting-and-support"></a>Resolução de problemas e suporte

### <a name="troubleshooting"></a>Resolução de problemas

Pode recuperar dados sobre o estado de implementações de extensão com o portal do Azure ou a CLI do Azure.

O exemplo seguinte mostra o estado de implementação de extensões para uma VM implementada através do modelo de implementação clássica, com a CLI clássica do Azure:

```azurecli
azure config mode asm
azure vm extension get myVM1
```
Resultado da execução de extensão é registado para arquivos encontrados no diretório seguinte:

```
/var/log/azure/Microsoft.Azure.NetworkWatcher.NetworkWatcherAgentLinux/
```

O exemplo seguinte mostra o estado de implementação da extensão NetworkWatcherAgentLinux para uma VM implementada através do Resource Manager com a CLI do Azure:

```azurecli
az vm extension show --name NetworkWatcherAgentLinux --resource-group myResourceGroup1 --vm-name myVM1
```

### <a name="support"></a>Suporte

Se precisar de mais ajuda a qualquer momento neste artigo, pode consultar o [documentação do observador de rede](/azure/network-watcher/), ou entre em contato com os especialistas do Azure no [fóruns do Azure do MSDN e Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, pode enviar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **obter suporte**. Para obter informações sobre como utilizar o suporte do Azure, consulte a [FAQ do suporte Microsoft Azure](https://azure.microsoft.com/support/faq/).
