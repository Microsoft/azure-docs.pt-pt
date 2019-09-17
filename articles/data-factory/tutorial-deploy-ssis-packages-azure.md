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
ms.date: 09/15/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: b3b180fe4b465f3e0c7de888043326fd1a43cf23
ms.sourcegitcommit: a819209a7c293078ff5377dee266fa76fd20902c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/16/2019
ms.locfileid: "71009626"
---
# <a name="provision-the-azure-ssis-integration-runtime-in-azure-data-factory"></a>Aprovisionar o Integration Runtime do Azure-SSIS no Azure Data Factory

Este tutorial fornece as etapas para usar o portal do Azure para provisionar um Integration Runtime do Azure-SQL Server Integration Services (SSIS) no Azure Data Factory (ADF). Azure-SSIS IR dá suporte a pacotes em execução implantados no catálogo do SSIS (SSISDB) hospedado pelo servidor/Instância Gerenciada do banco de dados SQL do Azure (modelo de implantação de projeto) e aqueles implantados em sistemas de arquivos/compartilhamentos de arquivos/arquivos do Azure (modelo de implantação Depois que Azure-SSIS ir for provisionado, você poderá usar ferramentas familiares, como SQL Server Data Tools (SSDT)/SQL Server Management Studio (SSMS) e utilitários de linha de `dtinstall`comando, / `dtutil` / `dtexec`como, para implante e execute seus pacotes no Azure. Para obter informações concetuais sobre IRs Azure-SSIS, veja [Descrição geral do runtime de integração Azure-SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime).

Neste tutorial, vai concluir os seguintes passos:

> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Aprovisionar um runtime de integração Azure-SSIS.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Subscrição do Azure**. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
- **Servidor de banco de dados SQL do Azure (opcional)** . Se você ainda não tiver um servidor de banco de dados, crie um no portal do Azure antes de começar. Por sua vez, o ADF criará SSISDB neste servidor de banco de dados. Recomendamos que crie o servidor de base de dados na mesma região do Azure que o integration runtime. Essa configuração permite que o Integration Runtime grave logs de execução em SSISDB sem cruzar regiões do Azure. 
    - Com base no servidor de bases de dados selecionado, a SSISDB pode ser criada em seu nome como uma base de dados individual, parte de um conjunto elástico, ou numa Instância Gerida e acessível na rede pública ou ao associar uma rede virtual. Para obter orientação sobre como escolher o tipo de servidor de banco de dados para hospedar o SSISDB, consulte comparar banco de dados [SQL do Azure/pool elástico/instância gerenciada](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance). Se você usar o servidor de banco de dados SQL do Azure com pontos de extremidade de serviço de rede virtual/Instância Gerenciada com um ponto de extremidades privado para hospedar o SSISDB ou exigir acesso a dados locais sem configurar o IR auto-hospedado, você precisará unir seu Azure-SSIS IR a uma rede virtual, consulte [criar Azure-SSIS ir em uma rede virtual](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).
    - Confirme que a definição **Permitir acesso aos serviços do Azure** está ativada para o servidor da base de dados. Isso não é aplicável quando você usa o servidor de banco de dados SQL do Azure com pontos de extremidade de serviço de rede virtual/Instância Gerenciada com um ponto de extremidades privado para hospedar o SSISDB. Para obter mais informações, veja [Proteger a base de dados SQL do Azure](../sql-database/sql-database-security-tutorial.md#create-firewall-rules). Para habilitar essa configuração usando o PowerShell, consulte [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule).
    - Adicione o endereço IP do computador cliente ou um intervalo de endereços IP que inclui o endereço IP do computador cliente à lista de endereços IP do cliente nas configurações de firewall do servidor de banco de dados. Para obter mais informações, veja [Regras de firewall ao nível do servidor da Base de Dados SQL do Azure e ao nível da base de dados](../sql-database/sql-database-firewall-configure.md).
    - Você pode se conectar ao servidor de banco de dados usando a autenticação do SQL com suas credenciais de administrador do servidor ou a autenticação do Azure Active Directory (AAD) com a identidade gerenciada para o ADF. Para esta última opção, tem de adicionar a identidade gerida do ADF a um grupo do AAD com permissões de acesso ao servidor de base de dados, veja [Criar Azure-SSIS IR com a autenticação do AAD](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).
    - Confirme se o servidor de banco de dados ainda não tem um SSISDB. O provisionamento de um Azure-SSIS IR não dá suporte ao uso de um SSISDB existente.

> [!NOTE]
> - Para obter uma lista de regiões do Azure nas quais o ADF e Azure-SSIS IR estão disponíveis no momento, confira [disponibilidade de ir do ADF + SSIS por região](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all). 

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados

Para criar o ADF por meio do portal do Azure, siga as instruções detalhadas em [criar ADF por meio do artigo da interface do usuário](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory) e selecione **fixar no painel** enquanto faz isso para permitir o acesso rápido após sua criação. 

Quando o ADF for criado, abra sua página de visão geral no portal do Azure e clique no bloco **criar & monitor** para iniciar sua página de **introdução** em uma guia separada, em que você pode continuar a criar seu Azure-SSIS ir.   

## <a name="create-an-azure-ssis-integration-runtime"></a>Criar um integration runtime do Azure-SSIS

### <a name="from-the-data-factory-overview"></a>Na visão geral de Data Factory

1. Na página **de introdução,** clique no bloco **configurar o SSIS Integration Runtime** . 

   ![Mosaico "Configurar Runtime de Integração do SSIS"](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

1. Para os restantes passos para configurar um IR Azure-SSIS, veja a secção [Aprovisionar um runtime de integração Azure-SSIS](#provision-an-azure-ssis-integration-runtime). 

### <a name="from-the-authoring-ui"></a>Da interface do usuário de criação

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

   f. Para **edição/licença**, selecione SQL Server edição/licença para o tempo de execução de integração: Standard ou Enterprise. Selecione Enterprise, se pretender utilizar as funcionalidades avançadas/premium no seu runtime de integração. 

   g. Para **economizar dinheiro**, selecione a opção benefício híbrido do Azure (AHB) para seu tempo de execução de integração: Sim ou não. Selecione Sim, se pretender trazer a sua própria licença do SQL Server com o Software Assurance para beneficiar das reduções de custos com a utilização híbrida. 

   h. Clique em **Seguinte**. 

1. Na página **Definições de SQL**, conclua os seguintes passos: 

   ![Definições de SQL](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

   a. Na caixa de seleção **criar catálogo do SSIS...** , selecione o modelo de implantação para que os pacotes sejam executados no seu Azure-SSIS ir: Modelo de implantação de projeto onde os pacotes são implantados no SSISDB hospedado pelo seu servidor de banco de dados ou modelo de implantação de pacote onde os pacotes são implantados em seus sistemas de arquivos/compartilhamentos de arquivos/arquivos Se você verificá-lo, precisará colocar seu próprio servidor de banco de dados para hospedar o SSISDB, que criaremos e gerenciaremos em seu nome.
   
   b. Em **Subscrição**, selecione a subscrição do Azure que tem o servidor da base de dados para alojar a SSISDB. 

   c. Em **Localização**, selecione a mesma localização do seu servidor de base de dados para alojar a SSISDB. Recomendamos que selecione a mesma localização do seu runtime de integração.

   d. Em **Ponto Final do Servidor de Base de Dados do Catálogo**, selecione o ponto final do seu servidor de base de dados para alojar a SSISDB. Com base no servidor de bases de dados selecionado, a SSISDB pode ser criada em seu nome como uma base de dados individual, parte de um conjunto elástico, ou numa Instância Gerida e acessível na rede pública ou ao associar uma rede virtual. Para obter orientação sobre como escolher o tipo de servidor de banco de dados para hospedar o SSISDB, consulte comparar banco de dados [SQL do Azure/pool elástico/instância gerenciada](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance). Se você selecionar servidor de banco de dados SQL do Azure com pontos de extremidade de serviço de rede virtual/Instância Gerenciada com um ponto de extremidades privado para hospedar o SSISDB ou exigir acesso a dados locais sem configurar o IR auto-hospedado, você precisará unir seu Azure-SSIS IR a uma rede virtual , consulte [criar Azure-SSIS ir em uma rede virtual](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). 

   e. Na caixa de seleção **usar autenticação do AAD...** , selecione o método de autenticação para o servidor de banco de dados para hospedar o SSISDB: Autenticação do SQL ou a autenticação do AAD com a identidade gerenciada para o ADF. Se você verificá-lo, precisará adicionar a identidade gerenciada para o ADF em um grupo do AAD com permissões de acesso ao seu servidor de banco de dados, consulte [criar Azure-SSIS ir com a autenticação do AAD](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). 

   f. Em **Nome de Utilizador Administrador**, introduza o nome de utilizador de autenticação SQL Server do servidor de base de dados para alojar a SSISDB. 

   g. Em **Palavra-passe de Administrador**, introduza a palavra-passe de autenticação SQL Server do servidor de base de dados para alojar a SSISDB. 

   h. Para **camada de serviço do banco de dados do catálogo**, selecione a camada de serviço do servidor de banco de dados para hospedar o SSISDB: Camada Basic/Standard/Premium ou nome do pool elástico.

   i. Clique em **Testar Ligação** e se tiver êxito, clique em **Seguinte**. 

1. Na página **Definições Avançadas**, conclua os seguintes passos: 

   ![Definições avançadas](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   a. Para **Máximo de Execuções Paralelas por Nó**, selecione o número máximo de pacotes para executar em simultâneo por nó do cluster no runtime de integração. Apenas são apresentados os números de pacotes suportados. Selecione um número baixo, se pretender utilizar mais do que um núcleo para executar um único pacote grande/pesado com computação/memória intensiva. Selecione um número elevado, se pretender executar um ou mais pacotes pequenos/leves num único núcleo. 

   b. Para **URI de SAS do Contentor de Configuração Personalizada**, opcionalmente, introduza o UIR (Uniform Resource Identifier) da SAS (Assinatura de Acesso Partilhado) do seu contentor do Azure Storage Blob onde estão armazenados o script de configuração e os respetivos ficheiros, veja [Configuração Personalizada do Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup). 

   c. Na caixa de verificação **Selecionar uma VNet...** , selecione se pretende associar o seu runtime de integração a uma rede virtual. Você deve verificá-lo se usar o servidor de banco de dados SQL do Azure com pontos de extremidade de serviço de rede virtual/Instância Gerenciada com um ponto de extremidades privado para hospedar o SSISDB ou exigir acesso a dados locais sem configurar o IR via hospedagem interna, consulte [criar Azure-SSIS ir em um rede virtual](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). 

   d. Na caixa de seleção **Configurar auto-hospedado...** , selecione se deseja configurar o infravermelho auto-hospedado como um proxy para o seu Azure-SSIS ir, consulte Configurar o ir para o [auto-hospedado como um proxy](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis). 

1. Clique em **Seguinte**. 

1. Na página **Resumo** , examine todas as configurações de provisionamento, marque os links de documentação recomendados e clique em **concluir** para iniciar a criação do tempo de execução de integração. 

   > [!NOTE]
   > Excluindo qualquer hora de instalação personalizada, esse processo deve ser concluído em 5 minutos.
   >
   > Se você usar o SSISDB, o serviço ADF se conectará ao servidor de banco de dados para preparar o SSISDB. 
   > 
   > Quando você provisiona um Azure-SSIS IR, o Access redistribuível e o Azure Feature Pack para SSIS também são instalados. Esses componentes fornecem conectividade com arquivos do Excel/Access e várias fontes de dados do Azure, além das fontes de dados já suportadas por componentes internos. Você também pode instalar componentes adicionais, consulte [instalação personalizada para Azure-SSIS ir](how-to-configure-azure-ssis-ir-custom-setup.md).

1. No separador **Ligações**, mude para **Runtimes de Integração**, se for necessário. Selecione **Atualizar** para atualizar o estado. 

   ![Estado de criação, com o botão "Atualizar"](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

1. Utilize as ligações na coluna **Ações** para iniciar/parar, editar ou eliminar o runtime de integração. Utilize a última ligação para ver o código JSON para o runtime de integração. Os botões de editar e eliminar estão ativados, apenas quando a resposta a incidentes está parada. 

   ![Ligações na coluna "Ações"](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png) 

## <a name="deploy-ssis-packages"></a>Implementar pacotes de SSIS

Se você usar o SSISDB, poderá implantar seus pacotes nele e executá-los em Azure-SSIS IR usando as ferramentas SSDT/SSMS que se conectam ao seu servidor de banco de dados por meio de seu ponto de extremidade do servidor. Para o servidor de banco de dados SQL do Azure/instância gerenciada com um ponto de `<server name>.database.windows.net`extremidade público, o formato de ponto de extremidade do servidor é / `<server name>.public.<dns prefix>.database.windows.net,3342`, respectivamente Se você não usar o SSISDB, poderá implantar seus pacotes em sistemas de arquivos/compartilhamentos de arquivos/arquivos do Azure e executá `dtinstall` -los em Azure-SSIS ir usando / / `dtutil` `dtexec` utilitários de linha de comando. Para obter mais informações, consulte [implantar pacotes do SSIS](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server). Em ambos os casos, você também pode executar seus pacotes implantados em Azure-SSIS IR usando a atividade executar pacote SSIS em pipelines do ADF, consulte [invocar a execução do pacote SSIS como uma atividade AAD de primeira classe](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity). 

Consulte também os seguintes artigos da documentação do SSIS: 

- [Implantar, executar e monitorar pacotes do SSIS no Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial) 
- [Conectar-se ao SSISDB no Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database) 
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