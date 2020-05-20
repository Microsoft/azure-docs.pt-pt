---
title: Log Analytics virtual machine extension for Linux (Extensão de máquina virtual do Log Analytics para Linux)
description: Desloque o agente Log Analytics na máquina virtual Linux utilizando uma extensão virtual da máquina.
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
ms.openlocfilehash: 34dbde25106dbb82fb9548ad53f368230f2c728c
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83654407"
---
# <a name="log-analytics-virtual-machine-extension-for-linux"></a>Log Analytics virtual machine extension for Linux (Extensão de máquina virtual do Log Analytics para Linux)

## <a name="overview"></a>Descrição geral

O Azure Monitor Logs fornece capacidades de monitorização, alerta e remediação de alerta em todos os ativos de nuvem e no local. A extensão da máquina virtual Log Analytics para o Linux é publicada e suportada pela Microsoft. A extensão instala o agente Log Analytics em máquinas virtuais Azure e inscreve máquinas virtuais num espaço de trabalho existente no Log Analytics. Este documento detalha as plataformas, configurações e opções de implementação suportadas para a extensão virtual da máquina Log Analytics para o Linux.

>[!NOTE]
>Como parte da transição em curso do Microsoft Operations Management Suite (OMS) para o Monitor Azure, o Agente OMS para Windows ou Linux será referido como o agente Log Analytics para Windows e O Agente Log Analytics para o Linux.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Pré-requisitos

### <a name="operating-system"></a>Sistema operativo

Para mais detalhes sobre as distribuições suportadas pelo Linux, consulte o artigo de visão geral do [agente Log Analytics.](../../azure-monitor/platform/log-analytics-agent.md#supported-linux-operating-systems)

### <a name="agent-and-vm-extension-version"></a>Versão de extensão de agente e VM
A tabela seguinte fornece um mapeamento da versão da extensão VM log Analytics e do pacote de agente Log Analytics para cada lançamento. Está incluído um link para as notas de lançamento para a versão do pacote do agente Log Analytics. As notas de lançamento incluem detalhes sobre correções de bugs e novas funcionalidades disponíveis para uma determinada libertação de agente.  

| Versão de extensão VM Log Analytics Linux | Versão de pacote de pacote de agente de análise de log analytics | 
|--------------------------------|--------------------------|
| 1.13.9 | 1.13.3-3 |
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

O Azure Security Center disponibiliza automaticamente o agente Log Analytics e liga-o a um espaço de trabalho padrão de Log Analytics criado pela ASC na sua subscrição Azure. Se estiver a utilizar o Azure Security Center, não corra os passos deste documento. Ao fazê-lo substitui o espaço de trabalho configurado e rompe a ligação com o Azure Security Center.

### <a name="internet-connectivity"></a>Conectividade Internet

A extensão do Agente Log Analytics para o Linux requer que a máquina virtual alvo esteja ligada à internet. 

## <a name="extension-schema"></a>Esquema de extensão

O Seguinte JSON mostra o esquema para a extensão do Agente de Análise de Log. A extensão requer o ID do espaço de trabalho e a chave espaço de trabalho do espaço de trabalho do log analytics alvo; estes valores podem ser [encontrados no seu espaço](../../azure-monitor/learn/quick-collect-linux-computer.md#obtain-workspace-id-and-key) de trabalho Log Analytics no portal Azure. Uma vez que a chave do espaço de trabalho deve ser tratada como dados sensíveis, deve ser armazenada numa configuração de configuração protegida. Os dados de definição protegidos por extensão Azure VM são encriptados e apenas desencriptados na máquina virtual alvo. Note que o **espaço de trabalhoId** e **workspaceKey** são sensíveis a casos.

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
>O esquema acima pressupõe que será colocado no nível raiz do modelo. Se colocá-lo dentro do recurso virtual da máquina no modelo, as propriedades e propriedades `type` `name` devem ser alteradas, como descrito [mais abaixo](#template-deployment).

### <a name="property-values"></a>Valores patrimoniais

| Name | Valor / Exemplo |
| ---- | ---- |
| apiVersion | 2018-06-01 |
| publicador | Microsoft.EnterpriseCloud.Monitoring |
| tipo | OmsAgentForLinux |
| typeHandlerVersion | 1.7 |
| workspaceId (por exemplo) | 6f680a37-00c6-41c7-a93f-1437e3462574 |
| workspaceKey (por exemplo) | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI+rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ== |


## <a name="template-deployment"></a>Implementação de modelos

As extensões VM azure podem ser implantadas com modelos de Gestor de Recursos Azure. Os modelos são ideais quando se implanta uma ou mais máquinas virtuais que requerem a configuração da implementação de posts, como o embarque em Registos do Monitor Azure. Um modelo de Gestor de Recursos de amostra que inclui a extensão VM do Agente de Análise de Log Analytics pode ser encontrado na [Galeria Quickstart Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-ubuntu-vm). 

A configuração JSON para uma extensão virtual da máquina pode ser aninhada dentro do recurso virtual da máquina, ou colocada no nível raiz ou superior de um modelo JSON do Gestor de Recursos. A colocação da configuração JSON afeta o valor do nome e do tipo de recursos. Para mais informações, consulte o nome e o [tipo de definição para os recursos infantis.](../../azure-resource-manager/templates/child-resource-name-type.md) 

O exemplo que se segue pressupõe que a extensão VM está aninhada dentro do recurso virtual da máquina. Ao nidificar o recurso de extensão, o JSON é colocado no `"resources": []` objeto da máquina virtual.

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

Ao colocar a extensão JSON na raiz do modelo, o nome do recurso inclui uma referência à máquina virtual dos pais, e o tipo reflete a configuração aninhada.  

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

## <a name="azure-cli-deployment"></a>Implantação Azure CLI

O Azure CLI pode ser utilizado para implantar a extensão VM do Agente De Log Analytics para uma máquina virtual existente. Substitua o valor *myWorkspaceKey* abaixo pela sua chave espaço de trabalho e o valor *myWorkspaceId* pelo seu ID espaço de trabalho. Estes valores podem ser encontrados no seu espaço de trabalho Log Analytics no portal Azure em *Definições Avançadas*. 

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

Os dados sobre o estado das extensões podem ser recuperados do portal Azure e utilizando o Azure CLI. Para ver o estado de implantação das extensões para um dado VM, execute o seguinte comando utilizando o Azure CLI.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

A saída de execução de extensão é registada no seguinte ficheiro:

```
/opt/microsoft/omsagent/bin/stdout
```

### <a name="error-codes-and-their-meanings"></a>Códigos de erro e seus significados

| Código de Erro | Significado | Ação Possível |
| :---: | --- | --- |
| 9 | Ativar chamado prematuramente | [Atualize o Agente Azure Linux](https://docs.microsoft.com/azure/virtual-machines/linux/update-agent) para a versão mais recente disponível. |
| 10 | VM já está ligado a um espaço de trabalho log Analytics | Para ligar o VM ao espaço de trabalho especificado no esquema de extensão, detete as paragensOnMultipleConnections a falsas configurações públicas ou remova esta propriedade. Este VM é faturado uma vez por cada espaço de trabalho a que está ligado. |
| 11 | Config inválido fornecido à extensão | Siga os exemplos anteriores para definir todos os valores de propriedade necessários para a implantação. |
| 17 | Falha na instalação do pacote Log Analytics | 
| 19 | Falha na instalação do pacote OMI | 
| 20 | Falha na instalação do pacote SCX |
| 51 | Esta extensão não é suportada no sistema de funcionamento do VM | |
| 55 | Não é possível ligar-se ao serviço Do Monitor Azure ou as encomendas necessárias em falta ou o gestor de pacotes dpkg está bloqueado| Verifique se o sistema tem acesso à Internet ou se foi fornecido um representante http válido. Além disso, verifique a correção do ID do espaço de trabalho e verifique se os utilitários de caracóis e alcatrão estão instalados. |

Informações adicionais de resolução de problemas podem ser encontradas no Guia de Resolução de [Problemas Log Analytics-Agent-for-Linux](../../azure-monitor/platform/vmext-troubleshoot.md).

### <a name="support"></a>Suporte

Se precisar de mais ajuda em qualquer ponto deste artigo, pode contactar os especialistas do Azure nos [fóruns MSDN Azure e Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, pode apresentar um incidente de apoio ao Azure. Vá ao site de [suporte azure](https://azure.microsoft.com/support/options/) e selecione Obter suporte. Para obter informações sobre a utilização do Suporte Azure, leia o suporte do [Microsoft Azure FAQ](https://azure.microsoft.com/support/faq/).
