---
title: Ligue-se ao Azure Cosmos DB usando ferramentas de análise de BI
description: Saiba como utilizar o controlador Azure Cosmos DB ODBC para criar tabelas e vistas para que os dados normalizados possam ser vistos em software de bi e análise de dados.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/02/2019
ms.author: sngun
ms.openlocfilehash: a7f9573769beb5bf1f6ebe47e6e0a277185ac42b
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92488423"
---
# <a name="connect-to-azure-cosmos-db-using-bi-analytics-tools-with-the-odbc-driver"></a>Ligue-se ao Azure Cosmos DB utilizando ferramentas de análise de BI com o controlador ODBC

O controlador Azure Cosmos DB ODBC permite-lhe ligar-se ao Azure Cosmos DB utilizando ferramentas bi analytics como sql Server Integration Services, Power BI Desktop e Tableau para que possa analisar e criar visualizações dos seus dados DB Azure Cosmos nessas soluções.

O controlador ODBC do Azure Cosmos DB é compatível com o ODBC 3.8 e suporta a sintaxe ANSI SQL-92. O controlador disponibiliza funcionalidades avançadas para ajudá-lo renormalizar os dados no Azure Cosmos DB. Com o controlador, pode representar os dados no Azure Cosmos DB como tabelas e vistas. O controlador permite-lhe efetuar operações de SQL em tabelas e vistas, incluindo agrupar por consultas, inserções, atualizações e eliminações.

> [!NOTE]
> A ligação ao Azure Cosmos DB com o controlador ODBC é atualmente suportada apenas para contas AZure Cosmos DB SQL API.

## <a name="why-do-i-need-to-normalize-my-data"></a>Por que preciso normalizar os meus dados?
Azure Cosmos DB é uma base de dados sem esquemas, que permite o rápido desenvolvimento de aplicações e a capacidade de iterar em modelos de dados sem se limitar a um esquema rigoroso. Uma única base de dados Azure Cosmos pode conter documentos JSON de várias estruturas. Isto é ótimo para o desenvolvimento rápido de aplicações, mas quando você quer analisar e criar relatórios dos seus dados usando análise de dados e ferramentas bi, os dados muitas vezes precisam ser achatados e aderir a um esquema específico.

É aqui que entra o condutor da ODBC. Ao utilizar o controlador ODBC, pode agora renormalizar os dados em Azure Cosmos DB em tabelas e vistas que se adequam às suas necessidades de análise e reporte de dados. Os esquemas renormalizados não têm impacto nos dados subjacentes e não limitam os desenvolvedores a aderir aos mesmos. Em vez disso, permitem-lhe aproveitar as ferramentas compatíveis com o ODBC para aceder aos dados. Por isso, agora a sua base de dados Azure Cosmos não só será uma das favoritas para a sua equipa de desenvolvimento, como os seus analistas de dados também vão adorar.

Vamos começar com o motorista da ODBC.

## <a name="step-1-install-the-azure-cosmos-db-odbc-driver"></a><a id="install"></a>Passo 1: Instalar o controlador Azure Cosmos DB ODBC

1. Descarregue os controladores para o seu ambiente:

    | Instalador | Sistemas operativos suportados| 
    |---|---| 
    |[Microsoft Azure Cosmos DB ODBC 64-bit.msi](https://aka.ms/cosmos-odbc-64x64) para Windows de 64 bits| Versões de 64 bits do Windows 8.1 ou posterior, Windows 8, Windows 7, Windows Server 2012 R2, Windows Server 2012 e Windows Server 2008 R2.| 
    |[Microsoft Azure Cosmos DB ODBC 32x64-bit.msi](https://aka.ms/cosmos-odbc-32x64) para windows de 32 bits em Windows de 64 bits| Versões de 64 bits do Windows 8.1 ou posterior, Windows 8, Windows 7, Windows XP, Windows Vista, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 e Windows Server 2003.| 
    |[Microsoft Azure Cosmos DB ODBC 32-bit.msi](https://aka.ms/cosmos-odbc-32x32) para Windows de 32 bits|Versões de 32 bits do Windows 8.1 ou posterior, Windows 8, Windows 7, Windows XP e Windows Vista.|

    Execute o ficheiro MSI localmente, que inicia o **Microsoft Azure Cosmos DB ODBC Driver Installation Wizard**. 

1. Preencha o assistente de instalação utilizando a entrada predefinida para instalar o controlador ODBC.

1. Abra a aplicação **ODBC Data Source Administrator** no seu computador. Pode fazê-lo digitando fontes de **dados ODBC** na caixa de pesquisa do Windows. 
    Pode confirmar que o controlador foi instalado clicando no separador **Condutores** e garantindo que o **Controlador ODBC da Microsoft Azure Cosmos** está listado.

    :::image type="content" source="./media/odbc-driver/odbc-driver.png" alt-text="Administrador de fonte de dados da Azure Cosmos DB ODBC":::

## <a name="step-2-connect-to-your-azure-cosmos-database"></a><a id="connect"></a>Passo 2: Conecte-se à sua base de dados Azure Cosmos

1. Depois de [instalar o controlador Azure Cosmos DB ODBC,](#install)na janela do **administrador de fonte de dados ODBC,** clique em **Adicionar**. Pode criar um DSN de Utilizador ou Sistema. Neste exemplo, está a criar um DSN do Utilizador.

1. Na janela **Criar Novas Fontes de Dados,** selecione **o Controlador DB ODBC do Microsoft Azure Cosmos**e, em seguida, clique em **Terminar**.

1. Na janela de **configuração SDN do condutor da Azure Cosmos DB ODBC,** preencha as seguintes informações: 

    :::image type="content" source="./media/odbc-driver/odbc-driver-dsn-setup.png" alt-text="Administrador de fonte de dados da Azure Cosmos DB ODBC":::
    - **Data Source Name**: O seu próprio nome amigável para o DSN ODBC. Este nome é exclusivo da sua conta DB Azure Cosmos, por isso diga-lhe adequadamente se tiver várias contas.
    - **Descrição**: Uma breve descrição da fonte de dados.
    - **Anfitrião**: URI para a sua conta DB Azure Cosmos. Pode recuperá-lo a partir da página Azure Cosmos DB Keys no portal Azure, como mostra a imagem seguinte. 
    - **Tecla de acesso**: A tecla primária ou secundária, de leitura ou de leitura apenas a partir da página Azure Cosmos DB Keys no portal Azure, como mostrado na imagem seguinte. Recomendamos que utilize a chave apenas de leitura se o DSN for utilizado para o processamento e reporte de dados apenas de leitura.
    :::image type="content" source="./media/odbc-driver/odbc-cosmos-account-keys.png" alt-text="Administrador de fonte de dados da Azure Cosmos DB ODBC" Contoso Account ODBC DSN".
        - Navegue para o sub-chave "Contoso Account ODBC DSN".
        - Clique com o botão direito para adicionar um novo valor **de corda:**
            - Nome do valor: **IgnoreSessionToken**
            - Dados de valor: **1** 
             :::image type="content" source="./media/odbc-driver/cosmos-odbc-edit-registry.png" alt-text="Administrador de fonte de dados da Azure Cosmos DB ODBC":::
    - **Consistência de consulta**: Selecione o [nível de consistência](consistency-levels.md) para as suas operações. O padrão é Sessão.
    - **Número de Retrírias**: Introduza o número de vezes para voltar a tentar uma operação se o pedido inicial não for concluído devido à limitação da taxa de serviço.
    - **Ficheiro Schema**: Tem várias opções aqui.
        - Por predefinição, deixando esta entrada como está (em branco), o condutor digitaliza a primeira página de dados para todos os recipientes para determinar o esquema de cada recipiente. Isto é conhecido como Mapeamento de Contentores. Sem um ficheiro de esquema definido, o condutor tem de efetuar a verificação para cada sessão de condutor e pode resultar num tempo de arranque mais elevado de uma aplicação utilizando o DSN. Recomendamos que associe sempre um ficheiro de esquema para um DSN.
        - Se já tiver um ficheiro de esquema (possivelmente um que criou usando o Editor Schema), pode clicar em **Procurar,** navegar para o seu ficheiro, clicar em **Guardar**e clicar **em OK**.
        - Se quiser criar um novo esquema, clique em **OK**e, em seguida, clique em **Schema Editor** na janela principal. Em seguida, dirijam-se à informação do Editor Schema. Depois de criar o novo ficheiro de esquema, lembre-se de voltar à janela **Advanced Options** para incluir o ficheiro de esquema recém-criado.

1. Assim que completar e fechar a janela **de configuração DSN do controlador DB ODBC do Azure Cosmos,** o novo DSN do utilizador é adicionado ao separador DSN do utilizador.

    :::image type="content" source="./media/odbc-driver/odbc-driver-user-dsn.png" alt-text="Administrador de fonte de dados da Azure Cosmos DB ODBC":::

## <a name="step-3-create-a-schema-definition-using-the-container-mapping-method"></a><a id="#container-mapping"></a>Passo 3: Criar uma definição de esquema utilizando o método de mapeamento do recipiente

Existem dois tipos de métodos de amostragem que pode utilizar: mapeamento de **contentores** ou **delimiters de mesa.** Uma sessão de amostragem pode utilizar ambos os métodos de amostragem, mas cada recipiente só pode utilizar um método específico de amostragem. Os passos abaixo criam um esquema para os dados em um ou mais recipientes utilizando o método de mapeamento do recipiente. Este método de amostragem recupera os dados na página de um recipiente para determinar a estrutura dos dados. Transpõe um recipiente para uma mesa do lado ODBC. Este método de amostragem é eficiente e rápido quando os dados num recipiente são homogéneos. Se um recipiente contiver dados heterogéneos, recomendamos que utilize o [método de mapeamento delimiters de mesa,](#table-mapping) pois fornece um método de amostragem mais robusto para determinar as estruturas de dados no recipiente. 

1. Depois de completar os passos 1-4 em [Connect to your Azure Cosmos database,](#connect)clique em **Schema Editor** na janela de **configuração DSN do controlador DB ODBC Azure Cosmos.**

    :::image type="content" source="./media/odbc-driver/odbc-driver-schema-editor.png" alt-text="Administrador de fonte de dados da Azure Cosmos DB ODBC":::
1. Na janela **do Editor Schema,** clique em **Criar Novo**.
    A janela **Generate Schema** exibe todos os contentores na conta DB do Azure Cosmos. 

1. Selecione um ou mais recipientes para amostrar e, em seguida, clique em **Amostra**. 

1. No **separador Design View,** a base de dados, o esquema e a tabela estão representados. Na vista de tabela, a digitalização apresenta o conjunto de propriedades associadas aos nomes das colunas (SQL Name, Source Name, etc.).
    Para cada coluna, pode modificar o nome SQL da coluna, o tipo SQL, comprimento SQL (se aplicável), Escala (se aplicável), Precisão (se aplicável) e Nulo.
    - Pode definir **a Coluna de Ocultação** como **verdadeira** se pretender excluir essa coluna dos resultados da consulta. Colunas marcadas Hide Column = verdade não são devolvidas para seleção e projeção, embora ainda sejam parte do esquema. Por exemplo, pode esconder todas as propriedades necessárias para o sistema Azure Cosmos, a partir de "_".
    - A coluna **de id** é o único campo que não pode ser ocultado, uma vez que é usado como a chave primária no esquema normalizado. 

1. Uma vez terminado de definir o esquema, clique em **'Guardar ficheiros',**  |  **Save**navegue no diretório para guardar o esquema e, em seguida, clique em **Guardar**.

1. Para utilizar este esquema com um DSN, abra a **janela de configuração DSN do controlador DB ODBC do Azure Cosmos** (através do Administrador de Fonte de Dados ODBC), clique em **Opções Avançadas**, e, em seguida, na caixa **de ficheiros Schema,** navegue para o esquema guardado. Guardar um ficheiro de esquema para um DSN existente modifica a ligação DSN ao âmbito dos dados e estrutura definidos por esquema.

## <a name="step-4-create-a-schema-definition-using-the-table-delimiters-mapping-method"></a><a id="table-mapping"></a>Passo 4: Criar uma definição de esquema utilizando o método de mapeamento delimiters de mesa

Existem dois tipos de métodos de amostragem que pode utilizar: mapeamento de **contentores** ou **delimiters de mesa.** Uma sessão de amostragem pode utilizar ambos os métodos de amostragem, mas cada recipiente só pode utilizar um método específico de amostragem. 

Os passos seguintes criam um esquema para os dados em um ou mais recipientes utilizando o método de mapeamento **delimiters de mesa.** Recomendamos que utilize este método de amostragem quando os seus recipientes contiverem dados heterogéneos. Pode utilizar este método para estender a amostragem a um conjunto de atributos e os seus valores correspondentes. Por exemplo, se um documento contiver uma propriedade "Tipo", pode estender a amostragem aos valores deste imóvel. O resultado final da amostragem seria um conjunto de tabelas para cada um dos valores do Tipo especificado. Por exemplo, Type = O carro produzirá uma mesa de carro enquanto o Tipo = Avião produziria uma tabela de avião.

1. Depois de completar os passos 1-4 em [Connect to your Azure Cosmos database,](#connect)clique em **Schema Editor** na janela de configuração DSN do controlador DB ODBC Azure Cosmos.

1. Na janela **do Editor Schema,** clique em **Criar Novo**.
    A janela **Generate Schema** exibe todos os contentores na conta DB do Azure Cosmos. 

1. Selecione um recipiente no separador **'Visualização** da amostra', na coluna **Definição de Mapeamento** para o recipiente, clique em **Editar**. Em seguida, na janela **Definição de Mapeamento,** selecione o método **Delimiters de tabela.** Em seguida, faça o seguinte:

    a. Na caixa **Atributos,** digite o nome de uma propriedade delimiter. Esta é uma propriedade no seu documento que pretende estender a amostragem para, por exemplo, a Cidade e a imprensa entrar. 

    b. Se quiser apenas estender a amostragem a determinados valores para o atributo que introduziu acima, selecione o atributo na caixa de seleção, introduza um valor na caixa **valor** (por exemplo, Seattle) e prima para introduzir. Pode continuar a adicionar vários valores para atributos. Certifique-se apenas de que o atributo correto é selecionado quando estiver a introduzir valores.

    Por exemplo, se incluir um valor **de Atributos** de City, e quiser limitar a sua mesa a incluir apenas linhas com um valor de cidade de Nova Iorque e Dubai, entraria em City na caixa de Atributos, e Nova Iorque e depois Dubai na caixa **Values.**

1. Clique em **OK**. 

1. Depois de completar as definições de mapeamento dos recipientes que pretende experimentar, na janela **do Editor Schema,** clique em **Sample**.
     Para cada coluna, pode modificar o nome SQL da coluna, o tipo SQL, comprimento SQL (se aplicável), Escala (se aplicável), Precisão (se aplicável) e Nulo.
    - Pode definir **a Coluna de Ocultação** como **verdadeira** se pretender excluir essa coluna dos resultados da consulta. Colunas marcadas Hide Column = verdade não são devolvidas para seleção e projeção, embora ainda sejam parte do esquema. Por exemplo, pode esconder todas as propriedades necessárias para o sistema Azure Cosmos. `_`
    - A coluna **de id** é o único campo que não pode ser ocultado, uma vez que é usado como a chave primária no esquema normalizado. 

1. Uma vez terminado de definir o esquema, clique em **'Guardar ficheiros',**  |  **Save**navegue no diretório para guardar o esquema e, em seguida, clique em **Guardar**.

1. De volta à janela de **configuração DSN do condutor DB ODBC do Azure Cosmos,** clique em **Opções Avançadas**. Em seguida, na caixa **de Ficheiros Schema,** navegue para o ficheiro de esquemas guardados e clique **em OK**. Clique **em OK** novamente para salvar o DSN. Isto salva o esquema que criaste para o DSN. 

## <a name="optional-set-up-linked-server-connection"></a>(Opcional) Configurar a ligação do servidor ligado

Pode consultar o Azure Cosmos DB do SQL Server Management Studio (SSMS) através da criação de uma ligação de servidor ligado.

1. Crie uma fonte de dados do sistema, conforme descrito no [Passo 2,](#connect)nomeado por exemplo `SDS Name` .

1. [Instale o SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) e ligue-se ao servidor. 

1. No editor de consulta SSMS, crie um objeto de servidor ligado `DEMOCOSMOS` para a fonte de dados com os seguintes comandos. `DEMOCOSMOS`Substitua-o pelo nome do seu servidor ligado e `SDS Name` pelo nome da fonte de dados do seu sistema.

    ```sql
    USE [master]
    GO
    
    EXEC master.dbo.sp_addlinkedserver @server = N'DEMOCOSMOS', @srvproduct=N'', @provider=N'MSDASQL', @datasrc=N'SDS Name'
    
    EXEC master.dbo.sp_addlinkedsrvlogin @rmtsrvname=N'DEMOCOSMOS', @useself=N'False', @locallogin=NULL, @rmtuser=NULL, @rmtpassword=NULL
    
    GO
    ```
    
Para ver o novo nome do servidor ligado, refresque a lista de Servidores Ligados.

:::image type="content" source="./media/odbc-driver/odbc-driver-linked-server-ssms.png" alt-text="Administrador de fonte de dados da Azure Cosmos DB ODBC":::

### <a name="query-linked-database"></a>Base de dados ligada à consulta

Para consultar a base de dados ligada, introduza uma consulta SSMS. Neste exemplo, a consulta seleciona a partir da tabela do recipiente denominado `customers` :

```sql
SELECT * FROM OPENQUERY(DEMOCOSMOS, 'SELECT *  FROM [customers].[customers]')
```

Execute a consulta. O resultado deve ser semelhante a este:

```
attachments/  1507476156    521 Bassett Avenue, Wikieup, Missouri, 5422   "2602bc56-0000-0000-0000-59da42bc0000"   2015-02-06T05:32:32 +05:00 f1ca3044f17149f3bc61f7b9c78a26df
attachments/  1507476156    167 Nassau Street, Tuskahoma, Illinois, 5998   "2602bd56-0000-0000-0000-59da42bc0000"   2015-06-16T08:54:17 +04:00 f75f949ea8de466a9ef2bdb7ce065ac8
attachments/  1507476156    885 Strong Place, Cassel, Montana, 2069       "2602be56-0000-0000-0000-59da42bc0000"   2015-03-20T07:21:47 +04:00 ef0365fb40c04bb6a3ffc4bc77c905fd
attachments/  1507476156    515 Barwell Terrace, Defiance, Tennessee, 6439     "2602c056-0000-0000-0000-59da42bc0000"   2014-10-16T06:49:04 +04:00      e913fe543490432f871bc42019663518
attachments/  1507476156    570 Ruby Street, Spokane, Idaho, 9025       "2602c156-0000-0000-0000-59da42bc0000"   2014-10-30T05:49:33 +04:00 e53072057d314bc9b36c89a8350048f3
```

> [!NOTE]
> O servidor DB cosmos ligado não suporta o nome de quatro partes. Um erro é devolvido semelhante à seguinte mensagem:

```
Msg 7312, Level 16, State 1, Line 44

Invalid use of schema or catalog for OLE DB provider "MSDASQL" for linked server "DEMOCOSMOS". A four-part name was supplied, but the provider does not expose the necessary interfaces to use a catalog or schema.
``` 

## <a name="optional-creating-views"></a>(Opcional) Criar pontos de vista
Pode definir e criar pontos de vista como parte do processo de amostragem. Estas vistas são equivalentes às vistas SQL. São apenas de leitura e são âmbito das seleções e projeções da consulta Azure Cosmos DB SQL definida. 

Para criar uma vista para os seus dados, na janela **Do Editor Schema,** na coluna **Ver Definições,** clique em **Adicionar** na linha do recipiente para amostrar. 

:::image type="content" source="./media/odbc-driver/odbc-driver-create-view.png" alt-text="Administrador de fonte de dados da Azure Cosmos DB ODBC":::


Em seguida, na janela **Ver Definições,** faça o seguinte:

1. Clique em **Novo,** insira um nome para a vista, por exemplo, EmployeesfromSeattleView e, em seguida, clique em **OK**.

1. Na janela **de visualização editar,** insira uma consulta DB Azure Cosmos. Esta deve ser uma [consulta Azure Cosmos DB SQL](./sql-query-getting-started.md), por `SELECT c.City, c.EmployeeName, c.Level, c.Age, c.Manager FROM c WHERE c.City = "Seattle"` exemplo, e depois clique **em OK**.

    :::image type="content" source="./media/odbc-driver/odbc-driver-create-view-2.png" alt-text="Administrador de fonte de dados da Azure Cosmos DB ODBC":::


Pode criar muitas vistas como quiser. Uma vez terminada a definição dos pontos de vista, pode então recolher amostras dos dados. 

## <a name="step-5-view-your-data-in-bi-tools-such-as-power-bi-desktop"></a>Passo 5: Ver os seus dados em ferramentas BI, como o Power BI Desktop

Pode utilizar o seu novo DSN para ligar ao Azure Cosmos DB com quaisquer ferramentas compatíveis com o ODBC - este passo mostra-lhe simplesmente como ligar-se ao Power BI Desktop e criar uma visualização Power BI.

1. Abra o Power BI Desktop.

1. Clique **em Obter Dados.**

    :::image type="content" source="./media/odbc-driver/odbc-driver-power-bi-get-data.png" alt-text="Administrador de fonte de dados da Azure Cosmos DB ODBC":::

1. Na janela **Obter Dados,** clique em **Other**  |  **Other ODBC**  |  **Connect**.

    :::image type="content" source="./media/odbc-driver/odbc-driver-power-bi-get-data-2.png" alt-text="Administrador de fonte de dados da Azure Cosmos DB ODBC":::

1. Na janela **ODBC,** selecione o nome de origem de dados que criou e, em seguida, clique em **OK**. Pode deixar as entradas em branco **das Opções Avançadas.**

   :::image type="content" source="./media/odbc-driver/odbc-driver-power-bi-get-data-3.png" alt-text="Administrador de fonte de dados da Azure Cosmos DB ODBC":::

1. No **Access uma fonte de dados utilizando uma janela do controlador ODBC,** selecione **Predefinido ou Personalizado** e, em seguida, clique em **Connect**. Não é necessário incluir as propriedades das **cordas de ligação credenciais.**

1. Na janela Do **Navegador,** no painel esquerdo, expanda a base de dados, o esquema e, em seguida, selecione a tabela. O painel de resultados inclui os dados utilizando o esquema que criou.

    :::image type="content" source="./media/odbc-driver/odbc-driver-power-bi-get-data-4.png" alt-text="Administrador de fonte de dados da Azure Cosmos DB ODBC":::

1. Para visualizar os dados no ambiente de trabalho Power BI, verifique a caixa em frente ao nome da tabela e, em seguida, clique em **Carregar**.

1. No Power BI Desktop, à esquerda, selecione o separador Dados :::image type="icon" source="./media/odbc-driver/odbc-driver-data-tab.png"::: para confirmar que os seus dados foram importados. 

1. Agora pode criar visuais utilizando o Power BI clicando no separador :::image type="icon" source="./media/odbc-driver/odbc-driver-report-tab.png"::: Relatório, clicando em **Novo Visual**e, em seguida, personalizando o seu azulejo. Para obter mais informações sobre a criação de visualizações no Power BI Desktop, consulte [os tipos de Visualização no Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-visualization-types-for-reports-and-q-and-a/). 

## <a name="troubleshooting"></a>Resolução de problemas

Se receber o seguinte erro, certifique-se de que os valores da Chave **anfitrião** e **do acesso** copiou o portal Azure no Passo [2](#connect) e que, em seguida, redoem. Utilize os botões de cópia à direita dos valores da Chave **anfitrião** e **do acesso** no portal Azure para copiar os valores isentos de erro.

```output
[HY000]: [Microsoft][Azure Cosmos DB] (401) HTTP 401 Authentication Error: {"code":"Unauthorized","message":"The input authorization token can't serve the request. Please check that the expected payload is built as per the protocol, and check the key being used. Server used the following payload to sign: 'get\ndbs\n\nfri, 20 jan 2017 03:43:55 gmt\n\n'\r\nActivityId: 9acb3c0d-cb31-4b78-ac0a-413c8d33e373"}
```


## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o Azure Cosmos DB, veja [Bem-vindo ao Azure Cosmos DB](introduction.md).