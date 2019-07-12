---
title: Diagnóstico de arranque para VMs no Azure | Microsoft Docs
description: Descrição geral de duas funcionalidades de depuração para máquinas virtuais no Azure
services: virtual-machines
author: Deland-Han
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: delhan
ms.openlocfilehash: 7796d24b88ccc531b8042bfadf9d87f0072a2994
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67709869"
---
# <a name="how-to-use-boot-diagnostics-to-troubleshoot-virtual-machines-in-azure"></a>Como utilizar o diagnóstico de arranque para resolver problemas relacionados com máquinas virtuais no Azure

Podem existir muitos motivos que uma máquina virtual entra num Estado de não arranque. Para resolver problemas com as máquinas virtuais criadas com o modelo de implementação do Resource Manager pode utilizar os seguintes recursos de depuração: Saída da consola e captura de ecrã de suporte para máquinas virtuais do Azure. 

Para máquinas virtuais do Linux, pode ver a saída de registo da consola a partir do Portal. Para máquinas virtuais Windows e Linux, o Azure permite-lhe ver uma captura de ecrã da VM do hipervisor. Os dois recursos são suportados para máquinas virtuais do Azure em todas as regiões. Tenha em atenção que as capturas de ecrã e a saída podem demorar até 10 minutos a aparecer na sua conta de armazenamento.

Pode selecionar o **diagnósticos de arranque** opção para ver o registo e a captura de ecrã.

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

## <a name="enable-diagnostics-on-a-virtual-machine-created-using-the-azure-portal"></a>Ativar o diagnóstico numa máquina virtual criada através do Portal do Azure

O procedimento seguinte é para uma máquina virtual criada com o modelo de implementação do Resource Manager.

Sobre o **gestão** separador **monitorização** secção, certifique-se de que **diagnósticos de arranque** está ativada. Do **conta de armazenamento de diagnóstico** pendente, selecione uma conta de armazenamento onde colocar os ficheiros de diagnóstico.
 
![Criar VM](./media/virtual-machines-common-boot-diagnostics/enable-boot-diagnostics-vm.png)

> [!NOTE]
> A funcionalidade de diagnóstico de arranque não suporta a conta de armazenamento premium. Se utilizar a conta de armazenamento premium para o diagnóstico de arranque, poderá receber o erro StorageAccountTypeNotSupported quando iniciar a VM.
>

### <a name="deploying-from-an-azure-resource-manager-template"></a>Implementar a partir de um modelo Azure Resource Manager

Se estiver a implementar a partir de um modelo Azure Resource Manager, navegue para o recurso de máquina virtual e acrescente a secção de perfil de diagnóstico. Defina o cabeçalho de versão de API para "2015-06-15" ou posterior. A versão mais recente é "2018-10-01".

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

Para obter mais informações sobre como implementar recursos com modelos, consulte [início rápido: Criar e implementar modelos Azure Resource Manager com o portal do Azure](../../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md).

## <a name="enable-boot-diagnostics-on-existing-virtual-machine"></a>Ativar o diagnóstico de arranque numa máquina virtual existente 

Para ativar o diagnóstico de arranque numa máquina virtual existente, siga estes passos:

1. Inicie sessão para o [portal do Azure](https://portal.azure.com)e, em seguida, selecione a máquina virtual.
2. Na **suporte + resolução de problemas** secção, selecione **diagnósticos de arranque**, em seguida, selecione o **definições** separador.
3. Na **diagnósticos de arranque** as definições, altere o estado para **no**e para o **conta de armazenamento** na lista pendente, selecione uma conta de armazenamento. 
4. Guarde a alteração.

    ![Atualizar VM Existente](./media/virtual-machines-common-boot-diagnostics/enable-for-existing-vm.png)

Tem de reiniciar a máquina virtual para que a alteração tenha efeito.

### <a name="enable-boot-diagnostics-using-the-azure-cli"></a>Ativar diagnósticos de arranque com a CLI do Azure

Pode utilizar a CLI do Azure para ativar o diagnóstico de arranque numa máquina virtual do Azure existente. Para obter mais informações, consulte [az vm boot-diagnostics](
https://docs.microsoft.com/cli/azure/vm/boot-diagnostics?view=azure-cli-latest).
