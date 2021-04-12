---
title: Prepare servidores Windows Server 2003 para migração com a Azure Migrate
description: Saiba como preparar servidores windows server 2003 para migração com a Azure Migrate.
author: rahulg1190
ms.author: rahugup
ms.manager: bsiva
ms.topic: how-to
ms.date: 05/27/2020
ms.openlocfilehash: f8d3dea970d07d951467a44661e12000ba413f72
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96753757"
---
# <a name="prepare-windows-server-2003-machines-for-migration"></a>Preparar máquinas windows server 2003 para migração

Este artigo descreve como preparar máquinas que executam o Windows Server 2003 para migração para Azure. 


> [!NOTE]
> [O suporte alargado do Windows Server 2003](/troubleshoot/azure/virtual-machines/run-win-server-2003#microsoft-windows-server-2003-end-of-support) terminou em 14 de julho de 2015.  A equipa de suporte do Azure continua a ajudar na resolução de problemas que dizem respeito a executar o Windows Server 2003 no Azure. No entanto, este suporte está limitado a questões que não requerem resolução de problemas ou patches ao nível do SISTEMA. Migrar as suas aplicações para instâncias Azure que executam uma versão mais recente do Windows Server é a abordagem recomendada para garantir que está a aproveitar eficazmente a flexibilidade e fiabilidade da nuvem Azure. No entanto, se ainda optar por migrar o Seu Windows Server 2003 para Azure, pode utilizar a ferramenta Azure Migrate: Server Migration se o seu Servidor do Windows for um VM a funcionar em VMware ou Hyper-V.


- Você pode usar migração sem agente para migrar [VMs Hiper-V](tutorial-migrate-hyper-v.md) e [VMware VMs](tutorial-migrate-vmware.md) para Azure.
- Para se ligar aos VMs Azure após a migração, os Serviços de Integração Hiper-V devem ser instalados no VM Azure. As máquinas Windows Server 2003 não têm isto instalado por predefinição.
- Não existe um link de descarregamento direto para instalar serviços de integração de Hiper-V, por isso tem de fazer o seguinte:
    - Para os VMs Hiper-V que não o tenham instalado, extrai ficheiros de instalação para serviços de integração numa máquina que executa o Windows Server 2012 R2/Windows Server 2012 com a função Hyper-V e, em seguida, copia o instalador para a máquina do Windows Server 2003. Os ficheiros de instalação não estão disponíveis em máquinas que executam o Windows Server 2016.
    - Para VMware VMs, cria uma tarefa de arranque que instala Serviços de Integração quando o VM Azure começa após a migração.


## <a name="install-on-hyper-v-vms"></a>Instalação em VMs Hiper-V

Antes da migração, verifique se os Serviços de Integração Hiper-V e, em seguida, instale se necessário.

1. Siga [estas instruções](/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#turn-an-integration-service-on-or-off-using-hyper-v-manager) para verificar se está instalado.
2. Se não estiver instalado, inscreva-se numa máquina que executa o Windows Server 2012 R2/Windows Server 2012 com a função Hyper-V.
3. Navegue para o ficheiro de instalação em **C:\Windows\System32\vmguest.iso**, e monte o ficheiro.
2. Copie a pasta de instalação para a máquina do Windows Server 2003 e instale serviços de integração.
4. Após a instalação, pode deixar as definições predefinidos nos Serviços de Integração. 

## <a name="install-on-vmware-vms"></a>Instalar em VMware VMs

1. Inscreva-se numa máquina que executa o Windows Server 2012 R2/Windows Server 2012 com a função Hyper-V.
2. Navegue para o ficheiro de instalação em **C:\Windows\System32\vmguest.iso**, e monte o ficheiro.
3. Copie a pasta de instalação para o VMware VM.
4. A partir da linha de comando na VM, ```gpedit.msc``` corra.
5. Abrir **configurações de configuração** do computador  >    >  **Scripts Scripts (Iniciar/Desligar)**.
6. No Nome do Script **De Adicionar a**  >    >  Startup, digite o endereço setup.exe.
7. Após a migração para Azure, o script corre a primeira vez que o Azure VM começa.
8. Reinicie manualmente o Azure VM. Há um pop-up nos diagnósticos de botas que indica que é necessário um recomeço.
9. Depois de o script ser executado e os Serviços de Integração Hiper-V estiverem instalados no Azure VM, pode remover o script do arranque.
10. Após a instalação, pode deixar as definições predefinidos nos Serviços de Integração. 

## <a name="next-steps"></a>Passos seguintes

- Reveja os requisitos de migração para [VMware](migrate-support-matrix-vmware-migration.md) e [Hiper-VMs.](migrate-support-matrix-hyper-v-migration.md)
- Migrar [VMware](server-migrate-overview.md) e [Hiper-VMs.](tutorial-migrate-hyper-v.md)
