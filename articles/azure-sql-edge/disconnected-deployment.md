---
title: Implementar borda Azure SQL com Docker - Azure SQL Edge
description: Saiba mais sobre a implantação do Azure SQL Edge com o Docker
keywords: SQL Edge, recipiente, estivador
services: sql-edge
ms.service: sql-edge
ms.topic: quickstart
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: fce098767fffd36376399bbd9396699e3d9fbfd3
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93392083"
---
# <a name="deploy-azure-sql-edge-with-docker"></a>Implementar borda Azure SQL com Docker

Neste arranque rápido, você usa Docker para puxar e executar a imagem do recipiente Azure SQL Edge. Em seguida, conecte-se com **sqlcmd** para criar a sua primeira base de dados e executar consultas.

Esta imagem é constituída por Azure SQL Edge com base no Ubuntu 18.04. Pode ser utilizado com o Motor Docker 1.8+ no Linux ou no Docker para Mac/Windows.

## <a name="prerequisites"></a>Pré-requisitos

- Docker Engine 1.8+ em qualquer distribuição de Linux ou Docker suportado para Mac/Windows. Para mais informações, consulte [instalar o Docker.](https://docs.docker.com/engine/installation/) Uma vez que as imagens Azure SQL Edge são baseadas em Ubuntu 18.04, recomenda-se que utilize um anfitrião de estivador Ubuntu 18.04.
- Docker **sobrepõe-se ao** condutor de armazenamento2. Este é o padrão para a maioria dos utilizadores. Se descobrir que não está a utilizar este fornecedor de armazenamento e precisa de ser alterado, consulte as instruções e avisos na documentação do [estivador para configurar a sobreposição2](https://docs.docker.com/storage/storagedriver/overlayfs-driver/#configure-docker-with-the-overlay-or-overlay2-storage-driver).
- Mínimo de 10 GB de espaço em disco.
- Mínimo de 1 GB de RAM.
- [Requisitos de hardware para Azure SQL Edge](./features.md#hardware-support).


## <a name="pull-and-run-the-container-image"></a>Puxe e corra a imagem do recipiente

Antes de iniciar os seguintes passos, certifique-se de que selecionou a sua concha preferida (bash, PowerShell ou cmd) no topo deste artigo.

1. Retire a imagem do recipiente Azure SQL Edge do Registo do Contentor da Microsoft.

    - Puxe a imagem do recipiente Azure SQL Edge
        ```bash
        sudo docker pull mcr.microsoft.com/azure-sql-edge:latest 
        ```

> [!NOTE]
> Para os comandos de bash neste artigo `sudo` é usado. Nas janelas & macOS, pode não ser necessário sudo. No Linux, se não quiser usar o sudo para executar o Docker, pode configurar um grupo de estivadores e adicionar utilizadores a esse grupo. Para obter mais informações, consulte [os passos de pós-instalação para Linux.](https://docs.docker.com/engine/install/linux-postinstall/)

O comando anterior retira as imagens mais recentes do contentor Azure SQL Edge. Para ver todas as imagens disponíveis, consulte [a página do hub azure-sql-egde Docker](https://hub.docker.com/_/microsoft-azure-sql-edge).

2. Para executar a imagem do recipiente com Docker, pode utilizar o seguinte comando a partir de uma casca de bash (Linux/macOS) ou de uma pronta de comando PowerShell elevada.
    
    - Inicie um exemplo de Azure SQL Edge em execução como a edição do Desenvolvedor
        ```bash
        sudo docker run --cap-add SYS_PTRACE -e 'ACCEPT_EULA=1' -e 'MSSQL_SA_PASSWORD=yourStrong(!)Password' -p 1433:1433 --name azuresqledge -d mcr.microsoft.com/azure-sql-edge
        ```

    - Inicie um exemplo de Azure SQL Edge a decorrer como a edição Premium
        ```bash
        sudo docker run --cap-add SYS_PTRACE -e 'ACCEPT_EULA=1' -e 'MSSQL_SA_PASSWORD=yourStrong(!)Password' -e 'MSSQL_PID=Premium' -p 1433:1433 --name azuresqledge -d mcr.microsoft.com/azure-sql-edge
        ```
    > [!NOTE]
    > Se estiver a utilizar o PowerShell no Windows para executar estes comandos, utilize cotações duplas em vez de cotações individuais.


    > [!NOTE]
    > A palavra-passe deve seguir a política de senha padrão do Microsoft SQL Database Engine, caso contrário o recipiente não pode configurar o motor SQL e deixará de funcionar. Por predefinição, a palavra-passe deve ter pelo menos 8 caracteres de comprimento e conter caracteres de três dos quatro conjuntos seguintes: Letras maiúsculas, letras minúsculas, dígitos base 10 e símbolos. Pode examinar o registo de erro executando o comando de registos de [estivadores.](https://docs.docker.com/engine/reference/commandline/logs/)
    
    O quadro a seguir apresenta uma descrição dos parâmetros no `docker run` exemplo anterior:

    | Parâmetro | Descrição |
    |-----|-----|
    | **-e "ACCEPT_EULA=Y"** |  Desa esta **ACCEPT_EULA** variável a qualquer valor que confirme a sua aceitação do [Contrato de Licenciamento do Utilizador Final](https://go.microsoft.com/fwlink/?linkid=2139274). Regulação necessária para a imagem Azure SQL Edge. |
    | **-e "MSSQL_SA_PASSWORD=yourStrong(!) Palavra-passe"** | Especifique a sua própria senha forte que seja de pelo menos 8 caracteres e cumpra os [requisitos de senha Azure SQL Edge](/sql/relational-databases/security/password-policy). Regulação necessária para a imagem Azure SQL Edge. |
    | **-p 1433:1433** | Mapear uma porta TCP no ambiente de acolhimento (primeiro valor) com uma porta TCP no recipiente (segundo valor). Neste exemplo, a Azure SQL Edge está a ouvir no TCP 1433 no recipiente e esta está exposta à porta, 1433, no hospedeiro. |
    | **--nome azuresqledge** | Especifique um nome personalizado para o recipiente em vez de um gerado aleatoriamente. Se executar mais de um recipiente, não poderá reutilizar este mesmo nome. |
    | **-d** | Executar o recipiente ao fundo (daemon) |

    Para obter uma lista completa de todas as variáveis de ambiente Azure SQL Edge, consulte [Configure Azure SQL Edge com Variáveis ambientais](configure.md#configure-by-using-environment-variables). Também pode utilizar um [ficheiro mssql.conf](configure.md#configure-by-using-an-mssqlconf-file) para configurar recipientes de borda Azure SQL.

3. Para ver os seus contentores Docker, use o `docker ps` comando.
   
   ```bash
    sudo docker ps -a
   ```

4. Se a coluna **STATUS** mostrar um estado de **Up** , então a Borda Azure SQL está a funcionar no recipiente e a ouvir na porta especificada na coluna **PORTS.** Se a coluna **STATUS** do seu recipiente Azure SQL Edge mostrar **Saída** , consulte a secção de resolução de problemas da Documentação do Aresta Azure SQL.

    O `-h` parâmetro (nome hospedeiro) também é útil, mas não é usado neste tutorial para a simplicidade. Isto altera o nome interno do recipiente para um valor personalizado. Este é o nome que verá devolvido na seguinte consulta Transact-SQL:

    ```sql
    SELECT @@SERVERNAME,
        SERVERPROPERTY('ComputerNamePhysicalNetBIOS'),
        SERVERPROPERTY('MachineName'),
        SERVERPROPERTY('ServerName')
    ```

    Definir `-h` e pelo mesmo valor é uma boa forma de identificar `--name` facilmente o recipiente-alvo.

5. Como passo final, altere a sua palavra-passe SA porque `SA_PASSWORD` a é visível na saída e armazenada no ambiente `ps -eax` variável com o mesmo nome. Veja os degraus abaixo.

## <a name="change-the-sa-password"></a>Alterar a palavra-passe SA

A conta **SA** é um administrador de sistema na instância Azure SQL Edge que é criada durante a configuração. Depois de criar o seu recipiente Azure SQL Edge, a `MSSQL_SA_PASSWORD` variável ambiente especificada é detetável correndo `echo $SA_PASSWORD` no recipiente. Por razões de segurança, altere a sua palavra-passe SA.

1. Escolha uma palavra-passe forte para usar para o utilizador SA.

2. Utilize `docker exec` para executar **sqlcmd** para alterar a palavra-passe utilizando o Transact-SQL. No exemplo seguinte, substitua a senha `<YourStrong!Passw0rd>` antiga, e a nova senha, `<YourNewStrong!Passw0rd>` pelos seus próprios valores de senha.

   ```bash
   sudo docker exec -it azuresqledge /opt/mssql-tools/bin/sqlcmd \
      -S localhost -U SA -P "<YourStrong@Passw0rd>" \
      -Q 'ALTER LOGIN SA WITH PASSWORD="<YourNewStrong@Passw0rd>"'
   ```

## <a name="connect-to-azure-sql-edge"></a>Ligue-se à Borda Azure SQL

Os passos seguintes utilizam a ferramenta de linha de comando Azure SQL Edge, **sqlcmd,** dentro do recipiente para ligar ao Aresta SQL Azure.

> [!NOTE]
> a ferramenta sqlcmd não está disponível dentro da versão ARM64 dos recipientes SQL Edge.

1. Utilize o `docker exec -it` comando para iniciar uma casca de pancada interativa dentro do seu recipiente de funcionamento. No exemplo a seguir é o `azuresqledge` nome especificado pelo parâmetro quando criou o `--name` recipiente.

   ```bash
   sudo docker exec -it azuresqledge "bash"
   ```

2. Uma vez dentro do recipiente, conecte-se localmente com sqlcmd. Sqlcmd não está no caminho por defeito, por isso tem que especificar o caminho completo.

   ```bash
   /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P "<YourNewStrong@Passw0rd>"
   ```

   > [!TIP]
   > Pode omitir a palavra-passe na linha de comando a ser solicitada para a introduzir.

3. Se for bem sucedido, deverá chegar a um pedido de comando **sqlcmd:** `1>` .

## <a name="create-and-query-data"></a>Criar e consultar dados

As secções seguintes **acompanham-no utilizando sqlcmd** e Transact-SQL para criar uma nova base de dados, adicionar dados e executar uma consulta simples.

### <a name="create-a-new-database"></a>Criar uma nova base de dados

Os passos seguintes criam uma nova base de dados chamada `TestDB` .

1. A partir da solicitação de comando **sqlcmd,** cole o seguinte comando Transact-SQL para criar uma base de dados de teste:

   ```sql
   CREATE DATABASE TestDB
   Go
   ```

2. Na linha seguinte, escreva uma consulta para devolver o nome de todas as bases de dados no seu servidor:

   ```sql
   SELECT Name from sys.Databases
   Go
   ```

### <a name="insert-data"></a>Inserir dados

Em seguida, crie uma nova `Inventory` mesa, e insira duas novas linhas.

1. A partir do pedido de comando **sqlcmd,** altere o contexto para a nova `TestDB` base de dados:

   ```sql
   USE TestDB
   ```

2. Criar uma nova tabela chamada `Inventory` :

   ```sql
   CREATE TABLE Inventory (id INT, name NVARCHAR(50), quantity INT)
   ```

3. Inserir dados na nova tabela:

   ```sql
   INSERT INTO Inventory VALUES (1, 'banana', 150); INSERT INTO Inventory VALUES (2, 'orange', 154);
   ```

4. Digite `GO` para executar os comandos anteriores:

   ```sql
   GO
   ```

### <a name="select-data"></a>Selecionar dados

Agora, fazer uma consulta para devolver os dados da `Inventory` tabela.

1. A partir do pedido de comando **sqlcmd,** insira uma consulta que retorna as linhas da `Inventory` tabela onde a quantidade é superior a 152:

   ```sql
   SELECT * FROM Inventory WHERE quantity > 152;
   ```

2. Execute o comando:

   ```sql
   GO
   ```

### <a name="exit-the-sqlcmd-command-prompt"></a>Saia da solicitação de comando sqlcmd

1. Para terminar a sua sessão **de sqlcmd,** escreva: `QUIT`

   ```sql
   QUIT
   ```

2. Para sair do comando interativo no seu recipiente, escreva `exit` . O seu contentor continua a funcionar depois de sair da casca de choque interativa.

## <a name="connect-from-outside-the-container"></a>Conecte-se de fora do recipiente

Também pode ligar-se à instância Azure SQL Edge na sua máquina Docker a partir de qualquer ferramenta externa de Linux, Windows ou macOS que suporte ligações SQL. Para obter mais informações sobre a ligação a um recipiente SQL Edge a partir do exterior, consulte [Connect e Query Azure SQL Edge](connect.md).

## <a name="remove-your-container"></a>Retire o seu recipiente

Se pretender remover o recipiente Azure SQL Edge utilizado neste tutorial, execute os seguintes comandos:

```bash
sudo docker stop azuresqledge
sudo docker rm azuresqledge
```

> [!WARNING]
> Parar e remover um recipiente elimina permanentemente quaisquer dados do Azure SQL Edge no recipiente. Se necessitar de preservar os seus dados, [criar e copiar um ficheiro de reserva para fora do recipiente](backup-restore.md) ou utilizar uma técnica de persistência de dados de [contentores](configure.md#persist-your-data).

## <a name="next-steps"></a>Passos Seguintes

- [Machine Learning e Inteligência Artificial com ONNX em SQL Edge](onnx-overview.md).
- [Construção de uma solução IoT de ponta a ponta com aresta SQL utilizando IoT Edge](tutorial-deploy-azure-resources.md).
- [Streaming de dados em Azure SQL Edge](stream-data.md)