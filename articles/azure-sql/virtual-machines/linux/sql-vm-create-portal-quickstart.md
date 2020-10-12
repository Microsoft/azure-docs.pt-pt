---
title: 'Quickstart: Criar um VM do Servidor SQL Linux em Azure'
description: Este tutorial mostra como criar uma máquina virtual do SQL Server 2017 do Linux no portal do Azure.
services: virtual-machines-linux
author: MashaMSFT
ms.date: 10/22/2019
tags: azure-service-management
ms.topic: quickstart
ms.service: virtual-machines-sql
ms.workload: iaas-sql-server
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: e1a9d2722987464b1bb3c8b1489a2d1258a41d15
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91273097"
---
# <a name="provision-a-linux-virtual-machine-running-sql-server-in-the-azure-portal"></a>Fornecimento de uma máquina virtual Linux que executa o SQL Server no portal Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Linux](sql-vm-create-portal-quickstart.md)
> * [Windows](../windows/sql-vm-create-portal-quickstart.md)

Neste tutorial de arranque rápido, utiliza-se o portal Azure para criar uma máquina virtual Linux com SQL Server 2017 instalada. Aprende-se o seguinte: 


* [Criar um Linux VM executando SQL Server a partir da galeria](#create)
* [Ligar à VM nova com ssh](#connect)
* [Alterar a palavra-passe SA](#password)
* [Configuração para ligações remotas](#remote)

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

## <a name="create-a-linux-vm-with-sql-server-installed"></a><a id="create"></a>Criar uma VM do Linux com o SQL Server instalado

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. No painel esquerdo, selecione **Criar um recurso**.

1. No painel **de recursos Criar,** selecione **Compute**.

1. Selecione **Veja tudo** ao lado do título **em destaque.**

   ![Ver todas as imagens de VMs](./media/sql-vm-create-portal-quickstart/azure-compute-blade.png)

1. Na caixa de pesquisa, digite **SQL Server 2019**e selecione **Enter** para iniciar a pesquisa.

1. Limitar os resultados da pesquisa selecionando **o sistema operativo**  >  **Redhat**.

    ![Filtro de pesquisa para imagens VM do SQL Server 2019](./media/sql-vm-create-portal-quickstart/searchfilter.png)

1. Selecione uma imagem do SQL Server 2019 Linux a partir dos resultados da pesquisa. Este tutorial utiliza **o SQL Server 2019 no RHEL74**.

   > [!TIP]
   > A edição developer permite-lhe testar ou desenvolver com as funcionalidades da edição Enterprise, mas sem custos de licenciamento do SQL Server. Só paga pelo custo de execução da VM do Linux.

1. Selecione **Criar**. 


### <a name="set-up-your-linux-vm"></a>Configurar o seu Linux VM

1. No **separador Básicos,** selecione o seu Grupo **de Subscrição** e **Recursos.** 

    ![Janela Informações básicas](./media/sql-vm-create-portal-quickstart/basics.png)

1. Em **nome da máquina Virtual,** insira um nome para o seu novo Linux VM.
1. Em seguida, digite ou selecione os seguintes valores:
   * **Região**: Selecione a região de Azure que é a certa para si.
   * **Opções de disponibilidade**: Escolha a opção de disponibilidade e redundância que é melhor para as suas apps e dados.
   * **Tamanho de alteração**: Selecione esta opção para escolher o tamanho da máquina e quando terminar, escolha **Selecione**. Para obter mais informações sobre os tamanhos das máquinas VM, consulte os [tamanhos de VM](../../../virtual-machines/sizes.md).

     ![Selecionar um tamanho de VM](./media/sql-vm-create-portal-quickstart/vmsizes.png)

   > [!TIP]
   > Para o desenvolvimento e testes funcionais, utilize um tamanho VM de **DS2** ou superior. Para testes de desempenho, utilize **DS13** ou superior.

   * **Tipo de autenticação**: Selecione **sSH chave pública**.

     > [!Note]
     > Tem a opção de utilizar uma chave pública SSH ou uma Palavra-passe para autenticação. O SSH é mais seguro. Para obter instruções sobre como gerar uma chave SSH, veja [Criar chaves SSH em Linux e Mac para VMs do Linux no Azure](../../../virtual-machines/linux/mac-create-ssh-keys.md).

   * **Nome de utilizador**: Introduza o nome do administrador para o VM.
   * **Chave pública SSH**: Insira a sua chave pública RSA.
   * **Portas de entrada pública**: Escolha **Deixe as portas selecionadas** e escolha a porta **SSH (22)** na lista **de portas de entrada pública Select.** Neste arranque rápido, este passo é necessário para ligar e completar a configuração do SQL Server. Se pretender ligar-se remotamente ao SQL Server, terá de permitir manualmente o tráfego para a porta predefinido (1433) utilizada pelo Microsoft SQL Server para ligações através da Internet após a criação da máquina virtual.

     ![Portas de entrada](./media/sql-vm-create-portal-quickstart/port-settings.png)

1. Faça quaisquer alterações que pretenda para as definições nos seguintes separadores adicionais ou mantenha as definições predefinidos.
    * **Discos**
    * **Redes**
    * **Gestão**
    * **Config convidado**
    * **Etiquetas**

1. Selecione **Rever + criar**.
1. No **painel 'Rever + criar',** selecione **Criar.**

## <a name="connect-to-the-linux-vm"></a><a id="connect"></a>Ligar à VM do Linux

Se já utiliza uma shell de BASH, utilize o comando **ssh** para ligar à VM do Azure. No comando seguinte, substitua o nome de utilizador e o endereço IP da VM para ligar à sua VM do Linux.

```bash
ssh azureadmin@40.55.55.555
```

Pode encontrar o endereço IP da VM no portal do Azure.

![Endereço IP no portal do Azure](./media/sql-vm-create-portal-quickstart/vmproperties.png)

Se estiver a correr no Windows e não tiver uma concha BASH, instale um cliente SSH, como o PuTTY.

1. [Transferir e instalar PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)

1. Execute o PuTTY.

1. No ecrã de configuração do PuTTY, introduza o endereço IP público da VM.

1. Selecione **Abrir** e insira o seu nome de utilizador e palavra-passe nas indicações.

Para obter mais informações sobre como ligar a VMs do Linux, veja [Criar uma VM do Linux no Azure com o Portal](../../../virtual-machines/linux/quick-create-portal.md).

> [!NOTE]
> Se vir um alerta de segurança PuTTY sobre a chave de anfitrião do servidor não estar em cache no registo, escolha entre as seguintes opções. Se confiar neste anfitrião, selecione **Sim** para adicionar a chave à cache do PuTTy e continue a ligar. Se pretender continuar a ligar apenas uma vez, sem adicionar a chave à cache, selecione **Nº**. Se não confia neste anfitrião, **selecione Cancelar** para abandonar a ligação.

## <a name="change-the-sa-password"></a><a id="password"></a>Alterar a palavra-passe SA

A máquina virtual nova instala o SQL Server com uma palavra-passe SA aleatória. Reinicie esta palavra-passe antes de ligar ao SQL Server com o login SA.

1. Depois de ligar à sua VM do Linux, abra um terminal de comando novo.

1. Altere a palavra-passe SA com os comandos seguintes:

   ```bash
   sudo systemctl stop mssql-server
   sudo /opt/mssql/bin/mssql-conf set-sa-password
   ```

   Introduza uma palavra-passe SA nova e a confirmação da palavra-passe quando lhe for pedido.

1. Reinicie o serviço do SQL Server.

   ```bash
   sudo systemctl start mssql-server
   ```

## <a name="add-the-tools-to-your-path-optional"></a>Adicionar as ferramentas ao seu caminho (opcional)

São instalados por predefinição vários [pacotes](sql-server-on-linux-vm-what-is-iaas-overview.md#packages) do SQL Server, incluindo o pacote SQL Server command-line Tools. O pacote das ferramentas contém as ferramentas **sqlcmd** e **bcp**. Para sua comodidade, opcionalmente, pode adicionar o caminho das ferramentas, `/opt/mssql-tools/bin/`, à variável do seu ambiente **PATH**.

1. Execute os comandos seguintes para modificar **PATH** para sessões de início de sessão e sessões interativas/sem início de sessão:

   ```bash
   echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bash_profile
   echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bashrc
   source ~/.bashrc
   ```

## <a name="configure-for-remote-connections"></a><a id="remote"></a> Configurar ligações remotas

Se tiver de ligar remotamente ao SQL Server na VM do Azure, tem de configurar uma regra de entrada no grupo de segurança de rede. A regra permite o tráfego na porta em que o SQL Server escuta (predefinição de 1433). Os passos seguintes mostram como utilizar o portal do Azure para este passo.

> [!TIP]
> Se selecionou a porta de entrada **MS SQL (1433)** nas definições durante o aprovisionamento, estas alterações foram efetuadas para si. Pode avançar para a secção seguinte que explica como configurar a firewall.

1. No portal do Azure, selecione **Máquinas virtuais** e selecione a sua VM do SQL Server.
1. No painel de navegação esquerdo, em **Definições,** selecione **Networking**.
1. Na janela de rede, **selecione Adicionar porta de entrada** de acordo com as **regras do porto de entrada**.

   ![Regras da porta de entrada](./media/sql-vm-create-portal-quickstart/networking.png)

1. Na lista **Serviço**, selecione **MS SQL**.

    ![Regra de grupo de segurança do MS SQL](./media/sql-vm-create-portal-quickstart/sqlnsgrule.png)

1. Clique em **OK** para guardar a regra da VM.

### <a name="open-the-firewall-on-rhel"></a>Abrir a firewall no RHEL

Este tutorial orientou-o na criação de uma VM do Red Hat Enterprise Linux (RHEL). Se quiser ligar remotamente a VMs do RHEL, também tem de abrir a porta 1433 na firewall do Linux.

1. [Ligue](#connect) à sua VM do RHEL.

1. Na shell de BASH, execute os comandos seguintes:

   ```bash
   sudo firewall-cmd --zone=public --add-port=1433/tcp --permanent
   sudo firewall-cmd --reload
   ```

## <a name="next-steps"></a>Passos seguintes

Agora que tem uma máquina virtual do SQL Server 2017 no Azure, pode ligar localmente com **sqlcmd** para executar consultas de Transact-SQL.

Se configurar o Azure VM para ligações remotas do SQL Server, deverá ser capaz de se ligar remotamente. Para obter um exemplo de como ligar remotamente ao SQL Server no Linux a partir do Windows, veja [Use SSMS on Windows to connect to SQL Server on Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-ssms) (Utilizar o SSMS no Windows para ligar ao SQL Server no Linux). Para ligar ao Visual Studio Code, veja [Use Visual Studio Code to create and run Transact-SQL scripts for SQL Server](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-vscode) (Utilizar o Visual Studio Code para criar e executar scripts de Transact-SQL para o SQL Server).

Para obter informações mais gerais sobre o SQL Server no Linux, consulte [a visão geral do SQL Server 2017 no Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview). Para obter mais informações sobre como utilizar máquinas virtuais do Linux do SQL Server 2017, veja [Overview of SQL Server 2017 virtual machines on Azure](sql-server-on-linux-vm-what-is-iaas-overview.md) (Descrição geral das máquinas virtuais do SQL Server 2017 no Azure).
