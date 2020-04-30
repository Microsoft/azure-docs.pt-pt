---
title: Configurar postgreSQL em um Linux VM
description: Saiba como instalar e configurar postgreSQL numa máquina virtual Linux em Azure
author: cynthn
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/01/2016
ms.author: cynthn
ms.openlocfilehash: d86e42dcc16d108cc82c9d245c7919145cef365f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81759345"
---
# <a name="install-and-configure-postgresql-on-azure"></a>Instale e configure postgreSQL no Azure
PostgreSQL é uma base de dados avançada de código aberto semelhante à Oracle e DB2. Inclui funcionalidades prontas para a empresa, tais como a conformidade total com o ACID, o processamento transacional fiável e o controlo de condivisões multi-versões. Também suporta normas como ANSI SQL e SQL/MED (incluindo invólucros de dados estrangeiros para oracle, MySQL, MongoDB, e muitos outros). É altamente extensível com suporte para mais de 12 línguas processuais, índices GIN e GiST, suporte de dados espaciais e múltiplas funcionalidades semelhantes a NoSQL para JSON ou aplicações baseadas em valor-chave.

Neste artigo, você aprenderá a instalar e configurar postgreSQL numa máquina virtual Azure que executa o Linux.


## <a name="install-postgresql"></a>Instalar postgresQL
> [!NOTE]
> Você já deve ter uma máquina virtual Azure executando Linux para completar este tutorial. Para criar e criar um VM Linux antes de prosseguir, consulte o [tutorial Azure Linux VM](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
> 

Neste caso, utilize a porta 1999 como porta PostgreSQL.  

Ligue-se ao Linux VM que criou via PuTTY. Se esta é a primeira vez que está a usar um VM Azure Linux, veja [como usar o SSH com o Linux no Azure](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para aprender a usar o PuTTY para se ligar a um VM Linux.

1. Executar o seguinte comando para mudar para a raiz (administrador):
   
        # sudo su -
2. Algumas distribuições têm dependências que deve instalar antes de instalar o PostgreSQL. Verifique a sua distro nesta lista e execute o comando apropriado:
   
   * Base de chapéu vermelho Linux:
     
           # yum install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y  
   * Debian base Linux:
     
            # apt-get install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam libxslt-devel tcl-devel python-devel -y  
   * SUSE Linux:
     
           # zypper install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y  
3. Baixe postgreSQL no diretório raiz e, em seguida, desaperte o pacote:
   
        # wget https://ftp.postgresql.org/pub/source/v9.3.5/postgresql-9.3.5.tar.bz2 -P /root/
   
        # tar jxvf  postgresql-9.3.5.tar.bz2
   
    O que precede é um exemplo. Pode encontrar o endereço de descarregamento mais detalhado no [Índice de /pub/fonte/](https://ftp.postgresql.org/pub/source/).
4. Para iniciar a construção, executar estes comandos:
   
        # cd postgresql-9.3.5
   
        # ./configure --prefix=/opt/postgresql-9.3.5
5. Se quiser construir tudo o que possa ser construído, incluindo a documentação (HTML e páginas masculinas) e módulos adicionais (),`contrib`execute o seguinte comando em vez disso:
   
        # gmake install-world
   
    Deve receber a seguinte mensagem de confirmação:
   
        PostgreSQL, contrib, and documentation successfully made. Ready to install.

## <a name="configure-postgresql"></a>Configure PostgreSQL
1. (Opcional) Crie um link simbólico para encurtar a referência postgresQL para não incluir o número da versão:
   
        # ln -s /opt/postgresql-9.3.5 /opt/pgsql
2. Crie um diretório para a base de dados:
   
        # mkdir -p /opt/pgsql_data
3. Crie um utilizador não-raiz e modifique o perfil desse utilizador. Em seguida, mude para este novo utilizador (chamado *postgres* no nosso exemplo):
   
        # useradd postgres
   
        # chown -R postgres.postgres /opt/pgsql_data
   
        # su - postgres
   
   > [!NOTE]
   > Por razões de segurança, o PostgreSQL utiliza um utilizador sem raiz para inicializar, iniciar ou desligar a base de dados.
   > 
   > 
4. Editar o ficheiro *bash_profile* inserindo os comandos abaixo. Estas linhas serão adicionadas ao fim do ficheiro *bash_profile:*
   
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
5. Executar o ficheiro *bash_profile:*
   
        $ source .bash_profile
6. Valide a sua instalação utilizando o seguinte comando:
   
        $ which psql
   
    Se a sua instalação for bem sucedida, verá a seguinte resposta:
   
        /opt/pgsql/bin/psql
7. Também pode verificar a versão PostgreSQL:
   
        $ psql -V

8. Inicializar a base de dados:
   
        $ initdb -D $PGDATA -E UTF8 --locale=C -U postgres -W
   
    Deve receber a seguinte saída:

![image](./media/postgresql-install/no1.png)

## <a name="set-up-postgresql"></a>Configurar postgreSQL
<!--    [postgres@ test ~]$ exit -->

Execute os seguintes comandos:

    # cd /root/postgresql-9.3.5/contrib/start-scripts

    # cp linux /etc/init.d/postgresql

Modificar duas variáveis no ficheiro /etc/init.d/postgresql. O prefixo está definido para o caminho de instalação do PostgreSQL: **/opt/pgsql**. O PGDATA está definido para o caminho de armazenamento de dados do PostgreSQL: **/opt/pgsql_data**.

    # sed -i '32s#usr/local#opt#' /etc/init.d/postgresql

    # sed -i '35s#usr/local/pgsql/data#opt/pgsql_data#' /etc/init.d/postgresql

![image](./media/postgresql-install/no2.png)

Altere o ficheiro para torná-lo executável:

    # chmod +x /etc/init.d/postgresql

Iniciar PostgreSQL:

    # /etc/init.d/postgresql start

Verifique se o ponto final do PostgreSQL está ligado:

    # netstat -tunlp|grep 1999

Deverá ver o resultado seguinte:

![image](./media/postgresql-install/no3.png)

## <a name="connect-to-the-postgres-database"></a>Ligue-se à base de dados Postgres
Mude para o utilizador de postgres mais uma vez:

    # su - postgres

Criar uma base de dados Postgres:

    $ createdb events

Ligue-se à base de dados de eventos que acabou de criar:

    $ psql -d events

## <a name="create-and-delete-a-postgres-table"></a>Criar e eliminar uma tabela Postgres
Agora que ligou à base de dados, pode criar tabelas.

Por exemplo, criar uma nova tabela de Postgres utilizando o seguinte comando:

    CREATE TABLE potluck (name VARCHAR(20),    food VARCHAR(30),    confirmed CHAR(1), signup_date DATE);

Criou agora uma tabela de quatro colunas com os seguintes nomes e restrições de colunas:

1. A coluna "nome" foi limitada pelo comando VARCHAR com menos de 20 caracteres.
2. A coluna "comida" indica o alimento que cada pessoa trará. O VARCHAR limita este texto a ter menos de 30 caracteres.
3. A coluna "confirmada" regista se a pessoa tem RSVP'd para o potluck. Os valores aceitáveis são "Y" e "N".
4. A coluna "data" mostra quando se inscreveram para o evento. Postgres requer que as datas sejam escritas como yyy-mm-dd.

Deve ver o seguinte se a sua mesa foi criada com sucesso:

![image](./media/postgresql-install/no4.png)

Também pode verificar a estrutura da tabela utilizando o seguinte comando:

![image](./media/postgresql-install/no5.png)

### <a name="add-data-to-a-table"></a>Adicionar dados a uma tabela
Primeiro, insira a informação numa linha:

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('John', 'Casserole', 'Y', '2012-04-11');

Deverá ver este resultado:

![image](./media/postgresql-install/no6.png)

Pode adicionar mais algumas pessoas à mesa também. Aqui estão algumas opções, ou pode criar as suas próprias:

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Sandy', 'Key Lime Tarts', 'N', '2012-04-14');

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES ('Tom', 'BBQ','Y', '2012-04-18');

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Tina', 'Salad', 'Y', '2012-04-18');

### <a name="show-tables"></a>Mostrar mesas
Utilize o seguinte comando para mostrar uma tabela:

    select * from potluck;

O resultado é:

![image](./media/postgresql-install/no7.png)

### <a name="delete-data-in-a-table"></a>Eliminar dados numa tabela
Utilize o seguinte comando para eliminar dados numa tabela:

    delete from potluck where name=’John’;

Isto elimina toda a informação na linha "John". O resultado é:

![image](./media/postgresql-install/no8.png)

### <a name="update-data-in-a-table"></a>Atualizar dados em uma tabela
Utilize o seguinte comando para atualizar os dados numa tabela. Para este, Sandy confirmou que estão presentes, por isso vamos mudar o RSVP de "N" para "Y":

     UPDATE potluck set confirmed = 'Y' WHERE name = 'Sandy';


## <a name="get-more-information-about-postgresql"></a>Obtenha mais informações sobre postgreSQL
Agora que concluiu a instalação do PostgreSQL num Azure Linux VM, pode desfrutar de usá-lo em Azure. Para saber mais sobre postgreSQL, visite o [site postgreSQL](https://www.postgresql.org/).

