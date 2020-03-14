---
title: Extensão da máquina virtual de análise de registo para Linux
description: Implemente o agente do Log Analytics na máquina virtual Linux utilizando uma extensão de máquina virtual.
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
ms.openlocfilehash: 9ddac229fc38a91a8b97b24dc2807080b2295758
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79250559"
---
# <a name="log-analytics-virtual-machine-extension-for-linux"></a>Extensão da máquina virtual de análise de registo para Linux

## <a name="overview"></a>Descrição geral

O Azure Monitor Logs fornece capacidades de monitorização, alerta e remediação de alerta em todos os ativos de nuvem e no local. A extensão da máquina virtual Log Analytics para o Linux é publicada e suportada pela Microsoft. A extensão instala o agente Log Analytics em máquinas virtuais do Azure e inscreve máquinas virtuais para uma área de trabalho do Log Analytics existente. Este documento detalha as plataformas suportadas, configurações e opções de implementação para a extensão de máquina virtual do Log Analytics para Linux.

>[!NOTE]
>Como parte da transição em curso do Microsoft Operations Management Suite (OMS) para o Azure Monitor, o agente do OMS para Windows ou Linux irá ser referido como o agente de Log Analytics para o agente do Windows e do Log Analytics para Linux.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Pré-requisitos

### <a name="operating-system"></a>Sistema operativo

Para mais detalhes sobre as distribuições suportadas pelo Linux, consulte o artigo de visão geral do [agente Log Analytics.](../../azure-monitor/platform/log-analytics-agent.md#supported-linux-operating-systems)

### <a name="agent-and-vm-extension-version"></a>Versão de agente e a extensão de VM
A tabela seguinte fornece um mapeamento da versão da extensão de VM do Log Analytics e pacote de agente do Log Analytics para cada versão. Uma ligação para as notas de versão para a versão de pacote de agente do Log Analytics está incluída. Notas de versão incluem detalhes sobre correções de bugs e novos recursos disponíveis para uma versão de determinado agente.  

| Versão de extensão de VM do Linux de análise do registo | Versão do pacote de agente de análise de registo | 
|--------------------------------|--------------------------|
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

Automaticamente, o Centro de segurança do Azure Aprovisiona o agente Log Analytics e liga-o para uma área de trabalho de Log Analytics do padrão criada pelo ASC na sua subscrição do Azure. Se estiver a utilizar o Centro de segurança do Azure, não execute os passos neste documento. Se o fizer, substitui a área de trabalho configurada e divide a ligação com o Centro de segurança do Azure.

### <a name="internet-connectivity"></a>Conectividade Internet

A extensão de agente do Log Analytics para o Linux requer que a máquina virtual de destino está ligada à internet. 

## <a name="extension-schema"></a>Esquema de extensão

O JSON seguinte mostra o esquema para a extensão de agente do Log Analytics. A extensão requer o ID do espaço de trabalho e a chave espaço de trabalho do espaço de trabalho do log analytics alvo; estes valores podem ser [encontrados no seu espaço](../../azure-monitor/learn/quick-collect-linux-computer.md#obtain-workspace-id-and-key) de trabalho Log Analytics no portal Azure. Uma vez que a chave da área de trabalho deve ser tratada como dados confidenciais, devem ser armazenado numa configuração de definição protegido. Dados de definição de protegidos de extensão VM do Azure são encriptados e desencriptados apenas na máquina de virtual de destino. Note que o **espaço de trabalhoId** e **workspaceKey** são sensíveis a casos.

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
>O esquema acima parte do princípio de que será colocado no nível de raiz do modelo. Se colocá-lo dentro do recurso virtual da máquina no modelo, as propriedades `type` e `name` devem ser alteradas, como descrito [mais abaixo](#template-deployment).

### <a name="property-values"></a>Valores de propriedade

| Nome | Valor / exemplo |
| ---- | ---- |
| apiVersion | 2018-06-01 |
| publicador | Microsoft.EnterpriseCloud.Monitoring |
| tipo | OmsAgentForLinux |
| typeHandlerVersion | 1.7 |
| workspaceId (por exemplo) | 6f680a37-00c6-41c7-a93f-1437e3462574 |
| workspaceKey (por exemplo) | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI+rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ== |


## <a name="template-deployment"></a>Implementação de modelos

Extensões VM do Azure podem ser implementadas com modelos Azure Resource Manager. Os modelos são ideais quando se implanta uma ou mais máquinas virtuais que requerem a configuração da implementação de posts, como o embarque em Registos do Monitor Azure. Um modelo de Gestor de Recursos de amostra que inclui a extensão VM do Agente de Análise de Log Analytics pode ser encontrado na [Galeria Quickstart Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-ubuntu-vm). 

A configuração do JSON para uma extensão de máquina virtual pode ser aninhada dentro do recurso de máquina virtual ou colocada na raiz ou de nível superior de um modelo do Resource Manager JSON. A colocação da configuração do JSON afeta o valor do tipo e nome do recurso. Para mais informações, consulte o nome e o [tipo de definição para os recursos infantis.](../../azure-resource-manager/templates/child-resource-name-type.md) 

O exemplo a seguir supõe que a extensão de VM é aninhada dentro do recurso de máquina virtual. Ao nidificar o recurso de extensão, o JSON é colocado no `"resources": []` objeto da máquina virtual.

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

Quando coloca a extensão de JSON na raiz do modelo, o nome do recurso inclui uma referência à máquina virtual principal e o tipo reflete a configuração aninhada.  

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

## <a name="azure-cli-deployment"></a>Implementação de CLI do Azure

A CLI do Azure pode ser utilizada para implementar a extensão de VM de agente do Log Analytics para uma máquina virtual existente. Substitua o valor *myWorkspaceKey* abaixo pela sua chave espaço de trabalho e o valor *myWorkspaceId* pelo seu ID espaço de trabalho. Estes valores podem ser encontrados no seu espaço de trabalho Log Analytics no portal Azure em *Definições Avançadas*. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name OmsAgentForLinux \
  --publisher Microsoft.EnterpriseCloud.Monitoring \
  --version 1.10.1 --protected-settings '{"workspaceKey":"myWorkspaceKey"}' \
  --settings '{"workspaceId":"myWorkspaceId"}'
```

## <a name="troubleshoot-and-support"></a>Resolução de problemas e suporte

### <a name="troubleshoot"></a>Resolução de problemas

Podem ser obtidos dados sobre o estado das implementações de extensão do portal do Azure e com a CLI do Azure. Para ver o estado de implementação de extensões para uma determinada VM, execute o seguinte comando com a CLI do Azure.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Resultado da execução de extensão é registado para o ficheiro seguinte:

```
/opt/microsoft/omsagent/bin/stdout
```

### <a name="error-codes-and-their-meanings"></a>Códigos de erro e seus significados

| Código de Erro | Significado | Ação possível |
| :---: | --- | --- |
| 9 | Ativar chamado prematuramente | [Atualize o Agente Azure Linux](https://docs.microsoft.com/azure/virtual-machines/linux/update-agent) para a versão mais recente disponível. |
| 10 | A VM já está ligada a uma área de trabalho do Log Analytics | Para ligar a VM para a área de trabalho especificada no esquema de extensão, definir stopOnMultipleConnections como false nas definições de públicas ou remova esta propriedade. Esta VM é faturado uma vez para cada área de trabalho está ligada. |
| 11 | Config inválido fornecido para a extensão | Siga os exemplos anteriores para definir todos os valores de propriedade necessários para a implementação. |
| 17 | Falha de instalação do pacote de análise de registo | 
| 19 | Falha de instalação do pacote OMI | 
| 20 | Falha de instalação do pacote SCX |
| 51 | Esta extensão não é suportada no sistema de operação da VM | |
| 55 | Não é possível ligar-se ao serviço Do Monitor Azure ou as encomendas necessárias em falta ou o gestor de pacotes dpkg está bloqueado| Verifique se o sistema tem acesso à Internet ou que foi fornecido um proxy HTTP válido. Além disso, verifique a correção do ID da área de trabalho de e verificar curl e tar utilitários estão instalados. |

Informações adicionais de resolução de problemas podem ser encontradas no Guia de Resolução de [Problemas Log Analytics-Agent-for-Linux](../../azure-monitor/platform/vmext-troubleshoot.md).

### <a name="support"></a>Suporte

Se precisar de mais ajuda em qualquer ponto deste artigo, pode contactar os especialistas do Azure nos [fóruns MSDN Azure e Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, pode enviar um incidente de suporte do Azure. Vá ao site de [suporte azure](https://azure.microsoft.com/support/options/) e selecione Obter suporte. Para obter informações sobre a utilização do Suporte Azure, leia o suporte do [Microsoft Azure FAQ](https://azure.microsoft.com/support/faq/).
