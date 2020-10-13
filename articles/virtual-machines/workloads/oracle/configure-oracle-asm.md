---
title: Configurar o Oracle ASM numa máquina virtual Azure Linux Microsoft Docs
description: Rapidamente coloque a Oracle ASM a funcionar no seu ambiente Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: dbakevlar
manager: ''
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/02/2018
ms.author: kegorman
ms.openlocfilehash: e82b458487dd4c865013c806447473d25c75017f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91328093"
---
# <a name="set-up-oracle-asm-on-an-azure-linux-virtual-machine"></a>Configurar o Oracle ASM numa máquina virtual Azure Linux  

As máquinas virtuais do Azure proporcionam um ambiente informático totalmente configurável e flexível. Este tutorial abrange a implantação básica da máquina virtual Azure combinada com a instalação e configuração da Oracle Automated Storage Management (ASM).  Saiba como:

> [!div class="checklist"]
> * Criar e ligar a um VM da Base de Dados do Oráculo
> * Instalar e configurar a Oracle Gestão automatizada de Armazenamento
> * Instalar e configurar a infraestrutura da Oracle Grid
> * Inicializar uma instalação Oracle ASM
> * Criar um Oráculo DB gerido pela ASM


Se optar por instalar e utilizar a CLI localmente, este tutorial requer a execução da versão 2.0.4 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)]( /cli/azure/install-azure-cli). 

## <a name="prepare-the-environment"></a>Preparar o ambiente

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Para criar um grupo de recursos, utilize o comando [az group create](/cli/azure/group). Um grupo de recursos Azure é um recipiente lógico no qual os recursos da Azure são implantados e geridos. Neste exemplo, um grupo de recursos nomeou *o myResourceGroup* na região *leste.*

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

### <a name="create-a-vm"></a>Criar uma VM

Para criar uma máquina virtual baseada na imagem da Oracle Database e configurá-la para utilizar o Oracle ASM, utilize o comando [az vm criar.](/cli/azure/vm) 

O exemplo a seguir cria um VM chamado myVM que é um tamanho Standard_DS2_v2 com quatro discos de dados anexados de 50 GB cada. Se já não existirem na localização da chave predefinida, também cria chaves SSH.  Para utilizar um conjunto específico de chaves, utilize a opção `--ssh-key-value`.  

   ```azurecli-interactive
   az vm create --resource-group myResourceGroup \
    --name myVM \
    --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
    --size Standard_DS2_v2 \
    --generate-ssh-keys \
    --data-disk-sizes-gb 50 50 50 50
   ```

Depois de criar o VM, o Azure CLI exibe informações semelhantes ao exemplo seguinte. Note o valor para `publicIpAddress` . Utilize este endereço para aceder ao VM.

   ```output
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

Para criar uma sessão SSH com o VM e configurar definições adicionais, utilize o seguinte comando. Substitua o endereço IP `publicIpAddress` pelo valor do seu VM.

```bash
ssh <publicIpAddress>
```

## <a name="install-oracle-asm"></a>Instalar o Oracle ASM

Para instalar o Oracle ASM, complete os seguintes passos. 

Para obter mais informações sobre a instalação da Oracle ASM, consulte [o Oracle ASMLib Downloads para Oracle Linux 6](https://www.oracle.com/technetwork/server-storage/linux/asmlib/ol6-1709075.html).  

1. Tem de iniciar sessão como raiz para continuar com a instalação ASM:

   ```bash
   sudo su -
   ```
   
2. Executar estes comandos adicionais para instalar componentes Oracle ASM:

   ```bash
    yum list | grep oracleasm 
    yum -y install kmod-oracleasm.x86_64 
    yum -y install oracleasm-support.x86_64 
    wget https://download.oracle.com/otn_software/asmlib/oracleasmlib-2.0.12-1.el6.x86_64.rpm 
    yum -y install oracleasmlib-2.0.12-1.el6.x86_64.rpm 
    rm -f oracleasmlib-2.0.12-1.el6.x86_64.rpm
   ```

3. Verifique se a Oracle ASM está instalada:

   ```bash
   rpm -qa |grep oracleasm
   ```

    A saída deste comando deve enumerar os seguintes componentes:

    ```bash
   oracleasm-support-2.1.10-4.el6.x86_64
   kmod-oracleasm-2.0.8-15.el6_9.x86_64
   oracleasmlib-2.0.12-1.el6.x86_64
    ```

4. A ASM requer utilizadores e funções específicos para funcionar corretamente. Os seguintes comandos criam as contas e grupos de utilizador pré-requisitos: 

   ```bash
    groupadd -g 54345 asmadmin 
    groupadd -g 54346 asmdba 
    groupadd -g 54347 asmoper 
    useradd -u 3000 -g oinstall -G dba,asmadmin,asmdba,asmoper grid 
    usermod -g oinstall -G dba,asmdba,asmadmin oracle
   ```

5. Verifique se os utilizadores e grupos foram criados corretamente:

   ```bash
   id grid
   ```

    A saída deste comando deve enumerar os seguintes utilizadores e grupos:

    ```bash
    uid=3000(grid) gid=54321(oinstall) groups=54321(oinstall),54322(dba),54345(asmadmin),54346(asmdba),54347(asmoper)
    ```
 
6. Crie uma pasta para *a grelha do* utilizador e mude o proprietário:

   ```bash
   mkdir /u01/app/grid 
   chown grid:oinstall /u01/app/grid
   ```

## <a name="set-up-oracle-asm"></a>Configurar o Oracle ASM

Para este tutorial, o utilizador predefinido é *a grelha* e o grupo predefinido é *asmadmin*. Certifique-se de que o utilizador *oráculo* faz parte do grupo asmadmin. Para configurar a sua instalação Oracle ASM, complete os seguintes passos:

1. A configuração do controlador da biblioteca Oracle ASM implica definir o utilizador predefinido (grelha) e o grupo predefinido (asmadmin), bem como configurar a unidade para iniciar o arranque (escolha y) e procurar discos no arranque (escolha y). Tem de responder às indicações do seguinte comando:

   ```bash
   /usr/sbin/oracleasm configure -i
   ```

   A saída deste comando deve ser semelhante à seguinte, parando com pedidos a serem respondidos.

    ```output
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

   A saída deste comando deve ser semelhante à seguinte listagem de discos disponíveis

   ```output
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

3. Formato disco */dev/sdc* executando o seguinte comando e respondendo as instruções com:
   - *n* para nova partição
   - *p* para partição primária
   - *1* para selecionar a primeira partição
   - pressione `enter` para o primeiro cilindro padrão
   - pressione `enter` para o último cilindro predefinido
   - pressione *w* para escrever as alterações à tabela de divisórias  

   ```bash
   fdisk /dev/sdc
   ```
   
   Utilizando as respostas acima fornecidas, a saída para o `fdisk` comando deve parecer-se com as seguintes:

   ```output
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

4. Repita o comando anterior `fdisk` para `/dev/sdd` , e `/dev/sde` `/dev/sdf` .

5. Verifique a configuração do disco:

   ```bash
   cat /proc/partitions
   ```

   A saída do comando deve parecer o seguinte:

   ```output
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

6. Verifique o estado de serviço da Oracle ASM e inicie o serviço Oracle ASM:

   ```bash
   service oracleasm status 
   service oracleasm start
   ```

   A saída do comando deve parecer o seguinte:

   ```output
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

   A saída do comando deve parecer o seguinte:

   ```output
   Marking disk "ASMSP" as an ASM disk:                       [  OK  ]
   Marking disk "DATA" as an ASM disk:                        [  OK  ]
   Marking disk "DATA1" as an ASM disk:                       [  OK  ]
   Marking disk "FRA" as an ASM disk:                         [  OK  ]
   ```

8. Liste discos ORACLE ASM:

   ```bash
   service oracleasm listdisks
   ```

   A saída do comando deve listar os seguintes discos Oracle ASM:

   ```output
    ASMSP
    DATA
    DATA1
    FRA
   ```

9. Altere as palavras-passe para os utilizadores de raiz, oráculo e grelha. **Tome nota destas novas palavras-passe** à medida que as utiliza mais tarde durante a instalação.

   ```bash
   passwd oracle 
   passwd grid 
   passwd root
   ```

10. Alterar a permissão de pasta:

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

## <a name="download-and-prepare-oracle-grid-infrastructure"></a>Descarregue e prepare a Infraestrutura oracle Grid

Para descarregar e preparar o software Oracle Grid Infrastructure, complete os seguintes passos:

1. Descarregue a Infraestrutura oracle Grid a partir da [página de descarregamento](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-linux-download-2240591.html)da Oracle ASM . 

   Sob o download intitulado **Oracle Database 12c Release 1 Grid Infrastructure (12.1.0.2.0) para Linux x86-64**, descarregue os dois ficheiros .zip.

2. Depois de transferir os ficheiros .zip para o seu computador cliente, pode utilizar o Secure Copy Protocol (SCP) para copiar os ficheiros para o seu VM:

   ```bash
   scp *.zip <publicIpAddress>:.
   ```

3. SSH de volta ao seu Oracle VM em Azure para mover os ficheiros .zip para a pasta /opt. Em seguida, mude o proprietário dos ficheiros:

   ```bash
   ssh <publicIPAddress>
   sudo mv ./*.zip /opt
   cd /opt
   sudo chown grid:oinstall linuxamd64_12102_grid_1of2.zip
   sudo chown grid:oinstall linuxamd64_12102_grid_2of2.zip
   ```

4. Desaperte os ficheiros. (Instale a ferramenta Linux unzip se ainda não estiver instalada.)

   ```bash
   sudo yum install unzip
   sudo unzip linuxamd64_12102_grid_1of2.zip
   sudo unzip linuxamd64_12102_grid_2of2.zip
   ```

5. Alterar a permissão:

   ```bash
   sudo chown -R grid:oinstall /opt/grid
   ```

6. Atualizar espaço de troca configurado. Os componentes da Oracle Grid precisam de pelo menos 6,8 GB de espaço de troca para instalar a Grelha. O tamanho do ficheiro swap padrão para imagens Oracle Linux em Azure é de apenas 2048MB. É necessário aumentar `ResourceDisk.SwapSizeMB` o ficheiro e reiniciar o serviço `/etc/waagent.conf` WALinuxAgent para que as definições atualizadas produzam efeitos. Por ser um ficheiro apenas de leitura, é necessário alterar permissões de ficheiros para permitir o acesso à escrita.

   ```bash
   sudo chmod 777 /etc/waagent.conf  
   vi /etc/waagent.conf
   ```

   Procure `ResourceDisk.SwapSizeMB` e altere o valor para **8192**. Terá de pressionar para `insert` introduzir o modo de inserção, digite o valor de **8192** e, em seguida, prima `esc` para voltar ao modo de comando. Para escrever as alterações e deixar o ficheiro, escreva `:wq` e prima `enter` .
   
   > [!NOTE]
   > Recomendamos vivamente que utilize sempre `WALinuxAgent` para configurar o espaço de troca para que seja sempre criado no disco efémero local (disco temporário) para melhor desempenho. Para obter mais informações, consulte [Como adicionar um ficheiro de troca em máquinas virtuais Linux Azure](https://support.microsoft.com/en-us/help/4010058/how-to-add-a-swap-file-in-linux-azure-virtual-machines).

## <a name="prepare-your-local-client-and-vm-to-run-x11"></a>Prepare o seu cliente local e vM para executar x11
Configurar o Oracle ASM requer uma interface gráfica para completar a instalação e configuração. Estamos a usar o protocolo x11 para facilitar esta instalação. Se estiver a utilizar um sistema de clientes (Mac ou Linux) que já tenha capacidades X11 ativadas e configuradas - pode saltar esta configuração e configurar exclusivamente para máquinas Windows. 

1. [Baixe o PuTTY](https://www.putty.org/) e [baixe o Xming](https://xming.en.softonic.com/) para o seu computador Windows. Será necessário concluir a instalação de ambas as aplicações com os valores predefinidos antes de prosseguir.

2. Depois de instalar o PuTTY, abra um pedido de comando, altere na pasta PuTTY (por exemplo, C:\Program Files\PuTTY) e corra `puttygen.exe` para gerar uma chave.

3. No gerador de chaves PuTTY:
   
   1. Gere uma tecla selecionando o `Generate` botão.
   2. Copiar o conteúdo da chave (Ctrl+C).
   3. Selecione o botão `Save private key`.
   4. Ignore o aviso sobre a fixação da chave com uma palavra-passe e, em seguida, selecione `OK` .

   ![Screenshot do gerador de chaves PuTTY](./media/oracle-asm/puttykeygen.png)

4. No seu VM, execute estes comandos:

   ```bash
   sudo su - grid
   mkdir .ssh 
   cd .ssh
   ```

5. Crie um ficheiro com o nome `authorized_keys`. Cole o conteúdo da chave neste ficheiro e, em seguida, guarde o ficheiro.

   > [!NOTE]
   > A chave deve conter a `ssh-rsa` corda. Além disso, o conteúdo da chave deve ser uma única linha de texto.
   >  

6. No seu sistema de clientes, inicie o PuTTY. No painel **de categorias,** vá à **Connection**  >  **SSH**  >  **Auth**. No ficheiro chave Privado para a caixa **de autenticação,** consulte a chave que gerou anteriormente.

   ![Screenshot das opções de autenticação SSH](./media/oracle-asm/setprivatekey.png)

7. No painel **de categorias,** vá à **Conexão**  >  **SSH**  >  **X11**. Selecione a caixa **de verificação de encaminhamento Enable X11.**

   ![Screenshot das opções de encaminhamento SSH X11](./media/oracle-asm/enablex11.png)

8. No painel **de categorias,** vá à **Sessão.** Introduza o seu Oracle ASM VM `<publicIPaddress>` na caixa de diálogo do nome do anfitrião, preencha um novo nome `Saved Session` e, em seguida, clique em `Save` .  Uma vez guardado, clique `open` para ligar à sua máquina virtual Oracle ASM.  A primeira vez que se liga é avisado de que o sistema remoto não está em cache no seu registo. Clique `yes` para adicioná-lo e continuar.

   ![Screenshot das opções de sessão de PuTTY](./media/oracle-asm/puttysession.png)

## <a name="install-oracle-grid-infrastructure"></a>Instalar infraestrutura de grelha de oráculo

Para instalar a Infraestrutura oracle Grid, complete os seguintes passos:

1. Inscreva-se como **grelha.** (Deve poder iniciar sedução sem ser solicitado para uma senha.) 

   > [!NOTE]
   > Se estiver a executar o Windows, certifique-se de que iniciou o Xming antes de iniciar a instalação.

   ```bash
   cd /opt/grid
   ./runInstaller
   ```

   Oracle Grid Infrastructure 12c Desbloqueia 1 Instalador aberto. (Pode levar alguns minutos para o instalador começar.)

2. Na página **Select Install Option,** selecione **Instalar e configurar a infraestrutura da grelha do oráculo para um servidor autónomo.**

   ![Screenshot da página de opção de instalação selecionada do instalador](./media/oracle-asm/install01.png)

3. Na página **Select Product Languages,** certifique-se de que o **inglês** ou o idioma que deseja é selecionado.  Clique em `next`.

4. Na página **do Grupo de Discos Criar ASM:**
   - Insira um nome para o grupo de discos.
   - Sob **Redundância,** selecione **External**.
   - Sob **o tamanho da unidade de atribuição**, selecione **4**.
   - Em **Add Disks**, selecione **ORCLASMSP**.
   - Clique em `next`.

5. Na página **'Especificar palavra-passe ASM',** selecione as **mesmas palavras-passe para estas contas** e introduza uma palavra-passe.

   ![Screenshot da página de passwords ESPECIFICAR ASM do instalador](./media/oracle-asm/install04.png)

6. Na página **'Especificar Opções de Gestão',** tem a opção de configurar o Controlo de Nuvem EM. Estamos ignorando esta opção - clique `next` para continuar. 

7. Na página **Grupos do Sistema Operativo Privilegiado,** utilize as definições predefinidos. Clique `next` para continuar.

8. Na página **'Localizar instalação' especifique,** utilize as definições predefinidos. Clique `next` para continuar.

9. Na página **'Criar Inventário',** altere o Diretório de Inventário para `/u01/app/grid/oraInventory` . Clique `next` para continuar.

   ![Screenshot da página de inventário do instalador](./media/oracle-asm/install08.png)

10. Na página de configuração da execução do **script Raiz,** selecione a caixa **de verificação de scripts de configuração de execução automática.** Em seguida, selecione a opção de credencial do **utilizador "raiz"** e introduza a palavra-passe do utilizador raiz.

    ![Screenshot da página de configuração de execução do script root do instalador](./media/oracle-asm/install09.png)

11. Na página **'Executar verificações de requisitos',** a configuração atual falhará com erros. Este é um comportamento esperado. Selecione `Fix & Check Again`.

12. Na caixa de diálogo **do Script Fixup,** clique `OK` em .

13. Na página **Resumo,** reveja as definições selecionadas e, em seguida, clique `Install` em .

    ![Screenshot da página sumária do instalador](./media/oracle-asm/install12.png)

14. Aparece uma caixa de diálogo de aviso informando-o de que os scripts de configuração têm de ser executados como um utilizador privilegiado. Clique `Yes` para continuar.

15. Na página **'Acabamento',** clique `Close` para terminar a instalação.

## <a name="set-up-your-oracle-asm-installation"></a>Configurar a sua instalação Oracle ASM

Para configurar a sua instalação Oracle ASM, complete os seguintes passos:

1. Certifique-se de que ainda está inscrito como **grelha,** a partir da sua sessão X11. Talvez precises de bater `enter` para reanimar o terminal. Em seguida, lançar o Assistente de Configuração de Gestão de Armazenamento Automatizado oracle:

   ```bash
   cd /u01/app/grid/product/12.1.0/grid/bin
   ./asmca
   ```

   O Assistente de Configuração Oracle ASM abre.

2. Na **configuração ASM: Caixa** de diálogo grupos de discos, clique no `Create` botão e, em seguida, clique `Show Advanced Options` em .

3. Na caixa de diálogo **do Grupo Criar Disco:**

   - Introduza o nome do grupo de discos **DATA**.
   - Em **Discos de Membro Select**, selecione **ORCL_DATA** e **ORCL_DATA1**.
   - Sob **o tamanho da unidade de atribuição**, selecione **4**.
   - Clique `ok` para criar o grupo de discos.
   - Clique `ok` para fechar a janela de confirmação.

   ![Screenshot da caixa de diálogo do Grupo De Discos Criar](./media/oracle-asm/asm02.png)

4. Na **configuração ASM: Caixa** de diálogo grupos de discos, clique no `Create` botão e, em seguida, clique `Show Advanced Options` em .

5. Na caixa de diálogo **do Grupo Criar Disco:**

   - Introduza o nome do grupo de discos **FRA**.
   - Sob **Redundância,** **selecione External (nenhum)**.
   - Em **Discos de Membro Select**, selecione **ORCL_FRA**.
   - Sob **o tamanho da unidade de atribuição**, selecione **4**.
   - Clique `ok` para criar o grupo de discos.
   - Clique `ok` para fechar a janela de confirmação.

   ![Screenshot da caixa de diálogo do Grupo De Discos Criar](./media/oracle-asm/asm04.png)

6. Selecione **Saída** para fechar o Assistente de Configuração ASM.

   ![Screenshot do Configure ASM: Caixa de diálogo grupos de discos com botão exit](./media/oracle-asm/asm05.png)

## <a name="create-the-database"></a>Criar a base de dados

O software de base de dados Oracle já está instalado na imagem do Azure Marketplace. Para criar uma base de dados, complete os seguintes passos:

1. Mude os utilizadores para o superuser Oráculo e, em seguida, inicialize o ouvinte para registar:

   ```bash
   su - oracle
   cd /u01/app/oracle/product/12.1.0/dbhome_1/bin
   ./dbca
   ```

   O Assistente de Configuração da Base de Dados abre.

2. Na página **Operação Base de Dados,** clique `Create Database` em .

3. Na página **modo criação:**

   - Insira um nome para a base de dados.
   - Para **o tipo de armazenamento,** certifique-se de que a **Gestão Automática de Armazenamento (ASM)** está selecionada.
   - Para **a localização dos ficheiros de base de dados,** utilize a localização padrão sugerida pela ASM.
   - Para **a Área de Recuperação Rápida,** utilize a localização asm indicada por defeito.
   - digite uma **palavra-passe administrativa** e **confirme a palavra-passe**.
   - garantir que `create as container database` está selecionado.
   - tipo de um `pluggable database name` valor.

4. Na página **Resumo,** reveja as definições selecionadas e, em seguida, clique `Finish` para criar a base de dados.

   ![Captura de ecrã da página Resumo](./media/oracle-asm/createdb03.png)

5. A Base de Dados foi criada. Na página **'Terminar',** tem a opção de desbloquear contas adicionais para utilizar esta base de dados e alterar as palavras-passe. Se o desejar, selecione **Password Management** - de outra forma clique em `close` .

## <a name="delete-the-vm"></a>Elimine a VM

Configura com sucesso a Oracle Automated Storage Management na imagem Do Oracle DB do Mercado Azure.  Quando já não precisar deste VM, pode utilizar o seguinte comando para remover o grupo de recursos, VM e todos os recursos relacionados:

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

[Tutorial: Configurar o Oracle DataGuard](configure-oracle-dataguard.md)

[Tutorial: Configurar o Oracle GoldenGate](Configure-oracle-golden-gate.md)

Rever [Arquiteto um Oráculo DB](oracle-design.md)
