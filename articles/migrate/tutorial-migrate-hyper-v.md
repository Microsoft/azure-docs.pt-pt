---
title: Migrar VMs hiper-V para Azure com migração do servidor migratório Azure
description: Saiba como migrar no local Hiper-V VMs para Azure com migração de servidores migratórios Azure
ms.topic: tutorial
ms.date: 11/18/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 2b9ed56186649b7644adbd1237ad74af50474cc5
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "80279713"
---
# <a name="migrate-hyper-v-vms-to-azure"></a>Migrar VMs Hyper-V para o Azure 

Este artigo mostra-lhe como migrar no local Hiper-V V MMs para Azure, usando migração sem agente com a ferramenta Migração Azure: Server Migração.

[A Azure Migrate](migrate-services-overview.md) fornece um centro central para acompanhar a descoberta, avaliação e migração das suas aplicações e cargas de trabalho no local, e VMs de nuvem privada/pública, para O Azure. O hub fornece ferramentas azure migrate para avaliação e migração, bem como ofertas de fornecedores de software independentes de terceiros (ISV).

Este tutorial é o terceiro de uma série que demonstra como avaliar e migrar hyper-V para Azure usando a Avaliação e Migração do Servidor Migratório Azure. Neste tutorial, ficará a saber como:


> [!div class="checklist"]
> * Prepare o Azure e o seu ambiente Hyper-V no local
> * Instale o ambiente de origem e implante um aparelho de replicação.
> * Configure o ambiente de destino.
> * Ative a replicação.
> * Faça uma migração de teste para ter certeza de que está tudo funcionando como esperado.
> * Fazer uma migração completa para Azure.

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.


## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, tem de:

1. [Reveja](hyper-v-migration-architecture.md) a arquitetura de migração Hyper-V.
2. [Complete o primeiro tutorial](tutorial-prepare-hyper-v.md) desta série para configurar Azure e Hyper-V para migração. No primeiro tutorial, tu:
    - [Prepare Azure](tutorial-prepare-hyper-v.md#prepare-azure) para a migração.
    - [Prepare o ambiente no local](tutorial-prepare-hyper-v.md#prepare-for-hyper-v-migration) para a migração.
3. Recomendamos que tente avaliar os VMs Hiper-V, utilizando o Azure Migrate: Server Assessment, antes de os migrar para o Azure. Para isso, [complete o segundo tutorial](tutorial-assess-hyper-v.md) desta série. Embora recomendemos que experimente uma avaliação, não precisa fazer uma avaliação antes de migrar VMs.
4. Certifique-se de que a sua conta Azure é atribuída a função de Colaborador de Máquina Virtual, para que tenha permissões para:

    - Criar uma VM no grupo de recursos selecionado.
    - Criar uma VM na rede virtual selecionada.
    - Escreva para um disco gerido pelo Azure.
5. [Criar uma rede Azure.](../virtual-network/manage-virtual-network.md#create-a-virtual-network) Quando se migra para o Azure, os VMs Azure criados juntam-se a uma rede Azure que especifica quando configura a migração.


## <a name="add-the-azure-migrate-server-migration-tool"></a>Adicione a ferramenta de migração do servidor migratório Azure Migrate

Se não seguiu o segundo tutorial para avaliar os VMs Hiper-V, tem de [seguir estas instruções](how-to-add-tool-first-time.md) para configurar um projeto Azure Migrate e adicionar a ferramenta de Avaliação do Servidor De Migração Azure ao projeto.

Se seguiu o segundo tutorial e já tem um projeto Azure Migrate, adicione a ferramenta Azure Migrate: Server Migration da seguinte forma:

1. No projeto Azure Migrate, clique em **Visão Geral**. 
2. No **Discover, avaliar e migrar servidores,** clique em **avaliar e migrar servidores**.
3. Nas **ferramentas de migração,** selecione Clique aqui para adicionar uma ferramenta de **migração quando estiver pronto para migrar**.

    ![Selecione uma ferramenta](./media/tutorial-migrate-hyper-v/select-migration-tool.png)

4. Na lista de ferramentas, selecione **Azure Migrate: Server Migration** > **Add tool**

    ![Ferramenta de migração do servidor](./media/tutorial-migrate-hyper-v/server-migration-tool.png)


## <a name="set-up-the-azure-migrate-appliance"></a>Instale o aparelho Migratório Azure

A Migração do Servidor Migratório Azure gere um agente de software em Anfitriões Hiper-V ou nós de cluster para orquestrar e replicar dados para a Azure Migrate e não requer um aparelho dedicado para migração.

- O aparelho Azure Migrate : Server Assessment realiza a descoberta de VM e envia metadados VM e dados de desempenho para a Migração do Servidor Migratório Migratório Migratório Migratório Migratório Migratório Migratório Migratório Migratório Migratório Migratório Azure Migrate.
- A orquestração de migração e a replicação de dados são tratadas pelo fornecedor de recuperação de sites do Microsoft Azure e pelo agente do Serviço de Recuperação do Microsoft Azure.

Para configurar o aparelho:
- Se seguiu o segundo tutorial para avaliar os VMs hiper-V, já montou o aparelho durante esse tutorial e não precisa de o fazer novamente.
- Se não seguiu o tutorial, tem de montar o aparelho agora. Para fazer isto, tu: 

    - Baixe um VHD Hiper-V comprimido a partir do portal Azure.
    - Crie o aparelho e verifique se pode ligar-se à Avaliação do Servidor Migratório Azure. 
    - Configure o aparelho pela primeira vez e registe-o com o projeto Azure Migrate.

    Siga as instruções detalhadas [neste artigo](how-to-set-up-appliance-hyper-v.md) para configurar o aparelho.

## <a name="prepare-hyper-v-hosts"></a>Prepare os anfitriões hyper-V

1. No projeto Azure Migrate > **Servers,** em **Azure Migrate: Server Migration**, clique **em Discover**.
2. Em **Discover machines** > **As suas máquinas são virtualizadas?** **Yes, with Hyper-V**
3. Na **região Target,** selecione a região de Azure para a qual pretende migrar as máquinas.
6. Selecione **Confirmar que a região alvo para a migração é o nome da região**.
7. Clique em **Criar recursos.** Isto cria um cofre de recuperação de sítios Azure em segundo plano.
    - Se já criou a migração com a Migração do Servidor Migratório Migratório Migratório Migratório Migratório Azure, esta opção não aparecerá uma vez que os recursos foram criados anteriormente.
    - Não pode alterar a região alvo deste projeto depois de clicar neste botão.
    - Todas as migrações subsequentes são para esta região.
    
8. Em **Prepare os servidores anfitriões Hyper-V,** descarregue o fornecedor de replicação Hyper-V e o ficheiro chave de registo.
    - A chave de registo é necessária para registar o hospedeiro Hyper-V com a Migração do Servidor Migratório Migratório Migratório Migratório Migratório Migratório Migratório Azure.
    - A chave é válida durante cinco dias depois de gerá-la.

    ![Fornecedor de descarregamento e chave](./media/tutorial-migrate-hyper-v/download-provider-hyper-v.png)

4. Copie o ficheiro de configuração do fornecedor e o ficheiro de chave de registo para cada anfitrião Hyper-V (ou nó de cluster) que executa VMs que pretende replicar.
5. Executar o ficheiro de configuração do fornecedor em cada anfitrião, conforme descrito no procedimento seguinte.
6. Depois de instalar o fornecedor nos anfitriões, nas **máquinas Discover,** clique em finalizar a **inscrição**.

    ![Finalizar o registo](./media/tutorial-migrate-hyper-v/finalize-registration.png)

Pode demorar até 15 minutos após finalizar o registo até que os VMs descobertos apareçam na Migração do Servidor Migratório De Migração de Migração de Migração de Migração de Migração de Migração. À medida que os VMs são descobertos, os **servidores descobertos contam aumentos.**

![Servidores descobertos](./media/tutorial-migrate-hyper-v/discovered-servers.png)

### <a name="register-hyper-v-hosts"></a>Registar anfitriões Hyper-V

Instale o ficheiro de configuração descarregado (AzureSiteRecoveryProvider.exe) em cada anfitrião Hyper-V relevante.

1. Executar o ficheiro de configuração do fornecedor em cada nó de hospedar ou cluster.
2. No assistente de configuração do fornecedor > **Microsoft Update,** opte por utilizar o Microsoft Update para verificar se há atualizações do Fornecedor.
3. Na **instalação,** aceite o local de instalação predefinido para o Fornecedor e o agente, e selecione **Instalar**.
4. Após a instalação, no Assistente de Registo > **Definições de Cofre,** selecione **Browse**, e no **Ficheiro chave,** selecione o ficheiro de chave do cofre que descarregou.
5. Em **Definições proxy,** especifique como o fornecedor que executa no hospedeiro se conecta à internet.
    - Se o aparelho estiver localizado atrás de um servidor proxy, tem de especificar as definições de procuração.
    - Especifique **http://ip-address**o **http://FQDN**nome proxy como, ou . Os servidores proxy HTTPS não são suportados.
   

6. Certifique-se de que o fornecedor pode alcançar os [URLs necessários](migrate-support-matrix-hyper-v-migration.md#hyper-v-hosts).
7. No **Registo**, depois de o anfitrião estar registado, clique em **Terminar**.

## <a name="replicate-hyper-v-vms"></a>Replicar VMs hiper-V

Com a descoberta concluída, pode começar a replicar Os VMs Hiper-V para Azure.

> [!NOTE]
> Podes replicar até 10 máquinas juntas. Se precisar de replicar mais, reproduza-os simultaneamente em lotes de 10.

1. No projeto Azure Migrate > **Servers,** **Azure Migrate: Server Migration**, clique em **Replicate**.
2. Em **Replicate**, > **Configurações** > de origem **Yes, with Hyper-V****São as suas máquinas virtualizadas?** Em seguida, clique em **Seguinte: Máquinas virtuais**.
3. Em **Máquinas virtuais**, selecione aquelas que quer replicar.
    - Se tiver executado uma avaliação para as VMs, poderá aplicar as recomendações de dimensionamento de VMs e tipo de discos (premium/standard) nos resultados da avaliação. Para tal, em **Importar definições de migração de uma avaliação do Azure Migrate?**, selecione a opção **Sim**.
    - Se não tiver executado uma avaliação ou não quiser utilizar as definições de avaliação, selecione as opções **Não**.
    - Se tiver selecionado para utilizar a avaliação, selecione o grupo de VMs e o nome da avaliação.

        ![Selecionar avaliação](./media/tutorial-migrate-hyper-v/select-assessment.png)

4. Em **Máquinas virtuais**, procure VMs conforme necessário e marque cada VM que quer migrar. Em seguida, clique em **Seguinte: Definições de alvo**.

    ![Selecione VMs](./media/tutorial-migrate-hyper-v/select-vms.png)

5. Nas **definições do Target**, selecione a região alvo para a qual irá migrar, a subscrição e o grupo de recursos em que os VMs Azure irão residir após a migração.
7. Na Conta de Armazenamento de **Replicação,** selecione a conta de Armazenamento Azure na qual os dados replicados serão armazenados em Azure.
8. **Rede Virtual**, selecione o Azure VNet/subnet a que os VMs Azure serão aderidos após a migração.
9. Em **Benefício Híbrido do Azure**:

    - Selecione **Não** se não quiser aplicar o Benefício Híbrido do Azure. Em seguida, clique **em Next**.
    - Selecione **Sim** se tiver computadores Windows Server abrangidos com subscrições ativas do Software Assurance ou do Windows Server e quiser aplicar o benefício aos computadores que está a migrar. Em seguida, clique em **Seguinte**.

    ![Definições de destino](./media/tutorial-migrate-hyper-v/target-settings.png)

10. Em **Computação**, analise o nome, o tamanho, o tipo de disco do SO e o conjunto de disponibilidade das VMs. As VMs devem estar em conformidade com os [requisitos do Azure](migrate-support-matrix-hyper-v-migration.md#azure-vm-requirements).

    - **Tamanho VM**: Se estiver a utilizar recomendações de avaliação, a queda do tamanho do VM conterá o tamanho recomendado. Caso contrário, o Azure Migrate escolhe um tamanho com base na correspondência mais próxima na subscrição do Azure. Como alternativa, escolha um tamanho manual em **Tamanho de VMs do Azure**. 
    - **Disco OS:** Especifique o disco OS (boot) para o VM. O disco do SO é o disco que possui o carregador e o instalador do sistema operativo. 
    - **Conjunto de disponibilidade**: Se o VM estiver num conjunto de disponibilidade azure após a migração, especifique o conjunto. O conjunto deve estar no grupo de recursos de destino especificado para a migração.

    ![Definições de computação VM](./media/tutorial-migrate-hyper-v/compute-settings.png)

11. Em **Discos**, especifique se os discos das VMs devem ser replicados para o Azure e selecione o tipo de disco (discos geridos SSD/HDD standard ou premium) no Azure. Em seguida, clique em **Seguinte**.
    - Pode excluir discos da replicação.
    - Se excluir discos, estes não estarão presentes na VM do Azure após a migração. 

    ![Discos](./media/tutorial-migrate-hyper-v/disks.png)

10. Em **Analisar e iniciar a replicação**, analise as definições e clique em **Replicar** para iniciar a replicação inicial para os servidores.

> [!NOTE]
> Pode atualizar as definições de replicação a qualquer momento antes do início da replicação, em **Gerir** > **máquinas de replicação**. As definições não podem ser alteradas após o início da replicação.

## <a name="provisioning-for-the-first-time"></a>Provisionamento pela primeira vez

Se este é o primeiro VM que está a replicar no projeto Azure Migrate, o Azure Migrate: Server Migration disponibiliza automaticamente estes recursos no mesmo grupo de recursos que o projeto.

- **Ônibus de serviço**: Azure Migrate: Server Migration usa o Ônibus de serviço para enviar mensagens de orquestração de replicação para o aparelho.
- **Conta de armazenamento**gateway : Azure Migrate: Server Migration usa a conta de armazenamento de gateway para armazenar informações estatais sobre os VMs que estão sendo replicados.
- **Conta de armazenamento de registos**: O aparelho Azure Migrate envia registos de replicação para VMs para uma conta de armazenamento de registo. A Azure Migrate aplica a informação de replicação aos discos geridos por réplicas.
- **Cofre chave**: O aparelho Azure Migrate utiliza o cofre chave para gerir as cordas de ligação para o autocarro de serviço e as chaves de acesso para as contas de armazenamento utilizadas na replicação. Devia ter configurado as permissões de que o cofre-chave precisa para aceder à conta de armazenamento quando [preparou o Azure](tutorial-prepare-hyper-v.md#prepare-azure) para avaliação e migração de VM hiper-V. 


## <a name="track-and-monitor"></a>Faixa e monitor


- Quando clicar em **Replicar** começa um trabalho de replicação de arranque. 
- Quando o trabalho de Replicação Inicial termina com sucesso, as máquinas iniciam a sua replicação inicial para Azure.
- Após os acabamentos iniciais da replicação, começa a replicação delta. As alterações incrementais nos discos no local são periodicamente replicadas para O Azure.

Pode rastrear o estado do emprego nas notificações do portal.

Pode monitorizar o estado de replicação clicando em **servidores de replicação** em **Azure Migrate: Server Migration**.
![Monitorizar a replicação](./media/tutorial-migrate-hyper-v/replicating-servers.png)



## <a name="run-a-test-migration"></a>Executar uma migração de teste


Quando a replicação delta começar, você pode executar uma migração de teste para os VMs, antes de executar uma migração completa para Azure. Recomendamos vivamente que faça isto pelo menos uma vez por cada máquina, antes de a migrar.

- A realização de um teste de migração verifica que a migração funcionará como esperado, sem afetar as máquinas no local, que permanecem operacionais, e continuam a replicar-se. 
- A migração de teste simula a migração criando um VM Azure utilizando dados replicados (geralmente migrando para um Azure VNet não-produzido na sua assinatura Azure).
- Pode utilizar o teste replicado Azure VM para validar a migração, realizar testes de aplicações e resolver quaisquer problemas antes da migração total.

Faça uma migração de teste da seguinte forma:


1. Nos **objetivos** > de migração**Servidores** > **Azure Migram: Migração do Servidor,** clique em **servidores migrados de teste**.

     ![Testar servidores migrados](./media/tutorial-migrate-hyper-v/test-migrated-servers.png)

2. Clique com o botão direito do rato na VM a testar e, em seguida, clique em **Testar migração**.

    ![Testar migração](./media/tutorial-migrate-hyper-v/test-migrate.png)

3. Em **Teste migração,** selecione a rede virtual Azure na qual o Azure VM será localizado após a migração. Recomendamos que utilize uma rede virtual não-produção.
4. A tarefa **Teste de migração** é iniciada. Monitorize a tarefa nas notificações do portal.
5. Após a conclusão da migração, veja a VM do Azure migrada em **Máquinas Virtuais** no portal do Azure. O nome do computador tem um sufixo **-Test**.
6. Após terminar o teste, clique com o botão direito do rato na VM do Azure em **Replicar computadores** e clique em **Limpar teste de migração**.

    ![Limpar a migração](./media/tutorial-migrate-hyper-v/clean-up.png)


## <a name="migrate-vms"></a>Migrar VMs

Depois de verificar que a migração do teste funciona como esperado, pode migrar as máquinas no local.

1. No projeto Azure Migrate > **Servers** > **Azure Migrate: Server Migration**, clique em **servidores de replicação**.

    ![Replicar servidores](./media/tutorial-migrate-hyper-v/replicate-servers.png)

2. Em **Replicar computadores**, clique com o botão direito do rato na VM > **Migrar**.
3. Em **Migrate** > **Desligue as máquinas virtuais e realize uma migração planeada sem perda de dados**, selecione **Yes** > **OK**.
    - Por predefinição, o Azure Migrate desliga a VM no local e executa uma replicação a pedido para sincronizar quaisquer alterações à VM ocorridas desde a última replicação. Tal garante que não haja perda de dados.
    - Se não quiser desligar a VM, selecione **Não**
4. Uma tarefa de migração é iniciada para a VM. Controle a tarefa nas notificações do Azure.
5. Após a conclusão da tarefa, pode ver e gerir a VM na página **Máquinas Virtuais**.

## <a name="complete-the-migration"></a>Complete a migração

1. Depois da migração ser feita, clique à direita no VM > Parar a **migração**. Isto faz o seguinte:
    - Para a replicação para a máquina no local.
    - Remove a máquina da contagem de **servidores de replicação** em Migração Azure: Migração do Servidor.
    - Limpa informações do estado de replicação para o VM.
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
