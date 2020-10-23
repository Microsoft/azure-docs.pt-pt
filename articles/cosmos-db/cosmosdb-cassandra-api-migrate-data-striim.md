---
title: Migrar dados para a conta Azure Cosmos DB Cassandra API usando Striim
description: Saiba como usar o Striim para migrar dados de uma base de dados da Oracle para uma conta Azure Cosmos DB Cassandra API.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/22/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: 5c9ec20ffe52c23c2dec5a624fc157da7ebd4a41
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92330942"
---
# <a name="migrate-data-to-azure-cosmos-db-cassandra-api-account-using-striim"></a>Migrar dados para a conta Azure Cosmos DB Cassandra API usando Striim

A imagem Striim no mercado Azure oferece um movimento contínuo de dados em tempo real, desde armazéns de dados e bases de dados até Azure. Ao mover os dados, pode executar desnormalização em linha, transformação de dados, ativar a análise em tempo real e cenários de reporte de dados. É fácil começar com a Striim para mover continuamente os dados da empresa para a Azure Cosmos DB Cassandra API. A Azure oferece uma oferta de marketplace que facilita a implantação de Striim e migra dados para a Azure Cosmos DB. 

Este artigo mostra como usar striim para migrar dados de uma base de **dados oracle** para uma **conta Azure Cosmos DB Cassandra API**.

## <a name="prerequisites"></a>Pré-requisitos

* Se não tiver uma [subscrição do Azure,](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing)crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.

* Uma base de dados da Oracle a funcionar no local com alguns dados.

## <a name="deploy-the-striim-marketplace-solution"></a>Implementar a solução de mercado Striim

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. **Selecione Criar um recurso** e procurar **Striim** no mercado Azure. Selecione a primeira opção e **Crie**.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/striim-azure-marketplace.png" alt-text="Encontre o item do mercado Striim":::

1. Em seguida, insira as propriedades de configuração da instância Striim. O ambiente Striim está implantado numa máquina virtual. A partir do painel **Básico,** introduza o **nome de utilizador VM**, **palavra-passe VM** (esta palavra-passe é usada para SSH no VM). Selecione a sua **Subscrição,** **Grupo de Recursos**e Detalhes de **Localização** onde pretende implementar o Striim. Uma vez concluído, selecione **OK**.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/striim-configure-basic-settings.png" alt-text="Encontre o item do mercado Striim":::


1. No painel de definições do **Cluster Striim,** escolha o tipo de implantação Striim e o tamanho da máquina virtual.

   |Definição | Valor | Descrição |
   | ---| ---| ---|
   |Tipo de implantação Striim |Autónomo | Striim pode funcionar em tipos de implantação **autónomos** ou **cluster.** O modo autónomo implantará o servidor Striim numa única máquina virtual e pode selecionar o tamanho dos VMs dependendo do volume de dados. O modo cluster irá implantar o servidor Striim em dois ou mais VMs com o tamanho selecionado. Ambientes de cluster com mais de 2 nós oferecem alta disponibilidade automática e failover.</br></br> Neste tutorial, pode selecionar a opção Autónoma. Utilize o VM do tamanho padrão "Standard_F4s". | 
   | Nome do cluster Striim|    <Striim_cluster_Name>|  Nome do aglomerado Striim.|
   | Senha de cluster Striim|   <Striim_cluster_password>|  Senha para o cluster.|

   Depois de preencher o formulário, selecione **OK** para continuar.

1. No painel de definições de **acesso Striim,** configufique o **endereço IP público** (escolha os valores predefinidos), Nome de domínio para **Striim,** **palavra-passe de administração** que gostaria de usar para iniciar sessão no Striim UI. Configurar um VNET e uma Sub-rede (escolha os valores predefinidos). Depois de preencher os detalhes, selecione **OK** para continuar.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/striim-access-settings.png" alt-text="Encontre o item do mercado Striim":::

1. O Azure validará a implantação e certificar-se-á de que tudo parece estar bem; validação leva alguns minutos para ser completada. Depois de concluída a validação, selecione **OK**.
  
1. Por fim, reveja os termos de utilização e selecione **Criar** para criar a sua instância Striim. 

## <a name="configure-the-source-database"></a>Configurar a base de dados de origem

Nesta secção, configura a base de dados Oráculo como fonte de movimento de dados.  Vai precisar do condutor da [Oracle JDBC](https://www.oracle.com/technetwork/database/features/jdbc/jdbc-ucp-122-3110062.html) para ligar à Oracle. Para ler as alterações a partir da base de dados Oráculo de origem, pode utilizar o [LogMiner](https://www.oracle.com/technetwork/database/features/availability/logmineroverview-088844.html) ou os [APIs XStream](https://docs.oracle.com/cd/E11882_01/server.112/e16545/xstrm_intro.htm#XSTRM72647). O controlador Oracle JDBC deve estar presente no classe Java da Striim para ler, escrever ou persistir dados da base de dados da Oracle.

Descarregue o controlador [ojdbc8.jar](https://www.oracle.com/technetwork/database/features/jdbc/jdbc-ucp-122-3110062.html) para a sua máquina local. Irá instalá-lo no cluster Striim mais tarde.

## <a name="configure-target-database"></a>Base de dados-alvo de configuração

Nesta secção, irá configurar a conta Azure Cosmos DB Cassandra API como o alvo do movimento de dados.

1. Crie uma [conta Azure Cosmos DB Cassandra API](create-cassandra-dotnet.md#create-a-database-account) utilizando o portal Azure.

1. Navegue para o painel **do Data Explorer** na sua conta Azure Cosmos. Selecione **Nova Tabela** para criar um novo recipiente. Assuma que está a migrar *produtos* e *encomenda* dados da base de dados da Oracle para a Azure Cosmos DB. Crie um novo Keyspace chamado **StriimDemo** com um recipiente Encomendas. Forre o recipiente com **1000 RUs**(este exemplo utiliza 1000 RUs, mas deve utilizar o rendimento estimado para a sua carga de trabalho) e **/ORDER_ID** como chave primária. Estes valores diferirão em função dos dados de origem. 

   :::image type="content" source="./media/cosmosdb-cassandra-api-migrate-data-striim/create-cassandra-api-account.png" alt-text="Encontre o item do mercado Striim":::

## <a name="configure-oracle-to-azure-cosmos-db-data-flow"></a>Configurar o oráculo para a azure Cosmos DB fluxo de dados

1. Agora, vamos voltar ao Striim. Antes de interagir com o Striim, instale o controlador Oracle JDBC que descarregou anteriormente.

1. Navegue para o caso Striim que implementou no portal Azure. Selecione o botão **'Ligar'** na barra de menu superior e a partir do separador **SSH,** copie o URL em Login utilizando o campo **de conta local VM.**

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/get-ssh-url.png" alt-text="Encontre o item do mercado Striim":::

1. Abra uma nova janela de terminal e execute o comando SSH que copiou do portal Azure. Este artigo utiliza terminal num MacOS, pode seguir as instruções semelhantes usando PuTTY ou um cliente SSH diferente numa máquina Windows. Quando solicitado, escreva **sim** para continuar e introduza a **palavra-passe** que definiu para a máquina virtual no passo anterior.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/striim-vm-connect.png" alt-text="Encontre o item do mercado Striim":::

1. Agora, abra um novo separador de terminal para copiar o ficheiro **ojdbc8.jar** que descarregou anteriormente. Utilize o seguinte comando SCP para copiar o ficheiro do frasco da sua máquina local para a pasta tmp da instância Striim em execução em Azure:

   ```bash
   cd <Directory_path_where_the_Jar_file_exists> 
   scp ojdbc8.jar striimdemo@striimdemo.westus.cloudapp.azure.com:/tmp
   ```

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/copy-jar-file.png" alt-text="Encontre o item do mercado Striim":::

1. Em seguida, volte para a janela onde fez SSH para a instância Striim e Login como sudo. Mova o ficheiro **ojdbc8.jar** do diretório **/tmp** para o **diretório libl** do seu exemplo Striim com os seguintes comandos:

   ```bash
   sudo su
   cd /tmp
   mv ojdbc8.jar /opt/striim/lib
   chmod +x ojdbc8.jar
   ```

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/move-jar-file.png" alt-text="Encontre o item do mercado Striim":::


1. A partir da mesma janela de terminal, reinicie o servidor Striim executando os seguintes comandos:

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

1. Agora, volte a Azure e copie o endereço IP público do seu VM Striim. 

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/copy-public-ip-address.png" alt-text="Encontre o item do mercado Striim":::

1. Para navegar para o UI web do Striim, abra um novo separador num browser e copie o IP público seguido por: 9080. Faça o súbs utilizando o nome de utilizador de **administração,** juntamente com a palavra-passe de administração especificada no portal Azure.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/striim-login-ui.png" alt-text="Encontre o item do mercado Striim":::

1. Agora chegará à página inicial do Striim. Existem três painéis diferentes - **Dashboards,** **Apps**e **SourcePreview.** O painel dashboards permite mover dados em tempo real e visualizá-lo. O painel apps contém os seus oleodutos de dados de streaming, ou fluxos de dados. No lado direito da página está o SourcePreview onde pode pré-visualizar os seus dados antes de os mover.

1. Selecione o painel **apps,** vamos focar-nos neste painel por enquanto. Existem uma variedade de aplicações de amostra que você pode usar para aprender sobre Striim, no entanto neste artigo você vai criar o nosso próprio. Selecione o botão **Adicionar App** no canto superior direito.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/add-striim-app.png" alt-text="Encontre o item do mercado Striim":::

1. Existem algumas maneiras diferentes de criar aplicações Striim. Selecione **Start from Scratch** para este cenário.

   :::image type="content" source="./media/cosmosdb-cassandra-api-migrate-data-striim/start-app-from-scratch.png" alt-text="Encontre o item do mercado Striim":::

1. Dê um nome amigável para a sua aplicação, algo como **oraToCosmosDB** e selecione **Save**.

   :::image type="content" source="./media/cosmosdb-cassandra-api-migrate-data-striim/create-new-application.png" alt-text="Encontre o item do mercado Striim":::

1. Você chegará ao Flow Designer, onde você pode arrastar e largar fora dos conectores de caixa para criar suas aplicações de streaming. Digite **o Oráculo** na barra de pesquisa, arraste e deixe cair a fonte **do CDC da Oracle** na tela da aplicação.  

   :::image type="content" source="./media/cosmosdb-cassandra-api-migrate-data-striim/oracle-cdc-source.png" alt-text="Encontre o item do mercado Striim":::

1. Introduza as propriedades de configuração de origem do seu exemplo Oráculo. O nome de origem é apenas uma convenção de nomeação para a aplicação Striim, você pode usar um nome como  **src_onPremOracle**. Introduza também outros detalhes como o tipo adaptador, URL de ligação, nome de utilizador, senha, nome de tabela. **Selecione Guardar** para continuar.

   :::image type="content" source="./media/cosmosdb-cassandra-api-migrate-data-striim/configure-source-parameters.png" alt-text="Encontre o item do mercado Striim":::

1. Agora, clique no ícone de onda do fluxo para ligar o exemplo de DB do Azure Cosmos. 

   :::image type="content" source="./media/cosmosdb-cassandra-api-migrate-data-striim/connect-to-target.png" alt-text="Encontre o item do mercado Striim":::

1. Antes de configurar o alvo, certifique-se de ter adicionado um [certificado de raiz de Baltimore ao ambiente java de Striim.](/azure/developer/java/sdk/java-sdk-add-certificate-ca-store#to-add-a-root-certificate-to-the-cacerts-store)

1. Introduza as propriedades de configuração do seu exemplo de DB Azure Cosmos e selecione **Save** para continuar. Aqui estão os parâmetros-chave a notar:

   * **Adaptador** - Use **DatabaseWriter**. Ao escrever para Azure Cosmos DB Cassandra API, é necessário DatabaseWriter. O piloto cassandra 3.6.0 está agregado com Striim. Se o DatabaseWriter exceder o número de RUs previstos no seu contentor Azure Cosmos, a aplicação falhará.

   * **URL de ligação** - Especifique o URL de ligação Azure Cosmos DB. O URL está no formato     `jdbc:cassandra://<contactpoint>:10350/<databaseName>?SSL=true`

   * **Nome de utilizador** - Especifique o nome da sua conta Azure Cosmos.
   
   * **Password** - Especifique a chave primária da sua conta Azure Cosmos.

   * **Tabelas** - As tabelas-alvo devem ter teclas primárias e as teclas primárias não podem ser atualizadas.

   :::image type="content" source="./media/cosmosdb-cassandra-api-migrate-data-striim/configure-target-parameters1.png" alt-text="Encontre o item do mercado Striim":::

   :::image type="content" source="./media/cosmosdb-cassandra-api-migrate-data-striim/configure-target-parameters2.png" alt-text="Encontre o item do mercado Striim":::

1. Agora, vamos em frente e executar a aplicação Striim. Na barra de menu superior selecione **Criar**e, em seguida, **implementar app**. Na janela de implantação pode especificar se pretende executar certas partes da sua aplicação em partes específicas da sua topologia de implantação. Já que estamos a executar uma simples topologia de implantação através do Azure, usaremos a opção padrão.

   :::image type="content" source="./media/cosmosdb-cassandra-api-migrate-data-striim/deploy-the-app.png" alt-text="Encontre o item do mercado Striim":::


1. Agora, vamos em frente e visualizar o fluxo para ver os dados fluindo através do Striim. Clique no ícone de onda e clique no ícone do olho ao lado. Depois de implementar, pode visualizar o fluxo para ver os dados a fluir. Selecione o ícone de **onda** e o **globo ocular** ao lado. Selecione o botão **Implantado** na barra de menu superior e selecione **Start App**.

   :::image type="content" source="./media/cosmosdb-cassandra-api-migrate-data-striim/start-the-app.png" alt-text="Encontre o item do mercado Striim":::

1. Ao utilizar um leitor **CDC (Change Data Capture),** o Striim irá recolher apenas novas alterações na base de dados. Se tiver dados a fluir através das suas tabelas de origem, vai vê-lo. No entanto, uma vez que esta é uma tabela de amostras, a fonte que não está ligada a qualquer aplicação. Se utilizar um gerador de dados de amostra, pode inserir uma cadeia de eventos na sua base de dados Oracle.

1. Verá dados a fluir pela plataforma Striim. A Striim também recolhe todos os metadados associados à sua tabela, o que é útil para monitorizar os dados e certificar-se de que os dados aterram no alvo certo.

   :::image type="content" source="./media/cosmosdb-cassandra-api-migrate-data-striim/setup-cdc-pipeline.png" alt-text="Encontre o item do mercado Striim":::

1. Finalmente, vamos entrar no Azure e navegar para a sua conta Azure Cosmos. Refresque o Data Explorer e poderá ver que os dados chegaram. 

Ao utilizar a solução Striim em Azure, pode migrar continuamente dados para Azure Cosmos DB de várias fontes como Oracle, Cassandra, MongoDB, e vários outros para Azure Cosmos DB. Para saber mais, visite o site da [Striim](https://www.striim.com/), [faça o download de um teste gratuito de 30 dias de Striim](https://go2.striim.com/download-free-trial), e para quaisquer problemas ao configurar a rota de migração com striim, apresente um pedido de [apoio.](https://go2.striim.com/request-support-striim)

## <a name="next-steps"></a>Passos seguintes

* Se estiver a migrar dados para a Azure Cosmos DB SQL API, veja [como migrar dados para a conta da API de Cassandra usando o Striim](cosmosdb-sql-api-migrate-data-striim.md)

* [Monitorize e depure os seus dados com métricas DB da Azure Cosmos](use-metrics.md)
