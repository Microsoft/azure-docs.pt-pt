---
title: 'Tutorial: Como migrar a sua base de dados SQLite para a Base de Dados Azure SQL sem servidor'
description: Aprenda a realizar uma migração offline de SQLite para Azure SQL Database sem servidor utilizando a Azure Data Factory.
services: sql-database
author: joplum
ms.author: joplum
manager: prda
ms.service: sql-database
ms.workload: data-services
ms.topic: article
ms.date: 01/08/2020
ms.custom: sqldbrb=1
ms.openlocfilehash: e72b75b39205d245351480d914ed26eeec8939f7
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84051333"
---
# <a name="how-to-migrate-your-sqlite-database-to-azure-sql-database-serverless"></a>Como migrar a sua base de dados SQLite para a Base de Dados Azure SQL sem servidor
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Para muitas pessoas, a SQLite fornece a sua primeira experiência de bases de dados e programação SQL. A sua inclusão em muitos sistemas operativos e aplicações populares faz da SQLite um dos motores de base de dados mais utilizados e utilizados do mundo. E como é provavelmente o primeiro motor de base de dados que muitas pessoas usam, muitas vezes pode acabar como uma parte central de projetos ou aplicações. Nos casos em que o projeto ou aplicação supera a implementação inicial do SQLite, os desenvolvedores podem precisar de migrar os seus dados para uma loja de dados fiável e centralizada.

O Azure SQL Database Serverless é um nível de cálculo para bases de dados únicas que escala automaticamente a computação com base na procura de carga de trabalho, e contas para a quantidade de computação usada por segundo. O nível de computação sem servidor também interrompe automaticamente as bases de dados durante períodos inativos quando apenas o armazenamento é faturado e retoma automaticamente as bases de dados quando a atividade retorna.

Depois de ter seguido os passos abaixo, a sua base de dados será migrada para o Azure SQL Database Serverless, permitindo-lhe disponibilizar a sua base de dados a outros utilizadores ou aplicações na nuvem e apenas pagar pelo que utiliza, com alterações mínimas de código de aplicação.

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição azure
- Base de dados SQLite2 ou SQLite3 que pretende migrar
- Um ambiente windows
  - Se não tiver um ambiente Windows local, pode utilizar um VM Windows em Azure para a migração. Mova e disponibilize o ficheiro de base de dados SQLite no VM utilizando ficheiros Azure e Explorador de Armazenamento.

## <a name="steps"></a>Passos

1. Fornecer uma nova Base de Dados Azure SQL no nível de computação Servidor.

    ![Screenshot do portal Azure mostrando exemplo de provisão para O Servidor de Base de Dados Azure SQL](./media/migrate-sqlite-db-to-azure-sql-serverless-offline-tutorial/provision-serverless.png)

2. Certifique-se de que tem o seu ficheiro de base de dados SQLite disponível no seu ambiente Windows. Instale um Controlador ODBC SQLite se ainda não tiver um (existem muitos disponíveis em Open Source, por exemplo, http://www.ch-werner.de/sqliteodbc/) .

3. Crie um Sistema DSN para a base de dados. Certifique-se de que utiliza a aplicação Data Source Administrator que corresponde à arquitetura do seu sistema (32 bits vs 64 bits). Pode encontrar qual a versão que está a executar nas definições do seu sistema.

    - Abra o Administrador de Fonte de Dados da ODBC no seu ambiente.
    - Clique no separador DSN do sistema e clique em "Adicionar"
    - Selecione o conector SQLite ODBC instalado e dê à ligação um nome significativo, por exemplo, sqlitemigrationsource
    - Desloque o nome da base de dados para o ficheiro .db
    - Salvar e sair

4. Descarregue e instale o tempo de execução de integração auto-hospedado. A forma mais fácil de o fazer é a opção de instalação do Expresso, conforme detalhado na documentação. Se optar por uma instalação manual, terá de fornecer à aplicação uma chave de autenticação, que pode estar localizada na sua instância Data Factory por:

    - Arranque da ADF (Autor e Monitor do serviço no portal Azure)
    - Clique no separador "Autor" (lápis azul) à esquerda
    - Clique em Ligações (inferior à esquerda), em seguida, tempos de integração
    - Adicione o novo Tempo de Funcionação de Integração Auto-Hospedada, dê-lhe um nome, selecione *Opção 2*.

5. Crie um novo serviço ligado para a base de dados SQLite de origem na sua Fábrica de Dados.

    ![Screenshot mostrando lâmina de serviços ligadas vazia na Fábrica de Dados Azure](./media/migrate-sqlite-db-to-azure-sql-serverless-offline-tutorial/linked-services-create.png)

6. Em **Conexões**, no **Linked Service,** clique em **Novo**.

7. Procure e selecione o conector "ODBC"

   ![Screenshot mostrando o logotipo do conector ODBC na lâmina de serviços ligados na Fábrica de Dados Azure](./media/migrate-sqlite-db-to-azure-sql-serverless-offline-tutorial/linked-services-odbc.png)

8. Dar ao serviço ligado um nome significativo, por exemplo, "sqlite_odbc". Selecione o seu tempo de execução de integração a partir do dropdown "Connect via integrationtime". Introduza a seguinte na cadeia de ligação, substituindo a variável Catálogo Inicial pelo ficheiro .db e o DSN com o nome da ligação DSN do sistema:

   ```
   Connection string: Provider=MSDASQL.1;Persist Security Info=False;Mode=ReadWrite;Initial Catalog=C:\sqlitemigrationsource.db;DSN=sqlitemigrationsource
    ```

9. Definir o tipo de autenticação para Anónimo

10. Testar a ligação

    ![Screenshot mostrando conexão bem sucedida na Fábrica de Dados Azure](./media/migrate-sqlite-db-to-azure-sql-serverless-offline-tutorial/linked-services-test-successful.png)

11. Crie outro serviço ligado para o seu alvo SQL sem servidor. Selecione a base de dados utilizando o assistente de serviço ligado e forneça as credenciais de autenticação SQL.

    ![Screenshot mostrando base de dados Azure SQL selecionada na Fábrica de Dados Azure](./media/migrate-sqlite-db-to-azure-sql-serverless-offline-tutorial/linked-services-create-target.png)

12. Extrai as declarações create TABLE da sua base de dados SQLite. Pode fazê-lo executando o script abaixo python no seu ficheiro de base de dados.

    ```
    #!/usr/bin/python
    import sqlite3
    conn = sqlite3.connect("sqlitemigrationsource.db")
    c = conn.cursor()

    print("Starting extract job..")
    with open('CreateTables.sql', 'w') as f:
        for tabledetails in c.execute("SELECT * FROM sqlite_master WHERE type='table'"):
            print("Extracting CREATE statement for " + (str(tabledetails[1])))
            print(tabledetails)
            f.write(str(tabledetails[4].replace('\n','') + ';\n'))
    c.close()
    ```

13. Crie as tabelas de aterragem no seu ambiente alvo SQL sem servidor, copiando as declarações da tabela CREATE Do ficheiro CreateTables.sql e executando as declarações SQL no Editor de Consulta no portal Azure.

14. Volte ao ecrã principal da sua Fábrica de Dados e clique em "Copiar Dados" para executar através do assistente de criação de emprego.

    ![Screenshot mostrando o logotipo do assistente de dados de cópia na Fábrica de Dados Azure](./media/migrate-sqlite-db-to-azure-sql-serverless-offline-tutorial/copy-data.png)

15. Selecione todas as tabelas da base de dados SQLite de origem utilizando as caixas de verificação e mapeie-as para as tabelas-alvo em Azure SQL. Uma vez que o trabalho esteja em execução, você tem migrado com sucesso os seus dados da SQLite para o Azure SQL!

## <a name="next-steps"></a>Próximos passos

- Para começar, consulte [Quickstart: Crie uma única base de dados na Base de Dados Azure SQL utilizando o portal Azure](single-database-create-quickstart.md).
- Para limites de recursos, consulte os limites de recursos de [nível de computação sem servidor](../../sql-database/sql-database-vcore-resource-limits-single-databases.md#general-purpose---serverless-compute---gen5).
