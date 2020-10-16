---
title: Descubra VMware VMs com avaliação do servidor Azure Migrate
description: Saiba como descobrir VMware VMs no local com a ferramenta de avaliação do servidor Azure Migrate
ms.topic: tutorial
ms.date: 09/14/2020
ms.custom: mvc
ms.openlocfilehash: f39ad3cbc357575f735b963346c8a8b0cc95e7c8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91442233"
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

Se acabou de criar uma conta gratuita do Azure, é o proprietário da sua subscrição. Se não for o proprietário da subscrição, trabalhe com o proprietário para atribuir as permissões da seguinte forma:

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

9. Em alternativa, o inquilino/administrador global pode atribuir o papel **de Desenvolvedor de Aplicações** a uma conta para permitir o registo de App(s) AAD. [Saiba mais](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).

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

Para configurar o aparelho utilizando um modelo OVA:
- Forneça um nome de aparelho e gere uma chave de projeto Azure Migrate no portal
- Descarregue um ficheiro de modelo OVA e importe-o para o vCenter Server.
- Crie o aparelho e verifique se pode ligar-se à Avaliação do Servidor Azure Migrate.
- Configure o aparelho pela primeira vez e registe-o com o projeto Azure Migrate utilizando a chave do projeto Azure Migrate.

> [!NOTE]
> Se, por alguma razão, não conseguir configurar o aparelho utilizando o gabarito, pode 15 000% pode use-lo com um script PowerShell. [Saiba mais](deploy-appliance-script.md#set-up-the-appliance-for-vmware).


### <a name="deploy-with-ova"></a>Implementar com OVA

Para configurar o aparelho utilizando um modelo OVA:
- Forneça um nome de aparelho e gere uma chave de projeto Azure Migrate no portal
- Descarregue um ficheiro de modelo OVA e importe-o para o vCenter Server.
- Crie o aparelho e verifique se pode ligar-se à Avaliação do Servidor Azure Migrate.
- Configure o aparelho pela primeira vez e registe-o com o projeto Azure Migrate utilizando a chave do projeto Azure Migrate.

### <a name="generate-the-azure-migrate-project-key"></a>Gere a chave do projeto Azure Migrate

1. Em **Objetivos de Migração** > **Servidores** > **Azure Migrate: Avaliação do Servidor**, selecione **Detetar**.
2. In **Discover machines**  >  **Are your machines virtualized?** **Yes, with VMware vSphere hypervisor**
3. Na **tecla de projeto 1:Generate Azure Migrate,** forneça um nome para o aparelho Azure Migrate que irá configurar para a descoberta de VMware VMs.O nome deve ser alfanumérico com 14 caracteres ou menos.
1. Clique na **chave Gerar** para iniciar a criação dos recursos Azure necessários. Por favor, não feche a página das máquinas Discover durante a criação de recursos.
1. Após a criação bem sucedida dos recursos Azure, é gerada uma **chave de projeto Azure Migrate.**
1. Copie a chave pois necessitará para completar o registo do aparelho durante a sua configuração.

### <a name="download-the-ova-template"></a>Descarregue o modelo OVA

Em **2: Descarregue o aparelho Azure Migrate,** selecione o . Ficheiro OVA e clique no **Download**. 


### <a name="verify-security"></a>Verificar segurança

Verifique se o ficheiro OVA está seguro, antes de o implementar:

1. No computador para o qual transferiu o ficheiro, abra uma janela de comando de administrador.
2. Executar o seguinte comando para gerar o haxixe para o ficheiro OVA:
  
   ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
   
   Utilização de exemplo: ```C:\>CertUtil -HashFile C:\Users\Administrator\Desktop\MicrosoftAzureMigration.ova SHA256```

3. Verifique as versões mais recentes do aparelho e os valores do haxixe:

    - Para a nuvem pública de Azure:
    
        **Algoritmo** | **Transferência** | **SHA256**
        --- | --- | ---
        VMware (11.9 GB) | [Versão mais recente](https://go.microsoft.com/fwlink/?linkid=2140333) | bd5c19eec93a62d52cc507a6b408d07f33f92b72b72b7d39b8a1e3dfec4ec62830d7

    - Para o Governo de Azure:
    
        **Algoritmo** | **Transferência** | **SHA256**
        --- | --- | ---
        VMware (85.8 MB) | [Versão mais recente](https://go.microsoft.com/fwlink/?linkid=2140337) | 2daaa2a59302bf911e8ef195d7d7c83527a9af0b860e2a627979085ca




### <a name="create-the-appliance-vm"></a>Criar o aparelho VM

Importe o ficheiro descarregado e crie um VM.

1. Na consola vSphere Client, clique em **File**  >  **Ficheiros Implementar OVF Modelo**.
2. No Assistente de Modelo de OVF de implementação > **Fonte**, especifique a localização do ficheiro OVA.
3. No **Nome** e **Localização**, especifique um nome amigável para o VM. Selecione o objeto de inventário no qual o VM será hospedado.
5. No **Host/Cluster**, especifique o hospedeiro ou o cluster em que o VM irá funcionar.
6. No **Armazenamento**, especifique o destino de armazenamento para o VM.
7. Em **Formato do Disco**, especifique o tipo e o tamanho do disco.
8. No **Mapeamento de Rede,** especifique a rede à qual o VM se ligará. A rede precisa de conectividade com a Internet, para enviar metadados para a Avaliação do Servidor Migratório Azure.
9. Reveja e confirme as definições e, em seguida, clique em **Concluir**.


### <a name="verify-appliance-access-to-azure"></a>Verifique o acesso do aparelho ao Azure

Certifique-se de que o aparelho VM pode ligar-se aos URLs Azure para nuvens [públicas](migrate-appliance.md#public-cloud-urls) e [governamentais.](migrate-appliance.md#government-cloud-urls)


### <a name="configure-the-appliance"></a>Configure o aparelho

Coloque o aparelho pela primeira vez.

> [!NOTE]
> Se configurar o aparelho utilizando um [script PowerShell](deploy-appliance-script.md) em vez do OVA descarregado, os dois primeiros passos deste procedimento não são relevantes.

1. Na consola vSphere Client, clique com o botão direito no VM e, em seguida, selecione **Open Console**.
2. Forneça o idioma, o fuso horário e a palavra-passe para o aparelho.
3. Abra um browser em qualquer máquina que possa ligar ao VM e abra o URL da aplicação web do aparelho: **https:// nome do aparelho ou endereço*IP*: 44368**.

   Em alternativa, pode abrir a aplicação a partir do ambiente de trabalho do aparelho selecionando o atalho da aplicação.
1. Aceite os termos da **licença**e leia as informações de terceiros.
1. Na aplicação web > **Configurar pré-requisitos,** faça o seguinte:
   - **Conectividade**: A aplicação verifica se o VM tem acesso à Internet. Se o VM utilizar um representante:
     - Clique em **Configurar o representante** para especificar o endereço de procuração (no formulário http://ProxyIPAddress ou na porta de http://ProxyFQDN) audição.
     - Especifique as credenciais se o proxy precisar de autenticação.
     - Apenas é suportado o proxy HTTP.
     - Se tiver adicionado detalhes de procuração ou desativado o proxy e/ou autenticação, clique em **Guardar** para ativar novamente a verificação de conectividade.
   - **Sincronização de tempo**: O tempo do aparelho deve estar sincronizado com o tempo de internet para que a descoberta funcione corretamente.
   - **Instalação de atualizações**: O aparelho assegura a instalação das atualizações mais recentes. Depois de concluída a verificação, pode clicar nos **serviços do aparelho para** ver o estado e as versões dos componentes em funcionamento no aparelho.
   - **Instalação VDDK**: O aparelho verifica se o Kit de Desenvolvimento de Discos Virtuais VMware vSphere (VDDK) está instalado. Se não estiver instalado, faça o download do VDDK 6.7 da VMware e extraia o conteúdo zip descarregado para o local especificado no aparelho, conforme indicado nas **instruções de instalação**.

     A migração do servidor Azure Migrate usa o VDDK para replicar máquinas durante a migração para Azure. 
1. Se desejar, pode **repetir os pré-requisitos** a qualquer momento durante a configuração do aparelho para verificar se o aparelho cumpre todos os requisitos.

### <a name="register-the-appliance-with-azure-migrate"></a>Registe o aparelho com a Azure Migrate

1. Cole a chave do **projeto Azure Migrate** copiada do portal. Se não tiver a chave, vá à Avaliação do Servidor> Descubra> Gerir os **aparelhos existentes**, selecione o nome do aparelho que forneceu no momento da geração de chaves e copie a chave correspondente.
1. Clique em **Iniciar sessão**. Abrirá um pedido de login do Azure num novo separador de navegador. Se não aparecer, certifique-se de ter desativado o bloqueador pop-up no navegador.
1. No novo separador, inscreva-se utilizando o seu nome de utilizador E palavra-passe Azure.
   
   O s-in com um PIN não é suportado.
3. Depois de iniciar sessão com sucesso, volte à aplicação web. 
4. Se a conta de utilizador Azure utilizada para a exploração madeireira tiver as [permissões certas](tutorial-prepare-vmware.md#prepare-azure) sobre os recursos Azure criados durante a geração chave, o registo do aparelho será iniciado.
1. Depois de o aparelho estar registado com sucesso, pode ver os dados do registo clicando nos **detalhes do Ver.**



## <a name="start-continuous-discovery"></a>Iniciar a descoberta contínua

O aparelho precisa de se ligar ao servidor vCenter para descobrir a configuração e os dados de desempenho dos VMs.

1. No **passo 1: Forneça credenciais do servidor vCenter**, clique em **Adicionar credenciais** para especificar um nome amigável para credenciais, adicionar **nome de utilizador** e **palavra-passe** para a conta vCenter Server que o aparelho utilizará para descobrir VMs na instância do servidor vCenter.
    - Deveria ter criado uma conta com as permissões necessárias no [tutorial anterior.](tutorial-prepare-vmware.md#set-up-permissions-for-assessment)
    - Se pretender estender a descoberta a objetos VMware específicos (centros de dados vCenter Server, clusters, uma pasta de clusters, anfitriões, uma pasta de anfitriões ou VMs individuais.), reveja as instruções [deste artigo](set-discovery-scope.md) para restringir a conta utilizada pela Azure Migrate.
1. No **passo 2: Forneça detalhes do servidor vCenter**, clique na **fonte de descoberta Adicionar** para selecionar o nome amigável para credenciais a partir do drop-down, especificar o endereço **IP/FQDN** da instância vCenter Server. Pode deixar a **Porta** para predefinição (443) ou especificar uma porta personalizada na qual o vCenter Server ouve e clica em **Guardar**.
1. Ao clicar em Guardar, o aparelho tentará validar a ligação ao servidor vCenter com as credenciais fornecidas e mostrar o **estado de Validação** na tabela contra o endereço IP/FQDN do servidor vCenter.
1. Pode **revalidar** a conectividade ao vCenter Server a qualquer momento antes de iniciar a descoberta.
1. No **passo 3: Forneça credenciais VM para descobrir aplicações instaladas e realizar mapeamento de dependência sem agente,** clique **em Adicionar credenciais,** e especifique o sistema operativo para o qual as credenciais são fornecidas, nome amigável para credenciais e o **nome** de utilizador e **senha**. Em seguida, clique em **Guardar**.

    - Você opcionalmente adiciona credenciais aqui se você criou uma conta para usar para o recurso de descoberta de [aplicações](how-to-discover-applications.md), ou a [funcionalidade de análise de dependência sem agente](how-to-create-group-machine-dependencies-agentless.md).
    - Se não quiser utilizar estas funcionalidades, pode clicar no slider para saltar o passo. Pode inverter a intenção a qualquer momento depois.
    - Reveja as credenciais necessárias para [a descoberta de aplicações,](migrate-support-matrix-vmware.md#application-discovery-requirements)ou para [análise de dependência sem agentes.](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless)

5. Clique em **Iniciar a descoberta,** para iniciar a descoberta de VM. Após a descoberta ter sido iniciada com sucesso, pode verificar o estado de descoberta com o endereço IP/FQDN do servidor vCenter na tabela.

A descoberta funciona da seguinte forma:
- Leva cerca de 15 minutos para que os metadados VM descobertos apareçam no portal.
- A descoberta de aplicações, funções e funcionalidades instaladas leva algum tempo. A duração depende do número de VMs descobertos. Para 500 VMs, leva aproximadamente uma hora para o inventário da aplicação aparecer no portal Azure Migrate.


## <a name="next-steps"></a>Passos seguintes

- [Avaliar VMware VMs](tutorial-assess-vmware.md) para migração para VMs Azure.
- [Reveja os dados](migrate-appliance.md#collected-data---vmware) que o aparelho recolhe durante a descoberta.
