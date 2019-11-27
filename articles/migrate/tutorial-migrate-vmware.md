---
title: Migrar migração de servidor de migrações do Azure sem agente de VMs VMware
description: Saiba como executar uma migração sem agente de VMs VMware com migrações para Azure.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 2b4aad83abc92170df5a7e7cfa7f7751b49b3424
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196406"
---
# <a name="migrate-vmware-vms-to-azure-agentless"></a>Migrar VMs do VMware para o Azure (sem agente)

Este artigo mostra como migrar VMs VMware locais para o Azure, usando a migração sem agente com a ferramenta de migração de servidor migrações para Azure.

As [migrações para Azure](migrate-services-overview.md) fornecem um hub central para acompanhar a descoberta, a avaliação e a migração de seus aplicativos e cargas de trabalho locais e as instâncias de VM AWS/GCP para o Azure. O Hub fornece ferramentas de migração do Azure para avaliação e migração, bem como ofertas de ISVs (fornecedores independentes de software) de terceiros.

Este tutorial é o terceiro de uma série que demonstra como avaliar e migrar VMs do VMware para o Azure usando a migração e avaliação do servidor de migrações para Azure. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Preparar VMs para migração.
> * Adicione a ferramenta de migração do servidor de migração do Azure.
> * Descubra as VMs que você deseja migrar.
> * Iniciar a replicação de VMs.
> * Execute uma migração de teste para verificar se tudo está funcionando conforme o esperado.
> * Execute uma migração completa da VM.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.

## <a name="migration-methods"></a>Métodos de migração

Você pode migrar VMs do VMware para o Azure usando a ferramenta de migração de servidor de migrações para Azure. Essa ferramenta oferece algumas opções para a migração de VM do VMware:

- Migração usando a replicação sem agente. Migre VMs sem a necessidade de instalar nada nelas.
- Migração com um agente para replicação. Instale um agente na VM para replicação.

Para decidir se deseja usar a migração baseada em agente ou sem agente, leia estes artigos:

- [Saiba como funciona a](server-migrate-overview.md) migração sem agente e [Compare os métodos de migração](server-migrate-overview.md#compare-migration-methods).
- [Leia este artigo](tutorial-migrate-vmware-agent.md) se você quiser usar o método baseado em agente.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, tem de:

1. [Conclua o primeiro tutorial](tutorial-prepare-vmware.md) desta série para configurar o Azure e o VMware para migração. Especificamente, neste tutorial, você precisa:
    - [Prepare o Azure](tutorial-prepare-vmware.md#prepare-azure) para migração.
    - [Prepare o ambiente local](tutorial-prepare-vmware.md#prepare-for-agentless-vmware-migration) para migração.
    
2. Recomendamos que você tente avaliar as VMs VMware com a avaliação de servidor de migrações para Azure antes de migrá-las para o Azure. Para configurar a avaliação, [conclua o segundo tutorial](tutorial-assess-vmware.md) desta série. Se você não quiser avaliar as VMs, poderá ignorar este tutorial. Embora seja recomendável experimentar uma avaliação, mas você não precisa executar uma avaliação antes de tentar uma migração.



## <a name="add-the-azure-migrate-server-migration-tool"></a>Adicionar a ferramenta de migração de servidor de migrações para Azure

Se você não seguir o segundo tutorial para avaliar as VMs do VMware, precisará [seguir estas instruções](how-to-add-tool-first-time.md) para configurar um projeto de migrações para Azure e selecionar a ferramenta de migração de servidor de migrações para Azure. 

Se você seguiu o segundo tutorial e já tiver um projeto de migrações para Azure configurado, adicione a ferramenta de migração de servidor migrações para Azure da seguinte maneira:

1. No projeto migrações para Azure, clique em **visão geral**. 
2. Em **servidores de descoberta, avaliação e migração**, clique em **avaliar e migrar servidores**.

     ![Avaliar e migrar servidores](./media/tutorial-migrate-vmware/assess-migrate.png)

3. Em **ferramentas de migração**, selecione **clique aqui para adicionar uma ferramenta de migração quando você estiver pronto para migrar**.

    ![Selecionar ferramenta](./media/tutorial-migrate-vmware/select-migration-tool.png)

4. Na lista de ferramentas, selecione **migrações para Azure: migração de servidor** > **Adicionar ferramenta**

    ![Ferramenta de migração de servidor](./media/tutorial-migrate-vmware/server-migration-tool.png)

## <a name="set-up-the-azure-migrate-appliance"></a>Configurar o dispositivo de migrações para Azure

A migração de servidor de migrações para Azure executa um dispositivo leve de VM VMware. O dispositivo executa a descoberta de VM e envia metadados de VM e dados de desempenho para migração de servidor de migrações para Azure. O mesmo dispositivo também é usado pela ferramenta de avaliação do servidor de migrações para Azure.

Se você seguiu o segundo tutorial para avaliar as VMs do VMware, você já configurou o dispositivo durante esse tutorial. Se você não seguir esse tutorial, precisará configurar o dispositivo agora. Para fazer isso, você: 

- Baixe um arquivo de modelo OVA e importe-o para vCenter Server.
- Crie o dispositivo e verifique se ele pode se conectar à avaliação do servidor de migrações para Azure. 
- Configure o dispositivo pela primeira vez e registre-o com o projeto de migrações para Azure.

Siga as instruções neste [artigo](how-to-set-up-appliance-vmware.md) para configurar o dispositivo.


## <a name="prepare-vms-for-migration"></a>Preparar VMs para migração

As migrações para Azure exigem algumas alterações de VM para garantir que as VMs possam ser migradas para o Azure.

- Para alguns sistemas operacionais, as migrações para Azure fazem essas alterações automaticamente. [Obter mais informações](migrate-support-matrix-vmware.md#agentless-migration-vmware-vm-requirements)
- Se você estiver migrando uma VM que não tem um desses sistemas operacionais, siga as instruções para preparar a VM.
- É importante fazer essas alterações antes de começar a migração. Se você migrar a VM antes de fazer a alteração, a VM poderá não ser inicializada no Azure.
- As alterações de configuração feitas em VMs locais são replicadas no Azure depois que a replicação para a VM é habilitada. Para garantir que as alterações sejam replicadas, verifique se o ponto de recuperação que você migra para o é posterior à hora em que as alterações de configuração foram feitas localmente.


### <a name="prepare-windows-server-vms"></a>Preparar VMs do Windows Server

**Ação** | **Detalhes** | **Sobre**
--- | --- | ---
Verifique se os volumes do Windows na VM do Azure usam as mesmas atribuições de letra de unidade que a VM local. | Configure a política SAN como tudo online. | 1. entre na VM com uma conta de administrador e abra uma janela de comando.<br/> 2. digite **DiskPart** para executar o utilitário DiskPart.<br/> 3. digite **política de San = OnlineAll**<br/> 4. digite Exit para sair do DiskPart e feche o prompt de comando.
Habilitar o console de acesso serial do Azure para a VM do Azure | Isso ajuda na solução de problemas. Você não precisa reinicializar a VM. A VM do Azure será inicializada usando a imagem de disco e isso será equivalente a uma reinicialização para a nova VM. | Siga [estas instruções](https://docs.microsoft.com/azure/virtual-machines/windows/serial-console) para habilitar o.
Instalar a integração de convidado do Hyper-V | Se você estiver migrando computadores que executam o Windows Server 2003, instale o Integration Services convidado do Hyper-V no sistema operacional da VM. | [Saiba mais](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#install-or-update-integration-services).
Ambiente de Trabalho Remoto | Habilite Área de Trabalho Remota na VM e verifique se o Firewall do Windows não está bloqueando o acesso Área de Trabalho Remota em nenhum perfil de rede. | [Saiba mais](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-allow-access).

### <a name="prepare-linux-vms"></a>Preparar VMs Linux

**Ação** | **Detalhes** 
--- | --- | ---
Instalar Integration Services Linux do Hyper-V | A maioria das novas versões das distribuições do Linux tem isso incluído por padrão.
Recriar a imagem de inicialização do Linux para conter os drivers do Hyper-V necessários | Isso garante que a VM será inicializada no Azure e só será necessária em algumas distribuições.
Habilitar o log do console serial do Azure | Isso ajuda na solução de problemas. Você não precisa reinicializar a VM. A VM do Azure será inicializada usando a imagem de disco e isso será equivalente a uma reinicialização para a nova VM.<br/> Siga [estas instruções](https://docs.microsoft.com/azure/virtual-machines/linux/serial-console) para habilitar o.
Atualizar arquivo do mapa do dispositivo | Atualizar o arquivo do mapa do dispositivo que tem o nome do dispositivo para associações de volume, para usar identificadores de dispositivo persistentes
Atualizar entradas fstab | Atualizar entradas para usar identificadores de volume persistentes.
Remover regra udev | Remova as regras udev que reservam nomes de interface com base no endereço Mac, etc.
Atualizar interfaces de rede | Atualize as interfaces de rede para receber o endereço IP com base no DHCP.
Habilitar SSH | Verifique se o SSH está habilitado e se o serviço sshd está definido para iniciar automaticamente na reinicialização.<br/> Verifique se as solicitações de conexão SSH de entrada não estão bloqueadas pelo firewall do sistema operacional ou por regras programáveis.

[Siga este artigo](https://docs.microsoft.com/azure/virtual-machines/linux/create-upload-generic) que aborda estas etapas para executar uma VM do Linux no Azure e inclui instruções para algumas das distribuições populares do Linux.  


## <a name="replicate-vms"></a>Replicar VMs

Com a deteção concluída, pode começar a replicação de VMs do VMware no Azure. 

> [!NOTE]
> Você pode replicar até 10 computadores juntos. Se você precisar replicar mais, replique-os simultaneamente em lotes de 10. Para a migração sem agente, você pode executar até 100 replicações simultâneas.

1. No projeto de migração do Azure > **servidores**, **migrações para Azure: migração de servidor**, clique em **replicar**.

    ![Replicar VMs](./media/tutorial-migrate-vmware/select-replicate.png)

2. Em **Replicar**, **Definições de origem** > **Os computadores estão virtualizados?** , selecione **Sim, com o VMware vSphere**.
3. Em **Aplicação no local**, selecione o nome da aplicação do Azure Migrate que configurou > **OK**. 

    ![Definições de origem](./media/tutorial-migrate-vmware/source-settings.png)

    - Esta etapa pressupõe que você já configurou um dispositivo quando concluiu o tutorial.
    - Se você ainda não configurou um dispositivo, siga as instruções neste [artigo](how-to-set-up-appliance-vmware.md).

4. Em **Máquinas virtuais**, selecione aquelas que quer replicar.
    - Se tiver executado uma avaliação para as VMs, poderá aplicar as recomendações de dimensionamento de VMs e tipo de discos (premium/standard) nos resultados da avaliação. Para tal, em **Importar definições de migração de uma avaliação do Azure Migrate?** , selecione a opção **Sim**.
    - Se não tiver executado uma avaliação ou não quiser utilizar as definições de avaliação, selecione as opções **Não**.
    - Se tiver selecionado para utilizar a avaliação, selecione o grupo de VMs e o nome da avaliação.

    ![Selecionar avaliação](./media/tutorial-migrate-vmware/select-assessment.png)

5. Em **Máquinas virtuais**, procure VMs conforme necessário e marque cada VM que quer migrar. Em seguida, clique em **Avançar: configurações de destino**.

    ![Selecionar VMs](./media/tutorial-migrate-vmware/select-vms.png)

6. Em **Definições de destino**, selecione a subscrição, assim como a região de destino para a qual vai migrar, e especifique o grupo de recursos no qual as VMs do Azure vão residir após a migração. Em **Rede Virtual**, selecione a VNet/sub-rede do Azure na qual as VMs do Azure vão ser associadas após a migração.
7. Em **Benefício Híbrido do Azure**:

    - Selecione **Não** se não quiser aplicar o Benefício Híbrido do Azure. Clique depois em **Seguinte**.
    - Selecione **Sim** se tiver computadores Windows Server abrangidos com subscrições ativas do Software Assurance ou do Windows Server e quiser aplicar o benefício aos computadores que está a migrar. Clique depois em **Seguinte**.

    ![Configurações de destino](./media/tutorial-migrate-vmware/target-settings.png)

8. Em **Computação**, analise o nome, o tamanho, o tipo de disco do SO e o conjunto de disponibilidade das VMs. As VMs devem estar em conformidade com os [requisitos do Azure](migrate-support-matrix-vmware.md#agentless-migration-vmware-vm-requirements).

    - **Tamanho da VM**: se você estiver usando recomendações de avaliação, a lista suspensa tamanho da VM conterá o tamanho recomendado. Caso contrário, o Azure Migrate escolhe um tamanho com base na correspondência mais próxima na subscrição do Azure. Como alternativa, escolha um tamanho manual em **Tamanho de VMs do Azure**. 
    - **Disco do so**: especifique o disco do sistema operacional (inicialização) para a VM. O disco do SO é o disco que possui o carregador e o instalador do sistema operativo. 
    - **Conjunto de disponibilidade**: se a VM deve estar em um conjunto de disponibilidade do Azure após a migração, especifique o conjunto. O conjunto deve estar no grupo de recursos de destino especificado para a migração.

    ![Configurações de computação da VM](./media/tutorial-migrate-vmware/compute-settings.png)

9. Em **Discos**, especifique se os discos das VMs devem ser replicados para o Azure e selecione o tipo de disco (discos geridos SSD/HDD standard ou premium) no Azure. Clique depois em **Seguinte**.
    - Pode excluir discos da replicação.
    - Se excluir discos, estes não estarão presentes na VM do Azure após a migração. 

    ![Discos](./media/tutorial-migrate-vmware/disks.png)

10. Em **Analisar e iniciar a replicação**, analise as definições e clique em **Replicar** para iniciar a replicação inicial para os servidores.

> [!NOTE]
> Pode atualizar as definições de replicação a qualquer momento antes do início da replicação, em **Gerir** > **Replicar computadores**. As definições não podem ser alteradas após o início da replicação.

### <a name="provisioning-for-the-first-time"></a>Provisionamento pela primeira vez

Se esta for a primeira VM que você está replicando no projeto de migrações para Azure, a migração de servidor de migrações para Azure provisiona automaticamente esses recursos no mesmo grupo de recursos que o projeto.

- **Barramento de serviço**: a migração de servidor de migrações para Azure usa o barramento de serviço para enviar mensagens de orquestração de replicação para o dispositivo.
- **Conta de armazenamento do gateway**: a migração do servidor usa a conta de armazenamento do gateway para armazenar informações de estado sobre as VMs que estão sendo replicadas.
- **Conta de armazenamento de log**: o dispositivo de migrações para Azure carrega logs de replicação para VMs em uma conta de armazenamento de log. As migrações para Azure aplicam as informações de replicação aos discos gerenciados de réplica.
- **Cofre de chaves**: o dispositivo de migrações para Azure usa o cofre de chaves para gerenciar cadeias de conexão para o barramento de serviço e chaves de acesso para as contas de armazenamento usadas na replicação. Você deve ter configurado as permissões que o cofre de chaves precisa para acessar a conta de armazenamento quando você se preparou. [Revise essas permissões](tutorial-prepare-vmware.md#assign-role-assignment-permissions).   


## <a name="track-and-monitor"></a>Acompanhar e monitorar


- Quando você clica em **replicar** , um trabalho iniciar replicação é iniciado. 
- Quando o trabalho iniciar replicação é concluído com êxito, os computadores começam sua replicação inicial para o Azure.
- Durante a replicação inicial, um instantâneo de VM é criado. Os dados do disco do instantâneo são replicados para discos gerenciados de réplica no Azure.
- Após a conclusão da replicação inicial, a replicação delta começa. As alterações incrementais em discos locais são replicadas periodicamente para os discos de réplica no Azure.

Você pode acompanhar o status do trabalho nas notificações do Portal.

Você pode monitorar o status de replicação clicando em **replicar servidores** no **Azure migrar: migração de servidor**.
![](./media/tutorial-migrate-vmware/replicating-servers.png) de replicação do monitor




## <a name="run-a-test-migration"></a>Executar uma migração de teste


Quando a replicação delta é iniciada, você pode executar uma migração de teste para as VMs antes de executar uma migração completa para o Azure. É altamente recomendável que você faça isso pelo menos uma vez para cada computador, antes de migrá-lo.

- A execução de uma migração de teste verifica se a migração funcionará conforme o esperado, sem afetar os computadores locais, que permanecem operacionais e continuam replicando. 
- A migração de teste simula a migração criando uma VM do Azure usando dados replicados (geralmente migrando para uma VNet de não produção em sua assinatura do Azure).
- Você pode usar a VM do Azure de teste replicado para validar a migração, executar testes de aplicativo e resolver problemas antes da migração completa.

Faça uma migração de teste da seguinte maneira:


1. Em **metas de migração** > **servidores** > **migrações para Azure: migração de servidor**, clique em **testar servidores migrados**.

     ![Testar servidores migrados](./media/tutorial-migrate-vmware/test-migrated-servers.png)

2. Clique com o botão direito do rato na VM a testar e, em seguida, clique em **Testar migração**.

    ![Testar migração](./media/tutorial-migrate-vmware/test-migrate.png)

3. Em **Testar Migração**, selecione a VNet do Azure na qual a VM do Azure vai estar localizada após a migração. Recomendamos que utilize uma VNet que não seja de produção.
4. A tarefa **Teste de migração** é iniciada. Monitorize a tarefa nas notificações do portal.
5. Após a conclusão da migração, veja a VM do Azure migrada em **Máquinas Virtuais** no portal do Azure. O nome do computador tem um sufixo **-Test**.
6. Após terminar o teste, clique com o botão direito do rato na VM do Azure em **Replicar computadores** e clique em **Limpar teste de migração**.

    ![Limpar a migração](./media/tutorial-migrate-vmware/clean-up.png)


## <a name="migrate-vms"></a>Migrar VMs

Depois de verificar se a migração de teste funciona conforme o esperado, você pode migrar os computadores locais.

1. No projeto de migração do Azure > **servidores** > **migrações para Azure: migração de servidor**, clique em **replicar servidores**.

    ![Replicar servidores](./media/tutorial-migrate-vmware/replicate-servers.png)

2. Em **Replicar computadores**, clique com o botão direito do rato na VM > **Migrar**.
3. Em **Migrar** > **Desligar máquinas virtuais e realizar uma migração planeada sem perda de dados**, selecione **Sim** > **OK**.
    - Por predefinição, o Azure Migrate desliga a VM no local e executa uma replicação a pedido para sincronizar quaisquer alterações à VM ocorridas desde a última replicação. Tal garante que não haja perda de dados.
    - Se não quiser desligar a VM, selecione **Não**
4. Uma tarefa de migração é iniciada para a VM. Controle a tarefa nas notificações do Azure.
5. Após a conclusão da tarefa, pode ver e gerir a VM na página **Máquinas Virtuais**.

## <a name="complete-the-migration"></a>Concluir a migração

1. Depois que a migração for concluída, clique com o botão direito do mouse na VM > **parar a migração**. Isso interrompe a replicação para o computador local e limpa as informações de estado de replicação da VM.
2. Instale o agente do [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) ou [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) da VM do Azure nos computadores migrados.
3. Execute otimizações de aplicação pós-migração, tais como atualizar cadeias de ligação de base de dados e configurações de servidor Web.
4. Execute testes de aplicação final e de aceitação da migração na aplicação migrada em execução no Azure.
5. Reduza o tráfego para a instância de VM do Azure migrada.
6. Remova as VMs no local do seu inventário de VMs locais.
7. Remova as VMs no local das cópias de segurança locais.
8. Atualize qualquer documentação interna para mostrar a nova localização e endereço IP das VMs do Azure. 

## <a name="post-migration-best-practices"></a>Práticas recomendadas após a migração

- Para uma maior resiliência:
    - Mantenha os dados seguros ao fazer uma cópia de segurança das VMs do Azure através do serviço Azure Backup. [Saiba mais](../backup/quick-backup-vm-portal.md).
    - Mantenha as cargas de trabalho em execução e continuamente disponíveis ao replicar VMs do Azure para uma região secundária com o Site Recovery. [Saiba mais](../site-recovery/azure-to-azure-tutorial-enable-replication.md).
- Para uma maior segurança:
    - Bloquear e limitar o acesso ao tráfego de entrada com a [central de segurança do Azure-administração just-in-time](https://docs.microsoft.com/azure/security-center/security-center-just-in-time).
    - Restrinja o tráfego de rede para os pontos finais de gestão com os [Grupos de Segurança de Rede](https://docs.microsoft.com/azure/virtual-network/security-overview).
    - Implemente o [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview) para ajudar a proteger discos e a manter os dados protegidos contra roubo e acesso não autorizado.
    - Leia mais sobre como [proteger recursos de IaaS](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/) e aceda ao [Centro de Segurança do Azure](https://azure.microsoft.com/services/security-center/).
- Para monitorização e gestão:
-  Considere implementar o [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/overview) para monitorizar a utilização e as despesas do recurso.


## <a name="next-steps"></a>Passos Seguintes

Investigue a [jornada de migração na nuvem](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/migrate) na estrutura de adoção de nuvem do Azure.
