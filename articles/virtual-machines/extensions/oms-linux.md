---
title: Extensão da máquina virtual do Azure Monitor para Linux | Documentos da Microsoft
description: Implemente o agente do Log Analytics na máquina virtual Linux utilizando uma extensão de máquina virtual.
services: virtual-machines-linux
documentationcenter: ''
author: roiyz-msft
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: c7bbf210-7d71-4a37-ba47-9c74567a9ea6
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/12/2019
ms.author: roiyz
ms.openlocfilehash: 538eb492829c8ad171d1d27b51405725f53f352a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57853227"
---
# <a name="azure-monitor-virtual-machine-extension-for-linux"></a>Extensão da máquina virtual do Azure Monitor para Linux

## <a name="overview"></a>Descrição geral

Registos de Monitor do Azure fornece capacidades de remediação do alerta, monitorização e alertas ativos na cloud e no local. A extensão de máquina virtual do agente do Log Analytics para Linux é publicada e suportada pela Microsoft. A extensão instala o agente Log Analytics em máquinas virtuais do Azure e inscreve máquinas virtuais para uma área de trabalho do Log Analytics existente. Este documento detalha as plataformas suportadas, configurações e opções de implementação para a extensão de máquina virtual do Azure Monitor para Linux.

>[!NOTE]
>Como parte da transição em curso do Microsoft Operations Management Suite (OMS) para o Azure Monitor, o agente do OMS para Windows ou Linux irá ser referido como o agente de Log Analytics para o agente do Windows e do Log Analytics para Linux.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Pré-requisitos

### <a name="operating-system"></a>Sistema operativo

A extensão de agente do Log Analytics pode ser executada nessas distribuições de Linux.

| Distribuição | Versão |
|---|---|
| CentOS Linux | 6 (x86/x64) e 7 (x64) |
| Amazon Linux | 2017.09 (x64) | 
| Oracle Linux | 6 e 7 (x86/x64) |
| Red Hat Enterprise Linux Server | 6 (x86/x64) e 7 (x64) |
| Debian GNU/Linux | 8 e 9 (x86/x64) |
| Ubuntu | 14.04 LTS (x86/x64) e 18.04 LTS (x64) 16.04 LTS (x86/x64) |
| SUSE Linux Enterprise Server | 12 (x64) |

>[!NOTE]
>OpenSSL inferior à versão 1.x não é suportado em qualquer plataforma e versão 1.10 só é suportado em plataformas de x86_64 (64 bits).  
>

### <a name="agent-and-vm-extension-version"></a>Versão de agente e a extensão de VM
A tabela seguinte fornece um mapeamento da versão da extensão de VM do Azure Monitor e pacote de agente do Log Analytics para cada versão. Uma ligação para as notas de versão para a versão de pacote de agente do Log Analytics está incluída. Notas de versão incluem detalhes sobre correções de bugs e novos recursos disponíveis para uma versão de determinado agente.  

| Versão de extensão de VM do Linux de Monitor do Azure | Versão do pacote de agente de análise de registo | 
|--------------------------------|--------------------------|
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

O JSON seguinte mostra o esquema para a extensão de agente do Log Analytics. A extensão requer o ID de área de trabalho e a chave de área de trabalho da área de trabalho do Log Analytics para destino; Estes valores podem ser [encontrado na sua área de trabalho do Log Analytics](../../azure-monitor/learn/quick-collect-linux-computer.md#obtain-workspace-id-and-key) no portal do Azure. Uma vez que a chave da área de trabalho deve ser tratada como dados confidenciais, devem ser armazenado numa configuração de definição protegido. Dados de definição de protegidos de extensão VM do Azure são encriptados e desencriptados apenas na máquina de virtual de destino. Tenha em atenção que **workspaceId** e **workspaceKey** diferenciam maiúsculas de minúsculas.

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
>O esquema acima parte do princípio de que será colocado no nível de raiz do modelo. Se colocar dentro do recurso de máquina virtual no modelo, o `type` e `name` devem ser alteradas as propriedades, conforme descrito [mais abaixo](#template-deployment).
>

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

Extensões VM do Azure podem ser implementadas com modelos Azure Resource Manager. Os modelos são ideais quando implementar um ou mais máquinas virtuais que necessitam de configuração pós-implementação, tais como a integração de registos do Azure Monitor. Um modelo do Resource Manager de exemplo que inclui a extensão de VM de agente do Log Analytics pode ser encontrado no [Galeria de início rápido do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-ubuntu-vm). 

A configuração do JSON para uma extensão de máquina virtual pode ser aninhada dentro do recurso de máquina virtual ou colocada na raiz ou de nível superior de um modelo do Resource Manager JSON. A colocação da configuração do JSON afeta o valor do tipo e nome do recurso. Para obter mais informações, consulte [defina o nome e tipo para recursos subordinados](../../azure-resource-manager/resource-group-authoring-templates.md#child-resources). 

O exemplo a seguir supõe que a extensão de VM é aninhada dentro do recurso de máquina virtual. Quando aninhar o recurso de extensão, o JSON é colocado no `"resources": []` objeto da máquina virtual.

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

A CLI do Azure pode ser utilizada para implementar a extensão de VM de agente do Log Analytics para uma máquina virtual existente. Substitua a *workspaceId* e *workspaceKey* com os da sua área de trabalho do Log Analytics. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name OmsAgentForLinux \
  --publisher Microsoft.EnterpriseCloud.Monitoring \
  --version 1.7 --protected-settings '{"workspaceKey": "omskey"}' \
  --settings '{"workspaceId": "omsid"}'
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
| 9 | Ativar chamado prematuramente | [Atualizar o agente do Linux do Azure](https://docs.microsoft.com/azure/virtual-machines/linux/update-agent) para a versão mais recente disponível. |
| 10 | A VM já está ligada a uma área de trabalho do Log Analytics | Para ligar a VM para a área de trabalho especificada no esquema de extensão, definir stopOnMultipleConnections como false nas definições de públicas ou remova esta propriedade. Esta VM é faturado uma vez para cada área de trabalho está ligada. |
| 11 | Config inválido fornecido para a extensão | Siga os exemplos anteriores para definir todos os valores de propriedade necessários para a implementação. |
| 17 | Falha de instalação do pacote de análise de registo | 
| 19 | Falha de instalação do pacote OMI | 
| 20 | Falha de instalação do pacote SCX |
| 51 | Esta extensão não é suportada no sistema de operação da VM | |
| 55 | Não é possível ligar ao serviço Azure Monitor ou pacotes necessários em falta ou dpkg Gerenciador de pacotes está bloqueado| Verifique se o sistema tem acesso à Internet ou que foi fornecido um proxy HTTP válido. Além disso, verifique a correção do ID da área de trabalho de e verificar curl e tar utilitários estão instalados. |

Informações adicionais de resolução de problemas podem ser encontradas no [guia de resolução de problemas do Log Analytics-agente para Linux](../../azure-monitor/platform/vmext-troubleshoot.md).

### <a name="support"></a>Suporte

Se precisar de mais ajuda a qualquer momento neste artigo, pode contactar os especialistas do Azure sobre o [fóruns do Azure do MSDN e Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, pode enviar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione o suporte de Get. Para informações sobre como utilizar o suporte do Azure, leia os [FAQ do suporte Microsoft Azure](https://azure.microsoft.com/support/faq/).
