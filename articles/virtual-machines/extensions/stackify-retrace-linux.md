---
title: O stackify Retrace extensão de agente Linux do Azure | Documentos da Microsoft
description: Implemente o agente de Stackify Retrace Linux numa máquina virtual Linux.
services: virtual-machines-linux
documentationcenter: ''
author: darinhoward
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/12/2018
ms.author: roiyz
ms.openlocfilehash: ca3232dc3862cffb67d396b3ec2333fdc28dbb65
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706644"
---
# <a name="stackify-retrace-linux-agent-extension"></a>O stackify Retrace extensão de agente do Linux

## <a name="overview"></a>Descrição geral

O stackify fornece produtos que controlam detalhes sobre a sua aplicação para ajudar a encontrar e corrigir os problemas rapidamente. Para equipes de desenvolvimento, Retrace é uma potência de superutilizadores de desempenho de aplicações totalmente integradas, ambiente de vários. Ele combina várias ferramentas que precisam de todas as equipes de desenvolvimento.

Retrace é a ferramenta de apenas que fornece todas as capacidades seguintes em todos os ambientes numa única plataforma.

* Gestão de desempenho de aplicações (APM)
* Registo de servidor e de aplicação
* Monitorização e controlo de erros
* Servidor, a aplicação e métricas personalizadas

**O Stackify prestes a extensão de agente do Linux**

Esta extensão fornece um caminho de instalação para o agente do Linux para Retrace. 

## <a name="prerequisites"></a>Pré-requisitos

### <a name="operating-system"></a>Sistema operativo 

O agente de Retrace pode ser executado nessas distribuições de Linux

| Distribuição | Version |
|---|---|
| Ubuntu | 16.04 LTS, 14.04 LTS, 16.10 e 17.04 |
| Debian | 7.9 + e 8.2 +, 9 |
| Red Hat | 6.7+, 7.1+ |
| CentOS | 6.3+, 7.0+ |

### <a name="internet-connectivity"></a>Conectividade Internet

A extensão de agente stackify analisa para o Linux requer que a máquina virtual de destino está ligada à internet. 

Poderá ter de ajustar a configuração da rede para permitir ligações para o Stackify, consulte https://support.stackify.com/hc/en-us/articles/207891903-Adding-Exceptions-to-a-Firewall. 


## <a name="extension-schema"></a>Esquema de extensão

---

O JSON seguinte mostra o esquema para a extensão do agente de Retrace Stackify. A extensão requer o `environment` e `activationKey`.

```json
    {
      "type": "extensions",
      "name": "StackifyExtension",
      "apiVersion": "[variables('apiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.Compute/virtualMachines',variables('vmName'))]"
      ],
      "properties": {
        "publisher": "Stackify.LinuxAgent.Extension",
        "type": "StackifyLinuxAgentExtension",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "environment": "myEnvironment"
        },
        "protectedSettings": {
          "activationKey": "myActivationKey"
        }
      }
    }      
```

## <a name="template-deployment"></a>Implementação de modelos 

Extensões VM do Azure podem ser implementadas com modelos Azure Resource Manager. O esquema JSON detalhado na secção anterior pode ser utilizado num modelo do Azure Resource Manager para executar a extensão de agente do Linux Stackify Retrace durante uma implementação de modelo do Azure Resource Manager.  

O JSON para uma extensão de máquina virtual pode ser aninhado dentro do recurso de máquina virtual ou colocado na raiz ou de nível superior de um modelo do Resource Manager JSON. A colocação do JSON afeta o valor do tipo e nome do recurso. Para obter mais informações, veja o nome do conjunto e o tipo para recursos subordinados.

O exemplo a seguir supõe que a extensão de Stackify Retrace Linux é aninhada dentro do recurso de máquina virtual. Quando aninhar o recurso de extensão, o JSON é colocado em "recursos": [] objeto da máquina virtual.

A extensão requer o `environment` e `activationKey`.

```json
    {
      "type": "extensions",
      "name": "StackifyExtension",
      "apiVersion": "[variables('apiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.Compute/virtualMachines',variables('vmName'))]"
      ],
      "properties": {
        "publisher": "Stackify.LinuxAgent.Extension",
        "type": "StackifyLinuxAgentExtension",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "environment": "myEnvironment"
        },
        "protectedSettings": {
          "activationKey": "myActivationKey"
        }
      }
    }      
```

Quando coloca a extensão de JSON na raiz do modelo, o nome do recurso inclui uma referência à máquina virtual principal e o tipo reflete a configuração aninhada.

```json
    {
        "type": "Microsoft.Compute/virtualMachines/extensions",
        "name": "<parentVmResource>/StackifyExtension",
        "apiVersion": "[variables('apiVersion')]",
        "location": "[resourceGroup().location]",
        "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
        ],
        "properties": {
            "publisher": "Stackify.LinuxAgent.Extension",
            "type": "StackifyLinuxAgentExtension",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
              "environment": "myEnvironment"
            },
            "protectedSettings": {
              "activationKey": "myActivationKey"
            }
        }
    }
```


## <a name="powershell-deployment"></a>Implementação do PowerShell

O `Set-AzVMExtension` comando pode ser utilizado para implementar a extensão de máquina virtual o Stackify agente Linux Retrace numa máquina virtual existente. Antes de executar o comando, as configurações de públicas e privadas tem de ser armazenados numa tabela de hash do PowerShell.

A extensão requer o `environment` e `activationKey`.

```powershell
$PublicSettings = @{"environment" = "myEnvironment"}
$ProtectedSettings = @{"activationKey" = "myActivationKey"}

Set-AzVMExtension -ExtensionName "Stackify.LinuxAgent.Extension" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Stackify.LinuxAgent.Extension" `
    -ExtensionType "StackifyLinuxAgentExtension" `
    -TypeHandlerVersion 1.0 `
    -Settings $PublicSettings `
    -ProtectedSettings $ProtectedSettings `
    -Location WestUS `
```

## <a name="azure-cli-deployment"></a>Implementação de CLI do Azure 

A ferramenta de CLI do Azure pode ser utilizada para implementar a extensão de máquina virtual o Stackify agente Linux Retrace numa máquina virtual existente.  

A extensão requer o `environment` e `activationKey`.

```azurecli
az vm extension set --publisher 'Stackify.LinuxAgent.Extension' --version 1.0 --name 'StackifyLinuxAgentExtension' --protected-settings '{"activationKey":"myActivationKey"}' --settings '{"environment":"myEnvironment"}'  --resource-group 'myResourceGroup' --vm-name 'myVmName'
```

## <a name="troubleshoot-and-support"></a>Resolução de problemas e suporte

### <a name="error-codes"></a>Códigos de erro

| Código de erro | Significado | Ação possível |
| :---: | --- | --- |
| 10 | Erro de instalação | é necessário o wget |
| 20 | Erro de instalação | é necessário o Python |
| 30 | Erro de instalação | é necessário o sudo |
| 40 | Erro de instalação | é necessário o activationKey |
| 51 | Erro de instalação | Distribuição de SO não suportada |
| 60 | Erro de instalação | é necessário o ambiente |
| 70 | Erro de instalação | Desconhecido |
| 80 | Ativar erro | Falha na configuração de serviço |
| 90 | Ativar erro | Arranque do serviço falhou |
| 100 | Desativar erros | Parar serviço falhou |
| 110 | Desativar erros | Falha ao remover o serviço |
| 120 | Erro de desinstalação | Parar serviço falhou |

Se precisar de mais ajuda pode contactar o suporte de Stackify em https://support.stackify.com.