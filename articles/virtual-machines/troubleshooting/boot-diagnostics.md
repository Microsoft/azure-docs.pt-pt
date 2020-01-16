---
title: Diagnóstico de inicialização para VMs no Azure | Documento da Microsoft
description: Visão geral dos dois recursos de depuração para máquinas virtuais no Azure
services: virtual-machines
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: delhan
ms.openlocfilehash: 0506527808892bf1ee531d892e2773d095e18560
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75965634"
---
# <a name="how-to-use-boot-diagnostics-to-troubleshoot-virtual-machines-in-azure"></a>Como usar o diagnóstico de inicialização para solucionar problemas de máquinas virtuais no Azure

Pode haver muitas razões pelas quais uma máquina virtual entra em um estado não inicializável. Para resolver problemas com suas máquinas virtuais criadas usando o modelo de implantação do Gerenciador de recursos, você pode usar os seguintes recursos de depuração: saída do console e suporte a captura de tela para máquinas virtuais do Azure. 

Para máquinas virtuais do Linux, você pode exibir a saída do log do console no Portal. Para máquinas virtuais Windows e Linux, o Azure permite que você veja uma captura de tela da VM do hipervisor. Os dois recursos têm suporte para máquinas virtuais do Azure em todas as regiões. Tenha em atenção que as capturas de ecrã e a saída podem demorar até 10 minutos a aparecer na sua conta de armazenamento.

Você pode selecionar a opção de **diagnóstico de inicialização** para exibir o log e a captura de tela.

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

## <a name="enable-diagnostics-on-a-virtual-machine-created-using-the-azure-portal"></a>Habilitar o diagnóstico em uma máquina virtual criada usando o portal do Azure

O procedimento a seguir é para uma máquina virtual criada usando o modelo de implantação do Gerenciador de recursos.

Na guia **Gerenciamento** , na seção **monitoramento** , verifique se o **diagnóstico de inicialização** está ativado. Na lista suspensa **conta de armazenamento de diagnóstico** , selecione uma conta de armazenamento na qual colocar os arquivos de diagnóstico.
 
![Criar VM](./media/virtual-machines-common-boot-diagnostics/enable-boot-diagnostics-vm.png)

> [!NOTE]
> O recurso de diagnóstico de inicialização não dá suporte à conta de armazenamento Premium. Se você usar a conta de armazenamento Premium para o diagnóstico de inicialização, poderá receber o erro StorageAccountTypeNotSupported ao iniciar a VM.
>

### <a name="deploying-from-an-azure-resource-manager-template"></a>Implantando de um modelo de Azure Resource Manager

Se você estiver implantando de um modelo de Azure Resource Manager, navegue até o recurso de máquina virtual e acrescente a seção de perfil de diagnóstico. Defina o cabeçalho de versão da API como "2015-06-15" ou posterior. A versão mais recente é "2018-10-01".

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

Para obter mais informações sobre como implantar recursos usando modelos, consulte [início rápido: criar e implantar modelos de Azure Resource Manager usando o portal do Azure](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).

## <a name="enable-boot-diagnostics-on-existing-virtual-machine"></a>Habilitar o diagnóstico de inicialização na máquina virtual existente 

Para habilitar o diagnóstico de inicialização em uma máquina virtual existente, siga estas etapas:

1. Entre no [portal do Azure](https://portal.azure.com)e, em seguida, selecione a máquina virtual.
2. Na seção **suporte + solução de problemas** , selecione **diagnóstico de inicialização**e, em seguida, selecione a guia **configurações** .
3. Em configurações de **diagnóstico de inicialização** , altere o status para **ativado**e, na lista suspensa **conta de armazenamento** , selecione uma conta de armazenamento. 
4. Guarde a alteração.

    ![Atualizar VM Existente](./media/virtual-machines-common-boot-diagnostics/enable-for-existing-vm.png)

Você deve reiniciar a máquina virtual para que a alteração tenha efeito.

### <a name="enable-boot-diagnostics-using-the-azure-cli"></a>Habilitar o diagnóstico de inicialização usando o CLI do Azure

Você pode usar o CLI do Azure para habilitar o diagnóstico de inicialização em uma máquina virtual do Azure existente. Para obter mais informações, consulte [AZ VM boot-Diagnostics](
https://docs.microsoft.com/cli/azure/vm/boot-diagnostics?view=azure-cli-latest).
