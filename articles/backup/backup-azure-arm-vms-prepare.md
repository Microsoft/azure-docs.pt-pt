---
title: Fazer cópias de segurança de VMs do Azure num cofre dos serviços de recuperação utilizando o Azure Backup
description: Descreve como fazer cópias de segurança de VMs do Azure num cofre dos serviços de recuperação utilizando a cópia de segurança do Azure
service: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: raynew
ms.openlocfilehash: bac61342f39821b6181a6a0e61bf0b11fb311007
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66239313"
---
# <a name="back-up-azure-vms-in-a-recovery-services-vault"></a>Fazer cópias de segurança de VMs do Azure num cofre dos serviços de recuperação

Este artigo descreve como fazer cópias de segurança de VMs do Azure num cofre dos serviços de recuperação, utilizando o [Azure Backup](backup-overview.md) serviço.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Prepare as VMs do Azure.
> * Crie um cofre.
> * Detetar VMs e configurar uma política de cópia de segurança.
> * Ative cópia de segurança para VMs do Azure.
> * Execute a cópia de segurança inicial.


> [!NOTE]
> Este artigo descreve como configurar um cofre e selecione as VMs para criar cópias de segurança. É útil se quiser fazer uma cópia de segurança de várias VMs. Em alternativa, pode [cópia de segurança uma única VM do Azure](backup-azure-vms-first-look-arm.md) diretamente a partir das definições de VM.

## <a name="before-you-start"></a>Antes de começar


- [Revisão](backup-architecture.md#architecture-direct-backup-of-azure-vms) a arquitetura de cópia de segurança de VM do Azure.
- [Saiba mais sobre](backup-azure-vms-introduction.md) cópia de segurança de VM do Azure e a extensão de cópia de segurança.
- [Reveja a matriz de suporte](backup-support-matrix-iaas.md) antes de configurar a cópia de segurança.

Além disso, existem algumas coisas que poderá ter de fazer em algumas circunstâncias:

- **Instalar o agente da VM na VM**: O Azure Backup cria cópias de segurança de VMs do Azure ao instalar uma extensão para o agente de VM do Azure em execução na máquina. Se a VM foi criada a partir de uma imagem do Azure marketplace, o agente está instalado e em execução. Se criar uma VM personalizada ou migrar uma máquina no local, talvez seja preciso [instalar manualmente o agente](#install-the-vm-agent).
- **Permitir explicitamente o acesso de saída**: Em geral, não precisa de permitir explicitamente o acesso de rede de saída para uma VM do Azure para que ele comunicar com o Azure Backup. No entanto, algumas VMs podem ocorrer problemas de ligação, que mostra a **ExtensionSnapshotFailedNoNetwork** Ocorreu um erro ao tentar ligar. Se isto acontecer, deve [permitir explicitamente o acesso de saída](#explicitly-allow-outbound-access), por isso, a extensão de cópia de segurança do Azure pode comunicar com endereços IP públicos do Azure para o tráfego de cópia de segurança.


## <a name="create-a-vault"></a>Criar um cofre

 Um cofre armazena cópias de segurança e pontos de recuperação criados ao longo do tempo e armazena as diretivas de cópia de segurança associadas a máquinas de cópia de segurança. Crie um cofre da seguinte forma:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Na pesquisa, escreva **serviços de recuperação**. Sob **serviços**, clique em **cofres dos serviços de recuperação**.

     ![Procure os cofres dos serviços de recuperação](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png) <br/>

3. Na **cofres dos serviços de recuperação** menu, clique em **+ adicionar**.

     ![Passo 2 da Criação do Cofre dos Serviços de Recuperação](./media/backup-azure-arm-vms-prepare/rs-vault-menu.png)

4. Na **cofre dos serviços de recuperação**, escreva um nome amigável para identificar o cofre.
    - O nome tem de ser exclusivo para a subscrição do Azure.
    - Pode conter 2 e 50 carateres.
    - Ele tem de começar com uma letra e pode conter apenas letras, números e hífenes.
5. Selecione a subscrição do Azure, o grupo de recursos e a região geográfica na qual deve ser criado no cofre. Em seguida, clique em **Criar**.
    - Pode demorar algum tempo para o cofre a ser criada.
    - Monitorize as notificações de estado na área de canto superior direito do portal.


 Depois do cofre for criado, ele aparece na lista de cofres dos serviços de recuperação. Se não vir o cofre, selecione **atualizar**.

![Lista de cofres de cópia de segurança](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

### <a name="modify-storage-replication"></a>Modificar a replicação de armazenamento

Por predefinição, cofres de utilização [armazenamento georredundante (GRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs).

- Se o Cofre é o mecanismo de cópia de segurança principal, recomendamos que utilize o GRS.
- Pode usar [armazenamento localmente redundante (LRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) para uma opção mais barata.

Modificar o tipo de replicação de armazenamento da seguinte forma:

1. No novo cofre, clique em **propriedades** no **definições** secção.
2. Na **propriedades**, em **configuração de cópia de segurança**, clique em **atualização**.
3. Selecione o tipo de replicação de armazenamento e clique em **guardar**.

      ![Definir a configuração de armazenamento do novo cofre](./media/backup-try-azure-backup-in-10-mins/full-blade.png)
> [!NOTE]
   > Não é possível modificar o tipo de replicação de armazenamento depois do Cofre está configurado e contém itens de cópia de segurança. Se quiser fazer isso precisa de recriar o cofre.

## <a name="apply-a-backup-policy"></a>Aplicar uma política de cópia de segurança

Configure uma política de cópia de segurança para o cofre.

1. No cofre, clique em **+ cópia de segurança** no **descrição geral** secção.

   ![Botão de cópia de segurança](./media/backup-azure-arm-vms-prepare/backup-button.png)


2. Na **objetivo de cópia de segurança** > **em que a sua carga de trabalho é executado?** selecione **Azure**. Na **o que fazer quiser a cópia de segurança?** selecionar **Máquina Virtual** >  **OK**. A extensão de VM é registada no cofre.

   ![Painéis de cópia de segurança e objetivo de cópia de segurança](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

3. Na **política de cópia de segurança**, selecione a política que pretende associar ao cofre.
    - A política predefinida cria uma cópia de segurança da VM uma vez por dia. As cópias de segurança diárias são retidas durante 30 dias. Instantâneos de recuperação instantânea são retidos durante dois dias.
    - Se não pretender utilizar a política predefinida, selecione **criar novo**e criar uma política personalizada, conforme descrito no procedimento seguinte.

      ![Política de cópia de segurança predefinida](./media/backup-azure-arm-vms-prepare/default-policy.png)

4. Na **selecionar máquinas virtuais**, selecione as VMs que pretende criar cópias de segurança através da política. Em seguida, clique em **OK**.

   - As VMs selecionadas são validadas.
   - Pode selecionar apenas as VMs na mesma região que o cofre.
   - As VMs só podem ser copiadas num único cofre.

     ![Painel "Selecionar máquinas virtuais"](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

5. Na **cópia de segurança**, clique em **ativar cópia de segurança**. Isto implementa a política para o Cofre e para as VMs e instala a extensão de cópia de segurança no agente VM em execução na VM do Azure.

     ![Botão "Ativar cópia de segurança"](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

Depois de ativar a cópia de segurança:

- O serviço de cópia de segurança instala a extensão de cópia de segurança, independentemente da VM está em execução.
- Uma cópia de segurança inicial será executado em conformidade com a sua agenda de cópia de segurança.
- Quando executam cópias de segurança, tenha em atenção que:
    - Uma VM que está em execução tem a maior possibilidade para capturar um ponto de recuperação consistentes com aplicações.
    - No entanto, mesmo que a VM está desativada-cópia de segurança. Tal uma VM é conhecida como uma VM offline. Neste caso, o ponto de recuperação será consistentes de falhas.


### <a name="create-a-custom-policy"></a>Criar uma política personalizada

Se tiver selecionado para criar uma nova política de cópia de segurança, preencha as definições de política.

1. Na **nome da política**, especifique um nome significativo.
2. Na **agenda de cópia de segurança** Especifique quando devem ser levadas a cópias de segurança. Pode efetuar cópias de segurança diárias ou semanais para VMs do Azure.
2. Na **restaurar instantâneas**, especifique o período de tempo que pretenda manter instantâneos localmente para o restauro imediato.
    - Ao restaurar, cópia de segurança de VM discos são copiados a partir do armazenamento, através da rede para a localização de armazenamento de recuperação. Com o restauro imediato, pode aproveitar instantâneos armazenados localmente tomados durante uma tarefa de cópia de segurança, sem aguardar a dados de cópia de segurança a serem transferidos para o cofre.
    - Pode manter os instantâneos para o restauro imediato para entre uma a cinco dias. Dois dias é a predefinição.
3. Na **período de retenção**, especifique o período de tempo que pretende manter os pontos de cópia de segurança diários ou semanais.
4. Na **retenção do ponto de cópia de segurança mensal**, especifique se pretende manter um mensal cópias de segurança das suas cópias de segurança diárias ou semanais.
5. Clique em **OK** para guardar a política.

    ![Nova política de cópia de segurança](./media/backup-azure-arm-vms-prepare/new-policy.png)

> [!NOTE]
   > Cópia de segurança do Azure não suporta o ajuste automático de relógio de alterações de economia de hora de Verão para cópias de segurança de VM do Azure. À medida que ocorrem alterações de tempo, modificar as políticas de cópia de segurança manualmente conforme necessário.

## <a name="trigger-the-initial-backup"></a>Acionar a cópia de segurança inicial

A cópia de segurança inicial será executado em conformidade com o agendamento, mas pode executá-lo imediatamente, da seguinte forma:

1. No menu do cofre, clique em **itens de cópia de segurança**.
2. Na **itens de cópia de segurança** clique em **Máquina Virtual do Azure**.
3. Na **itens de cópia de segurança** lista, clique nas reticências (...).
4. Clique em **agora a cópia de segurança**.
5. Na **cópia de segurança agora**, usar o controle de calendário para selecionar o último dia em que o ponto de recuperação deve ser mantido. Em seguida, clique em **OK**.
6. Monitorize as notificações de portais. Pode monitorizar o progresso da tarefa no dashboard do cofre > **tarefas de cópia de segurança** > **em curso**. Dependendo do tamanho da sua VM, a criação da cópia de segurança inicial poderá demorar algum tempo.

## <a name="verify-backup-job-status"></a>Verificar o estado da tarefa de cópia de segurança

A tarefa de cópia de segurança de detalhes para cada cópia de segurança VM é composta por 2 fases, o **instantâneo** seguido de fase a **transferir dados para o Cofre** fase.<br/>
A fase de instantâneo garante a disponibilidade de um ponto de recuperação armazenado juntamente com os discos para **restaura instantâneas** e estão disponíveis para um máximo de 5 dias, consoante o período de retenção de instantâneo configurada pelo utilizador. Transferência de dados para o Cofre cria um ponto de recuperação no cofre para retenção a longo prazo. Transferência de dados para o Cofre só começa depois de concluída a fase de instantâneo.

  ![Estado da tarefa de cópia de segurança](./media/backup-azure-arm-vms-prepare/backup-job-status.png)

Existem duas **subtarefas** em execução no back-end, um para a tarefa de cópia de segurança de front-end que pode ser verificada a partir do **tarefa de cópia de segurança** painel de detalhes como indicado abaixo:

  ![Estado da tarefa de cópia de segurança](./media/backup-azure-arm-vms-prepare/backup-job-phase.png)

O **transferir dados para o Cofre** fase pode demorar vários dias para concluir, dependendo do tamanho dos discos, de alterações por disco e vários outros fatores.

Estado da tarefa pode variar consoante os seguintes cenários:

**instantâneo** | **Transferência de dados para o Cofre** | **Estado da tarefa**
--- | --- | ---
Concluído | Em curso | Em curso
Concluído | Ignorada | Concluído
Concluído | Concluído | Concluído
Concluído | Com Falhas | Concluído com aviso
Com Falhas | Com Falhas | Com Falhas


Agora com esta capacidade, para a mesma VM, duas cópias de segurança podem executar em paralelo, mas qualquer fase (instantâneo, os dados de transferência para o cofre) pode ser executado apenas uma subtarefa. Em cenários foram que uma tarefa de cópia de segurança em curso resultou na cópia de segurança no dia seguinte para efetuar a ativação irá ser evitado com este desacoplamento funcionalidade. Cópias de segurança do dia subsequentes podem ter instantâneo concluída enquanto **transferir dados para o Cofre** ignorado se a tarefa de cópia de segurança de um dia anterior está no estado de progresso.
O ponto de recuperação incrementais criado no cofre capturará todas as alterações do último ponto de recuperação criado no cofre. Não há nenhum impacto de custo no utilizador.


## <a name="optional-steps-install-agentallow-outbound"></a>Passos opcionais (instalar agente/permitir saída)
### <a name="install-the-vm-agent"></a>Instalar o agente da VM

O Azure Backup cria cópias de segurança de VMs do Azure ao instalar uma extensão para o agente de VM do Azure em execução na máquina. Se a VM foi criada a partir de uma imagem do Azure Marketplace, o agente está instalado e em execução. Se criar uma VM personalizada ou migrar uma máquina no local, precisará de instalar o agente manualmente, conforme resumido na tabela.

**VM** | **Detalhes**
--- | ---
**Windows** | 1. [Transferir e instalar](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) o ficheiro MSI do agente.<br/><br/> 2. Instale com permissões de administrador na máquina.<br/><br/> 3. Certifique-se a instalação. Na *C:\WindowsAzure\Packages* na VM, com o botão direito **WaAppAgent.exe** > **propriedades**. Sobre o **detalhes** separador, **versão de produto** deve ser 2.6.1198.718 ou superior.<br/><br/> Se estiver a atualizar o agente, certifique-se de que não existem operações de cópia de segurança estão em execução, e [reinstale o agente](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409).
**Linux** | Instale utilizando um pacote DEB ou um RPM do repositório de pacotes de sua distribuição. Este é o método preferencial para instalar e atualizar o agente Linux do Azure. Todas as a [apoiadas pelo fornecedores de distribuição](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) integrar o pacote do agente Linux do Azure em suas imagens e repositórios. O agente está disponível no [GitHub](https://github.com/Azure/WALinuxAgent), mas não é recomendada a instalação a partir daí.<br/><br/> Se estiver a atualizar o agente, certifique-se de que não existem operações de cópia de segurança estão em execução e os binários de atualização.

### <a name="explicitly-allow-outbound-access"></a>Permitir explicitamente o acesso de saída

A extensão de cópia de segurança em execução na VM tem acesso de saída para os endereços IP públicos do Azure.

- Em geral não precisa de permitir explicitamente o acesso de rede de saída para uma VM do Azure para que ele comunicar com o Azure Backup.
- Caso se depare com dificuldades com VMs ligar, ou se vir o erro **ExtensionSnapshotFailedNoNetwork** ao tentar ligar, deve permitir explicitamente acesso para que a extensão de cópia de segurança pode comunicar com o IP público do Azure endereços para o tráfego de cópia de segurança. Métodos de acesso estão resumidos na tabela seguinte.


**Opção** | **ação** | **Detalhes**
--- | --- | ---
**Configurar regras NSG** | Permitir a [intervalos IP do datacenter do Azure](https://www.microsoft.com/download/details.aspx?id=41653).<br/><br/> Em vez de permitir que e gerir a cada intervalo de endereços, pode adicionar uma regra que permite o acesso para o serviço de cópia de segurança do Azure com um [etiqueta de serviço](backup-azure-arm-vms-prepare.md#set-up-an-nsg-rule-to-allow-outbound-access-to-azure). | [Saiba mais](../virtual-network/security-overview.md#service-tags) sobre as etiquetas de serviço.<br/><br/> Etiquetas de serviços simplificam o gerenciamento de acesso e não implicar custos adicionais.
**Implementar um proxy** | Implemente um servidor de proxy HTTP para o encaminhamento de tráfego. | Fornece acesso a todo do Azure e não apenas armazenamento.<br/><br/> É permitido um controle granular sobre os URLs de armazenamento.<br/><br/> Único ponto de acesso à internet para VMs.<br/><br/> Custos adicionais para o proxy.
**Configurar a Firewall do Azure** | Permitir o tráfego através da Firewall do Azure na VM, com uma etiqueta do FQDN para o serviço de cópia de segurança do Azure | Fácil de utilizar se tiver o Firewall do Azure numa sub-rede de VNet.<br/><br/> Não é possível criar suas próprias etiquetas FQDN ou modificar os FQDNs numa marca.<br/><br/> Se as VMs do Azure têm discos geridos, poderá ter de abrir um adicional de porta (8443) nas firewalls.

#### <a name="establish-network-connectivity"></a>Estabelecer conectividade de rede

Estabelecer conectividade com o NSG, por proxy, ou através da firewall

##### <a name="set-up-an-nsg-rule-to-allow-outbound-access-to-azure"></a>Configurar uma regra NSG para permitir o acesso de saída para o Azure

Se um NSG está a gerir o acesso à VM, permita o acesso de saída para o armazenamento de cópia de segurança para as portas e intervalos necessários.

1. Nas propriedades da VM > **Networking**, selecione **Adicionar regra de saída através da porta**.
2. Na **Adicionar regra de segurança de saída**, selecione **avançadas**.
3. Na **origem**, selecione **VirtualNetwork**.
4. Na **intervalos de portas de origem**, introduza um asterisco (*) para permitir o acesso de saída de qualquer porta.
5. Na **destino**, selecione **etiqueta de serviço**. Na lista, selecione **Storage.region**. A região é onde o Cofre e as VMs que pretende criar cópias de segurança, estão localizadas.
6. Na **intervalos de portas de destino**, selecione a porta.
    - VM não gerida com a conta de armazenamento não encriptada: 80
    - VM não gerida com a conta de armazenamento encriptado: 443 (predefinição)
    - VM gerida: 8443.
7. Na **protocolo**, selecione **TCP**.
8. Na **prioridade**, especifique um valor de prioridade inferior a superior quaisquer negar regras.

   Se tiver uma regra que nega o acesso, o novo permite a regra tem de ser superior. Por exemplo, se tem um **Deny_All** conjunto com prioridade 1000, sua nova regra de regras devem ser definida para menos de 1000.
9. Forneça um nome e descrição para a regra e selecione **OK**.

Pode aplicar a regra NSG para várias VMs para permitir o acesso de saída. Este vídeo o orienta pelo processo.

>[!VIDEO https://www.youtube.com/embed/1EjLQtbKm1M]


##### <a name="route-backup-traffic-through-a-proxy"></a>Encaminhar o tráfego de cópia de segurança através de um proxy

Pode encaminhar o tráfego de cópia de segurança através de um proxy e, em seguida, dar o acesso de proxy para os intervalos do Azure necessários. Configure o proxy de VM para permitir que o seguinte:

- A VM do Azure deve encaminhar todo o tráfego HTTP vinculado para a internet pública através do proxy.
- O proxy deve permitir tráfego de entrada de VMs na rede virtual aplicável.
- O NSG **NSF bloqueio** necessita de uma regra que permita o tráfego de internet de saída do proxy VM.

###### <a name="set-up-the-proxy"></a>Configurar o proxy

Se não tiver um proxy de conta de sistema, configure um da seguinte forma:

1. Baixe [PsExec](https://technet.microsoft.com/sysinternals/bb897553).
2. Execute **PsExec.exe -i -s cmd.exe** para executar a linha de comandos com uma conta do sistema.
3. Execute o navegador no contexto do sistema. Por exemplo, usar **%PROGRAMFILES%\Internet Explorer\iexplore.exe** para o Internet Explorer.  
4. Defina as definições de proxy.
   - Nas máquinas do Linux:
     - Adicionar esta linha para o **/etc/ambiente** ficheiro:
       - **http_proxy=http:\//proxy IP address:proxy port**
     - Adicionar estas linhas para o **/etc/waagent.Conf.** ficheiro:
         - **HttpProxy.Host=proxy IP address**
         - **HttpProxy.Port=proxy port**
   - Nas máquinas do Windows, nas configurações do navegador, especifica que um proxy a utilizar. Se estiver atualmente a utilizar um proxy numa conta de utilizador, pode utilizar este script para aplicar a definição ao nível da conta de sistema.
       ```powershell
      $obj = Get-ItemProperty -Path Registry::"HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections"
      Set-ItemProperty -Path Registry::"HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name DefaultConnectionSettings -Value $obj.DefaultConnectionSettings
      Set-ItemProperty -Path Registry::"HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name SavedLegacySettings -Value $obj.SavedLegacySettings
      $obj = Get-ItemProperty -Path Registry::"HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings"
      Set-ItemProperty -Path Registry::"HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name ProxyEnable -Value $obj.ProxyEnable
      Set-ItemProperty -Path Registry::"HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name Proxyserver -Value $obj.Proxyserver

       ```

###### <a name="allow-incoming-connections-on-the-proxy"></a>Permitir ligações de entrada no proxy

Permita ligações de entrada nas definições de proxy.

1. Na Firewall do Windows, abra **Firewall do Windows com segurança avançada**.
2. Com o botão direito **regras de entrada** > **nova regra**.
3. Na **tipo de regra**, selecione **personalizada** > **seguinte**.
4. Na **programa**, selecione **todos os programas** > **seguinte**.
5. Na **protocolos e portas**:
   - Definir o tipo como **TCP**.
   - Definir **portas de locais** ao **portas específicas**.
   - Definir **porta remota** ao **todas as portas**.

6. Concluir o assistente e especificar um nome para a regra.

###### <a name="add-an-exception-rule-to-the-nsg-for-the-proxy"></a>Adicionar uma regra de exceção para o NSG para o proxy

No NSG **NSF bloqueio**, permitir tráfego a partir de qualquer porta no 10.0.0.5 para qualquer endereço internet na porta 80 (HTTP) ou 443 (HTTPS).

O seguinte script do PowerShell fornece um exemplo para permitir o tráfego.
Em vez de permitir saída para todos os endereços de internet pública, pode especificar um intervalo de endereços IP (`-DestinationPortRange`), ou usar a marca de serviço storage.region.   

```powershell
Get-AzureNetworkSecurityGroup -Name "NSG-lockdown" |
Set-AzureNetworkSecurityRule -Name "allow-proxy " -Action Allow -Protocol TCP -Type Outbound -Priority 200 -SourceAddressPrefix "10.0.0.5/32" -SourcePortRange "*" -DestinationAddressPrefix Internet -DestinationPortRange "80-443"
```

##### <a name="allow-firewall-access-with-an-fqdn-tag"></a>Permitir o acesso de firewall com uma etiqueta do FQDN

Pode configurar o Firewall do Azure para permitir o acesso de saída para o tráfego de rede para o Azure Backup.

- [Saiba mais sobre](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal) implantar o Firewall do Azure.
- [Leia sobre](https://docs.microsoft.com/azure/firewall/fqdn-tags) FQDN etiquetas.



## <a name="next-steps"></a>Passos Seguintes

- Resolva quaisquer problemas com [agentes de VM do Azure](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md) ou [cópia de segurança de VM do Azure](backup-azure-vms-troubleshoot.md).
- [Restaurar](backup-azure-arm-restore-vms.md) VMs do Azure.
