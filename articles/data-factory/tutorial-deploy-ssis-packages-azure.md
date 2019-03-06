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
ms.date: 10/28/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 26aa8b17917e92a0bcb2393ac3f5d69a70dceefe
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57453716"
---
# <a name="provision-the-azure-ssis-integration-runtime-in-azure-data-factory"></a>Aprovisionar o Integration Runtime do Azure-SSIS no Azure Data Factory
Este tutorial disponibiliza os passos para utilizar o portal do Azure para aprovisionar um integration runtime (IR) Azure-SSIS no Azure Data Factory. Em seguida, pode utilizar o SQL Server Data Tools (SSDT) ou o SQL Server Management Studio (SSMS) para implementar e executar pacotes de SQL Server Integration Services (SSIS) neste runtime no Azure. Para obter informações concetuais sobre IRs Azure-SSIS, veja [Descrição geral do runtime de integração Azure-SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime).

Neste tutorial, vai concluir os seguintes passos:

> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Aprovisionar um runtime de integração Azure-SSIS.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Subscrição do Azure**. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar. 
- **Servidor da Base de Dados SQL do Azure**. Se ainda não tiver um servidor de base de dados, crie um no portal do Azure antes de começar. O Azure Data Factory cria o Catálogo de SSIS (base de dados SSISDB) neste servidor de base de dados. Recomendamos que crie o servidor de base de dados na mesma região do Azure que o integration runtime. Esta configuração permite que o runtime de integração escreva registos de execução na base de dados de SSISDB sem cruzamento entre regiões do Azure. 
- Com base no servidor de bases de dados selecionado, a SSISDB pode ser criada em seu nome como uma base de dados individual, parte de um conjunto elástico, ou numa Instância Gerida e acessível na rede pública ou ao associar uma rede virtual. Se utilizar a Base de Dados SQL do Azure com pontos finais de serviço de rede virtual/Instância Gerida para alojar a SSISDB ou pedir acesso a dados no local, terá de associar o Azure-SSIS IR a uma rede virtual, veja [Criar o Azure-SSIS IR numa rede virtual](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). 
- Confirme que a definição **Permitir acesso aos serviços do Azure** está ativada para o servidor da base de dados. Esta definição não se aplica ao utilizar a Base de Dados SQL do Azure com pontos finais do serviço de rede virtual/Instância Gerida para alojar a SSISDB. Para obter mais informações, veja [Proteger a base de dados SQL do Azure](../sql-database/sql-database-security-tutorial.md#create-firewall-rules). Para ativar esta definição com o PowerShell, consulte [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule). 
- Adicione o endereço IP do computador cliente ou um intervalo de endereços IP que inclua o endereço IP do computador cliente à lista de endereços IP do cliente nas definições de firewall para o servidor da base de dados. Para obter mais informações, veja [Regras de firewall ao nível do servidor da Base de Dados SQL e ao nível da base de dados](../sql-database/sql-database-firewall-configure.md). 
- Pode ligar ao servidor de base de dados através da autenticação SQL com as credenciais de administrador do servidor ou a autenticação do AAD (Azure Active Directory) com a identidade gerida do Azure Data Factory (ADF).  Para esta última opção, tem de adicionar a identidade gerida do ADF a um grupo do AAD com permissões de acesso ao servidor de base de dados, veja [Criar Azure-SSIS IR com a autenticação do AAD](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). 
- Certifique-se de que o servidor da Base de Dados SQL do Azure não tem um Catálogo de SSIS (base de dados SSISDB). O aprovisionamento de um IR Azure-SSIS não suporta a utilização de um Catálogo de SSIS existente. 

> [!NOTE]
> - Para obter uma lista de regiões do Azure em que o Data Factory e o Azure-SSIS Integration Runtime estão atualmente disponíveis, veja [Disponibilidade do ADF + IR SSIS por região](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all). 

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
1. Em **Versão**, selecione **V2 (Pré-visualização)**. 
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

   f. Para **licença deedição/**, selecione a edição/licença do SQL Server para o runtime de integração: Standard ou Enterprise. Selecione Enterprise, se pretender utilizar as funcionalidades avançadas/premium no seu runtime de integração. 

   g. Para **poupar dinheiro**, selecione a opção de Azure híbrido benefício (AHB) para o runtime de integração: Ou No. Selecione Sim, se pretender trazer a sua própria licença do SQL Server com o Software Assurance para beneficiar das reduções de custos com a utilização híbrida. 

   h. Clique em **Seguinte**. 

1. Na página **Definições de SQL**, conclua os seguintes passos: 

   ![Definições de SQL](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

   a. Em **Subscrição**, selecione a subscrição do Azure que tem o servidor da base de dados para alojar a SSISDB. 

   b. Em **Localização**, selecione a mesma localização do seu servidor de base de dados para alojar a SSISDB. Recomendamos que selecione a mesma localização do seu runtime de integração. 

   c. Em **Ponto Final do Servidor de Base de Dados do Catálogo**, selecione o ponto final do seu servidor de base de dados para alojar a SSISDB. Com base no servidor de bases de dados selecionado, a SSISDB pode ser criada em seu nome como uma base de dados individual, parte de um conjunto elástico, ou numa Instância Gerida e acessível na rede pública ou ao associar uma rede virtual. Para obter orientações sobre como escolher o tipo de servidor de base de dados para alojar o SSISDB, consulte [conjuntos de bases de dados única/elástico de comparar a base de dados de SQL do Azure e a instância gerida](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance). Se selecionar a Base de Dados SQL do Azure com pontos finais de serviço de rede virtual/Instância Gerida para alojar a SSISDB ou pedir acesso a dados no local, terá de associar o Azure-SSIS IR a uma rede virtual. Veja [Criar um IR Azure-SSIS numa rede virtual](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). 

   d. No **autenticação do AAD de utilização...**  caixa de verificação, selecione o método de autenticação para o seu servidor de base de dados para alojar o SSISDB: SQL ou do Azure Active Directory (AAD) com a identidade gerida para o Azure Data Factory (ADF). Se a marcar, tem de adicionar a identidade gerida do ADF a um grupo do AAD com permissões de acesso ao servidor de base de dados, veja [Criar Azure-SSIS IR com a autenticação do AAD](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). 

   e. Em **Nome de Utilizador Administrador**, introduza o nome de utilizador de autenticação SQL Server do servidor de base de dados para alojar a SSISDB. 

   f. Em **Palavra-passe de Administrador**, introduza a palavra-passe de autenticação SQL Server do servidor de base de dados para alojar a SSISDB. 

   g. Para **camada de serviço de base de dados de catálogo**, selecione a camada de serviço para o servidor de base de dados para alojar o SSISDB: Escalão básico/Standard/Premium ou o nome do conjunto elástico. 

   h. Clique em **Testar Ligação** e se tiver êxito, clique em **Seguinte**. 

1. Na página **Definições Avançadas**, conclua os seguintes passos: 

   ![Definições avançadas](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   a. Para **Máximo de Execuções Paralelas por Nó**, selecione o número máximo de pacotes para executar em simultâneo por nó do cluster no runtime de integração. Apenas são apresentados os números de pacotes suportados. Selecione um número baixo, se pretender utilizar mais do que um núcleo para executar um único pacote grande/pesado com computação/memória intensiva. Selecione um número elevado, se pretender executar um ou mais pacotes pequenos/leves num único núcleo. 

   b. Para **URI de SAS do Contentor de Configuração Personalizada**, opcionalmente, introduza o UIR (Uniform Resource Identifier) da SAS (Assinatura de Acesso Partilhado) do seu contentor do Azure Storage Blob onde estão armazenados o script de configuração e os respetivos ficheiros, veja [Configuração Personalizada do Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup). 

   c. Na caixa de verificação **Selecionar uma VNet...**, selecione se pretende associar o seu runtime de integração a uma rede virtual. Deverá verificá-la se utilizar a Base de Dados SQL do Azure com pontos finais de serviço de rede virtual/Instância Gerida para alojar a SSISDB ou pedir acesso a dados no local, veja [Criar o Azure-SSIS IR numa rede virtual](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). 

1. Clique em **Concluir** para iniciar a criação do runtime de integração. 

   > [!IMPORTANT]
   > Este processo demora cerca de 20 a 30 minutos a concluir.
   >
   > O serviço Data Factory liga ao seu servidor da Base de Dados SQL do Azure para preparar o Catálogo de SSIS (base de dados SSISDB). 
   > 
   > Quando aprovisionar uma instância de um IR Azure-SSIS, o Pacote de Funcionalidades do Azure para SSIS e o Acesso Redistribuível também são instalados. Estes componentes fornecem conectividade aos ficheiros do Excel e acesso e a várias origens de dados do Azure, para além das origens de dados suportadas pelos componentes incorporados. Também pode instalar componentes adicionais. Para mais informações, veja [Configuração personalizada do runtime de integração do Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md). 

1. No separador **Ligações**, mude para **Runtimes de Integração**, se for necessário. Selecione **Atualizar** para atualizar o estado. 

   ![Estado de criação, com o botão "Atualizar"](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

1. Utilize as ligações na coluna **Ações** para iniciar/parar, editar ou eliminar o runtime de integração. Utilize a última ligação para ver o código JSON para o runtime de integração. Os botões de editar e eliminar estão ativados, apenas quando a resposta a incidentes está parada. 

   ![Ligações na coluna "Ações"](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png) 

## <a name="deploy-ssis-packages"></a>Implementar pacotes de SSIS
Agora, utilize o SQL Server Data Tools (SSDT) ou o SQL Server Management Studio (SSMS) para implementar os pacotes de SSIS no Azure. Ligue ao servidor da Base de Dados SQL do Azure que aloja o Catálogo de SSIS (base de dados SSISDB). O nome do servidor da Base de Dados SQL do Azure está no formato `<servername>.database.windows.net`. 

Veja os artigos seguintes da documentação do SSIS: 

- [Deploy, run, and monitor an SSIS package on Azure (Implementar, executar e monitorizar pacotes do SSIS no Azure)](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial) 
- [Ligar ao Catálogo de SSIS no Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database) 
- [Schedule package execution on Azure (Agendar a execução de pacotes no Azure)](/sql/integration-services/lift-shift/ssis-azure-schedule-packages) 
- [Connect to on-premises data sources with Windows authentication (Ligar a origens de dados no local com a autenticação do Windows)](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, ficou a saber como: 

> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Aprovisionar um runtime de integração Azure-SSIS.

Para saber mais sobre como personalizar o runtime de integração do Azure-SSIS, avance para o seguinte artigo: 

> [!div class="nextstepaction"]
> [Personalizar o Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)
