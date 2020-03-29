---
title: Startup Azure VM está presa no Windows Update Microsoft Docs
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
ms.openlocfilehash: 8a47131cb4f19cce1664eafa50c67ab1a1171e67
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919435"
---
# <a name="azure-vm-startup-is-stuck-at-windows-update"></a>Startup Azure VM está presa na atualização do Windows

Este artigo ajuda a resolver o problema quando a sua Máquina Virtual (VM) está presa na fase de Atualização do Windows durante o arranque. 


## <a name="symptom"></a>Sintoma

 Um VM windows não começa. Quando verifica as imagens na janela de [diagnóstico](../troubleshooting/boot-diagnostics.md) boot, vê que a startup está presa no processo de atualização. Seguem-se exemplos de mensagens que poderá receber:

- Instalação do Windows ##% Não desligue o seu PC. Isto levará algum tempo O seu PC irá reiniciar várias vezes
- Mantenha o seu PC ligado até que isto esteja feito. Atualização de instalação # de #... 
- Não conseguimos completar as atualizações Desfazer as alterações Não desligue o computador
- Falha configurar atualizações do Windows Reverter alterações Não desligue o computador
- Error < error code > aplicando operações de atualização ##### de ###############
- Erro fatal < código de erro > aplicando operações de atualização ##### de ##### ($...)


## <a name="solution"></a>Solução

Dependendo do número de atualizações que estão a ser instaladas ou enroladas, o processo de atualização pode demorar algum tempo. Deixe o VM neste estado por 8 horas. Se o VM ainda estiver neste estado após esse período, reinicie o VM a partir do portal Azure, e veja se pode começar normalmente. Se este passo não funcionar, tente a seguinte solução.

### <a name="remove-the-update-that-causes-the-problem"></a>Remova a atualização que causa o problema

1. Tire uma foto do disco operativo do VM afetado como cópia de segurança. Para mais informações, consulte [snapshot um disco](../windows/snapshot-copy-managed-disk.md). 
2. [Fixe o disco OS a um VM](troubleshoot-recovery-disks-portal-windows.md)de recuperação .
3. Uma vez que o disco OS esteja ligado ao VM de recuperação, corra **diskmgmt.msc** para abrir a Gestão do Disco e certifique-se de que o disco em anexo está **ON .** Tome nota da letra de unidade que é atribuída ao disco OPERATIVO anexado que segura a pasta \windows. Se o disco estiver encriptado, desencriptar o disco antes de prosseguir com os próximos passos deste documento.

4. Abra uma instância de solicitação de comando elevada (Executar como administrador). Executar o seguinte comando para obter a lista dos pacotes de atualização que estão no disco OS anexado:

        dism /image:<Attached OS disk>:\ /get-packages > c:\temp\Patch_level.txt

    Por exemplo, se o disco OS anexado estiver acionado Por Unidade F, execute o seguinte comando:

        dism /image:F:\ /get-packages > c:\temp\Patch_level.txt
5. Abra o ficheiro C:\temp\Patch_level.txt, e depois leia-o de baixo para cima. Localize a atualização que está em **instalação pendente** ou **desinstale o** estado pendente.  Segue-se uma amostra do estado da atualização:

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
    > Dependendo do tamanho da embalagem, a ferramenta DISM levará algum tempo a processar a não instalação. Normalmente, o processo será concluído dentro de 16 minutos.

7. [Desmontar o disco OS e recriar o VM](troubleshoot-recovery-disks-portal-windows.md#unmount-and-detach-original-virtual-hard-disk). Em seguida, verifique se a questão está resolvida.
