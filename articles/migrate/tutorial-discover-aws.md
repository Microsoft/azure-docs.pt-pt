---
title: Descubra instâncias AWS com avaliação do servidor Azure Migrate
description: Saiba como descobrir instâncias AWS com avaliação do servidor Azure Migrate.
ms.topic: tutorial
ms.date: 09/14/2020
ms.custom: mvc
ms.openlocfilehash: 6924182e6425b0927583e8c6498c7073c4ff1806
ms.sourcegitcommit: 51df05f27adb8f3ce67ad11d75cb0ee0b016dc5d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90064407"
---
# <a name="tutorial-discover-aws-instances-with-server-assessment"></a>Tutorial: Descubra instâncias AWS com avaliação do servidor

Como parte da sua viagem de migração para Azure, você descobre os seus servidores para avaliação e migração.

Este tutorial mostra-lhe como descobrir instâncias da Amazon Web Services (AWS) com a ferramenta Azure Migrate: Server Assessment, utilizando um aparelho Azure Migrate leve. Implanta o aparelho como servidor físico, para descobrir continuamente metadados de máquina e desempenho.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Crie uma conta Azure.
> * Prepare os casos da AWS para a descoberta.
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
**Aparelho** | Precisa de um EC2 VM para executar o aparelho Azure Migrate. A máquina deve ter:<br/><br/> - Windows Server 2016 instalado. A execução do aparelho numa máquina com o Windows Server 2019 não é suportada.<br/><br/> - RAM de 16 GB, 8 vCPUs, cerca de 80 GB de armazenamento de disco e um interruptor virtual externo.<br/><br/> - Um endereço IP estático ou dinâmico, com acesso à Internet, diretamente ou através de um representante.
**Instâncias do Windows** | Permitir ligações de entrada na porta WinRM 5985 (HTTP), para que o aparelho possa puxar os metadados de configuração e desempenho.
**Instâncias linux** | Permitir ligações de entrada na porta 22 (TCP).

## <a name="prepare-an-azure-user-account"></a>Preparar uma conta de utilizador Azure

Para criar um projeto Azure Migrate e registar o aparelho Azure Migrate, precisa de uma conta com:
- Permissões de colaborador ou proprietário numa subscrição do Azure.
- Permissões para registar aplicações do Azure Ative Directory.

Se acabou de criar uma conta Azure gratuita, é o proprietário da sua subscrição. Se não for o proprietário da subscrição, trabalhe com o proprietário para atribuir as permissões da seguinte forma:

1. No portal Azure, procure por "subscrições", e em **Serviços,** **selecione Subscrições**.

    ![Caixa de pesquisa para procurar a subscrição do Azure](./media/tutorial-discover-aws/search-subscription.png)

2. Na página **Subscrições,** selecione a subscrição na qual pretende criar um projeto Azure Migrate. 
3. Na subscrição, selecione **Access Control (IAM)**  >  **Verifique o acesso**.
4. No **Acesso ao Cheques,** procure na conta de utilizador relevante.
5. In **Add a role assignment**, clique em **Adicionar**.

    ![Procure uma conta de utilizador para verificar o acesso e atribuir uma função](./media/tutorial-discover-aws/azure-account-access.png)

6. Na **atribuição de funções Adicionar**, selecione a função Contribuinte ou Proprietário e selecione a conta (azmigrateuser no nosso exemplo). Em seguida, clique em **Guardar**.

    ![Abre a página de atribuição de Função Adicionar para atribuir uma função à conta](./media/tutorial-discover-aws/assign-role.png)

7. No portal, procure utilizadores e em **Serviços,** selecione **Utilizadores.**
8. Nas **definições do Utilizador,** verifique se os utilizadores de Ad Azure podem registar aplicações (definidas para **Sim** por predefinição).

    ![Verifique nas Definições do Utilizador que os utilizadores podem registar aplicações de Ative Directory](./media/tutorial-discover-aws/register-apps.png)


## <a name="prepare-aws-instances"></a>Preparar instâncias AWS

Crie uma conta que o aparelho possa utilizar para aceder a instâncias AWS.

- Para os servidores do Windows, crie uma conta de utilizador local em todos os servidores do Windows que pretende incluir na descoberta. Adicione a conta de utilizador aos seguintes grupos: - Utilizadores de Gestão Remota - Utilizadores do Monitor de Desempenho - Utilizadores do Registo de Desempenho.
 - Nos servidores Linux, precisa de uma conta de superutilizador nos servidores Linux que deseja detetar.


## <a name="set-up-a-project"></a>Criar um projeto

Crie um novo projeto Azure Migrate.

1. No portal do Azure > **Todos os serviços**, procure **Azure Migrate**.
2. Em **Serviços**, selecione **Azure Migrate**.
3. Em **Visão Geral**, selecione **Criar projeto**.
5. No **projeto Create,** selecione o seu grupo de subscrição e recursos Azure. Crie um grupo de recursos se não tiver um.
6. Em **Detalhes do Projeto,** especifique o nome do projeto e a geografia em que pretende criar o projeto. Reveja geografias apoiadas para nuvens [públicas](migrate-support-matrix.md#supported-geographies-public-cloud) e [governamentais.](migrate-support-matrix.md#supported-geographies-azure-government)

   ![Caixas para nome e região do projeto](./media/tutorial-discover-aws/new-project.png)

7. Selecione **Criar**.
8. Aguarde alguns minutos para que o projeto do Azure Migrate seja implementado.

A ferramenta **Azure Migrate: Server Assessment** é adicionada por defeito ao novo projeto.

![Página mostrando a ferramenta de avaliação do servidor adicionada por padrão](./media/tutorial-discover-aws/added-tool.png)

## <a name="set-up-the-appliance"></a>Configurar o aparelho

Para configurar o aparelho numa instância AWS, descarregue e execute um script instalador num [local adequado](#prerequisites). Depois de criar o aparelho, verifique se pode ligar-se ao Azure, configurá-lo pela primeira vez e registá-lo com o projeto Azure Migrate.

### <a name="download-the-installer-script"></a>Descarregue o script do instalador

Descarregue o ficheiro com fecho para o aparelho.

1. In **Migration Goals**  >  **Servers**  >  **Azure Migrate: Server Assessment**, click **.click**.
2. In **Discover machines**  >  **Are your machines virtualized?** **Not virtualized/Other**
3. Clique **em Baixar** para descarregar o ficheiro com fecho de correr.



### <a name="install-the-appliance"></a>Instale o aparelho

O script do instalador faz o seguinte:

- Instala agentes e uma aplicação web para a descoberta e avaliação da AWS.
- Instale funções windows, incluindo o Windows Activation Service, IIS e PowerShell ISE.
- Descarregue e instale um módulo reescrito IIS. [Saiba mais](https://www.microsoft.com/download/details.aspx?id=7435).
- Atualiza uma chave de registo (HKLM) com detalhes de definição persistente para Azure Migrate.
- Cria os seguintes ficheiros no caminho:
    - **Config Ficheiros**: %Programdata%\Microsoft Azure\Config
    - **Ficheiros de registo**: %Programdata%\Microsoft Azure\Logs

Execute o guião da seguinte forma:

1. Extraia o ficheiro com fecho para uma pasta no servidor que irá hospedar o aparelho.  Certifique-se de que não coloca o guião numa máquina num aparelho Azure Migrate existente.
2. Lançar PowerShell no servidor acima com privilégio administrativo (elevado).
3. Mude o diretório PowerShell para a pasta onde o conteúdo foi extraído do ficheiro fechado descarregado.
4. Executar o script nomeado **AzureMigrateInstaller.ps1** executando o seguinte comando:

    - Para a nuvem pública: ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 ```
    - Para o Governo de Azure: ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-Server-USGov>AzureMigrateInstaller.ps1 ```

    O script lançará a aplicação web do aparelho quando terminar com sucesso.

Se encontrar algum problema, pode aceder aos registos de scripts em C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log para resolução de problemas.


### <a name="verify-appliance-access-to-azure"></a>Verifique o acesso do aparelho ao Azure

Verifique o acesso ao aparelho da seguinte forma:

1. Certifique-se de que a máquina do aparelho pode ligar-se aos URLs Azure para nuvens [públicas](migrate-appliance.md#public-cloud-urls) e [governamentais.](migrate-appliance.md#government-cloud-urls)
2. Certifique-se de que estas portas estão abertas na máquina do aparelho:

    - Permitir ligações de entrada na porta TCP 3389, para permitir ligações remotas de ambiente de trabalho ao aparelho.
    - Permitir que as ligações de entrada na porta 44368 acedam remotamente à aplicação web do aparelho utilizando o URL: https://<appliance-ip-or-name>:44368.
    - Permitir que as ligações de saída na porta 443 (HTTPS) enviem metadados de descoberta e desempenho para Azure Migrate.


### <a name="configure-the-appliance"></a>Configure o aparelho

Coloque o aparelho pela primeira vez.

1. Abra um browser em qualquer máquina que possa ligar ao VM e abra o URL da aplicação web do aparelho: **https:// nome do aparelho ou endereço*IP*: 44368**.

   Em alternativa, pode abrir a aplicação a partir do ambiente de trabalho do aparelho selecionando o atalho da aplicação.
2. Na aplicação web Azure Migrate Appliance > **Configurar pré-requisitos,** rever/aceitar os termos da licença e ler as informações de terceiros.
3. O aparelho verifica se o VM tem acesso à Internet e que o tempo no VM está sincronizado com o tempo de internet.
    - Se estiver a utilizar um representante, clique em **Configurar procuração** e especificar o endereço de procuração e a porta (no formato http://ProxyIPAddress ou http://ProxyFQDN) . 
    - Especifique credenciais se o representante necessitar de autenticação. Apenas é suportado o proxy HTTP.
4. O aparelho instala as últimas atualizações do Azure Migrate
5. Reveja as definições e clique em **Continuar.**

       

### <a name="register-the-appliance-with-azure-migrate"></a>Registe o aparelho com a Azure Migrate

1. In **Register with Azure Migrate**, selecione **Login**. Se não aparecer, certifique-se de ter desativado o bloqueador pop-up no navegador.

    ![Clique em Iniciar sessão para começar a registar o aparelho](./media/tutorial-discover-aws/register.png)

1. Na página **Iniciar súmis,** inscreva-se com o seu nome de utilizador E password Azure. O s-in com um PIN não é suportado.

    ![Inscreva-se no botão para registar o aparelho](./media/tutorial-discover-aws/sign-in.png)
1. Depois de iniciar sins com sucesso, volte para a aplicação.
1. No **Registo com Azure Migrate,** selecione a subscrição em que o projeto Azure Migrate foi criado e, em seguida, selecione o projeto.
1. Especifique um nome para o aparelho. O nome deve ser alfanumérico com 14 caracteres ou menos.
3. Selecione **Registar**. Em seguida, clique **em Continuar**. Uma mensagem mostra o registo como um sucesso.

    ![Preencha o nome de subscrição, projeto e aparelho e registe o aparelho](./media/tutorial-discover-aws/success-register.png)


## <a name="start-continuous-discovery"></a>Iniciar a descoberta contínua

O aparelho liga-se à instância AWS para ser descoberto.

1. Clique **em Adicionar Credenciais** para especificar as credenciais de conta que o aparelho utilizará para descobrir servidores.  
2. Inscreva-se com nome de utilizador e senha. Assine com uma chave não é suportado. Também o Utilizador deve ser um login de raiz ou parte do grupo de administração local.
3. Especifique o **Sistema Operativo,** um nome amigável para as credenciais, e o nome de utilizador e senha. Em seguida, clique em **Adicionar**.
Pode adicionar várias credenciais para servidores Windows e Linux.
4. Clique **em Adicionar servidor**e especifique detalhes do servidor- Endereço FQDN/IP e nome amigável de credenciais (uma entrada por linha) para ligar ao servidor.
5. Clique em **Validar**. Após a validação, é mostrada a lista de servidores que podem ser descobertos.
    - Se a validação falhar para um servidor, reveja o erro pairando sobre o ícone na coluna **Status.** Corrija problemas e valide novamente.
    - Para remover um servidor, selecione > **Eliminar**.
6. Após validação, clique em **Guardar e iniciar** a descoberta para iniciar o processo de descoberta.

Isto começa a ser descoberto. Normalmente demora menos de dois minutos por servidor para os metadados aparecerem no portal Azure.


## <a name="verify-discovered-vms-in-the-portal"></a>Verifique os VMs descobertos no portal

Após a descoberta, pode verificar se os VMs aparecem no portal Azure:

1. Abra o painel Azure Migrate.
2. Em **Azure Migrate - Servidores**  >  **Azure Migrate: Avaliação**do servidor , selecione o ícone que exibe a contagem para **servidores descobertos**.

## <a name="next-steps"></a>Próximos passos

- [Avaliar servidores físicos](tutorial-migrate-aws-virtual-machines.md) para migração para VMs Azure.
- [Reveja os dados](migrate-appliance.md#collected-data---physical) que o aparelho recolhe durante a descoberta.
