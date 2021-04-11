---
title: Migrar VMs Hiper-V para Azure com migração do servidor Azure Migrate
description: Saiba como migrar no local Hiper-V VMs para Azure com migração do servidor Azure Migrate
author: bsiva
ms.author: bsiva
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 03/18/2021
ms.custom:
- MVC
- fasttrack-edit
ms.openlocfilehash: 0072ce81fc619c39770eba52e24dc5a0c57280a6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104604581"
---
# <a name="migrate-hyper-v-vms-to-azure"></a>Migrar VMs Hyper-V para o Azure 

Este artigo mostra-lhe como migrar no local Hiper-V VMs para Azure com a ferramenta [Azure Migrate:Server Migration.](migrate-services-overview.md#azure-migrate-server-migration-tool)

Este tutorial é o terceiro de uma série que demonstra como avaliar e migrar máquinas para Azure. 

> [!NOTE]
> Os tutoriais mostram-lhe o caminho de implantação mais simples para um cenário para que possa configurar rapidamente uma prova de conceito. Os tutoriais utilizam opções predefinidas sempre que possível e não mostram todas as definições e caminhos possíveis. 

 Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Adicione a ferramenta Azure Migrate:Server Migration.
> * Descubra VMs que quer migrar.
> * Comece a replicar VMs.
> * Faça um teste de migração para garantir que tudo está funcionando como esperado.
> * Executar uma migração completa de VM.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.


## <a name="prerequisites"></a>Pré-requisitos


Antes de começar este tutorial, tem de:

1. [Reveja](hyper-v-migration-architecture.md) a arquitetura de migração Hyper-V.
2. [Revisão](migrate-support-matrix-hyper-v-migration.md#hyper-v-host-requirements) Requisitos de hospedeiro hiper-V para migração, e os URLs Azure aos quais os anfitriões e agrupamentos Hiper-V precisam de acesso para a migração de VM.
3. [Reveja](migrate-support-matrix-hyper-v-migration.md#hyper-v-vms) os requisitos para Os Hiper-VMs que pretende migrar para Azure.
4. Recomendamos que  [avalie os Hiper-VM](tutorial-assess-hyper-v.md) antes de os migrar para Azure, mas não é preciso.
5. Vá ao projeto já criado ou [crie um novo projeto](./create-manage-projects.md)
6. Verifique permissões para a sua conta Azure - A sua conta Azure necessita de permissões para criar um VM e escreva para um disco gerido azure.

## <a name="download-and-install-the-provider"></a>Descarregue e instale o fornecedor

Para migrar VMs hiper-V, a Azure Migrate:Server Migration instala fornecedores de software (Provedor de Recuperação de Sítios Microsoft Azure e agente do Serviço de Recuperação do Microsoft Azure) em anfitriões hiper-V ou nós de cluster. Note que o [aparelho Azure Migrate](migrate-appliance.md) não é utilizado para a migração de Hiper-V.

1. No projeto Azure Migrate > **Servers,** em **Azure Migrate: Server Migration**, clique em **Discover**.
2. In **Discover machines**  >  **Are your machines virtualized?** 
3. Na **região Alvo,** selecione a região de Azure para a qual pretende migrar as máquinas.
6. **Selecione Confirme que a região-alvo para a migração é o nome da região.**
7. Clique **em Criar recursos.** Isto cria um cofre de recuperação do local de Azure em segundo plano.
    - Se já estabeleceu migração com a Migração do Servidor Azure Migrate, esta opção não aparecerá uma vez que os recursos foram criados anteriormente.
    - Não é possível alterar a região alvo deste projeto depois de clicar neste botão.
    - Todas as migrações subsequentes são para esta região.
    
8. No **Prepare os servidores anfitriões Hyper-V,** descarregue o fornecedor de replicação Hyper-V e o ficheiro chave de registo.
    - A chave de registo é necessária para registar o anfitrião Hiper-V com a migração do servidor Azure Migrate.
    - A chave é válida durante cinco dias depois de gerá-la.

    ![Fornecedor de descarregamento e chave](./media/tutorial-migrate-hyper-v/download-provider-hyper-v.png)

4. Copie o ficheiro de configuração do fornecedor e o ficheiro chave de registo para cada anfitrião Hiper-V (ou nó de cluster) que executa VMs que pretende replicar.
5. Execute o ficheiro de configuração do fornecedor em cada anfitrião, conforme descrito no procedimento seguinte.
6. Depois de instalar o fornecedor nos anfitriões, nas **máquinas Discover,** clique em **Finalizar.**

    ![Finalizar o registo](./media/tutorial-migrate-hyper-v/finalize-registration.png)

Pode levar até 15 minutos após a finalização do registo até que os VMs descobertos apareçam na Migração do Servidor Azure Migrate. À medida que os VMs são descobertos, a contagem de **servidores descobertos** aumenta.

![Servidores descobertos](./media/tutorial-migrate-hyper-v/discovered-servers.png)


## <a name="replicate-hyper-v-vms"></a>Replicar VMs hiper-V

Com a descoberta concluída, pode começar a replicação de Hiper-VMs para Azure.

> [!NOTE]
> Pode replicar até 10 máquinas juntas. Se precisar de replicar mais, replique-os simultaneamente em lotes de 10.

1. No projeto Azure Migrate > **Servers**, **Azure Migrate: Server Migration**, clique em **Replicar**.
2. Em **Replicar,**> **Configurações de**  >  **Origem São virtualizadas?**  Em seguida, clique **em Seguinte: Máquinas virtuais**.
3. Em **Máquinas virtuais**, selecione aquelas que quer replicar.
    - Se tiver executado uma avaliação para as VMs, poderá aplicar as recomendações de dimensionamento de VMs e tipo de discos (premium/standard) nos resultados da avaliação. Para tal, em **Importar definições de migração de uma avaliação do Azure Migrate?**, selecione a opção **Sim**.
    - Se não tiver executado uma avaliação ou não quiser utilizar as definições de avaliação, selecione as opções **Não**.
    - Se tiver selecionado para utilizar a avaliação, selecione o grupo de VMs e o nome da avaliação.

        ![Selecionar avaliação](./media/tutorial-migrate-hyper-v/select-assessment.png)

4. Em **Máquinas virtuais**, procure VMs conforme necessário e marque cada VM que quer migrar. Em seguida, clique em **Seguinte: Definições de destino**.

    ![Selecione VMs](./media/tutorial-migrate-hyper-v/select-vms.png)

5. Nas **definições target**, selecione a região-alvo para a qual irá migrar, a subscrição e o grupo de recursos em que os VMs Azure residirão após a migração.
7. Na **Conta de Armazenamento de Replicação**, selecione a conta de Armazenamento Azure na qual os dados replicados serão armazenados no Azure.
8. **Rede Virtual**, selecione o Azure VNet/sub-rede ao qual os VMs Azure serão unidos após a migração.
9. Nas **opções de Disponibilidade,** selecione:
    -  Zona de Disponibilidade para fixar a máquina migrada a uma zona de disponibilidade específica na região. Utilize esta opção para distribuir servidores que formam um nível de aplicação de vários nós em zonas de disponibilidade. Se selecionar esta opção, terá de especificar a Zona de Disponibilidade para utilizar para cada uma das máquinas selecionadas no separador Compute. Esta opção só está disponível se a região-alvo selecionada para a migração suportar Zonas de Disponibilidade
    -  Disponibilidade Definir para colocar a máquina migrada num Conjunto de Disponibilidade. O Grupo de Recursos-Alvo que foi selecionado deve ter um ou mais conjuntos de disponibilidade para utilizar esta opção.
    - Não é necessária nenhuma opção de redundância de infraestrutura se não precisar de nenhuma destas configurações de disponibilidade para as máquinas migradas.
10. Em **Benefício Híbrido do Azure**:

    - Selecione **Não** se não quiser aplicar o Benefício Híbrido do Azure. Em seguida, clique **em Seguinte**.
    - Selecione **Sim** se tiver computadores Windows Server abrangidos com subscrições ativas do Software Assurance ou do Windows Server e quiser aplicar o benefício aos computadores que está a migrar. Em seguida, clique em **Seguinte**.

    ![Definições de destino](./media/tutorial-migrate-hyper-v/target-settings.png)

11. No **Compute,** reveja o nome, tamanho, tipo de disco de SO e configuração de disponibilidade (se selecionado no passo anterior). As VMs devem estar em conformidade com os [requisitos do Azure](migrate-support-matrix-hyper-v-migration.md#azure-vm-requirements).

    - **Tamanho VM**: Se estiver a utilizar recomendações de avaliação, a queda do tamanho de VM conterá o tamanho recomendado. Caso contrário, o Azure Migrate escolhe um tamanho com base na correspondência mais próxima na subscrição do Azure. Como alternativa, escolha um tamanho manual em **Tamanho de VMs do Azure**. 
    - **Disco de os:** Especifique o disco DE (arranque) para o VM. O disco do SO é o disco que possui o carregador e o instalador do sistema operativo. 
    - **Disponibilidade Conjunto**: Se o VM deve estar num conjunto de disponibilidade de Azure após a migração, especifique o conjunto. O conjunto deve estar no grupo de recursos de destino especificado para a migração.

    ![Definições de cálculo VM](./media/tutorial-migrate-hyper-v/compute-settings.png)

12. Em **Discos**, especifique os discos VM que precisam de ser replicados para Azure. Em seguida, clique em **Seguinte**.
    - Pode excluir discos da replicação.
    - Se excluir discos, estes não estarão presentes na VM do Azure após a migração. 

    ![A screenshot mostra o separador Discos da caixa de diálogo Replicate.](./media/tutorial-migrate-hyper-v/disks.png)

13. Em **Analisar e iniciar a replicação**, analise as definições e clique em **Replicar** para iniciar a replicação inicial para os servidores.

> [!NOTE]
> Pode atualizar as definições de replicação a qualquer momento antes de começar a replicação, em  >  **máquinas de replicação de gestão**. As definições não podem ser alteradas após o início da replicação.

## <a name="provision-for-the-first-time"></a>Provisão pela primeira vez

Se este for o primeiro VM que está a replicar no projeto Azure Migrate, a Azure Migrate: A Migração de Servidores fornece automaticamente estes recursos no mesmo grupo de recursos que o projeto.
- **Conta de armazenamento de cache**: O software do fornecedor de recuperação do site Azure instalado em anfitriões Hyper-V envia dados de replicação para os VMs configurados para replicação numa conta de armazenamento (conhecida como conta de armazenamento de cache, ou conta de armazenamento de registo) na sua subscrição. O serviço Azure Migrate copia então os dados de replicação carregados da conta de armazenamento para os discos geridos por réplicas correspondentes ao VM. A conta de armazenamento de cache precisa de ser especificada enquanto configura a replicação para um VM e o portal Azure Migrate cria automaticamente uma para o projeto Azure Migrate quando a replicação é configurada pela primeira vez no projeto.

## <a name="track-and-monitor"></a>Rastreio e monitor


- Quando clicar em **Replicar** um trabalho de replicação inicial começa. 
- Quando o trabalho de Replicação inicial termina com sucesso, as máquinas iniciam a sua replicação inicial ao Azure.
- Após o final da replicação inicial, começa a replicação delta. As alterações incrementais nos discos no local são periodicamente replicadas no Azure.

Pode acompanhar o estado do trabalho nas notificações do portal.

Pode monitorizar o estado de replicação clicando em **servidores replicantes** em **Azure Migrate: Migração do servidor**.
![Monitorizar a replicação](./media/tutorial-migrate-hyper-v/replicating-servers.png)



## <a name="run-a-test-migration"></a>Executar uma migração de teste


Quando a replicação delta começar, pode fazer uma migração de teste para os VMs, antes de executar uma migração completa para Azure. Recomendamos vivamente que o faça pelo menos uma vez por cada máquina, antes de a migrar.

- A realização de um teste de controlos de migração de que a migração funcionará como esperado, sem afetar as máquinas no local, que permanecem operacionais, e continuam a replicar-se. 
- A migração de testes simula a migração criando um VM Azure usando dados replicados (geralmente migrando para um Azure VNet não produtivo na sua assinatura Azure).
- Pode utilizar o teste replicado Azure VM para validar a migração, realizar testes de aplicações e resolver quaisquer problemas antes da migração completa.

Faça uma migração de teste da seguinte forma:


1. Nos **objetivos de migração**  >  **Servers**  >  **Azure Migrate: Server Migration**, clique em Test **servidores migrados**.

     ![Testar servidores migrados](./media/tutorial-migrate-hyper-v/test-migrated-servers.png)

2. Clique com o botão direito do rato na VM a testar e, em seguida, clique em **Testar migração**.

    ![Testar migração](./media/tutorial-migrate-hyper-v/test-migrate.png)

3. Em **Test Migration**, selecione a rede virtual Azure na qual o VM Azure será localizado após a migração. Recomendamos que utilize uma rede virtual não-produtora.
4. A tarefa **Teste de migração** é iniciada. Monitorize a tarefa nas notificações do portal.
5. Após a conclusão da migração, veja a VM do Azure migrada em **Máquinas Virtuais** no portal do Azure. O nome do computador tem um sufixo **-Test**.
6. Após terminar o teste, clique com o botão direito do rato na VM do Azure em **Replicar computadores** e clique em **Limpar teste de migração**.

    ![Limpar a migração](./media/tutorial-migrate-hyper-v/clean-up.png)


## <a name="migrate-vms"></a>Migrar VMs

Depois de verificar que a migração do teste funciona como esperado, pode migrar as máquinas no local.

1. No projeto Azure Migrate > **Servers**  >  **Azure Migrate: Server Migration**, clique em **Replicating servidores**.

    ![Replicar servidores](./media/tutorial-migrate-hyper-v/replicate-servers.png)

2. Em **Replicar computadores**, clique com o botão direito do rato na VM > **Migrar**.
3. Em **Migrar**  >  **Desligue as máquinas virtuais e realize uma migração planeada sem perda de dados**, selecione **Yes**  >  **OK**.
    - Por predefinição, o Azure Migrate desliga a VM no local e executa uma replicação a pedido para sincronizar quaisquer alterações à VM ocorridas desde a última replicação. Tal garante que não haja perda de dados.
    - Se não quiser desligar a VM, selecione **Não**
4. Uma tarefa de migração é iniciada para a VM. Controle a tarefa nas notificações do Azure.
5. Após a conclusão da tarefa, pode ver e gerir a VM na página **Máquinas Virtuais**.

## <a name="complete-the-migration"></a>Complete a migração

1. Depois de terminar a migração, clique com a direita na VM > **Parar a migração**. Isto faz o seguinte:
    - Para a replicação para a máquina no local.
    - Remove a máquina da contagem de **servidores replicantes** em Azure Migrate: Migração do servidor.
    - Limpa a informação do estado de replicação para o VM.
2. Instale o Azure VM [Windows](../virtual-machines/extensions/agent-windows.md) ou o agente [Linux](../virtual-machines/extensions/agent-linux.md) nas máquinas migratórias.
3. Execute otimizações de aplicação pós-migração, tais como atualizar cadeias de ligação de base de dados e configurações de servidor Web.
4. Execute testes de aplicação final e de aceitação da migração na aplicação migrada em execução no Azure.
5. Corte o tráfego para o exemplo de Azure VM migrado.
6. Remova as VMs no local do seu inventário de VMs locais.
7. Remova as VMs no local das cópias de segurança locais.
8. Atualize qualquer documentação interna para mostrar a nova localização e endereço IP das VMs do Azure. 

## <a name="post-migration-best-practices"></a>Boas práticas pós-migração

- Para uma maior resiliência:
    - Mantenha os dados seguros ao fazer uma cópia de segurança das VMs do Azure através do serviço Azure Backup. [Saiba mais](../backup/quick-backup-vm-portal.md).
    - Mantenha as cargas de trabalho em execução e continuamente disponíveis ao replicar VMs do Azure para uma região secundária com o Site Recovery. [Saiba mais](../site-recovery/azure-to-azure-tutorial-enable-replication.md).
- Para uma maior segurança:
    - Bloqueie e limite o acesso ao tráfego de entrada com [o Azure Security Center - Mesmo a tempo da administração](../security-center/security-center-just-in-time.md).
    - Restrinja o tráfego de rede para os pontos finais de gestão com os [Grupos de Segurança de Rede](../virtual-network/network-security-groups-overview.md).
    - Implemente o [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md) para ajudar a proteger discos e a manter os dados protegidos contra roubo e acesso não autorizado.
    - Leia mais sobre como [proteger recursos de IaaS](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/) e aceda ao [Centro de Segurança do Azure](https://azure.microsoft.com/services/security-center/).
- Para monitorização e gestão:
-  Considere implementar o [Azure Cost Management](../cost-management-billing/cloudyn/overview.md) para monitorizar a utilização e as despesas do recurso.


## <a name="next-steps"></a>Passos seguintes

Investigue a [jornada de migração](/azure/architecture/cloud-adoption/getting-started/migrate) em nuvem no Quadro de Azure Cloud Adopt.