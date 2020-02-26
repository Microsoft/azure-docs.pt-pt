---
title: Azure Firewall forçou túnel
description: Pode configurar túneis forçados para encaminhar o tráfego ligado à Internet para uma firewall adicional ou um aparelho virtual de rede para posterior processamento.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 02/24/2020
ms.author: victorh
ms.openlocfilehash: e51f6de370a5340082f64a0ca15c61583f75962b
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77597286"
---
# <a name="azure-firewall-forced-tunneling-preview"></a>Azure Firewall obrigou a túnel (pré-visualização)

Pode configurar o Azure Firewall para encaminhar todo o tráfego ligado à Internet para um próximo salto designado em vez de ir diretamente para a Internet. Por exemplo, pode ter uma firewall de borda no local ou outro aparelho virtual de rede (NVA) para processar o tráfego da rede antes de ser passado para a Internet.

> [!IMPORTANT]
> O túnel forçado da Azure Firewall está atualmente em pré-visualização pública.
>
> Esta pré-visualização pública é disponibilizada sem um contrato de nível de serviço e não deve ser utilizada para cargas de trabalho de produção. Algumas funcionalidades podem não ser suportadas, podem ter capacidades restringidas ou podem não estar disponíveis em todas as localizações do Azure. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Por defeito, o túnel forçado não é permitido no Azure Firewall para garantir que todas as suas dependências azure de saída sejam satisfeitas. As configurações da Rota Definida do Utilizador (UDR) no *AzureFirewallSubnet* que têm uma rota padrão que não vai diretamente para a Internet são desativadas.

## <a name="forced-tunneling-configuration"></a>Configuração forçada de túneis

Para apoiar a escavação forçada, o tráfego de gestão de serviços é separado do tráfego do cliente. Uma subnet adicional dedicada chamada *AzureFirewallManagementSubnet* (tamanho mínimo da sub-rede /26) é necessária com o seu próprio endereço IP público associado. A única rota permitida nesta sub-rede é uma rota padrão para a Internet, e a propagação da rota BGP deve ser desativada.

Se tiver uma rota padrão anunciada via BGP para forçar o tráfego no local, deve criar o *AzureFirewallSubnet* e *o AzureFirewallManagementSubnet* antes de implementar a sua firewall e ter um UDR com uma rota padrão para a Internet, e a **propagação** da rota de gateway de rede Virtual desativada.

Dentro desta configuração, o *AzureFirewallSubnet* pode agora incluir rotas para qualquer firewall ou NVA no local para processar o tráfego antes de passar para a Internet. Também pode publicar estas rotas via BGP para *AzureFirewallSubnet* se a **propagação** da rota de gateway de rede virtual estiver ativada nesta subnet.

Por exemplo, pode criar uma rota predefinida no *AzureFirewallSubnet* com o seu gateway VPN como o próximo salto para chegar ao seu dispositivo no local. Ou pode permitir a **propagação** da rota de gateway da rede Virtual para obter as rotas apropriadas para a rede no local.

![Propagação da rota de gateway da rede virtual](media/forced-tunneling/route-propagation.png)

Uma vez configurado o Azure Firewall para suportar túneis forçados, não pode desfazer a configuração. Se remover todas as outras configurações ip na sua firewall, a configuração IP de gestão também é removida e a firewall está ser realojada. O endereço IP público atribuído à configuração IP de gestão não pode ser removido, mas pode atribuir um endereço IP público diferente.

## <a name="next-steps"></a>Passos seguintes

- [Tutorial: Implementar e configurar firewall Azure numa rede híbrida utilizando o portal Azure](tutorial-hybrid-portal.md)