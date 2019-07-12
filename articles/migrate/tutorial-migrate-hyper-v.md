---
title: Migrar VMs de Hyper-V no local para o Azure com o Azure Migrate a migração do servidor | Documentos da Microsoft
description: Este artigo descreve como migrar VMs de Hyper-V no local para o Azure com a migração do servidor de migrar do Azure
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 07/09/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 4a88e9dddd492d5c24698bcde8c3a1fd942eaf66
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/12/2019
ms.locfileid: "67854198"
---
# <a name="migrate-hyper-v-vms-to-azure"></a>Migrar VMs do Hyper-V para o Azure 

Este artigo mostra como migrar VMs de Hyper-V no local para o Azure, utilizando a migração sem agente com a ferramenta de migração do servidor de migrar do Azure.

[O Azure Migrate](migrate-services-overview.md) fornece um concentrador central para controlar a deteção, avaliação e migração de aplicações no local e cargas de trabalho e pública/privada na cloud VMs, para o Azure. O hub fornece ferramentas do Azure Migrate para avaliação e migração, bem como ofertas de (ISV fornecedor) de terceiros independentes de software.

Este tutorial é o terceiro de uma série que demonstra como avaliar e migrar o Hyper-V para o Azure com o Azure Migrate servidor avaliação e migração. Neste tutorial, ficará a saber como:


> [!div class="checklist"]
> * Preparar o Azure e o seu ambiente de Hyper-V no local
> * Configurar o ambiente de origem e implementar uma aplicação de replicação.
> * Configurar o environmen de destino....
> * Ative a replicação.
> * Execute uma migração de teste para se certificar de que está tudo a funcionar conforme esperado.
> * Execute uma migração completa para o Azure.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.


## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, tem de:

1. [Revisão](migrate-architecture.md) a arquitetura de migração do Hyper-V.
2. [Concluir o tutorial primeiro](tutorial-prepare-hyper-v.md) desta série para configurar o Azure e Hyper-V para migração. No primeiro tutorial,:
    - [Preparar o Azure](tutorial-prepare-hyper-v.md#prepare-azure) para migração.
    - [Preparar o ambiente no local](tutorial-prepare-hyper-v.md#prepare-for-hyper-v-migration) para migração.
3. Recomendamos que experimente a avaliar as VMs de Hyper-V, com a avaliação migrar do servidor do Azure, antes de migrar para o Azure. Para fazer isso, [concluir o tutorial segundo](tutorial-assess-hyper-v.md) nesta série. Embora seja recomendável que experimentar uma avaliação, não precisa de executar uma avaliação antes de migrar as VMs.
4. Certifique-se de que a sua conta do Azure é atribuída a função de contribuinte de Máquina Virtual, para que não tem permissões para:

    - Criar uma VM no grupo de recursos selecionado.
    - Criar uma VM na rede virtual selecionada.
    - Escrever a Azure disco gerido.   
5. [Configurar uma rede do Azure](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Ao migrar para o Azure, as VMs do Azure criado estão associadas a uma rede do Azure que especificar quando configurar a migração.


## <a name="add-the-azure-migrate-server-migration-tool"></a>Adicionar a ferramenta de migração do servidor de migrar do Azure

Se não seguiu o tutorial segundo para avaliar as VMs de Hyper-V, terá [siga estas instruções](how-to-add-tool-first-time.md) para configurar um projeto do Azure Migrate e adicionar a ferramenta de migração do servidor de migrar do Azure para o projeto.

Se seguiu o segundo tutorial e já tiver uma configuração de projeto do Azure Migrate, adicione a ferramenta de migração do servidor de migrar do Azure da seguinte forma:

1. No projeto do Azure Migrate, clique em **descrição geral**. 
2. Na **Descubra, avalie e servidores de migração**, clique em **avaliar e migrar servidores de**.
3. Na **ferramentas de migração**, selecione **clique aqui para adicionar uma ferramenta de migração quando estiver pronto para migrar**.

    ![Selecionar ferramenta](./media/tutorial-migrate-hyper-v/select-migration-tool.png)

4. Na lista de ferramentas, selecione **Azure Migrate: Migração do servidor** > **ferramenta Add**

    ![Ferramenta de migração do servidor](./media/tutorial-migrate-hyper-v/server-migration-tool.png)


## <a name="set-up-the-azure-migrate-appliance"></a>Configurar a aplicação do Azure Migrate

Migração de servidor migrar do Azure é executado uma simples aplicação de VM de Hyper-V.

- A aplicação executa a deteção de VMS e envia dados de metadados e o desempenho da VM para a migração do servidor de migrar do Azure.
- A aplicação da mesma também é utilizada pela ferramenta de avaliação do servidor de migrar do Azure.

Para configurar a aplicação:
- Se seguiu o tutorial segundo para avaliar as VMs de Hyper-V, já configurou a aplicação durante esse tutorial.
- Se não siga este tutorial, terá de configurar a aplicação agora. Para fazer isso,: 

    - Transferir um VHD de Hyper-V comprimido do portal do Azure.
    - Criar a aplicação e verifique se pode ligar a avaliação do servidor de migrar do Azure. 
    - Configurar a aplicação pela primeira vez e registrá-la com o projeto do Azure Migrate.

    Siga as instruções em [este artigo](how-to-set-up-appliance-hyper-v.md) para configurar a aplicação.

## <a name="prepare-hyper-v-hosts"></a>Preparar anfitriões Hyper-V

1. No projeto do Azure Migrate > **servidores**, na **Azure Migrate: Migração do servidor**, clique em **detetar**.
2. Na **detetar máquinas** > **são as suas máquinas virtualizadas?** , selecione **Sim, com o Hyper-V**.
3. Na **região de destino**, selecione a região do Azure para o qual pretende migrar as máquinas.
6. Selecione **confirmar que a região de destino para a migração é o nome da região**.
7. Clique em **criar recursos**. Esta ação cria um cofre de recuperação de sites do Azure em segundo plano.
    - Se já tiver configurado a migração com o Azure Migrate migração do servidor, esta opção não aparecer uma vez que os recursos foram configurados anteriormente.
    - Não é possível alterar a região de destino para este projeto depois de clicar neste botão.
    - Todas as migrações subsequentes são nesta região.
    
8. Na **servidores de anfitrião do Hyper-V preparar**, transferir o fornecedor de replicação de Hyper-V e o ficheiro de chave de registo.
    - A chave de registo é necessária para registar o anfitrião de Hyper-V com migração do servidor de migrar do Azure.
    - A chave é válida durante cinco dias depois de gerá-la.

    ![Transferir o fornecedor e a chave](./media/tutorial-migrate-hyper-v/download-provider-hyper-v.png)

4. Copie o ficheiro de configuração do fornecedor e o ficheiro de chave de registo para cada anfitrião Hyper-V (ou nó de cluster) em execução VMs que pretende replicar.
5. Execute o fornecedor de ficheiro de configuração em cada anfitrião, conforme descrito no procedimento seguinte.
6. Depois de instalar o fornecedor nos anfitriões, nas **detetar máquinas**, clique em **finalizar o registo**.

    ![Finalizar o registo](./media/tutorial-migrate-hyper-v/finalize-registration.png)

Pode demorar até 15 minutos após a finalizar o registo até que as VMs detetadas apareçam na migração do servidor de migrar do Azure. À medida que são descobertas VMs, o **detetados servidores** contagem aumenta.

![Servidores detetados](./media/tutorial-migrate-hyper-v/discovered-servers.png)

### <a name="register-hyper-v-hosts"></a>Registe-se os anfitriões de Hyper-V

Instale o ficheiro de configuração transferido (AzureSiteRecoveryProvider.exe) em cada anfitrião de Hyper-V relevante.

1. Execute o fornecedor de ficheiro de configuração em cada nó de anfitrião ou cluster.
2. No Assistente de configuração de fornecedor > **Microsoft Update**, opte por utilizar o Microsoft Update para procurar atualizações do fornecedor.
3. Na **instalação**, aceite a localização de instalação predefinida para o fornecedor e o agente e selecione **instalar**.
4. Após a instalação, no Assistente de registo > **definições do cofre**, selecione **procurar**e, na **ficheiro de chave**, selecione o ficheiro de chave de cofre que transferiu.
5. Na **definições de Proxy**, especifique a forma como o fornecedor em execução no anfitrião estabelece ligação à internet.
    - Se a aplicação estiver localizada atrás de um servidor proxy, tem de especificar as definições de proxy.
    - Especifique o nome do proxy **http://ip-address** , ou **http://FQDN** . Servidores de proxy HTTPS não são suportados.
   

6. Certifique-se de que o fornecedor de contactar o [URLs necessários](migrate-support-matrix-hyper-v.md#migration-hyper-v-host-url-access).
7. Na **Registro**, após o anfitrião está registado, clique em **concluir**.

## <a name="replicate-hyper-v-vms"></a>Replicar VMs Hyper-V

Com a deteção foi concluída, pode começar a replicação de VMs de Hyper-V para o Azure.

1. No projeto do Azure Migrate > **servidores**, **Azure Migrate: Migração do servidor**, clique em **replicar**.
2. Na **replicar**, > **definições da origem** > **são as suas máquinas virtualizadas?** , selecione **Sim, com o Hyper-V**. Em seguida, clique em **seguinte: Máquinas virtuais**.
3. Na **máquinas virtuais**, selecione as máquinas que pretende replicar.
    - Se executar uma avaliação para as VMs, pode aplicar recomendações de (premium ou standard) de tipo de disco nos resultados da avaliação e de dimensionamento de VM. Para fazê-lo, em **importar definições de migração de uma avaliação do Azure Migrate?** , selecione a **Sim** opção.
    - Se não executou uma avaliação ou que não pretende utilizar as definições de avaliação, selecione o **não** opções.
    - Se tiver selecionado utilizar a avaliação, selecione o grupo de VMS e o nome de avaliação.

        ![Selecione a avaliação](./media/tutorial-migrate-hyper-v/select-assessment.png)

4. Na **máquinas virtuais**, procure por VMs, conforme necessário e verificar cada VM que pretende migrar. Em seguida, clique em **seguinte: Definições de destino**.

    ![Selecionar VMs](./media/tutorial-migrate-hyper-v/select-vms.png)

5. Na **definições de destino**, selecione a região de destino para o qual irá migrar, a subscrição e o grupo de recursos no qual as VMs do Azure irá residir após a migração.
7. Na **conta de armazenamento de replicação**, selecione a conta de armazenamento do Azure na qual os dados replicados serão armazenados no Azure.
8. **Rede virtual**, selecione a sub-rede/VNet do Azure para o qual as VMs do Azure vão ser associadas após a migração.
9. Na **benefício híbrido do Azure**:

    - Selecione **não** se não pretender aplicar o benefício híbrido do Azure. Clique depois em **Seguinte**.
    - Selecione **Sim** se tiver máquinas do Windows Server que são abrangidas por subscrições ativas do Software Assurance ou o Windows Server e que pretende aplicar o benefício para as máquinas que estiver a migrar. Clique depois em **Seguinte**.

    ![Definições de destino](./media/tutorial-migrate-hyper-v/target-settings.png)

10. Na **computação**, reveja o nome da VM, o tamanho, o tipo de disco do SO e o conjunto de disponibilidade. As VMs têm de cumprir [requisitos do Azure](migrate-support-matrix-vmware.md#agentless-migration-vmware-vm-requirements).

    - **Tamanho da VM**: Se estiver a utilizar as recomendações de avaliação, a lista pendente de tamanho VM irá conter o tamanho recomendado. Caso contrário, o Azure Migrate escolhe um tamanho com base na correspondência mais próxima na subscrição do Azure. Em alternativa, escolha um tamanho manual em **tamanho de VM do Azure**. 
    - **Disco do SO**: Especifique o disco do SO (arranque) para a VM. O disco do SO é o disco que tenha o carregador de inicialização do sistema operativo e o instalador. 
    - **Conjunto de disponibilidade**: Se a VM num conjunto após a migração de disponibilidade do Azure, especifique o conjunto. O conjunto tem de estar no grupo de recursos de destino especificado para a migração.

    ![Definições de computação VM](./media/tutorial-migrate-hyper-v/compute-settings.png)

11. Na **discos**, especifique se os discos da VM devem ser replicados para o Azure e selecione o tipo de disco (standard SSD/HDD ou discos geridos premium) no Azure. Clique depois em **Seguinte**.
    - Pode excluir discos da replicação.
    - Se excluir discos, não está presente na VM do Azure após a migração. 

    ![Discos](./media/tutorial-migrate-hyper-v/disks.png)

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
- Depois de concluída a replicação inicial, começa a replicação de diferenças. Periodicamente, as alterações incrementais para discos locais são replicadas para o Azure.

Pode controlar o estado da tarefa nas notificações do portal.

Pode monitorizar o estado de replicação ao clicar em **replicar servidores** no **Azure Migrate: Migração do servidor**.
![Monitor de replicação](./media/tutorial-migrate-hyper-v/replicating-servers.png)



## <a name="run-a-test-migration"></a>Executar uma migração de teste


Quando a replicação de diferenças é iniciada, pode executar uma migração de teste para as VMs, antes de executar uma migração completa para o Azure. É altamente recomendável que fazer isso, pelo menos, uma vez para cada máquina, antes de migrá-lo.

- Executar uma migração de teste verifica se a migração irá funcionar conforme esperado, sem afetar as máquinas no local, que permanecerão operacional e continuar a replicar. 
- Migração de teste simula a migração através da criação de VM do Azure com os dados replicados (normalmente, migrar para uma VNet de não produção na sua subscrição do Azure).
- Pode utilizar o teste replicado VM do Azure para validar a migração, realizar o teste de aplicações e resolver quaisquer problemas antes da migração completa.

Fazer uma migração de teste da seguinte forma:


1. Na **objetivos de migração** > **servidores** > **Azure Migrate: Migração do servidor**, clique em **teste migrados servidores**.

     ![Testar servidores migrados](./media/tutorial-migrate-hyper-v/test-migrated-servers.png)

2. Com o botão direito a VM para testar e clique em **teste migrar**.

    ![Migração de teste](./media/tutorial-migrate-hyper-v/test-migrate.png)

3. Na **migração de teste**, selecione a VNet do Azure na qual a VM do Azure estarão localizada após a migração. Recomendamos que utilize uma VNet de não produção.
4. O **migração de teste** da tarefa é iniciada. Monitorize a tarefa nas notificações do portal.
5. Depois de concluída a migração, ver a VM migrada do Azure no **máquinas virtuais** no portal do Azure. O nome da máquina tem um sufixo **-teste**.
6. Depois do teste é concluído, clique com botão direito a VM do Azure no **replicar máquinas**e clique em **limpar a migração de teste**.

    ![Limpar a migração](./media/tutorial-migrate-hyper-v/clean-up.png)


## <a name="migrate-vms"></a>Migrar VMs

Depois de verificar se a migração de teste funciona conforme esperado, pode migrar as máquinas no local.

1. No projeto do Azure Migrate > **servidores** > **Azure Migrate: Migração do servidor**, clique em **replicar servidores**.

    ![Replicar servidores](./media/tutorial-migrate-hyper-v/replicate-servers.png)

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
