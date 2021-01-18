---
title: Provisão do tempo de integração Azure-SSIS
description: Saiba como aprovisionar o runtime de integração do Azure-SSIS no Azure Data Factory, para que possa implementar e executar pacotes SSIS no Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 10/13/2020
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.openlocfilehash: 98d18c1cd65ccd50d120f8a9edd693f79e87787e
ms.sourcegitcommit: 6628bce68a5a99f451417a115be4b21d49878bb2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/18/2021
ms.locfileid: "98555851"
---
# <a name="provision-the-azure-ssis-integration-runtime-in-azure-data-factory"></a>Provisão do tempo de integração Azure-SSIS na Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este tutorial fornece passos para a utilização do portal Azure para fornecer um tempo de integração de servidores Azure-SQL (SSIS) na Azure Data Factory (ADF). Um Azure-SSIS IR suporta:

- Pacotes de execução implantados no catálogo SSIS (SSISDB) hospedados pelo servidor de base de dados Azure SQL/Gestão de Instâncias (Modelo de Implementação de Projetos)
- Executando pacotes implantados no sistema de ficheiros, ficheiros Azure ou base de dados do SQL Server (MSDB) hospedada por Azure SQL Managed Instance (Modelo de Implementação de Pacotes)

Depois de ser alojado um Azure-SSIS IR, pode utilizar ferramentas familiares para implantar e executar as suas encomendas em Azure. Estas ferramentas já estão ativadas pelo Azure e incluem ferramentas de dados do servidor SQL (SSDT), SQL Server Management Studio (SSMS) e utilitários de linha de comando como [dtutil](/sql/integration-services/dtutil-utility) e [AzureDTExec](./how-to-invoke-ssis-package-azure-enabled-dtexec.md).

Para obter informações concetuais sobre IRs Azure-SSIS, veja [Descrição geral do runtime de integração Azure-SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime).

Neste tutorial, irá concluir os seguintes passos:

> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Aprovisionar um runtime de integração Azure-SSIS.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Assinatura Azure**. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

- **Servidor de base de dados Azure SQL (opcional)**. Se ainda não tiver um servidor de base de dados, crie um no portal do Azure antes de começar. A Data Factory criará, por sua vez, uma instância SSISDB neste servidor de base de dados. 

  Recomendamos que crie o servidor de base de dados na mesma região do Azure que o integration runtime. Esta configuração permite que o tempo de execução da integração escreva os registos de execução em SSISDB sem atravessar regiões de Azure.

  Tenha em mente estes pontos:

  - Com base no servidor de base de dados selecionado, a instância SSISDB pode ser criada em seu nome como uma única base de dados, como parte de uma piscina elástica, ou em um caso gerido. Pode ser acessível numa rede pública ou através da adesão a uma rede virtual. Para obter orientações na escolha do tipo de servidor de base de dados para hospedar o SSISDB, consulte [Compare SQL Database e SQL Managed Instance](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-sql-database-and-sql-managed-instance). 
  
    Se utilizar um servidor Azure SQL Database com regras ip firewall/pontos finais de serviço de rede virtual ou uma instância gerida com ponto final privado para hospedar o SSISDB, ou se necessitar de acesso a dados no local sem configurar um IR auto-hospedado, tem de se juntar ao seu Azure-SSIS IR a uma rede virtual. Para obter mais informações, consulte [Criar um Azure-SSIS IR numa rede virtual.](./create-azure-ssis-integration-runtime.md)

  - Confirme que o **acesso a serviços Azure** está ativado para o servidor de base de dados. Esta definição não é aplicável quando utiliza um servidor de base de dados Azure SQL com regras de firewall IP/pontos finais de serviço de rede virtual ou uma instância gerida com ponto final privado para hospedar o SSISDB. Para mais informações, consulte [a Base de Dados SECURE Azure SQL](../azure-sql/database/secure-database-tutorial.md#create-firewall-rules). Para ativar esta definição utilizando o PowerShell, consulte [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule).

  - Adicione o endereço IP da máquina de cliente, ou uma série de endereços IP que incluem o endereço IP da máquina do cliente, à lista de endereços IP do cliente nas definições de firewall para o servidor de base de dados. Para obter mais informações, veja [Regras de firewall ao nível do servidor da Base de Dados SQL do Azure e ao nível da base de dados](../azure-sql/database/firewall-configure.md).

  - Pode ligar-se ao servidor de base de dados utilizando a autenticação SQL com as credenciais de administração do seu servidor ou utilizando a autenticação AZure AD com a identidade gerida para a sua fábrica de dados. Para este último, é necessário adicionar a identidade gerida para a sua fábrica de dados num grupo AZure AD com permissões de acesso ao servidor de base de dados. Para obter mais informações, consulte [Criar um IR Azure-SSIS com autenticação AD Azure](./create-azure-ssis-integration-runtime.md).

  - Confirme que o seu servidor de base de dados ainda não tem uma instância SSISDB. O provisionamento de um IR Azure-SSIS não suporta utilizando uma instância SSISDB existente.

> [!NOTE]
> Para uma lista das regiões de Azure em que a Data Factory e um Azure-SSIS IR estão atualmente disponíveis, consulte a [Data Factory e a disponibilidade de IR SSIS por região.](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all) 

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados

Para criar a sua fábrica de dados através do portal Azure, siga as instruções passo a passo na Criação de uma fábrica de [dados através da UI](./quickstart-create-data-factory-portal.md#create-a-data-factory). Selecione **Pin para o painel de instrumentos** ao fazê-lo, para permitir um acesso rápido após a sua criação. 

Após a criação da sua fábrica de dados, abra a sua página geral no portal Azure. Selecione o **azulejo do Monitor & autor** para abrir a página **Let's get start-page** em separado. Lá, pode continuar a criar o seu Azure-SSIS IR.

## <a name="create-an-azure-ssis-integration-runtime"></a>Criar um integration runtime do Azure-SSIS

### <a name="from-the-data-factory-overview"></a>A partir da visão geral da Fábrica de Dados

1. Na página **Introdução**, selecione o mosaico **Configurar Runtime de Integração do SSIS**. 

   ![Mosaico "Configurar Runtime de Integração do SSIS"](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

1. Para os restantes passos para configurar um IR Azure-SSIS, veja a secção [Aprovisionar um runtime de integração Azure-SSIS](#provision-an-azure-ssis-integration-runtime). 

### <a name="from-the-authoring-ui"></a>Da UI autora

1. No UI da Fábrica de Dados Azure, mude para o **separador Editar** e selecione **Ligações**. Em seguida, mude para o separador **Tempos de Execução** de Integração para ver os tempos de integração existentes na sua fábrica de dados. 

   ![Seleções para visualização de IRs existentes](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

1. Selecione **Novo** para criar um Azure-SSIS IR e abra o painel **de configuração de tempo de integração.** 

   ![Runtime de integração através do menu](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

1. No painel de configuração de **tempo de integração,** selecione os **pacotes SSIS existentes para executar em azulejo Azure** e, em seguida, selecione **Next**.

   ![Especifique o tipo de runtime de integração](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

1. Para os restantes passos para configurar um IR Azure-SSIS, veja a secção [Aprovisionar um runtime de integração Azure-SSIS](#provision-an-azure-ssis-integration-runtime). 

## <a name="provision-an-azure-ssis-integration-runtime"></a>Aprovisionar um runtime de integração Azure-SSIS

O painel **de configuração do tempo de execução** da Integração tem três páginas onde configura sucessivamente configurar configurar configurações gerais, de implantação e avançadas.

### <a name="general-settings-page"></a>Página de configurações gerais

Na página geral de **configurações** do painel de configuração do tempo de funcionação da **integração,** complete os seguintes passos. 

   ![Definições gerais](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   1. Em **Nome**, introduza o nome do seu runtime de integração. 

   1. Em **Descrição**, introduza a descrição do seu runtime de integração. 

   1. Em **Localização**, selecione a localização do seu runtime de integração. Apenas são apresentadas as localizações suportadas. Recomendamos que selecione a mesma localização do seu servidor de base de dados para alojar a SSISDB. 

   1. Em **Tamanho do Nó**, selecione o tamanho de nó do cluster do runtime de integração. Apenas são apresentados os tamanhos de nó suportados. Selecione um grande tamanho de nó (escala para cima) se quiser executar muitos pacotes intensivos de computação ou intensivos de memória. 

   1. Em **Numero de Nós**, selecione o número de nós do cluster do runtime de integração. Apenas são apresentados os números de nó suportados. Selecione um grande cluster com muitos nós (escala para fora) se quiser executar muitos pacotes em paralelo. 

   1. Para **edição/licença,** selecione a edição sql Server para o seu tempo de integração: Standard ou Enterprise. Selecione Enterprise se quiser utilizar funcionalidades avançadas no seu tempo de execução de integração. 

   1. Para **economizar dinheiro**, selecione a opção Azure Hybrid Benefit para o seu tempo de integração: **Sim** ou **Não**. Selecione **Sim** se quiser trazer a sua própria licença SQL Server com Software Assurance para beneficiar de economias de custos com uso híbrido. 

   1. Selecione **Seguinte**. 

### <a name="deployment-settings-page"></a>Página de definições de implementação

Na página de configurações de **implementação** do painel de configuração do tempo de execução da **integração,** tem as opções para criar lojas de pacotes SSISDB e Azure-SSIS IR.

#### <a name="creating-ssisdb"></a>Criação de SSISDB

Na página de definições de **implementação** do painel de configuração do tempo de execução da **integração,** se pretender implantar as suas encomendas no SSISDB (Modelo de Implementação de Projetos), selecione o **catálogo Create SSIS (SSISDB) hospedado pelo servidor de base de dados Azure SQL/Gestão de Instâncias para armazenar os seus projetos/pacotes/ambientes/caixa de verificação de registos de execução.** Em alternativa, se pretender implantar as suas encomendas no sistema de ficheiros, nos Ficheiros Azure ou na base de dados do SQL Server (MSDB) hospedada pela Azure SQL Managed Instance (Modelo de Implementação de Pacotes), não é necessário criar SSISDB nem selecionar a caixa de verificação.

Independentemente do seu modelo de implementação, se pretender utilizar o SqL Server Agent hospedado pela Azure SQL Managed Instance para orquestrar/agendar as execuções do seu pacote, é ativado pelo SSISDB, por isso selecione a caixa de verificação de qualquer maneira. Para obter mais informações, consulte [as execuções do pacote SSIS da Agenda através do Agente de Instância Gerido Azure SQL](./how-to-invoke-ssis-package-managed-instance-agent.md).
   
Se selecionar a caixa de verificação, preencha os seguintes passos para trazer o seu próprio servidor de base de dados para hospedar o SSISDB que iremos criar e gerir em seu nome.

   ![Definições de implementação para SSISDB](./media/tutorial-create-azure-ssis-runtime-portal/deployment-settings.png)
   
   1. Em **Subscrição**, selecione a subscrição do Azure que tem o servidor da base de dados para alojar a SSISDB. 

   1. Em **Localização**, selecione a mesma localização do seu servidor de base de dados para alojar a SSISDB. Recomendamos que selecione a mesma localização do seu runtime de integração.

   1. Em **Ponto Final do Servidor de Base de Dados do Catálogo**, selecione o ponto final do seu servidor de base de dados para alojar a SSISDB. 
   
      Com base no servidor de base de dados selecionado, a instância SSISDB pode ser criada em seu nome como uma única base de dados, como parte de uma piscina elástica, ou em um caso gerido. Pode ser acessível numa rede pública ou através da adesão a uma rede virtual. Para obter orientações na escolha do tipo de servidor de base de dados para hospedar o SSISDB, consulte [Compare SQL Database e SQL Managed Instance](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-sql-database-and-sql-managed-instance).   

      Se selecionar um servidor de base de dados Azure SQL com regras de firewall IP/pontos finais de serviço de rede virtual ou uma instância gerida com ponto final privado para hospedar o SSISDB, ou se necessitar de acesso a dados no local sem configurar um IR auto-hospedado, tem de se juntar ao seu Azure-SSIS IR a uma rede virtual. Para obter mais informações, consulte [Criar um Azure-SSIS IR numa rede virtual.](./create-azure-ssis-integration-runtime.md)

   1. Selecione a **autenticação Azure AD de utilização com a identidade gerida para a sua** caixa de verificação ADF para escolher o método de autenticação para o seu servidor de base de dados para hospedar o SSISDB. Você escolherá a autenticação SQL ou a autenticação AZURE AD com a identidade gerida para a sua fábrica de dados.

      Se selecionar a caixa de verificação, terá de adicionar a identidade gerida para a sua fábrica de dados num grupo AD Azure com permissões de acesso ao servidor de base de dados. Para obter mais informações, consulte [Criar um IR Azure-SSIS com autenticação AD Azure](./create-azure-ssis-integration-runtime.md).
   
   1. Para **o nome de utilizador Admin,** insira o nome de utilizador de autenticação SQL para o servidor de base de dados para hospedar o SSISDB. 

   1. Para **a Palavra-passe de Admin,** introduza a palavra-passe de autenticação SQL para o seu servidor de base de dados para hospedar o SSISDB. 

   1. Para **o Catálogo Database Service Tier**, selecione o nível de serviço para o seu servidor de base de dados para hospedar o SSISDB. Selecione o nível Básico, Standard ou Premium ou selecione um nome elástico da piscina.

Selecione **a ligação de teste** quando aplicável e se for bem sucedida, selecione **Seguinte**.

#### <a name="creating-azure-ssis-ir-package-stores"></a>Criação de lojas de pacotes Azure-SSIS IR

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

### <a name="advanced-settings-page"></a>Página definições avançadas

Na página **de configurações avançadas** do painel de configuração do tempo de execução da **integração,** complete os seguintes passos. 

   ![Definições avançadas](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   1. Para **execuções paralelas máximas por nó,** selecione o número máximo de pacotes a executar simultaneamente por nó no seu cluster de tempo de execução de integração. Apenas são apresentados os números de pacotes suportados. Selecione um número baixo se quiser usar mais do que um núcleo para executar um único pacote grande que seja compute ou memória intensiva. Selecione um número alto se quiser executar uma ou mais embalagens pequenas num único núcleo. 

   1. Selecione o **tempo de execução de integração Azure-SSIS com configurações de sistema/instalações de componentes adicionais** para escolher se deseja adicionar configurações personalizadas standard/express no seu Azure-SSIS IR. Para obter mais informações, consulte [a configuração personalizada para um Azure-SSIS IR](./how-to-configure-azure-ssis-ir-custom-setup.md).
   
   1. Selecione o **VNet para o seu tempo de execução de integração Azure-SSIS para aderir, permita que a ADF crie certos recursos de rede e, opcionalmente, traga a sua própria caixa de verificação de endereços IP públicos estáticos** para escolher se pretende aderir ao seu Azure-SSIS IR a uma rede virtual.

      Selecione-o se utilizar um servidor de base de dados Azure SQL com regras de firewall IP/pontos finais de serviço de rede virtual ou uma instância gerida com ponto final privado para hospedar o SSISDB, ou se necessitar de acesso a dados no local sem configurar um IR auto-hospedado. Para obter mais informações, consulte [Criar um Azure-SSIS IR numa rede virtual.](./create-azure-ssis-integration-runtime.md) 
   
   1. Selecione o **Prazo de Execução de Integração Self-Hosted como um proxy para a sua caixa de verificação de runtime de integração Azure-SSIS** para escolher se pretende configurar um IR auto-hospedado como proxy para o seu Azure-SSIS IR. Para obter mais informações, consulte [Configurar um IR auto-hospedado como procuração](./self-hosted-integration-runtime-proxy-ssis.md).   

   1. Selecione **Continuar**. 

Na página **sumária** do painel de configuração do tempo de execução da **integração,** reveja todas as definições de provisionamento, marque as ligações de documentação recomendadas e selecione **Acabamento** para iniciar a criação do seu tempo de execução de integração. 

   > [!NOTE]
   > Excluindo qualquer tempo de configuração personalizado, este processo deve terminar dentro de 5 minutos.
   >
   > Se utilizar o SSISDB, o serviço Data Factory liga-se ao servidor de base de dados para preparar o SSISDB. 
   > 
   > Quando forres um Pacote de Recursos Azure-SSIS IR, Access Redistributable e Azure Feature Pack para SSIS também estão instalados. Estes componentes fornecem conectividade aos ficheiros Excel, ficheiros de acesso e várias fontes de dados Azure, além das fontes de dados que os componentes incorporados já suportam. Para obter mais informações sobre componentes incorporados/pré-instalados, consulte [componentes incorporados/pré-instalados no Azure-SSIS IR](./built-in-preinstalled-components-ssis-integration-runtime.md). Para obter mais informações sobre componentes adicionais que possa instalar, consulte [configurações personalizadas para Azure-SSIS IR](./how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="connections-pane"></a>Painel de ligações

No painel **de ligações** do centro **De Gestão,** mude para a página **de tempos de execução** de integração e selecione **Refresh**. 

   ![Painel de ligações](./media/tutorial-create-azure-ssis-runtime-portal/connections-pane.png)

   Pode editar/reconfigurar o seu Azure-SSIS IR selecionando o seu nome. Também pode selecionar os botões relevantes para monitorizar/iniciar/parar/eliminar o seu Azure-SSIS IR, gerar automaticamente um pipeline ADF com a atividade do Pacote SSIS executar para executar no seu Azure-SSIS IR e ver o código/carga JSON do seu Azure-SSIS IR.  A edição/eliminação do seu Azure-SSIS IR só pode ser feita quando esta é interrompida.

## <a name="deploy-ssis-packages"></a>Implementar pacotes de SSIS

Se utilizar o SSISDB, pode colocar as suas encomendas nele e executá-las no seu Azure-SSIS IR utilizando as ferramentas SSDT ou SSMS ativadas pelo Azure. Estas ferramentas ligam-se ao servidor da sua base de dados através do seu ponto final do servidor: 

- Para um servidor de base de dados Azure SQL, o formato de ponto final do servidor é `<server name>.database.windows.net` .
- Para uma instância gerida com ponto final privado, o formato de ponto final do servidor é `<server name>.<dns prefix>.database.windows.net` .
- Para um caso gerido com ponto final público, o formato de ponto final do servidor é `<server name>.public.<dns prefix>.database.windows.net,3342` . 

Se não utilizar o SSISDB, pode implantar as suas encomendas no sistema de ficheiros, ficheiros Azure ou MSDB hospedados pela sua Azure SQL Managed Instance e executá-las no seu Azure-SSIS IR utilizando utilitários de linha de comando [dtutil](/sql/integration-services/dtutil-utility) e [AzureDTExec.](./how-to-invoke-ssis-package-azure-enabled-dtexec.md) 

Para obter mais informações, consulte [projetos/pacotes SSIS de implementação.](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages)

Em ambos os casos, também pode executar os seus pacotes implantados no Azure-SSIS IR utilizando a atividade do Pacote SSIS executar em pipelines data Factory. Para obter mais informações, consulte a execução do [pacote SSIS como uma atividade de primeira classe data factory](./how-to-invoke-ssis-package-ssis-activity.md).

Consulte também a seguinte documentação SSIS: 

- [Implementar, executar e monitorizar pacotes SSIS em Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial) 
- [Ligue ao SSISDB em Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database) 
- [Agendar execuções de pacotes em Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages) 
- [Connect to on-premises data sources with Windows authentication (Ligar a origens de dados no local com a autenticação do Windows)](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre como personalizar o runtime de integração do Azure-SSIS, avance para o seguinte artigo: 

> [!div class="nextstepaction"]
> [Personalize um Azure-SSIS IR](./how-to-configure-azure-ssis-ir-custom-setup.md)