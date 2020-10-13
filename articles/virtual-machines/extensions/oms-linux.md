---
title: Log Analytics virtual machine extension for Linux (Extensão de máquina virtual do Log Analytics para Linux)
description: Implemente o agente Log Analytics na máquina virtual Linux utilizando uma extensão de máquina virtual.
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
ms.date: 02/18/2020
ms.author: akjosh
ms.openlocfilehash: 38bbe52e45c348977cdda02a5399f6c89fb91bcc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91307454"
---
# <a name="log-analytics-virtual-machine-extension-for-linux"></a>Log Analytics virtual machine extension for Linux (Extensão de máquina virtual do Log Analytics para Linux)

## <a name="overview"></a>Descrição geral

O Azure Monitor Logs fornece capacidades de monitorização, alerta e alerta de remediação através de ativos de nuvem e no local. A extensão da máquina virtual Log Analytics para Linux é publicada e suportada pela Microsoft. A extensão instala o agente Log Analytics em máquinas virtuais Azure e inscreve máquinas virtuais num espaço de trabalho log analytics existente. Este documento detalha as plataformas, configurações e opções de implementação suportadas para a extensão da máquina virtual Log Analytics para o Linux.

>[!NOTE]
>Como parte da transição em curso do Microsoft Operations Management Suite (OMS) para o Azure Monitor, o Agente OMS para Windows ou Linux será referido como o agente log analytics para windows e log analytics para linux.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Pré-requisitos

### <a name="operating-system"></a>Sistema operativo

Para obter detalhes sobre as distribuições apoiadas do Linux, consulte o artigo [dos agentes do Azure Monitor.](../../azure-monitor/platform/agents-overview.md#supported-operating-systems)

### <a name="agent-and-vm-extension-version"></a>Versão de extensão de agente e VM
A tabela seguinte fornece um mapeamento da versão da extensão VM do Log Analytics e do pacote de agente Log Analytics para cada lançamento. Está incluído um link para as notas de lançamento para a versão do pacote do agente Log Analytics. As notas de lançamento incluem detalhes sobre correções de bugs e novas funcionalidades disponíveis para uma determinada versão do agente.  

| Versão de extensão do Log Analytics Linux VM | Versão do pacote do Agente De analítico log | 
|--------------------------------|--------------------------|
| 1.13.15 | [1.13.9-0](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.13.9-0) |
| 1.12.25 | [1.12.15-0](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.12.15-0) |
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

O Azure Security Center fornece automaticamente o agente Log Analytics e liga-o a um espaço de trabalho de Log Analytics padrão criado pela ASC na sua subscrição Azure. Se estiver a utilizar o Centro de Segurança Azure, não passe pelos passos deste documento. Ao fazê-lo, substitui o espaço de trabalho configurado e rompe a ligação com o Centro de Segurança Azure.

### <a name="internet-connectivity"></a>Conectividade Internet

A extensão do Agente Desanal para o Linux requer que a máquina virtual alvo esteja ligada à internet. 

## <a name="extension-schema"></a>Esquema de extensão

O JSON seguinte mostra o esquema para a extensão do Agente Desanal. A extensão requer o ID do espaço de trabalho e a chave do espaço de trabalho a partir do espaço de trabalho target Log Analytics; estes valores podem ser [encontrados no seu espaço de trabalho Log Analytics](../../azure-monitor/learn/quick-collect-linux-computer.md#obtain-workspace-id-and-key) no portal Azure. Uma vez que a chave do espaço de trabalho deve ser tratada como dados sensíveis, deve ser armazenada numa configuração de definição protegida. Os dados de definição protegidos por extensão Azure VM são encriptados e apenas desencriptados na máquina virtual alvo. Note que **o workspaceId** e **o workspaceKey** são sensíveis ao caso.

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
    "typeHandlerVersion": "1.13",
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
>O esquema acima pressupõe que será colocado ao nível da raiz do modelo. Se o colocar dentro do recurso de máquina virtual no modelo, as `type` propriedades e propriedades devem ser `name` alteradas, conforme descrito [mais abaixo](#template-deployment).

### <a name="property-values"></a>Valores patrimoniais

| Nome | Valor / Exemplo |
| ---- | ---- |
| apiVersion | 2018-06-01 |
| publicador | Microsoft.EnterpriseCloud.Monitoring |
| tipo | OmsAgentForLinux |
| typeHandlerVersion | 1.7 |
| workspaceId (por exemplo) | 6f680a37-00c6-41c7-a93f-1437e3462574 |
| workspaceKey (por exemplo) | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI+rSRgE8qVvRsGo9TXffbrTahyrwv35W0pOqQAUUQ== |


## <a name="template-deployment"></a>Implementação de modelos

As extensões Azure VM podem ser implementadas com modelos Azure Resource Manager. Os modelos são ideais quando se implanta uma ou mais máquinas virtuais que requerem configuração de implementação de posts, como a bordo de Registos monitores Azure. Um modelo de gestor de recursos de amostra que inclui a extensão VM do Agente de Analítica de Log pode ser encontrado na [Galeria Azure Quickstart](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-ubuntu-vm). 

A configuração JSON para uma extensão de máquina virtual pode ser aninhada dentro do recurso de máquina virtual, ou colocada no nível raiz ou superior de um modelo JSON do Gestor de Recursos. A colocação da configuração JSON afeta o valor do nome e do tipo do recurso. Para obter mais informações, consulte [o nome definido e o tipo para obter recursos para crianças.](../../azure-resource-manager/templates/child-resource-name-type.md) 

O exemplo a seguir pressupõe que a extensão VM está aninhada dentro do recurso da máquina virtual. Ao nidificar o recurso de extensão, o JSON é colocado no `"resources": []` objeto da máquina virtual.

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

Ao colocar a extensão JSON na raiz do modelo, o nome do recurso inclui uma referência à máquina virtual do progenitor, e o tipo reflete a configuração aninhada.  

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

## <a name="azure-cli-deployment"></a>Implantação do Azure CLI

O CLI Azure pode ser usado para implantar a extensão VM do Agente De Analítica de Log para uma máquina virtual existente. Substitua o valor *myWorkspaceKey* abaixo pela tecla do espaço de trabalho e pelo valor *myWorkspaceId* pelo seu ID do espaço de trabalho. Estes valores podem ser encontrados no seu espaço de trabalho Log Analytics no portal Azure em *Definições Avançadas*. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name OmsAgentForLinux \
  --publisher Microsoft.EnterpriseCloud.Monitoring \
  --version 1.10.1 --protected-settings '{"workspaceKey":"myWorkspaceKey"}' \
  --settings '{"workspaceId":"myWorkspaceId"}'
```

## <a name="troubleshoot-and-support"></a>Resolução de problemas e apoio

### <a name="troubleshoot"></a>Resolução de problemas

Os dados sobre o estado das extensões podem ser recuperados a partir do portal Azure e utilizando o CLI Azure. Para ver o estado de implantação das extensões para um determinado VM, executar o seguinte comando utilizando o Azure CLI.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

A saída de execução de extensão é registada no seguinte ficheiro:

```
/opt/microsoft/omsagent/bin/stdout
```

### <a name="error-codes-and-their-meanings"></a>Códigos de erro e os seus significados

| Código de Erro | Significado | Possível Ação |
| :---: | --- | --- |
| 9 | Ativar chamado prematuramente | [Atualize o Agente Azure Linux](./update-linux-agent.md) para a versão mais recente disponível. |
| 10 | VM já está conectado a um espaço de trabalho Log Analytics | Para ligar o VM ao espaço de trabalho especificado no esquema de extensão, defina stopOnMultipleConnections para falso em configurações públicas ou remova esta propriedade. Este VM é cobrado uma vez por cada espaço de trabalho a que está ligado. |
| 11 | Config inválido fornecido à extensão | Siga os exemplos anteriores para definir todos os valores de propriedade necessários para a implantação. |
| 17 | Falha na instalação do pacote Log Analytics | 
| 19 | Falha na instalação do pacote OMI | 
| 20 | Falha na instalação do pacote SCX |
| 51 | Esta extensão não é suportada no sistema de funcionamento do VM | |
| 52 | Esta extensão falhou devido a uma dependência em falta | Verifique a saída e os registos para obter mais informações sobre a dependência que falta. |
| 53 | Esta extensão falhou devido a parâmetros de configuração em falta ou errados | Verifique a saída e os registos para obter mais informações sobre o que correu mal. Além disso, verifique a correção do ID do espaço de trabalho e verifique se a máquina está ligada à internet. |
| 55 | Não é possível ligar-se ao serviço Azure Monitor ou pacotes necessários em falta ou o gestor de pacotes DPKG está bloqueado| Verifique se o sistema tem acesso à Internet ou se foi fornecido um proxy HTTP válido. Além disso, verifique a correção do ID do espaço de trabalho e verifique se os utilitários de caracóis e alcatrão estão instalados. |

Informações adicionais sobre a resolução de problemas podem ser encontradas no [Guia de Resolução de Problemas log Analytics-Agent-for-Linux](../../azure-monitor/platform/vmext-troubleshoot.md).

### <a name="support"></a>Suporte

Se precisar de mais ajuda em qualquer ponto deste artigo, pode contactar os especialistas da Azure nos [fóruns msdn Azure e Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, pode apresentar um incidente de suporte Azure. Vá ao [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione Obter suporte. Para obter informações sobre a utilização do Suporte Azure, leia o [suporte do Microsoft Azure FAQ](https://azure.microsoft.com/support/faq/).
