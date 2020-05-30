---
title: Firewall Azure forçou o túnel
description: Pode configurar um túnel forçado para encaminhar o tráfego ligado à Internet para uma firewall adicional ou um aparelho virtual de rede para posterior processamento.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 05/18/2020
ms.author: victorh
ms.openlocfilehash: d9afb93611712109d5e8fcc8a686f4f9196f3396
ms.sourcegitcommit: 0fa52a34a6274dc872832560cd690be58ae3d0ca
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/29/2020
ms.locfileid: "84204043"
---
# <a name="azure-firewall-forced-tunneling"></a>Firewall Azure forçou o túnel

Ao configurar um novo Azure Firewall, pode encaminhar todo o tráfego ligado à Internet para um próximo salto designado em vez de ir diretamente para a Internet. Por exemplo, pode ter uma firewall de borda no local ou outro aparelho virtual de rede (NVA) para processar o tráfego de rede antes de ser passado para a Internet. No entanto, não se pode configurar uma firewall existente para fazer túneis forçados.

Por defeito, os túneis forçados não são permitidos na Firewall de Azure para garantir que todas as suas dependências de Azure de saída sejam satisfeitas. As configurações da Rota Definida pelo Utilizador (UDR) na *AzureFirewallSubnet* que têm uma rota padrão que não vai diretamente para a Internet são desativadas.

## <a name="forced-tunneling-configuration"></a>Configuração de túneis forçados

Para apoiar a escavação forçada, o tráfego de Gestão de Serviços é separado do tráfego do cliente. Uma sub-rede adicional dedicada chamada *AzureFirewallManagementSubnet* (tamanho mínimo da sub-rede /26) é necessária com o seu próprio endereço IP público associado. A única rota permitida nesta sub-rede é uma rota predefinida para a Internet, e a propagação da rota BGP deve ser desativada.

Se tiver uma rota predefinida anunciada via BGP para forçar o tráfego para as instalações, tem de criar a *AzureFirewallSubnet* e *a AzureFirewallManagementSubnet* antes de implantar a sua firewall e ter um UDR com uma rota padrão para a Internet e **propagação da rota de gateway de rede virtual** desativada.

Dentro desta configuração, o *AzureFirewallSubnet* pode agora incluir rotas para qualquer firewall no local ou NVA para processar o tráfego antes de ser passado para a Internet. Também pode publicar estas rotas via BGP para *AzureFirewallSubnet* se a **propagação da rota de gateway de rede virtual** estiver ativada nesta sub-rede.

Por exemplo, pode criar uma rota padrão na *AzureFirewallSubnet* com o seu gateway VPN como o próximo salto para chegar ao seu dispositivo no local. Ou pode permitir a **propagação da rota de gateway de rede virtual** para obter as rotas apropriadas para a rede no local.

![Propagação da rota do gateway de rede virtual](media/forced-tunneling/route-propagation.png)

Uma vez configurar o Azure Firewall para suportar o túnel forçado, não pode desfazer a configuração. Se remover todas as outras configurações IP na sua firewall, a configuração IP de gestão também é removida e a firewall élocada. O endereço IP público atribuído à configuração IP de gestão não pode ser removido, mas pode atribuir um endereço IP público diferente.

## <a name="next-steps"></a>Passos seguintes

- [Tutorial: Implementar e configurar a Firewall Azure numa rede híbrida utilizando o portal Azure](tutorial-hybrid-portal.md)
