---
title: Aprovisionar o Integration Runtime do Azure-SSIS | Microsoft Docs
description: Saiba como aprovisionar o runtime de integração do Azure-SSIS no Azure Data Factory, para que possa implementar e executar pacotes SSIS no Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: tutorial
ms.date: 06/26/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: bd2c055d2f7f1e90918cb160cfdebfe88f7f8ea4
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/01/2019
ms.locfileid: "67484805"
---
# <a name="provision-the-azure-ssis-integration-runtime-in-azure-data-factory"></a>Aprovisionar o Integration Runtime do Azure-SSIS no Azure Data Factory

Este tutorial disponibiliza os passos para utilizar o portal do Azure para aprovisionar um Azure SQL Server Integration Services (SSIS) Integration Runtime (IR) no Azure Data Factory (ADF). Runtime de integração Azure-SSIS suporta a execução de pacotes implementados no catálogo de SSIS (SSISDB) hospedado por servidor/gerida de base de dados do Azure SQL instância (modelo de implementação do projeto) e os que são implementados em sistemas de ficheiros/ficheiro ficheiros de partilhas/do Azure (modelo de implementação do pacote). Depois do IR Azure-SSIS é aprovisionado, em seguida, pode utilizar ferramentas familiares, como o SQL Server Data Tools (SSDT) / SQL Server Management Studio (SSMS) e o comando utilitários da linha, como `dtinstall` / `dtutil` / `dtexec`ao Implemente e execute os seus pacotes no Azure. Para obter informações concetuais sobre IRs Azure-SSIS, veja [Descrição geral do runtime de integração Azure-SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime).

Neste tutorial, vai concluir os seguintes passos:

> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Aprovisionar um runtime de integração Azure-SSIS.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Subscrição do Azure**. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
- **Servidor de base de dados SQL do Azure (opcional)** . Se ainda não tiver um servidor de base de dados, crie um no portal do Azure antes de começar. ADF por sua vez criará SSISDB neste servidor de base de dados. Recomendamos que crie o servidor de base de dados na mesma região do Azure que o integration runtime. Esta configuração permite que o runtime de integração escreva registos de execução em SSISDB sem cruzamento entre regiões do Azure. 
    - Com base no servidor de bases de dados selecionado, a SSISDB pode ser criada em seu nome como uma base de dados individual, parte de um conjunto elástico, ou numa Instância Gerida e acessível na rede pública ou ao associar uma rede virtual. Para obter orientações sobre como escolher o tipo de servidor de base de dados para alojar o SSISDB, consulte [comparar o Azure SQL Database única base de dados/elastic pool/gerida instância](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance). Se utilizar a base de dados do Azure SQL server com a rede virtual serviço pontos finais/gerida instância numa rede virtual para alojar o SSISDB ou exigir acesso a dados no local, tem de associar o runtime de integração Azure-SSIS a uma rede virtual, consulte [criar Runtime de integração Azure-SSIS numa rede virtual](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).
    - Confirme que a definição **Permitir acesso aos serviços do Azure** está ativada para o servidor da base de dados. Não se aplica ao utilizar o servidor de base de dados do Azure SQL com a rede virtual serviço pontos finais/gerida instância numa rede virtual para alojar o SSISDB. Para obter mais informações, veja [Proteger a base de dados SQL do Azure](../sql-database/sql-database-security-tutorial.md#create-firewall-rules). Para ativar esta definição com o PowerShell, consulte [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule).
    - Adicione o endereço IP do computador cliente ou um intervalo de endereços IP que inclua o endereço IP do computador cliente à lista de endereços IP do cliente nas definições de firewall para o servidor de base de dados. Para obter mais informações, veja [Regras de firewall ao nível do servidor da Base de Dados SQL do Azure e ao nível da base de dados](../sql-database/sql-database-firewall-configure.md).
    - Pode ligar ao servidor da base de dados com autenticação de SQL com as suas credenciais de administrador do servidor ou autenticação do Azure Active Directory (AAD) com a identidade gerida para o ADF. Para esta última opção, tem de adicionar a identidade gerida do ADF a um grupo do AAD com permissões de acesso ao servidor de base de dados, veja [Criar Azure-SSIS IR com a autenticação do AAD](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).
    - Confirme que o servidor de base de dados não tem um SSISDB já. O aprovisionamento de um IR Azure-SSIS não suporta a utilização de um SSISDB existente.

> [!NOTE]
> - Para obter uma lista de regiões do Azure em que o ADF e IR Azure-SSIS estão atualmente disponíveis, consulte [ADF + IR do SSIS disponibilidade por região](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all). 

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados

1. Abra o browser **Microsoft Edge** ou **Google Chrome**. Atualmente, a IU do Data Factory é suportada apenas nos browsers Microsoft Edge e Google Chrome. 
1. Inicie sessão no [portal do Azure](https://portal.azure.com/). 
1. Selecione **Novo** no menu da esquerda, selecione **Dados + Análise** e, em seguida, selecione **Data Factory**. 

   ![Seleção do Data Factory no painel "Novo"](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)

1. Na página **Nova fábrica de dados**, introduza **MyAzureSsisDataFactory** em **Nome**. 

   ![Página "Nova fábrica de dados"](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)

   O nome do Azure Data Factory deve ser *globalmente exclusivo*. Se receber o seguinte erro, altere o nome da fábrica de dados (por exemplo, **&lt;oseunome&gt;MyAzureSsisDataFactory**) e tente criá-la novamente. Para regras de nomenclatura de artefactos do Data Factory, veja o artigo [Data Factory – Regras de Nomenclatura](naming-rules.md). 

   `Data factory name “MyAzureSsisDataFactory” is not available`

1. Em **Subscrição**, selecione a sua subscrição do Azure na qual pretende criar a fábrica de dados. 
1. Para **Grupo de Recursos**, siga um destes passos: 

   - Selecione **Utilizar existente** e selecione um grupo de recursos já existente na lista. 
   - Selecione **Criar novo** e introduza o nome de um grupo de recursos. 

   Para saber mais sobre os grupos de recursos, veja [Utilizar grupos de recursos para gerir os recursos do Azure](../azure-resource-manager/resource-group-overview.md). 
1. Em **Versão**, selecione **V2 (Pré-visualização)** . 
1. Em **Localização**, selecione a localização para a fábrica de dados. A lista mostra apenas as localizações que são suportadas para a criação de fábricas de dados. 
1. Selecione **Afixar ao dashboard**. 
1. Selecione **Criar**. 
1. No dashboard, é apresentado o mosaico seguinte, com o estado **A implementar fábrica de dados**: 

   ![Mosaico "A Implementar o Data Factory"](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)

1. Após concluir a criação, verá a página **Fábrica de dados**. 

   ![Home page da fábrica de dados](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)

1. Selecione **Criar e Monitorizar** para iniciar a interface de utilizador (IU) do Data Factory num separador à parte. 

## <a name="create-an-azure-ssis-integration-runtime"></a>Criar um integration runtime do Azure-SSIS

### <a name="from-the-data-factory-overview"></a>Desde a descrição geral do Data Factory

1. Na página **Introdução**, selecione o mosaico **Configurar Runtime de Integração do SSIS**. 

   ![Mosaico "Configurar Runtime de Integração do SSIS"](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

1. Para os restantes passos para configurar um IR Azure-SSIS, veja a secção [Aprovisionar um runtime de integração Azure-SSIS](#provision-an-azure-ssis-integration-runtime). 

### <a name="from-the-authoring-ui"></a>Desde a interface do Usuário de criação

1. Na IU do Azure Data Factory, mude para o separador **Editar**, selecione **Ligações** e, em seguida, mude para o separador **Runtimes de Integração** para ver os runtimes de integração existentes na sua fábrica de dados. 

   ![Seleções para visualização de IRs existentes](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

1. Selecione **Novo** para criar um IR Azure-SSIS. 

   ![Runtime de integração através do menu](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

1. Na janela **Configuração do Runtime de Integração**, selecione **Pacotes SSIS existentes de migração lift-and-shift a executar no Azure** e, em seguida, selecione **Seguinte**. 

   ![Especifique o tipo de runtime de integração](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

1. Para os restantes passos para configurar um IR Azure-SSIS, veja a secção [Aprovisionar um runtime de integração Azure-SSIS](#provision-an-azure-ssis-integration-runtime). 

## <a name="provision-an-azure-ssis-integration-runtime"></a>Aprovisionar um runtime de integração Azure-SSIS

1. Na página **Definições Gerais** da **Configuração do Runtime de Integração**, conclua os seguintes passos: 

   ![Definições gerais](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   a. Em **Nome**, introduza o nome do seu runtime de integração. 

   b. Em **Descrição**, introduza a descrição do seu runtime de integração. 

   c. Em **Localização**, selecione a localização do seu runtime de integração. Apenas são apresentadas as localizações suportadas. Recomendamos que selecione a mesma localização do seu servidor de base de dados para alojar a SSISDB. 

   d. Em **Tamanho do Nó**, selecione o tamanho de nó do cluster do runtime de integração. Apenas são apresentados os tamanhos de nó suportados. Selecione um tamanho de nó grande (aumentar verticalmente), se pretender vários pacotes de computação/memória intensiva. 

   e. Em **Numero de Nós**, selecione o número de nós do cluster do runtime de integração. Apenas são apresentados os números de nó suportados. Selecione um cluster grande com vários nós (aumentar horizontalmente), se pretender executar muitos pacotes em paralelo. 

   f. Para **licença deedição/** , selecione a edição/licença do SQL Server para o runtime de integração: Standard ou Enterprise. Selecione Enterprise, se pretender utilizar as funcionalidades avançadas/premium no seu runtime de integração. 

   g. Para **poupar dinheiro**, selecione a opção de Azure híbrido benefício (AHB) para o runtime de integração: Ou No. Selecione Sim, se pretender trazer a sua própria licença do SQL Server com o Software Assurance para beneficiar das reduções de custos com a utilização híbrida. 

   h. Clique em **Seguinte**. 

1. Na página **Definições de SQL**, conclua os seguintes passos: 

   ![Definições de SQL](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

   a. No **catálogo de SSIS criar...**  caixa de verificação, selecione a implementação de modelos de pacotes executar o runtime de integração Azure-SSIS: Arquivos de modelo de implementação do projeto em que os pacotes são implantados em SSISDB alojado pelo seu servidor de base de dados ou modelo de implementação de pacote em que os pacotes são implementados na sua partilhas de sistemas/ficheiro de arquivo/do Azure. Se o confira, terá de colocar o seu próprio servidor de base de dados para alojar o SSISDB que iremos criar e gerir em seu nome.
   
   b. Em **Subscrição**, selecione a subscrição do Azure que tem o servidor da base de dados para alojar a SSISDB. 

   c. Em **Localização**, selecione a mesma localização do seu servidor de base de dados para alojar a SSISDB. Recomendamos que selecione a mesma localização do seu runtime de integração. 

   d. Em **Ponto Final do Servidor de Base de Dados do Catálogo**, selecione o ponto final do seu servidor de base de dados para alojar a SSISDB. Com base no servidor de bases de dados selecionado, a SSISDB pode ser criada em seu nome como uma base de dados individual, parte de um conjunto elástico, ou numa Instância Gerida e acessível na rede pública ou ao associar uma rede virtual. Para obter orientações sobre como escolher o tipo de servidor de base de dados para alojar o SSISDB, consulte [comparar o Azure SQL Database única base de dados/elastic pool/gerida instância](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance). Se selecionar servidor de base de dados do Azure SQL com pontos finais do serviço de rede virtual/gerida instância de uma rede virtual para alojar o SSISDB ou exigem acesso a dados no local, terá de associar o runtime de integração Azure-SSIS a uma rede virtual, consulte [criar Azure-SSIS Runtime de integração numa rede virtual](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). 

   e. No **autenticação do AAD de utilização...**  caixa de verificação, selecione o método de autenticação para o seu servidor de base de dados para alojar o SSISDB: Autenticação do SQL ou autenticação do AAD com a identidade gerida para o ADF. Se o confira, terá de adicionar a identidade gerida para o ADF para um grupo do AAD com permissões de acesso ao seu servidor de base de dados, consulte [criar IR do Azure-SSIS com a autenticação de AAD](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). 

   f. Em **Nome de Utilizador Administrador**, introduza o nome de utilizador de autenticação SQL Server do servidor de base de dados para alojar a SSISDB. 

   g. Em **Palavra-passe de Administrador**, introduza a palavra-passe de autenticação SQL Server do servidor de base de dados para alojar a SSISDB. 

   h. Para **camada de serviço de base de dados de catálogo**, selecione a camada de serviço para o servidor de base de dados para alojar o SSISDB: Escalão básico/Standard/Premium ou o nome do conjunto elástico. 

   i. Clique em **Testar Ligação** e se tiver êxito, clique em **Seguinte**. 

1. Na página **Definições Avançadas**, conclua os seguintes passos: 

   ![Definições avançadas](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   a. Para **Máximo de Execuções Paralelas por Nó**, selecione o número máximo de pacotes para executar em simultâneo por nó do cluster no runtime de integração. Apenas são apresentados os números de pacotes suportados. Selecione um número baixo, se pretender utilizar mais do que um núcleo para executar um único pacote grande/pesado com computação/memória intensiva. Selecione um número elevado, se pretender executar um ou mais pacotes pequenos/leves num único núcleo. 

   b. Para **URI de SAS do Contentor de Configuração Personalizada**, opcionalmente, introduza o UIR (Uniform Resource Identifier) da SAS (Assinatura de Acesso Partilhado) do seu contentor do Azure Storage Blob onde estão armazenados o script de configuração e os respetivos ficheiros, veja [Configuração Personalizada do Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup). 

   c. Na caixa de verificação **Selecionar uma VNet...** , selecione se pretende associar o seu runtime de integração a uma rede virtual. Deve verificar-se utilizar o servidor de base de dados do Azure SQL com a rede virtual serviço pontos finais/gerida instância numa rede virtual para alojar o SSISDB ou exigem acesso a dados no local, veja [criar IR de SSIS do Azure numa rede virtual](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). 

1. Clique em **Concluir** para iniciar a criação do runtime de integração. 

   > [!NOTE]
   > Excluindo qualquer tempo de configuração personalizada, esse processo deve ser concluído dentro de 5 minutos.
   >
   > Se usar o SSISDB, o serviço do ADF irá ligar ao seu servidor de base de dados para preparar o SSISDB. 
   > 
   > Quando aprovisiona um IR Azure-SSIS, acesso redistribuível e pacote de funcionalidades do Azure para SSIS também são instalados. Estes componentes fornecem conectividade aos ficheiros de Excel/acesso e de várias origens de dados do Azure, além das origens de dados já suportadas pelos componentes incorporados. Também pode instalar componentes adicionais, consulte [configuração personalizada para IR Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md).

1. No separador **Ligações**, mude para **Runtimes de Integração**, se for necessário. Selecione **Atualizar** para atualizar o estado. 

   ![Estado de criação, com o botão "Atualizar"](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

1. Utilize as ligações na coluna **Ações** para iniciar/parar, editar ou eliminar o runtime de integração. Utilize a última ligação para ver o código JSON para o runtime de integração. Os botões de editar e eliminar estão ativados, apenas quando a resposta a incidentes está parada. 

   ![Ligações na coluna "Ações"](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png) 

## <a name="deploy-ssis-packages"></a>Implementar pacotes de SSIS

Se usar o SSISDB, pode implementar os pacotes para o mesmo e execute-os no runtime de integração Azure-SSIS com as ferramentas do SSDT/SSMS que se ligam ao seu servidor de base de dados por meio do seu ponto de extremidade do servidor. Para a base de dados do Azure SQL server/instância gerida com um ponto final público, o formato de ponto final de servidor é `<server name>.database.windows.net` / `<server name>.public.<dns prefix>.database.windows.net,3342`, respectivamente. Se não utilizar SSISDB, pode implementar os pacotes em partilhas de sistemas/ficheiro de arquivo/Azure arquivos e execute-os sobre como utilizar o IR Azure-SSIS `dtinstall` / `dtutil` / `dtexec` utilitários de linha de comandos. Para obter mais informações, consulte [pacotes do SSIS implementar](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server). Em ambos os casos, também pode executar os pacotes implementados no runtime de integração Azure-SSIS com a atividade de executar o pacote do SSIS em pipelines ADF, consulte [execução como uma atividade ADF primeira classe de pacotes do SSIS invocar](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity). 

Consulte também os seguintes artigos da documentação do SSIS: 

- [Implementar, executar e monitorizar pacotes do SSIS no Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial) 
- [Ligar ao SSISDB no Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database) 
- [Agendar execuções de pacote no Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages) 
- [Connect to on-premises data sources with Windows authentication (Ligar a origens de dados no local com a autenticação do Windows)](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como: 

> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Aprovisionar um runtime de integração Azure-SSIS.
> * Implementar pacotes de SSIS

Para saber mais sobre como personalizar o runtime de integração do Azure-SSIS, avance para o seguinte artigo: 

> [!div class="nextstepaction"]
> [Personalizar o Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)