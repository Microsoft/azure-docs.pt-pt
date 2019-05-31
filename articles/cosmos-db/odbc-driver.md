---
title: Ligar ao Azure Cosmos DB com ferramentas de análise de BI
description: Saiba como utilizar o controlador ODBC do Azure Cosmos DB para criar tabelas e vistas, de modo a que dados normalizados podem ser visualizados no software de análise de dados e BI.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: sngun
ms.openlocfilehash: 352cd23f00e911b895e52aacaced1bfba38f7f84
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/28/2019
ms.locfileid: "66257258"
---
# <a name="connect-to-azure-cosmos-db-using-bi-analytics-tools-with-the-odbc-driver"></a>Ligar ao Azure Cosmos DB com ferramentas de análise de BI com o controlador ODBC

O controlador ODBC do Azure Cosmos DB permite-lhe ligar ao Azure Cosmos DB com ferramentas de análise de BI como o SQL Server Integration Services, o Power BI Desktop e o Tableau, para que possa analisar e criar visualizações dos dados do Azure Cosmos DB dessas soluções.

O controlador ODBC do Azure Cosmos DB é compatível com o ODBC 3.8 e suporta a sintaxe ANSI SQL-92. O controlador disponibiliza funcionalidades avançadas para ajudá-lo renormalizar os dados no Azure Cosmos DB. Com o controlador, pode representar os dados no Azure Cosmos DB como tabelas e vistas. O controlador permite-lhe efetuar operações de SQL em tabelas e vistas, incluindo agrupar por consultas, inserções, atualizações e eliminações.

> [!NOTE]
> Ligar ao Azure Cosmos DB com o controlador ODBC é atualmente suportado para apenas para contas de API de SQL do Azure Cosmos DB.

## <a name="why-do-i-need-to-normalize-my-data"></a>Por que motivo é necessário normalizar os meus dados?
O Azure Cosmos DB é uma base de dados sem esquemas, o que permite o desenvolvimento rápido de aplicativos e a capacidade de iterar sobre modelos de dados sem a ser limitado a um esquema restrito. Uma base de dados do Azure Cosmos DB pode conter de várias estruturas de documentos JSON. Isso é ótimo para desenvolvimento rápido de aplicativos, mas quando pretende analisar e criar relatórios dos seus dados através de análise de dados e ferramentas de BI, os dados, muitas vezes, tem de ser simplificado e a aderir a um esquema específico.

É onde entra o controlador ODBC. Ao utilizar o controlador ODBC, agora pode renormalizar dados no Azure Cosmos DB em tabelas e vistas que se adequam às suas análises de dados e necessidades de relatórios. Os esquemas renormalized não tem nenhum impacto nos dados subjacentes e não confinar os desenvolvedores a cumprir a eles. Em vez disso, eles permitem-lhe tirar partido de ferramentas compatível com ODBC para acessar os dados. Então, agora a base de dados do Azure Cosmos DB não só será um favorito para a sua equipa de desenvolvimento, mas os analistas de dados vão adorá-lo demasiado.

Vamos começar com o controlador ODBC.

## <a id="install"></a>Passo 1: Instalar o controlador ODBC do Azure Cosmos DB

1. Transfira os controladores para o seu ambiente:

    | Instalador | Sistemas operativos suportados| 
    |---|---| 
    |[Microsoft Azure Cosmos DB ODBC 64-bit.msi](https://aka.ms/cosmos-odbc-64x64) para Windows de 64 bits| versões de 64 bits do Windows 8.1 ou posterior, Windows 8, Windows 7, Windows Server 2012 R2, Windows Server 2012 e Windows Server 2008 R2.| 
    |[Microsoft Azure Cosmos DB ODBC 32 x 64-bit.msi](https://aka.ms/cosmos-odbc-32x64) de 32 bits no Windows de 64 bits| versões de 64 bits do Windows 8.1 ou posterior, Windows 8, Windows 7, Windows XP, Windows Vista, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 e Windows Server 2003.| 
    |[Microsoft Azure Cosmos DB ODBC 32-bit.msi](https://aka.ms/cosmos-odbc-32x32) para Windows de 32 bits|as versões de 32 bits do Windows 8.1 ou posterior, Windows 8, Windows 7, Windows XP e Windows Vista.|

    Execute o ficheiro de msi localmente, que inicia o **instalação de Driver Assistente do Microsoft Azure Cosmos DB ODBC**. 

1. Conclua o Assistente de instalação usando o Portão de entrada para instalar o controlador ODBC.

1. Abra o **administrador da origem de dados de ODBC** aplicação no seu computador. Pode fazê-lo, escrevendo **origens de dados de ODBC** no Windows caixa de pesquisa. 
    Pode confirmar o driver foi instalado clicando a **Drivers** separador e garantir **o controlador de ODBC do Microsoft Azure Cosmos DB** está listado.

    ![Administrador de fonte de dados ODBC do Azure Cosmos DB](./media/odbc-driver/odbc-driver.png)

## <a id="connect"></a>Passo 2: Ligar à base de dados do Azure Cosmos DB

1. Após [instalar o controlador ODBC do Azure Cosmos DB](#install), na **administrador de fonte de dados de ODBC** janela, clique em **Add**. Pode criar um utilizador ou o DSN de sistema. Neste exemplo, está a criar um DSN de utilizador.

1. Na **criar nova origem de dados** janela, selecione **o controlador de ODBC do Microsoft Azure Cosmos DB**e, em seguida, clique em **concluir**.

1. Na **configuração do Azure Cosmos DB ODBC Driver SDN** janela, preencha as seguintes informações: 

    ![Janela de configuração de DSN de controladores de ODBC do DB Cosmos do Azure](./media/odbc-driver/odbc-driver-dsn-setup.png)
    - **Nome da origem de dados**: O nome amigável para o DSN ODBC. Este nome é exclusivo para a sua conta do Azure Cosmos DB, portanto, um nome adequadamente se tiver várias contas.
    - **Descrição**: Uma breve descrição da origem de dados.
    - **Host**: URI para a sua conta do Azure Cosmos DB. Pode obter isso da página de chaves do Azure Cosmos DB no portal do Azure, conforme mostrado na captura de ecrã seguinte. 
    - **Chave de acesso**: A chave primária ou secundária, leitura e escrita ou só de leitura da página de chaves do Azure Cosmos DB no portal do Azure, conforme mostrado na captura de ecrã seguinte. Recomendamos que utilize a chave só de leitura se o DSN é utilizado para processamento de dados só de leitura e de relatórios.
    ![Página do Azure Cosmos DB chaves](./media/odbc-driver/odbc-driver-keys.png)
    - **Encriptar a chave de acesso para**: Selecione a melhor opção com base nos utilizadores desta máquina. 
    
1. Clique nas **teste** para verificar se pode ligar a sua conta do Azure Cosmos DB. 

1. Clique em **opções avançadas** e defina os seguintes valores:
    - **Consultar de consistência**: Selecione o [nível de consistência](consistency-levels.md) das suas operações. A predefinição é de sessão.
    - **Número de tentativas**: Introduza o número de vezes a repetir uma operação, se o pedido inicial não for concluída devido a limitação de velocidade de serviço.
    - **Ficheiro de esquema**: Tem várias opções aqui.
        - Por padrão, deixando esta entrada como está (em branco), o driver analisa a primeira página de dados para todas as coleções determinar o esquema de cada coleção. Isso é conhecido como mapeamento de coleção. Sem um ficheiro de esquema definido, o controlador tem de executar a análise para cada sessão de controlador e pode resultar num tempo de inicialização mais elevado de um aplicativo usando o DSN. Recomendamos que sempre associar um ficheiro de esquema para um DSN.
        - Se já tiver um ficheiro de esquema (possivelmente um que criou utilizando o Editor de esquema), pode clicar **navegue**, navegue para o ficheiro, clique em **guardar**e, em seguida, clique em **OK**.
        - Se quiser criar um novo esquema, clique em **OK**e, em seguida, clique em **Editor de esquema** na janela principal. Em seguida, avance para as informações do Editor de esquema. Depois de criar o novo ficheiro de esquema, não se esqueça de voltar para o **opções avançadas** janela para incluir o ficheiro de esquema recém-criado.

1. Depois de concluir e feche o **configuração do Azure Cosmos DB ODBC Driver de DSN** janela, o novo utilizador DSN é adicionada à guia DSN de utilizador.

    ![Novo Azure Cosmos DB ODBC DSN no separador de DSN de utilizador](./media/odbc-driver/odbc-driver-user-dsn.png)

## <a id="#collection-mapping"></a>Passo 3: Criar uma definição de esquema usando o método de mapeamento de coleção

Existem dois tipos de métodos de amostragem, que pode utilizar: **mapeamento da coleção** ou **delimitadores de tabela**. Uma sessão de amostragem pode utilizar ambos os métodos de amostragem, mas cada coleção só pode utilizar um método de amostragem específicos. Os passos abaixo criam um esquema para os dados num ou mais coleções usando o método de mapeamento de coleção. Este método de amostragem obtém os dados na página de uma coleção para determinar a estrutura dos dados. Ele transposes uma coleção para uma tabela no lado do ODBC. Este método de amostragem é rápido e eficiente quando os dados de uma coleção são homogéneo. Se uma coleção contiver heterogéneo tipo de dados, recomendamos que utilize o [delimitadores de tabela de mapeamento de método](#table-mapping) pois fornece um método de amostragem mais robusto para determinar as estruturas de dados na coleção. 

1. Depois de concluir os passos 1 a 4 na [ligar à base de dados do Azure Cosmos DB](#connect), clique em **esquema Editor** no **configuração do Azure Cosmos DB ODBC Driver de DSN** janela.

    ![Botão de editor de esquema na janela de configuração de DSN do Driver de ODBC do Azure Cosmos DB](./media/odbc-driver/odbc-driver-schema-editor.png)
1. Na **Editor de esquema** janela, clique em **criar nova**.
    O **gerar esquema** janela exibe todas as coleções na conta do Azure Cosmos DB. 

1. Selecione uma ou mais coleções de exemplo e, em seguida, clique em **exemplo**. 

1. Na **modo de exibição Design** guia, a base de dados, esquemas e tabela são representados. Na vista de tabela, a análise apresenta o conjunto de propriedades associadas com os nomes das colunas (nome de SQL, nome de origem, etc.).
    Para cada coluna, pode modificar o nome SQL da coluna, o tipo SQL, o comprimento SQL (se aplicável), dimensionamento (se aplicável), precisão (se aplicável) e Nullable.
    - Pode definir **Ocultar coluna** ao **verdadeiro** se pretende excluir essa coluna nos resultados da consulta. Colunas marcado Ocultar coluna = true não são devolvidos para seleção e da projeção, embora eles ainda fazem parte do esquema. Por exemplo, pode ocultar todas as propriedades de sistema necessário do Azure Cosmos DB, começando com "_".
    - O **id** coluna é o único campo que não é possível ocultar como é usada como a chave primária no esquema normalizado. 

1. Assim que tiver concluído a definição do esquema, clique em **arquivo** | **guardar**, navegue para o diretório para guardar o esquema e, em seguida, clique em **guardar**.

1. Para usar esse esquema com um DSN, abra a **janela de configuração do Azure Cosmos DB ODBC Driver DSN** (via a origem de administrador de dados ODBC), clique em **opções avançadas**e, em seguida, no **doficheirodeesquema** caixa, navegue para o esquema guardado. Guardar um ficheiro de esquema um DSN existente modifica a ligação de DSN ao âmbito para os dados e uma estrutura definida pelo esquema.

## <a id="table-mapping"></a>Passo 4: Criar uma definição de esquema com os delimitadores de tabela de mapeamento de método

Existem dois tipos de métodos de amostragem, que pode utilizar: **mapeamento da coleção** ou **delimitadores de tabela**. Uma sessão de amostragem pode utilizar ambos os métodos de amostragem, mas cada coleção só pode utilizar um método de amostragem específicos. 

Os passos seguintes criam um esquema para os dados num ou mais coleções com o **delimitadores de tabela** mapeamento de método. Recomendamos que utilize este método de amostragem quando suas coleções contêm heterogêneo tipo de dados. Pode utilizar este método para definir o âmbito a amostragem para um conjunto de atributos e seus valores correspondentes. Por exemplo, se um documento contém uma propriedade "Type", pode definir o âmbito a amostragem para os valores dessa propriedade. O resultado final a amostragem seria um conjunto de tabelas para cada um dos valores para o tipo que especificou. Por exemplo, digite = carro produzirá uma tabela de carro ao tipo = plano produziria uma tabela de plano.

1. Depois de concluir os passos 1 a 4 na [ligar à base de dados do Azure Cosmos DB](#connect), clique em **Editor de esquema** na janela de configuração de DSN do Driver de ODBC do Azure Cosmos DB.

1. Na **Editor de esquema** janela, clique em **criar nova**.
    O **gerar esquema** janela exibe todas as coleções na conta do Azure Cosmos DB. 

1. Selecione uma coleção no **vista de exemplo** separador a **definição de mapeamento** coluna para a coleção, clique em **editar**. Em seguida, no **definição de mapeamento** janela, selecione **delimitadores de tabela** método. Em seguida, faça o seguinte:

    a. Na **atributos** , escreva o nome de uma propriedade de delimitador. Esta é uma propriedade no documento que pretende definir o âmbito a amostragem para, por exemplo, cidade e prima enter. 

    b. Se pretender apenas definir o âmbito a amostragem para determinados valores para o atributo introduzido acima, selecione o atributo na caixa de seleção, introduza um valor na **valor** caixa (por exemplo, Seattle) e, prima introduza. Pode continuar a adicionar vários valores de atributos. Certifique-se apenas de que o atributo correto está selecionado quando deverá introduzir valores.

    Por exemplo, se incluir um **atributos** valor de cidade e queira limitar sua tabela para incluir apenas linhas com um valor de cidade de nova York e Dubai, introduziria cidade na caixa de atributos e Nova Iorque e, em seguida, Dubai no **Valores** caixa.

1. Clique em **OK**. 

1. Depois de concluir as definições de mapeamento para as coleções que pretende, além de exemplo da **Editor de esquema** janela, clique em **exemplo**.
     Para cada coluna, pode modificar o nome SQL da coluna, o tipo SQL, o comprimento SQL (se aplicável), dimensionamento (se aplicável), precisão (se aplicável) e Nullable.
    - Pode definir **Ocultar coluna** ao **verdadeiro** se pretende excluir essa coluna nos resultados da consulta. Colunas marcado Ocultar coluna = true não são devolvidos para seleção e da projeção, embora eles ainda fazem parte do esquema. Por exemplo, pode ocultar todas as propriedades de sistema necessário do Azure Cosmos DB a partir do `_`.
    - O **id** coluna é o único campo que não é possível ocultar como é usada como a chave primária no esquema normalizado. 

1. Assim que tiver concluído a definição do esquema, clique em **arquivo** | **guardar**, navegue para o diretório para guardar o esquema e, em seguida, clique em **guardar**.

1. De volta a **configuração do Azure Cosmos DB ODBC Driver de DSN** janela, clique em **opções avançadas**. Em seguida, na **arquivo do esquema** caixa, navegue para o ficheiro de esquema guardada e clique em **OK**. Clique em **OK** novamente para guardar o DSN. Isso salva o esquema que criou o DSN. 

## <a name="optional-set-up-linked-server-connection"></a>(Opcional) Configurar a ligação ao servidor ligado

Pode consultar o Azure Cosmos DB do SQL Server Management Studio (SSMS) ao configurar uma ligação de servidor ligado.

1. Crie uma origem de dados do sistema, conforme descrito em [passo 2](#connect), com nome, por exemplo `SDS Name`.

1. [Instalar o SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) e ligar ao servidor. 

1. No editor de consulta do SSMS, criar um objeto servidor ligado `DEMOCOSMOS` da origem de dados com os seguintes comandos. Substitua `DEMOCOSMOS` com o nome do servidor ligado, e `SDS Name` com o nome da sua origem de dados do sistema.

    ```sql
    USE [master]
    GO
    
    EXEC master.dbo.sp_addlinkedserver @server = N'DEMOCOSMOS', @srvproduct=N'', @provider=N'MSDASQL', @datasrc=N'SDS Name'
    
    EXEC master.dbo.sp_addlinkedsrvlogin @rmtsrvname=N'DEMOCOSMOS', @useself=N'False', @locallogin=NULL, @rmtuser=NULL, @rmtpassword=NULL
    
    GO
    ```
    
Para ver o novo nome de servidor ligado, atualize a lista de servidores vinculados.

![Servidor ligado no SSMS](./media/odbc-driver/odbc-driver-linked-server-ssms.png)

### <a name="query-linked-database"></a>Consultar base de dados ligado

Para consultar a base de dados ligado, introduza uma consulta do SSMS. Neste exemplo, a consulta seleciona da tabela na coleção com o nome `customers`:

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
> O servidor do Cosmos DB ligado não suporta a nomenclatura de quatro partes. É devolvido um erro semelhante à mensagem seguinte:

```
Msg 7312, Level 16, State 1, Line 44

Invalid use of schema or catalog for OLE DB provider "MSDASQL" for linked server "DEMOCOSMOS". A four-part name was supplied, but the provider does not expose the necessary interfaces to use a catalog or schema.
``` 

## <a name="optional-creating-views"></a>(Opcional) Criar vistas
Pode definir e criar vistas como parte do processo de amostragem. Estas vistas são equivalentes às vistas SQL. Eles são só de leitura e são escopo, as seleções e projeções da consulta de SQL do Azure Cosmos DB definida. 

Para criar uma vista para os seus dados, na **Editor de esquema** janela, na **definições de exibição** coluna, clique em **Add** na linha da coleção ao exemplo. 
    ![Criar uma vista de dados](./media/odbc-driver/odbc-driver-create-view.png)


Em seguida, no **definições de exibição** janela, efetue o seguinte procedimento:

1. Clique em **New**, introduza um nome para a vista, por exemplo, EmployeesfromSeattleView e, em seguida, clique em **OK**.

1. Na **Editar vista** janela, introduza uma consulta do Azure Cosmos DB. Tem de ser um [consulta SQL do Azure Cosmos DB](how-to-sql-query.md), por exemplo `SELECT c.City, c.EmployeeName, c.Level, c.Age, c.Manager FROM c WHERE c.City = "Seattle"`e, em seguida, clique em **OK**.

    ![Adicionar consulta ao criar uma vista](./media/odbc-driver/odbc-driver-create-view-2.png)


Pode criar um vários modos de exibição como desejar. Quando tiver terminado definir as vistas, pode, em seguida, apresentar exemplos dos dados. 

## <a name="step-5-view-your-data-in-bi-tools-such-as-power-bi-desktop"></a>Passo 5: Ver os seus dados em ferramentas de BI como o Power BI Desktop

Pode usar o seu novo DSN para ligar ao Azure Cosmos DB com ferramentas compatível com ODBC - este passo simplesmente mostra-lhe como ligar ao Power BI Desktop e criar uma visualização do Power BI.

1. Abra o Power BI Desktop.

1. Clique em **obter dados**.

    ![Obter dados no Power BI Desktop](./media/odbc-driver/odbc-driver-power-bi-get-data.png)

1. Na **obter dados** janela, clique em **outros** | **ODBC** | **Connect**.

    ![Escolher origem de dados de ODBC em obter dados do Power BI](./media/odbc-driver/odbc-driver-power-bi-get-data-2.png)

1. Na **de ODBC** janela, selecione a origem de dados nome que criou e, em seguida, clique em **OK**. Pode deixar a **opções avançadas** entradas em branco.

    ![Escolha o nome da origem de dados (DSN) em obter dados do Power BI](./media/odbc-driver/odbc-driver-power-bi-get-data-3.png)

1. Na **aceder a uma origem de dados usando um controlador ODBC** janela, selecione **predefinido ou personalizado** e, em seguida, clique em **Connect**. Não é necessário incluir o **propriedades de cadeia de ligação de credenciais**.

1. Na **navegador** janela, no painel esquerdo, expanda a base de dados, o esquema e, em seguida, selecione a tabela. O painel de resultados inclui os dados usando o esquema que criou.

    ![Selecione a tabela no Power BI para que a dados](./media/odbc-driver/odbc-driver-power-bi-get-data-4.png)

1. Para visualizar os dados no Power BI desktop, selecione a caixa à frente o nome da tabela e, em seguida, clique em **carga**.

1. No Power BI Desktop, à esquerda, selecione o separador de dados ![Separador de dados no Power BI Desktop](./media/odbc-driver/odbc-driver-data-tab.png) para confirmar os seus dados foi importado.

1. Agora, pode criar elementos visuais com o Power BI ao clicar no separador de relatório ![separador de relatório no Power BI Desktop](./media/odbc-driver/odbc-driver-report-tab.png), clicando em **novo Visual**e, em seguida, personalizando o seu mosaico. Para obter mais informações sobre a criação de visualizações no Power BI Desktop, veja [tipos de visualização no Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-visualization-types-for-reports-and-q-and-a/).

## <a name="troubleshooting"></a>Resolução de problemas

Se receber o seguinte erro, certifique-se a **Host** e **chave de acesso** valores que copiou do portal do Azure no [passo 2](#connect) estão corretos e tente novamente. Utilize os botões de cópia à direita dos **Host** e **chave de acesso** valores no portal do Azure para copiar o erro de valores gratuito.

    [HY000]: [Microsoft][Azure Cosmos DB] (401) HTTP 401 Authentication Error: {"code":"Unauthorized","message":"The input authorization token can't serve the request. Please check that the expected payload is built as per the protocol, and check the key being used. Server used the following payload to sign: 'get\ndbs\n\nfri, 20 jan 2017 03:43:55 gmt\n\n'\r\nActivityId: 9acb3c0d-cb31-4b78-ac0a-413c8d33e373"}`

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre o Azure Cosmos DB, veja [Bem-vindo ao Azure Cosmos DB](introduction.md).
