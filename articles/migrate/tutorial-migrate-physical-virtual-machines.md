---
title: Migrar máquinas como servidor físico para Azure com O Migração Azure.
description: Este artigo descreve como migrar máquinas físicas para Azure com Azure Migrate.
ms.topic: tutorial
ms.date: 02/03/2020
ms.custom: MVC
ms.openlocfilehash: 3fbc94464c139add6e275890e1a1e415b2826f0d
ms.sourcegitcommit: a460fdc19d6d7af6d2b5a4527e1b5c4e0c49942f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77069528"
---
# <a name="migrate-machines-as-physical-servers-to-azure"></a>Migrar máquinas como servidores físicos para O Azure

Este artigo mostra-lhe como migrar máquinas como servidores físicos para o Azure, utilizando a ferramenta Migração De Migração De Migração De Migração De Migração De Migração De Migração De Migração De Migração De Migração De Migração De Migração De Migração De Migração De Migração de Emigrantes Azure.Server. As máquinas migratórias, tratando-as como servidores físicos, são úteis em vários cenários:

- Migrar servidores físicos locais.
- Migre VMs virtualizadas por plataformas como Xen, KVM.
- Migrar VMs Hyper-V ou VMware, se por alguma razão você não puder usar o processo de migração padrão para a migração [hyper-V](tutorial-migrate-hyper-v.md)ou [VMware.](server-migrate-overview.md)
- Migre VMs em execução em nuvens privadas.
- Migre VMs em execução em nuvens públicas, como Amazon Web Services (AWS) ou Google Cloud Platform (GCP).


[A Azure Migrate](migrate-services-overview.md) fornece um centro central para acompanhar a descoberta, avaliação e migração das suas aplicações e cargas de trabalho no local, e casos de VM em nuvem, para O Azure. O hub fornece ferramentas azure migrate para avaliação e migração, bem como ofertas de fornecedores de software independentes de terceiros (ISV).


Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Prepare o Azure para migração com a ferramenta de migração de servidor de migrações para Azure.
> * Verifique os requisitos de computadores que você deseja migrar e prepare um computador para o dispositivo de replicação de migrações para Azure que é usado para descobrir e migrar computadores para o Azure.
> * Adicione a ferramenta de migração de servidor migrações para Azure no Hub migrações para Azure.
> * Configure o dispositivo de replicação.
> * Instale o serviço de mobilidade nos computadores que você deseja migrar.
> * Ative a replicação.
> * Execute uma migração de teste para verificar se tudo está funcionando conforme o esperado.
> * Execute uma migração completa para o Azure.

> [!NOTE]
> Os tutoriais mostram o caminho de implantação mais simples para um cenário, para que você possa configurar rapidamente uma prova de conceito. Os tutoriais usam as opções padrão sempre que possível e não mostram todas as configurações e caminhos possíveis. Para obter instruções detalhadas, examine o How-tos para migrações para Azure.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.


## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, tem de:

1. [Reveja](migrate-architecture.md) a arquitetura da migração.
2. Verifique se sua conta do Azure é atribuída à função colaborador da máquina virtual, para que você tenha permissões para:

    - Criar uma VM no grupo de recursos selecionado.
    - Criar uma VM na rede virtual selecionada.
    - Gravar em um disco gerenciado do Azure. 

3. [Criar uma rede Azure.](../virtual-network/manage-virtual-network.md#create-a-virtual-network) Quando você replica para o Azure, as VMs do Azure são criadas e Unidas a uma rede do Azure que você especifica ao configurar a migração.


## <a name="prepare-azure"></a>Preparar o Azure

Configure as permissões do Azure antes de migrar com a migração de servidor de migrações para Azure.

- **Crie um projeto**: A sua conta Azure precisa de permissões para criar um projeto Azure Migrate. 
- **Registe o aparelho de replicação Azure Migrate**: O aparelho de replicação cria e regista uma aplicação azure ative directory na sua conta Azure. Delegar permissões para isso.
- **Criar achave-chave**: Para migrar máquinas, a Azure Migrate cria um Cofre chave no grupo de recursos, para gerir as chaves de acesso da conta de armazenamento de replicação na sua subscrição. Para criar o cofre, você precisa de permissões de atribuição de função no grupo de recursos no qual o projeto de migração do Azure reside. 


### <a name="assign-permissions-to-create-project"></a>Atribuir permissões para criar projeto

1. No portal Azure, abra a subscrição e selecione controlo de **acesso (IAM)** .
2. No **Check access,** encontre a conta relevante e clique nela para visualizar permissões.
3. Deve ter permissões de **Contribuinte** ou **Proprietário.**
    - Se você acabou de criar uma conta gratuita do Azure, você é o proprietário da sua assinatura.
    - Se você não for o proprietário da assinatura, trabalhe com o proprietário para atribuir a função.

### <a name="assign-permissions-to-register-the-replication-appliance"></a>Atribuir permissões para registrar o dispositivo de replicação

Para a migração baseada em agente, delegue permissões para migração de servidor de migrações para Azure para criar e registrar um aplicativo do Azure AD em sua conta. Você pode atribuir permissões usando um dos seguintes métodos:

- Um locatário/administrador global pode conceder permissões a usuários no locatário para criar e registrar aplicativos do Azure AD.
- Um locatário/administrador global pode atribuir a função de desenvolvedor de aplicativo (que tem as permissões) à conta.

Vale a pena observar que:

- Os aplicativos não têm nenhuma outra permissão de acesso na assinatura diferente daquelas descritas acima.
- Você só precisa dessas permissões ao registrar um novo dispositivo de replicação. Você pode remover as permissões depois que o dispositivo de replicação for configurado. 


#### <a name="grant-account-permissions"></a>Permissões de conta de concessão

O locatário/administrador global pode conceder permissões da seguinte maneira

1. Em Azure AD, o inquilino/administrador global deve navegar para **o Diretório Ativo do Azure** > **utilizadores** > **Definições**de utilizador .
2. O administrador deve definir **os registos** da App para **Sim**.

    ![Permissões do Azure AD](./media/tutorial-migrate-physical-virtual-machines/aad.png)

> [!NOTE]
> Essa é uma configuração padrão que não é sensível. [Saiba mais](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).

#### <a name="assign-application-developer-role"></a>Atribuir função de desenvolvedor de aplicativo 

O locatário/administrador global pode atribuir a função de desenvolvedor de aplicativo a uma conta. [Saiba mais](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="assign-permissions-to-create-key-vault"></a>Atribuir permissões para criar Key Vault

Atribua permissões de atribuição de função no grupo de recursos no qual o projeto de migrações para Azure reside, da seguinte maneira:

1. No grupo de recursos no portal Azure, selecione controlo de **acesso (IAM)** .
2. No **Check access,** encontre a conta relevante e clique nela para visualizar permissões. Precisa de permissões de **Proprietário** (ou **Colaborador** e Administrador de **Acesso ao Utilizador).**
3. Se você não tiver as permissões necessárias, solicite-as do proprietário do grupo de recursos. 

## <a name="prepare-for-migration"></a>Prepare para a migração

### <a name="check-machine-requirements-for-migration"></a>Verificar os requisitos do computador para a migração

Verifique se os computadores estão em conformidade com os requisitos de migração para o Azure. 

> [!NOTE]
> A migração baseada em agente com a migração de servidor de migrações para Azure baseia-se nos recursos do serviço de Azure Site Recovery. Alguns requisitos podem ser vinculados à documentação Site Recovery.

1. [Verifique os](migrate-support-matrix-physical-migration.md#physical-server-requirements) requisitos físicos do servidor.
2. Verifique as configurações da VM. As máquinas no local que replica para o Azure devem cumprir os requisitos da [Azure VM](migrate-support-matrix-physical-migration.md#azure-vm-requirements).


### <a name="prepare-a-machine-for-the-replication-appliance"></a>Preparar um computador para o dispositivo de replicação

A migração de servidor de migrações para Azure usa um dispositivo de replicação para replicar computadores no Azure. O dispositivo de replicação executa os seguintes componentes.

- Servidor de **configuração**: O servidor de configuração coordena as comunicações entre as instalações e o Azure e gere a replicação de dados.
- **Servidor de processos**: O servidor de processo funciona como um portal de replicação. Recebe dados de replicação; otimiza-o com cache, compressão e encriptação, e envia-o para uma conta de armazenamento em cache em Azure. 

Antes de começar, você precisa preparar um computador com Windows Server 2016 para hospedar o dispositivo de replicação. A máquina deve cumprir [estes requisitos.](migrate-replication-appliance.md) O dispositivo não deve ser instalado em um computador de origem que você deseja proteger.


## <a name="add-the-azure-migrate-server-migration-tool"></a>Adicionar a ferramenta de migração de servidor de migrações para Azure

Configure um projeto de migrações para Azure e adicione a ferramenta de migração de servidor do Azure Migrate para ele.

1. No portal do Azure > **Todos os serviços**, procure **Azure Migrate**.
2. Em **Serviços**, selecione **Azure Migrate**.
3. Em **Descrição geral**, clique em **Avaliar e migrar servidores**.
4. Em **Discover, avalie e emigra os servidores,** clique em **avaliar e migrar servidores**.

    ![Descobrir e avaliar servidores](./media/tutorial-migrate-physical-virtual-machines/assess-migrate.png)

5. Em **Detetar, avaliar e migrar servidores**, clique em **Adicionar ferramentas**.
6. Em **Migrar projeto**, selecione a sua subscrição do Azure e crie um grupo de recursos, caso não tenha um.
7. Em Detalhes do **Projeto,** especifique o nome do projeto e geografia em que pretende criar o projeto, e clique em **Next**

    ![Criar um projeto de migrações para Azure](./media/tutorial-migrate-physical-virtual-machines/migrate-project.png)

    Você pode criar um projeto de migrações para Azure em qualquer uma dessas regiões.

    **Geografia** | **Região**
    --- | ---
    Ásia | Sudeste Asiático
    Europa | Europa Setentrional ou Europa Ocidental
    Estados Unidos | Leste dos EUA ou Oeste EUA Central

    A geografia especificada para o projeto só é utilizada para armazenar os metadados recolhidos das VMs no local. Você pode selecionar qualquer região de destino para a migração real.
8. Na ferramenta de **avaliação Select,** selecione **Skip adicionando uma ferramenta de avaliação para agora** > **Seguinte**.
9. Na **ferramenta de migração Select**, selecione **Azure Migrate: Server Migration** > **Next**.
10. Em **Analisar + adicionar ferramentas**, analise as definições e clique em **Adicionar ferramentas**
11. Depois de adicionar a ferramenta, aparece no projeto Migratório Azure > Servidores > **Ferramentas migratórias.**

## <a name="set-up-the-replication-appliance"></a>Configurar o dispositivo de replicação

A primeira etapa da migração é configurar o dispositivo de replicação. Faça o download do ficheiro do instalador para o aparelho e execute-o na [máquina que preparou](#prepare-a-machine-for-the-replication-appliance). Depois de instalar o dispositivo, registre-o com a migração de servidor de migrações para Azure.


### <a name="download-the-replication-appliance-installer"></a>Baixar o instalador do dispositivo de replicação

1. No projeto Azure Migrate > **Servers,** em **Azure Migrate: Server Migration,** clique **em Discover**.

    ![Detetar VMs](./media/tutorial-migrate-physical-virtual-machines/migrate-discover.png)

3. Em **Discover machines** > **As suas máquinas estão virtualizadas?**
4. Na **região Target,** selecione a região de Azure para a qual pretende migrar as máquinas.
5. Selecione **Confirmar que a região alvo para a migração é o nome da região**.
6. Clique em **Criar recursos.** Isso cria um cofre de Azure Site Recovery em segundo plano.
    - Se você já configurou a migração com a migração de servidor de migrações para Azure, a opção de destino não pode ser configurada, pois os recursos foram configurados anteriormente.
    - Você não pode alterar a região de destino deste projeto depois de clicar nesse botão.
    - Todas as migrações subsequentes são para essa região.

7. Em Se pretender instalar um novo aparelho de **replicação,** selecione **Instale um aparelho**de replicação .
9. No **Descarregue e instale o software do aparelho de replicação,** descarregue o instalador do aparelho e a chave de registo. Você precisa da chave para registrar o dispositivo. A chave é válida por cinco dias após ser baixada.

    ![Baixar provedor](media/tutorial-migrate-physical-virtual-machines/download-provider.png)

10. Copie o arquivo de instalação do dispositivo e o arquivo de chave para o computador 2016 do Windows Server que você criou para o dispositivo.
11. Execute o arquivo de instalação do dispositivo de replicação, conforme descrito no próximo procedimento.
12. Depois de o aparelho ter reiniciado após a instalação, nas **máquinas Discover,** selecione o novo aparelho no **Select Configuration Server**e clique em Finalizar a **inscrição**. Finalizar o registro executa algumas tarefas finais para preparar o dispositivo de replicação.

    ![Finalizar registro](./media/tutorial-migrate-physical-virtual-machines/finalize-registration.png)

Pode levar algum tempo depois de finalizar o registo até que as máquinas descobertas apareçam na Migração do Servidor Migratório Migratório Migratório De Migração de Migração de Migração de Migração de Migração de Migração de Migração de Migração de Migração. À medida que os VMs são descobertos, os **servidores descobertos contam aumentos.**

![Servidores descobertos](./media/tutorial-migrate-physical-virtual-machines/discovered-servers.png)


## <a name="install-the-mobility-service"></a>Instalar o serviço de Mobilidade

Em computadores que você deseja migrar, você precisa instalar o agente do serviço de mobilidade. Os instaladores de agente estão disponíveis no dispositivo de replicação. Você encontra o instalador correto e instala o agente em cada computador que deseja migrar. Faça isto da seguinte forma:

1. Entre no dispositivo de replicação.
2. Navegue para **%ProgramData%\ASR\home\svsystems\pushinstallsvc\repositório**.
3. Localize o instalador para a versão e o sistema operacional do computador. Reveja [os sistemas operativos suportados](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#replicated-machines)pela revisão. 
4. Copie o arquivo do instalador para o computador que você deseja migrar.
5. Certifique-se de que você tenha a frase secreta que foi gerada quando você implantou o dispositivo.
    - Armazene o arquivo em um arquivo de texto temporário no computador.
    - Você pode obter a frase secreta no dispositivo de replicação. A partir da linha de comando, executar **C:\ProgramData\ASR\home\svsystems\bin\genpassphrase.exe -v** para ver a frase de passe atual.
    - Não gere novamente a senha. Isso interromperá a conectividade e você terá que registrar novamente o dispositivo de replicação.


### <a name="install-on-windows"></a>Instalar no Windows

1. Extraia o conteúdo do arquivo do instalador para uma pasta local (por exemplo, C:\Temp) no computador, da seguinte maneira:

    ```
    ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
    MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
    cd C:\Temp\Extracted
    ```
2. Execute o instalador do serviço de mobilidade:
    ```
   UnifiedAgent.exe /Role "MS" /Silent
    ```
3. Registre o agente com o dispositivo de replicação:
    ```
    cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
    UnifiedAgentConfigurator.exe  /CSEndPoint <replication appliance IP address> /PassphraseFilePath <Passphrase File Path>
    ```

### <a name="install-on-linux"></a>Instalar no Linux

1. Extraia o conteúdo do instalador tarball para uma pasta local (por exemplo,/tmp/MobSvcInstaller) no computador, da seguinte maneira:
    ```
    mkdir /tmp/MobSvcInstaller
    tar -C /tmp/MobSvcInstaller -xvf <Installer tarball>
    cd /tmp/MobSvcInstaller
    ```
2. Execute o script do instalador:
    ```
    sudo ./install -r MS -q
    ```
3. Registre o agente com o dispositivo de replicação:
    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <replication appliance IP address> -P <Passphrase File Path>
    ```

## <a name="replicate-machines"></a>Replicar máquinas

Agora, selecione computadores para migração. 

> [!NOTE]
> Você pode replicar até 10 computadores juntos. Se você precisar replicar mais, replique-os simultaneamente em lotes de 10.

1. No projeto Azure Migrate > **Servers,** **Azure Migrate: Server Migration,** clique em **Replicate**.

    ![Replicar VMs](./media/tutorial-migrate-physical-virtual-machines/select-replicate.png)

2. Em **Replicate**, > **Configurações** de origem >  **As suas máquinas estão virtualizadas?**
3. No **aparelho On-local,** selecione o nome do aparelho Azure Migrate que instalou.
4. No Servidor de **Processos,** selecione o nome do aparelho de replicação.
6. Nas **credenciais de hóspedes,** especifice uma conta de administração VM que será usada para a instalação push do serviço mobility. Neste tutorial, estamos instalando o serviço de mobilidade manualmente, para que você possa adicionar qualquer conta fictícia. Em seguida, clique em **Seguinte: Máquinas virtuais**.

    ![Replicar VMs](./media/tutorial-migrate-physical-virtual-machines/source-settings.png)

7. Nas **Máquinas Virtuais**, nas **definições de migração de importação de uma avaliação?**
8. Verifique cada VM que você deseja migrar. Em seguida, clique em **Seguinte: Definições de destino**.

    ![Selecione VMs](./media/tutorial-migrate-physical-virtual-machines/select-vms.png)


9. Em **Definições de destino**, selecione a subscrição, assim como a região de destino para a qual vai migrar, e especifique o grupo de recursos no qual as VMs do Azure vão residir após a migração.
10. Em **Rede Virtual**, selecione a VNet/sub-rede do Azure na qual as VMs do Azure vão ser associadas após a migração.
11. Em **Benefício Híbrido do Azure**:

    - Selecione **Não** se não quiser aplicar o Benefício Híbrido do Azure. Clique depois em **Seguinte**.
    - Selecione **Sim** se tiver computadores Windows Server abrangidos com subscrições ativas do Software Assurance ou do Windows Server e quiser aplicar o benefício aos computadores que está a migrar. Clique depois em **Seguinte**.

    ![Configurações de destino](./media/tutorial-migrate-physical-virtual-machines/target-settings.png)

12. Em **Computação**, analise o nome, o tamanho, o tipo de disco do SO e o conjunto de disponibilidade das VMs. As VMs devem estar em conformidade com os [requisitos do Azure](migrate-support-matrix-physical-migration.md#azure-vm-requirements).

    - **Tamanho VM**: Por padrão, a Migração do Servidor Migratório Azure escolhe um tamanho com base na correspondência mais próxima da subscrição azure. Como alternativa, escolha um tamanho manual em **Tamanho de VMs do Azure**. 
    - **Disco OS:** Especifique o disco OS (boot) para o VM. O disco do SO é o disco que possui o carregador e o instalador do sistema operativo. 
    - **Conjunto de disponibilidade**: Se o VM estiver num conjunto de disponibilidade azure após a migração, especifique o conjunto. O conjunto deve estar no grupo de recursos de destino especificado para a migração.

    ![Configurações de computação](./media/tutorial-migrate-physical-virtual-machines/compute-settings.png)

13. Nos **Discos,** especifique se os discos VM devem ser replicados para O Azure e selecione o tipo de disco (SSD/HDD padrão ou discos geridos premium) em Azure. Clique depois em **Seguinte**.
    - Pode excluir discos da replicação.
    - Se excluir discos, estes não estarão presentes na VM do Azure após a migração. 

    ![Definições do disco](./media/tutorial-migrate-physical-virtual-machines/disks.png)


14. Em **Analisar e iniciar a replicação**, analise as definições e clique em **Replicar** para iniciar a replicação inicial para os servidores.

> [!NOTE]
> Pode atualizar as definições de replicação a qualquer momento antes do início da replicação, **gerir** > **máquinas de replicação**. As definições não podem ser alteradas após o início da replicação.



## <a name="track-and-monitor"></a>Acompanhar e monitorar

- Quando clicar em **Replicar** começa um trabalho de replicação de arranque. 
- Quando o trabalho iniciar replicação é concluído com êxito, os computadores começam sua replicação inicial para o Azure.
- Após a conclusão da replicação inicial, a replicação delta começa. As alterações incrementais em discos locais são replicadas periodicamente para os discos de réplica no Azure.


Você pode acompanhar o status do trabalho nas notificações do Portal.

Pode monitorizar o estado de replicação clicando em **servidores de replicação** em **Azure Migrate: Server Migration**.
![monitor](./media/tutorial-migrate-physical-virtual-machines/replicating-servers.png) de replicação

## <a name="run-a-test-migration"></a>Executar uma migração de teste


Quando a replicação delta é iniciada, você pode executar uma migração de teste para as VMs antes de executar uma migração completa para o Azure. É altamente recomendável que você faça isso pelo menos uma vez para cada computador, antes de migrá-lo.

- A execução de uma migração de teste verifica se a migração funcionará conforme o esperado, sem afetar os computadores locais, que permanecem operacionais e continuam replicando. 
- A migração de teste simula a migração criando uma VM do Azure usando dados replicados (geralmente migrando para uma VNet de não produção em sua assinatura do Azure).
- Você pode usar a VM do Azure de teste replicado para validar a migração, executar testes de aplicativo e resolver problemas antes da migração completa.

Faça uma migração de teste da seguinte maneira:


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

Depois de verificar se a migração de teste funciona conforme o esperado, você pode migrar os computadores locais.

1. No projeto Azure Migrate > **Servers** > **Azure Migrate: Server Migration,** clique em **servidores de replicação**.

    ![Replicar servidores](./media/tutorial-migrate-physical-virtual-machines/replicate-servers.png)

2. Em **Replicar computadores**, clique com o botão direito do rato na VM > **Migrar**.
3. Em **Migrar** > **Desligar máquinas virtuais e realizar uma migração planeada sem perda de dados**, selecione **Sim** > **OK**.
    - Por predefinição, o Azure Migrate desliga a VM no local e executa uma replicação a pedido para sincronizar quaisquer alterações à VM ocorridas desde a última replicação. Tal garante que não haja perda de dados.
    - Se não quiser desligar a VM, selecione **Não**
4. Uma tarefa de migração é iniciada para a VM. Controle a tarefa nas notificações do Azure.
5. Após a conclusão da tarefa, pode ver e gerir a VM na página **Máquinas Virtuais**.

## <a name="complete-the-migration"></a>Complete a migração

1. Depois da migração ser feita, clique à direita no VM > **Pare a migração**. Isso interrompe a replicação para o computador local e limpa as informações de estado de replicação da VM.
2. Instale o Azure VM [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) ou o agente [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) nas máquinas migradas.
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
    - Bloqueie e limite o acesso ao tráfego de entrada com o [Azure Security Center - Mesmo na administração do tempo](https://docs.microsoft.com/azure/security-center/security-center-just-in-time).
    - Restrinja o tráfego de rede para os pontos finais de gestão com os [Grupos de Segurança de Rede](https://docs.microsoft.com/azure/virtual-network/security-overview).
    - Implemente o [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview) para ajudar a proteger discos e a manter os dados protegidos contra roubo e acesso não autorizado.
    - Leia mais sobre como [proteger recursos de IaaS](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/) e aceda ao [Centro de Segurança do Azure](https://azure.microsoft.com/services/security-center/).
- Para monitorização e gestão:
    - Considere implementar o [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/overview) para monitorizar a utilização e as despesas do recurso.


## <a name="next-steps"></a>Passos seguintes

Investigue a viagem de [migração](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/migrate) em nuvem no Quadro de Adoção de Nuvens Azure.
