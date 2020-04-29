---
title: Configure P2S - Instância gerida
description: Ligue-se a uma instância gerida pela Base de Dados Azure SQL utilizando o Estúdio de Gestão de Servidores SQL utilizando uma ligação ponto-a-local a partir de um computador cliente no local.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab, bonova, jovanpop
ms.date: 03/13/2019
ms.openlocfilehash: 30b2ba92174996ea2bae34e7553a3258d8ebee27
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79268889"
---
# <a name="quickstart-configure-a-point-to-site-connection-to-an-azure-sql-database-managed-instance-from-on-premises"></a>Quickstart: Configure uma ligação ponto-a-local a uma base de dados Azure SQL Gerida a partir do local

Este quickstart demonstra como se conectar a uma instância gerida pela Base de Dados Azure SQL utilizando o [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) a partir de um computador cliente no local através de uma ligação ponto-a-local. Para obter informações sobre ligações ponto-a-site, consulte sobre o [Ponto-a-Local VPN](../vpn-gateway/point-to-site-about.md)

## <a name="prerequisites"></a>Pré-requisitos

Este guia de início rápido:

- Utiliza os recursos criados [Create a Managed Instance](sql-database-managed-instance-get-started.md) como ponto de partida.
- Requer PowerShell 5.1 e AZ PowerShell 1.4.0 ou mais tarde no seu computador cliente no local. Se necessário, consulte as instruções para [a instalação do módulo PowerShell Azure](https://docs.microsoft.com/powershell/azure/install-az-ps#install-the-azure-powershell-module).
- Requer a versão mais recente do [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) no seu computador cliente no local.

## <a name="attach-a-vpn-gateway-to-your-managed-instance-virtual-network"></a>Fixe uma porta de entrada VPN à sua rede virtual De instância gerida

1. Abra a PowerShell no computador cliente no local.

2. Copie este guião PowerShell. Este script anexa um Gateway VPN à rede virtual Managed Instance que criou no quickstart [Create a Managed Instance.](sql-database-managed-instance-get-started.md) Este script utiliza o Módulo Az Do PowerShell Azure e fará o seguinte para os anfitriões baseados no Windows ou no Linux:

   - Cria e instala certificados na máquina cliente
   - Calcula a futura gama IP da sub-rede VPN Gateway
   - Cria a GatewaySubnet
   - Implementa o modelo de Gestor de Recursos Azure que liga o Gateway VPN à subnet VPN

     ```powershell
     $scriptUrlBase = 'https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/manage/azure-sql-db-managed-instance/attach-vpn-gateway'

     $parameters = @{
       subscriptionId = '<subscriptionId>'
       resourceGroupName = '<resourceGroupName>'
       virtualNetworkName = '<virtualNetworkName>'
       certificateNamePrefix  = '<certificateNamePrefix>'
       }

     Invoke-Command -ScriptBlock ([Scriptblock]::Create((iwr ($scriptUrlBase+'/attachVPNGateway.ps1?t='+ [DateTime]::Now.Ticks)).Content)) -ArgumentList $parameters, $scriptUrlBase
     ```

3. Repasse o script na janela PowerShell e forneça os parâmetros necessários. Os valores para, `<subscriptionId>` `<resourceGroup>`e `<virtualNetworkName>` devem coincidir com os que usou para o arranque rápido da Create [Managed Instance.](sql-database-managed-instance-get-started.md) O valor `<certificateNamePrefix>` para pode ser uma sequência da sua escolha.

4. Execute o script PowerShell.

> [!IMPORTANT]
> Não continue até que o script PowerShell esteja concluído.

## <a name="create-a-vpn-connection-to-your-managed-instance"></a>Crie uma ligação VPN à sua Instância Gerida

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Abra o grupo de recursos no qual criou o portal de rede virtual e, em seguida, abra o recurso de gateway da rede virtual.
3. Selecione **a configuração ponto-a-site** e, em seguida, selecione **transferir o cliente VPN**.

    ![Baixar cliente VPN](./media/sql-database-managed-instance-configure-p2s/download-vpn-client.png)  
4. No computador cliente no local, extrai os ficheiros do ficheiro zip e, em seguida, abra a pasta com os ficheiros extraídos.
5. Abra a pasta '**WindowsAmd64** e abra o ficheiro **VpnClientSetupAmd6.exe.**
6. Se receber uma mensagem **do Windows protegida,** clique em **Mais informações** e, em seguida, clique em **Executar de qualquer forma**.

    ![Instalar cliente VPN](./media/sql-database-managed-instance-configure-p2s/vpn-client-defender.png)\
7. Na caixa de diálogo Controlo de Conta de Utilizador, clique em **Sim** para continuar.
8. Na caixa de diálogo que refere a sua rede virtual, selecione **Sim** para instalar o Cliente VPN para a sua rede virtual.

## <a name="connect-to-the-vpn-connection"></a>Ligue-se à ligação VPN

1. Vá à **VPN** em **Rede & Internet** no seu computador cliente no local e selecione a sua rede virtual Managed Instance para estabelecer uma ligação a este VNet. Na imagem seguinte, o VNet chama-se **MyNewVNet**.

    ![Ligação VPN](./media/sql-database-managed-instance-configure-p2s/vpn-connection.png)  
2. Selecione **Ligar**.
3. Na caixa de diálogo, selecione **Ligar**.

    ![Ligação VPN](./media/sql-database-managed-instance-configure-p2s/vpn-connection2.png)  
4. Quando solicitado que o Gestor de Ligação necessita de um privilégio elevado para atualizar a tabela de rotas, escolha **Continuar**.
5. Selecione **Sim** na caixa de diálogo de controlo de conta do utilizador para continuar.

   Estabeleceu uma ligação VPN com o seu VNet de Instância Gerida.

    ![Ligação VPN](./media/sql-database-managed-instance-configure-p2s/vpn-connection-succeeded.png)  

## <a name="use-ssms-to-connect-to-the-managed-instance"></a>Utilize SSMS para ligar à Instância Gerida

1. No computador cliente no local, abra o Estúdio de Gestão de Servidores SQL (SSMS).
2. Na caixa de diálogo **Connect to Server,** introduza o nome de **anfitrião** totalmente qualificado para a sua Instância Gerida na caixa de **nome** sinuosa.
3. Selecione A autenticação do **servidor SQL,** forneça o seu nome de utilizador e palavra-passe e, em seguida, selecione **Connect**.

    ![ligação SSMS](./media/sql-database-managed-instance-configure-vm/ssms-connect.png)  

Depois de se ligar, pode ver o seu sistema e as bases de dados do utilizador no nó de Bases de Dados. Também pode ver vários objetos nos nós de Segurança, Objetos de Servidor, Replicação, Gestão, Agente de ServidorEs SQL e nódeos do Perfil XEvent.

## <a name="next-steps"></a>Passos seguintes

- Para um início rápido que mostre como se conectar a partir de uma máquina virtual Azure, consulte [Configurar uma ligação ponto-a-local](sql-database-managed-instance-configure-p2s.md).
- Para uma descrição geral das opções de ligação para aplicações, veja [Ligar as suas aplicações à Instância Gerida](sql-database-managed-instance-connect-app.md).
- Para restaurar uma base de dados existente do SQL Server a partir do local para uma instância gerida, pode utilizar o Serviço de Migração de Bases de [Dados Azure (DMS) para migração](../dms/tutorial-sql-server-to-managed-instance.md) ou o [comando RESTABELECIMENTO T-SQL](sql-database-managed-instance-get-started-restore.md) para restaurar a partir de um ficheiro de backup de base de dados.
