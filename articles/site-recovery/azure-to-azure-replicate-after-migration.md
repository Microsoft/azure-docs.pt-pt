---
title: Criação de recuperação de desastres após migração para Azure com recuperação do site Azure
description: Este artigo descreve como preparar máquinas para configurar a recuperação de desastres entre as regiões de Azure após a migração para Azure usando a Recuperação do Sítio Azure.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: 874c282ff878126297dc46ca0e7a4c19910e40a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74159105"
---
# <a name="set-up-disaster-recovery-for-azure-vms-after-migration-to-azure"></a>Configurar a recuperação após desastre de VMs do Azure após a migração para o Azure 


Siga este artigo se [emigrou máquinas no local para VMs Azure](tutorial-migrate-on-premises-to-azure.md) usando o serviço de [Recuperação](site-recovery-overview.md) do Site, e agora quer que os VMs sejam configurados para a recuperação de desastres para uma região secundária de Azure. O artigo descreve como garantir que o agente Azure VM é instalado em VMs migrados, e como remover o serviço de Mobilidade de Recuperação do Site que já não é necessário após a migração.



## <a name="verify-migration"></a>Verificar a migração

Antes de preparar a recuperação de desastres, certifique-se de que a migração está concluída como esperado. Para completar uma migração com sucesso, após a falha, deve selecionar a opção **Migração Completa,** para cada máquina que pretende migrar. 

## <a name="verify-the-azure-vm-agent"></a>Verifique o agente Azure VM

Cada VM Azure deve ter o [agente Azure VM](../virtual-machines/extensions/agent-windows.md) instalado. Para replicar Os VMs Azure, a Recovery do Local instala uma extensão no agente.

- Se a máquina estiver a funcionar na versão 9.7.0.0 ou posterior do serviço de Mobilidade de Recuperação do Site, o agente Azure VM é automaticamente instalado pelo serviço mobility em VMs windows. Em versões anteriores do serviço mobility, irá instalar o agente manualmente.
- Para os VMs Linux, tem de instalar manualmente o agente Azure VM. Só precisa de instalar o agente Azure VM se o serviço de Mobilidade instalado na máquina migrada for v9.6 ou mais cedo.


### <a name="install-the-agent-on-windows-vms"></a>Instale o agente nos VMs do Windows

Se estiver a executar uma versão do serviço de mobilidade de Recuperação do Site mais cedo do que 9.7.0.0, ou tiver alguma outra necessidade de instalar o agente manualmente, faça o seguinte:  

1. Certifique-se de que tem permissões de administração no VM.
2. Descarregue o [instalador vM Agent](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409).
3. Executar o ficheiro do instalador.

#### <a name="validate-the-installation"></a>Validar a instalação
Para verificar se o agente está instalado:

1. No Azure VM, na pasta C:\WindowsAzure\Packages, deverá ver o ficheiro WaAppAgent.exe.
2. Clique no ficheiro e no **Properties,** selecione o separador **Detalhes.**
3. Verifique se o campo versão do **produto** mostra 2.6.1198.718 ou superior.

[Saiba mais](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) sobre a instalação de agente para windows.

### <a name="install-the-agent-on-linux-vms"></a>Instale o agente em VMs Linux

Instale manualmente o agente [Azure Linux VM:](../virtual-machines/extensions/agent-linux.md)

1. Certifique-se de ter permissões de administração na máquina.
2. Recomendamos vivamente que instale o agente Linux VM utilizando um pacote RPM ou DEB do repositório de pacotes da sua distribuição. Todos os fornecedores de [distribuição endossados](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) integram o pacote de agentes Azure Linux nas suas imagens e repositórios.
    - Recomendamos vivamente que atualize o agente apenas através de um repositório de distribuição.
    - Não recomendamos instalar o agente Linux VM diretamente do GitHub e atualizá-lo.
    -  Se o mais recente agente para a sua distribuição não estiver disponível, contacte o suporte de distribuição para obter instruções sobre como instalá-lo. 

#### <a name="validate-the-installation"></a>Validar a instalação 

1. Execute este comando: **ps-e** para garantir que o agente Azure está a funcionar no Linux VM.
2. Se o processo não estiver em execução, reinicie-o utilizando os seguintes comandos:
    - Para Ubuntu: início do **walinuxagent de serviço**
    - Para outras distribuições: **início do waagent de serviço**


## <a name="uninstall-the-mobility-service"></a>Desinstalar o serviço de Mobilidade

1. Desinstale manualmente o serviço de Mobilidade a partir do Azure VM, utilizando um dos seguintes métodos. 
    - Para windows, no Painel de Controlo > **Programas de Adição/Remoção,** desinstale o Serviço de Mobilidade de Recuperação do **Site Microsoft Azure/servidor Master Target**. A uma altura elevada de comando, corra:
        ```
        MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
        ```
    - Para linux, inscreva-se como um utilizador de raiz. Num terminal, dirija-se a **/utilizador/local/ASR,** e execute o seguinte comando:
        ```
        ./uninstall.sh -Y
        ```
2. Reinicie o VM antes de configurar a replicação.

## <a name="next-steps"></a>Passos seguintes

[Reveja](site-recovery-extension-troubleshoot.md) a resolução de problemas para a extensão de recuperação do site no agente Azure VM.
[Reproduza rapidamente](azure-to-azure-quickstart.md) um VM Azure para uma região secundária.
