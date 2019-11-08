---
title: Configurar instância gerenciada pelo P2S
description: Conecte-se a um Instância Gerenciada do Banco de Dados SQL do Azure usando SQL Server Management Studio usando uma conexão ponto a site de um computador cliente local.
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
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818920"
---
# <a name="quickstart-configure-a-point-to-site-connection-to-an-azure-sql-database-managed-instance-from-on-premises"></a>Início rápido: configurar uma conexão ponto a site com um Instância Gerenciada do Banco de Dados SQL do Azure local

Este guia de início rápido demonstra como se conectar a um Instância Gerenciada do Banco de Dados SQL do Azure usando o SSMS ( [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) ) de um computador cliente local em uma conexão ponto a site. Para obter informações sobre conexões ponto a site, consulte [sobre VPN ponto a site](../vpn-gateway/point-to-site-about.md)

## <a name="prerequisites"></a>Pré-requisitos

Este guia de início rápido:

- Usa os recursos criados [criar um instância gerenciada](sql-database-managed-instance-get-started.md) como ponto de partida.
- Requer o PowerShell 5,1 e AZ PowerShell 1.4.0 ou posterior em seu computador cliente local. Se necessário, consulte as instruções para [instalar o módulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps#install-the-azure-powershell-module).
- Requer a versão mais recente do [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) no computador cliente local.

## <a name="attach-a-vpn-gateway-to-your-managed-instance-virtual-network"></a>Anexar um gateway de VPN à sua rede virtual Instância Gerenciada

1. Abra o PowerShell no computador cliente local.

2. Copie este script do PowerShell. Esse script anexa um gateway de VPN ao Instância Gerenciada rede virtual que você criou no guia de início rápido [criar um instância gerenciada](sql-database-managed-instance-get-started.md) . Esse script usa o módulo Azure PowerShell AZ e fará o seguinte para hosts baseados em Windows ou Linux:

   - Criar e instalar certificados no computador cliente
   - Calcula o intervalo de IP de sub-rede futuro do gateway de VPN
   - Cria o GatewaySubnet
   - Implanta o modelo de Azure Resource Manager que anexa o gateway de VPN à sub-rede VPN

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

3. Cole o script na janela do PowerShell e forneça os parâmetros necessários. Os valores para `<subscriptionId>`, `<resourceGroup>`e `<virtualNetworkName>` devem corresponder àqueles que você usou para [criar instância gerenciada](sql-database-managed-instance-get-started.md) início rápido. O valor de `<certificateNamePrefix>` pode ser uma cadeia de caracteres de sua escolha.

4. Execute o script do PowerShell.

> [!IMPORTANT]
> Não continue até que o script do PowerShell seja concluído.

## <a name="create-a-vpn-connection-to-your-managed-instance"></a>Criar uma conexão VPN com seu Instância Gerenciada

1. Iniciar sessão no [portal do Azure](https://portal.azure.com/).
2. Abra o grupo de recursos no qual você criou o gateway de rede virtual e, em seguida, abra o recurso de gateway de rede virtual.
3. Selecione **configuração ponto a site** e, em seguida, selecione **baixar cliente VPN**.

    ![Baixar cliente VPN](./media/sql-database-managed-instance-configure-p2s/download-vpn-client.png)  
4. No computador cliente local, extraia os arquivos do arquivo zip e, em seguida, abra a pasta com os arquivos extraídos.
5. Abra a pasta '**WindowsAmd64** ' e abra o arquivo **VpnClientSetupAmd64. exe** .
6. Se você receber uma mensagem **do computador protegido pelo Windows** , clique em **mais informações** e, em seguida, clique em **executar mesmo assim**.

    ![Instalar cliente VPN](./media/sql-database-managed-instance-configure-p2s/vpn-client-defender.png)\
7. Na caixa de diálogo controle de conta de usuário, clique em **Sim** para continuar.
8. Na caixa de diálogo que faz referência à sua rede virtual, selecione **Sim** para instalar o cliente VPN para sua rede virtual.

## <a name="connect-to-the-vpn-connection"></a>Conectar-se à conexão VPN

1. Acesse **VPN** na **rede & Internet** no computador cliente local e selecione sua rede virtual instância gerenciada para estabelecer uma conexão com essa VNet. Na imagem a seguir, a VNet é denominada **MyNewVNet**.

    ![Ligação VPN](./media/sql-database-managed-instance-configure-p2s/vpn-connection.png)  
2. Selecione **Ligar**.
3. Na caixa de diálogo, selecione **conectar**.

    ![Ligação VPN](./media/sql-database-managed-instance-configure-p2s/vpn-connection2.png)  
4. Quando solicitado que o Gerenciador de conexões precise de privilégios elevados para atualizar sua tabela de rotas, escolha **continuar**.
5. Selecione **Sim** na caixa de diálogo controle de conta de usuário para continuar.

   Você estabeleceu uma conexão VPN com seu Instância Gerenciada VNet.

    ![Ligação VPN](./media/sql-database-managed-instance-configure-p2s/vpn-connection-succeeded.png)  

## <a name="use-ssms-to-connect-to-the-managed-instance"></a>Use o SSMS para se conectar ao Instância Gerenciada

1. No computador cliente local, abra SQL Server Management Studio (SSMS).
2. Na caixa de diálogo **conectar ao servidor** , insira o nome de **host** totalmente qualificado para seu instância gerenciada na caixa **nome do servidor** .
3. Selecione **SQL Server autenticação**, forneça seu nome de usuário e senha e, em seguida, selecione **conectar**.

    ![ligação SSMS](./media/sql-database-managed-instance-configure-vm/ssms-connect.png)  

Depois de se conectar, você pode exibir os bancos de dados do sistema e do usuário no nó bancos de dados. Você também pode exibir vários objetos nos nós segurança, objetos de servidor, replicação, gerenciamento, SQL Server Agent e XEvent Profiler.

## <a name="next-steps"></a>Passos seguintes

- Para obter um guia de início rápido mostrando como se conectar de uma máquina virtual do Azure, consulte [Configurar uma conexão ponto a site](sql-database-managed-instance-configure-p2s.md).
- Para uma descrição geral das opções de ligação para aplicações, veja [Ligar as suas aplicações à Instância Gerida](sql-database-managed-instance-connect-app.md).
- Para restaurar um banco de dados de SQL Server existente do local para um Instância Gerenciada, você pode usar o [serviço de migração de banco de dados do Azure (DMS) para migração](../dms/tutorial-sql-server-to-managed-instance.md) ou o comando de [restauração T-SQL](sql-database-managed-instance-get-started-restore.md) para restaurar de um arquivo de backup de banco de dados.
