---
title: Prever o tempo de execução da integração Azure-SSIS
description: Saiba como aprovisionar o runtime de integração do Azure-SSIS no Azure Data Factory, para que possa implementar e executar pacotes SSIS no Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 12/23/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.openlocfilehash: 91962b578f9620384af86d25a33ba35830abb285
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418631"
---
# <a name="provision-the-azure-ssis-integration-runtime-in-azure-data-factory"></a>Prever o tempo de execução de integração Azure-SSIS na Fábrica de Dados Azure

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este tutorial fornece passos para a utilização do portal Azure para fornecer um tempo de execução de integração do Azure-SQL Server Integration Services (SSIS) na Azure Data Factory. Um IR Azure-SSIS suporta:

- Pacotes de execução implantados no catálogo SSIS (SSISDB) hospedados por um servidor de base de dados Azure SQL ou por uma instância gerida (Project Deployment Model).
- Pacotes de execução implantados em sistemas de ficheiros, partilhas de ficheiros ou Ficheiros Azure (Modelo de Implementação de Pacotes). 

Depois de ser aprovisionado um IR Azure-SSIS, pode utilizar ferramentas familiares para implantar e executar os seus pacotes em Azure. Estas ferramentas incluem Ferramentas de Dados do Servidor SQL (SSDT), Estúdio de `dtinstall` `dtutil`Gestão `dtexec`de Servidores SQL (SSMS) e ferramentas de linha de comando como , e .

Para obter informações concetuais sobre IRs Azure-SSIS, veja [Descrição geral do runtime de integração Azure-SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime).

Neste tutorial, vai concluir os seguintes passos:

> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Aprovisionar um runtime de integração Azure-SSIS.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Assinatura Azure.** Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
- **Servidor de base de dados Azure SQL (opcional)**. Se ainda não tiver um servidor de base de dados, crie um no portal do Azure antes de começar. A Data Factory irá, por sua vez, criar uma instância SSISDB neste servidor de base de dados. 

  Recomendamos que crie o servidor de base de dados na mesma região do Azure que o integration runtime. Esta configuração permite que a integração de tempo de execução escreva registos de execução no SSISDB sem atravessar regiões azure.

  Tenha em mente estes pontos:

  - Com base no servidor de base de dados selecionado, a instância SSISDB pode ser criada em seu nome como uma única base de dados, como parte de uma piscina elástica, ou numa instância gerida. Pode ser acessível numa rede pública ou através da adesão a uma rede virtual. Para obter orientações na escolha do tipo de servidor de base de dados para hospedar o SSISDB, consulte Compare uma base de [dados única azure SQL, piscina elástica e instância gerida](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance). 
  
    Se utilizar um servidor de base de dados Azure SQL com regras de firewall IP/pontos finais de serviço de rede virtual ou uma instância gerida com ponto final privado para hospedar o SSISDB, ou se necessitar de acesso a dados no local sem configurar um IR auto-hospedado, tem de se juntar ao seu IR Azure-SSIS a uma rede virtual. Para mais informações, consulte [Criar um IR Azure-SSIS numa rede virtual](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).

  - Confirme que a definição de **permitir o acesso aos serviços Azure** está ativada para o servidor de base de dados. Esta definição não é aplicável quando utiliza um servidor de base de dados Azure SQL com regras de firewall IP/pontos finais de serviço de rede virtual ou uma instância gerida com ponto final privado para hospedar o SSISDB. Para obter mais informações, veja [Proteger a base de dados SQL do Azure](../sql-database/sql-database-security-tutorial.md#create-firewall-rules). Para ativar esta definição utilizando o PowerShell, consulte [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule).

  - Adicione o endereço IP da máquina cliente, ou uma gama de endereços IP que inclua o endereço IP da máquina cliente, à lista de endereços IP do cliente nas definições de firewall para o servidor de base de dados. Para obter mais informações, veja [Regras de firewall ao nível do servidor da Base de Dados SQL do Azure e ao nível da base de dados](../sql-database/sql-database-firewall-configure.md).

  - Pode ligar-se ao servidor de base de dados utilizando a autenticação SQL com as credenciais de administração do servidor, ou utilizando a autenticação Azure AD com a identidade gerida para a sua fábrica de dados. Para este último, é necessário adicionar a identidade gerida para a sua fábrica de dados num grupo Azure AD com permissões de acesso ao servidor de base de dados. Para mais informações, consulte [Criar um IR Azure-SSIS com autenticação Azure AD](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).

  - Confirme que o seu servidor de base de dados ainda não tem uma instância SSISDB. O fornecimento de um IR Azure-SSIS não suporta a utilização de uma instância SSISDB existente.


> [!NOTE]
> Para uma lista das regiões do Azure em que a Fábrica de Dados e um IR Azure-SSIS estão atualmente disponíveis, consulte a data Factory e a disponibilidade do [Ir SSIS por região.](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all) 

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados

Para criar a sua fábrica de dados através do portal Azure, siga as instruções passo a passo na [Create a data factory via UI](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory). Selecione **Pin para dashboard** durante o faz, para permitir um acesso rápido após a sua criação. 

Depois da sua fábrica de dados ser criada, abra a sua página de visão geral no portal Azure. Selecione o azulejo **do Autor & Monitor** para abrir a página **Let's start num** separador. Aí, pode continuar a criar o seu IR Azure-SSIS.

## <a name="create-an-azure-ssis-integration-runtime"></a>Criar um integration runtime do Azure-SSIS

### <a name="from-the-data-factory-overview"></a>A partir da visão geral da Fábrica de Dados

1. Na página **Introdução**, selecione o mosaico **Configurar Runtime de Integração do SSIS**. 

   ![Mosaico "Configurar Runtime de Integração do SSIS"](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

1. Para os restantes passos para configurar um IR Azure-SSIS, veja a secção [Aprovisionar um runtime de integração Azure-SSIS](#provision-an-azure-ssis-integration-runtime). 

### <a name="from-the-authoring-ui"></a>Da autoria da UI

1. Na UI da Fábrica de Dados Azure, mude para o separador **Editar** e selecione **Ligações**. Em seguida, mude para o separador **Integration Runtimes** para ver os tempos de execução de integração existentes na sua fábrica de dados. 

   ![Seleções para visualização de IRs existentes](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

1. Selecione **Novo** para criar um IR Azure-SSIS. 

   ![Runtime de integração através do menu](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

1. No painel de configuração de tempo de **integração,** selecione os **pacotes SSIS existentes de lift-and-shift para executar em** azulejo Azure e, em seguida, selecione **Next**. 

   ![Especifique o tipo de runtime de integração](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

1. Para os restantes passos para configurar um IR Azure-SSIS, veja a secção [Aprovisionar um runtime de integração Azure-SSIS](#provision-an-azure-ssis-integration-runtime). 

## <a name="provision-an-azure-ssis-integration-runtime"></a>Aprovisionar um runtime de integração Azure-SSIS

1. Na secção **Definições Gerais** do painel de configuração de tempo de **integração,** complete os seguintes passos. 

   ![Definições gerais](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   1. Em **Nome**, introduza o nome do seu runtime de integração. 

   1. Em **Descrição**, introduza a descrição do seu runtime de integração. 

   1. Em **Localização**, selecione a localização do seu runtime de integração. Apenas são apresentadas as localizações suportadas. Recomendamos que selecione a mesma localização do seu servidor de base de dados para alojar a SSISDB. 

   1. Em **Tamanho do Nó**, selecione o tamanho de nó do cluster do runtime de integração. Apenas são apresentados os tamanhos de nó suportados. Selecione um grande tamanho do nó (escala para cima) se quiser executar muitos pacotes intensivos de computação ou de memória intensiva. 

   1. Em **Numero de Nós**, selecione o número de nós do cluster do runtime de integração. Apenas são apresentados os números de nó suportados. Selecione um grande cluster com muitos nós (escala para fora) se quiser executar muitos pacotes em paralelo. 

   1. Para **Edição/Licença,** selecione a edição do SQL Server para o seu tempo de integração: Standard ou Enterprise. Selecione Enterprise se pretender utilizar funcionalidades avançadas no seu tempo de execução de integração. 

   1. Para **economizar dinheiro,** selecione a opção Benefício Híbrido Azure para o seu tempo de integração: **Sim** ou **Não**. Selecione **Sim** se quiser trazer a sua própria licença SQL Server com Garantia de Software para beneficiar de economias de custos com uso híbrido. 

   1. Selecione **Seguinte**. 

1. Na secção **Definições SQL,** complete os seguintes passos. 

   ![Definições de SQL](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

   1. Selecione o **catálogo Create SSIS (SSISDB) hospedado pelo servidor de base de dados Azure SQL/Managed Instance para armazenar os seus projetos/pacotes/ambientes/caixas** de verificação de registos de execução para escolher o modelo de implementação de pacotes a executar no seu IR Azure-SSIS. Escolherá o Modelo de Implementação do Projeto onde os pacotes são implantados no SSISDB hospedado sisDB hospedados pelo seu servidor de base de dados, ou o Modelo de Implementação de Pacotes onde os pacotes são implantados em sistemas de ficheiros, partilhas de ficheiros ou Ficheiros Azure.
   
      Se selecionar a caixa de verificação, terá de trazer o seu próprio servidor de base de dados para hospedar o SSISDB que criaremos e geriremos em seu nome.
   
      1. Em **Subscrição**, selecione a subscrição do Azure que tem o servidor da base de dados para alojar a SSISDB. 

      1. Em **Localização**, selecione a mesma localização do seu servidor de base de dados para alojar a SSISDB. Recomendamos que selecione a mesma localização do seu runtime de integração.

      1. Em **Ponto Final do Servidor de Base de Dados do Catálogo**, selecione o ponto final do seu servidor de base de dados para alojar a SSISDB. 
   
         Com base no servidor de base de dados selecionado, a instância SSISDB pode ser criada em seu nome como uma única base de dados, como parte de uma piscina elástica, ou numa instância gerida. Pode ser acessível numa rede pública ou através da adesão a uma rede virtual. Para obter orientações na escolha do tipo de servidor de base de dados para hospedar o SSISDB, consulte Compare uma base de [dados única azure SQL, piscina elástica e instância gerida](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance).   

         Se selecionar um servidor de base de dados Azure SQL com regras de firewall IP/pontos finais de serviço de rede virtual ou uma instância gerida com ponto final privado para hospedar o SSISDB, ou se necessitar de acesso a dados no local sem configurar um IR auto-hospedado, tem de se juntar ao seu IR Azure-SSIS a uma rede virtual. Para mais informações, consulte [Criar um IR Azure-SSIS numa rede virtual](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).

      1. Selecione a **autenticação AAD use com a identidade gerida para a sua** caixa de verificação ADF para escolher o método de autenticação para o seu servidor de base de dados para hospedar o SSISDB. Escolherá a autenticação SQL ou a autenticação Azure AD com a identidade gerida para a sua fábrica de dados.

         Se selecionar a caixa de verificação, terá de adicionar a identidade gerida para a sua fábrica de dados num grupo Azure AD com permissões de acesso ao seu servidor de base de dados. Para mais informações, consulte [Criar um IR Azure-SSIS com autenticação Azure AD](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).
   
      1. Para o nome de **utilizador do Administrador,** introduza o nome de utilizador da autenticação SQL para o seu servidor de base de dados para hospedar o SSISDB. 

      1. Para **obter a palavra-passe do Administrador,** introduza a palavra-passe de autenticação SQL para o seu servidor de base de dados para hospedar o SSISDB. 

      1. Para **catalogar O Nível de Serviço**de Base de Dados do Catálogo, selecione o nível de serviço para o seu servidor de base de dados para hospedar o SSISDB. Selecione o nível Básico, Standard ou Premium, ou selecione um nome de piscina elástica.

      1. Selecione **Ligação de teste**. Se o teste for bem sucedido, selecione **Seguinte**. 

1. Na secção **Definições Avançadas,** preencha os seguintes passos. 

   ![Definições avançadas](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   1. Para **execuções paralelas máximas por nó,** selecione o número máximo de pacotes para executar simultaneamente por nó no seu cluster de tempo de execução de integração. Apenas são apresentados os números de pacotes suportados. Selecione um número baixo se quiser usar mais de um núcleo para executar um único pacote grande que seja computacional ou intensivo de memória. Selecione um número elevado se quiser executar um ou mais pacotes pequenos num único núcleo. 

   1. Selecione o Tempo de Funcionamento de **Integração Azure-SSIS com configurações/instalações de componentes adicionais** para escolher se pretende adicionar configurações personalizadas padrão/express no seu IR Azure-SSIS. Para mais informações, consulte [a configuração personalizada para um IR Azure-SSIS](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup).
   
   1. Selecione a Select a VNet para o seu Tempo de **Integração Azure-SSIS para aderir, permitir que a ADF crie certos recursos de rede e, opcionalmente, traga** a sua própria caixa de verificação de endereços IP públicos estáticos para escolher se pretende aderir ao seu IR Azure-SSIS a uma rede virtual.

      Selecione-o se utilizar um servidor de base de dados Azure SQL com regras de firewall IP/pontos finais de serviço de rede virtual ou uma instância gerida com ponto final privado para hospedar o SSISDB, ou se necessitar de acesso a dados no local sem configurar um IR auto-hospedado. Para mais informações, consulte [Criar um IR Azure-SSIS numa rede virtual](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). 
   
   1. Selecione o Tempo de Execução de Integração Auto-Hospedado como procuração para a sua caixa de verificação de tempo de **integração Azure-SSIS** para escolher se pretende configurar um IR auto-hospedado como procuração para o seu IR Azure-SSIS. Para mais informações, consulte [Configurar um IR auto-hospedado como procuração](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis).   

   1. Selecione **Continuar**. 

1. Na secção **Resumo,** reveja todas as definições de provisionamento, marque os links de documentação recomendados e selecione **Finish** para iniciar a criação do seu tempo de execução de integração. 

   > [!NOTE]
   > Excluindo qualquer tempo de configuração personalizado, este processo deve terminar dentro de 5 minutos.
   >
   > Se utilizar o SSISDB, o serviço Data Factory ligar-se-á ao seu servidor de base de dados para preparar o SSISDB. 
   > 
   > Quando for disponibilizaum Pacote de Funcionalidades Azure-SSIS, Access Redistribuable e Azure para SSIS também estão instalados. Estes componentes fornecem conectividade aos ficheiros Excel, ficheiros de Acesso e várias fontes de dados do Azure, além das fontes de dados que os componentes incorporados já suportam. Para obter informações sobre outros componentes que possa instalar, consulte a [configuração personalizada para um IR Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md).

1. No separador **Ligações**, mude para **Runtimes de Integração**, se for necessário. Selecione **Atualizar** para atualizar o estado. 

   ![Estado de criação, com o botão "Atualizar"](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

1. Utilize as ligações na coluna **Ações** para iniciar/parar, editar ou eliminar o runtime de integração. Utilize a última ligação para ver o código JSON para o runtime de integração. Os botões de editar e eliminar estão ativados, apenas quando a resposta a incidentes está parada. 

   ![Ligações na coluna "Ações"](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png) 

## <a name="deploy-ssis-packages"></a>Implementar pacotes de SSIS

Se utilizar as ferramentas SSISDB( SSDT) ou SQL Server Management Studio (SSMS). Estas ferramentas conectam-se ao seu servidor de base de dados através do seu ponto final do servidor: 

- Para um servidor de base de dados Azure `<server name>.database.windows.net`SQL, o formato final do servidor é .
- Para uma instância gerida com ponto final privado, `<server name>.<dns prefix>.database.windows.net`o formato final do servidor é .
- Para uma instância gerida com ponto final público, `<server name>.public.<dns prefix>.database.windows.net,3342`o formato final do servidor é . 

Se não utilizar o SSISDB, pode implantar os seus pacotes em sistemas de ficheiros, partilhas de ficheiros `dtinstall` `dtutil`ou `dtexec` Ficheiros Azure e executá-los no IR Azure-SSIS utilizando as ferramentas de linha de comando e linhas de comando. Para mais informações, consulte a implementação de [pacotes SSIS](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server). 

Em ambos os casos, também pode executar os seus pacotes implantados no Ir Azure-SSIS utilizando a atividade do Pacote Execute SSIS em pipelines data Factory. Para mais informações, consulte a execução do [pacote Invoke SSIS como uma atividade](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)de fábrica de dados de primeira classe.

Consulte também a seguinte documentação do SSIS: 

- [Implementar, executar e monitorizar os pacotes SSIS em Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial) 
- [Ligue-se ao SSISDB em Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database) 
- [Agendar execuções de pacotes em Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages) 
- [Connect to on-premises data sources with Windows authentication (Ligar a origens de dados no local com a autenticação do Windows)](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre como personalizar o runtime de integração do Azure-SSIS, avance para o seguinte artigo: 

> [!div class="nextstepaction"]
> [Personalize um IR Azure-SSIS](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)