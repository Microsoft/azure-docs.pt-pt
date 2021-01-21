---
title: A startup Azure VM está presa no Windows Update| Microsoft Docs
description: Saiba como resolver o problema quando uma startup Azure VM está presa na atualização do Windows.
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: v-jesits
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/09/2018
ms.author: genli
ms.openlocfilehash: 3090b7b889d914fc0cdb598b8bf29a73c81f50cb
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632008"
---
# <a name="azure-vm-startup-is-stuck-at-windows-update"></a>Startup Azure VM está presa na atualização do Windows

Este artigo ajuda a resolver o problema quando a sua Máquina Virtual (VM) está presa na fase de Atualização do Windows durante o arranque. 


## <a name="symptom"></a>Sintoma

 Um VM do Windows não começa. Quando verifica as imagens na janela de [diagnóstico boot,](../troubleshooting/boot-diagnostics.md) vê que a startup está presa no processo de atualização. Seguem-se exemplos de mensagens que poderá receber:

- Instalação do Windows ##% Não desligue o seu PC. Isto levará algum tempo O seu PC recomeçará várias vezes
- Mantenha o seu PC ligado até que isto esteja terminado. Instalar atualização # de #... 
- Não conseguimos completar as atualizações Desfazer alterações Não desligue o computador
- Falha na configuração das atualizações do Windows Reverter alterações Não desligue o computador
- Error < error code > applying update operations ## of #(\Regist...)
- Erro Fatal < código de erro > aplicando operações de atualização # de #$...)


## <a name="solution"></a>Solução
> [!TIP]
> Se tiver uma cópia de segurança recente do VM, poderá tentar [restaurar o VM da cópia de segurança](../../backup/backup-azure-arm-restore-vms.md) para corrigir o problema da bota.

Dependendo do número de atualizações que estão a ser instaladas ou enroladas, o processo de atualização pode demorar algum tempo. Deixe o VM neste estado por 8 horas. Se o VM ainda estiver neste estado após esse período, reinicie o VM a partir do portal Azure e veja se pode começar normalmente. Se este passo não funcionar, tente a seguinte solução.

### <a name="remove-the-update-that-causes-the-problem"></a>Remova a atualização que causa o problema

1. Tire uma foto do disco de SO do VM afetado como cópia de segurança. Para mais informações, consulte [Snapshot um disco](../windows/snapshot-copy-managed-disk.md). 
2. [Fixe o disco DE A uma VM de recuperação](troubleshoot-recovery-disks-portal-windows.md).
3. Assim que o disco OS estiver ligado ao VM de recuperação, corra **diskmgmt.msc** para abrir a Gestão do Disco e certifique-se de que o disco anexo está **ONLINE**. Tome nota da letra de acionamento atribuída ao disco de oss anexado que mantém a pasta \windows. Se o disco estiver encriptado, desencriptar o disco antes de prosseguir com os próximos passos neste documento.

4. Abra uma instância de ordem elevada (Executar como administrador). Executar o seguinte comando para obter a lista dos pacotes de atualização que estão no disco oss anexado:

    ```console
    dism /image:<Attached OS disk>:\ /get-packages > c:\temp\Patch_level.txt
    ```

    Por exemplo, se o disco de sos anexado estiver a unidade F, executar o seguinte comando:

    ```console
    dism /image:F:\ /get-packages > c:\temp\Patch_level.txt
    ```

5. Abra o ficheiro C:\temp\Patch_level.txt e leia-o de baixo para cima. Localizar a atualização que está em **Instalar Estado Pendente** ou **Desinstalar.**  Segue-se uma amostra do estado de atualização:

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
    > Dependendo do tamanho da embalagem, a ferramenta DISM demorará algum tempo a processar a não instalação. Normalmente, o processo será concluído dentro de 16 minutos.

7. [Retire o disco de SO e recrie o VM](troubleshoot-recovery-disks-portal-windows.md#unmount-and-detach-the-original-virtual-hard-disk). Então verifique se a questão está resolvida.
