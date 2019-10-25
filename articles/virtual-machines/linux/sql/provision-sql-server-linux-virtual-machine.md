---
title: Criar uma VM do SQL Server 2017 do Linux no Azure | Microsoft Docs
description: Este tutorial mostra como criar uma máquina virtual do SQL Server 2017 do Linux no portal do Azure.
services: virtual-machines-linux
author: MashaMSFT
manager: craigg
ms.date: 10/22/2019
ms.topic: conceptual
tags: azure-service-management
ms.service: virtual-machines-sql
ms.workload: iaas-sql-server
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 85d2396a05e7496b56bd83bd834150aa6d864c62
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72882704"
---
# <a name="provision-a-linux-sql-server-virtual-machine-in-the-azure-portal"></a>Aprovisionar uma máquina virtual do SQL Server do Linux no portal do Azure

> [!div class="op_single_selector"]
> * [Linux](provision-sql-server-linux-virtual-machine.md)
> * [Windows](../../windows/sql/virtual-machines-windows-portal-sql-server-provision.md)

Neste tutorial de início rápido, você usa o portal do Azure para criar uma máquina virtual Linux com o SQL Server 2017 instalado.

Neste tutorial, ficará a saber como:

* [Criar uma VM do SQL do Linux a partir da galeria](#create)
* [Ligar à VM nova com ssh](#connect)
* [Alterar a palavra-passe SA](#password)
* [Configurar ligações remotas](#remote)

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

## <a id="create"></a>Criar uma VM do Linux com o SQL Server instalado

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. No painel esquerdo, selecione **criar um recurso**.

1. No painel **criar um recurso** , selecione **computação**.

1. Selecione **Ver todos** ao lado do título em **destaque** .

   ![Ver todas as imagens de VMs](./media/provision-sql-server-linux-virtual-machine/azure-compute-blade.png)

1. Na caixa de pesquisa, digite **SQL Server 2019**e selecione **Enter** para iniciar a pesquisa.

1. Limite os resultados da pesquisa selecionando **sistema operacional** > **redhat**.

    ![Filtro de pesquisa para imagens de VM SQL Server 2019](./media/provision-sql-server-linux-virtual-machine/searchfilter.png)

1. Selecione uma imagem SQL Server 2019 do Linux nos resultados da pesquisa. Este tutorial usa **SQL Server 2019 no RHEL74**.

   > [!TIP]
   > A edição Developer permite que você teste ou desenvolva com os recursos do Enterprise Edition, mas sem custos de licenciamento SQL Server. Só paga pelo custo de execução da VM do Linux.

1. Selecione **Criar**. 


### <a name="set-up-your-linux-vm"></a>Configurar sua VM Linux

1. Na guia **noções básicas** , selecione sua **assinatura** e **grupo de recursos**. 

    ![Janela Informações básicas](./media/provision-sql-server-linux-virtual-machine/basics.png)

1. Em **nome da máquina virtual**, insira um nome para a nova VM do Linux.
1. Em seguida, digite ou selecione os seguintes valores:
   * **Região**: selecione a região do Azure ideal para você.
   * **Opções de disponibilidade**: escolha a opção de disponibilidade e redundância mais adequada para seus aplicativos e dados.
   * **Alterar tamanho**: Selecione esta opção para escolher um tamanho de máquina e, quando terminar, escolha **selecionar**. Para obter mais informações sobre os tamanhos das VMs, veja [Linux VM size](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-sizes) (Tamanhos de VMs do Linux).

     ![Selecionar um tamanho de VM](./media/provision-sql-server-linux-virtual-machine/vmsizes.png)

   > [!TIP]
   > Para desenvolvimento e teste funcional, use um tamanho de VM de **DS2** ou superior. Para testes de desempenho, utilize **DS13** ou superior.

   * **Tipo de autenticação**: selecione **chave pública SSH**.

     > [!Note]
     > Tem a opção de utilizar uma chave pública SSH ou uma Palavra-passe para autenticação. O SSH é mais seguro. Para obter instruções sobre como gerar uma chave SSH, veja [Criar chaves SSH em Linux e Mac para VMs do Linux no Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-mac-create-ssh-keys).

   * **Nome de usuário**: Insira o nome do administrador para a VM.
   * **Chave pública SSH**: Insira sua chave pública RSA.
   * **Portas de entrada públicas**: escolha **permitir portas selecionadas** e selecione a porta **SSH (22)** na lista **selecionar portas de entrada públicas** . Neste guia de início rápido, essa etapa é necessária para conectar e concluir a configuração de SQL Server. Se você quiser se conectar remotamente ao SQL Server, será necessário permitir manualmente o tráfego para a porta padrão (1433) usada pelo Microsoft SQL Server para conexões pela Internet após a criação da máquina virtual.

     ![Portas de entrada](./media/provision-sql-server-linux-virtual-machine/port-settings.png)

1. Faça as alterações desejadas nas configurações nas guias adicionais a seguir ou mantenha as configurações padrão.
    * **Discos**
    * **Redes**
    * **Gerenciamento**
    * **Configuração de convidado**
    * **Etiquetas**

1. Selecione **Rever + criar**.
1. No painel **revisar + criar** , selecione **criar**.

## <a id="connect"></a>Ligar à VM do Linux

Se já utiliza uma shell de BASH, utilize o comando **ssh** para ligar à VM do Azure. No comando seguinte, substitua o nome de utilizador e o endereço IP da VM para ligar à sua VM do Linux.

```bash
ssh azureadmin@40.55.55.555
```

Pode encontrar o endereço IP da VM no portal do Azure.

![Endereço IP no portal do Azure](./media/provision-sql-server-linux-virtual-machine/vmproperties.png)

Se você estiver executando o no Windows e não tiver um shell BASH, instale um cliente SSH, como a reproduzida.

1. [Transferir e instalar PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)

1. Execute o PuTTY.

1. No ecrã de configuração do PuTTY, introduza o endereço IP público da VM.

1. Selecione **abrir** e insira seu nome de usuário e senha nos prompts.

Para obter mais informações sobre como ligar a VMs do Linux, veja [Criar uma VM do Linux no Azure com o Portal](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-quick-create-portal).

> [!Note]
> Se você vir um alerta de segurança de saída sobre a chave de host do servidor que não está sendo armazenada em cache no registro, escolha uma das opções a seguir. Se você confiar nesse host, selecione **Sim** para adicionar a chave ao cache de retratação e continuar conectando. Se você quiser realizar a conexão apenas uma vez, sem adicionar a chave ao cache, selecione **não**. Se você não confiar nesse host, selecione **Cancelar** para abandonar a conexão.

## <a id="password"></a>Alterar a palavra-passe SA

A máquina virtual nova instala o SQL Server com uma palavra-passe SA aleatória. Redefina essa senha antes de se conectar a SQL Server com o logon SA.

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

## <a id="remote"></a> Configurar ligações remotas

Se tiver de ligar remotamente ao SQL Server na VM do Azure, tem de configurar uma regra de entrada no grupo de segurança de rede. A regra permite o tráfego na porta em que o SQL Server escuta (predefinição de 1433). Os passos seguintes mostram como utilizar o portal do Azure para este passo.

> [!TIP]
> Se selecionou a porta de entrada **MS SQL (1433)** nas definições durante o aprovisionamento, estas alterações foram efetuadas para si. Pode avançar para a secção seguinte que explica como configurar a firewall.

1. No portal do Azure, selecione **Máquinas virtuais** e selecione a sua VM do SQL Server.
1. No painel de navegação à esquerda, em **configurações**, selecione **rede**.
1. Na janela rede, selecione **Adicionar porta de entrada** em **regras de porta de entrada**.

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

Se você configurou a VM do Azure para conexões de SQL Server remotas, você deve ser capaz de se conectar remotamente. Para obter um exemplo de como ligar remotamente ao SQL Server no Linux a partir do Windows, veja [Use SSMS on Windows to connect to SQL Server on Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-ssms) (Utilizar o SSMS no Windows para ligar ao SQL Server no Linux). Para ligar ao Visual Studio Code, veja [Use Visual Studio Code to create and run Transact-SQL scripts for SQL Server](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-vscode) (Utilizar o Visual Studio Code para criar e executar scripts de Transact-SQL para o SQL Server).

Para obter mais informações gerais sobre SQL Server em Linux, consulte [visão geral do SQL Server 2017 no Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview). Para obter mais informações sobre como utilizar máquinas virtuais do Linux do SQL Server 2017, veja [Overview of SQL Server 2017 virtual machines on Azure](sql-server-linux-virtual-machines-overview.md) (Descrição geral das máquinas virtuais do SQL Server 2017 no Azure).
