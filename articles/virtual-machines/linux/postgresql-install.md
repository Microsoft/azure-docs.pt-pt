---
title: Configurar PostgreSQL num Linux VM
description: Saiba como instalar e configurar postgreSQL numa máquina virtual Linux em Azure
author: cynthn
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: how-to
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/01/2016
ms.author: cynthn
ms.openlocfilehash: 4052a9c8614a17c3b5cdd871ad78be8cc3258c5a
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98202594"
---
# <a name="install-and-configure-postgresql-on-azure"></a>Instale e configuure PostgreSQL em Azure
PostgreSQL é uma base de dados avançada de código aberto semelhante à Oracle e DB2. Inclui funcionalidades prontas para a empresa, tais como a conformidade total do ACID, o processamento transacional fiável e o controlo de concuncy em várias versões. Também suporta padrões como ANSI SQL e SQL/MED (incluindo invólucros de dados estrangeiros para Oracle, MySQL, MongoDB, entre muitos outros). É altamente extensível com suporte para mais de 12 línguas processuais, índices GIN e GiST, suporte de dados espaciais, e múltiplas funcionalidades semelhantes ao NoSQL para json ou aplicações baseadas em valor-chave.

Neste artigo, você vai aprender a instalar e configurar PostgreSQL em uma máquina virtual Azure executando Linux.


## <a name="install-postgresql"></a>Instalar PostgresqL
> [!NOTE]
> Já deve ter uma máquina virtual Azure a funcionar linux para completar este tutorial. Para criar e configurar um Linux VM antes de prosseguir, consulte o [tutorial Azure Linux VM](quick-create-cli.md).
> 
> 

Neste caso, utilize a porta 1999 como porta PostgreSQL.  

Ligue-se ao Linux VM que criou via PuTTY. Se esta é a primeira vez que você está usando um Azure Linux VM, veja [Como Usar SSH com Linux on Azure](mac-create-ssh-keys.md) para aprender a usar PuTTY para ligar a um Linux VM.

1. Executar o seguinte comando para mudar para a raiz (administrador):

    ```console
    # sudo su -
    ```

2. Algumas distribuições têm dependências que deve instalar antes de instalar o PostgreSQL. Verifique o seu distro nesta lista e execute o comando apropriado:
   
   * Base do chapéu vermelho Linux:

        ```console
        # yum install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y
        ```

   * Debian base Linux:

        ```console
        # apt-get install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam libxslt-devel tcl-devel python-devel -y
        ```

   * SUSE Linux:

        ```console
        # zypper install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y
        ```

3. Descarregue PostgreSQL no diretório de raiz e, em seguida, desaperte o pacote:

    ```console
    # wget https://ftp.postgresql.org/pub/source/v9.3.5/postgresql-9.3.5.tar.bz2 -P /root/

    # tar jxvf  postgresql-9.3.5.tar.bz2
    ```

    O que precede é um exemplo. Pode encontrar o endereço de descarregamento mais detalhado no [Índice de /pub/fonte/](https://ftp.postgresql.org/pub/source/).

4. Para iniciar a construção, executar estes comandos:

    ```console
    # cd postgresql-9.3.5

    # ./configure --prefix=/opt/postgresql-9.3.5
    ```

5. Se quiser construir tudo o que possa ser construído, incluindo a documentação (HTML e páginas do homem) e módulos adicionais `contrib` (), em vez disso, executar o seguinte comando:

    ```console
    # gmake install-world
    ```

    Deverá receber a seguinte mensagem de confirmação:

    ```output
    PostgreSQL, contrib, and documentation successfully made. Ready to install.
    ```

## <a name="configure-postgresql"></a>Configurar postgresql
1. (Opcional) Criar uma ligação simbólica para encurtar a referência PostgreSQL para não incluir o número da versão:

    ```console
    # ln -s /opt/postgresql-9.3.5 /opt/pgsql
    ```

2. Criar um diretório para a base de dados:

    ```console
    # mkdir -p /opt/pgsql_data
    ```

3. Crie um utilizador sem raízes e modifique o perfil desse utilizador. Em seguida, mude para este novo utilizador (chamado *postgres* no nosso exemplo):

    ```console
    # useradd postgres
   
    # chown -R postgres.postgres /opt/pgsql_data
   
    # su - postgres
    ```
   
   > [!NOTE]
   > Por razões de segurança, o PostgreSQL utiliza um utilizador não-raiz para inicializar, iniciar ou desligar a base de dados.
   > 
   > 
4. Edite o ficheiro *bash_profile* introduzindo os comandos abaixo. Estas linhas serão adicionadas ao fim do ficheiro *bash_profile:*

    ```config
    cat >> ~/.bash_profile <<EOF
    export PGPORT=1999
    export PGDATA=/opt/pgsql_data
    export LANG=en_US.utf8
    export PGHOME=/opt/pgsql
    export PATH=\$PATH:\$PGHOME/bin
    export MANPATH=\$MANPATH:\$PGHOME/share/man
    export DATA=`date +"%Y%m%d%H%M"`
    export PGUSER=postgres
    alias rm='rm -i'
    alias ll='ls -lh'
    EOF
    ```

5. Execute o ficheiro *bash_profile:*

    ```console
    $ source .bash_profile
    ```

6. Validar a sua instalação utilizando o seguinte comando:

    ```console
    $ which psql
    ```

    Se a sua instalação for bem sucedida, verá a seguinte resposta:

    ```output
    /opt/pgsql/bin/psql
    ```

7. Também pode verificar a versão PostgreSQL:

    ```sql
    $ psql -V
    ```

8. Inicializar a base de dados:

    ```console
    $ initdb -D $PGDATA -E UTF8 --locale=C -U postgres -W
    ```

    Deverá receber a seguinte saída:

![Screenshot que mostra a saída depois de rubricar a base de dados.](./media/postgresql-install/no1.png)

## <a name="set-up-postgresql"></a>Configurar PostgreSQL
<!--    [postgres@ test ~]$ exit -->

Execute os seguintes comandos:

```console
# cd /root/postgresql-9.3.5/contrib/start-scripts

# cp linux /etc/init.d/postgresql
```

Modifique duas variáveis no ficheiro /etc/init.d/postgresql. O prefixo é definido para o caminho de instalação de PostgreSQL: **/opt/pgsql**. O PGDATA está definido para a trajetória de armazenamento de dados do PostgreSQL: **/opt/pgsql_data**.

```config
# sed -i '32s#usr/local#opt#' /etc/init.d/postgresql

# sed -i '35s#usr/local/pgsql/data#opt/pgsql_data#' /etc/init.d/postgresql
```

![Screenshot que mostra o prefixo de instalação e o diretório de dados.](./media/postgresql-install/no2.png)

Altere o ficheiro para torná-lo executável:

```console
# chmod +x /etc/init.d/postgresql
```

Iniciar postgresql:

```console
# /etc/init.d/postgresql start
```

Verifique se o ponto final do PostgreSQL está ligado:

```console
# netstat -tunlp|grep 1999
```

Deverá ver o seguinte resultado:

![A screenshot que mostra o ponto final do PostgreSQL está ligado.](./media/postgresql-install/no3.png)

## <a name="connect-to-the-postgres-database"></a>Ligue-se à base de dados postgres
Mude novamente para o utilizador de postgres:

```console
# su - postgres
```

Criar uma base de dados postgres:

```console
$ createdb events
```

Ligue-se à base de dados de eventos que acabou de criar:

```console
$ psql -d events
```

## <a name="create-and-delete-a-postgres-table"></a>Criar e apagar uma tabela postgres
Agora que ligou à base de dados, pode criar tabelas nela.

Por exemplo, criar uma nova tabela de exemplo Postgres utilizando o seguinte comando:

```sql
CREATE TABLE potluck (name VARCHAR(20),    food VARCHAR(30),    confirmed CHAR(1), signup_date DATE);
```

Criou agora uma tabela de quatro colunas com os seguintes nomes de colunas e restrições:

1. A coluna "nome" foi limitada pelo comando VARCHAR com menos de 20 caracteres.
2. A coluna "food" indica o item alimentar que cada pessoa trará. VARCHAR limita este texto a ter menos de 30 caracteres.
3. A coluna "confirmada" regista se a pessoa tem RSVP'd para o potluck. Os valores aceitáveis são "Y" e "N".
4. A coluna "data" mostra quando se inscreveram para o evento. Postgres requer que as datas sejam escritas como yyy-mm-dd.

Deve ver o seguinte se a sua mesa foi criada com sucesso:

![Screenshot que mostra a mensagem que aparece quando a sua tabela é criada com sucesso.](./media/postgresql-install/no4.png)

Também pode verificar a estrutura da tabela utilizando o seguinte comando:

![Screenshot que mostra o comando para verificar a sua estrutura de mesa.](./media/postgresql-install/no5.png)

### <a name="add-data-to-a-table"></a>Adicionar dados a uma tabela
Primeiro, insira a informação numa linha:

```sql
INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('John', 'Casserole', 'Y', '2012-04-11');
```

Deverá ver este resultado:

![Screenshot que mostra a informação de linha que acrescentou.](./media/postgresql-install/no6.png)

Pode adicionar mais algumas pessoas à mesa também. Aqui estão algumas opções, ou pode criar as suas próprias:

```sql
INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Sandy', 'Key Lime Tarts', 'N', '2012-04-14');

INSERT INTO potluck (name, food, confirmed, signup_date) VALUES ('Tom', 'BBQ','Y', '2012-04-18');

INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Tina', 'Salad', 'Y', '2012-04-18');
```

### <a name="show-tables"></a>Mostrar mesas
Utilize o seguinte comando para mostrar uma tabela:

```sql
select * from potluck;
```

O resultado é:

![Screenshot que mostra a saída do comando para mostrar uma tabela.](./media/postgresql-install/no7.png)

### <a name="delete-data-in-a-table"></a>Apagar dados numa tabela
Utilize o seguinte comando para eliminar dados numa tabela:

```sql
delete from potluck where name=’John’;
```

Isto elimina toda a informação na linha "John". O resultado é:

![image](./media/postgresql-install/no8.png)

### <a name="update-data-in-a-table"></a>Atualizar dados numa tabela
Utilize o seguinte comando para atualizar dados numa tabela. Para este, a Sandy confirmou que estão presentes, por isso vamos mudar o RSVP de "N" para "Y":

```sql
UPDATE potluck set confirmed = 'Y' WHERE name = 'Sandy';
```

## <a name="get-more-information-about-postgresql"></a>Obtenha mais informações sobre PostgreSQL
Agora que concluiu a instalação do PostgreSQL num Azure Linux VM, poderá desfrutar da sua utilização em Azure. Para saber mais sobre o PostgreSQL, visite o [site da PostgreSQL.](https://www.postgresql.org/)
