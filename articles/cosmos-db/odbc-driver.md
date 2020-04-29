---
title: Ligue-se ao Azure Cosmos DB utilizando ferramentas de análise BI
description: Aprenda a usar o controlador Azure Cosmos DB ODBC para criar tabelas e vistas para que os dados normalizados possam ser vistos em BI e software de análise de dados.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: sngun
ms.openlocfilehash: 8be17f0b624c5c34709fb420adb434b77dbc0d91
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76721086"
---
# <a name="connect-to-azure-cosmos-db-using-bi-analytics-tools-with-the-odbc-driver"></a>Ligue-se ao Azure Cosmos DB utilizando ferramentas de análise bi com o condutor da ODBC

O controlador Azure Cosmos DB ODBC permite-lhe ligar-se ao Azure Cosmos DB utilizando ferramentas de análise BI como Serviços de Integração de Servidores SQL, Power BI Desktop e Tableau para que possa analisar e criar visualizações dos seus dados DoB Do MB em essas soluções.

O controlador ODBC do Azure Cosmos DB é compatível com o ODBC 3.8 e suporta a sintaxe ANSI SQL-92. O controlador disponibiliza funcionalidades avançadas para ajudá-lo renormalizar os dados no Azure Cosmos DB. Com o controlador, pode representar os dados no Azure Cosmos DB como tabelas e vistas. O controlador permite-lhe efetuar operações de SQL em tabelas e vistas, incluindo agrupar por consultas, inserções, atualizações e eliminações.

> [!NOTE]
> A ligação ao Azure Cosmos DB com o condutor da ODBC é atualmente suportada apenas para contas API Da Azure Cosmos DB SQL.

## <a name="why-do-i-need-to-normalize-my-data"></a>Por que preciso normalizar os meus dados?
A Azure Cosmos DB é uma base de dados sem esquemas, que permite o rápido desenvolvimento de aplicações e a capacidade de iterar em modelos de dados sem estar confinado a um esquema rigoroso. Uma única base de dados Azure Cosmos pode conter documentos JSON de várias estruturas. Isto é ótimo para o rápido desenvolvimento de aplicações, mas quando você quer analisar e criar relatórios dos seus dados usando análise de dados e ferramentas BI, os dados muitas vezes precisam ser achatados e aderir a um esquema específico.

É aqui que entra o condutor da ODBC. Ao utilizar o controlador ODBC, pode agora renormalizar os dados em Azure Cosmos DB em tabelas e visualizações que se adequam às suas necessidades de análise de dados e reporte. Os schemas renormalizados não têm impacto nos dados subjacentes e não limitam os desenvolvedores a aderir aos mesmos. Em vez disso, permitem-lhe alavancar ferramentas compatíveis com a ODBC para aceder aos dados. Portanto, agora a sua base de dados Azure Cosmos não só será uma das favoritas para a sua equipa de desenvolvimento, como os seus analistas de dados também vão adorar.

Vamos começar com o motorista da ODBC.

## <a name="step-1-install-the-azure-cosmos-db-odbc-driver"></a><a id="install"></a>Passo 1: Instalar o condutor do Azure Cosmos DB ODBC

1. Baixe os condutores para o seu ambiente:

    | Instalador | Sistemas operativos suportados| 
    |---|---| 
    |[Microsoft Azure Cosmos DB ODBC 64 bitsi](https://aka.ms/cosmos-odbc-64x64) para Windows de 64 bits| Versões de 64 bits do Windows 8.1 ou posterior, Windows 8, Windows 7, Windows Server 2012 R2, Windows Server 2012 e Windows Server 2008 R2.| 
    |[Microsoft Azure Cosmos DB ODBC 32x64-bit.msi](https://aka.ms/cosmos-odbc-32x64) por 32 bits em Windows de 64 bits| Versões de 64 bits do Windows 8.1 ou posterior, Windows 8, Windows 7, Windows XP, Windows Vista, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 e Windows Server 2003.| 
    |[Microsoft Azure Cosmos DB ODBC 32 bitsi](https://aka.ms/cosmos-odbc-32x32) para Windows de 32 bits|Versões de 32 bits do Windows 8.1 ou mais tarde, Windows 8, Windows 7, Windows XP e Windows Vista.|

    Executar o ficheiro msi localmente, que inicia o Assistente de Instalação do **Condutor DB ODBC da Microsoft Azure Cosmos**. 

1. Complete o assistente de instalação utilizando a entrada predefinida para instalar o controlador ODBC.

1. Abra a aplicação De Administrador de Fonte de **Dados da ODBC** no seu computador. Pode fazê-lo digitando fontes de dados da **ODBC** na caixa de pesquisa do Windows. 
    Pode confirmar que o controlador foi instalado clicando no separador **Drivers** e garantindo que o **Microsoft Azure Cosmos DB ODBC Driver** está listado.

    ![Administrador de fonte de dados da Azure Cosmos DB ODBC](./media/odbc-driver/odbc-driver.png)

## <a name="step-2-connect-to-your-azure-cosmos-database"></a><a id="connect"></a>Passo 2: Ligue-se à sua base de dados Azure Cosmos

1. Depois de [instalar o controlador Azure Cosmos DB ODBC,](#install)na janela **DoDBC Data Source Administrator,** clique em **Adicionar**. Pode criar um User ou System DSN. Neste exemplo, está a criar um DSN utilizador.

1. Na janela **Create New Data Source,** selecione **Microsoft Azure Cosmos DB ODBC Driver**, e, em seguida, clique em **Terminar**.

1. Na janela de configuração do **condutor SDN do Azure Cosmos DB ODBC,** preencha as seguintes informações: 

    ![Janela de configuração do condutor DSN do condutor da Azure Cosmos DB ODBC](./media/odbc-driver/odbc-driver-dsn-setup.png)
    - **Nome fonte de dados**: O seu próprio nome amigável para o DSN ODBC. Este nome é exclusivo da sua conta Azure Cosmos DB, por isso, nomeie-o adequadamente se tiver várias contas.
    - **Descrição**: Uma breve descrição da fonte de dados.
    - **Anfitrião**: URI para a sua conta Azure Cosmos DB. Pode recuperá-lo a partir da página Azure Cosmos DB Keys no portal Azure, como mostra a seguinte imagem. 
    - **Chave de acesso**: A tecla primária ou secundária, escrita ou leitura apenas a partir da página Azure Cosmos DB Keys no portal Azure, como mostra a seguinte imagem. Recomendamos que utilize a tecla apenas para leitura se o DSN for utilizado para o tratamento e relatório de dados apenas de leitura.
    ![Página de Chaves DB Do Cosmos Azure Cosmos](./media/odbc-driver/odbc-cosmos-account-keys.png)
    - **Chave de acesso encriptada para**: Selecione a melhor escolha com base nos utilizadores desta máquina. 
    
1. Clique no botão **Teste** para se certificar de que pode ligar-se à sua conta Azure Cosmos DB. 

1.  Clique em **Opções Avançadas** e detete os seguintes valores:
    *  **Versão REST API**: Selecione a [versão REST API](https://docs.microsoft.com/rest/api/cosmos-db/) para as suas operações. O padrão 2015-12-16. Se tiver recipientes com [chaves de partição grandes](large-partition-keys.md) e necessitar da versão REST API 2018-12-31:
        - Digite em **2018-12-31** para versão REST API
        - No menu **Iniciar,** digite "regedit" para encontrar e abrir a aplicação **Registry Editor.**
        - No Editor de Registo, navegue para o caminho: **Computador\HKEY_LOCAL_MACHINE\SOFTWARE\ODBC\ODBC. INI**
        - Crie uma nova subchave com o mesmo nome que o seu DSN, por exemplo, "Conta Contoso ODBC DSN".
        - Navegue para a subchave "Contoso Account ODBC DSN".
        - Clique à direita para adicionar um novo valor **de String:**
            - Nome de valor: **IgnoreSessionToken**
            - Dados de valor: **1**
            ![Definições do Editor de Registo](./media/odbc-driver/cosmos-odbc-edit-registry.png)
    - **Consistência da consulta**: Selecione o [nível de consistência](consistency-levels.md) para as suas operações. O padrão é Session.
    - **Número de Repetições**: Introduza o número de vezes para voltar a tentar uma operação se o pedido inicial não estiver concluído devido ao limite da taxa de serviço.
    - **Arquivo Schema**: Tem várias opções aqui.
        - Por predefinição, deixando esta entrada tal como está (em branco), o condutor digitaliza a primeira página de dados para todos os recipientes determinarem o esquema de cada recipiente. Isto é conhecido como Mapeamento de contentores. Sem um ficheiro de esquema definido, o condutor tem de realizar a varredura para cada sessão do condutor e pode resultar num maior tempo de arranque de uma aplicação utilizando o DSN. Recomendamos que associe sempre um ficheiro de esquemas para um DSN.
        - Se já tiver um ficheiro schema (possivelmente um que criou usando o Editor Schema), pode clicar em **Navegar,** navegar para o seu ficheiro, clicar em **Guardar**e clicar em **OK**.
        - Se quiser criar um novo esquema, clique em **OK**e clique em **Schema Editor** na janela principal. Em seguida, dirija-se à informação do Editor Schema. Depois de criar o novo ficheiro schema, lembre-se de voltar à janela **Opções Avançadas** para incluir o arquivo de esquemarecém-criado.

1. Uma vez concluída e fechado a janela de **configuração DSN do condutor Do DSN** do condutor do Azure Cosmos DB ODBC, o novo DSN do utilizador é adicionado ao separador DSN do utilizador.

    ![Novo Azure Cosmos DB ODBC DSN no separador DSN do utilizador](./media/odbc-driver/odbc-driver-user-dsn.png)

## <a name="step-3-create-a-schema-definition-using-the-container-mapping-method"></a><a id="#container-mapping"></a>Passo 3: Criar uma definição de esquema utilizando o método de mapeamento do contentor

Existem dois tipos de métodos de amostragem que pode utilizar: mapeamento de **contentores** ou **delimitadores de mesa**. Uma sessão de amostragem pode utilizar ambos os métodos de amostragem, mas cada recipiente só pode utilizar um método específico de amostragem. Os passos abaixo criam um esquema para os dados em um ou mais recipientes utilizando o método de mapeamento do recipiente. Este método de amostragem recupera os dados na página de um recipiente para determinar a estrutura dos dados. Transpõe um recipiente para uma mesa do lado da ODBC. Este método de amostragem é eficiente e rápido quando os dados de um recipiente são homogéneos. Se um recipiente contiver um tipo de dados heterogéneo, recomendamos que utilize o método de mapeamento de [delimitadores de mesa,](#table-mapping) uma vez que fornece um método de amostragem mais robusto para determinar as estruturas de dados no recipiente. 

1. Depois de completar os passos 1-4 em Connect à sua base de [dados Azure Cosmos,](#connect)clique em **Schema Editor** na janela de **configuração dSN do condutor do Azure Cosmos DB ODBC.**

    ![Botão de editor schema na janela de configuração do condutor DSN do Condutor DSN do Azure Cosmos](./media/odbc-driver/odbc-driver-schema-editor.png)
1. Na janela **do Editor Schema,** clique em **Criar Novo**.
    A janela **Generate Schema** exibe todos os recipientes na conta Azure Cosmos DB. 

1. Selecione um ou mais recipientes para provar e, em seguida, clique em **Sample**. 

1. No separador **Design View,** a base de dados, o esquema e a tabela estão representados. Na vista da tabela, a digitalização apresenta o conjunto de propriedades associadas aos nomes das colunas (Nome SQL, Nome fonte, etc.).
    Para cada coluna, pode modificar o nome SQL da coluna, o tipo SQL, comprimento SQL (se aplicável), Escala (se aplicável), Precisão (se aplicável) e Nulidade.
    - Pode definir a **Coluna oculta** **como verdadeira** se quiser excluir essa coluna dos resultados da consulta. Colunas marcadas Coluna de Couro = verdade não são devolvidas para seleção e projeção, embora ainda façam parte do esquema. Por exemplo, pode esconder todo o sistema Azure Cosmos DB propriedades necessárias a partir de "_".
    - A coluna **id** é o único campo que não pode ser escondido, uma vez que é usado como a chave primária no esquema normalizado. 

1. Uma vez terminada a definição do esquema, clique em **Guardar Ficheiros,** | **Save**navegar para o diretório para salvar o esquema e, em seguida, clique em **Guardar**.

1. Para utilizar este esquema com um DSN, abra a janela de **configuração DSN do condutor do Azure Cosmos DB ODBC** (através do Administrador de Fonte de Dados da ODBC), clique em **Opções Avançadas**, e depois na caixa **de ficheiros Schema,** navegue para o esquema guardado. Guardar um ficheiro schema para um DSN existente modifica a ligação DSN ao âmbito dos dados e estrutura definidos por esquema.

## <a name="step-4-create-a-schema-definition-using-the-table-delimiters-mapping-method"></a><a id="table-mapping"></a>Passo 4: Criar uma definição de esquema utilizando o método de mapeamento de delimitadores de mesa

Existem dois tipos de métodos de amostragem que pode utilizar: mapeamento de **contentores** ou **delimitadores de mesa**. Uma sessão de amostragem pode utilizar ambos os métodos de amostragem, mas cada recipiente só pode utilizar um método específico de amostragem. 

Os seguintes passos criam um esquema para os dados em um ou mais recipientes utilizando o método de mapeamento de **delimitadores de mesa.** Recomendamos que utilize este método de amostragem quando os seus recipientes contenham dados heterogéneos. Pode utilizar este método para analisar a amostragem de um conjunto de atributos e os seus valores correspondentes. Por exemplo, se um documento contiver uma propriedade "Tipo", pode examinar a amostragem dos valores desta propriedade. O resultado final da amostragem seria um conjunto de tabelas para cada um dos valores do Tipo especificado. Por exemplo, O Tipo = Carro produzirá uma tabela de carro enquanto o Tipo = Avião produziria uma tabela de planos.

1. Depois de completar os passos 1-4 em Connect à sua base de [dados Azure Cosmos,](#connect)clique em **Schema Editor** na janela de configuração dSN do condutor do Azure Cosmos DB ODBC.

1. Na janela **do Editor Schema,** clique em **Criar Novo**.
    A janela **Generate Schema** exibe todos os recipientes na conta Azure Cosmos DB. 

1. Selecione um recipiente no separador **Sample View,** na coluna Definição de **Mapeamento** para o recipiente, clique em **Editar**. Em seguida, na janela Definição de **Mapeamento,** selecione o método **Delimiters** de Tabela. Em seguida, faça o seguinte:

    a. Na caixa **Deatributos,** digite o nome de uma propriedade delimitador. Esta é uma propriedade no seu documento que pretende examinar a amostragem para, por exemplo, city e press enter. 

    b. Se pretender apenas examinar a amostragem para determinados valores para o atributo que introduziu acima, selecione o atributo na caixa de seleção, introduza um valor na caixa **de valor** (por exemplo, Seattle) e prima introduzir. Pode continuar a adicionar vários valores para atributos. Apenas certifique-se de que o atributo correto é selecionado quando estiver a introduzir valores.

    Por exemplo, se incluir um valor **de Atributos** da Cidade, e quiser limitar a sua mesa a incluir apenas linhas com um valor de cidade de Nova Iorque e Dubai, entraria na Cidade na caixa de Atributos, e Nova Iorque e depois Dubai na caixa **Values.**

1. Clique em **OK**. 

1. Depois de completar as definições de mapeamento dos recipientes que pretende provar, na janela **do Editor Schema,** clique em **Sample**.
     Para cada coluna, pode modificar o nome SQL da coluna, o tipo SQL, comprimento SQL (se aplicável), Escala (se aplicável), Precisão (se aplicável) e Nulidade.
    - Pode definir a **Coluna oculta** **como verdadeira** se quiser excluir essa coluna dos resultados da consulta. Colunas marcadas Coluna de Couro = verdade não são devolvidas para seleção e projeção, embora ainda façam parte do esquema. Por exemplo, pode esconder todas as propriedades necessárias ao `_`sistema Azure Cosmos DB a partir de .
    - A coluna **id** é o único campo que não pode ser escondido, uma vez que é usado como a chave primária no esquema normalizado. 

1. Uma vez terminada a definição do esquema, clique em **Guardar Ficheiros,** | **Save**navegar para o diretório para salvar o esquema e, em seguida, clique em **Guardar**.

1. De volta à janela de **configuração dSN do condutor do Azure Cosmos DB ODBC,** clique em **Opções Avançadas**. Em seguida, na caixa **de ficheiros Schema,** navegue para o ficheiro schema guardado e clique em **OK**. Clique **em OK** novamente para salvar o DSN. Isto salva o esquema que criaste para o DSN. 

## <a name="optional-set-up-linked-server-connection"></a>(Opcional) Configurar a ligação ao servidor linked

Pode consultar o Azure Cosmos DB do SQL Server Management Studio (SSMS) configurando uma ligação ao servidor ligado.

1. Criar uma fonte de dados do sistema `SDS Name`como descrito no [Passo 2,](#connect)nomeado por exemplo.

1. [Instale o Estúdio de Gestão de Servidores SQL](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) e ligue-se ao servidor. 

1. No editor de consulta SSMS, crie um objeto `DEMOCOSMOS` de servidor ligado para a fonte de dados com os seguintes comandos. Substitua-o `DEMOCOSMOS` pelo nome do `SDS Name` seu servidor ligado e com o nome da fonte de dados do seu sistema.

    ```sql
    USE [master]
    GO
    
    EXEC master.dbo.sp_addlinkedserver @server = N'DEMOCOSMOS', @srvproduct=N'', @provider=N'MSDASQL', @datasrc=N'SDS Name'
    
    EXEC master.dbo.sp_addlinkedsrvlogin @rmtsrvname=N'DEMOCOSMOS', @useself=N'False', @locallogin=NULL, @rmtuser=NULL, @rmtpassword=NULL
    
    GO
    ```
    
Para ver o novo nome do servidor ligado, refresque a lista de Servidores Linked.

![Servidor ligado em SSMS](./media/odbc-driver/odbc-driver-linked-server-ssms.png)

### <a name="query-linked-database"></a>Base de dados ligada à consulta

Para consultar a base de dados ligada, introduza uma consulta de SSMS. Neste exemplo, a consulta seleciona da mesa `customers`do recipiente denominado:

```sql
SELECT * FROM OPENQUERY(DEMOCOSMOS, 'SELECT *  FROM [customers].[customers]')
```

Execute a consulta. O resultado deve ser semelhante ao seguinte:

```
attachments/  1507476156    521 Bassett Avenue, Wikieup, Missouri, 5422   "2602bc56-0000-0000-0000-59da42bc0000"   2015-02-06T05:32:32 +05:00 f1ca3044f17149f3bc61f7b9c78a26df
attachments/  1507476156    167 Nassau Street, Tuskahoma, Illinois, 5998   "2602bd56-0000-0000-0000-59da42bc0000"   2015-06-16T08:54:17 +04:00 f75f949ea8de466a9ef2bdb7ce065ac8
attachments/  1507476156    885 Strong Place, Cassel, Montana, 2069       "2602be56-0000-0000-0000-59da42bc0000"   2015-03-20T07:21:47 +04:00 ef0365fb40c04bb6a3ffc4bc77c905fd
attachments/  1507476156    515 Barwell Terrace, Defiance, Tennessee, 6439     "2602c056-0000-0000-0000-59da42bc0000"   2014-10-16T06:49:04 +04:00      e913fe543490432f871bc42019663518
attachments/  1507476156    570 Ruby Street, Spokane, Idaho, 9025       "2602c156-0000-0000-0000-59da42bc0000"   2014-10-30T05:49:33 +04:00 e53072057d314bc9b36c89a8350048f3
```

> [!NOTE]
> O servidor Cosmos DB ligado não suporta nomeação em quatro partes. Um erro é devolvido semelhante à seguinte mensagem:

```
Msg 7312, Level 16, State 1, Line 44

Invalid use of schema or catalog for OLE DB provider "MSDASQL" for linked server "DEMOCOSMOS". A four-part name was supplied, but the provider does not expose the necessary interfaces to use a catalog or schema.
``` 

## <a name="optional-creating-views"></a>(Opcional) Criar pontos de vista
Pode definir e criar pontos de vista como parte do processo de amostragem. Estas vistas são equivalentes a vistas SQL. São apenas leituras e são abrangentes as seleções e projeções da consulta Azure Cosmos DB SQL definida. 

Para criar uma vista para os seus dados, na janela **Do Editor Schema,** na coluna **Definições** de Visualização, clique em **Adicionar** na linha do recipiente para provar. 
    ![Criar uma visão dos dados](./media/odbc-driver/odbc-driver-create-view.png)


Em seguida, na janela Definições de **Visualização,** faça o seguinte:

1. Clique em **Novo**, introduza um nome para a vista, por exemplo, Employees fromSeattleView e, em seguida, clique EM **OK**.

1. Na janela **edit,** insira uma consulta de Azure Cosmos DB. Esta deve ser uma [consulta Azure Cosmos DB SQL](how-to-sql-query.md), por exemplo `SELECT c.City, c.EmployeeName, c.Level, c.Age, c.Manager FROM c WHERE c.City = "Seattle"`, e, em seguida, clicar **OK**.

    ![Adicione consulta ao criar uma vista](./media/odbc-driver/odbc-driver-create-view-2.png)


Pode criar muitas vistas como quiser. Uma vez feito definindo as vistas, pode então provar os dados. 

## <a name="step-5-view-your-data-in-bi-tools-such-as-power-bi-desktop"></a>Passo 5: Veja os seus dados em ferramentas BI como Power BI Desktop

Pode utilizar o seu novo DSN para se ligar ao Azure Cosmos DB com quaisquer ferramentas compatíveis com a ODBC - este passo mostra-lhe simplesmente como ligar-se ao Power BI Desktop e criar uma visualização power BI.

1. Abra o Power BI Desktop.

1. Clique em **Obter Dados**.

    ![Obter Dados no Power BI Desktop](./media/odbc-driver/odbc-driver-power-bi-get-data.png)

1. Na janela **Obter Dados,** clique em **Outra** | **Ligação****ODBC** | .

    ![Escolha a fonte de dados da ODBC em Power BI Obter Dados](./media/odbc-driver/odbc-driver-power-bi-get-data-2.png)

1. Na janela **From ODBC,** selecione o nome de origem de dados que criou e, em seguida, clique em **OK**. Pode deixar as entradas **de Opções Avançadas** em branco.

    ![Escolha o nome de origem de dados (DSN) em Power BI Obtenha Dados](./media/odbc-driver/odbc-driver-power-bi-get-data-3.png)

1. No Acesso uma fonte de dados utilizando uma janela de **controlador ODBC,** selecione **Padrão ou Custom** e, em seguida, clique em **Connect**. Não precisa de incluir as propriedades da cadeia de **ligação credencial.**

1. Na janela **Do Navegador,** no painel esquerdo, expanda a base de dados, o esquema e, em seguida, selecione a tabela. O painel de resultados inclui os dados que utilizam o esquema que criou.

    ![Tabela selecionada em Power BI Obter Dados](./media/odbc-driver/odbc-driver-power-bi-get-data-4.png)

1. Para visualizar os dados no ambiente de trabalho power BI, verifique a caixa em frente ao nome da tabela e, em seguida, clique em **Carregar**.

1. No Power BI Desktop, na extrema esquerda, selecione o separador Dados ![Separador de dados no Ambiente de Trabalho Power BI](./media/odbc-driver/odbc-driver-data-tab.png) para confirmar que os seus dados foram importados.

1. Agora pode criar visuais usando o Power BI ![clicando no](./media/odbc-driver/odbc-driver-report-tab.png)separador Report report no Power BI Desktop, clicando em **New Visual**e, em seguida, personalizando o seu azulejo. Para obter mais informações sobre a criação de visualizações no Power BI Desktop, consulte os tipos de [visualização no Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-visualization-types-for-reports-and-q-and-a/).

## <a name="troubleshooting"></a>Resolução de problemas

Se receber o seguinte erro, certifique-se de que os valores da Chave de **Acolhimento** e **de Acesso** que copiou o portal Azure no [Passo 2](#connect) e, em seguida, tente novamente. Utilize os botões de cópia à direita dos valores da chave de **recolhimento** e **acesso** no portal Azure para copiar os valores isentos de erros.

    [HY000]: [Microsoft][Azure Cosmos DB] (401) HTTP 401 Authentication Error: {"code":"Unauthorized","message":"The input authorization token can't serve the request. Please check that the expected payload is built as per the protocol, and check the key being used. Server used the following payload to sign: 'get\ndbs\n\nfri, 20 jan 2017 03:43:55 gmt\n\n'\r\nActivityId: 9acb3c0d-cb31-4b78-ac0a-413c8d33e373"}`

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o Azure Cosmos DB, veja [Bem-vindo ao Azure Cosmos DB](introduction.md).
