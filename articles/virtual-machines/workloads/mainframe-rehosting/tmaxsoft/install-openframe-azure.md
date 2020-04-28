---
title: Instale o OpenFrame TmaxSoft em máquinas virtuais azure
description: Realoja as suas cargas de trabalho do mainframe IBM z/OS utilizando o ambiente OpenFrame TmaxSoft em Máquinas Virtuais Azure (VMs).
services: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.openlocfilehash: 1ad6e52c421d9cfec4640d3a330b5507d6ed3e9b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "72436059"
---
# <a name="install-tmaxsoft-openframe-on-azure"></a>Instale o OpenFrame TmaxSoft no Azure

Aprenda a configurar um ambiente OpenFrame em Azure adequado para o desenvolvimento, demonstrações, testes ou cargas de trabalho de produção. Este tutorial acompanha-o em cada passo.

O OpenFrame inclui vários componentes que criam o ambiente de emulação do computador principal no Azure. Por exemplo, os serviços online OpenFrame substituem o middleware mainframe, como o IBM Customer Information Control System (CICS), e o OpenFrame Batch, com a sua componente TJES, substitui o subsistema de entrada de emprego (JES) do mainframe da IBM.

O OpenFrame funciona com qualquer base de dados relacional, incluindo a Oracle Database, o Microsoft SQL Server, o IBM Db2 e o MySQL. Esta instalação do OpenFrame utiliza a base de dados relacional TmaxSoft Tibero. Tanto o OpenFrame como o Tibero funcionam num sistema operativo Linux. Este tutorial instala o CentOS 7.3, embora possa utilizar outras distribuições linux suportadas. O servidor de aplicações OpenFrame e a base de dados Tibero estão instalados numa máquina virtual (VM).

O tutorial intervém através da instalação dos componentes da suite OpenFrame. Alguns devem ser instalados separadamente.

Componentes principais do OpenFrame:

- Pacotes de instalação necessários.
- Base de dados de Tibero.
- A Conectividade open Database (ODBC) é utilizada por aplicações no OpenFrame para comunicar com a base de dados Tibero.
- OpenFrame Base, o middleware que gere todo o sistema.
- OpenFrame Batch, a solução que substitui os sistemas de lote do computador principal.
- TACF, um módulo de serviço que controla o acesso do utilizador a sistemas e recursos.
- ProSort, uma ferramenta de tipo para transações de lotes.
- OFCOBOL, um compilador que interpreta os programas COBOL do mainframe.
- OFASM, um compilador que interpreta os programas de montagem do computador principal.
- OpenFrame Server Type C (OSC), a solução que substitui o middleware do mainframe e o CICS IBM.
- Java Enterprise User Solution (JEUS), um servidor de aplicações web certificado para java Enterprise Edition 6.
- OFGW, o componente de gateway OpenFrame que fornece um ouvinte 3270.
- OFManager, uma solução que fornece as funções de funcionamento e gestão da OpenFrame no ambiente web.

Outros componentes OpenFrame necessários:

- OSI, a solução que substitui o middleware mainframe e o IMS DC.
- TJES, a solução que fornece o ambiente JES do mainframe.
- OFTSAM, a solução que permite utilizar ficheiros SAM (V)SAM no sistema aberto.
- OFHiDB, a solução que substitui o IMS DB do computador principal.
- OFPLI, um compilador que interpreta os programas PL/I do computador principal.
- PROTRIEVE, uma solução que executa a língua principal CA-Easytrieve.
- A OFMiner, uma solução que analisa os ativos dos mainframes e depois migra-os para o Azure.

## <a name="architecture"></a>Arquitetura

A figura que se segue fornece uma visão geral dos componentes arquitetónicos OpenFrame 7.0 instalados neste tutorial:

![Componentes OpenFrame](media/openframe-02.png)

## <a name="azure-system-requirements"></a>Requisitos do sistema Azure

A tabela seguinte enumera os requisitos para a instalação no Azure.
<!-- markdownlint-disable MD033 -->

<table>
<thead>
    <tr><th>Requisito</th><th>Descrição</th></tr>
</thead>
<tbody>
<tr><td>Distribuição de Linux suportada no Azure
</td>
<td>
Linux x86 2.6 (32-bit, 64-bit)<br/>
Chapéu Vermelho 7.x<br/>
CentOS 7.x<br/>
</td>
</tr>
<tr><td>Hardware
</td>
<td>Núcleos: 2 (mínimo)<br/>
Memória: 4 GB (mínimo)<br/>
Espaço de troca: 1 GB (mínimo)<br/>
Disco rígido: 100 GB (mínimo)<br/>
</td>
</tr>
<tr><td>Software opcional para utilizadores do Windows
</td>
<td>PuTTY: Usado neste guia para configurar funcionalidades vm<br/>
WinSCP: Um cliente popular da SFTP e cliente FTP que pode usar<br/>
Eclipse para Windows: Uma plataforma de desenvolvimento suportada pela TmaxSoft<br/>
(O Microsoft Visual Studio não é suportado neste momento)
</td>
</tr>
</tbody>
</table>

<!-- markdownlint-enable MD033 -->

## <a name="prerequisites"></a>Pré-requisitos

Planeie passar alguns dias para montar todo o software necessário e completar todos os processos manuais.

Antes de começar, faça o seguinte:

- Obtenha os meios de instalação OpenFrame da TmaxSoft. Se for um cliente TmaxSoft existente, contacte o seu representante DamaxSoft para obter uma cópia licenciada. Caso contrário, solicite uma versão experimental da [TmaxSoft](https://www.tmaxsoft.com/contact/).

- Solicite a documentação OpenFrame enviando e-mail para <support@tmaxsoft.com>.

- Obtenha uma assinatura Azure se ainda não tiver uma. Também pode criar uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

- Opcional. Instale um túnel VPN site-to-site ou uma caixa de salto que restrinja o acesso ao Azure VM aos utilizadores permitidos na sua organização. Este passo não é necessário, mas é uma boa prática.

## <a name="set-up-a-vm-on-azure-for-openframe-and-tibero"></a>Configurar um VM em Azure para OpenFrame e Tibero

Pode configurar o ambiente OpenFrame utilizando vários padrões de implementação, mas o procedimento seguinte mostra como implementar o servidor de aplicação OpenFrame e a base de dados Tibero num VM. Em ambientes maiores e para cargas de trabalho consideráveis, a melhor prática é implantar a base de dados separadamente no seu próprio VM para um melhor desempenho.

**Para criar um VM**

1. Vá ao portal Azure <https://portal.azure.com> e inscreva-se na sua conta.

2. Clique em **Máquinas virtuais**.

    ![Lista de recursos no portal Azure](media/vm-01.png)

3. Clique em **Adicionar**.

    ![Adicionar opção no portal Azure](media/vm-02.png)

4. À direita dos **Sistemas Operativos,** clique **em Mais**.

     ![Mais opção no portal Azure](media/vm-03.png)

5. Clique em **7.3 baseado em CentOS** para acompanhar exatamente este walk-through, ou pode escolher outra distribuição linux suportada.

     ![Opções do Sistema Operativo no portal Azure](media/vm-04.png)

6. Nas definições **básicas,** introduza **nome**, nome de **utilizador,** tipo de **autenticação,** **Subscrição** (Pay-As-You-Go é o estilo de pagamento AWS) e **grupo Derecursos** (utilize um existente ou crie um grupo TmaxSoft).

7. Quando estiver concluído (incluindo o par de chaves público/privado para **o tipo de autenticação),** clique em **Submeter**.

> [!NOTE]
> Se utilizar uma chave pública SSH para o tipo de **autenticação,** consulte os passos na secção seguinte para gerar o par de chaves público/privado e, em seguida, retome os passos aqui.

### <a name="generate-a-publicprivate-key-pair"></a>Gerar um par de chaves público/privado

Se estiver a utilizar um sistema operativo Windows, precisa de PuTTYgen para gerar um par de chaves público/privado.

A chave pública pode ser livremente partilhada, mas a chave privada deve ser mantida inteiramente secreta e nunca deve ser partilhada com outra parte. Depois de gerar as teclas, deve colar a **chave pública SSH** na configuração — na prática, enviando-a para o VM Linux. É armazenado dentro de\_chaves \~autorizadas dentro do diretório /ssh do diretório da conta de utilizador. O Linux VM é então capaz de reconhecer e validar a ligação uma vez que fornece a **chave privada SSH** associada no cliente SSH (no nosso caso, PuTTY).

Ao dar acesso a novos indivíduos o VM: 

- Cada novo indivíduo gera as suas próprias chaves públicas/privadas usando PuTTYgen.
- Os indivíduos armazenam as suas próprias chaves privadas separadamente e enviam a informação da chave pública ao administrador da VM.
- O administrador cola o conteúdo da \~chave pública ao ficheiro\_de chaves /.ssh/autorizado.
- O novo indivíduo liga-se via PuTTY.

**Para gerar um par de chaves público/privado**

1.  Baixe o PuTTYgen de e <https://www.putty.org/> instale-o utilizando todas as definições predefinidas.

2.  Para abrir o PuTTYgen, localize o\\diretório\\de instalação PuTTY em C: Program Files PuTTY.

    ![Interface PuTTY](media/puttygen-01.png)

3.  Clique em **Generate** (Gerar).

    ![Caixa de diálogo do gerador de chaves PuTTY](media/puttygen-02.png)

4.  Após a geração, guarde a chave pública e a chave privada. Colar o conteúdo da chave pública na secção **chave pública SSH** do painel De base da máquina ** \> virtual Create** (mostrado nos passos 6 e 7 na secção anterior).

    ![Caixa de diálogo do gerador de chaves PuTTY](media/puttygen-03.png)

### <a name="configure-vm-features"></a>Configurar funcionalidades VM

1. No portal Azure, no portal Escolha uma lâmina **de tamanho,** escolha as definições de hardware da máquina Linux que deseja. Os requisitos *mínimos* para a instalação tanto do Tibero como do OpenFrame são 2 CPUs e 4 GB DE RAM, como mostra esta instalação de exemplo:

    ![Criar máquina virtual - Básicos](media/create-vm-01.png)

2. Clique em **3 Definições** e use as definições predefinidas para configurar as funcionalidades opcionais.
3. Reveja os seus dados de pagamento.

    ![Criar máquina virtual - Comprar](media/create-vm-02.png)

4. Submeta as suas seleções. O Azure começa a implantar o VM. Este processo normalmente demora alguns minutos.

5. Quando o VM é implantado, o seu painel de instrumentos é apresentado, mostrando todas as definições selecionadas durante a configuração. Tome nota do **endereço IP público**.

    ![tmax no painel azure](media/create-vm-03.png)

6. Abra o PuTTY.

7. Para **nome do anfitrião,** escreva o seu nome de utilizador e o endereço IP público que copiou. Por exemplo, **nome de\@utilizador publicip**.

    ![Caixa de diálogo de configuração PuTTY](media/putty-01.png)

8. Na caixa **categoria,** clique em **Ligação \> SSH \> Auth**. Forneça o caminho para o seu ficheiro **de chave privada.**

    ![Caixa de diálogo de configuração PuTTY](media/putty-02.png)

9. Clique **em Abrir** para lançar a janela PuTTY. Se for bem sucedido, está ligado ao seu novo CentOS VM a funcionar no Azure.

10. Para iniciar sessão como utilizador de raiz, escreva **a bash sudo**.

    ![Logon do utilizador raiz na janela de comando](media/putty-03.png)

## <a name="set-up-the-environment-and-packages"></a>Configurar o ambiente e os pacotes

Agora que o VM é criado e está ligado, tem de executar alguns passos de configuração e instalar os pacotes de pré-instalação necessários.

1. Mapeie o nome **dedemo** para o endereço IP`vi /etc/hosts`local usando vi para editar o ficheiro anfitriões (). Assumindo que o nosso IP é 192.168.96.148 ofdemo, isto é antes da mudança:

    ```vi
    127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4 
    ::1              localhost localhost.localdomain localhost6 localhost6.localdomain 
    <IP Address>    <your hostname>
    ```

     Isto é depois da mudança:

    ```vi
    127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4 
    ::1              localhost localhost.localdomain localhost6 localhost6.localdomain 
    192.168.96.148   ofdemo
    ```

2. Criar grupos e utilizadores:

    ```vi
    [root@ofdemo ~]# adduser -d /home/oframe7 oframe7 
    [root@ofdemo ~]# passwd oframe7
    ```

3. Alterar a palavra-passe para o utilizador oframe7:

    ```vi
    New password: 
    Retype new password: 
    passwd: all authentication tokens updated successfully.
    ```

4. Atualização dos parâmetros de kernel em /etc/sysctl.conf:

    ```vi
    [root@ofdemo ~]# vi /etc/sysctl.conf
    kernel.shmall = 7294967296 
    kernel.sem = 10000 32000 10000 10000
    ```

5. Refresque os parâmetros do núcleo dinamicamente sem reiniciar:

    ```vi
    [root@ofdemo ~]# /sbin/sysctl -p
    ```

6. Obtenha os pacotes necessários: Certifique-se de que o servidor está ligado à Internet, descarregue os seguintes pacotes e, em seguida, instale-os:

     - dos2unix
     - glibc
     - glibc.i686 glibc.x86\_64
     - libaio
     - ncurses

          > [!NOTE]
          > Depois de instalar o pacote ncurses, crie as seguintes ligações simbólicas:
         ```
         ln -s /usr/lib64/libncurses.so.5.9 /usr/lib/libtermcap.so
         ln -s /usr/lib64/libncurses.so.5.9 /usr/lib/libtermcap.so.2
         ```

     - gcc
     - gcc-c++
     - libaio-devel.x86\_64
     - traço
     - ltrace
     - gdb

7. No caso da instalação Java RPM, faça o seguinte:

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

## <a name="install-the-tibero-database"></a>Instale a base de dados Tibero

Tibero fornece as várias funções-chave no ambiente OpenFrame em Azure:

- Tibero é usado como o armazenamento de dados interno OpenFrame para várias funções do sistema.
- Os ficheiros VSAM, incluindo KSDS, RRDS e ESDS, utilizam internamente a base de dados Tibero para armazenamento de dados.
- O repositório de dados TACF é armazenado em Tibero.
- As informações do catálogo OpenFrame estão armazenadas no Tibero.
- A base de dados Tibero pode ser usada como substituto da IBM Db2 para armazenar dados de aplicações.

**Para instalar o Tibero**

1. Verifique se o ficheiro do instalador binário Tibero está presente e reveja o número da versão.
2. Copie o software Tibero para a conta de utilizador tibero (oframe). Por exemplo:

    ```
    [oframe7@ofdemo ~]$ tar -xzvf tibero6-bin-6_rel_FS04-linux64-121793-opt-tested.tar.gz 
    [oframe7@ofdemo ~]$ mv license.xml /opt/tmaxdb/tibero6/license/
    ```

3. Abra o\_perfil .bash em vi (`vi .bash_profile`) e cola o seguinte nele:

    ```
    # Tibero6 ENV
    export TB_HOME=/opt/tmaxdb/tibero6 
    export TB_SID=TVSAM export TB_PROF_DIR=$TB_HOME/bin/prof 
    export LD_LIBRARY_PATH=$TB_HOME/lib:$TB_HOME/client/lib:$LD_LIBRARY_PATH 
    export PATH=$TB_HOME/bin:$TB_HOME/client/bin:$PATH
    ```

4. Para executar o perfil de bash, no tipo de pedido de comando:

    ```
    source .bash_profile
    ```

5. Gere o ficheiro de ponta (um ficheiro de configuração para tibero), e abra-o em vi. Por exemplo:

    ```
    [oframe7@ofdemo ~]$ sh $TB_HOME/config/gen_tip.sh
    [oframe7@ofdemo ~]$ vi $TB_HOME/config/$TB_SID.tip
    ```

6. Modificar \$\_TB HOME/cliente/config/tbdsn.tbr e colocar 127.0.0.1 em vez de local de acolhimento, como mostrado:

    ```
    TVSAM=( 
    (INSTANCE=(HOST=127.0.0.1)
        (PT=8629)
    (DB_NAME=TVSAM)
          )
     )
    ```

7. Criar a base de dados. É apresentado o seguinte resultado:

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

8. Para reciclar Tibero, primeiro desligue-o usando o `tbdown` comando. Por exemplo:

    ```
    [oframe7@ofdemo ~]$$ tbdown 
    Tibero instance terminated (NORMAL mode).
    ```

9. Agora arranque tibero usando `tbboot`. Por exemplo:

    ```
    [oframe7@ofdemo ~]$ tbboot
    Change core dump dir to /opt/tmaxdb/tibero6/bin/prof. Listener port = 8629

    Tibero 6  
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Tibero instance started up (NORMAL mode).
    ```

10. Para criar um espaço de mesa, aceda à base de dados utilizando o utilizador SYS (sys/tmax), em seguida, crie o espaço de mesa necessário para o volume predefinido e TACF:

    ```
    [oframe7@ofdemo ~]$ tbsql tibero/tmax
    tbSQL 6  
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Connected to Tibero.
    ```

11. Digite agora os seguintes comandos SQL:

    ```
    SQL> create tablespace "DEFVOL" datafile 'DEFVOL.dbf' size 500M autoextend on; create tablespace "TACF00" datafile 'TACF00.dbf' size 500M autoextend on; create tablespace "OFM_REPOSITORY" datafile 'ofm_repository.dbf' size 300M autoextend on;
    SQL> Tablespace 'DEFVOL' created.
    SQL> Tablespace 'TACF00' created.
    SQL> Tablespace ' OFM_REPOSITORY ' created.
    SQL> SQL> Disconnected.
    ```

12. Boot Tibero e verifique se os processos tibero estão em execução:

    ```
    [oframe7@ofdemo ~]$ tbboot 
    ps -ef | egrep tbsvr
    ```

Saída:

![Saída de Tibero](media/tibero-01.png)

## <a name="install-odbc"></a>Instalar o ODBC

As aplicações no OpenFrame comunicam com a base de dados Tibero utilizando a API ODBC fornecida pelo projeto unixODBC de código aberto.

Para instalar o ODBC:

1. Verifique se o ficheiro instalador unixODBC-2.3.4.tar.gz `wget unixODBC-2.3.4.tar.gz` está presente ou utilize o comando. Por exemplo:

     ```
     [oframe7@ofdemo ~]$ wget ftp://ftp.unixodbc.org/pub/unixODBC/unixODBC-2.3.4.tar.gz
     ```

2. Desaperte o binário. Por exemplo:

     ```
     [oframe7@ofdemo ~]$ tar -zxvf unixODBC-2.3.4.tar.gz
     ```

3. Navegue para o diretório unixODBC-2.3.4 e gere o Makefile utilizando as informações da máquina de verificação. Por exemplo:

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ ./configure --prefix=/opt/tmaxapp/unixODBC/ --sysconfdir=/opt/tmaxapp/unixODBC/etc
     ```

     Por predefinição, o unixODBC está instalado em `--prefix` /usr /local, por isso passa um valor para alterar a localização. Da mesma forma, os ficheiros de configuração `--sysconfdir` são instalados em /etc por padrão, por isso passa o valor da localização desejada.

4. Executar Makefile:`[oframe7@ofdemo unixODBC-2.3.4]$ make`

5. Copie o ficheiro executável no diretório do programa após a compilação. Por exemplo:

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ make install
     ```

6. Use vi para editar`vi ~/.bash_profile`o perfil de bash e adicionar o seguinte:

     ```
     # UNIX ODBC ENV 
     export ODBC_HOME=$HOME/unixODBC 
     export PATH=$ODBC_HOME/bin:$PATH 
     export LD_LIBRARY_PATH=$ODBC_HOME/lib:$LD_LIBRARY_PATH 
     export ODBCINI=$HOME/unixODBC/etc/odbc.ini 
     export ODBCSYSINI=$HOME
     ```

7. Aplique a ODBC. Editar os seguintes ficheiros em conformidade. Por exemplo:

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

8. Criar uma ligação simbólica e validar a ligação à base de dados tibero:

     ```
     [oframe7@ofdemo ~]$ ln $ODBC_HOME/lib/libodbc.so $ODBC_HOME/lib/libodbc.so.1 [oframe7@ofdemo ~]$ ln $ODBC_HOME/lib/libodbcinst.so 
     $ODBC_HOME/lib/libodbcinst.so.1

     [oframe7@ofdemo lib]$ isql TVSAM tibero tmax
     ```

A seguinte saída é visualizada:

![Saída ODBC mostrando ligado ao SQL](media/odbc-01.png)

## <a name="install-openframe-base"></a>Instalar base OpenFrame

O servidor de aplicações Base é instalado antes dos serviços individuais que o OpenFrame utiliza para gerir o sistema no Azure, incluindo os processos de servidor de manipulação de transações.

**Para instalar a Base OpenFrame**

1. Certifique-se de que a instalação Tibero\_foi\_bem\_sucedida\_e\_verifique se estão presentes os seguintes ficheiros de instalação OpenFrame Base7 0 Linux x86 64.bin e ficheiro de configuração base.properties.

2. Atualize o perfil da bash com as seguintes informações específicas de Tibero:

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

3. Execute o perfil da festa:`[oframe7@ofdemo ~]$ . .bash_profile`
4. Certifique-se de que os processos do Tibero estão em execução. Por exemplo:

     ```linux
     [oframe7@ofdemo ~]$ ps -ef|grep tbsvr
     ```

    ![Base](media/base-01.png)

     > [!IMPORTANT]
     > Certifique-se de que inicia o Tibero antes da instalação.

5. Gere licença em [technet.tmaxsoft.com](https://technet.tmaxsoft.com/en/front/main/main.do) e COLOQUE as licenças OpenFrame Base, Batch, TACF, OSC na pasta apropriada:

     ```
     [oframe7@ofdemo ~]$ cp license.dat /opt/tmaxapp/OpenFrame/core/license/
     [oframe7@ofdemo ~]$ cp lictjes.dat lictacf.dat licosc.dat $OPENFRAME_HOME/license/
     ```

6. Descarregue os ficheiros binários e base.properties da Base OpenFrame:

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

7. Execute o instalador utilizando o ficheiro base.properties. Por exemplo:

    ```
    [oframe7@ofdemo ~]$ chmod a+x OpenFrame_Base7_0_Linux_x86_64.bin 
    [oframe7@ofdemo ~]$ ./OpenFrame_Base7_0_Linux_x86_64.bin -f base.properties
    ```

    Quando estiver concluída, a mensagem Completa de Instalação é dissipada.

8. Verifique a estrutura de diretório `ls -ltr` da Base OpenFrame utilizando o comando. Por exemplo:

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

9. Iniciar base openframe:

     ```
     [oframe7@ofdemo ~]$ cp /usr/lib/libtermcap.so.2 $TMAXDIR/lib
     Startup Tmax Server
     [oframe7@ofdemo ~]$ tmboot
     ```

     ![saída de comando tmboot](media/base-02.png)

10. Verifique se o estado do processo está pronto usando o comando tmadmin em si. RDY é apresentado na coluna de **estado** para cada um dos processos:

     ![saída de comando tmadmin](media/base-03.png)

11. Desligue a Base OpenFrame:

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

## <a name="install-openframe-batch"></a>Instalar lote openframe

O OpenFrame Batch é composto por vários componentes que simulam ambientes de lote principal e são usados para executar trabalhos de lote em Azure.

**Para instalar lote**

1. Certifique-se de que a instalação da\_base\_\_foi\_bem sucedida e, em seguida, verifique se o ficheiro de instalação do OpenFrame Batch7 0 Fix2 MVS\_Linux\_x86\_64.bin e o ficheiro de configuração do lote.properties estão presentes:

2. No pedido de `vi batch.properties` comando, digite para editar o ficheiro batch.properties usando vi.

3. Modificar os parâmetros da seguinte forma:

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

4. Para executar o instalador do lote, no tipo de pedido de comando:

     ```
     ./OpenFrame_Batch7_0_Fix2_MVS_Linux_x86_64.bin -f batch.properties
     ```

5. Quando a instalação estiver concluída, inicie as `tmboot` suites OpenFrame instaladas digitando o pedido de comando.

    ![saída de tmboot](media/tmboot-01.png)

6. Digite `tmadmin` no pedido de comando para verificar o processo OpenFrame.

    ![Tela tmax admin](media/tmadmin-01.png)

7. Executar os seguintes comandos:

     ```
     $$2 NODE1 (tmadm): quit 
     ADM quit for node (NODE1)
     ```

8. Utilize `tmdown` o comando para ligar e desligar o Lote:

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

## <a name="install-tacf"></a>Instalar tacf

O TACF Manager é um módulo de serviço OpenFrame que controla o acesso dos utilizadores a sistemas e recursos através da segurança RACF.

**Para instalar o TACF**

1. Verifique se o\_ficheiro\_de\_instalação Do Conjunto Tacf7 0 Fix2\_Linux\_x86\_64.bin e o ficheiro de configuração tacf.properties estão presentes.
2. Certifique-se de que a instalação do Lote foi bem`vi tacf.properties`sucedida e, em seguida, use vi para abrir o ficheiro tacf.properties ().
3. Modificar os parâmetros TACF:

     ```
     OPENFRAME_HOME=/opt/tmaxapp/OpenFrame 
     USE_OS_AUTH=NO 
     TACF_USERNAME=tibero 
     TACF_PASSWORD=tmax 
     TACF_DATABASE=oframe 
     TACF_TABLESPACE=TACF00 
     TACF_TABLE_CREATE=YES
     ```

4. Depois de completar o instalador TACF, aplique as variáveis ambientais TACF. Na linha de comandos, escreva:

     ```
     source \~/.bash\_profile
     ```

5. Executar o instalador TACF. Na linha de comandos, escreva:

     ```
     ./OpenFrame_Tacf7_0_Fix2_Linux_x86_64.bin -f tacf.properties
     ```

     A saída é mais ou menos assim:

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

6. No pedido de `tmboot` comando, escreva para reiniciar o OpenFrame. A saída é mais ou menos assim:

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

7. Verifique se o estado `tmadmin` do `si` processo está pronto a ser utilizado no comando. Por exemplo:

     ```
     [oframe7\@ofdemo \~]\$ tmadmin
     ```

     Na coluna de **estado,** RDY aparece:

    ![RDY na coluna de estado](media/tmboot-02.png)

8. Executar os seguintes comandos:

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

9. Desligue o servidor `tmdown` utilizando o comando. A saída é mais ou menos assim:

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

## <a name="install-prosort"></a>Instalar Prosort

ProSort é um utilitário usado em transações de lote para classificar dados.

**Para instalar proSort**

1. Certifique-se de que a instalação do Lote foi bem sucedida e, em seguida, verifique se o ficheiro instalador **\_prosort-bin-prosort 2sp3-linux64-2123-opt.tar.gz** está presente.

2. Execute o instalador utilizando o ficheiro de propriedades. Na linha de comandos, escreva:

     ```
     tar -zxvf prosort-bin-prosort\_2sp3-linux64-2123-opt.tar.gz
     ```

3. Mova o diretório prosort para a localização da casa. Na linha de comandos, escreva:

     ```
     mv prosort /opt/tmaxapp/prosort
     ```

4. Crie um subdiretório de licença e copie o ficheiro de licença lá. Por exemplo:

     ```
     cd /opt/tmaxapp/prosort 
     mkdir license 
     cp /opt/tmaxsw/oflicense/prosort/license.xml /opt/tmaxapp/prosort/license
     ```

5. Abra bash.profile em`vi .bash_profile`vi ( ) e atualize-o da seguinte forma:

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

6. Para executar o perfil de bash, no pedido de comando, escreva:`. .bash_profile`

7. Crie o ficheiro de configuração. Por exemplo:

     ```
     oframe@oframe7: cd /opt/tmaxapp/prosort/config 
     oframe@oframe7: ./gen_tip.sh 
     Using PROSORT_SID "gbg"
      /home/oframe7/prosort/config/gbg.tip generated
     ```

8. Criar a ligação simbólica. Por exemplo:

     ```
     oframe@oframe7: cd /opt/tmaxapp/OpenFrame/util/ 
     oframe@oframe7home/oframe7/OpenFrame/util :  ln -s DFSORT SORT
     ```

9. Verifique a instalação ProSort `prosort -h` executando o comando. Por exemplo:

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

## <a name="install-ofcobol"></a>Instalação OFCOBOL

OFCOBOL é o compilador OpenFrame que interpreta os programas COBOL do mainframe. 

**Para instalar a OFCOBOL**

1. Certifique-se de que a instalação Batch/Online\_foi bem\_\_sucedida\_e,\_em\_seguida, verifique se o ficheiro de instalação OpenFrame COBOL3 0 40 Linux x86 64.bin está presente.

2. Para executar o instalador OFCOBOL, no pedido de comando, escreva:

     ```
      ./OpenFrame\_COBOL3\_0\_40\_Linux\_x86\_64.bin
     ```

3. Leia o acordo de licenciamento e pressione Enter para continuar.

4. Aceite o acordo de licenciamento. Quando a instalação estiver concluída, aparece o seguinte:

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

5. Abra o perfil de`vi .bash_profile`bash em vi e verifique se é atualizado com variáveis OFCOBOL.
6. Execute o perfil da festa. Na linha de comandos, escreva:

     ```
      source ~/.bash_profile
     ```

7. Copie a licença OFCOBOL para a pasta instalada. Por exemplo:
     ```
     mv licofcob.dat $OFCOB_HOME/license
     ```
8. Vá ao ficheiro de configuração OpenFrame tjclrun.conf e abra-o em vi. Por exemplo:
     ```
     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/config 
     [oframe7@ofdemo ~]$ vi tjclrun.conf
     ```

   Aqui está a secção SYSLIB antes da mudança:
     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bin LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${COBDIR}/lib:/ usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib
     ```
   Aqui está a secção SYSLIB após a mudança:
     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bin LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${COBDIR}/lib:/ usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib :${ODBC_HOME}/lib 
     :${OFCOB_HOME}/lib
     ```
9. Reveja o\_ficheiro\_OpenFrame COBOL InstallLog.log em vi e verifique se não existem erros. Por exemplo:
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
10. Utilize `ofcob --version` o comando e reveja o número da versão para verificar a instalação. Por exemplo:

     ```
     [oframe7@ofdemo ~]$ ofcob --version 
     OpenFrame COBOL Compiler 3.0.54 
     CommitTag:: 645f3f6bf7fbe1c366a6557c55b96c48454f4bf
     ```

11. Reiniciar o OpenFrame utilizando o `tmdown/tmboot` comando.

## <a name="install-ofasm"></a>Instalação ofasm

OFASM é o compilador OpenFrame que interpreta os programas de montagem do computador principal.

**Para instalar o OFASM**

1. Certifique-se de que a instalação Batch/Online\_foi bem\_\_sucedida\_e,\_em seguida, verifique se o ficheiro instalador OpenFrame ASM3 0 Linux x86 64.bin está presente.

2. Execute o instalador. Por exemplo:

     ```
     [oframe7@ofdemo ~]$ ./OpenFrame_ASM3_0_Linux_x86_64.bin
     ```

3. Leia o acordo de licenciamento e pressione Enter para continuar.
4. Aceite o acordo de licenciamento.
5. Verifique se o perfil de bash é atualizado com variáveis OFASM. Por exemplo:

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

6. Abra o ficheiro de configuração OpenFrame tjclrun.conf em vi e edite-o da seguinte forma:

     ```
     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/config 
     [oframe7@ofdemo ~]$ vi tjclrun.conf
     ```

     Aqui está a secção [SYSLIB] *antes* da alteração:

     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bi n:${OPENFRAME_HOME}/volume_default/SYS1.LOADLIB LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${CO BDIR}/lib:/usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib:${OFCOB_HOM E}/lib:${ODBC_HOME}/lib:${OFPLI_HOME}/lib
     ```

     Aqui está a secção [SYSLIB] *após* a mudança:

     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bi n:${OPENFRAME_HOME}/volume_default/SYS1.LOADLIB LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${CO BDIR}/lib:/usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib:${OFCOB_HOM E}/lib:${ODBC_HOME}/lib:${OFPLI_HOME}/lib:${OFASM_HOME}/lib
     ```

7. Abra o\_ficheiro\_OpenFrame ASM InstallLog.log em vi e verifique se não existem erros. Por exemplo:

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

8. Reboot OpenFrame emitindo um dos seguintes comandos:

     ```
     tmdown / tmboot
     ```

     — ou —

     ```
     oscdown / oscboot
     ```

## <a name="install-osc"></a>Instalar osOsC

OsC é o ambiente OpenFrame semelhante ao IBM CICS que suporta transações OLTP de alta velocidade e outras funções de gestão.

**Para instalar o OSC**

1. Certifique-se de que a instalação da\_base\_foi\_bem\_sucedida\_e\_verifique se o ficheiro de instalação Do Sistema OpenFrame OSC7 0 Fix2 Linux x86 64.bin e o ficheiro de configuração osc.properties estão presentes.
2. Editar os seguintes parâmetros no ficheiro osc.properties:
     ```
     OPENFRAME_HOME=/opt/tmaxapp/OpenFrame OSC_SYS_OSC_NCS_PATH=/opt/tmaxapp/OpenFrame/temp/OSC_NCS OSC_APP_OSC_TC_PATH=/opt/tmaxapp/OpenFrame/temp/OSC_TC
     ```

3. Executar o instalador utilizando o ficheiro de propriedades como mostrado:

     ```
     [oframe7@ofdemo ~]$ chmod a+x OpenFrame_OSC7_0_Fix2_Linux_x86_64.bin [oframe7@ofdemo ~]$ ./OpenFrame_OSC7_0_Fix2_Linux_x86_64.bin -f osc.properties
     ```

     Quando terminada, é apresentada a mensagem "Instalação Completa".

4. Verifique se o perfil da batida é atualizado com variáveis OSC.
5. Reveja o\_ficheiro\_OpenFrame OSC7 0\_Fix2\_InstallLog.log. Deverá ter um aspeto semelhante a:

     ```
     Summary 
     ------ 
     Installation: Successful.

     233 Successes
     0 Warnings
     0 NonFatalErrors
     0 FatalError
     ```

6. Use vi para abrir o ficheiro de configuração de ofsys.seq. Por exemplo:

     ```
     vi $OPENFRAME_HOME/config/ofsys.seq
     ```

7. Nas \#secções \#BASE e BATCH, edite os parâmetros como mostrado.

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

8. Copie o ficheiro da licença. Por exemplo:

     ```
     [oframe7@ofdemo ~]$ cp /home/oframe7/oflicense/ofonline/licosc.dat 

     $OPENFRAME_HOME/license

     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/license 
     oframe@oframe7/OpenFrame/license / ls -l 
     -rwxr-xr-x. 1 oframe mqm 80 Sep 12 01:37 licosc.dat 
     -rwxr-xr-x. 1 oframe mqm 80 Sep  8 09:40 lictacf.dat 
     -rwxrwxr-x. 1 oframe mqm 80 Sep  3 11:54 lictjes.da
     ```

9. Para iniciar e desligar o OSC, inicialize a `osctdlinit OSCOIVP1` região CICS compartilhou a memória digitando no pedido de comando.

10. Corra `oscboot` para iniciar o OSC. A saída é mais ou menos assim:

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

11. Para verificar se o estado do `tmadmin` processo está pronto, utilize o comando em si. Todos os processos devem apresentar RDY na coluna de **estado.**

    ![Processos exibindo RDY](media/tmadmin-02.png)

12. Desligue o OSC `oscdown` utilizando o comando.

## <a name="install-jeus"></a>Instalar JEUS

A JEUS (Java Enterprise User Solution) fornece a camada de apresentação do servidor de aplicações web OpenFrame.

Antes de instalar o JEUS, instale o pacote Apache Ant, que fornece as bibliotecas e ferramentas de linha de comando necessárias para instalar jeus.

**Para instalar a Formiga Apache**

1. Baixe o binário `wget` de Formiga usando o comando. Por exemplo:

     ```
     wget http://apache.mirror.cdnetworks.com/ant/binaries/apacheant-1.9.7-bin.tar.gz
     ```

2. Utilize `tar` a utilidade para extrair o ficheiro binário e movê-lo para um local apropriado. Por exemplo:

     ```
     tar -xvzf apache-ant-1.9.7-bin.tar.gz
     ```

3. Para eficiência, crie uma ligação simbólica:

     ```
     ln -s apache-ant-1.9.7 ant
     ```

4. Abra o perfil de`vi .bash_profile`bash em vi ()e atualize-o com as seguintes variáveis:

     ```
     # Ant ENV
     export ANT_HOME=$HOME/ant 
     export PATH=$HOME/ant/bin:$PATH
     ```

5.  Aplique a variável ambiente modificada. Por exemplo:

     ```
     [oframe7\@ofdemo \~]\$ source \~/.bash\_profile
     ```

**Para instalar jeus**

1. Expanda o instalador utilizando o `tar` utilitário. Por exemplo:

     ```
     [oframe7@ofdemo ~]$ tar -zxvf jeus704.tar.gz
     ```

2. Crie uma pasta`mkdir jeus7` **jeus** e desaperte o binário.
3. Mude para o diretório de **configuração** (ou utilize o parâmetro JEUS para o seu próprio ambiente). Por exemplo:

     ```
     [oframe7@ofdemo ~]$ cd jeus7/setup/
     ```

4. Execute `ant clean-all` antes de executar a construção. A saída é mais ou menos assim:

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

5.  Faça uma cópia de segurança do ficheiro domain-config-template.properties. Por exemplo:

     ```
     [oframe7@ofdemo ~]$ cp domain-config-template.properties domain-configtemplate.properties.bkp
     ```

6. Abra o ficheiro domain-config-template.properties in vi:

     ```
     [oframe7\@ofdemo setup]\$ vi domain-config-template.properties
     ```

7. Mudar `jeus.password=jeusadmin nodename=Tmaxsoft` para`jeus.password=tmax1234 nodename=ofdemo`

8. Execute `ant install` o comando para construir JEUS.
9.  Atualize o\_ficheiro de perfil .bash com as variáveis JEUS, como mostrado:

     ```
     # JEUS ENV 
     export JEUS_HOME=/opt/tmaxui/jeus7 PATH="/opt/tmaxui/jeus7/bin:/opt/tmaxui/jeus7/lib/system:/opt/tmaxui/jeus7/webserver/bin:$ {PATH}" 
     export PATH
     ```

10. Execute o perfil da festa. Por exemplo:

     ```
     [oframe7@ofdemo setup]$ . .bash_profile
     ```

11. *Opcional.* Crie um pseudónimo para facilitar o encerramento e arranque dos componentes JEUS:

     ```     
     # JEUS alias

     alias dsboot='startDomainAdminServer -domain jeus_domain -u administrator -p jeusadmin'
     alias msboot='startManagedServer -domain jeus_domain -server server1 -u administrator -p jeusadmin' 
     alias msdown=‘jeusadmin -u administrator -p tmax1234 "stop-server server1“’ 
     alias dsdown=‘jeusadmin -domain jeus_domain -u administrator -p tmax1234 "local-shutdown“’
     ```

12. Para verificar a instalação, inicie o servidor de administração de domínio como mostrado:

     ```
     [oframe7@ofdemo ~]$ startDomainAdminServer -domain jeus_domain -u administrator -p jeusadmin
     ```

13. Verifique por via web através da sintaxe:

     ```
     http://<IP>:<port>/webadmin/login
     ```

     Por exemplo, <http://192.168.92.133:9736/webadmin/login.> o ecrã de logon aparece:
    
     ![Tela de logon JEUS WebAdmin](media/jeus-01.png)

     > [!NOTE]
     > Se tiver algum problema com a segurança da porta, abra`systemctl stop firewall`a porta 9736 ou desative a firewall ( ).

14. Para alterar o nome de anfitrião para server1, clique em **Bloquear & Editar**e, em seguida, clique no **servidor1**. Na janela do Servidor, altere o nome de anfitrião da seguinte forma:

    1.  Mude o nome de **nodenome** para **ofdemo**.
    2.  Clique **ok** no lado direito da janela.
    3.  Clique **Aplique alterações** no lado inferior esquerdo da janela e para descrição, introduza a alteração do *nome do anfitrião*.

    ![Tela JEUS WebAdmin](media/jeus-02.png)

15. Verifique se a configuração é bem sucedida no ecrã de confirmação.

    ![ecrã do Servidor jeus_domain](media/jeus-03.png)

16. Inicie o processo de servidor gerido "server1" utilizando o seguinte comando:

     ```
     [oframe7@ofdemo ~]$ startManagedServer -domain jeus_domain -server server1 -u administrator -p jeusadmin
     ```

## <a name="install-ofgw"></a>Instalação DAGW

OFGW É o portal OpenFrame que suporta a comunicação entre o emulador terminal 3270 e a base OSI e gere as sessões entre o emulador terminal e o OSI.

**Para instalar a OFGW**

1. Certifique-se de que a JEUS foi instalada com\_\_sucesso\_e, em seguida, verifique se o ficheiro de instalação GENÉRICO.bin OFGW7 0 1 está presente.
2. Execute o instalador. Por exemplo:

     ```
     [oframe7@ofdemo ~]$ ./OFGW7_0_1_Generic.bin
     ````

3. Utilize os seguintes locais para as instruções correspondentes:
     -   Diretório jeus home
     -   Nome do domínio JEUS
     -   Nome do servidor JEUS
     -   Condutor de Tibero
     -   Id do nó tmax dedemo

4. Aceite o resto das predefinições e, em seguida, prima Enter para sair do instalador.

5. Verifique se o URL para OFGW está a funcionar como esperado:

     ```
     Type URL 
     http://192.168.92.133:8088/webterminal/ and press enter
      < IP >               :8088/webterminal/
     ```

     Aparece o seguinte ecrã:

    ![Terminal Web OpenFrame](media/ofgw-01.png)

## <a name="install-ofmanager"></a>Instalação do Manager

O OFManager fornece funções de operação e gestão para o OpenFrame no ambiente web.

**Para instalar o OFManager**

1. Verifique se o\_ficheiro do instalador OFManager7 Generic.bin está presente.
2. Execute o instalador. Por exemplo:

     ```
     OFManager7_Generic.bin
     ```

3.  Pressione Entrar para continuar, em seguida, aceitar o contrato de licença.
4.  Escolha a pasta de instalação.
5.  Aceite as predefinições.
6.  Escolha tibero como base de dados.
7.  Pressione Introduza para sair do instalador.
8.  Verifique se o URL para OFManager está a funcionar como esperado:

     ```
     Type URL http://192.168.92.133:8088/ofmanager and press enter <  IP >  : < PORT >  ofmanager Enter ID:   ROOT 
     Password: SYS1
     ```

O ecrã de partida aparece:

![Ecrã de logon do Tmax OpenFrame Manager](media/ofmanager-01.png)

Isto completa a instalação dos componentes OpenFrame.

## <a name="next-steps"></a>Passos seguintes

Se está a considerar uma migração de computadores principais, o nosso ecossistema parceiro em expansão está disponível para o ajudar. Para obter orientações detalhadas sobre a escolha de uma solução parceira, consulte a Aliança de Modernização da [Plataforma.](https://datamigration.microsoft.com/)

-   [Introdução ao Azure](https://docs.microsoft.com/azure/)
-   [Documentação do Servidor de Integração do Anfitrião (HIS)](https://docs.microsoft.com/host-integration-server/)
-   [Guia de elevador e mudança do Centro de Dados Virtuais azure](https://blogs.msdn.microsoft.com/azurecat/2018/03/12/new-whitepaper-azure-virtual-datacenter-lift-and-shift-guide/)
