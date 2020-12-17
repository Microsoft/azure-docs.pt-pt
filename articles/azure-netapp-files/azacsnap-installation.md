---
title: Instale a ferramenta Azure Application Consistent Snapshot para ficheiros Azure NetApp / Microsoft Docs
description: Fornece um guia para a instalação da ferramenta Azure Application Consistent Snapshot que pode utilizar com ficheiros Azure NetApp.
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 5c2182fc80c3745e0238c378c1cade0530393181
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/17/2020
ms.locfileid: "97632844"
---
# <a name="install-azure-application-consistent-snapshot-tool-preview"></a>Instalar a ferramenta Azure Application Consistent Snapshot (pré-visualização)

Este artigo fornece um guia para a instalação da ferramenta Azure Application Consistent Snapshot que pode utilizar com ficheiros Azure NetApp.

## <a name="introduction"></a>Introdução

O auto-instalador transferível foi concebido para tornar as ferramentas instantâneas fáceis de configurar e executar com privilégios de utilizador não-raiz (por exemplo, azacsnap). O instalador configurará o utilizador e colocará as ferramentas instantâneas na subdirectória dos utilizadores `$HOME/bin` (predefinição = `/home/azacsnap/bin` ).
O instalador tenta determinar as definições e caminhos corretos para todos os ficheiros com base na configuração do utilizador que executa a instalação (por exemplo, raiz). Se os passos pré-requisitos (ativar a comunicação com o armazenamento e o SAP HANA) forem executados como raiz, então a instalação copiará a chave privada e `hdbuserstore` para a localização do utilizador de cópia. No entanto, é possível que as etapas que permitem a comunicação com o back-end de armazenamento e o SAP HANA sejam feitos manualmente por um administrador experiente após a instalação.

## <a name="prerequisites-for-installation"></a>Pré-requisitos para a instalação

Siga as diretrizes para configurar e executar os instantâneos e comandos de recuperação de desastres. Recomenda-se que os seguintes passos sejam concluídos como raiz antes de instalar e utilizar as ferramentas de instantâneo.

1. **O SO está remendado**: Ver patching e configuração SMT em [Como instalar e configurar SAP HANA (Grandes Instâncias) no Azure](/azure/virtual-machines/workloads/sap/hana-installation#setting-up-smt-server-for-suse-linux).
1. **A sincronização do tempo é configurada**. O cliente terá de fornecer um servidor de tempo compatível com NTP e configurar o SO em conformidade.
1. **HANA está instalado** : Consulte as instruções de instalação da HANA na [instalação SAP NetWeaver na base de dados HANA](https://blogs.msdn.microsoft.com/saponsqlserver/2017/11/21/sap-netweaver-installation-on-hana-database/).
1. **[Ativar a comunicação com o armazenamento](#enable-communication-with-storage)** (consulte a secção separada para mais detalhes): O Cliente deve configurar o SSH com um par de chaves privada/pública e fornecer a chave pública para cada nó onde as ferramentas instantâneas estão previstas para serem executadas nas Operações da Microsoft para configuração no back-end de armazenamento.
   1. **Para ficheiros Azure NetApp (consulte secção separada para mais detalhes)**: O Cliente deve gerar o ficheiro de autenticação principal do serviço.
   1. **Para Azure Large Instance (consulte a secção separada para mais detalhes)**: O cliente deve configurar o SSH com um par de chaves privada/pública e fornecer a chave pública para cada nó onde as ferramentas instantâneas estão previstas para serem executadas nas Operações da Microsoft para configuração no back-end de armazenamento.

      Teste-o utilizando sSH para ligar a um dos nóns (por exemplo, `ssh -l <Storage UserName> <Storage IP Address>` ).
      Digite `exit` para o logout do pedido de armazenamento.

      As operações da Microsoft fornecerão o iMI do utilizador de armazenamento e armazenamento no momento do fornecimento.
  
1. **[Ativar a comunicação com o SAP HANA](#enable-communication-with-sap-hana)** (consulte a secção separada para mais detalhes): O Cliente deve configurar um utilizador SAP HANA apropriado com os privilégios necessários para realizar o instantâneo.
   1. Esta definição pode ser testada a partir da linha de comando da seguinte forma usando o texto em `grey`
      1. HANAv1

            `hdbsql -n <HANA IP address> -i <HANA instance> -U <HANA user> "\s"`

      1. HANAv2

            `hdbsql -n <HANA IP address> -i <HANA instance> -d SYSTEMDB -U <HANA user> "\s"`

      - Os exemplos acima são para comunicação não-SSL para SAP HANA.

## <a name="enable-communication-with-storage"></a>Permitir a comunicação com o armazenamento

Esta secção explica como permitir a comunicação com o armazenamento.

### <a name="azure-netapp-files"></a>Azure NetApp Files

Criar diretor de serviços da RBAC

1. Dentro de uma sessão da Azure Cloud Shell, certifique-se de que está ligado à subscrição onde pretende ser associado ao principal de serviço por padrão:

    ```azurecli-interactive
    az account show
    ```

1. Se a subscrição não estiver correta, utilize

    ```azurecli-interactive
    az account set -s <subscription name or id>
    ```

1. Criar um diretor de serviço usando O Azure CLI pelo seguinte exemplo

    ```azurecli-interactive
    az ad sp create-for-rbac --sdk-auth
    ```

    1. Isto deve gerar uma saída como o seguinte exemplo:

        ```output
        {
          "clientId": "00aa000a-aaaa-0000-00a0-00aa000aaa0a",
          "clientSecret": "00aa000a-aaaa-0000-00a0-00aa000aaa0a",
          "subscriptionId": "00aa000a-aaaa-0000-00a0-00aa000aaa0a",
          "tenantId": "00aa000a-aaaa-0000-00a0-00aa000aaa0a",
          "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
          "resourceManagerEndpointUrl": "https://management.azure.com/",
          "activeDirectoryGraphResourceId": "https://graph.windows.net/",
          "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
          "galleryEndpointUrl": "https://gallery.azure.com/",
          "managementEndpointUrl": "https://management.core.windows.net/"
        }
        ```

    > Este comando atribuirá automaticamente o papel de contribuinte do RBAC ao principal de serviço a nível de subscrição, podendo reduzir o âmbito ao grupo de recursos específico onde os seus testes irão criar os recursos.

1. Corte e cole o conteúdo de saída num ficheiro chamado `azureauth.json` armazenado no mesmo sistema que o comando e proteja o ficheiro com `azacsnap` permissões adequadas do sistema.

### <a name="azure-large-instance"></a>Azure Grande Instância

A comunicação com o back-end de armazenamento executa-se por um canal SSH encriptado. Os seguintes passos de exemplo são fornecer orientações sobre a configuração do SSH para esta comunicação.

1. Modificar o `/etc/ssh/ssh_config` ficheiro

    Consulte a seguinte saída onde a `MACs hmac-sha1` linha foi adicionada:

    ```output
    # RhostsRSAAuthentication no
    # RSAAuthentication yes
    # PasswordAuthentication yes
    # HostbasedAuthentication no
    # GSSAPIAuthentication no
    # GSSAPIDelegateCredentials no
    # GSSAPIKeyExchange no
    # GSSAPITrustDNS no
    # BatchMode no
    # CheckHostIP yes
    # AddressFamily any
    # ConnectTimeout 0
    # StrictHostKeyChecking ask
    # IdentityFile ~/.ssh/identity
    # IdentityFile ~/.ssh/id_rsa
    # IdentityFile ~/.ssh/id_dsa
    # Port 22
    Protocol 2
    # Cipher 3des
    # Ciphers aes128-ctr,aes192-ctr,aes256-ctr,arcfour256,arcfour128,aes128-cbc,3des-
    cbc
    # MACs hmac-md5,hmac-sha1,umac-64@openssh.com,hmac-ripemd
    MACs hmac-sha
    # EscapeChar ~
    # Tunnel no
    # TunnelDevice any:any
    # PermitLocalCommand no
    # VisualHostKey no
    # ProxyCommand ssh -q -W %h:%p gateway.example.com
    ```

1. Criar um par de chaves privado/público

    Utilizando o seguinte comando de exemplo para gerar o par de chaves, não introduza uma palavra-passe ao gerar uma chave.

    ```bash
    ssh-keygen -t rsa –b 5120 -C ""
    ```

1. Envie a chave pública para as Operações da Microsoft

    Envie a saída do `cat /root/.ssh/id_rsa.pub` comando (exemplo abaixo) para as Operações da Microsoft para permitir que as ferramentas instantâneas se comuniquem com o subsistema de armazenamento.

    ```bash
    cat /root/.ssh/id_rsa.pub
    ```

    ```output
    ssh-rsa
    AAAAB3NzaC1yc2EAAAADAQABAAABAQDoaRCgwn1Ll31NyDZy0UsOCKcc9nu2qdAPHdCzleiTWISvPW
    FzIFxz8iOaxpeTshH7GRonGs9HNtRkkz6mpK7pCGNJdxS4wJC9MZdXNt+JhuT23NajrTEnt1jXiVFH
    bh3jD7LjJGMb4GNvqeiBExyBDA2pXdlednOaE4dtiZ1N03Bc/J4TNuNhhQbdsIWZsqKt9OPUuTfD
    j0XvwUTLQbR4peGNfN1/cefcLxDlAgI+TmKdfgnLXIsSfbacXoTbqyBRwCi7p+bJnJD07zSc9YCZJa
    wKGAIilSg7s6Bq/2lAPDN1TqwIF8wQhAg2C7yeZHyE/ckaw/eQYuJtN+RNBD
    ```

## <a name="enable-communication-with-sap-hana"></a>Ativar a comunicação com a SAP HANA

As ferramentas instantâneas comunicam com o SAP HANA e precisam de um utilizador com permissões adequadas para iniciar e libertar o ponto de poupança da base de dados. O exemplo a seguir mostra a configuração do utilizador SAP HANA v2 e a `hdbuserstore` comunicação à base de dados SAP HANA.

Os comandos de exemplo a seguir configuram um utilizador (AZACSNAP) no SYSTEMDB em SAP HANA 2.
base de dados, alterar o endereço IP, nomes de utilizador e palavras-passe, conforme apropriado:

1. Ligue-se ao SYSTEMDB para criar o utilizador

    ```bash
    hdbsql -n <IP_address_of_host>:30013 -i 00 -u SYSTEM -p <SYSTEM_USER_PASSWORD>
    ```

    ```output
    Welcome to the SAP HANA Database interactive terminal.

    Type: \h for help with commands
    \q to quit

    hdbsql SYSTEMDB=>
    ```

1. Criar o utilizador

    Este exemplo cria o utilizador AZACSNAP no SYSTEMDB.

    ```sql
    hdbsql SYSTEMDB=> CREATE USER AZACSNAP PASSWORD <AZACSNAP_PASSWORD_CHANGE_ME> NO FORCE_FIRST_PASSWORD_CHANGE;
    ```

1. Conceder permissões ao utilizador

    Este exemplo estabelece a permissão para o utilizador AZACSNAP permitir a realização de uma base de dados instantânea de armazenamento consistente.

    ```sql
    hdbsql SYSTEMDB=> GRANT BACKUP ADMIN, CATALOG READ, MONITORING TO AZACSNAP;
    ```

1. *OPCIONAL* - Evitar que a palavra-passe do utilizador expire

    > [!NOTE]
    > Consulte a política corporativa antes de fazer esta mudança.

   Este exemplo desativa a expiração da palavra-passe para o utilizador AZACSNAP, sem esta alteração a palavra-passe do utilizador expirará evitando que as imagens sejam tomadas corretamente.  

   ```sql
   hdbsql SYSTEMDB=> ALTER USER AZACSNAP DISABLE PASSWORD LIFETIME;
   ```

1. Configurar a Loja de Utilizadores SAP HANA Secure (alterar a palavra-passe) Este exemplo utiliza `hdbuserstore` o comando da concha Linux para configurar a loja de utilizadores SAP HANA Secure.

    ```bash
    hdbuserstore Set AZACSNAP <IP_address_of_host>:30013 AZACSNAP <AZACSNAP_PASSWORD_CHANGE_ME>
    ```

1. Verifique a loja de utilizador SEGURA SAP HANA Para verificar se a loja de utilizador segura está configurada corretamente, utilize o `hdbuserstore` comando para listar a saída semelhante ao exemplo seguinte. Mais detalhes sobre a utilização `hdbuserstore` estão disponíveis no site da SAP.

    ```bash
    hdbuserstore List
    ```

    ```output
    DATA FILE : /home/azacsnap/.hdb/sapprdhdb80/SSFS_HDB.DAT
    KEY FILE : /home/azacsnap/.hdb/sapprdhdb80/SSFS_HDB.KEY

    KEY AZACSNAP
    ENV : <IP_address_of_host>:
    USER: AZACSNAP
    ```

### <a name="additional-instructions-for-using-the-log-trimmer-sap-hana-20-and-later"></a>Instruções adicionais para a utilização do aparador de madeira (SAP HANA 2.0 e posterior)

Se utilizar o aparador de registos, os comandos de exemplo a seguir configuram um utilizador (AZACSNAP) na base de dados (s) DO TENANT num sistema de base de dados SAP HANA 2.0. Lembre-se de alterar o endereço IP, nomes de utilizador e palavras-passe conforme apropriado:

1. Ligue-se à base de dados DO TENANT para criar o utilizador, detalhes específicos do inquilino são `<IP_address_of_host>` e `<SYSTEM_USER_PASSWORD>` .  Além disso, note-se a porta `30015` ( ) necessária para comunicar com a base de dados DO INQUILINO.

    ```bash
    hdbsql -n <IP_address_of_host>:30015 - i 00 -u SYSTEM -p <SYSTEM_USER_PASSWORD>
    ```

    ```output  
    Welcome to the SAP HANA Database interactive terminal.

    Type: \h for help with commands
    \q to quit

    hdbsql TENANTDB=>
    ```

1. Criar o utilizador

    Este exemplo cria o utilizador AZACSNAP no SYSTEMDB.

    ```sql
    hdbsql TENANTDB=> CREATE USER AZACSNAP PASSWORD <AZACSNAP_PASSWORD_CHANGE_ME> NO FORCE_FIRST_PASSWORD_CHANGE;
    ```

1. Conceder permissões ao utilizador

    Este exemplo estabelece a permissão para o utilizador AZACSNAP permitir a realização de uma base de dados instantânea de armazenamento consistente.

    ```sql
    hdbsql TENANTDB=> GRANT BACKUP ADMIN, CATALOG READ, MONITORING TO AZACSNAP;
    ```

1. *OPCIONAL* - Evitar que a palavra-passe do utilizador expire

    > [!NOTE]
    > Consulte a política corporativa antes de fazer esta mudança.

   Este exemplo desativa a expiração da palavra-passe para o utilizador AZACSNAP, sem esta alteração a palavra-passe do utilizador expirará evitando que as imagens sejam tomadas corretamente.  

   ```sql
   hdbsql TENANTDB=> ALTER USER AZACSNAP DISABLE PASSWORD LIFETIME;
   ```

> [!NOTE]  
> Repita estes passos para todas as bases de dados dos inquilinos. É possível obter os detalhes de conexão para todos os inquilinos usando a seguinte consulta SQL contra o SYSTEMDB.

```sql
SELECT HOST, SQL_PORT, DATABASE_NAME FROM SYS_DATABASES.M_SERVICES WHERE SQL_PORT LIKE '3%'
```

Consulte a seguinte consulta de exemplo e saída.

```bash
hdbsql -jaxC -n 10.90.0.31:30013 -i 00 -u SYSTEM -p <SYSTEM_USER_PASSWORD> " SELECT HOST,SQL_PORT, DATABASE_NAME FROM SYS_DATABASES.M_SERVICES WHERE SQL_PORT LIKE '3%' "
```

```output
sapprdhdb80,30013,SYSTEMDB
sapprdhdb80,30015,H81
sapprdhdb80,30041,H82
```

### <a name="using-ssl-for-communication-with-sap-hana"></a>Utilização de SSL para comunicação com SAP HANA

A `azacsnap` ferramenta utiliza o comando da SAP HANA para comunicar com o SAP `hdbsql` HANA. Isto inclui a utilização de opções SSL ao encriptar a comunicação com o SAP HANA. `azacsnap` utiliza `hdbsql` as opções SSL do comando da seguinte forma.

As seguintes são sempre utilizadas quando se utiliza a `azacsnap --ssl` opção:

- `-e` - Ativa a encriptação TLSTLS/SSL. O servidor escolhe o mais alto disponível.
- `-ssltrustcert` - Especifica se valida o certificado do servidor.
- `-sslhostnameincert "*"` - Especifica o nome de anfitrião utilizado para verificar a identidade do servidor. Especificando `"*"` como nome de anfitrião, o nome de anfitrião do servidor não é validado.

A comunicação SSL também requer ficheiros Key Store e Trust Store.  Embora seja possível que estes ficheiros sejam armazenados em locais predefinidos numa instalação Do Linux, para garantir que o material chave correto está a ser utilizado para os vários sistemas SAP HANA (isto é, nos casos em que são utilizados diferentes ficheiros de loja de chaves e lojas de confiança para cada sistema SAP HANA) `azacsnap` espera que os ficheiros de loja de chaves e loja de confiança sejam armazenados no `securityPath` local conforme especificado no ficheiro de `azacsnap` configuração.

#### <a name="key-store-files"></a>Ficheiros da Loja-Chave

- Se utilizar vários SIDs com o mesmo material chave, é mais fácil criar ligações para a localização securityPath tal como definida no `azacsnap` ficheiro config.  Certifique-se de que estes valores existem para cada SID utilizando SSL.
  - Para abertura:
    - `ln $HOME/.ssl/key.pem <securityPath>/<SID>_keystore`
  - Para o commoncrypto:
    - `ln $SECUDIR/sapcli.pse <securityPath>/<SID>_keystore`
- Se utilizar vários SIDs com o material chave diferente por SID, copie (ou mova e mude o nome) os ficheiros para a localização securityPath, tal como definido no `azacsnap` ficheiro configurar SIDs.
  - Para abertura:
    - `mv key.pem <securityPath>/<SID>_keystore`
  - Para o commoncrypto:
    - `mv sapcli.pse <securityPath>/<SID>_keystore`

Quando `azacsnap` as `hdbsql` chamadas, irá adicionar à linha de `-sslkeystore=<securityPath>/<SID>_keystore` comando.

#### <a name="trust-store-files"></a>Ficheiros Trust Store

- Se utilizar vários SIDs com o mesmo material chave, crie ligações rígidas para a localização securityPath, tal como definido no `azacsnap` ficheiro config.  Certifique-se de que estes valores existem para cada SID utilizando SSL.
  - Para abertura:
    - `ln $HOME/.ssl/trust.pem <securityPath>/<SID>_truststore`
  - Para o commoncrypto:
    - `ln $SECUDIR/sapcli.pse <securityPath>/<SID>_truststore`
- Se utilizar vários SIDs com o material chave diferente por SID, copie (ou mova e mude o nome) os ficheiros para a localização securityPath, tal como definido no `azacsnap` ficheiro configurar SIDs.
  - Para abertura:
    - `mv trust.pem <securityPath>/<SID>_truststore`
  - Para o commoncrypto:
    - `mv sapcli.pse <securityPath>/<SID>_truststore`

> [!NOTE]
> O `<SID>` componente dos nomes dos ficheiros deve ser o identificador do sistema SAP HANA, tudo em maiúsão (por exemplo, `H80` , `PR1` etc.)

Quando `azacsnap` as `hdbsql` chamadas, irá adicionar à linha de `-ssltruststore=<securityPath>/<SID>_truststore` comando.

Portanto, correndo `azacsnap -c test --test hana --ssl openssl` onde o está no ficheiro `SID` `H80` config, executaria as ligações da `hdbsql` seguinte forma:

```bash
hdbsql \
    -e \
    -ssltrustcert \
    -sslhostnameincert "*" \
    -sslprovider openssl \
    -sslkeystore ./security/H80_keystore \
    -ssltruststore ./security/H80_truststore
    "sql statement"
```

> [!NOTE]
> O `\` personagem é uma linha de comando para melhorar a clareza dos múltiplos parâmetros passados na linha de comando.

## <a name="installing-the-snapshot-tools"></a>Instalação das ferramentas instantâneas

O auto-instalador transferível foi concebido para tornar as ferramentas instantâneas fáceis de configurar e executar com privilégios de utilizador não-raiz (por exemplo, azacsnap). O instalador configurará o utilizador e colocará as ferramentas instantâneas na subdirectória dos utilizadores `$HOME/bin` (predefinição = `/home/azacsnap/bin` ).

O instalador tenta determinar as definições e caminhos corretos para todos os ficheiros com base na configuração do utilizador que executa a instalação (por exemplo, raiz). Se os passos de configuração anteriores (Ativar a comunicação com o armazenamento e o SAP HANA) forem executados como raiz, então a instalação copiará a chave privada e `hdbuserstore` a localização do utilizador de reserva. No entanto, é possível que as etapas que permitem a comunicação com o back-end de armazenamento e o SAP HANA sejam feitos manualmente por um administrador experiente após a instalação.

> [!NOTE]
> Para as instalações anteriores da SAP HANA em Azure Large Instance, o diretório de ferramentas instantâneas pré-instaladas foi `/hana/shared/<SID>/exe/linuxx86_64/hdb` .

Com os [passos pré-requisitos concluídos,](#prerequisites-for-installation) agora é possível instalar as ferramentas instantâneas utilizando o instalador auto-instalador da seguinte forma:

1. Copie o instalador auto-instalador descarregado para o sistema alvo.
1. Execute o instalador auto-instalador como `root` utilizador, veja o seguinte exemplo. Se necessário, faça o ficheiro executável utilizando o `chmod +x *.run` comando.

Executar o comando do instalador auto-instalador sem quaisquer argumentos mostrará ajuda na utilização do instalador para instalar as ferramentas instantâneas da seguinte forma:

```bash
chmod +x azacsnap_installer_v5.0.run
./azacsnap_installer_v5.0.run
```

```output
Usage: ./azacsnap_installer_v5.0.run [-v] -I [-u <HLI Snapshot Command user>]
./azacsnap_installer_v5.0.run [-v] -X [-d <directory>]
./azacsnap_installer_v5.0.run [-h]

Switches enclosed in [] are optional for each command line.
- h prints out this usage.
- v turns on verbose output.
- I starts the installation.
- u is the Linux user to install the scripts into, by default this is
'azacsnap'.
- X will only extract the commands.
- d is the target directory to extract into, by default this is
'./snapshot_cmds'.
Examples of a target directory are ./tmp or /usr/local/bin
```

> [!NOTE]
> O instalador auto-instalador tem a opção de extrair (-X) as ferramentas instantâneas do pacote sem realizar qualquer criação e configuração do utilizador. Isto permite que um administrador experiente complete os passos de configuração manualmente, ou copie os comandos para atualizar uma instalação existente.

### <a name="easy-installation-of-snapshot-tools-default"></a>Fácil instalação de ferramentas instantâneas (padrão)

O instalador foi concebido para instalar rapidamente as ferramentas instantâneas para SAP HANA em Azure. Por predefinição, se o instalador for executado apenas com a opção -I, fará os seguintes passos:

1. Crie o utilizador Snapshot 'azacsnap', o diretório doméstico e a adesão ao grupo.
1. Configurar o login do utilizador do azacsnap `~/.profile` .
1. Search filesystem para diretórios para adicionar ao azacsnap, `$PATH` estes são tipicamente os caminhos para as ferramentas SAP HANA, tais como `hdbsql` e `hdbuserstore` .
1. Procure o sistema de ficheiros para diretórios a adicionar ao azacsnap's `$LD_LIBRARY_PATH` . Muitos comandos requerem que seja definido um caminho de biblioteca para executar corretamente, isto configura-o para o utilizador instalado.
1. Copie as teclas SSH para armazenamento em linha traseira para azacsnap do utilizador "raiz" (o utilizador que executa a instalação). Isto pressupõe que o utilizador "raiz" já configura a conectividade com o armazenamento
    - ver secção "[Ativar a comunicação com o armazenamento](#enable-communication-with-storage)".
1. Copie a loja de utilizador segura da ligação SAP HANA para o utilizador-alvo, azacsnap. Isto pressupõe que o utilizador "raiz" já configura a loja segura do utilizador – ver secção "Ativar a comunicação com o SAP HANA".
1. As ferramentas instantâneas são extraídas em `/home/azacsnap/bin/` .
1. Os comandos `/home/azacsnap/bin/` têm as suas permissões definidas (propriedade e bit executável, etc.).

O exemplo a seguir mostra a saída correta do instalador quando funciona com a opção de instalação predefinido.

```bash
./azacsnap_installer_v5.0.run -I
```

```output
+-----------------------------------------------------------+
| Azure Application Consistent Snapshot tool Installer      |
+-----------------------------------------------------------+
|-> Installer version '5.0'
|-> Create Snapshot user 'azacsnap', home directory, and set group membership.
|-> Configure azacsnap .profile
|-> Search filesystem for directories to add to azacsnap's $PATH
|-> Search filesystem for directories to add to azacsnap's $LD_LIBRARY_PATH
|-> Copying SSH keys for back-end storage for azacsnap.
|-> Copying HANA connection keystore for azacsnap.
|-> Extracting commands into /home/azacsnap/bin/.
|-> Making commands in /home/azacsnap/bin/ executable.
|-> Creating symlink for hdbsql command in /home/azacsnap/bin/.
+-----------------------------------------------------------+
| Install complete! Follow the steps below to configure.    |
+-----------------------------------------------------------+
+-----------------------------------------------------------+
|  Install complete!  Follow the steps below to configure.  |
+-----------------------------------------------------------+

1. Change into the snapshot user account.....
     su - azacsnap
2. Set up the HANA Secure User Store..... (command format below)
     hdbuserstore Set <ADMIN_USER> <HOSTNAME>:<PORT> <admin_user> <password>
3. Change to location of commands.....
     cd /home/azacsnap/bin/
4. Configure the customer details file.....
     azacsnap -c configure --configuration new
5. Test the connection to storage.....
     azacsnap -c test --test storage
6. Test the connection to HANA.....
   a. without SSL
     azacsnap -c test --test hana
   b. with SSL,  you will need to choose the correct SSL option
     azacsnap -c test --test hana --ssl=<commoncrypto|openssl>
7. Run your first snapshot backup..... (example below)
     azacsnap -c backup --volume=data --prefix=hana_test --frequency=15min --retention=1
```

### <a name="uninstall-the-snapshot-tools"></a>Desinstale as ferramentas instantâneas

Se as ferramentas instantâneas tiverem sido instaladas utilizando as definições predefinidas, a desinstalação requer apenas a remoção do utilizador para o quais os comandos foram instalados (predefinido = azacsnap).

```bash
userdel -f -r azacsnap
```

### <a name="manual-installation-of-the-snapshot-tools"></a>Instalação manual das ferramentas instantâneas

Em alguns casos, é necessário instalar as ferramentas manualmente, mas a recomendação é utilizar a opção padrão do instalador para facilitar este processo.

Cada linha que começa com um `#` personagem demonstra que os comandos de exemplo que seguem o personagem são executados pelo utilizador raiz. O `\` no final de uma linha é o personagem padrão de continuação da linha para um comando de concha.

Como superusador de raiz, uma instalação manual pode ser alcançada da seguinte forma:

1. Obtenha o ID do grupo "sapsys", neste caso o ID do grupo = 1010

    ```bash
    grep sapsys /etc/group
    ```

    ```output
    sapsys:x:1010:
    ```

1. Crie o utilizador Snapshot 'azacsnap', diretório doméstico e definir a adesão ao grupo utilizando o ID do grupo a partir do passo 1.

    ```bash
    useradd -m -g 1010 -c "Azure SAP HANA Snapshots User" azacsnap
    ```

1. Certifique-se de que o login do utilizador azacsnap `.profile` existe.

    ```bash
    echo "" >> /home/azacsnap/.profile
    ```

1. Search filesystem para diretórios para adicionar ao $PATH do Azacsnap, estes são tipicamente os caminhos para as ferramentas SAP HANA, tais como `hdbsql` e `hdbuserstore` .

    ```bash
    HDBSQL_PATH=`find -L /hana/shared/[A-z0-9][A-z0-9][A-z0-9]/HDB*/exe /usr/sap/hdbclient -name hdbsql -exec dirname {} + 2> /dev/null | sort | uniq | tr '\n' ':'`
    ```

1. Adicione o PATH atualizado ao perfil do utilizador

    ```bash
    echo "export PATH=\"\$PATH:$HDBSQL_PATH\"" >> /home/azacsnap/.profile
    ```

1. Procure o sistema de ficheiros para os diretórios para adicionar ao $LD_LIBRARY_PATH do Azacsnap.

    ```bash
    NEW_LIB_PATH=`find -L /hana/shared/[A-z0-9][A-z0-9][A-z0-9]/HDB*/exe /usr/sap/hdbclient -name "*.so" -exec dirname {} + 2> /dev/null | sort | uniq | tr '\n' ':'`
    ```

1. Adicione o caminho da biblioteca atualizado ao perfil do utilizador

    ```bash
    echo "export LD_LIBRARY_PATH=\"\$LD_LIBRARY_PATH:$NEW_LIB_PATH\"" >> /home/azacsnap/.profile
    ```

1. Em Azure Grandes Instâncias
    1. Copie as teclas SSH para armazenamento em linha traseira para azacsnap do utilizador "raiz" (o utilizador que executa a instalação). Isto pressupõe que o utilizador "raiz" já configura a conectividade com o armazenamento
       > ver secção "[Ativar a comunicação com o armazenamento](#enable-communication-with-storage)".

        ```bash
        cp -pr ~/.ssh /home/azacsnap/.
        ```

    1. Desature corretamente as permissões do utilizador para os ficheiros SSH

        ```bash
        chown -R azacsnap.sapsys /home/azacsnap/.ssh
        ```

1. Em ficheiros Azure NetApp
    1. Configure o caminho do utilizador `DOTNET_BUNDLE_EXTRACT_BASE_DIR` de acordo com a orientação de extrato de ficheiro único .NET Core.
        1. SUSE Linux

            ```bash
            echo "export DOTNET_BUNDLE_EXTRACT_BASE_DIR=\$HOME/.net" >> /home/azacsnap/.profile
            echo "[ -d $DOTNET_BUNDLE_EXTRACT_BASE_DIR] && chmod 700 $DOTNET_BUNDLE_EXTRACT_BASE_DIR" >> /home/azacsnap/.profile
            ```

        1. RHEL

            ```bash
            echo "export DOTNET_BUNDLE_EXTRACT_BASE_DIR=\$HOME/.net" >> /home/azacsnap/.bash_profile
            echo "[ -d $DOTNET_BUNDLE_EXTRACT_BASE_DIR] && chmod 700 $DOTNET_BUNDLE_EXTRACT_BASE_DIR" >> /home/azacsnap/.bash_profile
            ```

1. Copie a loja de utilizador segura da ligação SAP HANA para o utilizador-alvo, azacsnap. Isto pressupõe que o utilizador "raiz" já configura a loja segura do utilizador.
    > ver secção "[Ativar a comunicação com o SAP HANA](#enable-communication-with-sap-hana)".

    ```bash
    cp -pr ~/.hdb /home/azacsnap/.
    ```

1. Desa esta medida as permissões do utilizador para os `hdbuserstore` ficheiros

    ```bash
    chown -R azacsnap.sapsys /home/azacsnap/.hdb
    ```

1. Extrair as ferramentas instantâneas em /home/azacsnap/bin/.

    ```bash
    ./azacsnap_installer_v5.0.run -X -d /home/azacsnap/bin
    ```

1. Tornar os comandos executáveis

    ```bash
    chmod 700 /home/azacsnap/bin/*
    ```

1. Certifique-se de que as permissões de propriedade corretas são definidas no diretório do utilizador

    ```bash
    chown -R azacsnap.sapsys /home/azacsnap/*
    ```

### <a name="complete-the-setup-of-snapshot-tools"></a>Complete a configuração de ferramentas instantâneas

O instalador fornece passos para concluir após a instalação das ferramentas instantâneas.
Siga estes passos para configurar e testar as ferramentas instantâneas.  Após um teste bem sucedido, efetue a primeira base de dados instantâneo de armazenamento consistente.

A seguinte saída mostra os passos a completar após a execução do instalador com as opções de instalação predefinidos:

1. Alterar para a conta de utilizador instantânea
    1. `su - azacsnap`
1. Configurar a Loja de Utilizadores Seguras HANA
   1. `hdbuserstore Set <ADMIN_USER> <HOSTNAME>:<PORT> <admin_user> <password>`
1. Alteração à localização dos comandos
   1. `cd /home/azacsnap/bin/`
1. Configure o ficheiro de detalhes do cliente
   1. `azacsnap -c configure –-configuration new`
1. Teste a ligação ao armazenamento.....
   1. `azacsnap -c test –-test storage`
1. Teste a ligação a HANA.....
    1. sem SSL
       1. `azacsnap -c test –-test hana`
    1. com SSL, terá de escolher a opção SSL correta
       1. `azacsnap -c test –-test hana --ssl=<commoncrypto|openssl>`
1. Faça o seu primeiro backup de instantâneo
    1. `azacsnap -c backup –-volume data--prefix=hana_test --retention=1`

O passo 2 será necessário se "[Enable communication with SAP HANA](#enable-communication-with-sap-hana)" não foi feito antes da instalação.

> [!NOTE]
> Os comandos de teste devem ser executados corretamente. Caso contrário, os comandos podem falhar.

## <a name="configuring-the-database"></a>Configurar a base de dados

Esta secção explica como configurar a base de dados.

### <a name="sap-hana-configuration"></a>Configuração SAP HANA

Existem algumas alterações recomendadas a aplicar no SAP HANA para garantir a proteção das cópias de segurança e do catálogo de registos. Por predefinição, o `basepath_logbackup` e irá fazer a `basepath_catalogbackup` produção dos seus ficheiros para o `$(DIR_INSTANCE)/backup/log` diretório, e é improvável que este caminho esteja num volume `azacsnap` configurado para instantâneas destes ficheiros não será protegido com instantâneos de armazenamento.

Os `hdbsql` seguintes exemplos de comando destinam-se a demonstrar a definição dos caminhos de registo e catálogo para locais que se encontram em volumes de armazenamento que podem ser instantâneos por `azacsnap` . Certifique-se de que verifica os valores na linha de comando corresponde à configuração local SAP HANA.

### <a name="configure-log-backup-location"></a>Configurar a localização do backup de registo

Neste exemplo, a alteração está a ser feita ao `basepath_logbackup` parâmetro.

```bash
hdbsql -jaxC -n <HANA_ip_address>:30013 -i 00 -u SYSTEM -p <SYSTEM_USER_PASSWORD> "ALTER SYSTEM ALTER CONFIGURATION ('global.ini', 'SYSTEM') SET ('persistence', 'basepath_logbackup') = '/hana/logbackups/H80' WITH RECONFIGURE"
```

### <a name="configure-catalog-backup-location"></a>Configurar a localização de backup do catálogo

Neste exemplo, a alteração está a ser feita ao `basepath_catalogbackup` parâmetro. Em primeiro lugar, verifique se o `basepath_catalogbackup` caminho existe no sistema de ficheiros, se não criar o caminho com a mesma propriedade que o diretório.

```bash
ls -ld /hana/logbackups/H80/catalog
```

```output
drwxr-x--- 4 h80adm sapsys 4096 Jan 17 06:55 /hana/logbackups/H80/catalog
```

Se o caminho tiver de ser criado, o exemplo a seguir cria o caminho e define a correta propriedade e permissões. Estes comandos terão de ser executados como raiz.

```bash
mkdir /hana/logbackups/H80/catalog
chown --reference=/hana/shared/H80/HDB00 /hana/logbackups/H80/catalog
chmod --reference=/hana/shared/H80/HDB00 /hana/logbackups/H80/catalog
ls -ld /hana/logbackups/H80/catalog
```

```output
drwxr-x--- 4 h80adm sapsys 4096 Jan 17 06:55 /hana/logbackups/H80/catalog
```

O exemplo a seguir alterará a definição SAP HANA.

```bash
hdbsql -jaxC -n <HANA_ip_address>:30013 -i 00 -u SYSTEM -p <SYSTEM_USER_PASSWORD> "ALTER SYSTEM ALTER CONFIGURATION ('global.ini', 'SYSTEM') SET ('persistence', 'basepath_catalogbackup') = '/hana/logbackups/H80/catalog' WITH RECONFIGURE"
```

### <a name="check-log-and-catalog-backup-locations"></a>Verifique as localizações de backup de registos e catálogos

Depois de escamar as alterações acima, confirme que as definições estão corretas com o seguinte comando.
Neste exemplo, as definições que foram definidas seguindo as orientações acima serão apresentadas como definições de SISTEMA.

> Esta consulta também devolve as definições DEFAULT para comparação.

```bash
hdbsql -jaxC -n <HANA_ip_address> - i 00 -U AZACSNAP "select * from sys.m_inifile_contents where (key = 'basepath_databackup' or key ='basepath_datavolumes' or key = 'basepath_logbackup' or key = 'basepath_logvolumes' or key = 'basepath_catalogbackup')"
```

```output
global.ini,DEFAULT,,,persistence,basepath_catalogbackup,$(DIR_INSTANCE)/backup/log
global.ini,DEFAULT,,,persistence,basepath_databackup,$(DIR_INSTANCE)/backup/data
global.ini,DEFAULT,,,persistence,basepath_datavolumes,$(DIR_GLOBAL)/hdb/data
global.ini,DEFAULT,,,persistence,basepath_logbackup,$(DIR_INSTANCE)/backup/log
global.ini,DEFAULT,,,persistence,basepath_logvolumes,$(DIR_GLOBAL)/hdb/log
global.ini,SYSTEM,,,persistence,basepath_catalogbackup,/hana/logbackups/H80/catalog
global.ini,SYSTEM,,,persistence,basepath_datavolumes,/hana/data/H80
global.ini,SYSTEM,,,persistence,basepath_logbackup,/hana/logbackups/H80
global.ini,SYSTEM,,,persistence,basepath_logvolumes,/hana/log/H80
```

### <a name="configure-log-backup-timeout"></a>Configurar o tempo de backup do registo

A definição predefinida para o SAP HANA realizar uma cópia de segurança de registo é de 900 segundos (15 minutos). Recomenda-se reduzir este valor para 300 segundos (ou seja, 5 minutos).  Em seguida, é possível executar cópias de segurança regulares (por exemplo, a cada 10 minutos) adicionando o volume log_backups na secção de volume OTHER do ficheiro de configuração.

```bash
hdbsql -jaxC -n <HANA_ip_address>:30013 -i 00 -u SYSTEM -p <SYSTEM_USER_PASSWORD> "ALTER SYSTEM ALTER CONFIGURATION ('global.ini', 'SYSTEM') SET ('persistence', 'log_backup_timeout_s') = '300' WITH RECONFIGURE"
```

#### <a name="check-log-backup-timeout"></a>Verifique o tempo de backup do registo

Depois de escoar a alteração para o tempo de reserva de registo, verifique se este foi definido da seguinte forma.
Neste exemplo, as definições que foram definidas serão apresentadas como as definições do SISTEMA, mas esta consulta também devolve as definições DEFAULT para comparação.

```bash
hdbsql -jaxC -n <HANA_ip_address> - i 00 -U AZACSNAP "select * from sys.m_inifile_contents where key like '%log_backup_timeout%' "
```

```output
global.ini,DEFAULT,,,persistence,log_backup_timeout_s,900
global.ini,SYSTEM,,,persistence,log_backup_timeout_s,300
```

## <a name="next-steps"></a>Passos seguintes

- [Configure Aplicação Azure Ferramenta Snapshot consistente](azacsnap-cmd-ref-configure.md)
