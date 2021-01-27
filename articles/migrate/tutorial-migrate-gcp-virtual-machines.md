---
title: Descubra, avalie e emigre as instâncias VM da Plataforma Google Cloud (GCP) para Azure
description: Este artigo descreve como migrar VMs GCP para Azure com Azure Migrate.
author: deseelam
ms.author: deseelam
ms.manager: bsiva
ms.topic: tutorial
ms.date: 08/19/2020
ms.custom: MVC
ms.openlocfilehash: 12b8e30b0107b6b008cbd6467ada7c2d44f5e6d6
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98871642"
---
# <a name="discover-assess-and-migrate-google-cloud-platform-gcp-vms-to-azure"></a>Descubra, avalie e emigre os VMs da Google Cloud Platform (GCP) para Azure

Este tutorial mostra-lhe como descobrir, avaliar e migrar máquinas virtuais (GCP) (Plataforma cloud) do Google cloud para VMs Azure, utilizando Azure Migrate: Server Assessment e Azure Migrate: Server Migration tools.

Neste tutorial, vai aprender a:
> [!div class="checklist"]
>
> * Verifique os pré-requisitos para a migração.
> * Prepare os recursos do Azure com a Azure Migrate: Migração do servidor. Configure as permissões para a sua conta Azure e recursos para trabalhar com a Azure Migrate.
> * Preparar exemplos de VM GCP para migração.
> * Adicione o Azure Migrate: Ferramenta de migração de servidores no hub Azure Migrate.
> * Configurar o aparelho de replicação e implementar o servidor de configuração.
> * Instale o serviço de Mobilidade em VMs GCP que pretende migrar.
> * Ativar a replicação nas VMs.
> * Rastreia e monitorize o estado de replicação. 
> * Faça um teste de migração para garantir que tudo está funcionando como esperado.
> * Fazer uma migração completa para Azure.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.

## <a name="discover-and-assess"></a>Descubra e avalie

Antes de migrar para Azure, recomendamos que realize uma avaliação de descoberta e migração em VM. Esta avaliação ajuda a tamanho certo dos seus VMs GCP para migração para Azure, e estima potenciais custos de execução do Azure.

Configurar uma avaliação da seguinte forma:

1. Siga o [tutorial](./tutorial-discover-gcp.md) para configurar o Azure e prepare os seus VMS GCP para uma avaliação. Tenha em atenção que:

    - A Azure Migrate utiliza a autenticação de palavra-passe ao descobrir instâncias GCP VM. As instâncias GCP não suportam a autenticação de palavra-passe por padrão. Antes de poder descobrir, tem de ativar a autenticação de senhas.
        - Para máquinas Windows, permita a porta WinRM 5985 (HTTP). Isto permite chamadas remotas de WMI.
        - Para máquinas Linux:
            1. Inscreva-se em cada máquina Linux.
            2. Abra o ficheiro sshd_config : vi /etc/ssh/sshd_config
            3. No ficheiro, localizar a linha **passwordAustração** e alterar o valor para **sim**.
            4. Guarde o ficheiro e feche-o. Reinicie o serviço de ssh.
    - Se estiver a utilizar um utilizador de raiz para descobrir os seus VMs Linux, certifique-se de que o login de raiz é permitido nos VMs.
        1. Inscreva-se em cada máquina Linux
        2. Abra o ficheiro sshd_config : vi /etc/ssh/sshd_config
        3. No ficheiro, localizar a linha **PermitRootLogin** e alterar o valor para **sim**.
        4. Guarde o ficheiro e feche-o. Reinicie o serviço de ssh.

2. Em seguida, siga este [tutorial](./tutorial-assess-gcp.md) para configurar um projeto e aparelho Azure Migrate para descobrir e avaliar os seus VMs GCP.

Embora recomendemos que experimente uma avaliação, realizar uma avaliação não é um passo obrigatório para poder migrar VMs.



## <a name="prerequisites"></a>Pré-requisitos 

- Certifique-se de que os VMS GCP que pretende migrar estão a executar uma versão de SISTEMA suportada. Os VM GCP são tratados como máquinas físicas para efeitos da migração. Reveja os [sistemas operativos suportados e as versões kernel](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines) para o fluxo de trabalho de migração de servidores físicos. Pode utilizar comandos padrão como *hostnamectl* ou *uname -a* para verificar as versões DE e Kernel para os seus VMs Linux.  Recomendamos que efetue uma migração de teste para validar se o VM funcionar como esperado antes de prosseguir com a migração real.
- Certifique-se de que os seus VM GCP cumprem as [configurações suportadas](./migrate-support-matrix-physical-migration.md#physical-server-requirements) para a migração para Azure.
- Verifique se os VMS GCP que replica ao Azure cumprem os [requisitos da Azure VM.](./migrate-support-matrix-physical-migration.md#azure-vm-requirements)
- São necessárias algumas alterações nos VM antes de as migrar para Azure.
    - Para alguns sistemas operativos, a Azure Migrate faz estas alterações automaticamente.
    - É importante fazer estas mudanças antes de começar a migração. Se migrar o VM antes de fazer a alteração, o VM pode não arrancar em Azure.
Reveja as alterações do [Windows](prepare-for-migration.md#windows-machines) e [do Linux](prepare-for-migration.md#linux-machines) que precisa de fazer.

### <a name="prepare-azure-resources-for-migration"></a>Preparar recursos do Azure para a migração

Prepare o Azure para a migração com a Azure Migrate: ferramenta de migração do servidor.

**Tarefa** | **Detalhes**
--- | ---
**Criar um projeto do Azure Migrate** | A sua conta Azure necessita de permissões de Colaborador ou Proprietário para [criar um novo projeto.](./create-manage-projects.md)
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

[Crie](../virtual-network/manage-virtual-network.md#create-a-virtual-network) uma rede virtual Azure (VNet). Quando se replica para Azure, os VMs Azure que são criados juntam-se ao Azure VNet que especifica quando configura a migração.

## <a name="prepare-gcp-instances-for-migration"></a>Preparar instâncias GCP para a migração

Para se preparar para a migração do GCP para o Azure, é necessário preparar e implantar um aparelho de replicação para a migração.

### <a name="prepare-a-machine-for-the-replication-appliance"></a>Preparar uma máquina para o aparelho de replicação

Azure Migrate: A migração do servidor utiliza um aparelho de replicação para replicar máquinas para o Azure. O aparelho de replicação executa os seguintes componentes.

- **Servidor de configuração**: O servidor de configuração coordena as comunicações entre os VMs GCP e o Azure e gere a replicação de dados.
- **Servidor de** processo : O servidor de processo funciona como um gateway de replicação. Recebe dados de replicação, otimiza-os com caching, compressão e encriptação, e envia-os para uma conta de armazenamento de cache em Azure.

Prepare-se para a colocação do aparelho da seguinte forma:

- Crie um VM GCP separado para hospedar o aparelho de replicação. Este caso deve estar a executar o Windows Server 2012 R2 ou o Windows Server 2016. [Reveja](./migrate-replication-appliance.md#appliance-requirements) os requisitos de hardware, software e networking para o aparelho.
- O aparelho não deve ser instalado num VM de origem que pretende replicar ou no aparelho de deteção e avaliação Azure Migrate que possa ter instalado anteriormente. Deve ser implantado num VM diferente.
- A origem dos VMS GCP a migrar deve ter uma linha de visão de rede para o aparelho de replicação. Configure as regras necessárias de Firewall para permitir isto. Recomenda-se que o aparelho de replicação seja implantado na mesma rede VPC que os VMs de origem a migrar. Se o aparelho de replicação precisar de estar num VPC diferente, os VPCs têm de ser ligados através do observação do VPC.
- Os VM da origem comunicam com o aparelho de replicação nas portas HTTPS 443 (orquestração do canal de controlo) e TCP 9443 (transporte de dados) para a gestão da replicação e transferência de dados de replicação. O aparelho de replicação, por sua vez, orquestra e envia dados de replicação para a Azure sobre a porta HTTPS 443. Para configurar estas regras, edite as regras de entrada/saída do grupo de segurança com as portas apropriadas e informações IP de origem.

   ![Regras de firewall do GCP](./media/tutorial-migrate-gcp-virtual-machines/gcp-firewall-rules.png)
     
 
   ![Editar regras de firewall](./media/tutorial-migrate-gcp-virtual-machines/gcp-edit-firewall-rule.png)

- O aparelho de replicação utiliza o MySQL. Reveja as [opções](migrate-replication-appliance.md#mysql-installation) de instalação do MySQL no aparelho.
- Reveja os URLs Azure necessários para que o aparelho de replicação aceda a nuvens [públicas](migrate-replication-appliance.md#url-access) e [governamentais.](migrate-replication-appliance.md#azure-government-url-access)

## <a name="set-up-the-replication-appliance"></a>Configurar o aparelho de replicação

O primeiro passo da migração é configurar o aparelho de replicação. Para configurar o aparelho para a migração de VMs GCP, tem de descarregar o ficheiro do instalador para o aparelho e, em seguida, executá-lo no [VM que preparou](#prepare-a-machine-for-the-replication-appliance).

### <a name="download-the-replication-appliance-installer"></a>Descarregue o instalador de aparelhos de replicação

1. No projeto Azure Migrate > **Servers,** em **Azure Migrate: Server Migration**, clique em **Discover**.

    ![Detetar VMs](./media/tutorial-migrate-physical-virtual-machines/migrate-discover.png)

2. In **Discover machines**  >  **Are your machines virtualized?** 
3. Na **região Alvo,** selecione a região de Azure para a qual pretende migrar as máquinas.
4. **Selecione Confirme que a região-alvo para a migração é <>de nome da região**.
5. Clique **em Criar recursos.** Isto cria um cofre de recuperação do local de Azure em segundo plano.
    - Se já estabeleceu migração com a Migração do Servidor Azure Migrate, a opção-alvo não pode ser configurada, uma vez que os recursos foram criados anteriormente.
    - Não é possível alterar a região alvo deste projeto depois de clicar neste botão.
    - Para migrar os seus VMs para uma região diferente, terá de criar um novo/diferente projeto Azure Migrate.

6. Na  **instalação de um novo aparelho de replicação?**
7. Em **Transferir e instalar o software do aparelho de replicação,** descarregue o instalador do aparelho e a chave de registo. É necessário fazer a chave para registar o aparelho. A chave é válida por cinco dias após o download.

    ![Fornecedor de descarregamento](media/tutorial-migrate-physical-virtual-machines/download-provider.png)

8. Copie o ficheiro de configuração do aparelho e o ficheiro chave para o Windows Server 2016 ou Windows Server 2012 GCP VM que criou para o aparelho de replicação.
9. Executar o ficheiro de instalação do aparelho de replicação, conforme descrito no procedimento seguinte.  
    9.1. Sob **Antes de Começar**, selecione **Instalar o servidor de configuração e o servidor de processos** e depois selecione **Seguinte**.   
    9.2 Na **Licença de Software de Terceiros,** selecione aceito o contrato de licença de **terceiros** e, em seguida, selecione **Next**.   
    9.3 No **Registo**, selecione **Procurar,** e depois vá para onde coloca o ficheiro da chave de registo do cofre. Selecione **Seguinte**.  
    9.4 Nas **definições da Internet**, selecione **Connect to Azure Site Recovery sem um servidor proxy** e, em seguida, selecione **Next**.  
    9.5 A página **pré-requisitos Verificar** verifica vários itens. Quando terminar, selecione **Seguinte**.  
    9.6 Na **Configuração MySQL,** forneça uma palavra-passe para o DB MySQL e, em seguida, selecione **Next**.  
    9.7 Em **Detalhes ambientais**, selecione **Nº**. Não precisas de proteger os teus VMs. Em seguida, selecione **Seguinte**.  
    9.8 No **Local de Instalação,** selecione **Seguinte** para aceitar o predefinido.  
    9.9 Na **Seleção de Rede,** selecione **Seguinte** para aceitar o predefinido.  
    9.10 Em **resumo,** selecione **Instalar**.   
    9.11 **O progresso da instalação** mostra-lhe informações sobre o processo de instalação. Quando terminar, selecione **Concluir**. Uma janela apresenta uma mensagem sobre uma reinicialização. Selecione **OK**.   
    9.12 Em seguida, uma janela apresenta uma mensagem sobre a palavra-passe de ligação do servidor de configuração. Copie a frase-passe para a sua prancheta e guarde a palavra-passe num ficheiro de texto temporário nos VMs de origem. Você precisará desta palavra-passe mais tarde, durante o processo de instalação do serviço de mobilidade.
10. Após a conclusão da instalação, o assistente de configuração do Aparelho será lançado automaticamente (Também pode lançar o assistente manualmente utilizando o atalho cspsconfigtool que é criado no ambiente de trabalho do aparelho). Utilize o separador 'Contas's Manage do assistente para adicionar detalhes da conta a utilizar para impulsionar a instalação do serviço Mobility. Neste tutorial, vamos instalar manualmente o Serviço de Mobilidade em VMs de origem a serem replicados, por isso crie uma conta falsa neste passo e prossiga. Pode fornecer os seguintes detalhes para criar a conta falsa - "guest" como o nome amigável, "username" como nome de utilizador, e "password" como palavra-passe para a conta. Utilizará esta conta falsa na fase de Replicação ativa. 
11. Depois de o aparelho ter sido reiniciado após a configuração, nas **máquinas Discover**, selecione o novo aparelho no **Servidor de Configuração Select** e clique em Finalizar o **registo**. Finalizar o registo executa algumas tarefas finais para preparar o aparelho de replicação.

    ![Finalizar o registo](./media/tutorial-migrate-physical-virtual-machines/finalize-registration.png)

## <a name="install-the-mobility-service"></a>Instalar o serviço de Mobilidade

Um agente de serviço de mobilidade deve ser instalado na fonte GCP VMs para ser migrado. Os instaladores do agente estão disponíveis no aparelho de replicação. Encontre o instalador certo e instale o agente em cada máquina que pretende migrar. Faça o seguinte:

1. Inscreva-se no aparelho de replicação.
2. Navegue para **%ProgramData%\ASR\home\home\svsystems\pushinstallsvc\repositório**.
3. Encontre o instalador para o sistema operativo gCP VMs de origem e versão. Rever [sistemas operativos suportados.](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines)
4. Copie o ficheiro do instalador para a fonte GCP VM que pretende migrar.
5. Certifique-se de que tem o ficheiro de texto de palavras-passe guardado que foi criado quando instalou o aparelho de replicação.
    - Se se esqueceu de guardar a palavra-passe, pode ver a frase-passe no aparelho de replicação com este passo. A partir da linha de comando, corra **C:\ProgramData\ASR\home\svsystems\bin\genpassphrase.exe -v** para ver a frase de passagem atual.
    - Agora, copie esta palavra-passe para a sua área de transferência e guarde-a num ficheiro de texto temporário nos VMs de origem.

### <a name="installation-guide-for-windows-gcp-vms"></a>Guia de instalação para VMs GCP do Windows

1. Extrair o conteúdo do ficheiro do instalador numa pasta local (por exemplo C:\Temp) no VM GCP, da seguinte forma:

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

### <a name="installation-guide-for-linux-gcp-vms"></a>Guia de instalação para VMs Linux GCP

1. Extrair o conteúdo da bola de tarball do instalador para uma pasta local (por exemplo/tmp/MobSvcInstaller) no GCP VM, da seguinte forma:
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

## <a name="enable-replication-for-gcp-vms"></a>Permitir a replicação de VMs GCP

> [!NOTE]
> Através do portal, pode adicionar até 10 VMs para replicação de uma só vez. Para replicar mais VMs simultaneamente, pode adicioná-los em lotes de 10.

1. No projeto Azure Migrate > **Servers**, **Azure Migrate: Server Migration**, clique em **Replicar**.

    ![Replicar VMs](./media/tutorial-migrate-physical-virtual-machines/select-replicate.png)

2. Em **Replicar,**> **As definições de**  >  **Origem são virtualizadas?** 
3. No **aparelho no local,** selecione o nome do aparelho Azure Migrate que instalou.
4. No **Process Server,** selecione o nome do aparelho de replicação. 
5. Nas **credenciais do Guest**, selecione a conta falsa criada anteriormente durante a instalação do [instalador de replicação](#download-the-replication-appliance-installer) para instalar manualmente o serviço Mobility (a instalação push não está suportada). Em seguida, clique **em Seguinte: Máquinas virtuais**.   
 
    ![Definições de replicação](./media/tutorial-migrate-physical-virtual-machines/source-settings.png)
6. Em **Máquinas Virtuais,** nas  **definições de migração de importação a partir de uma avaliação?**
7. Verifique cada VM que deseja migrar. Em seguida, clique em **Seguinte: Definições de destino**.

    ![Selecione VMs](./media/tutorial-migrate-physical-virtual-machines/select-vms.png)

8. Em **Definições de destino**, selecione a subscrição, assim como a região de destino para a qual vai migrar, e especifique o grupo de recursos no qual as VMs do Azure vão residir após a migração.
9. Em **Rede Virtual**, selecione a VNet/sub-rede do Azure na qual as VMs do Azure vão ser associadas após a migração.
10. Nas **opções de Disponibilidade,** selecione:
    -  Zona de Disponibilidade para fixar a máquina migrada a uma zona de disponibilidade específica na região. Utilize esta opção para distribuir servidores que formam um nível de aplicação de vários nós em zonas de disponibilidade. Se selecionar esta opção, terá de especificar a Zona de Disponibilidade para utilizar para cada uma das máquinas selecionadas no separador Compute. Esta opção só está disponível se a região-alvo selecionada para a migração suportar Zonas de Disponibilidade
    -  Disponibilidade Definir para colocar a máquina migrada num Conjunto de Disponibilidade. O Grupo de Recursos-Alvo que foi selecionado deve ter um ou mais conjuntos de disponibilidade para utilizar esta opção.
    - Não é necessária nenhuma opção de redundância de infraestrutura se não precisar de nenhuma destas configurações de disponibilidade para as máquinas migradas.
11. No **tipo de encriptação do disco,** selecione:
    - Encriptação em repouso com chave gerida pela plataforma
    - Encriptação em repouso com chave gerida pelo cliente
    - Dupla encriptação com chaves geridas pela plataforma e geridas pelo cliente

   > [!NOTE]
   > Para replicar VMs com CMK, terá de [criar uma encriptação de disco definida](../virtual-machines/disks-enable-customer-managed-keys-portal.md#set-up-your-disk-encryption-set) no Grupo de Recursos alvo. Um conjunto de conjunto de encriptação de disco mapeia Discos Geridos para um Cofre de Chaves que contém o CMK para usar para SSE.
  
12. Em **Benefício Híbrido do Azure**:

    - Selecione **Não** se não quiser aplicar o Benefício Híbrido do Azure. Em seguida, clique em **Seguinte**.
    - Selecione **Sim** se tiver computadores Windows Server abrangidos com subscrições ativas do Software Assurance ou do Windows Server e quiser aplicar o benefício aos computadores que está a migrar. Em seguida, clique em **Seguinte**.

    ![Definições de destino](./media/tutorial-migrate-vmware/target-settings.png)

13. No **Compute,** reveja o nome, tamanho, tipo de disco de SO e configuração de disponibilidade (se selecionado no passo anterior). As VMs devem estar em conformidade com os [requisitos do Azure](migrate-support-matrix-physical-migration.md#azure-vm-requirements).

    - **Tamanho VM**: Se estiver a utilizar recomendações de avaliação, o dropdown do tamanho VM mostra o tamanho recomendado. Caso contrário, o Azure Migrate escolhe um tamanho com base na correspondência mais próxima na subscrição do Azure. Como alternativa, escolha um tamanho manual em **Tamanho de VMs do Azure**.
    - **Disco de os:** Especifique o disco DE (arranque) para o VM. O disco do SO é o disco que possui o carregador e o instalador do sistema operativo.
    - **Zona de Disponibilidade**: Especifique a Zona de Disponibilidade a utilizar.
    - **Conjunto de disponibilidade**: Especifique o Conjunto de Disponibilidade a utilizar.

![Definições de cálculo](./media/tutorial-migrate-physical-virtual-machines/compute-settings.png)

14. Em Discos , **especifique** se os discos VM devem ser replicados para Azure e selecione o tipo de disco (discos geridos padrão SSD/HDD ou discos geridos premium) em Azure. Em seguida, clique em **Seguinte**.
    - Pode excluir discos da replicação.
    - Se excluir discos, estes não estarão presentes na VM do Azure após a migração. 

    ![Definições de disco](./media/tutorial-migrate-physical-virtual-machines/disks.png)

15. Em **Analisar e iniciar a replicação**, analise as definições e clique em **Replicar** para iniciar a replicação inicial para os servidores.

> [!NOTE]
> Pode atualizar as definições de replicação a qualquer momento antes de começar a replicação, **Gerir**  >  **máquinas de replicação**. As definições não podem ser alteradas após o início da replicação.

## <a name="track-and-monitor-replication-status"></a>Rastrear e monitorizar o estado de replicação

- Quando clicar em **Replicar** um trabalho de replicação inicial começa.
- Quando o trabalho de Replicação inicial termina com sucesso, os VMs iniciam a sua replicação inicial ao Azure.
- Após o final da replicação inicial, começa a replicação delta. As alterações incrementais dos discos VM GCP são periodicamente replicadas nos discos de réplica em Azure.

Pode acompanhar o estado do trabalho nas notificações do portal.

Pode monitorizar o estado de replicação clicando em **servidores replicantes** em **Azure Migrate: Migração do servidor**.  

![Monitorizar a replicação](./media/tutorial-migrate-physical-virtual-machines/replicating-servers.png)

## <a name="run-a-test-migration"></a>Executar uma migração de teste

Quando a replicação delta começar, pode fazer uma migração de teste para os VMs, antes de executar uma migração completa para Azure. A migração de testes é altamente recomendada e proporciona uma oportunidade para descobrir quaisquer problemas potenciais e corrigi-los antes de prosseguir com a migração real. É aconselhável fazer isto pelo menos uma vez por cada VM antes de o migrar.

- A realização de um teste de controlos de migração de que a migração funcionará como esperado, sem afetar os VMs GCP, que permanecem operacionais, e continuam a replicar-se.
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


## <a name="migrate-gcp-vms"></a>VMs de GCP migram

Depois de verificar que a migração do teste funciona como esperado, pode migrar os VMs GCP.

1. No projeto Azure Migrate > **Servers**  >  **Azure Migrate: Server Migration**, clique em **Replicating servidores**.

    ![Replicar servidores](./media/tutorial-migrate-physical-virtual-machines/replicate-servers.png)

2. Em **Replicar computadores**, clique com o botão direito do rato na VM > **Migrar**.
3. Em **Migrar**  >  **Desligue as máquinas virtuais e realize uma migração planeada sem perda de dados**, selecione **Yes**  >  **OK**.
    - Se não quiser desligar o VM, selecione **Nº**.
4. Uma tarefa de migração é iniciada para a VM. Pode ver o estado do trabalho clicando no ícone da campainha de notificação no canto superior direito da página do portal ou indo para a página de trabalhos da ferramenta de migração do servidor (Clique em visão geral no azulejo da ferramenta > Selecione Jobs a partir do menu esquerdo).
5. Após a conclusão da tarefa, pode ver e gerir a VM na página Máquinas Virtuais.

### <a name="complete-the-migration"></a>Complete a migração

1. Depois de terminar a migração, clique com a direita na VM > **Parar a migração**. Isto faz o seguinte:
    - Para a replicação para o GCP VM.
    - Remove o VM GCP da contagem de **servidores replicantes** em Azure Migrate: Migração do servidor.
    - Limpa a informação do estado de replicação para o VM.
2. Instale o Azure VM [Windows](../virtual-machines/extensions/agent-windows.md) ou o agente [Linux](../virtual-machines/extensions/agent-linux.md) nas máquinas migratórias.
3. Execute otimizações de aplicação pós-migração, tais como atualizar cadeias de ligação de base de dados e configurações de servidor Web.
4. Execute testes de aplicação final e de aceitação da migração na aplicação migrada em execução no Azure.
5. Corte o tráfego para o exemplo de Azure VM migrado.
6. Atualize qualquer documentação interna para mostrar a nova localização e endereço IP das VMs do Azure. 




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
    - Considere implementar o [Azure Cost Management](../cost-management-billing/cloudyn/overview.md) para monitorizar a utilização e as despesas do recurso.



## <a name="troubleshooting--tips"></a>Resolução de problemas / Dicas

**Pergunta:** Não consigo ver o meu VM GCP na lista descoberta de servidores para migração   
**Resposta:** Verifique se o seu aparelho de replicação satisfaz os requisitos. Certifique-se de que o Agente de Mobilidade está instalado na origem VM para ser migrado e está registado o Servidor de Configuração. Verifique as regras de firewall para permitir uma via de rede entre o aparelho de replicação e os VMs GCP de origem.  

**Pergunta:** Como sei se o meu VM foi migrado com sucesso?   
**Resposta:** Após a migração, pode ver e gerir o VM a partir da página Máquinas Virtuais. Ligue-se ao VM migrado para validar.  

**Pergunta:** Sou incapaz de importar VMs para migração dos meus resultados de avaliação de servidores anteriormente criados   
**Resposta:** Atualmente, não apoiamos a importação de avaliação para este fluxo de trabalho. Como solução alternativa, pode exportar a avaliação e, em seguida, selecionar manualmente a recomendação VM durante o passo de Replicação ativa.
  
**Pergunta:** Estou a receber o erro "Falhei em ir buscar o BIOS GUID" enquanto tentava descobrir os meus VMs GCP   
**Resposta:** Utilize o login de raiz para autenticação e não para qualquer pseudo utilizador. Se não conseguir utilizar um utilizador de raiz, certifique-se de que as capacidades necessárias são definidas no utilizador, de acordo com as instruções fornecidas na [matriz de suporte](migrate-support-matrix-physical.md#physical-server-requirements). Reveja também os sistemas operativos suportados para VMs GCP.  

**Pergunta:** O meu estado de replicação não está a progredir.   
**Resposta:** Verifique se o seu aparelho de replicação satisfaz os requisitos. Certifique-se de que ativou as portas necessárias no seu aparelho de replicação TCP porta 9443 e HTTPS 443 para o transporte de dados. Certifique-se de que não existem versões duplicadas do aparelho de replicação ligadas ao mesmo projeto.   

**Pergunta:** Não consigo descobrir instâncias GCP usando Azure Migrate devido ao código de estado HTTP 504 do serviço remoto de gestão do Windows    
**Resposta:** Certifique-se de rever os requisitos do aparelho migrar a Azure e as necessidades de acesso a URL. Certifique-se de que não estão a bloquear o registo do aparelho.

**Pergunta:** Tenho de fazer alguma alteração antes de migrar os meus VMs GCP para Azure   
**Resposta:** Poderá ter de escoar estas alterações antes de migrar os seus VMS EC2 para Azure:

- Se estiver a utilizar o init de nuvem para o seu fornecimento de VM, é melhor desativar a inserção de nuvens no VM antes de a replicar para Ozure. As etapas de provisionamento realizadas por cloud-init no VM talvez GCP específico e não serão válidas após a migração para Azure.  
- Reveja a secção [de pré-requisitos](#prerequisites) para determinar se existem alterações necessárias para o sistema operativo antes de as migrar para Azure.
- Recomendamos sempre que faça um teste de migração antes da migração final.  

## <a name="next-steps"></a>Próximos passos

Investigue a [jornada de migração](/azure/architecture/cloud-adoption/getting-started/migrate) em nuvem no Quadro de Azure Cloud Adopt.