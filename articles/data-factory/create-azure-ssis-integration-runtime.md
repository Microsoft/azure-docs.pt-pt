---
title: Criar Azure-SSIS Integration Runtime no Azure Data Factory | Microsoft Docs
description: Saiba como criar Azure-SSIS Integration Runtime no Azure Data Factory para que você possa implantar e executar pacotes do SSIS no Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/15/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 36cb4d306cd74dcde09fa55fc582a043bc324f65
ms.sourcegitcommit: a819209a7c293078ff5377dee266fa76fd20902c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/16/2019
ms.locfileid: "71010043"
---
# <a name="create-azure-ssis-integration-runtime-in-azure-data-factory"></a>Criar Azure-SSIS Integration Runtime no Azure Data Factory

Este tutorial fornece etapas para provisionar um Integration Runtime (IR) do Azure-SQL Server Integration Services (SSIS) no Azure Data Factory (ADF). Azure-SSIS IR dá suporte a pacotes em execução implantados no catálogo do SSIS (SSISDB) hospedado pelo servidor/Instância Gerenciada do banco de dados SQL do Azure (modelo de implantação de projeto) e aqueles implantados em sistemas de arquivos/compartilhamentos de arquivos/arquivos do Azure (modelo de implantação Depois que Azure-SSIS ir for provisionado, você poderá usar ferramentas familiares, como SQL Server Data Tools (SSDT)/SQL Server Management Studio (SSMS) e utilitários de linha de `dtinstall`comando, / `dtutil` / `dtexec`como, para implante e execute seus pacotes no Azure.

O [tutorial: O provisionamento de Azure-SSIS ir](tutorial-create-azure-ssis-runtime-portal.md) mostra como criar um Azure-SSIS ir por meio do aplicativo portal do Azure/ADF e, opcionalmente, usar o servidor/instância gerenciada do banco de dados SQL do Azure para hospedar o SSISDB. Este artigo expande o tutorial e mostra como fazer o seguinte:

- Opcionalmente, use o servidor de banco de dados SQL do Azure com pontos de extremidade de serviço de rede virtual/Instância Gerenciada com um ponto de extremidades privado para hospedar o SSISDB. Como pré-requisito, você precisa configurar as permissões/configurações de rede virtual para seu Azure-SSIS IR para ingressar em uma rede virtual.

- Opcionalmente, use a autenticação Azure Active Directory (AAD) com a identidade gerenciada para que o ADF se conecte ao servidor/Instância Gerenciada do banco de dados SQL do Azure. Como pré-requisito, você precisará adicionar a identidade gerenciada para o ADF como um usuário de banco de dados capaz de criar o SSISDB.

- Opcionalmente, ingresse seu Azure-SSIS IR em uma rede virtual/configure o IR auto-hospedado como um proxy para que seu Azure-SSIS IR acesse dados no local.

## <a name="overview"></a>Descrição geral

Este artigo mostra diferentes maneiras de provisionar Azure-SSIS IR:

- [Azure portal](#azure-portal)
- [Azure PowerShell](#azure-powershell)
- [Modelo do Azure Resource Manager](#azure-resource-manager-template)

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Subscrição do Azure**. Se você ainda não tiver uma assinatura, poderá criar uma conta de [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/) .
- **Servidor/instância gerenciada do banco de dados SQL do Azure (opcional)** . Se você ainda não tiver um servidor de banco de dados, crie um no portal do Azure antes de começar. Por sua vez, o ADF criará SSISDB neste servidor de banco de dados. Recomendamos que crie o servidor de base de dados na mesma região do Azure que o integration runtime. Essa configuração permite que o Integration Runtime grave logs de execução em SSISDB sem cruzar regiões do Azure. 
    - Com base no servidor de bases de dados selecionado, a SSISDB pode ser criada em seu nome como uma base de dados individual, parte de um conjunto elástico, ou numa Instância Gerida e acessível na rede pública ou ao associar uma rede virtual. Para obter orientação sobre como escolher o tipo de servidor de banco de dados para hospedar o SSISDB, consulte comparar banco de dados [SQL do Azure/pool elástico/instância gerenciada](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance). Se você usar o servidor de banco de dados SQL do Azure com pontos de extremidade de serviço de rede virtual/Instância Gerenciada com um ponto de extremidades privado para hospedar o SSISDB ou exigir acesso a dados locais sem configurar o IR auto-hospedado, você precisará unir seu Azure-SSIS IR a uma rede virtual, consulte [ingressar Azure-SSIS ir em uma rede virtual](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).
    - Confirme que a definição **Permitir acesso aos serviços do Azure** está ativada para o servidor da base de dados. Isso não é aplicável quando você usa o servidor de banco de dados SQL do Azure com pontos de extremidade de serviço de rede virtual/Instância Gerenciada com um ponto de extremidades privado para hospedar o SSISDB. Para obter mais informações, veja [Proteger a base de dados SQL do Azure](../sql-database/sql-database-security-tutorial.md#create-firewall-rules). Para habilitar essa configuração usando o PowerShell, consulte [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule).
    - Adicione o endereço IP do computador cliente ou um intervalo de endereços IP que inclui o endereço IP do computador cliente à lista de endereços IP do cliente nas configurações de firewall do servidor de banco de dados. Para obter mais informações, veja [Regras de firewall ao nível do servidor da Base de Dados SQL do Azure e ao nível da base de dados](../sql-database/sql-database-firewall-configure.md).
    - Você pode se conectar ao servidor de banco de dados usando a autenticação do SQL com suas credenciais de administrador do servidor ou a autenticação do Azure Active Directory (AAD) com a identidade gerenciada para o ADF.  Para o último, você precisa adicionar a identidade gerenciada para o ADF em um grupo do AAD com permissões de acesso para o servidor de banco de dados, consulte [habilitar a autenticação do AAD para Azure-SSIS ir](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir).
    - Confirme se o servidor de banco de dados ainda não tem um SSISDB. O provisionamento de um Azure-SSIS IR não dá suporte ao uso de um SSISDB existente.
- **Azure Resource Manager rede virtual (opcional)** . Você deve ter uma rede virtual Azure Resource Manager se pelo menos uma das seguintes condições for verdadeira:
    - Você está hospedando o SSISDB no servidor de banco de dados SQL do Azure com pontos de extremidade de serviço de rede virtual/Instância Gerenciada com um ponto de extremidades privado.
    - Você deseja se conectar a armazenamentos de dados locais de pacotes do SSIS em execução no seu Azure-SSIS IR sem configurar o IR via hospedagem interna.
- **Azure PowerShell (opcional)** . Siga as instruções sobre [como instalar e configurar Azure PowerShell](/powershell/azure/install-az-ps), se você quiser executar um script do PowerShell para provisionar seus Azure-SSIS ir.

### <a name="region-support"></a>Suporte de região

Para obter uma lista de regiões do Azure, nas quais o ADF e Azure-SSIS IR estão disponíveis no momento, confira [disponibilidade de ir do ADF + SSIS por região](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all).

### <a name="compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance"></a>Comparar banco de dados individual/pool elástico e Instância Gerenciada do Banco de Dados SQL do banco de dados SQL

A tabela a seguir compara determinados recursos do servidor de banco de dados SQL do Azure e Instância Gerenciada como eles se relacionam com o IR do Azure-SSIR:

| Funcionalidade | banco de dados individual/pool elástico| Instância Gerida |
|---------|--------------|------------------|
| **Scheduling** | SQL Server Agent não está disponível.<br/><br/>Consulte [agendar uma execução de pacote no pipeline do ADF](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages?view=sql-server-2017#activity).| O agente de Instância Gerenciada está disponível. |
| **Autenticação** | Você pode criar SSISDB com um usuário de banco de dados independente representando qualquer grupo do AAD com a identidade gerenciada do ADF como um membro na função **db_owner** .<br/><br/>Consulte [habilitar a autenticação do Azure ad para criar o SSISDB no servidor do banco de dados SQL do Azure](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database). | Você pode criar SSISDB com um usuário de banco de dados independente representando a identidade gerenciada do ADF. <br/><br/>Consulte [habilitar a autenticação do Azure ad para criar o SSISDB no instância gerenciada do banco de dados SQL do Azure](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database-managed-instance). |
| **Camada de serviços** | Ao criar Azure-SSIS IR com o servidor de banco de dados SQL do Azure, você pode selecionar a camada de serviço para SSISDB. Há várias camadas de serviço. | Quando você cria Azure-SSIS IR com seu Instância Gerenciada, não é possível selecionar a camada de serviço para SSISDB. Todos os bancos de dados em seu Instância Gerenciada compartilham o mesmo recurso alocado para essa instância. |
| **Rede virtual** | O dá suporte somente a Azure Resource Manager redes virtuais para o Azure-SSIS IR ingressar se você usar o servidor de banco de dados SQL do Azure com pontos de extremidade de serviço de rede virtual ou exigir acesso a armazenamentos de dados locais sem configurar o IR via hospedagem interna. | O dá suporte a apenas Azure Resource Manager redes virtuais para o Azure-SSIS IR ingressar. A rede virtual é necessária quando você não habilita um ponto de extremidade público para sua Instância Gerenciada.<br/><br/>Se você ingressar seu Azure-SSIS IR para a mesma rede virtual que seu Instância Gerenciada, verifique se o Azure-SSIS IR está em uma sub-rede diferente da Instância Gerenciada. Se você ingressar seu Azure-SSIS IR em uma rede virtual diferente de sua Instância Gerenciada, recomendamos um emparelhamento de rede virtual ou rede virtual para conexão de rede virtual. Consulte [conectar seu aplicativo ao instância gerenciada do banco de dados SQL do Azure](../sql-database/sql-database-managed-instance-connect-app.md). |
| **Transações distribuídas** | Com suporte por meio de transações elásticas. Não há suporte para transações do Microsoft Coordenador de Transações Distribuídas (MSDTC). Se seus pacotes SSIS usam o MSDTC para coordenar transações distribuídas, considere migrar para transações elásticas para o banco de dados SQL do Azure. Para obter mais informações, consulte [transações distribuídas entre bancos de dados de nuvem](../sql-database/sql-database-elastic-transactions-overview.md). | Não suportado. |
| | | |

## <a name="azure-portal"></a>Portal do Azure

Nesta seção, você usará portal do Azure, especificamente a interface do usuário do ADF/app, para criar Azure-SSIS IR.

### <a name="create-a-data-factory"></a>Criar uma fábrica de dados

Para criar o ADF por meio do portal do Azure, siga as instruções detalhadas em [criar ADF por meio do artigo da interface do usuário](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory) e selecione **fixar no painel** enquanto faz isso para permitir o acesso rápido após sua criação. 

Quando o ADF for criado, abra sua página de visão geral no portal do Azure e clique no bloco **criar & monitor** para iniciar sua página de **introdução** em uma guia separada, em que você pode continuar a criar seu Azure-SSIS ir.   

### <a name="provision-an-azure-ssis-integration-runtime"></a>Aprovisionar um runtime de integração de SSIS do Azure

1. Na página **de introdução,** clique no bloco **configurar o SSIS Integration Runtime** .

   ![Configurar o mosaico do Integration Runtime do SSIS](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

2. Na página **Definições Gerais** da **Configuração do Runtime de Integração**, conclua os seguintes passos:

   ![Definições gerais](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

    a. Em **Nome**, introduza o nome do seu runtime de integração.

    b. Em **Descrição**, introduza a descrição do seu runtime de integração.

    c. Em **Localização**, selecione a localização do seu runtime de integração. Apenas são apresentadas as localizações suportadas. Recomendamos que selecione a mesma localização do seu servidor de base de dados para alojar a SSISDB.

    d. Em **Tamanho do Nó**, selecione o tamanho de nó do cluster do runtime de integração. Apenas são apresentados os tamanhos de nó suportados. Selecione um tamanho de nó grande (aumentar verticalmente), se pretender vários pacotes de computação/memória intensiva.

    e. Em **Numero de Nós**, selecione o número de nós do cluster do runtime de integração. Apenas são apresentados os números de nó suportados. Selecione um cluster grande com vários nós (aumentar horizontalmente), se pretender executar muitos pacotes em paralelo.

    f. Para **edição/licença**, selecione SQL Server edição/licença para o tempo de execução de integração: Standard ou Enterprise. Selecione Enterprise, se pretender utilizar as funcionalidades avançadas/premium no seu runtime de integração.

    g. Para **economizar dinheiro**, selecione a opção benefício híbrido do Azure (AHB) para seu tempo de execução de integração: Sim ou não. Selecione Sim, se pretender trazer a sua própria licença do SQL Server com o Software Assurance para beneficiar das reduções de custos com a utilização híbrida.

    h. Clique em **Seguinte**.

3. Na página **Definições de SQL**, conclua os seguintes passos:

   ![Definições de SQL](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

    a. Na caixa de seleção **criar catálogo do SSIS...** , selecione o modelo de implantação para que os pacotes sejam executados no seu Azure-SSIS ir: Modelo de implantação de projeto onde os pacotes são implantados no SSISDB hospedado pelo seu servidor de banco de dados ou modelo de implantação de pacote onde os pacotes são implantados em seus sistemas de arquivos/compartilhamentos de arquivos/arquivos Se você verificá-lo, precisará colocar seu próprio servidor de banco de dados para hospedar o SSISDB, que criaremos e gerenciaremos em seu nome.
   
    b. Em **Subscrição**, selecione a subscrição do Azure que tem o servidor da base de dados para alojar a SSISDB. 

    c. Em **Localização**, selecione a mesma localização do seu servidor de base de dados para alojar a SSISDB. Recomendamos que selecione a mesma localização do seu runtime de integração. 

    d. Em **Ponto Final do Servidor de Base de Dados do Catálogo**, selecione o ponto final do seu servidor de base de dados para alojar a SSISDB. Com base no servidor de bases de dados selecionado, a SSISDB pode ser criada em seu nome como uma base de dados individual, parte de um conjunto elástico, ou numa Instância Gerida e acessível na rede pública ou ao associar uma rede virtual. Para obter orientação sobre como escolher o tipo de servidor de banco de dados para hospedar o SSISDB, consulte comparar banco de dados [SQL do Azure/pool elástico/instância gerenciada](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance). Se você selecionar servidor de banco de dados SQL do Azure com pontos de extremidade de serviço de rede virtual/Instância Gerenciada com um ponto de extremidades privado para hospedar o SSISDB ou exigir acesso a dados locais sem configurar o IR auto-hospedado, você precisará unir seu Azure-SSIS IR a uma rede virtual , consulte [ingressar Azure-SSIS ir em uma rede virtual](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). 

    e. Na caixa de seleção **usar autenticação do AAD...** , selecione o método de autenticação para o servidor de banco de dados para hospedar o SSISDB: Autenticação do SQL ou a autenticação do AAD com a identidade gerenciada para o ADF. Se você verificá-lo, precisará adicionar a identidade gerenciada para o ADF em um grupo do AAD com permissões de acesso ao seu servidor de banco de dados, consulte [habilitar a autenticação do AAD para Azure-SSIS ir](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir). 

    f. Em **Nome de Utilizador Administrador**, introduza o nome de utilizador de autenticação SQL Server do servidor de base de dados para alojar a SSISDB. 

    g. Em **Palavra-passe de Administrador**, introduza a palavra-passe de autenticação SQL Server do servidor de base de dados para alojar a SSISDB. 

    h. Para **camada de serviço do banco de dados do catálogo**, selecione a camada de serviço do servidor de banco de dados para hospedar o SSISDB: Camada Basic/Standard/Premium ou nome do pool elástico. 

    i. Clique em **Testar Ligação** e se tiver êxito, clique em **Seguinte**. 

4. Na página **Definições Avançadas**, conclua os seguintes passos:

    ![Definições avançadas](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

    a. Para **Máximo de Execuções Paralelas por Nó**, selecione o número máximo de pacotes para executar em simultâneo por nó do cluster no runtime de integração. Apenas são apresentados os números de pacotes suportados. Selecione um número baixo, se desejar usar mais de um núcleo para executar um único pacote grande/pesado que seja de computação/uso intensivo de memória. Selecione um número elevado, se pretender executar um ou mais pacotes pequenos/leves num único núcleo.

    b. Para **URI de SAS do Contentor de Configuração Personalizada**, opcionalmente, introduza o UIR (Uniform Resource Identifier) da SAS (Assinatura de Acesso Partilhado) do seu contentor do Azure Storage Blob onde estão armazenados o script de configuração e os respetivos ficheiros, veja [Configuração Personalizada do Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup).

5. Na caixa de seleção **selecionar uma rede virtual...** , selecione se deseja unir seu tempo de execução de integração a uma rede virtual. Verifique se você usa o servidor de banco de dados SQL do Azure com pontos de extremidade de serviço de rede virtual/Instância Gerenciada com um ponto de extremidades privado para hospedar o SSISDB ou exigir acesso a dados locais, ou seja, você tem fontes/destinos de dados locais em seus pacotes do SSIS, sem Configurando o IR auto-hospedado, consulte [ingressar Azure-SSIS ir em uma rede virtual](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). Se você verificá-lo, conclua as seguintes etapas:

   ![Configurações avançadas com rede virtual](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

    a. Para **assinatura**, selecione a assinatura do Azure que tem sua rede virtual.

    b. Para **local**, o mesmo local do tempo de execução de integração é selecionado.

    c. Para **tipo**, selecione o tipo de sua rede virtual: Clássico ou Azure Resource Manager. Recomendamos que você selecione Azure Resource Manager rede virtual, uma vez que a rede virtual clássica será preterida em breve.

    d. Para **nome da VNet**, selecione o nome da sua rede virtual. Essa rede virtual deve ser a mesma usada para o servidor de banco de dados SQL do Azure com pontos de extremidade de serviço de rede virtual/Instância Gerenciada em uma rede virtual para hospedar o SSISDB ou aquele conectado à sua rede local.

    e. Para **nome da sub-rede**, selecione o nome da sub-rede para sua rede virtual. Isso deve ser uma sub-rede diferente daquela usada para Instância Gerenciada em uma rede virtual para hospedar o SSISDB.

6. Na caixa de seleção **Configurar auto-hospedado...** , selecione se deseja configurar o infravermelho auto-hospedado como um proxy para o seu Azure-SSIS ir, consulte Configurar o ir para o [auto-hospedado como um proxy](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis). Se você verificá-lo, conclua as seguintes etapas:

   ![Configurações avançadas com o IR auto-hospedado](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

    a. Para **Integration Runtime auto-hospedados**, selecione o ir auto-hospedado existente como um proxy para Azure-SSIS ir.

    b. Para o **serviço vinculado de armazenamento de preparo**, selecione o serviço vinculado do armazenamento de BLOBs do Azure existente ou crie um novo para preparo.

    c. Para o **caminho de preparo**, especifique um contêiner de blob no armazenamento de BLOBs do Azure selecionado ou deixe-o vazio para usar um padrão para preparo.

7. Clique em **validação de VNet** e em **Avançar**. 

8. Na página **Resumo** , examine todas as configurações de provisionamento, marque os links de documentação recomendados e clique em **concluir** para iniciar a criação do tempo de execução de integração.

    > [!NOTE]
    > Excluindo qualquer hora de instalação personalizada, esse processo deve ser concluído em 5 minutos, mas pode levar aproximadamente 20-30 minutos para Azure-SSIS IR ingressar em uma rede virtual.
    >
    > Se você usar o SSISDB, o serviço ADF se conectará ao servidor de banco de dados para preparar o SSISDB. Ele também configura permissões e configurações para sua rede virtual, se especificado, e une seu Azure-SSIS IR à rede virtual.
    > 
    > Quando você provisiona um Azure-SSIS IR, o Access redistribuível e o Azure Feature Pack para SSIS também são instalados. Esses componentes fornecem conectividade com arquivos do Excel/Access e várias fontes de dados do Azure, além das fontes de dados já suportadas por componentes internos. Você também pode instalar componentes adicionais, consulte [instalação personalizada para Azure-SSIS ir](how-to-configure-azure-ssis-ir-custom-setup.md).

7. Na janela **Ligações**, mude para **Runtimes de Integração** se for necessário. Clique em **Atualizar** para atualizar o estado.

   ![Estado da criação](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

8. Use a coluna links em **ações** para parar/iniciar, editar ou excluir o tempo de execução de integração. Utilize a última ligação para ver o código JSON para o runtime de integração. Os botões de editar e eliminar estão ativados, apenas quando a resposta a incidentes está parada.

   ![Azure SSIS IR - ações](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)

### <a name="azure-ssis-integration-runtimes-in-the-portal"></a>Runtime de integração de SSIS do Azure no portal

1. Na IU do Azure Data Factory, mude para o separador **Editar**, clique em **Ligações**e, em seguida, mude para o separador **Runtimes de Integração** para ver os runtimes de integração existente na sua fábrica de dados.

   ![Exibir IRs existente](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

2. Clique em **Novo** para criar um novo SSIS IR do Azure.

   ![Runtime de integração através do menu](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

3. Para criar um tempo de execução de integração de SSIS do Azure, clique em **Novo** conforme mostrado na imagem.

4. Na janela de Configuração do Integration Runtime, selecione **pacotes SSIS existentes de migração lift-and-shift a executar no Azure**e, em seguida, clique em **Seguinte**.

   ![Especifique o tipo de runtime de integração](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

5. Veja a secção [Aprovisionar um runtime de integração do Azure SSIS](#provision-an-azure-ssis-integration-runtime) para os restantes passos para configurar um SSIS IR do Azure.

## <a name="azure-powershell"></a>Azure PowerShell

Nesta seção, você usa o Azure PowerShell para criar um Azure-SSIS IR.

### <a name="create-variables"></a>Criar variáveis

Copie e cole os seguintes valores de script-especifique para as variáveis. 

```powershell
### Azure Data Factory information
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - Must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime information - This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2
# Azure-SSIS IR edition/license info: Standard or Enterprise
$AzureSSISEdition = "Standard" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported, but for other nodes, up to (2 x number of cores) are currently supported
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database server with virtual network service endpoints/Managed Instance with a private endpoint/on-premises data without configuring Self-Hosted IR, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Please use the same subnet as the one used for your Azure SQL Database server with virtual network service endpoints or a different subnet than the one used for your Managed Instance with a private endpoint

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or Managed Instance name.DNS prefix.database.windows.net or Managed Instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, please ensure that there is no existing SSISDB on your database server, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure Active Directory (AAD)
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for AAD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for AAD authentication]"
# For the basic pricing tier, specify "Basic", not "B" - For standard/premium/elastic pool tiers, specify "S0", "S1", "S2", "S3", etc., see https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for Managed Instance]"
```

### <a name="sign-in-and-select-subscription"></a>Entrar e selecionar assinatura

Adicione o seguinte código ao script para entrar e selecione sua assinatura do Azure:

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName
```

### <a name="validate-the-connection-to-database-server"></a>Validar a conexão com o servidor de banco de dados

Adicione o script a seguir para validar o servidor/Instância Gerenciada do banco de dados SQL do Azure.

```powershell
# Validate only if you use SSISDB and do not use VNet or AAD authentication
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

### <a name="configure-virtual-network"></a>Configurar uma rede virtual

Adicione o seguinte script para configurar automaticamente permissões/definições de rede virtual para o integration runtime do Azure-SSIS para associar.

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

Crie um [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) usando o comando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) . Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos como um grupo.

Se o grupo de recursos já existir, não copie esse código para o script. 

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

Execute os comandos a seguir para criar um tempo de execução de integração do Azure-SSIS que executa pacotes do SSIS no Azure.

Se você não usar o SSISDB, poderá omitir os parâmetros CatalogServerEndpoint, CatalogPricingTier e CatalogAdminCredential.

Se você não usar o servidor de banco de dados SQL do Azure com pontos de extremidade de serviço de rede virtual/Instância Gerenciada com um ponto de extremidades privado para hospedar o SSISDB ou exigir acesso a dados locais, poderá omitir os parâmetros VNetId e Subnet ou passar valores vazios para eles. Você também pode omiti-los se configurar o IR auto-hospedado como um proxy para o Azure-SSIS IR acessar dados no local. Caso contrário, você não pode omiti-los e deve passar valores válidos de sua configuração de rede virtual, consulte [ingressar Azure-SSIS ir em uma rede virtual](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

Se você usar Instância Gerenciada para hospedar o SSISDB, poderá omitir o parâmetro CatalogPricingTier ou passar um valor vazio para ele. Caso contrário, você não pode omiti-lo e deve passar um valor válido da lista de tipos de preço com suporte para o banco de dados SQL do Azure, consulte [limites de recursos do banco de dados SQL](../sql-database/sql-database-resource-limits.md).

Se você usar a autenticação do Azure Active Directory (AAD) com a identidade gerenciada para o ADF se conectar ao servidor de banco de dados, poderá omitir o parâmetro CatalogAdminCredential, mas deverá adicionar a identidade gerenciada do ADF a um grupo do AAD com acesso permissões para o servidor de banco de dados, consulte [habilitar autenticação do AAD para Azure-SSIS ir](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir). Caso contrário, você não pode omiti-lo e deve passar um objeto válido formado por seu nome de usuário do administrador do servidor e senha para autenticação do SQL.

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
       
# Add CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -CatalogServerEndpoint $SSISDBServerEndpoint `
                                               -CatalogPricingTier $SSISDBPricingTier

    if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword)) # Add CatalogAdminCredential parameter if you do not use AAD authentication
    {
        $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
        $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                                   -DataFactoryName $DataFactoryName `
                                                   -Name $AzureSSISName `
                                                   -CatalogAdminCredential $serverCreds
    }
}

# Add SetupScriptContainerSasUri parameter if you use custom setup
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}
```

### <a name="start-integration-runtime"></a>Iniciar o integration runtime

Execute os comandos a seguir para iniciar o tempo de execução de integração do Azure-SSIS.

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
> Excluindo qualquer hora de instalação personalizada, esse processo deve ser concluído em 5 minutos, mas pode levar aproximadamente 20-30 minutos para Azure-SSIS IR ingressar em uma rede virtual.
>
> Se você usar o SSISDB, o serviço ADF se conectará ao servidor de banco de dados para preparar o SSISDB. Ele também configura permissões e configurações para sua rede virtual, se especificado, e une seu Azure-SSIS IR à rede virtual.
> 
> Quando você provisiona um Azure-SSIS IR, o Access redistribuível e o Azure Feature Pack para SSIS também são instalados. Esses componentes fornecem conectividade com arquivos do Excel/Access e várias fontes de dados do Azure, além das fontes de dados já suportadas por componentes internos. Você também pode instalar componentes adicionais, consulte [instalação personalizada para Azure-SSIS ir](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="full-script"></a>Script completo

Este é o script completo que cria um tempo de execução de integração do Azure-SSIS.

```powershell
### Azure Data Factory information
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - Must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime information - This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2
# Azure-SSIS IR edition/license info: Standard or Enterprise
$AzureSSISEdition = "Standard" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported, but for other nodes, up to (2 x number of cores) are currently supported
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database server with virtual network service endpoints/Managed Instance with a private endpoint/on-premises data without configuring Self-Hosted IR, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Please use the same subnet as the one used with your Azure SQL Database server with virtual network service endpoints or a different subnet than the one used for your Managed Instance with a private endpoint

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or Managed Instance name.DNS prefix.database.windows.net or Managed Instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, please ensure that there is no existing SSISDB on your database server, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure Active Directory (AAD)
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for AAD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for AAD authentication]"
# For the basic pricing tier, specify "Basic", not "B" - For standard/premium/elastic pool tiers, specify "S0", "S1", "S2", "S3", etc., see https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for Managed Instance]"

### Sign in and select subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName

### Validate the connection to database server
# Validate only if you use SSISDB and do not use VNet or AAD authentication
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

### Configure virtual network
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

    if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword)) # Add CatalogAdminCredential parameter if you do not use AAD authentication
    {
        $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
        $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                                   -DataFactoryName $DataFactoryName `
                                                   -Name $AzureSSISName `
                                                   -CatalogAdminCredential $serverCreds
    }
}

# Add SetupScriptContainerSasUri parameter when you use custom setup
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}

### Start integration runtime
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

## <a name="azure-resource-manager-template"></a>Modelo Azure Resource Manager

Nesta seção, você usa o modelo de Azure Resource Manager para criar o tempo de execução de integração do Azure-SSIS. Aqui está um exemplo de explicação:

1. Crie um arquivo JSON com o seguinte modelo de Azure Resource Manager. Substitua os valores nos colchetes angulares (Coloque os contentores) pelos seus próprios valores.

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

2. Para implantar o modelo de Azure Resource Manager, execute o comando New-AzResourceGroupDeployment, conforme mostrado no exemplo a seguir, em que ADFTutorialResourceGroup é o nome do seu grupo de recursos e ADFTutorialARM. JSON é o arquivo que contém a definição de JSON para seu data factory e Azure-SSIS IR.

    ```powershell
    New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json
    ```

    Esse comando cria o data factory e Azure-SSIS IR nele, mas não inicia o IR.

3. Para iniciar seu Azure-SSIS IR, execute o comando Start-AzDataFactoryV2IntegrationRuntime:

    ```powershell
    Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "<Resource Group Name>" `
                                                 -DataFactoryName "<Data Factory Name>" `
                                                 -Name "<Azure SSIS IR Name>" `
                                                 -Force
    ```

> [!NOTE]
> Excluindo qualquer hora de instalação personalizada, esse processo deve ser concluído em 5 minutos, mas pode levar aproximadamente 20-30 minutos para Azure-SSIS IR ingressar em uma rede virtual.
>
> Se você usar o SSISDB, o serviço ADF se conectará ao servidor de banco de dados para preparar o SSISDB. Ele também configura permissões e configurações para sua rede virtual, se especificado, e une seu Azure-SSIS IR à rede virtual.
> 
> Quando você provisiona um Azure-SSIS IR, o Access redistribuível e o Azure Feature Pack para SSIS também são instalados. Esses componentes fornecem conectividade com arquivos do Excel/Access e várias fontes de dados do Azure, além das fontes de dados já suportadas por componentes internos. Você também pode instalar componentes adicionais, consulte [instalação personalizada para Azure-SSIS ir](how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="deploy-ssis-packages"></a>Implementar pacotes de SSIS

Se você usar o SSISDB, poderá implantar seus pacotes nele e executá-los em Azure-SSIS IR usando as ferramentas SSDT/SSMS que se conectam ao seu servidor de banco de dados por meio de seu ponto de extremidade do servidor.  Para o servidor/instância gerenciada do banco de dados SQL do Azure com um ponto de extremidade privado/instância gerenciada com um ponto `<server name>.database.windows.net`de extremidade público, o formato do ponto de extremidade do servidor é / / `<server name>.<dns prefix>.database.windows.net` `<server name>.public.<dns prefix>.database.windows.net,3342`, respectivamente. Se você não usar o SSISDB, poderá implantar seus pacotes em sistemas de arquivos/compartilhamentos de arquivos/arquivos do Azure e executá `dtinstall` -los em Azure-SSIS ir usando / / `dtutil` `dtexec` utilitários de linha de comando. Para obter mais informações, consulte [implantar pacotes do SSIS](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server). Em ambos os casos, você também pode executar seus pacotes implantados em Azure-SSIS IR usando a atividade executar pacote SSIS em pipelines do ADF, consulte [invocar a execução do pacote SSIS como uma atividade AAD de primeira classe](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).

## <a name="next-steps"></a>Passos seguintes

Consulte também outros tópicos Azure-SSIS IR nesta documentação:

- [Azure-SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime). Este artigo fornece informações sobre os tempos de execução de integração em geral, incluindo Azure-SSIS IR.
- [Azure-SSIS ir de monitor](monitor-integration-runtime.md#azure-ssis-integration-runtime). Este artigo mostra como recuperar e compreender as informações sobre seu Azure-SSIS IR.
- [Gerenciar Azure-SSIS ir](manage-azure-ssis-integration-runtime.md). Este artigo mostra como parar, iniciar ou excluir seu Azure-SSIS IR-ele também mostra como escalar horizontalmente o Azure-SSIS IR adicionando mais nós.
- [Ingresse Azure-SSIS ir em uma rede virtual](join-azure-ssis-integration-runtime-virtual-network.md). Este artigo fornece informações sobre como unir seu Azure-SSIS IR a uma rede virtual.