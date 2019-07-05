---
title: Criar Runtime de integração Azure-SSIS no Azure Data Factory | Documentos da Microsoft
description: Saiba como criar Runtime de integração Azure-SSIS no Azure Data Factory, para que possa implementar e executar pacotes do SSIS no Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/26/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: fb5335c8dfd94006ba3f0d8d6b890869dd9f3717
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/01/2019
ms.locfileid: "67484824"
---
# <a name="create-azure-ssis-integration-runtime-in-azure-data-factory"></a>Criar Runtime de integração Azure-SSIS no Azure Data Factory

Este tutorial disponibiliza os passos para o aprovisionamento de um Azure SQL Server Integration Services (SSIS) Integration Runtime (IR) no Azure Data Factory (ADF). Runtime de integração Azure-SSIS suporta a execução de pacotes implementados no catálogo de SSIS (SSISDB) hospedado por servidor/gerida de base de dados do Azure SQL instância (modelo de implementação do projeto) e os que são implementados em sistemas de ficheiros/ficheiro ficheiros de partilhas/do Azure (modelo de implementação do pacote). Depois do IR Azure-SSIS é aprovisionado, em seguida, pode utilizar ferramentas familiares, como o SQL Server Data Tools (SSDT) / SQL Server Management Studio (SSMS) e o comando utilitários da linha, como `dtinstall` / `dtutil` / `dtexec`ao Implemente e execute os seus pacotes no Azure.

O [Tutorial: Aprovisionar o IR Azure-SSIS](tutorial-create-azure-ssis-runtime-portal.md) mostra-lhe como criar um IR Azure-SSIS através da aplicação de portal/ADF do Azure e, opcionalmente, utilizar a base de dados do Azure SQL server/gerida instância para alojar o SSISDB. Este artigo é uma continuação do tutorial e mostra como efetue os seguintes procedimentos:

- Opcionalmente, utilize o servidor de base de dados do Azure SQL com a rede virtual serviço pontos finais/gerida instância numa rede virtual para alojar o SSISDB. Como pré-requisito, tem de configurar permissões/definições de rede virtual para o runtime de integração Azure-SSIS associar uma rede virtual.

- Opcionalmente, utilize a autenticação do Azure Active Directory (AAD) com a identidade gerida para o ADF para ligar à base de dados do Azure SQL server/gerida instância. Como pré-requisito, terá de adicionar a identidade gerida para o ADF como um utilizador de base de dados capaz de criar o SSISDB.

## <a name="overview"></a>Descrição geral

Este artigo mostra-lhe diferentes formas de aprovisionamento do Azure-SSIS IR:

- [Azure portal](#azure-portal)
- [Azure PowerShell](#azure-powershell)
- [Modelo do Azure Resource Manager](#azure-resource-manager-template)

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Subscrição do Azure**. Se ainda não tiver uma subscrição, pode criar uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/) conta.
- **Azure base de dados do SQL server/gerida instância (opcional)** . Se ainda não tiver um servidor de base de dados, crie um no portal do Azure antes de começar. ADF por sua vez criará SSISDB neste servidor de base de dados. Recomendamos que crie o servidor de base de dados na mesma região do Azure que o integration runtime. Esta configuração permite que o runtime de integração escreva registos de execução em SSISDB sem cruzamento entre regiões do Azure. 
    - Com base no servidor de bases de dados selecionado, a SSISDB pode ser criada em seu nome como uma base de dados individual, parte de um conjunto elástico, ou numa Instância Gerida e acessível na rede pública ou ao associar uma rede virtual. Para obter orientações sobre como escolher o tipo de servidor de base de dados para alojar o SSISDB, consulte [comparar o Azure SQL Database única base de dados/elastic pool/gerida instância](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance). Se utilizar a base de dados do Azure SQL server com a rede virtual serviço pontos finais/gerida instância numa rede virtual para alojar o SSISDB ou exigir acesso a dados no local, tem de associar o runtime de integração Azure-SSIS a uma rede virtual, consulte [associação do Azure-SSIS IR para um rede virtual](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).
    - Confirme que a definição **Permitir acesso aos serviços do Azure** está ativada para o servidor da base de dados. Não se aplica ao utilizar o servidor de base de dados do Azure SQL com a rede virtual serviço pontos finais/gerida instância numa rede virtual para alojar o SSISDB. Para obter mais informações, veja [Proteger a base de dados SQL do Azure](../sql-database/sql-database-security-tutorial.md#create-firewall-rules). Para ativar esta definição com o PowerShell, consulte [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule).
    - Adicione o endereço IP do computador cliente ou um intervalo de endereços IP que inclua o endereço IP do computador cliente à lista de endereços IP do cliente nas definições de firewall para o servidor de base de dados. Para obter mais informações, veja [Regras de firewall ao nível do servidor da Base de Dados SQL do Azure e ao nível da base de dados](../sql-database/sql-database-firewall-configure.md).
    - Pode ligar ao servidor da base de dados com autenticação de SQL com as suas credenciais de administrador do servidor ou autenticação do Azure Active Directory (AAD) com a identidade gerida para o ADF.  Para a última opção, terá de adicionar a identidade gerida para o ADF para um grupo do AAD com permissões de acesso para o servidor de base de dados, consulte [autenticação do AAD ativar para IR Azure-SSIS](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir).
    - Confirme que o servidor de base de dados não tem um SSISDB já. O aprovisionamento de um IR Azure-SSIS não suporta a utilização de um SSISDB existente.
- **Rede do Azure Resource Manager virtual (opcional)** . Tem de ter uma rede virtual do Azure Resource Manager se, pelo menos, uma das seguintes condições for verdadeira:
    - Está a alojar o SSISDB no servidor de base de dados do Azure SQL com a rede virtual serviço pontos finais/gerida instância numa rede virtual.
    - Se pretende ligar a dados no local arquivos de pacotes do SSIS em execução no seu ir Azure-SSIS.
- **O Azure PowerShell (opcional)** . Siga as instruções [como instalar e configurar o Azure PowerShell](/powershell/azure/install-az-ps), se quiser executar um script do PowerShell para aprovisionar o ir Azure-SSIS.

### <a name="region-support"></a>Suporte de região

Para obter uma lista de regiões do Azure, no qual o ADF e IR Azure-SSIS estão atualmente disponíveis, consulte [ADF + IR do SSIS disponibilidade por região](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all).

### <a name="compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance"></a>Comparar o conjunto de elástico/base de dados única da base de dados SQL e SQL Database Managed Instance

A tabela seguinte compara a determinados recursos do servidor de base de dados do Azure SQL e a instância gerida como sua relação com o runtime de integração Azure-SSIR:

| Funcionalidade | conjunto elástico/base de dados única| Instância Gerida |
|---------|--------------|------------------|
| **Agendamento** | SQL Server Agent não está disponível.<br/><br/>Ver [agendar uma execução de pacotes no pipeline do ADF](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages?view=sql-server-2017#activity).| Agente de instância gerida está disponível. |
| **Autenticação** | É possível criar o SSISDB com um utilizador de base de dados contida que representa a qualquer grupo do AAD com a identidade gerida de sua ADF como membro de **db_owner** função.<br/><br/>Ver [autenticação de ativar o Azure AD para criar o SSISDB no servidor de base de dados do Azure SQL](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database). | É possível criar o SSISDB com um utilizador de base de dados contida que representa a identidade gerida de sua ADF. <br/><br/>Ver [autenticação de ativar o Azure AD para criar o SSISDB na instância gerida da base de dados SQL do Azure](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database-managed-instance). |
| **Camada de serviços** | Quando cria o IR Azure-SSIS com o seu servidor de base de dados do Azure SQL, pode selecionar o escalão de serviço para SSISDB. Existem várias camadas de serviços. | Quando cria o IR Azure-SSIS com a sua instância gerida, é possível selecionar o escalão de serviço para SSISDB. Todas as bases de dados na sua instância gerida partilham o mesmo recurso atribuído a essa instância. |
| **Rede virtual** | Oferece suporte apenas do Azure Resource Manager as redes virtuais para o runtime de integração Azure-SSIS associar se utilizar o servidor de base de dados do Azure SQL com pontos finais de serviço de rede virtual ou precisar de acesso a arquivos de dados no local. | Oferece suporte apenas do Azure Resource Manager as redes virtuais para o runtime de integração Azure-SSIS associar. A rede virtual é sempre necessária.<br/><br/>Se associar o runtime de integração Azure-SSIS à mesma rede virtual que sua instância gerida, certifique-se de que o runtime de integração Azure-SSIS é numa sub-rede diferente, a sua instância gerida. Se associar o runtime de integração Azure-SSIS a uma rede virtual diferente do que a sua instância gerida, recomendamos um peering de rede virtual ou rede virtual para a ligação de rede virtual. Ver [ligar a sua aplicação para a instância gerida da base de dados SQL do Azure](../sql-database/sql-database-managed-instance-connect-app.md). |
| **Transações distribuídas** | Compatíveis por meio de transações elásticas. Transações do coordenador de transações distribuídas ' (MSDTC) da Microsoft não são suportadas. Se seus pacotes do SSIS usar MSDTC para coordenar transações distribuídas, considere a migração para transações elásticas da base de dados do Azure SQL. Para mais informações, veja [transações distribuídas entre bases de dados de cloud](../sql-database/sql-database-elastic-transactions-overview.md). | Não suportado. |
| | | |

## <a name="azure-portal"></a>Portal do Azure

Nesta seção, utiliza o portal do Azure, especificamente ADF Interface de utilizador (IU) / aplicação, para criar o ir Azure-SSIS.

### <a name="create-a-data-factory"></a>Criar uma fábrica de dados

1. Abra o browser **Microsoft Edge** ou **Google Chrome**. Atualmente, a IU do Data Factory é suportada apenas nos browsers Microsoft Edge e Google Chrome.
1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
1. Clique em **Novo** no menu da esquerda, clique em **Dados + Análise** e, em seguida, em **Data Factory**.

   ![Novo -> DataFactory](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)

1. Na página **Nova fábrica de dados**, introduza **MyAzureSsisDataFactory** no **nome**.

   ![Página Nova fábrica de dados](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)

   O nome do Azure Data Factory deve ser **globalmente exclusivo**. Se receber o seguinte erro, altere o nome da fábrica de dados (por exemplo, oseunomeMyAzureSsisDataFactory) e tente criá-la novamente. Veja o artigo [Data Factory – Naming Rules](naming-rules.md) (Data Factory – Regras de Nomenclatura) para obter as regras de nomenclatura dos artefactos do Data Factory.

   `Data factory name “MyAzureSsisDataFactory” is not available`

1. Selecione a sua subscrição do **Azure** na qual pretende criar a fábrica de dados.
1. No **Grupo de Recursos**, siga um destes passos:

   - Selecione **Utilizar existente** e selecione um grupo de recursos já existente na lista pendente.
   - Selecione **Criar novo** e introduza o nome de um grupo de recursos.

   Para saber mais sobre os grupos de recursos, veja [Utilizar grupos de recursos para gerir os recursos do Azure](../azure-resource-manager/resource-group-overview.md).

1. Selecione **V2** para a **versão**.
1. Selecione a **localização** da fábrica de dados. Só são mostradas na lista as localizações que são suportadas para a criação de fábricas de dados.
1. Selecione **Afixar ao dashboard**.
1. Clique em **Criar**.
1. No dashboard, verá o mosaico seguinte com o estado: **Implementar fábrica de dados**.

    ![Mosaico “implementar a fábrica de dados”](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)

1. Depois de concluída a criação, vai ver a página **Data Factory**, conforme mostrado na imagem.

    ![Home page da fábrica de dados](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)

1. Clique em **Criar e Monitorizar** para iniciar a interface de utilizador (IU) do Data Factory num separador à parte.

### <a name="provision-an-azure-ssis-integration-runtime"></a>Aprovisionar um runtime de integração de SSIS do Azure

1. Na página de introdução, clique no mosaico **Configurar o Integration Runtime do SSIS**.

   ![Configurar o mosaico do Integration Runtime do SSIS](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

2. Na página **Definições Gerais** da **Configuração do Runtime de Integração**, conclua os seguintes passos:

   ![Definições gerais](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

    a. Em **Nome**, introduza o nome do seu runtime de integração.

    b. Em **Descrição**, introduza a descrição do seu runtime de integração.

    c. Em **Localização**, selecione a localização do seu runtime de integração. Apenas são apresentadas as localizações suportadas. Recomendamos que selecione a mesma localização do seu servidor de base de dados para alojar a SSISDB.

    d. Em **Tamanho do Nó**, selecione o tamanho de nó do cluster do runtime de integração. Apenas são apresentados os tamanhos de nó suportados. Selecione um tamanho de nó grande (aumentar verticalmente), se pretender vários pacotes de computação/memória intensiva.

    e. Em **Numero de Nós**, selecione o número de nós do cluster do runtime de integração. Apenas são apresentados os números de nó suportados. Selecione um cluster grande com vários nós (aumentar horizontalmente), se pretender executar muitos pacotes em paralelo.

    f. Para **licença deedição/** , selecione a edição/licença do SQL Server para o runtime de integração: Standard ou Enterprise. Selecione Enterprise, se pretender utilizar as funcionalidades avançadas/premium no seu runtime de integração.

    g. Para **poupar dinheiro**, selecione a opção de Azure híbrido benefício (AHB) para o runtime de integração: Ou No. Selecione Sim, se pretender trazer a sua própria licença do SQL Server com o Software Assurance para beneficiar das reduções de custos com a utilização híbrida.

    h. Clique em **Seguinte**.

3. Na página **Definições de SQL**, conclua os seguintes passos:

   ![Definições de SQL](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

    a. No **catálogo de SSIS criar...**  caixa de verificação, selecione a implementação de modelos de pacotes executar o runtime de integração Azure-SSIS: Arquivos de modelo de implementação do projeto em que os pacotes são implantados em SSISDB alojado pelo seu servidor de base de dados ou modelo de implementação de pacote em que os pacotes são implementados na sua partilhas de sistemas/ficheiro de arquivo/do Azure. Se o confira, terá de colocar o seu próprio servidor de base de dados para alojar o SSISDB que iremos criar e gerir em seu nome.
   
    b. Em **Subscrição**, selecione a subscrição do Azure que tem o servidor da base de dados para alojar a SSISDB. 

    c. Em **Localização**, selecione a mesma localização do seu servidor de base de dados para alojar a SSISDB. Recomendamos que selecione a mesma localização do seu runtime de integração. 

    d. Em **Ponto Final do Servidor de Base de Dados do Catálogo**, selecione o ponto final do seu servidor de base de dados para alojar a SSISDB. Com base no servidor de bases de dados selecionado, a SSISDB pode ser criada em seu nome como uma base de dados individual, parte de um conjunto elástico, ou numa Instância Gerida e acessível na rede pública ou ao associar uma rede virtual. Para obter orientações sobre como escolher o tipo de servidor de base de dados para alojar o SSISDB, consulte [comparar o Azure SQL Database única base de dados/elastic pool/gerida instância](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance). Se selecionar servidor de base de dados do Azure SQL com pontos finais do serviço de rede virtual/gerida instância de uma rede virtual para alojar o SSISDB ou exigem acesso a dados no local, terá de associar o runtime de integração Azure-SSIS a uma rede virtual, consulte [associação do Azure-SSIS IR para uma rede virtual](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). 

    e. No **autenticação do AAD de utilização...**  caixa de verificação, selecione o método de autenticação para o seu servidor de base de dados para alojar o SSISDB: Autenticação do SQL ou autenticação do AAD com a identidade gerida para o ADF. Se o confira, terá de adicionar a identidade gerida para o ADF para um grupo do AAD com permissões de acesso ao seu servidor de base de dados, consulte [autenticação do AAD ativar para IR Azure-SSIS](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir). 

    f. Em **Nome de Utilizador Administrador**, introduza o nome de utilizador de autenticação SQL Server do servidor de base de dados para alojar a SSISDB. 

    g. Em **Palavra-passe de Administrador**, introduza a palavra-passe de autenticação SQL Server do servidor de base de dados para alojar a SSISDB. 

    h. Para **camada de serviço de base de dados de catálogo**, selecione a camada de serviço para o servidor de base de dados para alojar o SSISDB: Escalão básico/Standard/Premium ou o nome do conjunto elástico. 

    i. Clique em **Testar Ligação** e se tiver êxito, clique em **Seguinte**. 

4. Na página **Definições Avançadas**, conclua os seguintes passos:

    ![Definições avançadas](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

    a. Para **Máximo de Execuções Paralelas por Nó**, selecione o número máximo de pacotes para executar em simultâneo por nó do cluster no runtime de integração. Apenas são apresentados os números de pacotes suportados. Selecione um número baixo, se pretender utilizar mais de um núcleo para executar um único pacote de grandes/pesados-los, o que é a computação/memória-intensiva. Selecione um número elevado, se pretender executar um ou mais pacotes pequenos/leves num único núcleo.

    b. Para **URI de SAS do Contentor de Configuração Personalizada**, opcionalmente, introduza o UIR (Uniform Resource Identifier) da SAS (Assinatura de Acesso Partilhado) do seu contentor do Azure Storage Blob onde estão armazenados o script de configuração e os respetivos ficheiros, veja [Configuração Personalizada do Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup).

5. No **selecionar uma rede virtual...**  caixa de verificação, selecione se pretende associar o runtime de integração a uma rede virtual. Verifique-o se utilizar o servidor de base de dados do Azure SQL com a rede virtual serviço pontos finais/gerida instância numa rede virtual para alojar o SSISDB ou precisar de acesso a dados no local. Isto é, tiver origens/destinos de dados no local em seus pacotes do SSIS, veja [associar a IR Azure-SSIS a uma rede virtual](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). Se o confira, conclua os seguintes passos:

   ![Definições avançadas com a rede virtual](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

    a. Para **subscrição**, selecione a subscrição do Azure que tenha a sua rede virtual.

    b. Para **localização**, a mesma localização do seu integration runtime está selecionada.

    c. Para **tipo**, selecione o tipo de sua rede virtual: Clássico ou do Azure Resource Manager. Recomendamos que selecionar a rede virtual do Azure Resource Manager, desde clássico rede virtual será preterida em breve.

    d. Para **nome da VNet**, selecione o nome da sua rede virtual. Esta rede virtual deve ser o mesmo usado para o servidor de base de dados do Azure SQL com a rede virtual serviço pontos finais/gerida instância numa rede virtual para alojar o SSISDB ou o um ligado à sua rede no local.

    e. Para **nome da sub-rede**, selecione o nome da sub-rede da rede virtual. Deve ser uma sub-rede diferente daquele usado para a instância gerida numa rede virtual para alojar o SSISDB.

6. Clique em **validação de VNet** e se tiver êxito, clique em **concluir** para iniciar a criação do Azure-SSIS integration runtime.

    > [!NOTE]
    > Excluindo qualquer tempo de configuração personalizada, este processo deve ser concluído dentro de 5 minutos, mas pode demorar cerca de 20 a 30 minutos para IR Azure-SSIS adesão a uma rede virtual.
    >
    > Se usar o SSISDB, o serviço do ADF irá ligar ao seu servidor de base de dados para preparar o SSISDB. Também configura permissões e definições da sua rede virtual, se for especificado e associa o runtime de integração Azure-SSIS para a rede virtual.
    > 
    > Quando aprovisiona um IR Azure-SSIS, acesso redistribuível e pacote de funcionalidades do Azure para SSIS também são instalados. Estes componentes fornecem conectividade aos ficheiros de Excel/acesso e de várias origens de dados do Azure, além das origens de dados já suportadas pelos componentes incorporados. Também pode instalar componentes adicionais, consulte [configuração personalizada para IR Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md).

7. Na janela **Ligações**, mude para **Runtimes de Integração** se for necessário. Clique em **Atualizar** para atualizar o estado.

   ![Estado da criação](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

8. Use os links em **ações** coluna para iniciar/parar, editar ou eliminar o runtime de integração. Utilize a última ligação para ver o código JSON para o runtime de integração. Os botões de editar e eliminar estão ativados, apenas quando a resposta a incidentes está parada.

   ![Azure SSIS IR - ações](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)

### <a name="azure-ssis-integration-runtimes-in-the-portal"></a>Runtime de integração de SSIS do Azure no portal

1. Na IU do Azure Data Factory, mude para o separador **Editar**, clique em **Ligações**e, em seguida, mude para o separador **Runtimes de Integração** para ver os runtimes de integração existente na sua fábrica de dados.

   ![Ver IRs existentes](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

2. Clique em **Novo** para criar um novo SSIS IR do Azure.

   ![Runtime de integração através do menu](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

3. Para criar um tempo de execução de integração de SSIS do Azure, clique em **Novo** conforme mostrado na imagem.

4. Na janela de Configuração do Integration Runtime, selecione **pacotes SSIS existentes de migração lift-and-shift a executar no Azure**e, em seguida, clique em **Seguinte**.

   ![Especifique o tipo de runtime de integração](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

5. Veja a secção [Aprovisionar um runtime de integração do Azure SSIS](#provision-an-azure-ssis-integration-runtime) para os restantes passos para configurar um SSIS IR do Azure.

## <a name="azure-powershell"></a>Azure PowerShell

Nesta secção, vai utilizar o Azure PowerShell para criar um ir Azure-SSIS.

### <a name="create-variables"></a>Criar variáveis

Copie e cole o seguinte script – especifique valores para as variáveis. 

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
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database server with virtual network service endpoints/Managed Instance in a virtual network/on-premises data, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Please use the same subnet as the one used with your Azure SQL Database server with virtual network service endpoints or a different subnet than the one used for your Managed Instance in a virtual network

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or Managed Instance name.DNS prefix.database.windows.net or Managed Instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, please ensure that there is no existing SSISDB on your database server, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure Active Directory (AAD)
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for AAD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for AAD authentication]"
# For the basic pricing tier, specify "Basic", not "B" - For standard/premium/elastic pool tiers, specify "S0", "S1", "S2", "S3", etc., see https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for Managed Instance]"
```

### <a name="sign-in-and-select-subscription"></a>Iniciar sessão e selecione a subscrição

Adicione o seguinte código ao script para iniciar sessão e selecione a sua subscrição do Azure:

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName
```

### <a name="validate-the-connection-to-database-server"></a>Validar a ligação ao servidor de base de dados

Adicione o seguinte script para validar a sua base de dados do Azure SQL server/gerida instância.

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

Criar uma [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) utilizando o [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) comando. Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos como um grupo.

Se o grupo de recursos já existir, não copie este código ao seu script. 

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

Execute os seguintes comandos para criar um runtime de integração Azure-SSIS que execute pacotes de SSIS no Azure.

Se não utilizar SSISDB, pode omitir CatalogServerEndpoint CatalogPricingTier e CatalogAdminCredential parâmetros.

Se não utilizar o servidor de base de dados do Azure SQL com a rede virtual serviço pontos finais/gerida instância numa rede virtual para alojar o SSISDB ou exigem acesso a dados no local, pode omitir parâmetros VNetId e a sub-rede ou passar valores vazios para eles. Caso contrário, não é possível omiti-los e tem passar valores válidos da sua configuração de rede virtual, consulte [associar a IR Azure-SSIS a uma rede virtual](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

Se utilizar a instância gerida para alojar o SSISDB, pode omitir o parâmetro de CatalogPricingTier ou passar um valor vazio para o mesmo. Caso contrário, não é possível omiti-lo e tem passar um valor válido na lista de suportadas escalões de preço para a base de dados do Azure SQL, consulte [limites de recursos da base de dados SQL](../sql-database/sql-database-resource-limits.md).

Se utilizar a autenticação do Azure Active Directory (AAD) com a identidade gerida para o ADF para ligar ao servidor da base de dados, pode omitir o parâmetro CatalogAdminCredential, mas tem de adicionar a identidade gerida para o ADF para um grupo do AAD com o acesso permissões para o servidor de base de dados, consulte [autenticação do AAD ativar para IR Azure-SSIS](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir). Caso contrário, não é possível omiti-lo e tem de passar um objeto válido formado a partir de seu nome de utilizador de administrador de servidor e a palavra-passe para a autenticação SQL.

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

Execute os seguintes comandos para iniciar o runtime de integração Azure-SSIS.

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
> Excluindo qualquer tempo de configuração personalizada, este processo deve ser concluído dentro de 5 minutos, mas pode demorar cerca de 20 a 30 minutos para IR Azure-SSIS adesão a uma rede virtual.
>
> Se usar o SSISDB, o serviço do ADF irá ligar ao seu servidor de base de dados para preparar o SSISDB. Também configura permissões e definições da sua rede virtual, se for especificado e associa o runtime de integração Azure-SSIS para a rede virtual.
> 
> Quando aprovisiona um IR Azure-SSIS, acesso redistribuível e pacote de funcionalidades do Azure para SSIS também são instalados. Estes componentes fornecem conectividade aos ficheiros de Excel/acesso e de várias origens de dados do Azure, além das origens de dados já suportadas pelos componentes incorporados. Também pode instalar componentes adicionais, consulte [configuração personalizada para IR Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="full-script"></a>Script completo

Aqui está o script completo que cria um runtime de integração Azure-SSIS.

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
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database server with virtual network service endpoints/Managed Instance in a virtual network/on-premises data, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Please use the same subnet as the one used with your Azure SQL Database server with virtual network service endpoints or a different subnet than the one used for your Managed Instance in a virtual network

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

Nesta secção, vai utilizar o modelo Azure Resource Manager para criar o runtime de integração Azure-SSIS. Eis um exemplo passo a passo:

1. Crie um ficheiro JSON com o seguinte modelo Azure Resource Manager. Substitua os valores inclinado entre colchetes (espaços reservados) em seus próprios valores.

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

2. Para implementar o modelo Azure Resource Manager, execute o comando New-AzResourceGroupDeployment, conforme mostrado no exemplo seguinte, onde ADFTutorialResourceGroup é o nome do seu grupo de recursos e adftutorialarm. JSON é o ficheiro que contém a definição de JSON para o data factory e o ir Azure-SSIS.

    ```powershell
    New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json
    ```

    Este comando cria a sua fábrica de dados e o IR Azure-SSIS no mesmo, mas não inicia o IR.

3. Para iniciar o runtime de integração Azure-SSIS, execute o comando de início AzDataFactoryV2IntegrationRuntime:

    ```powershell
    Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "<Resource Group Name>" `
                                                 -DataFactoryName "<Data Factory Name>" `
                                                 -Name "<Azure SSIS IR Name>" `
                                                 -Force
    ```

> [!NOTE]
> Excluindo qualquer tempo de configuração personalizada, este processo deve ser concluído dentro de 5 minutos, mas pode demorar cerca de 20 a 30 minutos para IR Azure-SSIS adesão a uma rede virtual.
>
> Se usar o SSISDB, o serviço do ADF irá ligar ao seu servidor de base de dados para preparar o SSISDB. Também configura permissões e definições da sua rede virtual, se for especificado e associa o runtime de integração Azure-SSIS para a rede virtual.
> 
> Quando aprovisiona um IR Azure-SSIS, acesso redistribuível e pacote de funcionalidades do Azure para SSIS também são instalados. Estes componentes fornecem conectividade aos ficheiros de Excel/acesso e de várias origens de dados do Azure, além das origens de dados já suportadas pelos componentes incorporados. Também pode instalar componentes adicionais, consulte [configuração personalizada para IR Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="deploy-ssis-packages"></a>Implementar pacotes de SSIS

Se usar o SSISDB, pode implementar os pacotes para o mesmo e execute-os no runtime de integração Azure-SSIS com as ferramentas do SSDT/SSMS que se ligam ao seu servidor de base de dados por meio do seu ponto de extremidade do servidor.  Para a base de dados do Azure SQL server/gerida instância numa instância gerida/rede virtual com um ponto final público, o formato de ponto final de servidor é `<server name>.database.windows.net` / `<server name>.<dns prefix>.database.windows.net` / `<server name>.public.<dns prefix>.database.windows.net,3342`, respectivamente. Se não utilizar SSISDB, pode implementar os pacotes em partilhas de sistemas/ficheiro de arquivo/Azure arquivos e execute-os sobre como utilizar o IR Azure-SSIS `dtinstall` / `dtutil` / `dtexec` utilitários de linha de comandos. Para obter mais informações, consulte [pacotes do SSIS implementar](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server). Em ambos os casos, também pode executar os pacotes implementados no runtime de integração Azure-SSIS com a atividade de executar o pacote do SSIS em pipelines ADF, consulte [execução como uma atividade ADF primeira classe de pacotes do SSIS invocar](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).

## <a name="next-steps"></a>Passos Seguintes

Consulte também outros tópicos de IR Azure-SSIS nesta documentação:

- [Runtime de integração Azure-SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime). Este artigo fornece informações sobre os runtimes de integração em geral, incluindo ir Azure-SSIS.
- [Monitorizar o runtime de integração Azure-SSIS](monitor-integration-runtime.md#azure-ssis-integration-runtime). Este artigo mostra-lhe como obter e compreender informações sobre o ir Azure-SSIS.
- [Gerir o IR Azure-SSIS](manage-azure-ssis-integration-runtime.md). Este artigo mostra-lhe como parar, iniciar ou eliminar o runtime de integração Azure-SSIS - mostra também como aumentar horizontalmente o runtime de integração Azure-SSIS ao adicionar mais nós.
- [Associar o IR Azure-SSIS a uma rede virtual](join-azure-ssis-integration-runtime-virtual-network.md). Este artigo fornece informações sobre como associar o runtime de integração Azure-SSIS a uma rede virtual.