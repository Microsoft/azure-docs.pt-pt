---
title: Configurar o Oracle ASM em uma máquina virtual Linux do Azure | Microsoft Docs
description: Coloque o Oracle ASM em funcionamento rapidamente em seu ambiente do Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: romitgirdhar
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/02/2018
ms.author: rogirdh
ms.openlocfilehash: 91150251140379c15d4ab3711ded571c9ad2c024
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101657"
---
# <a name="set-up-oracle-asm-on-an-azure-linux-virtual-machine"></a>Configure o Oracle ASM numa máquina virtual do Linux para o Azure  

As máquinas virtuais do Azure proporcionam um ambiente informático totalmente configurável e flexível. Este tutorial aborda a implantação básica da máquina virtual do Azure combinada com a instalação e a configuração do Oracle ASM (Automated Storage Management).  Saiba como:

> [!div class="checklist"]
> * Criar e conectar-se a uma VM Oracle Database
> * Instalar e configurar o Oracle Automated Storage Management
> * Instalar e configurar a infraestrutura de grade do Oracle
> * Inicializar uma instalação do Oracle ASM
> * Criar um Oracle DB gerenciado pelo ASM


[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este tutorial requer a execução da versão 2.0.4 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli). 

## <a name="prepare-the-environment"></a>Preparar o ambiente

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Para criar um grupo de recursos, utilize o comando [az group create](/cli/azure/group). Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. Neste exemplo, um grupo de recursos chamado *MyResource* Group na região *eastus* .

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

### <a name="create-a-vm"></a>Criar uma VM

Para criar uma máquina virtual com base na imagem de Oracle Database e configurá-la para usar o Oracle ASM, use o comando [AZ VM Create](/cli/azure/vm) . 

O exemplo a seguir cria uma VM chamada myVM que é um tamanho de Standard_DS2_v2 com quatro discos de dados anexados de 50 GB cada. Se eles ainda não existirem no local de chave padrão, ele também criará chaves SSH.  Para utilizar um conjunto específico de chaves, utilize a opção `--ssh-key-value`.  

   ```azurecli-interactive
   az vm create --resource-group myResourceGroup \
    --name myVM \
    --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
    --size Standard_DS2_v2 \
    --generate-ssh-keys \
    --data-disk-sizes-gb 50 50 50 50
   ```

Depois de criar a VM, CLI do Azure exibe informações semelhantes ao exemplo a seguir. Observe o valor de `publicIpAddress`. Use esse endereço para acessar a VM.

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

Para criar uma sessão SSH com a VM e definir configurações adicionais, use o comando a seguir. Substitua o endereço IP pelo `publicIpAddress` valor de sua VM.

```bash 
ssh <publicIpAddress>
```

## <a name="install-oracle-asm"></a>Instalar o Oracle ASM

Para instalar o Oracle ASM, conclua as etapas a seguir. 

Para obter mais informações sobre como instalar o Oracle ASM, consulte [downloads do Oracle ASMLib para Oracle Linux 6](https://www.oracle.com/technetwork/server-storage/linux/asmlib/ol6-1709075.html).  

1. Você precisa fazer logon como raiz para continuar com a instalação do ASM:

   ```bash
   sudo su -
   ```
   
2. Execute estes comandos adicionais para instalar os componentes do Oracle ASM:

   ```bash
    yum list | grep oracleasm 
    yum -y install kmod-oracleasm.x86_64 
    yum -y install oracleasm-support.x86_64 
    wget https://download.oracle.com/otn_software/asmlib/oracleasmlib-2.0.12-1.el6.x86_64.rpm 
    yum -y install oracleasmlib-2.0.12-1.el6.x86_64.rpm 
    rm -f oracleasmlib-2.0.12-1.el6.x86_64.rpm
   ```

3. Verifique se o Oracle ASM está instalado:

   ```bash
   rpm -qa |grep oracleasm
   ```

    A saída desse comando deve listar os seguintes componentes:

    ```bash
   oracleasm-support-2.1.10-4.el6.x86_64
   kmod-oracleasm-2.0.8-15.el6_9.x86_64
   oracleasmlib-2.0.12-1.el6.x86_64
    ```

4. O ASM requer usuários e funções específicos para funcionar corretamente. Os comandos a seguir criam as contas de usuário e os grupos de pré-requisito: 

   ```bash
    groupadd -g 54345 asmadmin 
    groupadd -g 54346 asmdba 
    groupadd -g 54347 asmoper 
    useradd -u 3000 -g oinstall -G dba,asmadmin,asmdba,asmoper grid 
    usermod -g oinstall -G dba,asmdba,asmadmin oracle
   ```

5. Verifique se os usuários e grupos foram criados corretamente:

   ```bash
   id grid
   ```

    A saída desse comando deve listar os seguintes usuários e grupos:

    ```bash
    uid=3000(grid) gid=54321(oinstall) groups=54321(oinstall),54322(dba),54345(asmadmin),54346(asmdba),54347(asmoper)
    ```
 
6. Crie uma pasta para a *grade* de usuário e altere o proprietário:

   ```bash
   mkdir /u01/app/grid 
   chown grid:oinstall /u01/app/grid
   ```

## <a name="set-up-oracle-asm"></a>Configurar o Oracle ASM

Para este tutorial, o usuário padrão é *grade* e o grupo padrão é *asmadmin*. Verifique se o usuário *Oracle* faz parte do grupo asmadmin. Para configurar a instalação do Oracle ASM, conclua as seguintes etapas:

1. A configuração do driver da biblioteca Oracle ASM envolve a definição do usuário padrão (grade) e do grupo padrão (asmadmin), bem como a configuração da unidade para iniciar na inicialização (escolha y) e para verificar se há discos na inicialização (escolha y). Você precisa responder aos prompts do seguinte comando:

   ```bash
   /usr/sbin/oracleasm configure -i
   ```

   A saída desse comando deve ser semelhante ao seguinte, parando com os prompts a serem respondidos.

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

2. Exibir a configuração do disco:
   ```bash
   cat /proc/partitions
   ```

   A saída desse comando deve ser semelhante à lista de discos disponíveis a seguir

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

3. Formate o disco */dev/sdc* executando o seguinte comando e respondendo aos prompts com:
   - *n* para nova partição
   - *p* para a partição primária
   - *1* para selecionar a primeira partição
   - Pressione `enter` para o primeiro cilindro padrão
   - Pressione `enter` para o último cilindro padrão
   - Pressione *w* para gravar as alterações na tabela de partição  

   ```bash
   fdisk /dev/sdc
   ```
   
   Usando as respostas fornecidas acima, a saída do comando fdisk deve ser semelhante ao seguinte:

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

4. Repita o comando fdisk anterior para `/dev/sdd`, `/dev/sde`e `/dev/sdf`.

5. Verifique a configuração do disco:

   ```bash
   cat /proc/partitions
   ```

   A saída do comando deve ser semelhante ao seguinte:

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

6. Verifique o status do serviço Oracle ASM e inicie o serviço Oracle ASM:

   ```bash
   service oracleasm status 
   service oracleasm start
   ```

   A saída do comando deve ser semelhante ao seguinte:
   
   ```bash
   Checking if ASM is loaded: no
   Checking if /dev/oracleasm is mounted: no
   Initializing the Oracle ASMLib driver:                     [  OK  ]
   Scanning the system for Oracle ASMLib disks:               [  OK  ]
   ```

7. Criar discos Oracle ASM:

   ```bash
   service oracleasm createdisk ASMSP /dev/sdc1 
   service oracleasm createdisk DATA /dev/sdd1 
   service oracleasm createdisk DATA1 /dev/sde1 
   service oracleasm createdisk FRA /dev/sdf1
   ```    

   A saída do comando deve ser semelhante ao seguinte:

   ```bash
   Marking disk "ASMSP" as an ASM disk:                       [  OK  ]
   Marking disk "DATA" as an ASM disk:                        [  OK  ]
   Marking disk "DATA1" as an ASM disk:                       [  OK  ]
   Marking disk "FRA" as an ASM disk:                         [  OK  ]
   ```

8. Listar discos Oracle ASM:

   ```bash
   service oracleasm listdisks
   ```   

   A saída do comando deve listar os seguintes discos do Oracle ASM:

   ```bash
    ASMSP
    DATA
    DATA1
    FRA
   ```

9. Altere as senhas para os usuários raiz, Oracle e grade. **Anote essas novas senhas** , pois você as usará posteriormente durante a instalação.

   ```bash
   passwd oracle 
   passwd grid 
   passwd root
   ```

10. Altere a permissão da pasta:

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

## <a name="download-and-prepare-oracle-grid-infrastructure"></a>Baixe e prepare a infraestrutura de grade do Oracle

Para baixar e preparar o software de infraestrutura de grade do Oracle, conclua as seguintes etapas:

1. Baixe a infraestrutura de grade do Oracle na [página de download do Oracle ASM](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-linux-download-2240591.html). 

   Sob o download intitulado **Oracle Database 12c versão 1 Grid Infrastructure (12.1.0.2.0) para Linux x86-64**, baixe os dois arquivos. zip.

2. Depois de baixar os arquivos. zip para o computador cliente, você pode usar o protocolo SCP para copiar os arquivos para a VM:

   ```bash
   scp *.zip <publicIpAddress>:.
   ```

3. Use o SSH novamente em sua VM do Oracle no Azure para mover os arquivos. zip para a pasta/opt. Em seguida, altere o proprietário dos arquivos:

   ```bash
   ssh <publicIPAddress>
   sudo mv ./*.zip /opt
   cd /opt
   sudo chown grid:oinstall linuxamd64_12102_grid_1of2.zip
   sudo chown grid:oinstall linuxamd64_12102_grid_2of2.zip
   ```

4. Descompacte os arquivos. (Instale a ferramenta de descompactação do Linux se ela ainda não estiver instalada.)
   
   ```bash
   sudo yum install unzip
   sudo unzip linuxamd64_12102_grid_1of2.zip
   sudo unzip linuxamd64_12102_grid_2of2.zip
   ```

5. Alterar permissão:
   
   ```bash
   sudo chown -R grid:oinstall /opt/grid
   ```

6. Atualizar espaço de permuta configurado. Os componentes da grade Oracle precisam de pelo menos 6,8 GB de espaço de permuta para instalar a grade. O tamanho do arquivo de permuta padrão para as imagens Oracle Linux no Azure é apenas 2048 MB. Você precisa aumentar `ResourceDisk.SwapSizeMB` o `/etc/waagent.conf` arquivo e reiniciar o serviço WALinuxAgent para que as configurações atualizadas entrem em vigor. Como é um arquivo somente leitura, você precisa alterar as permissões de arquivo para habilitar o acesso de gravação.

   ```bash
   sudo chmod 777 /etc/waagent.conf  
   vi /etc/waagent.conf
   ```
   
   Procure e altere o valor para **8192.** `ResourceDisk.SwapSizeMB` Você precisará pressionar `insert` para entrar no modo de inserção, digitar o valor de **8192** e, em `esc` seguida, pressionar para retornar ao modo de comando. Para gravar as alterações e sair do arquivo, digite `:wq` e pressione `enter`.
   
   > [!NOTE]
   > É altamente recomendável que você sempre `WALinuxAgent` use para configurar o espaço de permuta para que ele seja sempre criado no disco efêmero local (disco temporário) para obter o melhor desempenho. Para obter mais informações sobre o, consulte [como adicionar um arquivo de permuta em máquinas virtuais Linux do Azure](https://support.microsoft.com/en-us/help/4010058/how-to-add-a-swap-file-in-linux-azure-virtual-machines).

## <a name="prepare-your-local-client-and-vm-to-run-x11"></a>Preparar o cliente local e a VM para executar o X11
Configurar o Oracle ASM requer uma interface gráfica para concluir a instalação e a configuração. Estamos usando o protocolo X11 para facilitar essa instalação. Se você estiver usando um sistema cliente (Mac ou Linux) que já tenha recursos de X11 habilitados e configurados, você poderá ignorar essa configuração e configuração exclusivas para computadores Windows. 

1. [Baixe](https://www.putty.org/) a saída e [Baixe o Xming](https://xming.en.softonic.com/) no computador com Windows. Você precisará concluir a instalação de ambos os aplicativos com os valores padrão antes de continuar.

2. Depois de instalar a geração, abra um prompt de comando, altere para a pasta de saída (por exemplo, C:\Program Files\PuTTY) e execute `puttygen.exe` para gerar uma chave.

3. No gerador de chave de geração:
   
   1. Gere uma chave selecionando o `Generate` botão.
   2. Copie o conteúdo da chave (Ctrl + C).
   3. Selecione o botão `Save private key`.
   4. Ignore o aviso sobre como proteger a chave com uma senha e, em seguida, `OK`selecione.

   ![Captura de tela do gerador de chave de geração](./media/oracle-asm/puttykeygen.png)

4. Em sua VM, execute estes comandos:

   ```bash
   sudo su - grid
   mkdir .ssh 
   cd .ssh
   ```

5. Crie um ficheiro com o nome `authorized_keys`. Cole o conteúdo da chave nesse arquivo e, em seguida, salve o arquivo.

   > [!NOTE]
   > A chave deve conter a cadeia `ssh-rsa`de caracteres. Além disso, o conteúdo da chave deve ser uma única linha de texto.
   >  

6. No sistema cliente, inicie a reinicialização. No painel **categoria** , acesse **conexão** > **SSH** > **autenticação**. Na caixa **arquivo de chave privada para autenticação** , navegue até a chave que você gerou anteriormente.

   ![Captura de tela das opções de autenticação SSH](./media/oracle-asm/setprivatekey.png)

7. No painel **categoria** , vá para **conexão** > **SSH** > **X11**. Marque a caixa de seleção **habilitar encaminhamento X11** .

   ![Captura de tela das opções de encaminhamento do SSH X11](./media/oracle-asm/enablex11.png)

8. No painel **categoria** , vá para **sessão**. Insira sua VM `<publicIPaddress>` Oracle ASM na caixa de diálogo nome do host, preencha um novo `Saved Session` nome e, em seguida `Save`, clique em.  Depois de salvo, clique `open` em ativar para conectar-se à sua máquina virtual Oracle ASM.  Na primeira vez que você se conectar, será avisado de que o sistema remoto não está armazenado em cache no registro. Clique em `yes` ativado para adicioná-lo e continuar.

   ![Captura de tela das opções de sessão de saída](./media/oracle-asm/puttysession.png)

## <a name="install-oracle-grid-infrastructure"></a>Instalar a infraestrutura de grade do Oracle

Para instalar a infraestrutura de grade do Oracle, conclua as seguintes etapas:

1. Entre como **grade**. (Você deve ser capaz de entrar sem ser solicitado a fornecer uma senha.) 

   > [!NOTE]
   > Se você estiver executando o Windows, certifique-se de ter iniciado o Xming antes de começar a instalação.

   ```bash
   cd /opt/grid
   ./runInstaller
   ```

   O instalador do Oracle Grid Infrastructure 12c versão 1 é aberto. (Pode levar alguns minutos para que o instalador seja iniciado.)

2. Na página **selecionar opção de instalação** , selecione **instalar e configurar a infraestrutura de grade do Oracle para um servidor autônomo**.

   ![Captura de tela da página Selecionar opção de instalação do instalador](./media/oracle-asm/install01.png)

3. Na página **selecionar idiomas do produto** , verifique se o **Inglês** ou o idioma desejado está selecionado.  Clique em `next`.

4. Na página **Criar grupo de discos ASM** :
   - Insira um nome para o grupo de discos.
   - Em **redundância**, selecione **externo**.
   - Em **tamanho da unidade de alocação**, selecione **4**.
   - Em **adicionar discos**, selecione **ORCLASMSP**.
   - Clique em `next`.

5. Na página **especificar senha do ASM** , selecione a opção **usar as mesmas senhas para essas contas** e insira uma senha.

   ![Captura de tela da página especificar senha do ASM do instalador](./media/oracle-asm/install04.png)

6. Na página **especificar opções de gerenciamento** , você tem a opção de configurar o controle de nuvem em. Estamos ignorando esta opção-clique `next` para continuar. 

7. Na página **grupos de sistemas operacionais privilegiados** , use as configurações padrão. Clique `next` para continuar.

8. Na página **especificar local de instalação** , use as configurações padrão. Clique `next` para continuar.

9. Na página **criar inventário** , altere o diretório de inventário para `/u01/app/grid/oraInventory`. Clique `next` para continuar.

   ![Captura de tela da página Criar inventário do instalador](./media/oracle-asm/install08.png)

10. Na página **configuração de execução de script raiz** , marque a caixa de seleção **executar scripts de configuração automaticamente** . Em seguida, selecione a opção **usar credencial de usuário "raiz"** e insira a senha de usuário raiz.

    ![Captura de tela da página de configuração de execução de script raiz do instalador](./media/oracle-asm/install09.png)

11. Na página **executar verificações de pré-requisitos** , a configuração atual falhará com erros. Esse é um comportamento esperado. Selecione `Fix & Check Again`.

12. Na caixa de diálogo **script de correção** , `OK`clique em.

13. Na página **Resumo** , examine as configurações selecionadas e clique em `Install`.

    ![Captura de tela da página de resumo do instalador](./media/oracle-asm/install12.png)

14. Uma caixa de diálogo de aviso é exibida informando que os scripts de configuração precisam ser executados como um usuário privilegiado. Clique `Yes` para continuar.

15. Na página **concluir** , clique `Close` em para concluir a instalação.

## <a name="set-up-your-oracle-asm-installation"></a>Configurar a instalação do Oracle ASM

Para configurar a instalação do Oracle ASM, conclua as seguintes etapas:

1. Verifique se você ainda está conectado como **grade**, da sua sessão X11. Talvez seja necessário pressionar `enter` para refazer o terminal. Em seguida, inicie o assistente de configuração do gerenciamento de armazenamento automatizado da Oracle:

   ```bash
   cd /u01/app/grid/product/12.1.0/grid/bin
   ./asmca
   ```

   O assistente de configuração do Oracle ASM é aberto.

2. **No configurar asm: Caixa de** diálogo grupos de discos, `Create` clique no botão e, `Show Advanced Options`em seguida, clique em.

3. Na caixa de diálogo **Criar grupo de discos** :

   - Insira os **dados**do nome do grupo de discos.
   - Em **Selecionar discos membro**, selecione **ORCL_DATA** e **ORCL_DATA1**.
   - Em **tamanho da unidade de alocação**, selecione **4**.
   - Clique `ok` para criar o grupo de discos.
   - Clique `ok` para fechar a janela de confirmação.

   ![Captura de tela da caixa de diálogo Criar grupo de discos](./media/oracle-asm/asm02.png)

4. **No configurar asm: Caixa de** diálogo grupos de discos, `Create` clique no botão e, `Show Advanced Options`em seguida, clique em.

5. Na caixa de diálogo **Criar grupo de discos** :

   - Insira o nome do grupo de discos **FRA**.
   - Em **redundância**, selecione **externo (nenhum)** .
   - Em **Selecionar discos Membros**, selecione **ORCL_FRA**.
   - Em **tamanho da unidade de alocação**, selecione **4**.
   - Clique `ok` para criar o grupo de discos.
   - Clique `ok` para fechar a janela de confirmação.

   ![Captura de tela da caixa de diálogo Criar grupo de discos](./media/oracle-asm/asm04.png)

6. Selecione **sair** para fechar o assistente de configuração do asm.

   ![Captura de tela da configuração ASM: Caixa de diálogo grupos de discos com o botão sair](./media/oracle-asm/asm05.png)

## <a name="create-the-database"></a>Criar o banco de dados

O software de banco de dados Oracle já está instalado na imagem do Azure Marketplace. Para criar um banco de dados, conclua as seguintes etapas:

1. Alterne os usuários para o superusuário Oracle e, em seguida, inicialize o ouvinte para registro em log:

   ```bash
   su - oracle
   cd /u01/app/oracle/product/12.1.0/dbhome_1/bin
   ./dbca
   ```
   O assistente de configuração de banco de dados é aberto.

2. Na página **operação de banco** de dados `Create Database`, clique em.

3. Na página **modo de criação** :

   - Insira um nome para o banco de dados.
   - Para **tipo de armazenamento**, verifique se o **gerenciamento automático de armazenamento (ASM)** está selecionado.
   - Para **local dos arquivos de banco de dados**, use o local sugerido padrão do asm.
   - Para a **área de recuperação rápida**, use o local sugerido padrão do asm.
   - Digite uma **senha administrativa** e **confirme a senha**.
   - Verifique `create as container database` se está selecionado.
   - Digite um `pluggable database name` valor.

4. Na página **Resumo** , examine as configurações selecionadas e clique `Finish` em para criar o banco de dados.

   ![Captura de tela da página Resumo](./media/oracle-asm/createdb03.png)

5. O banco de dados foi criado. Na página **concluir** , você tem a opção de desbloquear contas adicionais para usar esse banco de dados e alterar as senhas. Se você quiser fazer isso, selecione **Gerenciamento de senhas** – caso contrário, `close`clique em.

## <a name="delete-the-vm"></a>Elimine a VM

Você configurou com êxito o gerenciamento de armazenamento automatizado da Oracle na imagem de Oracle DB do Azure Marketplace.  Quando você não precisar mais dessa VM, poderá usar o seguinte comando para remover o grupo de recursos, a VM e todos os recursos relacionados:

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passos Seguintes

[Tutorial: Configurar o Oracle DataGuard](configure-oracle-dataguard.md)

[Tutorial: Configurar o Oracle GoldenGate](Configure-oracle-golden-gate.md)

Examinar [o arquiteto um Oracle DB](oracle-design.md)
