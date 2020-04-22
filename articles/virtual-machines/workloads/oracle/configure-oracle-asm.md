---
title: Instale o Oracle ASM numa máquina virtual Azure Linux [ Microsoft Docs
description: Rapidamente coloque a Oracle ASM a funcionar no seu ambiente Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: BorisB2015
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/02/2018
ms.author: borisb
ms.openlocfilehash: aa65b789d02c60ef6042aa62e1c138c0e1bd7224
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81676905"
---
# <a name="set-up-oracle-asm-on-an-azure-linux-virtual-machine"></a>Instale o Oracle ASM numa máquina virtual Azure Linux  

As máquinas virtuais do Azure proporcionam um ambiente informático totalmente configurável e flexível. Este tutorial cobre a implantação básica da máquina virtual Azure combinada com a instalação e configuração da Oracle Automated Storage Management (ASM).  Saiba como:

> [!div class="checklist"]
> * Criar e ligar a um VM de Base de Dados Oracle
> * Instalar e configurar a Gestão automatizada de Armazenamento da Oracle
> * Instalar e configurar a infraestrutura da Oracle Grid
> * Inicializar uma instalação Oracle ASM
> * Criar um Oracle DB gerido pela ASM


Se optar por instalar e utilizar a CLI localmente, este tutorial requer a execução da versão 2.0.4 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)]( /cli/azure/install-azure-cli). 

## <a name="prepare-the-environment"></a>Preparar o ambiente

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Para criar um grupo de recursos, utilize o comando [az group create](/cli/azure/group). Um grupo de recursos Azure é um recipiente lógico no qual os recursos azure são implantados e geridos. Neste exemplo, um grupo de recursos nomeado *myResourceGroup* na região *oriental.*

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

### <a name="create-a-vm"></a>Criar uma VM

Para criar uma máquina virtual baseada na imagem da Base de Dados Oracle e configurá-la para utilizar o Oracle ASM, utilize o comando [az vm criar.](/cli/azure/vm) 

O exemplo seguinte cria um VM chamado myVM que é um tamanho Standard_DS2_v2 com quatro discos de dados anexados de 50 GB cada. Se ainda não existirem na localização da chave predefinida, também cria chaves SSH.  Para utilizar um conjunto específico de chaves, utilize a opção `--ssh-key-value`.  

   ```azurecli-interactive
   az vm create --resource-group myResourceGroup \
    --name myVM \
    --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
    --size Standard_DS2_v2 \
    --generate-ssh-keys \
    --data-disk-sizes-gb 50 50 50 50
   ```

Depois de criar o VM, o Azure CLI apresenta informações semelhantes ao seguinte exemplo. Note o `publicIpAddress`valor para . Usa este endereço para aceder ao VM.

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

Para criar uma sessão SSH com o VM e configurar configurar configurações adicionais, utilize o seguinte comando. Substitua o endereço `publicIpAddress` IP pelo valor do seu VM.

```bash
ssh <publicIpAddress>
```

## <a name="install-oracle-asm"></a>Instale o Oracle ASM

Para instalar o Oracle ASM, complete os seguintes passos. 

Para mais informações sobre a instalação do Oracle ASM, consulte [o Oracle ASMLib Downloads para o Oracle Linux 6](https://www.oracle.com/technetwork/server-storage/linux/asmlib/ol6-1709075.html).  

1. É necessário iniciar sessão como raiz para continuar com a instalação ASM:

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

3. Verifique se o Oracle ASM está instalado:

   ```bash
   rpm -qa |grep oracleasm
   ```

    A saída deste comando deve enumerar os seguintes componentes:

    ```bash
   oracleasm-support-2.1.10-4.el6.x86_64
   kmod-oracleasm-2.0.8-15.el6_9.x86_64
   oracleasmlib-2.0.12-1.el6.x86_64
    ```

4. A ASM requer utilizadores e funções específicas para funcionar corretamente. Os seguintes comandos criam as contas e grupos de utilizadores pré-requisitos: 

   ```bash
    groupadd -g 54345 asmadmin 
    groupadd -g 54346 asmdba 
    groupadd -g 54347 asmoper 
    useradd -u 3000 -g oinstall -G dba,asmadmin,asmdba,asmoper grid 
    usermod -g oinstall -G dba,asmdba,asmadmin oracle
   ```

5. Verificar se os utilizadores e grupos foram criados corretamente:

   ```bash
   id grid
   ```

    A saída deste comando deve enumerar os seguintes utilizadores e grupos:

    ```bash
    uid=3000(grid) gid=54321(oinstall) groups=54321(oinstall),54322(dba),54345(asmadmin),54346(asmdba),54347(asmoper)
    ```
 
6. Crie uma pasta para a *grelha* do utilizador e altere o proprietário:

   ```bash
   mkdir /u01/app/grid 
   chown grid:oinstall /u01/app/grid
   ```

## <a name="set-up-oracle-asm"></a>Configurar o Oracle ASM

Para este tutorial, o utilizador predefinido é *a grelha* e o grupo predefinido é *asmadmin .* Certifique-se de que o utilizador do *oráculo* faz parte do grupo asmadmin. Para configurar a sua instalação Oracle ASM, complete os seguintes passos:

1. A configuração do controlador da biblioteca Oracle ASM envolve definir o utilizador padrão (grelha) e o grupo predefinido (asmadmina), bem como configurar a unidade para iniciar a bota (escolha y) e procurar discos na bota (escolha y). Precisa responder às instruções do seguinte comando:

   ```bash
   /usr/sbin/oracleasm configure -i
   ```

   A saída deste comando deve parecer semelhante à seguinte, parando com instruções a serem respondidas.

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

   A saída deste comando deve parecer semelhante à seguinte listagem de discos disponíveis

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
   - pressionar `enter` para o primeiro cilindro padrão
   - pressionar `enter` para o último cilindro padrão
   - pressionar *w* para escrever as alterações para a mesa de partição  

   ```bash
   fdisk /dev/sdc
   ```
   
   Utilizando as respostas acima fornecidas, a saída para o `fdisk` comando deve parecer a seguinte:

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

4. Repita o `fdisk` comando `/dev/sdd`anterior `/dev/sde`para. `/dev/sdf`

5. Verifique a configuração do disco:

   ```bash
   cat /proc/partitions
   ```

   A saída do comando deve parecer a seguinte:

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

6. Verifique o estado do serviço Oracle ASM e inicie o serviço Oracle ASM:

   ```bash
   service oracleasm status 
   service oracleasm start
   ```

   A saída do comando deve parecer a seguinte:

   ```output
   Checking if ASM is loaded: no
   Checking if /dev/oracleasm is mounted: no
   Initializing the Oracle ASMLib driver:                     [  OK  ]
   Scanning the system for Oracle ASMLib disks:               [  OK  ]
   ```

7. Crie discos Oracle ASM:

   ```bash
   service oracleasm createdisk ASMSP /dev/sdc1 
   service oracleasm createdisk DATA /dev/sdd1 
   service oracleasm createdisk DATA1 /dev/sde1 
   service oracleasm createdisk FRA /dev/sdf1
   ```

   A saída do comando deve parecer a seguinte:

   ```output
   Marking disk "ASMSP" as an ASM disk:                       [  OK  ]
   Marking disk "DATA" as an ASM disk:                        [  OK  ]
   Marking disk "DATA1" as an ASM disk:                       [  OK  ]
   Marking disk "FRA" as an ASM disk:                         [  OK  ]
   ```

8. Lista de discos Oracle ASM:

   ```bash
   service oracleasm listdisks
   ```

   A saída do comando deve enumerar os seguintes discos Oracle ASM:

   ```output
    ASMSP
    DATA
    DATA1
    FRA
   ```

9. Altere as palavras-passe para os utilizadores da raiz, oráculo e grelha. **Tome nota destas novas palavras-passe,** uma vez que as utiliza mais tarde durante a instalação.

   ```bash
   passwd oracle 
   passwd grid 
   passwd root
   ```

10. Alterar a permissão da pasta:

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

## <a name="download-and-prepare-oracle-grid-infrastructure"></a>Descarregue e prepare a Infraestrutura da Rede Oracle

Para descarregar e preparar o software oracle Grid Infrastructure, complete os seguintes passos:

1. Baixe a Oracle Grid Infrastructure a partir da página de [descarregamento oracle ASM](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-linux-download-2240591.html). 

   Sob o download intitulado **Oracle Database 12c Release 1 Grid Infrastructure (12.1.0.2.0) para Linux x86-64**, descarregue os dois ficheiros .zip.

2. Depois de transferir os ficheiros .zip para o seu computador cliente, pode utilizar o Secure Copy Protocol (SCP) para copiar os ficheiros para o seu VM:

   ```bash
   scp *.zip <publicIpAddress>:.
   ```

3. SSH de volta ao seu Oracle VM em Azure para mover os ficheiros .zip para a pasta /opt. Em seguida, altere o proprietário dos ficheiros:

   ```bash
   ssh <publicIPAddress>
   sudo mv ./*.zip /opt
   cd /opt
   sudo chown grid:oinstall linuxamd64_12102_grid_1of2.zip
   sudo chown grid:oinstall linuxamd64_12102_grid_2of2.zip
   ```

4. Desaperte os ficheiros. (Instale a ferramenta de sintetizador Linux se ainda não estiver instalada.)

   ```bash
   sudo yum install unzip
   sudo unzip linuxamd64_12102_grid_1of2.zip
   sudo unzip linuxamd64_12102_grid_2of2.zip
   ```

5. Alterar a permissão:

   ```bash
   sudo chown -R grid:oinstall /opt/grid
   ```

6. Atualizar espaço de troca configurado. Os componentes da Oracle Grid precisam de pelo menos 6,8 GB de espaço de troca para instalar a Grelha. O tamanho do ficheiro de troca padrão para imagens Oracle Linux em Azure é de apenas 2048MB. É necessário `ResourceDisk.SwapSizeMB` aumentar `/etc/waagent.conf` o ficheiro e reiniciar o serviço WALinuxAgent para que as definições atualizadas entrem em vigor. Como é um ficheiro só para leitura, é necessário alterar as permissões de ficheiros para permitir o acesso à escrita.

   ```bash
   sudo chmod 777 /etc/waagent.conf  
   vi /etc/waagent.conf
   ```

   Procure `ResourceDisk.SwapSizeMB` e altere o valor para **8192**. Terá de premir `insert` para introduzir o modo de inserção, digite o valor de **8192** e, em seguida, prima `esc` para voltar ao modo de comando. Para escrever as alterações e `:wq` deixar `enter`o ficheiro, digite e prima .
   
   > [!NOTE]
   > Recomendamos vivamente que `WALinuxAgent` utilize sempre para configurar o espaço de troca para que seja sempre criado no disco efémero local (disco temporário) para melhor desempenho. Para mais informações, consulte [Como adicionar um ficheiro de troca em máquinas virtuais Linux Azure](https://support.microsoft.com/en-us/help/4010058/how-to-add-a-swap-file-in-linux-azure-virtual-machines).

## <a name="prepare-your-local-client-and-vm-to-run-x11"></a>Prepare o seu cliente local e VM para executar x11
Configurar o Oracle ASM requer uma interface gráfica para completar a instalação e configuração. Estamos a usar o protocolo x11 para facilitar esta instalação. Se estiver a utilizar um sistema de cliente (Mac ou Linux) que já tem capacidades X11 ativadas e configuradas - pode saltar esta configuração e configurar exclusivamente para máquinas Windows. 

1. [Baixe o PuTTY](https://www.putty.org/) e [baixe o Xming](https://xming.en.softonic.com/) para o seu computador Windows. Terá de concluir a instalação de ambas as aplicações com os valores predefinidos antes de prosseguir.

2. Depois de instalar o PuTTY, abra um pedido de comando, mude para a pasta PuTTY (por exemplo, C:\Program Files\PuTTY), e corra `puttygen.exe` de forma a gerar uma chave.

3. No Gerador de Chaves PuTTY:
   
   1. Gere uma tecla selecionando o `Generate` botão.
   2. Copiar o conteúdo da chave (Ctrl+C).
   3. Selecione o botão `Save private key`.
   4. Ignore o aviso sobre a fixação da chave `OK`com uma frase-passe e, em seguida, selecione .

   ![Screenshot do gerador de chaves PuTTY](./media/oracle-asm/puttykeygen.png)

4. No seu VM, execute estes comandos:

   ```bash
   sudo su - grid
   mkdir .ssh 
   cd .ssh
   ```

5. Crie um ficheiro com o nome `authorized_keys`. Cola o conteúdo da chave neste ficheiro e, em seguida, guarde o ficheiro.

   > [!NOTE]
   > A chave deve `ssh-rsa`conter a corda . Além disso, o conteúdo da chave deve ser uma única linha de texto.
   >  

6. No seu sistema de clientes, inicie a PuTTY. No painel **de categorias,** vá à **Conexão** > **SSH** > **Auth**. No **ficheiro de teclas Privada para** caixa de autenticação, navegue para a chave que gerou anteriormente.

   ![Screenshot das opções de autenticação SSH](./media/oracle-asm/setprivatekey.png)

7. No painel **de categorias,** vá à **Ligação** > **SSH** > **X11**. Selecione a caixa de verificação de **reencaminhamento Enable X11.**

   ![Screenshot das opções de encaminhamento SSH X11](./media/oracle-asm/enablex11.png)

8. No painel **de categorias,** vá à **Sessão.** Introduza o seu `<publicIPaddress>` VM Oracle ASM na caixa `Saved Session` de diálogo de `Save`nome anfitrião, preencha um novo nome e, em seguida, clique em .  Uma vez guardado, clique para `open` ligar à sua máquina virtual Oracle ASM.  A primeira vez que se liga, avisa-se que o sistema remoto não está em cache no seu registo. Clique `yes` para adicioná-lo e continuar.

   ![Screenshot das opções de sessão putty](./media/oracle-asm/puttysession.png)

## <a name="install-oracle-grid-infrastructure"></a>Instalar a Infraestrutura da Rede Oracle

Para instalar a Oracle Grid Infrastructure, complete os seguintes passos:

1. Inscreva-se como **grelha.** (Deverá poder fazer o insessão sem ser solicitado por uma senha.) 

   > [!NOTE]
   > Se estiver a executar o Windows, certifique-se de que começou a Xming antes de iniciar a instalação.

   ```bash
   cd /opt/grid
   ./runInstaller
   ```

   Oracle Grid Infrastructure 12c Lançamento 1 Instalador abre. (Pode levar alguns minutos para o instalador começar.)

2. Na página de Opção de **Instalação Selecione Instalar** **e configurar a infraestrutura**da grelha oracle para um servidor autónomo .

   ![Screenshot da página de opção de instalação select a instalação do instalador](./media/oracle-asm/install01.png)

3. Na página **Select Product Languages,** certifique-se de que o **inglês** ou o idioma que deseja está selecionado.  Clique em `next`.

4. Na página **Create ASM Disk Group:**
   - Introduza um nome para o grupo de discos.
   - Em **'Redundância',** selecione **External**.
   - Sob **o tamanho da unidade de atribuição,** selecione **4**.
   - Em **Add Disks,** selecione **ORCLASMSP**.
   - Clique em `next`.

5. Na página **'Palavra-passe', selecione** as **mesmas palavras-passe para estas contas** e introduza uma palavra-passe.

   ![Screenshot da página de palavra-passe ASM do instalador](./media/oracle-asm/install04.png)

6. Na página **'Opções de Gestão Especificação',** tem a opção de configurar o Controlo de Nuvem EM. Estamos a ignorar esta `next` opção - clique para continuar. 

7. Na página de Grupos de **Sistemas Operativos Privilegiados,** utilize as definições predefinidas. Clique `next` para continuar.

8. Na página **de localização de especificação,** utilize as definições predefinidas. Clique `next` para continuar.

9. Na página **Criar Inventário,** altere `/u01/app/grid/oraInventory`o Diretório de Inventário para . Clique `next` para continuar.

   ![Screenshot da página de Inventário de Criação do instalador](./media/oracle-asm/install08.png)

10. Na página de configuração de execução do **script Root,** selecione a caixa de verificação de scripts de **configuração de executar automaticamente.** Em seguida, selecione a opção credencial do **utilizador "root" use** e introduza a palavra-passe do utilizador raiz.

    ![Screenshot da página de configuração de execução de script raiz do instalador](./media/oracle-asm/install09.png)

11. Na página **'Verificações Pré-Requisito',** a configuração atual falhará com erros. Este é um comportamento esperado. Selecione `Fix & Check Again`.

12. Na caixa de diálogo **Fixup Script,** clique `OK`em .

13. Na página **Resumo,** reveja as definições `Install`selecionadas e, em seguida, clique em .

    ![Screenshot da página sumária do instalador](./media/oracle-asm/install12.png)

14. Uma caixa de diálogo de aviso aparece informando-lhe que os scripts de configuração precisam de ser executados como um utilizador privilegiado. Clique `Yes` para continuar.

15. Na página **'Terminar',** clique para `Close` terminar a instalação.

## <a name="set-up-your-oracle-asm-installation"></a>Instale a sua instalação Oracle ASM

Para configurar a sua instalação Oracle ASM, complete os seguintes passos:

1. Certifique-se de que ainda está inscrito na **grelha,** a partir da sua sessão X11. Talvez precises `enter` de bater para reanimar o terminal. Em seguida, lance o Assistente de Configuração de Gestão automática de Armazenamento oracle:

   ```bash
   cd /u01/app/grid/product/12.1.0/grid/bin
   ./asmca
   ```

   Abre o Oracle ASM Configuration Assistant.

2. Na caixa de diálogo **Configure ASM: Grupos de disco,** clique no `Create` botão e, em seguida, clique `Show Advanced Options`em .

3. Na caixa de diálogo **create disk group:**

   - Introduza o nome do grupo de disco **DATA**.
   - Em **discos de membro selecionados,** selecione **ORCL_DATA** e **ORCL_DATA1**.
   - Sob **o tamanho da unidade de atribuição,** selecione **4**.
   - Clique `ok` para criar o grupo de discos.
   - Clique `ok` para fechar a janela de confirmação.

   ![Screenshot da caixa de diálogo do Grupo Criar Disco](./media/oracle-asm/asm02.png)

4. Na caixa de diálogo **Configure ASM: Grupos de disco,** clique no `Create` botão e, em seguida, clique `Show Advanced Options`em .

5. Na caixa de diálogo **create disk group:**

   - Introduza o nome de grupo de disco **FRA**.
   - Em **'Redundância',** selecione **External (nenhum)**.
   - Em **discos de membro selecionados,** selecione **ORCL_FRA**.
   - Sob **o tamanho da unidade de atribuição,** selecione **4**.
   - Clique `ok` para criar o grupo de discos.
   - Clique `ok` para fechar a janela de confirmação.

   ![Screenshot da caixa de diálogo do Grupo Criar Disco](./media/oracle-asm/asm04.png)

6. Selecione **Saída** para fechar o Assistente de Configuração ASM.

   ![Screenshot da caixa de diálogo Configure ASM: Grupos de disco com botão saída](./media/oracle-asm/asm05.png)

## <a name="create-the-database"></a>Criar a base de dados

O software de base de dados Oracle já está instalado na imagem do Azure Marketplace. Para criar uma base de dados, complete os seguintes passos:

1. Mude os utilizadores para o superutilizador Oracle e, em seguida, inicialize o ouvinte para a exploração madeireira:

   ```bash
   su - oracle
   cd /u01/app/oracle/product/12.1.0/dbhome_1/bin
   ./dbca
   ```

   Abre o Assistente de Configuração de Bases de Dados.

2. Na página de Funcionamento da **Base de Dados,** clique `Create Database`em .

3. Na página modo **criação:**

   - Introduza um nome para a base de dados.
   - Para o tipo de **armazenamento,** certifique-se de que a **Gestão Automática de Armazenamento (ASM)** é selecionada.
   - Para **obter ficheiros de base**de dados Localização , utilize a localização sugerida por predefinição da ASM.
   - Para **a Área de Recuperação Rápida,** utilize a localização sugerida por predefinição da ASM.
   - digitar uma **palavra-passe administrativa** e **confirmar a palavra-passe**.
   - garantir `create as container database` que é selecionado.
   - escrever em `pluggable database name` um valor.

4. Na página **Resumo,** reveja as definições `Finish` selecionadas e clique em criar a base de dados.

   ![Captura de ecrã da página Resumo](./media/oracle-asm/createdb03.png)

5. A Base de Dados foi criada. Na página **'Terminar',** tem a opção de desbloquear contas adicionais para utilizar esta base de dados e alterar as palavras-passe. Se assim o desejar, selecione **Password** Management `close`- caso contrário clique em .

## <a name="delete-the-vm"></a>Elimine a VM

Configurou com sucesso a Oracle Automated Storage Management na imagem Oracle DB do Azure Marketplace.  Quando já não necessitar deste VM, pode utilizar o seguinte comando para remover o grupo de recursos, VM, e todos os recursos relacionados:

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

[Tutorial: Configure Oracle DataGuard](configure-oracle-dataguard.md)

[Tutorial: Configure Oracle GoldenGate](Configure-oracle-golden-gate.md)

Rever [Arquiteto um Oráculo DB](oracle-design.md)
