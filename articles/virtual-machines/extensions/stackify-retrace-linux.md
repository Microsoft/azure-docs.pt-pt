---
title: Stackify retrace extensão do agente Linux do Azure | Microsoft Docs
description: Implante o agente do Linux de rerastreamento do Stackify em uma máquina virtual do Linux.
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
ms.author: roiyz
ms.openlocfilehash: 6db152394a8e57689b34436b48dbcb4ecdc58b5a
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70084302"
---
# <a name="stackify-retrace-linux-agent-extension"></a>Extensão do agente do Linux de retrace Stackify

## <a name="overview"></a>Descrição geral

O Stackify fornece produtos que acompanham detalhes sobre seu aplicativo para ajudar a localizar e corrigir problemas rapidamente. Para equipes de desenvolvedores, o retrace é um desempenho de aplicativo totalmente integrado e de vários ambientes. Ele combina várias ferramentas de que cada equipe de desenvolvimento precisa.

O retrace é a única ferramenta que fornece todos os recursos a seguir em todos os ambientes em uma única plataforma.

* APM (gerenciamento de desempenho de aplicativos)
* Log de aplicativo e servidor
* Rastreamento e monitoramento de erros
* Métricas de servidor, aplicativo e personalizadas

**Sobre a extensão do agente do Stackify Linux**

Essa extensão fornece um caminho de instalação para o agente do Linux para rerastrear. 

## <a name="prerequisites"></a>Pré-requisitos

### <a name="operating-system"></a>Sistema operativo 

O agente de rerastreamento pode ser executado em relação a essas distribuições do Linux

| Distribuição | Version |
|---|---|
| Ubuntu | 16, 4 LTS, 14, 4 LTS, 16,10 e 17, 4 |
| Debian | 7.9 + e 8.2 +, 9 |
| Red Hat | 6.7 +, 7.1 + |
| CentOS | 6.3 +, 7.0 + |

### <a name="internet-connectivity"></a>Conectividade Internet

A extensão do agente Stackify para Linux requer que a máquina virtual de destino esteja conectada à Internet. 

Talvez seja necessário ajustar a configuração de rede para permitir conexões com o Stackify, https://support.stackify.com/hc/en-us/articles/207891903-Adding-Exceptions-to-a-Firewall consulte. 


## <a name="extension-schema"></a>Esquema de extensão

---

O JSON a seguir mostra o esquema para a extensão do agente de rerastreamento Stackify. A extensão requer o `environment` e `activationKey`o.

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

Extensões VM do Azure podem ser implementadas com modelos Azure Resource Manager. O esquema JSON detalhado na seção anterior pode ser usado em um modelo de Azure Resource Manager para executar a extensão de agente do Stackify de rerastreamento do Linux durante uma implantação de modelo de Azure Resource Manager.  

O JSON para uma extensão de máquina virtual pode ser aninhado dentro do recurso de máquina virtual ou colocado no nível raiz ou superior de um modelo JSON do Gerenciador de recursos. O posicionamento do JSON afeta o valor do nome do recurso e do tipo. Para obter mais informações, consulte definir nome e tipo para recursos filho.

O exemplo a seguir pressupõe que a extensão do Linux Stackify retrace esteja aninhada dentro do recurso de máquina virtual. Ao aninhar o recurso de extensão, o JSON é colocado no objeto "Resources": [] da máquina virtual.

A extensão requer o `environment` e `activationKey`o.

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


## <a name="powershell-deployment"></a>Implantação do PowerShell

O `Set-AzVMExtension` comando pode ser usado para implantar a extensão de máquina virtual do agente do Linux Stackify retrace para uma máquina virtual existente. Antes de executar o comando, as configurações públicas e privadas precisam ser armazenadas em uma tabela de hash do PowerShell.

A extensão requer o `environment` e `activationKey`o.

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

A ferramenta CLI do Azure pode ser usada para implantar a extensão de máquina virtual do agente do Linux Stackify retrace em uma máquina virtual existente.  

A extensão requer o `environment` e `activationKey`o.

```azurecli
az vm extension set --publisher 'Stackify.LinuxAgent.Extension' --version 1.0 --name 'StackifyLinuxAgentExtension' --protected-settings '{"activationKey":"myActivationKey"}' --settings '{"environment":"myEnvironment"}'  --resource-group 'myResourceGroup' --vm-name 'myVmName'
```

## <a name="troubleshoot-and-support"></a>Resolução de problemas e suporte

### <a name="error-codes"></a>Códigos de erro

| Código de erro | Significado | Ação possível |
| :---: | --- | --- |
| 10 | Erro de instalação | wget é obrigatório |
| 20 | Erro de instalação | o Python é obrigatório |
| 30 | Erro de instalação | sudo é obrigatório |
| 40 | Erro de instalação | activationKey é obrigatório |
| 51 | Erro de instalação | SO distribuição não suportado |
| 60 | Erro de instalação | o ambiente é necessário |
| 70 | Erro de instalação | Desconhecido |
| 80 | Habilitar erro | Falha na instalação do serviço |
| 90 | Habilitar erro | Falha na inicialização do serviço |
| 100 | Erro de desabilitação | Falha na interrupção do serviço |
| 110 | Erro de desabilitação | Falha na remoção do serviço |
| 120 | Erro de desinstalação | Falha na interrupção do serviço |

Se precisar de mais ajuda, entre em contato com o https://support.stackify.com suporte do Stackify em.