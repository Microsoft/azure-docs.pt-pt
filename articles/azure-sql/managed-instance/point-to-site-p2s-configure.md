---
title: Configure a conectividade ponto-a-local utilizando SSMS
titleSuffix: Azure SQL Managed Instance
description: Conecte-se a Azure SQL Managed Instance usando o SQL Server Management Studio (SSMS) utilizando uma ligação ponto-a-local a partir de um computador cliente no local.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, jovanpop
ms.date: 03/13/2019
ms.openlocfilehash: 3baf2d7ed7c326895ae40948fc2d0a4cc03021f9
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92788371"
---
# <a name="quickstart-configure-a-point-to-site-connection-to-azure-sql-managed-instance-from-on-premises"></a>Quickstart: Configurar uma ligação ponto-a-local com Azure SQL Gestão Instância a partir de instalações
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Este quickstart demonstra como ligar-se a Azure SQL Managed Instance usando [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) (SSMS) a partir de um computador cliente no local sobre uma ligação ponto-a-local. Para obter informações sobre as ligações ponto-a-local, consulte [a VPN ponto-a-local](../../vpn-gateway/point-to-site-about.md).

## <a name="prerequisites"></a>Pré-requisitos

Este guia de início rápido:

- Utiliza os recursos criados na  [Criação de um caso gerido](instance-create-quickstart.md) como ponto de partida.
- Requer PowerShell 5.1 e Azure PowerShell 1.4.0 ou mais tarde no seu computador cliente no local. Se necessário, consulte as instruções para [a instalação do módulo Azure PowerShell](/powershell/azure/install-az-ps#install-the-azure-powershell-module).
- Requer a mais recente versão do [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) no seu computador cliente no local.

## <a name="attach-a-vpn-gateway-to-a-virtual-network"></a>Anexar uma porta de entrada VPN a uma rede virtual

1. Abra o PowerShell no computador do cliente no local.

2. Copie este script PowerShell. Este script anexa uma porta de entrada VPN à rede virtual SQL Managed Instance que criou no [Create a managed instance](instance-create-quickstart.md) quickstart. Este script utiliza o Módulo Azure PowerShell Az e faz o seguinte para anfitriões baseados no Windows ou linux:

   - Cria e instala certificados numa máquina cliente
   - Calcula a futura gama IP de gateway VPN
   - Cria a sub-rede gateway
   - Implementa o modelo de Gestor de Recursos Azure que anexa o gateway VPN à sub-rede VPN

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

3. Cole o script na sua janela PowerShell e forneça os parâmetros necessários. Os valores para `<subscriptionId>` , e devem corresponder aos que `<resourceGroup>` `<virtualNetworkName>` usou para o Create um quickstart [de instância gerida.](instance-create-quickstart.md) O valor para `<certificateNamePrefix>` pode ser uma cadeia da sua escolha.

4. Execute o script PowerShell.

> [!IMPORTANT]
> Não continue até que o script PowerShell esteja completo.

## <a name="create-a-vpn-connection"></a>Criar uma ligação VPN

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. Abra o grupo de recursos no qual criou o gateway de rede virtual e, em seguida, abra o recurso de gateway de rede virtual.
3. Selecione **configuração ponto-a-local** e, em seguida, selecione **Baixar o cliente VPN** .

    ![Baixar cliente VPN](./media/point-to-site-p2s-configure/download-vpn-client.png)  
4. No computador do cliente no local, extraia os ficheiros do ficheiro zip e, em seguida, abra a pasta com os ficheiros extraídos.
5. Abra a pasta **WindowsAmd64** e abra o ficheiro **VpnClientSetupAmd64.exe.**
6. Se receber uma mensagem do Windows protegida da sua mensagem **para PC,** clique em **Mais informações** e clique em **Executar de qualquer maneira** .

    ![Instalar cliente VPN](./media/point-to-site-p2s-configure/vpn-client-defender.png)
7. Na caixa de diálogo Controlo de Conta de Utilizador, clique em **Sim** para continuar.
8. Na caixa de diálogo que refere a sua rede virtual, selecione **Sim** para instalar o cliente VPN para a sua rede virtual.

## <a name="connect-to-the-vpn-connection"></a>Ligue-se à ligação VPN

1. Vá à **VPN** em **Network & Internet** no seu computador cliente no local e selecione a sua rede virtual SQL Managed Instance para estabelecer uma ligação a este VNet. Na imagem a seguir, o VNet chama-se **MyNewVNet** .

    ![Ligação VPN](./media/point-to-site-p2s-configure/vpn-connection.png)  
2. Selecione **Ligar** .
3. Na caixa de diálogo, selecione **Connect** .

    ![Screenshot que realça o botão Ligar.](./media/point-to-site-p2s-configure/vpn-connection2.png)  
4. Quando lhe for solicitado que o Connection Manager precisa de privilégios elevados para atualizar a sua tabela de rotas, escolha **Continue** .
5. Selecione **Sim** na caixa de diálogo de controlo da conta do utilizador para continuar.

   Estabeleceu uma ligação VPN com o seu SQL Managed Instance VNet.

    ![Screenshot que realça a mensagem Conectada quando tiver estabelecido a sua ligação.](./media/point-to-site-p2s-configure/vpn-connection-succeeded.png)  

## <a name="connect-with-ssms"></a>Ligar com o SSMS

1. No computador cliente no local, abra o SQL Server Management Studio.
2. Na caixa de diálogo **'Ligar ao Servidor',** insira o **nome de anfitrião** totalmente qualificado para a sua instância gerida na caixa **de nomes do Servidor.**
3. Selecione **A autenticação do servidor SQL,** forneça o seu nome de utilizador e senha e, em seguida, selecione **Connect** .

    ![SSMS ligam](./media/point-to-site-p2s-configure/ssms-connect.png)  

Depois de se ligar, pode visualizar o seu sistema e bases de dados de utilizadores no nó de Bases de Dados. Também pode ver vários objetos nos nós de Segurança, Objetos de Servidor, Replicação, Gestão, Agente do Servidor SQL e nós do Perfil XEvent.

## <a name="next-steps"></a>Passos seguintes

- Para obter um arranque rápido que mostre como ligar a partir de uma máquina virtual Azure, consulte [configurar uma ligação ponto-a-local](point-to-site-p2s-configure.md).
- Para obter uma visão geral das opções de ligação para aplicações, consulte [Conecte as suas aplicações à SQL Managed Instance](connect-application-instance.md).
- Para restaurar uma base de dados do SQL Server existente de um local para um caso gerido, pode utilizar o Serviço de [Migração da Base de Dados Azure para migração](../../dms/tutorial-sql-server-to-managed-instance.md) ou o [comando T-SQL RESTORE](restore-sample-database-quickstart.md) para restaurar a partir de um ficheiro de backup de base de dados.