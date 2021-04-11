---
title: Confiúdo de recuperação de desastres após migração para Azure com recuperação do local de Azure
description: Este artigo descreve como preparar máquinas para estabelecer a recuperação de desastres entre as regiões de Azure após a migração para Azure usando a Recuperação do Local de Azure.
services: site-recovery
ms.service: site-recovery
ms.topic: article
ms.date: 11/14/2019
ms.openlocfilehash: 41fa4f5d042b5c284a58b0fd87486e2309b03053
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "106581732"
---
# <a name="set-up-disaster-recovery-for-azure-vms-after-migration-to-azure"></a>Configurar a recuperação após desastre de VMs do Azure após a migração para o Azure 


Siga este artigo se [você emigrou as máquinas no local para Azure VMs](./migrate-tutorial-on-premises-azure.md) usando o serviço de Recuperação de [Locais,](site-recovery-overview.md) e agora você quer obter os VMs configurados para recuperação de desastres para uma região secundária de Azure. O artigo descreve como garantir que o agente Azure VM é instalado em VMs migrados, e como remover o serviço de Mobilidade de Recuperação do Local que já não é necessário após a migração.



## <a name="verify-migration"></a>Verificar migração

Antes de preparar a recuperação de desastres, certifique-se de que a migração está concluída como esperado. Para completar uma migração com sucesso, após o failover, deve selecionar a opção **Migração Completa,** para cada máquina que pretende migrar. 

## <a name="verify-the-azure-vm-agent"></a>Verifique o agente Azure VM

Cada Azure VM deve ter o [agente Azure VM](../virtual-machines/extensions/agent-windows.md) instalado. Para replicar VMs Azure, a Recuperação do Local instala uma extensão no agente.

- Se a máquina estiver a executar a versão 9.7.0.0 ou posterior do serviço de Mobilidade de Recuperação do Local, o agente Azure VM é automaticamente instalado pelo serviço mobility em VMs windows. Nas versões anteriores do serviço Mobility, instalará o agente manualmente.
- Para os VMs Linux, deve instalar manualmente o agente Azure VM. Só é necessário instalar o agente Azure VM se o serviço de Mobilidade instalado na máquina migratória for v9.6 ou mais cedo.


### <a name="install-the-agent-on-windows-vms"></a>Instale o agente em VMs do Windows

Se estiver a executar uma versão do serviço de mobilidade de Recuperação do Site antes de 9.7.0.0, ou tiver alguma outra necessidade de instalar o agente manualmente, faça o seguinte:  

1. Certifique-se de que tem permissões de administração no VM.
2. Descarregue o [instalador do Agente VM](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409).
3. Executar o ficheiro do instalador.

#### <a name="validate-the-installation"></a>Validar a instalação
Para verificar se o agente está instalado:

1. No Azure VM, na pasta C:\WindowsAzure\Packages, deverá ver o ficheiro WaAppAgent.exe.
2. Clique com o direito no ficheiro e, em **Propriedades,** selecione o separador **Detalhes.**
3. Verifique se o campo **versão do produto** apresenta 2.6.1198.718 ou superior.

[Saiba mais](../virtual-machines/extensions/agent-windows.md) sobre a instalação do agente para windows.

### <a name="install-the-agent-on-linux-vms"></a>Instale o agente em VMs Linux

Instale manualmente o agente [Azure Linux VM](../virtual-machines/extensions/agent-linux.md) da seguinte forma:

1. Certifique-se de que tem permissões de administração na máquina.
2. Recomendamos vivamente que instale o agente Linux VM utilizando um RPM ou um pacote DEB do repositório de pacotes da sua distribuição. Todos os [fornecedores de distribuição endossados](../virtual-machines/linux/endorsed-distros.md) integram o pacote de agente Azure Linux nas suas imagens e repositórios.
    - Recomendamos vivamente que atualize o agente apenas através de um repositório de distribuição.
    - Não recomendamos instalar o agente Linux VM diretamente do GitHub e atualizá-lo.
    -  Se o último agente para a sua distribuição não estiver disponível, contacte o suporte de distribuição para obter instruções sobre como instalá-lo. 

#### <a name="validate-the-installation"></a>Validar a instalação 

1. Executar este comando: **ps-e** para garantir que o agente Azure está em execução no Linux VM.
2. Se o processo não estiver em curso, reinicie-o utilizando os seguintes comandos:
    - Para Ubuntu: **início de serviço walinuxagent**
    - Para outras distribuições: **início waagent de serviço**


## <a name="uninstall-the-mobility-service"></a>Desinstalar o serviço de Mobilidade

1. Desinstalar manualmente o serviço de Mobilidade a partir do Azure VM, utilizando um dos seguintes métodos. 
    - Para o Windows, no Painel de Controlo > **adicionar/remover programas**, desinstale **o Serviço de Mobilidade de Recuperação do Site microsoft Azure/servidor De destino Principal**. Com um pedido de comando elevado, corra:
        ```
        MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
        ```
    - Para o Linux, inscreva-se como um utilizador de raiz. Num terminal, vá a **/utilizador/local/ASR,** e execute o seguinte comando:
        ```
        ./uninstall.sh -Y
        ```
2. Reinicie o VM antes de configurar a replicação.

## <a name="next-steps"></a>Passos seguintes

[Reveja a resolução de problemas](site-recovery-extension-troubleshoot.md) para a extensão de recuperação do local no agente Azure VM.
[Replique rapidamente](azure-to-azure-quickstart.md) um Azure VM para uma região secundária.
