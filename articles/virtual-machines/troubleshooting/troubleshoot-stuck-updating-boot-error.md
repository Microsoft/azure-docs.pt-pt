---
title: Inicialização VM do Azure está bloqueada nos Windows Update | Documentos da Microsoft
description: Saiba como resolver o problema, quando uma inicialização de VM do Azure está bloqueada nos Windows update.
services: virtual-machines-windows
documentationCenter: ''
authors: genli
manager: cshepard
editor: v-jesits
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/09/2018
ms.author: genli
ms.openlocfilehash: 7fbf157152bd5c2c35c496b1fd8e707884d0f3da
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56731468"
---
# <a name="azure-vm-startup-is-stuck-at-windows-update"></a>Inicialização VM do Azure está bloqueada nos Windows update

Este artigo ajuda a resolver o problema quando a Máquina Virtual (VM) está bloqueada a fase de atualização do Windows durante o arranque. 

> [!NOTE] 
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Resource Manager e clássica](../../azure-resource-manager/resource-manager-deployment-model.md). Este artigo explica como utilizar o modelo de implementação do Resource Manager. Recomendamos que utilize este modelo para novas implementações em vez de usar o modelo de implementação clássica.

## <a name="symptom"></a>Sintoma

 Uma VM do Windows não iniciar. Quando dá entrada as capturas de ecrã do [diagnósticos de arranque](../troubleshooting/boot-diagnostics.md) janela, verá que o arranque fica preso no processo de atualização. Seguem-se exemplos de mensagens que possa receber:

- Instalar Windows ##% não desativar o seu PC. Isto irá demorar algum tempo seu PC será reiniciado diversas vezes
- Mantenha seu PC em até que isso é feito. Instalar a atualizar n. º de #... 
- Não conseguimos concluir as atualizações de anular alterações não desativar o seu computador
- Atualizações de Windows configuração Reverting alterações do falha não desligar o computador
- Erro < código do erro > aplicação de operações de atualização de # # # de # # # (\Regist...)
- Erro fatal < código do erro > aplicação de operações de atualização de # # # de # # # (US $$...)


## <a name="solution"></a>Solução

Dependendo do número de atualizações que estão a obter instalado ou revertida backup, o processo de atualização pode demorar algum tempo. Deixe a VM neste estado por 8 horas. Se a VM ainda está nesse Estado após esse período, reinicie a VM a partir do portal do Azure e veja se pode inicie normalmente. Se este passo não funcionar, experimente a seguinte solução.

### <a name="remove-the-update-that-causes-the-problem"></a>Remover a atualização que faz com que o problema

1. Tire um instantâneo do disco do SO da VM afetado como uma cópia de segurança. Para obter mais informações, consulte [instantâneo de um disco](../windows/snapshot-copy-managed-disk.md). 
2. [Anexar o disco do SO a uma VM de recuperação](troubleshoot-recovery-disks-portal-windows.md).
3. Depois do disco do SO é anexado na VM de recuperação, execute **Diskmgmt. msc** para abrir a gestão de discos e certifique-se de que o disco anexado está **ONLINE**. Observe a letra de unidade que está atribuída ao disco do SO anexado que contém a pasta \Windows. Se o disco está criptografado, descriptografar o disco antes de continuar com os passos neste documento.

4. Abra uma instância de linha de comandos elevada (executar como administrador). Execute o seguinte comando para obter a lista dos pacotes de atualização que estão no disco do SO anexado:

        dism /image:<Attached OS disk>:\ /get-packages > c:\temp\Patch_level.txt

    Por exemplo, se utilizar o disco do SO anexado como unidade F, execute o seguinte comando:

        dism /image:F:\ /get-packages > c:\temp\Patch_level.txt
5. Abra o ficheiro de C:\temp\Patch_level.txt e, em seguida, lê-lo na parte inferior de cópia de segurança. Localize a atualização que está **instalação pendente** ou **desinstalar pendente** estado.  Segue-se um exemplo do Estado de atualização:

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
    > Dependendo do tamanho do pacote, a ferramenta DISM irá demorar algum tempo para processar a desinstalação. Normalmente, o processo será concluído dentro de minutos de 16.

7. [Desanexar o disco do SO e recriar a VM](troubleshoot-recovery-disks-portal-windows.md#unmount-and-detach-original-virtual-hard-disk). Em seguida, verifique se o problema foi resolvido.