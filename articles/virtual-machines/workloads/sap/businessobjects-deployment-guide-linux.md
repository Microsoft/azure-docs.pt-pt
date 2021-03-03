---
title: SAP BusinessObjects Implantação de plataforma bi em Azure para linux | Microsoft Docs
description: Implementar e configurar a PLATAFORMA DE BI SAP BusinessObjects em Azure para o Linux
services: virtual-machines-windows,virtual-network,storage,azure-netapp-files,azure-files,mysql
documentationcenter: saponazure
author: dennispadia
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/05/2020
ms.author: depadia
ms.openlocfilehash: 7a40acf7dea11781a0381e93ea180c5185b7818b
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101671924"
---
# <a name="sap-businessobjects-bi-platform-deployment-guide-for-linux-on-azure"></a>Guia de implementação da plataforma de BI SAP BusinessObjects para Linux no Azure

Este artigo descreve a estratégia de implantação da Plataforma SAP BOBI no Azure para o Linux. Neste exemplo, duas máquinas virtuais com Discos Geridos Premium SSD à medida que o seu diretório de instalação estão configurados. A base de dados Azure para o MySQL é utilizada para a base de dados CMS e os ficheiros Azure NetApp para o Servidor de Repositório de Ficheiros são partilhados em ambos os servidores. A aplicação web padrão de Tomcat Java e a aplicação bi plataforma são instaladas em ambas as máquinas virtuais. Para carregar o equilíbrio do pedido do utilizador, o Application Gateway é utilizado com capacidades de descarregamento TLS/SSL nativos.

Este tipo de arquitetura é eficaz para pequenos ambientes de implantação ou não produção. Para a produção ou implantação em larga escala, pode ter anfitriões separados para aplicação web e pode também ter vários anfitriões de aplicações BOBI que permitem ao servidor processar mais informações.

![Implantação SAP BOBI em Azure para Linux](media/businessobjects-deployment-guide/businessobjects-deployment-linux.png)

Neste exemplo, abaixo a versão do produto e o layout do sistema de ficheiros é usado

- Plataforma SAP BusinessObjects 4.3
- SUSE Linux Enterprise Server 12 SP5
- Base de Dados Azure para MySQL (Versão: 8.0.15)
- Conector MySQL C API - libmysqlclient (versão: 6.1.11)

| Sistema de Ficheiros        | Descrição                                                                                                               | Tamanho (GB)             | Proprietário  | Group  | Armazenamento                    |
|--------------------|---------------------------------------------------------------------------------------------------------------------------|-----------------------|--------|--------|----------------------------|
| /usr/seiva           | O sistema de ficheiros para instalação de caso SAP BOBI, aplicação web tomcat predefinido e controladores de base de dados (se necessário) | Diretrizes de Dimensionamento SAP | bl1adm | sapsys | Disco Premium Gerido - SSD |
| /usr/seiva/frsinput  | O diretório de montagem é para os ficheiros partilhados em todos os anfitriões BOBI que serão usados como Diretório de Repositório de Ficheiros de Entrada  | Necessidade de Negócios         | bl1adm | sapsys | Azure NetApp Files         |
| /usr/seiva/frsoutput | O diretório de montagem é para os ficheiros partilhados em todos os anfitriões BOBI que serão usados como Diretório de Repositório de Ficheiros de Saída | Necessidade de Negócios         | bl1adm | sapsys | Azure NetApp Files         |

## <a name="deploy-linux-virtual-machine-via-azure-portal"></a>Implementar máquina virtual linux através do portal Azure

Nesta secção, vamos criar duas máquinas virtuais (VMs) com imagem do Sistema Operativo Linux (OS) para a Plataforma SAP BOBI. Os passos de alto nível para criar Máquinas Virtuais são os seguintes -

1. Criar um [Grupo de Recursos](../../../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups)

2. Criar uma [Rede Virtual.](../../../virtual-network/quick-create-portal.md#create-a-virtual-network)

   - Não utilize uma única sub-rede para todos os serviços Azure na implantação da Plataforma SAP BI. Com base na arquitetura da Plataforma SAP BI, é necessário criar várias sub-redes. Nesta implementação, criaremos três sub-redes - Sub-rede de aplicação, sub-rede de repositório de ficheiros e sub-rede de Gateway de Aplicação.
   - Em Azure, os Ficheiros Application Gateway e Azure NetApp precisam sempre de estar na sub-rede separada. Consulte o Gateway de [Aplicações Azure](../../../application-gateway/configuration-overview.md) e [as Diretrizes para o Azure NetApp Files Network Planning](../../../azure-netapp-files/azure-netapp-files-network-topologies.md) para obter mais detalhes.

3. Criar um conjunto de disponibilidade.

   - Para obter redundância para cada nível em implantação de vários casos, coloque as máquinas virtuais para cada nível num conjunto de disponibilidade. Certifique-se de separar os conjuntos de disponibilidade para cada nível com base na sua arquitetura.

4. Criar máquina virtual 1 **(azusbosl1).**

   - Pode usar imagem personalizada ou escolher uma imagem do Azure Marketplace. Consulte a [implementação de um VM do Mercado Azure para SAP](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/deployment-guide.md#scenario-1-deploying-a-vm-from-the-azure-marketplace-for-sap) ou [implantação de um VM com uma imagem personalizada para SAP](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/deployment-guide.md#scenario-2-deploying-a-vm-with-a-custom-image-for-sap) com base na sua necessidade.

5. Criar Máquina Virtual 2 **(azusbosl2).**
6. Adicione um disco Premium SSD. Será utilizado como diretório de instalação SAP BOBI.

## <a name="provision-azure-netapp-files"></a>Provision Azure NetApp Files

Antes de continuar com a configuração para ficheiros Azure NetApp, familiarize-se com a documentação do Azure [NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md).

O Azure NetApp Files está disponível em várias [regiões do Azure.](https://azure.microsoft.com/global-infrastructure/services/?products=netapp) Verifique se a região de Azure selecionada oferece ficheiros Azure NetApp.

Utilize [a disponibilidade de Ficheiros Azure NetApp por página da Região Azure](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all) para verificar a disponibilidade de Ficheiros Azure NetApp por região.

Solicite a bordo aos Ficheiros Azure NetApp indo para [registar as instruções do Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-register.md) antes de implementar ficheiros Azure NetApp.

### <a name="deploy-azure-netapp-files-resources"></a>Implementar recursos de ficheiros Azure NetApp

As seguintes instruções pressupõem que já implementou a sua [rede virtual Azure](../../../virtual-network/virtual-networks-overview.md). Os recursos e VMs do Azure NetApp, onde serão montados os recursos do Azure NetApp Files, devem ser implantados na mesma rede virtual Azure ou em redes virtuais Azure.

1. Se ainda não implementou os recursos, solicite [a bordo para os Ficheiros Azure NetApp](../../../azure-netapp-files/azure-netapp-files-register.md).

2. Crie uma conta NetApp na região Azure selecionada seguindo as instruções na [Criação de uma conta NetApp.](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md)

3. Crie um pool de capacidade do Azure NetApp Files seguindo as instruções em [Configurar um conjunto de capacidades Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md).

   - A arquitetura da Plataforma SAP BI apresentada neste artigo utiliza um único pool de capacidade Azure NetApp Files ao nível do Serviço *Premium.* Para o Servidor repositório de ficheiros SAP BI no Azure, recomendamos a utilização de um Azure NetApp Files *Premium* ou um [Nível](../../../azure-netapp-files/azure-netapp-files-service-levels.md)de serviço *Ultra* .

4. Delege uma sub-rede para ficheiros Azure NetApp, conforme descrito nas instruções em [Delegado uma sub-rede para ficheiros Azure NetApp](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md).

5. Implementar volumes de ficheiros Azure NetApp seguindo as instruções na [Criar um volume NFS para ficheiros Azure NetApp](../../../azure-netapp-files/azure-netapp-files-create-volumes.md).

   O volume ANF pode ser implantado como NFSv3 e NFSv4.1, uma vez que ambos os protocolos são suportados para a Plataforma SAP BOBI. Implemente os volumes na respetiva sub-rede Azure NetApp. Os endereços IP dos Volumes Azure NetApp são atribuídos automaticamente.

Tenha em mente que os recursos do Azure NetApp Files e os VMs Azure devem estar na mesma rede virtual Azure ou em redes virtuais Azure. Por exemplo, azusbobi-frsinput, azusbobi-frsoutput são os nomes de volume e nfs://10.31.2.4/azusbobi-frsinput, nfs://10.31.2.4/azusbobi-frsoutput são os caminhos de arquivo para os volumes de ficheiros Azure NetApp.

- Volume azusbobi-frsinput (nfs://10.31.2.4/azusbobi-frsinput)
- Volume azusbobi-frsoutput (nfs://10.31.2.4/azusbobi-frsoutput)

### <a name="important-considerations"></a>Considerações importantes

Enquanto está a criar os seus Ficheiros Azure NetApp para o Servidor de Repositório de Ficheiros da Plataforma SAP BOBI, esteja ciente da seguinte consideração:

1. A capacidade mínima é de 4 tebibytes (TiB).
2. O tamanho mínimo do volume é de 100 gibibytes (GiB).
3. Os ficheiros Azure NetApp e todas as máquinas virtuais onde os volumes dos Ficheiros Azure NetApp serão montados devem estar na mesma rede virtual Azure ou em [redes virtuais na](../../../virtual-network/virtual-network-peering-overview.md) mesma região. O acesso dos Ficheiros Azure NetApp sobre o observante VNET na mesma região é agora suportado. O acesso do Azure NetApp ao que está a ser olhando global ainda não está suportado.
4. A rede virtual selecionada deve ter uma sub-rede delegada nos Ficheiros Azure NetApp.
5. Com a política de [exportação](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md)do Azure NetApp Files, pode controlar os clientes autorizados, o tipo de acesso (ler-escrever, ler apenas, e assim por diante).
6. A funcionalidade Azure NetApp Files ainda não está ciente da zona. Atualmente, a funcionalidade não está implantada em todas as zonas de disponibilidade numa região do Azure. Esteja ciente das potenciais implicações de latência em algumas regiões de Azure.
7. Os volumes de Ficheiros Azure NetApp podem ser implantados como volumes NFSv3 ou NFSv4.1. Ambos os protocolos são suportados para as Aplicações da Plataforma SAP BI.

## <a name="configure-file-systems-on-linux-servers"></a>Configurar sistemas de ficheiros em servidores linux

Os passos desta secção utilizam os seguintes prefixos:

**[A]**: O passo aplica-se a todos os anfitriões

### <a name="format-and-mount-sap-file-system"></a>Formato e suporte sistema de ficheiros SAP

1. **[A]** Lista todos os discos anexos

    ```bash
    sudo lsblk
    NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
    sda      8:0    0   30G  0 disk
    ├─sda1   8:1    0    2M  0 part
    ├─sda2   8:2    0  512M  0 part /boot/efi
    ├─sda3   8:3    0    1G  0 part /boot
    └─sda4   8:4    0 28.5G  0 part /
    sdb      8:16   0   32G  0 disk
    └─sdb1   8:17   0   32G  0 part /mnt
    sdc      8:32   0  128G  0 disk
    sr0     11:0    1  628K  0 rom  
    # Premium SSD of 128 GB is attached to Virtual Machine, whose device name is sdc
    ```

2. **[A]** Dispositivo de bloqueio de formato para /usr/seiva

    ```bash
    sudo mkfs.xfs /dev/sdc
    ```

3. **[A]** Criar diretório de montagem

    ```bash
    sudo mkdir -p /usr/sap
    ```

4. **[A]** Obter UUID do dispositivo de bloco

    ```bash
    sudo blkid

    #It will display information about block device. Copy UUID of the formatted block device

    /dev/sdc: UUID="0eb5f6f8-fa77-42a6-b22d-7a9472b4dd1b" TYPE="xfs"
    ```

5. **[A]** Manter a entrada do sistema de ficheiros em /etc/fstab

    ```bash
    sudo echo "UUID=0eb5f6f8-fa77-42a6-b22d-7a9472b4dd1b /usr/sap xfs defaults,nofail 0 2" >> /etc/fstab
    ```

6. **[A]** Sistema de ficheiros mount

    ```bash
    sudo mount -a

    sudo df -h

    Filesystem                     Size  Used Avail Use% Mounted on
    devtmpfs                       7.9G  8.0K  7.9G   1% /dev
    tmpfs                          7.9G   82M  7.8G   2% /run
    tmpfs                          7.9G     0  7.9G   0% /sys/fs/cgroup
    /dev/sda4                       29G  1.8G   27G   6% /
    tmpfs                          1.6G     0  1.6G   0% /run/user/1000
    /dev/sda3                     1014M   87M  928M   9% /boot
    /dev/sda2                      512M  1.1M  511M   1% /boot/efi
    /dev/sdb1                       32G   49M   30G   1% /mnt
    /dev/sdc                       128G   29G  100G  23% /usr/sap
    ```

### <a name="mount-azure-netapp-files-volume"></a>Monte volume de ficheiros NetApp do Monte Azure

1. **[A]** Criar diretórios de montagem

   ```bash
   sudo mkdir -p /usr/sap/frsinput
   sudo mkdir -p /usr/sap/frsoutput
   ```

2. **[A]** Configurar o Cliente OS para apoiar o NFSv4.1 Mount **(Apenas aplicável se utilizar o NFSv4.1)**

   Se estiver a utilizar volumes de Ficheiros Azure NetApp com o protocolo NFSv4.1, execute a seguinte configuração em todos os VMs, onde os volumes de Ficheiros NFSv4.1 do Azure NetApp precisam de ser montados.

   **Verifique as definições de domínio NFS**

   Certifique-se de que o domínio está configurado como o domínio padrão dos Ficheiros Azure NetApp que é,  **defaultv4iddomain.com** e o mapeamento não é definido para **ninguém**.

   ```bash
   sudo cat /etc/idmapd.conf
   # Example
   [General]
   Domain = defaultv4iddomain.com
   [Mapping]
   Nobody-User = nobody
   Nobody-Group = nobody
   ```

   > [!Important]
   >
   > Certifique-se de que define o domínio NFS em /etc/idmapd.conf no VM para corresponder à configuração de domínio predefinido nos ficheiros Azure NetApp: **defaultv4iddomain.com**. Se houver um desfasamento entre a configuração de domínio no cliente NFS (ou seja, o VM) e o servidor NFS, ou seja, a configuração do Azure NetApp, então as permissões para ficheiros nos volumes Azure NetApp que são montados nos VMs serão apresentadas como ninguém.

   Verificar `nfs4_disable_idmapping` . Deve ser definido para **Y.** Para criar a estrutura do diretório onde `nfs4_disable_idmapping` se encontra, execute o comando de montagem. Não poderá criar manualmente o diretório em /sys/módulos, porque o acesso é reservado para o núcleo/condutores.

   ```bash
   # Check nfs4_disable_idmapping
   cat /sys/module/nfs/parameters/nfs4_disable_idmapping

   # If you need to set nfs4_disable_idmapping to Y
   mkdir /mnt/tmp
   mount -t nfs -o sec=sys,vers=4.1 10.31.2.4:/azusbobi-frsinput /mnt/tmp
   umount /mnt/tmp

   echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping

   # Make the configuration permanent
   echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
   ```

3. **[A]** Adicionar entradas de montagem

   Se utilizar o NFSv3

   ```bash
   sudo echo "10.31.2.4:/azusbobi-frsinput /usr/sap/frsinput  nfs   rw,hard,rsize=65536,wsize=65536,vers=3" >> /etc/fstab
   sudo echo "10.31.2.4:/azusbobi-frsoutput /usr/sap/frsoutput  nfs   rw,hard,rsize=65536,wsize=65536,vers=3" >> /etc/fstab
   ```

   Se utilizar o NFSv4.1

   ```bash
   sudo echo "10.31.2.4:/azusbobi-frsinput /usr/sap/frsinput  nfs   rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys" >> /etc/fstab
   sudo echo "10.31.2.4:/azusbobi-frsoutput /usr/sap/frsoutput  nfs   rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys" >> /etc/fstab
   ```

4. **[A]** Volumes do Monte NFS

   ```bash
   sudo mount -a

   sudo df -h

   Filesystem                     Size  Used Avail Use% Mounted on
   devtmpfs                       7.9G  8.0K  7.9G   1% /dev
   tmpfs                          7.9G   82M  7.8G   2% /run
   tmpfs                          7.9G     0  7.9G   0% /sys/fs/cgroup
   /dev/sda4                       29G  1.8G   27G   6% /
   tmpfs                          1.6G     0  1.6G   0% /run/user/1000
   /dev/sda3                     1014M   87M  928M   9% /boot
   /dev/sda2                      512M  1.1M  511M   1% /boot/efi
   /dev/sdb1                       32G   49M   30G   1% /mnt
   /dev/sdc                       128G   29G  100G  23% /usr/sap
   10.31.2.4:/azusbobi-frsinput   101T   18G  100T   1% /usr/sap/frsinput
   10.31.2.4:/azusbobi-frsoutput  100T  512K  100T   1% /usr/sap/frsoutput
   ```

## <a name="configure-cms-database---azure-database-for-mysql"></a>Configure base de dados CMS - Base de dados Azure para MySQL

Esta secção fornece detalhes sobre como fornecer Azure Database para o MySQL usando o portal Azure. Também fornece instruções sobre como criar bases de dados cms e de auditoria para a Plataforma SAP BOBI e uma conta de utilizador para aceder à base de dados.

As diretrizes só são aplicáveis se estiver a utilizar o Azure DB para o MySQL. Para outras bases de dados, consulte a SAP ou documentação específica da base de dados para obter instruções.

### <a name="create-an-azure-database-for-mysql"></a>Criar uma base de dados Azure para o MySQL

Inscreva-se no portal Azure e siga os passos mencionados neste [Guia rápido de Base de Dados de Azure para o MySQL](../../../mysql/quickstart-create-mysql-server-database-using-azure-portal.md). Poucos pontos a notar ao providenciar a Base de Dados Azure para o MySQL -

1. Selecione a mesma região para Azure Database para o MySQL onde os seus servidores de aplicações SAP BI Platform estão em execução.

2. Escolha uma versão DB suportada com base na [Matriz de Disponibilidade de Produto (PAM) para SAP BI](https://support.sap.com/pam) específica para a sua versão SAP BOBI. Siga as mesmas diretrizes de compatibilidade abordadas para o MySQL AB em SAP PAM

3. Em "compute+storage", selecione **o servidor Configure** e selecione o nível de preços apropriado com base na sua saída de dimensionamento.

4. **Armazenamento O crescimento automático** é ativado por predefinição. Tenha em mente que [o armazenamento](../../../mysql/concepts-pricing-tiers.md#storage) só pode ser dimensionado, não para baixo.

5. Por predefinição, **o período de retenção de back up** é de sete dias, mas pode [configurar opcionalmente](../../../mysql/howto-restore-server-portal.md#set-backup-configuration) até 35 dias.

6. As cópias de segurança da Base de Dados Azure para o MySQL são localmente redundantes por padrão, por isso, se pretender cópias de segurança do servidor no armazenamento geo-redundante, selecione **Geographically Redundante** das Opções de **Redundância de Backup**.

> [!NOTE]
> A alteração das [opções de redundância de backup](../../../mysql/concepts-backup.md#backup-redundancy-options) após a criação do servidor não é suportada.

### <a name="configure-connection-security"></a>Configurar a segurança da ligação

Por predefinição, o servidor criado está protegido com uma firewall e não é acessível publicamente. Para fornecer acesso à rede virtual onde os servidores de aplicações da Plataforma SAP BI estão em execução, siga os passos abaixo -  

1. Aceda aos recursos do servidor no portal Azure e selecione **a segurança de ligação** do menu lateral esquerdo para o seu recurso de servidor.
2. Selecione **Sim** para **Permitir o acesso aos serviços Azure**.
3. De acordo com as regras VNET, **selecione Adicionar a rede virtual existente.** Selecione a rede virtual e a sub-rede do servidor de aplicações da Plataforma SAP BI. Também precisa de fornecer acesso à caixa Jump ou a outros servidores de onde pode ligar [a bancada mySQL workbench](../../../mysql/connect-workbench.md) à Azure Database para o MySQL. MySQL Workbench será usado para criar base de dados cms e auditoria
4. Assim que forem adicionadas redes virtuais, **selecione Save**.

### <a name="create-cms-and-audit-database"></a>Criar CMS e base de dados de auditoria

1. Descarregue e instale a bancada mySQL do [site mySQL](https://dev.mysql.com/downloads/workbench/). Certifique-se de que instala a bancada de trabalho MySQL no servidor que pode aceder à Base de Dados Azure para o MySQL.

2. Ligue-se ao servidor utilizando a bancada mySQL Workbench. Siga as instruções mencionadas neste [artigo.](../../../mysql/connect-workbench.md#get-connection-information) Se o teste de ligação for bem sucedido, receberá a seguinte mensagem -

   ![Ligação sql workbench](media/businessobjects-deployment-guide/businessobjects-sql-workbench.png)

3. No separador de consulta SQL, corra abaixo a consulta para criar esquema para a base de dados cms e de auditoria.

   ```sql
   # Here cmsbl1 is the database name of CMS database. You can provide the name you want for CMS database.
   CREATE SCHEMA `cmsbl1` DEFAULT CHARACTER SET utf8;

   # auditbl1 is the database name of Audit database. You can provide the name you want for CMS database.
   CREATE SCHEMA `auditbl1` DEFAULT CHARACTER SET utf8;
   ```
   
4. Criar conta de utilizador para ligar ao esquema

   ```sql
   # Create a user that can connect from any host, use the '%' wildcard as a host part
   CREATE USER 'cmsadmin'@'%' IDENTIFIED BY 'password';
   CREATE USER 'auditadmin'@'%' IDENTIFIED BY 'password';

   # Grant all privileges to a user account over a specific database:
   GRANT ALL PRIVILEGES ON cmsbl1.* TO 'cmsadmin'@'%' WITH GRANT OPTION;
   GRANT ALL PRIVILEGES ON auditbl1.* TO 'auditadmin'@'%' WITH GRANT OPTION;

   # Following any updates to the user privileges, be sure to save the changes by issuing the FLUSH PRIVILEGES
   FLUSH PRIVILEGES;
   ```

5. Para verificar os privilégios e funções da conta de utilizador do MySQL

   ```sql
   USE sys;
   SHOW GRANTS for 'cmsadmin'@'%';
   +------------------------------------------------------------------------+
   | Grants for cmsadmin@%                                                  |
   +------------------------------------------------------------------------+
   | GRANT USAGE ON *.* TO `cmsadmin`@`%`                                   |
   | GRANT ALL PRIVILEGES ON `cmsbl1`.* TO `cmsadmin`@`%` WITH GRANT OPTION |
   +------------------------------------------------------------------------+

   USE sys;
   SHOW GRANTS FOR 'auditadmin'@'%';
   +----------------------------------------------------------------------------+
   | Grants for auditadmin@%                                                    |
   +----------------------------------------------------------------------------+
   | GRANT USAGE ON *.* TO `auditadmin`@`%`                                     |
   | GRANT ALL PRIVILEGES ON `auditbl1`.* TO `auditadmin`@`%` WITH GRANT OPTION |
   +----------------------------------------------------------------------------+
   ```

### <a name="install-mysql-c-api-connector-libmysqlclient-on-linux-server"></a>Instale o conector API MySQL C (libmysqlclient) no servidor linux

Para o servidor de aplicação SAP BOBI aceder à base de dados, requer cliente/motoristas de base de dados. O Conector API mySQL C para Linux tem de ser utilizado para aceder a bases de dados cms e de auditoria. A ligação da ODBC à base de dados CMS não é suportada. Esta secção fornece instruções sobre como configurar o Conector API API MySQL C no Linux.

1. Consulte [os controladores e ferramentas de gestão do MySQL compatíveis com a Base de Dados Azure para o artigo mySQL,](../../../mysql/concepts-compatibility.md) que descreve os controladores compatíveis com a Base de Dados Azure para o MySQL. Verifique o controlador **MySQL Connector/C (libmysqlclient)** no artigo.

2. Consulte este [link](https://downloads.mysql.com/archives/c-c/) para baixar os controladores.

3. Selecione o sistema operativo e descarregue o pacote rpm de componentes partilhados do Conector MySQL. Neste exemplo, é utilizada a versão do conector mysql-c-share-6.1.11.

4. Instale o conector em todas as instâncias de aplicação SAP BOBI.

   ```bash
   # Install rpm package
   SLES: sudo zypper install <package>.rpm
   RHEL: sudo yum install <package>.rpm
   ```

5. Verifique o caminho da libmysqlclient.so

   ```bash
   # Find the location of libmysqlclient.so file
   whereis libmysqlclient

   # sample output
   libmysqlclient: /usr/lib64/libmysqlclient.so
   ```

6. Desaponte LD_LIBRARY_PATH para apontar para o `/usr/lib64` diretório da conta de utilizador que será utilizada para a instalação.

   ```bash
   # This configuration is for bash shell. If you are using any other shell for sidadm, kindly set environment variable accordingly.
   vi /home/bl1adm/.bashrc

   export LD_LIBRARY_PATH=/usr/lib64
   ```

## <a name="server-preparation"></a>Preparação do servidor

Os passos desta secção utilizam os seguintes prefixos:

**[A]**: O passo aplica-se a todos os anfitriões.

1. **[A]** Com base no sabor do Linux (SLES ou RHEL), é necessário definir parâmetros de kernel e instalar bibliotecas necessárias. Consulte a secção **de requisitos do Sistema** no Guia de [Instalação da Plataforma de Inteligência Empresarial para Unix](https://help.sap.com/viewer/65018c09dbe04052b082e6fc4ab60030/4.3/en-US).

2. **[A]** Certifique-se de que o fuso horário da sua máquina está corretamente definido. Consulte a [secção de requisitos Unix e Linux adicionais](https://help.sap.com/viewer/65018c09dbe04052b082e6fc4ab60030/4.3/en-US/46b143336e041014910aba7db0e91070.html) no Guia de Instalação.

3. **[A]** Crie a conta de utilizador **(bl1** adm) e grupo (sapsys) sob os quais os processos de fundo do software podem ser executados. Utilize esta conta para executar a instalação e executar o software. A conta não requer privilégios de raiz.

4. **[A]** Defina o ambiente da conta de utilizador **(bl1** adm) para utilizar um local UTF-8 suportado e certifique-se de que o software da consola suporta conjuntos de caracteres UTF-8. Para garantir que o seu sistema operativo utiliza o local correto, desajuste as variáveis ambientais LC_ALL e LANG para o seu local preferido no seu ambiente de utilizador **(bl1** adm).

   ```bash
   # This configuration is for bash shell. If you are using any other shell for sidadm, kindly set environment variable accordingly.
   vi /home/bl1adm/.bashrc

   export LANG=en_US.utf8
   export LC_ALL=en_US.utf8
   ```

5. **[A]** Conta de utilizador configurar **(bl1** adm).

   ```bash
   # Set ulimit for bl1adm to unlimited
   root@azusbosl1:~> ulimit -f unlimited bl1adm
   root@azusbosl1:~> ulimit -u unlimited bl1adm

   root@azusbosl1:~> su - bl1adm
   bl1adm@azusbosl1:~> ulimit -a

   core file size          (blocks, -c) unlimited
   data seg size           (kbytes, -d) unlimited
   scheduling priority             (-e) 0
   file size               (blocks, -f) unlimited
   pending signals                 (-i) 63936
   max locked memory       (kbytes, -l) 64
   max memory size         (kbytes, -m) unlimited
   open files                      (-n) 1024
   pipe size            (512 bytes, -p) 8
   POSIX message queues     (bytes, -q) 819200
   real-time priority              (-r) 0
   stack size              (kbytes, -s) 8192
   cpu time               (seconds, -t) unlimited
   max user processes              (-u) unlimited
   virtual memory          (kbytes, -v) unlimited
   file locks                      (-x) unlimited
   ```

6. Descarregue e extraia meios para a PLATAFORMA BI SAP BusinessObjects do SAP Service Marketplace.

## <a name="installation"></a>Instalação

Verifique localmente para obter o anúncio da conta **de utilizador bl1** no servidor

```bash
bl1adm@azusbosl1:~> locale
LANG=en_US.utf8
LC_ALL=en_US.utf8
```

Navegue para meios de comunicação da SAP BusinessObjects BI Platform e corra abaixo do comando com **bl1** adm utilizador -

```bash
./setup.sh -InstallDir /usr/sap/BL1
```

Siga o Guia de Instalação [da Plataforma SAP BOBI](https://help.sap.com/viewer/product/SAP_BUSINESSOBJECTS_BUSINESS_INTELLIGENCE_PLATFORM) para o Unix, específico da sua versão. Poucos pontos a notar durante a instalação da Plataforma SAP BOBI.

- No ecrã **de Registo de Produto configurado,** pode utilizar a chave de licença temporária para soluções SAP BusinessObjects a partir do SAP Note [1288121](https://launchpad.support.sap.com/#/notes/1288121) ou pode gerar chave de licença no MERCADO de Serviços SAP

- No ecrã **Select Install Type,** selecione instalação **completa** no primeiro servidor (azusbosl1), para outro servidor (azusbosl2) selecione **Custom /Expande** que expandirá a configuração BOBI existente.

- No ecrã **Select Predefinido ou existente,** selecione **configurar uma base de dados existente,** que lhe solicitará a seleção da base de dados CMS e auditoria. Selecione **MySQL** para o tipo de base de dados CMS e tipo de base de dados de auditoria.

  Também pode selecionar Nenhuma base de dados de auditoria, se não quiser configurar a auditoria durante a instalação.

- Selecione as opções apropriadas no **ecrã do Servidor de Aplicações Web De Java** com base na sua arquitetura SAP BOBI. Neste exemplo, selecionamos a opção 1, que instala o servidor Tomcat na mesma Plataforma SAP BOBI.

- Introduza informações sobre bases de **dados CMS na Base de Dados de Repositório CMS - MySQL**. Entrada de exemplo para informações de base de dados CMS para instalação linux. Azure Database for MySQL é utilizada na porta padrão 3306
  
  ![Implantação SAP BOBI no Linux - Base de Dados CMS](media/businessobjects-deployment-guide/businessobjects-deployment-linux-sql-cms.png)

- (Opcional) Introduza informações da base de dados de auditoria em **Configure Audit Repository Database - MySQL**. Informação de exemplo para informação de base de dados de auditoria para instalação do Linux.

  ![Implantação DO SAP BOBI no Linux - Base de Dados de Auditoria](media/businessobjects-deployment-guide/businessobjects-deployment-linux-sql-audit.png)

- Siga as instruções e introduza as entradas necessárias para completar a instalação.

Para a colocação em várias instâncias, executar a instalação no segundo hospedeiro (azusbosl2). Durante **o ecrã Select Install Type,** selecione Custom / **Expande** que expandirá a configuração BOBI existente.

Na base de dados Azure para a oferta mySQL, um gateway é usado para redirecionar as ligações para instâncias de servidor. Depois de a ligação ser estabelecida, o cliente MySQL apresenta a versão do MySQL definida no gateway, não a versão real em execução na instância do servidor MySQL. Para determinar a versão da instância do servidor MySQL, utilize o comando `SELECT VERSION();` no prompt do MySQL. Assim, na Consola de Gestão Central (CMC), encontrará diferentes versões de base de dados que é basicamente a versão definida no gateway. Consulte [a Base de Dados de Azure suportada para versões de servidor MySQL](../../../mysql/concepts-supported-versions.md) para obter mais detalhes.

![Implantação SAP BOBI em Definições Linux - CMC](media/businessobjects-deployment-guide/businessobjects-deployment-linux-sql-cmc.png)

```sql
# Run direct query to the database using MySQL Workbench

select version();

+-----------+
| version() |
+-----------+
| 8.0.15    |
+-----------+
```

## <a name="post-installation"></a>Instalação de postes

### <a name="tomcat-clustering---session-replication"></a>Agrupamento tomcat - replicação da sessão

O Tomcat suporta o agrupamento de dois ou mais servidores de aplicações para replicação de sessão e failover. As sessões da plataforma SAP BOBI são serializadas, uma sessão de utilizador pode falhar sem problemas com outro caso de tomcat, mesmo quando um servidor de aplicação falha.

Por exemplo, se um utilizador estiver ligado a um servidor web que falhe enquanto o utilizador estiver a navegar numa hierarquia de pastas na aplicação SAP BI. Com um cluster corretamente configurado, o utilizador pode continuar a navegar na hierarquia da pasta sem ser redirecionado para assinar na página.

Na nota [SAP 2808640,](https://launchpad.support.sap.com/#/notes/2808640)são fornecidos passos para configurar o agrupamento de tomcats utilizando multicast. Mas em Azure, o multicast não é apoiado. Assim, para fazer o agrupamento Tomcat funcionar em Azure, deve utilizar [o StaticMembershipInterceptor](https://tomcat.apache.org/tomcat-8.0-doc/config/cluster-interceptor.html#Static_Membership) (NOTA SAP [2764907).](https://launchpad.support.sap.com/#/notes/2764907) Verifique o [Agrupamento Tomcat utilizando a Static Membership para a PLATAFORMA BI SAP BusinessObjects](https://blogs.sap.com/2020/09/04/sap-on-azure-tomcat-clustering-using-static-membership-for-sap-businessobjects-bi-platform/) no blog SAP para configurar o aglomerado de tomcat em Azure.

### <a name="load-balancing-web-tier-of-sap-bi-platform"></a>Nível web de equilíbrio de carga da plataforma SAP BI

Na implementação de vários instâncias SAP BOBI, os servidores de Aplicação Web Java (web tier) estão a funcionar em dois ou mais anfitriões. Para distribuir a carga do utilizador uniformemente através de servidores web, pode utilizar um balanceador de carga entre utilizadores finais e servidores web. No Azure, pode utilizar o Azure Load Balancer ou o Azure Application Gateway para gerir o tráfego nos servidores da sua aplicação web. Os detalhes sobre cada oferta são explicados na secção seguinte.

#### <a name="azure-load-balancer-network-based-load-balancer"></a>Equilibrador de carga Azure (balançador de carga baseado em rede)

[O Azure Load Balancer](../../../load-balancer/load-balancer-overview.md) é um balanceador de carga de alto desempenho, de baixa latência 4 (TCP, UDP) que distribui o tráfego entre máquinas virtuais saudáveis. Uma sonda de saúde do balanceador de carga monitoriza uma determinada porta em cada VM e distribui apenas o tráfego para uma máquina virtual operacional. Pode escolher um equilibrador de carga pública ou um equilibrador interno, dependendo se pretende que a Plataforma SAP BI seja ou não acessível a partir da Internet. A sua zona redundante, garantindo alta disponibilidade em zonas de disponibilidade.

Consulte a secção de Balanço de Carga Interna na figura abaixo, onde o servidor de aplicações web funciona na porta 8080, porta Tomcat HTTP padrão, que será monitorizada por sonda de saúde. Assim, qualquer pedido de entrada que venha de utilizadores finais será redirecionado para os servidores de aplicações web (azusbosl1 ou azusbosl2) no pool de backend. O balançador de carga não suporta a rescisão TLS/SSL (também conhecida como Descarga TLS/SSL). Se estiver a utilizar o equilibrador de carga Azure para distribuir tráfego por servidores web, recomendamos a utilização do Balancer de Carga Padrão.

> [!NOTE]
> Quando os VMs sem endereços IP públicos forem colocados no pool de backend de saldos de carga standard Azure (sem endereço IP público), não haverá conectividade de saída na Internet, a menos que seja realizada uma configuração adicional para permitir o encaminhamento para pontos finais públicos. Para obter detalhes sobre como alcançar a conectividade de saída, consulte [a conectividade do ponto final público para máquinas virtuais utilizando o Azure Standard Load Balancer em cenários de alta disponibilidade SAP](high-availability-guide-standard-load-balancer-outbound-connections.md).

![Azure Load Balancer para equilibrar tráfego através de Servidores Web](media/businessobjects-deployment-guide/businessobjects-deployment-load-balancer.png)

#### <a name="azure-application-gateway-web-application-load-balancer"></a>Gateway de aplicação Azure (balanceador de carga de aplicação web)

[O Azure Application Gateway (AGW)](../../../application-gateway/overview.md) fornece o Controlador de Entrega de Aplicações (ADC) como um serviço, que é usado para ajudar a aplicação a direcionar o tráfego do utilizador para um ou mais servidores de aplicações web. Oferece várias capacidades de equilíbrio de carga de camada 7, como descarregamento de TLS/SSL, Firewall de Aplicação Web (WAF), afinidade de sessão baseada em cookies e outras para as suas aplicações.

Na Plataforma SAP BI, o gateway de aplicações direciona o tráfego web da aplicação para os recursos especificados numa piscina de backend - azusbosl1 ou azusbos2. Você atribui um ouvinte ao porto, cria regras, e adiciona recursos a uma piscina de backend. Abaixo figura, o gateway de aplicações com endereço IP frontend privado (10.31.3.20) atua como ponto de entrada para os utilizadores, lida com ligações TLS/SSL (HTTPS - TCP/443) privadas, desencriptado o TLS/SSL e passando o pedido não encriptado (HTTP - TCP/8080) para os servidores na piscina de trás. Com a função de rescisão TLS/SSL incorporada, apenas precisamos de manter um certificado TLS/SSL no gateway de aplicações, o que simplifica as operações.

![Gateway de aplicações para equilibrar tráfego através de Servidores Web](media/businessobjects-deployment-guide/businessobjects-deployment-application-gateway.png)

Para configurar o Gateway de aplicações para o SERVIDOR Web SAP BOBI, pode consultar [os Servidores Web SAP BOBI de Equilíbrio de Carga utilizando o Gateway de Aplicações Azure](https://blogs.sap.com/2020/09/17/sap-on-azure-load-balancing-web-application-servers-for-sap-bobi-using-azure-application-gateway/) no blog SAP.

> [!NOTE]
> Recomendamos que utilize o Gateway de aplicação Azure para carregar o equilíbrio do tráfego para o servidor web, uma vez que fornece funcionalidades como descarregamento SSL, gestão centralização SSL para reduzir a encriptação e desencriptação no servidor, algoritmo de Round-Robin para distribuir tráfego, capacidades de Firewall de Aplicação Web (WAF), alta disponibilidade e assim por diante.

### <a name="sap-businessobjects-bi-platform---back-up-and-restore"></a>SAP BusinessObjects BI Platform - voltar e restaurar

Backup and Restore é um processo de criação de cópias periódicas de dados e aplicações para separar a localização. Assim, pode ser restaurado ou recuperado para estado anterior se os dados ou aplicações originais forem perdidos ou danificados. É também uma componente essencial de qualquer estratégia de recuperação de desastres comerciais.

Para desenvolver uma estratégia abrangente de backup e restauro para a Plataforma SAP BOBI, identifique os componentes que levam ao tempo de inatividade do sistema ou à interrupção na aplicação. Na Plataforma SAP BOBI, a cópia de segurança dos seguintes componentes é vital para proteger a aplicação.

- Diretório de Instalação SAP BOBI (Discos Premium Geridos)
- Servidor de Repositório de Ficheiros (Ficheiros Azure NetApp ou Ficheiros Azure Premium)
- Base de Dados CMS (Base de Dados Azure para MySQL ou Base de Dados em VM Azure)

A secção seguinte descreve como implementar a estratégia de backup e restauro para cada componente na Plataforma SAP BOBI.

#### <a name="backup--restore-for-sap-bobi-installation-directory"></a>Backup & restaurar para o diretório de instalação SAP BOBI

Em Azure, a forma mais simples de fazer backup dos servidores de aplicações e de todos os discos anexos é utilizando o [Serviço de Backup Azure.](../../../backup/backup-overview.md) Fornece cópias de segurança independentes e isoladas para proteger a destruição não intencional dos dados nos seus VMs. As cópias de segurança são armazenadas num cofre dos Serviços de Recuperação com gestão de pontos de recuperação incorporada. A configuração e o dimensionamento são simples, as cópias de segurança são otimizadas e podem ser restauradas facilmente quando necessário.

Como parte do processo de backup, o instantâneo é recolhido e os dados são transferidos para o cofre do Serviço de Recuperação sem impacto nas cargas de trabalho de produção. O instantâneo proporciona um nível de consistência diferente como descrito no artigo [de consistência instantânea.](../../../backup/backup-azure-vms-introduction.md#snapshot-consistency) Também pode optar por fazer backup do subconjunto dos discos de dados em VM, utilizando a cópia de segurança dos discos seletivos e restaurando a funcionalidade. Para obter mais informações, consulte o documento [de backup Azure VM](../../../backup/backup-azure-vms-introduction.md) e [FAQs - Backup Azure VMs](../../../backup/backup-azure-vm-backup-faq.yml).

#### <a name="backup--restore-for-file-repository-server"></a>Backup & restaurar para servidor de repositório de ficheiros

Para **ficheiros Azure NetApp,** pode criar instantâneos a pedido e agendar instantâneos automáticos utilizando políticas instantâneas. As cópias snapshot fornecem uma cópia pontual do seu volume ANF. Para obter mais informações, consulte [Gerir as imagens utilizando ficheiros Azure NetApp](../../../azure-netapp-files/azure-netapp-files-manage-snapshots.md).

A cópia de segurança **do Azure Files** está integrada no serviço [Azure Backup](../../../backup/backup-overview.md) nativo, que centraliza a função de backup e restauro juntamente com a cópia de segurança dos VMs e simplifica o trabalho de operação. Para obter mais informações, consulte a cópia de segurança do [Ficheiro Azure](../../../backup/azure-file-share-backup-overview.md) e [as FAQs - Faça backup de Ficheiros Azure](../../../backup/backup-azure-files-faq.md).

#### <a name="backup--restore-for-cms-database"></a>Backup & restaurar para base de dados CMS

A azure Database of MySQL é a oferta DBaaS em Azure, que cria automaticamente cópias de segurança do servidor e armazena-as em armazenamento localmente redundante ou geo-redundante do utilizador. A Azure Database do MySQL retira cópias dos ficheiros de dados e do registo de transações. Dependendo do tamanho máximo de armazenamento suportado, ou requer cópias de segurança completas e diferenciais (servidores de armazenamento máximo de 4-TB) ou cópias de segurança instantâneas (até 16-TB máximo de armazenamento). Estas cópias de segurança permitem restaurar um servidor em qualquer ponto no tempo dentro do período de retenção de backup configurado. O período de retenção por defeito de backup é de sete dias, o que pode [configurar opcionalmente](../../../mysql/howto-restore-server-portal.md#set-backup-configuration) até três dias. Todas as cópias de segurança são encriptadas através da encriptação AES de 256 bits.

Estes ficheiros de backup não são expostos ao utilizador e não podem ser exportados. Estas cópias de segurança só podem ser utilizadas para operações de restauro na Base de Dados Azure para o MySQL. Pode usar [o meu "mysqldump"](../../../mysql/concepts-migrate-dump-restore.md) para copiar uma base de dados. Para mais informações, consulte [a Cópia de Segurança e restauro na Base de Dados Azure para o MySQL](../../../mysql/concepts-backup.md).

Para a base de dados instalada em Máquinas Virtuais, pode utilizar ferramentas de backup padrão ou [cópia de segurança Azure](../../../backup/sap-hana-db-about.md) para base de dados HANA. Além disso, se os Serviços e ferramentas Azure não cumprirem os seus requisitos, pode utilizar outras ferramentas de backup ou script para criar backup de discos.

## <a name="sap-businessobjects-bi-platform-reliability"></a>SAP BusinessObjects fiabilidade da plataforma BI

A Plataforma BI SAP BusinessObjects inclui diferentes níveis, que são otimizados para tarefas e operações específicas. Quando um componente de um único nível fica indisponível, a aplicação SAP BOBI tornar-se-á inacessível ou determinada funcionalidade da aplicação não funcionará. Por isso, é preciso garantir que cada nível é concebido para ser fiável para manter a aplicação operacional sem qualquer perturbação de negócio.

Esta secção centra-se nas seguintes opções para a Plataforma SAP BOBI -

- **Alta Disponibilidade:** Uma plataforma alta disponível tem pelo menos dois de tudo dentro da região de Azure para manter a aplicação operacional se um dos servidores ficar indisponível.
- **Recuperação de Desastres:** É um processo de restaurar a funcionalidade da sua aplicação se houver alguma perda catastrófica como toda a Região de Azure fica indisponível devido a algum desastre natural.

A implementação desta solução varia em base na natureza da configuração do sistema em Azure. Assim, o cliente precisa de adaptar a solução de Alta Disponibilidade e Recuperação de Desastres com base na sua exigência de negócio.

### <a name="high-availability"></a>Elevada disponibilidade

Alta Disponibilidade refere-se a um conjunto de tecnologias que podem minimizar as perturbações de TI, fornecendo continuidade de aplicação/serviços de negócio através de componentes redundantes, tolerantes a falhas ou protegidos por falhas dentro do mesmo centro de dados. No nosso caso, os centros de dados estão dentro de uma região de Azure. O artigo [Arquitetura e Cenários de Alta Disponibilidade para SAP](sap-high-availability-architecture-scenarios.md) fornecem uma visão inicial sobre diferentes técnicas de alta disponibilidade e recomendação oferecida sobre Azure para aplicações SAP, que irá elogiar as instruções nesta secção.

Com base no resultado de dimensionamento da Plataforma SAP BOBI, é necessário projetar a paisagem e determinar a distribuição de componentes de BI através de Máquinas Virtuais E sub-redes Azure. O nível de redundância na arquitetura distribuída depende do objetivo de tempo de recuperação exigido (RTO) e do Objetivo do Ponto de Recuperação (RPO). A Plataforma SAP BOBI inclui diferentes níveis e componentes em cada nível devem ser concebidos para obter redundância. De modo que se um componente falhar, não há qualquer interrupção na sua aplicação SAP BOBI. Por exemplo,

- Servidores de aplicações redundantes como servidores de aplicações bi e servidor web
- Componentes únicos como base de dados CMS, Servidor de Repositório de Ficheiros, Balanceador de Carga

A secção seguinte descreve como obter uma elevada disponibilidade em cada componente da Plataforma SAP BOBI.

#### <a name="high-availability-for-application-servers"></a>Alta disponibilidade para servidores de aplicações

Para os Servidores de Aplicações BI e Web, quer estejam instalados separadamente ou em conjunto, não necessita de uma solução específica de alta disponibilidade. Você pode alcançar uma alta disponibilidade por redundância, isto é, configurando várias instâncias de BI e Web Servers em várias Máquinas Virtuais Azure.

Para reduzir o impacto do tempo de inatividade devido a um ou mais eventos, é aconselhável seguir abaixo a alta prática de disponibilidade para os servidores de aplicações que executam em várias Máquinas virtuais.

- Utilize Zonas de Disponibilidade para proteger falhas do datacenter.
- Configure várias máquinas virtuais num Conjunto de Disponibilidade para redundância.
- Utilize discos geridos para VMs num conjunto de disponibilidade.
- Configure cada nível de aplicação em conjuntos de disponibilidade separados.

Para mais informações, consulte [Gerir a disponibilidade de máquinas virtuais Linux](../../manage-availability.md)

#### <a name="high-availability-for-cms-database"></a>Alta disponibilidade para base de dados CMS

Se estiver a utilizar o Serviço de Base de Dados Azure como serviço de serviço (DBaaS) para a base de dados CMS, o quadro de alta disponibilidade é fornecido por padrão. Basta selecionar a região e o serviço inerente a alta disponibilidade, redundância e capacidades de resiliência sem exigir que configurá quaisquer componentes adicionais. Para mais detalhes sobre o SLA da oferta DBaaS suportada no Azure, consulte [Alta disponibilidade na Base de Dados Azure para o MySQL](../../../mysql/concepts-high-availability.md) e Alta disponibilidade para [Azure SQL Database](../../../azure-sql/database/high-availability-sla.md)

Para outras implementações de DBMS para base de dados CMS consulte [guias de implantação DBMS para a carga de trabalho SAP,](dbms_guide_general.md)que fornece informações sobre diferentes implementações de DBMS e a sua abordagem para alcançar uma elevada disponibilidade.

#### <a name="high-availability-for-file-repository-server"></a>Alta disponibilidade para servidor de repositório de ficheiros

O Servidor de Repositório de Ficheiros (FRS) refere-se aos diretórios de discos onde conteúdos como relatórios, universos e ligações são armazenados. Está a ser partilhado em todos os servidores de aplicações desse sistema. Por isso, tens de ter a certeza que está altamente disponível.

No Azure, pode escolher [ficheiros Azure Premium](../../../storage/files/storage-files-introduction.md) ou [Ficheiros Azure NetApp](../../../azure-netapp-files/azure-netapp-files-introduction.md) para partilha de ficheiros que são projetados para serem de natureza altamente disponível e altamente durável. Para mais informações, consulte a secção [de redundância](../../../storage/files/storage-files-planning.md#redundancy) para ficheiros Azure.

> [!NOTE]
> O Protocolo SMB para Ficheiros Azure está geralmente disponível, mas o suporte do Protocolo NFS para Ficheiros Azure está atualmente em pré-visualização. Para mais informações, consulte [o suporte NFS 4.1 para Ficheiros Azure está agora em pré-visualização](https://azure.microsoft.com/en-us/blog/nfs-41-support-for-azure-files-is-now-in-preview/)

Como este serviço de partilha de ficheiros não está disponível em toda a região, certifique-se de que se refere aos [Produtos disponíveis pelo](https://azure.microsoft.com/en-us/global-infrastructure/services/) site da região para obter informações atualizadas. Se o serviço não estiver disponível na sua região, pode criar o servidor NFS a partir do qual pode partilhar o sistema de ficheiros com a aplicação SAP BOBI. Mas também terá de considerar a sua elevada disponibilidade.

#### <a name="high-availability-for-load-balancer"></a>Alta disponibilidade para equilibrador de carga

Para distribuir tráfego através do servidor web, pode utilizar o Azure Load Balancer ou o Azure Application Gateway. A redundância para qualquer um dos equilibradores de carga pode ser alcançada com base no SKU que escolher para implantação.

- Para o Azure Load Balancer, a redundância pode ser conseguida configurando o frontend standard load balancer como redundante de zona. Para mais informações, consulte [o Balancer de Carga Padrão e as Zonas de Disponibilidade](../../../load-balancer/load-balancer-standard-availability-zones.md)
- Para o Gateway de Aplicações, a alta disponibilidade pode ser alcançada com base no tipo de nível selecionado durante a implementação.
  - v1 SKU suporta cenários de alta disponibilidade quando você implementou duas ou mais instâncias. O Azure distribui estas instâncias por domínios de atualização e falhas para garantir que as instâncias não falham todas ao mesmo tempo. Assim, com este SKU, a redundância pode ser alcançada dentro da zona
  - v2 SKU garante automaticamente que novas instâncias são espalhadas por domínios de falhas e domínios de atualização. Se optar pela redundância da zona, as instâncias mais recentes também estão espalhadas por zonas de disponibilidade para oferecer resiliência de falhas zonais. Para mais detalhes, consulte [Autoscaling e Zone-redundante Application Gateway v2](../../../application-gateway/application-gateway-autoscaling-zone-redundant.md)

#### <a name="reference-high-availability-architecture-for-sap-businessobjects-bi-platform"></a>Arquitetura de alta disponibilidade de referência para plataforma BI SAP BusinessObjects

Abaixo a arquitetura de referência descreve a configuração da Plataforma SAP BOBI usando o conjunto de disponibilidade, que fornece redundância e disponibilidade de VMs dentro da zona. A arquitetura mostra o uso de diferentes Serviços Azure como Azure Application Gateway, Azure NetApp Files e Azure Database for MySQL para a Plataforma SAP BOBI que oferece redundância incorporada, o que reduz a complexidade de gerir diferentes soluções de alta disponibilidade.

Abaixo, o tráfego de entrada (HTTPS - TCP/443) é equilibrado em carga utilizando O Gateway de Aplicação Azure v1 SKU, que está altamente disponível quando implantado em duas ou mais instâncias. Várias instâncias de servidores web, servidores de gestão e servidores de processamento são implementados em máquinas virtuais separadas para obter redundância e cada nível é implementado em conjuntos de disponibilidade separados. O Azure NetApp Files tem redundância incorporada dentro do data center, pelo que os volumes ANF para o Servidor de Repositório de Ficheiros estarão altamente disponíveis. A Base de Dados CMS é abastada na Base de Dados Azure para o MySQL (DBaaS) que tem uma elevada disponibilidade inerente. Para mais informações, consulte [Alta disponibilidade na Base de Dados Azure para o guia MySQL.](../../../mysql/concepts-high-availability.md)

![SAP BusinessObjects Plataforma BI Redundância usando Conjuntos de Disponibilidade](media/businessobjects-deployment-guide/businessobjects-deployment-high-availability.png)

A arquitetura acima fornece uma visão de como a implantação do SAP BOBI em Azure pode ser feita. Mas não abrange todas as opções possíveis de configuração para a Plataforma SAP BOBI no Azure. O cliente pode adaptar a sua implementação com base na sua exigência de negócio, escolhendo diferentes produtos/serviços para diferentes componentes como Balancer de Carga, Servidor de Repositório de Ficheiros e DBMS.

Em várias Regiões do Azure, são oferecidas Zonas de Disponibilidade, o que significa que dispõe de fonte de energia, arrefecimento e rede independentes. Permite ao cliente implementar a aplicação em duas ou três zonas de disponibilidade. Para o cliente que pretenda alcançar uma elevada disponibilidade através de AZs pode implantar a Plataforma SAP BOBI através de zonas de disponibilidade, certificando-se de que cada componente da aplicação é redundante.

### <a name="disaster-recovery"></a>Recuperação após desastre

A instrução nesta secção explica a estratégia de fornecer proteção de recuperação de desastres para a Plataforma SAP BOBI. Complementa o documento ["Recuperação de Desastres" para](../../../site-recovery/site-recovery-sap.md) a SAP, que representa os recursos primários para a abordagem global de recuperação de desastres sap.

#### <a name="reference-disaster-recovery-architecture-for-sap-businessobjects-bi-platform"></a>Arquitetura de recuperação de desastres de referência para a plataforma BI SAP BusinessObjects

Esta arquitetura de referência está executando a implementação em várias instâncias da Plataforma SAP BOBI com servidores de aplicações redundantes. Para a recuperação de desastres, deve falhar em todos os níveis para uma região secundária. Cada nível usa uma estratégia diferente para fornecer proteção de recuperação de desastres.

![SAP BusinessObjects BI Platform Recuperação de Desastres](media/businessobjects-deployment-guide/businessobjects-deployment-disaster-recovery.png)

#### <a name="load-balancer"></a>Balanceador de carga

O Balancer de Carga é utilizado para distribuir tráfego através de Servidores de Aplicações Web da Plataforma SAP BOBI. Para obter DR para Azure Application Gateway, implementar a configuração paralela do gateway de aplicações na região secundária.

#### <a name="virtual-machines-running-web-and-bi-application-servers"></a>Máquinas virtuais que executam servidores de aplicações web e BI

O serviço de recuperação do site Azure pode ser usado para replicar máquinas virtuais que executam servidores de aplicações Web e BI na região secundária. Replica os servidores da região secundária para que quando ocorrem desastres e interrupções, possa facilmente falhar no seu ambiente replicado e continuar a trabalhar

#### <a name="file-repository-servers"></a>Servidores de repositório de ficheiros

- **O Azure NetApp Files** fornece volumes NFS e SMB, pelo que qualquer ferramenta de cópia baseada em ficheiros pode ser usada para replicar dados entre regiões do Azure. Para obter mais informações sobre como copiar o volume anf em outra região, consulte [faQs Sobre ficheiros Azure NetApp](../../../azure-netapp-files/azure-netapp-files-faqs.md#how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region)

  Pode utilizar a replicação da região de ficheiros Azure NetApp, que está atualmente em [pré-visualização](https://azure.microsoft.com/en-us/blog/azure-netapp-files-cross-region-replication-and-new-enhancements-in-preview/) que utiliza a tecnologia ® NetApp SnapMirror. Assim, apenas os blocos alterados são enviados pela rede num formato comprimido e eficiente. Esta tecnologia proprietária minimiza a quantidade de dados necessários para se replicar em todas as regiões, o que poupa custos de transferência de dados. Também encurta o tempo de replicação para que possa alcançar um objetivo de ponto de restauro menor (RPO). Consulte [os Requisitos e considerações para a utilização da replicação entre regiões](../../../azure-netapp-files/cross-region-replication-requirements-considerations.md) para obter mais informações.

- **Os ficheiros premium Azure** só suportam armazenamento redundante localmente (LRS) e zona de armazenamento redundante (ZRS). Para a estratégia Azure Premium Files DR, pode utilizar [a AzCopy](../../../storage/common/storage-use-azcopy-v10.md) ou [a Azure PowerShell](/powershell/module/az.storage/) para copiar os seus ficheiros para outra conta de armazenamento numa região diferente. Para obter mais informações, consulte [a recuperação de desastres e a falha da conta de armazenamento](../../../storage/common/storage-disaster-recovery-guidance.md)

#### <a name="cms-database"></a>Base de dados CMS

A Azure Database for MySQL fornece várias opções para recuperar a base de dados em caso de desastre. Escolha a opção apropriada que funcione para o seu negócio.

- Permitir réplicas de leitura transversal para melhorar a continuidade do seu negócio e o planeamento de recuperação de desastres. Pode replicar-se do servidor de origem até cinco réplicas. As réplicas de leitura são atualizadas assíncronea usando a tecnologia binária de replicação de registos do MySQL. As réplicas são novos servidores que geres similares ao Azure Database regular para servidores MySQL. Saiba mais sobre réplicas lidas, regiões disponíveis, restrições e como falhar a partir do artigo de [conceitos de réplicas lidas.](../../../mysql/concepts-read-replicas.md)

- Utilize a Base de Dados Azure para a funcionalidade de geo-restauro do MySQL que restaura o servidor utilizando cópias de segurança geo-redundantes. Estas cópias de segurança são acessíveis mesmo quando a região em que o seu servidor está hospedado está offline. Pode restaurar destas cópias de segurança para qualquer outra região e voltar a colocar o seu servidor online.

  > [!NOTE]
  > O geo-restauro só é possível se forte o servidor com armazenamento de backup geo-redundante. A alteração das **opções de redundância de backup** após a criação do servidor não é suportada. Para mais informações, consulte o artigo [de Backup Despedimento.](../../../mysql/concepts-backup.md#backup-redundancy-options)

Segue-se a recomendação para a recuperação de catástrofes de cada nível utilizado neste exemplo.

| Tiers de plataforma SAP BOBI   | Recomendação                                                                                           |
|---------------------------|----------------------------------------------------------------------------------------------------------|
| Gateway de Aplicação do Azure | Configuração paralela do Gateway de Aplicação na Região Secundária                                                |
| Servidores de aplicações web   | Replicar usando a recuperação do site                                                                         |
| Servidores de aplicações BI    | Replicar usando a recuperação do site                                                                         |
| Azure NetApp Files        | Ferramenta de cópia baseada em ficheiros para replicar dados para região secundária **ou** replicação da região transversal ANF (pré-visualização) |
| Base de Dados do Azure para MySQL  | Cruze a região leia réplicas **ou** restaure o backup de backups geo-redundantes.                             |

## <a name="next-steps"></a>Passos seguintes

- [Configurar a recuperação de desastres para uma implementação de aplicativos SAP de vários níveis](../../../site-recovery/site-recovery-sap.md)
- [Azure Virtual Machines planejamento e implementação para SAP](planning-guide.md)
- [Implantação de máquinas virtuais Azure para SAP](deployment-guide.md)
- [Implantação DBMS de máquinas virtuais Azure para SAP](./dbms_guide_general.md)
