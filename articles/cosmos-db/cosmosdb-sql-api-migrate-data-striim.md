---
title: Migrar dados para a conta Azure Cosmos DL API usando Striim
description: Saiba como usar o Striim para migrar dados de uma base de dados da Oracle para uma conta Azure Cosmos DB SQL API.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/22/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: 1e190c9f06dc2c662760421b7240eafdf22986b0
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92491313"
---
# <a name="migrate-data-to-azure-cosmos-db-sql-api-account-using-striim"></a>Migrar dados para a conta Azure Cosmos DL API usando Striim
 
A imagem Striim no mercado Azure oferece um movimento contínuo de dados em tempo real, desde armazéns de dados e bases de dados até Azure. Ao mover os dados, pode executar desnormalização em linha, transformação de dados, ativar a análise em tempo real e cenários de reporte de dados. É fácil começar com a Striim a mover continuamente os dados da empresa para a Azure Cosmos DB SQL API. A Azure oferece uma oferta de marketplace que facilita a implantação de Striim e migra dados para a Azure Cosmos DB. 

Este artigo mostra como usar o Striim para migrar dados de uma base de **dados da Oracle** para uma **conta Azure Cosmos DB SQL API**.

## <a name="prerequisites"></a>Pré-requisitos

* Se não tiver uma [subscrição do Azure,](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing)crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.

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
   |Tipo de implantação Striim |Autónomo | Striim pode funcionar em tipos de implantação **autónomos** ou **cluster.** O modo autónomo implantará o servidor Striim numa única máquina virtual e pode selecionar o tamanho dos VMs dependendo do volume de dados. O modo cluster irá implantar o servidor Striim em dois ou mais VMs com o tamanho selecionado. Ambientes de cluster com mais de 2 nós oferecem alta disponibilidade automática e failover.</br></br> Neste tutorial, pode selecionar a opção Autónoma. Utilize o VM do tamanho padrão "Standard_F4s".  | 
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

## <a name="configure-the-target-database"></a>Configurar a base de dados-alvo

Nesta secção, irá configurar a conta Azure Cosmos DB SQL API como o alvo do movimento de dados.

1. Crie uma [conta Azure Cosmos DB SQL API](create-cosmosdb-resources-portal.md) utilizando o portal Azure.

1. Navegue para o painel **do Data Explorer** na sua conta Azure Cosmos. Selecione **Novo Recipiente** para criar um novo recipiente. Assuma que está a migrar *produtos* e *encomenda* dados da base de dados da Oracle para a Azure Cosmos DB. Crie uma nova base de dados chamada **StriimDemo** com um contentor chamado **Orders**. Forre o recipiente com **1000 RUs** (este exemplo utiliza 1000 RUs, mas deve utilizar o rendimento estimado para a sua carga de trabalho) e **/ORDER_ID** como chave de partição. Estes valores diferirão em função dos dados de origem. 

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/create-sql-api-account.png" alt-text="Encontre o item do mercado Striim":::

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

1. Existem algumas maneiras diferentes de criar aplicações Striim. Selecione **Iniciar com o modelo** para começar com um modelo existente.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/start-with-template.png" alt-text="Encontre o item do mercado Striim" e selecione **Target: Azure Cosmos DB** e, em seguida, selecione **Oracle CDC para Azure Cosmos DB**.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/oracle-cdc-cosmosdb.png" alt-text="Encontre o item do mercado Striim":::

1. Na página seguinte, diga o seu pedido. Pode fornecer um nome como **oraToCosmosDB** e, em seguida, selecionar **Save**.

1. Em seguida, insira a configuração de origem da sua origem Oráculo. Introduza um valor para o **Nome Fonte**. O nome de origem é apenas uma convenção de nomeação para a aplicação Striim, você pode usar algo como **src_onPremOracle**. Introduza valores para o resto dos parâmetros de origem **URL**, **Nome de utilizador**, **Password,** escolha **LogMiner** como leitor para ler dados da Oracle. Selecione **Seguinte** para continuar.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/configure-source-parameters.png" alt-text="Encontre o item do mercado Striim":::

1. Striim verificará o seu ambiente e certificar-se-á de que pode ligar-se à sua fonte, ter os privilégios certos, e que o CDC foi configurado corretamente. Uma vez validados todos os valores, selecione **Seguinte**.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/validate-source-parameters.png" alt-text="Encontre o item do mercado Striim":::

1. Selecione as tabelas da base de dados Oracle que gostaria de migrar. Por exemplo, vamos escolher a tabela Encomendas e selecionar **a Seguinte**. 

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/select-source-tables.png" alt-text="Encontre o item do mercado Striim":::

1. Depois de selecionar a tabela de origem, pode fazer operações mais complicadas, como mapeamento e filtragem. Neste caso, apenas irá criar uma réplica da sua tabela de origem em Azure Cosmos DB. Então, **selecione Next** to configure o alvo

1. Agora, vamos configurar o alvo:

   * **Nome do alvo** - Forneça um nome amigável para o alvo. 
   * **Entrada A partir de** - Da lista de dropdown, selecione o fluxo de entrada do que criou na configuração Oráculo de origem. 
   * **Coleções**- Introduza as propriedades de configuração DB do Azure Cosmos. A sintaxe de coleções é **SourceSchema.SourceTable, TargetDatabase.TargetContainer**. Neste exemplo, o valor seria "SYSTEM. ORDENS, StriimDemo.Orders". 
   * **AccessKey** - A Chave Primária da sua conta Azure Cosmos.
   * **ServiceEndpoint** – O URI da sua conta Azure Cosmos, podem ser encontrados na secção **Chaves** do portal Azure. 

   **Selecione Save** and **Next**.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/configure-target-parameters.png" alt-text="Encontre o item do mercado Striim":::


1. Em seguida, chegará ao flow designer, onde pode arrastar e largar os conectores da caixa para criar as suas aplicações de streaming. Não irá fazer quaisquer modificações no fluxo neste momento. então vá em frente e implemente a aplicação selecionando o botão **implementar app.**
 
   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/deploy-app.png" alt-text="Encontre o item do mercado Striim":::

1. Na janela de implantação, pode especificar se pretende executar certas partes da sua aplicação em partes específicas da sua topologia de implantação. Já que estamos a executar uma simples topologia de implantação através do Azure, usaremos a opção padrão.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/deploy-using-default-option.png" alt-text="Encontre o item do mercado Striim":::

1. Depois de implementar, pode visualizar o fluxo para ver os dados a fluir. Selecione o ícone de **onda** e o globo ocular ao lado. Selecione o botão **Implantado** na barra de menu superior e selecione **Start App**.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/start-app.png" alt-text="Encontre o item do mercado Striim":::

1. Ao utilizar um leitor **CDC (Change Data Capture),** o Striim irá recolher apenas novas alterações na base de dados. Se tiver dados a fluir através das suas tabelas de origem, vai vê-lo. No entanto, uma vez que esta é uma tabela de demonstração, a fonte não está ligada a qualquer aplicação. Se utilizar um gerador de dados de amostra, pode inserir uma cadeia de eventos na sua base de dados Oracle.

1. Verá dados a fluir pela plataforma Striim. A Striim também recolhe todos os metadados associados à sua tabela, o que é útil para monitorizar os dados e certificar-se de que os dados aterram no alvo certo.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/configure-cdc-pipeline.png" alt-text="Encontre o item do mercado Striim":::

1. Finalmente, vamos entrar no Azure e navegar para a sua conta Azure Cosmos. Refresque o Data Explorer e poderá ver que os dados chegaram.  

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/portal-validate-results.png" alt-text="Encontre o item do mercado Striim":::

Ao utilizar a solução Striim em Azure, pode migrar continuamente dados para Azure Cosmos DB de várias fontes como Oracle, Cassandra, MongoDB, e vários outros para Azure Cosmos DB. Para saber mais, visite o site da [Striim](https://www.striim.com/), [faça o download de um teste gratuito de 30 dias de Striim](https://go2.striim.com/download-free-trial), e para quaisquer problemas ao configurar a rota de migração com striim, apresente um pedido de [apoio.](https://go2.striim.com/request-support-striim)

## <a name="next-steps"></a>Passos seguintes

* Se estiver a migrar dados para a Azure Cosmos DB SQL API, veja [como migrar dados para a conta da API de Cassandra usando o Striim](cosmosdb-cassandra-api-migrate-data-striim.md)

* [Monitorize e depure os seus dados com métricas DB da Azure Cosmos](use-metrics.md)