---
title: Fazer cópias de segurança de VMs do Azure num cofre dos serviços de recuperação com o Azure Backup
description: Descreve como fazer cópias de segurança de VMs do Azure num cofre dos serviços de recuperação com o Azure Backup
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: raynew
ms.openlocfilehash: c6d6e380cded18a089f624f90d998477a89293be
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58259046"
---
# <a name="back-up-azure-vms-in-a-recovery-services-vault"></a>Fazer cópias de segurança de VMs do Azure num cofre dos serviços de recuperação

Este artigo descreve como fazer cópias de segurança para a VM do Azure com um [cópia de segurança do Azure](backup-overview.md) ao implantar e ativar a cópia de segurança num cofre dos serviços de recuperação.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Certifique-se a cenários suportados e os pré-requisitos.
> * Prepare as VMs do Azure. Instalar o agente de VM do Azure se for necessário e verifique se o acesso de saída para as VMs.
> * Crie um cofre.
> * Configurar o armazenamento para o Cofre
> * Detetar VMs, configurar definições de cópia de segurança e a política.
> * Ativar cópia de segurança para VMs do Azure


> [!NOTE]
   > Este artigo descreve como fazer cópias de segurança de VMs do Azure ao configurar um cofre e selecionar VMs para criar cópias de segurança. É útil se quiser fazer uma cópia de segurança de várias VMs. Também pode [cópia de segurança uma VM do Azure](backup-azure-vms-first-look-arm.md) diretamente a partir das definições de VM.

## <a name="before-you-start"></a>Antes de começar

O Azure Backup cria cópias de segurança de VMs do Azure ao instalar uma extensão para o agente de VM do Azure em execução na máquina.

1. [Revisão](backup-architecture.md#architecture-direct-backup-of-azure-vms) arquitetura de cópia de segurança de VM do Azure.
[Saiba mais sobre](backup-azure-vms-introduction.md) cópia de segurança de VM do Azure e a extensão de cópia de segurança.
2. [Reveja a matriz de suporte](backup-support-matrix-iaas.md) para cópia de segurança de VM do Azure.
3. Prepare as VMs do Azure. Instalar o agente da VM, se não estiver instalado e verifique se o acesso de saída para VMs que pretende criar cópias de segurança.


## <a name="prepare-azure-vms"></a>Preparar as VMs do Azure

Instale o agente VM, se necessário e verificar o acesso de saída das VMs.

### <a name="install-the-vm-agent"></a>Instalar o agente da VM
Se for necessário, instale o agente da seguinte forma.

**VM** | **Detalhes**
--- | ---
**VMs do Windows** | [Transferir e instalar](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) o ficheiro MSI do agente. Instale com permissões de administrador na máquina.<br/><br/> Para verificar a instalação, na *C:\WindowsAzure\Packages* na VM, com o botão direito do WaAppAgent.exe > **propriedades**, > **detalhes** separador. **Versão do produto** deve ser 2.6.1198.718 ou superior.<br/><br/> Se estiver a atualizar o agente, certifique-se de que não existem operações de cópia de segurança estão em execução, e [reinstale o agente](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409).
**VMs do Linux** | A instalação a utilizar um RPM ou um pacote DEB no repositório de pacotes de sua distribuição é o método preferencial de instalação e atualização do agente Linux do Azure. Todas as a [apoiadas pelo fornecedores de distribuição](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) integrar o pacote do agente Linux do Azure em suas imagens e repositórios. O agente está disponível no [GitHub](https://github.com/Azure/WALinuxAgent), mas não é recomendada a instalação a partir daí.<br/><br/> Se estiver a atualizar o agente, certifique-se de que nenhuma operação de cópia de segurança está em execução e os binários de atualização.


### <a name="establish-network-connectivity"></a>Estabelecer conectividade de rede

A extensão de cópia de segurança em execução na VM tem de ter acesso de saída para os endereços IP públicos do Azure.

- Sem acesso de rede de saída explícita é necessário para VM do Azure para comunicar com o serviço de cópia de segurança do Azure.
- No entanto, determinadas mais antigas máquinas de virtuais pode sentir alguns problemas e falhar com o erro **ExtensionSnapshotFailedNoNetwork** quando tentam se conectar. Neste caso, utilize uma das seguintes opções para que a extensão de cópia de segurança pode comunicar com endereços IP públicos do Azure para o tráfego de cópia de segurança.

   **Opção** | **Ação** | **Vantagens** | **Desvantagens**
   --- | --- | --- | ---
   **Configurar regras NSG** | Permitir a [intervalos IP do datacenter do Azure](https://www.microsoft.com/download/details.aspx?id=41653).<br/><br/>  Pode adicionar uma regra que permite o acesso para o serviço de cópia de segurança do Azure com um [etiqueta de serviço](backup-azure-arm-vms-prepare.md#set-up-an-nsg-rule-to-allow-outbound-access-to-azure), em vez de individualmente, permitindo e gerir cada intervalo de endereços. [Saiba mais](../virtual-network/security-overview.md#service-tags) sobre as etiquetas de serviço. | Sem custos adicionais. Simples de gerenciar com etiquetas de serviço
   **Implementar um proxy** | Implemente um servidor de proxy HTTP para o encaminhamento de tráfego. | Fornece acesso a todo do Azure e não apenas armazenamento. É permitido um controle granular sobre os URLs de armazenamento.<br/><br/> Único ponto de acesso à internet para VMs.<br/><br/> Custos adicionais para o proxy.<br/><br/>
   **Configurar a Firewall do Azure** | Permitir o tráfego através da Firewall do Azure na VM, com uma etiqueta do FQDN para o serviço de cópia de segurança do Azure.|  Fácil de utilizar se tiver o Firewall do Azure numa sub-rede de VNet | Não é possível criar suas próprias etiquetas FQDN ou modificar os FQDNs numa marca.<br/><br/> Se utilizar Managed Disks do Azure, poderá ter de abertura de portas adicionais (porta 8443) nas firewalls.

#### <a name="set-up-an-nsg-rule-to-allow-outbound-access-to-azure"></a>Configurar uma regra NSG para permitir o acesso de saída para o Azure

Se a sua VM do Azure tem acesso gerido por um NSG, permita o acesso de saída para o armazenamento de cópia de segurança para as portas e intervalos necessários.

1. Na VM > **Networking**, clique em **Adicionar regra de saída através da porta**.
2. Na **Adicionar regra de segurança de saída**, clique em **avançadas**.
3. Na **origem**, selecione **VirtualNetwork**.
4. Na **intervalos de portas de origem**, escreva um asterisco (*) para permitir o acesso de saída de qualquer porta.
5. Na **destino**, selecione **etiqueta de serviço**. Na lista, selecione **Storage.region**. A região é a região em que o Cofre e as VMs que pretende criar cópias de segurança, estão localizadas.
6. Na **intervalos de portas de destino**, selecione a porta.
    - VM não gerida com a conta de armazenamento não encriptada: 80
    - VM não gerida com a conta de armazenamento encriptado: 443 (predefinição)
    - VM gerida: 8443.
7. Na **protocolo**, selecione **TCP**.
8. Na **prioridade**, especifique um valor de prioridade inferior a superior quaisquer negar regras. Se tiver uma regra negar o acesso, o novo permite a regra tem de ser superior. Por exemplo, se tem um **Deny_All** conjunto com prioridade 1000, sua nova regra de regras devem ser definida para menos de 1000.
9. Forneça um nome e descrição para a regra e clique em **OK**.

Pode aplicar a regra NSG para várias VMs para permitir o acesso de saída.

Este vídeo o orienta pelo processo.

>[!VIDEO https://www.youtube.com/embed/1EjLQtbKm1M]


#### <a name="route-backup-traffic-through-a-proxy"></a>Encaminhar o tráfego de cópia de segurança através de um proxy

Pode encaminhar o tráfego de cópia de segurança através de um proxy e, em seguida, dar o acesso de proxy para os intervalos do Azure necessários.
Deve configurar o proxy VM para permitir que o seguinte:

- A VM do Azure deve encaminhar todo o tráfego HTTP vinculado para a internet pública através do proxy.
- O proxy deve permitir tráfego de entrada de VMs na rede virtual (VNet) aplicável.
- O NSG **NSF bloqueio** necessita de uma regra que permita o tráfego de internet de saída do proxy VM.

##### <a name="set-up-the-proxy"></a>Configurar o proxy
Se não tiver um proxy de conta de sistema, configure um da seguinte forma:

1. Baixe [PsExec](https://technet.microsoft.com/sysinternals/bb897553).

2. Execute **PsExec.exe -i -s cmd.exe** para executar a linha de comandos com uma conta do sistema.
3. Execute o navegador no contexto do sistema. Por exemplo: **%PROGRAMFILES%\Internet Explorer\iexplore.exe** para o Internet Explorer.  
4. Defina as definições de proxy.
   - Nas máquinas do Linux:
     - Adicionar esta linha para o **/etc/ambiente** ficheiro:
       - **http_proxy=http:\//proxy IP address:proxy port**
     - Adicionar estas linhas para o **/etc/waagent.Conf.** ficheiro:
         - **HttpProxy.Host=proxy IP address**
         - **HttpProxy.Port=proxy port**
   - Nas máquinas do Windows, nas configurações do navegador, especifica que um proxy a utilizar. Se estiver atualmente a utilizar um proxy numa conta de utilizador, pode utilizar este script para aplicar a definição ao nível da conta de sistema.
       ```powershell
      $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections"
      Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name DefaultConnectionSettings -Value $obj.DefaultConnectionSettings
      Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name SavedLegacySettings -Value $obj.SavedLegacySettings
      $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings"
      Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name ProxyEnable -Value $obj.ProxyEnable
      Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name Proxyserver -Value $obj.Proxyserver

       ```

##### <a name="allow-incoming-connections-on-the-proxy"></a>Permitir ligações de entrada no proxy

Permita ligações de entrada nas definições de proxy.

- Por exemplo, abra **Firewall do Windows com segurança avançada**.
    - Com o botão direito **regras de entrada** > **nova regra**.
    - Na **tipo de regra** selecionar **personalizada** > **seguinte**.
    - Na **programa**, selecione **todos os programas** > **seguinte**.
    - Na **protocolos e portas** definir o tipo como **TCP**, **portas Local** para **portas específicas**, e **porta remota**para **todas as portas**.
    - Concluir o assistente e especificar um nome para a regra.

##### <a name="add-an-exception-rule-to-the-nsg-for-the-proxy"></a>Adicionar uma regra de exceção para o NSG para o proxy

No NSG **NSF bloqueio**, permitir tráfego a partir de qualquer porta no 10.0.0.5 para qualquer endereço internet na porta 80 (HTTP) ou 443 (HTTPS).

- O seguinte script do PowerShell fornece um exemplo para permitir o tráfego.
- Em vez de permitir saída para todos os endereços de internet pública, pode especificar um intervalo de endereços IP (-DestinationPortRange), ou usar a marca de serviço storage.region.   

    ```powershell
    Get-AzureNetworkSecurityGroup -Name "NSG-lockdown" |
    Set-AzureNetworkSecurityRule -Name "allow-proxy " -Action Allow -Protocol TCP -Type Outbound -Priority 200 -SourceAddressPrefix "10.0.0.5/32" -SourcePortRange "*" -DestinationAddressPrefix Internet -DestinationPortRange "80-443"
    ```

### <a name="allow-firewall-access-with-fqdn-tag"></a>Permitir o acesso de firewall com a etiqueta do FQDN

Pode configurar a Firewall do Azure para permitir o acesso de saída para o tráfego de rede para o Azure Backup.

- [Saiba mais sobre](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal) implantar o Firewall do Azure.
- [Leia sobre](https://docs.microsoft.com/azure/firewall/fqdn-tags) FQDN etiquetas.

## <a name="set-up-storage-replication"></a>Configurar a replicação de armazenamento

Por predefinição, o seu Cofre tem [armazenamento georredundante (GRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs). Recomendamos GRS para a cópia de segurança primária, mas pode usar[armazenamento localmente redundante](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) para uma opção mais barata.

Armazenamento para o Cofre lida automaticamente com o Azure Backup. Tem de especificar a forma como os que o armazenamento é replicado.
Modificar os replicação de armazenamento da seguinte forma:

1. No painel **Cofres dos Serviços de Recuperação**, clique em novo cofre. Sob o **configurações** secção, clique em **propriedades**.
2. Na **propriedades**, em **configuração de cópia de segurança**, clique em **atualização**.

3. Selecione o tipo de replicação de armazenamento e clique em **guardar**.

      ![Definir a configuração de armazenamento do novo cofre](./media/backup-try-azure-backup-in-10-mins/full-blade.png)


## <a name="configure-a-backup-policy"></a>Configurar uma política de cópia de segurança

Detetar VMs na subscrição e configurar a cópia de segurança.

1. No cofre > **descrição geral**, clique em **+ cópia de segurança**

   ![Botão de cópia de segurança](./media/backup-azure-arm-vms-prepare/backup-button.png)

   O **cópia de segurança** e **objetivo de cópia de segurança** painéis abrir.

2. Na **objetivo de cópia de segurança**> **em que a sua carga de trabalho é executado?**, selecione **Azure**. Na **o que fazer quiser a cópia de segurança?**, selecione **Máquina Virtual** >  **OK**. A extensão de VM é registada no cofre.

   ![Painéis de cópia de segurança e objetivo de cópia de segurança](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

   Este passo registra a extensão de VM com o cofre. O **objetivo de cópia de segurança** fecha o painel e o **política de cópia de segurança** painel abre-se.

3. Na **política de cópia de segurança**, selecione a política que pretende associar ao cofre. Em seguida, clique em **OK**.
    - Os detalhes da política predefinida estão listados no menu pendente.
    - Clique em **criar novo** para criar uma política. [Saiba mais](backup-azure-arm-vms-prepare.md#configure-a-backup-policy) sobre como definir uma política.

      !["Cópia de segurança" e "Política de cópia de segurança" painéis](./media/backup-azure-arm-vms-prepare/select-backup-goal-2.png)

4. Na **selecionar máquinas virtuais** painel, selecione as VMs que irão utilizar a política de cópia de segurança especificada > **OK**.

   - A VM selecionada é validada.
   - Pode selecionar apenas as VMs na mesma região que o cofre. As VMs só podem ser copiadas num único cofre.

     ![Painel "Selecionar máquinas virtuais"](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

5. Na **cópia de segurança**, selecione **ativar cópia de segurança**.

   - Isto implementa a política para o Cofre e para as VMs e instala a extensão de cópia de segurança no agente VM em execução na VM do Azure.
   - Este passo não cria o ponto de recuperação inicial para a VM.

     ![Botão "Ativar cópia de segurança"](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

Depois de ativar a cópia de segurança:

- Uma cópia de segurança inicial é executada de acordo com sua agenda de cópia de segurança.
- O serviço de cópia de segurança instala a extensão de cópia de segurança, independentemente da VM está em execução.
    - Uma VM em execução fornece a maior possibilidade de obter um ponto de recuperação consistente com aplicações.
    -  No entanto, a VM é uma cópia de segurança, mesmo que ele é desativado e não é possível instalar a extensão. Isso é conhecido como *offline VM*. Neste caso, o ponto de recuperação será *consistente com a falha*.
    Tenha em atenção que o Azure Backup não suporta ajuste automático de relógio para alterações de economia de hora de Verão para cópias de segurança de VM do Azure. Modificar as políticas de cópia de segurança manualmente conforme necessário.

## <a name="run-the-initial-backup"></a>Executar a cópia de segurança inicial

A cópia de segurança inicial será executado em conformidade com o agendamento, a menos que manualmente o executa imediatamente. Executá-la manualmente da seguinte forma:

1. No menu do cofre, clique em **itens de cópia de segurança**.
2. Na **itens de cópia de segurança** clique em **Máquina Virtual do Azure**.
3. Na **itens de cópia de segurança** lista, clique nas reticências **...** .
4. Clique em **agora a cópia de segurança**.
5. Na **cópia de segurança agora**, usar o controle de calendário para selecionar o último dia em que o ponto de recuperação deve ser mantido > **OK**.
6. Monitorize as notificações de portais. Pode monitorizar o progresso da tarefa no dashboard do cofre > **tarefas de cópia de segurança** > **em curso**. Dependendo do tamanho da sua VM, a criação da cópia de segurança inicial poderá demorar algum tempo.



## <a name="next-steps"></a>Passos Seguintes

- Resolver quaisquer problemas que ocorrem com o [agentes de VM do Azure](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md) ou [cópia de segurança de VM do Azure](backup-azure-vms-troubleshoot.md).
- [Fazer cópia de segurança de VMs do Azure](backup-azure-vms-first-look-arm.md)
