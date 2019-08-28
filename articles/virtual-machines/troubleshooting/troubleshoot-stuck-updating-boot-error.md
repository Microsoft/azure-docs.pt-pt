---
title: A inicialização da VM do Azure está presa em Windows Update | Microsoft Docs
description: Saiba como solucionar o problema quando uma inicialização de VM do Azure está presa no Windows Update.
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: v-jesits
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/09/2018
ms.author: genli
ms.openlocfilehash: b9a93448e084a42a935a8281ccd069e3604b1f18
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70089608"
---
# <a name="azure-vm-startup-is-stuck-at-windows-update"></a>A inicialização da VM do Azure está presa no Windows Update

Este artigo ajuda a resolver o problema quando sua VM (máquina virtual) está presa no estágio de Windows Update durante a inicialização. 

> [!NOTE] 
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Resource Manager e clássico](../../azure-resource-manager/resource-manager-deployment-model.md). Este artigo aborda o uso do modelo de implantação do Gerenciador de recursos. Recomendamos que você use esse modelo para novas implantações em vez de usar o modelo de implantação clássico.

## <a name="symptom"></a>Sintoma

 Uma VM do Windows não é iniciada. Ao verificar as capturas de tela na janela de [diagnóstico de inicialização](../troubleshooting/boot-diagnostics.md) , você verá que a inicialização está presa no processo de atualização. Veja a seguir exemplos de mensagens que você pode receber:

- A instalação do Windows # #% não desliga seu PC. Isso levará um tempo que seu computador será reiniciado várias vezes
- Mantenha seu PC ligado até que isso seja feito. Instalando a atualização n º de #... 
- Não foi possível concluir as atualizações desfazendo as alterações não desligue o computador
- Falha ao configurar as atualizações do Windows a reversão das alterações não desliga o computador
- Erro < código de erro > aplicar as operações de atualização # # # # # de # # # # # (\Regist...)
- Erro fatal < código de erro > aplicar as operações de atualização # # # # # de # # # # # ($ $...)


## <a name="solution"></a>Solução

Dependendo do número de atualizações que estão sendo instaladas ou revertidas, o processo de atualização pode demorar um pouco. Deixe a VM nesse estado por 8 horas. Se a VM ainda estiver nesse estado após esse período, reinicie a VM do portal do Azure e veja se ela pode ser iniciada normalmente. Se essa etapa não funcionar, tente a solução a seguir.

### <a name="remove-the-update-that-causes-the-problem"></a>Remover a atualização que causa o problema

1. Tire um instantâneo do disco do sistema operacional da VM afetada como um backup. Para obter mais informações, consulte [instantâneo de um disco](../windows/snapshot-copy-managed-disk.md). 
2. [Anexar o disco do SO a uma VM de recuperação](troubleshoot-recovery-disks-portal-windows.md).
3. Depois que o disco do sistema operacional for anexado na VM de recuperação, execute **diskmgmt. msc** para abrir o gerenciamento de disco e verifique se o disco anexado está **online**. Anote a letra da unidade atribuída ao disco do sistema operacional anexado que contém a pasta \Windows. Se o disco estiver criptografado, descriptografe o disco antes de prosseguir com as próximas etapas deste documento.

4. Abra uma instância de prompt de comando com privilégios elevados (executar como administrador). Execute o seguinte comando para obter a lista dos pacotes de atualização que estão no disco do sistema operacional anexado:

        dism /image:<Attached OS disk>:\ /get-packages > c:\temp\Patch_level.txt

    Por exemplo, se o disco do sistema operacional anexado for a unidade F, execute o seguinte comando:

        dism /image:F:\ /get-packages > c:\temp\Patch_level.txt
5. Abra o arquivo C:\temp\Patch_level.txt e, em seguida, leia-o de baixo para cima. Localize a atualização que está no estado **instalação pendente** ou desinstalação **pendente** .  Veja a seguir um exemplo do status da atualização:

     ```
    Package Identity : Package_for_RollupFix~31bf3856ad364e35~amd64~~17134.345.1.5
    State : Install Pending
    Release Type : Security Update
    Install Time :
    ```
6. Remova a atualização que causou o problema:
    
    ```
    dism /Image:<Attached OS disk>:\ /Remove-Package /PackageName:<PACKAGE NAME TO DELETE>
    ```
    Exemplo: 

    ```
    dism /Image:F:\ /Remove-Package /PackageName:Package_for_RollupFix~31bf3856ad364e35~amd64~~17134.345.1.5
    ```

    > [!NOTE] 
    > Dependendo do tamanho do pacote, a ferramenta DISM levará algum tempo para processar a desinstalação. Normalmente, o processo será concluído em 16 minutos.

7. [Desanexar o disco do SO e recriar a VM](troubleshoot-recovery-disks-portal-windows.md#unmount-and-detach-original-virtual-hard-disk). Em seguida, verifique se o problema foi resolvido.
