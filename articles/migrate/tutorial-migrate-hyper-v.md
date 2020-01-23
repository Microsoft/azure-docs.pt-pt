---
title: Migrar VMs do Hyper-V para o Azure com migração de servidor de migrações para Azure
description: Saiba como migrar VMs do Hyper-V locais para o Azure com migração de servidor de migrações para Azure
ms.topic: tutorial
ms.date: 11/18/2019
ms.custom: MVC
ms.openlocfilehash: a321c3e731a6649f0831f7d515f1c464b311c9ac
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76545913"
---
# <a name="migrate-hyper-v-vms-to-azure"></a>Migrar VMs do Hyper-V para o Azure 

Este artigo mostra como migrar VMs do Hyper-V locais para o Azure, usando a migração sem agente com a ferramenta migrações para Azure: Server Migration.

As [migrações para Azure](migrate-services-overview.md) fornecem um hub central para acompanhar a descoberta, avaliação e migração de seus aplicativos e cargas de trabalho locais e VMs de nuvem privada/pública, para o Azure. O Hub fornece ferramentas de migração do Azure para avaliação e migração, bem como ofertas de ISVs (fornecedores independentes de software) de terceiros.

Este tutorial é o terceiro de uma série que demonstra como avaliar e migrar o Hyper-V para o Azure usando a migração e avaliação do servidor de migrações para Azure. Neste tutorial, ficará a saber como:


> [!div class="checklist"]
> * Preparar o Azure e seu ambiente do Hyper-V local
> * Configure o ambiente de origem e implante um dispositivo de replicação.
> * Configure o ambiente de destino.
> * Ative a replicação.
> * Execute uma migração de teste para verificar se tudo está funcionando conforme o esperado.
> * Execute uma migração completa para o Azure.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.


## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, tem de:

1. [Examine](hyper-v-migration-architecture.md) a arquitetura de migração do Hyper-V.
2. [Conclua o primeiro tutorial](tutorial-prepare-hyper-v.md) desta série para configurar o Azure e o Hyper-V para migração. No primeiro tutorial, você:
    - [Prepare o Azure](tutorial-prepare-hyper-v.md#prepare-azure) para migração.
    - [Prepare o ambiente local](tutorial-prepare-hyper-v.md#prepare-for-hyper-v-migration) para migração.
3. Recomendamos que você tente avaliar as VMs do Hyper-V usando migrações para Azure: avaliação de servidor, antes de migrá-las para o Azure. Para fazer isso, [conclua o segundo tutorial](tutorial-assess-hyper-v.md) desta série. Embora seja recomendável experimentar uma avaliação, você não precisa executar uma avaliação antes de migrar as VMs.
4. Verifique se sua conta do Azure é atribuída à função colaborador da máquina virtual, para que você tenha permissões para:

    - Criar uma VM no grupo de recursos selecionado.
    - Criar uma VM na rede virtual selecionada.
    - Gravar em um disco gerenciado do Azure.
5. [Configure uma rede do Azure](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Quando você migra para o Azure, as VMs do Azure criadas são unidas a uma rede do Azure que você especifica ao configurar a migração.


## <a name="add-the-azure-migrate-server-migration-tool"></a>Adicionar a ferramenta de migração de servidor de migrações para Azure

Se você não seguir o segundo tutorial para avaliar as VMs do Hyper-V, precisará [seguir estas instruções](how-to-add-tool-first-time.md) para configurar um projeto de migrações para Azure e adicionar a ferramenta de migração do servidor de migrações para o projeto.

Se você seguiu o segundo tutorial e já tiver um projeto de migrações para Azure, adicione a ferramenta migrações para Azure: servidor de migração da seguinte maneira:

1. No projeto migrações para Azure, clique em **visão geral**. 
2. Em **servidores de descoberta, avaliação e migração**, clique em **avaliar e migrar servidores**.
3. Em **ferramentas de migração**, selecione **clique aqui para adicionar uma ferramenta de migração quando você estiver pronto para migrar**.

    ![Selecionar ferramenta](./media/tutorial-migrate-hyper-v/select-migration-tool.png)

4. Na lista de ferramentas, selecione **migrações para Azure: migração de servidor** > **Adicionar ferramenta**

    ![Ferramenta de migração de servidor](./media/tutorial-migrate-hyper-v/server-migration-tool.png)


## <a name="set-up-the-azure-migrate-appliance"></a>Configurar o dispositivo de migrações para Azure

A migração de servidor de migrações para Azure executa um dispositivo de VM leve do Hyper-V.

- O dispositivo executa a descoberta de VM e envia metadados de VM e dados de desempenho para migração de servidor de migrações para Azure.
- O dispositivo também é usado pela ferramenta migrações para Azure: Server Assessment para migrar VMs do Hyper-V para o Azure.

Para configurar o dispositivo:
- Se você seguiu o segundo tutorial para avaliar as VMs do Hyper-V, você já configurou o dispositivo durante esse tutorial e não precisará fazê-lo novamente.
- Se você não seguir esse tutorial, precisará configurar o dispositivo agora. Para fazer isso, você: 

    - Baixe um VHD do Hyper-V compactado da portal do Azure.
    - Crie o dispositivo e verifique se ele pode se conectar à avaliação do servidor de migrações para Azure. 
    - Configure o dispositivo pela primeira vez e registre-o com o projeto de migrações para Azure.

    Siga as instruções detalhadas neste [artigo](how-to-set-up-appliance-hyper-v.md) para configurar o dispositivo.

## <a name="prepare-hyper-v-hosts"></a>Preparar hosts Hyper-V

1. No projeto de migrações para Azure > **servidores**, em **migrações para Azure: migração de servidor**, clique em **descobrir**.
2. Em **descobrir computadores** > **são seus computadores virtualizados?** , selecione **Sim, com o Hyper-V**.
3. Em **região de destino**, selecione a região do Azure para a qual você deseja migrar os computadores.
6. Selecione **confirmar que a região de destino para migração é Region-Name**.
7. Clique em **criar recursos**. Isso cria um cofre de Azure Site Recovery em segundo plano.
    - Se você já configurou a migração com a migração de servidor de migrações para Azure, essa opção não aparecerá, pois os recursos foram configurados anteriormente.
    - Você não pode alterar a região de destino deste projeto depois de clicar nesse botão.
    - Todas as migrações subsequentes são para essa região.
    
8. Em **preparar servidores de host Hyper-v**, baixe o provedor de replicação do Hyper-v e o arquivo de chave de registro.
    - A chave de registro é necessária para registrar o host Hyper-V com migração de servidor de migrações para Azure.
    - A chave é válida durante cinco dias depois de gerá-la.

    ![Baixar o provedor e a chave](./media/tutorial-migrate-hyper-v/download-provider-hyper-v.png)

4. Copie o arquivo de configuração do provedor e o arquivo de chave de registro para cada host do Hyper-V (ou nó de cluster) executando VMs que você deseja replicar.
5. Execute o arquivo de instalação do provedor em cada host, conforme descrito no próximo procedimento.
6. Depois de instalar o provedor em hosts, em **descobrir máquinas**, clique em **finalizar registro**.

    ![Finalizar registro](./media/tutorial-migrate-hyper-v/finalize-registration.png)

Pode levar até 15 minutos após a finalização do registro até que as VMs descobertas sejam exibidas na migração do servidor de migrações para Azure. À medida que as VMs são descobertas, a contagem de **servidores descobertos** aumenta.

![Servidores descobertos](./media/tutorial-migrate-hyper-v/discovered-servers.png)

### <a name="register-hyper-v-hosts"></a>Registrar hosts Hyper-V

Instale o arquivo de instalação baixado (AzureSiteRecoveryProvider. exe) em cada host do Hyper-V relevante.

1. Execute o arquivo de instalação do provedor em cada host ou nó de cluster.
2. No assistente de instalação do provedor > **Microsoft Update**, opte por usar Microsoft Update para verificar se há atualizações do provedor.
3. Em **instalação**, aceite o local de instalação padrão para o provedor e o agente e selecione **instalar**.
4. Após a instalação, no assistente de registro > **configurações do cofre**, selecione **procurar**e, em arquivo de **chave**, selecione o arquivo de chave do cofre que você baixou.
5. Em **configurações de proxy**, especifique como o provedor em execução no host se conecta à Internet.
    - Se o dispositivo estiver localizado atrás de um servidor proxy, você precisará especificar as configurações de proxy.
    - Especifique o nome do proxy como **http://ip-address** ou **http://FQDN** . Não há suporte para servidores proxy HTTPS.
   

6. Verifique se o provedor pode alcançar as [URLs necessárias](migrate-support-matrix-hyper-v-migration.md#hyper-v-hosts).
7. Em **registro**, depois que o host for registrado, clique em **concluir**.

## <a name="replicate-hyper-v-vms"></a>Replicar VMs do Hyper-V

Com a descoberta concluída, você pode começar a replicação de VMs do Hyper-V para o Azure.

> [!NOTE]
> Você pode replicar até 10 computadores juntos. Se você precisar replicar mais, replique-os simultaneamente em lotes de 10.

1. No projeto de migração do Azure > **servidores**, **migrações para Azure: migração de servidor**, clique em **replicar**.
2. Em **replicate**, > **configurações de origem** > **são suas máquinas virtualizadas?** , selecione **Sim, com o Hyper-V**. Em seguida, clique em **Avançar: máquinas virtuais**.
3. Em **Máquinas virtuais**, selecione aquelas que quer replicar.
    - Se tiver executado uma avaliação para as VMs, poderá aplicar as recomendações de dimensionamento de VMs e tipo de discos (premium/standard) nos resultados da avaliação. Para tal, em **Importar definições de migração de uma avaliação do Azure Migrate?** , selecione a opção **Sim**.
    - Se não tiver executado uma avaliação ou não quiser utilizar as definições de avaliação, selecione as opções **Não**.
    - Se tiver selecionado para utilizar a avaliação, selecione o grupo de VMs e o nome da avaliação.

        ![Selecionar avaliação](./media/tutorial-migrate-hyper-v/select-assessment.png)

4. Em **Máquinas virtuais**, procure VMs conforme necessário e marque cada VM que quer migrar. Em seguida, clique em **Avançar: configurações de destino**.

    ![Selecionar VMs](./media/tutorial-migrate-hyper-v/select-vms.png)

5. Em **configurações de destino**, selecione a região de destino para a qual você migrará, a assinatura e o grupo de recursos no qual as VMs do Azure residirão após a migração.
7. Em **conta de armazenamento de replicação**, selecione a conta de armazenamento do Azure na qual os dados replicados serão armazenados no Azure.
8. **Rede virtual**, selecione a VNet/sub-rede do Azure na qual as VMs do Azure serão Unidas após a migração.
9. Em **Benefício Híbrido do Azure**:

    - Selecione **Não** se não quiser aplicar o Benefício Híbrido do Azure. Clique depois em **Seguinte**.
    - Selecione **Sim** se tiver computadores Windows Server abrangidos com subscrições ativas do Software Assurance ou do Windows Server e quiser aplicar o benefício aos computadores que está a migrar. Clique depois em **Seguinte**.

    ![Configurações de destino](./media/tutorial-migrate-hyper-v/target-settings.png)

10. Em **Computação**, analise o nome, o tamanho, o tipo de disco do SO e o conjunto de disponibilidade das VMs. As VMs devem estar em conformidade com os [requisitos do Azure](migrate-support-matrix-hyper-v-migration.md#azure-vm-requirements).

    - **Tamanho da VM**: se você estiver usando recomendações de avaliação, a lista suspensa tamanho da VM conterá o tamanho recomendado. Caso contrário, o Azure Migrate escolhe um tamanho com base na correspondência mais próxima na subscrição do Azure. Como alternativa, escolha um tamanho manual em **Tamanho de VMs do Azure**. 
    - **Disco do so**: especifique o disco do sistema operacional (inicialização) para a VM. O disco do SO é o disco que possui o carregador e o instalador do sistema operativo. 
    - **Conjunto de disponibilidade**: se a VM deve estar em um conjunto de disponibilidade do Azure após a migração, especifique o conjunto. O conjunto deve estar no grupo de recursos de destino especificado para a migração.

    ![Configurações de computação da VM](./media/tutorial-migrate-hyper-v/compute-settings.png)

11. Em **Discos**, especifique se os discos das VMs devem ser replicados para o Azure e selecione o tipo de disco (discos geridos SSD/HDD standard ou premium) no Azure. Clique depois em **Seguinte**.
    - Pode excluir discos da replicação.
    - Se excluir discos, estes não estarão presentes na VM do Azure após a migração. 

    ![Discos](./media/tutorial-migrate-hyper-v/disks.png)

10. Em **Analisar e iniciar a replicação**, analise as definições e clique em **Replicar** para iniciar a replicação inicial para os servidores.

> [!NOTE]
> Pode atualizar as definições de replicação a qualquer momento antes do início da replicação, em **Gerir** > **Replicar computadores**. As definições não podem ser alteradas após o início da replicação.

## <a name="provisioning-for-the-first-time"></a>Provisionamento pela primeira vez

Se esta for a primeira VM que você está replicando no projeto de migrações para Azure, migre do Azure: a migração de servidor provisiona automaticamente esses recursos no mesmo grupo de recursos que o projeto.

- **Barramento de serviço**: migrações para Azure: a migração de servidor usa o barramento de serviço para enviar mensagens de orquestração de replicação para o dispositivo.
- **Conta de armazenamento de gateway**: migrações para Azure: a migração de servidor usa a conta de armazenamento de gateway para armazenar informações de estado sobre as VMs que estão sendo replicadas.
- **Conta de armazenamento de log**: o dispositivo de migrações para Azure carrega logs de replicação para VMs em uma conta de armazenamento de log. As migrações para Azure aplicam as informações de replicação aos discos gerenciados por réplica.
- **Cofre de chaves**: o dispositivo de migrações para Azure usa o cofre de chaves para gerenciar cadeias de conexão para o barramento de serviço e chaves de acesso para as contas de armazenamento usadas na replicação. Você deve ter configurado as permissões que o cofre de chaves precisa para acessar a conta de armazenamento quando você [preparou o Azure](tutorial-prepare-hyper-v.md#prepare-azure) para avaliação e migração de VMs do Hyper-V. 


## <a name="track-and-monitor"></a>Acompanhar e monitorar


- Quando você clica em **replicar** , um trabalho iniciar replicação é iniciado. 
- Quando o trabalho iniciar replicação é concluído com êxito, os computadores começam sua replicação inicial para o Azure.
- Após a conclusão da replicação inicial, a replicação delta começa. Alterações incrementais em discos locais são replicadas periodicamente para o Azure.

Você pode acompanhar o status do trabalho nas notificações do Portal.

Você pode monitorar o status de replicação clicando em **replicar servidores** no **Azure migrar: migração de servidor**.
![Monitorar a replicação](./media/tutorial-migrate-hyper-v/replicating-servers.png)



## <a name="run-a-test-migration"></a>Executar uma migração de teste


Quando a replicação delta é iniciada, você pode executar uma migração de teste para as VMs antes de executar uma migração completa para o Azure. É altamente recomendável que você faça isso pelo menos uma vez para cada computador, antes de migrá-lo.

- A execução de uma migração de teste verifica se a migração funcionará conforme o esperado, sem afetar os computadores locais, que permanecem operacionais e continuam replicando. 
- A migração de teste simula a migração criando uma VM do Azure usando dados replicados (geralmente migrando para uma VNet do Azure de não produção em sua assinatura do Azure).
- Você pode usar a VM do Azure de teste replicado para validar a migração, executar testes de aplicativo e resolver problemas antes da migração completa.

Faça uma migração de teste da seguinte maneira:


1. Em **metas de migração** > **servidores** > **migrações para Azure: migração de servidor**, clique em **testar servidores migrados**.

     ![Testar servidores migrados](./media/tutorial-migrate-hyper-v/test-migrated-servers.png)

2. Clique com o botão direito do rato na VM a testar e, em seguida, clique em **Testar migração**.

    ![Testar migração](./media/tutorial-migrate-hyper-v/test-migrate.png)

3. Em **migração de teste**, selecione a rede virtual do Azure na qual a VM do Azure estará localizada após a migração. Recomendamos que você use uma rede virtual que não seja de produção.
4. A tarefa **Teste de migração** é iniciada. Monitorize a tarefa nas notificações do portal.
5. Após a conclusão da migração, veja a VM do Azure migrada em **Máquinas Virtuais** no portal do Azure. O nome do computador tem um sufixo **-Test**.
6. Após terminar o teste, clique com o botão direito do rato na VM do Azure em **Replicar computadores** e clique em **Limpar teste de migração**.

    ![Limpar a migração](./media/tutorial-migrate-hyper-v/clean-up.png)


## <a name="migrate-vms"></a>Migrar VMs

Depois de verificar se a migração de teste funciona conforme o esperado, você pode migrar os computadores locais.

1. No projeto de migração do Azure > **servidores** > **migrações para Azure: migração de servidor**, clique em **replicar servidores**.

    ![Replicar servidores](./media/tutorial-migrate-hyper-v/replicate-servers.png)

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


## <a name="next-steps"></a>Passos seguintes

Investigue a [jornada de migração na nuvem](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/migrate) na estrutura de adoção de nuvem do Azure.
