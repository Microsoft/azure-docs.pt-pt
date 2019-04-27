---
title: Instalar TmaxSoft OpenFrame em máquinas virtuais do Azure
description: Realojar IBM z/OS mainframe cargas de trabalho usando o ambiente de TmaxSoft OpenFrame em máquinas virtuais do Azure (VMs).
services: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.openlocfilehash: 78a8b5e7a1c5512f81315519210bc7759dd15342
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60800868"
---
# <a name="install-tmaxsoft-openframe-on-azure"></a>Instalar o TmaxSoft OpenFrame no Azure

Saiba como configurar um ambiente de OpenFrame no Azure adequado para o desenvolvimento, demonstrações, teste ou cargas de trabalho de produção. Este tutorial orienta-o através de cada passo.

OpenFrame inclui vários componentes que criar o ambiente de emulação de mainframe no Azure. Por exemplo, serviços online do OpenFrame substituem o middleware de mainframe, como o IBM Customer informações controle sistema (CICS) e OpenFrame Batch, com seu componente TJES, substitui subsistema de entrada da tarefa (JES do mainframe IBM).

OpenFrame funciona com qualquer base de dados relacional, incluindo Oracle Database, Microsoft SQL Server, IBM Db2 e MySQL. Esta instalação do OpenFrame utiliza a base de dados relacional TmaxSoft Tibero. OpenFrame e Tibero executam num sistema operativo Linux. Este tutorial instala CentOS 7.3, apesar de poder utilizar outras distribuições suportadas de Linux. O servidor de aplicações OpenFrame e a base de dados Tibero são instalados numa máquina virtual (VM).

O tutorial orienta-o ao longo da instalação dos componentes de suite OpenFrame. Alguns devem ser instalado em separado.

Componentes de OpenFrame principal:

- Pacotes de instalação necessários.
- Base de dados de Tibero.
- Conectividade ODBC (Open Database) é utilizado por aplicações no OpenFrame para comunicar com a base de dados Tibero.
- OpenFrame Base, o middleware que gerencia todo o sistema.
- OpenFrame Batch, a solução que substitui os sistemas de batch do mainframe.
- TACF, um módulo de serviço que controla o acesso de utilizador para sistemas e recursos.
- ProSort, uma ferramenta de ordenação para transações de batches.
- OFCOBOL, um compilador que interpreta os programas de COBOL do mainframe.
- OFASM, um compilador que interpreta os programas de montador o mainframe.
- OpenFrame servidor tipo C (OSC), a solução de que substitui o mainframe middleware e IBM CICS.
- Java Enterprise utilizador solução (JEUS), um servidor de aplicação web que está certificado para Java Enterprise Edition 6.
- OFGW, o componente de gateway OpenFrame que fornece um serviço de escuta 3270.
- OFManager, uma solução que fornece funções de gerenciamento e operação do OpenFrame no ambiente de web.

Outro necessários OpenFrame componentes:

- OSI, a solução de que substitui o middleware de mainframe e IMS DC.
- TJES, a solução que fornece JES ambiente o mainframe.
- OFTSAM, a solução que permite que os arquivos de SAM (V) a serem usados no sistema aberto.
- OFHiDB, a solução que substitui o mainframe's IMS DB.
- OFPLI, um compilador que interpreta o mainframe do PL / eu programas.
- PROTRIEVE, uma solução que executa o idioma de mainframe Easytrieve de AC.
- OFMiner, uma solução que analisa os ativos de mainframes e os migra para o Azure.

## <a name="architecture"></a>Arquitetura

A figura a seguir fornece uma descrição geral dos componentes arquitetônicos OpenFrame 7.0 instalado neste tutorial:

![Componentes de OpenFrame](media/openframe-02.png)

## <a name="azure-system-requirements"></a>Requisitos de sistema do Azure

A tabela seguinte lista os requisitos para a instalação no Azure.
<!-- markdownlint-disable MD033 -->

<table>
<thead>
    <tr><th>Requisito</th><th>Descrição</th></tr>
</thead>
<tbody>
<tr><td>Distribuições suportadas de Linux no Azure
</td>
<td>
Linux x86 2.6 (32 bits, 64 bits)<br/>
Red Hat 7.x<br/>
CentOS 7.x<br/>
</td>
</tr>
<tr><td>Hardware
</td>
<td>Núcleos: 2 (mínimo)<br/>
Memória: 4 GB (mínimo)<br/>
Espaço de comutação: 1 GB (mínimo)<br/>
Disco rígido: 100 GB (mínimo)<br/>
</td>
</tr>
<tr><td>Software opcional para os utilizadores do Windows
</td>
<td>PuTTY: Utilizados neste guia para configurar funcionalidades VM<br/>
WinSCP: Um cliente SFTP popular e o cliente FTP, que pode utilizar<br/>
Eclipse para Windows: Uma plataforma de programação suportada pelo TmaxSoft<br/>
(O Microsoft Visual Studio não é suportado neste momento)
</td>
</tr>
</tbody>
</table>

<!-- markdownlint-enable MD033 -->

## <a name="prerequisites"></a>Pré-requisitos

Planeje gastar durante alguns dias para montar o software necessário e concluir todos os processos manuais.

Antes de começar, faça o seguinte:

- Obter a mídia de instalação OpenFrame do TmaxSoft. Se for um cliente existente do TmaxSoft, contacte o seu representante de TmaxSoft para uma cópia licenciada. Caso contrário, solicitar uma versão de avaliação do [TmaxSoft](http://www.tmaxsoft.com/contact/).

- Pedir a documentação de OpenFrame ao enviar e-mail para <support@tmaxsoft.com>.

- Obtenha uma subscrição do Azure se ainda não tiver uma. Também pode criar uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

- Opcional. Configure um túnel VPN de site a site ou uma jumpbox que restringe o acesso à VM do Azure para os utilizadores permitidos na sua organização. Este passo não é necessário, mas é uma prática recomendada.

## <a name="set-up-a-vm-on-azure-for-openframe-and-tibero"></a>Configurar uma VM no Azure para OpenFrame e Tibero

Pode configurar o ambiente de OpenFrame usando vários padrões de implementação, mas o procedimento seguinte mostra como implementar o servidor de aplicações OpenFrame e a base de dados de Tibero numa VM. Em ambientes maiores e para cargas de trabalho especial, uma prática recomendada é implantar a base de dados em separado na sua própria VM para um melhor desempenho.

**Para criar uma VM**

1. Aceda ao portal do Azure em <http://portal.azure.com> e inicie sessão na sua conta.

2. Clique em **máquinas virtuais**.

    ![Lista de recursos no portal do Azure](media/vm-01.png)

3. Clique em **Adicionar**.

    ![Adicionar opção no portal do Azure](media/vm-02.png)

4. À direita da **sistemas operativos**, clique em **mais**.

     ![Opção de mais no portal do Azure](media/vm-03.png)

5. Clique em **baseada em CentOS 7.3** para seguir este passo a passo exatamente, ou pode escolher outro suportada a distribuição de Linux.

     ![Opções de sistema operativo no portal do Azure](media/vm-04.png)

6. Na **Noções básicas** as definições, introduza **nome**, **nome de utilizador**, **tipo de autenticação**, **subscrição** (Pay as you go é o estilo AWS de pagamento), e **grupo de recursos** (utilize um já existente ou crie um grupo de TmaxSoft).

7. Quando concluir (incluindo o par de chaves públicas/privadas para **tipo de autenticação**), clique em **submeter**.

> [!NOTE]
> Se utilizar uma chave pública SSH **tipo de autenticação**, veja os passos na secção seguinte para gerar o par de chaves públicas/privadas, em seguida, retomar os passos aqui descritos.

### <a name="generate-a-publicprivate-key-pair"></a>Gerar um par de chaves públicas/privadas

Se estiver a utilizar um sistema operativo do Windows, precisa PuTTYgen para gerar um par de chaves públicas/privadas.

A chave pública pode ser compartilhada livremente, mas a chave privada deve ser mantida em sigilo totalmente e nunca deve ser partilhada com outra parte. Depois de gerar as chaves, tem de colar a **chave pública SSH** na configuração — na verdade, carregá-lo para a VM do Linux. É armazenado dentro autorizado\_chaves dentro o \~/.ssh diretórios do diretório de raiz da conta de utilizador. A VM do Linux, em seguida, é capaz de reconhecer e validar a ligação depois de fornecer associada **chave privada SSH** no cliente SSH (no nosso caso, o PuTTY).

Ao oferecer novos indivíduos com acesso a VM: 

- Cada novo indivíduo gera suas próprias chaves públicas/privadas com o PuTTYgen.
- Indivíduos armazenam suas próprias chaves privadas em separado e enviam as informações da chave públicas para o administrador da VM.
- O administrador cola o conteúdo da chave pública para o \~/.ssh/authorized\_ficheiro de chaves.
- O novo indivíduo liga-se por meio do PuTTY.

**Para gerar um par de chaves públicas/privadas**

1.  Baixe o PuTTYgen de <https://www.putty.org/> e instalá-lo através de todas as configurações padrão.

2.  Para abrir o PuTTYgen, localize o diretório de instalação PuTTY em c:\\Program Files\\PuTTY.

    ![Interface do puTTY](media/puttygen-01.png)

3.  Clique em **gerar**.

    ![Caixa de diálogo de gerador de chave puTTY](media/puttygen-02.png)

4.  Após a geração, guarde a chave pública e a chave privada. Cole o conteúdo da chave pública no **chave pública SSH** secção a **criar máquina virtual \> Noções básicas** painel (mostrado nas etapas 6 e 7 na secção anterior).

    ![Caixa de diálogo de gerador de chave puTTY](media/puttygen-03.png)

### <a name="configure-vm-features"></a>Configurar funcionalidades VM

1. No portal do Azure, no **escolher um tamanho** painel, selecione as definições de hardware de máquina do Linux que pretende. O *mínima* os requisitos para instalar Tibero e OpenFrame são 2 CPUs e 4 GB de RAM, conforme apresentado nesta instalação de exemplo:

    ![Criar máquina virtual - Noções básicas](media/create-vm-01.png)

2. Clique em **3 definições** e utilize as predefinições para configurar funcionalidades opcionais.
3. Reveja os detalhes de pagamento.

    ![Criar máquina virtual - compra](media/create-vm-02.png)

4. Submeta as suas seleções. Azure começa a implementar a VM. Este processo normalmente demora alguns minutos.

5. Quando a VM é implementada, é apresentado o dashboard do mesmo, que mostra todas as definições que foram selecionadas durante a configuração. Anote o **endereço IP público**.

    ![Tmax no dashboard do Azure](media/create-vm-03.png)

6. Abra o PuTTY.

7. Para **nome de anfitrião**, digite seu nome de utilizador e o endereço IP público é copiado. Por exemplo, **nome de utilizador\@publicip**.

    ![Caixa de diálogo de configuração do puTTY](media/putty-01.png)

8. Na **categoria** , clique em **ligação \> SSH \> Auth**. Forneça o caminho para sua **chave privada** ficheiro.

    ![Caixa de diálogo de configuração do puTTY](media/putty-02.png)

9. Clique em **aberto** para iniciar a janela PuTTY. Se tiver êxito, está ligado à sua nova VM CentOS em execução no Azure.

10. Para iniciar sessão como utilizador raiz, escreva **sudo bash**.

    ![Início de sessão de utilizador de raiz na janela de comando](media/putty-03.png)

## <a name="set-up-the-environment-and-packages"></a>Configurar o ambiente e os pacotes

Agora que a VM é criada e estiver conectado, tem de efetuar alguns passos de configuração e instalar os pacotes de pré-instalação necessários.

1. Mapear o nome **ofdemo** para o endereço IP local através de vi para editar o ficheiro de anfitriões (`vi /etc/hosts`). Supondo que nossa IP é 192.168.96.148 ofdemo, isso é antes da alteração:

    ```vi
    127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4 
    ::1              localhost localhost.localdomain localhost6 localhost6.localdomain 
    <IP Address>    <your hostname>
    ```

     Trata-se após a alteração:

    ```vi
    127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4 
    ::1              localhost localhost.localdomain localhost6 localhost6.localdomain 
    192.168.96.148   ofdemo
    ```

2. Crie grupos e utilizadores:

    ```vi
    [root@ofdemo ~]# adduser -d /home/oframe7 oframe7 
    [root@ofdemo ~]# passwd oframe7
    ```

3. Altere a palavra-passe para o utilizador oframe7:

    ```vi
    New password: 
    Retype new password: 
    passwd: all authentication tokens updated successfully.
    ```

4. Atualize os parâmetros de kernel no /etc/sysctl.conf:

    ```vi
    [root@ofdemo ~]# vi /etc/sysctl.conf
    kernel.shmall = 7294967296 
    kernel.sem = 10000 32000 10000 10000
    ```

5. Atualize os parâmetros de kernel dinamicamente sem reinicialização:

    ```vi
    [root@ofdemo ~]# /sbin/sysctl -p
    ```

6. Obtenha os pacotes necessários: Certifique-se de que o servidor está ligado à Internet, transfira os pacotes seguintes e, em seguida, instalá-los:

     - dos2unix
     - glibc
     - glibc.i686 glibc.x86\_64
     - libaio
     - ncurses

          > [!NOTE]
          > Depois de instalar o pacote de ncurses, crie as ligações simbólicas seguintes:
         ```
         ln -s /usr/lib64/libncurses.so.5.9 /usr/lib/libtermcap.so
         ln -s /usr/lib64/libncurses.so.5.9 /usr/lib/libtermcap.so.2
         ```

     - gcc
     - gcc-c++
     - libaio-devel.x86\_64
     - strace
     - ltrace
     - gdb

7. Em caso de instalação de RPM de Java, faça o seguinte:

```
root@ofdemo ~]# rpm -ivh jdk-7u79-linux-x64.rpm
[root@ofdemo ~]# vi .bash_profile

# JAVA ENV
export JAVA_HOME=/usr/java/jdk1.7.0_79/
export PATH=$JAVA_HOME/bin:$PATH
export CLASSPATH=$CLASSPATH:$JAVA_HOME/jre/lib/ext:$JAVA_HOME/lib/tools.jar

[root@ofdemo ~]# source /etc/profile
[root@ofdemo ~]# java –version

java version "1.7.0_79"
Java(TM) SE Runtime Environment (build 1.7.0_79-b15)
Java HotSpot(TM) 64-Bit Server VM (build 24.79-b02, mixed mode)

[root@ofdemo ~]# echo $JAVA_HOME /usr/java/jdk1.7.0_79/
```

## <a name="install-the-tibero-database"></a>Instalar a base de dados Tibero

Tibero fornece várias funções de chave no ambiente de OpenFrame no Azure:

- Tibero é utilizado como o arquivo de dados interna do OpenFrame para várias funções de sistema.
- Ficheiros VSAM, incluindo KSDS RRDS e ESDS, utilizam a base de dados Tibero internamente para armazenamento de dados.
- Está armazenado o repositório de dados TACF no Tibero.
- As informações do catálogo OpenFrame são armazenadas no Tibero.
- A base de dados Tibero pode servir como um substituto para IBM Db2 para armazenar dados da aplicação.

**Para instalar Tibero**

1. Certifique-se de que o ficheiro de instalador binário Tibero está presente e rever o número de versão.
2. Copie o software de Tibero para a conta de utilizador Tibero (oframe). Por exemplo:

    ```
    [oframe7@ofdemo ~]$ tar -xzvf tibero6-bin-6_rel_FS04-linux64-121793-opt-tested.tar.gz 
    [oframe7@ofdemo ~]$ mv license.xml /opt/tmaxdb/tibero6/license/
    ```

3. Abrir .bash\_perfil no vi (`vi .bash_profile`) e cole o seguinte:

    ```
    # Tibero6 ENV
    export TB_HOME=/opt/tmaxdb/tibero6 
    export TB_SID=TVSAM export TB_PROF_DIR=$TB_HOME/bin/prof 
    export LD_LIBRARY_PATH=$TB_HOME/lib:$TB_HOME/client/lib:$LD_LIBRARY_PATH 
    export PATH=$TB_HOME/bin:$TB_HOME/client/bin:$PATH
    ```

4. Para executar o perfil de bash, o tipo de linha de comandos:

    ```
    source .bash_profile
    ```

5. Gerar o ficheiro de tip (um ficheiro de configuração para Tibero), em seguida, abra-o no vi. Por exemplo:

    ```
    [oframe7@ofdemo ~]$ sh $TB_HOME/config/gen_tip.sh
    [oframe7@ofdemo ~]$ vi $TB_HOME/config/$TB_SID.tip
    ```

6. Modificar \$TB\_HOME/client/config/tbdsn.tbr e colocar em vez disso, o 127.0.0.1 oflocalhost conforme mostrado:

    ```
    TVSAM=( 
    (INSTANCE=(HOST=127.0.0.1)
        (PT=8629)
    (DB_NAME=TVSAM)
          )
     )
    ```

7. Crie a base de dados. É apresentada a seguinte saída:

    ```
    Change core dump dir to /opt/tmaxdb/tibero6/bin/prof.
    Listener port = 8629
    Tibero 6
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Tibero instance started up (NOMOUNT mode).
     /--------------------- newmount sql ------------------------/
    create database character set MSWIN949 national character set UTF16;
    /-----------------------------------------------------------/
    Database created.
    Change core dump dir to /opt/tmaxdb/tibero6/bin/prof.
    Listener port = 8629
    Tibero 6
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Tibero instance started up (NORMAL mode).
    /opt/tmaxdb/tibero6/bin/tbsvr
    ………………………..
    Creating agent table...
    Done.
    For details, check /opt/tmaxdb/tibero6/instance/TVSAM/log/system_init.log.
    ************************************************** 
    * Tibero Database TVSAM is created successfully on Fri Aug 12 19:10:43 UTC 2016.
    *     Tibero home directory ($TB_HOME) =
    *         /opt/tmaxdb/tibero6
    *     Tibero service ID ($TB_SID) = TVSAM
    *     Tibero binary path =
    *         /opt/tmaxdb/tibero6/bin:/opt/tmaxdb/tibero6/client/bin
    *     Initialization parameter file =
    *         /opt/tmaxdb/tibero6/config/TVSAM.tip
    * 
    * Make sure that you always set up environment variables $TB_HOME and
    * $TB_SID properly before you run Tibero.
     ******************************************************************************
    ```

8. Para reciclar Tibero, primeiro encerrá-lo usando o `tbdown` comando. Por exemplo:

    ```
    [oframe7@ofdemo ~]$$ tbdown 
    Tibero instance terminated (NORMAL mode).
    ```

9. Agora inicializam usando a Tibero `tbboot`. Por exemplo:

    ```
    [oframe7@ofdemo ~]$ tbboot
    Change core dump dir to /opt/tmaxdb/tibero6/bin/prof. Listener port = 8629

    Tibero 6  
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Tibero instance started up (NORMAL mode).
    ```

10. Para criar um espaço de tabelas, acessar o banco de dados usando SYS utilizador (sys/tmax), em seguida, crie o espaço de tabelas necessário para o volume predefinido e TACF:

    ```
    [oframe7@ofdemo ~]$ tbsql tibero/tmax
    tbSQL 6  
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Connected to Tibero.
    ```

11. Agora, escreva os seguintes comandos SQL:

    ```
    SQL> create tablespace "DEFVOL" datafile 'DEFVOL.dbf' size 500M autoextend on; create tablespace "TACF00" datafile 'TACF00.dbf' size 500M autoextend on; create tablespace "OFM_REPOSITORY" datafile 'ofm_repository.dbf' size 300M autoextend on;
    SQL> Tablespace 'DEFVOL' created.
    SQL> Tablespace 'TACF00' created.
    SQL> Tablespace ' OFM_REPOSITORY ' created.
    SQL> SQL> Disconnected.
    ```

12. Efetuar o arranque Tibero e certifique-se de que os processos de Tibero estão em execução:

    ```
    [oframe7@ofdemo ~]$ tbboot 
    ps -ef | egrep tbsvr
    ```

Saída:

![Saída de Tibero](media/tibero-01.png)

## <a name="install-odbc"></a>Instalar o ODBC

Aplicativos em OpenFrame se comunicar com a base de dados de Tibero com a API do ODBC fornecido pelo projeto unixODBC de código-fonte aberto.

Para instalar o ODBC:

1. Certifique-se de que o ficheiro de instalador unixODBC 2.3.4.tar.gz está presente ou utilize o `wget unixODBC-2.3.4.tar.gz` comando. Por exemplo:

     ```
     [oframe7@ofdemo ~]$ wget ftp://ftp.unixodbc.org/pub/unixODBC/unixODBC-2.3.4.tar.gz
     ```

2. Deszipe o binário. Por exemplo:

     ```
     [oframe7@ofdemo ~]$ tar -zxvf unixODBC-2.3.4.tar.gz
     ```

3. Navegue para o diretório de unixODBC 2.3.4 e gerar o Makefile utilizando as informações da máquina de verificação. Por exemplo:

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ ./configure --prefix=/opt/tmaxapp/unixODBC/ --sysconfdir=/opt/tmaxapp/unixODBC/etc
     ```

     Por predefinição, unixODBC é instalado em /usr /local, então, `--prefix` transmite um valor para alterar a localização. Da mesma forma, arquivos de configuração estão instalados no /etc por predefinição, por isso, `--sysconfdir` transmite o valor da localização pretendida.

4. Execute Makefile: `[oframe7@ofdemo unixODBC-2.3.4]$ make`

5. Copie o ficheiro executável no diretório do programa depois de compilar. Por exemplo:

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ make install
     ```

6. Utilize o vi para editar o perfil de bash (`vi ~/.bash_profile`) e adicione o seguinte:

     ```
     # UNIX ODBC ENV 
     export ODBC_HOME=$HOME/unixODBC 
     export PATH=$ODBC_HOME/bin:$PATH 
     export LD_LIBRARY_PATH=$ODBC_HOME/lib:$LD_LIBRARY_PATH 
     export ODBCINI=$HOME/unixODBC/etc/odbc.ini 
     export ODBCSYSINI=$HOME
     ```

7. Aplica o ODBC. Edite os seguintes ficheiros da mesma forma. Por exemplo:

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ source ~/.bash_profile

     [oframe7@ofdemo ~]$ cd

     [oframe7@ofdemo ~]$ odbcinst -j unixODBC 2.3.4
     DRIVERS............: /home/oframe7/odbcinst.ini
     SYSTEM DATA SOURCES: /home/oframe7/odbc.ini
     FILE DATA SOURCES..: /home/oframe7/ODBCDataSources
     USER DATA SOURCES..: /home/oframe7/unixODBC/etc/odbc.ini
     SQLULEN Size.......: 8
     SQLLEN Size........: 8
     SQLSETPOSIROW Size.: 8

     [oframe7@ofdemo ~]$ vi odbcinst.ini

     [Tibero]
     Description = Tibero ODBC driver for Tibero6
     Driver = /opt/tmaxdb/tibero6/client/lib/libtbodbc.so
     Setup = 
     FileUsage = 
     CPTimeout = 
     CPReuse = 
     Driver Logging = 7

     [ODBC]
     Trace = NO 
     TraceFile = /home/oframe7/odbc.log 
     ForceTrace = Yes 
     Pooling = No 
     DEBUG = 1

     [oframe7@ofdemo ~]$ vi odbc.ini

     [TVSAM]
     Description = Tibero ODBC driver for Tibero6 
     Driver = Tibero 
     DSN = TVSAM 
     SID = TVSAM 
     User = tibero 
     password = tmax
     ```

8. Criar um link simbólico e validar a ligação de base de dados Tibero:

     ```
     [oframe7@ofdemo ~]$ ln $ODBC_HOME/lib/libodbc.so $ODBC_HOME/lib/libodbc.so.1 [oframe7@ofdemo ~]$ ln $ODBC_HOME/lib/libodbcinst.so 
     $ODBC_HOME/lib/libodbcinst.so.1

     [oframe7@ofdemo lib]$ isql TVSAM tibero tmax
     ```

É apresentada a seguinte saída:

![Saída ODBC mostrando ligada ao SQL](media/odbc-01.png)

## <a name="install-openframe-base"></a>Instalar OpenFrame Base

O servidor de Base do aplicativo está instalado antes dos serviços individuais que OpenFrame utiliza para gerir o sistema no Azure, incluindo a transação tratar de processos de servidor.

**Para instalar a Base de OpenFrame**

1. Certifique-se a instalação de Tibero foi concluída com êxito, em seguida, certifique-se de que o seguinte OpenFrame\_Base7\_0\_Linux\_x86\_ficheiro de instalador do 64. bin e o ficheiro de configuração de base.properties estão presentes.

2. Atualize o perfil de bash com as seguintes informações de Tibero específicas:

     ```bash
     alias ofhome='cd $OPENFRAME_HOME'
     alias ulog='cd $OPENFRAME_HOME/log/tmax/ulog'
     alias sysjcl='cd $OPENFRAME_HOME/volume_default/SYS1.JCLLIB'
     alias sysload='cd $OPENFRAME_HOME/volume_default/SYS1.LOADLIB'
     alias sysproc='cd $OPENFRAME_HOME/volume_default/SYS1.PROCLIB'
     alias oscsrc='cd $OPENFRAME_HOME/osc/oivp'
     alias osisrc='cd $OPENFRAME_HOME/osi/oivp'
     alias defvol='cd $OPENFRAME_HOME/volume_default'
     ```

3. Execute o perfil do bash:`[oframe7@ofdemo ~]$ . .bash_profile`
4. Certifique-se de que os processos de Tibero estão em execução. Por exemplo:

     ```linux
     [oframe7@ofdemo ~]$ ps -ef|grep tbsvr
     ```

    ![Base](media/base-01.png)

     > [!IMPORTANT]
     > Certifique-se de que iniciar Tibero antes da instalação.

5. Gerar licença em [technet.tmaxsoft.com](https://technet.tmaxsoft.com/en/front/main/main.do) e colocar a Base de OpenFrame, Batch, TACF, licenças de OSC na pasta apropriada:

     ```
     [oframe7@ofdemo ~]$ cp license.dat /opt/tmaxapp/OpenFrame/core/license/
     [oframe7@ofdemo ~]$ cp lictjes.dat lictacf.dat licosc.dat $OPENFRAME_HOME/license/
     ```

6. Transfira os ficheiros de binário e base.properties OpenFrame Base:

     ```
     [oframe7@ofdemo ~]$ vi base.properties
     OPENFRAME_HOME= <appropriate location for installation> ex. /opt/tmaxapp/OpenFrame TP_HOST_NAME=<your IP Hostname> ex. ofdemo 
     TP_HOST_IP=<your IP Address> ex. 192.168.96.148 
     TP_SHMKEY=63481 
     TP_TPORTNO=6623 
     TP_UNBLOCK_PORT=6291 
     TP_NODE_NAME=NODE1 
     TP_NODE_LIST=NODE1 
     MASCAT_NAME=SYS1.MASTER.ICFCAT 
     MASCAT_CREATE=YES 
     DEFAULT_VOLSER=DEFVOL 
     VOLADD_DEFINE=YES TSAM_USERNAME=tibero 
     TSAM_PASSWORD=tmax 
     TSAM_DATABASE=oframe 
     DATASET_SHMKEY=63211 
     DSLOCK_DATA=SYS1.DSLOCK.DATA 
     DSLOCK_LOG=SYS1.DSLOCK.LOG 
     DSLOCK_SEQ=dslock_seq.dat 
     DSLOCK_CREATE=YES 
     OPENFRAME_LICENSE_PATH=/opt/tmaxapp/license/OPENFRAME TMAX_LICENSE_PATH=/opt/tmaxapp/license/TMAX
     ```

7. Execute o instalador usando o arquivo base.properties. Por exemplo:

    ```
    [oframe7@ofdemo ~]$ chmod a+x OpenFrame_Base7_0_Linux_x86_64.bin 
    [oframe7@ofdemo ~]$ ./OpenFrame_Base7_0_Linux_x86_64.bin -f base.properties
    ```

    Quando terminar, a mensagem de instalação completa é diplayed.

8. Certifique-se de que o diretório de Base de OpenFrame estrutura com o `ls -ltr` comando. Por exemplo:

     ```
     [oframe7@ofdemo OpenFrame]$ ls -ltr
     total 44

     drwxrwxr-x. 4 oframe7 oframe7 61 Nov 30 16:57 UninstallerData 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 bin 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 cpm drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 data 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 include 
     drwxrwxr-x. 2 oframe7 oframe7 8192 Nov 30 16:57 lib 
     drwxrwxr-x. 6 oframe7 oframe7 48 Nov 30 16:57 log 
     drwxrwxr-x. 2 oframe7 oframe7 6 Nov 30 16:57 profile 
     drwxrwxr-x. 7 oframe7 oframe7 62 Nov 30 16:57 sample 
     drwxrwxr-x. 2 oframe7 oframe7 6 Nov 30 16:57 schema 
     drwxrwxr-x. 2 oframe7 oframe7 6 Nov 30 16:57 temp 
     drwxrwxr-x. 3 oframe7 oframe7 16 Nov 30 16:57 shared 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:58 license 
     drwxrwxr-x. 23 oframe7 oframe7 4096 Nov 30 16:58 core 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:58 config 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:58 scripts 
     drwxrwxr-x. 2 oframe7 oframe7 25 Nov 30 16:58 volume_default
     ```

9. Inicie OpenFrame Base:

     ```
     [oframe7@ofdemo ~]$ cp /usr/lib/libtermcap.so.2 $TMAXDIR/lib
     Startup Tmax Server
     [oframe7@ofdemo ~]$ tmboot
     ```

     ![saída do comando tmboot](media/base-02.png)

10. Certifique-se de que o estado do processo está pronto a utilizar o comando tmadmin em si. RDY é apresentado na **estado** coluna para cada um dos processos:

     ![saída do comando tmadmin](media/base-03.png)

11. Encerre OpenFrame Base:

     ```
     [oframe7@ofdemo ~]$ tmdown 
     Do you really want to down whole Tmax? (y : n): y

     TMDOWN for node(NODE1) is starting: 
     TMDOWN: SERVER(ofrsasvr:36) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrdsedt:39) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(vtammgr:43) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrcmsvr:40) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrdmsvr:38) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrlhsvr:37) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofruisvr:41) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrsmlog:42) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: CLH downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: CLL downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: TLM downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: TMM downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: TMAX is down
     ```

## <a name="install-openframe-batch"></a>Instalar OpenFrame Batch

O Batch de OpenFrame é composta por vários componentes que simular ambientes de batch de mainframe e é utilizado para executar tarefas de lote no Azure.

**Para instalar o Batch**

1. Certifique-se a instalação base foi concluída com êxito, em seguida, certifique-se de que o OpenFrame\_Batch7\_0\_Fix2\_MVS\_Linux\_x86\_ficheiro de instalador do 64. bin e ficheiro de configuração de batch.Properties estão presentes:

2. No prompt de comando, digite `vi batch.properties` para editar o ficheiro de batch.properties através de vi.

3. Modifique os parâmetros da seguinte forma:

     ```
     OPENFRAME_HOME = /opt/tmaxapp/OpenFrame
     DEFAULT_VOLSER=DEFVOL 
     TP_NODE_NAME=NODE1 
     TP_NODE_LIST=NODE1 
     RESOURCE_SHMKEY=66991 
     #JOBQ_DATASET_CREATE=YES 
     #OUTPUTQ_DATASET_CREATE=YES 
     DEFAULT_JCLLIB_CREATE=YES 
     DEFAULT_PROCLIB_CREATE=YES 
     DEFAULT_USERLIB_CREATE=YES 
     TJES_USERNAME=tibero 
     TJES_PASSWORD=tmax 
     TJES_DATABASE=oframe 
     BATCH_TABLE_CREATE=YES
     ```

4. Para executar o instalador do batch, na linha a linha de comandos, digite:

     ```
     ./OpenFrame_Batch7_0_Fix2_MVS_Linux_x86_64.bin -f batch.properties
     ```

5. Quando a instalação estiver concluída, inicie os conjuntos de OpenFrame instalados, escrevendo `tmboot` no prompt de comando.

    ![saída de tmboot](media/tmboot-01.png)

6. Tipo de `tmadmin` no prompt de comando para verificar o processo de OpenFrame.

    ![Ecrã de Tmax Admin](media/tmadmin-01.png)

7. Execute os seguintes comandos:

     ```
     $$2 NODE1 (tmadm): quit 
     ADM quit for node (NODE1)
     ```

8. Utilize o `tmdown` comando para iniciar e encerrar Batch:

     ```
     [oframe7@ofdemo ~]$tmdown
     Do you really want to down whole Tmax? (y : n): y

     TMDOWN for node(NODE1) is starting: 
     TMDOWN: SERVER(ofrsasvr:36) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:44) downed: Wed Sep  7 16:01:46 2016
     TMDOWN: SERVER(vtammgr: 43) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrcmsvr:40) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:45) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:46) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrdmsvr:38) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:47) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrdsedt:39) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjschd:54) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjinit:55) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:48) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjspbk:57) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:49) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:50) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:51) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrlhsvr:37) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:52) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:53) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjhist:56) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofruisvr:41) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmtsmgr:59) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrpmsvr:58) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrsmlog:42) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: CLL downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: TLM downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: CLH downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: TMM downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: TMAX is down
     ```

## <a name="install-tacf"></a>Instalar TACF

O Gestor de TACF é um módulo de serviço OpenFrame que controla o acesso dos utilizadores aos recursos através de segurança RACF e sistemas.

**Para instalar TACF**

1. Certifique-se de que o OpenFrame\_Tacf7\_0\_Fix2\_Linux\_x86\_ficheiro de instalador do 64. bin e o ficheiro de configuração de tacf.properties estão presentes.
2. Certifique-se a instalação do Batch foi concluída com êxito, em seguida, utilize o vi para abrir o ficheiro de tacf.properties (`vi tacf.properties`).
3. Modifique os parâmetros TACF:

     ```
     OPENFRAME_HOME=/opt/tmaxapp/OpenFrame 
     USE_OS_AUTH=NO 
     TACF_USERNAME=tibero 
     TACF_PASSWORD=tmax 
     TACF_DATABASE=oframe 
     TACF_TABLESPACE=TACF00 
     TACF_TABLE_CREATE=YES
     ```

4. Depois de concluir o instalador TACF, aplicam-se as variáveis de ambiente de TACF. Na linha de comandos, escreva:

     ```
     source \~/.bash\_profile
     ```

5. Execute o instalador TACF. Na linha de comandos, escreva:

     ```
     ./OpenFrame_Tacf7_0_Fix2_Linux_x86_64.bin -f tacf.properties
     ```

     O resultado tem um aspeto semelhante ao seguinte:

     ```
     Wed Dec 07 17:36:42 EDT 2016
     Free Memory: 18703 kB 
     Total Memory: 28800 kB

     4 Command Line Args: 
     0:  -f 1:  tacf.properties 
     2:  -m 
     3:  SILENT 
     java.class.path: 
     /tmp/install.dir.41422/InstallerData 
     /tmp/install.dir.41422/InstallerData/installer.zip 
     ZGUtil.CLASS_PATH: 
     /tmp/install.dir.41422/InstallerData 
     tmp/install.dir.41422/InstallerData/installer.zip 
     sun.boot.class.path: 
     /tmp/install.dir.41422/Linux/resource/jre/lib/resources.jar /tmp/install.dir.41422/Linux/resource/jre/lib/rt.jar /tmp/install.dir.41422/Linux/resource/jre/lib/sunrsasign.jar /tmp/install.dir.41422/Linux/resource/jre/lib/jsse.jar /tmp/install.dir.41422/Linux/resource/jre/lib/jce.jar /tmp/install.dir.41422/Linux/resource/jre/lib/charsets.jar /tmp/install.dir.41422/Linux/resource/jre/lib/jfr.jar /tmp/install.dir.41422/Linux/resource/jre/classes
     ```

6. No prompt de comando, digite `tmboot` reiniciar OpenFrame. O resultado tem um aspeto semelhante ao seguinte:

     ```
     TMBOOT for node(NODE1) is starting: 
     Welcome to Tmax demo system: it will expire 2016/11/4 
     Today: 2016/9/7 
     TMBOOT: TMM is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: CLL is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: CLH is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: TLM(tlm) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrsasvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrlhsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrdmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrdsedt) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrcmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofruisvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrsmlog) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(vtammgr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjschd) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjinit) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjhist) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjspbk) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrpmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmtsmgr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(tmsvr) is starting: Wed Sep  7 17:48:53 2016
     ```

7. Certifique-se de que o estado do processo está pronto a utilizar `tmadmin` no `si` comando. Por exemplo:

     ```
     [oframe7\@ofdemo \~]\$ tmadmin
     ```

     Na **estado** RDY de coluna, é apresentado:

    ![RDY na coluna Estado](media/tmboot-02.png)

8. Execute os seguintes comandos:

     ```
     $$2 NODE1 (tmadm): quit 
     DM quit for node (NODE1)

     [oframe7@ofdemo ~]$ tacfmgr 
     Input USERNAME  : ROOT 
     Input PASSWORD  : SYS1

     TACFMGR: TACF MANAGER START!!!
     QUIT TACFMGR: TACF MANAGER END!!!

     [oframe7@ofdemo ~]$ tmdow
     ```

9. Encerrar o servidor usando o `tmdown` comando. O resultado tem um aspeto semelhante ao seguinte:

     ```
     [oframe7@ofdemo ~]$ tmdown 
     Do you really want to down whole Tmax? (y : n): y

     TMDOWN for node(NODE1) is starting: 
     TMDOWN: SERVER(ofrlhsvr:37) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrdsedt:39) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjschd:54) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjmsvr:47) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjmsvr:48) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrdmsvr:38) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjmsvr:50) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjhist:56) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrsasvr:36) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrcmsvr:40) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjspbk:57) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(tmsvr:60) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrpmsvr:58) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmtsmgr:59) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: CLL downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: CLH downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: TLM downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: TMM downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: TMAX is down
     ```

## <a name="install-prosort"></a>Instalação ProSort

ProSort é um utilitário usado nas transações de batch de ordenação de dados.

**Para instalar ProSort**

1. Certifique-se de que a instalação do Batch foi concluída com êxito e, em seguida, certifique-se de que o **prosort bin prosort\_2sp3-linux64-2123-opt.tar.gz** ficheiro de instalador está presente.

2. Execute o instalador usando o arquivo de propriedades. Na linha de comandos, escreva:

     ```
     tar -zxvf prosort-bin-prosort\_2sp3-linux64-2123-opt.tar.gz
     ```

3. Mova o diretório de prosort para a localização de home page. Na linha de comandos, escreva:

     ```
     mv prosort /opt/tmaxapp/prosort
     ```

4. Crie um subdiretório de licença e copie o ficheiro de licença lá. Por exemplo:

     ```
     cd /opt/tmaxapp/prosort 
     mkdir license 
     cp /opt/tmaxsw/oflicense/prosort/license.xml /opt/tmaxapp/prosort/license
     ```

5. Abra bash.profile no vi (`vi .bash_profile`) e atualizá-lo da seguinte forma:

     ```bash
     #       PROSORT

     PROSORT_HOME=/opt/tmaxapp/prosort 
     PROSORT_SID=gbg 
     PATH=$PATH:$PROSORT_HOME/bin LD_LIBRARY_PATH=$PROSORT_HOME/lib:$LD_LIBRARY_PATH LIBPATH$PROSORT_HOME/lib:$LIBPATH 
     export PROSORT_HOME PROSORT_SID 
     PATH LD_LIBRARY_PATH LIBPATH 
     PATH=$PATH:$OPENFRAME_HOME/shbin 
     export PATH
     ```

6. Para executar o perfil de bash, a linha de comandos, escreva: `. .bash_profile`

7. Crie o ficheiro de configuração. Por exemplo:

     ```
     oframe@oframe7: cd /opt/tmaxapp/prosort/config 
     oframe@oframe7: ./gen_tip.sh 
     Using PROSORT_SID "gbg"
      /home/oframe7/prosort/config/gbg.tip generated
     ```

8. Crie a ligação simbólica. Por exemplo:

     ```
     oframe@oframe7: cd /opt/tmaxapp/OpenFrame/util/ 
     oframe@oframe7home/oframe7/OpenFrame/util :  ln -s DFSORT SORT
     ```

9. Certifique-se a instalação ProSort executando o `prosort -h` comando. Por exemplo:

     ```
     oframe@oframe7: prosort -h

     Usage: prosort [options] [sort script files]
     options ------
     -h             Display this information 
     -v             Display version information 
     -s             Display state information 
     -j             Display profile information 
     -x             Use SyncSort compatible mode
     ```

## <a name="install-ofcobol"></a>Instalar OFCOBOL

OFCOBOL é o compilador OpenFrame que interpreta os programas de COBOL do mainframe. 

**Para instalar OFCOBOL**

1. Certifique-se de que foi concluída com êxito a instalação de Batch/Online, em seguida, certifique-se de que o OpenFrame\_COBOL3\_0\_40\_Linux\_x86\_64. bin instalador ficheiro está presente.

2. Para executar o instalador OFCOBOL, a linha de comandos, escreva:

     ```
      ./OpenFrame\_COBOL3\_0\_40\_Linux\_x86\_64.bin
     ```

3. Leia o contrato de licenciamento e prima Enter para continuar.

4. Aceite o contrato de licenciamento. Quando a instalação estiver concluída, aparece o seguinte:

     ```
     Choose Install Folder 
     --------------------
     Where would you like to install?
     Default Install Folder: /home/oframe7/OFCOBOL

     ENTER AN ABSOLUTE PATH, OR PRESS <ENTER> TO ACCEPT THE DEFAULT : /opt/tmaxapp/OFCOBOL

     INSTALL FOLDER IS: /opt/tmaxapp/OFCOBOL 
     IS THIS CORRECT? (Y/N): Y[oframe7@ofdemo ~]$ vi .bash_profile

     ============================================================================ Installing... 
     ------------
     [==================|==================|==================|==================] 
     [------------------|------------------|------------------|------------------]

     =============================================================================== Installation Complete 
     --------------------
     Congratulations. OpenFrame_COBOL has been successfully installed
     PRESS <ENTER> TO EXIT THE INSTALLER
     ```

5. Abra o perfil de bash no vi (`vi .bash_profile`) e certifique-se de que é atualizado com as variáveis OFCOBOL.
6. Execute o perfil de bash. Na linha de comandos, escreva:

     ```
      source ~/.bash_profile
     ```

7. Copie a licença OFCOBOL para a pasta instalada. Por exemplo:
     ```
     mv licofcob.dat $OFCOB_HOME/license
     ```
8. Vá para o ficheiro de configuração de tjclrun.conf OpenFrame e abri-lo no vi. Por exemplo:
     ```
     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/config 
     [oframe7@ofdemo ~]$ vi tjclrun.conf
     ```

   Esta é a seção SYSLIB antes da alteração:
     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bin LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${COBDIR}/lib:/ usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib
     ```
   Esta é a seção SYSLIB depois da alteração:
     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bin LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${COBDIR}/lib:/ usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib :${ODBC_HOME}/lib 
     :${OFCOB_HOME}/lib
     ```
9. Reveja o OpenFrame\_COBOL\_InstallLog.log vi de ficheiros e certifique-se de que não há nenhum erro. Por exemplo:
     ```
     [oframe7@ofdemo ~]$ vi $OFCOB_HOME/UninstallerData/log/OpenFrame_COBOL_InstallLog.log 
     …….. 
     Summary 
     ------
     Installation: Successful. 
     131 Successes 
     0 Warnings 
     0 NonFatalErrors 
     0 FatalError
     ```
10. Utilize o `ofcob --version` de comandos e rever o número de versão para verificar a instalação. Por exemplo:

     ```
     [oframe7@ofdemo ~]$ ofcob --version 
     OpenFrame COBOL Compiler 3.0.54 
     CommitTag:: 645f3f6bf7fbe1c366a6557c55b96c48454f4bf
     ```

11. Reiniciar OpenFrame usando o `tmdown/tmboot` comando.

## <a name="install-ofasm"></a>Instalar OFASM

OFASM é o compilador OpenFrame que interpreta os programas de montador o mainframe.

**Para instalar OFASM**

1. Certifique-se de que foi concluída com êxito a instalação de Batch/Online, em seguida, certifique-se de que o OpenFrame\_ASM3\_0\_Linux\_x86\_64. bin instalador ficheiro está presente.

2. Execute o instalador. Por exemplo:

     ```
     [oframe7@ofdemo ~]$ ./OpenFrame_ASM3_0_Linux_x86_64.bin
     ```

3. Leia o contrato de licenciamento e prima Enter para continuar.
4. Aceite o contrato de licenciamento.
5. Certifique-se de que o perfil de bash é atualizado com variáveis OFASM. Por exemplo:

     ```
     [oframe7@ofdemo ~]$ source .bash_profile
     [oframe7@ofdemo ~]$ ofasm --version 
     # TmaxSoft OpenFrameAssembler v3 r328 
     (3ff35168d34f6e2046b96415bbe374160fcb3a34)

     [oframe7@ofdemo OFASM]$ vi .bash_profile

     # OFASM ENV 
     export OFASM_HOME=/opt/tmaxapp/OFASM 
     export OFASM_MACLIB=$OFASM_HOME/maclib/free_macro 
     export PATH="${PATH}:$OFASM_HOME/bin:" 
     export LD_LIBRARY_PATH="./:$OFASM_HOME/lib:$LD_LIBRARY_PATH"
     ```

6. Abra o ficheiro de configuração de tjclrun.conf OpenFrame no vi e editá-lo da seguinte forma:

     ```
     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/config 
     [oframe7@ofdemo ~]$ vi tjclrun.conf
     ```

     Eis a seção [SYSLIB] *antes de* a alteração:

     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bi n:${OPENFRAME_HOME}/volume_default/SYS1.LOADLIB LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${CO BDIR}/lib:/usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib:${OFCOB_HOM E}/lib:${ODBC_HOME}/lib:${OFPLI_HOME}/lib
     ```

     Eis a seção [SYSLIB] *depois de* a alteração:

     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bi n:${OPENFRAME_HOME}/volume_default/SYS1.LOADLIB LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${CO BDIR}/lib:/usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib:${OFCOB_HOM E}/lib:${ODBC_HOME}/lib:${OFPLI_HOME}/lib:${OFASM_HOME}/lib
     ```

7. Abra o OpenFrame\_ASM\_InstallLog.log vi de ficheiros e certifique-se de que não há nenhum erro. Por exemplo:

     ```
     [oframe7@ofdemo ~]$ vi 
     $OFASM_HOME/UninstallerData/log/OpenFrame_ASM_InstallLog.log 
     …….. 
     Summary 
     ------

     Installation: Successful.

     55 Successes 
     0 Warnings 
     0 NonFatalErrors 
     0 FatalErrors
     ```

8. Reinicie OpenFrame através da emissão de um dos seguintes comandos:

     ```
     tmdown / tmboot
     ```

     — ou,

     ```
     oscdown / oscboot
     ```

## <a name="install-osc"></a>Instalar OSC

OSC é o ambiente de OpenFrame semelhante à IBM CICS que oferece suporte a transações de OLTP de alta velocidade e outras funções de gestão.

**Para instalar OSC**

1. Certifique-se a instalação base foi concluída com êxito, em seguida, certifique-se de que o OpenFrame\_OSC7\_0\_Fix2\_Linux\_x86\_ficheiro de instalador do 64. bin e o ficheiro de configuração de osc.properties estão presente.
2. Edite os seguintes parâmetros no ficheiro osc.properties:
     ```
     OPENFRAME_HOME=/opt/tmaxapp/OpenFrame OSC_SYS_OSC_NCS_PATH=/opt/tmaxapp/OpenFrame/temp/OSC_NCS OSC_APP_OSC_TC_PATH=/opt/tmaxapp/OpenFrame/temp/OSC_TC
     ```

3. Execute o instalador usando o arquivo de propriedades, conforme mostrado:

     ```
     [oframe7@ofdemo ~]$ chmod a+x OpenFrame_OSC7_0_Fix2_Linux_x86_64.bin [oframe7@ofdemo ~]$ ./OpenFrame_OSC7_0_Fix2_Linux_x86_64.bin -f osc.properties
     ```

     Quando terminar, é apresentada a mensagem "Instalação concluída".

4. Certifique-se de que o perfil de bash é atualizado com as variáveis do OSC.
5. Reveja o OpenFrame\_OSC7\_0\_Fix2\_InstallLog.log ficheiro. Deverá ter um aspeto semelhante a:

     ```
     Summary 
     ------ 
     Installation: Successful.

     233 Successes
     0 Warnings
     0 NonFatalErrors
     0 FatalError
     ```

6. Utilize o vi para abrir o ficheiro de configuração ofsys.seq. Por exemplo:

     ```
     vi $OPENFRAME_HOME/config/ofsys.seq
     ```

7. Na \#BASE e \#secções do BATCH, edite os parâmetros conforme mostrado.

     ```
     Before changes
     #BASE
     ofrsasvr
     ofrlhsvr
     ofrdmsvr
     ofrdsedt
     ofrcmsvr
     ofruisvr
     ofrsmlog
     vtammgr
     TPFMAGENT

     #BATCH 
     #BATCH#obmtsmgr
     #BATCH#ofrpmsvr
     #BATCH#obmjmsvr
     #BATCH#obmjschd
     #BATCH#obmjinit
     #BATCH#obmjhist
     #BATCH#obmjspbk
     #TACF #TACF#tmsvr

     After changes  #BATCH
     #BASE          obmtsmgr 
     ofrsasvr       ofrpmsvr
     ofrlhsvr       obmjmsvr
     ofrdmsvr       obmjschd
     ofrdsedt       obmjinit
     ofrcmsvr       obmjhist
     ofruisvr       obmjspbk
     ofrsmlog
     vtammgr        #TACF
     TPFMAGENT      tmsvr
    ```

8. Copie o ficheiro de licença. Por exemplo:

     ```
     [oframe7@ofdemo ~]$ cp /home/oframe7/oflicense/ofonline/licosc.dat 

     $OPENFRAME_HOME/license

     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/license 
     oframe@oframe7/OpenFrame/license / ls -l 
     -rwxr-xr-x. 1 oframe mqm 80 Sep 12 01:37 licosc.dat 
     -rwxr-xr-x. 1 oframe mqm 80 Sep  8 09:40 lictacf.dat 
     -rwxrwxr-x. 1 oframe mqm 80 Sep  3 11:54 lictjes.da
     ```

9. Para iniciar e encerrar OSC, inicializar a memória de região partilhada do CICS digitando `osctdlinit OSCOIVP1` no prompt de comando.

10. Executar `oscboot` para arranque OSC. O resultado tem um aspeto semelhante ao seguinte:

     ```
     OSCBOOT : pre-processing       [ OK ]

     TMBOOT for node(NODE1) is starting: 
     Welcome to Tmax demo system: it will expire 2016/11/4 
     Today: 2016/9/12 
          TMBOOT: TMM is starting: Mon Sep 12 01:40:25 2016 
          TMBOOT: CLL is starting: Mon Sep 12 01:40:25 2016 
          TMBOOT: CLH is starting: Mon Sep 12 01:40:25 2016 
          TMBOOT: TLM(tlm) is starting: Mon Sep 12 01:40:25 2016 
     ```

11. Para verificar se o estado do processo está pronto, utilize o `tmadmin` comando em si. Todos os processos deverá apresentar RDY no **estado** coluna.

    ![Exibindo RDY de processos](media/tmadmin-02.png)

12. Encerrar OSC usando o `oscdown` comando.

## <a name="install-jeus"></a>Instalar JEUS

JEUS (solução de utilizador do Java empresarial) fornece a camada de apresentação do servidor de aplicativo da web de OpenFrame.

Antes de instalar JEUS, instale o pacote de Apache Ant, que fornece as bibliotecas e ferramentas da linha de comandos necessárias para instalar JEUS.

**Para instalar a Apache Ant**

1. Download Ant binário no `wget` comando. Por exemplo:

     ```
     wget http://apache.mirror.cdnetworks.com/ant/binaries/apacheant-1.9.7-bin.tar.gz
     ```

2. Utilize o `tar` utilitário para extrair o ficheiro binário e mova-o para uma localização adequada. Por exemplo:

     ```
     tar -xvzf apache-ant-1.9.7-bin.tar.gz
     ```

3. Para uma eficiência, crie um link simbólico:

     ```
     ln -s apache-ant-1.9.7 ant
     ```

4. Abra o perfil de bash no vi (`vi .bash_profile`) e atualize-o com as seguintes variáveis:

     ```
     # Ant ENV
     export ANT_HOME=$HOME/ant 
     export PATH=$HOME/ant/bin:$PATH
     ```

5.  Aplicam-se a variável de ambiente modificado. Por exemplo:

     ```
     [oframe7\@ofdemo \~]\$ source \~/.bash\_profile
     ```

**Para instalar JEUS**

1. Expanda o instalador usando o `tar` utilitário. Por exemplo:

     ```
     [oframe7@ofdemo ~]$ tar -zxvf jeus704.tar.gz
     ```

2. Criar uma **jeus** pasta (`mkdir jeus7`) e deszipe o binário.
3. Alterar para o **configuração** diretório (ou utilize o parâmetro JEUS para o seu próprio ambiente). Por exemplo:

     ```
     [oframe7@ofdemo ~]$ cd jeus7/setup/
     ```

4. Executar `ant clean-all` antes de executar a compilação. O resultado tem um aspeto semelhante ao seguinte:

     ```
     Buildfile: /home/oframe7jeus7/setup/build.xml

     clean-bin:
     delete-domain:
     [echo] Deleting a domain configuration: domain = jeus_domain
     delete-nodesxml:
     clean-config:
     clean-all:
     BUILD SUCCESSFUL
     Total time: 0 seconds
     ```

5.  Fazer uma cópia de segurança do ficheiro de domínio-config-template.properties. Por exemplo:

     ```
     [oframe7@ofdemo ~]$ cp domain-config-template.properties domain-configtemplate.properties.bkp
     ```

6. Abra o ficheiro de domínio-config-template.properties no vi:

     ```
     [oframe7\@ofdemo setup]\$ vi domain-config-template.properties
     ```

7. Alteração `jeus.password=jeusadmin nodename=Tmaxsoft` para `jeus.password=tmax1234 nodename=ofdemo`

8. Executar o `ant install` comando para criar JEUS.
9.  Atualizar o .bash\_ficheiro de perfil com as variáveis JEUS, conforme mostrado:

     ```
     # JEUS ENV 
     export JEUS_HOME=/opt/tmaxui/jeus7 PATH="/opt/tmaxui/jeus7/bin:/opt/tmaxui/jeus7/lib/system:/opt/tmaxui/jeus7/webserver/bin:$ {PATH}" 
     export PATH
     ```

10. Execute o perfil de bash. Por exemplo:

     ```
     [oframe7@ofdemo setup]$ . .bash_profile
     ```

11. *Opcional*. Crie um alias para fácil encerramento e arranque de componentes JEUS:

     ```     
     # JEUS alias

     alias dsboot='startDomainAdminServer -domain jeus_domain -u administrator -p jeusadmin'
     alias msboot='startManagedServer -domain jeus_domain -server server1 -u administrator -p jeusadmin' 
     alias msdown=‘jeusadmin -u administrator -p tmax1234 "stop-server server1“’ 
     alias dsdown=‘jeusadmin -domain jeus_domain -u administrator -p tmax1234 "local-shutdown“’
     ```

12. Para verificar a instalação, inicie o servidor de administrador de domínio, conforme mostrado:

     ```
     [oframe7@ofdemo ~]$ startDomainAdminServer -domain jeus_domain -u administrator -p jeusadmin
     ```

13. Verificar início de sessão de web utilizando a sintaxe:

     ```
     http://<IP>:<port>/webadmin/login
     ```

     Por exemplo, <http://192.168.92.133:9736/webadmin/login.> é apresentado o ecrã de início de sessão:
    
     ![Ecrã de início de sessão de JEUS WebAdmin](media/jeus-01.png)

     > [!NOTE]
     > Se tiver quaisquer problemas com a segurança de porta, abra a porta 9736 ou desabilitar o firewall (`systemctl stop firewall`).

14. Para alterar o nome de anfitrião para o servidor1, clique em **bloquear & Editar**, em seguida, clique em **servidor1**. Na janela do servidor, altere o nome do anfitrião da seguinte forma:

    1.  Alteração **Nodename** ao **ofdemo**.
    2.  Clique em **OK** no lado direito da janela.
    3.  Clique em **aplicar alterações** no canto inferior esquerdo da janela e para a descrição, introduza *alteração de nome de anfitrião*.

    ![Ecrã de JEUS WebAdmin](media/jeus-02.png)

15. Certifique-se de que a configuração está concluída com êxito no ecrã de confirmação.

    ![jeus_domain Server screen](media/jeus-03.png)

16. Inicie o processo de servidor gerido "servidor1" utilizando o seguinte comando:

     ```
     [oframe7@ofdemo ~]$ startManagedServer -domain jeus_domain -server server1 -u administrator -p jeusadmin
     ```

## <a name="install-ofgw"></a>Instalar OFGW

OFGW é o gateway de OpenFrame que suporta a comunicação entre o emulador do terminal 3270 e a base de OSI e gerencia as sessões entre o emulador do terminal e OSI.

**Para instalar OFGW**

1. Certifique-se de que JEUS foi instalado com êxito, em seguida, certifique-se de que o OFGW7\_0\_1\_Generic.bin instalador ficheiro está presente.
2. Execute o instalador. Por exemplo:

     ```
     [oframe7@ofdemo ~]$ ./OFGW7_0_1_Generic.bin
     ````

3. Utilize as seguintes localizações para as instruções correspondentes:
     -   Diretório inicial JEUS do
     -   Nome de domínio JEUS
     -   Nome do servidor JEUS
     -   Driver de Tibero
     -   ID do nó Tmax ofdemo

4. Aceitar o restante dos padrões, em seguida, prima Enter para sair do instalador.

5. Certifique-se de que o URL para OFGW está a funcionar conforme esperado:

     ```
     Type URL 
     http://192.168.92.133:8088/webterminal/ and press enter
      < IP >               :8088/webterminal/
     ```

     É apresentado o ecrã seguinte:

    ![OpenFrame WebTerminal](media/ofgw-01.png)

## <a name="install-ofmanager"></a>Instalar OFManager

OFManager fornece funções de gestão e operação para OpenFrame no ambiente de web.

**Para instalar OFManager**

1. Certifique-se de que o OFManager7\_Generic.bin instalador ficheiro está presente.
2. Execute o instalador. Por exemplo:

     ```
     OFManager7_Generic.bin
     ```

3.  Prima Enter para continuar, em seguida, aceite o contrato de licença.
4.  Escolha a pasta de instalação.
5.  Aceite as predefinições.
6.  Escolha Tibero como a base de dados.
7.  Prima Enter para sair do instalador.
8.  Certifique-se de que o URL para OFManager está a funcionar conforme esperado:

     ```
     Type URL http://192.168.92.133:8088/ofmanager and press enter <  IP >  : < PORT >  ofmanager Enter ID:   ROOT 
     Password: SYS1
     ```

É apresentado o ecrã de início:

![Ecrã de início de sessão de Tmax OpenFrame Manager](media/ofmanager-01.png)

Esta ação conclui a instalação dos componentes OpenFrame.

## <a name="next-steps"></a>Passos Seguintes

Se estiver Considerando uma migração de mainframe, nosso ecossistema de parceiros expansão está disponível para ajudá-lo. Para obter orientações detalhadas sobre como escolher uma solução de parceiro, consulte a [Alliance de modernização de plataforma](https://www.platformmodernization.org/pages/mainframe.aspx).

-   [Introdução ao Azure](https://docs.microsoft.com/azure/)
-   [Documentação do Host Integration Server (HIS)](https://docs.microsoft.com/host-integration-server/)
-   [Guia de migração Lift-and-Shift de Datacenter Virtual do Azure](https://blogs.msdn.microsoft.com/azurecat/2018/03/12/new-whitepaper-azure-virtual-datacenter-lift-and-shift-guide/)
