---
title: Migrar VMs VMware locais para o Azure com migração de servidor de migrações do Azure baseada em agente | Microsoft Docs
description: Este artigo descreve como executar uma migração baseada em agente de computadores locais para o Azure com a migração de servidor de migrações para Azure
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 09/04/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: aecbaab1ed29a1acfdcb4eec53b88fc266bbab09
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70309414"
---
# <a name="migrate-vmware-vms-to-azure-agent-based"></a>Migrar VMs do VMware para o Azure (baseado em agente)

Este artigo mostra como migrar VMs VMware locais para o Azure, usando a migração baseada em agente com a ferramenta de migração de servidor migrações para Azure.

As [migrações para Azure](migrate-services-overview.md) fornecem um hub central para acompanhar a descoberta, a avaliação e a migração de seus aplicativos e cargas de trabalho locais e as instâncias de VM AWS/GCP para o Azure. O Hub fornece ferramentas de migração do Azure para avaliação e migração, bem como ofertas de ISVs (fornecedores independentes de software) de terceiros.


Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Configure o ambiente de origem e implante um dispositivo de replicação de migração do Azure para a migração baseada em agente.
> * Configure o ambiente de destino para a migração.
> * Configure uma política de replicação.
> * Ative a replicação.
> * Execute uma migração de teste para verificar se tudo está funcionando conforme o esperado.
> * Execute uma migração completa para o Azure.

> [!NOTE]
> Os tutoriais mostram o caminho de implantação mais simples para um cenário, para que você possa configurar rapidamente uma prova de conceito. Os tutoriais usam as opções padrão sempre que possível e não mostram todas as configurações e caminhos possíveis. Para obter instruções detalhadas, consulte o How-tos para avaliação e migração do VMware.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.

## <a name="before-you-begin"></a>Antes de começar

Recomendamos que você experimente a avaliação de VM VMware com a avaliação de servidor de migrações para Azure, antes de migrar as VMs para o Azure. Configure uma avaliação da seguinte maneira:

1. Siga o tutorial para [preparar o Azure e o VMware](tutorial-prepare-vmware.md) para avaliação.
2. Em seguida, siga [este tutorial](tutorial-assess-vmware.md) para configurar um dispositivo de migração do Azure para avaliação e descobrir e avaliar VMS.


Embora seja recomendável experimentar uma avaliação, você não precisa executar uma avaliação antes de migrar as VMs.

## <a name="migration-methods"></a>Métodos de migração

Você pode migrar VMs do VMware para o Azure usando a ferramenta de migração de servidor de migrações para Azure. Essa ferramenta oferece algumas opções para a migração de VM do VMware:

- Replicação sem agente. Migre VMs sem a necessidade de instalar nada nelas.
- Migração baseada em agente. ou replicação. Instale um agente (o agente de serviços de mobilidade) na VM para replicação.

Para decidir se deseja usar a migração baseada em agente ou sem agente, leia estes artigos:

- [Saiba mais sobre](server-migrate-overview.md) as opções de migração do VMware.
- [Compare os métodos de migração](server-migrate-overview.md#compare-migration-methods).
- [Siga este artigo](tutorial-migrate-vmware.md) para experimentar a migração sem agente.



## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, tem de:

1. [Examine](migrate-architecture.md) a arquitetura de migração do VMware.
2. Verifique se sua conta do Azure é atribuída à função colaborador da máquina virtual, para que você tenha permissões para:

    - Criar uma VM no grupo de recursos selecionado.
    - Criar uma VM na rede virtual selecionada.
    - Gravar em um disco gerenciado do Azure. 

3. [Configure uma rede do Azure](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Os computadores locais são replicados para o Azure Managed disks. Quando você faz failover para o Azure para migração, as VMs do Azure são criadas a partir desses discos gerenciados e ingressadas em uma rede do Azure que você especifica ao configurar a migração.


## <a name="prepare-azure"></a>Preparar o Azure

Se você já tiver executado uma avaliação com a avaliação do servidor de migrações para Azure, poderá ignorar as instruções nesta seção, pois você já concluiu essas etapas. 

Se você não tiver executado uma avaliação, precisará configurar as permissões do Azure antes de migrar com a migração de servidor de migrações para Azure.

- **Criar um projeto**: Sua conta do Azure precisa de permissões para criar um projeto de migrações para Azure. 
- **Registrar o dispositivo de replicação de migrações para Azure**: O dispositivo de replicação cria e registra um aplicativo Azure Active Directory em sua conta do Azure. Você precisa delegar permissões para isso.
- **Criar Key Vault**: Para migrar VMs VMware usando a migração de servidor de migrações para Azure, as migrações para Azure criam uma Key Vault no grupo de recursos, para gerenciar chaves de acesso para a conta de armazenamento de replicação em sua assinatura. Para criar o cofre, você precisa de permissões de atribuição de função no grupo de recursos no qual o projeto de migração do Azure reside. 


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

1. No Azure AD, o locatário/administrador global deve navegar até **Azure Active Directory** > **configurações de usuário**de**usuários** > .
2. O administrador deve definir **registros de aplicativo** como **Sim**.

    ![Permissões do Azure AD](./media/tutorial-prepare-vmware/aad.png)

> [!NOTE]
> Essa é uma configuração padrão que não é sensível. [Saiba mais](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).

#### <a name="assign-application-developer-role"></a>Atribuir função de desenvolvedor de aplicativo 

O locatário/administrador global pode atribuir a função de desenvolvedor de aplicativo a uma conta. [Saiba mais](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="assign-permissions-to-create-key-vault"></a>Atribuir permissões para criar Key Vault

Atribua permissões de atribuição de função no grupo de recursos no qual o projeto de migrações para Azure reside, da seguinte maneira:

1. No grupo de recursos no portal do Azure, selecione **controle de acesso (iam)** .
2. Em **verificar acesso**, localize a conta relevante e clique nela para exibir permissões. Você precisa de permissões de **proprietário** (ou **administrador de acesso de usuário**e **colaborador** ).
3. Se você não tiver as permissões necessárias, solicite-as do proprietário do grupo de recursos. 


## <a name="prepare-on-premises-vmware"></a>Preparar o VMware no local

### <a name="prepare-an-account-for-automatic-discovery"></a>Preparar uma conta para a deteção automática

A migração de servidor de migrações para Azure precisa acessar servidores VMware para:

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

- O dispositivo de replicação de migrações para Azure pode fazer uma instalação por push desse serviço quando você habilita a replicação de um computador, ou você pode instalá-lo manualmente ou usando ferramentas de instalação.
- Neste tutorial, vamos instalar o Serviço de mobilidade com a instalação push.
- Para a instalação por push, você precisa preparar uma conta que a migração do servidor de migrações para Azure pode usar para acessar a VM.

Prepare a conta da seguinte forma:

1. Prepare um domínio ou uma conta local com permissões para instalar na VM.
2. Para VMs do Windows, se você não estiver usando uma conta de domínio, desabilite o controle de acesso de usuário remoto no computador local adicionando a entrada DWORD **LocalAccountTokenFilterPolicy**, com um valor de no registro, em **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\ Windows\CurrentVersion\Policies\System**
3. Para VMs do Linux, prepare uma conta raiz no servidor Linux de origem.


### <a name="check-vmware-requirements"></a>Verificar os requisitos de VMware

Verifique se os servidores VMware e as VMs estão em conformidade com os requisitos de migração para o Azure. 


> [!NOTE]
> A migração baseada em agente com a migração de servidor de migrações para Azure baseia-se nos recursos do serviço de Azure Site Recovery. Alguns requisitos podem ser vinculados à documentação Site Recovery.

1. [Verifique](migrate-support-matrix-vmware.md#agent-based-migration-vmware-server-requirements) os requisitos do servidor do VMware.
2. [Verificar](migrate-support-matrix-vmware.md#agent-based-migration-vmware-vm-requirements) Requisitos de suporte de VM para migração.
3. Verifique as configurações da VM. As VMs locais que você replica para o Azure devem estar em conformidade com [os requisitos de VM do Azure](migrate-support-matrix-vmware.md#azure-vm-requirements).



## <a name="add-the-azure-migrate-server-migration-tool"></a>Adicionar a ferramenta de migração de servidor de migrações para Azure

Se você não seguir o tutorial para avaliar as VMs do VMware, configure um projeto de migrações para Azure e adicione a ferramenta de migração de servidor de migrações para Azure:

1. No portal do Azure > **Todos os serviços**, procure **Azure Migrate**.
2. Em **Serviços**, selecione **Azure Migrate**.

    ![Configurar migrações para Azure](./media/tutorial-migrate-vmware-agent/azure-migrate-search.png)

3. Em **Descrição geral**, clique em **Avaliar e migrar servidores**.
4. Em **descobrir, avaliar e migrar servidores**, clique em **avaliar e migrar servidores**.

    ![Descobrir e avaliar servidores](./media/tutorial-migrate-vmware-agent/assess-migrate.png)

1. Em **Detetar, avaliar e migrar servidores**, clique em **Adicionar ferramentas**.
2. Em **Migrar projeto**, selecione a sua subscrição do Azure e crie um grupo de recursos, caso não tenha um.
3. Em **detalhes do projeto**, especifique o nome do projeto e a geografia na qual você deseja criar o projeto e clique em **Avançar**

    ![Criar um projeto de migrações para Azure](./media/tutorial-migrate-vmware-agent/migrate-project.png)

    Você pode criar um projeto de migrações para Azure em qualquer uma dessas regiões.

    **Geografia** | **Região**
    --- | ---
    Ásia | Sudeste Asiático
    Europa | Europa Setentrional ou Europa Ocidental
    Estados Unidos | Leste dos EUA ou Oeste EUA Central

    A geografia especificada para o projeto só é utilizada para armazenar os metadados recolhidos das VMs no local. Você pode selecionar qualquer região de destino para a migração real.
4. Em **selecionar ferramenta de avaliação**, selecione **ignorar a adição de uma ferramenta de avaliação agora** > em**seguida**.
5. Em **Selecionar ferramenta de migração**, selecione **Azure Migrate:** Migração > de servidor**em seguida**.
6. Em **examinar + adicionar ferramentas**, examine as configurações e clique em **Adicionar ferramentas**
7. Depois de adicionar a ferramenta, ela aparecerá nas**ferramentas de migração**do projeto de migração do Azure > **servidores** > .

## <a name="set-up-the-replication-appliance"></a>Configurar o dispositivo de replicação

A primeira etapa da migração é configurar o dispositivo de replicação. O dispositivo de replicação é uma VM VMware local única e altamente disponível que hospeda esses componentes:

- **Servidor de configuração**: O servidor de configuração coordena as comunicações entre o local e o Azure, e gere a replicação de dados.
- **Servidor de processos**: O servidor de processos atua como um gateway de replicação. Ele recebe dados de replicação; otimiza-o com caching, compactação e criptografia e o envia para uma conta de armazenamento de cache no Azure. O servidor de processo também instala o agente do serviço de mobilidade nas VMs que você deseja replicar e executa a descoberta automática de VMs do VMware locais.


Para configurar o dispositivo de replicação, baixe um modelo OVA (Open Virtualization Application) preparado. Você importa o modelo para o VMware e cria a VM do dispositivo de replicação. 

### <a name="download-the-replication-appliance-template"></a>Baixar o modelo do dispositivo de replicação

Baixe o modelo da seguinte maneira:

1. No projeto migrações para Azure, clique em **servidores** em **metas de migração**.
2. No **Azure migrar-servidores** > **migrações do Azure: Migração**de servidor, clique em **descobrir**.

    ![Detetar VMs](./media/tutorial-migrate-vmware-agent/migrate-discover.png)

3. Nos **Descobrir computadores** > **Os computadores estão virtualizados?** , clique **Sim, com o hipervisor VMware vSphere**.
4. Em **como você deseja migrar?** , selecione **usando a replicação baseada em agente**.
5. Em **região de destino**, selecione a região do Azure para a qual você deseja migrar os computadores.
6. Selecione **confirmar que a região de destino para migração é Region-Name**.
7. Clique em **criar recursos**. Isso cria um cofre de Azure Site Recovery em segundo plano.
    - Você não pode alterar a região de destino deste projeto depois de clicar nesse botão.
    - Todas as migrações subsequentes são para essa região.

    ![Criar cofre dos Serviços de Recuperação](./media/tutorial-migrate-vmware-agent/create-resources.png)

8. Em deseja **instalar um novo dispositivo de replicação?** , selecione **instalar um dispositivo de replicação**.
9. Clique em **baixar**para baixar o dispositivo de replicação. Isso baixa um modelo OVF que você usa para criar uma nova VM VMware que executa o dispositivo.
    ![Baixar o OVA](./media/tutorial-migrate-vmware-agent/download-ova.png)
10. Observe o nome do grupo de recursos e o cofre dos serviços de recuperação. Você precisa deles durante a implantação do dispositivo.


### <a name="import-the-template-in-vmware"></a>Importar o modelo no VMware

Depois de baixar o modelo OVF, você o importará para o VMware para criar o aplicativo de replicação em uma VM VMware que executa o Windows Server 2016.

1. Inicie sessão no servidor VMware vCenter ou no anfitrião vSphere ESXi, com o VMWare vSphere Client.
2. No menu **Ficheiro**, selecione **Implementar o Modelo OVF**, para iniciar o Assistente para **Implementar o Modelo OVF**. 
3. Em **selecionar origem**, insira o local do OVF baixado.
4. Em **detalhes da revisão**, selecione **Avançar**.
5. Em **selecionar nome e pasta** e **selecione configuração**, aceite as configurações padrão.
6. Em **selecionar armazenamento** > ,**selecione formato de disco virtual**para obter o melhor desempenho selecione **provisionamento espesso adiantado para zero**.
7. Nas restantes páginas do assistente, aceite as predefinições.
8. Em **pronto para concluir**, para definir a VM com as configurações padrão, selecione **ligar após a implantação** > **concluir**.

   > [!TIP]
   > Se pretender adicionar outra NIC, desmarque **Ligar após a implementação** > **Concluir**. Por predefinição, o modelo contém uma NIC única. Pode adicionar mais NICs após a implementação.

### <a name="kick-off-replication-appliance-setup"></a>Iniciar a instalação do dispositivo de replicação

1. A partir da consola do VMWare vSphere Client, ligue a VM.
2. A VM arranca para uma experiência de instalação do Windows Server 2016. Aceite o contrato de licença e introduza uma palavra-passe de administrador.
3. Após a conclusão da instalação, entre na VM como administrador, usando a senha de administrador.
4. Na primeira vez que você entrar, a ferramenta de instalação do dispositivo de replicação (Azure Site Recovery ferramenta de configuração) será iniciada em alguns segundos.
5. Insira um nome a ser usado para registrar o dispositivo com a migração de servidor de migrações para Azure. Clique depois em **Seguinte**.
6. A ferramenta verifica se a VM pode ligar ao Azure. Depois de a ligação estar estabelecida, selecione **Iniciar sessão** para iniciar sessão na sua subscrição do Azure.
7. Aguarde até que a ferramenta termine de registrar um aplicativo do Azure AD para identificar o dispositivo. O dispositivo é reinicializado.
1. Inicie sessão na máquina novamente. Em poucos segundos, o Assistente de Gestão do Servidor de Configuração inicia automaticamente.

### <a name="register-the-replication-appliance"></a>Registrar o dispositivo de replicação

Conclua a configuração e o registro do dispositivo de replicação.

1. No assistente de gerenciamento do servidor de configuração, selecione **instalação conectividade**.
2. Selecione a NIC (por padrão, há apenas uma NIC) que o dispositivo de replicação usa para a descoberta de VM e para fazer uma instalação por push do serviço de mobilidade em computadores de origem.
3. Selecione a NIC que o dispositivo de replicação usa para conectividade com o Azure. Em seguida, selecione **Guardar**. Você não pode alterar essa configuração depois que ela é configurada.
4. Se o dispositivo estiver localizado atrás de um servidor proxy, você precisará especificar as configurações de proxy.
    - Especifique o nome do proxy **http://ip-address** como, **http://FQDN** ou. Não há suporte para servidores proxy HTTPS.
5. Quando for solicitada a assinatura, os grupos de recursos e os detalhes do cofre, adicione os detalhes que você anotou ao baixar o modelo do dispositivo.
6. Em **Instalar software de terceiros**, aceite o contrato de licença. Selecione **Transferir e Instalar** para instalar o Servidor MySQL.
7. Selecione **Instalar VMware PowerCLI**. Certifique-se de que todas as janelas do browser estão fechadas antes de efetuar este procedimento. Em seguida, selecione **Continuar**.
8. Em **Validar configuração da aplicação**, os pré-requisitos são verificados antes de continuar.
9. Em **Configurar servidor vCenter Server/vSphere ESXi**, introduza o FQDN ou endereço IP do servidor vCenter ou anfitrião vSphere onde estão localizadas as VMs que pretende replicar. Introduza a porta em que o servidor está a escutar. Introduza um nome amigável a utilizar para o servidor VMware no cofre.
10. Insira as credenciais para a conta que você [criou](#prepare-an-account-for-automatic-discovery) para a descoberta do VMware. Selecione **Adicionar** > **continuar**.
11. Em **configurar credenciais da máquina virtual**, insira as credenciais que você [criou](#prepare-an-account-for-mobility-service-installation) para a instalação por push do serviço de mobilidade, ao habilitar a replicação para VMs.  
    - Para computadores Windows, a conta precisa de privilégios de administrador local nos computadores que pretende replicar.
    - Para o Linux, forneça detalhes para a conta raiz.
12. Selecione **Finalizar configuração** para concluir o registo.


Depois que o dispositivo de replicação é registrado, a avaliação de servidor migrações para Azure se conecta aos servidores VMware usando as configurações especificadas e descobre as VMs. Você pode exibir as VMs descobertas em **gerenciar** > **itens descobertos**, na **outra** guia.


## <a name="replicate-vms"></a>Replicar VMs

Agora, selecione VMs para migração.

> [!NOTE]
> Você pode replicar até 10 computadores juntos. Se você precisar replicar mais, replique-os simultaneamente em lotes de 10.

1. No projeto do Azure Migrate > **Servidores**, **Azure Migrate: Migração do Servidor**, clique em **Replicar**.

    ![Replicar VMs](./media/tutorial-migrate-vmware-agent/select-replicate.png)

2. Em **Replicar**, **Definições de origem** > **Os computadores estão virtualizados?** , selecione **Sim, com o VMware vSphere**.
3. Em **dispositivo local**, selecione o nome do dispositivo de migrações para Azure que você configurou.
4. No **vCenter Server**, especifique o nome do servidor vCenter que gerencia as VMs ou o servidor vSphere no qual as VMs estão hospedadas.
5. Em **servidor de processo**, selecione o nome do dispositivo de replicação.
6. Em **credenciais de convidado**, especifique a conta de administrador de VM que será usada para a instalação por push do serviço de mobilidade. Depois, clique em **Seguinte: Máquinas**virtuais.

    ![Replicar VMs](./media/tutorial-migrate-vmware-agent/source-settings.png)

7. Em **máquinas virtuais**, selecione os computadores que você deseja replicar.

    - Se tiver executado uma avaliação para as VMs, poderá aplicar as recomendações de dimensionamento de VMs e tipo de discos (premium/standard) nos resultados da avaliação. Para tal, em **Importar definições de migração de uma avaliação do Azure Migrate?** , selecione a opção **Sim**.
    - Se não tiver executado uma avaliação ou não quiser utilizar as definições de avaliação, selecione as opções **Não**.
    - Se tiver selecionado para utilizar a avaliação, selecione o grupo de VMs e o nome da avaliação.

8. Verifique cada VM que você deseja migrar. Depois, clique em **Seguinte: Definições de destino**.
9. Em **Definições de destino**, selecione a subscrição, assim como a região de destino para a qual vai migrar, e especifique o grupo de recursos no qual as VMs do Azure vão residir após a migração.
10. Em **Rede Virtual**, selecione a VNet/sub-rede do Azure na qual as VMs do Azure vão ser associadas após a migração.
11. Em **Benefício Híbrido do Azure**:

    - Selecione **Não** se não quiser aplicar o Benefício Híbrido do Azure. Clique depois em **Seguinte**.
    - Selecione **Sim** se tiver computadores Windows Server abrangidos com subscrições ativas do Software Assurance ou do Windows Server e quiser aplicar o benefício aos computadores que está a migrar. Clique depois em **Seguinte**.

12. Em **Computação**, analise o nome, o tamanho, o tipo de disco do SO e o conjunto de disponibilidade das VMs. As VMs devem estar em conformidade com os [requisitos do Azure](migrate-support-matrix-vmware.md#agentless-migration-vmware-vm-requirements).

    - **Tamanho das VMs**: se estiver a utilizar recomendações de avaliação, a lista suspensa Tamanho de VMs incluirá o tamanho recomendado. Caso contrário, o Azure Migrate escolhe um tamanho com base na correspondência mais próxima na subscrição do Azure. Como alternativa, escolha um tamanho manual em **Tamanho de VMs do Azure**. 
    - **Disco do SO**: especifique o disco (arranque) do SO da VM. O disco do SO é o disco que possui o carregador e o instalador do sistema operativo. 
    - **Conjunto de disponibilidade**: se a VM deve estar num conjunto de disponibilidade do Azure após a migração, especifique o conjunto. O conjunto deve estar no grupo de recursos de destino especificado para a migração.

13. Em **discos**, especifique se os discos de VM devem ser replicados no Azure e selecione o tipo de disco (SSD padrão/HDD ou discos gerenciados Premium) no Azure. Clique depois em **Seguinte**.
    - Pode excluir discos da replicação.
    - Se excluir discos, estes não estarão presentes na VM do Azure após a migração. 

14. Em **Analisar e iniciar a replicação**, analise as definições e clique em **Replicar** para iniciar a replicação inicial para os servidores.

> [!NOTE]
> Você pode atualizar as configurações de replicação a qualquer momento antes de iniciar a replicação, **gerenciar** > **máquinas**de replicação. As definições não podem ser alteradas após o início da replicação.




## <a name="track-and-monitor"></a>Acompanhar e monitorar

- Quando você clica em **replicar** , um trabalho iniciar replicação é iniciado. 
- Quando o trabalho iniciar replicação é concluído com êxito, os computadores começam sua replicação inicial para o Azure.
- Após a conclusão da replicação inicial, a replicação delta começa. As alterações incrementais em discos locais são replicadas periodicamente para os discos de réplica no Azure.


Você pode acompanhar o status do trabalho nas notificações do Portal.

![Acompanhar trabalho](./media/tutorial-migrate-vmware-agent/jobs.png)

Você pode monitorar o status de replicação clicando em **replicar servidores** nas **migrações para Azure: Migração**de servidor.
![Monitorar a replicação](./media/tutorial-migrate-vmware-agent/replicate-servers.png)

## <a name="run-a-test-migration"></a>Executar uma migração de teste


Quando a replicação delta é iniciada, você pode executar uma migração de teste para as VMs antes de executar uma migração completa para o Azure. É altamente recomendável que você faça isso pelo menos uma vez para cada computador, antes de migrá-lo.

- A execução de uma migração de teste verifica se a migração funcionará conforme o esperado, sem afetar os computadores locais, que permanecem operacionais e continuam replicando. 
- A migração de teste simula a migração criando uma VM do Azure usando dados replicados (geralmente migrando para uma VNet de não produção em sua assinatura do Azure).
- Você pode usar a VM do Azure de teste replicado para validar a migração, executar testes de aplicativo e resolver problemas antes da migração completa.

Faça uma migração de teste da seguinte maneira:


1. Em **Objetivos de migração** > **Servidores** > **Azure Migrate: Migração do Servidor**, clique em **Testar servidores migrados**.

     ![Testar servidores migrados](./media/tutorial-migrate-vmware-agent/test-migrated-servers.png)

2. Clique com o botão direito do rato na VM a testar e, em seguida, clique em **Testar migração**.

    ![Testar migração](./media/tutorial-migrate-vmware-agent/test-migrate.png)

3. Em **Testar Migração**, selecione a VNet do Azure na qual a VM do Azure vai estar localizada após a migração. Recomendamos que utilize uma VNet que não seja de produção.
4. A tarefa **Teste de migração** é iniciada. Monitorize a tarefa nas notificações do portal.
5. Após a conclusão da migração, veja a VM do Azure migrada em **Máquinas Virtuais** no portal do Azure. O nome do computador tem um sufixo **-Test**.
6. Após terminar o teste, clique com o botão direito do rato na VM do Azure em **Replicar computadores** e clique em **Limpar teste de migração**.

    ![Limpar a migração](./media/tutorial-migrate-vmware-agent/clean-up.png)


## <a name="migrate-vms"></a>Migrar VMs

Depois de verificar se a migração de teste funciona conforme o esperado, você pode migrar os computadores locais.

1. No projeto do Azure Migrate > **Servidores** > **Azure Migrate: Migração do Servidor**, clique em **Replicar servidores**.

    ![Replicar servidores](./media/tutorial-migrate-vmware-agent/replicate-servers.png)

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
-  Considere implementar o [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/overview) para monitorizar a utilização e as despesas do recurso.


## <a name="next-steps"></a>Passos Seguintes

Investigue a [jornada de migração na nuvem](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/migrate) na estrutura de adoção de nuvem do Azure.
