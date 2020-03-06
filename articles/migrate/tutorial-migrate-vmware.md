---
title: Migração de VMware Migratório Migratório Migratório Migratório Migratório Migratório Imware
description: Aprenda a executar uma migração sem agente de VMware VMs com Azure Migrate.
ms.topic: tutorial
ms.date: 11/19/2019
ms.custom: mvc
ms.openlocfilehash: 825d6ff16a1f51fa476541ee10fea5f8a1c2972e
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78304213"
---
# <a name="migrate-vmware-vms-to-azure-agentless"></a>VMs migratórios para Azure (sem agente)

Este artigo mostra-lhe como migrar vMs no local para Azure, utilizando migração sem agente com a ferramenta migração do servidor de migração do servidor migratório Azure Migrate.

[A Azure Migrate](migrate-services-overview.md) fornece um centro central para acompanhar a descoberta, avaliação e migração das suas aplicações e cargas de trabalho no local, e instâncias de VM AWS/GCP, para o Azure. O hub fornece ferramentas azure migrate para avaliação e migração, bem como ofertas de fornecedores de software independentes de terceiros (ISV).

Este tutorial é o terceiro de uma série que demonstra como avaliar e migrar VMware VMs para Azure usando a Avaliação e Migração do Servidor migratório Azure. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Prepare mVs para migração.
> * Adicione a ferramenta de migração do servidor de migração Azure.
> * Descubra vMs que quer migrar.
> * Comece a replicar VMs.
> * Faça uma migração de teste para ter certeza de que está tudo funcionando como esperado.
> * Executar uma migração VM completa.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.

## <a name="migration-methods"></a>Métodos de migração

Pode migrar VMware VMs para Azure utilizando a ferramenta migração do servidor de migração do servidor migratório Azure Migrate. Esta ferramenta oferece um par de opções para a migração VMware VM:

- Migração usando replicação sem agente. Migrar VMs sem precisar de instalar nada neles.
- Migração com um agente para replicação. Instale um agente no VM para replicação.

Para decidir se pretende utilizar migração sem agentes ou baseada em agentes, reveja estes artigos:

- [Saiba como](server-migrate-overview.md) funciona a migração sem agente e compare métodos de [migração.](server-migrate-overview.md#compare-migration-methods)
- [Leia este artigo](tutorial-migrate-vmware-agent.md) se quiser utilizar o método baseado no agente.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, tem de:

1. [Complete o primeiro tutorial](tutorial-prepare-vmware.md) desta série para configurar Azure e VMware para migração. Especificamente, neste tutorial é preciso:
    - [Prepare Azure](tutorial-prepare-vmware.md#prepare-azure) para a migração.
    - [Prepare o ambiente no local](tutorial-prepare-vmware.md#prepare-for-agentless-vmware-migration) para a migração.
    
2. Recomendamos que tente avaliar vMware VMs com Avaliação de Servidor esmigrado Azure antes de os migrar para o Azure. Para configurar a avaliação, [complete o segundo tutorial](tutorial-assess-vmware.md) desta série. Se não quiser avaliar os VMs, pode ignorar este tutorial. Embora recomendemos que experimente uma avaliação, mas não precisa fazer uma avaliação antes de tentar uma migração.



## <a name="add-the-azure-migrate-server-migration-tool"></a>Adicione a ferramenta de migração do servidor migratório Azure Migrate

Se não seguiu o segundo tutorial para avaliar vMware VMs, precisa [seguir estas instruções](how-to-add-tool-first-time.md) configurar um projeto Azure Migrate e selecionar a ferramenta de migração do servidor de migração do Servidor Migratório Azure Migrate. 

Se seguiu o segundo tutorial e já tiver um projeto Azure Migrate criado, adicione a ferramenta de migração do servidor migratório Azure da seguinte forma:

1. No projeto Azure Migrate, clique em **Visão Geral**. 
2. No **Discover, avaliar e migrar servidores,** clique em **avaliar e migrar servidores**.

     ![Avaliar e migrar servidores](./media/tutorial-migrate-vmware/assess-migrate.png)

3. Nas **ferramentas de migração,** selecione Clique aqui para adicionar uma ferramenta de **migração quando estiver pronto para migrar**.

    ![Selecione uma ferramenta](./media/tutorial-migrate-vmware/select-migration-tool.png)

4. Na lista de ferramentas, selecione **Azure Migrate: Server Migration** > **Adicionar ferramenta**

    ![Ferramenta de migração do servidor](./media/tutorial-migrate-vmware/server-migration-tool.png)

## <a name="set-up-the-azure-migrate-appliance"></a>Instale o aparelho Migratório Azure

A Migração do Servidor Migratório Azure tem um aparelho VMware VM leve. O aparelho realiza a descoberta de VM e envia metadados VM e dados de desempenho para a Migração do Servidor Migratório Migratório Migratório Migratório Migratório Migratório Migratório Migratório Migratório Migratório Azure. O mesmo aparelho também é utilizado pela ferramenta de avaliação do servidor de migração Azure.

Se seguiu o segundo tutorial para avaliar vMware VMs, já montou o aparelho durante esse tutorial. Se não seguiu o tutorial, tem de montar o aparelho agora. Para fazer isto, tu: 

- Descarregue um ficheiro de modelo OVA e importe-o para vCenter Server.
- Crie o aparelho e verifique se pode ligar-se à Avaliação do Servidor Migratório Azure. 
- Configure o aparelho pela primeira vez e registe-o com o projeto Azure Migrate.

Siga as instruções [deste artigo](how-to-set-up-appliance-vmware.md) para configurar o aparelho.


## <a name="prepare-vms-for-migration"></a>Prepare VMs para migração

A Azure Migrate requer algumas alterações vm para garantir que os VMs podem ser migrados para Azure.

- Para alguns sistemas operativos, o Azure Migrate faz estas alterações automaticamente. [Saiba mais](migrate-support-matrix-vmware-migration.md#agentless-vmware-vms)
- Se estiver a migrar um VM que não tenha um destes sistemas operativos, siga as instruções para preparar o VM.
- É importante fazer estas mudanças antes de começar a migração. Se migraro VM antes de fazer a mudança, o VM pode não arrancar em Azure.
- As alterações de configuração que faz nos VMs no local são replicadas para Azure após a replicação para o VM estar ativada. Para garantir que as alterações são replicadas, certifique-se de que o ponto de recuperação para o qual migra é mais tarde do que o momento em que as alterações de configuração foram feitas no local.


### <a name="prepare-windows-server-vms"></a>Preparar VMs do Servidor windows

**Ação** | **Detalhes** | **Instruções**
--- | --- | ---
Certifique-se de que os volumes do Windows em VM Azure utilizam as mesmas atribuições de letra de unidade que o VM no local. | Configure a política san como Online All. | 1. Inscreva-se no VM com uma conta de administração e abra uma janela de comando.<br/> 2. Digite **a peça de disco** para executar o utilitário Diskpart.<br/> 3. Tipo **SAN POLICY=OnlineAll**<br/> 4. Saída de tipo para sair de Diskpart e fechar o pedido de comando.
Ativar a consola de acesso em série Azure para o Azure VM | Isto ajuda na resolução de problemas. Não precisas de reiniciar o VM. O Azure VM arrancará com a imagem do disco, o que equivale a um reboot para o novo VM. | Siga [estas instruções](https://docs.microsoft.com/azure/virtual-machines/windows/serial-console) para ativar.
Instale integração de hóspedes hiper-V | Se estiver a migrar máquinas que executam o Windows Server 2003, instale serviços de integração de hóspedes Hiper-V no sistema operativo VM. | [Saiba mais](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#install-or-update-integration-services).
Ambiente de Trabalho Remoto | Ative o Remote Desktop no VM e verifique se o Windows Firewall não está a bloquear o acesso remoto ao ambiente de trabalho em quaisquer perfis de rede. | [Saiba mais](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-allow-access).

### <a name="prepare-linux-vms"></a>Prepare Linux VMs

**Ação** | **Detalhes** 
--- | --- | ---
Instale serviços de integração Hyper-V Linux | A maioria das novas versões de distribuições do Linux têm isto incluído por padrão.
Reconstruir a imagem de init Linux para conter os necessários condutores hyper-V | Isto garante que o VM irá arrancar em Azure, e só é necessário em algumas distribuições.
Ativar o registo de consolas em série Azure | Isto ajuda na resolução de problemas. Não precisas de reiniciar o VM. O Azure VM arrancará com a imagem do disco, o que equivale a um reboot para o novo VM.<br/> Siga [estas instruções](https://docs.microsoft.com/azure/virtual-machines/linux/serial-console) para ativar.
Atualizar ficheiro de mapa do dispositivo | Atualize o ficheiro do mapa do dispositivo que tem o nome do dispositivo para associações de volume, para utilizar identificadores persistentes do dispositivo
Atualizar entradas de fstab | Atualizar as entradas para utilizar identificadores de volume persistentes.
Remover a regra udev | Remova quaisquer regras udev que reservessem nomes de interface com base no endereço mac, etc.
Atualizar interfaces de rede | Atualizar interfaces de rede para receber endereço IP com base no DHCP.
Ativar o ssh | Certifique-se de que o ssh está ativado e o serviço sshd está programado para começar automaticamente no reboot.<br/> Certifique-se de que os pedidos de ligação ssh de entrada não são bloqueados pela firewall do SISTEMA ou regras scriptáveis.

[Siga este artigo](https://docs.microsoft.com/azure/virtual-machines/linux/create-upload-generic) que discute estes passos para executar um Linux VM em Azure, e inclua instruções para algumas das populares distribuições linux.  


## <a name="replicate-vms"></a>Replicar VMs

Com a deteção concluída, pode começar a replicação de VMs do VMware no Azure. 

> [!NOTE]
> Podes replicar até 10 máquinas juntas. Se precisar de replicar mais, reproduza-os simultaneamente em lotes de 10. Para uma migração sem agente, pode executar até 100 replicações simultâneas.

1. No projeto Azure Migrate > **Servers,** **Azure Migrate: Server Migration,** clique em **Replicate**.

    ![Replicar VMs](./media/tutorial-migrate-vmware/select-replicate.png)

2. Em **Replicar**, **Definições de origem** > **Os computadores estão virtualizados?** , selecione **Sim, com o VMware vSphere**.
3. Em **Aplicação no local**, selecione o nome da aplicação do Azure Migrate que configurou > **OK**. 

    ![Definições de origem](./media/tutorial-migrate-vmware/source-settings.png)

    - Este passo pressupõe que já tenha montado um aparelho quando completou o tutorial.
    - Se não tiver montado um aparelho, siga as instruções [deste artigo](how-to-set-up-appliance-vmware.md).

4. Em **Máquinas virtuais**, selecione aquelas que quer replicar.
    - Se tiver executado uma avaliação para as VMs, poderá aplicar as recomendações de dimensionamento de VMs e tipo de discos (premium/standard) nos resultados da avaliação. Para tal, em **Importar definições de migração de uma avaliação do Azure Migrate?** , selecione a opção **Sim**.
    - Se não tiver executado uma avaliação ou não quiser utilizar as definições de avaliação, selecione as opções **Não**.
    - Se tiver selecionado para utilizar a avaliação, selecione o grupo de VMs e o nome da avaliação.

    ![Selecionar avaliação](./media/tutorial-migrate-vmware/select-assessment.png)

5. Em **Máquinas virtuais**, procure VMs conforme necessário e marque cada VM que quer migrar. Em seguida, clique em **Seguinte: Definições de destino**.

    ![Selecione VMs](./media/tutorial-migrate-vmware/select-vms.png)

6. Em **Definições de destino**, selecione a subscrição, assim como a região de destino para a qual vai migrar, e especifique o grupo de recursos no qual as VMs do Azure vão residir após a migração. Em **Rede Virtual**, selecione a VNet/sub-rede do Azure na qual as VMs do Azure vão ser associadas após a migração.
7. Em **Benefício Híbrido do Azure**:

    - Selecione **Não** se não quiser aplicar o Benefício Híbrido do Azure. Clique depois em **Seguinte**.
    - Selecione **Sim** se tiver computadores Windows Server abrangidos com subscrições ativas do Software Assurance ou do Windows Server e quiser aplicar o benefício aos computadores que está a migrar. Clique depois em **Seguinte**.

    ![Definições de destino](./media/tutorial-migrate-vmware/target-settings.png)

8. Em **Computação**, analise o nome, o tamanho, o tipo de disco do SO e o conjunto de disponibilidade das VMs. As VMs devem estar em conformidade com os [requisitos do Azure](migrate-support-matrix-vmware-migration.md#azure-vm-requirements).

    - **Tamanho VM**: Se estiver a utilizar recomendações de avaliação, a queda do tamanho do VM conterá o tamanho recomendado. Caso contrário, o Azure Migrate escolhe um tamanho com base na correspondência mais próxima na subscrição do Azure. Como alternativa, escolha um tamanho manual em **Tamanho de VMs do Azure**. 
    - **Disco OS:** Especifique o disco OS (boot) para o VM. O disco do SO é o disco que possui o carregador e o instalador do sistema operativo. 
    - **Conjunto de disponibilidade**: Se o VM estiver num conjunto de disponibilidade azure após a migração, especifique o conjunto. O conjunto deve estar no grupo de recursos de destino especificado para a migração.

    ![Definições de computação VM](./media/tutorial-migrate-vmware/compute-settings.png)

9. Em **Discos**, especifique se os discos das VMs devem ser replicados para o Azure e selecione o tipo de disco (discos geridos SSD/HDD standard ou premium) no Azure. Clique depois em **Seguinte**.
    - Pode excluir discos da replicação.
    - Se excluir discos, estes não estarão presentes na VM do Azure após a migração. 

    ![Discos](./media/tutorial-migrate-vmware/disks.png)

10. Em **Analisar e iniciar a replicação**, analise as definições e clique em **Replicar** para iniciar a replicação inicial para os servidores.

> [!NOTE]
> Pode atualizar as definições de replicação a qualquer momento antes do início da replicação, em **Gerir** > **Replicar computadores**. As definições não podem ser alteradas após o início da replicação.

### <a name="provisioning-for-the-first-time"></a>Provisionamento pela primeira vez

Se este é o primeiro VM que está a replicar no projeto Azure Migrate, a Migração do Servidor Migratório Azure disponibiliza automaticamente estes recursos no mesmo grupo de recursos que o projeto.

- **Ônibus de serviço**: Azure Migrate Server Migration usa o ônibus de serviço para enviar mensagens de orquestração de replicação para o aparelho.
- **Conta de armazenamento**gateway : Server Migration usa a conta de armazenamento de gateway para armazenar informações estatais sobre os VMs que estão sendo replicados.
- **Conta de armazenamento de registos**: O aparelho Azure Migrate envia registos de replicação para VMs para uma conta de armazenamento de registo. O Azure Migrate aplica a informação de replicação aos discos geridos pela réplica.
- **Cofre chave**: O aparelho Azure Migrate utiliza o cofre chave para gerir as cordas de ligação para o autocarro de serviço e as chaves de acesso para as contas de armazenamento utilizadas na replicação. Devia ter configurado as permissões que o cofre precisa para aceder à conta de armazenamento quando se preparava. [Reveja estas permissões.](tutorial-prepare-vmware.md#assign-permissions-to-create-a-key-vault)   


## <a name="track-and-monitor"></a>Faixa e monitor


- Quando clicar em **Replicar** começa um trabalho de replicação de arranque. 
- Quando o trabalho de Replicação Inicial termina com sucesso, as máquinas iniciam a sua replicação inicial para Azure.
- Durante a replicação inicial, é criado um instantâneo VM. Os dados do disco do instantâneo são replicados para replicar discos geridos em Azure.
- Após os acabamentos iniciais da replicação, começa a replicação delta. As alterações incrementais nos discos no local são periodicamente replicadas para os discos de réplica em Azure.

Pode rastrear o estado do emprego nas notificações do portal.

Pode monitorizar o estado de replicação clicando em **servidores de replicação** em **Azure Migrate: Server Migration**.
![monitor](./media/tutorial-migrate-vmware/replicating-servers.png) de replicação




## <a name="run-a-test-migration"></a>Executar uma migração de teste


Quando a replicação delta começar, você pode executar uma migração de teste para os VMs, antes de executar uma migração completa para Azure. Recomendamos vivamente que faça isto pelo menos uma vez por cada máquina, antes de a migrar.

- A realização de um teste de migração verifica que a migração funcionará como esperado, sem afetar as máquinas no local, que permanecem operacionais, e continuam a replicar-se. 
- A migração de teste simula a migração criando um VM Azure usando dados replicados (geralmente migrando para um VNet não-produção na sua assinatura Azure).
- Pode utilizar o teste replicado Azure VM para validar a migração, realizar testes de aplicações e resolver quaisquer problemas antes da migração total.

Faça uma migração de teste da seguinte forma:


1. Nos **objetivos de migração** > **servidores** > **Migração Do Servidor,** clique em **servidores migrados**de teste .

     ![Testar servidores migrados](./media/tutorial-migrate-vmware/test-migrated-servers.png)

2. Clique com o botão direito do rato na VM a testar e, em seguida, clique em **Testar migração**.

    ![Testar migração](./media/tutorial-migrate-vmware/test-migrate.png)

3. Em **Testar Migração**, selecione a VNet do Azure na qual a VM do Azure vai estar localizada após a migração. Recomendamos que utilize uma VNet que não seja de produção.
4. A tarefa **Teste de migração** é iniciada. Monitorize a tarefa nas notificações do portal.
5. Após a conclusão da migração, veja a VM do Azure migrada em **Máquinas Virtuais** no portal do Azure. O nome do computador tem um sufixo **-Test**.
6. Após terminar o teste, clique com o botão direito do rato na VM do Azure em **Replicar computadores** e clique em **Limpar teste de migração**.

    ![Limpar a migração](./media/tutorial-migrate-vmware/clean-up.png)


## <a name="migrate-vms"></a>Migrar VMs

Depois de verificar que a migração do teste funciona como esperado, pode migrar as máquinas no local.

1. No projeto Azure Migrate > **Servers** > **Azure Migrate: Server Migration,** clique em **servidores de replicação**.

    ![Replicar servidores](./media/tutorial-migrate-vmware/replicate-servers.png)

2. Em **Replicar computadores**, clique com o botão direito do rato na VM > **Migrar**.
3. Em **Migrar** > **Desligar máquinas virtuais e realizar uma migração planeada sem perda de dados**, selecione **Sim** > **OK**.
    - Por predefinição, o Azure Migrate desliga a VM no local e executa uma replicação a pedido para sincronizar quaisquer alterações à VM ocorridas desde a última replicação. Tal garante que não haja perda de dados.
    - Se não quiser desligar a VM, selecione **Não**
4. Uma tarefa de migração é iniciada para a VM. Controle a tarefa nas notificações do Azure.
5. Após a conclusão da tarefa, pode ver e gerir a VM na página **Máquinas Virtuais**.

## <a name="complete-the-migration"></a>Complete a migração

1. Depois da migração ser feita, clique à direita na VM > **Stop Replication**. Isto para a replicação para a máquina no local, e limpa informações do estado de replicação para o VM.
2. Instale o Azure VM [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) ou o agente [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) nas máquinas migradas.
3. Execute otimizações de aplicação pós-migração, tais como atualizar cadeias de ligação de base de dados e configurações de servidor Web.
4. Execute testes de aplicação final e de aceitação da migração na aplicação migrada em execução no Azure.
5. Corte o tráfego para a instância azure vm migrada.
6. Remova as VMs no local do seu inventário de VMs locais.
7. Remova as VMs no local das cópias de segurança locais.
8. Atualize qualquer documentação interna para mostrar a nova localização e endereço IP das VMs do Azure. 

## <a name="post-migration-best-practices"></a>Boas práticas pós-migração

- Para uma maior resiliência:
    - Mantenha os dados seguros ao fazer uma cópia de segurança das VMs do Azure através do serviço Azure Backup. [Saiba mais](../backup/quick-backup-vm-portal.md).
    - Mantenha as cargas de trabalho em execução e continuamente disponíveis ao replicar VMs do Azure para uma região secundária com o Site Recovery. [Saiba mais](../site-recovery/azure-to-azure-tutorial-enable-replication.md).
- Para uma maior segurança:
    - Bloqueie e limite o acesso ao tráfego de entrada com o [Azure Security Center - Mesmo na administração do tempo](https://docs.microsoft.com/azure/security-center/security-center-just-in-time).
    - Restrinja o tráfego de rede para os pontos finais de gestão com os [Grupos de Segurança de Rede](https://docs.microsoft.com/azure/virtual-network/security-overview).
    - Implemente o [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview) para ajudar a proteger discos e a manter os dados protegidos contra roubo e acesso não autorizado.
    - Leia mais sobre como [proteger recursos de IaaS](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/) e aceda ao [Centro de Segurança do Azure](https://azure.microsoft.com/services/security-center/).
- Para monitorização e gestão:
-  Considere implementar o [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/overview) para monitorizar a utilização e as despesas do recurso.


## <a name="next-steps"></a>Passos seguintes

Investigue a viagem de [migração](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/migrate) em nuvem no Quadro de Adoção de Nuvens Azure.
