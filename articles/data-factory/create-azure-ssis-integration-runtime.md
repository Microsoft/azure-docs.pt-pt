---
title: Criar um tempo de integração Azure-SSIS na Azure Data Factory
description: Saiba como criar um tempo de integração Azure-SSIS na Azure Data Factory para que possa implementar e executar pacotes SSIS em Azure.
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/22/2021
author: swinarko
ms.author: sawinark
ms.openlocfilehash: 4b26abe1d1340e4e8c5f034fad72f612f0b246a2
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101739419"
---
# <a name="create-an-azure-ssis-integration-runtime-in-azure-data-factory"></a>Criar um tempo de integração Azure-SSIS na Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este artigo fornece passos para o provisionamento de um tempo de integração de servidores Azure-SQL (SSIS) na Azure Data Factory (ADF). Um Azure-SSIS IR suporta:

- Pacotes de execução implantados no catálogo SSIS (SSISDB) hospedados pelo servidor de base de dados Azure SQL/Gestão de Instâncias (Modelo de Implementação de Projetos)
- Executando pacotes implantados no sistema de ficheiros, ficheiros Azure ou base de dados do SQL Server (MSDB) hospedada por Azure SQL Managed Instance (Modelo de Implementação de Pacotes)

Depois de ser alojado um Azure-SSIS IR, pode utilizar ferramentas familiares para implantar e executar as suas encomendas em Azure. Estas ferramentas já estão ativadas pelo Azure e incluem ferramentas de dados do servidor SQL (SSDT), SQL Server Management Studio (SSMS) e utilitários de linha de comando como [dtutil](/sql/integration-services/dtutil-utility) e [AzureDTExec](./how-to-invoke-ssis-package-azure-enabled-dtexec.md).

O tutorial [Provisioning Azure-SSIS IR](./tutorial-deploy-ssis-packages-azure.md) mostra como criar um Azure-SSIS IR através do portal Azure ou da app Data Factory. O tutorial também mostra como utilizar opcionalmente um servidor de base de dados Azure SQL ou uma instância gerida para hospedar o SSISDB. Este artigo expande-se no tutorial e descreve como fazer estas tarefas opcionais:

- Utilize um servidor de base de dados Azure SQL com regras de firewall IP/pontos finais de serviço de rede virtual ou uma instância gerida com ponto final privado para hospedar o SSISDB. Como pré-requisito, é necessário configurar permissões e configurações de rede virtuais para que o seu IR Azure-SSIS se junte a uma rede virtual.

- Utilize a autenticação do Azure Ative Directory (Azure AD) com a identidade gerida para a sua fábrica de dados ligar-se a um servidor de base de dados Azure SQL ou a uma instância gerida. Como pré-requisito, precisa de adicionar a identidade gerida para a sua fábrica de dados como um utilizador de base de dados que pode criar uma instância SSISDB.

- Junte-se ao seu Azure-SSIS IR a uma rede virtual, ou configuure um IR auto-hospedado como representante para o seu Azure-SSIS IR para aceder a dados no local.

Este artigo mostra como providenciar um Azure-SSIS IR utilizando o portal Azure PowerShell e um modelo de Gestor de Recursos Azure.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Assinatura Azure**. Se ainda não tiver uma subscrição, pode criar uma conta [de teste gratuita.](https://azure.microsoft.com/pricing/free-trial/)

- **Servidor de base de dados Azure SQL ou SQL Managed Instance (opcional)**. Se ainda não tiver um servidor de base de dados ou uma instância gerida, crie um no portal Azure antes de começar. A Data Factory criará, por sua vez, uma instância SSISDB neste servidor de base de dados. 

  Recomendamos que crie o servidor de base de dados ou caso gerido na mesma região do Azure que o tempo de execução da integração. Esta configuração permite que o tempo de execução da integração escreva os registos de execução em SSISDB sem atravessar regiões de Azure.

  Tenha em mente estes pontos:

  - O exemplo SSISDB pode ser criado em seu nome como uma única base de dados, como parte de uma piscina elástica, ou em um caso gerido. Pode ser acessível numa rede pública ou através da adesão a uma rede virtual. Para obter orientações na escolha entre a Base de Dados SQL e a SQL Managed Instance para hospedar o SSISDB, consulte a secção Compare SQL Database e a secção [SQL Managed Instance](#comparison-of-sql-database-and-sql-managed-instance) neste artigo. 
  
    Se utilizar um servidor Azure SQL Database com regras ip firewall/pontos finais de serviço de rede virtual ou uma instância gerida pelo SQL com ponta final privada para hospedar o SSISDB, ou se necessitar de acesso a dados no local sem configurar um IR auto-hospedado, tem de se juntar ao seu Azure-SSIS IR a uma rede virtual. Para obter mais informações, consulte [Junte-se a uma Azure-SSIS IR a uma rede virtual.](./join-azure-ssis-integration-runtime-virtual-network.md)

  - Confirme que o **acesso a serviços Azure** está ativado para o servidor de base de dados. Esta definição não é aplicável quando utiliza um servidor de base de dados Azure SQL com regras de firewall IP/pontos finais de serviço de rede virtual ou um exemplo gerido pelo SQL com ponto final privado para hospedar o SSISDB. Para mais informações, consulte [a Base de Dados SECURE Azure SQL](../azure-sql/database/secure-database-tutorial.md#create-firewall-rules). Para ativar esta definição utilizando o PowerShell, consulte [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule).

  - Adicione o endereço IP da máquina de cliente, ou uma série de endereços IP que incluem o endereço IP da máquina do cliente, à lista de endereços IP do cliente nas definições de firewall para o servidor de base de dados. Para obter mais informações, veja [Regras de firewall ao nível do servidor da Base de Dados SQL do Azure e ao nível da base de dados](../azure-sql/database/firewall-configure.md).

  - Pode ligar-se ao servidor de base de dados utilizando a autenticação SQL com as credenciais de administração do seu servidor ou utilizando a autenticação AZure AD com a identidade gerida para a sua fábrica de dados. Para este último, é necessário adicionar a identidade gerida para a sua fábrica de dados num grupo AZure AD com permissões de acesso ao servidor de base de dados. Para obter mais informações, consulte [a autenticação Azure AD para um Azure-SSIS IR](./enable-aad-authentication-azure-ssis-ir.md).

  - Confirme que o seu servidor de base de dados ainda não tem uma instância SSISDB. O provisionamento de um IR Azure-SSIS não suporta utilizando uma instância SSISDB existente.

- **Rede virtual Azure Resource Manager (opcional)**. Deve ter uma rede virtual Azure Resource Manager se pelo menos uma das seguintes condições for verdadeira:

  - Está a hospedar o SSISDB num servidor de base de dados Azure SQL com regras de firewall IP/pontos finais de serviço de rede virtual ou um caso gerido com ponto final privado.

  - Pretende ligar-se às lojas de dados no local a partir de pacotes SSIS que estão a decorrer no seu Azure-SSIS IR sem configurar um IR auto-hospedado.

- **Azure PowerShell (opcional)**. Siga as instruções em [Como instalar e configurar a Azure PowerShell,](/powershell/azure/install-az-ps)se pretender executar um script PowerShell para forre o seu Azure-SSIS IR.

### <a name="regional-support"></a>Suporte regional

Para obter uma lista das regiões de Azure em que a Data Factory e um Azure-SSIS IR estão disponíveis, consulte a [Data Factory e a disponibilidade de IR SSIS por região.](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all)

### <a name="comparison-of-sql-database-and-sql-managed-instance"></a>Comparação da Base de Dados SQL e da SqL Gestão de Instâncias

A tabela a seguir compara certas funcionalidades de um servidor de base de dados Azure SQL e da SQL Managed Instance no que diz respeito ao Azure-SSIR IR:

| Funcionalidade | SQL Database| SQL Caso gerido |
|---------|--------------|------------------|
| **Agendamento** | O Agente de Servidor SQL não está disponível.<br/><br/>Consulte [a execução de um pacote num oleoduto da Fábrica de Dados.](/sql/integration-services/lift-shift/ssis-azure-schedule-packages#activity)| O Agente de Instância Gerida está disponível. |
| **Autenticação** | Pode criar um caso SSISDB com um utilizador de base de dados contido que represente qualquer grupo AD Azure com a identidade gerida da sua fábrica de dados como membro na **função db_owner.**<br/><br/>Consulte [a autenticação Azure AD para criar um SSISDB no servidor base de dados Azure SQL](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database). | Pode criar um caso SSISDB com um utilizador de base de dados contido que represente a identidade gerida da sua fábrica de dados. <br/><br/>Consulte [a autenticação Azure AD para criar um SSISDB em Azure SQL Managed Instance](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-sql-managed-instance). |
| **Escalão de serviço** | Quando criar um Azure-SSIS IR com o seu servidor Azure SQL Database, pode selecionar o nível de serviço para SSISDB. Há vários níveis de serviço. | Quando cria um Azure-SSIS IR com a sua instância gerida, não pode selecionar o nível de serviço para SSISDB. Todas as bases de dados do seu caso gerido partilham o mesmo recurso atribuído a esse caso. |
| **Rede virtual** | O seu Azure-SSIS IR pode aderir a uma rede virtual Azure Resource Manager se utilizar um servidor de base de dados Azure SQL com regras de firewall IP/pontos finais de serviço de rede virtual. | O seu Azure-SSIS IR pode aderir a uma rede virtual Azure Resource Manager se utilizar uma instância gerida com um ponto final privado. A rede virtual é necessária quando não ativa um ponto final público para a sua instância gerida.<br/><br/>Se se juntar ao seu Azure-SSIS IR à mesma rede virtual que a sua instância gerida, certifique-se de que o seu Azure-SSIS IR está numa sub-rede diferente da sua instância gerida. Se se juntar ao seu Azure-SSIS IR a uma rede virtual diferente da sua instância gerida, recomendamos um espreitamento de rede virtual ou uma ligação rede-a-rede. Consulte [a sua aplicação a uma base de dados Azure SQL Gestão de Casos](../azure-sql/managed-instance/connect-application-instance.md). |
| **Transações distribuídas** | Esta funcionalidade é suportada através de transações elásticas. As transações do Coordenador de Transações Distribuídas da Microsoft (MSDTC) não são suportadas. Se os seus pacotes SSIS utilizarem o MSDTC para coordenar transações distribuídas, considere migrar para transações elásticas para a Base de Dados Azure SQL. Para obter mais informações, consulte [transações distribuídas através de bases de dados em nuvem.](../azure-sql/database/elastic-transactions-overview.md) | Não suportado. |
| | | |

## <a name="use-the-azure-portal-to-create-an-integration-runtime"></a>Use o portal Azure para criar um tempo de integração

Nesta secção, utiliza-se o portal Azure, especificamente a interface de utilizador da Data Factory (UI) ou a app, para criar um Azure-SSIS IR.

### <a name="create-a-data-factory"></a>Criar uma fábrica de dados

Para criar a sua fábrica de dados através do portal Azure, siga as instruções passo a passo na Criação de uma fábrica de [dados através da UI](./quickstart-create-data-factory-portal.md#create-a-data-factory). Selecione **Pin para o painel de instrumentos** ao fazê-lo, para permitir um acesso rápido após a sua criação. 

Após a criação da sua fábrica de dados, abra a sua página geral no portal Azure. Selecione o **azulejo do Monitor & autor** para abrir a página **Let's get start-page** em separado. Lá, pode continuar a criar o seu Azure-SSIS IR.   

### <a name="provision-an-azure-ssis-integration-runtime"></a>Aprovisionar um runtime de integração Azure-SSIS

Na página **Let's get start,** selecione o azulejo **de configuração do tempo de execução da integração SSIS** para abrir o painel de configuração de tempo de execução da **Integração.**

   ![Configurar o mosaico do Integration Runtime do SSIS](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

   O painel **de configuração do tempo de execução** da Integração tem três páginas onde configura sucessivamente configurar configurar configurações gerais, de implantação e avançadas.

#### <a name="general-settings-page"></a>Página de configurações gerais

Na página geral de **configurações** do painel de configuração do tempo de funcionação da **integração,** complete os seguintes passos.

   ![Definições gerais](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   1. Em **Nome**, introduza o nome do seu runtime de integração.

   2. Em **Descrição**, introduza a descrição do seu runtime de integração.

   3. Em **Localização**, selecione a localização do seu runtime de integração. Apenas são apresentadas as localizações suportadas. Recomendamos que selecione a mesma localização do seu servidor de base de dados para alojar a SSISDB.

   4. Para **o tamanho do nó,** selecione o tamanho do nó no seu cluster de tempo de execução de integração. Apenas são apresentados os tamanhos de nó suportados. Selecione um grande tamanho de nó (escala para cima) se quiser executar muitos pacotes intensivos de computação ou intensivos de memória.
   > [!NOTE]
   > Se necessitar de [isolamento computacional,](../azure-government/azure-secure-isolation-guidance.md#compute-isolation)selecione o tamanho do nó **Standard_E64i_v3.** Este tamanho do nó representa máquinas virtuais isoladas que consomem todo o seu hospedeiro físico e fornecem o nível de isolamento necessário por certas cargas de trabalho, tais como as cargas de trabalho do Nível de Impacto 5 (IL5) do Departamento de Defesa dos EUA.
   
   5. Em **Numero de Nós**, selecione o número de nós do cluster do runtime de integração. Apenas são apresentados os números de nó suportados. Selecione um grande cluster com muitos nós (escala para fora) se quiser executar muitos pacotes em paralelo.

   6. Para **edição/licença,** selecione a edição sql Server para o seu tempo de integração: Standard ou Enterprise. Selecione Enterprise se quiser utilizar funcionalidades avançadas no seu tempo de execução de integração.

   7. Para **economizar dinheiro**, selecione a opção Azure Hybrid Benefit para o seu tempo de integração: **Sim** ou **Não**. Selecione **Sim** se quiser trazer a sua própria licença SQL Server com Software Assurance para beneficiar de economias de custos com uso híbrido.

   8. Selecione **Seguinte**.

#### <a name="deployment-settings-page"></a>Página de definições de implementação

Na página de configurações de **implementação** do painel de configuração do tempo de execução da **integração,** tem as opções para criar lojas de pacotes SSISDB e Azure-SSIS IR.

##### <a name="creating-ssisdb"></a>Criação de SSISDB

Na página de definições de **implementação** do painel de configuração do tempo de execução da **integração,** se pretender implantar as suas encomendas no SSISDB (Modelo de Implementação de Projetos), selecione o **catálogo Create SSIS (SSISDB) hospedado pelo servidor de base de dados Azure SQL/Gestão de Instâncias para armazenar os seus projetos/pacotes/ambientes/caixa de verificação de registos de execução.** Em alternativa, se pretender implantar as suas encomendas no sistema de ficheiros, nos Ficheiros Azure ou na base de dados do SQL Server (MSDB) hospedada pela Azure SQL Managed Instance (Modelo de Implementação de Pacotes), não é necessário criar SSISDB nem selecionar a caixa de verificação.

Independentemente do seu modelo de implementação, se pretender utilizar o SqL Server Agent hospedado pela Azure SQL Managed Instance para orquestrar/agendar as execuções do seu pacote, é ativado pelo SSISDB, por isso selecione a caixa de verificação de qualquer maneira. Para obter mais informações, consulte [as execuções do pacote SSIS da Agenda através do Agente de Instância Gerido Azure SQL](./how-to-invoke-ssis-package-managed-instance-agent.md).
   
Se selecionar a caixa de verificação, preencha os seguintes passos para trazer o seu próprio servidor de base de dados para hospedar o SSISDB que iremos criar e gerir em seu nome.

   ![Definições de implementação para SSISDB](./media/tutorial-create-azure-ssis-runtime-portal/deployment-settings.png)
   
   1. Em **Subscrição**, selecione a subscrição do Azure que tem o servidor da base de dados para alojar a SSISDB. 

   1. Em **Localização**, selecione a mesma localização do seu servidor de base de dados para alojar a SSISDB. Recomendamos que selecione a mesma localização do seu runtime de integração.

   1. Em **Ponto Final do Servidor de Base de Dados do Catálogo**, selecione o ponto final do seu servidor de base de dados para alojar a SSISDB. 
   
      Com base no servidor de base de dados selecionado, a instância SSISDB pode ser criada em seu nome como uma única base de dados, como parte de uma piscina elástica, ou em um caso gerido. Pode ser acessível numa rede pública ou através da adesão a uma rede virtual. Para obter orientações na escolha do tipo de servidor de base de dados para hospedar o SSISDB, consulte [Compare SQL Database e SQL Managed Instance](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-sql-database-and-sql-managed-instance).   

      Se selecionar um servidor de base de dados Azure SQL com regras de firewall IP/pontos finais de serviço de rede virtual ou uma instância gerida com ponto final privado para hospedar o SSISDB, ou se necessitar de acesso a dados no local sem configurar um IR auto-hospedado, tem de se juntar ao seu Azure-SSIS IR a uma rede virtual. Para obter mais informações, consulte [Junte-se a uma Azure-SSIS IR a uma rede virtual.](./join-azure-ssis-integration-runtime-virtual-network.md)

   1. Selecione a **autenticação Azure AD de utilização com a identidade gerida para a sua** caixa de verificação ADF para escolher o método de autenticação para o seu servidor de base de dados para hospedar o SSISDB. Você escolherá a autenticação SQL ou a autenticação AZURE AD com a identidade gerida para a sua fábrica de dados.

      Se selecionar a caixa de verificação, terá de adicionar a identidade gerida para a sua fábrica de dados num grupo AD Azure com permissões de acesso ao servidor de base de dados. Para obter mais informações, consulte [a autenticação Azure AD para um Azure-SSIS IR](./enable-aad-authentication-azure-ssis-ir.md).
   
   1. Para **o nome de utilizador Admin,** insira o nome de utilizador de autenticação SQL para o servidor de base de dados para hospedar o SSISDB. 

   1. Para **a Palavra-passe de Admin,** introduza a palavra-passe de autenticação SQL para o seu servidor de base de dados para hospedar o SSISDB. 

   1. Selecione o **par de runtime de integração Azure-SSIS de dupla posição com caixa de verificação de failover SSISDB** para configurar um duplo standby Azure SSIS IR pair que funciona em sincronização com Azure SQL Database/Managed Instance failover group para continuidade de negócios e recuperação de desastres (BCDR).
   
      Se selecionar a caixa de verificação, insira um nome para identificar o seu par de IRs Azure-SISis primários e secundários na caixa de texto de dupla dupla dupla de **espera.** Tem de introduzir o mesmo nome de pares ao criar as suas IRs Azure-SSIS primárias e secundárias.

      Para mais informações, consulte [configurar o seu Azure-SSIS IR para BCDR](./configure-bcdr-azure-ssis-integration-runtime.md).

   1. Para **o Catálogo Database Service Tier**, selecione o nível de serviço para o seu servidor de base de dados para hospedar o SSISDB. Selecione o nível Básico, Standard ou Premium ou selecione um nome elástico da piscina.

Selecione **a ligação de teste** quando aplicável e se for bem sucedida, selecione **Seguinte**.

> [!NOTE]
   > Se utilizar o servidor Azure SQL Database para hospedar o SSISDB, os seus dados serão armazenados em armazenamento geo-redundante para cópias de segurança por predefinição. Se não pretender que os seus dados sejam replicados noutras regiões, por favor siga as instruções para configurar a [redundância de armazenamento de backup utilizando o PowerShell](../azure-sql/database/automated-backups-overview.md?tabs=single-database#configure-backup-storage-redundancy-by-using-powershell).
   
##### <a name="creating-azure-ssis-ir-package-stores"></a>Criação de lojas de pacotes Azure-SSIS IR

Na página de definições de implementação do painel de configuração do tempo de execução de **integração,** se pretender gerir os seus pacotes que são implantados em MSDB, sistema de ficheiros ou **Ficheiros** Azure (Modelo de Implementação de Pacotes) com lojas de pacotes Azure-SSIS IR, selecione as **lojas de pacotes Create para gerir os seus pacotes que são implantados no sistema de ficheiros/Ficheiros Azure/Base de dados do SLL Server (MSDB) hospedados por Azure-SSIS Managed Instance.**
   
A loja de pacotes Azure-SSIS IR permite importar/eliminar/eliminar/executar pacotes e monitorizar/parar de funcionar pacotes através de SSMS semelhantes à [antiga loja de pacotes SSIS](/sql/integration-services/service/package-management-ssis-service). Para obter mais informações, consulte [os pacotes SSIS com lojas de pacotes Azure-SSIS IR](./azure-ssis-integration-runtime-package-store.md).
   
Se selecionar esta caixa de verificação, pode adicionar várias lojas de pacotes ao seu Azure-SSIS IR selecionando **New**. Inversamente, uma loja de pacotes pode ser partilhada por vários IRs Azure-SSIS.

![Definições de implementação para MSDB/sistema de ficheiros/Ficheiros Azure](./media/tutorial-create-azure-ssis-runtime-portal/deployment-settings2.png)

No painel de **embalagem Add,** complete os seguintes passos.
   
   1. Para **o nome da loja pacote,** insira o nome da sua loja de pacotes. 

   1. Para **o serviço ligado à loja pacote**, selecione o seu serviço ligado existente que armazena as informações de acesso para sistema de ficheiros/Azure Files/Azure SQL Managed Instance onde os seus pacotes são implantados ou criar um novo selecionando **New**. No novo painel **de serviço ligado,** complete os seguintes passos.
   
      > [!NOTE]
      > Pode utilizar serviços ligados ao **Azure File Storage** ou **ao Sistema de Ficheiros** para aceder a Ficheiros Azure. Se utilizar o serviço de armazenamento de **ficheiros Azure,** a loja de pacotes Azure-SSIS IR suporta apenas o método de autenticação **Basic** **(não's account** nem **SAS URI)** por enquanto. Para utilizar a autenticação **básica** no serviço ligado ao **Armazenamento de Ficheiros Azure,** pode anexar `?feature.upgradeAzureFileStorage=false` o URL do portal ADF no seu navegador. Em alternativa, pode utilizar o serviço ligado ao **Sistema de Ficheiros** para aceder a Ficheiros Azure. 

      ![Definições de implantação de serviços ligados](./media/tutorial-create-azure-ssis-runtime-portal/deployment-settings-linked-service.png)

      1. Para **nome,** insira o nome do seu serviço ligado. 
         
      1. Para **descrição,** insira a descrição do seu serviço ligado. 
         
      1. Para **o tipo**, selecione **Azure File Storage**, **Azure SQL Managed Instance**, ou File **System**.

      1. Pode ignorar **o Connect via tempo de integração,** uma vez que utilizamos sempre o seu Azure-SSIS IR para obter as informações de acesso para lojas de pacotes.

      1. Se selecionar **o Armazenamento de Ficheiros Azure,** preencha os seguintes passos. 

         1. Para **o método de seleção de conta**, selecione **Azure por subscrição** ou **introduza manualmente**.
         
         1. Se selecionar **A subscrição do Azure,** selecione a subscrição relevante do **Azure,** **o nome da conta de armazenamento** e a partilha de **ficheiros**.
            
         1. Se selecionar **Entrar manualmente,** introduza `\\<storage account name>.file.core.windows.net\<file share name>` para o nome de **utilizador**, para o nome `Azure\<storage account name>` de **utilizador** e para `<storage account key>` a **palavra-passe** ou selecione o cofre da **chave Azure** onde é armazenado como secreto.

      1. Se selecionar **Azure SQL Managed Instance,** complete os seguintes passos. 

         1. **Selecione a cadeia de ligação** para introduzi-lo manualmente ou o cofre da chave **Azure,** onde é armazenado em segredo.
         
         1. Se selecionar **a cadeia De ligação,** complete os seguintes passos. 

            1. Para **nome de domínio totalmente qualificado,** insira ou como ponto final privado ou público da sua Instância Gerida `<server name>.<dns prefix>.database.windows.net` `<server name>.public.<dns prefix>.database.windows.net,3342` Azure SQL, respectivamente. Se introduzir o ponto final privado, a **ligação de teste** não é aplicável, uma vez que a ADF UI não consegue alcançá-la.

            1. Para **o nome da base de dados,** insira `msdb` .
               
            1. Para **tipo de autenticação**, selecione **A autenticação SQL,** **Identidade Gerida,** ou **Principal de Serviço.**

            1. Se selecionar **a autenticação SQL,** insira o nome de **utilizador** e **palavra-passe** relevantes ou selecione o cofre da **chave Azure** onde é armazenado como secreto.

            1. Se selecionar **Identidade Gerida,** conceda ao seu ADF acesso de identidade gerido à sua Azure SQL Managed Instance.

            1. Se selecionar **o Service Principal,** insira a **chave principal** de **ID** e Serviço do Serviço relevante ou selecione o seu Cofre de **Chaves Azure** onde é armazenado em segredo.

      1. Se selecionar **o sistema de ficheiros,** insira o caminho da pasta UNC onde os seus pacotes são implantados para **o Anfitrião,** bem como o nome de **utilizador** e **palavra-passe** relevantes ou selecione o seu **Cofre de Chave Azure** onde é armazenado como secreto.

      1. Selecione **a ligação de teste** quando aplicável e se for bem sucedida, selecione **Criar**.

   1. As suas lojas de pacotes adicionadas aparecerão na página **de definições de Implementação.** Para removê-las, selecione as suas caixas de verificação e, em seguida, **selecione Delete**.

Selecione **a ligação de teste** quando aplicável e se for bem sucedida, selecione **Seguinte**.

#### <a name="advanced-settings-page"></a>Página definições avançadas

Na página **de configurações avançadas** do painel de configuração do tempo de execução da **integração,** complete os seguintes passos.

   ![Definições avançadas](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   1. Para **execuções paralelas máximas por nó,** selecione o número máximo de pacotes a executar simultaneamente por nó no seu cluster de tempo de execução de integração. Apenas são apresentados os números de pacotes suportados. Selecione um número baixo se quiser usar mais do que um núcleo para executar um único pacote grande que seja compute ou memória intensiva. Selecione um número alto se quiser executar uma ou mais embalagens pequenas num único núcleo.

   1. Selecione o **tempo de execução de integração Azure-SSIS com configurações de sistema/instalações de componentes adicionais** para escolher se deseja adicionar configurações personalizadas standard/express no seu Azure-SSIS IR. Para obter mais informações, consulte [a configuração personalizada para um Azure-SSIS IR](./how-to-configure-azure-ssis-ir-custom-setup.md).

      Se selecionar a caixa de verificação, complete os seguintes passos.

      ![Configurações avançadas com configurações personalizadas](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)
   
      1. Para **o recipiente de configuração personalizado SAS URI,** introduza o SAS URI do seu recipiente onde armazena scripts e ficheiros associados para configurações personalizadas padrão.

      1. Para **configuração personalizada express**, selecione **Novo** para abrir o painel **de configuração personalizado Add Express** e, em seguida, selecione quaisquer tipos no menu de **dropdown do tipo de configuração personalizada Express,** por exemplo, **Executar comando cmdkey**, Adicionar **variável ambiente,** **instalar componente licenciado,** etc.

         Se selecionar o tipo **de componente licenciado instalar,** pode selecionar quaisquer componentes integrados dos nossos parceiros ISV no menu de dropdown **de nome componente** e, se necessário, introduzir a chave de licença do produto/carregar o ficheiro de licença do produto que adquiriu na caixa de  / **ficheiros da chave licença.**
  
         As configurações personalizadas expressas adicionadas aparecerão na página **de definições Avançadas.** Para removê-las, pode selecionar as suas caixas de verificação e, em seguida, selecionar **Eliminar**.

   1. Selecione o **VNet para o seu tempo de execução de integração Azure-SSIS para aderir, permita que a ADF crie certos recursos de rede e, opcionalmente, traga a sua própria caixa de verificação de endereços IP públicos estáticos** para escolher se pretende aderir ao seu tempo de execução de integração a uma rede virtual. 

      Selecione-o se utilizar um servidor de base de dados Azure SQL com regras de firewall IP/pontos finais de serviço de rede virtual ou uma instância gerida com ponto final privado para hospedar o SSISDB, ou se necessitar de acesso a dados no local (ou seja, tem fontes de dados ou destinos no local nos seus pacotes SSIS) sem configurar um IR auto-hospedado. Para obter mais informações, consulte [Join Azure-SSIS IR para uma rede virtual.](./join-azure-ssis-integration-runtime-virtual-network.md) 

      Se selecionar a caixa de verificação, complete os seguintes passos.

      ![Definições avançadas com uma rede virtual](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

      1. Para **Subscrição**, selecione a subscrição Azure que tem a sua rede virtual.

      1. Para **a localização,** é selecionado o mesmo local do seu tempo de execução de integração.

      1. Para **tipo,** selecione o tipo da sua rede virtual: classic ou Azure Resource Manager. Recomendamos que selecione uma rede virtual Azure Resource Manager, porque redes virtuais clássicas serão depreciadas em breve.

      1. Para **O Nome VNet,** selecione o nome da sua rede virtual. Deve ser o mesmo utilizado para o seu servidor Azure SQL Database com pontos finais de serviço de rede virtual ou caso gerido com ponto final privado para hospedar o SSISDB. Ou deve ser o mesmo ligado à sua rede no local. Caso contrário, pode ser qualquer rede virtual para trazer os seus próprios endereços IP públicos estáticos para Azure-SSIS IR.

      1. Para **nome da sub-rede,** selecione o nome da sub-rede para a sua rede virtual. Deve ser o mesmo utilizado para o seu servidor Azure SQL Database com pontos finais de serviço de rede virtual para hospedar o SSISDB. Ou deve ser uma sub-rede diferente daquela usada para a sua instância gerida com ponto final privado para hospedar o SSISDB. Caso contrário, pode ser qualquer sub-rede para trazer os seus próprios endereços IP públicos estáticos para Azure-SSIS IR.

      1. Selecione os **endereços IP públicos estáticos para a sua caixa de verificação de tempo de execução de integração Azure-SSIS** para escolher se pretende trazer os seus próprios endereços IP públicos estáticos para Azure-SSIS IR, para que possa permitir que fiquem na firewall para as suas fontes de dados.

         Se selecionar a caixa de verificação, complete os seguintes passos.

         1. Para **o primeiro endereço IP público estático**, selecione o primeiro endereço IP público estático que satisfaz os requisitos para o seu Azure-SSIS IR. Se não tiver nenhum, clique em **Criar uma nova** ligação para criar endereços IP públicos estáticos no portal Azure e, em seguida, clique no botão de atualização aqui, para que possa selecioná-los.
      
         1. Para **o segundo endereço IP público estático**, selecione o segundo endereço IP público estático que satisfaz os requisitos para o seu Azure-SSIS IR. Se não tiver nenhum, clique em **Criar uma nova** ligação para criar endereços IP públicos estáticos no portal Azure e, em seguida, clique no botão de atualização aqui, para que possa selecioná-los.

   1. Selecione o **Prazo de Execução de Integração Self-Hosted como um proxy para a sua caixa de verificação de runtime de integração Azure-SSIS** para escolher se pretende configurar um IR auto-hospedado como proxy para o seu Azure-SSIS IR. Para obter mais informações, consulte [Configurar um IR auto-hospedado como procuração](./self-hosted-integration-runtime-proxy-ssis.md). 

      Se selecionar a caixa de verificação, complete os seguintes passos.

      ![Configurações avançadas com um IR auto-hospedado](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

      1. Para **o tempo de execução de integração auto-hospedado,** selecione o seu IR auto-hospedado existente como um proxy para Azure-SSIS IR.

      1. Para **o Serviço de Ligação ao Armazenamento de Fases**, selecione o serviço de armazenamento Azure Blob existente ou crie um novo para a encenação.

      1. Para **o Caminho de Preparação**, especifique um recipiente de bolhas na sua conta de armazenamento Azure Blob selecionada ou deixe-o vazio para utilizar um padrão para a encenação.

   1. Selecione **VNet Validation**  >  **Continue**. 

Na secção **Resumo,** reveja todas as definições de provisionamento, marque as ligações de documentação recomendadas e selecione **Terminar** para iniciar a criação do seu tempo de execução de integração.

   > [!NOTE]
   > Excluindo qualquer tempo de configuração personalizado, este processo deve terminar dentro de 5 minutos. Mas pode levar 20 a 30 minutos para o Azure-SSIS IR se juntar a uma rede virtual.
   >
   > Se utilizar o SSISDB, o serviço Data Factory liga-se ao servidor de base de dados para preparar o SSISDB. Também configura permissões e configurações para a sua rede virtual, se especificado, e junta o seu IR Azure-SSIS à rede virtual.
   > 
   > Quando forres um Pacote de Recursos Azure-SSIS IR, Access Redistributable e Azure Feature Pack para SSIS também estão instalados. Estes componentes fornecem conectividade aos ficheiros Excel, ficheiros de acesso e várias fontes de dados Azure, além das fontes de dados que os componentes incorporados já suportam. Para obter mais informações sobre componentes incorporados/pré-instalados, consulte [componentes incorporados/pré-instalados no Azure-SSIS IR](./built-in-preinstalled-components-ssis-integration-runtime.md). Para obter mais informações sobre componentes adicionais que possa instalar, consulte [configurações personalizadas para Azure-SSIS IR](./how-to-configure-azure-ssis-ir-custom-setup.md).

#### <a name="connections-pane"></a>Painel de ligações

No painel **de ligações** do centro **De Gestão,** mude para a página **de tempos de execução** de integração e selecione **Refresh**. 

   ![Painel de ligações](./media/tutorial-create-azure-ssis-runtime-portal/connections-pane.png)

   Pode editar/reconfigurar o seu Azure-SSIS IR selecionando o seu nome. Também pode selecionar os botões relevantes para monitorizar/iniciar/parar/eliminar o seu Azure-SSIS IR, gerar automaticamente um pipeline ADF com a atividade do Pacote SSIS executar para executar no seu Azure-SSIS IR e ver o código/carga JSON do seu Azure-SSIS IR.  A edição/eliminação do seu Azure-SSIS IR só pode ser feita quando esta é interrompida.

### <a name="azure-ssis-integration-runtimes-in-the-portal"></a>Runtime de integração de SSIS do Azure no portal

1. No UI da Fábrica de Dados Azure, mude para o **separador Editar** e selecione **Ligações**. Em seguida, mude para o separador **Tempos de Execução** de Integração para ver os tempos de integração existentes na sua fábrica de dados.

   ![Ver IRs existentes](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

1. Selecione **Novo** para criar um novo Azure-SSIS IR e abra o painel **de configuração de tempo de integração.**

   ![Runtime de integração através do menu](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

1. No painel de configuração de **tempo de integração,** selecione os **pacotes SSIS existentes para executar em azulejo Azure** e, em seguida, selecione **Next**.

   ![Especifique o tipo de runtime de integração](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

1. Para os passos restantes para a criação de um Azure-SSIS IR, consulte a secção de execução de [integração Azure SSIS.](#provision-an-azure-ssis-integration-runtime)

## <a name="use-azure-powershell-to-create-an-integration-runtime"></a>Use a Azure PowerShell para criar um tempo de integração

Nesta secção, utiliza-se a Azure PowerShell para criar um Azure-SSIS IR.

### <a name="create-variables"></a>Criar variáveis

Copiar e colar o seguinte script. Especificar valores para as variáveis. 

```powershell
### Azure Data Factory info
# If your input contains a PSH special character like "$", precede it with the escape character "`" - for example, "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime info - This is a Data Factory compute resource for running SSIS packages.
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2
# Azure-SSIS IR edition/license info: Standard or Enterprise
$AzureSSISEdition = "Standard" # Standard by default, whereas Enterprise lets you use advanced features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, whereas BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported. For other nodes, up to (2 x number of cores) are currently supported.
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info: Standard/express custom setups
$SetupScriptContainerSasUri = "" # OPTIONAL to provide a SAS URI of blob container for standard custom setup where your script and its associated files are stored
$ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|InstallAzurePowerShell|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO|KingswaySoft.IntegrationToolkit|KingswaySoft.ProductivityPack|Theobald.XtractIS|AecorSoft.IntegrationService|CData.Standard|CData.Extended or leave it empty]" # OPTIONAL to configure an express custom setup without script
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use an Azure SQL Database server with IP firewall rules/virtual network service endpoints or a managed instance with private endpoint to host SSISDB, or if you require access to on-premises data without configuring a self-hosted IR. We recommend an Azure Resource Manager virtual network, because classic virtual networks will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for your Azure SQL Database server with virtual network service endpoints, or a different subnet from the one used for your managed instance with a private endpoint
# Public IP address info: OPTIONAL to provide two standard static public IP addresses with DNS name under the same subscription and in the same region as your virtual network
$FirstPublicIP = "[your first public IP address resource ID or leave it empty]"
$SecondPublicIP = "[your second public IP address resource ID or leave it empty]"

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or managed instance name.DNS prefix.database.windows.net or managed instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, ensure that there's no existing SSISDB on your database server, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure AD
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for Azure AD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for Azure AD authentication]"
# For the basic pricing tier, specify "Basic," not "B." For standard, premium, and elastic pool tiers, specify "S0," "S1," "S2," "S3," etc. See https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server.
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for managed instance]"

### Self-hosted integration runtime info - This can be configured as a proxy for on-premises data access 
$DataProxyIntegrationRuntimeName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingLinkedServiceName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingPath = "" # OPTIONAL to configure a proxy for on-premises data access 
```

### <a name="sign-in-and-select-a-subscription"></a>Iniciar s.ção e selecionar uma subscrição

Adicione o seguinte script para iniciar sôm e selecione a subscrição do Azure.

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName
```

### <a name="validate-the-connection-to-database-server"></a>Validar a ligação ao servidor de base de dados

Adicione o seguinte script para validar o seu servidor de base de dados Azure SQL ou instância gerida.

```powershell
# Validate only if you use SSISDB and you don't use virtual network or Azure AD authentication
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    if([string]::IsNullOrEmpty($VnetId) -and [string]::IsNullOrEmpty($SubnetName))
    {
        if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) -and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
        {
            $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword
            $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
            Try
            {
                $sqlConnection.Open();
            }
            Catch [System.Data.SqlClient.SqlException]
            {
                Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_";
                Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
                $yn = Read-Host
                if(!($yn -ieq "Y"))
                {
                    Return;
                }
            }
        }
    }
}
```

### <a name="configure-the-virtual-network"></a>Configurar a rede virtual

Adicione o seguinte script para configurar automaticamente permissões e configurações de rede virtuais para o tempo de integração do Azure-SSIS para aderir.

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um [grupo de recursos Azure](../azure-resource-manager/management/overview.md) utilizando o comando [New-AzResourceGroup.](/powershell/module/az.resources/new-azresourcegroup) Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos como um grupo.

Se o grupo de recursos já existir, não copie este código para o script. 

```powershell
New-AzResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

### <a name="create-a-data-factory"></a>Criar uma fábrica de dados

Execute o comando seguinte para criar uma fábrica de dados.

```powershell
Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
    -Location $DataFactoryLocation `
    -Name $DataFactoryName
```

### <a name="create-an-integration-runtime"></a>Criar um integration runtime

Executar os seguintes comandos para criar um tempo de integração Azure-SSIS que executa pacotes SSIS em Azure.

Se não utilizar o SSISDB, pode omitir os `CatalogServerEndpoint` `CatalogPricingTier` parâmetros e `CatalogAdminCredential` os parâmetros.

Se não utilizar um servidor Azure SQL Database com regras ip firewall/pontos finais de serviço de rede virtual ou uma instância gerida com ponto final privado para hospedar o SSISDB, ou exigir acesso a dados no local, pode omitir os `VNetId` parâmetros e `Subnet` parâmetros ou passar valores vazios para eles. Também pode omiti-los se configurar um IR auto-hospedado como representante do seu IR Azure-SSIS para aceder a dados no local. Caso contrário, não pode omiti-los e deve passar valores válidos da sua configuração de rede virtual. Para obter mais informações, consulte [Junte-se a uma Azure-SSIS IR a uma rede virtual.](./join-azure-ssis-integration-runtime-virtual-network.md)

Se utilizar a instância gerida para hospedar o SSISDB, pode omitir o `CatalogPricingTier` parâmetro ou passar um valor vazio por ele. Caso contrário, não pode omiti-lo e deve passar um valor válido da lista de níveis de preços suportados para Azure SQL Database. Para obter mais informações, consulte [os limites de recursos da Base de Dados SQL.](../azure-sql/database/resource-limits-logical-server.md)

Se utilizar a autenticação Azure AD com a identidade gerida para a sua fábrica de dados ligar-se ao servidor de base de dados, pode omitir o `CatalogAdminCredential` parâmetro. Mas deve adicionar a identidade gerida para a sua fábrica de dados num grupo AZure AD com permissões de acesso ao servidor de base de dados. Para obter mais informações, consulte [a autenticação Azure AD para um Azure-SSIS IR](./enable-aad-authentication-azure-ssis-ir.md). Caso contrário, não pode omiti-lo e deve passar um objeto válido formado a partir do nome de utilizador e palavra-passe do seu servidor e palavra-passe para a autenticação SQL.

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Description $AzureSSISDescription `
    -Type Managed `
    -Location $AzureSSISLocation `
    -NodeSize $AzureSSISNodeSize `
    -NodeCount $AzureSSISNodeNumber `
    -Edition $AzureSSISEdition `
    -LicenseType $AzureSSISLicenseType `
    -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
    -VnetId $VnetId `
    -Subnet $SubnetName
       
# Add the CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -CatalogServerEndpoint $SSISDBServerEndpoint `
        -CatalogPricingTier $SSISDBPricingTier

    if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword)) # Add the CatalogAdminCredential parameter if you don't use Azure AD authentication
    {
        $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
        $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -CatalogAdminCredential $serverCreds
    }
}

# Add custom setup parameters if you use standard/express custom setups
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}
if(![string]::IsNullOrEmpty($ExpressCustomSetup))
{
    if($ExpressCustomSetup -eq "RunCmdkey")
    {
        $addCmdkeyArgument = "YourFileShareServerName or YourAzureStorageAccountName.file.core.windows.net"
        $userCmdkeyArgument = "YourDomainName\YourUsername or azure\YourAzureStorageAccountName"
        $passCmdkeyArgument = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourPassword or YourAccessKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.CmdkeySetup($addCmdkeyArgument, $userCmdkeyArgument, $passCmdkeyArgument)
    }
    if($ExpressCustomSetup -eq "SetEnvironmentVariable")
    {
        $variableName = "YourVariableName"
        $variableValue = "YourVariableValue"
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.EnvironmentVariableSetup($variableName, $variableValue)
    }
    if($ExpressCustomSetup -eq "InstallAzurePowerShell")
    {
        $moduleVersion = "YourAzModuleVersion"
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.AzPowerShellSetup($moduleVersion)
    }
    if($ExpressCustomSetup -eq "SentryOne.TaskFactory")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "oh22is.SQLPhonetics.NET")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "oh22is.HEDDA.IO")
    {
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup)
    }
    if($ExpressCustomSetup -eq "KingswaySoft.IntegrationToolkit")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "KingswaySoft.ProductivityPack")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }    
    if($ExpressCustomSetup -eq "Theobald.XtractIS")
    {
        $jsonData = Get-Content -Raw -Path YourLicenseFile.json
        $jsonData = $jsonData -replace '\s',''
        $jsonData = $jsonData.replace('"','\"')
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString($jsonData)
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "AecorSoft.IntegrationService")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "CData.Standard")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "CData.Extended")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }    
    # Create an array of one or more express custom setups
    $setups = New-Object System.Collections.ArrayList
    $setups.Add($setup)

    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -ExpressCustomSetup $setups
}

# Add self-hosted integration runtime parameters if you configure a proxy for on-premises data accesss
if(![string]::IsNullOrEmpty($DataProxyIntegrationRuntimeName) -and ![string]::IsNullOrEmpty($DataProxyStagingLinkedServiceName))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -DataProxyIntegrationRuntimeName $DataProxyIntegrationRuntimeName `
        -DataProxyStagingLinkedServiceName $DataProxyStagingLinkedServiceName

    if(![string]::IsNullOrEmpty($DataProxyStagingPath))
    {
        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -DataProxyStagingPath $DataProxyStagingPath
    }
}

# Add public IP address parameters if you bring your own static public IP addresses
if(![string]::IsNullOrEmpty($FirstPublicIP) -and ![string]::IsNullOrEmpty($SecondPublicIP))
{
    $publicIPs = @($FirstPublicIP, $SecondPublicIP)
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -PublicIPs $publicIPs
}
```

### <a name="start-the-integration-runtime"></a>Inicie o tempo de execução da integração

Executar os seguintes comandos para iniciar o tempo de integração Azure-SSIS.

```powershell
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

> [!NOTE]
> Excluindo qualquer tempo de configuração personalizado, este processo deve terminar dentro de 5 minutos. Mas pode levar 20 a 30 minutos para o Azure-SSIS IR se juntar a uma rede virtual.
>
> Se utilizar o SSISDB, o serviço Data Factory liga-se ao servidor de base de dados para preparar o SSISDB. Também configura permissões e configurações para a sua rede virtual, se especificado, e junta o seu IR Azure-SSIS à rede virtual.
> 
> Quando forres um Pacote de Recursos Azure-SSIS IR, Access Redistributable e Azure Feature Pack para SSIS também estão instalados. Estes componentes fornecem conectividade aos ficheiros Excel, ficheiros de acesso e várias fontes de dados Azure, além das fontes de dados que os componentes incorporados já suportam. Para obter mais informações sobre componentes incorporados/pré-instalados, consulte [componentes incorporados/pré-instalados no Azure-SSIS IR](./built-in-preinstalled-components-ssis-integration-runtime.md). Para obter mais informações sobre componentes adicionais que possa instalar, consulte [configurações personalizadas para Azure-SSIS IR](./how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="full-script"></a>Script completo

Aqui está o roteiro completo que cria um tempo de integração Azure-SSIS.

```powershell
### Azure Data Factory info
# If your input contains a PSH special character like "$", precede it with the escape character "`" - for example, "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime info - This is a Data Factory compute resource for running SSIS packages.
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2
# Azure-SSIS IR edition/license info: Standard or Enterprise
$AzureSSISEdition = "Standard" # Standard by default, whereas Enterprise lets you use advanced features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, whereas BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from the Azure Hybrid Benefit option
# For a Standard_D1_v2 node, up to four parallel executions per node are supported. For other nodes, up to (2 x number of cores) are currently supported.
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info: Standard/express custom setups
$SetupScriptContainerSasUri = "" # OPTIONAL to provide a SAS URI of blob container for standard custom setup where your script and its associated files are stored
$ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|InstallAzurePowerShell|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO|KingswaySoft.IntegrationToolkit|KingswaySoft.ProductivityPack|Theobald.XtractIS|AecorSoft.IntegrationService|CData.Standard|CData.Extended or leave it empty]" # OPTIONAL to configure an express custom setup without script
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use an Azure SQL Database server with IP firewall rules/virtual network service endpoints or a managed instance with private endpoint to host SSISDB, or if you require access to on-premises data without configuring a self-hosted IR. We recommend an Azure Resource Manager virtual network, because classic virtual networks will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for your Azure SQL Database server with virtual network service endpoints, or a different subnet from the one used for your managed instance with a private endpoint
# Public IP address info: OPTIONAL to provide two standard static public IP addresses with DNS name under the same subscription and in the same region as your virtual network
$FirstPublicIP = "[your first public IP address resource ID or leave it empty]"
$SecondPublicIP = "[your second public IP address resource ID or leave it empty]"

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or managed instance name.DNS prefix.database.windows.net or managed instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, ensure that there's no existing SSISDB on your database server, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure AD
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for Azure AD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for Azure AD authentication]"
# For the basic pricing tier, specify "Basic," not "B." For standard, premium, and elastic pool tiers, specify "S0," "S1," "S2," "S3," etc. See https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server.
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for managed instance]"

### Self-hosted integration runtime info - This can be configured as a proxy for on-premises data access 
$DataProxyIntegrationRuntimeName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingLinkedServiceName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingPath = "" # OPTIONAL to configure a proxy for on-premises data access 

### Sign in and select a subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName

### Validate the connection to the database server
# Validate only if you use SSISDB and don't use a virtual network or Azure AD authentication
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    if([string]::IsNullOrEmpty($VnetId) -and [string]::IsNullOrEmpty($SubnetName))
    {
        if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) -and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
        {
            $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword
            $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
            Try
            {
                $sqlConnection.Open();
            }
            Catch [System.Data.SqlClient.SqlException]
            {
                Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_";
                Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
                $yn = Read-Host
                if(!($yn -ieq "Y"))
                {
                    Return;
                }
            }
        }
    }
}

### Configure a virtual network
# Make sure to run this script against the subscription to which the virtual network belongs
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}

### Create a data factory
Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
    -Location $DataFactoryLocation `
    -Name $DataFactoryName

### Create an integration runtime
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Description $AzureSSISDescription `
    -Type Managed `
    -Location $AzureSSISLocation `
    -NodeSize $AzureSSISNodeSize `
    -NodeCount $AzureSSISNodeNumber `
    -Edition $AzureSSISEdition `
    -LicenseType $AzureSSISLicenseType `
    -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
    -VnetId $VnetId `
    -Subnet $SubnetName
       
# Add CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -CatalogServerEndpoint $SSISDBServerEndpoint `
        -CatalogPricingTier $SSISDBPricingTier

    if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword)) # Add the CatalogAdminCredential parameter if you don't use Azure AD authentication
    {
        $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
        $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -CatalogAdminCredential $serverCreds
    }
}

# Add custom setup parameters if you use standard/express custom setups
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}
if(![string]::IsNullOrEmpty($ExpressCustomSetup))
{
    if($ExpressCustomSetup -eq "RunCmdkey")
    {
        $addCmdkeyArgument = "YourFileShareServerName or YourAzureStorageAccountName.file.core.windows.net"
        $userCmdkeyArgument = "YourDomainName\YourUsername or azure\YourAzureStorageAccountName"
        $passCmdkeyArgument = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourPassword or YourAccessKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.CmdkeySetup($addCmdkeyArgument, $userCmdkeyArgument, $passCmdkeyArgument)
    }
    if($ExpressCustomSetup -eq "SetEnvironmentVariable")
    {
        $variableName = "YourVariableName"
        $variableValue = "YourVariableValue"
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.EnvironmentVariableSetup($variableName, $variableValue)
    }
    if($ExpressCustomSetup -eq "InstallAzurePowerShell")
    {
        $moduleVersion = "YourAzModuleVersion"
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.AzPowerShellSetup($moduleVersion)
    }
    if($ExpressCustomSetup -eq "SentryOne.TaskFactory")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "oh22is.SQLPhonetics.NET")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "oh22is.HEDDA.IO")
    {
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup)
    }
    if($ExpressCustomSetup -eq "KingswaySoft.IntegrationToolkit")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "KingswaySoft.ProductivityPack")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }    
    if($ExpressCustomSetup -eq "Theobald.XtractIS")
    {
        $jsonData = Get-Content -Raw -Path YourLicenseFile.json
        $jsonData = $jsonData -replace '\s',''
        $jsonData = $jsonData.replace('"','\"')
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString($jsonData)
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "AecorSoft.IntegrationService")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "CData.Standard")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "CData.Extended")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    # Create an array of one or more express custom setups
    $setups = New-Object System.Collections.ArrayList
    $setups.Add($setup)

    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -ExpressCustomSetup $setups
}

# Add self-hosted integration runtime parameters if you configure a proxy for on-premises data accesss
if(![string]::IsNullOrEmpty($DataProxyIntegrationRuntimeName) -and ![string]::IsNullOrEmpty($DataProxyStagingLinkedServiceName))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -DataProxyIntegrationRuntimeName $DataProxyIntegrationRuntimeName `
        -DataProxyStagingLinkedServiceName $DataProxyStagingLinkedServiceName

    if(![string]::IsNullOrEmpty($DataProxyStagingPath))
    {
        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -DataProxyStagingPath $DataProxyStagingPath
    }
}

# Add public IP address parameters if you bring your own static public IP addresses
if(![string]::IsNullOrEmpty($FirstPublicIP) -and ![string]::IsNullOrEmpty($SecondPublicIP))
{
    $publicIPs = @($FirstPublicIP, $SecondPublicIP)
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -PublicIPs $publicIPs
}

### Start the integration runtime
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

## <a name="use-an-azure-resource-manager-template-to-create-an-integration-runtime"></a>Use um modelo de Gestor de Recursos Azure para criar um tempo de integração

Nesta secção, utiliza-se um modelo de Gestor de Recursos Azure para criar o tempo de integração Azure-SSIS. Aqui está uma amostra de passagem:

1. Crie um ficheiro JSON com o seguinte modelo de Gestor de Recursos Azure. Substitua os valores nos suportes angulares (espaços reservados) pelos seus próprios valores.

    ```json
    {
        "contentVersion": "1.0.0.0",
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "parameters": {},
        "variables": {},
        "resources": [{
            "name": "<Specify a name for your data factory>",
            "apiVersion": "2018-06-01",
            "type": "Microsoft.DataFactory/factories",
            "location": "East US",
            "properties": {},
            "resources": [{
                "type": "integrationruntimes",
                "name": "<Specify a name for your Azure-SSIS IR>",
                "dependsOn": [ "<The name of the data factory you specified at the beginning>" ],
                "apiVersion": "2018-06-01",
                "properties": {
                    "type": "Managed",
                    "typeProperties": {
                        "computeProperties": {
                            "location": "East US",
                            "nodeSize": "Standard_D8_v3",
                            "numberOfNodes": 1,
                            "maxParallelExecutionsPerNode": 8
                        },
                        "ssisProperties": {
                            "catalogInfo": {
                                "catalogServerEndpoint": "<Azure SQL Database server name>.database.windows.net",
                                "catalogAdminUserName": "<Azure SQL Database server admin username>",
                                "catalogAdminPassword": {
                                    "type": "SecureString",
                                    "value": "<Azure SQL Database server admin password>"
                                },
                                "catalogPricingTier": "Basic"
                            }
                        }
                    }
                }
            }]
        }]
    }
    ```

2. Para implementar o modelo Azure Resource Manager, executar o `New-AzResourceGroupDeployment` comando como mostrado no exemplo seguinte. No exemplo, `ADFTutorialResourceGroup` é o nome do seu grupo de recursos. `ADFTutorialARM.json` é o ficheiro que contém a definição JSON para a sua fábrica de dados e o Azure-SSIS IR.

    ```powershell
    New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json
    ```

    Este comando cria a sua fábrica de dados e a Azure-SSIS IR nele, mas não inicia o IR.

3. Para iniciar o seu Azure-SSIS IR, executar o `Start-AzDataFactoryV2IntegrationRuntime` comando:

    ```powershell
    Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "<Resource Group Name>" `
        -DataFactoryName "<Data Factory Name>" `
        -Name "<Azure SSIS IR Name>" `
        -Force
    ```

> [!NOTE]
> Excluindo qualquer tempo de configuração personalizado, este processo deve terminar dentro de 5 minutos. Mas pode levar 20 a 30 minutos para o Azure-SSIS IR se juntar a uma rede virtual.
>
> Se utilizar o SSISDB, o serviço Data Factory liga-se ao servidor de base de dados para preparar o SSISDB. Também configura permissões e configurações para a sua rede virtual, se especificado, e junta o seu IR Azure-SSIS à rede virtual.
> 
> Quando forres um Pacote de Recursos Azure-SSIS IR, Access Redistributable e Azure Feature Pack para SSIS também estão instalados. Estes componentes fornecem conectividade aos ficheiros Excel, ficheiros de acesso e várias fontes de dados Azure, além das fontes de dados que os componentes incorporados já suportam. Para obter mais informações sobre componentes incorporados/pré-instalados, consulte [componentes incorporados/pré-instalados no Azure-SSIS IR](./built-in-preinstalled-components-ssis-integration-runtime.md). Para obter mais informações sobre componentes adicionais que possa instalar, consulte [configurações personalizadas para Azure-SSIS IR](./how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="deploy-ssis-packages"></a>Implementar pacotes de SSIS

Se utilizar o SSISDB, pode colocar as suas encomendas nele e executá-las no seu Azure-SSIS IR utilizando as ferramentas SSDT ou SSMS ativadas pelo Azure. Estas ferramentas ligam-se ao servidor da sua base de dados através do seu ponto final do servidor: 

- Para um servidor de base de dados Azure SQL, o formato de ponto final do servidor é `<server name>.database.windows.net` .
- Para uma instância gerida com ponto final privado, o formato de ponto final do servidor é `<server name>.<dns prefix>.database.windows.net` .
- Para um caso gerido com ponto final público, o formato de ponto final do servidor é `<server name>.public.<dns prefix>.database.windows.net,3342` . 

Se não utilizar o SSISDB, pode implantar as suas encomendas no sistema de ficheiros, ficheiros Azure ou MSDB hospedados pela sua Azure SQL Managed Instance e executá-las no seu Azure-SSIS IR utilizando utilitários de linha de comando [dtutil](/sql/integration-services/dtutil-utility) e [AzureDTExec.](./how-to-invoke-ssis-package-azure-enabled-dtexec.md) 

Para obter mais informações, consulte [projetos/pacotes SSIS de implementação.](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages)

Em ambos os casos, também pode executar os seus pacotes implantados no Azure-SSIS IR utilizando a atividade do Pacote SSIS executar em pipelines data Factory. Para obter mais informações, consulte a execução do [pacote SSIS como uma atividade de primeira classe data factory](./how-to-invoke-ssis-package-ssis-activity.md).

## <a name="next-steps"></a>Passos seguintes

Consulte outros tópicos do Azure-SSIS IR nesta documentação:

- [Tempo de integração Azure-SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime). Este artigo fornece informações sobre os tempos de integração em geral, incluindo o Azure-SSIS IR.
- [Monitor an Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime) (Monitorizar um IR Azure-SSIS) Este artigo mostra-lhe como recuperar e entender informações sobre o seu Azure-SSIS IR.
- [Manage an Azure-SSIS IR](manage-azure-ssis-integration-runtime.md) (Gerir um IR Azure-SSIS). Este artigo mostra-lhe como parar, iniciar ou eliminar o seu Azure-SSIS IR. Também mostra como escalar o seu Azure-SSIS IR adicionando mais nós.
- [Implementar, executar e monitorizar pacotes SSIS em Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Ligue ao SSISDB em Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Connect to on-premises data sources with Windows authentication (Ligar a origens de dados no local com a autenticação do Windows)](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 
- [Agendar execuções de pacotes em Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)