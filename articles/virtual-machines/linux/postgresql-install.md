---
title: Configurar o PostgreSQL em uma VM Linux
description: Saiba como instalar e configurar o PostgreSQL em uma máquina virtual do Linux no Azure
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: 1a747363-0cc5-4ba3-9be7-084dfeb04651
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/01/2016
ms.author: cynthn
ms.openlocfilehash: f6d521c7003583228990c80a90c1454821f584d3
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74035271"
---
# <a name="install-and-configure-postgresql-on-azure"></a>Instalar e configurar o PostgreSQL no Azure
PostgreSQL é um banco de dados avançado de código aberto semelhante ao Oracle e ao DB2. Ele inclui recursos prontos para a empresa, como conformidade total de ACID, processamento transacional confiável e controle de simultaneidade de várias versões. Ele também dá suporte a padrões como SQL ANSI e SQL/MED (incluindo wrappers de dados externos para Oracle, MySQL, MongoDB e muitos outros). Ele é altamente extensível com suporte para mais de 12 linguagens de procedimentos, iniciar e superíndicees, suporte a dados espaciais e vários recursos semelhantes a NoSQL para aplicativos JSON ou com valor de chave.

Neste artigo, você aprenderá a instalar e configurar o PostgreSQL em uma máquina virtual do Azure que executa o Linux.

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="install-postgresql"></a>Instalar o PostgreSQL
> [!NOTE]
> Você já deve ter uma máquina virtual do Azure executando o Linux para concluir este tutorial. Para criar e configurar uma VM do Linux antes de continuar, consulte o [tutorial da VM Linux do Azure](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
> 

Nesse caso, use a porta 1999 como a porta PostgreSQL.  

Conecte-se à VM Linux criada por meio de uma recriação. Se esta for a primeira vez que você está usando uma VM Linux do Azure, consulte [como usar SSH com Linux no Azure](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para saber como usar a reutilização para se conectar a uma VM do Linux.

1. Execute o seguinte comando para alternar para a raiz (admin):
   
        # sudo su -
2. Algumas distribuições têm dependências que você deve instalar antes de instalar o PostgreSQL. Verifique o distribuição nesta lista e execute o comando apropriado:
   
   * Linux Red Hat base:
     
           # yum install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y  
   * Linux base Debian:
     
            # apt-get install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam libxslt-devel tcl-devel python-devel -y  
   * SUSE Linux:
     
           # zypper install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y  
3. Baixe o PostgreSQL no diretório raiz e descompacte o pacote:
   
        # wget https://ftp.postgresql.org/pub/source/v9.3.5/postgresql-9.3.5.tar.bz2 -P /root/
   
        # tar jxvf  postgresql-9.3.5.tar.bz2
   
    A seguir está um exemplo. Você pode encontrar o endereço de download mais detalhado no [índice de/pub/Source/](https://ftp.postgresql.org/pub/source/).
4. Para iniciar a compilação, execute estes comandos:
   
        # cd postgresql-9.3.5
   
        # ./configure --prefix=/opt/postgresql-9.3.5
5. Se você quiser compilar tudo que possa ser compilado, incluindo a documentação (páginas HTML e Man) e módulos adicionais (contrib), execute o seguinte comando em vez disso:
   
        # gmake install-world
   
    Você deve receber a seguinte mensagem de confirmação:
   
        PostgreSQL, contrib, and documentation successfully made. Ready to install.

## <a name="configure-postgresql"></a>Configurar PostgreSQL
1. Adicional Crie um link simbólico para encurtar a referência do PostgreSQL para não incluir o número de versão:
   
        # ln -s /opt/postgresql-9.3.5 /opt/pgsql
2. Crie um diretório para o banco de dados:
   
        # mkdir -p /opt/pgsql_data
3. Crie um usuário não raiz e modifique o perfil do usuário. Em seguida, alterne para esse novo usuário (chamado *postgres* em nosso exemplo):
   
        # useradd postgres
   
        # chown -R postgres.postgres /opt/pgsql_data
   
        # su - postgres
   
   > [!NOTE]
   > Por motivos de segurança, o PostgreSQL usa um usuário não raiz para inicializar, iniciar ou desligar o banco de dados.
   > 
   > 
4. Edite o arquivo *bash_profile* inserindo os comandos abaixo. Essas linhas serão adicionadas ao final do arquivo de *bash_profile* :
   
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
5. Execute o arquivo de *bash_profile* :
   
        $ source .bash_profile
6. Valide a instalação usando o seguinte comando:
   
        $ which psql
   
    Se a instalação for bem-sucedida, você verá a seguinte resposta:
   
        /opt/pgsql/bin/psql
7. Você também pode verificar a versão do PostgreSQL:
   
        $ psql -V

8. Inicialize o banco de dados:
   
        $ initdb -D $PGDATA -E UTF8 --locale=C -U postgres -W
   
    Você deve receber a seguinte saída:

![image](./media/postgresql-install/no1.png)

## <a name="set-up-postgresql"></a>Configurar PostgreSQL
<!--    [postgres@ test ~]$ exit -->

Execute os seguintes comandos:

    # cd /root/postgresql-9.3.5/contrib/start-scripts

    # cp linux /etc/init.d/postgresql

Modifique duas variáveis no arquivo/etc/init.d/PostgreSQL. O prefixo é definido como o caminho de instalação do PostgreSQL: **/opt/pgsql**. PGDATA é definido como o caminho de armazenamento de dados de PostgreSQL: **/opt/pgsql_data**.

    # sed -i '32s#usr/local#opt#' /etc/init.d/postgresql

    # sed -i '35s#usr/local/pgsql/data#opt/pgsql_data#' /etc/init.d/postgresql

![image](./media/postgresql-install/no2.png)

Altere o arquivo para torná-lo executável:

    # chmod +x /etc/init.d/postgresql

Start PostgreSQL:

    # /etc/init.d/postgresql start

Verifique se o ponto de extremidade do PostgreSQL está em:

    # netstat -tunlp|grep 1999

Deverá ver o resultado seguinte:

![image](./media/postgresql-install/no3.png)

## <a name="connect-to-the-postgres-database"></a>Conectar-se ao banco de dados Postgres
Alterne para o usuário postgres mais uma vez:

    # su - postgres

Criar um banco de dados Postgres:

    $ createdb events

Conecte-se ao banco de dados de eventos que você acabou de criar:

    $ psql -d events

## <a name="create-and-delete-a-postgres-table"></a>Criar e excluir uma tabela postgres
Agora que você se conectou ao banco de dados, você pode criar tabelas nele.

Por exemplo, crie um novo exemplo de tabela postgres usando o seguinte comando:

    CREATE TABLE potluck (name VARCHAR(20),    food VARCHAR(30),    confirmed CHAR(1), signup_date DATE);

Agora você configurou uma tabela de quatro colunas com os seguintes nomes de coluna e restrições:

1. A coluna "Name" foi limitada pelo comando VARCHAR com menos de 20 caracteres.
2. A coluna "comida" indica o item de alimentos que cada pessoa trará. VARCHAR limita esse texto a menos de 30 caracteres.
3. A coluna "confirmada" registra se a pessoa tem um RSVP para a festa americana. Os valores aceitáveis são "Y" e "N".
4. A coluna "data" mostra quando eles se inscreveram para o evento. Postgres exige que as datas sejam gravadas como aaaa-mm-dd.

Você deverá ver o seguinte se a tabela tiver sido criada com êxito:

![image](./media/postgresql-install/no4.png)

Você também pode verificar a estrutura da tabela usando o seguinte comando:

![image](./media/postgresql-install/no5.png)

### <a name="add-data-to-a-table"></a>Adicionar dados a uma tabela
Primeiro, insira as informações em uma linha:

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('John', 'Casserole', 'Y', '2012-04-11');

Você deve ver esta saída:

![image](./media/postgresql-install/no6.png)

Você também pode adicionar mais algumas pessoas à tabela. Aqui estão algumas opções ou você pode criar suas próprias:

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Sandy', 'Key Lime Tarts', 'N', '2012-04-14');

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES ('Tom', 'BBQ','Y', '2012-04-18');

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Tina', 'Salad', 'Y', '2012-04-18');

### <a name="show-tables"></a>Mostrar tabelas
Use o seguinte comando para mostrar uma tabela:

    select * from potluck;

A saída é:

![image](./media/postgresql-install/no7.png)

### <a name="delete-data-in-a-table"></a>Excluir dados de uma tabela
Use o seguinte comando para excluir dados em uma tabela:

    delete from potluck where name=’John’;

Isso exclui todas as informações na linha "João". A saída é:

![image](./media/postgresql-install/no8.png)

### <a name="update-data-in-a-table"></a>Atualizar dados em uma tabela
Use o comando a seguir para atualizar dados em uma tabela. Para isso, arenoso confirmou que eles estão participando, portanto, alteraremos o RSVP de "N" para "Y":

     UPDATE potluck set confirmed = 'Y' WHERE name = 'Sandy';


## <a name="get-more-information-about-postgresql"></a>Obter mais informações sobre PostgreSQL
Agora que você concluiu a instalação do PostgreSQL em uma VM Linux do Azure, você pode aproveitar o uso dele no Azure. Para saber mais sobre o PostgreSQL, visite o [site do PostgreSQL](https://www.postgresql.org/).

