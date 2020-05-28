---
title: Configurar ponto final público - instância gerida
description: Saiba como configurar um ponto final público para a Instância Gerida Azure SQL
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=1
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto, carlrab
ms.date: 05/07/2019
ms.openlocfilehash: e11e8181ba17f9833cd4add7650ad9f81a158fd9
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84118757"
---
# <a name="configure-public-endpoint-in-azure-sql-managed-instance"></a>Configure ponto final público em Caso Gerido Azure SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

O ponto final público para uma [instância gerida](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index) permite o acesso de dados à sua instância gerida de fora da [rede virtual](../../virtual-network/virtual-networks-overview.md). Você pode aceder a sua instância gerida a partir de serviços azure multi-inquilinos como Power BI, Azure App Service ou uma rede no local. Ao utilizar o ponto final público numa instância gerida, não precisa de utilizar uma VPN, que pode ajudar a evitar problemas de entrada de VPN.

Neste artigo, aprenderá a:

> [!div class="checklist"]
>
> - Ativar ponto final público para a sua instância gerida no portal Azure
> - Ativar ponto final público para a sua instância gerida usando powerShell
> - Configure o seu grupo de segurança de rede de instância gerido para permitir o tráfego para o ponto final da instância gerida
> - Obtenha a cadeia de conexão de ponto final de instância gerida

## <a name="permissions"></a>Permissões

Devido à sensibilidade dos dados que se encontra numa instância gerida, a configuração para ativar o ponto final do caso gerido requer um processo em duas etapas. Esta medida de segurança adere à separação de direitos (SoD):

- A via final pública sobre uma instância gerida tem de ser feita pela administração de instância gerida. A administração de instância gerida pode ser encontrada na página **de visão geral** do seu recurso de instância gerido.
- Permitir o tráfego utilizando um grupo de segurança de rede que precisa de ser feito por um administrador de rede. Para mais informações, consulte [as permissões](../../virtual-network/manage-network-security-group.md#permissions)do grupo de segurança da rede .

## <a name="enabling-public-endpoint-for-a-managed-instance-in-the-azure-portal"></a>Habilitar o ponto final público para uma instância gerida no portal Azure

1. Lançar o portal Azure em<https://portal.azure.com/.>
1. Abra o grupo de recursos com a instância gerida e selecione a instância gerida pelo **SQL** em que pretende configurar o ponto final público.
1. Nas definições de **Segurança,** selecione o separador de **rede Virtual.**
1. Na página de configuração da rede Virtual, selecione **'Activar'** e, em seguida, o ícone **Guardar** para atualizar a configuração.

![mi-vnet-config.png](./media/public-endpoint-configure/mi-vnet-config.png)

## <a name="enabling-public-endpoint-for-a-managed-instance-using-powershell"></a>Habilitar o ponto final público para uma instância gerida usando powerShell

### <a name="enable-public-endpoint"></a>Ativar o ponto final do público

Executar os seguintes comandos PowerShell. Substitua **o id de subscrição** pelo seu ID de subscrição. Substitua também o **nome rg** pelo grupo de recursos para a sua instância gerida e substitua o **nome mi** pelo nome da sua instância gerida.

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

### <a name="disable-public-endpoint"></a>Desativar o ponto final do público

Para desativar o ponto final público utilizando o PowerShell, executaria o seguinte comando (e também não se esqueça de fechar o NSG para a porta de entrada 3342 se o tiver configurado):

```powershell
Set-AzSqlInstance -PublicDataEndpointEnabled $false -force
```

## <a name="allow-public-endpoint-traffic-on-the-network-security-group"></a>Permitir o tráfego de pontos finais públicos no grupo de segurança da rede

1. Se tiver a página de configuração da instância gerida ainda aberta, navegue para o separador **Overview.** Caso contrário, volte ao recurso de **instância gerido pela SQL.** Selecione a ligação **rede/sub-rede Virtual,** que o levará à página de configuração da rede Virtual.

    ![mi-overview.png](./media/public-endpoint-configure/mi-overview.png)

1. Selecione o separador **Subnets** no painel de configuração à esquerda da sua rede Virtual e tome nota do **GRUPO DE SEGURANÇA** para a sua instância gerida.

    ![mi-vnet-subnet.png](./media/public-endpoint-configure/mi-vnet-subnet.png)

1. Volte para o seu grupo de recursos que contém a sua instância gerida. Deve ver o nome do grupo de segurança da **Rede** acima referido. Selecione o nome para entrar na página de configuração do grupo de segurança da rede.

1. Selecione o separador de regras de **segurança de entrada** e **adicione** uma regra que tenha uma prioridade superior à regra **deny_all_inbound** com as seguintes definições: </br> </br>

    |Definição  |Valor sugerido  |Descrição  |
    |---------|---------|---------|
    |**Origem**     |Qualquer endereço IP ou etiqueta de serviço         |<ul><li>Para serviços Azure como Power BI, selecione a etiqueta de serviço Azure Cloud</li> <li>Para o seu computador ou Azure VM, utilize o endereço IP NAT</li></ul> |
    |**Intervalo de portas de origem**     |* |Deixe isto para * (qualquer) como as portas de origem são geralmente dinamicamente atribuídas e, como tal, imprevisíveis |
    |**Destino**     |Qualquer         |Deixando o destino como Qualquer para permitir o tráfego na subnet de instância gerida |
    |**Intervalos de portas de destino**     |3342         |Porta de destino de âmbito para 3342, que é o ponto final público tDS de instância gerida |
    |**Protocolo**     |TCP         |SQL Managed Instance usa protocolo TCP para TDS |
    |**Ação**     |Permitir         |Permitir que o tráfego de entrada gerencie a instância através do ponto final público |
    |**Prioridade**     |1300         |Certifique-se de que esta regra é mais prioritária do que a **regra deny_all_inbound** |

    ![mi-nsg-rules.png](./media/public-endpoint-configure/mi-nsg-rules.png)

    > [!NOTE]
    > O porto 3342 é utilizado para ligações públicas de ponto final para instância gerida, e não pode ser alterado neste momento.

## <a name="obtaining-the-managed-instance-public-endpoint-connection-string"></a>Obtenção da cadeia de ligação de ponto final de instância gerida

1. Navegue para a página de configuração de instância gerida que foi ativada para ponto final público. Selecione o separador de **cordas de ligação** sob a configuração **Definições.**
1. Note que o nome do anfitrião do ponto final do público vem no formato <mi_name>. **público**.<dns_zone>.database.windows.net e que a porta utilizada para a ligação é 3342.

    ![mi-público-endpoint-conn-string.png](./media/public-endpoint-configure/mi-public-endpoint-conn-string.png)

## <a name="next-steps"></a>Próximos passos

Aprenda a utilizar o [Azure SQL Managed Instance com segurança com ponto final público](public-endpoint-overview.md).
