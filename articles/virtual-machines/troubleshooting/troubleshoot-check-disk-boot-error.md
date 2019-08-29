---
title: Verificando o sistema de arquivos ao inicializar uma VM do Azure | Microsoft Docs
description: Saiba como resolver o problema que a VM mostra verificando o sistema de arquivos ao inicializar | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/31/2018
ms.author: genli
ms.openlocfilehash: ad4053c2dda50598853528bb6e8b3441c455fbba
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70080230"
---
# <a name="windows-shows-checking-file-system-when-booting-an-azure-vm"></a>O Windows mostra "verificando o sistema de arquivos" ao inicializar uma VM do Azure

Este artigo descreve o erro "verificando o sistema de arquivos" que você pode encontrar ao inicializar uma VM (máquina virtual) do Windows no Microsoft Azure.

> [!NOTE] 
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Resource Manager e clássico](../../azure-resource-manager/resource-manager-deployment-model.md). Este artigo descreve como usar o modelo de implantação do Gerenciador de recursos, que é recomendável usar para novas implantações em vez do modelo de implantação clássico.

## <a name="symptom"></a>Sintoma 

Uma VM do Windows não é iniciada. Ao verificar as capturas de tela de inicialização no [diagnóstico de inicialização](boot-diagnostics.md), você verá que o processo de verificação de disco (chkdsk. exe) está sendo executado com uma das seguintes mensagens:

- Verificando e reparando a unidade (C:)
- Verificando o sistema de arquivos em C:

## <a name="cause"></a>Causa

Se um erro de NTFS for encontrado no sistema de arquivos, o Windows irá verificar e reparar a consistência do disco na próxima reinicialização. Geralmente isso acontece se a VM tivesse uma reinicialização inesperada ou se o processo de desligamento de VM foi interrompido abruptamente.

## <a name="solution"></a>Solução 

O Windows será inicializado normalmente depois que o processo de verificação de disco for concluído. Se a VM estiver presa no processo de verificação de disco, tente executar o disco de verificação na VM offline:
1.  Tire um instantâneo do disco do sistema operacional da VM afetada como um backup. Para obter mais informações, consulte [instantâneo de um disco](../windows/snapshot-copy-managed-disk.md).
2.  [Anexar o disco do SO a uma VM de recuperação](troubleshoot-recovery-disks-portal-windows.md).  
3.  Na VM de recuperação, execute verificar disco no disco do sistema operacional anexado. No exemplo a seguir, a letra de driver do disco do sistema operacional anexado é E: 
        
        chkdsk E: /f
4.  Depois que o disco de verificação for concluído, desanexe o disco da VM de recuperação e anexe novamente o disco à VM afetada como um disco do sistema operacional. Para obter mais informações, consulte [solucionar problemas de uma VM do Windows anexando o disco do sistema operacional a uma VM de recuperação](troubleshoot-recovery-disks-portal-windows.md).
