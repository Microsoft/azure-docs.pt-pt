---
title: Para migrar VMs no local VMware para o Azure com a migração de servidor migrar sem agente do Azure | Documentos da Microsoft
description: Descreve como executar e sem agente migração de VMs de VMware no local para o Azure com o Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 07/08/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 3510c0505a5a3c1353642baf5060a83d13fdd43a
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67809128"
---
# <a name="migrate-vmware-vms-to-azure-agentless"></a>Migrar VMs de VMware para o Azure (sem agente)

Este artigo mostra como migrar VMs de VMware no local para o Azure, utilizando a migração sem agente com a ferramenta de migração do servidor de migrar do Azure.

[O Azure Migrate](migrate-services-overview.md) fornece um concentrador central para controlar a deteção, avaliação e migração de aplicações no local e cargas de trabalho e instâncias de VM do AWS/GCP, para o Azure. O hub fornece ferramentas do Azure Migrate para avaliação e migração, bem como ofertas de (ISV fornecedor) de terceiros independentes de software.

Este tutorial é o terceiro de uma série que demonstra como avaliar e migrar VMs do VMware para o Azure com o Azure Migrate servidor avaliação e migração. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Prepare VMs para a migração.
> * Adicione a ferramenta de migração de servidor de migração do Azure.
> * Detete VMs que pretende migrar.
> * Inicie a replicação de VMs.
> * Execute uma migração de teste para se certificar de que está tudo a funcionar conforme esperado.
> * Execute uma migração de VM completa.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.

## <a name="migration-methods"></a>Métodos de migração

É possível migrar VMs de VMware para o Azure com a ferramenta de migração do servidor de migrar do Azure. Essa ferramenta oferece duas opções para a migração de VM de VMware:

- Migração usando a replicação sem agente. Migre VMs sem precisar instalar nada nos mesmos.
- Migração com um agente de replicação. Instale um agente na VM para replicação.

Para decidir se pretende utilizar a migração sem agente ou com base em agente, consulte estes artigos:

- [Saiba como](server-migrate-overview.md) migração sem agente funciona, e [reveja as limitações](server-migrate-overview.md#agentless-migration-limitations).
- [Leia este artigo](tutorial-migrate-vmware-agent.md) se desejar usar o método baseado em agente.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, tem de:

1. [Compreender](migrate-architecture.md) a arquitetura de migração do VMware.
2. [Concluir o tutorial primeiro](tutorial-prepare-vmware.md) desta série para configurar o Azure e VMware para migração. Especificamente, neste tutorial tem de:
    - [Preparar o Azure](tutorial-prepare-vmware.md#prepare-azure) para migração.
    - [Preparar o ambiente no local](tutorial-prepare-vmware.md#prepare-for-agentless-vmware-migration) para migração.
    
3. Recomendamos que tente avaliar VMs VMware com avaliação de servidor de migrar do Azure antes de migrar para o Azure. Para configurar a avaliação, [concluir o tutorial segundo](tutorial-assess-vmware.md) nesta série. Se não pretender avaliar VMs, pode ignorar este tutorial. Embora seja recomendável que experimentar uma avaliação, mas não tem de executar uma avaliação antes de tentar uma migração.



## <a name="add-the-azure-migrate-server-migration-tool"></a>Adicionar a ferramenta de migração do servidor de migrar do Azure

Se não seguiu o tutorial segundo para avaliar VMware VMs, terá [siga estas instruções](how-to-add-tool-first-time.md) configurar projetos do Azure Migrate e selecione a ferramenta de migração do servidor de migrar do Azure. 

Se seguiu o segundo tutorial e já tiver um projeto do Azure Migrate, configurar, adicione a ferramenta de migração do servidor de migrar do Azure da seguinte forma:

1. No projeto do Azure Migrate, clique em **descrição geral**. 
2. Na **Descubra, avalie e servidores de migração**, clique em **avaliar e migrar servidores de**.

     ![Avaliar e migrar servidores](./media/tutorial-migrate-vmware/assess-migrate.png)

3. Na **ferramentas de migração**, selecione **clique aqui para adicionar uma ferramenta de migração quando estiver pronto para migrar**.

    ![Selecionar ferramenta](./media/tutorial-migrate-vmware/select-migration-tool.png)

4. Na lista de ferramentas, selecione **Azure Migrate: Migração do servidor** > **ferramenta Add**

    ![Ferramenta de migração do servidor](./media/tutorial-migrate-vmware/server-migration-tool.png)

## <a name="set-up-the-azure-migrate-appliance"></a>Configurar a aplicação do Azure Migrate

Migração de servidor migrar do Azure é executado uma simples aplicação de VM de VMware. A aplicação executa a deteção de VMS e envia dados de metadados e o desempenho da VM para a migração do servidor de migrar do Azure. A aplicação da mesma também é utilizada pela ferramenta de avaliação do servidor de migrar do Azure.

Se seguiu o tutorial segundo para avaliar VMware VMs, já configurou a aplicação durante esse tutorial. Se não siga este tutorial, terá de configurar a aplicação agora. Para fazer isso,: 

- Transferir um ficheiro de modelo OVA e importe-o ao vCenter Server.
- Criar a aplicação e verifique se pode ligar a avaliação do servidor de migrar do Azure. 
- Configurar a aplicação pela primeira vez e registrá-la com o projeto do Azure Migrate.

Siga as instruções em [este artigo](how-to-set-up-appliance-vmware.md) para configurar a aplicação.


## <a name="prepare-vms-for-migration"></a>Preparar VMs para a migração

O Azure Migrate requer algumas alterações VM para garantir que as VMs podem ser migradas para o Azure.

- Para algumas [sistemas operativos](server-migrate-overview.md#agentless-migration-limitations), do Azure Migrate torna estas alterações automaticamente.
- Se estiver a migrar uma VM que não tem um destes sistemas operativos, siga as instruções para preparar a VM.
- É importante efetuar estas alterações antes de começar a migração. Se migrar a VM antes de fazer a alteração, a VM pode não efetuar o arranque no Azure.
- As alterações de configuração que fizer nas VMs no local são replicadas para o Azure após a replicação para a VM está ativada. Para garantir que as alterações são replicadas, certifique-se de que migrar para o ponto de recuperação é posterior à hora em que as alterações de configuração efetuadas no local.


### <a name="prepare-windows-server-vms"></a>Preparar VMs do Windows Server

**ação** | **Detalhes** | **Instruções**
--- | --- | ---
Certifique-se de que o volumes do Windows na VM do Azure utilizam as mesmo atribuições da letra da unidade da VM no local. | Configure a política de SAN como Online em todos os. | 1. Entrar para a VM com uma conta de administrador e, abra uma janela de comando.<br/> 2. Tipo **diskpart** para executar o utilitário Diskpart exe.<br/> 3. Tipo de **política de SAN = OnlineAll**<br/> 4. Tipo de saída para deixar o Diskpart e feche a linha de comandos.
Ativar a consola de acesso de série do Azure para a VM do Azure | Isto ajuda na resolução de problemas. Não precisa de reiniciar a VM. A VM do Azure será inicializado com a imagem de disco, e isso é equivalente a um reinício para a nova VM. | Siga [estas instruções](https://docs.microsoft.com/azure/virtual-machines/windows/serial-console#enable-serial-console-in-custom-or-older-images) para ativar.
Instalar a integração do convidado de Hyper-V | Se estiver a migrar máquinas que executem o Windows Server 2003, instale os serviços de integração do convidado de Hyper-V no sistema operacional de VM. | [Saiba mais](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#install-or-update-integration-services).
Ambiente de Trabalho Remoto | Ativar o ambiente de trabalho remoto na VM e verifique que a Firewall do Windows não está a bloquear o acesso de ambiente de trabalho remoto em todos os perfis de rede. | [Saiba mais](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-allow-access).

### <a name="prepare-linux-vms"></a>Preparar as VMs do Linux

**ação** | **Detalhes** 
--- | --- | ---
Instalar serviços de integração do Linux de Hyper-V | A maioria das novas versões das distribuições de Linux tem incluído por predefinição.
Recriar a imagem de inicialização do Linux para conter os controladores necessários do Hyper-V | Isto garante que a VM arrancará no Azure e é necessário apenas em algumas distribuições.
Ativar o registo da consola de série do Azure | Isto ajuda na resolução de problemas. Não precisa de reiniciar a VM. A VM do Azure será inicializado com a imagem de disco, e isso é equivalente a um reinício para a nova VM.<br/> Siga [estas instruções](https://docs.microsoft.com/azure/virtual-machines/linux/serial-console) para ativar.
Atualizar ficheiro de mapa de dispositivo | Atualizar o ficheiro de mapa de dispositivo que tem o nome do dispositivo para associações de volume, ao utilizar identificadores de dispositivo persistente
Atualizar as entradas de fstab | Atualize as entradas para utilizar identificadores de volume persistente.
Remover regra de udev | Remova quaisquer regras de udev reserva-se os nomes das interfaces com base no endereço de mac etc.
Interfaces de rede de atualização | Atualize as interfaces de rede para receber o endereço IP com base no DHCP.
Ativar o ssh | Certifique-se de ssh está ativado e o serviço de sshd está definido para ser iniciado automaticamente na reinicialização.<br/> Certifique-se de que entrada ssh pedidos de ligação não estão bloqueados pela firewall do SO ou regras programável por scripts.

[Siga este artigo](https://docs.microsoft.com/azure/virtual-machines/linux/create-upload-generic) que aborda estes passos para executar uma VM do Linux no Azure e inclui instruções para algumas das distribuições Linux populares.  


## <a name="replicate-vms"></a>Replicar VMs

Com a deteção foi concluída, pode começar a replicação de VMs de VMware para o Azure.

1. No projeto do Azure Migrate > **servidores**, **Azure Migrate: Migração do servidor**, clique em **replicar**.

    ![Replicar VMs](./media/tutorial-migrate-vmware/select-replicate.png)

2. Na **replicar**, > **definições da origem** > **são as suas máquinas virtualizadas?** , selecione **Sim, com o VMware vSphere**.
3. Na **aplicação no local**, selecione o nome da aplicação do Azure Migrate que configurou > **OK**. 

    ![Definições da origem](./media/tutorial-migrate-vmware/source-settings.png)

    - Este passo parte do princípio de que já tiver configurado a uma aplicação quando concluiu o tutorial.
    - Se ainda não tiver configurado uma aplicação, em seguida, siga as instruções em [este artigo](how-to-set-up-appliance-vmware.md).

4. Na **máquinas virtuais**, selecione as máquinas que pretende replicar.
    - Se executar uma avaliação para as VMs, pode aplicar recomendações de (premium ou standard) de tipo de disco nos resultados da avaliação e de dimensionamento de VM. Para fazê-lo, em **importar definições de migração de uma avaliação do Azure Migrate?** , selecione a **Sim** opção.
    - Se não executou uma avaliação ou que não pretende utilizar as definições de avaliação, selecione o **não** opções.
    - Se tiver selecionado utilizar a avaliação, selecione o grupo de VMS e o nome de avaliação.

    ![Selecione a avaliação](./media/tutorial-migrate-vmware/select-assessment.png)

5. Na **máquinas virtuais**, procure por VMs, conforme necessário e verificar cada VM que pretende migrar. Em seguida, clique em **seguinte: Definições de destino**.

    ![Selecionar VMs](./media/tutorial-migrate-vmware/select-vms.png)

6. Na **definições de destino**, selecione a subscrição e região ao qual irá migrar e especifique o grupo de recursos no qual as VMs do Azure irá residir após a migração de destino. Na **rede Virtual**, selecione a sub-rede/VNet do Azure para o qual as VMs do Azure vão ser associadas após a migração.
7. Na **benefício híbrido do Azure**:

    - Selecione **não** se não pretender aplicar o benefício híbrido do Azure. Clique depois em **Seguinte**.
    - Selecione **Sim** se tiver máquinas do Windows Server que são abrangidas por subscrições ativas do Software Assurance ou o Windows Server e que pretende aplicar o benefício para as máquinas que estiver a migrar. Clique depois em **Seguinte**.

    ![Definições de destino](./media/tutorial-migrate-vmware/target-settings.png)

8. Na **computação**, reveja o nome da VM, o tamanho, o tipo de disco do SO e o conjunto de disponibilidade. As VMs têm de cumprir [requisitos do Azure](migrate-support-matrix-vmware.md#agentless-migration-vmware-vm-requirements).

    - **Tamanho da VM**: Se estiver a utilizar as recomendações de avaliação, a lista pendente de tamanho VM irá conter o tamanho recomendado. Caso contrário, o Azure Migrate escolhe um tamanho com base na correspondência mais próxima na subscrição do Azure. Em alternativa, escolha um tamanho manual em **tamanho de VM do Azure**. 
    - **Disco do SO**: Especifique o disco do SO (arranque) para a VM. O disco do SO é o disco que tenha o carregador de inicialização do sistema operativo e o instalador. 
    - **Conjunto de disponibilidade**: Se a VM num conjunto após a migração de disponibilidade do Azure, especifique o conjunto. O conjunto tem de estar no grupo de recursos de destino especificado para a migração.

    ![Definições de computação VM](./media/tutorial-migrate-vmware/compute-settings.png)

9. Na **discos**, especifique se os discos da VM devem ser replicados para o Azure e selecione o tipo de disco (standard SSD/HDD ou discos geridos premium) no Azure. Clique depois em **Seguinte**.
    - Pode excluir discos da replicação.
    - Se excluir discos, não está presente na VM do Azure após a migração. 

    ![Discos](./media/tutorial-migrate-vmware/disks.png)

10. Na **reveja e iniciar a replicação**, reveja as definições e clique em **replicar** para iniciar a replicação inicial para os servidores.

> [!NOTE]
> Pode atualizar as definições de replicação a qualquer momento antes de inicia a replicação, além **Manage** > **replicar máquinas**. Não não possível alterar as definições depois da replicação é iniciada.

### <a name="provisioning-for-the-first-time"></a>Aprovisionamento pela primeira vez

Se esta for a primeira VM está a replicar no projeto do Azure Migrate, migração do servidor de migrar do Azure Aprovisiona automaticamente estes recursos no mesmo grupo de recursos como o projeto.

- **Service bus**: Migração de servidor migrar do Azure utiliza o service bus para enviar mensagens de orquestração de replicação para a aplicação.
- **Conta de armazenamento de gateway**: Migração do servidor utiliza a conta de armazenamento de gateway para armazenar informações de estado sobre as VMs a ser replicada.
- **Conta de armazenamento de registo**: A aplicação do Azure Migrate carrega os registos de replicação para as VMs para uma conta de armazenamento de registo. O Azure Migrate aplica-se as informações de replicação para os discos geridos de réplica.
- **Cofre de chaves**: A aplicação do Azure Migrate utiliza o Cofre de chaves para gerir as cadeias de ligação para o service bus e as chaves de acesso para as contas de armazenamento utilizadas na replicação. Deve ter a configurar as permissões que o Cofre de chaves tem de aceder à conta de armazenamento quando preparou. [Reveja estas permissões](tutorial-prepare-vmware.md#assign-role-assignment-permissions).   


## <a name="track-and-monitor"></a>Controlar e monitorizar


- Quando clica em **replicar** começa uma tarefa de replicação começar. 
- Quando a tarefa de iniciar a replicação é concluída com êxito, as máquinas começam a replicação inicial para o Azure.
- Durante a replicação inicial, é criado um instantâneo VM. Dados de disco a partir do instantâneo são replicados para discos geridos de réplica no Azure.
- Depois de concluída a replicação inicial, começa a replicação de diferenças. As alterações incrementais para discos locais periodicamente são replicadas para os discos de réplica no Azure.

Pode controlar o estado da tarefa nas notificações do portal.

Pode monitorizar o estado de replicação ao clicar em **replicar servidores** no **Azure Migrate: Migração do servidor**.
![Monitor de replicação](./media/tutorial-migrate-vmware/replicating-servers.png)




## <a name="run-a-test-migration"></a>Executar uma migração de teste


Quando a replicação de diferenças é iniciada, pode executar uma migração de teste para as VMs, antes de executar uma migração completa para o Azure. É altamente recomendável que fazer isso, pelo menos, uma vez para cada máquina, antes de migrá-lo.

- Executar uma migração de teste verifica se a migração irá funcionar conforme esperado, sem afetar as máquinas no local, que permanecerão operacional e continuar a replicar. 
- Migração de teste simula a migração através da criação de VM do Azure com os dados replicados (normalmente, migrar para uma VNet de não produção na sua subscrição do Azure).
- Pode utilizar o teste replicado VM do Azure para validar a migração, realizar o teste de aplicações e resolver quaisquer problemas antes da migração completa.

Fazer uma migração de teste da seguinte forma:


1. Na **objetivos de migração** > **servidores** > **Azure Migrate: Migração do servidor**, clique em **teste migrados servidores**.

     ![Testar servidores migrados](./media/tutorial-migrate-vmware/test-migrated-servers.png)

2. Com o botão direito a VM para testar e clique em **teste migrar**.

    ![Migração de teste](./media/tutorial-migrate-vmware/test-migrate.png)

3. Na **migração de teste**, selecione a VNet do Azure na qual a VM do Azure estarão localizada após a migração. Recomendamos que utilize uma VNet de não produção.
4. O **migração de teste** da tarefa é iniciada. Monitorize a tarefa nas notificações do portal.
5. Depois de concluída a migração, ver a VM migrada do Azure no **máquinas virtuais** no portal do Azure. O nome da máquina tem um sufixo **-teste**.
6. Depois do teste é concluído, clique com botão direito a VM do Azure no **replicar máquinas**e clique em **limpar a migração de teste**.

    ![Limpar a migração](./media/tutorial-migrate-vmware/clean-up.png)


## <a name="migrate-vms"></a>Migrar VMs

Depois de verificar se a migração de teste funciona conforme esperado, pode migrar as máquinas no local.

1. No projeto do Azure Migrate > **servidores** > **Azure Migrate: Migração do servidor**, clique em **replicar servidores**.

    ![Replicar servidores](./media/tutorial-migrate-vmware/replicate-servers.png)

2. Na **replicar máquinas**, clique com o botão direito a VM > **migrar**.
3. Na **Migrate** > **encerrar máquinas virtuais e executar uma migração planeada sem perda de dados**, selecione **Sim** > **OK** .
    - Por padrão do Azure Migrate encerra a VM no local e executa uma replicação sob demanda para sincronizar as alterações VM que ocorreu desde a última replicação ocorreu. Isto garante que nenhuma perda de dados.
    - Se não pretender encerrar a VM, selecione **não**
4. Inicia uma tarefa de migração para a VM. Controle o trabalho nas notificações do Azure.
5. Depois da tarefa é concluída, pode ver e gerir a VM a partir da **máquinas virtuais** página.

## <a name="complete-the-migration"></a>Concluir a migração

1. Após a migração estiver concluída, clique com botão direito a VM > **parar migração**. Para a replicação para a máquina no local e limpa as informações de estado de replicação para a VM.
2. Instalar a VM do Azure [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) ou [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) agente nas máquinas migradas.
3. Execute otimizações de aplicação pós-migração, tais como atualizar cadeias de ligação de base de dados e configurações de servidor Web.
4. Execute testes de aplicação final e de aceitação da migração na aplicação migrada em execução no Azure.
5. Transitar o tráfego para a instância de VM do Azure migrada.
6. Remova as VMs no local do seu inventário de VMs locais.
7. Remova as VMs no local das cópias de segurança locais.
8. Atualize qualquer documentação interna para mostrar a nova localização e endereço IP das VMs do Azure. 

## <a name="post-migration-best-practices"></a>Melhores práticas de pós-migração

- Para uma maior resiliência:
    - Mantenha os dados seguros ao fazer uma cópia de segurança das VMs do Azure através do serviço Azure Backup. [Saiba mais](../backup/quick-backup-vm-portal.md).
    - Mantenha as cargas de trabalho em execução e continuamente disponíveis ao replicar VMs do Azure para uma região secundária com o Site Recovery. [Saiba mais](../site-recovery/azure-to-azure-tutorial-enable-replication.md).
- Para uma maior segurança:
    - Bloquear e limitar o acesso de tráfego de entrada com [Centro de segurança do Azure - Just-in administração time](https://docs.microsoft.com/azure/security-center/security-center-just-in-time).
    - Restrinja o tráfego de rede para os pontos finais de gestão com os [Grupos de Segurança de Rede](https://docs.microsoft.com/azure/virtual-network/security-overview).
    - Implemente o [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview) para ajudar a proteger discos e a manter os dados protegidos contra roubo e acesso não autorizado.
    - Leia mais sobre como [proteger recursos de IaaS](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/) e aceda ao [Centro de Segurança do Azure](https://azure.microsoft.com/services/security-center/).
- Para monitorização e gestão:
-  Considere implementar o [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/overview) para monitorizar a utilização e as despesas do recurso.


## <a name="next-steps"></a>Passos Seguintes

Investigar o [percurso de migração da cloud](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/migrate) na estrutura de adoção de Cloud do Azure.
