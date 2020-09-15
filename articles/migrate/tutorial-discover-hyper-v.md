---
title: Descubra VMs hiper-V com avaliação do servidor Azure Migrate
description: Saiba como descobrir em locais Hiper-VMs com a ferramenta Azure Migrate Server Assessment.
ms.topic: tutorial
ms.date: 09/14/2020
ms.custom: mvc
ms.openlocfilehash: fdf96b5767b461953fa88923aaa5050aff4613bc
ms.sourcegitcommit: 51df05f27adb8f3ce67ad11d75cb0ee0b016dc5d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90064372"
---
# <a name="tutorial-discover-hyper-v-vms-with-server-assessment"></a>Tutorial: Descubra VMs hiper-V com avaliação do servidor

Como parte da sua viagem de migração para Azure, você descobre o seu inventário no local e cargas de trabalho. 

Este tutorial mostra-lhe como descobrir no local máquinas virtuais Hiper-V (VMs) com a ferramenta Azure Migrate: Server Assessment, utilizando um aparelho Azure Migrate leve. Você implanta o aparelho como um Hiper-VM, para descobrir continuamente metadados de máquina e desempenho.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma conta Azure
> * Prepare o ambiente Hiper-V para ser descoberto.
> * Criar um projeto do Azure Migrate.
> * Instale o aparelho Azure Migrate.
> * Comece a descoberta contínua.

> [!NOTE]
> Os tutoriais mostram o caminho mais rápido para experimentar um cenário e usam opções padrão.  

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este tutorial, verifique se tem estes pré-requisitos no lugar.


**Requisito** | **Detalhes**
--- | ---
**Anfitrião Hyper-V** | Os anfitriões hiper-V nos quais os VMs estão localizados podem ser autónomos, ou num cluster.<br/><br/> O anfitrião deve estar a executar o Windows Server 2019, o Windows Server 2016 ou o Windows Server 2012 R2.<br/><br/> Verifique se as ligações de entrada são permitidas na porta WinRM 5985 (HTTP), para que o aparelho possa ligar-se para retirar metadados VM e dados de desempenho, utilizando uma sessão de Modelo de Informação Comum (CIM).
**Implantação do aparelho** | vCenter Server precisa de recursos para alocar um VM para o aparelho:<br/><br/> - Windows Server 2016<br/><br/> -32 GB de RAM<br/><br/> - Oito vCPUs<br/><br/> - Cerca de 80 GB de armazenamento de disco.<br/><br/> - Um interruptor virtual externo.<br/><br/> - Acesso à Internet para o VM, diretamente ou através de um representante.
**VMs** | Os VMs podem estar a executar qualquer sistema operativo Windows ou Linux. 

Antes de começar, pode [rever os dados](migrate-appliance.md#collected-data---hyper-v) que o aparelho recolhe durante a descoberta.

## <a name="prepare-an-azure-user-account"></a>Preparar uma conta de utilizador Azure

Para criar um projeto Azure Migrate e registar o aparelho Azure Migrate, precisa de uma conta com:
- Permissões de colaborador ou proprietário numa subscrição do Azure.
- Permissões para registar aplicações do Azure Ative Directory.

Se acabou de criar uma conta Azure gratuita, é o proprietário da sua subscrição. Se não for o proprietário da subscrição, trabalhe com o proprietário para atribuir as permissões da seguinte forma:


1. No portal Azure, procure por "subscrições", e em **Serviços,** **selecione Subscrições**.

    ![Caixa de pesquisa para procurar a subscrição do Azure](./media/tutorial-discover-hyper-v/search-subscription.png)

2. Na página **Subscrições,** selecione a subscrição na qual pretende criar um projeto Azure Migrate. 
3. Na subscrição, selecione **Access Control (IAM)**  >  **Verifique o acesso**.
4. No **Acesso ao Cheques,** procure na conta de utilizador relevante.
5. In **Add a role assignment**, clique em **Adicionar**.

    ![Procure uma conta de utilizador para verificar o acesso e atribuir uma função](./media/tutorial-discover-hyper-v/azure-account-access.png)

6. Na **atribuição de funções Adicionar**, selecione a função Contribuinte ou Proprietário e selecione a conta (azmigrateuser no nosso exemplo). Em seguida, clique em **Guardar**.

    ![Abre a página de atribuição de Função Adicionar para atribuir uma função à conta](./media/tutorial-discover-hyper-v/assign-role.png)

7. No portal, procure utilizadores e em **Serviços,** selecione **Utilizadores.**
8. Nas **definições do Utilizador,** verifique se os utilizadores de Ad Azure podem registar aplicações (definidas para **Sim** por predefinição).

    ![Verifique nas Definições do Utilizador que os utilizadores podem registar aplicações de Ative Directory](./media/tutorial-discover-hyper-v/register-apps.png)

## <a name="prepare-hyper-v-hosts"></a>Preparar anfitriões Hiper-V

Crie uma conta com acesso ao Administrador nos anfitriões Hiper-V. O aparelho utiliza esta conta para ser descoberto.

- Opção 1: Preparar uma conta com o acesso do Administrador à máquina de anfitrião Hyper-V.
- Opção 2: Preparar uma conta de administração local, ou conta Dedmin de Domínio, e adicionar a conta a estes grupos: Utilizadores de Gestão Remota, Administradores de Hiper-V e Utilizadores de Monitores de Desempenho.


## <a name="set-up-a-project"></a>Criar um projeto

et-up um novo projeto Azure Migrate.

1. No portal do Azure > **Todos os serviços**, procure **Azure Migrate**.
2. Em **Serviços**, selecione **Azure Migrate**.
3. Em **Visão Geral**, selecione **Criar projeto**.
5. No **projeto Create,** selecione o seu grupo de subscrição e recursos Azure. Crie um grupo de recursos se não tiver um.
6. Em **Detalhes do Projeto,** especifique o nome do projeto e a geografia em que pretende criar o projeto. Reveja geografias apoiadas para nuvens [públicas](migrate-support-matrix.md#supported-geographies-public-cloud) e [governamentais.](migrate-support-matrix.md#supported-geographies-azure-government)

   ![Caixas para nome e região do projeto](./media/tutorial-discover-hyper-v/new-project.png)

7. Selecione **Criar**.
8. Aguarde alguns minutos para que o projeto do Azure Migrate seja implementado.

A ferramenta **Azure Migrate: Server Assessment** é adicionada por defeito ao novo projeto.

![Página mostrando a ferramenta de avaliação do servidor adicionada por padrão](./media/tutorial-discover-hyper-v/added-tool.png)


## <a name="set-up-the-appliance"></a>Configurar o aparelho

Este tutorial instala o aparelho num Hiper-V VM.
- Descarregue o modelo do aparelho e importe o modelo no vCenter Server para criar o VM do aparelho.
- Depois de criar o aparelho, instale-o pela primeira vez e registe-o com o projeto Azure Migrate.

> [!NOTE]
> Se, por alguma razão, não conseguir configurar o aparelho utilizando um modelo, pode 15 000% pode utilizá-lo com um script PowerShell. [Saiba mais](deploy-appliance-script.md#set-up-the-appliance-for-hyper-v).


### <a name="download-the-vhd"></a>Descarregue o VHD

1. In **Migration Goals**  >  **Servers**  >  **Azure Migrate: Server Assessment**, select **Discover**.
2. In **Discover machines**  >  **Are your machines virtualized?** **Yes, with Hyper-V**
3. Selecione **Baixar** para descarregar o ficheiro VHD.

   ![Seleções para descarregar um ficheiro OVA](./media/tutorial-discover-hyper-v/download-vhd.png)



### <a name="deploy-the-appliance-vm"></a>Desdobre o aparelho VM

Importe o ficheiro descarregado e crie um VM:

1. Descarregue o ficheiro VHD com fecho para o anfitrião Hyper-V no qual o aparelho VM será colocado.
2. Extraia o ficheiro com fecho.

    - No local extraído, o ficheiro abre-se numa pasta chamada AzureMigrateAppliance_VersionNumber.
    - Esta pasta contém uma sub-dobragem, também chamada AzureMigrateAppliance_VersionNumber.
    - Esta sub-dobradeira contém mais três sub-dobradeiras - Snapshots, Discos Rígidos Virtuais e Máquinas Virtuais.

3. Abra o Gestor de Hyper-V. Em **Ações,** clique em **Import Virtual Machine**.

    ![Item do menu em Hyper-V Manager para importar o VHD](./media/tutorial-discover-hyper-v/deploy-vhd.png)

4. No > do assistente de máquina virtual de importação **Antes de começar,** clique em **seguida**.
5. Na **Pasta Localizar,** selecione a pasta **Máquinas Virtuais.** Em seguida, clique em **Seguinte**.
6. Na **Máquina Virtual Selecionada,** clique em **Seguinte**.
7. No **Choose Import Type,** clique **em Copiar a máquina virtual (crie um novo ID único)**. Em seguida, clique em **Seguinte**.
8. No **Destino Escolha,** deixe a definição predefinida. Clique em **Seguinte**.
9. Nas **pastas de armazenamento,** deixe a definição predefinida. Clique em **Seguinte**.
10. In **Choose Network**, especifique o interruptor virtual que o VM irá utilizar. O switch precisa de conectividade na Internet para enviar dados para o Azure. [Saiba](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-switch-for-hyper-v-virtual-machines) como criar um interruptor virtual.
11. Em **Resumo,** reveja as definições. Em seguida, clique em **Concluir**.
12. No Hyper-V Manager > **Máquinas Virtuais,** inicie o VM.


### <a name="verify-appliance-access-to-azure"></a>Verifique o acesso do aparelho ao Azure

Verifique o acesso ao aparelho da seguinte forma:

1. Verifique se o VM pode ligar-se ao Azure.
    - Na nuvem pública, a máquina do aparelho deve ser capaz de ligar a estes [URLs](migrate-appliance.md#public-cloud-urls).
    - Na nuvem governamental, o aparelho deve ser capaz de ligar a estes [URLs governamentais.](migrate-appliance.md#government-cloud-urls)
2. Certifique-se de que estas portas estão abertas na máquina do aparelho:

    - Permitir ligações de entrada na porta TCP 3389, para permitir ligações remotas de ambiente de trabalho ao aparelho.
    - Permitir que as ligações de entrada na porta 44368 acedam remotamente à aplicação web do aparelho utilizando o URL: https:// \<appliance-ip-or-name> :44368.
    - Permitir que as ligações de saída na porta 443 (HTTPS) enviem metadados de descoberta e desempenho para Azure Migrate.


### <a name="configure-the-appliance"></a>Configure o aparelho

Coloque o aparelho pela primeira vez.

> [!NOTE]
> Se configurar o aparelho utilizando um [script PowerShell](deploy-appliance-script.md) em vez do VHD descarregado, os dois primeiros passos deste procedimento não são relevantes.

1. No Hyper-V Manager > **Máquinas Virtuais,** clique com o botão direito no VM > **Connect**.
2. Forneça o idioma, o fuso horário e a palavra-passe para o aparelho.
3. Abra um browser em qualquer máquina que possa ligar ao VM e abra o URL da aplicação web do aparelho: **https:// nome do aparelho ou endereço*IP*: 44368**.

   Em alternativa, pode abrir a aplicação a partir do ambiente de trabalho do aparelho clicando no atalho da aplicação.
1. Na aplicação web > **Configurar pré-requisitos,** faça o seguinte:
    - **Licença**: Aceite os termos da licença e leia as informações de terceiros.
    - **Conectividade**: A aplicação verifica se o VM tem acesso à Internet. Se o VM utilizar um representante:
      - Clique **nas definições de Procuração**e especifique o endereço de procuração e a porta de audição, no formulário http://ProxyIPAddress ou http://ProxyFQDN .
      - Especifique as credenciais se o proxy precisar de autenticação.
      - Apenas é suportado o proxy HTTP.
    - **Sincronização temporal:** O tempo é verificado. O tempo do aparelho deve estar sincronizado com o tempo de internet para que a descoberta de VM funcione corretamente.
    - **Instalar atualizações**: A Azure Migrate Server Assessment verifica se o aparelho tem as últimas atualizações instaladas.

### <a name="register-the-appliance"></a>Registar o aparelho 

1. In **Register with Azure Migrate**, selecione **Login**. Se não aparecer, certifique-se de ter desativado o bloqueador pop-up no navegador.

    ![Clique em Iniciar sessão para começar a registar o aparelho](./media/tutorial-discover-hyper-v/register.png)

1. Na página **Iniciar súmis,** inscreva-se com o seu nome de utilizador E password Azure. O s-in com um PIN não é suportado.

    ![Inscreva-se no botão para registar o aparelho](./media/tutorial-discover-hyper-v/sign-in.png)
1. Depois de iniciar sins com sucesso, volte para a aplicação.
1. No **Registo com Azure Migrate,** selecione a subscrição em que o projeto Azure Migrate foi criado e, em seguida, selecione o projeto.
1. Especifique um nome para o aparelho. O nome deve ser alfanumérico com 14 caracteres ou menos.
3. Selecione **Registar**. Em seguida, clique **em Continuar**. Uma mensagem mostra o registo como um sucesso.

    ![Preencha o nome de subscrição, projeto e aparelho e registe o aparelho](./media/tutorial-discover-hyper-v/success-register.png)


### <a name="delegate-credentials-for-smb-vhds"></a>Credenciais de delegado para VHDs SMB

Se estiver a executar VHDs em SMBs, deve ativar a delegação de credenciais do aparelho para os anfitriões Hiper-V. Para tal, permite que cada hospedeiro atue como delegado do aparelho. Se seguiu os tutoriais por ordem, fê-lo no tutorial anterior, quando preparou o Hyper-V para avaliação e migração. Você deveria ter configurado CredSSP para os anfitriões [manualmente](tutorial-prepare-hyper-v.md#enable-credssp-to-delegate-credentials), ou [executando um script](tutorial-prepare-hyper-v.md#run-the-script) que faz isso.

Ativar o aparelho da seguinte forma:

#### <a name="option-1"></a>Opção 1

No aparelho VM, este comando. HyperVHost1/HyperVHost2 são nomes de anfitriões exemplo.

```
Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com, HyperVHost2.contoso.com, HyperVHost1, HyperVHost2 -Force
```

Exemplo: ` Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com HyperVHost2.contoso.com -Force `

#### <a name="option-2"></a>Opção 2

Em alternativa, faça-o no Editor de Política do Grupo Local sobre o aparelho:

1. Na **Local Computer Policy**  >  **configuração**do computador local, clique na delegação de credenciais **de sistema de modelos**  >  **System**  >  **administrativos**.
2. Clique duas **vezes Em permitir a delegação de credenciais frescas**e selecione **Ativado**.
3. Em **Opções**, clique em **Mostrar**, e adicione cada anfitrião Hyper-V que pretende descobrir na lista, com **wsman/** como prefixo.
4. Em seguida, na Delegação de **Credenciais,** clique duplo **Deixe delegar credenciais frescas com autenticação do servidor apenas NTLM**. Mais uma vez, adicione cada anfitrião Hyper-V que pretende descobrir na lista, com **wsman/** como prefixo.



## <a name="start-continuous-discovery"></a>Iniciar a descoberta contínua

O aparelho precisa de se ligar aos anfitriões Hiper-V para descobrir VMs.


### <a name="connect-to-hyper-v-hosts"></a>Ligar aos anfitriões Hiper-V

1. No **nome de utilizador** e na **palavra-passe,** especifique as credenciais de conta que o aparelho utilizará para descobrir VMs. Especifique um nome amigável para as credenciais e clique em **Guardar detalhes.**
2. Clique **em Adicionar anfitrião**e especifique detalhes do anfitrião/cluster hyper-V.
3. Clique em **Validar**. Após a validação, é indicado o número de VMs que podem ser descobertos em cada hospedeiro/cluster.
    - Se a validação falhar para um anfitrião, reveja o erro pairando sobre o ícone na coluna **Status.** Corrija problemas e valide novamente.
    - Para remover anfitriões ou agrupamentos, selecione > **Eliminar**.
    - Não se pode remover um hospedeiro específico de um aglomerado. Só se pode remover todo o aglomerado.
    - Pode adicionar um cluster, mesmo que existam problemas com anfitriões específicos no cluster.
4. Após validação, clique em **Guardar e iniciar** a descoberta para iniciar o processo de descoberta.

Isto começa a ser descoberto. Demora cerca de 1,5 minutos por anfitrião para que os metadados dos servidores descobertos apareçam no portal Azure.


### <a name="verify-discovered-vms-in-the-portal"></a>Verifique os VMs descobertos no portal

Após a descoberta, pode verificar se os VMs aparecem no portal Azure:

1. Abra o painel Azure Migrate.
2. Em **Azure Migrate - Servidores**  >  **Azure Migrate: Avaliação**do servidor , selecione o ícone que exibe a contagem para **servidores descobertos**.

## <a name="next-steps"></a>Próximos passos

- [Avaliar VMs hiper-V](tutorial-assess-hyper-v.md) para migração para VMs Azure.
- [Reveja os dados](migrate-appliance.md#collected-data---hyper-v) que o aparelho recolhe durante a descoberta.