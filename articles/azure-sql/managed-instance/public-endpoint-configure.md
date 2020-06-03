---
title: Configure ponto final público - Azure SQL Gestd Instance
description: Saiba como configurar um ponto final público para Azure SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=1
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto, carlrab
ms.date: 05/07/2019
ms.openlocfilehash: a6d4ea22d3b05b14ce0d3e63912ea8bb7a432e57
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/03/2020
ms.locfileid: "84310160"
---
# <a name="configure-public-endpoint-in-azure-sql-managed-instance"></a>Configure o ponto final público em Azure SQL Gestd Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

O ponto final público para uma [instância gerida](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index) permite o acesso de dados à sua instância gerida de fora da [rede virtual.](../../virtual-network/virtual-networks-overview.md) Você pode aceder a sua instância gerida a partir de serviços Azure multi-inquilinos como Power BI, Azure App Service, ou uma rede no local. Ao utilizar o ponto final público numa instância gerida, não precisa de usar uma VPN, o que pode ajudar a evitar problemas de produção VPN.

Neste artigo, aprenderá a:

> [!div class="checklist"]
>
> - Ativar o ponto final público para o seu exemplo gerido no portal Azure
> - Ativar o ponto final público para o seu caso gerido usando o PowerShell
> - Configure o seu grupo de segurança de rede de instância gerido para permitir o tráfego para o ponto final público de instância gerida
> - Obtenha a cadeia de ligação de ponto final de instância gerida

## <a name="permissions"></a>Permissões

Devido à sensibilidade dos dados que estão em caso gerido, a configuração para permitir o ponto final público gerido requer um processo em duas etapas. Esta medida de segurança adere à separação dos direitos (SOD):

- A viabilizar o ponto final público sobre uma instância gerida tem de ser feita pela administração de instâncias geridas. O administrador de caso gerido pode ser encontrado na página **geral** do seu recurso de instância gerido.
- Permitir o tráfego utilizando um grupo de segurança de rede que precisa ser feito por um administrador de rede. Para obter mais informações, consulte [as permissões do grupo de segurança da rede.](../../virtual-network/manage-network-security-group.md#permissions)

## <a name="enabling-public-endpoint-for-a-managed-instance-in-the-azure-portal"></a>Permitir o ponto final público para uma instância gerida no portal Azure

1. Lançar o portal Azure em<https://portal.azure.com/.>
1. Abra o grupo de recursos com a instância gerida e selecione a **instância gerida sql** em que pretende configurar o ponto final público.
1. Nas definições **de Segurança,** selecione o **separador rede Virtual.**
1. Na página de configuração da rede Virtual, selecione **Ativar** e, em seguida, o ícone **Guardar** para atualizar a configuração.

![mi-vnet-config.png](./media/public-endpoint-configure/mi-vnet-config.png)

## <a name="enabling-public-endpoint-for-a-managed-instance-using-powershell"></a>Permitir o ponto final público para um caso gerido usando o PowerShell

### <a name="enable-public-endpoint"></a>Permitir o ponto final público

Executar os seguintes comandos PowerShell. Substitua o **id de subscrição** pelo seu ID de subscrição. Substitua também o **nome rg** pelo grupo de recursos para a sua instância gerida e substitua o **nome mi-name** pelo nome da sua instância gerida.

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

Para desativar o ponto final público utilizando o PowerShell, executaria o seguinte comando (e também não se esqueça de fechar o NSG para a porta de entrada 3342 se o tiver configurado):

```powershell
Set-AzSqlInstance -PublicDataEndpointEnabled $false -force
```

## <a name="allow-public-endpoint-traffic-on-the-network-security-group"></a>Permitir o tráfego de pontos finais públicos no grupo de segurança da rede

1. Se tiver a página de configuração do caso gerido ainda aberta, navegue para **SQL managed instance** o **separador Visão Geral.** Selecione o link **rede virtual/sub-rede,** que o levará à página de configuração da rede Virtual.

    ![mi-overview.png](./media/public-endpoint-configure/mi-overview.png)

1. Selecione o separador **Subnets** no painel de configuração esquerda da sua rede Virtual e tome nota do **GRUPO DE SEGURANÇA** para a sua instância gerida.

    ![mi-vnet-subnet.png](./media/public-endpoint-configure/mi-vnet-subnet.png)

1. Volte para o seu grupo de recursos que contém o seu caso gerido. Deve ver o nome do **grupo de segurança da Rede** acima indicado. Selecione o nome para entrar na página de configuração do grupo de segurança de rede.

1. Selecione o **separador regras de segurança de entrada** e **Adicione** uma regra que tenha maior prioridade do que a regra **deny_all_inbound** com as seguintes definições: </br> </br>

    |Definição  |Valor sugerido  |Descrição  |
    |---------|---------|---------|
    |**Origem**     |Qualquer endereço IP ou tag de serviço         |<ul><li>Para serviços Azure como Power BI, selecione a Etiqueta de Serviço de Nuvem Azure</li> <li>Para o seu computador ou máquina virtual Azure, utilize o endereço NAT IP</li></ul> |
    |**Intervalo de portas de origem**     |* |Deixe isto para * (qualquer) uma vez que as portas de origem são geralmente atribuídas dinamicamente e, como tal, imprevisíveis |
    |**Destino**     |Qualquer         |Deixando o destino como Qualquer para permitir o tráfego na sub-rede de instância gerida |
    |**Intervalos de portas de destino**     |3342         |Porto de destino de âmbito para 3342, que é o ponto final público de instância gerida TDS |
    |**Protocolo**     |TCP         |SQL Managed Instance usa protocolo TCP para TDS |
    |**Ação**     |Permitir         |Permitir que o tráfego de entrada seja gerido através do ponto final público |
    |**Prioridade**     |1300         |Certifique-se de que esta regra é maior prioridade do que a **regra deny_all_inbound** |

    ![mi-nsg-rules.png](./media/public-endpoint-configure/mi-nsg-rules.png)

    > [!NOTE]
    > A porta 3342 é utilizada para ligações públicas de ponto final para ocorrências geridas, e não pode ser alterada neste momento.

## <a name="obtaining-the-managed-instance-public-endpoint-connection-string"></a>Obtenção da cadeia de ligação de ponto final de instância gerida

1. Navegue para a página de configuração de exemplo gerida que foi ativada para o ponto final público. Selecione o separador **'Ligação' de séries** na configuração **'Definições'.**
1. Note que o nome de anfitrião do ponto final público vem no formato <mi_name>. **público**.<dns_zone>.database.windows.net e que a porta utilizada para a ligação é 3342.

    ![mi-public-endpoint-conn-string.png](./media/public-endpoint-configure/mi-public-endpoint-conn-string.png)

## <a name="next-steps"></a>Passos seguintes

Saiba como utilizar [a Azure SQL Managed Instance de forma segura com o ponto final público](public-endpoint-overview.md).
