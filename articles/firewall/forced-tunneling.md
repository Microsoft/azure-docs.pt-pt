---
title: Azure Firewall forçou túnel
description: Pode configurar túneis forçados para encaminhar o tráfego ligado à Internet para uma firewall adicional ou um aparelho virtual de rede para posterior processamento.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 05/11/2020
ms.author: victorh
ms.openlocfilehash: 463bccb29d59f06e7381d7d7123946029223a93a
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83199723"
---
# <a name="azure-firewall-forced-tunneling"></a>Azure Firewall forçou túnel

Pode configurar o Azure Firewall para encaminhar todo o tráfego ligado à Internet para um próximo salto designado em vez de ir diretamente para a Internet. Por exemplo, pode ter uma firewall de borda no local ou outro aparelho virtual de rede (NVA) para processar o tráfego da rede antes de ser passado para a Internet.

Por defeito, o túnel forçado não é permitido no Azure Firewall para garantir que todas as suas dependências azure de saída sejam satisfeitas. As configurações da Rota Definida do Utilizador (UDR) no *AzureFirewallSubnet* que têm uma rota padrão que não vai diretamente para a Internet são desativadas.

## <a name="forced-tunneling-configuration"></a>Configuração forçada de túneis

Para apoiar a escavação forçada, o tráfego de Gestão de Serviços é separado do tráfego do cliente. Uma subnet adicional dedicada chamada *AzureFirewallManagementSubnet* (tamanho mínimo da sub-rede /26) é necessária com o seu próprio endereço IP público associado. A única rota permitida nesta sub-rede é uma rota padrão para a Internet, e a propagação da rota BGP deve ser desativada.

Se tiver uma rota padrão anunciada via BGP para forçar o tráfego no local, deve criar o *AzureFirewallSubnet* e *o AzureFirewallManagementSubnet* antes de implementar a sua firewall e ter um UDR com uma rota padrão para a Internet, e a **propagação** da rota de gateway de rede Virtual desativada.

Dentro desta configuração, o *AzureFirewallSubnet* pode agora incluir rotas para qualquer firewall ou NVA no local para processar o tráfego antes de passar para a Internet. Também pode publicar estas rotas via BGP para *AzureFirewallSubnet* se a **propagação** da rota de gateway de rede virtual estiver ativada nesta subnet.

Por exemplo, pode criar uma rota predefinida no *AzureFirewallSubnet* com o seu gateway VPN como o próximo salto para chegar ao seu dispositivo no local. Ou pode permitir a **propagação** da rota de gateway da rede Virtual para obter as rotas apropriadas para a rede no local.

![Propagação da rota de gateway da rede virtual](media/forced-tunneling/route-propagation.png)

Uma vez configurado o Azure Firewall para suportar túneis forçados, não pode desfazer a configuração. Se remover todas as outras configurações ip na sua firewall, a configuração IP de gestão também é removida e a firewall está ser realojada. O endereço IP público atribuído à configuração IP de gestão não pode ser removido, mas pode atribuir um endereço IP público diferente.

## <a name="next-steps"></a>Passos seguintes

- [Tutorial: Implementar e configurar firewall Azure numa rede híbrida utilizando o portal Azure](tutorial-hybrid-portal.md)
