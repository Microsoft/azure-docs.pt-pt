---
title: Migrar máquinas virtualizadas ou de computadores físicos no local para o Azure com a migração do servidor de migrar do Azure | Documentos da Microsoft
description: Este artigo descreve como migrar máquinas físicas no local ou em máquinas virtualizadas no Azure com o Azure Migrate migração do servidor.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 07/09/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 006f7270570eba17fb44421b1c181d9ad68ad2ee
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/12/2019
ms.locfileid: "67854183"
---
# <a name="migrate-physical-or-virtualized-servers-to-azure"></a>Migrar servidores físicos ou virtualizados para o Azure 

Este artigo mostra como migrar servidores físicos ou virtualizados para o Azure. A ferramenta de migração do servidor de migrar do Azure oferece a migração de servidores físicos e virtualizados, utilizando a replicação baseada em agente. Utilizar esta ferramenta, pode migrar uma grande variedade de máquinas para o Azure:

- Migre servidores físicos no local.
- Migre VMs virtualizadas por plataformas, como o Xen, KVM.
- Migre o Hyper-V ou VMs do VMware. Isto é útil se por algum motivo não é possível utilizar o fluxo de migração padrão que a migração do servidor de migrar do Azure oferece para [Hyper-V](tutorial-migrate-hyper-v.md), [VMware sem agente](tutorial-migrate-vmware.md) migração, ou [VMware com base em agente](tutorial-migrate-vmware-agent.md) migração.
- Migre VMs em execução em nuvens privadas.
- Migre VMs em execução em clouds públicas, como Amazon Web Services (AWS) ou o Google Cloud Platform (GCP).


[O Azure Migrate](migrate-services-overview.md) fornece a instâncias de um hub central para controlar a deteção, avaliação e migração de suas cargas de trabalho e aplicações no local e na cloud a VM, para o Azure. O hub fornece ferramentas do Azure Migrate para avaliação e migração, bem como ofertas de (ISV fornecedor) de terceiros independentes de software.


Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Prepare o Azure para a migração com a ferramenta de migração do servidor de migrar do Azure.
> * Verificar os requisitos de máquinas que quer migrar e preparar uma máquina para a aplicação de replicação do Azure Migrate é utilizada para detetar e migrar as máquinas para o Azure.
> * Adicione a ferramenta de migração do servidor de migrar do Azure no hub do Azure Migrate.
> * Configure a aplicação de replicação.
> * Instale o serviço de mobilidade nas máquinas que pretende migrar.
> * Ative a replicação.
> * Execute uma migração de teste para se certificar de que está tudo a funcionar conforme esperado.
> * Execute uma migração completa para o Azure.

> [!NOTE]
> Tutoriais mostram-lhe o caminho de implantação mais simples para um cenário para que pode configurar rapidamente a uma prova de conceito. Tutoriais utilizam as opções predefinidas, sempre que possível e não mostram todas as configurações possíveis e caminhos. Para obter instruções detalhadas, consulte os guias de instruções para o Azure Migrate.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.


## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, tem de:

1. [Revisão](migrate-architecture.md) a arquitetura de migração.
2. Certifique-se de que a sua conta do Azure é atribuída a função de contribuinte de Máquina Virtual, para que não tem permissões para:

    - Criar uma VM no grupo de recursos selecionado.
    - Criar uma VM na rede virtual selecionada.
    - Escrever a Azure disco gerido. 

3. [Configurar uma rede do Azure](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Ao replicar para o Azure, as VMs do Azure são criadas e associadas a uma rede do Azure que especificar quando configurar a migração.


## <a name="prepare-azure"></a>Preparar o Azure

Configure as permissões do Azure para poder migrar com o Azure Migrate migração do servidor.

- **Criar um projeto**: Sua conta do Azure necessita de permissões para criar um projeto do Azure Migrate. 
- **Registe-se a aplicação de replicação do Azure Migrate**: A aplicação de replicação cria e regista uma aplicação do Azure Active Directory na sua conta do Azure. Delegar permissões para isso.
- **Criar Cofre de chaves**: Para migrar máquinas, do Azure Migrate cria um cofre de chaves no grupo de recursos, para gerir as chaves de acesso à conta de armazenamento de replicação na sua subscrição. Para criar o cofre, tem as permissões de atribuição de função no grupo de recursos no qual reside o projeto do Azure Migrate. 


### <a name="assign-permissions-to-create-project"></a>Atribuir permissões para criar o projeto

1. No portal do Azure, abra a subscrição e selecione **controlo de acesso (IAM)** .
2. Na **verificar acesso**, localizar a conta relevante e clique nele para ver as permissões.
3. Deve ter **contribuinte** ou **proprietário** permissões.
    - Se acabou de criar uma conta gratuita do Azure, é o proprietário da sua subscrição.
    - Se não for o proprietário da subscrição, trabalhe com o proprietário para atribuir a função.

### <a name="assign-permissions-to-register-the-replication-appliance"></a>Atribuir permissões para registar a aplicação de replicação

Para a migração baseada em agente, delegar permissões para Azure migrar servidor de migração criar e registar uma aplicação do Azure AD na sua conta. Pode atribuir permissões ao utilizar um dos seguintes métodos:

- Um administrador global/inquilino pode conceder permissões aos utilizadores no inquilino, para criar e registar aplicações do Azure AD.
- Um administrador global/inquilino pode atribuir a função de desenvolvedor do aplicativo (que tem as permissões) para a conta.

É importante observar que:

- As aplicações não tem quaisquer outras permissões de acesso na subscrição que não estão descritas acima.
- Só tem estas permissões ao registar um novo dispositivo de replicação. Pode remover as permissões depois de configurar a aplicação de replicação. 


#### <a name="grant-account-permissions"></a>Conceder permissões de conta

O administrador de inquilino/global pode conceder permissões da seguinte forma

1. No Azure AD, o administrador global/inquilino deve acessar **do Azure Active Directory** > **utilizadores** > **as definições de utilizador**.
2. O administrador deve definir **registos de aplicações** ao **Sim**.

    ![Permissões do Azure AD](./media/tutorial-migrate-physical-virtual-machines/aad.png)

> [!NOTE]
> Esta é uma definição de predefinição que não seja confidencial. [Saiba mais](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).

#### <a name="assign-application-developer-role"></a>Atribuir a função de programador da aplicação 

O administrador global/inquilino pode atribuir a função de programador da aplicação a uma conta. [Saiba mais](https://docs.microsoft.comazure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).

## <a name="assign-permissions-to-create-key-vault"></a>Atribuir permissões para criar o Cofre de chaves

Atribua permissões de atribuição de função no grupo de recursos em que o projeto do Azure Migrate reside, da seguinte forma:

1. No grupo de recursos no portal do Azure, selecione **controlo de acesso (IAM)** .
2. Na **verificar acesso**, localizar a conta relevante e clique nele para ver as permissões. Precisa **proprietário** (ou **contribuinte** e **administrador de acesso de utilizador**) permissões.
3. Se não tiver as permissões necessárias, solicitá-los do proprietário do grupo de recursos. 

## <a name="prepare-for-migration"></a>Prepare para a migração

### <a name="check-machine-requirements-for-migration"></a>Verificar os requisitos da máquina para a migração

Certifique-se de máquinas cumprem os requisitos para migração para o Azure. 

> [!NOTE]
> Migração baseada em agente com a migração do servidor de migrar do Azure baseia-se nas funcionalidades do serviço Azure Site Recovery. Alguns requisitos podem ligar a documentação do Site Recovery.

1. [Verifique](migrate-support-matrix-vmware.md#agent-based-migration-vmware-server-requirements) os requisitos do servidor do VMware.
2. [Certifique-se de](migrate-support-matrix-vmware.md#agent-based-migration-vmware-vm-requirements) VM suporte os requisitos para migração.
3. Verifique se as definições da VM. As VMs no local que replica para o Azure tem de cumprir [requisitos de VM do Azure](migrate-support-matrix-vmware.md#azure-vm-requirements).


### <a name="prepare-a-machine-for-the-replication-appliance"></a>Preparar uma máquina para a aplicação de replicação

Migração de servidor migrar do Azure utiliza uma aplicação de replicação para replicar máquinas para o Azure. A aplicação de replicação executa os seguintes componentes.

- **Servidor de configuração**: O servidor de configuração coordena as comunicações entre o local e o Azure, e gere a replicação de dados.
- **Servidor de processos**: O servidor de processos atua como um gateway de replicação. Recebe dados de replicação. otimiza-os com colocação em cache, compressão e encriptação e envia-os para uma conta de armazenamento de cache no Azure. 

Antes de começar, terá de preparar uma máquina do Windows Server 2016 para alojar a aplicação de replicação. A máquina deve cumprir [estes requisitos](migrate-support-matrix-vmware.md#agent-based-migration-replication-appliance-requirements).


## <a name="add-the-azure-migrate-server-migration-tool"></a>Adicionar a ferramenta de migração do servidor de migrar do Azure

Configurar um projeto do Azure Migrate e, em seguida, adicione a ferramenta de migração do servidor de migrar do Azure ao mesmo.

1. No portal do Azure > **todos os serviços**, procure **Azure Migrate**.
2. Sob **serviços**, selecione **Azure Migrate**.
3. Na **descrição geral**, clique em **avaliar e migrar servidores**.
4. Sob **detetar, avaliar e migrar os servidores**, clique em **avaliar e migrar servidores**.

    ![Detetar e avaliar os servidores](./media/tutorial-migrate-physical-virtual-machines/assess-migrate.png)

5. Na **detetar, avaliar e migrar os servidores**, clique em **adicionar ferramentas**.
6. Na **migrar projeto**, selecione a sua subscrição do Azure e criar um grupo de recursos se não tiver uma.
7. Na **detalhes do projeto**, especifique o nome do projeto e na geografia, no qual pretende criar o projeto e clique em **seguinte**

    ![Criar um projeto do Azure Migrate](./media/tutorial-migrate-physical-virtual-machines/migrate-project.png)

    Pode criar um projeto do Azure Migrate em qualquer uma dessas áreas geográficas.

    **Geografia** | **Região**
    --- | ---
    Ásia | Sudeste Asiático
    Europa | Europa do Norte ou da Europa Ocidental
    Estados Unidos | E.U.A. Leste ou oeste dos E.U.A.

    A geografia especificada para o projeto só é utilizada para armazenar os metadados recolhidos das VMs no local. Pode selecionar qualquer região de destino para a migração real.
8. Na **ferramenta de avaliação Select**, selecione **ignorar a adição de uma ferramenta de avaliação por agora** > **seguinte**.
9. Na **ferramenta de migração Select**, selecione **Azure Migrate: Migração do servidor** > **próxima**.
10. Na **rever + adicionar ferramentas**, reveja as definições e clique em **adicionar ferramentas**
11. Depois de adicionar a ferramenta, é apresentado no projeto do Azure Migrate > **servidores** > **ferramentas de migração**.

## <a name="set-up-the-replication-appliance"></a>Configurar a aplicação de replicação

A primeira etapa de migração é configurar a aplicação de replicação. Transfira o ficheiro de instalador da aplicação e executá-lo no [máquina que preparou](#prepare-a-machine-for-the-replication-appliance). Depois de instalar a aplicação, registá-lo com a migração do servidor de migrar do Azure.


### <a name="download-the-replication-appliance-installer"></a>Transferir o instalador da aplicação de replicação

1. No projeto do Azure Migrate > **servidores**, na ***Azure Migrate: Migração do servidor**, clique em **detetar**.

    ![Detetar VMs](./media/tutorial-migrate-physical-virtual-machines/migrate-discover.png)

3. Na **detetar máquinas** > **são as suas máquinas virtualizadas?** , clique em **não virtualizado/outro**.
4. Na **região de destino**, selecione a região do Azure para o qual pretende migrar as máquinas.
5. Selecione **confirmar que a região de destino para a migração é o nome da região**.
6. Clique em **criar recursos**. Esta ação cria um cofre de recuperação de sites do Azure em segundo plano.
    - Se já tiver configurado a migração com o Azure Migrate migração do servidor, a opção de destino não pode ser configurada, uma vez que os recursos foram configurados anteriormente.
    - Não é possível alterar a região de destino para este projeto depois de clicar neste botão.
    - Todas as migrações subsequentes são nesta região.

7. Na **que pretende instalar um novo dispositivo de replicação?** , selecione **instalar uma aplicação de replicação**.
9. Na **transferir e instalar o software de aplicação de replicação**, transfira o instalador da aplicação e a chave de registo. Tem a chave para registar a aplicação. A chave é válida durante cinco dias depois de ser transferido.

    ![Transferir o fornecedor](media/tutorial-migrate-physical-virtual-machines/download-provider.png)

10. Copie o ficheiro de configuração da aplicação e o ficheiro de chave para a máquina de Windows Server 2016 que criou para a aplicação.
11. Execute o ficheiro de configuração de aplicação de replicação, conforme descrito no procedimento seguinte.
12. Depois da aplicação for reiniciado após a configuração, no **detetar máquinas**, selecione a aplicação para novos **selecionar servidor de configuração**e clique em **finalizar o registo**. Finalizar registo executa algumas tarefas finais para preparar a aplicação de replicação.

    ![Finalizar o registo](./media/tutorial-migrate-physical-virtual-machines/finalize-registration.png)

Pode demorar até 15 minutos após a finalizar o registo até que máquinas detetadas apareçam na migração do servidor de migrar do Azure. À medida que são descobertas VMs, o **detetados servidores** contagem aumenta.

![Servidores detetados](./media/tutorial-migrate-physical-virtual-machines/discovered-servers.png)


## <a name="install-the-mobility-service"></a>Instalar o serviço de Mobilidade

Nas máquinas que pretende migrar, terá de instalar o agente do serviço de mobilidade. Os programas de instalação do agente estão disponíveis na aplicação da replicação. Localizar o instalador correto e instalar o agente em cada máquina que pretende migrar. Fazer isso da seguinte forma:

1. Inicie sessão na aplicação da replicação.
2. Navegue para **%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository**.
3. Localize o instalador para o sistema operativo da máquina e a versão. Revisão [sistemas operativos suportados](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#replicated-machines). 
4. Copie o ficheiro de instalador para a máquina que pretende migrar.
5. Certifique-se de que tem a frase de acesso que foi gerado quando implementou a aplicação.
    - Store o arquivo num arquivo de texto temporária no computador.
    - Pode obter a frase de acesso na aplicação da replicação. A partir da linha de comandos, execute **C:\ProgramData\ASR\svsystems\bin\genpassphrase.exe - v** para ver a frase de acesso atual.
    - Não voltar a gerar a frase de acesso. Tal irá interromper a conectividade e terá de voltar a registar a aplicação de replicação.


### <a name="install-on-windows"></a>Instalar no Windows

1. Extraia o conteúdo do ficheiro de instalador para uma pasta local (por exemplo, C:\Temp) na máquina, da seguinte forma:

    ```
    ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
    MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
    cd C:\Temp\Extracted
    ```
2. Execute o instalador do serviço de mobilidade:
    ```
   UnifiedAgent.exe /Role "MS" /Silent
    ```
3. Registe o agente com a aplicação de replicação:
    ```
    cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
    UnifiedAgentConfigurator.exe  /CSEndPoint <replication appliance IP address> /PassphraseFilePath <Passphrase File Path>
    ```

### <a name="install-on-linux"></a>Instalar no Linux

1. Extraia o conteúdo do tarball instalador para uma pasta local (por exemplo /tmp/MobSvcInstaller) na máquina, da seguinte forma:
    ```
    mkdir /tmp/MobSvcInstaller
    tar -C /tmp/MobSvcInstaller -xvf <Installer tarball>
    cd /tmp/MobSvcInstaller
    ```
2. Execute o script de instalador:
    ```
    sudo ./install -r MS -q
    ```
3. Registe o agente com a aplicação de replicação:
    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <replication appliance IP address> -P <Passphrase File Path>
    ```

## <a name="replicate-machines"></a>Replicar máquinas

1. No projeto do Azure Migrate > **servidores**, **Azure Migrate: Migração do servidor**, clique em **replicar**.

    ![Replicar VMs](./media/tutorial-migrate-physical-virtual-machines/select-replicate.png)

2. Na **replicar**, > **definições da origem** > **são as suas máquinas virtualizadas?** , selecione **Sim, com o VMware vSphere**.
3. Na **aplicação no local**, selecione o nome da aplicação do Azure Migrate que configurou.
4. Na **servidor vCenter**, especifique o nome do servidor vCenter gerenciamento de VMs, ou o servidor vSphere nos quais as VMs estão alojadas.
5. Na **servidor de processos**, selecione o nome da aplicação da replicação.
6. Na **credenciais do convidado**, especificar uma conta de administrador VM que será utilizada para a instalação push do serviço de mobilidade. Neste tutorial, está a instalar o serviço de mobilidade manualmente, para que pode adicionar qualquer conta fictícia. Em seguida, clique em **seguinte: Máquinas virtuais**.

    ![Replicar VMs](./media/tutorial-migrate-physical-virtual-machines/source-settings.png)

7. Na **máquinas virtuais**, na **importar definições de migração de uma avaliação?** , deixe a predefinição **não, vou especificar as definições de migração manualmente**.
8. Verifique cada VM que pretende migrar. Em seguida, clique em **seguinte: Definições de destino**.

    ![Selecionar VMs](./media/tutorial-migrate-physical-virtual-machines/select-vms.png)


9. Na **definições de destino**, selecione a subscrição e região ao qual irá migrar e especifique o grupo de recursos no qual as VMs do Azure irá residir após a migração de destino.
10. Na **rede Virtual**, selecione a sub-rede/VNet do Azure para o qual as VMs do Azure vão ser associadas após a migração.
11. Na **benefício híbrido do Azure**:

    - Selecione **não** se não pretender aplicar o benefício híbrido do Azure. Clique depois em **Seguinte**.
    - Selecione **Sim** se tiver máquinas do Windows Server que são abrangidas por subscrições ativas do Software Assurance ou o Windows Server e que pretende aplicar o benefício para as máquinas que estiver a migrar. Clique depois em **Seguinte**.

    ![Definições de destino](./media/tutorial-migrate-physical-virtual-machines/target-settings.png)

12. Na **computação**, reveja o nome da VM, o tamanho, o tipo de disco do SO e o conjunto de disponibilidade. As VMs têm de cumprir [requisitos do Azure](migrate-support-matrix-vmware.md#azure-vm-requirements).

    - **Tamanho da VM**: Por predefinição, a migração do servidor de migrar do Azure escolhe um tamanho com base na correspondência mais próxima na subscrição do Azure. Em alternativa, escolha um tamanho manual em **tamanho de VM do Azure**. 
    - **Disco do SO**: Especifique o disco do SO (arranque) para a VM. O disco do SO é o disco que tenha o carregador de inicialização do sistema operativo e o instalador. 
    - **Conjunto de disponibilidade**: Se a VM num conjunto após a migração de disponibilidade do Azure, especifique o conjunto. O conjunto tem de estar no grupo de recursos de destino especificado para a migração.

    ![Definições de computação](./media/tutorial-migrate-physical-virtual-machines/compute-settings.png)

13. Na **discos**, especifique se os discos da VM devem ser replicados para o Azure e selecione o tipo de disco (SSD/HDD ou premium managed disks standard) no Azure. Clique depois em **Seguinte**.
    - Pode excluir discos da replicação.
    - Se excluir discos, não está presente na VM do Azure após a migração. 

    ![Definições do disco](./media/tutorial-migrate-physical-virtual-machines/disks.png)


14. Na **reveja e iniciar a replicação**, reveja as definições e clique em **replicar** para iniciar a replicação inicial para os servidores.

> [!NOTE]
> Pode atualizar as definições de replicação a qualquer momento antes de inicia a replicação, **Manage** > **replicar máquinas**. Não não possível alterar as definições depois da replicação é iniciada.



## <a name="track-and-monitor"></a>Controlar e monitorizar

- Quando clica em **replicar** começa uma tarefa de replicação começar. 
- Quando a tarefa de iniciar a replicação é concluída com êxito, as máquinas começam a replicação inicial para o Azure.
- Depois de concluída a replicação inicial, começa a replicação de diferenças. As alterações incrementais para discos locais periodicamente são replicadas para os discos de réplica no Azure.


Pode controlar o estado da tarefa nas notificações do portal.

Pode monitorizar o estado de replicação ao clicar em **replicar servidores** no **Azure Migrate: Migração do servidor**.
![Monitor de replicação](./media/tutorial-migrate-physical-virtual-machines/replicating-servers.png)

## <a name="run-a-test-migration"></a>Executar uma migração de teste


Quando a replicação de diferenças é iniciada, pode executar uma migração de teste para as VMs, antes de executar uma migração completa para o Azure. É altamente recomendável que fazer isso, pelo menos, uma vez para cada máquina, antes de migrá-lo.

- Executar uma migração de teste verifica se a migração irá funcionar conforme esperado, sem afetar as máquinas no local, que permanecerão operacional e continuar a replicar. 
- Migração de teste simula a migração através da criação de VM do Azure com os dados replicados (normalmente, migrar para uma VNet de não produção na sua subscrição do Azure).
- Pode utilizar o teste replicado VM do Azure para validar a migração, realizar o teste de aplicações e resolver quaisquer problemas antes da migração completa.

Fazer uma migração de teste da seguinte forma:


1. Na **objetivos de migração** > **servidores** > **Azure Migrate: Migração do servidor**, clique em **teste migrados servidores**.

     ![Testar servidores migrados](./media/tutorial-migrate-physical-virtual-machines/test-migrated-servers.png)

2. Com o botão direito a VM para testar e clique em **teste migrar**.

    ![Migração de teste](./media/tutorial-migrate-physical-virtual-machines/test-migrate.png)

3. Na **migração de teste**, selecione a VNet do Azure na qual a VM do Azure estarão localizada após a migração. Recomendamos que utilize uma VNet de não produção.
4. O **migração de teste** da tarefa é iniciada. Monitorize a tarefa nas notificações do portal.
5. Depois de concluída a migração, ver a VM migrada do Azure no **máquinas virtuais** no portal do Azure. O nome da máquina tem um sufixo **-teste**.
6. Depois do teste é concluído, clique com botão direito a VM do Azure no **replicar máquinas**e clique em **limpar a migração de teste**.

    ![Limpar a migração](./media/tutorial-migrate-physical-virtual-machines/clean-up.png)


## <a name="migrate-vms"></a>Migrar VMs

Depois de verificar se a migração de teste funciona conforme esperado, pode migrar as máquinas no local.

1. No projeto do Azure Migrate > **servidores** > **Azure Migrate: Migração do servidor**, clique em **replicar servidores**.

    ![Replicar servidores](./media/tutorial-migrate-physical-virtual-machines/replicate-servers.png)

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
    - Considere implementar o [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/overview) para monitorizar a utilização e as despesas do recurso.


## <a name="next-steps"></a>Passos seguintes

Investigar o [percurso de migração da cloud](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/migrate) na estrutura de adoção de Cloud do Azure.
