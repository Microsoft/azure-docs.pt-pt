---
title: Diagnóstico de arranque Azure
description: Visão geral dos diagnósticos de arranque da Azure e diagnósticos de arranque geridos
services: virtual-machines
ms.service: virtual-machines
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.date: 11/06/2020
ms.openlocfilehash: 408ba76c44d1161a4b91ccc037721796c7b94661
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96500755"
---
# <a name="azure-boot-diagnostics"></a>Diagnóstico de arranque Azure

O diagnóstico de arranque é uma funcionalidade de depuração para máquinas virtuais Azure (VM) que permite o diagnóstico de falhas de arranque VM. O diagnóstico de arranque permite que um utilizador observe o estado do seu VM à medida que está a ser iniciado através da recolha de informações de registos em série e imagens.

## <a name="boot-diagnostics-storage-account"></a>Conta de armazenamento de diagnóstico de arranque
Ao criar um VM no portal Azure, o diagnóstico de arranque é ativado por padrão. A experiência de diagnóstico de arranque recomendada é usar uma conta de armazenamento gerida, uma vez que produz melhorias significativas no desempenho no tempo para criar um VM Azure. Isto porque será utilizada uma conta de armazenamento gerida pelo Azure, removendo o tempo necessário para criar uma nova conta de armazenamento de utilizador para armazenar os dados de diagnóstico de arranque.

Uma experiência alternativa de diagnóstico de arranque é usar uma conta de armazenamento gerida pelo utilizador. Um utilizador pode criar uma nova conta de armazenamento ou utilizar uma já existente. 

> [!IMPORTANT]
> As bolhas de dados de diagnóstico de arranque (que compreendem registos e imagens instantâneas) são armazenadas numa conta de armazenamento gerida. Os clientes serão cobrados apenas em GiBs usados pelas bolhas, e não no tamanho provisível do disco. Os contadores instantâneos serão utilizados para a faturação da conta de armazenamento gerida. Uma vez que as contas geridas são criadas em LRS Standard ou ZRS Standard, os clientes serão cobrados a $0,05/GB por mês apenas pelo tamanho das suas bolhas de dados de diagnóstico. Para obter mais informações sobre este preço, consulte [os preços dos discos geridos](https://azure.microsoft.com/pricing/details/managed-disks/). Os clientes verão esta taxa ligada ao seu recurso VM URI. 

## <a name="boot-diagnostics-view"></a>Vista de diagnóstico de arranque
Localizada na lâmina da máquina virtual, a opção de diagnóstico de arranque encontra-se na secção *de Suporte e Resolução de Problemas* no portal Azure. A seleção de diagnósticos de arranque apresentará uma imagem e informações de registo em série. O registo em série contém mensagens de kernel e a imagem é uma imagem do seu estado atual dos VMs. Baseado em se o VM está a executar Windows ou Linux determina como seria a imagem esperada. Para o Windows, os utilizadores irão ver um fundo de ambiente de trabalho e, para o Linux, os utilizadores irão ver uma solicitação de login.

:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-linux.png" alt-text="Screenshot dos diagnósticos de botas Linux":::
:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-windows.png" alt-text="Screenshot dos diagnósticos de arranque do Windows":::

## <a name="enable-managed-boot-diagnostics"></a>Ativar diagnósticos de arranque geridos 
Os diagnósticos de arranque geridos podem ser ativados através dos modelos do portal Azure, CLI e ARM. A habilitação através do PowerShell ainda não está suportada. 

### <a name="enable-managed-boot-diagnostics-using-the-azure-portal"></a>Ativar diagnósticos de arranque geridos utilizando o portal Azure
Ao criar um VM no portal Azure, a definição predefinida é ter diagnósticos de arranque ativados utilizando uma conta de armazenamento gerida. Para ver isto, navegue no separador *Gestão* durante a criação de VM. 

:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-enable-portal.png" alt-text="Screenshot permitindo diagnósticos de arranque geridos durante a criação de VM.":::

### <a name="enable-managed-boot-diagnostics-using-cli"></a>Ativar diagnósticos de arranque geridos usando CLI
Os diagnósticos de arranque com uma conta de armazenamento gerida são suportados no Azure CLI 2.12.0 e posterior. Se não inserir um nome ou URI para uma conta de armazenamento, será utilizada uma conta gerida. Para obter mais informações e amostras de código consulte a [documentação do CLI para diagnóstico de arranque](/cli/azure/vm/boot-diagnostics?preserve-view=true&view=azure-cli-latest).

### <a name="enable-managed-boot-diagnostics-using-azure-resource-manager-arm-templates"></a>Ativar diagnósticos de arranque geridos utilizando modelos do Azure Resource Manager (ARM)
Tudo depois da versão API 2020-06-01 suporta diagnósticos de arranque geridos. Para mais informações, consulte [a visualização do caso de diagnóstico de arranque](/rest/api/compute/virtualmachines/createorupdate#bootdiagnostics).

```ARM Template
            "name": "[parameters('virtualMachineName')]",
            "type": "Microsoft.Compute/virtualMachines",
            "apiVersion": "2020-06-01",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[concat('Microsoft.Network/networkInterfaces/', parameters('networkInterfaceName'))]"
            ],
            "properties": {
                "hardwareProfile": {
                    "vmSize": "[parameters('virtualMachineSize')]"
                },
                "storageProfile": {
                    "osDisk": {
                        "createOption": "fromImage",
                        "managedDisk": {
                            "storageAccountType": "[parameters('osDiskType')]"
                        }
                    },
                    "imageReference": {
                        "publisher": "Canonical",
                        "offer": "UbuntuServer",
                        "sku": "18.04-LTS",
                        "version": "latest"
                    }
                },
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[resourceId('Microsoft.Network/networkInterfaces', parameters('networkInterfaceName'))]"
                        }
                    ]
                },
                "osProfile": {
                    "computerName": "[parameters('virtualMachineComputerName')]",
                    "adminUsername": "[parameters('adminUsername')]",
                    "linuxConfiguration": {
                        "disablePasswordAuthentication": true
                    }
                },
                "diagnosticsProfile": {
                    "bootDiagnostics": {
                        "enabled": true
                    }
                }
            }
        }
    ],

```

## <a name="limitations"></a>Limitações
- O diagnóstico de arranque só está disponível para VMs do Azure Resource Manager.
- Os diagnósticos de arranque geridos não suportam VMs utilizando discos de OS não geridos.
- Os diagnósticos de arranque não suportam contas de armazenamento premium, se uma conta de armazenamento premium for utilizada para diagnósticos de arranque os utilizadores receberão um `StorageAccountTypeNotSupported` erro ao iniciar o VM. 
- As contas de armazenamento geridas são suportadas na versão API do Gestor de Recursos "2020-06-01" e posteriormente.
- A Azure Serial Console é atualmente incompatível com uma conta de armazenamento gerida para diagnósticos de arranque. Saiba mais sobre [a Consola Em Série Azure.](./troubleshooting/serial-console-overview.md)
- O Portal apenas suporta a utilização de diagnósticos de arranque com uma conta de armazenamento gerida para VMs de exemplo único.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre a [Consola em Série Azure](./troubleshooting/serial-console-overview.md) e como utilizar diagnósticos de arranque para [resolver problemas em máquinas virtuais em Azure](./troubleshooting/boot-diagnostics.md).