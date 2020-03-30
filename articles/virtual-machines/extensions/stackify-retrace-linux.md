---
title: Stackify Retrace Azure Linux Agent Extension
description: Desloque o agente Stackify Retrace Linux numa máquina virtual Linux.
services: virtual-machines-linux
documentationcenter: ''
author: darinhoward
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/12/2018
ms.author: akjosh
ms.openlocfilehash: 5914947bd994ee405f253e34c3dd919dd6561898
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253796"
---
# <a name="stackify-retrace-linux-agent-extension"></a>Stackify Retrace Linux Agent Extension

## <a name="overview"></a>Descrição geral

Stackify fornece produtos que rastreiam detalhes sobre a sua aplicação para ajudar a encontrar e corrigir problemas rapidamente. Para equipas de desenvolvedores, o Retrace é um super-poder de desempenho de desempenho de aplicações totalmente integrado, multi-ambiente. Combina várias ferramentas que todas as equipas de desenvolvimento precisam.

Retrace é a ferramenta ONLY que fornece todas as seguintes capacidades em todos os ambientes numa única plataforma.

* Gestão de desempenho de aplicações (APM)
* Registo de aplicação e servidor
* Rastreio e monitorização de erros
* Servidor, aplicação e métricas personalizadas

**Sobre stackify Linux Agent Extension**

Esta extensão fornece um caminho de instalação para o Agente Linux para Retrace. 

## <a name="prerequisites"></a>Pré-requisitos

### <a name="operating-system"></a>Sistema operativo 

O agente Retrace pode ser executado contra estas distribuições linux

| Distribuição | Versão |
|---|---|
| Ubuntu | 16.04 LTS, 14.04 LTS, 16.10 e 17.04 |
| Debian | 7.9+ e 8.2+, 9 |
| Red Hat | 6.7+, 7.1+ |
| CentOS | 6.3+, 7.0+ |

### <a name="internet-connectivity"></a>Conectividade Internet

A extensão do Agente Stackify para o Linux requer que a máquina virtual alvo esteja ligada à internet. 

Pode ser necessário ajustar a configuração da sua rede https://support.stackify.com/hc/en-us/articles/207891903-Adding-Exceptions-to-a-Firewallpara permitir que as ligações ao Stackify, ver . 


## <a name="extension-schema"></a>Esquema de extensão

---

O jSON seguinte mostra o esquema para a extensão do Agente de Retrace Stackify. A extensão `environment` requer `activationKey`a e .

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

As extensões VM azure podem ser implantadas com modelos de Gestor de Recursos Azure. O esquema JSON detalhado na secção anterior pode ser usado num modelo de Gestor de Recursos Azure para executar a extensão do agente Stackify Retrace Linux durante uma implementação do modelo do Gestor de Recursos Azure.  

O JSON para uma extensão virtual da máquina pode ser aninhado dentro do recurso virtual da máquina, ou colocado no nível raiz ou superior de um modelo JSON do Gestor de Recursos. A colocação do JSON afeta o valor do nome e do tipo de recursos. Para mais informações, consulte o nome e o tipo de definição para os recursos infantis.

O exemplo seguinte pressupõe que a extensão Stackify Retrace Linux está aninhada dentro do recurso virtual da máquina. Ao nidificar o recurso de extensão, o JSON é colocado nos "recursos": [] objeto da máquina virtual.

A extensão `environment` requer `activationKey`a e .

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

Ao colocar a extensão JSON na raiz do modelo, o nome do recurso inclui uma referência à máquina virtual dos pais, e o tipo reflete a configuração aninhada.

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


## <a name="powershell-deployment"></a>Implementação powerShell

O `Set-AzVMExtension` comando pode ser utilizado para implantar a extensão virtual do Agente De trace Stackify para uma máquina virtual existente. Antes de executar o comando, as configurações públicas e privadas precisam de ser armazenadas numa tabela de hash PowerShell.

A extensão `environment` requer `activationKey`a e .

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

## <a name="azure-cli-deployment"></a>Implantação Azure CLI 

A ferramenta Azure CLI pode ser utilizada para implementar a extensão virtual do Agente Stackify Retrace Linux para uma máquina virtual existente.  

A extensão `environment` requer `activationKey`a e .

```azurecli
az vm extension set --publisher 'Stackify.LinuxAgent.Extension' --version 1.0 --name 'StackifyLinuxAgentExtension' --protected-settings '{"activationKey":"myActivationKey"}' --settings '{"environment":"myEnvironment"}'  --resource-group 'myResourceGroup' --vm-name 'myVmName'
```

## <a name="troubleshoot-and-support"></a>Resolução de problemas e apoio

### <a name="error-codes"></a>Códigos de erro

| Código de erro | Significado | Possível ação |
| :---: | --- | --- |
| 10 | Instalar Erro | wget é necessário |
| 20 | Instalar Erro | python é necessário |
| 30 | Instalar Erro | sudo é necessário |
| 40 | Instalar Erro | activaçãoÉ necessário activarChave |
| 51 | Instalar Erro | Distro osso não suportado |
| 60 | Instalar Erro | ambiente é necessário |
| 70 | Instalar Erro | Desconhecido |
| 80 | Ativar erro | A configuração do serviço falhou |
| 90 | Ativar erro | Startup de serviço falhou |
| 100 | Erro de desativar | Paragem de serviço falhada |
| 110 | Erro de desativar | Remoção de serviço falhou |
| 120 | Desinstalar erro | Paragem de serviço falhada |

Se precisar de mais ajuda, pode https://support.stackify.comcontactar o suporte stackify em .