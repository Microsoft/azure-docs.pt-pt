---
title: Configurar Oracle ASM numa máquina virtual Linux do Azure | Documentos da Microsoft
description: Obtenha rapidamente Oracle ASM cópia de segurança e em execução no seu ambiente do Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: romitgirdhar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/02/2018
ms.author: rogirdh
ms.openlocfilehash: 0af6e87d3e0b4b3b40b63db07384d4a33a9d43e1
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57998961"
---
# <a name="set-up-oracle-asm-on-an-azure-linux-virtual-machine"></a>Configure o Oracle ASM numa máquina virtual do Linux para o Azure  

As máquinas virtuais do Azure proporcionam um ambiente informático totalmente configurável e flexível. Este tutorial abrange a implantação de máquinas virtuais do Azure básico, combinada com a instalação e configuração do Oracle automatizada armazenamento gestão (ASM).  Saiba como:

> [!div class="checklist"]
> * Criar e ligar a uma VM de base de dados Oracle
> * Instalar e configurar a gestão de armazenamento automatizada da Oracle
> * Instalar e configurar a infraestrutura de grade do Oracle
> * Inicializar uma instalação de Oracle ASM
> * Criar uma BD da Oracle geridos por ASM


[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este tutorial requer a execução da versão 2.0.4 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli). 

## <a name="prepare-the-environment"></a>Preparar o ambiente

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Para criar um grupo de recursos, utilize o comando [az group create](/cli/azure/group). Um grupo de recursos do Azure é um contentor lógico no qual os recursos são implementados e geridos. Neste exemplo, um grupo de recursos chamado *myResourceGroup* no *eastus* região.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

### <a name="create-a-vm"></a>Criar uma VM

Para criar uma máquina virtual com base na imagem de base de dados Oracle e configurar para utilizar o Oracle ASM, utilize o [az vm criar](/cli/azure/vm) comando. 

O exemplo seguinte cria uma VM com o nome myVM que é um tamanho de Standard_DS2_v2 com quatro discos de dados anexados, de 50 GB. Se eles ainda não existam numa localização predefinida da chave, ele também cria chaves SSH.  Para utilizar um conjunto específico de chaves, utilize a opção `--ssh-key-value`.  

   ```azurecli-interactive
   az vm create --resource-group myResourceGroup \
    --name myVM \
    --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
    --size Standard_DS2_v2 \
    --generate-ssh-keys \
    --data-disk-sizes-gb 50 50 50 50
   ```

Depois de criar a VM, a CLI do Azure mostra informações semelhantes ao seguinte exemplo. Tenha em atenção o valor para `publicIpAddress`. Utilize este endereço para aceder à VM.

   ```azurecli
   {
     "fqdns": "",
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
     "location": "eastus",
     "macAddress": "00-0D-3A-36-2F-56",
     "powerState": "VM running",
     "privateIpAddress": "10.0.0.4",
     "publicIpAddress": "13.64.104.241",
     "resourceGroup": "myResourceGroup"
   }
   ```

### <a name="connect-to-the-vm"></a>Ligar à VM

Para criar uma sessão SSH com a VM e configurar definições adicionais, utilize o seguinte comando. Substitua o endereço IP com o `publicIpAddress` valor para a sua VM.

```bash 
ssh <publicIpAddress>
```

## <a name="install-oracle-asm"></a>Instale o Oracle ASM

Para instalar o Oracle ASM, conclua os passos seguintes. 

Para obter mais informações sobre como instalar o Oracle ASM, consulte [Oracle ASMLib Downloads para Oracle Linux 6](https://www.oracle.com/technetwork/server-storage/linux/asmlib/ol6-1709075.html).  

1. Tem de iniciar sessão como raiz para poder continuar com a instalação de ASM:

   ```bash
   sudo su -
   ```
   
2. Execute estes comandos adicionais para instalar componentes de Oracle ASM:

   ```bash
    yum list | grep oracleasm 
    yum -y install kmod-oracleasm.x86_64 
    yum -y install oracleasm-support.x86_64 
    wget https://download.oracle.com/otn_software/asmlib/oracleasmlib-2.0.12-1.el6.x86_64.rpm 
    yum -y install oracleasmlib-2.0.12-1.el6.x86_64.rpm 
    rm -f oracleasmlib-2.0.12-1.el6.x86_64.rpm
   ```

3. Certifique-se de que o Oracle ASM está instalado:

   ```bash
   rpm -qa |grep oracleasm
   ```

    O resultado deste comando deve listar os seguintes componentes:

    ```bash
   oracleasm-support-2.1.10-4.el6.x86_64
   kmod-oracleasm-2.0.8-15.el6_9.x86_64
   oracleasmlib-2.0.12-1.el6.x86_64
    ```

4. ASM requer a utilizadores específicos e as funções para funcionar corretamente. Os comandos seguintes criam as contas de utilizador de pré-requisitos e grupos: 

   ```bash
    groupadd -g 54345 asmadmin 
    groupadd -g 54346 asmdba 
    groupadd -g 54347 asmoper 
    useradd -u 3000 -g oinstall -G dba,asmadmin,asmdba,asmoper grid 
    usermod -g oinstall -G dba,asmdba,asmadmin oracle
   ```

5. Certifique-se de que os utilizadores e grupos foram criados corretamente:

   ```bash
   id grid
   ```

    O resultado deste comando deve listar os utilizadores e grupos que se seguem:

    ```bash
    uid=3000(grid) gid=54321(oinstall) groups=54321(oinstall),54322(dba),54345(asmadmin),54346(asmdba),54347(asmoper)
    ```
 
6. Crie uma pasta para o usuário *grid* e alterar o proprietário:

   ```bash
   mkdir /u01/app/grid 
   chown grid:oinstall /u01/app/grid
   ```

## <a name="set-up-oracle-asm"></a>Configurar Oracle ASM

Para este tutorial, o utilizador predefinido é *grade* e o grupo predefinido é *asmadmin*. Certifique-se de que o *oracle* utilizador faz parte do grupo asmadmin. Para configurar a sua instalação do Oracle ASM, conclua os seguintes passos:

1. Configurar o controlador de biblioteca do Oracle ASM envolve a definição do utilizador predefinido (grid) e o grupo predefinido (asmadmin), bem como configurar a unidade para iniciar no arranque (escolher y) e para verificar a existência de discos no arranque (escolher y). Precisa responda aos pedidos do seguinte comando:

   ```bash
   /usr/sbin/oracleasm configure -i
   ```

   O resultado deste comando deve ter um aspeto semelhante ao seguinte, solicita a parar com a serem respondidas.

    ```bash
   Configuring the Oracle ASM library driver.

   This will configure the on-boot properties of the Oracle ASM library
   driver. The following questions will determine whether the driver is
   loaded on boot and what permissions it will have. The current values
   will be shown in brackets ('[]'). Hitting <ENTER> without typing an
   answer will keep that current value. Ctrl-C will abort.

   Default user to own the driver interface []: grid
   Default group to own the driver interface []: asmadmin
   Start Oracle ASM library driver on boot (y/n) [n]: y
   Scan for Oracle ASM disks on boot (y/n) [y]: y
   Writing Oracle ASM library driver configuration: done
   ```

2. Ver a configuração do disco:
   ```bash
   cat /proc/partitions
   ```

   O resultado deste comando deve ser semelhante da seguinte lista de discos disponíveis

   ```bash
   8       16   14680064 sdb
   8       17   14678976 sdb1
   8        0   52428800 sda
   8        1     512000 sda1
   8        2   51915776 sda2
   8       48   52428800 sdd
   8       64   52428800 sde
   8       80   52428800 sdf
   8       32   52428800 sdc
   11       0       1152 sr0
   ```

3. Disco de formato */desenvolvimento/sdc* executando o seguinte comando e responda aos pedidos com:
   - *n* para a nova partição
   - *p* para a partição primária
   - *1* para selecionar a primeira partição
   - Prima `enter` para cilindro primeiro padrão
   - Prima `enter` para cilindro último padrão
   - Prima *w* para escrever as alterações para a tabela de partições  

   ```bash
   fdisk /dev/sdc
   ```
   
   Utilizar as respostas fornecidas acima, a saída do comando de fdisk deve ter um aspeto semelhante ao seguinte:

   ```bash
   Device contains not a valid DOS partition table, or Sun, SGI or OSF disklabel
   Building a new DOS disklabel with disk identifier 0xf865c6ca.
   Changes will remain in memory only, until you decide to write them.
   After that, of course, the previous content won't be recoverable.

   Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)

   The device presents a logical sector size that is smaller than
   the physical sector size. Aligning to a physical sector (or optimal
   I/O) size boundary is recommended, or performance may be impacted.

   WARNING: DOS-compatible mode is deprecated. It's strongly recommended to
           switch off the mode (command 'c') and change display units to
           sectors (command 'u').

   Command (m for help): n
   Command action
     e   extended
     p   primary partition (1-4)
   p
   Partition number (1-4): 1
   First cylinder (1-6527, default 1):
   Using default value 1
   Last cylinder, +cylinders or +size{K,M,G} (1-6527, default 6527):
   Using default value 6527

   Command (m for help): w
   The partition table has been altered!

   Calling ioctl() to re-read partition table.
   Syncing disks.
   ```

4. Repita o comando fdisk anterior para `/dev/sdd`, `/dev/sde`, e `/dev/sdf`.

5. Verifique a configuração do disco:

   ```bash
   cat /proc/partitions
   ```

   A saída do comando deve ter um aspeto semelhante ao seguinte:

   ```bash
   major minor  #blocks  name

     8       16   14680064 sdb
     8       17   14678976 sdb1
     8       32   52428800 sdc
     8       33   52428096 sdc1
     8       48   52428800 sdd
     8       49   52428096 sdd1
     8       64   52428800 sde
     8       65   52428096 sde1
     8       80   52428800 sdf
     8       81   52428096 sdf1
     8        0   52428800 sda
     8        1     512000 sda1
     8        2   51915776 sda2
     11       0    1048575 sr0
   ```

6. Verificar o estado do serviço Oracle ASM e iniciar o serviço de Oracle ASM:

   ```bash
   service oracleasm status 
   service oracleasm start
   ```

   A saída do comando deve ter um aspeto semelhante ao seguinte:
   
   ```bash
   Checking if ASM is loaded: no
   Checking if /dev/oracleasm is mounted: no
   Initializing the Oracle ASMLib driver:                     [  OK  ]
   Scanning the system for Oracle ASMLib disks:               [  OK  ]
   ```

7. Crie discos do Oracle ASM:

   ```bash
   service oracleasm createdisk ASMSP /dev/sdc1 
   service oracleasm createdisk DATA /dev/sdd1 
   service oracleasm createdisk DATA1 /dev/sde1 
   service oracleasm createdisk FRA /dev/sdf1
   ```    

   A saída do comando deve ter um aspeto semelhante ao seguinte:

   ```bash
   Marking disk "ASMSP" as an ASM disk:                       [  OK  ]
   Marking disk "DATA" as an ASM disk:                        [  OK  ]
   Marking disk "DATA1" as an ASM disk:                       [  OK  ]
   Marking disk "FRA" as an ASM disk:                         [  OK  ]
   ```

8. Listar discos da Oracle ASM:

   ```bash
   service oracleasm listdisks
   ```   

   O resultado do comando deve listar dos discos de Oracle ASM seguintes:

   ```bash
    ASMSP
    DATA
    DATA1
    FRA
   ```

9. Altere as palavras-passe para os utilizadores de raiz, oracle e grid. **Anote estas novas palavras-passe** como está a utilizar mais tarde durante a instalação.

   ```bash
   passwd oracle 
   passwd grid 
   passwd root
   ```

10. Altere a permissão de pasta:

    ```bash
    chmod -R 775 /opt 
    chown grid:oinstall /opt 
    chown oracle:oinstall /dev/sdc1 
    chown oracle:oinstall /dev/sdd1 
    chown oracle:oinstall /dev/sde1 
    chown oracle:oinstall /dev/sdf1 
    chmod 600 /dev/sdc1 
    chmod 600 /dev/sdd1 
    chmod 600 /dev/sde1 
    chmod 600 /dev/sdf1
    ```

## <a name="download-and-prepare-oracle-grid-infrastructure"></a>Transferir e preparar a infraestrutura de grade do Oracle

Para transferir e preparar o software de infraestrutura de grade do Oracle, conclua os seguintes passos:

1. Transferir a infraestrutura de grade Oracle a partir da [página de download do Oracle ASM](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-linux-download-2240591.html). 

   Sob o download intitulado **Oracle Database 12C versão 1 Grid infraestrutura (12.1.0.2.0) para Linux x86-64**, transferir os dois arquivos. zip.

2. Depois de transferir os ficheiros. zip para o seu computador cliente, pode utilizar o protocolo de cópia segura (SCP) para copiar os ficheiros para a VM:

   ```bash
   scp *.zip <publicIpAddress>:.
   ```

3. SSH novamente para a sua VM do Oracle no Azure para mover os ficheiros. zip para o / optar ativamente por participar pasta. Em seguida, altere o proprietário dos arquivos:

   ```bash
   ssh <publicIPAddress>
   sudo mv ./*.zip /opt
   cd /opt
   sudo chown grid:oinstall linuxamd64_12102_grid_1of2.zip
   sudo chown grid:oinstall linuxamd64_12102_grid_2of2.zip
   ```

4. Descompacte os arquivos. (O instalar a Linux deszipe ferramenta se ainda não estiver instalado.)
   
   ```bash
   sudo yum install unzip
   sudo unzip linuxamd64_12102_grid_1of2.zip
   sudo unzip linuxamd64_12102_grid_2of2.zip
   ```

5. Permissão de alteração:
   
   ```bash
   sudo chown -R grid:oinstall /opt/grid
   ```

6. Espaço de comutação de atualização configurado. Componente de grade do Oracle precisa de, pelo menos, 6.8 GB de espaço de comutação para instalar a grelha. O tamanho de ficheiro de troca de padrão de imagens do Oracle Linux no Azure é apenas de 2048MB. Precisa de aumentar `ResourceDisk.SwapSizeMB` no `/etc/waagent.conf` de ficheiro e reinicie o serviço de WALinuxAgent para que as definições atualizadas entrar em vigor. Como é um ficheiro só de leitura, terá de alterar as permissões de arquivo para permitir o acesso de escrita.

   ```bash
   sudo chmod 777 /etc/waagent.conf  
   vi /etc/waagent.conf
   ```
   
   Procure `ResourceDisk.SwapSizeMB` e altere o valor para **8192**. Será necessário pressionar `insert` para entrar no modo de inserção, escreva o valor de **8192** e, em seguida, prima `esc` para retornar ao modo de comando. Para gravar as alterações e sair o ficheiro, escreva `:wq` e prima `enter`.
   
   > [!NOTE]
   > É altamente recomendável que sempre usar `WALinuxAgent` para configurar o espaço de comutação para que ele é sempre criado no disco efémeros local (disco temporário) para um melhor desempenho. Para obter mais informações sobre, consulte [como adicionar um ficheiro de comutação em máquinas de virtuais de Linux do Azure](https://support.microsoft.com/en-us/help/4010058/how-to-add-a-swap-file-in-linux-azure-virtual-machines).

## <a name="prepare-your-local-client-and-vm-to-run-x11"></a>Preparar o seu cliente local e a VM para executar de x11
Configurar Oracle ASM requer uma interface gráfica para concluir a instalação e configuração. Estamos a utilizar o x11 protocolo para facilitar esta instalação. Se estiver a utilizar um sistema de cliente (Mac ou Linux) que já tenha X11 capacidades ativada e configurada - pode ignorar esta configuração exclusiva e às máquinas do Windows. 

1. [Transfira o PuTTY](https://www.putty.org/) e [transferir Xming](https://xming.en.softonic.com/) para o seu computador Windows. Terá de concluir a instalação dos dois desses aplicativos com os valores predefinidos antes de continuar.

2. Depois de instalar PuTTY, abra uma linha de comandos, mude para a pasta PuTTY (por exemplo, C:\Program Files\PuTTY) e executar `puttygen.exe` para gerar uma chave.

3. No gerador de chave PuTTY:
   
   1. Gerar uma chave ao selecionar o `Generate` botão.
   2. Copie o conteúdo da chave (Ctrl + C).
   3. Selecione o botão `Save private key`.
   4. Ignorar o aviso sobre como proteger a chave com uma frase de acesso e, em seguida, selecione `OK`.

   ![Captura de ecrã do gerador de chave PuTTY](./media/oracle-asm/puttykeygen.png)

4. Na sua VM, execute estes comandos:

   ```bash
   sudo su - grid
   mkdir .ssh 
   cd .ssh
   ```

5. Crie um ficheiro com o nome `authorized_keys`. Cole o conteúdo da chave nesse arquivo e, em seguida, guarde o ficheiro.

   > [!NOTE]
   > A chave tem de conter a cadeia de caracteres `ssh-rsa`. Além disso, o conteúdo da chave tem de ser uma única linha de texto.
   >  

6. No seu sistema de cliente, inicie o PuTTY. Na **categoria** painel, aceda à **ligação** > **SSH** > **Auth**. Na **ficheiro de chave privada para autenticação** caixa, navegue para a chave que gerou anteriormente.

   ![Captura de ecrã das opções de autenticação SSH](./media/oracle-asm/setprivatekey.png)

7. Na **categoria** painel, aceda à **ligação** > **SSH** > **X11**. Selecione o **reencaminhamento de X11 de Enable** caixa de verificação.

   ![Captura de ecrã do SSH X11 opções de reencaminhamento](./media/oracle-asm/enablex11.png)

8. Na **categoria** painel, aceda à **sessão**. Introduza a sua VM do Oracle ASM `<publicIPaddress>` na caixa de diálogo de nome de anfitrião, preencher um novo `Saved Session` atribua um nome e, em seguida, clique em `Save`.  Depois de guardar, clique em `open` para ligar à sua máquina virtual do Oracle ASM.  Na primeira vez que se liga é avisado que no sistema remoto não é colocado em cache no seu registo. Clique em `yes` para adicioná-lo e continuar.

   ![Captura de ecrã das opções de sessão PuTTY](./media/oracle-asm/puttysession.png)

## <a name="install-oracle-grid-infrastructure"></a>Instalar a infraestrutura de grade do Oracle

Para instalar a infraestrutura de grade do Oracle, conclua os seguintes passos:

1. Inicie sessão como **grid**. (Deve ser capaz de iniciar sessão sem está sendo solicitada uma palavra-passe.) 

   > [!NOTE]
   > Se estiver a executar o Windows, certifique-se de que iniciar Xming antes de iniciar a instalação.

   ```bash
   cd /opt/grid
   ./runInstaller
   ```

   Infraestrutura de grade do Oracle 12C versão 1 instalador abre. (Pode demorar alguns minutos para que o instalador iniciar.)

2. Sobre o **selecione a opção de instalação** , selecione **instalar e configurar a infraestrutura de grade do Oracle para um servidor autónomo**.

   ![Captura de ecrã da página de selecionar a opção de instalação do instalador](./media/oracle-asm/install01.png)

3. Sobre o **selecionar idiomas de produto** página, certifique-se **inglês** ou o idioma que pretende está selecionado.  Clique em `next`.

4. Sobre o **criar grupo de disco de ASM** página:
   - Introduza um nome para o grupo de disco.
   - Sob **redundância**, selecione **externo**.
   - Sob **tamanho da unidade de alocação**, selecione **4**.
   - Sob **adicionar discos**, selecione **ORCLASMSP**.
   - Clique em `next`.

5. Sobre o **especificar a palavra-passe de ASM** página, selecione a **utilizar as mesmas palavras-passe para estas contas** opção e introduza uma palavra-passe.

   ![Captura de ecrã da página de especificar a palavra-passe de ASM do instalador](./media/oracle-asm/install04.png)

6. Sobre o **especificar as opções de gestão** página, tem a opção para configurar o controlo de Cloud EM. Estamos a ignorar esta opção - clique `next` para continuar. 

7. Sobre o **grupos com privilégios de sistema operativo** página, utilize as predefinições. Clique em `next` para continuar.

8. Sobre o **especificar uma localização de instalação** página, utilize as predefinições. Clique em `next` para continuar.

9. Sobre o **Criar inventário** página, altere o diretório de inventário para `/u01/app/grid/oraInventory`. Clique em `next` para continuar.

   ![Captura de ecrã da página de inventário de criar o instalador](./media/oracle-asm/install08.png)

10. Sobre o **configuração de execução do script de raiz** página, selecione a **automaticamente executar scripts de configuração** caixa de verificação. Em seguida, selecione o **utilizar credencial de utilizador "raiz"** opção e introduza a palavra-passe de utilizador de raiz.

    ![Captura de ecrã da página de configuração de execução de script de raiz do instalador](./media/oracle-asm/install09.png)

11. Sobre o **realizar verificações de pré-requisitos** página, a configuração atual irá falhar com erros. Este é um comportamento esperado. Selecione `Fix & Check Again`.

12. Na **a correção do Script** caixa de diálogo, clique em `OK`.

13. Sobre o **resumo** página, reveja as definições selecionadas e, em seguida, clique em `Install`.

    ![Captura de ecrã da página de resumo do instalador](./media/oracle-asm/install12.png)

14. Uma caixa de diálogo de aviso é apresentado a informar configuração de scripts tem de ser executado como um utilizador com privilégios. Clique em `Yes` para continuar.

15. Sobre o **Finish** página, clique em `Close` para concluir a instalação.

## <a name="set-up-your-oracle-asm-installation"></a>Configurar a sua instalação do Oracle ASM

Para configurar a sua instalação do Oracle ASM, conclua os seguintes passos:

1. Certifique-se de que ainda estiver conectado como **grid**, de sua X11 sessão. Poderá ter de pressionar `enter` de recuperar o terminal. Em seguida, inicie o Oracle automatizada armazenamento gestão de configuração do assistente:

   ```bash
   cd /u01/app/grid/product/12.1.0/grid/bin
   ./asmca
   ```

   Assistente de configuração de ASM do Oracle é aberto.

2. Na **configurar ASM: Grupos de disco** caixa de diálogo, clique nas `Create` botão e, em seguida, clique em `Show Advanced Options`.

3. Na **criar grupo de disco** caixa de diálogo:

   - Introduza o nome do grupo de disco **dados**.
   - Sob **selecionar discos do membro**, selecione **ORCL_DATA** e **ORCL_DATA1**.
   - Sob **tamanho da unidade de alocação**, selecione **4**.
   - Clique em `ok` para criar o grupo de disco.
   - Clique em `ok` para fechar a janela de confirmação.

   ![Captura de ecrã da caixa de diálogo Criar grupo de disco](./media/oracle-asm/asm02.png)

4. Na **configurar ASM: Grupos de disco** caixa de diálogo, clique nas `Create` botão e, em seguida, clique em `Show Advanced Options`.

5. Na **criar grupo de disco** caixa de diálogo:

   - Introduza o nome do grupo de disco **FRA**.
   - Sob **redundância**, selecione **externo (nenhum)**.
   - Sob **selecionar discos do membro**, selecione **ORCL_FRA**.
   - Sob **tamanho da unidade de alocação**, selecione **4**.
   - Clique em `ok` para criar o grupo de disco.
   - Clique em `ok` para fechar a janela de confirmação.

   ![Captura de ecrã da caixa de diálogo Criar grupo de disco](./media/oracle-asm/asm04.png)

6. Selecione **saída** para fechar o Assistente de configuração do ASM.

   ![Captura de ecrã do configurar ASM: Caixa de diálogo de grupos de disco com o botão de saída](./media/oracle-asm/asm05.png)

## <a name="create-the-database"></a>Criar a base de dados

O software de base de dados Oracle já está instalado na imagem do Azure Marketplace. Para criar uma base de dados, conclua os seguintes passos:

1. Mude os utilizadores para o superusuário Oracle e, em seguida, inicializar o serviço de escuta para o registo:

   ```bash
   su - oracle
   cd /u01/app/oracle/product/12.1.0/dbhome_1/bin
   ./dbca
   ```
   É aberto o Assistente de configuração do banco de dados.

2. Sobre o **operação de base de dados** página, clique em `Create Database`.

3. Sobre o **modo de criação de** página:

   - Introduza um nome para a base de dados.
   - Para **tipo de armazenamento**, certifique-se **gestão de armazenamento automática (ASM)** está selecionada.
   - Para **localização de ficheiros de base de dados**, utilize a predefinição ASM sugestões de localização.
   - Para **rápida recuperação área**, utilize a predefinição ASM sugestões de localização.
   - Escreva um **palavra-passe administrativa** e **Confirmar palavra-passe**.
   - Certifique-se de `create as container database` está selecionada.
   - Escreva um `pluggable database name` valor.

4. Sobre o **resumo** página, reveja as definições selecionadas e, em seguida, clique em `Finish` para criar a base de dados.

   ![Captura de ecrã da página de resumo](./media/oracle-asm/createdb03.png)

5. A base de dados foi criado. Sobre o **concluir** página, tem a opção para desbloquear contas adicionais para utilizar esta base de dados e alterar as palavras-passe. Se desejar fazê-lo, selecione **gestão de palavra-passe** -caso contrário, clique em `close`.

## <a name="delete-the-vm"></a>Elimine a VM

Configurou com êxito Oracle automatizada com gestão de armazenamento na imagem Oracle DB do Azure Marketplace.  Quando já não precisar esta VM, pode utilizar o seguinte comando para remover o grupo de recursos, a VM e todos os recursos relacionados:

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passos Seguintes

[Tutorial: Configurar Oracle DataGuard](configure-oracle-dataguard.md)

[Tutorial: Configurar o Oracle GoldenGate](Configure-oracle-golden-gate.md)

Revisão [Arquitetar BD da Oracle](oracle-design.md)
