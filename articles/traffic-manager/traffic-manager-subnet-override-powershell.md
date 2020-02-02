---
title: Subnet Azure Traffic Manager sobreposição usando Azure PowerShell  Microsoft Docs
description: Este artigo irá ajudá-lo a entender como a subposição da subnet do Gestor de Tráfego é usada para sobrepor o método de encaminhamento de um perfil do Gestor de Tráfego para direcionar o tráfego para um ponto final baseado no endereço IP do utilizador final através da gama IP pré-definida para mapeamentos de pontofinal usando O Azure PowerShell.
services: traffic-manager
documentationcenter: ''
author: rohinkoul
manager: twooley
ms.topic: article
ms.service: traffic-manager
ms.date: 09/18/2019
ms.author: rohink
ms.openlocfilehash: 323093ec78a9486d19496b0ee90e37cb42eea341
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/01/2020
ms.locfileid: "76938428"
---
# <a name="traffic-manager-subnet-override-using-azure-powershell"></a>Subnet do Gestor de Tráfego sobreposição usando Azure Powershell

A subposição da sub-rede do Gestor de Tráfego permite alterar o método de encaminhamento de um perfil.  A adição de uma sobreposição direcionará o tráfego com base no endereço IP do utilizador final com uma gama IP predefinida para mapeamento de ponto final. 

## <a name="how-subnet-override-works"></a>Como funciona a sobreposição da subnet

Quando as substituições da subnet são adicionadas a um perfil de gestor de tráfego, o Traffic Manager verificará primeiro se há uma subposição para o endereço IP do utilizador final. Se um for encontrado, a consulta de DNS do utilizador será direcionada para o ponto final correspondente.  Se não for encontrado um mapeamento, o Gestor de Tráfego recorrerá ao método original de encaminhamento do perfil. 

As gamas de endereços IP podem ser especificadas como gamas CIDR (por exemplo, 1.2.3.0/24) ou como intervalos de endereços (por exemplo, 1.2.3.4-5.6.7.8). As gamas IP associadas a cada ponto final devem ser únicas a esse ponto final. Qualquer sobreposição de gamas IP entre diferentes pontos finais fará com que o perfil seja rejeitado pelo Gestor de Tráfego.

Existem dois tipos de perfis de encaminhamento que suportam sobreposições de sub-rede:

* **Geographic** - Se o Gestor de Tráfego encontrar uma sub-rede sobreposição para o endereço IP da consulta DNS, irá encaminhar a consulta para o ponto final qualquer que seja a saúde do ponto final.
* **Desempenho** - Se o Gestor de Tráfego encontrar uma subposição para o endereço IP da consulta DNS, só irá encaminhar o tráfego para o ponto final se for saudável.  O Gestor de Tráfego vai voltar ao heurístico de encaminhamento de desempenho se o ponto final de subposição não for saudável.

## <a name="create-a-traffic-manager-subnet-override"></a>Criar uma sub-rede de gestor de tráfego

Para criar uma subposição de sub-rede do Gestor de Tráfego, pode utilizar o Azure PowerShell para adicionar as subredes para a sobreposição ao ponto final do Gestor de Tráfego.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Você pode executar os comandos a seguir no [Azure cloud Shell](https://shell.azure.com/powershell)ou executando o PowerShell do seu computador. O Azure Cloud Shell é um shell interativo gratuito. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. Se executar o PowerShell a partir do seu computador, precisa do módulo Azure PowerShell, 1.0.0 ou mais tarde. Pode executá`Get-Module -ListAvailable Az` para encontrar a versão instalada. Se precisar de instalar ou atualizar, veja [Install Azure PowerShell module](/powershell/azure/install-az-ps)(Instalar o módulo do Azure PowerShell). Se estiver a executar a PowerShell localmente, também precisa de correr `Login-AzAccount` para iniciar sessão no Azure.


1. **Recuperar o ponto final do Gestor de Tráfego:**

    Para ativar a sobreposição da subneta, recupere o ponto final a que pretende adicionar o sobreposição e armazene-o numa variável utilizando [o Get-AzTrafficManagerEndpoint](https://docs.microsoft.com/powershell/module/az.trafficmanager/get-aztrafficmanagerendpoint?view=azps-2.5.0).

    Substitua o Nome, Perfil e Nome do Grupo de Recursos com os valores do ponto final que está a mudar.

    ```powershell

    $TrafficManagerEndpoint = Get-AzTrafficManagerEndpoint -Name "contoso" -ProfileName "ContosoProfile" -ResourceGroupName "ResourceGroup" -Type AzureEndpoints

    ```
2. **Adicione a gama de endereços IP ao ponto final:**
    
    Para adicionar a gama de endereços IP ao ponto final, utilizará [add-AzTrafficManagerIpAddressRange](https://docs.microsoft.com/powershell/module/az.trafficmanager/add-aztrafficmanageripaddressrange?view=azps-2.5.0&viewFallbackFrom=azps-2.4.0) para adicionar o intervalo.

    ```powershell

    ### Add a range of IPs ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "1.2.3.4" -Last "5.6.7.8"

    ### Add a subnet ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "9.10.11.0" -Scope 24

    ### Add a range of IPs with a subnet ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "12.13.14.0" -Last "12.13.14.31" -Scope 27
 
    ```
    Uma vez adicionados os intervalos, utilize o [Set-AzTrafficManagerEndpoint](https://docs.microsoft.com/powershell/module/az.trafficmanager/set-aztrafficmanagerendpoint?view=azps-2.5.0) para atualizar o ponto final.

    ```powershell

    Set-AzTrafficManagerEndpoint -TrafficManagerEndpoint $TrafficManagerEndpoint

    ```
A remoção da gama de endereços IP pode ser concluída utilizando [remove-AzTrafficManagerIpAddressRange](https://docs.microsoft.com/powershell/module/az.trafficmanager/remove-aztrafficmanageripaddressrange?view=azps-2.5.0).

1.  **Recuperar o ponto final do Gestor de Tráfego:**

    Para ativar a sobreposição da subneta, recupere o ponto final a que pretende adicionar o sobreposição e armazene-o numa variável utilizando [o Get-AzTrafficManagerEndpoint](https://docs.microsoft.com/powershell/module/az.trafficmanager/get-aztrafficmanagerendpoint?view=azps-2.5.0).

    Substitua o Nome, Perfil e Nome do Grupo de Recursos com os valores do ponto final que está a mudar.

    ```powershell

    $TrafficManagerEndpoint = Get-AzTrafficManagerEndpoint -Name "contoso" -ProfileName "ContosoProfile" -ResourceGroupName "ResourceGroup" -Type AzureEndpoints

    ```
2. **Retire a gama de endereços IP do ponto final:**

    ```powershell
    
    ### Remove a range of IPs ###
    Remove-AzTrafficManagerIpAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "1.2.3.4" -Last "5.6.7.8"

    ### Remove a subnet ###
    Remove-AzTrafficManagerIpAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "9.10.11.0" -Scope 24

    ### Remove a range of IPs with a subnet ###
    Remove-AzTrafficManagerIpAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "12.13.14.0" -Last "12.13.14.31" -Scope 27

    ```
     Uma vez removidos os intervalos, utilize o [Set-AzTrafficManagerEndpoint](https://docs.microsoft.com/powershell/module/az.trafficmanager/set-aztrafficmanagerendpoint?view=azps-2.5.0) para atualizar o ponto final.

    ```powershell

    Set-AzTrafficManagerEndpoint -TrafficManagerEndpoint $TrafficManagerEndpoint

    ```

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre os [métodos de roteamento de tráfego](traffic-manager-routing-methods.md)do Traffic Manager.

Conheça o [método de encaminhamento de tráfego subnet](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods#subnet-traffic-routing-method)
