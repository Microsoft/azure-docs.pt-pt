---
title: 'Tutorial: Como migrar a sua base de dados SQLite para a Azure SQL Database sem servidor'
description: Aprenda a realizar uma migração offline de SQLite para Azure SQL Database sem servidor usando Azure Data Factory.
services: sql-database
author: joplum
ms.author: joplum
ms.service: sql-database
ms.workload: data-services
ms.topic: tutorial
ms.date: 01/08/2020
ms.custom: sqldbrb=1
ms.openlocfilehash: e2f240247cbba0f80254d504792df45be55c6a1b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92790411"
---
# <a name="how-to-migrate-your-sqlite-database-to-azure-sql-database-serverless"></a>Como migrar a sua base de dados SQLite para o Azure SQL Database sem servidor
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Para muitas pessoas, a SQLite proporciona a sua primeira experiência de bases de dados e programação SQL. A sua inclusão em muitos sistemas operativos e aplicações populares faz da SQLite um dos motores de base de dados mais implantados e usados no mundo. E como é provavelmente o primeiro motor de base de dados que muitas pessoas usam, muitas vezes pode acabar como parte central de projetos ou aplicações. Nos casos em que o projeto ou aplicação ultrapassa a implementação inicial da SQLite, os desenvolvedores podem precisar de migrar os seus dados para uma loja de dados centralizada e fiável.

Azure SQL Database Serverless é um nível de cálculo para bases de dados únicas que escala automaticamente o cálculo com base na procura de carga de trabalho, e faturas para a quantidade de cálculo usado por segundo. O nível de computação sem servidor também coloca automaticamente em pausa as bases de dados durante períodos inativos quando apenas o armazenamento é faturado e retoma automaticamente as bases de dados quando a atividade é retomada.

Depois de seguir os passos abaixo, a sua base de dados será migrada para o Azure SQL Database Serverless, permitindo-lhe disponibilizar a sua base de dados a outros utilizadores ou aplicações na nuvem e apenas pagar pelo que utiliza, com alterações mínimas no código de aplicação.

## <a name="prerequisites"></a>Pré-requisitos

- Uma Subscrição do Azure
- Base de dados SQLite2 ou SQLite3 que deseja migrar
- Um ambiente windows
  - Se não tiver um ambiente Windows local, pode utilizar um Windows VM em Azure para a migração. Mova e disponibilize o seu ficheiro de base de dados SQLite no VM utilizando ficheiros Azure e Explorador de Armazenamento.

## <a name="steps"></a>Passos

1. Provisionar uma nova Base de Dados Azure SQL no nível de computação serverless.

    ![Screenshot do portal Azure mostrando o exemplo de provisionamento para Azure SQL Database Serverless](./media/migrate-sqlite-db-to-azure-sql-serverless-offline-tutorial/provision-serverless.png)

2. Certifique-se de que tem o seu ficheiro de base de dados SQLite disponível no seu ambiente Windows. Instale um Controlador ODBC SQLite se ainda não tiver um (existem muitos disponíveis em Open Source, por exemplo, http://www.ch-werner.de/sqliteodbc/) .

3. Crie um Sistema DSN para a base de dados. Certifique-se de que utiliza a aplicação Data Source Administrator que corresponde à arquitetura do seu sistema (32 bit vs 64-bit). Pode encontrar qual a versão que está a executar nas definições do seu sistema.

    - Abra o administrador de fonte de dados ODBC no seu ambiente.
    - Clique no separador DSN do sistema e clique em "Adicionar"
    - Selecione o conector SQLite ODBC que instalou e dê à ligação um nome significativo, por exemplo, sqlitemigrationsource
    - Desave o nome da base de dados no ficheiro .db
    - Salvar e sair

4. Descarregue e instale o tempo de execução de integração auto-hospedado. A forma mais fácil de o fazer é a opção de instalação do Expresso, conforme detalhado na documentação. Se optar por uma instalação manual, terá de fornecer à aplicação uma chave de autenticação, que pode ser localizada na sua instância data Factory por:

    - Arranque da ADF (Autor e Monitor a partir do serviço no portal Azure)
    - Clique no separador "Autor" (lápis azul) à esquerda
    - Clique em Conexões (inferior à esquerda), em seguida, integração tempos de execução
    - Adicione novos Self-Hosted Prazo de Integração, dê-lhe um nome, selecione *Opção 2*.

5. Crie um novo serviço ligado para a base de dados SQLite de origem na sua Fábrica de Dados.

    ![Screenshot mostrando lâmina de serviços ligados vazios na Fábrica de Dados Azure](./media/migrate-sqlite-db-to-azure-sql-serverless-offline-tutorial/linked-services-create.png)

6. Em **Conexões**, em **Serviço Linked**, clique em **New**.

7. Procure e selecione o conector "ODBC"

   ![Screenshot mostrando o logotipo do conector ODBC na lâmina de serviços ligado na Azure Data Factory](./media/migrate-sqlite-db-to-azure-sql-serverless-offline-tutorial/linked-services-odbc.png)

8. Dê ao serviço ligado um nome significativo, por exemplo, "sqlite_odbc". Selecione o seu tempo de integração a partir do dropdown "Connect via integration". Introduza o seguinte na cadeia de ligação, substituindo a variável Catálogo Inicial pelo ficheiro .db e o DSN com o nome da ligação DSN do sistema:

   ```
   Connection string: Provider=MSDASQL.1;Persist Security Info=False;Mode=ReadWrite;Initial Catalog=C:\sqlitemigrationsource.db;DSN=sqlitemigrationsource
    ```

9. Desavista o tipo de autenticação para Anónimo

10. Testar a ligação

    ![Screenshot mostrando conexão bem sucedida na Azure Data Factory](./media/migrate-sqlite-db-to-azure-sql-serverless-offline-tutorial/linked-services-test-successful.png)

11. Crie outro serviço ligado para o seu alvo SERVERLESS SQL. Selecione a base de dados utilizando o assistente de serviço ligado e forneça as credenciais de autenticação SQL.

    ![Screenshot mostrando Azure SQL Base de dados selecionado na Azure Data Factory](./media/migrate-sqlite-db-to-azure-sql-serverless-offline-tutorial/linked-services-create-target.png)

12. Extrair as declarações de tabela CREATE da sua base de dados SQLite. Pode fazê-lo executando o script python abaixo no seu ficheiro de base de dados.

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

13. Crie as tabelas de aterragem no seu ambiente alvo sem servidor SQL copiando as declarações de tabela CREATE a partir do ficheiro CreateTables.sql e executando as declarações SQL no Editor de Consulta no portal Azure.

14. Volte ao ecrã principal da sua Data Factory e clique em "Copiar dados" para passar pelo assistente de criação de emprego.

    ![Screenshot mostrando o logotipo do assistente de dados de cópia na Fábrica de Dados Azure](./media/migrate-sqlite-db-to-azure-sql-serverless-offline-tutorial/copy-data.png)

15. Selecione todas as tabelas da base de dados origem SQLite utilizando as caixas de verificação e mapee-as para as tabelas-alvo em Azure SQL. Uma vez executado o trabalho, você conseguiu migrar os seus dados da SQLite para Azure SQL!

## <a name="next-steps"></a>Passos seguintes

- Para começar, consulte [Quickstart: Crie uma única base de dados na Base de Dados Azure SQL utilizando o portal Azure](single-database-create-quickstart.md).
- Para obter limites de recursos, consulte [os limites de recursos de nível de cálculo serverless](./resource-limits-vcore-single-databases.md#general-purpose---serverless-compute---gen5).