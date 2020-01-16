---
title: Migrar máquinas físicas locais ou máquinas virtualizadas para o Azure com migração de servidor de migrações para Azure | Microsoft Docs
description: Este artigo descreve como migrar máquinas físicas locais ou máquinas virtualizadas para o Azure com a migração de servidor de migrações para Azure.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 4a6e33770f93c365d5ccd034803c7c7f247d528a
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028800"
---
# <a name="migrate-physical-or-virtualized-servers-to-azure"></a>Migrar servidores físicos ou virtualizados para o Azure 

Este artigo mostra como migrar servidores físicos ou virtualizados para o Azure. A ferramenta de migração de servidor de migrações para Azure oferece migração de servidores físicos e virtualizados, usando a replicação baseada em agente. Usando essa ferramenta, você pode migrar uma ampla variedade de máquinas para o Azure:

- Migrar servidores físicos locais.
- Migre VMs virtualizadas por plataformas como Xen, KVM.
- Migre VMs Hyper-V ou VMware. Isso é útil se, por alguma razão, você não conseguir usar o fluxo de migração padrão que migrar para o servidor de migração do Azure oferece para [Hyper-V](tutorial-migrate-hyper-v.md), migração [sem agente do VMware](tutorial-migrate-vmware.md) ou migração [baseada em agente do VMware](tutorial-migrate-vmware-agent.md) .
- Migre VMs em execução em nuvens privadas.
- Migre VMs em execução em nuvens públicas, como Amazon Web Services (AWS) ou Google Cloud Platform (GCP).


As [migrações para Azure](migrate-services-overview.md) fornecem um hub central para acompanhar a descoberta, avaliação e migração de seus aplicativos e cargas de trabalho locais e instâncias de VM de nuvem para o Azure. O Hub fornece ferramentas de migração do Azure para avaliação e migração, bem como ofertas de ISVs (fornecedores independentes de software) de terceiros.


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

1. [Examine](migrate-architecture.md) a arquitetura de migração.
2. Verifique se sua conta do Azure é atribuída à função colaborador da máquina virtual, para que você tenha permissões para:

    - Criar uma VM no grupo de recursos selecionado.
    - Criar uma VM na rede virtual selecionada.
    - Gravar em um disco gerenciado do Azure. 

3. [Configure uma rede do Azure](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Quando você replica para o Azure, as VMs do Azure são criadas e Unidas a uma rede do Azure que você especifica ao configurar a migração.


## <a name="prepare-azure"></a>Preparar o Azure

Configure as permissões do Azure antes de migrar com a migração de servidor de migrações para Azure.

- **Criar um projeto**: sua conta do Azure precisa de permissões para criar um projeto de migrações para Azure. 
- **Registrar o dispositivo de replicação de migrações para Azure**: o dispositivo de replicação cria e registra um aplicativo Azure Active Directory em sua conta do Azure. Delegar permissões para isso.
- **Criar Key Vault**: para migrar computadores, as migrações para Azure criam uma Key Vault no grupo de recursos, para gerenciar chaves de acesso para a conta de armazenamento de replicação em sua assinatura. Para criar o cofre, você precisa de permissões de atribuição de função no grupo de recursos no qual o projeto de migração do Azure reside. 


### <a name="assign-permissions-to-create-project"></a>Atribuir permissões para criar projeto

1. No portal do Azure, abra a assinatura e selecione **controle de acesso (iam)** .
2. Em **verificar acesso**, localize a conta relevante e clique nela para exibir permissões.
3. Você deve ter permissões de **colaborador** ou de **proprietário** .
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

1. No Azure AD, o locatário/administrador global deve navegar até **Azure Active Directory** > **usuários** > **configurações de usuário**.
2. O administrador deve definir **registros de aplicativo** como **Sim**.

    ![Permissões do Azure AD](./media/tutorial-migrate-physical-virtual-machines/aad.png)

> [!NOTE]
> Essa é uma configuração padrão que não é sensível. [Saiba mais](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).

#### <a name="assign-application-developer-role"></a>Atribuir função de desenvolvedor de aplicativo 

O locatário/administrador global pode atribuir a função de desenvolvedor de aplicativo a uma conta. [Saiba mais](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="assign-permissions-to-create-key-vault"></a>Atribuir permissões para criar Key Vault

Atribua permissões de atribuição de função no grupo de recursos no qual o projeto de migrações para Azure reside, da seguinte maneira:

1. No grupo de recursos no portal do Azure, selecione **controle de acesso (iam)** .
2. Em **verificar acesso**, localize a conta relevante e clique nela para exibir permissões. Você precisa de permissões de **proprietário** (ou **administrador de acesso de usuário**e **colaborador** ).
3. Se você não tiver as permissões necessárias, solicite-as do proprietário do grupo de recursos. 

## <a name="prepare-for-migration"></a>Prepare para a migração

### <a name="check-machine-requirements-for-migration"></a>Verificar os requisitos do computador para a migração

Verifique se os computadores estão em conformidade com os requisitos de migração para o Azure. 

> [!NOTE]
> A migração baseada em agente com a migração de servidor de migrações para Azure baseia-se nos recursos do serviço de Azure Site Recovery. Alguns requisitos podem ser vinculados à documentação Site Recovery.

1. [Verifique](migrate-support-matrix-physical-migration.md#physical-server-requirements) os requisitos do servidor físico.
2. Verifique as configurações da VM. Os computadores locais que você replica para o Azure devem estar em conformidade com [os requisitos de VM do Azure](migrate-support-matrix-physical-migration.md#azure-vm-requirements).


### <a name="prepare-a-machine-for-the-replication-appliance"></a>Preparar um computador para o dispositivo de replicação

A migração de servidor de migrações para Azure usa um dispositivo de replicação para replicar computadores no Azure. O dispositivo de replicação executa os seguintes componentes.

- **Servidor de configuração**: o servidor de configuração coordena as comunicações entre o local e o Azure e gerencia a replicação de dados.
- **Servidor de processo**: o servidor de processo atua como um gateway de replicação. Ele recebe dados de replicação; otimiza-o com caching, compactação e criptografia e o envia para uma conta de armazenamento de cache no Azure. 

Antes de começar, você precisa preparar um computador com Windows Server 2016 para hospedar o dispositivo de replicação. O computador deve estar em conformidade com [esses requisitos](migrate-replication-appliance.md). O dispositivo não deve ser instalado em um computador de origem que você deseja proteger.


## <a name="add-the-azure-migrate-server-migration-tool"></a>Adicionar a ferramenta de migração de servidor de migrações para Azure

Configure um projeto de migrações para Azure e adicione a ferramenta de migração de servidor do Azure Migrate para ele.

1. No portal do Azure > **Todos os serviços**, procure **Azure Migrate**.
2. Em **Serviços**, selecione **Azure Migrate**.
3. Em **Descrição geral**, clique em **Avaliar e migrar servidores**.
4. Em **descobrir, avaliar e migrar servidores**, clique em **avaliar e migrar servidores**.

    ![Descobrir e avaliar servidores](./media/tutorial-migrate-physical-virtual-machines/assess-migrate.png)

5. Em **Detetar, avaliar e migrar servidores**, clique em **Adicionar ferramentas**.
6. Em **Migrar projeto**, selecione a sua subscrição do Azure e crie um grupo de recursos, caso não tenha um.
7. Em **detalhes do projeto**, especifique o nome do projeto e a geografia na qual você deseja criar o projeto e clique em **Avançar**

    ![Criar um projeto de migrações para Azure](./media/tutorial-migrate-physical-virtual-machines/migrate-project.png)

    Você pode criar um projeto de migrações para Azure em qualquer uma dessas regiões.

    **Geografia** | **Região**
    --- | ---
    Ásia | Sudeste Asiático
    Europa | Europa Setentrional ou Europa Ocidental
    Estados Unidos | Leste dos EUA ou Oeste EUA Central

    A geografia especificada para o projeto só é utilizada para armazenar os metadados recolhidos das VMs no local. Você pode selecionar qualquer região de destino para a migração real.
8. Em **selecionar ferramenta de avaliação**, selecione **ignorar a adição de uma ferramenta de avaliação por enquanto** > **em Avançar**.
9. Em **selecionar ferramenta de migração**, selecione **migrações para Azure: migração de servidor** > **Avançar**.
10. Em **Analisar + adicionar ferramentas**, analise as definições e clique em **Adicionar ferramentas**
11. Depois de adicionar a ferramenta, ela aparece no projeto de migração do Azure > **servidores** > **ferramentas de migração**.

## <a name="set-up-the-replication-appliance"></a>Configurar o dispositivo de replicação

A primeira etapa da migração é configurar o dispositivo de replicação. Você baixa o arquivo do instalador para o dispositivo e o executa no [computador que você preparou](#prepare-a-machine-for-the-replication-appliance). Depois de instalar o dispositivo, registre-o com a migração de servidor de migrações para Azure.


### <a name="download-the-replication-appliance-installer"></a>Baixar o instalador do dispositivo de replicação

1. No projeto de migrações para Azure > **servidores**, em ***migrações para Azure: migração de servidor**, clique em **descobrir**.

    ![Detetar VMs](./media/tutorial-migrate-physical-virtual-machines/migrate-discover.png)

3. No **Discover machines** > **seus computadores são virtualizados?** , clique em **não virtualizado/outro**.
4. Em **região de destino**, selecione a região do Azure para a qual você deseja migrar os computadores.
5. Selecione **confirmar que a região de destino para migração é Region-Name**.
6. Clique em **criar recursos**. Isso cria um cofre de Azure Site Recovery em segundo plano.
    - Se você já configurou a migração com a migração de servidor de migrações para Azure, a opção de destino não pode ser configurada, pois os recursos foram configurados anteriormente.
    - Você não pode alterar a região de destino deste projeto depois de clicar nesse botão.
    - Todas as migrações subsequentes são para essa região.

7. Em deseja **instalar um novo dispositivo de replicação?** , selecione **instalar um dispositivo de replicação**.
9. Em **baixar e instalar o software do dispositivo de replicação**, baixe o instalador do dispositivo e a chave de registro. Você precisa da chave para registrar o dispositivo. A chave é válida por cinco dias após ser baixada.

    ![Baixar provedor](media/tutorial-migrate-physical-virtual-machines/download-provider.png)

10. Copie o arquivo de instalação do dispositivo e o arquivo de chave para o computador 2016 do Windows Server que você criou para o dispositivo.
11. Execute o arquivo de instalação do dispositivo de replicação, conforme descrito no próximo procedimento.
12. Depois que o dispositivo for reiniciado após a instalação, em **descobrir computadores**, selecione o novo dispositivo em **selecionar servidor de configuração**e clique em **finalizar o registro**. Finalizar o registro executa algumas tarefas finais para preparar o dispositivo de replicação.

    ![Finalizar registro](./media/tutorial-migrate-physical-virtual-machines/finalize-registration.png)

Pode levar até 15 minutos após a finalização do registro até que os computadores descobertos apareçam na migração do servidor de migrações para Azure. À medida que as VMs são descobertas, a contagem de **servidores descobertos** aumenta.

![Servidores descobertos](./media/tutorial-migrate-physical-virtual-machines/discovered-servers.png)


## <a name="install-the-mobility-service"></a>Instalar o serviço de Mobilidade

Em computadores que você deseja migrar, você precisa instalar o agente do serviço de mobilidade. Os instaladores de agente estão disponíveis no dispositivo de replicação. Você encontra o instalador correto e instala o agente em cada computador que deseja migrar. Proceda da seguinte forma:

1. Entre no dispositivo de replicação.
2. Navegue até **%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository**.
3. Localize o instalador para a versão e o sistema operacional do computador. Examine os [sistemas operacionais com suporte](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#replicated-machines). 
4. Copie o arquivo do instalador para o computador que você deseja migrar.
5. Certifique-se de que você tenha a frase secreta que foi gerada quando você implantou o dispositivo.
    - Armazene o arquivo em um arquivo de texto temporário no computador.
    - Você pode obter a frase secreta no dispositivo de replicação. Na linha de comando, execute **C:\ProgramData\ASR\home\svsystems\bin\genpassphrase.exe-v** para exibir a frase secreta atual.
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

1. No projeto de migração do Azure > **servidores**, **migrações para Azure: migração de servidor**, clique em **replicar**.

    ![Replicar VMs](./media/tutorial-migrate-physical-virtual-machines/select-replicate.png)

2. No **replicate**, > **configurações de origem** > **seus computadores são virtualizados?** , selecione **não virtualizados/outros**.
3. Em **dispositivo local**, selecione o nome do dispositivo de migrações para Azure que você configurou.
4. No **vCenter Server**, especifique o nome do servidor vCenter que gerencia as VMs ou o servidor vSphere no qual as VMs estão hospedadas.
5. Em **servidor de processo**, selecione o nome do dispositivo de replicação.
6. Em **credenciais de convidado**, você especifica uma conta de administrador de VM que será usada para a instalação por push do serviço de mobilidade. Neste tutorial, estamos instalando o serviço de mobilidade manualmente, para que você possa adicionar qualquer conta fictícia. Em seguida, clique em **Avançar: máquinas virtuais**.

    ![Replicar VMs](./media/tutorial-migrate-physical-virtual-machines/source-settings.png)

7. Em **máquinas virtuais**, em **Importar configurações de migração de uma avaliação?** , deixe a configuração padrão **não, vou especificar as configurações de migração manualmente**.
8. Verifique cada VM que você deseja migrar. Em seguida, clique em **Avançar: configurações de destino**.

    ![Selecionar VMs](./media/tutorial-migrate-physical-virtual-machines/select-vms.png)


9. Em **Definições de destino**, selecione a subscrição, assim como a região de destino para a qual vai migrar, e especifique o grupo de recursos no qual as VMs do Azure vão residir após a migração.
10. Em **Rede Virtual**, selecione a VNet/sub-rede do Azure na qual as VMs do Azure vão ser associadas após a migração.
11. Em **Benefício Híbrido do Azure**:

    - Selecione **Não** se não quiser aplicar o Benefício Híbrido do Azure. Clique depois em **Seguinte**.
    - Selecione **Sim** se tiver computadores Windows Server abrangidos com subscrições ativas do Software Assurance ou do Windows Server e quiser aplicar o benefício aos computadores que está a migrar. Clique depois em **Seguinte**.

    ![Configurações de destino](./media/tutorial-migrate-physical-virtual-machines/target-settings.png)

12. Em **Computação**, analise o nome, o tamanho, o tipo de disco do SO e o conjunto de disponibilidade das VMs. As VMs devem estar em conformidade com os [requisitos do Azure](migrate-support-matrix-physical-migration.md#azure-vm-requirements).

    - **Tamanho da VM**: por padrão, a migração do servidor de migrações para Azure escolhe um tamanho com base na correspondência mais próxima na assinatura do Azure. Como alternativa, escolha um tamanho manual em **Tamanho de VMs do Azure**. 
    - **Disco do so**: especifique o disco do sistema operacional (inicialização) para a VM. O disco do SO é o disco que possui o carregador e o instalador do sistema operativo. 
    - **Conjunto de disponibilidade**: se a VM deve estar em um conjunto de disponibilidade do Azure após a migração, especifique o conjunto. O conjunto deve estar no grupo de recursos de destino especificado para a migração.

    ![Configurações de computação](./media/tutorial-migrate-physical-virtual-machines/compute-settings.png)

13. Em **discos**, especifique se os discos de VM devem ser replicados no Azure e selecione o tipo de disco (SSD padrão/HDD ou discos gerenciados Premium) no Azure. Clique depois em **Seguinte**.
    - Pode excluir discos da replicação.
    - Se excluir discos, estes não estarão presentes na VM do Azure após a migração. 

    ![Definições do disco](./media/tutorial-migrate-physical-virtual-machines/disks.png)


14. Em **Analisar e iniciar a replicação**, analise as definições e clique em **Replicar** para iniciar a replicação inicial para os servidores.

> [!NOTE]
> Você pode atualizar as configurações de replicação a qualquer momento antes de iniciar a replicação, **gerenciar** > **máquinas**de replicação. As definições não podem ser alteradas após o início da replicação.



## <a name="track-and-monitor"></a>Acompanhar e monitorar

- Quando você clica em **replicar** , um trabalho iniciar replicação é iniciado. 
- Quando o trabalho iniciar replicação é concluído com êxito, os computadores começam sua replicação inicial para o Azure.
- Após a conclusão da replicação inicial, a replicação delta começa. As alterações incrementais em discos locais são replicadas periodicamente para os discos de réplica no Azure.


Você pode acompanhar o status do trabalho nas notificações do Portal.

Você pode monitorar o status de replicação clicando em **replicar servidores** no **Azure migrar: migração de servidor**.
![Monitorar a replicação](./media/tutorial-migrate-physical-virtual-machines/replicating-servers.png)

## <a name="run-a-test-migration"></a>Executar uma migração de teste


Quando a replicação delta é iniciada, você pode executar uma migração de teste para as VMs antes de executar uma migração completa para o Azure. É altamente recomendável que você faça isso pelo menos uma vez para cada computador, antes de migrá-lo.

- A execução de uma migração de teste verifica se a migração funcionará conforme o esperado, sem afetar os computadores locais, que permanecem operacionais e continuam replicando. 
- A migração de teste simula a migração criando uma VM do Azure usando dados replicados (geralmente migrando para uma VNet de não produção em sua assinatura do Azure).
- Você pode usar a VM do Azure de teste replicado para validar a migração, executar testes de aplicativo e resolver problemas antes da migração completa.

Faça uma migração de teste da seguinte maneira:


1. Em **metas de migração** > **servidores** > **migrações para Azure: migração de servidor**, clique em **testar servidores migrados**.

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

1. No projeto de migração do Azure > **servidores** > **migrações para Azure: migração de servidor**, clique em **replicar servidores**.

    ![Replicar servidores](./media/tutorial-migrate-physical-virtual-machines/replicate-servers.png)

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
    - Considere implementar o [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/overview) para monitorizar a utilização e as despesas do recurso.


## <a name="next-steps"></a>Passos seguintes

Investigue a [jornada de migração na nuvem](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/migrate) na estrutura de adoção de nuvem do Azure.
