---
title: VMs migratórios vMware com migração de servidores de migração azure baseado seleções
description: Aprenda a executar uma migração baseada em agentes de VMware VMs com Azure Migrate.
ms.topic: tutorial
ms.date: 03/09/2020
ms.custom: MVC
ms.openlocfilehash: 6855c3e81aece0358146608b6cf179fb923c54c8
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535337"
---
# <a name="migrate-vmware-vms-to-azure-agent-based"></a>VMs migratórios para Azure (baseado em agente)

Este artigo mostra-lhe como migrar vMs no local para Azure, utilizando a migração baseada em agentes com a ferramenta migração do servidor de migração do servidor migratório Azure Migrate.


Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Instale o ambiente de origem e implante um aparelho de replicação Azure Migrate para migração baseada em agentes.
> * Criar o ambiente-alvo para a migração.
> * Configure uma política de replicação.
> * Ative a replicação.
> * Faça uma migração de teste para ter certeza de que está tudo funcionando como esperado.
> * Fazer uma migração completa para Azure.

> [!NOTE]
> Os tutoriais mostram-lhe o caminho de implantação mais simples para um cenário para que possa rapidamente configurar uma prova de conceito. Os tutoriais usam opções padrão sempre que possível, e não mostram todas as configurações e caminhos possíveis. Para obter instruções detalhadas, reveja os How-tos para avaliação e migração de VMware.

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.

## <a name="before-you-begin"></a>Antes de começar

Recomendamos que experimente a avaliação vMware VM com a Avaliação do Servidor Migratório Azure, antes de migrar VMs para O Azure. Configurar uma avaliação da seguinte forma:

1. Siga o tutorial para [preparar Azure e VMware](tutorial-prepare-vmware.md) para avaliação.
2. Em seguida, siga [este tutorial](tutorial-assess-vmware.md) para configurar um aparelho Azure Migrate para avaliação, e descubra e avalie vMs.


Embora recomendemos que experimente uma avaliação, não precisa fazer uma avaliação antes de migrar VMs.

## <a name="migration-methods"></a>Métodos de migração

Pode migrar VMware VMs para Azure utilizando a ferramenta migração do servidor de migração do servidor migratório Azure Migrate. Esta ferramenta oferece um par de opções para a migração VMware VM:

- Réplica sem agente. Migrar VMs sem precisar de instalar nada neles.
- Migração baseada em agente. ou replicação. Instale um agente (o agente de serviços de mobilidade) no VM para replicação.

Para decidir se pretende utilizar migração sem agentes ou baseada em agentes, reveja estes artigos:

- [Conheça](server-migrate-overview.md) as opções de migração VMware.
- [Compare os métodos de migração.](server-migrate-overview.md#compare-migration-methods)
- [Siga este artigo](tutorial-migrate-vmware.md) para experimentar a migração sem agente.



## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, tem de:

1. [Reveja](migrate-architecture.md) a arquitetura de migração VMware.
2. Certifique-se de que a sua conta Azure é atribuída a função de Colaborador de Máquina Virtual, para que tenha permissões para:

    - Criar uma VM no grupo de recursos selecionado.
    - Criar uma VM na rede virtual selecionada.
    - Escreva para um disco gerido pelo Azure. 

3. [Criar uma rede Azure.](../virtual-network/manage-virtual-network.md#create-a-virtual-network) As máquinas no local são replicadas para discos geridos pelo Azure. Quando falha o Azure para migração, os VMs Azure são criados a partir destes discos geridos, e juntou-se a uma rede Azure que especifica quando configura a migração.


## <a name="prepare-azure"></a>Preparar o Azure

Se já fez uma avaliação com a Avaliação do Servidor Migratório Azure, pode ignorar as instruções nesta secção, uma vez que já concluiu estes passos. 

Se ainda não fez uma avaliação, precisa de configurar permissões Azure antes de poder migrar com a Migração do Servidor Migratório Migratório Azure Migrate.

- **Crie um projeto**: A sua conta Azure precisa de permissões para criar um projeto Azure Migrate. 
- **Registe o aparelho de replicação Azure Migrate**: O aparelho de replicação cria e regista uma aplicação azure ative directory na sua conta Azure. Delegado permissões para isto.
- **Create Key Vault**: Para migrar VMware VMware utilizando a Migração do Servidor Migratório Migratório Migratório Azure Migração, a Azure Migrate cria um Cofre chave no grupo de recursos, para gerir as chaves de acesso da conta de armazenamento de replicação na sua subscrição. Para criar o cofre, você precisa de permissões de atribuição de papéis no grupo de recursos em que o projeto Azure Migrate reside. 


### <a name="assign-permissions-to-create-project"></a>Atribuir permissões para criar projeto

1. No portal Azure, abra a subscrição e selecione controlo de **acesso (IAM)**.
2. No **Check access,** encontre a conta relevante e clique nela para visualizar permissões.
3. Deve ter permissões de **Contribuinte** ou **Proprietário.**
    - Se acabou de criar uma conta Azure gratuita, é o proprietário da sua subscrição.
    - Se não for o proprietário da subscrição, trabalhe com o proprietário para atribuir o papel.

### <a name="assign-permissions-to-register-the-replication-appliance"></a>Atribuir permissões para registar o aparelho de replicação

Para a migração baseada em agentes, delege permissões para a Migração do Servidor Migratório Migratório Migratório Azure migrate para criar e registar uma aplicação Azure AD na sua conta. Pode atribuir permissões utilizando um dos seguintes métodos:

- Um inquilino/administrador global pode conceder permissões aos utilizadores do inquilino, para criar e registar aplicações da Azure AD.
- Um inquilino/administrador global pode atribuir a função de Desenvolvedor de Aplicações (que tem as permissões) à conta.

Vale a pena notar que:

- As aplicações não têm outras permissões de acesso na subscrição que não as descritas acima.
- Só precisa destas permissões quando regista um novo aparelho de replicação. Pode retirar as permissões após a instalação do aparelho de replicação. 


#### <a name="grant-account-permissions"></a>Concessão de permissões de conta

O inquilino/administrador global pode conceder permissões da seguinte forma

1. Em Azure AD, o inquilino/administrador global deve navegar para**as Definições**de**Utilizadores** > de **Diretório** > Ativo Azure .
2. O administrador deve definir **os registos** da App para **Sim**.

    ![Permissões da AD Azure](./media/tutorial-prepare-vmware/aad.png)

> [!NOTE]
> Esta é uma definição padrão que não é sensível. [Saiba mais](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).

#### <a name="assign-application-developer-role"></a>Atribuir papel de desenvolvedor de aplicações 

O inquilino/administrador global pode atribuir a função de Desenvolvedor de Aplicações a uma conta. [Saiba mais](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="assign-permissions-to-create-key-vault"></a>Atribuir permissões para criar o Cofre chave

Atribuir permissões de atribuição de funções no grupo de recursos em que reside o projeto Azure Migrate:

1. No grupo de recursos no portal Azure, selecione controlo de **acesso (IAM)**.
2. No **Check access,** encontre a conta relevante e clique nela para visualizar permissões. Precisa de permissões de **Proprietário** (ou **Colaborador** e Administrador de **Acesso ao Utilizador).**
3. Se não tiver as permissões necessárias, solicite-as ao proprietário do grupo de recursos. 


## <a name="prepare-on-premises-vmware"></a>Preparar o VMware no local

### <a name="prepare-an-account-for-automatic-discovery"></a>Preparar uma conta para a deteção automática

A migração do servidor migratório Azure necessita de acesso aos servidores VMware para:

- Detetar automaticamente VMs. É necessária, pelo menos, uma conta só de leitura.
- Orquestrar a replicação, a ativação pós-falha e a reativação pós-falha. Precisa de uma conta com capacidade para executar operações como criar e remover discos, e ligar VMs.

Crie a conta da seguinte forma:

1. Para utilizar uma conta dedicada, crie uma função ao nível do vCenter. Dê um nome à função, como **Azure_Site_Recovery**.
2. Atribua à função as permissões resumidas na tabela abaixo.
3. Crie um utilizador no servidor vCenter ou no anfitrião vSphere. Atribua a função ao utilizador.

#### <a name="vmware-account-permissions"></a>Permissões de conta de VMware

**Tarefa** | **Função/Permissões** | **Detalhes**
--- | --- | ---
**Deteção de VMs** | Pelo menos um utilizador só de leitura<br/><br/> Objeto Data Center –> Propagar ao Objeto Subordinado, função=Só de Leitura | Utilizador atribuído ao nível do datacenter, com acesso a todos os objetos no datacenter.<br/><br/> Para restringir o acesso, atribuir a função **De acesso com** o Propagate ao objeto **infantil,** aos objetos infantis (hospedeiros vSphere, lojas de dados, VMs e redes).
**Replicação, ativação pós-falha e reativação pós-falha completas** |  Criar uma função (Azure_Site_Recovery) com as permissões necessárias e, em seguida, atribuir a função a um grupo ou utilizador de VMware<br/><br/> Objeto Data Center –> Propagar ao Objeto Subordinado, função=Azure_Site_Recovery<br/><br/> Arquivo de Dados -> Alocar espaço, navegar no arquivo de dados, operações de ficheiro de baixo nível, remover ficheiros, atualizar ficheiros de máquinas virtuais<br/><br/> Rede -> Atribuição de rede<br/><br/> Recursos -> Atribuir VM a agrupamento de recursos, migrar VMs desligadas, migrar VMs ligadas<br/><br/> Tarefas -> Criar tarefa, atualizar tarefa<br/><br/> Máquina virtual -> Configuração<br/><br/> Máquina virtual -> Interagir -> responder a perguntas, ligação de dispositivos, configurar suportes de dados em CD, configurar suportes de dados em disquete, desligar, ligar, instalação de ferramentas de VMware<br/><br/> Máquina virtual -> Inventário -> Criar, registar, anular o registo<br/><br/> Máquina virtual -> Aprovisionamento -> Permitir transferência de máquinas virtuais, permitir carregamento de ficheiros de máquinas virtuais<br/><br/> Máquina virtual -> Instantâneos -> Remover instantâneos | Utilizador atribuído ao nível do datacenter, com acesso a todos os objetos no datacenter.<br/><br/> Para restringir o acesso, atribuir a função **De acesso com** o **Propagate a objetos infantis,** aos objetos infantis (vSphere hosts, datastores, VMsa, nd networks).

### <a name="prepare-an-account-for-mobility-service-installation"></a>Preparar uma conta para a instalação do serviço de Mobilidade

O Serviço de mobilidade tem de ser instalado em cada computador que queira replicar.

- O aparelho de replicação Azure Migrate pode fazer uma instalação de pressão deste serviço quando ativa a replicação de uma máquina, ou pode instalá-lo manualmente, ou utilizando ferramentas de instalação.
- Neste tutorial, vamos instalar o Serviço de mobilidade com a instalação push.
- Para a instalação push, você precisa preparar uma conta que a Migração do Servidor Migratório Migratório Azure pode usar para aceder ao VM. Esta conta é utilizada apenas para a instalação push, se não instalar manualmente o serviço mobility.

Prepare a conta da seguinte forma:

1. Prepare um domínio ou uma conta local com permissões para instalar na VM.
2. Para os VMs do Windows, se não estiver a utilizar uma conta de domínio, desative o controlo de acesso remoto ao utilizador na máquina local, adicionando a entrada do DWORD **LocalAccountTokenFilterPolicy,** com um valor no registo, em **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**
3. Para os VMs Linux, prepare uma conta raiz no servidor linux fonte.


### <a name="check-vmware-requirements"></a>Verificar os requisitos de VMware

Certifique-se de que os servidores vMware e VMs cumprem os requisitos de migração para o Azure. 


> [!NOTE]
> A migração baseada em agentes com a Migração do Servidor Migratório Migratório Migratório Azure migrate baseia-se em funcionalidades do serviço de recuperação do site Azure. Alguns requisitos podem ligar-se à documentação de Recuperação do Site.

1. [Verifique](migrate-support-matrix-vmware-migration.md#agent-based-vmware-servers) os requisitos do servidor do VMware.
2. [Verificar](migrate-support-matrix-vmware-migration.md#agent-based-vmware-vms) Requisitos de apoio vm para a migração.
3. Verifique as definições de VM. As VMs no local que replica para o Azure devem cumprir os requisitos da [Azure VM](migrate-support-matrix-vmware-migration.md#azure-vm-requirements).



## <a name="add-the-azure-migrate-server-migration-tool"></a>Adicione a ferramenta de migração do servidor migratório Azure Migrate

Se não seguiu o tutorial para avaliar vMware VMs, criou um projeto Azure Migrate e, em seguida, adicione a ferramenta de migração do servidor migratório Azure:

1. No portal do Azure > **Todos os serviços**, procure **Azure Migrate**.
2. Em **Serviços**, selecione **Azure Migrate**.

    ![Criar o Azure Migrate](./media/tutorial-migrate-vmware-agent/azure-migrate-search.png)

3. Em **Descrição geral**, clique em **Avaliar e migrar servidores**.
4. Em **Discover, avalie e emigra os servidores,** clique em **avaliar e migrar servidores**.

    ! [Descubra e avalie servidores] (./media/tutorial-migrate-vmware-agent/assess-migrate.png

1. Em **Detetar, avaliar e migrar servidores**, clique em **Adicionar ferramentas**.
2. Em **Migrar projeto**, selecione a sua subscrição do Azure e crie um grupo de recursos, caso não tenha um.
3. Em **Detalhes do Projeto**, especifique o nome do projeto e a geografia na qual pretende criar o projeto e clique em **Seguinte**. Rever geografias apoiadas para nuvens [públicas](migrate-support-matrix.md#supported-geographies-public-cloud) e [governamentais.](migrate-support-matrix.md#supported-geographies-azure-government)

    ![Criar um projeto Azure Migrate](./media/tutorial-migrate-vmware-agent/migrate-project.png)


4. Na ferramenta de **avaliação Select,** selecione **Skip adicionando uma ferramenta de avaliação para já** > **Seguinte**.
5. Na **ferramenta de migração Select,** selecione **Azure Migrate: Server Migration** > **Next**.
6. Em **Analisar + adicionar ferramentas**, analise as definições e clique em **Adicionar ferramentas**
7. Depois de adicionar a ferramenta, aparece no projeto Azure Migrate > ferramentas de**migração**de **servidores.** > 

## <a name="set-up-the-replication-appliance"></a>Configurar o aparelho de replicação

O primeiro passo da migração é a instalação do aparelho de replicação. O aparelho de replicação é um VMware VMware único, altamente disponível, no local que acolhe estes componentes:

- Servidor de **configuração**: O servidor de configuração coordena as comunicações entre as instalações e o Azure e gere a replicação de dados.
- **Servidor de processos**: O servidor de processo funciona como um portal de replicação. Recebe dados de replicação; otimiza-o com cache, compressão e encriptação, e envia-o para uma conta de armazenamento em cache em Azure. O servidor de processo saque também o agente do Serviço de Mobilidade em VMs que pretende replicar e realiza a descoberta automática de VMware vMware no local.


Pode configurar o aparelho de replicação de várias maneiras.

- Configurar com um modelo de aplicação de virtualização aberta (OVA) descarregado. Importa-se o modelo em VMware e cria-se o vM do aparelho de replicação. Este é o método usado neste tutorial.
- Prepara-te com um guião.

### <a name="download-the-replication-appliance-template"></a>Descarregue o modelo do aparelho de replicação

Descarregue o modelo da seguinte forma:

1. No projeto Azure Migrate clique em **Servidores** sob **objetivos de migração**.
2. In **Azure Migrate - Servers** > **Azure Migrate: Servers Migração,** clique em **Descobrir**.

    ![Detetar VMs](./media/tutorial-migrate-vmware-agent/migrate-discover.png)

3. Em **Discover machines** > **As suas máquinas estão virtualizadas?** **Yes, with VMWare vSphere hypervisor**
4. Em **Como pretende migrar?** **Using agent-based replication**
5. Na **região Target,** selecione a região de Azure para a qual pretende migrar as máquinas.
6. Selecione **Confirmar que a região alvo para a migração é o nome da região**.
7. Clique em **Criar recursos.** Isto cria um cofre de recuperação de sítios Azure em segundo plano.
    - Não pode alterar a região alvo deste projeto depois de clicar neste botão.
    - Todas as migrações subsequentes são para esta região.

    ![Criar cofre dos Serviços de Recuperação](./media/tutorial-migrate-vmware-agent/create-resources.png)

8. Em Se pretender instalar um novo aparelho de **replicação,** selecione **Instale um aparelho**de replicação .
9. Clique em **Baixar,** para descarregar o aparelho de replicação. Isto descarrega um modelo OVF que utiliza para criar um novo VMware VMware VM que executa o aparelho.
    ![Baixar OVA](./media/tutorial-migrate-vmware-agent/download-ova.png)
10. Reparem no nome do grupo de recursos e no cofre dos Serviços de Recuperação. Precisa destes durante a colocação do aparelho.


### <a name="import-the-template-in-vmware"></a>Importar o modelo no VMware

Depois de descarregar o modelo OVF, importa-o em VMware para criar a aplicação de replicação num VMware VM running Windows Server 2016.

1. Inicie sessão no servidor VMware vCenter ou no anfitrião vSphere ESXi, com o VMWare vSphere Client.
2. No menu **'Ficheiro',** selecione Implementar o **modelo OVF** para iniciar o assistente de **modelo OVF de implantação**. 
3. Na **fonte Select,** introduza a localização do OVF descarregado.
4. Em **detalhes de Revisão,** selecione **Next**.
5. No **Select name and folder** e **selecione a configuração,** aceite as definições predefinidas.
6. Em >  **armazenamento Selecione****o formato de disco virtual ,** para melhor desempenho selecione Thick Provision Eager **Zeroed**.
7. Nas restantes páginas do assistente, aceite as predefinições.
8. **Em pronto para completar**, para configurar o VM com as definições predefinidas, selecione A **alimentação após a colocação** > de acabamento **.**

   > [!TIP]
   > Se quiser adicionar um NIC adicional, limpe **a energia após a implantação** > **Acabamento**. Por predefinição, o modelo contém uma NIC única. Pode adicionar mais NICs após a implementação.

### <a name="kick-off-replication-appliance-setup"></a>Iniciar a configuração do aparelho de replicação

1. A partir da consola do VMWare vSphere Client, ligue a VM.
2. A VM arranca para uma experiência de instalação do Windows Server 2016. Aceite o contrato de licença e introduza uma palavra-passe de administrador.
3. Depois de a instalação terminar, inscreva-se no VM como administrador, utilizando a senha de administração.
4. A primeira vez que inicia a sua inscrição, a ferramenta de configuração do aparelho de replicação (Ferramenta de Configuração de Recuperação do Local Azure) começa em poucos segundos.
5. Introduza um nome a utilizar para registar o aparelho com a Migração do Servidor Migratório Migratório Migratório Migratório Migratório Azure Migrate. Em seguida, clique em **Seguinte**.
6. A ferramenta verifica se a VM pode ligar ao Azure. Depois de a ligação estar estabelecida, selecione **Iniciar sessão** para iniciar sessão na sua subscrição do Azure.
7. Aguarde que a ferramenta termine de registar uma aplicação Azure AD para identificar o aparelho. O aparelho reinicia.
1. Inicie sessão na máquina novamente. Em poucos segundos, o Assistente de Gestão do Servidor de Configuração inicia automaticamente.

### <a name="register-the-replication-appliance"></a>Registe o aparelho de replicação

Termine de configurar e registar o aparelho de replicação.

1. No Assistente de Gestão do Servidor de Configuração, **selecione conectividade configuração**.
2. Selecione o NIC (por predefinição, há apenas um NIC) que o aparelho de replicação utiliza para a descoberta de VM e para fazer uma instalação push do serviço mobility em máquinas de origem.
3. Selecione o NIC que o aparelho de replicação utiliza para a conectividade com o Azure. Em seguida, selecione **Guardar**. Não é possível alterar esta definição depois de configurada.
4. Se o aparelho estiver localizado atrás de um servidor proxy, tem de especificar as definições de procuração.
    - Especifique **http://ip-address**o **http://FQDN**nome proxy como, ou . Os servidores proxy HTTPS não são suportados.
5. Quando solicitado para a subscrição, grupos de recursos e detalhes do cofre, adicione os detalhes que observou quando descarregou o modelo do aparelho.
6. Em **Instalar software de terceiros**, aceite o contrato de licença. Selecione **Transferir e Instalar** para instalar o Servidor MySQL.
7. Selecione **Instalar VMware PowerCLI**. Certifique-se de que todas as janelas do browser estão fechadas antes de efetuar este procedimento. Em seguida, selecione **Continuar**.
8. Em **Validar configuração da aplicação**, os pré-requisitos são verificados antes de continuar.
9. Em **Configurar servidor vCenter Server/vSphere ESXi**, introduza o FQDN ou endereço IP do servidor vCenter ou anfitrião vSphere onde estão localizadas as VMs que pretende replicar. Introduza a porta em que o servidor está a escutar. Introduza um nome amigável a utilizar para o servidor VMware no cofre.
10. Introduza as credenciais para a conta que [criou](#prepare-an-account-for-automatic-discovery) para a descoberta da VMware. **Selecione Adicionar** > **Continuar**.
11. Na Configuração das credenciais de **máquinavirtual,** introduza as credenciais [criadas](#prepare-an-account-for-mobility-service-installation) para a instalação push do serviço Mobility, quando ativa a replicação para VMs.  
    - Para computadores Windows, a conta precisa de privilégios de administrador local nos computadores que pretende replicar.
    - Para o Linux, forneça detalhes para a conta raiz.
12. Selecione **Finalizar configuração** para concluir o registo.


Após a matrícula do aparelho de replicação, a Avaliação do Servidor Migrador Azure liga-se aos servidores VMware utilizando as definições especificadas e descobre VMs. Pode ver VMs descobertos em **itens Manage** > **Discovered,** no **separador Outros.**


## <a name="replicate-vms"></a>Replicar VMs

Agora, selecione VMs para migração.

> [!NOTE]
> Podes replicar até 10 máquinas juntas. Se precisar de replicar mais, reproduza-os simultaneamente em lotes de 10.

1. No projeto Azure Migrate > **Servers,** **Azure Migrate: Server Migration**, clique em **Replicate**.

    ![Replicar VMs](./media/tutorial-migrate-vmware-agent/select-replicate.png)

2. Em **Replicar**, **Definições de origem** > **Os computadores estão virtualizados?**, selecione **Sim, com o VMware vSphere**.
3. No **aparelho On-local,** selecione o nome do aparelho Azure Migrate que instalou.
4. No **servidor vCenter,** especifique o nome do servidor vCenter que gere os VMs ou o servidor vSphere no qual os VMs estão hospedados.
5. No Servidor de **Processos,** selecione o nome do aparelho de replicação.
6. Nas **credenciais de hóspedes,** especifique a conta de administração VM que será utilizada para a instalação de impulsos do serviço mobility. Em seguida, clique em **Seguinte: Máquinas virtuais**.

    ![Replicar VMs](./media/tutorial-migrate-vmware-agent/source-settings.png)

7. Em **Máquinas Virtuais,** selecione as máquinas que pretende replicar.

    - Se tiver executado uma avaliação para as VMs, poderá aplicar as recomendações de dimensionamento de VMs e tipo de discos (premium/standard) nos resultados da avaliação. Para tal, em **Importar definições de migração de uma avaliação do Azure Migrate?**, selecione a opção **Sim**.
    - Se não tiver executado uma avaliação ou não quiser utilizar as definições de avaliação, selecione as opções **Não**.
    - Se tiver selecionado para utilizar a avaliação, selecione o grupo de VMs e o nome da avaliação.

8. Verifique cada VM que queira migrar. Em seguida, clique em **Seguinte: Definições de destino**.
9. Em **Definições de destino**, selecione a subscrição, assim como a região de destino para a qual vai migrar, e especifique o grupo de recursos no qual as VMs do Azure vão residir após a migração.
10. Em **Rede Virtual**, selecione a VNet/sub-rede do Azure na qual as VMs do Azure vão ser associadas após a migração.
11. Em **Benefício Híbrido do Azure**:

    - Selecione **Não** se não quiser aplicar o Benefício Híbrido do Azure. Em seguida, clique em **Seguinte**.
    - Selecione **Sim** se tiver computadores Windows Server abrangidos com subscrições ativas do Software Assurance ou do Windows Server e quiser aplicar o benefício aos computadores que está a migrar. Em seguida, clique em **Seguinte**.

12. Em **Computação**, analise o nome, o tamanho, o tipo de disco do SO e o conjunto de disponibilidade das VMs. As VMs devem estar em conformidade com os [requisitos do Azure](migrate-support-matrix-vmware-migration.md#agent-based-vmware-vms).

    - **Tamanho VM**: Se estiver a utilizar recomendações de avaliação, a queda do tamanho do VM conterá o tamanho recomendado. Caso contrário, o Azure Migrate escolhe um tamanho com base na correspondência mais próxima na subscrição do Azure. Como alternativa, escolha um tamanho manual em **Tamanho de VMs do Azure**. 
    - **Disco OS:** Especifique o disco OS (boot) para o VM. O disco do SO é o disco que possui o carregador e o instalador do sistema operativo. 
    - **Conjunto de disponibilidade**: Se o VM estiver num conjunto de disponibilidade azure após a migração, especifique o conjunto. O conjunto deve estar no grupo de recursos de destino especificado para a migração.

13. Nos **Discos,** especifique se os discos VM devem ser replicados para O Azure e selecione o tipo de disco (SSD/HDD padrão ou discos geridos premium) em Azure. Em seguida, clique em **Seguinte**.
    - Pode excluir discos da replicação.
    - Se excluir discos, estes não estarão presentes na VM do Azure após a migração. 

14. Em **Analisar e iniciar a replicação**, analise as definições e clique em **Replicar** para iniciar a replicação inicial para os servidores.

> [!NOTE]
> Pode atualizar as definições de replicação a qualquer momento antes do início da replicação, **gerir** > **as máquinas de replicação**. As definições não podem ser alteradas após o início da replicação.




## <a name="track-and-monitor"></a>Faixa e monitor

- Quando clicar em **Replicar** começa um trabalho de replicação de arranque. 
- Quando o trabalho de Replicação Inicial termina com sucesso, as máquinas iniciam a sua replicação inicial para Azure.
- Após os acabamentos iniciais da replicação, começa a replicação delta. As alterações incrementais nos discos no local são periodicamente replicadas para os discos de réplica em Azure.


Pode rastrear o estado do emprego nas notificações do portal.

![Track job](./media/tutorial-migrate-vmware-agent/jobs.png)

Pode monitorizar o estado de replicação clicando em **servidores de replicação** em **Azure Migrate: Server Migration**.
![Monitorizar a replicação](./media/tutorial-migrate-vmware-agent/replicate-servers.png)

## <a name="run-a-test-migration"></a>Executar uma migração de teste


Quando a replicação delta começar, você pode executar uma migração de teste para os VMs, antes de executar uma migração completa para Azure. Recomendamos vivamente que faça isto pelo menos uma vez por cada máquina, antes de a migrar.

- A realização de um teste de migração verifica que a migração funcionará como esperado, sem afetar as máquinas no local, que permanecem operacionais, e continuam a replicar-se. 
- A migração de teste simula a migração criando um VM Azure usando dados replicados (geralmente migrando para um VNet não-produção na sua assinatura Azure).
- Pode utilizar o teste replicado Azure VM para validar a migração, realizar testes de aplicações e resolver quaisquer problemas antes da migração total.

Faça uma migração de teste da seguinte forma:


1. Nos **objetivos** > de migração**Servidores** > **Azure Migram: Migração do Servidor,** clique em **servidores migrados de teste**.

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

1. No projeto Azure Migrate > **Servers** > **Azure Migrate: Server Migration**, clique em **servidores de replicação**.

    ![Replicar servidores](./media/tutorial-migrate-vmware-agent/replicate-servers.png)

2. Em **Replicar computadores**, clique com o botão direito do rato na VM > **Migrar**.
3. Em **Migrate** > **Desligue as máquinas virtuais e realize uma migração planeada sem perda de dados**, selecione **Yes** > **OK**.
    - Por padrão, a Azure Migrate encerra o VM no local para garantir a perda mínima de dados. 
    - Se não quiser desligar a VM, selecione **Não**
4. Uma tarefa de migração é iniciada para a VM. Controle a tarefa nas notificações do Azure.
5. Após a conclusão da tarefa, pode ver e gerir a VM na página **Máquinas Virtuais**.

## <a name="complete-the-migration"></a>Complete a migração

1. Depois da migração ser feita, clique à direita no VM > Parar a **migração**. Isto faz o seguinte:
    - Para a replicação para a máquina no local.
    - Remove a máquina da contagem de **servidores de replicação** em Migração Azure: Migração do Servidor.
    - Limpa informações do estado de replicação para o VM.
2. Instale o Azure VM [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) ou o agente [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) nas máquinas migradas.
3. Execute otimizações de aplicação pós-migração, tais como atualizar cadeias de ligação de base de dados e configurações de servidor Web.
4. Execute testes de aplicação final e de aceitação da migração na aplicação migrada em execução no Azure.
5. Corte o tráfego para a instância azure vm migrada.
6. Remova as VMs no local do seu inventário de VMs locais.
7. Remova as VMs no local das cópias de segurança locais.
8. Atualize qualquer documentação interna para mostrar a nova localização e endereço IP das VMs do Azure. 

## <a name="post-migration-best-practices"></a>Boas práticas pós-migração

- Local
    - Mova o tráfego da aplicação para a aplicação em execução na instância da VM do Azure migrada.
    - Remova as VMs no local do seu inventário de VMs locais.
    - Remova as VMs no local das cópias de segurança locais.
    - Atualize qualquer documentação interna para mostrar a nova localização e endereço IP das VMs do Azure.
- Ajuste as definições de VM Azure após a migração:
    - O [agente de VM do Azure](../virtual-machines/extensions/agent-windows.md) gere a interação da VM com o Controlador de Recursos de Infraestrutura do Azure. É necessário para alguns serviços do Azure, como o Azure Backup, o Site Recovery e a Segurança do Azure. Ao migrar VMare VMs com migração baseada em agentes, o instalador do Serviço de Mobilidade instala o agente Azure VM nas máquinas Windows. Nos VMs Linux, recomendamos que instale o agente após a migração.
    - Desinstale manualmente o serviço de Mobilidade a partir do VM Azure após a migração.
    - Desinstale manualmente as ferramentas VMware após a migração.
- Em Azure:
    - Execute otimizações de aplicação pós-migração, tais como atualizar cadeias de ligação de base de dados e configurações de servidor Web.
    - Execute testes de aplicação final e de aceitação da migração na aplicação migrada em execução no Azure.
- Continuidade do negócio/recuperação de desastres
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
