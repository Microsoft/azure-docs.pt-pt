---
title: Descubra servidores em Hiper-V com Azure Migrate Discovery e avaliação
description: Saiba como descobrir servidores no local em Hiper-V com a ferramenta Azure Migrate Discovery e avaliação.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 03/10/2021
ms.custom: mvc
ms.openlocfilehash: ff83b488a6e3193eee8cb12af7de0a60b42e4c75
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104771401"
---
# <a name="tutorial-discover-servers-running-on-hyper-v-with-azure-migrate-discovery-and-assessment"></a>Tutorial: Descubra servidores em execução em Hiper-V com Azure Migrate: Descoberta e avaliação

Como parte da sua viagem de migração para Azure, você descobre o seu inventário no local e cargas de trabalho. 

Este tutorial mostra-lhe como descobrir servidores no local em anfitriões Hiper-V com a ferramenta Azure Migrate: Discovery and assessment, utilizando um aparelho Azure Migrate leve. Implanta o aparelho como servidor no anfitrião Hyper-V, para descobrir continuamente metadados de máquina e desempenho.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma conta Azure
> * Prepare o ambiente Hiper-V para ser descoberto.
> * Criar um projeto.
> * Instale o aparelho Azure Migrate.
> * Comece a descoberta contínua.

> [!NOTE]
> Os tutoriais mostram o caminho mais rápido para experimentar um cenário e usam opções padrão.  

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este tutorial, verifique se tem estes pré-requisitos no lugar.

**Requisito** | **Detalhes**
--- | ---
**Anfitrião Hyper-V** | Os anfitriões hiper-V nos quais os servidores estão localizados podem ser autónomos ou num cluster.<br/><br/> O anfitrião deve estar a executar o Windows Server 2019, o Windows Server 2016 ou o Windows Server 2012 R2.<br/><br/> Verifique se as ligações de entrada são permitidas na porta WinRM 5985 (HTTP), para que o aparelho possa ligar-se para puxar metadados do servidor e dados de desempenho, utilizando uma sessão do Modelo de Informação Comum (CIM).
**Implantação do aparelho** | O anfitrião hiper-V necessita de recursos para alocar um servidor para o aparelho:<br/><br/> - 16 GB de RAM, 8 vCPUs e cerca de 80 GB de armazenamento em disco.<br/><br/> - Um interruptor virtual externo e acesso à Internet no aparelho, diretamente ou através de um representante.
**Servidores** | Os servidores podem estar a executar qualquer sistema operativo Windows ou Linux. 

## <a name="prepare-an-azure-user-account"></a>Preparar uma conta de utilizador Azure

Para criar um projeto e registar o aparelho Azure Migrate, precisa de uma conta com:
- Permissões de colaborador ou proprietário numa subscrição do Azure.
- Permissões para registar aplicações Azure Ative Directory (AAD).

Se acabou de criar uma conta gratuita do Azure, é o proprietário da sua subscrição. Se não for o proprietário da subscrição, trabalhe com o proprietário para atribuir as permissões da seguinte forma:

1. No portal Azure, procure por "subscrições", e em **Serviços,** **selecione Subscrições**.

    ![Caixa de pesquisa para procurar a subscrição do Azure](./media/tutorial-discover-hyper-v/search-subscription.png)

2. Na página **Subscrições,** selecione a subscrição na qual pretende criar um projeto. 
3. Na subscrição, selecione **Access Control (IAM)**  >  **Verifique o acesso**.
4. No **Acesso ao Cheques,** procure na conta de utilizador relevante.
5. In **Add a role assignment**, clique em **Adicionar**.

    ![Procure uma conta de utilizador para verificar o acesso e atribuir uma função](./media/tutorial-discover-hyper-v/azure-account-access.png)

6. Na **atribuição de funções Adicionar**, selecione a função Contribuinte ou Proprietário e selecione a conta (azmigrateuser no nosso exemplo). Em seguida, clique em **Guardar**.

    ![Abre a página de atribuição de Função Adicionar para atribuir uma função à conta](./media/tutorial-discover-hyper-v/assign-role.png)

1. Para registar o aparelho, a sua conta Azure necessita de **permissões para registar aplicações AAD.**
1. No portal Azure, navegue para as Definições de Utilizador de Utilizadores **do Diretório Ativo Azure**  >    >  .
1. Nas **definições do Utilizador,** verifique se os utilizadores de Ad Azure podem registar aplicações (definidas para **Sim** por predefinição).

    ![Verifique nas Definições do Utilizador que os utilizadores podem registar aplicações de Ative Directory](./media/tutorial-discover-hyper-v/register-apps.png)

9. Caso as definições de 'Registos de aplicações' sejam definidas como 'Não', solicite ao arrendatário/administrador global que atribua a permissão necessária. Em alternativa, o administrador inquilino/global pode atribuir o papel **de Desenvolvedor de Aplicações** a uma conta para permitir o registo da App AAD. [Saiba mais](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="prepare-hyper-v-hosts"></a>Preparar anfitriões Hiper-V

Pode preparar os anfitriões Hyper-V manualmente ou usar um script. Os passos de preparação são resumidos na tabela. O script prepara-os automaticamente.

**Passo** | **Roteiro** | **Manual**
--- | --- | ---
Verificar os requisitos do anfitrião | Verifica se o anfitrião está a executar uma versão suportada do Hyper-V e o papel de Hiper-V.<br/><br/>Ativa o serviço WinRM e abre as portas 5985 (HTTP) e 5986 (HTTPS) no anfitrião (necessário para a recolha de metadados). | O anfitrião deve estar a executar o Windows Server 2019, o Windows Server 2016 ou o Windows Server 2012 R2.<br/><br/> Verifique se as ligações de entrada são permitidas na porta WinRM 5985 (HTTP), para que o aparelho possa ligar-se para puxar metadados do servidor e dados de desempenho, utilizando uma sessão do Modelo de Informação Comum (CIM).<br/><br/> O guião não é suportado atualmente em anfitriões com um local não inglês.  
Verificar a versão PowerShell | Verifica se está a executar o script numa versão suportada do PowerShell. | Verifique se está a executar a versão 4.0 do PowerShell ou mais tarde no anfitrião Hyper-V.
Criar uma conta | Verifica se tem as permissões corretas no anfitrião Do Hiper-V.<br/><br/> Permite-lhe criar uma conta de utilizador local com as permissões corretas. | Opção 1: Preparar uma conta com o acesso do Administrador à máquina de anfitrião Hyper-V.<br/><br/> Opção 2: Preparar uma conta de administração local, ou conta Dedmin de Domínio, e adicionar a conta a estes grupos: Utilizadores de Gestão Remota, Administradores de Hiper-V e Utilizadores de Monitores de Desempenho.
Ativar a remoing powerShell | Ativa a remoagem powerShell no hospedeiro, de modo a que o aparelho Azure Migrate possa executar comandos PowerShell no hospedeiro, sobre uma ligação WinRM. | Para configurar, em cada anfitrião, abra uma consola PowerShell como administrador, e executar este comando: ``` powershell Enable-PSRemoting -force ```
Criar serviços de integração Hyper-V | Verifica se os Serviços de Integração Hiper-V estão ativados em todos os servidores geridos pelo anfitrião. | [Ativar os Serviços de Integração Hiper-V](/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services) em cada servidor.<br/><br/> Se estiver a executar o Windows Server 2003, [siga estas instruções](prepare-windows-server-2003-migration.md).
Credenciais de delegado se os discos de servidor estiverem localizados em ações remotas de SMB | Credenciais de delegados | Executar este comando para permitir que o CredSSP delegue credenciais em anfitriões que executam servidores com discos em ações SMB: ```powershell Enable-WSManCredSSP -Role Server -Force ```<br/><br/> Pode executar este comando remotamente em todos os anfitriões Do Hiper-V.<br/><br/> Se adicionar novos nós de anfitrião num cluster, são automaticamente adicionados para serem descobertos, mas precisa de ativar o CredSSP manualmente.<br/><br/> Ao configurar o aparelho, termine de configurar o CredSSP, [colocando-o no aparelho](#delegate-credentials-for-smb-vhds). 

### <a name="run-the-script"></a>Executar o script

1. Descarregue o script do [Microsoft Download Center](https://aka.ms/migrate/script/hyperv). O guião é assinado criptograficamente pela Microsoft.
2. Valide a integridade do script utilizando ficheiros de haxixe MD5 ou SHA256. Os valores da hashtag estão abaixo. Executar este comando para gerar o haxixe para o script:

    ```powershell
    C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]
    ```
    Utilização de exemplo:

    ```powershell
    C:\>CertUtil -HashFile C:\Users\Administrators\Desktop\ MicrosoftAzureMigrate-Hyper-V.ps1 SHA256
    ```
3. Depois de validar a integridade do script, execute o script em cada anfitrião Hyper-V com este comando PowerShell:

    ```powershell
    PS C:\Users\Administrators\Desktop> MicrosoftAzureMigrate-Hyper-V.ps1
    ```
Os valores do haxixe são:

**Hash** |  **Valor**
--- | ---
MD5 | 0ef418f31915d01f896ac42a80dc414e
SHA256 | 0ad60e7299925eff4d1ae9f1c7db485dc9316ef45b0964148a3c07c80761ade2

## <a name="set-up-a-project"></a>Criar um projeto

Crie um novo projeto.

1. No portal do Azure > **Todos os serviços**, procure **Azure Migrate**.
2. Em **Serviços**, selecione **Azure Migrate**.
3. Em **Visão Geral**, selecione **Criar projeto**.
5. No **projeto Create,** selecione o seu grupo de subscrição e recursos Azure. Crie um grupo de recursos se não tiver um.
6. Em **Detalhes do Projeto,** especifique o nome do projeto e a geografia em que pretende criar o projeto. Reveja geografias apoiadas para nuvens [públicas](migrate-support-matrix.md#supported-geographies-public-cloud) e [governamentais.](migrate-support-matrix.md#supported-geographies-azure-government)

   ![Caixas para nome e região do projeto](./media/tutorial-discover-hyper-v/new-project.png)

7. Selecione **Criar**.
8. Espere alguns minutos para o projeto ser lançado. A **ferramenta Azure Migrate: Discovery and assessment** é adicionada por defeito ao novo projeto.

![Página mostrando Azure Migrate: Ferramenta de descoberta e avaliação adicionada por padrão](./media/tutorial-discover-hyper-v/added-tool.png)

> [!NOTE]
> Se já criou um projeto, pode utilizar o mesmo projeto para registar aparelhos adicionais para descobrir e avaliar mais nenhum dos servidores. [Saiba mais](create-manage-projects.md#find-a-project)

## <a name="set-up-the-appliance"></a>Configurar o aparelho

A Azure Migrate utiliza um aparelho Azure Migrate leve. O aparelho executa a descoberta do servidor e envia metadados de configuração e desempenho do servidor para a Azure Migrate. O aparelho pode ser configurado através da implantação de um ficheiro VHD que pode ser descarregado do projeto.

> [!NOTE]
> Se por alguma razão não conseguir configurar o aparelho utilizando o modelo, pode alterá-lo utilizando um script PowerShell num servidor do Windows Server 2016 existente. [Saiba mais](deploy-appliance-script.md#set-up-the-appliance-for-hyper-v).

Este tutorial configura o aparelho num servidor em funcionamento em ambiente Hiper-V, da seguinte forma:

1. Forneça um nome de aparelho e gere uma chave de projeto no portal.
1. Faça o download de um Hiper-VHD comprimido do portal Azure.
1. Crie o aparelho e verifique se pode ligar-se ao Azure Migrate: Descoberta e avaliação.
1. Configure o aparelho pela primeira vez e registe-o com o projeto utilizando a chave do projeto.

### <a name="1-generate-the-project-key"></a>1. Gerar a chave do projeto

1. Em **Objetivos de Migração**  >  **Servidores**  >  **Azure Migrate: Descoberta e avaliação**, selecione **Discover**.
2. In **Discover Servers**  >  **Are your servers virtualized?** 
3. Em **1:Gere a tecla do projeto,** forneça um nome para o aparelho Azure Migrate que irá configurar para a descoberta dos servidores. O nome deve ser alfanumérico com 14 caracteres ou menos.
1. Clique na **chave Gerar** para iniciar a criação dos recursos Azure necessários. Por favor, não feche a página do servidor Discover durante a criação de recursos.
1. Após a criação bem sucedida dos recursos Azure, gera-se uma **chave de projeto.**
1. Copie a chave pois necessitará para completar o registo do aparelho durante a sua configuração.

### <a name="2-download-the-vhd"></a>2. Descarregue o VHD

Em **2: Descarregue o aparelho Azure Migrate,** selecione o . Ficheiro VHD e clique no **Download**.

### <a name="verify-security"></a>Verificar segurança

Verifique se o ficheiro com fecho está seguro, antes de o colocar.

1. No computador para o qual transferiu o ficheiro, abra uma janela de comando de administrador.

2. Executar o seguinte comando PowerShell para gerar o haxixe para o ficheiro ZIP
    - ```C:\>Get-FileHash -Path <file_location> -Algorithm [Hashing Algorithm]```
    - Utilização de exemplo: ```C:\>Get-FileHash -Path ./AzureMigrateAppliance_v3.20.09.25.zip -Algorithm SHA256```

3.  Verifique as versões mais recentes do aparelho e os valores do haxixe:

    - Para a nuvem pública de Azure:

        **Cenário** | **Transferência** | **SHA256**
        --- | --- | ---
        Hiper-V (8,91 GB) | [Versão mais recente](https://go.microsoft.com/fwlink/?linkid=2140422) |  40a037987777179428b1c6ebee2614b092e69ac56d48a2bbc75eeef86c99a

    - Para o Governo de Azure:

        **Cenário** _ | _ *Baixar** | **SHA256**
        --- | --- | ---
        Hiper-V (85,8 MB) | [Versão mais recente](https://go.microsoft.com/fwlink/?linkid=2140424) |  cfed44bb52c9ab3024a628dc7a5d0df8c624f156ec1ecc350716bae30b257f

### <a name="3-create-an-appliance"></a>3. Criar um aparelho

Importe o ficheiro descarregado e crie um aparelho.

1. Extraia o ficheiro VHD com fecho para uma pasta no hospedeiro Hyper-V que irá hospedar o aparelho. Três pastas são extraídas.
2. Abra o Gestor de Hyper-V. Em **Ações,** clique em **Import Virtual Machine**.
2. No > do assistente de máquina virtual de importação **Antes de começar,** clique em **seguida**.
3. Na **Pasta Localizar**, especifique a pasta que contém o VHD extraído. Em seguida, clique em **Seguinte**.
1. Na **Máquina Virtual Selecionada,** clique em **Seguinte**.
2. No **Choose Import Type,** clique **em Copiar a máquina virtual (crie um novo ID único)**. Em seguida, clique em **Seguinte**.
3. No **Destino Escolha,** deixe a definição predefinida. Clique em **Seguinte**.
4. Nas **pastas de armazenamento,** deixe a definição predefinida. Clique em **Seguinte**.
5. Na **Choose Network**, especifique o interruptor virtual que o aparelho irá utilizar. O switch precisa de conectividade na Internet para enviar dados para o Azure.
6. Em **Resumo,** reveja as definições. Em seguida, clique em **Concluir**.
7. No Hyper-V Manager > **Máquinas Virtuais,** ligue o aparelho.

### <a name="verify-appliance-access-to-azure"></a>Verifique o acesso do aparelho ao Azure

Certifique-se de que o aparelho pode ligar-se aos URLs Azure para nuvens [públicas](migrate-appliance.md#public-cloud-urls) e [governamentais.](migrate-appliance.md#government-cloud-urls)

### <a name="4-configure-the-appliance"></a>4. Configurar o aparelho

Coloque o aparelho pela primeira vez.

> [!NOTE]
> Se configurar o aparelho utilizando um [script PowerShell](deploy-appliance-script.md) em vez do VHD descarregado, os dois primeiros passos deste procedimento não são relevantes.

1. No Hyper-V Manager > **Máquinas Virtuais,** clique com o botão direito > **Connect**.
2. Forneça o idioma, o fuso horário e a palavra-passe para o aparelho.
3. Abra um browser em qualquer máquina que possa ligar ao aparelho e abra o URL da aplicação web do aparelho: **https:// nome do aparelho ou endereço *IP*: 44368**.

   Em alternativa, pode abrir a aplicação a partir do ambiente de trabalho do aparelho clicando no atalho da aplicação.
1. Aceite os termos da **licença** e leia as informações de terceiros.
1. Na aplicação web > **Configurar pré-requisitos,** faça o seguinte:
    - **Conectividade**: A aplicação verifica se o aparelho tem acesso à Internet. Se o aparelho utilizar um representante:
      - Clique no **proxy configurar** e especifique o endereço de procuração (no formulário http://ProxyIPAddress ou na porta de http://ProxyFQDN) audição.
      - Especifique as credenciais se o proxy precisar de autenticação.
      - Apenas é suportado o proxy HTTP.
      - Se tiver adicionado detalhes de procuração ou desativado o proxy e/ou autenticação, clique em **Guardar** para ativar novamente a verificação de conectividade.
    - **Sincronização temporal:** O tempo é verificado. O tempo do aparelho deve estar sincronizado com o tempo de internet para que a descoberta do servidor funcione corretamente.
    - **Instalar atualizações**: Azure Migrate: A descoberta e avaliação verificam se o aparelho tem as últimas atualizações instaladas. Depois de concluída a verificação, pode clicar nos **serviços do aparelho para** ver o estado e as versões dos componentes em funcionamento no aparelho.

### <a name="register-the-appliance-with-azure-migrate"></a>Registe o aparelho com a Azure Migrate

1. Cole a chave do **projeto** copiada do portal. Se não tiver a chave, vá ao **Azure Migrate: Descoberta e avaliação> Descubra> Gerir os aparelhos existentes**, selecione o nome do aparelho que forneceu no momento da geração chave e copie a chave correspondente.
1. Necessitará de um código de dispositivo para autenticar com o Azure. Clicar no **Login** abrirá um código modal com o código do dispositivo, como mostrado abaixo.

    ![Modal mostrando o código do dispositivo](./media/tutorial-discover-vmware/device-code.png)

1. Clique no **código copy & Iniciar sessão** para copiar o código do dispositivo e abrir um pedido de Login Azure num novo separador de navegador. Se não aparecer, certifique-se de ter desativado o bloqueador pop-up no navegador.
1. No novo separador, cole o código do dispositivo e inscreva-se utilizando o seu nome de utilizador Estaure e a palavra-passe.
   
   O s-in com um PIN não é suportado.
3. Caso feche o separador de login acidentalmente sem iniciar sessão, é necessário atualizar o separador de navegador do gestor de configuração do aparelho para ativar novamente o botão Iniciar sessão.
1. Depois de iniciar sessão com sucesso, volte ao separador anterior com o gestor de configuração do aparelho.
4. Se a conta de utilizador Azure utilizada para a exploração madeireira tiver as permissões certas sobre os recursos Azure criados durante a geração chave, o registo do aparelho será iniciado.
1. Depois de o aparelho estar registado com sucesso, pode ver os dados do registo clicando nos **detalhes do Ver.**

### <a name="delegate-credentials-for-smb-vhds"></a>Credenciais de delegado para VHDs SMB

Se estiver a executar VHDs em SMBs, deve ativar a delegação de credenciais do aparelho para os anfitriões Hiper-V. Para o fazer a partir do aparelho:

1. No aparelho, este comando. HyperVHost1/HyperVHost2 são nomes de anfitriões exemplo.

    ```
    Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com, HyperVHost2.contoso.com, HyperVHost1, HyperVHost2 -Force
    ```

2. Em alternativa, faça-o no Editor de Política do Grupo Local sobre o aparelho:
    - Na   >  **configuração** do computador local, clique na delegação de credenciais **de sistema de modelos**  >    >  **administrativos**.
    - Clique duas **vezes Em permitir a delegação de credenciais frescas** e selecione **Ativado**.
    - Em **Opções**, clique em **Mostrar**, e adicione cada anfitrião Hyper-V que pretende descobrir na lista, com **wsman/** como prefixo.
    - Na  **Delegação de Credenciais,** clique duplo **Deixe delegar credenciais frescas com autenticação do servidor apenas NTLM**. Mais uma vez, adicione cada anfitrião Hyper-V que pretende descobrir na lista, com **wsman/** como prefixo.

## <a name="start-continuous-discovery"></a>Iniciar a descoberta contínua

Ligue do aparelho a anfitriões ou agrupamentos Hiper-V e inicie a descoberta do servidor.

1. No **passo 1: Forneça credenciais de anfitrião Hyper-V**, clique em **Adicionar credenciais** para especificar um nome amigável para credenciais, adicionar **nome de utilizador** e **palavra-passe** para um anfitrião/cluster Hyper-V que o aparelho utilizará para descobrir servidores. Clique em **Guardar**.
1. Se quiser adicionar várias credenciais ao mesmo tempo, clique em **Adicionar mais** para guardar e adicionar mais credenciais. Várias credenciais são suportadas para a descoberta de servidores em ambiente Hiper-V.
1. No **passo 2: Forneça detalhes do anfitrião/cluster hyper-V,** clique na **fonte de descoberta Add** para especificar o endereço **IP/cluster/FQDN** do hiper-V e o nome amigável para credenciais de ligação ao hospedeiro/cluster.
1. Pode **adicionar um único item** de cada vez ou adicionar **vários itens** de uma só vez. Existe também uma opção para fornecer detalhes de anfitrião/cluster Hiper-V através **do Import CSV**.

    - Se escolher **Adicionar um único item,** tem de especificar o nome amigável para credenciais e o endereço **IP/cluster/FQDN** do anfitrião Hiper-V/cluster e clicar em **Guardar**.
    - Se escolher **Adicionar vários itens** _(selecionados por padrão)_, pode adicionar vários registos ao mesmo tempo, especificando **o endereço IP/cluster/FQDN** do Hiper-V com o nome amigável para credenciais na caixa de texto. **Verifique** os registos adicionados e clique em **Guardar**.
    - Se escolher **O CSV de Importação,** pode descarregar um ficheiro de modelo CSV, preencher o ficheiro com o **endereço IP/FQDN** do anfitrião Hiper-V/cluster e nome amigável para credenciais. Em seguida, importa o ficheiro para o aparelho, **verifique** os registos do ficheiro e clique em **Guardar**.

1. Ao clicar em Guardar, o aparelho tentará validar a ligação aos anfitriões/clusters hiper-V adicionados e mostrar o estado de **Validação** na tabela contra cada hospedeiro/cluster.
    - Para anfitriões/clusters validados com sucesso, pode ver mais detalhes clicando no seu endereço IP/FQDN.
    - Se a validação falhar para um anfitrião, reveja o erro clicando na **Validação falhada** na coluna Status da tabela. Corrija o problema e valide novamente.
    - Para remover anfitriões ou agrupamentos, clique em **Apagar**.
    - Não se pode remover um hospedeiro específico de um aglomerado. Só se pode remover todo o aglomerado.
    - Pode adicionar um cluster, mesmo que existam problemas com anfitriões específicos no cluster.
1. Pode **revalidar** a conectividade aos anfitriões/agrupamentos a qualquer momento antes de iniciar a descoberta.
1. Clique em **Iniciar a descoberta**, para iniciar a descoberta do servidor a partir dos anfitriões/clusters validados com sucesso. Após a descoberta ter sido iniciada com sucesso, pode verificar o estado de descoberta contra cada hospedeiro/cluster na tabela.

Isto começa a ser descoberto. Leva aproximadamente 2 minutos por anfitrião para que os metadados dos servidores descobertos apareçam no portal Azure.

## <a name="verify-servers-in-the-portal"></a>Verificar servidores no portal

Após o fim da descoberta, pode verificar se os servidores aparecem no portal.

1. Abra o dashboard do Azure Migrate.
2. Em **Azure Migrate - Servidores**  >  **Azure Migrate: Página de descoberta e avaliação,** clique no ícone que exibe a contagem para **servidores descobertos**.

## <a name="next-steps"></a>Passos seguintes

- [Avaliar servidores em ambiente Hiper-V](tutorial-assess-hyper-v.md) para migração para VMs Azure.
- [Reveja os dados](migrate-appliance.md#collected-data---hyper-v) que o aparelho recolhe durante a descoberta.


