---
title: Migrar máquinas como servidor físico para Azure com O Migração Azure.
description: Este artigo descreve como migrar máquinas físicas para Azure com Azure Migrate.
ms.topic: tutorial
ms.date: 02/03/2020
ms.custom: MVC
ms.openlocfilehash: bd55f422451df935301245eef3e9dd10f7ab55b1
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2020
ms.locfileid: "78945745"
---
# <a name="migrate-machines-as-physical-servers-to-azure"></a>Migrar máquinas como servidores físicos para O Azure

Este artigo mostra-lhe como migrar máquinas como servidores físicos para o Azure, utilizando a ferramenta Migração De Migração De Migração De Migração De Migração De Migração De Migração De Migração De Migração De Migração De Migração De Migração De Migração De Migração De Migração de Emigrantes Azure.Server. As máquinas migratórias, tratando-as como servidores físicos, são úteis em vários cenários:

- Migrar no local servidores físicos.
- VMs migrados por plataformas como Xen, KVM.
- Migrar VMs Hyper-V ou VMware, se por alguma razão você não puder usar o processo de migração padrão para a migração [hyper-V](tutorial-migrate-hyper-v.md)ou [VMware.](server-migrate-overview.md)
- VMs migrando em nuvens privadas.
- Os VMs migram em nuvens públicas como a Amazon Web Services (AWS) ou a Google Cloud Platform (GCP).


[A Azure Migrate](migrate-services-overview.md) fornece um centro central para acompanhar a descoberta, avaliação e migração das suas aplicações e cargas de trabalho no local, e casos de VM em nuvem, para O Azure. O hub fornece ferramentas azure migrate para avaliação e migração, bem como ofertas de fornecedores de software independentes de terceiros (ISV).


Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Prepare o Azure para a migração com a ferramenta de migração do servidor migratório Azure Migrate.
> * Verifique os requisitos das máquinas que pretende migrar e prepare uma máquina para o aparelho de replicação Azure Migrate que é usado para descobrir e migrar máquinas para Azure.
> * Adicione a ferramenta de migração do servidor migratório Azure no centro de migração Azure.
> * Instale o aparelho de replicação.
> * Instale o serviço de Mobilidade em máquinas que pretende migrar.
> * Ative a replicação.
> * Faça uma migração de teste para ter certeza de que está tudo funcionando como esperado.
> * Fazer uma migração completa para Azure.

> [!NOTE]
> Os tutoriais mostram-lhe o caminho de implantação mais simples para um cenário para que possa rapidamente configurar uma prova de conceito. Os tutoriais usam opções padrão sempre que possível, e não mostram todas as configurações e caminhos possíveis. Para obter instruções detalhadas, reveja o How-tos para O Migração Azure.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.


## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, tem de:

1. [Reveja](migrate-architecture.md) a arquitetura da migração.
2. Certifique-se de que a sua conta Azure é atribuída a função de Colaborador de Máquina Virtual, para que tenha permissões para:

    - Criar uma VM no grupo de recursos selecionado.
    - Criar uma VM na rede virtual selecionada.
    - Escreva para um disco gerido pelo Azure. 

3. [Criar uma rede Azure.](../virtual-network/manage-virtual-network.md#create-a-virtual-network) Quando se replica para o Azure, os VMs Azure são criados e juntam-se a uma rede Azure que especifica quando configura a migração.


## <a name="prepare-azure"></a>Preparar o Azure

Instale permissões Azure antes de poder migrar com a Migração do Servidor Migratório Migratório Azure Migrate.

- **Crie um projeto**: A sua conta Azure precisa de permissões para criar um projeto Azure Migrate. 

### <a name="assign-permissions-to-create-project"></a>Atribuir permissões para criar projeto

1. No portal Azure, abra a subscrição e selecione controlo de **acesso (IAM)** .
2. No **Check access,** encontre a conta relevante e clique nela para visualizar permissões.
3. Deve ter permissões de **Contribuinte** ou **Proprietário.**
    - Se acabou de criar uma conta Azure gratuita, é o proprietário da sua subscrição.
    - Se não for o proprietário da subscrição, trabalhe com o proprietário para atribuir o papel.

## <a name="prepare-for-migration"></a>Prepare para a migração

### <a name="check-machine-requirements-for-migration"></a>Verifique os requisitos da máquina para a migração

Certifique-se de que as máquinas cumprem os requisitos de migração para Azure. 

> [!NOTE]
> A migração baseada em agentes com a Migração do Servidor Migratório Migratório Migratório Migratório Migratório De Migração de Migração de Migração de Migração de Migração de Migração de Migração de Migração de Migração de Migração de Migração de Migração de Migração, tem a mesma arquitetura de replicação que a funcionalidade de recuperação de desastres baseada no agente do serviço de recuperação de sites Azure, e alguns dos componentes usados partilham a mesma base de código. Alguns requisitos podem ligar-se à documentação de Recuperação do Site.

1. [Verifique os](migrate-support-matrix-physical-migration.md#physical-server-requirements) requisitos físicos do servidor.
2. Verifique as definições de VM. As máquinas no local que replica para o Azure devem cumprir os requisitos da [Azure VM](migrate-support-matrix-physical-migration.md#azure-vm-requirements).


### <a name="prepare-a-machine-for-the-replication-appliance"></a>Prepare uma máquina para o aparelho de replicação

A Migração do Servidor Migratório Azure usa um aparelho de replicação para replicar máquinas para o Azure. O aparelho de replicação executa os seguintes componentes.

- Servidor de **configuração**: O servidor de configuração coordena as comunicações entre as instalações e o Azure e gere a replicação de dados.
- **Servidor de processos**: O servidor de processo funciona como um portal de replicação. Recebe dados de replicação; otimiza-o com cache, compressão e encriptação, e envia-o para uma conta de armazenamento em cache em Azure. 

Antes de começar, tem de preparar uma máquina Windows Server 2016 para alojar o aparelho de replicação. A máquina deve cumprir [estes requisitos.](migrate-replication-appliance.md) O aparelho não deve ser instalado numa máquina de origem que queira proteger.


## <a name="add-the-azure-migrate-server-migration-tool"></a>Adicione a ferramenta de migração do servidor migratório Azure Migrate

Criar um projeto Azure Migrate e, em seguida, adicionar-lhe a ferramenta de migração do servidor migratório de migração do Servidor Migratório Azure.

1. No portal do Azure > **Todos os serviços**, procure **Azure Migrate**.
2. Em **Serviços**, selecione **Azure Migrate**.
3. Em **Descrição geral**, clique em **Avaliar e migrar servidores**.
4. Em **Discover, avalie e emigra os servidores,** clique em **avaliar e migrar servidores**.

    ![Descubra e avalie servidores](./media/tutorial-migrate-physical-virtual-machines/assess-migrate.png)

5. Em **Detetar, avaliar e migrar servidores**, clique em **Adicionar ferramentas**.
6. Em **Migrar projeto**, selecione a sua subscrição do Azure e crie um grupo de recursos, caso não tenha um.
7. Em Detalhes do **Projeto,** especifique o nome do projeto e geografia em que pretende criar o projeto, e clique em **Next**

    ![Criar um projeto Azure Migrate](./media/tutorial-migrate-physical-virtual-machines/migrate-project.png)

    Você pode criar um projeto Azure Migrate em qualquer uma destas geografias.

    **Geografia** | **Região**
    --- | ---
    Ásia | Ásia Sudeste
    Europa | Norte da Europa ou Europa Ocidental
    Estados Unidos | Leste dos EUA ou Centro-Oeste dos EUA

    A geografia especificada para o projeto só é utilizada para armazenar os metadados recolhidos das VMs no local. Pode selecionar qualquer região-alvo para a migração real.
8. Na ferramenta de **avaliação Select,** selecione **Skip adicionando uma ferramenta de avaliação para agora** > **Seguinte**.
9. Na **ferramenta de migração Select**, selecione **Azure Migrate: Server Migration** > **Next**.
10. Em **Analisar + adicionar ferramentas**, analise as definições e clique em **Adicionar ferramentas**
11. Depois de adicionar a ferramenta, aparece no projeto Migratório Azure > Servidores > **Ferramentas migratórias.**

## <a name="set-up-the-replication-appliance"></a>Configurar o aparelho de replicação

O primeiro passo da migração é a instalação do aparelho de replicação. Faça o download do ficheiro do instalador para o aparelho e execute-o na [máquina que preparou](#prepare-a-machine-for-the-replication-appliance). Depois de instalar o aparelho, registe-o com a Migração do Servidor Migratório Migratório Migratório De Migração de Migração de Migração de Migração de Migração de Migração de Migração de Migração.


### <a name="download-the-replication-appliance-installer"></a>Descarregue o instalador de aparelhos de replicação

1. No projeto Azure Migrate > **Servers,** em **Azure Migrate: Server Migration,** clique **em Discover**.

    ![Detetar VMs](./media/tutorial-migrate-physical-virtual-machines/migrate-discover.png)

3. Em **Discover machines** > **As suas máquinas estão virtualizadas?**
4. Na **região Target,** selecione a região de Azure para a qual pretende migrar as máquinas.
5. Selecione **Confirmar que a região alvo para a migração é o nome da região**.
6. Clique em **Criar recursos.** Isto cria um cofre de recuperação de sítios Azure em segundo plano.
    - Se já criou a migração com a Migração do Servidor Migratório Migratório Migratório Migratório De Migração de Migração De Migração De Migração, a opção-alvo não pode ser configurada, uma vez que os recursos foram criados anteriormente.
    - Não pode alterar a região alvo deste projeto depois de clicar neste botão.
    - Todas as migrações subsequentes são para esta região.

7. Em Se pretender instalar um novo aparelho de **replicação,** selecione **Instale um aparelho**de replicação .
9. No **Descarregue e instale o software do aparelho de replicação,** descarregue o instalador do aparelho e a chave de registo. É necessário colocar a chave na chave para registar o aparelho. A chave é válida por cinco dias após o download.

    ![Fornecedor de descarregamento](media/tutorial-migrate-physical-virtual-machines/download-provider.png)

10. Copie o ficheiro de configuração do aparelho e o ficheiro chave para a máquina Windows Server 2016 que criou para o aparelho.
11. Executar o ficheiro de configuração do aparelho de replicação, conforme descrito no procedimento seguinte.
12. Depois de o aparelho ter reiniciado após a instalação, nas **máquinas Discover,** selecione o novo aparelho no **Select Configuration Server**e clique em Finalizar a **inscrição**. Finalizar o registo executa algumas tarefas finais para preparar o aparelho de replicação.

    ![Finalizar o registo](./media/tutorial-migrate-physical-virtual-machines/finalize-registration.png)

Pode levar algum tempo depois de finalizar o registo até que as máquinas descobertas apareçam na Migração do Servidor Migratório Migratório Migratório De Migração de Migração de Migração de Migração de Migração de Migração de Migração de Migração de Migração. À medida que os VMs são descobertos, os **servidores descobertos contam aumentos.**

![Servidores descobertos](./media/tutorial-migrate-physical-virtual-machines/discovered-servers.png)


## <a name="install-the-mobility-service"></a>Instalar o serviço de Mobilidade

Nas máquinas que pretende migrar, precisa de instalar o agente de serviço mobility. Os instaladores do agente estão disponíveis no aparelho de replicação. Encontre o instalador certo e instale o agente em cada máquina que queira migrar. Faça isto da seguinte forma:

1. Inscreva-se no aparelho de replicação.
2. Navegue para **%ProgramData%\ASR\home\svsystems\pushinstallsvc\repositório**.
3. Encontre o instalador para o sistema operativo e versão da máquina. Reveja [os sistemas operativos suportados](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#replicated-machines)pela revisão. 
4. Copie o ficheiro do instalador para a máquina que pretende migrar.
5. Certifique-se de que tem a frase-passe gerada quando lançou o aparelho.
    - Guarde o ficheiro num ficheiro de texto temporário na máquina.
    - Pode obter a palavra-passe no aparelho de replicação. A partir da linha de comando, executar **C:\ProgramData\ASR\home\svsystems\bin\genpassphrase.exe -v** para ver a frase de passe atual.
    - Não regenerar a frase-passe. Isto quebrará a conectividade e terá de voltar a registar o aparelho de replicação.


### <a name="install-on-windows"></a>Instalar no Windows

1. Extrair o conteúdo do ficheiro instalador numa pasta local (por exemplo, C:\Temp) na máquina, da seguinte forma:

    ```
    ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
    MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
    cd C:\Temp\Extracted
    ```
2. Executar o Instalador de Serviços de Mobilidade:
    ```
   UnifiedAgent.exe /Role "MS" /Silent
    ```
3. Registe o agente com o aparelho de replicação:
    ```
    cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
    UnifiedAgentConfigurator.exe  /CSEndPoint <replication appliance IP address> /PassphraseFilePath <Passphrase File Path>
    ```

### <a name="install-on-linux"></a>Instalar no Linux

1. Extraio o conteúdo da bola de tarball do instalador para uma pasta local (por exemplo/tmp/MobSvcInstaller) na máquina, da seguinte forma:
    ```
    mkdir /tmp/MobSvcInstaller
    tar -C /tmp/MobSvcInstaller -xvf <Installer tarball>
    cd /tmp/MobSvcInstaller
    ```
2. Executar o script do instalador:
    ```
    sudo ./install -r MS -q
    ```
3. Registe o agente com o aparelho de replicação:
    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <replication appliance IP address> -P <Passphrase File Path>
    ```

## <a name="replicate-machines"></a>Máquinas de replicar

Agora, selecione máquinas para migração. 

> [!NOTE]
> Podes replicar até 10 máquinas juntas. Se precisar de replicar mais, reproduza-os simultaneamente em lotes de 10.

1. No projeto Azure Migrate > **Servers,** **Azure Migrate: Server Migration,** clique em **Replicate**.

    ![Replicar VMs](./media/tutorial-migrate-physical-virtual-machines/select-replicate.png)

2. Em **Replicate**, > **Configurações** de origem >  **As suas máquinas estão virtualizadas?**
3. No **aparelho On-local,** selecione o nome do aparelho Azure Migrate que instalou.
4. No Servidor de **Processos,** selecione o nome do aparelho de replicação.
6. Nas **credenciais de hóspedes,** especifice uma conta de administração VM que será usada para a instalação push do serviço mobility. Neste tutorial estamos a instalar manualmente o serviço de Mobilidade, para que possa adicionar qualquer conta de boneco. Em seguida, clique em **Seguinte: Máquinas virtuais**.

    ![Replicar VMs](./media/tutorial-migrate-physical-virtual-machines/source-settings.png)

7. Nas **Máquinas Virtuais**, nas **definições de migração de importação de uma avaliação?**
8. Verifique cada VM que queira migrar. Em seguida, clique em **Seguinte: Definições de destino**.

    ![Selecione VMs](./media/tutorial-migrate-physical-virtual-machines/select-vms.png)


9. Em **Definições de destino**, selecione a subscrição, assim como a região de destino para a qual vai migrar, e especifique o grupo de recursos no qual as VMs do Azure vão residir após a migração.
10. Em **Rede Virtual**, selecione a VNet/sub-rede do Azure na qual as VMs do Azure vão ser associadas após a migração.
11. Em **Benefício Híbrido do Azure**:

    - Selecione **Não** se não quiser aplicar o Benefício Híbrido do Azure. Clique depois em **Seguinte**.
    - Selecione **Sim** se tiver computadores Windows Server abrangidos com subscrições ativas do Software Assurance ou do Windows Server e quiser aplicar o benefício aos computadores que está a migrar. Clique depois em **Seguinte**.

    ![Definições de destino](./media/tutorial-migrate-physical-virtual-machines/target-settings.png)

12. Em **Computação**, analise o nome, o tamanho, o tipo de disco do SO e o conjunto de disponibilidade das VMs. As VMs devem estar em conformidade com os [requisitos do Azure](migrate-support-matrix-physical-migration.md#azure-vm-requirements).

    - **Tamanho VM**: Por padrão, a Migração do Servidor Migratório Azure escolhe um tamanho com base na correspondência mais próxima da subscrição azure. Como alternativa, escolha um tamanho manual em **Tamanho de VMs do Azure**. 
    - **Disco OS:** Especifique o disco OS (boot) para o VM. O disco do SO é o disco que possui o carregador e o instalador do sistema operativo. 
    - **Conjunto de disponibilidade**: Se o VM estiver num conjunto de disponibilidade azure após a migração, especifique o conjunto. O conjunto deve estar no grupo de recursos de destino especificado para a migração.

    ![Definições computadas](./media/tutorial-migrate-physical-virtual-machines/compute-settings.png)

13. Nos **Discos,** especifique se os discos VM devem ser replicados para O Azure e selecione o tipo de disco (SSD/HDD padrão ou discos geridos premium) em Azure. Clique depois em **Seguinte**.
    - Pode excluir discos da replicação.
    - Se excluir discos, estes não estarão presentes na VM do Azure após a migração. 

    ![Definições do disco](./media/tutorial-migrate-physical-virtual-machines/disks.png)


14. Em **Analisar e iniciar a replicação**, analise as definições e clique em **Replicar** para iniciar a replicação inicial para os servidores.

> [!NOTE]
> Pode atualizar as definições de replicação a qualquer momento antes do início da replicação, **gerir** > **máquinas de replicação**. As definições não podem ser alteradas após o início da replicação.



## <a name="track-and-monitor"></a>Faixa e monitor

- Quando clicar em **Replicar** começa um trabalho de replicação de arranque. 
- Quando o trabalho de Replicação Inicial termina com sucesso, as máquinas iniciam a sua replicação inicial para Azure.
- Após os acabamentos iniciais da replicação, começa a replicação delta. As alterações incrementais nos discos no local são periodicamente replicadas para os discos de réplica em Azure.


Pode rastrear o estado do emprego nas notificações do portal.

Pode monitorizar o estado de replicação clicando em **servidores de replicação** em **Azure Migrate: Server Migration**.
![monitor](./media/tutorial-migrate-physical-virtual-machines/replicating-servers.png) de replicação

## <a name="run-a-test-migration"></a>Executar uma migração de teste


Quando a replicação delta começar, você pode executar uma migração de teste para os VMs, antes de executar uma migração completa para Azure. Recomendamos vivamente que faça isto pelo menos uma vez por cada máquina, antes de a migrar.

- A realização de um teste de migração verifica que a migração funcionará como esperado, sem afetar as máquinas no local, que permanecem operacionais, e continuam a replicar-se. 
- A migração de teste simula a migração criando um VM Azure usando dados replicados (geralmente migrando para um VNet não-produção na sua assinatura Azure).
- Pode utilizar o teste replicado Azure VM para validar a migração, realizar testes de aplicações e resolver quaisquer problemas antes da migração total.

Faça uma migração de teste da seguinte forma:


1. Nos **objetivos de migração** > **servidores** > **Migração Do Servidor,** clique em **servidores migrados**de teste .

     ![Testar servidores migrados](./media/tutorial-migrate-physical-virtual-machines/test-migrated-servers.png)

2. Clique com o botão direito do rato na VM a testar e, em seguida, clique em **Testar migração**.

    ![Testar migração](./media/tutorial-migrate-physical-virtual-machines/test-migrate.png)

3. Em **Testar Migração**, selecione a VNet do Azure na qual a VM do Azure vai estar localizada após a migração. Recomendamos que utilize uma VNet que não seja de produção.
4. A tarefa **Teste de migração** é iniciada. Monitorize a tarefa nas notificações do portal.
5. Após a conclusão da migração, veja a VM do Azure migrada em **Máquinas Virtuais** no portal do Azure. O nome do computador tem um sufixo **-Test**.
6. Após terminar o teste, clique com o botão direito do rato na VM do Azure em **Replicar computadores** e clique em **Limpar teste de migração**.

    ![Limpar a migração](./media/tutorial-migrate-physical-virtual-machines/clean-up.png)


## <a name="migrate-vms"></a>Migrar VMs

Depois de verificar que a migração do teste funciona como esperado, pode migrar as máquinas no local.

1. No projeto Azure Migrate > **Servers** > **Azure Migrate: Server Migration,** clique em **servidores de replicação**.

    ![Replicar servidores](./media/tutorial-migrate-physical-virtual-machines/replicate-servers.png)

2. Em **Replicar computadores**, clique com o botão direito do rato na VM > **Migrar**.
3. Em **Migrar** > **Desligar máquinas virtuais e realizar uma migração planeada sem perda de dados**, selecione **Sim** > **OK**.
    - Por predefinição, o Azure Migrate desliga a VM no local e executa uma replicação a pedido para sincronizar quaisquer alterações à VM ocorridas desde a última replicação. Tal garante que não haja perda de dados.
    - Se não quiser desligar a VM, selecione **Não**
4. Uma tarefa de migração é iniciada para a VM. Controle a tarefa nas notificações do Azure.
5. Após a conclusão da tarefa, pode ver e gerir a VM na página **Máquinas Virtuais**.

## <a name="complete-the-migration"></a>Complete a migração

1. Depois da migração ser feita, clique à direita no VM > **Pare a migração**. Isto faz o seguinte:
    - Para a replicação para a máquina no local.
    - Remove a máquina da contagem de **servidores de replicação** em Migração Azure: Migração do Servidor.
    - Limpa as informações do estado de replicação para a máquina.
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
    - Considere implementar o [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/overview) para monitorizar a utilização e as despesas do recurso.


## <a name="next-steps"></a>Passos seguintes

Investigue a viagem de [migração](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/migrate) em nuvem no Quadro de Adoção de Nuvens Azure.
