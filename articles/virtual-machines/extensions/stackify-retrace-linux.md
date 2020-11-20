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
ms.subservice: extensions
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/12/2018
ms.author: akjosh
ms.openlocfilehash: 0f9ac4f7c88e72cb6ddadc2450947697b0e3c6ef
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94961979"
---
# <a name="stackify-retrace-linux-agent-extension"></a>Stackify Retrace Extensão do Agente Linux

## <a name="overview"></a>Descrição geral

O Stackify fornece produtos que rastreiam detalhes sobre a sua aplicação para ajudar a encontrar e corrigir problemas rapidamente. Para equipas de desenvolvedores, o Retrace é um super-poder de desempenho de aplicações totalmente integrado, multi-ambiente. Combina várias ferramentas que todas as equipas de desenvolvimento precisam.

A Retrace é a única ferramenta que fornece todas as seguintes capacidades em todos os ambientes numa única plataforma.

* Gestão do desempenho da aplicação (APM)
* Registo de aplicações e servidores
* Rastreio e monitorização de erros
* Servidor, aplicação e métricas personalizadas

**Sobre a extensão do agente Do Stackify Linux**

Esta extensão fornece um caminho de instalação para o Agente Linux para Refazer. 

## <a name="prerequisites"></a>Pré-requisitos

### <a name="operating-system"></a>Sistema operativo 

O agente Retrace pode ser executado contra estas distribuições Linux

| Distribuição | Versão |
|---|---|
| Ubuntu | 16.04 LTS, 14.04 LTS, 16.10 e 17.04 |
| Debian | 7.9+ e 8.2+, 9 |
| Red Hat | 6.7+, 7.1+ |
| CentOS | 6.3+, 7.0+ |

### <a name="internet-connectivity"></a>Conectividade Internet

A extensão do Agente Stackify para o Linux requer que a máquina virtual alvo esteja ligada à internet. 

Pode ser necessário ajustar a sua configuração de rede para permitir ligações ao Stackify, ver https://support.stackify.com/hc/en-us/articles/207891903-Adding-Exceptions-to-a-Firewall . 


## <a name="extension-schema"></a>Esquema de extensão

---

O JSON seguinte mostra o esquema para a extensão do Agente Detracição de Stackify. A extensão requer o `environment` e `activationKey` .

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

As extensões Azure VM podem ser implementadas com modelos Azure Resource Manager. O esquema JSON detalhado na secção anterior pode ser usado num modelo de Gestor de Recursos Azure para executar a extensão do Agente Linux de Stackify retrace durante uma implementação do modelo do Gestor de Recursos Azure.  

O JSON para uma extensão de máquina virtual pode ser aninhado dentro do recurso de máquina virtual, ou colocado no nível raiz ou superior de um modelo JSON do Gestor de Recursos. A colocação do JSON afeta o valor do nome e do tipo de recurso. Para obter mais informações, consulte o nome definido e o tipo para obter recursos para crianças.

O exemplo a seguir pressupõe que a extensão Stackify Retrace Linux está aninhada dentro do recurso da máquina virtual. Ao nidificar o recurso de extensão, o JSON é colocado nos "recursos": [] objeto da máquina virtual.

A extensão requer o `environment` e `activationKey` .

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

Ao colocar a extensão JSON na raiz do modelo, o nome do recurso inclui uma referência à máquina virtual do progenitor, e o tipo reflete a configuração aninhada.

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

O `Set-AzVMExtension` comando pode ser utilizado para implantar a extensão virtual da máquina virtual Stackify Retrace Agent para uma máquina virtual existente. Antes de executar o comando, as configurações públicas e privadas precisam de ser armazenadas numa tabela de haxixe PowerShell.

A extensão requer o `environment` e `activationKey` .

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

## <a name="azure-cli-deployment"></a>Implantação do Azure CLI 

A ferramenta Azure CLI pode ser usada para implantar a extensão da máquina virtual Stackify Retrace Agent para uma máquina virtual existente.  

A extensão requer o `environment` e `activationKey` .

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
| 40 | Instalar Erro | ativaçãoKey é necessário |
| 51 | Instalar Erro | OS distro não suportado |
| 60 | Instalar Erro | ambiente é necessário |
| 70 | Instalar Erro | Desconhecido |
| 80 | Ativar erro | A configuração do serviço falhou |
| 90 | Ativar erro | Startup de serviço falhou |
| 100 | Desativar erro | Paragem de serviço falhou |
| 110 | Desativar erro | Remoção de serviço falhou |
| 120 | Desinstalar erro | Paragem de serviço falhou |

Se precisar de mais ajuda, pode contactar o suporte do Stackify em https://support.stackify.com .