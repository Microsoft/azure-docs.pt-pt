---
title: Configurar o PostgreSQL numa VM do Linux | Documentos da Microsoft
description: Saiba como instalar e configurar o PostgreSQL numa máquina virtual Linux no Azure
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: 1a747363-0cc5-4ba3-9be7-084dfeb04651
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/01/2016
ms.author: cynthn
ms.openlocfilehash: 76f1ddeebb173bf19b15753d12e4374e6365bf8f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57998986"
---
# <a name="install-and-configure-postgresql-on-azure"></a>Instalar e configurar o PostgreSQL no Azure
PostgreSQL é um código-fonte aberto banco de dados avançado semelhante para Oracle e DB2. Inclui funcionalidades empresariais como conformidade completa ACID, processamento transacional fiável e controlo de simultaneidade de várias versões. Ele também oferece suporte a padrões como ANSI SQL e SQL/MED (incluindo invólucros de dados externa para Oracle, MySQL, MongoDB e muitas outras). É altamente extensível com suporte para as linguagens de procedimento mais 12, índices GIN e essência, suporte de dados geográficos e vários recursos do tipo do NoSQL para JSON ou aplicativos baseada em chave-valor.

Neste artigo, aprenderá como instalar e configurar o PostgreSQL numa máquina virtual do Azure em execução no Linux.

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="install-postgresql"></a>Instalar o PostgreSQL
> [!NOTE]
> Já tem de ter uma máquina virtual do Azure em execução no Linux para concluir este tutorial. Para criar e configurar uma VM do Linux antes de continuar, consulte a [tutorial do Azure Linux VM](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
> 

Neste caso, utilize a porta de 1999 como a porta do PostgreSQL.  

Ligue-se para o Linux VM criada por meio do PuTTY. Se esta for a primeira vez que está a utilizar uma VM do Linux do Azure, veja [como utilizar o SSH com Linux no Azure](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para aprender a utilizar o PuTTY para ligar a uma VM do Linux.

1. Execute o comando seguinte para mudar para a raiz (administrador):
   
        # sudo su -
2. Algumas distribuições têm dependências que tem de instalar antes de instalar o PostgreSQL. Verifique a existência de sua distribuição nesta lista e execute o comando adequado:
   
   * Linux base do Red Hat:
     
           # yum install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y  
   * Debian base Linux:
     
            # apt-get install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam libxslt-devel tcl-devel python-devel -y  
   * SUSE Linux:
     
           # zypper install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y  
3. Transferir o PostgreSQL para o diretório de raiz e, em seguida, deszipe o pacote:
   
        # wget https://ftp.postgresql.org/pub/source/v9.3.5/postgresql-9.3.5.tar.bz2 -P /root/
   
        # tar jxvf  postgresql-9.3.5.tar.bz2
   
    O procedimento acima é um exemplo. Pode encontrar o endereço de download mais detalhado no [índice de/pub/origem/](https://ftp.postgresql.org/pub/source/).
4. Para iniciar a compilação, execute estes comandos:
   
        # cd postgresql-9.3.5
   
        # ./configure --prefix=/opt/postgresql-9.3.5
5. Se quiser criar tudo o que pode ser criado, incluindo a documentação (páginas HTML e man) e os módulos adicionais (contrib), execute o seguinte comando em vez disso:
   
        # gmake install-world
   
    Deverá receber a seguinte mensagem de confirmação:
   
        PostgreSQL, contrib, and documentation successfully made. Ready to install.

## <a name="configure-postgresql"></a>Configurar o PostgreSQL
1. (Opcional) Crie um link simbólico para encurtar a referência de PostgreSQL não incluir o número de versão:
   
        # ln -s /opt/postgresql-9.3.5 /opt/pgsql
2. Crie um diretório para a base de dados:
   
        # mkdir -p /opt/pgsql_data
3. Criar um utilizador não raiz e modificar do perfil do usuário. Em seguida, mudar para este utilizador novo (chamado *postgres* no nosso exemplo):
   
        # useradd postgres
   
        # chown -R postgres.postgres /opt/pgsql_data
   
        # su - postgres
   
   > [!NOTE]
   > Por motivos de segurança, o PostgreSQL utiliza um utilizador não raiz para inicializar, iniciar ou encerrar a base de dados.
   > 
   > 
4. Editar a *bash_profile* ficheiro ao introduzir os comandos abaixo. Estas linhas serão adicionadas ao final da *bash_profile* ficheiro:
   
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
5. Executar o *bash_profile* ficheiro:
   
        $ source .bash_profile
6. Valide a instalação usando o seguinte comando:
   
        $ which psql
   
    Se a instalação estiver concluída com êxito, verá a seguinte resposta:
   
        /opt/pgsql/bin/psql
7. Também pode verificar a versão de PostgreSQL:
   
        $ psql -V

8. Inicialize a base de dados:
   
        $ initdb -D $PGDATA -E UTF8 --locale=C -U postgres -W
   
    Deverá receber a seguinte saída:

![image](./media/postgresql-install/no1.png)

## <a name="set-up-postgresql"></a>Configurar o PostgreSQL
<!--    [postgres@ test ~]$ exit -->

Execute os seguintes comandos:

    # cd /root/postgresql-9.3.5/contrib/start-scripts

    # cp linux /etc/init.d/postgresql

Modifique as duas variáveis no ficheiro /etc/init.d/postgresql. O prefixo é definido para o caminho de instalação do PostgreSQL: **/OPT/Microsoft pgsql**. PGDATA está definido como o caminho de armazenamento de dados do PostgreSQL: **/OPT/Microsoft pgsql_data**.

    # sed -i '32s#usr/local#opt#' /etc/init.d/postgresql

    # sed -i '35s#usr/local/pgsql/data#opt/pgsql_data#' /etc/init.d/postgresql

![image](./media/postgresql-install/no2.png)

Altere o arquivo para tornar executável:

    # chmod +x /etc/init.d/postgresql

Start PostgreSQL:

    # /etc/init.d/postgresql start

Verifique se o ponto de final do PostgreSQL está em:

    # netstat -tunlp|grep 1999

Deverá ver o resultado seguinte:

![image](./media/postgresql-install/no3.png)

## <a name="connect-to-the-postgres-database"></a>Ligar à base de dados Postgres
Mude para o utilizador postgres mais uma vez:

    # su - postgres

Crie uma base de dados Postgres:

    $ createdb events

Ligar à base de dados de eventos que acabou de criar:

    $ psql -d events

## <a name="create-and-delete-a-postgres-table"></a>Criar e eliminar uma tabela de Postgres
Agora que tem ligação à base de dados, pode criar tabelas no mesmo.

Por exemplo, crie uma nova tabela de Postgres de exemplo com o seguinte comando:

    CREATE TABLE potluck (name VARCHAR(20),    food VARCHAR(30),    confirmed CHAR(1), signup_date DATE);

Configurou uma tabela de quatro colunas com os seguintes nomes de coluna e as restrições:

1. A coluna "name" tem sido limitada pelo comando VARCHAR ter menos de 20 carateres.
2. A coluna "food" indica o item de comida que irá fornecer a cada pessoa. VARCHAR limita este texto para ser inferior a 30 carateres.
3. A coluna "confirmada" regista se a pessoa tem RSVP'd para o potluck. Os valores aceitáveis são "Y" e "N".
4. A coluna de "data" mostra quando eles se inscreveu para o evento. Postgres requer que as datas escritos como aaaa-mm-dd.

Deverá ver o seguinte se a tabela foi criada com êxito:

![image](./media/postgresql-install/no4.png)

Também pode verificar a estrutura da tabela, utilizando o seguinte comando:

![image](./media/postgresql-install/no5.png)

### <a name="add-data-to-a-table"></a>Adicionar dados a uma tabela
Em primeiro lugar, insira informações numa linha:

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('John', 'Casserole', 'Y', '2012-04-11');

Verá esta saída:

![image](./media/postgresql-install/no6.png)

Pode adicionar alguns mais pessoas para a tabela também. Aqui estão algumas opções, ou pode criar o seu próprio:

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Sandy', 'Key Lime Tarts', 'N', '2012-04-14');

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES ('Tom', 'BBQ','Y', '2012-04-18');

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Tina', 'Salad', 'Y', '2012-04-18');

### <a name="show-tables"></a>Mostrar tabelas
Utilize o seguinte comando para mostrar uma tabela:

    select * from potluck;

O resultado é:

![image](./media/postgresql-install/no7.png)

### <a name="delete-data-in-a-table"></a>Eliminar dados numa tabela
Utilize o seguinte comando para eliminar dados numa tabela:

    delete from potluck where name=’John’;

Esta ação elimina todas as informações na linha "João". O resultado é:

![image](./media/postgresql-install/no8.png)

### <a name="update-data-in-a-table"></a>Atualizar os dados de uma tabela
Utilize o seguinte comando para atualizar os dados numa tabela. Por esse motivo, Sandy tiver confirmado que ela é participar, pelo que iremos alterar seu RSVP de "N" como "Y":

     UPDATE potluck set confirmed = 'Y' WHERE name = 'Sandy';


## <a name="get-more-information-about-postgresql"></a>Obter mais informações sobre PostgreSQL
Agora que concluiu a instalação do PostgreSQL na VM Linux do Azure, pode desfrutar utilizá-la no Azure. Para saber mais sobre o PostgreSQL, visite o [Web site do PostgreSQL](https://www.postgresql.org/).

