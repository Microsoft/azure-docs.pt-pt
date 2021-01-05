---
title: Diagnósticos de arranque para VMs em Azure / Microsoft Doc
description: Visão geral das duas funcionalidades de depuração para máquinas virtuais em Azure
services: virtual-machines
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: delhan
ms.openlocfilehash: 236390529ddaf84416448138d7b5bdf31caf681b
ms.sourcegitcommit: 42922af070f7edf3639a79b1a60565d90bb801c0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/31/2020
ms.locfileid: "97827500"
---
# <a name="how-to-use-boot-diagnostics-to-troubleshoot-virtual-machines-in-azure"></a>Como usar diagnósticos de arranque para resolver problemas em máquinas virtuais em Azure

Pode haver muitas razões para que uma máquina virtual entre num estado não-inicializável. Para resolver problemas com as suas máquinas virtuais criadas com o modelo de implementação do Gestor de Recursos, pode utilizar as seguintes funcionalidades de depuração: Saída de consola e suporte screenshot para máquinas virtuais Azure. 

Para máquinas virtuais Linux, pode ver a saída do seu registo de consola a partir do Portal. Tanto para máquinas virtuais Windows como Linux, o Azure permite-lhe ver uma imagem do VM a partir do hipervisor. Ambas as funcionalidades são suportadas por máquinas virtuais Azure em todas as regiões. Nota, as imagens e a saída podem demorar até 10 minutos a aparecer na sua conta de armazenamento.

Pode selecionar a opção **de diagnóstico boot** para visualizar o registo e a imagem.

![Resource Manager](./media/virtual-machines-common-boot-diagnostics/screenshot1.png)

## <a name="common-boot-errors"></a>Erros de arranque comuns

- [0xC000000E](https://support.microsoft.com/help/4010129)
- [0xC000000F](https://support.microsoft.com/help/4010130)
- [0xC0000011](https://support.microsoft.com/help/4010134)
- [0xC0000034](https://support.microsoft.com/help/4010140)
- [0xC0000098](https://support.microsoft.com/help/4010137)
- [0xC00000BA](https://support.microsoft.com/help/4010136)
- [0xC000014C](https://support.microsoft.com/help/4010141)
- [0xC0000221](https://support.microsoft.com/help/4010132)
- [0xC0000225](https://support.microsoft.com/help/4010138)
- [0xC0000359](https://support.microsoft.com/help/4010135)
- [0xC0000605](https://support.microsoft.com/help/4010131)
- [Não foi encontrado nenhum sistema operativo](https://support.microsoft.com/help/4010142)
- [Falha de arranque ou INACCESSIBLE_BOOT_DEVICE](https://support.microsoft.com/help/4010143)

## <a name="enable-diagnostics-on-a-virtual-machine-created-using-the-azure-portal"></a>Ativar diagnósticos numa máquina virtual criada com o Portal Azure

O procedimento a seguir é para uma máquina virtual criada utilizando o modelo de implementação do Gestor de Recursos.

No separador **Gestão,** na secção **de Monitorização,** certifique-se de que os diagnósticos de Arranque estão **ligados.** A partir da lista de down-down da conta de **armazenamento de Diagnósticos,** selecione uma conta de armazenamento na qual colocar os ficheiros de diagnóstico.
 
![Criar VM](./media/virtual-machines-common-boot-diagnostics/enable-boot-diagnostics-vm.png)

> [!NOTE]
> A funcionalidade de diagnóstico boot não suporta conta de armazenamento premium ou tipos de conta de armazenamento redundantes da zona. Se utilizar a conta de armazenamento premium para diagnósticos boot, poderá receber o erro de ArmazenamentoAccountTypeNotSupported quando iniciar o VM.
>

### <a name="deploying-from-an-azure-resource-manager-template"></a>Implantação a partir de um modelo de gestor de recursos Azure

Se estiver a implementar a partir de um modelo de Gestor de Recursos Azure, navegue para o seu recurso de máquina virtual e adende a secção de perfil de diagnóstico. Desa cos um pouco mais tarde do cabeçalho da versão API para "2015-06-15". A versão mais recente é "2018-10-01".

```json
{
  "apiVersion": "2018-10-01",
  "type": "Microsoft.Compute/virtualMachines",
  … 
```

O perfil de diagnóstico permite-lhe selecionar a conta de armazenamento onde quer colocar estes registos.

```json
    "diagnosticsProfile": {
    "bootDiagnostics": {
    "enabled": true,
    "storageUri": "[concat('https://', parameters('newStorageAccountName'), '.blob.core.windows.net')]"
    }
    }
    }
}
```

Para obter mais informações sobre a implementação de recursos utilizando modelos, consulte [Quickstart: Criar e implementar modelos de Gestor de Recursos Azure utilizando o portal Azure](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).

## <a name="enable-boot-diagnostics-on-existing-virtual-machine"></a>Ativar diagnósticos de arranque na máquina virtual existente 

Para ativar os diagnósticos boot numa máquina virtual existente, siga estes passos:

1. Inscreva-se no [portal Azure](https://portal.azure.com)e, em seguida, selecione a máquina virtual.
2. Na secção **Suporte + resolução de problemas,** selecione **diagnósticos de arranque** e, em seguida, selecione o **separador Definições.**
3. Nas **definições de diagnóstico boot,** altere o estado para **On**, e a partir da lista de down-down da **conta de Armazenamento** selecione uma conta de armazenamento. 
4. Guarde a alteração.

    ![Atualizar VM Existente](./media/virtual-machines-common-boot-diagnostics/enable-for-existing-vm.png)

### <a name="enable-boot-diagnostics-using-the-azure-cli"></a>Ativar diagnósticos de arranque com a CLI do Azure

Pode utilizar o CLI Azure para ativar os diagnósticos de arranque numa máquina virtual Azure existente. Para obter mais informações, consulte [os diagnósticos de arranque az vm](/cli/azure/vm/boot-diagnostics?view=azure-cli-latest).
