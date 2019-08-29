---
title: Extensão da máquina virtual do agente do observador de rede do Azure para Linux | Microsoft Docs
description: Implante o agente do observador de rede na máquina virtual Linux usando uma extensão de máquina virtual.
services: virtual-machines-linux
documentationcenter: ''
author: gurudennis
manager: amku
editor: ''
tags: azure-resource-manager
ms.assetid: 5c81e94c-e127-4dd2-ae83-a236c4512345
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/14/2017
ms.author: dennisg
ms.openlocfilehash: b59e4c570032bdd3341dc7d519f23f4cd86984c7
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70084435"
---
# <a name="network-watcher-agent-virtual-machine-extension-for-linux"></a>Extensão da máquina virtual do agente do observador de rede para Linux

## <a name="overview"></a>Descrição geral

O observador de [rede do Azure](/azure/network-watcher/) é um serviço de monitoramento, diagnóstico e análise de desempenho de rede que permite o monitoramento de redes do Azure. A extensão da máquina virtual (VM) do agente do observador de rede é um requisito para alguns dos recursos do observador de rede em VMs do Azure, como a captura de tráfego de rede sob demanda e outras funcionalidades avançadas.

Este artigo detalha as plataformas com suporte e as opções de implantação para a extensão de VM do agente do observador de rede para Linux. A instalação do agente não interrompe nem exige uma reinicialização da VM. Você pode implantar a extensão em máquinas virtuais implantadas. Se a máquina virtual for implantada por um serviço do Azure, verifique a documentação do serviço para determinar se ele permite ou não instalar extensões na máquina virtual.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="operating-system"></a>Sistema operativo

A extensão do agente do observador de rede pode ser configurada para as seguintes distribuições do Linux:

| Distribuição | Version |
|---|---|
| Ubuntu | mais de 12 |
| Debian | 7 e 8 |
| Red Hat | 6 e 7 |
| Oracle Linux | 6.8+ e 7 |
| SUSE Linux Enterprise Server | 11 e 12 |
| OpenSUSE Leap | 42.3 + |
| CentOS | 6.5+ e 7 |
| CoreOS | 899.17.0 + |


### <a name="internet-connectivity"></a>Conectividade Internet

Algumas das funcionalidades do agente do observador de rede exigem que uma VM esteja conectada à Internet. Sem a capacidade de estabelecer conexões de saída, alguns dos recursos do agente do observador de rede podem funcionar incorretamente ou ficar indisponíveis. Para obter mais informações sobre a funcionalidade do observador de rede que requer o agente, consulte a documentação do observador de[rede](/azure/network-watcher/).

## <a name="extension-schema"></a>Esquema de extensão

O JSON a seguir mostra o esquema para a extensão do agente do observador de rede. A extensão não exige ou dá suporte a qualquer configuração fornecida pelo usuário. A extensão depende de sua configuração padrão.

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
| publisher | Microsoft.Azure.NetworkWatcher |
| type | NetworkWatcherAgentLinux |
| typeHandlerVersion | 1.4 |

## <a name="template-deployment"></a>Implementação de modelos

Você pode implantar extensões de VM do Azure com um modelo de Azure Resource Manager. Para implantar a extensão do agente do observador de rede, use o esquema JSON anterior em seu modelo.

## <a name="azure-classic-cli-deployment"></a>Implantação da CLI clássica do Azure

O exemplo a seguir implanta a extensão de VM do agente do observador de rede em uma VM existente implantada por meio do modelo de implantação clássico:

```azurecli
azure config mode asm
azure vm extension set myVM1 NetworkWatcherAgentLinux Microsoft.Azure.NetworkWatcher 1.4
```

## <a name="azure-cli-deployment"></a>Implementação de CLI do Azure

O exemplo a seguir implanta a extensão de VM do agente do observador de rede em uma VM existente implantada por meio do Resource Manager:

```azurecli
az vm extension set --resource-group myResourceGroup1 --vm-name myVM1 --name NetworkWatcherAgentLinux --publisher Microsoft.Azure.NetworkWatcher --version 1.4
```

## <a name="troubleshooting-and-support"></a>Resolução de problemas e suporte

### <a name="troubleshooting"></a>Resolução de problemas

Você pode recuperar dados sobre o estado das implantações de extensão usando o portal do Azure ou CLI do Azure.

O exemplo a seguir mostra o estado de implantação de extensões para uma VM implantada por meio do modelo de implantação clássico, usando a CLI clássica do Azure:

```azurecli
azure config mode asm
azure vm extension get myVM1
```
A saída de execução de extensão é registrada em arquivos encontrados no seguinte diretório:

```
/var/log/azure/Microsoft.Azure.NetworkWatcher.NetworkWatcherAgentLinux/
```

O exemplo a seguir mostra o estado de implantação da extensão NetworkWatcherAgentLinux para uma VM implantada por meio do Resource Manager, usando o CLI do Azure:

```azurecli
az vm extension show --name NetworkWatcherAgentLinux --resource-group myResourceGroup1 --vm-name myVM1
```

### <a name="support"></a>Suporte

Se precisar de mais ajuda a qualquer momento neste artigo, você poderá consultar a documentação do [observador de rede](/azure/network-watcher/)ou entrar em contato com os especialistas do Azure nos [fóruns do azure e do Stack Overflow do MSDN](https://azure.microsoft.com/support/forums/). Em alternativa, pode enviar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **obter suporte**. Para obter informações sobre como usar o suporte do Azure, consulte as [perguntas frequentes sobre suporte do Microsoft Azure](https://azure.microsoft.com/support/faq/).
