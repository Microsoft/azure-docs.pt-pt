---
title: Migrar dados para a conta API Da Azure Cosmos DB SQL usando a Striim
description: Aprenda a usar o Striim para migrar dados de uma base de dados da Oracle para uma conta API Azure Cosmos DB SQL.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: 36ba9e2d3385184f32876a6d067b58f7c21a90bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "71003277"
---
# <a name="migrate-data-to-azure-cosmos-db-sql-api-account-using-striim"></a>Migrar dados para a conta API Da Azure Cosmos DB SQL usando a Striim
 
A imagem Striim no mercado Azure oferece movimento contínuo de dados em tempo real de armazéns de dados e bases de dados para Azure. Ao mover os dados, pode realizar desnormalização em linha, transformação de dados, permitir análises em tempo real e cenários de reporte de dados. É fácil começar com a Striim para mover continuamente os dados da empresa para a API Db SQL da Azure Cosmos. A Azure oferece uma oferta de marketplace que facilita a implementação de dados da Striim e migração para a Azure Cosmos DB. 

Este artigo mostra como usar o Striim para migrar dados de uma base de **dados da Oracle** para uma conta **API Azure Cosmos DB SQL**.

## <a name="prerequisites"></a>Pré-requisitos

* Se não tiver uma [subscrição do Azure](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.

* Uma base de dados da Oracle a funcionar no local com alguns dados.

## <a name="deploy-the-striim-marketplace-solution"></a>Implementar a solução de mercado Striim

1. Assine no [portal Azure.](https://portal.azure.com/)

1. Selecione **Criar um recurso** e procurar **striim** no mercado Azure. Selecione a primeira opção e **Crie**.

   ![Encontre o item do mercado Striim](./media/cosmosdb-sql-api-migrate-data-striim/striim-azure-marketplace.png)

1. Em seguida, introduza as propriedades de configuração da instância Striim. O ambiente Striim é implantado numa máquina virtual. A partir do painel **Basics,** introduza o nome de **utilizador VM**, **palavra-passe VM** (esta palavra-passe é utilizada para SSH no VM). Selecione a sua **Subscrição,** **Grupo de Recursos**e Detalhes de **Localização** onde deseja implementar o Striim. Uma vez concluído, selecione **OK**.

   ![Configure as definições básicas para Striim](./media/cosmosdb-sql-api-migrate-data-striim/striim-configure-basic-settings.png)

1. No painel de definições do **Cluster Striim,** escolha o tipo de implantação striim e o tamanho da máquina virtual.

   |Definição | Valor | Descrição |
   | ---| ---| ---|
   |Tipo de implantação striim |Autónomo | O Striim pode funcionar em tipos de implantação **autónomo** ou **cluster.** O modo autónomo irá implantar o servidor Striim numa única máquina virtual e pode selecionar o tamanho dos VMs dependendo do volume de dados. O modo cluster irá implantar o servidor Striim em dois ou mais VMs com o tamanho selecionado. Ambientes de cluster com mais de 2 nós oferecem alta disponibilidade automática e falha.</br></br> Neste tutorial, pode selecionar a opção Autónoma. Utilize o VM de tamanho "Standard_F4s" predefinido.  | 
   | Nome do aglomerado Striim|    <Striim_cluster_Name>|  Nome do aglomerado Striim.|
   | Senha de cluster Striim|   <Striim_cluster_password>|  Senha para o cluster.|

   Depois de preencher o formulário, selecione **OK** para continuar.

1. No painel de definições de **acesso Striim,** configure o **endereço IP público** (escolha os valores predefinidos), nome de domínio para **Striim,** **palavra-passe de administrador** que gostaria de usar para iniciar sessão no Striim UI. Configure um VNET e subnet (escolha os valores predefinidos). Depois de preencher os detalhes, selecione **OK** para continuar.

   ![Definições de acesso striim](./media/cosmosdb-sql-api-migrate-data-striim/striim-access-settings.png)

1. O Azure validará a implantação e certificar-se-á de que tudo parece bom; validação leva poucos minutos para completar. Depois de concluída a validação, selecione **OK**.
  
1. Por fim, reveja os termos de utilização e selecione **Criar** para criar a sua instância Striim. 

## <a name="configure-the-source-database"></a>Configure a base de dados de origem 

Nesta secção, configura a base de dados Oracle como fonte de movimento de dados.  Vai precisar do piloto da [Oracle JDBC](https://www.oracle.com/technetwork/database/features/jdbc/jdbc-ucp-122-3110062.html) para se ligar à Oracle. Para ler as alterações da sua base de dados da Fonte Oracle, pode utilizar o [LogMiner](https://www.oracle.com/technetwork/database/features/availability/logmineroverview-088844.html) ou os [APIs XStream](https://docs.oracle.com/cd/E11882_01/server.112/e16545/xstrm_intro.htm#XSTRM72647). O condutor da Oracle JDBC deve estar presente no caminho da classe Java da Striim para ler, escrever ou persistir dados da base de dados da Oracle.

Descarregue o condutor [do ojdbc8.jar](https://www.oracle.com/technetwork/database/features/jdbc/jdbc-ucp-122-3110062.html) na sua máquina local. Irá instalá-lo no cluster Striim mais tarde.

## <a name="configure-the-target-database"></a>Configure a base de dados de destino

Nesta secção, irá configurar a conta API Azure Cosmos DB SQL como o alvo para o movimento de dados.

1. Crie uma [conta API Azure Cosmos DB SQL](create-cosmosdb-resources-portal.md) utilizando o portal Azure.

1. Navegue para o painel **do Data Explorer** na sua conta Azure Cosmos. Selecione **Novo Recipiente** para criar um novo recipiente. Assuma que está a migrar *produtos* e *encomenda* dados da base de dados da Oracle para o Azure Cosmos DB. Crie uma nova base de dados chamada **StriimDemo** com um recipiente chamado **Encomendas**. Forme o recipiente com **1000 RUs** (este exemplo utiliza 1000 RUs, mas deve utilizar a entrada estimada para a sua carga de trabalho) e **/ORDER_ID** como chave de partição. Estes valores diferirão consoante os seus dados de origem. 

   ![Criar uma conta da API de SQL](./media/cosmosdb-sql-api-migrate-data-striim/create-sql-api-account.png)

## <a name="configure-oracle-to-azure-cosmos-db-data-flow"></a>Configure o fluxo de dados da Oracle para azure Cosmos DB

1. Agora, vamos voltar para o Striim. Antes de interagir com a Striim, instale o controlador Oracle JDBC que descarregou anteriormente.

1. Navegue até à instância Striim que implantou no portal Azure. Selecione o botão **Ligar** na barra de menu superior e a partir do separador **SSH,** copie o URL em Login utilizando o campo de **conta local VM.**

   ![Obtenha o URL SSH](./media/cosmosdb-sql-api-migrate-data-striim/get-ssh-url.png)

1. Abra uma nova janela terminal e execute o comando SSH que copiou do portal Azure. Este artigo utiliza terminal num MacOS, pode seguir as instruções semelhantes utilizando putty ou um cliente SSH diferente numa máquina Windows. Quando solicitado, escreva **sim** para continuar e introduza a **palavra-passe** que definiu para a máquina virtual no passo anterior.

   ![Ligue-se ao Striim VM](./media/cosmosdb-sql-api-migrate-data-striim/striim-vm-connect.png)

1. Agora, abra um novo separador de terminal para copiar o ficheiro **ojdbc8.jar** que descarregou anteriormente. Utilize o seguinte comando SCP para copiar o ficheiro do frasco da sua máquina local para a pasta TMP da instância Striim em funcionamento em Azure:

   ```bash
   cd <Directory_path_where_the_Jar_file_exists> 
   scp ojdbc8.jar striimdemo@striimdemo.westus.cloudapp.azure.com:/tmp
   ```

   ![Copie o ficheiro Jar da máquina de localização para Striim](./media/cosmosdb-sql-api-migrate-data-striim/copy-jar-file.png)

1. Em seguida, navegue de volta para a janela onde fez SSH para a instância Striim e Login como sudo. Mova o ficheiro **ojdbc8.jar** do diretório **/tmp** para o diretório **lib** da sua instância Striim com os seguintes comandos:

   ```bash
   sudo su
   cd /tmp
   mv ojdbc8.jar /opt/striim/lib
   chmod +x ojdbc8.jar
   ```

   ![Mova o ficheiro Jar para a pasta lib](./media/cosmosdb-sql-api-migrate-data-striim/move-jar-file.png)


1. A partir da mesma janela terminal, reinicie o servidor Striim executando os seguintes comandos:

   ```bash
   Systemctl stop striim-node
   Systemctl stop striim-dbms
   Systemctl start striim-dbms
   Systemctl start striim-node
   ```
 
1. Striim vai demorar um minuto a começar. Se quiser ver o estado, faça o seguinte comando: 

   ```bash
   tail -f /opt/striim/logs/striim-node.log
   ```

1. Agora, navegue de volta para Azure e copie o endereço IP público do seu Striim VM. 

   ![Copiar endereço IP Striim VM](./media/cosmosdb-sql-api-migrate-data-striim/copy-public-ip-address.png)

1. Para navegar para a Web UI do Striim, abra um novo separador num browser e copie o IP público seguido por: 9080. Faça o indicação utilizando o nome de utilizador **do administrador,** juntamente com a senha de administração especificada no portal Azure.

   ![Inscreva-se no Striim](./media/cosmosdb-sql-api-migrate-data-striim/striim-login-ui.png)

1. Agora vais chegar à página inicial do Striim. Existem três painéis diferentes – **Dashboards,** **Apps**e **SourcePreview.** O painel dashboards permite-lhe mover dados em tempo real e visualizá-lo. O painel de Apps contém os seus pipelines de dados de streaming ou fluxos de dados. Na mão direita da página está o SourcePreview onde pode pré-visualizar os seus dados antes de os mover.

1. Selecione o painel **apps,** vamos focar-nos neste painel por enquanto. Existem uma variedade de aplicações de amostra que pode usar para aprender sobre o Striim, no entanto neste artigo você vai criar o nosso próprio. Selecione o botão **Adicionar App** no canto superior direito.

   ![Adicione a app Striim](./media/cosmosdb-sql-api-migrate-data-striim/add-striim-app.png)

1. Existem algumas formas diferentes de criar aplicações Striim. Selecione **Iniciar com modelo** para começar com um modelo existente.

   ![Inicie a aplicação com o modelo](./media/cosmosdb-sql-api-migrate-data-striim/start-with-template.png)

1. No campo de **modelos de pesquisa,** escreva "Cosmos" e selecione **Target: Azure Cosmos DB** e, em seguida, selecione **Oracle CDC para Azure Cosmos DB**.

   ![Selecione Oracle CDC para Cosmos DB](./media/cosmosdb-sql-api-migrate-data-striim/oracle-cdc-cosmosdb.png)

1. Na página seguinte, nomeie a sua candidatura. Pode fornecer um nome como **oraToCosmosDB** e, em seguida, selecionar **Guardar**.

1. Em seguida, introduza a configuração de origem da sua fonte oráculo. Introduza um valor para o **Nome Fonte**. O nome de origem é apenas uma convenção de nomeação para a aplicação Striim, você pode usar algo como **src_onPremOracle**. Introduza valores para o resto dos parâmetros de origem **URL**, **Username**, **Password**, escolha **O LogMiner** como o leitor para ler dados da Oracle. Selecione **Seguinte** para continuar.

   ![Configurar parâmetros de origem](./media/cosmosdb-sql-api-migrate-data-striim/configure-source-parameters.png)

1. A Striim verificará o seu ambiente e certificar-se-á de que pode ligar-se à sua fonte, ter os privilégios certos, e que o CDC foi configurado corretamente. Uma vez validados todos os valores, selecione **Seguinte**.

   ![Validar os parâmetros de origem](./media/cosmosdb-sql-api-migrate-data-striim/validate-source-parameters.png)

1. Selecione as tabelas da base de dados Oracle que gostaria de migrar. Por exemplo, vamos escolher a tabela Encomendas e selecionar **Seguinte**. 

   ![Selecione tabelas de origem](./media/cosmosdb-sql-api-migrate-data-striim/select-source-tables.png)

1. Depois de selecionar a tabela de origem, pode fazer operações mais complicadas, tais como mapeamento e filtragem. Neste caso, você apenas vai criar uma réplica da sua mesa de origem em Azure Cosmos DB. Então, selecione **Next** para configurar o alvo

1. Agora, vamos configurar o alvo:

   * **Nome do alvo** - Forneça um nome amigável para o alvo. 
   * **Entrada De** - A partir da lista de dropdown, selecione o fluxo de entrada do que criou na configuração de origem Oracle. 
   * **Coleções**- Introduza as propriedades de configuração do Azure Cosmos DB. A sintaxe de cobranças é **SourceSchema.SourceTable, TargetDatabase.TargetContainer**. Neste exemplo, o valor seria "SYSTEM. ENCOMENDAS, StriimDemo.Orders". 
   * **AccessKey** - A Chave Primária da sua conta Azure Cosmos.
   * **ServiceEndpoint** – O URI da sua conta Azure Cosmos, pode ser encontrado sob a secção **Chaves** do portal Azure. 

   Selecione **Guardar** e **Seguir**.

   ![Configurar parâmetros-alvo](./media/cosmosdb-sql-api-migrate-data-striim/configure-target-parameters.png)


1. Em seguida, você chegará ao designer de fluxo, onde você pode arrastar e largar os conectores da caixa para criar suas aplicações de streaming. Não irá fazer modificações no fluxo neste momento. por isso, vá em frente e implemente a aplicação selecionando o botão implementação da **App.**
 
   ![Implementar a aplicação](./media/cosmosdb-sql-api-migrate-data-striim/deploy-app.png)

1. Na janela de implantação, pode especificar se pretende executar certas partes da sua aplicação em partes específicas da sua topologia de implementação. Já que estamos a fazer uma simples topologia de implantação através do Azure, usaremos a opção padrão.

   ![Use a opção por defeito](./media/cosmosdb-sql-api-migrate-data-striim/deploy-using-default-option.png)

1. Após a implementação, pode pré-visualizar o fluxo para ver os dados a fluir. Selecione o ícone da **onda** e o globo ocular ao lado. Selecione o botão **Implantado** na barra de menu superior e selecione **Start App**.

   ![Inicie a aplicação](./media/cosmosdb-sql-api-migrate-data-striim/start-app.png)

1. Ao utilizar um leitor de **CDC(Change Data Capture),** a Striim irá recolher apenas novas alterações na base de dados. Se tiver dados que fluem através das suas tabelas de origem, verá. No entanto, uma vez que esta é uma mesa de demonstração, a fonte não está ligada a nenhuma aplicação. Se utilizar um gerador de dados de amostra, pode inserir uma cadeia de eventos na sua base de dados Oracle.

1. Verá os dados a fluir através da plataforma Striim. A Striim recolhe todos os metadados associados à sua tabela, o que é útil para monitorizar os dados e certificar-se de que os dados aterram no alvo certo.

   ![Configure o gasoduto CDC](./media/cosmosdb-sql-api-migrate-data-striim/configure-cdc-pipeline.png)

1. Finalmente, vamos entrar no Azure e navegar para a sua conta Azure Cosmos. Refresque o Data Explorer e pode ver que os dados chegaram.  

   ![Validar dados migrados em Azure](./media/cosmosdb-sql-api-migrate-data-striim/portal-validate-results.png)

Ao utilizar a solução Striim em Azure, pode migrar continuamente dados para o Azure Cosmos DB de várias fontes como o Oráculo, Cassandra, MongoDB e várias outras para o Azure Cosmos DB. Para saber mais visite o site da [Striim](https://www.striim.com/), faça o download de um teste gratuito de 30 dias [da Striim,](https://go2.striim.com/download-free-trial)e para quaisquer problemas ao configurar o caminho de migração com a Striim, faça um pedido de [apoio.](https://go2.striim.com/request-support-striim)

## <a name="next-steps"></a>Passos seguintes

* Se está a migrar dados para a Azure Cosmos DB SQL API, veja [como migrar dados para a conta Cassandra API usando a Striim](cosmosdb-cassandra-api-migrate-data-striim.md)

* [Monitorize e depreite os seus dados com métricas de DB da Azure Cosmos](use-metrics.md)
