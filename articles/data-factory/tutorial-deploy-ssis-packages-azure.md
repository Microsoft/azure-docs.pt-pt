---
title: Provisionar o tempo de execução de integração do Azure-SSIS
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
ms.openlocfilehash: 14871aa790bd94dbd23dea30c8a229eac7945c75
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73683559"
---
# <a name="provision-the-azure-ssis-integration-runtime-in-azure-data-factory"></a>Provisionar o tempo de execução de integração do Azure-SSIS no Azure Data Factory

Este tutorial fornece as etapas para usar o portal do Azure para provisionar um IR (tempo de execução de integração) do Azure-SQL Server Integration Services (SSIS) no Azure Data Factory. Um Azure-SSIS IR dá suporte a:

- Executando pacotes implantados no catálogo do SSIS (SSISDB) hospedado por um servidor de banco de dados SQL do Azure ou uma instância gerenciada (modelo de implantação de projeto).
- Executando pacotes implantados em sistemas de arquivos, compartilhamentos de arquivos ou arquivos do Azure (modelo de implantação de pacote). 

Depois que um Azure-SSIS IR for provisionado, você poderá usar ferramentas familiares para implantar e executar seus pacotes no Azure. Essas ferramentas incluem SQL Server Data Tools, SQL Server Management Studio e ferramentas de linha de comando como `dtinstall`, `dtutil`e `dtexec`.

Para obter informações concetuais sobre IRs Azure-SSIS, veja [Descrição geral do runtime de integração Azure-SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime).

Neste tutorial, vai concluir os seguintes passos:

> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Aprovisionar um runtime de integração Azure-SSIS.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Subscrição do Azure**. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
- **Servidor de banco de dados SQL do Azure (opcional)** . Se ainda não tiver um servidor de base de dados, crie um no portal do Azure antes de começar. O Data Factory, por sua vez, criará uma instância SSISDB neste servidor de banco de dados. 

  Recomendamos que crie o servidor de base de dados na mesma região do Azure que o integration runtime. Essa configuração permite que o Integration Runtime grave logs de execução em SSISDB sem cruzar regiões do Azure.

  Tenha esses pontos em mente:

  - Com base no servidor de banco de dados selecionado, a instância SSISDB pode ser criada em seu nome como um banco de dados individual, como parte de um pool elástico ou em uma instância gerenciada. Ele pode ser acessível em uma rede pública ou ingressando em uma rede virtual. Para obter orientação sobre como escolher o tipo de servidor de banco de dados para hospedar o SSISDB, consulte comparar um banco de dados [SQL do Azure, um pool elástico e uma instância gerenciada](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance). 
  
    Se você usar um servidor de banco de dados SQL do Azure com pontos de extremidade de serviço de rede virtual ou uma instância gerenciada com um ponto de extremidades privado para hospedar o SSISDB ou se precisar de acesso a dados locais sem configurar o IR auto-hospedado, será necessário ingressar seu Azure-SSIS IR em um virtual rede. Para obter mais informações, consulte [criar um Azure-SSIS ir em uma rede virtual](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).
  - Confirme que a definição **Permitir acesso aos serviços do Azure** está ativada para o servidor da base de dados. Essa configuração não é aplicável quando você usa um servidor de banco de dados SQL do Azure com pontos de extremidade de serviço de rede virtual ou uma instância gerenciada com um ponto de extremidades privado para hospedar o SSISDB. Para obter mais informações, veja [Proteger a base de dados SQL do Azure](../sql-database/sql-database-security-tutorial.md#create-firewall-rules). Para habilitar essa configuração usando o PowerShell, consulte [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule).
  - Adicione o endereço IP do computador cliente ou um intervalo de endereços IP que inclui o endereço IP do computador cliente à lista de endereços IP do cliente nas configurações de firewall do servidor de banco de dados. Para obter mais informações, veja [Regras de firewall ao nível do servidor da Base de Dados SQL do Azure e ao nível da base de dados](../sql-database/sql-database-firewall-configure.md).
  - Você pode se conectar ao servidor de banco de dados usando a autenticação do SQL com suas credenciais de administrador do servidor ou usando a autenticação do Azure AD com a identidade gerenciada para seu data factory. Para o último, você precisa adicionar a identidade gerenciada para seu data factory em um grupo do Azure AD com permissões de acesso ao servidor de banco de dados. Para obter mais informações, consulte [criar um Azure-SSIS ir com a autenticação do Azure ad](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).
  - Confirme se o servidor de banco de dados ainda não tem uma instância SSISDB. O provisionamento de um Azure-SSIS IR não dá suporte ao uso de uma instância SSISDB existente.


> [!NOTE]
> Para obter uma lista de regiões do Azure nas quais Data Factory e um Azure-SSIS IR estão disponíveis no momento, consulte [Data Factory e a disponibilidade de ir do SSIS por região](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all). 

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados

Para criar seu data factory por meio do portal do Azure, siga as instruções passo a passo em [criar um data Factory por meio da interface do usuário](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory). Selecione **fixar no painel** ao fazer isso, para permitir acesso rápido após sua criação. 

Depois que o data factory for criado, abra sua página de visão geral na portal do Azure. Selecione o bloco **criar & monitor** para abrir a página **vamos começar** em uma guia separada. Lá, você pode continuar a criar seu Azure-SSIS IR.

## <a name="create-an-azure-ssis-integration-runtime"></a>Criar um integration runtime do Azure-SSIS

### <a name="from-the-data-factory-overview"></a>Na visão geral de Data Factory

1. Na página **Introdução**, selecione o mosaico **Configurar Runtime de Integração do SSIS**. 

   ![Mosaico "Configurar Runtime de Integração do SSIS"](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

1. Para os restantes passos para configurar um IR Azure-SSIS, veja a secção [Aprovisionar um runtime de integração Azure-SSIS](#provision-an-azure-ssis-integration-runtime). 

### <a name="from-the-authoring-ui"></a>Da interface do usuário de criação

1. Na interface do usuário do Azure Data Factory, alterne para a guia **Editar** e selecione **conexões**. Em seguida, alterne para a guia **tempos de execução de integração** para exibir os tempos de execução de integração existentes em seu data Factory. 

   ![Seleções para visualização de IRs existentes](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

1. Selecione **Novo** para criar um IR Azure-SSIS. 

   ![Runtime de integração através do menu](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

1. Na janela **Configuração do Runtime de Integração**, selecione **Pacotes SSIS existentes de migração lift-and-shift a executar no Azure** e, em seguida, selecione **Seguinte**. 

   ![Especifique o tipo de runtime de integração](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

1. Para os restantes passos para configurar um IR Azure-SSIS, veja a secção [Aprovisionar um runtime de integração Azure-SSIS](#provision-an-azure-ssis-integration-runtime). 

## <a name="provision-an-azure-ssis-integration-runtime"></a>Aprovisionar um runtime de integração Azure-SSIS

1. Na página **configurações gerais** da **instalação do Integration Runtime**, conclua as etapas a seguir. 

   ![Definições gerais](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   a. Em **Nome**, introduza o nome do seu runtime de integração. 

   b. Em **Descrição**, introduza a descrição do seu runtime de integração. 

   c. Em **Localização**, selecione a localização do seu runtime de integração. Apenas são apresentadas as localizações suportadas. Recomendamos que selecione a mesma localização do seu servidor de base de dados para alojar a SSISDB. 

   d. Em **Tamanho do Nó**, selecione o tamanho de nó do cluster do runtime de integração. Apenas são apresentados os tamanhos de nó suportados. Selecione um tamanho de nó grande (escalar verticalmente) se desejar executar muitos pacotes de computação intensiva ou de uso intensivo de memória. 

   e. Em **Numero de Nós**, selecione o número de nós do cluster do runtime de integração. Apenas são apresentados os números de nó suportados. Selecione um cluster grande com muitos nós (scale out) se você quiser executar muitos pacotes em paralelo. 

   f. Para **edição/licença**, selecione o SQL Server Edition para o tempo de execução de integração: Standard ou Enterprise. Selecione empresa se desejar usar recursos avançados no tempo de execução de integração. 

   g. Para **economizar dinheiro**, selecione a opção benefício híbrido do Azure para o tempo de execução de integração: **Sim** ou **não**. Selecione **Sim** se desejar trazer sua própria licença de SQL Server com o Software Assurance para se beneficiar da economia de custos com o uso híbrido. 

   h. Selecione **Seguinte**. 

1. Na página **configurações do SQL** , conclua as etapas a seguir. 

   ![Definições de SQL](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

   a. Marque a caixa de seleção **criar catálogo do SSIS...** para escolher o modelo de implantação para que os pacotes sejam executados no seu Azure-SSIS ir. Você escolherá o modelo de implantação de projeto em que os pacotes são implantados no SSISDB hospedado pelo servidor de banco de dados ou o modelo de implantação de pacote em que os pacotes são implantados em sistemas de arquivos, compartilhamentos de arquivos ou arquivos do Azure.
   
   Se você marcar a caixa de seleção, precisará colocar seu próprio servidor de banco de dados para hospedar o SSISDB que criaremos e gerenciaremos em seu nome.
   
   b. Em **Subscrição**, selecione a subscrição do Azure que tem o servidor da base de dados para alojar a SSISDB. 

   c. Em **Localização**, selecione a mesma localização do seu servidor de base de dados para alojar a SSISDB. Recomendamos que selecione a mesma localização do seu runtime de integração.

   d. Em **Ponto Final do Servidor de Base de Dados do Catálogo**, selecione o ponto final do seu servidor de base de dados para alojar a SSISDB. 
   
   Com base no servidor de banco de dados selecionado, a instância SSISDB pode ser criada em seu nome como um banco de dados individual, como parte de um pool elástico ou em uma instância gerenciada. Ele pode ser acessível em uma rede pública ou ingressando em uma rede virtual. Para obter orientação sobre como escolher o tipo de servidor de banco de dados para hospedar o SSISDB, consulte comparar um banco de dados [SQL do Azure, um pool elástico e uma instância gerenciada](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance).   

   Se você selecionar um servidor de banco de dados SQL do Azure com pontos de extremidade de serviço de rede virtual ou uma instância gerenciada com um ponto de extremidades privado para hospedar o SSISDB ou se precisar de acesso a dados locais sem configurar o IR auto-hospedado, será necessário unir seu Azure-SSIS IR a um rede virtual. Para obter mais informações, consulte [criar um Azure-SSIS ir em uma rede virtual](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).

   e. Marque a caixa de seleção **usar a autenticação do AAD com a identidade gerenciada para o ADF** para escolher o método de autenticação para o servidor de banco de dados para hospedar o SSISDB. Você escolherá a autenticação do SQL ou a autenticação do Azure AD com a identidade gerenciada para seu data factory.

   Se você marcar a caixa de seleção, precisará adicionar a identidade gerenciada para seu data factory em um grupo do Azure AD com permissões de acesso ao seu servidor de banco de dados. Para obter mais informações, consulte [criar um Azure-SSIS ir com a autenticação do Azure ad](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).
   
   f. Para **nome de usuário do administrador**, insira o nome de usuário de autenticação do SQL do servidor de banco de dados para hospedar o SSISDB. 

   g. Para **senha de administrador**, insira a senha de autenticação do SQL para o servidor de banco de dados para hospedar o SSISDB. 

   h. Para **camada de serviço do banco de dados do catálogo**, selecione a camada de serviço do servidor de banco de dados para hospedar o SSISDB. Selecione a camada básica, Standard ou Premium ou selecione um nome de pool elástico.

   i. Selecione **testar conexão**. Se o teste for bem-sucedido, selecione **Avançar**. 

1. Na página **Configurações avançadas** , conclua as etapas a seguir. 

   ![Definições avançadas](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   a. Para **execuções paralelas máximas por nó**, selecione o número máximo de pacotes a serem executados simultaneamente por nó em seu cluster de tempo de execução de integração. Apenas são apresentados os números de pacotes suportados. Selecione um número baixo se desejar usar mais de um núcleo para executar um único pacote grande que seja com uso intensivo de computação ou de memória. Selecione um número alto se desejar executar um ou mais pacotes pequenos em um único núcleo. 

   b. Para o **URI de SAS do contêiner de instalação personalizada**, opcionalmente, insira o URI (identificador de recurso uniforme) da SAS (assinatura de acesso compartilhado) do contêiner de armazenamento de BLOBs do Azure em que o script de instalação e seus arquivos associados estão armazenados. Para obter mais informações, consulte [instalação personalizada para um Azure-SSIS ir](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup).
   
   c. Marque a caixa de seleção **selecionar uma VNet para sua Azure-SSIS Integration Runtime para unir e permitir que o ADF crie determinados recursos de rede** para escolher se deseja unir seu tempo de execução de integração a uma rede virtual.

   Selecione-o se você usar um servidor de banco de dados SQL do Azure com pontos de extremidade de serviço de rede virtual ou uma instância gerenciada com um ponto de extremidades privado para hospedar o SSISDB ou se você precisar de acesso a dados locais sem configurar um IR hospedado internamente. Para obter mais informações, consulte [criar um Azure-SSIS ir em uma rede virtual](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). 
   
   d. Marque a caixa de seleção **configurar Integration Runtime Autohospedados como um proxy para sua Azure-SSIS Integration Runtime** para escolher se deseja configurar um ir auto-hospedado como um proxy para seu Azure-SSIS ir. Para obter mais informações, consulte [configurar um ir auto-hospedado como um proxy](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis).   

1. Selecione **Seguinte**. 

1. Na página **Resumo** , examine todas as configurações de provisionamento, marque os links de documentação recomendados e selecione **concluir** para iniciar a criação do tempo de execução de integração. 

   > [!NOTE]
   > Excluindo qualquer hora de instalação personalizada, esse processo deve ser concluído em 5 minutos.
   >
   > Se você usar o SSISDB, o serviço de Data Factory se conectará ao servidor de banco de dados para preparar o SSISDB. 
   > 
   > Quando você provisiona um Azure-SSIS IR, o Access redistribuível e o Azure Feature Pack para SSIS também são instalados. Esses componentes fornecem conectividade com arquivos do Excel, arquivos de acesso e várias fontes de dados do Azure, além das fontes de dados que os componentes internos já suportam. Para obter informações sobre outros componentes que você pode instalar, consulte [instalação personalizada para um Azure-SSIS ir](how-to-configure-azure-ssis-ir-custom-setup.md).

1. No separador **Ligações**, mude para **Runtimes de Integração**, se for necessário. Selecione **Atualizar** para atualizar o estado. 

   ![Estado de criação, com o botão "Atualizar"](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

1. Utilize as ligações na coluna **Ações** para iniciar/parar, editar ou eliminar o runtime de integração. Utilize a última ligação para ver o código JSON para o runtime de integração. Os botões de editar e eliminar estão ativados, apenas quando a resposta a incidentes está parada. 

   ![Ligações na coluna "Ações"](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png) 

## <a name="deploy-ssis-packages"></a>Implementar pacotes de SSIS

Se você usar o SSISDB, poderá implantar seus pacotes nele e executá-los no Azure-SSIS IR usando as ferramentas SQL Server Data Tools ou SQL Server Management Studio. Essas ferramentas se conectam ao servidor de banco de dados por meio do ponto de extremidade do servidor: 

- Para um servidor de banco de dados SQL do Azure com um ponto de extremidade privado, o formato de ponto de extremidade do servidor é `<server name>.database.windows.net`.
- Para uma instância gerenciada com um ponto de extremidade privado, o formato de ponto de extremidade do servidor é `<server name>.<dns prefix>.database.windows.net`.
- Para uma instância gerenciada com um ponto de extremidade público, o formato de ponto de extremidade do servidor é `<server name>.public.<dns prefix>.database.windows.net,3342`. 

Se você não usar o SSISDB, poderá implantar seus pacotes em sistemas de arquivos, compartilhamentos de arquivos ou arquivos do Azure. Em seguida, você pode executá-los no Azure-SSIS IR usando as ferramentas de linha de comando `dtinstall`, `dtutil`e `dtexec`. Para obter mais informações, consulte [implantar pacotes do SSIS](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server). 

Em ambos os casos, você também pode executar seus pacotes implantados no Azure-SSIS IR usando a atividade executar pacote SSIS em pipelines Data Factory. Para obter mais informações, consulte [invocar a execução do pacote SSIS como uma atividade de data Factory de primeira classe](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).

Consulte também a seguinte documentação do SSIS: 

- [Implantar, executar e monitorar pacotes do SSIS no Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial) 
- [Conectar-se ao SSISDB no Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database) 
- [Agendar execuções de pacote no Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages) 
- [Connect to on-premises data sources with Windows authentication (Ligar a origens de dados no local com a autenticação do Windows)](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre como personalizar o runtime de integração do Azure-SSIS, avance para o seguinte artigo: 

> [!div class="nextstepaction"]
> [Personalizar um Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)