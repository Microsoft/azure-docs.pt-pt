---
title: Criar um tempo de integração Azure-SSIS na Fábrica de Dados Azure
description: Saiba como criar um tempo de funcionação de integração Azure-SSIS na Azure Data Factory para que possa implementar e executar pacotes SSIS em Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/27/2020
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.openlocfilehash: af6e6346616255cfb153d59df7031c6d0d4710da
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84117891"
---
# <a name="create-an-azure-ssis-integration-runtime-in-azure-data-factory"></a>Criar um tempo de integração Azure-SSIS na Fábrica de Dados Azure

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este artigo fornece medidas para o fornecimento de um tempo de execução de integração do Azure-SQL Server Integration Services (SSIS) na Azure Data Factory. Um IR Azure-SSIS suporta:

- Pacotes de execução implantados no catálogo SSIS (SSISDB) na Base de Dados Azure SQL ou Na instância gerida pela SQL (Modelo de Implantação de Projetos).
- Pacotes de execução implantados em sistemas de ficheiros, partilhas de ficheiros ou Ficheiros Azure (Modelo de Implementação de Pacotes). 

Depois de ser aprovisionado um IR Azure-SSIS, pode utilizar ferramentas familiares para implantar e executar os seus pacotes em Azure. Estas ferramentas incluem Ferramentas de Dados do Servidor SQL (SSDT), Estúdio de Gestão de Servidores SQL (SSMS) e ferramentas de linha de comando como `dtinstall` , `dtutil` e `dtexec` .

O tutorial [provisioning Azure-SSIS IR](tutorial-create-azure-ssis-runtime-portal.md) mostra como criar um IR Azure-SSIS através do portal Azure ou da aplicação Data Factory. O tutorial também mostra como usar opcionalmente uma Base de Dados SQL ou Instância Gerida SQL para hospedar o SSISDB. Este artigo expande-se no tutorial e descreve como fazer estas tarefas opcionais:

- Utilize a Base de Dados SQL com regras de firewall IP/pontos finais de serviço de rede virtual ou uma Instância Gerida SQL com ponto final privado para hospedar o SSISDB. Como pré-requisito, é necessário configurar permissões e configurações de rede virtual para que o seu IR Azure-SSIS se junte a uma rede virtual.

- Utilize a autenticação azure Ative Directory (Azure AD) com a identidade gerida para a sua fábrica de dados ligar-se à Base de Dados SQL ou à Instância Gerida sQL. Como pré-requisito, é necessário adicionar a identidade gerida para a sua fábrica de dados como um utilizador de base de dados que pode criar uma instância SSISDB.

- Junte-se ao seu IR Azure-SSIS a uma rede virtual, ou configure um IR auto-hospedado como procuração para o seu IR Azure-SSIS aceder aos dados no local.

Este artigo mostra como fornecer um IR Azure-SSIS utilizando o portal Azure, Azure PowerShell, e um modelo de Gestor de Recursos Azure.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Assinatura Azure.** Se ainda não tem uma subscrição, pode criar uma conta [de teste gratuita.](https://azure.microsoft.com/pricing/free-trial/)

- **Base de Dados SQL ou Instância Gerida SQL (opcional)**. Se ainda não tiver um servidor na Base de Dados SQL ou numa instância de Instância Gerida SQL, crie um no portal Azure antes de começar. A Data Factory irá, por sua vez, criar uma instância SSISDB para este servidor ou instância. 

  Recomendamos que crie o servidor ou tenha uma instância gerida na mesma região do Azure que o tempo de execução da integração. Esta configuração permite que a integração de tempo de execução escreva registos de execução no SSISDB sem atravessar regiões azure.

  Tenha em mente estes pontos:

  - Com base no produto selecionado, a instância SSISDB pode ser criada em seu nome na Base de Dados SQL como uma única base de dados ou como parte de uma piscina elástica, ou numa Instância Gerida SQL. Pode ser acessível numa rede pública ou através da adesão a uma rede virtual. Para obter orientações na escolha do produto de base de dados apropriado para hospedar o SSISDB, consulte [Compare SQL Database e SQL Managed Instance](#comparison-of-sql-database-and-sql-managed-instance). 
  
    Se utilizar a Base de Dados SQL com regras de firewall IP/pontos finais de serviço de rede virtual ou uma Instância Gerida SQL com ponto final privado para hospedar o SSISDB, ou se necessitar de acesso a dados no local sem configurar um IR auto-hospedado, tem de se juntar ao seu IR Azure-SSIS a uma rede virtual. Para mais informações, consulte [Join a Azure-SSIS IR a uma rede virtual](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

  - Para a Base de Dados SQL, configure as regras de firewall IP/pontos finais do serviço de rede virtual ou uma Instância Gerida SQL com ponto final privado para hospedar o SSISDB. Para obter mais informações, veja [Proteger a base de dados SQL do Azure](../azure-sql/database/secure-database-tutorial.md#create-firewall-rules). Para ativar esta definição utilizando o PowerShell, consulte [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule).

  - Adicione o endereço IP da máquina cliente, ou uma gama de endereços IP que inclua o endereço IP da máquina cliente, à lista de endereços IP do cliente nas definições de firewall para o servidor de base de dados. Para mais informações, consulte [as regras de firewall ao nível do Servidor e](../azure-sql/database/firewall-configure.md)da base de dados .

  - Pode ligar-se ao servidor de base de dados utilizando a autenticação SQL com as credenciais de administração do servidor, ou utilizando a autenticação Azure AD com a identidade gerida para a sua fábrica de dados. Para este último, é necessário adicionar a identidade gerida para a sua fábrica de dados num grupo Azure AD com permissões de acesso ao servidor de base de dados. Para mais informações, consulte a [autenticação Enable Azure AD para um IR Azure-SSIS](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir).

  - Confirme que o seu servidor de base de dados ainda não tem uma instância SSISDB. O fornecimento de um IR Azure-SSIS não suporta a utilização de uma instância SSISDB existente.

- **Rede virtual do Gestor de Recursos Azure (opcional)**. Deve ter uma rede virtual do Gestor de Recursos Azure se pelo menos uma das seguintes condições for verdadeira:
  - Está a hospedar o SSISDB na Base de Dados SQL com regras de firewall IP/pontos finais de serviço de rede virtual ou uma Instância Gerida SQL com ponto final privado.
  - Pretende ligar-se às lojas de dados no local a partir de pacotes SSIS que estão a funcionar no seu IR Azure-SSIS sem configurar um IR auto-hospedado.

- **Azure PowerShell (opcional)**. Siga as instruções em Como instalar e configurar o [Azure PowerShell,](/powershell/azure/install-az-ps)se pretender executar um script PowerShell para fornecer o seu IR Azure-SSIS.

### <a name="regional-support"></a>Suporte regional

Para uma lista das regiões do Azure em que a Fábrica de Dados e um IR Azure-SSIS estão disponíveis, consulte a Data Factory e a disponibilidade do [SSIS IR por região.](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all)

### <a name="comparison-of-sql-database-and-sql-managed-instance"></a>Comparação entre base de dados SQL e Instância Gerida SQL

O quadro seguinte compara certas características da Base de Dados SQL e da Instância Gerida SQL no que diz respeito ao IR Azure-SSIR:

| Funcionalidade | Base de Dados SQL | Instância Gerida do SQL |
|---------|--------------|------------------|
| **Agendamento** | O Agente servidor SQL não está disponível.<br/><br/>Consulte [a execução de um pacote num oleoduto data Factory](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages?view=sql-server-2017#activity).| O Agente de Instância Gerida SQL está disponível. |
| **Autenticação** | Pode criar uma instância SSISDB com um utilizador de base de dados contido que represente qualquer grupo Azure AD com a identidade gerida da sua fábrica de dados como membro na **função db_owner.**<br/><br/>Ver [autenticação AD Enable Azure para criar uma instância SSISDB na Base de Dados SQL](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database). | Pode criar uma instância SSISDB com um utilizador de base de dados contido que represente a identidade gerida da sua fábrica de dados. <br/><br/>Ver [autenticação AD Enable Azure para criar uma instância SSISDB em Instância Gerida SQL](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-sql-managed-instance). |
| **Nível de serviço** | Quando criar um IR Azure-SSIS com base de dados SQL, pode selecionar o nível de serviço para SSISDB. Existem vários níveis de serviço. | Quando cria um IR Azure-SSIS com Instância Gerida SQL, não é possível selecionar o nível de serviço para o SSISDB. Todas as bases de dados da SQL Managed Instance partilham o mesmo recurso atribuído a esse caso. |
| **Rede virtual** | O seu Ir Azure-SSIS pode juntar-se a uma rede virtual do Gestor de Recursos Azure se utilizar a Base de Dados SQL com regras de firewall IP/pontos finais de serviço de rede virtual. | O seu Ir Azure-SSIS pode aderir a uma rede virtual do Gestor de Recursos Azure se utilizar a Instância Gerida SQL com ponto final privado. A rede virtual é necessária quando não permite um ponto final público para a sua instância gerida.<br/><br/>Se se juntar ao seu IR Azure-SSIS à mesma rede virtual que a sua instância gerida, certifique-se de que o seu IR Azure-SSIS se encontra numa sub-rede diferente da sua instância gerida. Se você juntar o seu IR Azure-SSIS a uma rede virtual diferente da sua instância gerida, recomendamos um peering de rede virtual ou uma ligação de rede-rede. Consulte [A Ligação da sua aplicação à Instância Gerida SQL](../azure-sql/managed-instance/connect-application-instance.md). |
| **Transações distribuídas** | Esta funcionalidade é suportada através de transações elásticas. As transações do Microsoft Distributed Transaction (MSDTC) não são suportadas. Se os seus pacotes SSIS utilizarem mSDTC para coordenar transações distribuídas, considere migrar para transações elásticas para a Base de Dados Azure SQL. Para mais informações, consulte [as transações distribuídas através](../azure-sql/database/elastic-transactions-overview.md)das bases de dados da nuvem . | Não suportado. |
| | | |

## <a name="use-the-azure-portal-to-create-an-integration-runtime"></a>Utilize o portal Azure para criar um tempo de integração

Nesta secção, utiliza-se o portal Azure, especificamente a interface de utilizador da Data Factory (UI) ou app, para criar um IR Azure-SSIS.

### <a name="create-a-data-factory"></a>Criar uma fábrica de dados

Para criar a sua fábrica de dados através do portal Azure, siga as instruções passo a passo na [Create a data factory via UI](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory). Selecione **Pin para dashboard** durante o faz, para permitir um acesso rápido após a sua criação. 

Depois da sua fábrica de dados ser criada, abra a sua página de visão geral no portal Azure. Selecione o azulejo **Do Autor & Monitor** para abrir a sua página **Let's start on** on num separador. Aí, pode continuar a criar o seu IR Azure-SSIS.   

### <a name="provision-an-azure-ssis-integration-runtime"></a>Aprovisionar um runtime de integração Azure-SSIS

1. Na página **Introdução**, selecione o mosaico **Configurar Runtime de Integração do SSIS**.

   ![Configurar o mosaico do Integration Runtime do SSIS](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

2. Na secção **Definições Gerais** do painel de configuração de tempo de **integração,** complete os seguintes passos.

   ![Definições gerais](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   1. Em **Nome**, introduza o nome do seu runtime de integração.
   2. Em **Descrição**, introduza a descrição do seu runtime de integração.
   3. Em **Localização**, selecione a localização do seu runtime de integração. Apenas são apresentadas as localizações suportadas. Recomendamos que selecione a mesma localização do seu servidor de base de dados para alojar a SSISDB.
   4. Para tamanho **do nó,** selecione o tamanho do nó no seu cluster de tempo de integração. Apenas são apresentados os tamanhos de nó suportados. Selecione um grande tamanho do nó (escala para cima) se quiser executar muitos pacotes intensivos de computação ou de memória intensiva.
   5. Em **Numero de Nós**, selecione o número de nós do cluster do runtime de integração. Apenas são apresentados os números de nó suportados. Selecione um grande cluster com muitos nós (escala para fora) se quiser executar muitos pacotes em paralelo.
   6. Para **Edição/Licença,** selecione a edição do SQL Server para o seu tempo de integração: Standard ou Enterprise. Selecione Enterprise se pretender utilizar funcionalidades avançadas no seu tempo de execução de integração.
   7. Para **economizar dinheiro,** selecione a opção Benefício Híbrido Azure para o seu tempo de integração: **Sim** ou **Não**. Selecione **Sim** se quiser trazer a sua própria licença SQL Server com Garantia de Software para beneficiar de economias de custos com uso híbrido.
   8. Selecione **Seguinte**.

3. Na secção **Definições SQL,** complete os seguintes passos.

   ![Definições de SQL](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

   1. Selecione o **catálogo Create SSIS (SSISDB) com a SQL Database/SQL Managed Instance para armazenar os seus projetos/pacotes/ambientes/caixas** de verificação de registos de execução para escolher o produto Azure SQL para pacotes a executar no seu IR Azure-SSIS. Você escolherá o Modelo de Implementação do Projeto onde os pacotes são implantados no SSISDB hospedado sQL Database ou SQL Managed Instance, ou o Modelo de Implementação de Pacotes onde os pacotes são implantados em sistemas de ficheiros, partilhas de ficheiros ou Ficheiros Azure. 
    
    Se selecionar a caixa de verificação, terá de trazer a sua própria Base de Dados SQL ou SQL Managed Instance para acolher a instância SSISDB que criaremos e geriremos em seu nome.
   
    1. Para **subscrição,** selecione a subscrição Azure que tem a sua Base de Dados SQL ou Instância Gerida SQL para hospedar o SSISDB. 

    1. Para **localização**, selecione a localização da sua Base de Dados SQL ou da Instância Gerida SQL. Recomendamos que selecione a mesma localização do seu runtime de integração. 

    1. Para **catalogar ponto final**do servidor de dados de dados, selecione o ponto final da sua Base de Dados SQL ou instância gerida por SQL. 
    
       Com base na base de dados SQL selecionada ou na Instância Gerida SQL, a instância SSISDB pode ser criada em seu nome como uma única base de dados ou como parte de um pool elástico na Base de Dados SQL, ou como base de dados de instância numa instância gerida por SQL. Pode ser acessível numa rede pública ou através da adesão a uma rede virtual. Para obter orientações na escolha do produto para hospedar o SSISDB, consulte a Base de [Dados SQL Compare Azure e a Instância Gerida SQL](#comparison-of-sql-database-and-sql-managed-instance).
       Se selecionar a Base de Dados SQL com regras de firewall IP/pontos finais de serviço de rede virtual ou A Instância Gerida SQL com ponto final privado para hospedar o SSISDB, ou se necessitar de acesso a dados no local sem configurar um IR auto-hospedado, precisa de se juntar ao seu IR Azure-SSIS a uma rede virtual. Para mais informações, consulte [Join a Azure-SSIS IR a uma rede virtual](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). 

    1. Selecione a **autenticação AAD use com a identidade gerida para a sua** caixa de verificação ADF para escolher o método de autenticação para o seu servidor de base de dados para hospedar o SSISDB. Escolherá a autenticação SQL ou a autenticação Azure AD com a identidade gerida para a sua fábrica de dados. 
    
        Se selecionar a caixa de verificação, terá de adicionar a identidade gerida para a sua fábrica de dados num grupo Azure AD com permissões de acesso ao seu servidor de base de dados. Para mais informações, consulte a [autenticação Enable Azure AD para um IR Azure-SSIS](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir). 

    1. Para o nome de **utilizador do Administrador,** introduza o nome de utilizador da autenticação SQL para o seu servidor de base de dados para hospedar o SSISDB. 

    1. Para **obter a palavra-passe do Administrador,** introduza a palavra-passe de autenticação SQL para o seu servidor de base de dados para hospedar o SSISDB. 

    1. Para **catalogar O Nível de Serviço**de Base de Dados do Catálogo, selecione o nível de serviço para o seu servidor de base de dados para hospedar o SSISDB. Selecione o nível Básico, Standard ou Premium, ou selecione um nome de piscina elástica. 

      1. Selecione **Ligação de teste**. Se o teste for bem sucedido, selecione **Seguinte**. 

4. Na secção **Definições Avançadas,** preencha os seguintes passos.

   ![Definições avançadas](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   1. Para **execuções paralelas máximas por nó,** selecione o número máximo de pacotes para executar simultaneamente por nó no seu cluster de tempo de execução de integração. Apenas são apresentados os números de pacotes suportados. Selecione um número baixo se quiser usar mais de um núcleo para executar um único pacote grande que seja computacional ou intensivo de memória. Selecione um número elevado se quiser executar um ou mais pacotes pequenos num único núcleo.

   1. Selecione o Tempo de Funcionamento de **Integração Azure-SSIS com configurações/instalações de componentes adicionais** para escolher se pretende adicionar configurações personalizadas padrão/express no seu IR Azure-SSIS. Para mais informações, consulte [a configuração personalizada para um IR Azure-SSIS](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup).

      Se selecionar a caixa de verificação, complete os seguintes passos.

      ![Configurações avançadas com configurações personalizadas](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)
   
      1. Para o recipiente de **configuração Personalizado SAS URI,** introduza o SAS URI do seu recipiente onde armazena scripts e ficheiros associados para configurações personalizadas padrão.

      1. Para **configuração personalizada Express,** selecione **Nova** para abrir o painel de **configuração personalizado Add express** e, em seguida, selecione quaisquer tipos sob o menu de dropdown do tipo **de configuração express,** por exemplo, executar comando **cmdkey,** **Adicionar variável ambiente,** Instalar componente **licenciado,** etc.

        Se selecionar o tipo de **componente licenciado Instalar,** pode então selecionar quaisquer componentes integrados dos nossos parceiros ISV no menu de dropdown do **nome Componente** e, se necessário, insira a chave de licença de produto que adquiriu no campo **chave Licença.**
  
        As configurações personalizadas expressas adicionadas aparecerão na secção **Definições Avançadas.** Para removê-las, pode selecionar as suas caixas de verificação e, em seguida, selecionar **Apagar**.

   1. Selecione o Select a VNet para o seu Tempo de **Integração Azure-SSIS para aderir, permitir que a ADF crie certos recursos de rede e, opcionalmente, traga** a sua própria caixa de verificação de endereços IP públicos estáticos para escolher se pretende aderir ao seu tempo de integração numa rede virtual. 

    Selecione-o se utilizar a Base de Dados SQL com regras de firewall IP/pontos finais de serviço de rede virtual ou Instância Gerida SQL com ponto final privado para hospedar o SSISDB, ou se necessitar de acesso a dados no local (isto é, tem fontes de dados ou destinos no local nos seus pacotes SSIS) sem configurar um IR auto-hospedado. Para mais informações, consulte [O Ir Azure-SSIS para uma rede virtual](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). 

    Se selecionar a caixa de verificação, complete os seguintes passos.

    ![Definições avançadas com uma rede virtual](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

    1. Para **Subscrição**, selecione a subscrição Azure que tem a sua rede virtual.

    1. Para **localização**, é selecionada a mesma localização do seu tempo de execução de integração.

    1. Para **Tipo**, selecione o tipo da sua rede virtual: classic ou Gestor de Recursos Azure. Recomendamos que selecione uma rede virtual do Gestor de Recursos Azure, porque as redes virtuais clássicas serão depreciadas em breve.

    1. Para **Nome VNet,** selecione o nome da sua rede virtual. Deve ser o mesmo utilizado para a sua Base de Dados SQL com pontos finais de serviço de rede virtual ou a sua Instância Gerida SQL com ponto final privado para hospedar o SSISDB. Ou deve ser a mesma ligada à sua rede no local. Caso contrário, pode ser qualquer rede virtual para trazer os seus próprios endereços IP públicos estáticos para o Ir Azure-SSIS.

    1. Para **nome sub-rede,** selecione o nome da subnet para a sua rede virtual. Deve ser o mesmo usado para a sua Base de Dados SQL com pontos finais de serviço de rede virtual para hospedar o SSISDB. Ou deve ser uma sub-rede diferente da utilizada para o seu SQL Managed Instance com ponto final privado para hospedar o SSISDB. Caso contrário, pode ser qualquer subrede para trazer os seus próprios endereços IP públicos estáticos para o Ir Azure-SSIS.

    1. Selecione os endereços IP públicos estáticos para a sua caixa de verificação de tempo de **integração Azure-SSIS** para escolher se pretende trazer os seus próprios endereços IP públicos estáticos para o Ir Azure-SSIS, para que possa permitir na firewall as suas fontes de dados.

        Se selecionar a caixa de verificação, complete os seguintes passos.

        1. Para **primeiro endereço IP público estático,** selecione o primeiro endereço IP público estático que satisfaz os requisitos para o seu IR Azure-SSIS. Se não tiver nenhum, clique em **Criar um novo** link para criar endereços IP públicos estáticos no portal Azure e, em seguida, clique no botão de atualização aqui, para que possa selecioná-los.
        2. Para **o segundo endereço IP público estático,** selecione o segundo endereço IP público estático que satisfaça os requisitos para o seu IR Azure-SSIS. Se não tiver nenhum, clique em **Criar um novo** link para criar endereços IP públicos estáticos no portal Azure e, em seguida, clique no botão de atualização aqui, para que possa selecioná-los.

   1. Selecione o Tempo de Execução de Integração Auto-Hospedado como procuração para a sua caixa de verificação de tempo de **integração Azure-SSIS** para escolher se pretende configurar um IR auto-hospedado como procuração para o seu IR Azure-SSIS. Para mais informações, consulte [Configurar um IR auto-hospedado como procuração](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis). 

      Se selecionar a caixa de verificação, complete os seguintes passos.

      ![Configurações avançadas com um IR auto-hospedado](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

      1. Para o tempo de execução de **integração auto-hospedado,** selecione o seu IR auto-hospedado existente como procuração para o Ir Azure-SSIS.

      1. Para o Serviço Ligado ao Armazenamento de **Montagem,** selecione o serviço ligado ao armazenamento Azure Blob existente ou crie um novo para encenação.

      1. Para **o Caminho de Encenação,** especifique um recipiente de bolha na sua conta de armazenamento Azure Blob selecionada ou deixe-o vazio para utilizar um predefinido para a encenação.

   1. Selecione **Validação VNet**  >  **Continue**. 

1. Na secção **Resumo,** reveja todas as definições de provisionamento, marque os links de documentação recomendados e selecione **Finish** para iniciar a criação do seu tempo de execução de integração.

   > [!NOTE]
   > Excluindo qualquer tempo de configuração personalizado, este processo deve terminar dentro de 5 minutos. Mas pode levar 20 a 30 minutos para o IR Azure-SSIS se juntar a uma rede virtual.
   >
   > Se utilizar o SSISDB, o serviço Data Factory ligar-se-á ao seu servidor de base de dados para preparar o SSISDB. Também configura permissões e configurações para a sua rede virtual, se especificado, e junta o seu IR Azure-SSIS à rede virtual.
   > 
   > Quando for disponibilizaum Pacote de Funcionalidades Azure-SSIS, Access Redistribuable e Azure para SSIS também estão instalados. Estes componentes fornecem conectividade aos ficheiros Excel, ficheiros de Acesso e várias fontes de dados do Azure, além das fontes de dados que os componentes incorporados já suportam. Para obter informações sobre outros componentes que possa instalar, consulte a [configuração personalizada para um IR Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md).

1. No separador **Ligações**, mude para **Runtimes de Integração**, se for necessário. Selecione **Atualizar** para atualizar o estado.

   ![Estado da criação](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

1. Utilize as ligações na coluna **Ações** para iniciar/parar, editar ou eliminar o runtime de integração. Utilize a última ligação para ver o código JSON para o runtime de integração. Os botões de editar e eliminar estão ativados, apenas quando a resposta a incidentes está parada.

   ![Ações do IR Azure SSIS](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)

### <a name="azure-ssis-integration-runtimes-in-the-portal"></a>Runtime de integração de SSIS do Azure no portal

1. Na UI da Fábrica de Dados Azure, mude para o separador **Editar** e selecione **Ligações**. Em seguida, mude para o separador **Integration Runtimes** para ver os tempos de execução de integração existentes na sua fábrica de dados.

   ![Ver IRs existentes](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

1. Selecione **New** para criar um novo IR Azure-SSIS.

   ![Runtime de integração através do menu](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

1. No painel de configuração de tempo de **integração,** selecione os **pacotes SSIS existentes de lift-and-shift para executar em** azulejo Azure e, em seguida, selecione **Next**.

   ![Especifique o tipo de runtime de integração](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

1. Para as restantes etapas para a criação de um IR Azure-SSIS, consulte a secção de tempo de execução de [integração Azure SSIS.](#provision-an-azure-ssis-integration-runtime)

## <a name="use-azure-powershell-to-create-an-integration-runtime"></a>Use o Azure PowerShell para criar um tempo de integração

Nesta secção, utiliza-se o Azure PowerShell para criar um IR Azure-SSIS.

### <a name="create-variables"></a>Criar variáveis

Copiar e colar o seguinte script. Especifique valores para as variáveis. 

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
$ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO|KingswaySoft.IntegrationToolkit|KingswaySoft.ProductivityPack|Theobald.XtractIS or leave it empty]" # OPTIONAL to configure an express custom setup without script
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use SQL Database with IP firewall rules/virtual network service endpoints or SQL Managed Instance with private endpoint to host SSISDB, or if you require access to on-premises data without configuring a self-hosted IR. We recommend an Azure Resource Manager virtual network, because classic virtual networks will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for SQL Database with virtual network service endpoints, or a different subnet from the one used for SQL Managed Instance with a private endpoint
# Public IP address info: OPTIONAL to provide two standard static public IP addresses with DNS name under the same subscription and in the same region as your virtual network
$FirstPublicIP = "[your first public IP address resource ID or leave it empty]"
$SecondPublicIP = "[your second public IP address resource ID or leave it empty]"

### SSISDB info
$SSISDBServerEndpoint = "[your logical server name.database.windows.net or managed instance name.DNS prefix.database.windows.net or managed instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, ensure that there's no existing SSISDB on your database server, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure AD
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for Azure AD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for Azure AD authentication]"
# For the basic pricing tier, specify "Basic," not "B." For standard, premium, and elastic pool tiers, specify "S0," "S1," "S2," "S3," etc. See https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server.
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for SQL Database or leave it empty for SQL Managed Instance]"

### Self-hosted integration runtime info - This can be configured as a proxy for on-premises data access 
$DataProxyIntegrationRuntimeName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingLinkedServiceName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingPath = "" # OPTIONAL to configure a proxy for on-premises data access 
```

### <a name="sign-in-and-select-a-subscription"></a>Iniciar sessão e selecionar uma subscrição

Adicione o seguinte script para iniciar sessão e selecione a sua subscrição Azure.

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName
```

### <a name="validate-the-connection"></a>Validar a ligação

Adicione o seguinte script para validar a ligação à Base de Dados SQL ou à Instância Gerida SQL.

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
                Write-Warning "Cannot connect, exception: $_";
                Write-Warning "Please make sure the server or instance you specified has already been created. Do you want to proceed? [Y/N]"
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

### <a name="configure-the-virtual-network"></a>Configure a rede virtual

Adicione o seguinte script para configurar automaticamente permissões e configurações de rede virtual para o seu tempo de funcionamento de integração Azure-SSIS para aderir.

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

Crie um grupo de [recursos Azure](../azure-resource-manager/management/overview.md) utilizando o comando [New-AzResourceGroup.](/powershell/module/az.resources/new-azresourcegroup) Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos como um grupo.

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

Executar os seguintes comandos para criar um tempo de funcionação de integração Azure-SSIS que executa pacotes SSIS em Azure.

Se não utilizar o SSISDB, pode omiti-lo e os `CatalogServerEndpoint` `CatalogPricingTier` `CatalogAdminCredential` parâmetros.

Se não utilizar a Base de Dados SQL com regras de firewall IP/pontos finais de serviço de rede virtual ou Instância Gerida SQL com ponto final privado para hospedar o SSISDB, ou exigir acesso aos dados no local, pode omitir os `VNetId` parâmetros e `Subnet` parâmetros ou passar valores vazios para eles. Também pode omiti-los se configurar um IR auto-hospedado como procuração para o seu IR Azure-SSIS aceder aos dados no local. Caso contrário, não pode omiti-los e deve passar valores válidos a partir da sua configuração de rede virtual. Para mais informações, consulte [Join a Azure-SSIS IR a uma rede virtual](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

Se utilizar a SQL Managed Instance para hospedar o SSISDB, pode omitir o `CatalogPricingTier` parâmetro ou passar um valor vazio para o mesmo. Caso contrário, não pode omitir e deve passar um valor válido da lista de níveis de preços suportados para a Base de Dados Azure SQL. Para mais informações, consulte os limites de recursos da Base de [Dados SQL](../sql-database/sql-database-resource-limits.md).

Se utilizar a autenticação Azure AD com a identidade gerida para a sua fábrica de dados ligar ao servidor de base de dados, pode omitir o `CatalogAdminCredential` parâmetro. Mas tem de adicionar a identidade gerida para a sua fábrica de dados num grupo Azure AD com permissões de acesso ao servidor de base de dados. Para mais informações, consulte a [autenticação Enable Azure AD para um IR Azure-SSIS](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir). Caso contrário, não pode omitir e deve passar um objeto válido formado a partir do seu nome de utilizador e palavra-passe para autenticação SQL.

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

Executar os seguintes comandos para iniciar o tempo de execução de integração Azure-SSIS.

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
> Excluindo qualquer tempo de configuração personalizado, este processo deve terminar dentro de 5 minutos. Mas pode levar 20 a 30 minutos para o IR Azure-SSIS se juntar a uma rede virtual.
>
> Se utilizar o SSISDB, o serviço Data Factory ligar-se-á ao seu servidor de base de dados para preparar o SSISDB. Também configura permissões e configurações para a sua rede virtual, se especificado, e junta o seu IR Azure-SSIS à rede virtual.
> 
> Quando for disponibilizaum Pacote de Funcionalidades Azure-SSIS, Access Redistribuable e Azure para SSIS também estão instalados. Estes componentes fornecem conectividade aos ficheiros Excel, ficheiros de Acesso e várias fontes de dados do Azure, além das fontes de dados que os componentes incorporados já suportam. Para obter informações sobre outros componentes que possa instalar, consulte a [configuração personalizada para um IR Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="full-script"></a>Script completo

Aqui está o roteiro completo que cria um tempo de funcionação de integração Azure-SSIS.

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
$ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO|KingswaySoft.IntegrationToolkit|KingswaySoft.ProductivityPack|Theobald.XtractIS or leave it empty]" # OPTIONAL to configure an express custom setup without script
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use SQL Database with IP firewall rules/virtual network service endpoints or SQL Managed Instance with private endpoint to host SSISDB, or if you require access to on-premises data without configuring a self-hosted IR. We recommend an Azure Resource Manager virtual network, because classic virtual networks will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for SQL Database with virtual network service endpoints, or a different subnet from the one used for SQL Managed Instance with a private endpoint
# Public IP address info: OPTIONAL to provide two standard static public IP addresses with DNS name under the same subscription and in the same region as your virtual network
$FirstPublicIP = "[your first public IP address resource ID or leave it empty]"
$SecondPublicIP = "[your second public IP address resource ID or leave it empty]"

### SSISDB info
$SSISDBServerEndpoint = "[your logical server name.database.windows.net or managed instance name.DNS prefix.database.windows.net or managed instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, ensure that there's no existing SSISDB on your database server, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure AD
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for Azure AD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for Azure AD authentication]"
# For the basic pricing tier, specify "Basic," not "B." For standard, premium, and elastic pool tiers, specify "S0," "S1," "S2," "S3," etc. See https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server.
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for SQL Database or leave it empty for SQL Managed Instance]"

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
                Write-Warning "Cannot connect, exception: $_";
                Write-Warning "Please make sure the server or instance you specified has already been created. Do you want to proceed? [Y/N]"
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

## <a name="use-an-azure-resource-manager-template-to-create-an-integration-runtime"></a>Use um modelo de Gestor de Recursos Azure para criar um tempo de execução de integração

Nesta secção, você usa um modelo de Gestor de Recursos Azure para criar o tempo de execução de integração Azure-SSIS. Aqui está uma amostra de passagem:

1. Crie um ficheiro JSON com o seguinte modelo de Gestor de Recursos Azure. Substitua os valores nos suportes angulares (espaços reservados) por valores próprios.

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
                                "catalogServerEndpoint": "<server name>.database.windows.net",
                                "catalogAdminUserName": "<server admin username>",
                                "catalogAdminPassword": {
                                    "type": "SecureString",
                                    "value": "<server admin password>"
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

2. Para implementar o modelo de Gestor de Recursos Azure, execute o `New-AzResourceGroupDeployment` comando como mostrado no exemplo seguinte. No exemplo, `ADFTutorialResourceGroup` é o nome do seu grupo de recursos. `ADFTutorialARM.json`é o ficheiro que contém a definição JSON para a sua fábrica de dados e o IR Azure-SSIS.

    ```powershell
    New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json
    ```

    Este comando cria a sua fábrica de dados e o IR Azure-SSIS, mas não inicia o IR.

3. Para iniciar o seu IR Azure-SSIS, execute o `Start-AzDataFactoryV2IntegrationRuntime` comando:

    ```powershell
    Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "<Resource Group Name>" `
        -DataFactoryName "<Data Factory Name>" `
        -Name "<Azure SSIS IR Name>" `
        -Force
    ```

> [!NOTE]
> Excluindo qualquer tempo de configuração personalizado, este processo deve terminar dentro de 5 minutos. Mas pode levar 20 a 30 minutos para o IR Azure-SSIS se juntar a uma rede virtual.
>
> Se utilizar o SSISDB, o serviço Data Factory ligar-se-á ao seu servidor de base de dados para preparar o SSISDB. Também configura permissões e configurações para a sua rede virtual, se especificado, e junta o seu IR Azure-SSIS à rede virtual.
> 
> Quando for disponibilizaum Pacote de Funcionalidades Azure-SSIS, Access Redistribuable e Azure para SSIS também estão instalados. Estes componentes fornecem conectividade aos ficheiros Excel, ficheiros de Acesso e várias fontes de dados do Azure, além das fontes de dados que os componentes incorporados já suportam. Para obter informações sobre outros componentes que possa instalar, consulte a [configuração personalizada para um IR Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="deploy-ssis-packages"></a>Implementar pacotes de SSIS

Se utilizar as ferramentas SSISDB(SSDT) ou SQL Server Management Studio (SSMS). Estas ferramentas conectam-se ao seu servidor de base de dados através do seu ponto final do servidor: 

- Para a Base de Dados SQL, o formato final do servidor é `<server name>.database.windows.net` .
- Para a Instância Gerida sQL com ponto final privado, o formato final do servidor é `<server name>.<dns prefix>.database.windows.net` .
- Para a Instância Gerida sQL com ponto final público, o formato final do servidor é `<server name>.public.<dns prefix>.database.windows.net,3342` . 

Se não utilizar o SSISDB, pode implementar os seus pacotes em sistemas de ficheiros, partilhas de ficheiros ou Ficheiros Azure e executá-los no seu IR Azure-SSIS utilizando as ferramentas de linha de comando e linhas `dtinstall` `dtutil` de `dtexec` comando. Para mais informações, consulte a implementação de [pacotes SSIS](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server). 

Em ambos os casos, também pode executar os seus pacotes implantados no Ir Azure-SSIS utilizando a atividade do Pacote Execute SSIS em pipelines data Factory. Para mais informações, consulte a execução do [pacote Invoke SSIS como uma atividade](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)de fábrica de dados de primeira classe.

## <a name="next-steps"></a>Próximos passos

Consulte outros tópicos do IR Azure-SSIS nesta documentação:

- Tempo de execução de [integração Azure-SSIS.](concepts-integration-runtime.md#azure-ssis-integration-runtime) Este artigo fornece informações sobre os tempos de integração em geral, incluindo o IR Azure-SSIS.
- [Monitor an Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime) (Monitorizar um IR Azure-SSIS) Este artigo mostra-lhe como recuperar e compreender informações sobre o seu IR Azure-SSIS.
- [Manage an Azure-SSIS IR](manage-azure-ssis-integration-runtime.md) (Gerir um IR Azure-SSIS). Este artigo mostra-lhe como parar, começar ou apagar o seu IR Azure-SSIS. Também mostra como escalar o seu IR Azure-SSIS adicionando mais nós.
- [Implementar, executar e monitorizar os pacotes SSIS em Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Ligue-se ao SSISDB em Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Connect to on-premises data sources with Windows authentication (Ligar a origens de dados no local com a autenticação do Windows)](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 
- [Agendar execuções de pacotes em Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)