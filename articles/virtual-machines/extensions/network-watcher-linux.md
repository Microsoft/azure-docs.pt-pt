---
title: Extensão de máquina virtual do Agente observador de rede Azure para Linux
description: Implemente o Agente observador de rede na máquina virtual Linux utilizando uma extensão de máquina virtual.
services: virtual-machines-linux
documentationcenter: ''
author: mimckitt
manager: vashan
editor: ''
tags: azure-resource-manager
ms.assetid: 5c81e94c-e127-4dd2-ae83-a236c4512345
ms.service: virtual-machines-linux
ms.subservice: extensions
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/14/2017
ms.author: mimckitt
ms.openlocfilehash: 870606299ee0e76c7a55578f258e837c4b8a9368
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94952272"
---
# <a name="network-watcher-agent-virtual-machine-extension-for-linux"></a>Extensão de máquina virtual do Agente do Observador de Rede para Linux

## <a name="overview"></a>Descrição geral

[O Azure Network Watcher](../../network-watcher/index.yml) é um serviço de monitorização, diagnóstico e análise de desempenho de rede que permite a monitorização das redes Azure. A extensão da máquina virtual do Network Watcher Agent (VM) é um requisito para algumas das funcionalidades do Observador de Rede em VMs Azure, como a captura do tráfego de rede a pedido e outras funcionalidades avançadas.

Este artigo detalha as plataformas suportadas e as opções de implementação da extensão VM do Agente observador de rede para o Linux. A instalação do agente não interrompe, nem requer um reinício, do VM. Pode implantar a extensão em máquinas virtuais que implementar. Se a máquina virtual for implantada por um serviço Azure, verifique a documentação do serviço para determinar se permite ou não instalar extensões na máquina virtual.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="operating-system"></a>Sistema operativo

A extensão do Agente observador de rede pode ser configurada para as seguintes distribuições linux:

| Distribuição | Versão |
|---|---|
| Ubuntu | 12+ |
| Debian | 7 e 8 |
| Red Hat | 6 e 7 |
| Oracle Linux | 6.8+ e 7 |
| SUSE Linux Enterprise Server | 11 e 12 |
| Salto OpenSUSE | 42.3+ |
| CentOS | 6.5+ e 7 |
| CoreOS | 899.17.0+ |


### <a name="internet-connectivity"></a>Conectividade Internet

Algumas das funcionalidades do Agente observador de rede requerem que um VM esteja ligado à Internet. Sem a capacidade de estabelecer ligações de saída, algumas das funcionalidades do Agente observador de rede podem funcionar mal ou ficar indisponíveis. Para obter mais informações sobre a funcionalidade do Observador de Rede que requer o agente, consulte a[documentação do Observador de Rede](../../network-watcher/index.yml).

## <a name="extension-schema"></a>Esquema de extensão

O JSON seguinte mostra o esquema para a extensão do Agente observador de rede. A extensão não requer, nem suporta, quaisquer definições fornecidas pelo utilizador. A extensão baseia-se na sua configuração padrão.

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

### <a name="property-values"></a>Valores patrimoniais

| Nome | Valor / Exemplo |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| publicador | Microsoft.Azure.NetworkWatcher |
| tipo | NetworkWatcherAgentLinux |
| typeHandlerVersion | 1.4 |

## <a name="template-deployment"></a>Implementação de modelos

Pode implementar extensões Azure VM com um modelo de Gestor de Recursos Azure. Para implementar a extensão do Agente observador de rede, utilize o esquema de json anterior no seu modelo.

## <a name="azure-classic-cli-deployment"></a>Implantação clássica do CLI Azure

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

O exemplo a seguir implementa a extensão VM do Agente observador de rede a um VM existente implantado através do modelo de implementação clássico:

```console
azure config mode asm
azure vm extension set myVM1 NetworkWatcherAgentLinux Microsoft.Azure.NetworkWatcher 1.4
```

## <a name="azure-cli-deployment"></a>Implantação do Azure CLI

O exemplo a seguir implementa a extensão VM do Agente observador de rede a um VM existente implantado através do Gestor de Recursos:

```azurecli
az vm extension set --resource-group myResourceGroup1 --vm-name myVM1 --name NetworkWatcherAgentLinux --publisher Microsoft.Azure.NetworkWatcher --version 1.4
```

## <a name="troubleshooting-and-support"></a>Resolução de problemas e apoio

### <a name="troubleshooting"></a>Resolução de problemas

Pode obter dados sobre o estado das extensões utilizando o portal Azure ou o Azure CLI.

O exemplo a seguir mostra o estado de implantação da extensão NetworkWatcherAgentLinux para um VM implantado através do Gestor de Recursos, utilizando o Azure CLI:

```azurecli
az vm extension show --name NetworkWatcherAgentLinux --resource-group myResourceGroup1 --vm-name myVM1
```

### <a name="support"></a>Suporte

Se precisar de mais ajuda em qualquer ponto deste artigo, pode consultar a [documentação do Observador de Rede,](../../network-watcher/index.yml)ou contactar os especialistas do Azure nos [fóruns msdn Azure e Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, pode apresentar um incidente de suporte Azure. Vá ao [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione Obter **suporte**. Para obter informações sobre a utilização do Suporte Azure, consulte o [suporte do Microsoft Azure FAQ](https://azure.microsoft.com/support/faq/).
