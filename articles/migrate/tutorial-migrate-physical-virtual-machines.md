---
title: Migrar máquinas como servidor físico para Azure com Azure Migrate.
description: Este artigo descreve como migrar máquinas físicas para Azure com Azure Migrate.
ms.topic: tutorial
ms.date: 04/15/2020
ms.custom: MVC
ms.openlocfilehash: 2f9b3d43effff49da6aa348e22c0c974606f4c2b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91296336"
---
# <a name="migrate-machines-as-physical-servers-to-azure"></a>Migrar máquinas como servidores físicos para Azure

Este artigo mostra-lhe como migrar máquinas como servidores físicos para Azure, utilizando a ferramenta Azure Migrate:Server Migration. As máquinas migratórias tratando-as como servidores físicos são úteis em vários cenários:

- Migrar nos servidores físicos.
- Migrar VMs virtualizados por plataformas como Xen, KVM.
- Migrar VMs hiper-V ou VMware, se por alguma razão não conseguir utilizar o processo de migração padrão para a migração [de Hiper-V](tutorial-migrate-hyper-v.md)ou [VMware.](server-migrate-overview.md)
- VMs migram correndo em nuvens privadas.
- Migrar VMs em nuvens públicas como Amazon Web Services (AWS) ou Google Cloud Platform (GCP).


Este tutorial é o terceiro de uma série que demonstra como avaliar e migrar servidores físicos para Azure. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Prepare-se para utilizar o Azure com a Azure Migrate:Migração do servidor.
> * Verifique os requisitos para as máquinas que pretende migrar e prepare uma máquina para o aparelho de replicação Azure Migrate que é usado para descobrir e migrar máquinas para Azure.
> * Adicione a ferramenta de migração do servidor Azure Migrate no hub Azure Migrate.
> * Configurar o aparelho de replicação.
> * Instale o serviço de Mobilidade em máquinas que pretende migrar.
> * Ative a replicação.
> * Faça um teste de migração para garantir que tudo está funcionando como esperado.
> * Fazer uma migração completa para Azure.

> [!NOTE]
> Os tutoriais mostram-lhe o caminho de implantação mais simples para um cenário para que possa configurar rapidamente uma prova de conceito. Os tutoriais utilizam opções predefinidas sempre que possível e não mostram todas as definições e caminhos possíveis. Para obter instruções detalhadas, reveja os How-tos para Azure Migrate.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.


## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, tem de:

[Reveja](./agent-based-migration-architecture.md) a arquitetura da migração.

## <a name="prepare-azure"></a>Preparar o Azure

Prepare o Azure para a migração com a Migração do Servidor.

**Tarefa** | **Detalhes**
--- | ---
**Criar um projeto do Azure Migrate** | A sua conta Azure precisa de permissões de Contribuidor ou Proprietário para criar um projeto.
**Verifique permissões para a sua conta Azure** | A sua conta Azure necessita de permissões para criar um VM e escrever para um disco gerido pelo Azure.


### <a name="assign-permissions-to-create-project"></a>Atribuir permissões para criar projeto

1. No portal do Azure, abra a subscrição e selecione **Controlo de acesso (IAM)** .
2. No **Acesso ao Cheque,** encontre a conta relevante e clique nela para ver permissões.
3. Deve ter permissões **de Colaborador** ou **Proprietário.**
    - Se acabou de criar uma conta gratuita do Azure, é o proprietário da sua subscrição.
    - Se não for o proprietário da subscrição, trabalhe com o proprietário para atribuir a função.


### <a name="assign-azure-account-permissions"></a>Atribuir permissões de conta Azure

Atribua a função contribuidora da máquina virtual à conta Azure. Isto fornece permissões para:

- Criar uma VM no grupo de recursos selecionado.
- Criar uma VM na rede virtual selecionada.
- Escreva para um disco gerido pelo Azure. 

### <a name="create-an-azure-network"></a>Criar uma rede Azure

[Crie](../virtual-network/manage-virtual-network.md#create-a-virtual-network) uma rede virtual Azure (VNet). Quando se replica para Azure, os VMs Azure são criados e unidos ao Azure VNet que especifica quando configura a migração.

## <a name="prepare-for-migration"></a>Prepare para a migração

Para se preparar para a migração física do servidor, é necessário verificar as definições do servidor físico e preparar-se para implementar um aparelho de replicação.

### <a name="check-machine-requirements-for-migration"></a>Verifique os requisitos da máquina para a migração

Certifique-se de que as máquinas cumprem os requisitos de migração para Azure. 

> [!NOTE]
> Ao migrar máquinas físicas, a Azure Migrate:Server Migration utiliza a mesma arquitetura de replicação que a recuperação de desastres baseada em agentes no serviço de recuperação do local de Azure, e alguns componentes partilham a mesma base de código. Algum conteúdo pode ligar-se à documentação de Recuperação do Site.

1. [Verifique os](migrate-support-matrix-physical-migration.md#physical-server-requirements) requisitos do servidor físico.
2. Verifique se as máquinas no local que replica ao Azure cumprem os [requisitos da Azure VM](migrate-support-matrix-physical-migration.md#azure-vm-requirements).
3. São necessárias algumas alterações nos VM antes de as migrar para Azure.
    - Para alguns sistemas operativos, a Azure Migrate faz estas alterações automaticamente. 
    - É importante fazer estas mudanças antes de começar a migração. Se migrar o VM antes de fazer a alteração, o VM pode não arrancar em Azure. Reveja as alterações do [Windows](prepare-for-migration.md#windows-machines) e [do Linux](prepare-for-migration.md#linux-machines) que precisa de fazer.

### <a name="prepare-a-machine-for-the-replication-appliance"></a>Preparar uma máquina para o aparelho de replicação

Azure Migrate:Server Migration usa um aparelho de replicação para replicar máquinas para Azure. O aparelho de replicação executa os seguintes componentes.

- **Servidor de configuração**: O servidor de configuração coordena as comunicações entre as instalações e o Azure e gere a replicação de dados.
- **Servidor de**processo : O servidor de processo funciona como um gateway de replicação. Recebe dados de replicação; otimiza-o com caching, compressão e encriptação, e envia-o para uma conta de armazenamento de cache em Azure. 

Prepare-se para a colocação do aparelho da seguinte forma:

- Prepara-se uma máquina para hospedar o aparelho de replicação. [Reveja](migrate-replication-appliance.md#appliance-requirements) os requisitos da máquina.
- O aparelho de replicação utiliza o MySQL. Reveja as [opções](migrate-replication-appliance.md#mysql-installation) de instalação do MySQL no aparelho.
- Reveja os URLs Azure necessários para que o aparelho de replicação aceda a nuvens [públicas](migrate-replication-appliance.md#url-access) e [governamentais.](migrate-replication-appliance.md#azure-government-url-access)
- Reveja os requisitos de acesso [porta] (migração-replicação-aparelho.md#porta-acesso) para o aparelho de replicação.

> [!NOTE]
> O aparelho de replicação não deve ser instalado numa máquina de origem que pretende replicar ou no aparelho de deteção e avaliação Azure Migrate que possa ter instalado anteriormente.

## <a name="add-the-server-migration-tool"></a>Adicione a ferramenta de migração do servidor

Crie um projeto Azure Migrate e, em seguida, adicione-lhe a ferramenta de migração do servidor.

1. No portal do Azure > **Todos os serviços**, procure **Azure Migrate**.
2. Em **Serviços**, selecione **Azure Migrate**.
3. Em **Descrição geral**, clique em **Avaliar e migrar servidores**.
4. No **âmbito do Discover, avaliar e migrar servidores,** clique em **Avaliar e migrar servidores**.

    ![Detetar e avaliar servidores](./media/tutorial-migrate-physical-virtual-machines/assess-migrate.png)

5. Em **Detetar, avaliar e migrar servidores**, clique em **Adicionar ferramentas**.
6. Em **Migrar projeto**, selecione a sua subscrição do Azure e crie um grupo de recursos, caso não tenha um.
7. Em **Detalhes do Projeto**, especifique o nome do projeto e a geografia na qual pretende criar o projeto e clique em **Seguinte**. Reveja geografias apoiadas para nuvens [públicas](migrate-support-matrix.md#supported-geographies-public-cloud) e [governamentais.](migrate-support-matrix.md#supported-geographies-azure-government)

    ![Criar um projeto do Azure Migrate](./media/tutorial-migrate-physical-virtual-machines/migrate-project.png)

8. Em **Selecionar ferramenta de avaliação**, selecione **Ignorar a adição de uma ferramenta de avaliação por enquanto** > **Seguinte**.
9. Em **Selecionar ferramenta de migração**, selecione **Azure Migrate: Migração de Servidores** > **Seguinte**.
10. Em **Analisar + adicionar ferramentas**, analise as definições e clique em **Adicionar ferramentas**
11. Depois de adicionar a ferramenta, aparece no projeto Azure **Servers**Migrate >  >  **ferramentas de migração**de servidores .

## <a name="set-up-the-replication-appliance"></a>Configurar o aparelho de replicação

O primeiro passo da migração é configurar o aparelho de replicação. Para configurar o aparelho para a migração física do servidor, descarregue o ficheiro do instalador para o aparelho e, em seguida, coloque-o na [máquina que preparou](#prepare-a-machine-for-the-replication-appliance). Depois de instalar o aparelho, registe-o com a Migração do Servidor Azure Migrate.


### <a name="download-the-replication-appliance-installer"></a>Descarregue o instalador de aparelhos de replicação

1. No projeto Azure Migrate > **Servers,** em **Azure Migrate: Server Migration**, clique em **Discover**.

    ![Detetar VMs](./media/tutorial-migrate-physical-virtual-machines/migrate-discover.png)

3. In **Discover machines**  >  **Are your machines virtualized?** **Not virtualized/Other**
4. Na **região Alvo,** selecione a região de Azure para a qual pretende migrar as máquinas.
5. **Selecione Confirme que a região-alvo para a migração é o nome da região.**
6. Clique **em Criar recursos.** Isto cria um cofre de recuperação do local de Azure em segundo plano.
    - Se já estabeleceu migração com a Migração do Servidor Azure Migrate, a opção-alvo não pode ser configurada, uma vez que os recursos foram criados anteriormente.    
    - Não é possível alterar a região alvo deste projeto depois de clicar neste botão.
    - Todas as migrações subsequentes são para esta região.

7. Na **Install a replication appliance** **instalação de um novo aparelho de replicação?**
9. Em **Transferir e instalar o software do aparelho de replicação,** descarregue o instalador do aparelho e a chave de registo. É necessário fazer a chave para registar o aparelho. A chave é válida por cinco dias após o download.

    ![Fornecedor de descarregamento](media/tutorial-migrate-physical-virtual-machines/download-provider.png)

10. Copie o ficheiro de configuração do aparelho e o ficheiro chave para a máquina do Windows Server 2016 que criou para o aparelho.
11. Após a conclusão da instalação, o assistente de configuração do Aparelho será lançado automaticamente (Também pode lançar o assistente manualmente utilizando o atalho cspsconfigtool que é criado no ambiente de trabalho do aparelho). Utilize o separador 'Contas's Manage do assistente para adicionar detalhes da conta a utilizar para impulsionar a instalação do serviço Mobility. Neste tutorial, vamos instalar manualmente o Serviço de Mobilidade em VMs de origem a serem replicados, por isso crie uma conta falsa neste passo e prossiga. Pode fornecer os seguintes detalhes para criar a conta falsa - "guest" como o nome amigável, "username" como nome de utilizador, e "password" como palavra-passe para a conta. Utilizará esta conta falsa na fase de Replicação ativa. 

12. Depois de o aparelho ter sido reiniciado após a configuração, nas **máquinas Discover**, selecione o novo aparelho no **Servidor de Configuração Select**e clique em Finalizar o **registo**. Finalizar o registo executa algumas tarefas finais para preparar o aparelho de replicação.

    ![Finalizar o registo](./media/tutorial-migrate-physical-virtual-machines/finalize-registration.png)

Pode demorar algum tempo depois de finalizar o registo até que as máquinas descobertas apareçam na Migração do Servidor Azure Migrate. À medida que os VMs são descobertos, a contagem de **servidores descobertos** aumenta.

![Servidores descobertos](./media/tutorial-migrate-physical-virtual-machines/discovered-servers.png)


## <a name="install-the-mobility-service"></a>Instalar o serviço de Mobilidade

Nas máquinas que pretende migrar, tem de instalar o agente de serviço de Mobilidade. Os instaladores do agente estão disponíveis no aparelho de replicação. Encontre o instalador certo e instale o agente em cada máquina que pretende migrar. Proceda da seguinte forma:

1. Inscreva-se no aparelho de replicação.
2. Navegue para **%ProgramData%\ASR\home\home\svsystems\pushinstallsvc\repositório**.
3. Encontre o instalador para o sistema operativo e versão da máquina. Rever [sistemas operativos suportados.](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines) 
4. Copie o ficheiro do instalador para a máquina que pretende migrar.
5. Certifique-se de que tem a frase-passe gerada quando lançou o aparelho.
    - Guarde o ficheiro num ficheiro de texto temporário na máquina.
    - Pode obter a palavra-passe no aparelho de replicação. A partir da linha de comando, corra **C:\ProgramData\ASR\home\svsystems\bin\genpassphrase.exe -v** para ver a frase de passagem atual.
    - Não regenerar a frase. Isto quebrará a conectividade e terá de voltar a registar o aparelho de replicação.


### <a name="install-on-windows"></a>Instalar no Windows

1. Extrair o conteúdo do ficheiro instalador numa pasta local (por exemplo C:\Temp) na máquina, da seguinte forma:

    ```
    ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
    MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
    cd C:\Temp\Extracted
    ```
2. Executar o Instalador do Serviço de Mobilidade:
    ```
   UnifiedAgent.exe /Role "MS" /Silent
    ```
3. Registar o agente com o aparelho de replicação:
    ```
    cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
    UnifiedAgentConfigurator.exe  /CSEndPoint <replication appliance IP address> /PassphraseFilePath <Passphrase File Path>
    ```

### <a name="install-on-linux"></a>Instalar no Linux

1. Extrair o conteúdo da bola de tarball do instalador para uma pasta local (por exemplo/tmp/MobSvcInstaller) na máquina, da seguinte forma:
    ```
    mkdir /tmp/MobSvcInstaller
    tar -C /tmp/MobSvcInstaller -xvf <Installer tarball>
    cd /tmp/MobSvcInstaller
    ```
2. Executar o script do instalador:
    ```
    sudo ./install -r MS -q
    ```
3. Registar o agente com o aparelho de replicação:
    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <replication appliance IP address> -P <Passphrase File Path>
    ```

## <a name="replicate-machines"></a>Máquinas de replicar

Agora, selecione máquinas para migração. 

> [!NOTE]
> Pode replicar até 10 máquinas juntas. Se precisar de replicar mais, replique-os simultaneamente em lotes de 10.

1. No projeto Azure Migrate > **Servers**, **Azure Migrate: Server Migration**, clique em **Replicar**.

    ![Screenshot do ecrã Azure Migrate - Servidores mostrando o botão Replicar selecionado em Azure Migrate: Migração de servidores sob ferramentas de migração.](./media/tutorial-migrate-physical-virtual-machines/select-replicate.png)

2. Em **Replicar,**> **As definições de**  >  **Origem são virtualizadas?** **Not virtualized/Other**
3. No **aparelho no local,** selecione o nome do aparelho Azure Migrate que instalou.
4. No **Process Server,** selecione o nome do aparelho de replicação.
6. Nas **credenciais do Guest**, selecione a conta falsa criada anteriormente durante a instalação do [instalador de replicação](#download-the-replication-appliance-installer) para instalar manualmente o serviço Mobility (a instalação push não está suportada). Em seguida, clique **em Seguinte: Máquinas virtuais**.   

    ![Screenshot do separador Definições De Origem no ecrã Replica com o campo de credenciais do Convidado em destaque.](./media/tutorial-migrate-physical-virtual-machines/source-settings.png)

7. Em **Máquinas Virtuais,** nas **No, I'll specify the migration settings manually** **definições de migração de importação a partir de uma avaliação?**
8. Verifique cada VM que deseja migrar. Em seguida, clique em **Seguinte: Definições de destino**.

    ![Selecione VMs](./media/tutorial-migrate-physical-virtual-machines/select-vms.png)


9. Em **Definições de destino**, selecione a subscrição, assim como a região de destino para a qual vai migrar, e especifique o grupo de recursos no qual as VMs do Azure vão residir após a migração.
10. Em **Rede Virtual**, selecione a VNet/sub-rede do Azure na qual as VMs do Azure vão ser associadas após a migração.
11. Nas **opções de Disponibilidade,** selecione:
    -  Zona de Disponibilidade para fixar a máquina migrada a uma zona de disponibilidade específica na região. Utilize esta opção para distribuir servidores que formam um nível de aplicação de vários nós em zonas de disponibilidade. Se selecionar esta opção, terá de especificar a Zona de Disponibilidade para utilizar para cada uma das máquinas selecionadas no separador Compute. Esta opção só está disponível se a região-alvo selecionada para a migração suportar Zonas de Disponibilidade
    -  Disponibilidade Definir para colocar a máquina migrada num Conjunto de Disponibilidade. O Grupo de Recursos-Alvo que foi selecionado deve ter um ou mais conjuntos de disponibilidade para utilizar esta opção.
    - Não é necessária nenhuma opção de redundância de infraestrutura se não precisar de nenhuma destas configurações de disponibilidade para as máquinas migradas.
12. Em **Benefício Híbrido do Azure**:

    - Selecione **Não** se não quiser aplicar o Benefício Híbrido do Azure. Em seguida, clique em **Seguinte**.
    - Selecione **Sim** se tiver computadores Windows Server abrangidos com subscrições ativas do Software Assurance ou do Windows Server e quiser aplicar o benefício aos computadores que está a migrar. Em seguida, clique em **Seguinte**.

    ![Definições de destino](./media/tutorial-migrate-physical-virtual-machines/target-settings.png)

13. No **Compute,** reveja o nome, tamanho, tipo de disco de SO e configuração de disponibilidade (se selecionado no passo anterior). As VMs devem estar em conformidade com os [requisitos do Azure](migrate-support-matrix-physical-migration.md#azure-vm-requirements).

    - **Tamanho VM**: Se estiver a utilizar recomendações de avaliação, o dropdown do tamanho VM mostra o tamanho recomendado. Caso contrário, o Azure Migrate escolhe um tamanho com base na correspondência mais próxima na subscrição do Azure. Como alternativa, escolha um tamanho manual em **Tamanho de VMs do Azure**.
    - **Disco de os:** Especifique o disco DE (arranque) para o VM. O disco do SO é o disco que possui o carregador e o instalador do sistema operativo.
    - **Zona de Disponibilidade**: Especifique a Zona de Disponibilidade a utilizar.
    - **Conjunto de disponibilidade**: Especifique o Conjunto de Disponibilidade a utilizar.

> [!NOTE]
> Se quiser selecionar uma opção de disponibilidade diferente para um conjunto de máquinas virtuais, vá ao passo 1 e repita os passos selecionando diferentes opções de disponibilidade após iniciar a replicação para um conjunto de máquinas virtuais.

   ![Definições de cálculo](./media/tutorial-migrate-physical-virtual-machines/compute-settings.png)

13. Em Discos , **especifique**se os discos VM devem ser replicados para Azure e selecione o tipo de disco (discos geridos padrão SSD/HDD ou discos geridos premium) em Azure. Em seguida, clique em **Seguinte**.
    - Pode excluir discos da replicação.
    - Se excluir discos, estes não estarão presentes na VM do Azure após a migração. 

    ![Definições de disco](./media/tutorial-migrate-physical-virtual-machines/disks.png)


14. Em **Analisar e iniciar a replicação**, analise as definições e clique em **Replicar** para iniciar a replicação inicial para os servidores.

> [!NOTE]
> Pode atualizar as definições de replicação a qualquer momento antes de começar a replicação, **Gerir**  >  **máquinas de replicação**. As definições não podem ser alteradas após o início da replicação.



## <a name="track-and-monitor"></a>Rastreio e monitor

- Quando clicar em **Replicar** um trabalho de replicação inicial começa. 
- Quando o trabalho de Replicação inicial termina com sucesso, as máquinas iniciam a sua replicação inicial ao Azure.
- Após o final da replicação inicial, começa a replicação delta. As alterações incrementais nos discos no local são periodicamente replicadas nos discos de réplica em Azure.


Pode acompanhar o estado do trabalho nas notificações do portal.

Pode monitorizar o estado de replicação clicando em **servidores replicantes** em **Azure Migrate: Migração do servidor**.
![Monitorizar a replicação](./media/tutorial-migrate-physical-virtual-machines/replicating-servers.png)

## <a name="run-a-test-migration"></a>Executar uma migração de teste


Quando a replicação delta começar, pode fazer uma migração de teste para os VMs, antes de executar uma migração completa para Azure. Recomendamos vivamente que o faça pelo menos uma vez por cada máquina, antes de a migrar.

- A realização de um teste de controlos de migração de que a migração funcionará como esperado, sem afetar as máquinas no local, que permanecem operacionais, e continuam a replicar-se. 
- A migração de testes simula a migração criando um VM Azure usando dados replicados (normalmente migrando para um VNet não produtivo na sua assinatura Azure).
- Pode utilizar o teste replicado Azure VM para validar a migração, realizar testes de aplicações e resolver quaisquer problemas antes da migração completa.

Faça uma migração de teste da seguinte forma:


1. Nos **objetivos de migração**  >  **Servers**  >  **Azure Migrate: Server Migration**, clique em Test **servidores migrados**.

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

1. No projeto Azure Migrate > **Servers**  >  **Azure Migrate: Server Migration**, clique em **Replicating servidores**.

    ![Replicar servidores](./media/tutorial-migrate-physical-virtual-machines/replicate-servers.png)

2. Em **Replicar computadores**, clique com o botão direito do rato na VM > **Migrar**.
3. Em **Migrar**  >  **Desligue as máquinas virtuais e realize uma migração planeada sem perda de dados**, selecione **Yes**  >  **OK**.
    - Se não quiser desligar a VM, selecione **Não**
    
    Nota: Para a migração do servidor físico, a recomendação é baixar a aplicação como parte da janela de migração (não deixe que as aplicações aceitem quaisquer ligações) e, em seguida, inicie a migração (o servidor precisa de ser mantido em funcionamento, para que as alterações restantes possam ser sincronizadas) antes de a migração ser concluída.

4. Uma tarefa de migração é iniciada para a VM. Controle a tarefa nas notificações do Azure.
5. Após a conclusão da tarefa, pode ver e gerir a VM na página **Máquinas Virtuais**.

## <a name="complete-the-migration"></a>Complete a migração

1. Depois de terminar a migração, clique com a direita na VM > **Parar a migração**. Isto faz o seguinte:
    - Para a replicação para a máquina no local.
    - Remove a máquina da contagem de **servidores replicantes** em Azure Migrate: Migração do servidor.
    - Limpa a informação do estado de replicação para a máquina.
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
    - Restrinja o tráfego de rede para os pontos finais de gestão com os [Grupos de Segurança de Rede](../virtual-network/security-overview.md).
    - Implemente o [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md) para ajudar a proteger discos e a manter os dados protegidos contra roubo e acesso não autorizado.
    - Leia mais sobre como [proteger recursos de IaaS](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/) e aceda ao [Centro de Segurança do Azure](https://azure.microsoft.com/services/security-center/).
- Para monitorização e gestão:
    - Considere implementar o [Azure Cost Management](../cost-management-billing/cloudyn/overview.md) para monitorizar a utilização e as despesas do recurso.


## <a name="next-steps"></a>Passos seguintes

Investigue a [jornada de migração](/azure/architecture/cloud-adoption/getting-started/migrate) em nuvem no Quadro de Azure Cloud Adopt.
