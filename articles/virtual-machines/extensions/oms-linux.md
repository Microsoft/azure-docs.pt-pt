---
title: Extensão de máquina virtual Azure Monitor para Linux | Microsoft Docs
description: Implante o agente de Log Analytics na máquina virtual Linux usando uma extensão de máquina virtual.
services: virtual-machines-linux
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: c7bbf210-7d71-4a37-ba47-9c74567a9ea6
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/06/2019
ms.author: akjosh
ms.openlocfilehash: 75f659f9559703cedccef0d8e726b5c8c5bb49be
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72435835"
---
# <a name="azure-monitor-virtual-machine-extension-for-linux"></a>Extensão de máquina virtual Azure Monitor para Linux

## <a name="overview"></a>Visão geral

Os logs de Azure Monitor fornecem recursos de monitoramento, alertas e correção de alertas em ativos locais e na nuvem. A extensão da máquina virtual do agente de Log Analytics para Linux é publicada e tem suporte da Microsoft. A extensão instala o agente de Log Analytics em máquinas virtuais do Azure e registra as máquinas virtuais em um espaço de trabalho Log Analytics existente. Este documento detalha as plataformas, configurações e opções de implantação com suporte para a extensão da máquina virtual Azure Monitor para Linux.

>[!NOTE]
>Como parte da transição contínua do Microsoft Operations Management Suite (OMS) para Azure Monitor, o agente do OMS para Windows ou Linux será chamado de agente de Log Analytics para Windows e Log Analytics Agent para Linux.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Pré-requisitos

### <a name="operating-system"></a>Sistema operativo

Para obter detalhes sobre as distribuições do Linux com suporte, consulte o artigo [visão geral do agente de log Analytics](../../azure-monitor/platform/log-analytics-agent.md#supported-linux-operating-systems) .

### <a name="agent-and-vm-extension-version"></a>Versão do agente e da extensão da VM
A tabela a seguir fornece um mapeamento da versão do Azure Monitor a extensão de VM e o pacote de Log Analytics Agent para cada versão. Um link para as notas de versão da versão do pacote do agente de Log Analytics está incluído. As notas de versão incluem detalhes sobre correções de bugs e novos recursos disponíveis para uma determinada versão do agente.  

| Azure Monitor versão da extensão de VM do Linux | Versão do pacote do agente de Log Analytics | 
|--------------------------------|--------------------------|
| 1.11.15 | [1.11.0-9](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.11.0-9) |
| 1.10.0 | [1.10.0-1](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.10.0-1) |
| 1.9.1 | [1.9.0-0](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.9.0-0) |
| 1.8.11 | [1.8.1-256](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.8.1.256)| 
| 1.8.0 | [1.8.0-256](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/1.8.0-256)| 
| 1.7.9 | [1.6.1-3](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.6.1.3)| 
| 1.6.42.0 | [1.6.0-42](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.6.0-42)| 
| 1.4.60.2 | [1.4.4-210](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.4-210)| 
| 1.4.59.1 | [1.4.3-174](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.3-174)|
| 1.4.58.7 | [14.2-125](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.2-125)|
| 1.4.56.5 | [1.4.2-124](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.2-124)|
| 1.4.55.4 | [1.4.1-123](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.1-123)|
| 1.4.45.3 | [1.4.1-45](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.1-45)|
| 1.4.45.2 | [1.4.0-45](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.0-45)|
| 1.3.127.5 | [1.3.5-127](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent-201705-v1.3.5-127)| 
| 1.3.127.7 | [1.3.5-127](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent-201705-v1.3.5-127)|
| 1.3.18.7 | [1.3.4-15](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent-201704-v1.3.4-15)|  

### <a name="azure-security-center"></a>Centro de Segurança do Azure

A central de segurança do Azure provisiona automaticamente o agente de Log Analytics e o conecta a um espaço de trabalho de Log Analytics padrão criado pelo ASC em sua assinatura do Azure. Se você estiver usando a central de segurança do Azure, não execute as etapas neste documento. Isso substitui o espaço de trabalho configurado e interrompe a conexão com a central de segurança do Azure.

### <a name="internet-connectivity"></a>Conectividade Internet

A extensão do agente de Log Analytics para Linux requer que a máquina virtual de destino esteja conectada à Internet. 

## <a name="extension-schema"></a>Esquema de extensão

O JSON a seguir mostra o esquema para a extensão do agente de Log Analytics. A extensão requer a ID do espaço de trabalho e a chave do espaço de trabalho do destino Log Analytics espaço de trabalho; esses valores podem ser [encontrados no espaço de trabalho log Analytics](../../azure-monitor/learn/quick-collect-linux-computer.md#obtain-workspace-id-and-key) no portal do Azure. Como a chave do espaço de trabalho deve ser tratada como dados confidenciais, ela deve ser armazenada em uma configuração protegida. Os dados de configuração protegidos da extensão de VM do Azure são criptografados e descriptografados somente na máquina virtual de destino. Observe que **workspaceid** e **workspaceKey** diferenciam maiúsculas de minúsculas.

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "OMSExtension",
  "apiVersion": "2018-06-01",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.7",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

>[!NOTE]
>O esquema acima pressupõe que ele será colocado no nível raiz do modelo. Se você colocá-lo dentro do recurso de máquina virtual no modelo, as propriedades `type` e `name` deverão ser alteradas, conforme descrito [mais abaixo](#template-deployment).
>

### <a name="property-values"></a>Valores de propriedade

| Nome | Valor/exemplo |
| ---- | ---- |
| apiVersion | 2018-06-01 |
| Programa | Microsoft. EnterpriseCloud. Monitoring |
| tipo | OmsAgentForLinux |
| typeHandlerVersion | 1,7 |
| workspaceid (por exemplo,) | 6f680a37-00c6-41c7-a93f-1437e3462574 |
| workspaceKey (por exemplo,) | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI + rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ = = |


## <a name="template-deployment"></a>Implementação de modelos

As extensões de VM do Azure podem ser implantadas com modelos de Azure Resource Manager. Os modelos são ideais ao implantar uma ou mais máquinas virtuais que exigem a configuração pós-implantação, como a integração a logs de Azure Monitor. Um modelo do Resource Manager de exemplo que inclui a extensão de VM do agente de Log Analytics pode ser encontrado na [Galeria de início rápido do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-ubuntu-vm). 

A configuração JSON para uma extensão de máquina virtual pode ser aninhada dentro do recurso de máquina virtual ou colocada no nível raiz ou superior de um modelo JSON do Gerenciador de recursos. O posicionamento da configuração JSON afeta o valor do nome do recurso e do tipo. Para obter mais informações, consulte [definir nome e tipo para recursos filho](../../azure-resource-manager/child-resource-name-type.md). 

O exemplo a seguir pressupõe que a extensão de VM esteja aninhada dentro do recurso de máquina virtual. Ao aninhar o recurso de extensão, o JSON é colocado no objeto `"resources": []` da máquina virtual.

```json
{
  "type": "extensions",
  "name": "OMSExtension",
  "apiVersion": "2018-06-01",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.7",
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

Ao colocar a extensão JSON na raiz do modelo, o nome do recurso inclui uma referência à máquina virtual pai e o tipo reflete a configuração aninhada.  

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "<parentVmResource>/OMSExtension",
  "apiVersion": "2018-06-01",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.7",
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

## <a name="azure-cli-deployment"></a>Implantação de CLI do Azure

O CLI do Azure pode ser usado para implantar a extensão de VM do agente Log Analytics em uma máquina virtual existente. Substitua o valor *myworkspacekey pela* abaixo pela sua chave do espaço de trabalho e pelo valor *myworkspaceid* pela sua ID do espaço de trabalho. Esses valores podem ser encontrados em seu espaço de trabalho do Log Analytics no portal do Azure em *Configurações avançadas*. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name OmsAgentForLinux \
  --publisher Microsoft.EnterpriseCloud.Monitoring \
  --version 1.10.1 --protected-settings '{"workspaceKey":"myWorkspaceKey"}' \
  --settings '{"workspaceId":"myWorkspaceId"}'
```

## <a name="troubleshoot-and-support"></a>Solução de problemas e suporte

### <a name="troubleshoot"></a>Resolução de problemas

Os dados sobre o estado das implantações de extensão podem ser recuperados do portal do Azure e usando o CLI do Azure. Para ver o estado de implantação das extensões de uma determinada VM, execute o comando a seguir usando o CLI do Azure.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

A saída de execução de extensão é registrada no seguinte arquivo:

```
/opt/microsoft/omsagent/bin/stdout
```

### <a name="error-codes-and-their-meanings"></a>Códigos de erro e seus significados

| Código de erro | Significado | Ação possível |
| :---: | --- | --- |
| 9 | Habilitar chamado prematuramente | [Atualize o agente Linux do Azure](https://docs.microsoft.com/azure/virtual-machines/linux/update-agent) para a versão mais recente disponível. |
| 10 | A VM já está conectada a um espaço de trabalho Log Analytics | Para conectar a VM ao espaço de trabalho especificado no esquema de extensão, defina stopOnMultipleConnections como false nas configurações públicas ou remova essa propriedade. Essa VM é cobrada uma vez para cada espaço de trabalho ao qual está conectada. |
| 11 | Configuração inválida fornecida para a extensão | Siga os exemplos anteriores para definir todos os valores de propriedade necessários para a implantação. |
| 17 | Falha na instalação do pacote Log Analytics | 
| 19 | Falha na instalação do pacote OMI | 
| 20 | Falha na instalação do pacote SCX |
| 51 | Não há suporte para essa extensão no sistema operacional da VM | |
| 55 | Não é possível conectar ao serviço de Azure Monitor ou aos pacotes necessários ausentes ou o Gerenciador de pacotes dpkg está bloqueado| Verifique se o sistema tem acesso à Internet ou se um proxy HTTP válido foi fornecido. Além disso, verifique a exatidão da ID do espaço de trabalho e se os utilitários de rotação e tar estão instalados. |

Informações adicionais de solução de problemas podem ser encontradas no [Guia de solução de problemas do log Analytics-Agent-for-Linux](../../azure-monitor/platform/vmext-troubleshoot.md).

### <a name="support"></a>Suporte

Se precisar de mais ajuda a qualquer momento neste artigo, você poderá entrar em contato com os especialistas do Azure nos [fóruns do Azure e do Stack Overflow do MSDN](https://azure.microsoft.com/support/forums/). Como alternativa, você pode arquivar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione obter suporte. Para obter informações sobre como usar o suporte do Azure, leia as [perguntas frequentes sobre suporte do Microsoft Azure](https://azure.microsoft.com/support/faq/).
