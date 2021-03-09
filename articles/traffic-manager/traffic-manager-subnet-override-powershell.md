---
title: Sub-rede Azure Traffic Manager sobrepõe-se usando a Azure PowerShell | Microsoft Docs
description: Este artigo irá ajudá-lo a entender como a sub-rede do Traffic Manager é usada para substituir o método de encaminhamento de um perfil de Gestor de Tráfego para direcionar o tráfego para um ponto final baseado no endereço IP do utilizador final através da gama IP pré-indefinida para mapeamentos de ponto final utilizando Azure PowerShell.
services: traffic-manager
documentationcenter: ''
author: duongau
ms.topic: how-to
ms.service: traffic-manager
ms.date: 09/18/2019
ms.author: duau
ms.openlocfilehash: 7dd7f43044a9643eb7e9d5296dfb209e425d5fb6
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102504786"
---
# <a name="traffic-manager-subnet-override-using-azure-powershell"></a>Sub-rede do Gestor de Tráfego sobrepõe-se usando Azure Powershell

A substituição da sub-rede do Traffic Manager permite alterar o método de encaminhamento de um perfil.  A adição de uma sobreposição irá direcionar o tráfego com base no endereço IP do utilizador final com uma gama IP predefinida para o mapeamento do ponto final. 

## <a name="how-subnet-override-works"></a>Como funciona a sobreposição da sub-rede

Quando as sobreposições de sub-rede são adicionadas a um perfil de gestor de tráfego, o Traffic Manager verificará primeiro se existe uma substituição da sub-rede para o endereço IP do utilizador final. Se um for encontrado, a consulta de DNS do utilizador será direcionada para o ponto final correspondente.  Se não for encontrado um mapeamento, o Gestor de Tráfego recorrerá ao método de encaminhamento original do perfil. 

Os intervalos de endereços IP podem ser especificados como intervalos CIDR (por exemplo, 1.2.3.0/24) ou como intervalos de endereço (por exemplo, 1.2.3.4-5.6.7.8). As gamas IP associadas a cada ponto final devem ser únicas a esse ponto final. Qualquer sobreposição de gamas IP entre diferentes pontos finais fará com que o perfil seja rejeitado pelo Gestor de Tráfego.

Existem dois tipos de perfis de encaminhamento que suportam substituições de sub-rede:

* **Geographic** - Se o Traffic Manager encontrar uma substituição de sub-rede para o endereço IP da consulta DNS, irá encaminhar a consulta para o ponto final, seja qual for a saúde do ponto final.
* **Desempenho** - Se o Traffic Manager encontrar uma substituição de sub-rede para o endereço IP da consulta DNS, só irá encaminhar o tráfego para o ponto final se for saudável.  O Gestor de Tráfego vai recuar para o encaminhamento de desempenho heurístico se o ponto final de substituição da sub-rede não for saudável.

## <a name="create-a-traffic-manager-subnet-override"></a>Criar uma sub-rede do Traffic Manager

Para criar uma sub-rede do Traffic Manager, pode utilizar o Azure PowerShell para adicionar as sub-redes para a substituição do ponto final do Traffic Manager.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pode executar os comandos que seguem na [Azure Cloud Shell,](https://shell.azure.com/powershell)ou executando o PowerShell a partir do seu computador. O Azure Cloud Shell é uma concha interativa gratuita. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. Se executar o PowerShell a partir do computador, precisa do módulo Azure PowerShell, 1.0.0 ou mais tarde. Pode correr `Get-Module -ListAvailable Az` para encontrar a versão instalada. Se precisar de instalar ou atualizar, veja [Install Azure PowerShell module](/powershell/azure/install-az-ps)(Instalar o módulo do Azure PowerShell). Se estiver a executar o PowerShell localmente, também tem de correr `Login-AzAccount` para entrar no Azure.


1. **Recuperar o ponto final do Gestor de Tráfego:**

    Para ativar a sobreposição da sub-rede, recupere o ponto final a que deseja adicionar a sobreposição e guarde-a numa variável utilizando [o Get-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/get-aztrafficmanagerendpoint).

    Substitua o Nome, Nome de Perfil e Nome do Grupo de Recursos pelos valores do ponto final que está a alterar.

    ```powershell

    $TrafficManagerEndpoint = Get-AzTrafficManagerEndpoint -Name "contoso" -ProfileName "ContosoProfile" -ResourceGroupName "ResourceGroup" -Type AzureEndpoints

    ```
2. **Adicione o intervalo de endereço IP ao ponto final:**
    
    Para adicionar a gama de endereços IP ao ponto final, utilizará [Add-AzTrafficManagerIpAddressRange](/powershell/module/az.trafficmanager/add-aztrafficmanageripaddressrange) para adicionar a gama.

    ```powershell

    ### Add a range of IPs ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "1.2.3.4" -Last "5.6.7.8"

    ### Add a subnet ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "9.10.11.0" -Scope 24

    ### Add a range of IPs with a subnet ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "12.13.14.0" -Last "12.13.14.31" -Scope 27
 
    ```
    Assim que as gamas forem adicionadas, utilize [o Set-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/set-aztrafficmanagerendpoint) para atualizar o ponto final.

    ```powershell

    Set-AzTrafficManagerEndpoint -TrafficManagerEndpoint $TrafficManagerEndpoint

    ```
A remoção do intervalo de endereços IP pode ser concluída utilizando [remove-AzTrafficManagerIpAddressRange](/powershell/module/az.trafficmanager/remove-aztrafficmanageripaddressrange).

1.  **Recuperar o ponto final do Gestor de Tráfego:**

    Para ativar a sobreposição da sub-rede, recupere o ponto final a que deseja adicionar a sobreposição e guarde-a numa variável utilizando [o Get-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/get-aztrafficmanagerendpoint).

    Substitua o Nome, Nome de Perfil e Nome do Grupo de Recursos pelos valores do ponto final que está a alterar.

    ```powershell

    $TrafficManagerEndpoint = Get-AzTrafficManagerEndpoint -Name "contoso" -ProfileName "ContosoProfile" -ResourceGroupName "ResourceGroup" -Type AzureEndpoints

    ```
2. **Remova a gama de endereços IP do ponto final:**

    ```powershell
    
    ### Remove a range of IPs ###
    Remove-AzTrafficManagerIpAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "1.2.3.4" -Last "5.6.7.8"

    ### Remove a subnet ###
    Remove-AzTrafficManagerIpAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "9.10.11.0" -Scope 24

    ### Remove a range of IPs with a subnet ###
    Remove-AzTrafficManagerIpAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "12.13.14.0" -Last "12.13.14.31" -Scope 27

    ```
     Assim que os intervalos forem removidos, utilize [o Set-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/set-aztrafficmanagerendpoint) para atualizar o ponto final.

    ```powershell

    Set-AzTrafficManagerEndpoint -TrafficManagerEndpoint $TrafficManagerEndpoint

    ```

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre [os métodos de encaminhamento de tráfego](traffic-manager-routing-methods.md)do Traffic Manager .

Conheça o [método de encaminhamento de tráfego sub-rede](./traffic-manager-routing-methods.md#subnet-traffic-routing-method)