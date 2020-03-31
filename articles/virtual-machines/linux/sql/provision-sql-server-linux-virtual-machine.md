---
title: Criar uma VM do SQL Server 2017 do Linux no Azure | Microsoft Docs
description: Este tutorial mostra como criar uma máquina virtual do SQL Server 2017 do Linux no portal do Azure.
services: virtual-machines-linux
author: MashaMSFT
manager: craigg
ms.date: 10/22/2019
tags: azure-service-management
ms.topic: conceptual
ms.service: virtual-machines-sql
ms.workload: iaas-sql-server
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 43ba4eed4dcfd6d8e86c21f1ee5214108c44a8c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060237"
---
# <a name="provision-a-linux-sql-server-virtual-machine-in-the-azure-portal"></a>Aprovisionar uma máquina virtual do SQL Server do Linux no portal do Azure

> [!div class="op_single_selector"]
> * [Linux](provision-sql-server-linux-virtual-machine.md)
> * [Windows](../../windows/sql/virtual-machines-windows-portal-sql-server-provision.md)

Neste tutorial de arranque rápido, utiliza-se o portal Azure para criar uma máquina virtual Linux com o SQL Server 2017 instalado.

Neste tutorial, ficará a saber como:

* [Criar uma VM do SQL do Linux a partir da galeria](#create)
* [Ligar à VM nova com ssh](#connect)
* [Alterar a senha da SA](#password)
* [Configurar para ligações remotas](#remote)

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

## <a name="create-a-linux-vm-with-sql-server-installed"></a><a id="create"></a>Criar uma VM do Linux com o SQL Server instalado

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).

1. No painel esquerdo, selecione **Criar um recurso**.

1. No **Painel Criar um painel** de recursos, selecione **Compute**.

1. Selecione **Ver tudo** ao lado da rubrica **Em destaque.**

   ![Ver todas as imagens de VMs](./media/provision-sql-server-linux-virtual-machine/azure-compute-blade.png)

1. Na caixa de pesquisa, digite **o SQL Server 2019**e selecione **Entrar** para iniciar a pesquisa.

1. Limite os resultados de pesquisa selecionando o **sistema** > operativo**Redhat**.

    ![Filtro de pesquisa para imagens VM SQL Server 2019](./media/provision-sql-server-linux-virtual-machine/searchfilter.png)

1. Selecione uma imagem Linux SQL Server 2019 a partir dos resultados da pesquisa. Este tutorial utiliza **o SQL Server 2019 no RHEL74**.

   > [!TIP]
   > A edição do Developer permite testar ou desenvolver com as funcionalidades da edição da Enterprise, mas sem custos de licenciamento do SQL Server. Só paga pelo custo de execução da VM do Linux.

1. Selecione **Criar**. 


### <a name="set-up-your-linux-vm"></a>Instale o seu Linux VM

1. No separador **Basics,** selecione o seu Grupo **de Subscrição** e **Recursos**. 

    ![Janela Informações básicas](./media/provision-sql-server-linux-virtual-machine/basics.png)

1. Em nome de **máquina virtual,** introduza um nome para o seu novo Linux VM.
1. Em seguida, digite ou selecione os seguintes valores:
   * **Região**: Selecione a região azure que é a certa para si.
   * **Opções de disponibilidade**: Escolha a opção de disponibilidade e redundância que é melhor para as suas apps e dados.
   * **Alterar o tamanho**: Selecione esta opção para escolher o tamanho da máquina e quando estiver feito, escolha **Selecionar**. Para obter mais informações sobre os tamanhos das VMs, veja [Linux VM size](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-sizes) (Tamanhos de VMs do Linux).

     ![Selecionar um tamanho de VM](./media/provision-sql-server-linux-virtual-machine/vmsizes.png)

   > [!TIP]
   > Para o desenvolvimento e testes funcionais, utilize um tamanho VM de **DS2** ou superior. Para testes de desempenho, utilize **DS13** ou superior.

   * **Tipo de autenticação**: Selecione **a tecla pública SSH**.

     > [!Note]
     > Tem a opção de utilizar uma chave pública SSH ou uma Palavra-passe para autenticação. O SSH é mais seguro. Para obter instruções sobre como gerar uma chave SSH, veja [Criar chaves SSH em Linux e Mac para VMs do Linux no Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-mac-create-ssh-keys).

   * **Nome de utilizador**: Introduza o nome do Administrador para o VM.
   * **Chave pública SSH**: Introduza a sua chave pública RSA.
   * **Portas**de entrada pública : Escolha **permitir portas selecionadas** e escolha a porta **SSH (22)** na lista de portas de **entrada pública Select.** Neste arranque rápido, este passo é necessário para ligar e completar a configuração do Servidor SQL. Se pretender ligar-se remotamente ao SQL Server, terá de permitir manualmente o tráfego à porta predefinida (1433) utilizada pelo Microsoft SQL Server para ligações através da Internet após a criação da máquina virtual.

     ![Portas de entrada](./media/provision-sql-server-linux-virtual-machine/port-settings.png)

1. Faça quaisquer alterações que pretenda para as definições nos seguintes separadores adicionais ou mantenha as definições predefinidas.
    * **Discos**
    * **Redes**
    * **Gestão**
    * **Config convidado**
    * **Etiquetas**

1. Selecione **Rever + criar**.
1. Na **Análise + criar** painel, selecione **Criar**.

## <a name="connect-to-the-linux-vm"></a><a id="connect"></a>Ligar à VM do Linux

Se já utiliza uma shell de BASH, utilize o comando **ssh** para ligar à VM do Azure. No comando seguinte, substitua o nome de utilizador e o endereço IP da VM para ligar à sua VM do Linux.

```bash
ssh azureadmin@40.55.55.555
```

Pode encontrar o endereço IP da VM no portal do Azure.

![Endereço IP no portal do Azure](./media/provision-sql-server-linux-virtual-machine/vmproperties.png)

Se estiver a correr no Windows e não tiver uma concha BASH, instale um cliente SSH, como o PuTTY.

1. [Transferir e instalar PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)

1. Execute o PuTTY.

1. No ecrã de configuração do PuTTY, introduza o endereço IP público da VM.

1. Selecione **Abrir** e introduza o seu nome de utilizador e palavra-passe nas instruções.

Para obter mais informações sobre como ligar a VMs do Linux, veja [Criar uma VM do Linux no Azure com o Portal](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-quick-create-portal).

> [!Note]
> Se vir um alerta de segurança PuTTY sobre a chave hospedeira do servidor não estar em cache no registo, escolha entre as seguintes opções. Se confia neste anfitrião, selecione **Sim** para adicionar a chave à cache de PuTTy e continuar a ligar. Se quiser continuar a ligar apenas uma vez, sem adicionar a chave à cache, selecione **No**. Se não confia neste hospedeiro, selecione **Cancelar** para abandonar a ligação.

## <a name="change-the-sa-password"></a><a id="password"></a>Alterar a palavra-passe SA

A máquina virtual nova instala o SQL Server com uma palavra-passe SA aleatória. Reinicie esta palavra-passe antes de se ligar ao SQL Server com o login SA.

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

São instalados por predefinição vários [pacotes](sql-server-linux-virtual-machines-overview.md#packages) do SQL Server, incluindo o pacote SQL Server command-line Tools. O pacote das ferramentas contém as ferramentas **sqlcmd** e **bcp**. Para sua comodidade, opcionalmente, pode adicionar o caminho das ferramentas, `/opt/mssql-tools/bin/`, à variável do seu ambiente **PATH**.

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
1. No painel de navegação à esquerda, em **Definições,** **selecione Networking**.
1. Na janela de rede, **selecione Adicionar porta de entrada** de acordo com as regras da porta de **entrada**.

   ![Regras da porta de entrada](./media/provision-sql-server-linux-virtual-machine/networking.png)

1. Na lista **Serviço**, selecione **MS SQL**.

    ![Regra de grupo de segurança do MS SQL](./media/provision-sql-server-linux-virtual-machine/sqlnsgrule.png)

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

Se configurar o Azure VM para ligações remotas do Servidor SQL, deverá conseguir ligar-se remotamente. Para obter um exemplo de como ligar remotamente ao SQL Server no Linux a partir do Windows, veja [Use SSMS on Windows to connect to SQL Server on Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-ssms) (Utilizar o SSMS no Windows para ligar ao SQL Server no Linux). Para ligar ao Visual Studio Code, veja [Use Visual Studio Code to create and run Transact-SQL scripts for SQL Server](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-vscode) (Utilizar o Visual Studio Code para criar e executar scripts de Transact-SQL para o SQL Server).

Para obter informações mais gerais sobre o SQL Server no Linux, consulte a [visão geral do SQL Server 2017 no Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview). Para obter mais informações sobre como utilizar máquinas virtuais do Linux do SQL Server 2017, veja [Overview of SQL Server 2017 virtual machines on Azure](sql-server-linux-virtual-machines-overview.md) (Descrição geral das máquinas virtuais do SQL Server 2017 no Azure).
