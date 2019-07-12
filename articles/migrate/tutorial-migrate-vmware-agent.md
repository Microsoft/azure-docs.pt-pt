---
title: Para migrar VMs no local VMware no Azure com o agente-migração com base no Azure migrar servidor | Documentos da Microsoft
description: Este artigo descreve como efetuar uma migração com base em agente de máquinas no local para o Azure com a migração do servidor de migrar do Azure
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 07/08/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 78edd22502bbf08fda8d1828413bbc0b0f8aa9c7
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67809338"
---
# <a name="migrate-vmware-vms-to-azure-agent-based"></a>Migrar VMs de VMware para o Azure (baseado em agente)

Este artigo mostra como migrar VMs de VMware no local para o Azure, utilizando a migração baseada em agente com a ferramenta de migração do servidor de migrar do Azure.

[O Azure Migrate](migrate-services-overview.md) fornece um concentrador central para controlar a deteção, avaliação e migração de aplicações no local e cargas de trabalho e instâncias de VM do AWS/GCP, para o Azure. O hub fornece ferramentas do Azure Migrate para avaliação e migração, bem como ofertas de (ISV fornecedor) de terceiros independentes de software.


Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Configurar o ambiente de origem e implementar uma aplicação de replicação do Azure Migrate para migração com base no agente.
> * Configure o ambiente de destino para migração.
> * Configure uma política de replicação.
> * Ative a replicação.
> * Execute uma migração de teste para se certificar de que está tudo a funcionar conforme esperado.
> * Executar um uma migração completa para o Azure.

> [!NOTE]
> Tutoriais mostram-lhe o caminho de implantação mais simples para um cenário para que pode configurar rapidamente a uma prova de conceito. Tutoriais utilizam as opções predefinidas, sempre que possível e não mostram todas as configurações possíveis e caminhos. Para obter instruções detalhadas, consulte os guias de instruções para a migração e de avaliação de VMware.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.

## <a name="before-you-begin"></a>Antes de começar

Recomendamos que experimente avaliação da VM de VMware com avaliação migrar do servidor do Azure, antes de migrar VMs para o Azure. Configure uma avaliação da seguinte forma:

1. Siga o tutorial para [preparar o Azure e VMware](tutorial-prepare-vmware.md) para avaliação.
2. Em seguida, siga [deste tutorial](tutorial-assess-vmware.md) para configurar uma aplicação do Azure Migrate para avaliação e detetar e avaliar VMs.


Embora seja recomendável que experimentar uma avaliação, não precisa de executar uma avaliação antes de migrar as VMs.

## <a name="migration-methods"></a>Métodos de migração

É possível migrar VMs de VMware para o Azure com a ferramenta de migração do servidor de migrar do Azure. Essa ferramenta oferece duas opções para a migração de VM de VMware:

- Replicação sem agente. Migre VMs sem precisar instalar nada nos mesmos.
- Migração baseada em agente. ou a replicação. Instale um agente (o agente dos serviços de mobilidade) na VM para replicação.

Para decidir se pretende utilizar a migração sem agente ou com base em agente, consulte estes artigos:

- [Saiba mais sobre](server-migrate-overview.md) as opções de migração do VMware.
- [Reveja as limitações](server-migrate-overview.md#agentless-migration-limitations) para a migração sem agente.
- [Siga este artigo](tutorial-migrate-vmware.md) para experimentar o migração sem agente.



## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, tem de:

1. [Revisão](migrate-architecture.md) a arquitetura de migração do VMware.
2. Certifique-se de que a sua conta do Azure é atribuída a função de contribuinte de Máquina Virtual, para que não tem permissões para:

    - Criar uma VM no grupo de recursos selecionado.
    - Criar uma VM na rede virtual selecionada.
    - Escrever a Azure disco gerido. 

3. [Configurar uma rede do Azure](../virtual-network/manage-virtual-network.md#create-a-virtual-network). As máquinas são replicadas para o Azure no local discos geridos. Quando efetuar a ativação pós-falha para o Azure para a migração, as VMs do Azure são criadas a partir destes discos geridos e associadas a uma rede do Azure que especificar quando configurar a migração.


## <a name="prepare-azure"></a>Preparar o Azure

Se já tiver executado uma avaliação com a avaliação do servidor de migrar do Azure, pode ignorar as instruções nesta secção, uma vez que já concluiu estes passos. 

Se ainda não executou uma avaliação, terá de configurar as permissões do Azure para poder migrar com o Azure Migrate migração do servidor.

- **Criar um projeto**: Sua conta do Azure necessita de permissões para criar um projeto do Azure Migrate. 
- **Registe-se a aplicação de replicação do Azure Migrate**: A aplicação de replicação cria e regista uma aplicação do Azure Active Directory na sua conta do Azure. É necessário delegar permissões para isso.
- **Criar Cofre de chaves**: Para migrar VMs VMware com o Azure Migrate migração do servidor, Azure Migrate cria um cofre de chaves no grupo de recursos, para gerir as chaves de acesso à conta de armazenamento de replicação na sua subscrição. Para criar o cofre, tem as permissões de atribuição de função no grupo de recursos no qual reside o projeto do Azure Migrate. 


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

    ![Permissões do Azure AD](./media/tutorial-prepare-vmware/aad.png)

> [!NOTE]
> Esta é uma definição de predefinição que não seja confidencial. [Saiba mais](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).

#### <a name="assign-application-developer-role"></a>Atribuir a função de programador da aplicação 

O administrador global/inquilino pode atribuir a função de programador da aplicação a uma conta. [Saiba mais](https://docs.microsoft.comazure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).

## <a name="assign-permissions-to-create-key-vault"></a>Atribuir permissões para criar o Cofre de chaves

Atribua permissões de atribuição de função no grupo de recursos em que o projeto do Azure Migrate reside, da seguinte forma:

1. No grupo de recursos no portal do Azure, selecione **controlo de acesso (IAM)** .
2. Na **verificar acesso**, localizar a conta relevante e clique nele para ver as permissões. Precisa **proprietário** (ou **contribuinte** e **administrador de acesso de utilizador**) permissões.
3. Se não tiver as permissões necessárias, solicitá-los do proprietário do grupo de recursos. 


## <a name="prepare-on-premises-vmware"></a>Preparar o VMware no local

### <a name="prepare-an-account-for-automatic-discovery"></a>Preparar uma conta para a deteção automática

Migração de servidor migrar do Azure precisa de acesso a servidores VMware para:

- Detetar automaticamente VMs. É necessária, pelo menos, uma conta só de leitura.
- Orquestrar a replicação, a ativação pós-falha e a reativação pós-falha. Precisa de uma conta com capacidade para executar operações como criar e remover discos, e ligar VMs.

Crie a conta da seguinte forma:

1. Para utilizar uma conta dedicada, crie uma função ao nível do vCenter. Dê um nome à função, como **Azure_Site_Recovery**.
2. Atribua à função as permissões resumidas na tabela abaixo.
3. Crie um utilizador no servidor vCenter ou no anfitrião vSphere. Atribua a função ao utilizador.

#### <a name="vmware-account-permissions"></a>Permissões de conta de VMware

**Tarefa** | **Função/Permissões** | **Detalhes**
--- | --- | ---
**Deteção de VMs** | Pelo menos um utilizador só de leitura<br/><br/> Objeto Data Center –> Propagar ao Objeto Subordinado, função=Só de Leitura | Utilizador atribuído ao nível do datacenter, com acesso a todos os objetos no datacenter.<br/><br/> Para restringir o acesso, atribua a função **Sem acesso** com **Propagar ao objeto subordinado** aos objetos subordinados (anfitriões vSphere, arquivos de dados, VMs e redes).
**Replicação, ativação pós-falha e reativação pós-falha completas** |  Criar uma função (Azure_Site_Recovery) com as permissões necessárias e, em seguida, atribuir a função a um grupo ou utilizador de VMware<br/><br/> Objeto Data Center –> Propagar ao Objeto Subordinado, função=Azure_Site_Recovery<br/><br/> Arquivo de Dados -> Alocar espaço, navegar no arquivo de dados, operações de ficheiro de baixo nível, remover ficheiros, atualizar ficheiros de máquinas virtuais<br/><br/> Rede -> Atribuição de rede<br/><br/> Recursos -> Atribuir VM a agrupamento de recursos, migrar VMs desligadas, migrar VMs ligadas<br/><br/> Tarefas -> Criar tarefa, atualizar tarefa<br/><br/> Máquina virtual -> Configuração<br/><br/> Máquina virtual -> Interagir -> responder a perguntas, ligação de dispositivos, configurar suportes de dados em CD, configurar suportes de dados em disquete, desligar, ligar, instalação de ferramentas de VMware<br/><br/> Máquina virtual -> Inventário -> Criar, registar, anular o registo<br/><br/> Máquina virtual -> Aprovisionamento -> Permitir transferência de máquinas virtuais, permitir carregamento de ficheiros de máquinas virtuais<br/><br/> Máquina virtual -> Instantâneos -> Remover instantâneos | Utilizador atribuído ao nível do datacenter, com acesso a todos os objetos no datacenter.<br/><br/> Para restringir o acesso, atribua a função **Sem acesso** com **Propagar ao objeto subordinado** aos objetos subordinados (anfitriões vSphere, arquivos de dados, VMs e redes).

### <a name="prepare-an-account-for-mobility-service-installation"></a>Preparar uma conta para a instalação do serviço de Mobilidade

O Serviço de mobilidade tem de ser instalado em cada computador que queira replicar.

- A aplicação de replicação do Azure Migrate pode fazer uma instalação de push deste serviço, quando ativar a replicação para uma máquina ou pode instalá-lo manualmente, ou utilizando ferramentas de instalação.
- Neste tutorial, vamos instalar o Serviço de mobilidade com a instalação push.
- Para a instalação de push, terá de preparar uma conta que a migração do servidor de migrar do Azure pode utilizar para aceder à VM.

Prepare a conta da seguinte forma:

1. Prepare um domínio ou uma conta local com permissões para instalar na VM.
2. Para VMs do Windows, se não estiver a utilizar uma conta de domínio, desative o controlo de acesso de utilizador remoto na máquina local ao adicionar a entrada DWORD **LocalAccountTokenFilterPolicy**, com o valor no Registro, em **HKEY _ LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**
3. Para VMs do Linux, prepare-se uma conta raiz no servidor Linux de origem.


### <a name="check-vmware-requirements"></a>Verificar os requisitos de VMware

Certifique-se de que servidores de VMware e VMs que estão em conformidade com os requisitos para migração para o Azure. 


> [!NOTE]
> Migração baseada em agente com a migração do servidor de migrar do Azure baseia-se nas funcionalidades do serviço Azure Site Recovery. Alguns requisitos podem ligar a documentação do Site Recovery.

1. [Verifique](migrate-support-matrix-vmware.md#agent-based-migration-vmware-server-requirements) os requisitos do servidor do VMware.
2. [Certifique-se de](migrate-support-matrix-vmware.md#agent-based-migration-vmware-vm-requirements) VM suporte os requisitos para migração.
3. Verifique se as definições da VM. As VMs no local que replica para o Azure tem de cumprir [requisitos de VM do Azure](migrate-support-matrix-vmware.md#azure-vm-requirements).



## <a name="add-the-azure-migrate-server-migration-tool"></a>Adicionar a ferramenta de migração do servidor de migrar do Azure

Se não seguiu o tutorial para avaliar VMware VMs, configure um projeto do Azure Migrate e, em seguida, adicione a ferramenta de migração do servidor de migrar do Azure:

1. No portal do Azure > **todos os serviços**, procure **Azure Migrate**.
2. Sob **serviços**, selecione **Azure Migrate**.

    ![Configurar o Azure Migrate](./media/tutorial-migrate-vmware-agent/azure-migrate-search.png)

3. Na **descrição geral**, clique em **avaliar e migrar servidores**.
4. Sob **detetar, avaliar e migrar os servidores**, clique em **avaliar e migrar servidores**.

    ![Detetar e avaliar os servidores](./media/tutorial-migrate-vmware-agent/assess-migrate.png)

1. Na **detetar, avaliar e migrar os servidores**, clique em **adicionar ferramentas**.
2. Na **migrar projeto**, selecione a sua subscrição do Azure e criar um grupo de recursos se não tiver uma.
3. Na **detalhes do projeto**, especifique o nome do projeto e na geografia, no qual pretende criar o projeto e clique em **seguinte**

    ![Criar um projeto do Azure Migrate](./media/tutorial-migrate-vmware-agent/migrate-project.png)

    Pode criar um projeto do Azure Migrate em qualquer uma dessas áreas geográficas.

    **Geografia** | **Região**
    --- | ---
    Ásia | Sudeste Asiático
    Europa | Europa do Norte ou da Europa Ocidental
    Estados Unidos | E.U.A. Leste ou oeste dos E.U.A.

    A geografia especificada para o projeto só é utilizada para armazenar os metadados recolhidos das VMs no local. Pode selecionar qualquer região de destino para a migração real.
4. Na **ferramenta de avaliação Select**, selecione **ignorar a adição de uma ferramenta de avaliação por agora** > **seguinte**.
5. Na **ferramenta de migração Select**, selecione **Azure Migrate: Migração do servidor** > **próxima**.
6. Na **rever + adicionar ferramentas**, reveja as definições e clique em **adicionar ferramentas**
7. Depois de adicionar a ferramenta, é apresentado no projeto do Azure Migrate > **servidores** > **ferramentas de migração**.

## <a name="set-up-the-replication-appliance"></a>Configurar a aplicação de replicação

A primeira etapa de migração é configurar a aplicação de replicação. A aplicação de replicação é uma única, elevada disponibilidade, VM de VMware que aloja esses componentes no local:

- **Servidor de configuração**: O servidor de configuração coordena as comunicações entre o local e o Azure, e gere a replicação de dados.
- **Servidor de processos**: O servidor de processos atua como um gateway de replicação. Recebe dados de replicação. otimiza-os com colocação em cache, compressão e encriptação e envia-os para uma conta de armazenamento de cache no Azure. O servidor de processos também instala o agente do serviço de mobilidade nas VMs que pretende replicar, e efetua a deteção automática de VMs de VMware no local.


Para configurar a aplicação da replicação, transferir um modelo de aplicativo de Virtualização aberto (OVA) preparado. Importe o modelo para o VMware e criar a aplicação de replicação da VM. 

### <a name="download-the-replication-appliance-template"></a>Transferir o modelo de aplicação de replicação

Transferir o modelo da seguinte forma:

1. No projeto do Azure Migrate, clique em **servidores** sob **objetivos de migração**.
2. Na **do Azure Migrate - servidores** > **do Azure Migrate: Migração do servidor**, clique em **detetar**.

    ![Detetar VMs](./media/tutorial-migrate-vmware-agent/migrate-discover.png)

3. Na **detetar máquinas** > **são as suas máquinas virtualizadas?** , clique em **Sim, com o hipervisor do VMWare vSphere**.
4. Na **como deseja migrar?** , selecione **utilizando a replicação baseada em agente**.
5. Na **região de destino**, selecione a região do Azure para o qual pretende migrar as máquinas.
6. Selecione **confirmar que a região de destino para a migração é o nome da região**.
7. Clique em **criar recursos**. Esta ação cria um cofre de recuperação de sites do Azure em segundo plano.
    - Não é possível alterar a região de destino para este projeto depois de clicar neste botão.
    - Todas as migrações subsequentes são nesta região.

    ![Criar cofre dos Serviços de Recuperação](./media/tutorial-migrate-vmware-agent/create-resources.png)

8. Na **que pretende instalar um novo dispositivo de replicação?** , selecione **instalar uma aplicação de replicação**.
9. Clique em **transferir**, para transferir a aplicação de replicação. Esta ação transfere um modelo OVF que utilizar para criar uma nova VM do VMware que executa a aplicação.
    ![Baixe o OVA](./media/tutorial-migrate-vmware-agent/download-ova.png)
10. Tenha em atenção o nome do grupo de recursos e o Cofre dos serviços de recuperação. É necessário durante a implementação da aplicação.


### <a name="import-the-template-in-vmware"></a>Importar o modelo no VMware

Depois de transferir o modelo OVF, importá-lo para o VMware, para criar a aplicação de replicação numa VM do VMware com o Windows Server 2016.

1. Inicie sessão no servidor VMware vCenter ou no anfitrião vSphere ESXi, com o VMWare vSphere Client.
2. No menu **Ficheiro**, selecione **Implementar o Modelo OVF**, para iniciar o Assistente para **Implementar o Modelo OVF**. 
3. Na **selecionar origem**, introduza a localização do OVF transferido.
4. Na **rever detalhes**, selecione **próxima**.
5. Na **selecione o nome e a pasta** e **selecionar configuração**, aceite as predefinições.
6. Na **selecionar armazenamento** > **selecionar virtual formato de disco**, para melhor desempenho, selecione **Thick Provision Eager Zeroed**.
7. Nas restantes páginas do assistente, aceite as predefinições.
8. Na **pronto para concluir**, para configurar uma VM com as configurações padrão, selecionadas **ligar após a implementação** > **concluir**.

   > [!TIP]
   > Se pretender adicionar outra NIC, desmarque **Ligar após a implementação** > **Concluir**. Por predefinição, o modelo contém uma NIC única. Pode adicionar mais NICs após a implementação.

### <a name="kick-off-replication-appliance-setup"></a>Iniciar a configuração de aplicação de replicação

1. A partir da consola do VMWare vSphere Client, ligue a VM.
2. A VM arranca para uma experiência de instalação do Windows Server 2016. Aceite o contrato de licença e introduza uma palavra-passe de administrador.
3. Após a instalação terminar, inicie sessão para a VM como administrador, usando a palavra-passe de administrador.
4. Na primeira vez que iniciar sessão, a ferramenta de configuração de aplicação de replicação (ferramenta de configuração de recuperação de Site do Azure) é iniciado dentro de alguns segundos.
5. Introduza um nome a utilizar para registar a aplicação da migração do servidor de migrar do Azure. Clique depois em **Seguinte**.
6. A ferramenta verifica se a VM pode ligar ao Azure. Depois de a ligação estar estabelecida, selecione **Iniciar sessão** para iniciar sessão na sua subscrição do Azure.
7. Aguarde que a ferramenta acabe de registar uma aplicação do Azure AD para identificar a aplicação. A aplicação é reiniciada.
1. Inicie sessão na máquina novamente. Em poucos segundos, o Assistente de Gestão do Servidor de Configuração inicia automaticamente.

### <a name="register-the-replication-appliance"></a>Registre-se a aplicação de replicação

Conclua a configuração e registar a aplicação de replicação.

1. No Assistente de gestão do servidor de configuração, selecione **configurar a conectividade**.
2. Selecione o NIC (por predefinição há apenas uma NIC) que a aplicação de replicação utiliza para deteção de VMS e fazer uma instalação push do serviço de mobilidade nas máquinas de origem.
3. Selecione o NIC que a aplicação de replicação utiliza para a conectividade com o Azure. Em seguida, selecione **Guardar**. Não é possível alterar esta definição depois de estar configurada.
4. Se a aplicação estiver localizada atrás de um servidor proxy, tem de especificar as definições de proxy.
    - Especifique o nome do proxy **http://ip-address** , ou **http://FQDN** . Servidores de proxy HTTPS não são suportados.
5. Quando lhe for pedido para a subscrição, grupos de recursos e os detalhes do cofre, adicionam os detalhes que anotou quando transferiu o modelo de aplicação.
6. Em **Instalar software de terceiros**, aceite o contrato de licença. Selecione **Transferir e Instalar** para instalar o Servidor MySQL.
7. Selecione **Instalar VMware PowerCLI**. Certifique-se de que todas as janelas do browser estão fechadas antes de efetuar este procedimento. Em seguida, selecione **Continuar**.
8. Em **Validar configuração da aplicação**, os pré-requisitos são verificados antes de continuar.
9. Em **Configurar servidor vCenter Server/vSphere ESXi**, introduza o FQDN ou endereço IP do servidor vCenter ou anfitrião vSphere onde estão localizadas as VMs que pretende replicar. Introduza a porta em que o servidor está a escutar. Introduza um nome amigável a utilizar para o servidor VMware no cofre.
10. Introduza as credenciais da conta [criado](#prepare-an-account-for-automatic-discovery) para a deteção de VMware. Selecione **adicione** > **continuar**.
11. Na **configurar as credenciais da máquina virtual**, introduza as credenciais que [criada](#prepare-an-account-for-mobility-service-installation) para a instalação push do serviço de mobilidade, quando ativa a replicação para as VMs.  
    - Para computadores Windows, a conta precisa de privilégios de administrador local nos computadores que pretende replicar.
    - Para o Linux, forneça detalhes para a conta raiz.
12. Selecione **Finalizar configuração** para concluir o registo.


Depois da aplicação de replicação está registrada, avaliação de servidor de migrar do Azure liga-se aos servidores VMware com as definições especificadas e Deteta as VMs. Pode ver as VMs detetadas na **Manage** > **itens localizados**, na **outros** separador.


## <a name="replicate-vms"></a>Replicar VMs

1. No projeto do Azure Migrate > **servidores**, **Azure Migrate: Migração do servidor**, clique em **replicar**.

    ![Replicar VMs](./media/tutorial-migrate-vmware-agent/select-replicate.png)

2. Na **replicar**, > **definições da origem** > **são as suas máquinas virtualizadas?** , selecione **Sim, com o VMware vSphere**.
3. Na **aplicação no local**, selecione o nome da aplicação do Azure Migrate que configurou.
4. Na **servidor vCenter**, especifique o nome do servidor vCenter gerenciamento de VMs, ou o servidor vSphere nos quais as VMs estão alojadas.
5. Na **servidor de processos**, selecione o nome da aplicação da replicação.
6. Na **credenciais do convidado**, especifique a conta de administrador VM que será utilizada para a instalação push do serviço de mobilidade. Em seguida, clique em **seguinte: Máquinas virtuais**.

    ![Replicar VMs](./media/tutorial-migrate-vmware-agent/source-settings.png)

7. Na **máquinas virtuais**, selecione as máquinas que pretende replicar.

    - Se executar uma avaliação para as VMs, pode aplicar recomendações de (premium ou standard) de tipo de disco nos resultados da avaliação e de dimensionamento de VM. Para fazê-lo, em **importar definições de migração de uma avaliação do Azure Migrate?** , selecione a **Sim** opção.
    - Se não executou uma avaliação ou que não pretende utilizar as definições de avaliação, selecione o **não** opções.
    - Se tiver selecionado utilizar a avaliação, selecione o grupo de VMS e o nome de avaliação.

8. Verifique cada VM que pretende migrar. Em seguida, clique em **seguinte: Definições de destino**.
9. Na **definições de destino**, selecione a subscrição e região ao qual irá migrar e especifique o grupo de recursos no qual as VMs do Azure irá residir após a migração de destino.
10. Na **rede Virtual**, selecione a sub-rede/VNet do Azure para o qual as VMs do Azure vão ser associadas após a migração.
11. Na **benefício híbrido do Azure**:

    - Selecione **não** se não pretender aplicar o benefício híbrido do Azure. Clique depois em **Seguinte**.
    - Selecione **Sim** se tiver máquinas do Windows Server que são abrangidas por subscrições ativas do Software Assurance ou o Windows Server e que pretende aplicar o benefício para as máquinas que estiver a migrar. Clique depois em **Seguinte**.

12. Na **computação**, reveja o nome da VM, o tamanho, o tipo de disco do SO e o conjunto de disponibilidade. As VMs têm de cumprir [requisitos do Azure](migrate-support-matrix-vmware.md#agentless-migration-vmware-vm-requirements).

    - **Tamanho da VM**: Se estiver a utilizar as recomendações de avaliação, a lista pendente de tamanho VM irá conter o tamanho recomendado. Caso contrário, o Azure Migrate escolhe um tamanho com base na correspondência mais próxima na subscrição do Azure. Em alternativa, escolha um tamanho manual em **tamanho de VM do Azure**. 
    - **Disco do SO**: Especifique o disco do SO (arranque) para a VM. O disco do SO é o disco que tenha o carregador de inicialização do sistema operativo e o instalador. 
    - **Conjunto de disponibilidade**: Se a VM num conjunto após a migração de disponibilidade do Azure, especifique o conjunto. O conjunto tem de estar no grupo de recursos de destino especificado para a migração.

13. Na **discos**, especifique se os discos da VM devem ser replicados para o Azure e selecione o tipo de disco (SSD/HDD ou premium managed disks standard) no Azure. Clique depois em **Seguinte**.
    - Pode excluir discos da replicação.
    - Se excluir discos, não está presente na VM do Azure após a migração. 

14. Na **reveja e iniciar a replicação**, reveja as definições e clique em **replicar** para iniciar a replicação inicial para os servidores.

> [!NOTE]
> Pode atualizar as definições de replicação a qualquer momento antes de inicia a replicação, **Manage** > **replicar máquinas**. Não não possível alterar as definições depois da replicação é iniciada.




## <a name="track-and-monitor"></a>Controlar e monitorizar

- Quando clica em **replicar** começa uma tarefa de replicação começar. 
- Quando a tarefa de iniciar a replicação é concluída com êxito, as máquinas começam a replicação inicial para o Azure.
- Depois de concluída a replicação inicial, começa a replicação de diferenças. As alterações incrementais para discos locais periodicamente são replicadas para os discos de réplica no Azure.


Pode controlar o estado da tarefa nas notificações do portal.

![Tarefa de faixa](./media/tutorial-migrate-vmware-agent/jobs.png)

Pode monitorizar o estado de replicação ao clicar em **replicar servidores** no **Azure Migrate: Migração do servidor**.
![Monitor de replicação](./media/tutorial-migrate-vmware-agent/replicate-servers.png)

## <a name="run-a-test-migration"></a>Executar uma migração de teste


Quando a replicação de diferenças é iniciada, pode executar uma migração de teste para as VMs, antes de executar uma migração completa para o Azure. É altamente recomendável que fazer isso, pelo menos, uma vez para cada máquina, antes de migrá-lo.

- Executar uma migração de teste verifica se a migração irá funcionar conforme esperado, sem afetar as máquinas no local, que permanecerão operacional e continuar a replicar. 
- Migração de teste simula a migração através da criação de VM do Azure com os dados replicados (normalmente, migrar para uma VNet de não produção na sua subscrição do Azure).
- Pode utilizar o teste replicado VM do Azure para validar a migração, realizar o teste de aplicações e resolver quaisquer problemas antes da migração completa.

Fazer uma migração de teste da seguinte forma:


1. Na **objetivos de migração** > **servidores** > **Azure Migrate: Migração do servidor**, clique em **teste migrados servidores**.

     ![Testar servidores migrados](./media/tutorial-migrate-vmware-agent/test-migrated-servers.png)

2. Com o botão direito a VM para testar e clique em **teste migrar**.

    ![Migração de teste](./media/tutorial-migrate-vmware-agent/test-migrate.png)

3. Na **migração de teste**, selecione a VNet do Azure na qual a VM do Azure estarão localizada após a migração. Recomendamos que utilize uma VNet de não produção.
4. O **migração de teste** da tarefa é iniciada. Monitorize a tarefa nas notificações do portal.
5. Depois de concluída a migração, ver a VM migrada do Azure no **máquinas virtuais** no portal do Azure. O nome da máquina tem um sufixo **-teste**.
6. Depois do teste é concluído, clique com botão direito a VM do Azure no **replicar máquinas**e clique em **limpar a migração de teste**.

    ![Limpar a migração](./media/tutorial-migrate-vmware-agent/clean-up.png)


## <a name="migrate-vms"></a>Migrar VMs

Depois de verificar se a migração de teste funciona conforme esperado, pode migrar as máquinas no local.

1. No projeto do Azure Migrate > **servidores** > **Azure Migrate: Migração do servidor**, clique em **replicar servidores**.

    ![Replicar servidores](./media/tutorial-migrate-vmware-agent/replicate-servers.png)

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
-  Considere implementar o [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/overview) para monitorizar a utilização e as despesas do recurso.


## <a name="next-steps"></a>Passos Seguintes

Investigar o [percurso de migração da cloud](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/migrate) na estrutura de adoção de Cloud do Azure.
