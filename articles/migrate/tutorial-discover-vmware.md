---
title: Descubra servidores em execução em ambiente VMware com Azure Migrate Discovery e avaliação
description: Saiba como descobrir servidores no local em execução em ambiente VMware com a ferramenta Azure Migrate Discovery e avaliação
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 03/25/2021
ms.custom: mvc
ms.openlocfilehash: 09b04c67519bfa920a3781612823c5755cbc6d2d
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2021
ms.locfileid: "105627800"
---
# <a name="tutorial-discover-servers-running-in-vmware-environment-with-azure-migrate-discovery-and-assessment"></a>Tutorial: Descubra servidores em execução em ambiente VMware com Azure Migrate: Descoberta e avaliação

Como parte da sua viagem de migração para Azure, você descobre o seu inventário no local e cargas de trabalho.

Este tutorial mostra-lhe como descobrir servidores em ambiente VMware com Azure Migrate: Ferramenta de descoberta e avaliação, utilizando um aparelho Azure Migrate leve. Implementa o aparelho como um servidor em execução no seu servidor vCenter, para descobrir continuamente servidores e seus metadados de desempenho, aplicações em execução em servidores, dependências de servidores e instâncias e bases de dados do SQL Server.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Crie uma conta Azure.
> * Prepare o ambiente VMware para ser descoberto.
> * Criar um projeto.
> * Instale o aparelho Azure Migrate.
> * Comece a descoberta contínua.

> [!NOTE]
> Os tutoriais mostram o caminho mais rápido para experimentar um cenário e usam opções padrão sempre que possível.  

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este tutorial, verifique se tem estes pré-requisitos no lugar.

**Requisito** | **Detalhes**
--- | ---
**vCenter Server/ESXi anfitrião** | Precisa de um servidor vCenter que executa a versão 5.5, 6.0, 6.5 ou 6.7.<br/><br/> Os servidores devem ser hospedados numa versão de execução do anfitrião ESXi 5.5 ou posterior.<br/><br/> No servidor vCenter, permita ligações de entrada na porta TCP 443, para que o aparelho possa recolher os metadados de configuração e desempenho.<br/><br/> O aparelho liga-se ao servidor vCenter na porta 443 por predefinição. Se o vCenter Server ouvir numa porta diferente, pode modificar a porta quando fornecer os dados do vCenter Server no gestor de configuração do aparelho.<br/><br/> Nos anfitriões ESXi, certifique-se de que o acesso à entrada é permitido na porta TCP 443 para realizar a descoberta de aplicações instaladas e análise de dependência de agentes em servidores.
**Aparelho** | vCenter Server precisa de recursos para alocar um servidor para o aparelho Azure Migrate:<br/><br/> - 32 GB de RAM, 8 vCPUs e cerca de 80 GB de armazenamento em disco.<br/><br/> - Um interruptor virtual externo e acesso à Internet no servidor do aparelho, diretamente ou através de um representante.
**Servidores** | Todas as versões Windows e Linux OS são suportadas para a descoberta de metadados de configuração e desempenho. <br/><br/> Para realizar a descoberta de aplicações em servidores, todas as versões Windows e Linux OS são suportadas. Consulte [aqui](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) as versões de SO suportadas para análise de dependência sem agentes.<br/><br/> Para realizar a descoberta de aplicações instaladas e análise de dependência sem agentes, as Ferramentas VMware (mais tarde de 10.2.0) devem ser instaladas e em execução nos servidores. Os servidores do Windows devem ter a versão PowerShell 2.0 ou posteriormente instalada.<br/><br/> Para descobrir as instâncias e bases de dados do SQL Server, consulte [aqui](migrate-support-matrix-vmware.md#requirements-for-discovery-of-sql-server-instances-and-databases) as versões e edições suportadas do SQL Server, as versões e mecanismos de autenticação suportados do Windows OS.

## <a name="prepare-an-azure-user-account"></a>Preparar uma conta de utilizador Azure

Para criar um projeto e registar o aparelho Azure Migrate, precisa de uma conta com:

- Permissões do Contribuinte ou proprietário na subscrição do Azure
- Permissões para registar aplicações do Azure Ative Directory (AAD)
- Permissões de administrador de acesso ao utilizador e ao utilizador na subscrição do Azure para criar um Cofre chave, utilizado durante a migração de servidores sem agente

Se acabou de criar uma conta gratuita do Azure, é o proprietário da sua subscrição. Se não for o proprietário da subscrição, trabalhe com o proprietário para atribuir as permissões da seguinte forma:

1. No portal Azure, procure por "subscrições", e em **Serviços,** **selecione Subscrições**.

    :::image type="content" source="./media/tutorial-discover-vmware/search-subscription.png" alt-text="Caixa de pesquisa para procurar a subscrição do Azure":::


2. Na página **Subscrições,** selecione a subscrição na qual pretende criar um projeto.
3. Na subscrição, selecione **Access Control (IAM)**  >  **Verifique o acesso**.
4. No **Acesso ao Cheques,** procure na conta de utilizador relevante.
5. In **Add a role assignment**, clique em **Adicionar**.
:::image type="content" source="./media/tutorial-discover-vmware/azure-account-access.png" alt-text="Procure uma conta de utilizador para verificar o acesso e atribuir uma função":::
    
6. Na **atribuição de funções Adicionar**, selecione a função Contribuinte ou Proprietário e selecione a conta (azmigrateuser no nosso exemplo). Em seguida, clique em **Guardar**.

    :::image type="content" source="./media/tutorial-discover-vmware/assign-role.png" alt-text="Abre a página de atribuição de Função Adicionar para atribuir uma função à conta":::

1. Para registar o aparelho, a sua conta Azure necessita de **permissões para registar aplicações AAD.**
1. No portal Azure, navegue para as Definições de Utilizador de Utilizadores **do Diretório Ativo Azure**  >    >  .
1. Nas **definições do Utilizador,** verifique se os utilizadores de Ad Azure podem registar aplicações (definidas para **Sim** por predefinição).

    :::image type="content" source="./media/tutorial-discover-vmware/register-apps.png" alt-text="Verifique nas Definições do Utilizador que os utilizadores podem registar aplicações de Ative Directory":::

9. Caso as definições de 'Registos de aplicações' sejam definidas como 'Não', solicite ao arrendatário/administrador global que atribua a permissão necessária. Em alternativa, o administrador inquilino/global pode atribuir o papel **de Desenvolvedor de Aplicações** a uma conta para permitir o registo da App AAD. [Saiba mais](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="prepare-vmware"></a>Preparar VMware

No vCenter Server, verifique se a sua conta tem permissões para criar um VM utilizando um ficheiro OVA. Isto é necessário quando colocar o aparelho Azure Migrate como VMware VM, utilizando um ficheiro OVA.

O Azure Migrate precisa de uma conta de leitura do vCenter Server apenas para a descoberta e avaliação dos servidores em execução no seu ambiente VMware. Se também pretender realizar a descoberta de aplicações instaladas e análise de dependência sem agentes, a conta necessita de privilégios habilitados para **Máquinas Virtuais > Operações de Hóspedes.**

### <a name="create-an-account-to-access-vcenter"></a>Criar uma conta para aceder ao vCenter

No vSphere Web Client, crie uma conta da seguinte forma:

1. Utilizando uma conta com privilégios de administração, no vSphere Web Client > selecione **Administração**.
2. **Aceda**, selecione **SSO Users and Groups**.
3. Nos **Utilizadores,** adicione um novo utilizador.
4. Em **Novo Utilizador,** digite os detalhes da conta. Em seguida, clique em **OK**.
5. Em **Permissões Globais,** selecione a conta de utilizador e atribua a **função Read-only** à conta. Em seguida, clique em **OK**.
6. Se também pretender realizar a descoberta de aplicações instaladas e análise de dependência sem agentes, vá a **Roles** > selecione o papel **apenas de Leitura** e, em **Privilégios,** selecione **Guest Operations**. Pode propagar os privilégios a todos os objetos sob o servidor vCenter selecionando a caixa de verificação "Propagate to children".

    :::image type="content" source="./media/tutorial-discover-vmware/guest-operations.png" alt-text="Caixa de verificação para permitir operações de hóspedes no papel apenas de leitura":::

> [!NOTE]
> Pode limitar a descoberta a centros de dados específicos do VCenter Server, clusters, uma pasta de clusters, anfitriões, uma pasta de anfitriões ou servidores individuais, digitalizando a conta vCenter Server. [**Saiba mais**](set-discovery-scope.md) sobre como analisar a conta de utilizador do vCenter Server.

### <a name="create-an-account-to-access-servers"></a>Criar uma conta para aceder a servidores

Precisa de uma conta de utilizador com os privilégios necessários nos servidores para realizar a descoberta de aplicações instaladas, análise de dependência de agentes e descoberta de instâncias e bases de dados do SQL Server. Pode fornecer a conta de utilizador no gestor de configuração do aparelho. O aparelho não instala quaisquer agentes nos servidores.

1. Para servidores windows, crie uma conta (local ou domínio) com permissões administrativas nos servidores. Para descobrir as instâncias e bases de dados do SQL Server, é necessário que a conta do Servidor Windows ou SQL seja membro da função do servidor Sysadmin. [Saiba mais](/sql/relational-databases/security/authentication-access/server-level-roles) sobre como atribuir a função necessária à conta de utilizador.
2. Para os servidores Linux, crie uma conta com privilégios Root. Em alternativa, pode criar uma conta com estas permissões em ficheiros /bin/netstat e /bin/ls: CAP_DAC_READ_SEARCH e CAP_SYS_PTRACE.

> [!NOTE]
> Agora pode adicionar várias credenciais de servidor no gestor de configuração para executar a descoberta de aplicações instaladas, análise de dependência de agentes e descoberta de instâncias e bases de dados do SQL Server. Pode adicionar várias credenciais de autenticação de domínio/ Windows (não domínio)/ Linux (não domínio) e/ou SQL Server. [**Saber mais**](add-server-credentials.md)

## <a name="set-up-a-project"></a>Criar um projeto

Crie um novo projeto.

1. No portal do Azure > **Todos os serviços**, procure **Azure Migrate**.
2. Em **Serviços**, selecione **Azure Migrate**.
3. Em **Visão Geral** > selecionar com base nos seus objetivos de migração : **Windows, Linux e SQL Server** ou **SQL Server (apenas)** ou explore mais **cenários** > selecione **Criar o projeto**.
5. No **projeto Create,** selecione o seu grupo de subscrição e recursos Azure. Crie um grupo de recursos se não tiver um.
6. Em **Detalhes do Projeto,** especifique o nome do projeto e a geografia em que pretende criar o projeto. Reveja geografias apoiadas para nuvens [públicas](migrate-support-matrix.md#supported-geographies-public-cloud) e [governamentais.](migrate-support-matrix.md#supported-geographies-azure-government)

    :::image type="content" source="./media/tutorial-discover-vmware/new-project.png" alt-text="Caixas para nome e região do projeto":::

7. Selecione **Criar**.
8. Espere alguns minutos para o projeto ser lançado. A **ferramenta Azure Migrate: Discovery and assessment** é adicionada por defeito ao novo projeto.

> [!NOTE]
> Se já criou um projeto, pode utilizar o mesmo projeto para registar aparelhos adicionais para descobrir e avaliar mais não. de servidores. [ **Saiba mais**](create-manage-projects.md#find-a-project)

## <a name="set-up-the-appliance"></a>Configurar o aparelho

Azure Migrate: Descoberta e avaliação utilize um aparelho Azure Migrate leve. O aparelho executa a descoberta do servidor e envia metadados de configuração e desempenho do servidor para a Azure Migrate. O aparelho pode ser configurado através da implementação de um modelo OVA que pode ser descarregado do projeto.

> [!NOTE]
> Se por alguma razão não conseguir configurar o aparelho utilizando o modelo, pode alterá-lo utilizando um script PowerShell num servidor do Windows Server 2016 existente. [**Saiba mais**](deploy-appliance-script.md#set-up-the-appliance-for-vmware).

### <a name="deploy-with-ova"></a>Implementar com OVA

Para configurar o aparelho utilizando um modelo OVA:

1. Forneça um nome de aparelho e gere uma chave de projeto no portal.
1. Descarregue um ficheiro de modelo OVA e importe-o para o vCenter Server. Verifique se o OVA está seguro.
1. Crie o aparelho a partir do ficheiro OVA e verifique se pode ligar-se ao Azure Migrate.
1. Configure o aparelho pela primeira vez e registe-o com o projeto utilizando a chave do projeto.

### <a name="1-generate-the-project-key"></a>1. Gerar a chave do projeto

1. Em **Objetivos de Migração**  >  **Windows, Linux e SQL Servers**  >  **Azure Migrate: Discovery and assessment**, selecione **Discover**.
2. In **Discover servers**  >  **Are your servers virtualized?** 
3. Em **1:Gere a tecla do projeto,** forneça um nome para o aparelho Azure Migrate que irá configurar para a descoberta de servidores no seu ambiente VMware. O nome deve ser alfanumérico com 14 caracteres ou menos.
1. Clique na **chave Gerar** para iniciar a criação dos recursos Azure necessários. Não feche a página Discover durante a criação de recursos.
1. Após a criação bem sucedida dos recursos Azure, gera-se uma **chave de projeto.**
1. Copie a chave pois necessitará para completar o registo do aparelho durante a sua configuração.

### <a name="2-download-the-ova-template"></a>2. Descarregue o modelo OVA

Em **2: Descarregue o aparelho Azure Migrate,** selecione o . Ficheiro OVA e clique no **Download**.

### <a name="verify-security"></a>Verificar segurança

Verifique se o ficheiro OVA está seguro, antes de o implementar:

1. No servidor para o qual descarregou o ficheiro, abra uma janela de comando do administrador.
2. Executar o seguinte comando para gerar o haxixe para o ficheiro OVA:
  
   ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
   
   Utilização de exemplo: ```C:\>CertUtil -HashFile C:\Users\Administrator\Desktop\MicrosoftAzureMigration.ova SHA256```

3. Verifique as versões mais recentes do aparelho e os valores do haxixe:

    - Para a nuvem pública de Azure:
    
        **Algoritmo** | **Transferência** | **SHA256**
        --- | --- | ---
        VMware (11.9 GB) | [Versão mais recente](https://go.microsoft.com/fwlink/?linkid=2140333) | e9c9a1fe4f3ebae8108328e8f3a7933d7835ecd871d1b17f367621ce3c74

    - Para o Governo de Azure:
    
        **Algoritmo** | **Transferência** | **SHA256**
        --- | --- | ---
        VMware (85.8 MB) | [Versão mais recente](https://go.microsoft.com/fwlink/?linkid=2140337) | 2daaa2a59302bf911e8ef195d7d7c83527a9af0b860e2a627979085ca

### <a name="3-create-the-appliance-server"></a>3. Criar o servidor do aparelho

Importe o ficheiro descarregado e crie um servidor em ambiente VMware

1. Na consola vSphere Client, clique em   >  **Ficheiros Implementar OVF Modelo**.
2. No Assistente de Modelo de OVF de implementação > **Fonte**, especifique a localização do ficheiro OVA.
3. No **Nome** e **Localização**, especifique um nome amigável para o servidor. Selecione o objeto de inventário no qual o servidor será hospedado.
5. No **Host/Cluster**, especifique o anfitrião ou o cluster no qual o servidor irá funcionar.
6. No **Armazenamento**, especifique o destino de armazenamento para o servidor.
7. Em **Formato do Disco**, especifique o tipo e o tamanho do disco.
8. No **Mapeamento de Rede,** especifique a rede à qual o servidor se ligará. A rede precisa de conectividade na Internet, para enviar metadados para a Azure Migrate.
9. Reveja e confirme as definições e, em seguida, clique em **Concluir**.

### <a name="verify-appliance-access-to-azure"></a>Verifique o acesso do aparelho ao Azure

Certifique-se de que o servidor do aparelho pode ligar-se aos URLs Azure para nuvens [públicas](migrate-appliance.md#public-cloud-urls) e [governamentais.](migrate-appliance.md#government-cloud-urls)

### <a name="4-configure-the-appliance"></a>4. Configurar o aparelho

Coloque o aparelho pela primeira vez.

> [!NOTE]
> Se configurar o aparelho utilizando um [**script PowerShell**](deploy-appliance-script.md) em vez do OVA descarregado, os dois primeiros passos deste procedimento não são relevantes.

1. Na consola vSphere Client, clique com o botão direito no servidor e, em seguida, selecione **Open Console**.
2. Forneça o idioma, o fuso horário e a palavra-passe para o aparelho.
3. Abra um browser em qualquer máquina que possa ligar ao aparelho e abra o URL do gestor de configuração do aparelho: `https://appliance name or IP address: 44368` .

   Em alternativa, pode abrir o gestor de configuração a partir do ambiente de trabalho do servidor do aparelho selecionando o atalho para o gestor de configuração.
1. Aceite os termos da **licença** e leia as informações de terceiros.
1. No gestor de configuração > **Configurar pré-requisitos,** faça o seguinte:
   - **Conectividade**: O aparelho verifica se o servidor tem acesso à Internet. Se o servidor utilizar um representante:
     - Clique no **representante de configuração** para especificar o endereço de procuração `http://ProxyIPAddress` ou a porta de `http://ProxyFQDN` escuta.
     - Especifique as credenciais se o proxy precisar de autenticação.
     - Apenas é suportado o proxy HTTP.
     - Se tiver adicionado detalhes de procuração ou desativado o proxy e/ou autenticação, clique em **Guardar** para ativar novamente a verificação de conectividade.
   - **Sincronização de tempo**: O tempo do aparelho deve estar sincronizado com o tempo de internet para que a descoberta funcione corretamente.
   - **Instalação de atualizações**: O aparelho assegura a instalação das atualizações mais recentes. Depois de concluída a verificação, pode clicar nos **serviços do aparelho ver** o estado e as versões dos serviços em execução no servidor do aparelho.
   - **Instalação VDDK**: O aparelho verifica se o Kit de Desenvolvimento de Discos Virtuais VMware vSphere (VDDK) está instalado. Se não estiver instalado, faça o download do VDDK 6.7 da VMware e extraia o conteúdo zip descarregado para o local especificado no aparelho, conforme indicado nas **instruções de instalação**.

     A migração do servidor Azure Migrate usa o VDDK para replicar servidores durante a migração para Azure. 
1. Se desejar, pode **repetir os pré-requisitos** a qualquer momento durante a configuração do aparelho para verificar se o aparelho cumpre todos os requisitos.

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-prerequisites.png" alt-text="Painel 1 no gestor de configuração do aparelho":::

### <a name="register-the-appliance-with-azure-migrate"></a>Registe o aparelho com a Azure Migrate

1. Cole a chave do **projeto** copiada do portal. Se não tiver a chave, vá ao **Azure Migrate: Descoberta e avaliação> Descubra> Gerir os aparelhos existentes**, selecione o nome do aparelho que forneceu no momento da geração chave e copie a chave correspondente.
1. Necessitará de um código de dispositivo para autenticar com o Azure. Clicar no **Login** abrirá um código modal com o código do dispositivo, como mostrado abaixo.

    :::image type="content" source="./media/tutorial-discover-vmware/device-code.png" alt-text="Modal mostrando o código do dispositivo":::

1. Clique no **código copy & Iniciar sessão** para copiar o código do dispositivo e abrir um pedido de Login Azure num novo separador de navegador. Se não aparecer, certifique-se de ter desativado o bloqueador pop-up no navegador.
1. No novo separador, cole o código do dispositivo e inscreva-se utilizando o seu nome de utilizador Estaure e a palavra-passe.
   
   O s-in com um PIN não é suportado.
3. Caso feche o separador de login acidentalmente sem iniciar sessão, é necessário atualizar o separador de navegador do gestor de configuração do aparelho para ativar novamente o botão Iniciar sessão.
1. Depois de iniciar sessão com sucesso, volte ao separador anterior com o gestor de configuração do aparelho.
1. Se a conta de utilizador Azure utilizada para a exploração madeireira tiver as permissões certas sobre os recursos Azure criados durante a geração chave, o registo do aparelho será iniciado.
1. Depois de o aparelho estar registado com sucesso, pode ver os dados do registo clicando nos **detalhes do Ver.**

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-registration.png" alt-text="Painel 2 no gestor de configuração do aparelho":::

## <a name="start-continuous-discovery"></a>Iniciar a descoberta contínua

### <a name="provide-vcenter-server-details"></a>Fornecer detalhes do servidor vCenter

O aparelho precisa de se ligar ao servidor vCenter para descobrir a configuração e os dados de desempenho dos servidores.

1. No **passo 1: Forneça credenciais do servidor vCenter**, clique em **Adicionar credenciais** para especificar um nome amigável para credenciais, adicionar **nome de utilizador** e **palavra-passe** para a conta vCenter Server que o aparelho utilizará para descobrir servidores em execução no servidor vCenter.
    - Deveria ter criado uma conta com as permissões necessárias, conforme coberto neste artigo acima.
    - Se pretender estender a descoberta a objetos VMware específicos (centros de dados vCenter Server, clusters, uma pasta de clusters, anfitriões, uma pasta de anfitriões ou servidores individuais.), reveja as instruções [deste artigo](set-discovery-scope.md) para restringir a conta utilizada pela Azure Migrate.
1. No **passo 2: Forneça detalhes do servidor vCenter**, clique na **fonte de descoberta Adicionar** para selecionar o nome amigável para credenciais a partir do drop-down, especificar o endereço **IP/FQDN** do servidor vCenter. Pode deixar a **Porta** para predefinição (443) ou especificar uma porta personalizada na qual o vCenter Server ouve e clica em **Guardar**.
1. Ao clicar em **Guardar**, o aparelho tentará validar a ligação ao servidor vCenter com as credenciais fornecidas e mostrar o **estado de Validação** na tabela contra o endereço IP/FQDN do servidor vCenter.
1. Pode **revalidar** a conectividade ao vCenter Server a qualquer momento antes de iniciar a descoberta.

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-manage-sources.png" alt-text="Painel 3 no gestor de configuração do aparelho para detalhes do servidor vCenter":::

### <a name="provide-server-credentials"></a>Fornecer credenciais de servidor

No **Passo 3: Fornecer credenciais de servidor para realizar inventário de software, análise de dependência de agente e descoberta de instâncias e bases de dados do SQL Server,** pode optar por fornecer múltiplas credenciais de servidor ou se não quiser utilizar estas funcionalidades, pode optar por saltar o passo e proceder à descoberta do vCenter Server. Pode mudar a sua intenção a qualquer momento.

:::image type="content" source="./media/tutorial-discover-vmware/appliance-server-credentials-mapping.png" alt-text="Painel 3 no gestor de configuração do aparelho para detalhes do servidor":::

Se pretender utilizar estas funcionalidades, pode fornecer credenciais de servidor seguindo os passos abaixo. O aparelho tentará mapear automaticamente as credenciais para os servidores para executar as funcionalidades de descoberta.

- Pode adicionar credenciais de servidor clicando no botão **Adicionar Credenciais.** Isto abrirá um modelol onde pode escolher o **tipo de Credenciais** a partir do drop-down.
- Pode fornecer credenciais de autenticação de domínio/ Windows (não domínio)/ Linux (não domínio)/ Credenciais de autenticação do SQL Server. [Saiba mais](add-server-credentials.md) sobre como fornecer credenciais e como as lidamos.
- Para cada tipo de credenciais, é necessário especificar um nome amigável para credenciais, adicionar **username** e **Password** e clicar em **Guardar**.
- Se escolher credenciais de domínio, também terá de especificar o FQDN para o domínio. O FQDN é necessário para validar a autenticidade das credenciais com o Diretório Ativo desse domínio.
- Reveja as [permissões necessárias](add-server-credentials.md#required-permissions) na conta para a descoberta de aplicações instaladas, análise de dependência de agentes ou para descoberta de instâncias e bases de dados do SQL Server.
- Se quiser adicionar várias credenciais ao mesmo tempo, clique em **Adicionar mais** para guardar e adicionar mais credenciais.
- Ao clicar em **Guardar** ou **Adicionar mais,** o aparelho valida as credenciais de domínio com o Diretório Ativo do domínio para a sua autenticidade. Isto é feito para evitar qualquer bloqueio de conta quando o aparelho faz várias iterações para mapear credenciais para os respetivos servidores.
- Pode ver o **estado de validação** de todas as credenciais de domínio na tabela de credenciais. Apenas as credenciais de domínio serão validadas.
- Se a validação falhar, pode clicar no estado **falhado** para ver o erro encontrado e clicar em **credenciais de Revalidato** depois de corrigir o problema para validar novamente as credenciais de domínio falhadas.

     :::image type="content" source="./media/tutorial-discover-vmware/add-server-credentials-multiple.png" alt-text="Painel 3 no gestor de configuração do aparelho para fornecer múltiplas credenciais":::

### <a name="start-discovery"></a>Iniciar a deteção

1. Clique em **Iniciar a descoberta**, para iniciar a descoberta do servidor vCenter. Após a descoberta ter sido iniciada com sucesso, pode verificar o estado de descoberta com o endereço IP/FQDN do vCenter Server na tabela de fontes.
1. Se tiver fornecido credenciais de servidor, o inventário de software (descoberta de aplicações instaladas) será iniciado automaticamente após a descoberta do vCenter Server ter concluído. O inventário de software é realizado uma vez a cada 12 horas.
1. [O inventário](how-to-discover-applications.md) de software identifica as instâncias do SQL Server em execução nos servidores e utilizando as informações, o aparelho tenta ligar-se às instâncias através da autenticação do Windows ou das credenciais de autenticação do SQL Server fornecidas no aparelho e recolher dados nas bases de dados do SQL Server e suas propriedades. A descoberta SQL é realizada uma vez a cada 24 horas.
1. Durante o inventário do software, as credenciais de servidores adicionados serão iteradas contra servidores e validadas para análise de dependência de agente. Pode ativar a análise de dependência de agentes para servidores a partir do portal. Apenas os servidores onde a validação é bem sucedida podem ser selecionados para permitir a análise da dependência de agentes.

> [!Note]
>A Azure Migrate encriptará a comunicação entre o aparelho Azure Migrate e as instâncias do SQL Server de origem (com a propriedade de ligação encriptada definida para TRUE). Estas ligações são encriptadas com [**TrustServerCertificate**](/dotnet/api/system.data.sqlclient.sqlconnectionstringbuilder.trustservercertificate) (definido para TRUE); a camada de transporte utilizará o SSL para encriptar o canal e contornar a cadeia de certificados para validar a confiança. O servidor do aparelho deve ser criado para [**confiar na autoridade principal do certificado**](/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine).<br/>
Se não tiver sido fornectado nenhum certificado no servidor quando este começa, o SQL Server gera um certificado auto-assinado que é utilizado para encriptar pacotes de login. [**Saiba mais**](/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine).

A descoberta funciona da seguinte forma:

- Leva cerca de 15 minutos para o inventário de servidores descobertos aparecer no portal.
- A descoberta de aplicações instaladas pode demorar algum tempo. A duração depende do número de servidores descobertos. Para 500 servidores, leva aproximadamente uma hora para que o inventário descoberto apareça no portal Azure Migrate.
- Após a descoberta dos servidores estar concluída, pode ativar a análise de dependência sem agente nos servidores a partir do portal.
- Os dados do SQL Server e das bases de dados começarão a aparecer no portal dentro de 24 horas a partir do início da descoberta.

## <a name="next-steps"></a>Passos seguintes

- [Avaliar servidores](./tutorial-assess-vmware-azure-vm.md) para migração para VMs Azure.
- [Avaliar os Servidores SQL](./tutorial-assess-sql.md) para a migração para a Azure SQL.
- [Reveja os dados](migrate-appliance.md#collected-data---vmware) que o aparelho recolhe durante a descoberta.
