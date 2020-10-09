---
title: VMs migrar VMware com migração de servidores Azure Migrate com base em agentes
description: Aprenda a executar uma migração baseada em agentes de VMware VMs com Azure Migrate.
ms.topic: tutorial
ms.date: 06/09/2020
ms.custom: MVC
ms.openlocfilehash: f437c0f3b9f786863d3b58f10d1a7384b0f1e8ba
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91296149"
---
# <a name="migrate-vmware-vms-to-azure-agent-based"></a>Migrar VMware VMs para Azure (baseado em agente)

Este artigo mostra-lhe como migrar vMware VMs para Azure, utilizando a ferramenta [Azure Migrate:Server Migration,](migrate-services-overview.md#azure-migrate-server-migration-tool) com migração baseada em agentes.  Também pode migrar VMware VMs usando migração baseada em agentes. [Compare](server-migrate-overview.md#compare-migration-methods) os métodos.


 Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Prepare o Azure para trabalhar com a Azure Migrate.
> * Preparem-se para a migração baseada em agentes. Crie uma conta VMware para que a Azure Migrate possa descobrir máquinas para migração. Crie uma conta para que o agente de serviço de Mobilidade possa instalar nas máquinas que pretende migrar e preparar uma máquina para funcionar como o aparelho de replicação.
> * Adicione a ferramenta Azure Migrate:Server Migration
> * Configurar o aparelho de replicação.
> * Replicar as VMs.
> * Faça um teste de migração para garantir que tudo está funcionando como esperado.
> * Fazer uma migração completa para Azure.

> [!NOTE]
> Os tutoriais mostram-lhe o caminho de implantação mais simples para um cenário para que possa configurar rapidamente uma prova de conceito. Os tutoriais utilizam opções predefinidas sempre que possível e não mostram todas as definições e caminhos possíveis. 

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.


## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este tutorial, [reveja](./agent-based-migration-architecture.md) a arquitetura migratória baseada em agentes VMware.

## <a name="prepare-azure"></a>Preparar o Azure

Complete as tarefas na tabela para preparar o Azure para a migração baseada em agentes.

**Tarefa** | **Detalhes**
--- | ---
**Criar um projeto do Azure Migrate** | A sua conta Azure necessita de permissões de Colaborador ou Proprietário para criar um projeto.
**Verifique permissões da conta Azure** | A sua conta Azure necessita de permissões para criar um VM e escrever para um disco gerido pelo Azure.
**Criar uma rede Azure** | Crie uma rede que os VMs da Azure se juntem após a migração.

### <a name="assign-permissions-to-create-project"></a>Atribuir permissões para criar projeto
Se não tiver um projeto Azure Migrate, verifique as permissões para criar um.


1. No portal do Azure, abra a subscrição e selecione **Controlo de acesso (IAM)** .
2. No **Acesso ao Cheque,** encontre a conta relevante e clique nela para ver permissões.
3. Verifique se tem permissões **de Colaborador** ou **Proprietário.**

    - Se acabou de criar uma conta gratuita do Azure, é o proprietário da sua subscrição.
    - Se não for o proprietário da subscrição, trabalhe com o proprietário para atribuir a função.
    
### <a name="assign-azure-account-permissions"></a>Atribuir permissões de conta Azure

Atribua a função contribuinte da máquina virtual à conta, para que tenha permissões para:

- Criar uma VM no grupo de recursos selecionado.
- Criar uma VM na rede virtual selecionada.
- Escreva para um disco gerido pelo Azure. 


### <a name="set-up-an-azure-network"></a>Configurar uma rede do Azure

[Crie uma rede Azure.](../virtual-network/manage-virtual-network.md#create-a-virtual-network) As máquinas no local são replicadas em discos geridos aZure. Quando falhas no Azure para migração, os VMs Azure são criados a partir destes discos geridos, e unidos à rede Azure que criaste.

## <a name="prepare-for-migration"></a>Prepare para a migração

Verifique os requisitos e permissões de suporte e prepare-se para implantar um aparelho de replicação. 

### <a name="prepare-an-account-to-discover-vms"></a>Preparar uma conta para descobrir VMs

A migração do servidor Azure Migrate necessita de acesso aos servidores VMware para descobrir VMs que pretende migrar. Crie a conta da seguinte forma:

1. Para utilizar uma conta dedicada, crie uma função ao nível do vCenter. Dê ao papel um nome como **Azure_Migrate**.
2. Atribua à função as permissões resumidas na tabela abaixo.
3. Crie um utilizador no servidor vCenter ou no anfitrião vSphere. Atribua a função ao utilizador.

#### <a name="vmware-account-permissions"></a>Permissões de conta de VMware

**Tarefa** | **Função/Permissões** | **Detalhes**
--- | --- | ---
**Deteção de VMs** | Pelo menos um utilizador só de leitura<br/><br/> Objeto Data Center –> Propagar ao Objeto Subordinado, função=Só de Leitura | Utilizador atribuído ao nível do datacenter, com acesso a todos os objetos no datacenter.<br/><br/> Para restringir o acesso, atribua a função **de não acesso** com o Propagate ao objeto **infantil,** aos objetos infantis (anfitriões vSphere, datastores, VMs e redes).
**Replicação** |  Criar uma função (Azure_Site_Recovery) com as permissões necessárias e, em seguida, atribuir a função a um grupo ou utilizador de VMware<br/><br/> Objeto Data Center –> Propagar ao Objeto Subordinado, função=Azure_Site_Recovery<br/><br/> Arquivo de Dados -> Alocar espaço, navegar no arquivo de dados, operações de ficheiro de baixo nível, remover ficheiros, atualizar ficheiros de máquinas virtuais<br/><br/> Rede -> Atribuição de rede<br/><br/> Recursos -> Atribuir VM a agrupamento de recursos, migrar VMs desligadas, migrar VMs ligadas<br/><br/> Tarefas -> Criar tarefa, atualizar tarefa<br/><br/> Máquina virtual -> Configuração<br/><br/> Máquina virtual -> Interagir -> responder a perguntas, ligação de dispositivos, configurar suportes de dados em CD, configurar suportes de dados em disquete, desligar, ligar, instalação de ferramentas de VMware<br/><br/> Máquina virtual -> Inventário -> Criar, registar, anular o registo<br/><br/> Máquina virtual -> Aprovisionamento -> Permitir transferência de máquinas virtuais, permitir carregamento de ficheiros de máquinas virtuais<br/><br/> Máquina virtual -> Instantâneos -> Remover instantâneos | Utilizador atribuído ao nível do datacenter, com acesso a todos os objetos no datacenter.<br/><br/> Para restringir o acesso, atribua a função **de acesso Sem acesso** com o Propagate ao objeto **infantil,** aos objetos infantis (vSSphere hosts, datastores, VMsa, nd networks).

### <a name="prepare-an-account-for-mobility-service-installation"></a>Preparar uma conta para a instalação do serviço de Mobilidade

O Serviço de mobilidade tem de ser instalado em cada computador que queira replicar.

- O aparelho de replicação Azure Migrate pode fazer uma instalação deste serviço quando ativar a replicação de uma máquina, ou pode instalá-lo manualmente ou utilizar ferramentas de instalação.
- Neste tutorial, vamos instalar o Serviço de mobilidade com a instalação push.
- Para a instalação push, precisa de preparar uma conta que a Migração do Servidor Azure Migrate pode utilizar para aceder ao VM. Esta conta é utilizada apenas para a instalação push, se não instalar manualmente o serviço Mobility.

Prepare a conta da seguinte forma:

1. Prepare um domínio ou uma conta local com permissões para instalar na VM.
2. Para os VMs do Windows, se não estiver a utilizar uma conta de domínio, desative o controlo de acesso ao utilizador remoto na máquina local adicionando a entrada DWORD **LocalAccountTokenFilterPolicy**, com um valor de registo, sob **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**
3. Para os VMs Linux, prepare uma conta raiz no servidor Linux de origem.


### <a name="prepare-a-machine-for-the-replication-appliance"></a>Preparar uma máquina para o aparelho de replicação

O aparelho é utilizado para replicar máquinas para Azure. O aparelho é único, altamente disponível, no local VMware VM que acolhe estes componentes:

- **Servidor de configuração**: O servidor de configuração coordena as comunicações entre as instalações e o Azure e gere a replicação de dados.
- **Servidor de**processo : O servidor de processo funciona como um gateway de replicação. Recebe dados de replicação; otimiza-o com caching, compressão e encriptação, e envia-o para uma conta de armazenamento de cache em Azure. O servidor de processo também instala o agente do Serviço de Mobilidade em VMs que pretende replicar, e executa a descoberta automática de VMware VMware no local.

Prepare-se para o aparelho da seguinte forma:

- [Rever os requisitos do aparelho](migrate-replication-appliance.md#appliance-requirements). Geralmente, configura o aparelho de replicação um VMware VM usando um ficheiro OVA descarregado. O modelo cria um aparelho que cumpre todos os requisitos.
- O MySQL deve ser instalado no aparelho. [Reveja os](migrate-replication-appliance.md#mysql-installation) métodos de instalação.
- Reveja os [URLs de nuvem pública](migrate-replication-appliance.md#url-access)e [URLs do Governo Azure](migrate-replication-appliance.md#azure-government-url-access) a que a máquina do aparelho precisa de aceder.
- [Reveja as portas](migrate-replication-appliance.md#port-access) a que a máquina de aparelhos de replicação precisa de aceder.



### <a name="check-vmware-requirements"></a>Verificar os requisitos de VMware

Certifique-se de que os servidores vMware e VMs cumprem os requisitos de migração para Azure. 

1. [Verifique](migrate-support-matrix-vmware-migration.md#vmware-requirements-agent-based) os requisitos do servidor do VMware.
2. [Verificar](migrate-support-matrix-vmware-migration.md#vm-requirements-agent-based) Requisitos de VM para migração.
3. Verifique as definições de Azure. As VMs no local que replicar para Azure devem cumprir os [requisitos da Azure VM](migrate-support-matrix-vmware-migration.md#azure-vm-requirements).
4. São necessárias algumas alterações nos VM antes de as migrar para Azure.
    - É importante fazer estas mudanças antes de começar a migração. Se migrar o VM antes de fazer a alteração, o VM pode não arrancar em Azure.
    - Reveja as alterações do [Windows](prepare-for-migration.md#windows-machines) e [do Linux](prepare-for-migration.md#linux-machines) que precisa de fazer.

> [!NOTE]
> A migração baseada em agentes com a migração do servidor Azure Migrate baseia-se nas funcionalidades do serviço de recuperação do site Azure. Alguns requisitos podem ligar-se à documentação de Recuperação do Local.



## <a name="add-the-azure-migrateserver-migration-tool"></a>Adicione a ferramenta Azure Migrate:Server Migration

Se ainda não tiver um projeto Azure Migrate, [crie-o](how-to-add-tool-first-time.md) agora e adicione a ferramenta de migração do servidor.

Se tiver um projeto, adicione a ferramenta da seguinte forma:

1. No portal do Azure > **Todos os serviços**, procure **Azure Migrate**.
2. Em **Serviços**, selecione **Azure Migrate**.

    ![Configurar Azure Migrate](./media/tutorial-migrate-vmware-agent/azure-migrate-search.png)

3. Em **Descrição geral**, clique em **Avaliar e migrar servidores**.
4. No **âmbito do Discover, avaliar e migrar servidores,** clique em **Avaliar e migrar servidores**.

    ![Detetar e avaliar servidores](./media/tutorial-migrate-vmware-agent/assess-migrate.png)

1. Em **Detetar, avaliar e migrar servidores**, clique em **Adicionar ferramentas**.
2. Em **Migrar projeto**, selecione a sua subscrição do Azure e crie um grupo de recursos, caso não tenha um.
3. Em **Detalhes do Projeto**, especifique o nome do projeto e a geografia na qual pretende criar o projeto e clique em **Seguinte**. Reveja geografias apoiadas para nuvens [públicas](migrate-support-matrix.md#supported-geographies-public-cloud) e [governamentais.](migrate-support-matrix.md#supported-geographies-azure-government)

    ![Criar um projeto do Azure Migrate](./media/tutorial-migrate-vmware-agent/migrate-project.png)


4. Em **Selecionar ferramenta de avaliação**, selecione **Ignorar a adição de uma ferramenta de avaliação por enquanto** > **Seguinte**.
5. Em **Selecionar ferramenta de migração**, selecione **Azure Migrate: Migração de Servidores** > **Seguinte**.
6. Em **Analisar + adicionar ferramentas**, analise as definições e clique em **Adicionar ferramentas**
7. Depois de adicionar a ferramenta, aparece no projeto Azure **Servers**Migrate >  >  **ferramentas de migração**de servidores .

## <a name="set-up-the-replication-appliance"></a>Configurar o aparelho de replicação

Este procedimento descreve como configurar o aparelho com um modelo de Aplicação de Virtualização Aberta (OVA) descarregado. Se não puder utilizar este método, pode configurar o aparelho [utilizando um script](tutorial-migrate-physical-virtual-machines.md#set-up-the-replication-appliance). 

### <a name="download-the-replication-appliance-template"></a>Descarregue o modelo do aparelho de replicação

Descarregue o modelo da seguinte forma:

1. No projeto Azure Migrate clique em **Servidores** em **Objetivos de Migração**.
2. In **Azure Migrate - Servidores**  >  **Azure Migrate: Migração do servidor,** clique em **Descobrir**.

    ![Detetar VMs](./media/tutorial-migrate-vmware-agent/migrate-discover.png)

3. In **Discover machines**  >  **Are your machines virtualized?** **Yes, with VMware vSphere hypervisor**
4. Em **Como pretende migrar?** **Using agent-based replication**
5. Na **região Alvo,** selecione a região de Azure para a qual pretende migrar as máquinas.
6. **Selecione Confirme que a região-alvo para a migração é o nome da região.**
7. Clique **em Criar recursos.** Isto cria um cofre de recuperação do local de Azure em segundo plano. Não é possível alterar a região alvo deste projeto depois de clicar neste botão, e todas as migrações subsequentes são para esta região.

    ![Criar cofre dos Serviços de Recuperação](./media/tutorial-migrate-vmware-agent/create-resources.png)

8. Na **Install a replication appliance** **instalação de um novo aparelho de replicação?**
9. Clique **em Baixar**. Isto descarrega um modelo OVF.
    ![Baixar OVA](./media/tutorial-migrate-vmware-agent/download-ova.png)
10. Observe o nome do grupo de recursos e do cofre dos Serviços de Recuperação. Precisa destes durante a colocação do aparelho.


### <a name="import-the-template-in-vmware"></a>Importar o modelo no VMware

Depois de descarregar o modelo OVF, importa-o em VMware para criar a aplicação de replicação num VMware VM que executa o Windows Server 2016.

1. Inscreva-se no servidor VMware vCenter ou no vSphere ESXi com o VMware vSphere Client.
2. No menu **'Ficheiro',** selecione **implementar o modelo OVF** para iniciar o **Assistente de Modelo de OVF**de implementação . 
3. In **Select source**, insira a localização do OVF descarregado.
4. Em **detalhes de revisão**, selecione **Next**.
5. No **Nome e na pasta Select** e na **configuração Select,** aceite as definições predefinidos.
6. Em **Select storage**Select virtual disk  >  **format**, para melhor desempenho selecione Thick Provision Eager **Zeroed**.
7. Nas restantes páginas do assistente, aceite as predefinições.
8. Pronto **para completar,** para configurar o VM com as definições predefinidos, selecione **Ligar a ligar após a colocação**  >  **Terminar**.

   > [!TIP]
   > Se quiser adicionar um NIC adicional, **limpe a energia após a colocação**  >  **Terminar**. Por predefinição, o modelo contém uma NIC única. Pode adicionar mais NICs após a implementação.

### <a name="start-appliance-setup"></a>Iniciar a configuração do aparelho

1. Na consola VMware vSphere Client, ligue o VM. A VM arranca para uma experiência de instalação do Windows Server 2016.
2. Aceite o contrato de licença e introduza uma palavra-passe de administrador.
3. Após o fim da instalação, inscreva-se no VM como administrador, utilizando a palavra-passe de administração. A primeira vez que iniciar seduca, a ferramenta de configuração do aparelho de replicação (Ferramenta de Configuração de Recuperação do Local Azure) começa dentro de alguns segundos.
5. Introduza um nome a utilizar para registar o aparelho com a Migração do Servidor. Em seguida, clique em **Seguinte**.
6. A ferramenta verifica se a VM pode ligar ao Azure. Depois de a ligação estar estabelecida, selecione **Iniciar sessão** para iniciar sessão na sua subscrição do Azure.
7. Aguarde que a ferramenta termine de registar uma aplicação AD AZure para identificar o aparelho. O aparelho reinicia.
1. Inicie sessão na máquina novamente. Em poucos segundos, o Assistente de Gestão do Servidor de Configuração inicia automaticamente.

### <a name="register-the-replication-appliance"></a>Registar o aparelho de replicação

Termine de configurar e registar o aparelho de replicação.

1. Na configuração do aparelho, selecione **Configurar conectividade**.
2. Selecione o NIC (por padrão existe apenas um NIC) que o aparelho de replicação utiliza para a descoberta de VM, e para fazer uma instalação push do serviço de Mobilidade em máquinas de origem.
3. Selecione o NIC que o aparelho de replicação utiliza para a conectividade com o Azure. Em seguida, selecione **Guardar**. Não é possível alterar esta definição depois de configurada.
4. Se o aparelho estiver localizado atrás de um servidor proxy, tem de especificar as definições de procuração.
    - Especifique o nome de procuração como **http://ip-address** , ou **http://FQDN** . Os servidores de procuração HTTPS não são suportados.
5. Quando solicitado para a subscrição, grupos de recursos e detalhes do cofre, adicione os detalhes que notou quando descarregou o modelo do aparelho.
6. Em **Instalar software de terceiros**, aceite o contrato de licença. Selecione **Transferir e Instalar** para instalar o Servidor MySQL.
7. Selecione **Instalar VMware PowerCLI**. Certifique-se de que todas as janelas do browser estão fechadas antes de efetuar este procedimento. Em seguida, selecione **Continuar**.
8. Em **Validar configuração da aplicação**, os pré-requisitos são verificados antes de continuar.
9. Em **Configurar servidor vCenter Server/vSphere ESXi**, introduza o FQDN ou endereço IP do servidor vCenter ou anfitrião vSphere onde estão localizadas as VMs que pretende replicar. Introduza a porta em que o servidor está a escutar. Introduza um nome amigável a utilizar para o servidor VMware no cofre.
10. Insira as credenciais para a conta que [criou](#prepare-an-account-to-discover-vms) para a descoberta da VMware. **Selecione**  >  **Adicionar Continuar**.
11. Nas **credenciais de máquina virtual Configure,** insira as credenciais que [criou](#prepare-an-account-for-mobility-service-installation) para impulsionar a instalação do serviço mobility, quando ativar a replicação para VMs.  
    - Para computadores Windows, a conta precisa de privilégios de administrador local nos computadores que pretende replicar.
    - Para o Linux, forneça detalhes para a conta raiz.
12. Selecione **Finalizar configuração** para concluir o registo.


Após a inscrição do aparelho de replicação, a Avaliação do Servidor Azure Migrate liga-se aos servidores VMware utilizando as definições especificadas e descobre VMs. Pode ver VMs **Manage**descobertos em  >  **itens Geridos**gestão, no separador **Outro.**



## <a name="replicate-vms"></a>Replicar VMs

Selecione VMs para migração.

> [!NOTE]
> No portal pode selecionar até 10 máquinas ao mesmo tempo para replicação. Se precisar de replicar mais, agrupe-os em lotes de 10.

1. No projeto Azure Migrate > **Servers**, **Azure Migrate: Server Migration**, clique em **Replicar**.

    ![Screenshot do ecrã dos Servidores em Azure Migrate. O botão Replicar é selecionado em Azure Migrate: Migração de servidores sob ferramentas de migração.](./media/tutorial-migrate-vmware-agent/select-replicate.png)

2. Em **Replicar**, **Definições de origem** > **Os computadores estão virtualizados?**, selecione **Sim, com o VMware vSphere**.
3. No **aparelho no local,** selecione o nome do aparelho Azure Migrate que instalou.
4. No **servidor vCenter**, especifique o nome do servidor vCenter que gere os VMs ou o servidor vSphere no qual os VMs estão hospedados.
5. No **Process Server,** selecione o nome do aparelho de replicação.
6. Nas **credenciais de Hóspedes**, especifique a conta de administração VM que será utilizada para a instalação de push do serviço Mobility. Em seguida, clique **em Seguinte: Máquinas virtuais**.

    ![Screenshot do separador Definições de Origem no ecrã Replica. O campo de credenciais de Hóspedes é destacado e o valor está definido para conta VM-administração.](./media/tutorial-migrate-vmware-agent/source-settings.png)

7. Em **Máquinas Virtuais,** selecione as máquinas que pretende replicar.

    - Se tiver executado uma avaliação para as VMs, poderá aplicar as recomendações de dimensionamento de VMs e tipo de discos (premium/standard) nos resultados da avaliação. Para tal, em **Importar definições de migração de uma avaliação do Azure Migrate?**, selecione a opção **Sim**.
    - Se não tiver executado uma avaliação ou não quiser utilizar as definições de avaliação, selecione as opções **Não**.
    - Se tiver selecionado para utilizar a avaliação, selecione o grupo de VMs e o nome da avaliação.
8. Nas **opções de Disponibilidade,** selecione:
    -  Zona de Disponibilidade para fixar a máquina migrada a uma zona de disponibilidade específica na região. Utilize esta opção para distribuir servidores que formam um nível de aplicação de vários nós em zonas de disponibilidade. Se selecionar esta opção, terá de especificar a Zona de Disponibilidade para utilizar para cada uma das máquinas selecionadas no separador Compute. Esta opção só está disponível se a região-alvo selecionada para a migração suportar Zonas de Disponibilidade
    -  Disponibilidade Definir para colocar a máquina migrada num Conjunto de Disponibilidade. O Grupo de Recursos-Alvo que foi selecionado deve ter um ou mais conjuntos de disponibilidade para utilizar esta opção.
    - Não é necessária nenhuma opção de redundância de infraestrutura se não precisar de nenhuma destas configurações de disponibilidade para as máquinas migradas.
9. Verifique cada VM que deseja migrar. Em seguida, clique em **Seguinte: Definições de destino**.
10. Em **Definições de destino**, selecione a subscrição, assim como a região de destino para a qual vai migrar, e especifique o grupo de recursos no qual as VMs do Azure vão residir após a migração.
11. Em **Rede Virtual**, selecione a VNet/sub-rede do Azure na qual as VMs do Azure vão ser associadas após a migração.
12. Nas **opções de Disponibilidade,** selecione:
    -  Zona de Disponibilidade para fixar a máquina migrada a uma zona de disponibilidade específica na região. Utilize esta opção para distribuir servidores que formam um nível de aplicação de vários nós em zonas de disponibilidade. Se selecionar esta opção, terá de especificar a Zona de Disponibilidade para utilizar para cada uma das máquinas selecionadas no separador Compute. Esta opção só está disponível se a região-alvo selecionada para a migração suportar Zonas de Disponibilidade
    -  Disponibilidade Definir para colocar a máquina migrada num Conjunto de Disponibilidade. O Grupo de Recursos-Alvo que foi selecionado deve ter um ou mais conjuntos de disponibilidade para utilizar esta opção.
    - Não é necessária nenhuma opção de redundância de infraestrutura se não precisar de nenhuma destas configurações de disponibilidade para as máquinas migradas.
    
13. Em **Benefício Híbrido do Azure**:

    - Selecione **Não** se não quiser aplicar o Benefício Híbrido do Azure. Em seguida, clique em **Seguinte**.
    - Selecione **Sim** se tiver computadores Windows Server abrangidos com subscrições ativas do Software Assurance ou do Windows Server e quiser aplicar o benefício aos computadores que está a migrar. Em seguida, clique em **Seguinte**.

14. No **Compute,** reveja o nome, tamanho, tipo de disco de SO e configuração de disponibilidade (se selecionado no passo anterior). As VMs devem estar em conformidade com os [requisitos do Azure](migrate-support-matrix-vmware-migration.md#azure-vm-requirements).

   - **Tamanho VM**: Se estiver a utilizar recomendações de avaliação, o dropdown do tamanho VM mostra o tamanho recomendado. Caso contrário, o Azure Migrate escolhe um tamanho com base na correspondência mais próxima na subscrição do Azure. Como alternativa, escolha um tamanho manual em **Tamanho de VMs do Azure**. 
    - **Disco de os:** Especifique o disco DE (arranque) para o VM. O disco do SO é o disco que possui o carregador e o instalador do sistema operativo. 
    - **Zona de Disponibilidade**: Especifique a Zona de Disponibilidade a utilizar.
    - **Conjunto de disponibilidade**: Especifique o Conjunto de Disponibilidade a utilizar.

15. Em Discos , **especifique**se os discos VM devem ser replicados para Azure e selecione o tipo de disco (discos geridos padrão SSD/HDD ou discos geridos premium) em Azure. Em seguida, clique em **Seguinte**.
    - Pode excluir discos da replicação.
    - Se excluir discos, estes não estarão presentes na VM do Azure após a migração. 

16. Em **Analisar e iniciar a replicação**, analise as definições e clique em **Replicar** para iniciar a replicação inicial para os servidores.

> [!NOTE]
> Pode atualizar as definições de replicação a qualquer momento antes de começar a replicação, **Gerir**  >  **máquinas de replicação**. As definições não podem ser alteradas após o início da replicação.


## <a name="track-and-monitor"></a>Rastreio e monitor

1. Acompanhe o estado do trabalho nas notificações do portal. 

    ![Trabalho de pista](./media/tutorial-migrate-vmware-agent/jobs.png)
    
2. Para monitorizar o estado de replicação, clique em **Replicar servidores** em **Azure Migrate: Migração do servidor**.

    ![Monitorizar a replicação](./media/tutorial-migrate-vmware-agent/replicate-servers.png)

A replicação ocorre da seguinte forma:
- Quando o trabalho de Replicação inicial termina com sucesso, as máquinas iniciam a sua replicação inicial ao Azure.
- Após o final da replicação inicial, começa a replicação delta. As alterações incrementais nos discos no local são periodicamente replicadas nos discos de réplica em Azure.


## <a name="run-a-test-migration"></a>Executar uma migração de teste


Quando a replicação delta começar, pode fazer uma migração de teste para os VMs, antes de executar uma migração completa para Azure. Recomendamos vivamente que o faça pelo menos uma vez por cada máquina, antes de a migrar.

- A realização de um teste de controlos de migração de que a migração funcionará como esperado, sem afetar as máquinas no local, que permanecem operacionais, e continuam a replicar-se. 
- A migração de testes simula a migração criando um VM Azure usando dados replicados (normalmente migrando para um VNet não produtivo na sua assinatura Azure).
- Pode utilizar o teste replicado Azure VM para validar a migração, realizar testes de aplicações e resolver quaisquer problemas antes da migração completa.

Faça uma migração de teste da seguinte forma:


1. Nos **objetivos de migração**  >  **Servers**  >  **Azure Migrate: Server Migration**, clique em Test **servidores migrados**.

     ![Testar servidores migrados](./media/tutorial-migrate-vmware-agent/test-migrated-servers.png)

2. Clique com o botão direito do rato na VM a testar e, em seguida, clique em **Testar migração**.

    ![Testar migração](./media/tutorial-migrate-vmware-agent/test-migrate.png)

3. Em **Testar Migração**, selecione a VNet do Azure na qual a VM do Azure vai estar localizada após a migração. Recomendamos que utilize uma VNet que não seja de produção.
4. A tarefa **Teste de migração** é iniciada. Monitorize a tarefa nas notificações do portal.
5. Após a conclusão da migração, veja a VM do Azure migrada em **Máquinas Virtuais** no portal do Azure. O nome do computador tem um sufixo **-Test**.
6. Após terminar o teste, clique com o botão direito do rato na VM do Azure em **Replicar computadores** e clique em **Limpar teste de migração**.

    ![Limpar a migração](./media/tutorial-migrate-vmware-agent/clean-up.png)


## <a name="migrate-vms"></a>Migrar VMs

Depois de verificar que a migração do teste funciona como esperado, pode migrar as máquinas no local.

1. No projeto Azure Migrate > **Servers**  >  **Azure Migrate: Server Migration**, clique em **Replicating servidores**.

    ![Replicar servidores](./media/tutorial-migrate-vmware-agent/replicate-servers.png)

2. Em **Replicar computadores**, clique com o botão direito do rato na VM > **Migrar**.
3. Em **Migrar**  >  **Desligue as máquinas virtuais e realize uma migração planeada sem perda de dados**, selecione **Yes**  >  **OK**.
    - Por defeito, a Azure Migrate desliga o VM no local para garantir a perda mínima de dados. 
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

- No local
    - Mova o tráfego da aplicação para a aplicação em execução na instância da VM do Azure migrada.
    - Remova as VMs no local do seu inventário de VMs locais.
    - Remova as VMs no local das cópias de segurança locais.
    - Atualize qualquer documentação interna para mostrar a nova localização e endereço IP das VMs do Azure.
- Ajuste as definições de Azure VM após a migração:
    - O [agente de VM do Azure](../virtual-machines/extensions/agent-windows.md) gere a interação da VM com o Controlador de Recursos de Infraestrutura do Azure. É necessário para alguns serviços do Azure, como o Azure Backup, o Site Recovery e a Segurança do Azure. Ao migrar VMare VMs com migração baseada em agentes, o instalador do Serviço de Mobilidade instala o agente Azure VM em máquinas Windows. Nos VMs Linux, recomendamos que instale o agente após a migração.
    - Desinstalar manualmente o serviço de Mobilidade do Azure VM após a migração.
    - Desinstalar manualmente as ferramentas VMware após a migração.
- Em Azure:
    - Execute otimizações de aplicação pós-migração, tais como atualizar cadeias de ligação de base de dados e configurações de servidor Web.
    - Execute testes de aplicação final e de aceitação da migração na aplicação migrada em execução no Azure.
- Continuidade do negócio/recuperação de desastres
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
