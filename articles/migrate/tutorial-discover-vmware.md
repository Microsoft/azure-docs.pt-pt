---
title: Descubra VMware VMs com avaliação do servidor Azure Migrate
description: Saiba como descobrir VMware VMs no local com a ferramenta de avaliação do servidor Azure Migrate
ms.topic: tutorial
ms.date: 09/14/2020
ms.custom: mvc
ms.openlocfilehash: 55a7f15233d32f5ee60a57ad34e8b0177ca29ff3
ms.sourcegitcommit: 51df05f27adb8f3ce67ad11d75cb0ee0b016dc5d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90064455"
---
# <a name="tutorial-discover-vmware-vms-with-server-assessment"></a>Tutorial: Descubra VMware VMs com avaliação do servidor

Como parte da sua viagem de migração para Azure, você descobre o seu inventário no local e cargas de trabalho. 

Este tutorial mostra-lhe como descobrir no local máquinas virtuais VMware (VMs) com a ferramenta Azure Migrate: Server Assessment, utilizando um aparelho Azure Migrate leve. Implementa o aparelho como VMware VM, para descobrir continuamente metadados de VM e desempenho, aplicações em execução em VMs e dependências de VM.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Crie uma conta Azure.
> * Prepare o ambiente VMware para ser descoberto.
> * Criar um projeto do Azure Migrate.
> * Instale o aparelho Azure Migrate.
> * Comece a descoberta contínua.

> [!NOTE]
> Os tutoriais mostram o caminho mais rápido para experimentar um cenário e usam opções padrão sempre que possível.  

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.


## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este tutorial, verifique se tem estes pré-requisitos no lugar.


**Requisito** | **Detalhes**
--- | ---
**vCenter Server/ESXi anfitrião** | Precisa de um servidor vCenter que executa a versão 5.5, 6.0, 6.5 ou 6.7.<br/><br/> Os VMs devem ser hospedados numa versão de execução de anfitrião ESXi 5.5 ou posterior.<br/><br/> No vCenter Server, permita ligações de entrada na porta TCP 443, para que o aparelho possa recolher dados de avaliação.<br/><br/> O aparelho liga-se ao vCenter na porta 443 por defeito. Se o servidor vCenter ouvir numa porta diferente, pode modificar a porta quando ligar do aparelho ao servidor para iniciar a descoberta.<br/><br/> No servidor EXSi que acolhe os VMs, certifique-se de que o acesso à entrada é permitido na porta TCP 443, para a descoberta de aplicações.
**Aparelho** | vCenter Server precisa de recursos para alocar um VM para o aparelho Azure Migrate:<br/><br/> - Windows Server 2016<br/><br/> - 32 GB de RAM, oito vCPUs e cerca de 80 GB de armazenamento de disco.<br/><br/> - Um interruptor virtual externo e acesso à Internet para o VM, diretamente ou através de um representante.
**VMs** | Para utilizar este tutorial, os VMs do Windows devem estar a executar o Windows Server 2016, 2012 R2, 2012 ou 2008 R2.<br/><br/> Os VMs linux devem estar a executar Red Hat Enterprise Linux 7/6/5, Ubuntu Linux 14.04/16.04, Debian 7/8, Oracle Linux 6/7 ou CentOS 5/6/7.<br/><br/> Os VMs precisam de ferramentas VMware (uma versão posterior a 10.2.0) instaladas e em funcionamento.<br/><br/> Nos VMs do Windows, o Windows PowerShell 2.0 ou mais tarde deve ser instalado.


## <a name="prepare-an-azure-user-account"></a>Preparar uma conta de utilizador Azure

Para criar um projeto Azure Migrate e registar o aparelho Azure Migrate, precisa de uma conta com:
- Permissões de colaborador ou proprietário numa subscrição do Azure.
- Permissões para registar aplicações do Azure Ative Directory.

Se acabou de criar uma conta Azure gratuita, é o proprietário da sua subscrição. Se não for o proprietário da subscrição, trabalhe com o proprietário para atribuir as permissões da seguinte forma:

1. No portal Azure, procure por "subscrições", e em **Serviços,** **selecione Subscrições**.

    ![Caixa de pesquisa para procurar a subscrição do Azure](./media/tutorial-discover-vmware/search-subscription.png)

2. Na página **Subscrições,** selecione a subscrição na qual pretende criar um projeto Azure Migrate. 
3. Na subscrição, selecione **Access Control (IAM)**  >  **Verifique o acesso**.
4. No **Acesso ao Cheques,** procure na conta de utilizador relevante.
5. In **Add a role assignment**, clique em **Adicionar**.

    ![Procure uma conta de utilizador para verificar o acesso e atribuir uma função](./media/tutorial-discover-vmware/azure-account-access.png)

6. Na **atribuição de funções Adicionar**, selecione a função Contribuinte ou Proprietário e selecione a conta (azmigrateuser no nosso exemplo). Em seguida, clique em **Guardar**.

    ![Abre a página de atribuição de Função Adicionar para atribuir uma função à conta](./media/tutorial-discover-vmware/assign-role.png)

7. No portal, procure utilizadores e em **Serviços,** selecione **Utilizadores.**
8. Nas **definições do Utilizador,** verifique se os utilizadores de Ad Azure podem registar aplicações (definidas para **Sim** por predefinição).

    ![Verifique nas Definições do Utilizador que os utilizadores podem registar aplicações de Ative Directory](./media/tutorial-discover-vmware/register-apps.png)


## <a name="prepare-vmware"></a>Preparar VMware

No vCenter Server, crie uma conta que o aparelho possa utilizar para aceder ao servidor vCenter e verifique se as portas necessárias estão abertas. Também precisa de uma conta que o aparelho pode usar para aceder a VMs. 

### <a name="create-an-account-to-access-vcenter"></a>Criar uma conta para aceder ao vCenter

No vSphere Web Client, crie uma conta da seguinte forma:

1. Utilizando uma conta com privilégios de administração, no vSphere Web Client > selecione **Administração**.
2. **Aceda**, selecione **SSO Users and Groups**.
3. Nos **Utilizadores,** adicione um novo utilizador.
4. Em **Novo Utilizador,** digite os detalhes da conta. Em seguida, clique em **OK**.
5. Em **Permissões Globais,** selecione a conta de utilizador e atribua a **função Read-only** à conta. Em seguida, clique em **OK**.
6. Em **Papéis** > selecione o papel **apenas para ler,** e em **Privileges**, selecione **Guest Operations**. Estes privilégios são necessários para descobrir aplicações em execução em VMs e para analisar as dependências de VM.
 
    ![Caixa de verificação para permitir operações de hóspedes no papel apenas de leitura](./media/tutorial-discover-vmware/guest-operations.png)


### <a name="create-an-account-to-access-vms"></a>Criar uma conta para aceder a VMs

O aparelho acede aos VMs para descobrir aplicações e analisar as dependências de VM. O aparelho não instala agentes em VMs.

1. Crie uma conta Dedmin Local que o aparelho pode usar para descobrir aplicações e dependências em VMs windows.
2. Para as máquinas Linux, crie uma conta de utilizador com privilégios Root, ou alternadamente, uma conta de utilizador com estas permissões em ficheiros /bin/netstat e /bin/ls: CAP_DAC_READ_SEARCH e CAP_SYS_PTRACE.

> [!NOTE]
> O Azure Migrate suporta uma credencial para a descoberta de aplicações em todos os servidores do Windows, e uma credencial para a descoberta de apps em todas as máquinas Linux.


## <a name="set-up-a-project"></a>Criar um projeto

Crie um novo projeto Azure Migrate.

1. No portal do Azure > **Todos os serviços**, procure **Azure Migrate**.
2. Em **Serviços**, selecione **Azure Migrate**.
3. Em **Visão Geral**, selecione **Criar projeto**.
5. No **projeto Create,** selecione o seu grupo de subscrição e recursos Azure. Crie um grupo de recursos se não tiver um.
6. Em **Detalhes do Projeto,** especifique o nome do projeto e a geografia em que pretende criar o projeto. Reveja geografias apoiadas para nuvens [públicas](migrate-support-matrix.md#supported-geographies-public-cloud) e [governamentais.](migrate-support-matrix.md#supported-geographies-azure-government)

   ![Caixas para nome e região do projeto](./media/tutorial-discover-vmware/new-project.png)

7. Selecione **Criar**.
8. Aguarde alguns minutos para que o projeto do Azure Migrate seja implementado.

A ferramenta **Azure Migrate: Server Assessment** é adicionada por defeito ao novo projeto.

![Página mostrando a ferramenta de avaliação do servidor adicionada por padrão](./media/tutorial-discover-vmware/added-tool.png)


## <a name="set-up-the-appliance"></a>Configurar o aparelho

Este tutorial configura o aparelho num VMware VMware VM.
- Descarregue o modelo do aparelho e importe-o no vCenter Server para criar o VM do aparelho.
- Depois de criar o aparelho, instale-o pela primeira vez e registe-o com o projeto Azure Migrate.

> [!NOTE]
> Se, por alguma razão, não conseguir configurar o aparelho utilizando o gabarito, pode 15 000% pode use-lo com um script PowerShell. [Saiba mais](deploy-appliance-script.md#set-up-the-appliance-for-vmware).


### <a name="download-the-ova-template"></a>Descarregue o modelo OVA

1. In **Migration Goals**  >  **Servers**  >  **Azure Migrate: Server Assessment**, select **Discover**.
2. In **Discover machines**  >  **Are your machines virtualized?** **Yes, with VMWare vSphere hypervisor**
3. Selecione **Baixar** para descarregar o ficheiro do modelo OVA.

   ![Seleção para descarregar um ficheiro OVA](./media/tutorial-discover-vmware/download-ova.png)


### <a name="deploy-the-appliance-vm"></a>Desdobre o aparelho VM

Importe o ficheiro descarregado e crie um VM:

1. Inscreva-se na consola vSphere Client, utilizando a conta vCenter que criou.
2. No separador **Máquinas Virtuais** > menu **Ações,** selecione **implementar o modelo OVF**, para abrir o assistente de modelo de OVF de implementação.
3. Em **Selecione um modelo OVF,** selecione **o ficheiro local**e navegue no modelo de descarregamento. 
   ![vSphere menu comando para implantar um modelo OVF](./media/tutorial-discover-vmware/deploy-ovf.png)
3. Especificar o resto do assistente de definições de assistente, inclui a localização de implantação, o anfitrião/cluster no qual o VM será executado e as definições de armazenamento/rede.
4. Pronto **para completar,** clique **em Terminar**. O sistema importa e implementa o modelo. 
5. Após a colocação, o VM do aparelho aparece no separador **Máquinas Virtuais.**
6. Selecione o VM > **Ligar a Alimentação**.
7. Nos **termos**de licença de > consola remota VMware, rever/aceitar os termos da licença.
8. Nas **definições de Personalização,** crie uma palavra-passe para a conta de utilizador.


### <a name="verify-appliance-access-to-azure"></a>Verifique o acesso do aparelho ao Azure

Verifique o acesso do aparelho VM.

1. Verifique se o VM pode ligar-se ao Azure.
    - Na nuvem pública, a máquina do aparelho deve ser capaz de ligar a estes [URLs](migrate-appliance.md#public-cloud-urls).
    - Na nuvem governamental, o VM deve ser capaz de ligar a estes [URLs governamentais.](migrate-appliance.md#government-cloud-urls)
2. Certifique-se de que estas portas estão abertas na máquina do aparelho:

    - Permitir ligações de entrada na porta TCP 3389, para permitir ligações remotas de ambiente de trabalho ao aparelho.
    - Permitir que as ligações de entrada na porta 44368 acedam remotamente à aplicação web do aparelho utilizando o URL: https://<appliance-ip-or-name>:44368.
    - Permitir que as ligações de saída na porta 443 (HTTPS) enviem metadados de descoberta e desempenho para Azure Migrate.

### <a name="configure-the-appliance"></a>Configure o aparelho

Coloque o aparelho pela primeira vez.

1. Inscreva-se no aparelho VM. 
    - A aplicação web do aparelho abre automaticamente num browser.
    - Em alternativa, pode abrir a aplicação a partir do ambiente de trabalho do aparelho com o atalho da aplicação.
2. Na aplicação web Azure Migrate Appliance > **Configurar pré-requisitos,** rever/aceitar os termos da licença e ler as informações de terceiros.
3. O aparelho verifica se o VM tem acesso à Internet e que o tempo no VM está sincronizado com o tempo de internet.
    - Se estiver a utilizar um representante, clique em **Configurar procuração** e especificar o endereço de procuração e a porta (no formato http://ProxyIPAddress ou http://ProxyFQDN) . 
    - Especifique credenciais se o representante necessitar de autenticação. Apenas é suportado o proxy HTTP.
4. O aparelho instala as atualizações mais recentes do Azure Migrate e verifica se o Kit de Desenvolvimento de Discos Virtuais VMWare vSphere (VDDK) está instalado.
5. Para instalar o VDDK 6.7, clique em **Baixar** para o descarregar a partir de VMware e extrair o conteúdo de ficheiro zip descarregado para o local especificado no aparelho. Em seguida, clique em **Verificar e instalar**.

    ![Pré-requisitos da página para verificar o acesso à Internet e as definições para a implantação do aparelho](./media/tutorial-discover-vmware/prerequisites.png)
  
3. Depois da instalação do VDDK, reveja as definições e clique em **Continuar**.

### <a name="register-the-appliance"></a>Registar o aparelho 

1. In **Register with Azure Migrate**, selecione **Login**. Se não aparecer, certifique-se de ter desativado o bloqueador pop-up no navegador.

    ![Clique em Iniciar sessão para começar a registar o aparelho](./media/tutorial-discover-vmware/register.png)

1. Na página **Iniciar súmis,** inscreva-se com o seu nome de utilizador E password Azure. O s-in com um PIN não é suportado.

    ![Inscreva-se no botão para registar o aparelho](./media/tutorial-discover-vmware/sign-in.png)
1. Depois de iniciar sins com sucesso, volte para a aplicação.
1. No **Registo com Azure Migrate,** selecione a subscrição em que o projeto Azure Migrate foi criado e, em seguida, selecione o projeto.
1. Especifique um nome para o aparelho. O nome deve ser alfanumérico com 14 caracteres ou menos.
3. Selecione **Registar**. Em seguida, clique **em Continuar**. Uma mensagem mostra o registo como um sucesso.

    ![Preencha o nome de subscrição, projeto e aparelho e registe o aparelho](./media/tutorial-discover-vmware/success-register.png)

## <a name="start-continuous-discovery"></a>Iniciar a descoberta contínua

O aparelho precisa de se ligar ao servidor vCenter para descobrir VMs.

### <a name="connect-to-vcenter-server"></a>Ligue-se ao servidor vCenter

O aparelho precisa de se ligar ao servidor vCenter para descobrir VMs.

1. Em **Especificar vCenter Server**, especifique o nome (FQDN) ou endereço IP do servidor vCenter.
2. Deixe a porta predefinido ou especifique uma porta personalizada na qual o vCenter Server ouve.
3. No **nome de utilizador** e **palavra-passe**, especifique as credenciais de conta vCenter Server que o aparelho utilizará para descobrir VMs no servidor vCenter.
3. **Selecione Validar a ligação**, para se certificar de que o aparelho pode ligar-se ao servidor vCenter. 

    ![vCenter detalhes do servidor e botão para validar uma ligação ao servidor vCenter](./media/tutorial-discover-vmware/vcenter.png)

1. In **Discover applications and dependências em VMs,** clique em **Adicionar credenciais**.
1. Especifique a credencial Windows/Linux que está a usar para a descoberta de aplicações e análise de dependência.
1. Clique **em Guardar e iniciar a descoberta,** para iniciar o processo de descoberta.

    ![Adicione credenciais para a descoberta de aplicações e análise de dependência, e inicie a descoberta contínua. ](./media/tutorial-discover-vmware/start-discovery.png)


Após o início da descoberta:

- Leva cerca de 15 minutos para que os metadados VM descobertos apareçam no portal.
- A descoberta da aplicação leva algum tempo. O tempo depende do número de VMs sendo descobertos. Para 500 VMs, leva aproximadamente uma hora para o inventário da aplicação aparecer no portal Azure Migrate.



## <a name="verify-discovered-vms-in-the-portal"></a>Verifique os VMs descobertos no portal

Após a descoberta, pode verificar se os VMs aparecem no portal Azure:

1. Abra o painel Azure Migrate.
2. Em **Azure Migrate - Servidores**  >  **Azure Migrate: Avaliação**do servidor , selecione o ícone que exibe a contagem para **servidores descobertos**.

## <a name="next-steps"></a>Próximos passos

- [Avaliar VMware VMs](tutorial-assess-vmware.md) para migração para VMs Azure.
- [Reveja os dados](migrate-appliance.md#collected-data---vmware) que o aparelho recolhe durante a descoberta.
