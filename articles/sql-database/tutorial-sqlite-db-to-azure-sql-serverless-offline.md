---
title: 'Tutorial: como migrar seu banco de dados SQLite para o banco de dados SQL sem servidor do Azure'
description: Saiba como executar uma migração offline do SQLite para o banco de dados SQL sem servidor do Azure usando Azure Data Factory.
services: sql-database
author: joplum
ms.author: joplum
manager: prda
ms.service: sql-database
ms.workload: data-services
ms.topic: article
ms.date: 01/08/2020
ms.openlocfilehash: c718daa4bc99bffd6fcfeb084299bed6682fe884
ms.sourcegitcommit: 5b073caafebaf80dc1774b66483136ac342f7808
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75780512"
---
# <a name="how-to-migrate-your-sqlite-database-to-azure-sql-database-serverless"></a>Como migrar seu banco de dados SQLite para o banco de dados SQL sem servidor do Azure
Para muitas pessoas, o SQLite fornece sua primeira experiência de bancos de dados e programação SQL. A inclusão em muitos sistemas operacionais e aplicativos populares torna o SQLite um dos mecanismos de banco de dados mais implantados e usados no mundo todo. E, como é provável que o primeiro mecanismo de banco de dados use muitas pessoas, muitas vezes isso pode acabar como uma parte central de projetos ou aplicativos. Nesses casos em que o projeto ou aplicativo ultrapassa a implementação inicial do SQLite, os desenvolvedores podem precisar migrar seus dados para um repositório de dados confiável e centralizado.

O banco de dados SQL sem servidor do Azure é uma camada de computação para bancos únicos de dados que dimensionam automaticamente a computação com base na demanda da carga de trabalho e cobra pela quantidade de computação usada por segundo. A camada de computação sem servidor também pausa automaticamente os bancos de dados durante períodos inativos quando apenas o armazenamento é cobrado e retoma automaticamente os bancos de dados quando a atividade retorna.

Depois de seguir as etapas abaixo, seu banco de dados será migrado para o banco de dados SQL sem servidor do Azure, permitindo que você disponibilize seu banco de dados para outros usuários ou aplicativos na nuvem e pague apenas pelo que usar, com alterações mínimas de código do aplicativo.

## <a name="prerequisites"></a>Pré-requisitos
- Uma assinatura do Azure
- Banco de dados SQLite2 ou SQLite3 que você deseja migrar
- Um ambiente do Windows
  - Se você não tiver um ambiente local do Windows, poderá usar uma VM do Windows no Azure para a migração. Mova e torne o arquivo de banco de dados SQLite disponível na VM usando arquivos e Gerenciador de Armazenamento do Azure.

## <a name="steps"></a>Passos

1. Provisione um novo banco de dados SQL do Azure na camada de computação sem servidor.

    ![captura de tela de portal do Azure mostrando o exemplo de provisionamento para o banco de dados SQL sem servidor do Azure](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/provision-serverless.png)

2. Verifique se você tem o arquivo de banco de dados SQLite disponível em seu ambiente Windows. Instale um driver ODBC do SQLite se você ainda não tiver um (há muitos disponíveis em software livre, por exemplo, http://www.ch-werner.de/sqliteodbc/).

3. Crie um DSN de sistema para o banco de dados. Certifique-se de usar o aplicativo de administrador de fonte de dados que corresponde à sua arquitetura de sistema (32 bits vs 64 bits). Você pode encontrar qual versão está executando nas configurações do sistema.

    - Abra o administrador de fonte de dados ODBC em seu ambiente.
    - Clique na guia DSN do sistema e clique em "Adicionar"
    - Selecione o conector ODBC do SQLite que você instalou e dê à conexão um nome significativo, por exemplo, sqlitemigrationsource
    - Defina o nome do banco de dados para o arquivo. DB
    - Salvar e sair

4. Baixe e instale o Integration Runtime de hospedagem interna. A maneira mais fácil de fazer isso é a opção de instalação expressa, conforme detalhado na documentação. Se você optar por uma instalação manual, precisará fornecer ao aplicativo uma chave de autenticação, que pode ser localizada em sua instância de Data Factory:

    - Iniciando o ADF (criar e monitorar a partir do serviço no portal do Azure)
    - Clique na guia "autor" (lápis azul) à esquerda
    - Clique em conexões (inferior à esquerda) e em tempos de execução de integração
    - Adicione novo Integration Runtime auto-hospedado, dê a ele um nome, selecione a *opção 2*.

5. Crie um novo serviço vinculado para o banco de dados SQLite de origem em seu Data Factory.

    ![captura de tela mostrando a folha de serviços vinculados vazio no Azure Data Factory](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/linked-services-create.png)

6. Em conexões, em serviço vinculado, clique em novo

7. Pesquise e selecione o conector "ODBC"


    ![captura de tela mostrando o logotipo do conector ODBC na folha serviços vinculados no Azure Data Factory](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/linked-services-odbc.png)

8. Dê ao serviço vinculado um nome significativo, por exemplo, "sqlite_odbc". Selecione o tempo de execução de integração na lista suspensa "conectar via tempo de execução de integração". Insira o abaixo na cadeia de conexão, substituindo a variável de catálogo inicial pelo caminho de arquivo. db e o DSN pelo nome da conexão DSN do sistema: 

    ```
    Connection string: Provider=MSDASQL.1;Persist Security Info=False;Mode=ReadWrite;Initial Catalog=C:\sqlitemigrationsource.db;DSN=sqlitemigrationsource
    ```

9. Definir o tipo de autenticação como anônimo

10. Testar a ligação

    ![captura de tela mostrando conexão bem-sucedida no Azure Data Factory](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/linked-services-test-successful.png)

11. Crie outro serviço vinculado para seu destino SQL sem servidor. Selecione o banco de dados usando o assistente de serviço vinculado e forneça as credenciais de autenticação do SQL.

    ![captura de tela mostrando o banco de dados SQL do Azure selecionado no Azure Data Factory](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/linked-services-create-target.png)

12. Extraia as instruções CREATE TABLE do seu banco de dados SQLite. Você pode fazer isso executando o script do Python abaixo em seu arquivo de banco de dados.

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

13. Crie as tabelas de aterrissagem no ambiente de destino SQL sem servidor copiando as instruções CREATE TABLE do arquivo Createtables. SQL e executando as instruções SQL no editor de consultas no portal do Azure.

14. Volte para a tela inicial do seu Data Factory e clique em "Copiar Dados" para executar o assistente de criação de trabalho.

    ![captura de tela mostrando o logotipo do assistente de Copiar Dados no Azure Data Factory](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/copy-data.png)

15. Selecione todas as tabelas do banco de dados SQLite de origem usando as caixas de seleção e mapeie-as para as tabelas de destino no SQL do Azure. Após a execução do trabalho, você migrou com êxito os dados do SQLite para o SQL do Azure!

## <a name="next-steps"></a>Passos seguintes

- Para começar, consulte [início rápido: criar um banco de dados individual no banco de dados SQL do Azure usando o portal do Azure](sql-database-single-database-get-started.md).
- Para limites de recursos, consulte [limites de recursos da camada de computação sem servidor](sql-database-vCore-resource-limits-single-databases.md#general-purpose---serverless-compute---gen5).
