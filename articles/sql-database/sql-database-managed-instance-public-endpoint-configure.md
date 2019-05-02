---
title: Configurar o ponto final público - base de dados do SQL do Azure geridos instância | Documentos da Microsoft
description: Saiba como configurar um ponto final público para a instância gerida
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 04/26/2019
ms.openlocfilehash: ea16efbb846f21ec7c3fa39b2efeac741d8f8ce0
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64928366"
---
# <a name="configure-public-endpoint-in-azure-sql-database-managed-instance"></a>Configurar o ponto final público na instância gerida da base de dados do Azure SQL

Ponto final público para um [instância gerida](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index) ativa o acesso de dados à sua instância gerida de fora a [rede virtual](../virtual-network/virtual-networks-overview.md). É possível aceder à sua instância gerida dos serviços do Azure do multi-inquilinos, como o Power BI, serviço de aplicações do Azure ou uma rede no local. Ao utilizar o ponto final público numa instância gerida, não é necessário usar uma VPN, que pode ajudar a evitar problemas de débito VPN.

Neste artigo, ficará a saber como:

> [!div class="checklist"]
> - Ativar o ponto final público para a sua instância gerida no portal do Azure
> - Ativar o ponto final público para a sua instância gerida com o PowerShell
> - Configurar o seu grupo de segurança de rede de instância gerida para permitir o tráfego para o ponto final público de instância gerida
> - Obter a cadeia de ligação de ponto final público de instância gerida

## <a name="permissions"></a>Permissões

Devido à sensibilidade dos dados que está numa instância gerida, a configuração para ativar o ponto final público de instância gerida necessita de um processo de dois passos. Esta medida de segurança obedeça à separação de funções (SoD):

- Ativar o ponto final público numa instância gerida precisa ser feito pelo administrador instância gerida. A administração da instãncia gerida pode ser encontrada no **descrição geral** página do seu SQL gerida recursos de instância.
- Permitir o tráfego através de um grupo de segurança de rede que precisa ser feito por um administrador de rede. Para obter mais informações, consulte [permissões do grupo de segurança de rede](../virtual-network/manage-network-security-group.md#permissions).

## <a name="enabling-public-endpoint-for-a-managed-instance-in-the-azure-portal"></a>Ativar o ponto final público para uma instância gerida no portal do Azure

1. Inicie o portal do Azure em <https://portal.azure.com/.>
1. Abra o grupo de recursos com a instância gerida e selecione o **instância gerida SQL** que pretende configurar o ponto final público.
1. Sobre o **Security** definições, selecione o **rede Virtual** separador.
1. Na página de configuração de rede Virtual, selecione **habilitar** e, em seguida, o **guardar** ícone para atualizar a configuração.

![mi-vnet-config.png](media/sql-database-managed-instance-public-endpoint-configure/mi-vnet-config.png)

## <a name="enabling-public-endpoint-for-a-managed-instance-using-powershell"></a>Ativar o ponto final público para uma instância gerida com o PowerShell

### <a name="enable-public-endpoint"></a>Ativar o ponto final público

Execute os seguintes comandos do PowerShell. Substitua **id de subscrição** com o ID da subscrição. Substitua, também **rg-name** com o grupo de recursos da sua instância gerida e replace **mi-name** com o nome da sua instância gerida.

```powershell
Install-Module -Name Az

Import-Module Az.Accounts
Import-Module Az.Sql

Connect-AzAccount

# Use your subscription ID in place of subscription-id below

Select-AzSubscription -SubscriptionId {subscription-id}

# Replace rg-name with the resource group for your managed instance, and replace mi-name with the name of your managed instance

$mi = Get-AzSqlInstance -ResourceGroupName {rg-name} -Name {mi-name}

$mi = $mi | Set-AzSqlInstance -PublicDataEndpointEnabled $true -force
```

### <a name="disable-public-endpoint"></a>Desativar o ponto final público

Para desativar o ponto final público com o PowerShell, executaria o seguinte comando (e também não se esqueça de fechar o NSG para a porta de entrada 3342, se tiver configurado):

```powershell
Set-AzSqlInstance -PublicDataEndpointEnabled $false -force
```

## <a name="allow-public-endpoint-traffic-on-the-network-security-group"></a>Permitir o tráfego de ponto final público no grupo de segurança de rede

1. Se tiver a página de configuração da instância gerida ainda aberta, navegue para o **descrição geral** separador. Caso contrário, volte ao seu **instância gerida SQL** recursos. Selecione o **sub-rede e rede Virtual** link, que leva-o para a página de configuração de rede Virtual.

    ![mi-overview.png](media/sql-database-managed-instance-public-endpoint-configure/mi-overview.png)

1. Selecione o **sub-redes** separador no painel esquerdo da configuração da sua rede Virtual e anote o **grupo de segurança** para a sua instância gerida.

    ![mi-vnet-subnet.png](media/sql-database-managed-instance-public-endpoint-configure/mi-vnet-subnet.png)

1. Regresse ao seu grupo de recursos que contém a instância gerida. Deverá ver o **grupo de segurança de rede** nome indicado acima. Selecione o nome para entrar em página de configuração do grupo de segurança de rede.

1. Selecione o **regras de segurança de entrada** separador, e **Add** uma regra com prioridade mais alta do que o **deny_all_inbound** regra com as seguintes definições: </br> </br>

    |Definição  |Valor sugerido  |Descrição  |
    |---------|---------|---------|
    |**Origem**     |Qualquer endereço IP ou a etiqueta de serviço         |<ul><li>Para obter serviços do Azure, como o Power BI, selecione a etiqueta de serviço de Cloud do Azure</li> <li>Para o seu computador ou VM do Azure, utilize o endereço IP do NAT</li></ul> |
    |**Intervalos de portas de origem**     |*         |Deixe esta opção para * (qualquer) como portas de origem são normalmente imprevisíveis alocada dinamicamente e como tal, |
    |**Destino**     |Qualquer         |Deixando de destino como qualquer para permitir o tráfego para a sub-rede de instância gerida |
    |**Intervalos de portas de destino**     |3342         |Porta de destino âmbito para 3342, que é o instância gerida TDS ponto final público |
    |**Protocolo**     |TCP         |Geridas protocolo TCP de usos de instância do TDS |
    |**Ação**     |Permitir         |Permitir tráfego de entrada para a instância gerida através do ponto final público |
    |**Prioridade**     |1300         |Certifique-se de que esta regra é uma prioridade mais alta do que o **deny_all_inbound** regra |

    ![mi-nsg-rules.png](media/sql-database-managed-instance-public-endpoint-configure/mi-nsg-rules.png)

    > [!NOTE]
    > Porta 3342 é utilizada para o ponto final público ligações a instância gerida e não podem ser alteradas neste momento.

## <a name="obtaining-the-managed-instance-public-endpoint-connection-string"></a>Obter a cadeia de ligação de ponto final público de instância gerida

1. Navegue para a página de configuração de instância gerida de SQL que tenha sido ativada para o ponto final público. Selecione o **cadeias de ligação** separador sob a **definições** configuração.
1. Tenha em atenção que o nome de anfitrião do ponto final público é fornecido no formato < mi_name >. **público**. < dns_zone >. database.windows.net e de que a porta utilizada para a ligação é 3342.

    ![mi-public-endpoint-conn-string.png](media/sql-database-managed-instance-public-endpoint-configure/mi-public-endpoint-conn-string.png)

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre [usando o banco de dados do Azure SQL gerida a instância de forma segura com o ponto final público](sql-database-managed-instance-public-endpoint-securely.md).