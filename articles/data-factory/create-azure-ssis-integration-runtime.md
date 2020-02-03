---
title: Criar um tempo de execução de integração do Azure-SSIS no Azure Data Factory
description: Saiba como criar um tempo de execução de integração do Azure-SSIS no Azure Data Factory para que você possa implantar e executar pacotes do SSIS no Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/01/2020
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.openlocfilehash: 4146191453e7c7a589dacc41345a36a29a540de5
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964355"
---
# <a name="create-an-azure-ssis-integration-runtime-in-azure-data-factory"></a>Criar um tempo de execução de integração do Azure-SSIS no Azure Data Factory

Este artigo fornece etapas para o provisionamento de um IR (tempo de execução de integração) do SSIS (Azure-SQL Server Integration Services) no Azure Data Factory. Um Azure-SSIS IR dá suporte a:

- Executando pacotes implantados no catálogo do SSIS (SSISDB) hospedado por um servidor de banco de dados SQL do Azure ou uma instância gerenciada (modelo de implantação de projeto).
- Executando pacotes implantados em sistemas de arquivos, compartilhamentos de arquivos ou arquivos do Azure (modelo de implantação de pacote). 

Depois que um Azure-SSIS IR for provisionado, você poderá usar ferramentas familiares para implantar e executar seus pacotes no Azure. Essas ferramentas incluem SQL Server Data Tools (SSDT), SQL Server Management Studio (SSMS) e ferramentas de linha de comando como `dtinstall`, `dtutil`e `dtexec`.

O tutorial de [Azure-SSIS ir de provisionamento](tutorial-create-azure-ssis-runtime-portal.md) mostra como criar um Azure-SSIS ir por meio do portal do Azure ou do aplicativo Data Factory. O tutorial também mostra como usar opcionalmente um servidor de banco de dados SQL do Azure ou uma instância gerenciada para hospedar o SSISDB. Este artigo expande o tutorial e descreve como executar essas tarefas opcionais:

- Use um servidor de banco de dados SQL do Azure com regras de firewall de IP/pontos de extremidade de serviço de rede virtual ou uma instância gerenciada com um EndPoint privado para hospedar o SSISDB. Como pré-requisito, você precisa configurar as permissões e as configurações de rede virtual para seu Azure-SSIS IR para ingressar em uma rede virtual.

- Use a autenticação do Azure Active Directory (AD do Azure) com a identidade gerenciada para seu data factory para se conectar a um servidor de banco de dados SQL do Azure ou a uma instância gerenciada. Como pré-requisito, você precisa adicionar a identidade gerenciada para seu data factory como um usuário de banco de dados que pode criar uma instância do SSISDB.

- Ingresse seu Azure-SSIS IR em uma rede virtual ou configure um IR auto-hospedado como proxy para o Azure-SSIS IR acessar dados no local.

Este artigo mostra como provisionar um Azure-SSIS IR usando o portal do Azure, Azure PowerShell e um modelo de Azure Resource Manager.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Subscrição do Azure**. Se você ainda não tiver uma assinatura, poderá criar uma conta de [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/) .

- **Servidor de banco de dados SQL do Azure ou instância gerenciada (opcional)** . Se ainda não tiver um servidor de base de dados, crie um no portal do Azure antes de começar. O Data Factory, por sua vez, criará uma instância SSISDB neste servidor de banco de dados. 

  Recomendamos que crie o servidor de base de dados na mesma região do Azure que o integration runtime. Essa configuração permite que o Integration Runtime grave logs de execução em SSISDB sem cruzar regiões do Azure.

  Tenha esses pontos em mente:

  - Com base no servidor de banco de dados selecionado, a instância SSISDB pode ser criada em seu nome como um banco de dados individual, como parte de um pool elástico ou em uma instância gerenciada. Ele pode ser acessível em uma rede pública ou ingressando em uma rede virtual. Para obter orientação sobre como escolher o tipo de servidor de banco de dados para hospedar o SSISDB, consulte a seção comparar um banco de dados [SQL do Azure, um pool elástico e uma instância gerenciada](#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance) neste artigo. 
  
    Se você usar um servidor de banco de dados SQL do Azure com regras de firewall de IP/pontos de extremidade de serviço de rede virtual ou uma instância gerenciada com o Endpoint privado para hospedar o SSISDB ou se precisar de acesso a dados locais sem configurar um IR auto hospedado, será necessário ingressar seu Azure-SSIS IR em uma rede virtual. Para obter mais informações, consulte [unir um Azure-SSIS ir a uma rede virtual](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

  - Confirme que a definição **Permitir acesso aos serviços do Azure** está ativada para o servidor da base de dados. Essa configuração não é aplicável quando você usa um servidor de banco de dados SQL do Azure com regras de firewall de IP/pontos de extremidade de serviço de rede virtual ou uma instância gerenciada com ponto de extremidade privado para hospedar o SSISDB. Para obter mais informações, veja [Proteger a base de dados SQL do Azure](../sql-database/sql-database-security-tutorial.md#create-firewall-rules). Para habilitar essa configuração usando o PowerShell, consulte [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule).

  - Adicione o endereço IP do computador cliente ou um intervalo de endereços IP que inclui o endereço IP do computador cliente à lista de endereços IP do cliente nas configurações de firewall do servidor de banco de dados. Para obter mais informações, veja [Regras de firewall ao nível do servidor da Base de Dados SQL do Azure e ao nível da base de dados](../sql-database/sql-database-firewall-configure.md).

  - Você pode se conectar ao servidor de banco de dados usando a autenticação do SQL com suas credenciais de administrador do servidor ou usando a autenticação do Azure AD com a identidade gerenciada para seu data factory. Para o último, você precisa adicionar a identidade gerenciada para seu data factory em um grupo do Azure AD com permissões de acesso ao servidor de banco de dados. Para obter mais informações, consulte [habilitar a autenticação do Azure ad para um Azure-SSIS ir](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir).

  - Confirme se o servidor de banco de dados ainda não tem uma instância SSISDB. O provisionamento de um Azure-SSIS IR não dá suporte ao uso de uma instância SSISDB existente.

- **Azure Resource Manager rede virtual (opcional)** . Você deve ter uma rede virtual Azure Resource Manager se pelo menos uma das seguintes condições for verdadeira:
  - Você está hospedando o SSISDB em um servidor de banco de dados SQL do Azure com regras de firewall de IP/pontos de extremidade de serviço de rede virtual ou uma instância gerenciada com ponto de extremidade privado.
  - Você deseja se conectar a armazenamentos de dados locais de pacotes do SSIS em execução no seu Azure-SSIS IR sem configurar um IR via hospedagem interna.

- **Azure PowerShell (opcional)** . Siga as instruções em [como instalar e configurar Azure PowerShell](/powershell/azure/install-az-ps), se você quiser executar um script do PowerShell para provisionar seus Azure-SSIS ir.

### <a name="regional-support"></a>Apoio regional

Para obter uma lista de regiões do Azure nas quais Data Factory e um Azure-SSIS IR estão disponíveis, consulte [Data Factory e a disponibilidade de ir do SSIS por região](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all).

### <a name="comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance"></a>Comparação de um banco de dados SQL individual, pool elástico e instância gerenciada

A tabela a seguir compara determinados recursos de um servidor de banco de dados SQL do Azure e uma instância gerenciada, pois eles se relacionam com o IR do Azure-SSIR:

| Funcionalidade | Banco de dados individual/pool elástico| Instância gerida |
|---------|--------------|------------------|
| **Agendamento** | O SQL Server Agent não está disponível.<br/><br/>Consulte [agendar uma execução de pacote em um pipeline de data Factory](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages?view=sql-server-2017#activity).| O agente de Instância Gerenciada está disponível. |
| **Autenticação** | Pode criar uma instância SSISDB com um utilizador de base de dados contido que represente qualquer grupo Azure AD com a identidade gerida da sua fábrica de dados como membro na **função db_owner.**<br/><br/>Consulte [habilitar a autenticação do Azure ad para criar uma instância do SSISDB em um servidor de banco de dados SQL do Azure](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database). | Você pode criar uma instância SSISDB com um usuário de banco de dados independente que representa a identidade gerenciada do seu data factory. <br/><br/>Consulte [habilitar a autenticação do Azure ad para criar uma instância do SSISDB em uma instância gerenciada do banco de dados SQL do Azure](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database-managed-instance). |
| **Camada de serviços** | Ao criar um Azure-SSIS IR com o servidor de banco de dados SQL do Azure, você pode selecionar a camada de serviço para SSISDB. Há várias camadas de serviço. | Quando você cria um Azure-SSIS IR com sua instância gerenciada, não é possível selecionar a camada de serviço para SSISDB. Todos os bancos de dados em sua instância gerenciada compartilham o mesmo recurso alocado para essa instância. |
| **Rede virtual** | Seu Azure-SSIS IR poderá ingressar em uma rede virtual Azure Resource Manager se você usar um servidor de banco de dados SQL do Azure com regras de firewall de IP/pontos de extremidade de serviço de rede virtual. | Seu Azure-SSIS IR poderá ingressar em uma rede virtual Azure Resource Manager se você usar uma instância gerenciada com um ponto de extremidade privado. A rede virtual é necessária quando você não habilita um ponto de extremidade público para sua instância gerenciada.<br/><br/>Se você ingressar seu Azure-SSIS IR para a mesma rede virtual que sua instância gerenciada, verifique se o Azure-SSIS IR está em uma sub-rede diferente da instância gerenciada. Se você ingressar seu Azure-SSIS IR em uma rede virtual diferente de sua instância gerenciada, recomendamos um emparelhamento de rede virtual ou uma conexão de rede para rede. Consulte [conectar seu aplicativo a uma instância gerenciada do banco de dados SQL do Azure](../sql-database/sql-database-managed-instance-connect-app.md). |
| **Transações distribuídas** | Esse recurso é suportado por meio de transações elásticas. Não há suporte para transações do Microsoft Coordenador de Transações Distribuídas (MSDTC). Se seus pacotes SSIS usam o MSDTC para coordenar transações distribuídas, considere migrar para transações elásticas para o banco de dados SQL do Azure. Para obter mais informações, consulte [transações distribuídas entre bancos de dados de nuvem](../sql-database/sql-database-elastic-transactions-overview.md). | Não suportado. |
| | | |

## <a name="use-the-azure-portal-to-create-an-integration-runtime"></a>Usar o portal do Azure para criar um Integration Runtime

Nesta seção, você usa o portal do Azure, especificamente a interface do usuário ou o aplicativo Data Factory, para criar um Azure-SSIS IR.

### <a name="create-a-data-factory"></a>Criar uma fábrica de dados

Para criar seu data factory por meio do portal do Azure, siga as instruções passo a passo em [criar um data Factory por meio da interface do usuário](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory). Selecione **fixar no painel** ao fazer isso, para permitir acesso rápido após sua criação. 

Depois que o data factory for criado, abra sua página de visão geral na portal do Azure. Selecione o bloco **criar & monitor** para abrir a página **vamos começar** em uma guia separada. Lá, você pode continuar a criar seu Azure-SSIS IR.   

### <a name="provision-an-azure-ssis-integration-runtime"></a>Aprovisionar um runtime de integração Azure-SSIS

1. Na página **Introdução**, selecione o mosaico **Configurar Runtime de Integração do SSIS**.

   ![Configurar o mosaico do Integration Runtime do SSIS](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

1. Na seção **configurações gerais** do painel de **instalação do Integration Runtime** , conclua as etapas a seguir.

   ![Definições gerais](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   1. Em **Nome**, introduza o nome do seu runtime de integração.

   1. Em **Descrição**, introduza a descrição do seu runtime de integração.

   1. Em **Localização**, selecione a localização do seu runtime de integração. Apenas são apresentadas as localizações suportadas. Recomendamos que selecione a mesma localização do seu servidor de base de dados para alojar a SSISDB.

   1. Para **tamanho do nó**, selecione o tamanho do nó em seu cluster do Integration Runtime. Apenas são apresentados os tamanhos de nó suportados. Selecione um tamanho de nó grande (escalar verticalmente) se desejar executar muitos pacotes de computação intensiva ou de uso intensivo de memória.

   1. Em **Numero de Nós**, selecione o número de nós do cluster do runtime de integração. Apenas são apresentados os números de nó suportados. Selecione um cluster grande com muitos nós (scale out) se você quiser executar muitos pacotes em paralelo.

   1. Para **edição/licença**, selecione o SQL Server Edition para o tempo de execução de integração: Standard ou Enterprise. Selecione empresa se desejar usar recursos avançados no tempo de execução de integração.

   1. Para **economizar dinheiro**, selecione a opção benefício híbrido do Azure para o tempo de execução de integração: **Sim** ou **não**. Selecione **Sim** se desejar trazer sua própria licença de SQL Server com o Software Assurance para se beneficiar da economia de custos com o uso híbrido.

   1. Selecione **Seguinte**.

1. Na seção **configurações do SQL** , conclua as etapas a seguir.

   ![Definições de SQL](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

   1. Selecione a caixa de seleção **criar catálogo do SSIS (SSISDB) hospedado pelo servidor do banco de dados SQL do Azure/instância gerenciada para armazenar projetos/pacotes/ambientes/logs de execução** para escolher o modelo de implantação para que os pacotes sejam executados no seu Azure-SSIS ir. Você escolherá o modelo de implantação de projeto em que os pacotes são implantados no SSISDB hospedado pelo servidor de banco de dados ou o modelo de implantação de pacote em que os pacotes são implantados em sistemas de arquivos, compartilhamentos de arquivos ou arquivos do Azure. 
    
      Se você marcar a caixa de seleção, precisará colocar seu próprio servidor de banco de dados para hospedar a instância do SSISDB que criaremos e gerenciaremos em seu nome.
   
      1. Em **Subscrição**, selecione a subscrição do Azure que tem o servidor da base de dados para alojar a SSISDB. 

      1. Em **Localização**, selecione a mesma localização do seu servidor de base de dados para alojar a SSISDB. Recomendamos que selecione a mesma localização do seu runtime de integração. 

      1. Em **Ponto Final do Servidor de Base de Dados do Catálogo**, selecione o ponto final do seu servidor de base de dados para alojar a SSISDB. 
    
         Com base no servidor de banco de dados selecionado, a instância SSISDB pode ser criada em seu nome como um banco de dados individual, como parte de um pool elástico ou em uma instância gerenciada. Ele pode ser acessível em uma rede pública ou ingressando em uma rede virtual. Para obter orientação sobre como escolher o tipo de servidor de banco de dados para hospedar o SSISDB, consulte a seção comparar um banco de dados [SQL do Azure, um pool elástico e uma instância gerenciada](#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance) neste artigo. 
    
         Se você selecionar um servidor de banco de dados SQL do Azure com regras de firewall de IP/pontos de extremidade de serviço de rede virtual ou uma instância gerenciada com o Endpoint privado para hospedar o SSISDB ou se precisar de acesso a dados locais sem configurar um IR auto hospedado, será necessário ingressar seu Azure-SSIS IR em uma rede virtual. Para obter mais informações, consulte [unir um Azure-SSIS ir a uma rede virtual](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). 

      1. Marque a caixa de seleção **usar a autenticação do AAD com a identidade gerenciada para o ADF** para escolher o método de autenticação para o servidor de banco de dados para hospedar o SSISDB. Você escolherá a autenticação do SQL ou a autenticação do Azure AD com a identidade gerenciada para seu data factory. 
    
         Se você marcar a caixa de seleção, precisará adicionar a identidade gerenciada para seu data factory em um grupo do Azure AD com permissões de acesso ao seu servidor de banco de dados. Para obter mais informações, consulte [habilitar a autenticação do Azure ad para um Azure-SSIS ir](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir). 

      1. Para **nome de usuário do administrador**, insira o nome de usuário de autenticação do SQL do servidor de banco de dados para hospedar o SSISDB. 

      1. Para **senha de administrador**, insira a senha de autenticação do SQL para o servidor de banco de dados para hospedar o SSISDB. 

      1. Para **camada de serviço do banco de dados do catálogo**, selecione a camada de serviço do servidor de banco de dados para hospedar o SSISDB. Selecione a camada básica, Standard ou Premium ou selecione um nome de pool elástico. 

      1. Selecione **Ligação de teste**. Se o teste for bem-sucedido, selecione **Avançar**. 

1. Na seção **Configurações avançadas** , conclua as etapas a seguir.

   ![Definições avançadas](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   1. Para **execuções paralelas máximas por nó**, selecione o número máximo de pacotes a serem executados simultaneamente por nó em seu cluster de tempo de execução de integração. Apenas são apresentados os números de pacotes suportados. Selecione um número baixo se desejar usar mais de um núcleo para executar um único pacote grande que seja com uso intensivo de computação ou de memória. Selecione um número alto se desejar executar um ou mais pacotes pequenos em um único núcleo.

   1. Marque a caixa de seleção **personalizar seu Azure-SSIS Integration Runtime com configurações de sistema/instalações de componentes adicionais** para escolher se deseja adicionar configurações personalizadas padrão/Express no seu Azure-SSIS ir. Para obter mais informações, consulte [instalação personalizada para um Azure-SSIS ir](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup).

      Se você marcar a caixa de seleção, conclua as etapas a seguir.

      ![Configurações avançadas com instalações personalizadas](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)
   
      1. Para o **URI de SAS do contêiner de instalação personalizada**, insira o URI de SAS do seu contêiner em que você armazena scripts e arquivos associados para configurações personalizadas padrão.

      1. Para **a instalação personalizada expressa**, selecione **novo** para abrir o painel **Adicionar configuração personalizada expressa** e, em seguida, selecione qualquer tipo no menu suspenso **tipo de instalação personalizada expressa** , por exemplo, **Execute o comando cmdkey**, **adicione a variável de ambiente**, **Instale o componente licenciado**, etc.

         Se você selecionar o tipo de **componente de instalação licenciada** , poderá selecionar todos os componentes integrados de nossos parceiros ISV no menu suspenso **nome do componente** e, se necessário, inserir a chave de licença do produto que você comprou a partir deles no campo chave de **licença** .
  
         Suas configurações personalizadas expressas adicionadas aparecerão na seção **Advanced Settings** . Para removê-los, você pode selecionar suas caixas de seleção e, em seguida, selecionar **excluir**.

   1. Selecione a **opção Selecionar uma VNet para sua Azure-SSIS Integration Runtime para ingressar, permitir que o ADF crie determinados recursos de rede e, opcionalmente, traga seus próprios endereços IP públicos estáticos** para escolher se deseja unir seu tempo de execução de integração a uma rede virtual. 

      Selecione-o se você usar um servidor de banco de dados SQL do Azure com as regras de firewall de IP/pontos de extremidade de serviço de rede virtual ou uma instância gerenciada com um EndPoint privado para hospedar o SSISDB ou se precisar de acesso a dados locais (ou seja, se você tiver fontes de dados locais ou destinos em seus pacotes SSIS) sem configurar um IR via hospedagem interna. Para obter mais informações, consulte [unir Azure-SSIS ir a uma rede virtual](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). 

      Se você marcar a caixa de seleção, conclua as etapas a seguir.

      ![Definições avançadas com uma rede virtual](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

      1. Para **assinatura**, selecione a assinatura do Azure que tem sua rede virtual.

      1. Para **local**, o mesmo local do tempo de execução de integração é selecionado.

      1. Para **tipo**, selecione o tipo de sua rede virtual: clássica ou Azure Resource Manager. Recomendamos que você selecione um Azure Resource Manager rede virtual, pois as redes virtuais clássicas serão preteridas em breve.

      1. Para **nome da VNet**, selecione o nome da sua rede virtual. Ele deve ser o mesmo usado para o servidor de banco de dados SQL do Azure com pontos de extremidade de serviço de rede virtual ou instância gerenciada com ponto de extremidades privado para hospedar o SSISDB. Ou deve ser o mesmo conectado à sua rede local. Caso contrário, pode ser qualquer rede virtual para trazer seus próprios endereços IP públicos estáticos para Azure-SSIS IR.

      1. Para **nome da sub-rede**, selecione o nome da sub-rede para sua rede virtual. Ele deve ser o mesmo usado para o servidor de banco de dados SQL do Azure com pontos de extremidade de serviço de rede virtual para hospedar o SSISDB. Ou deve ser uma sub-rede diferente da usada para sua instância gerenciada com ponto de extremidade privado para hospedar o SSISDB. Caso contrário, pode ser qualquer sub-rede para trazer seus próprios endereços IP públicos estáticos para Azure-SSIS IR.

      1. Marque a caixa de seleção **colocar endereços IP públicos estáticos para seu Azure-SSIS Integration Runtime** para escolher se deseja trazer seus próprios endereços IP públicos estáticos para Azure-SSIS ir, para que você possa permiti-los no firewall para suas fontes de dados.

         Se você marcar a caixa de seleção, conclua as etapas a seguir.

         1. Para o **primeiro endereço IP público estático**, selecione o primeiro endereço IP público estático que atende aos requisitos para seu Azure-SSIS ir. Se você não tiver nenhum, clique em **criar novo** link para criar endereços IP públicos estáticos em portal do Azure e, em seguida, clique no botão atualizar aqui, para que você possa selecioná-los.
      
         1. Para o **segundo endereço IP público estático**, selecione o segundo endereço IP público estático que atende aos requisitos para seu Azure-SSIS ir. Se você não tiver nenhum, clique em **criar novo** link para criar endereços IP públicos estáticos em portal do Azure e, em seguida, clique no botão atualizar aqui, para que você possa selecioná-los.

   1. Marque a caixa de seleção **configurar Integration Runtime autohospedado como um proxy para sua Azure-SSIS Integration Runtime** para escolher se deseja configurar um ir auto-hospedado como proxy para o Azure-SSIS ir. Para obter mais informações, consulte [configurar um ir auto-hospedado como proxy](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis). 

      Se você marcar a caixa de seleção, conclua as etapas a seguir.

      ![Configurações avançadas com um IR auto-hospedado](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

      1. Para **Integration Runtime auto-hospedados**, selecione o ir auto-hospedado existente como um proxy para Azure-SSIS ir.

      1. Para o **serviço vinculado de armazenamento de preparo**, selecione o serviço vinculado do armazenamento de BLOBs do Azure existente ou crie um novo para preparo.

      1. Para **caminho de preparo**, especifique um contêiner de BLOB em sua conta de armazenamento de BLOBs do Azure selecionada ou deixe em branco para usar um padrão para preparo.

   1. Selecione **validação de VNet** > **continuar**. 

1. Na seção **Resumo** , examine todas as configurações de provisionamento, marque os links de documentação recomendados e selecione **concluir** para iniciar a criação do tempo de execução de integração.

   > [!NOTE]
   > Excluindo qualquer hora de instalação personalizada, esse processo deve ser concluído em 5 minutos. Mas pode levar de 20-30 minutos para que o Azure-SSIS IR ingresse em uma rede virtual.
   >
   > Se você usar o SSISDB, o serviço de Data Factory se conectará ao servidor de banco de dados para preparar o SSISDB. Ele também configura permissões e configurações para sua rede virtual, se especificado, e une seu Azure-SSIS IR à rede virtual.
   > 
   > Quando você provisiona um Azure-SSIS IR, o Access redistribuível e o Azure Feature Pack para SSIS também são instalados. Esses componentes fornecem conectividade com arquivos do Excel, arquivos de acesso e várias fontes de dados do Azure, além das fontes de dados que os componentes internos já suportam. Para obter informações sobre outros componentes que você pode instalar, consulte [instalação personalizada para um Azure-SSIS ir](how-to-configure-azure-ssis-ir-custom-setup.md).

1. No separador **Ligações**, mude para **Runtimes de Integração**, se for necessário. Selecione **Atualizar** para atualizar o estado.

   ![Estado da criação](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

1. Utilize as ligações na coluna **Ações** para iniciar/parar, editar ou eliminar o runtime de integração. Utilize a última ligação para ver o código JSON para o runtime de integração. Os botões de editar e eliminar estão ativados, apenas quando a resposta a incidentes está parada.

   ![Ações de IR do Azure SSIS](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)

### <a name="azure-ssis-integration-runtimes-in-the-portal"></a>Runtime de integração de SSIS do Azure no portal

1. Na interface do usuário do Azure Data Factory, alterne para a guia **Editar** e selecione **conexões**. Em seguida, alterne para a guia **tempos de execução de integração** para exibir os tempos de execução de integração existentes em seu data Factory.

   ![Exibir IRs existente](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

1. Selecione **novo** para criar um novo Azure-SSIS ir.

   ![Runtime de integração através do menu](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

1. No painel de **configuração do Integration Runtime** , selecione os pacotes do SSIS com a comparação entre a **mudança e a tecla Shift existentes para executar no bloco do Azure** e selecione **Avançar**.

   ![Especifique o tipo de runtime de integração](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

1. Para as etapas restantes para configurar um Azure-SSIS IR, consulte a seção [provisionar um tempo de execução de integração do Azure SSIS](#provision-an-azure-ssis-integration-runtime) .

## <a name="use-azure-powershell-to-create-an-integration-runtime"></a>Usar Azure PowerShell para criar um Integration Runtime

Nesta seção, você usará Azure PowerShell para criar um Azure-SSIS IR.

### <a name="create-variables"></a>Criar variáveis

Copie e cole o script a seguir. Especifique valores para as variáveis. 

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
$ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO or leave it empty]" # OPTIONAL to configure an express custom setup without script
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

### <a name="sign-in-and-select-a-subscription"></a>Entrar e selecionar uma assinatura

Adicione o script a seguir para entrar e selecionar sua assinatura do Azure.

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName
```

### <a name="validate-the-connection-to-database-server"></a>Validar a conexão com o servidor de banco de dados

Adicione o script a seguir para validar o servidor do banco de dados SQL do Azure ou a instância gerenciada.

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

Adicione o script a seguir para configurar automaticamente as permissões e as configurações de rede virtual para o tempo de execução de integração do Azure-SSIS ingressar.

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

Crie um [grupo de recursos do Azure](../azure-resource-manager/management/overview.md) usando o comando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) . Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos como um grupo.

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

Execute os comandos a seguir para criar um tempo de execução de integração do Azure-SSIS que executa pacotes do SSIS no Azure.

Se você não usar o SSISDB, poderá omitir os parâmetros `CatalogServerEndpoint`, `CatalogPricingTier`e `CatalogAdminCredential`.

Se você não usar um servidor de banco de dados SQL do Azure com as regras de firewall de IP/pontos de extremidade de serviço de rede virtual ou uma instância gerenciada com um EndPoint privado para hospedar o SSISDB ou precisar de acesso a dados locais, você poderá omitir os parâmetros `VNetId` e `Subnet` ou passar valores vazios para eles. Você também pode omiti-los se configurar um IR auto-hospedado como proxy para seu Azure-SSIS IR acessar dados no local. Caso contrário, você não pode omiti-los e deve passar valores válidos de sua configuração de rede virtual. Para obter mais informações, consulte [unir um Azure-SSIS ir a uma rede virtual](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

Se você usar a instância gerenciada para hospedar o SSISDB, poderá omitir o parâmetro `CatalogPricingTier` ou passar um valor vazio para ele. Caso contrário, você não pode omiti-lo e deve passar um valor válido da lista de tipos de preço com suporte para o banco de dados SQL do Azure. Para obter mais informações, consulte [limites de recursos do banco de dados SQL](../sql-database/sql-database-resource-limits.md).

Se você usar a autenticação do Azure AD com a identidade gerenciada para seu data factory para se conectar ao servidor de banco de dados, poderá omitir o parâmetro `CatalogAdminCredential`. Mas você deve adicionar a identidade gerenciada para seu data factory em um grupo do Azure AD com permissões de acesso ao servidor de banco de dados. Para obter mais informações, consulte [habilitar a autenticação do Azure ad para um Azure-SSIS ir](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir). Caso contrário, você não pode omiti-lo e deve passar um objeto válido formado por seu nome de usuário do administrador do servidor e senha para autenticação do SQL.

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

### <a name="start-the-integration-runtime"></a>Iniciar o Integration Runtime

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
> Excluindo qualquer hora de instalação personalizada, esse processo deve ser concluído em 5 minutos. Mas pode levar de 20-30 minutos para que o Azure-SSIS IR ingresse em uma rede virtual.
>
> Se você usar o SSISDB, o serviço de Data Factory se conectará ao servidor de banco de dados para preparar o SSISDB. Ele também configura permissões e configurações para sua rede virtual, se especificado, e une seu Azure-SSIS IR à rede virtual.
> 
> Quando você provisiona um Azure-SSIS IR, o Access redistribuível e o Azure Feature Pack para SSIS também são instalados. Esses componentes fornecem conectividade com arquivos do Excel, arquivos de acesso e várias fontes de dados do Azure, além das fontes de dados que os componentes internos já suportam. Para obter informações sobre outros componentes que você pode instalar, consulte [instalação personalizada para um Azure-SSIS ir](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="full-script"></a>Script completo

Este é o script completo que cria um tempo de execução de integração do Azure-SSIS.

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
$ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO or leave it empty]" # OPTIONAL to configure an express custom setup without script
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

## <a name="use-an-azure-resource-manager-template-to-create-an-integration-runtime"></a>Usar um modelo de Azure Resource Manager para criar um tempo de execução de integração

Nesta seção, você usa um modelo de Azure Resource Manager para criar o tempo de execução de integração do Azure-SSIS. Veja um exemplo de explicação:

1. Crie um arquivo JSON com o seguinte modelo de Azure Resource Manager. Substitua os valores nos colchetes angulares (espaços reservados) pelos seus próprios valores.

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

2. Para implantar o modelo de Azure Resource Manager, execute o comando `New-AzResourceGroupDeployment`, conforme mostrado no exemplo a seguir. No exemplo, `ADFTutorialResourceGroup` é o nome do seu grupo de recursos. `ADFTutorialARM.json` é o arquivo que contém a definição de JSON para seu data factory e o Azure-SSIS IR.

    ```powershell
    New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json
    ```

    Esse comando cria o data factory e Azure-SSIS IR nele, mas não inicia o IR.

3. Para iniciar o Azure-SSIS IR, execute o comando `Start-AzDataFactoryV2IntegrationRuntime`:

    ```powershell
    Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "<Resource Group Name>" `
        -DataFactoryName "<Data Factory Name>" `
        -Name "<Azure SSIS IR Name>" `
        -Force
    ```

> [!NOTE]
> Excluindo qualquer hora de instalação personalizada, esse processo deve ser concluído em 5 minutos. Mas pode levar de 20-30 minutos para que o Azure-SSIS IR ingresse em uma rede virtual.
>
> Se você usar o SSISDB, o serviço de Data Factory se conectará ao servidor de banco de dados para preparar o SSISDB. Ele também configura permissões e configurações para sua rede virtual, se especificado, e une seu Azure-SSIS IR à rede virtual.
> 
> Quando você provisiona um Azure-SSIS IR, o Access redistribuível e o Azure Feature Pack para SSIS também são instalados. Esses componentes fornecem conectividade com arquivos do Excel, arquivos de acesso e várias fontes de dados do Azure, além das fontes de dados que os componentes internos já suportam. Para obter informações sobre outros componentes que você pode instalar, consulte [instalação personalizada para um Azure-SSIS ir](how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="deploy-ssis-packages"></a>Implementar pacotes de SSIS

Se você usar o SSISDB, poderá implantar seus pacotes nele e executá-los em seu Azure-SSIS IR usando as ferramentas SQL Server Data Tools (SSDT) ou SQL Server Management Studio (SSMS). Essas ferramentas se conectam ao servidor de banco de dados por meio do ponto de extremidade do servidor: 

- Para um servidor de banco de dados SQL do Azure, o formato de ponto de extremidade do servidor é `<server name>.database.windows.net`.
- Para uma instância gerenciada com ponto de extremidade privado, o formato de ponto de extremidade do servidor é `<server name>.<dns prefix>.database.windows.net`.
- Para uma instância gerenciada com um ponto de extremidade público, o formato de ponto de extremidade do servidor é `<server name>.public.<dns prefix>.database.windows.net,3342`. 

Se você não usar o SSISDB, poderá implantar seus pacotes em sistemas de arquivos, compartilhamentos de arquivos ou arquivos do Azure e executá-los em seu Azure-SSIS IR usando as ferramentas de linha de comando `dtinstall`, `dtutil`e `dtexec`. Para obter mais informações, consulte [implantar pacotes do SSIS](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server). 

Em ambos os casos, você também pode executar seus pacotes implantados em Azure-SSIS IR usando a atividade executar pacote SSIS em pipelines Data Factory. Para obter mais informações, consulte [invocar a execução do pacote SSIS como uma atividade de data Factory de primeira classe](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).

## <a name="next-steps"></a>Passos seguintes

Consulte outros tópicos Azure-SSIS IR nesta documentação:

- [Tempo de execução de integração do Azure-SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime). Este artigo fornece informações sobre os tempos de execução de integração em geral, incluindo Azure-SSIS IR.
- [Monitor an Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime) (Monitorizar um IR Azure-SSIS) Este artigo mostra como recuperar e compreender as informações sobre seu Azure-SSIS IR.
- [Manage an Azure-SSIS IR](manage-azure-ssis-integration-runtime.md) (Gerir um IR Azure-SSIS). Este artigo mostra como parar, iniciar ou excluir seu Azure-SSIS IR. Ele também mostra como escalar horizontalmente sua Azure-SSIS IR adicionando mais nós.
- [Associar um IR Azure-SSIS a uma rede virtual](join-azure-ssis-integration-runtime-virtual-network.md). Este artigo fornece informações sobre como unir seu Azure-SSIS IR a uma rede virtual.