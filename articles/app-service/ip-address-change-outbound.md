---
title: Preparar para alterar o endereço IP de saída
description: Se o endereço IP de saída for alterado, saiba o que fazer para que seu aplicativo continue a funcionar após a alteração.
ms.topic: article
ms.date: 06/28/2018
ms.custom: seodec18
ms.openlocfilehash: 2be4bc92dde278b054bd04f412f937440027ece7
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74671671"
---
# <a name="how-to-prepare-for-an-outbound-ip-address-change"></a>Como preparar para uma alteração de endereço IP de saída

Se você recebeu uma notificação de que os endereços IP de saída do seu aplicativo de serviço Azure App estão sendo alterados, siga as instruções neste artigo.

## <a name="determine-if-you-have-to-do-anything"></a>Determine se você precisa fazer algo

* Opção 1: se o aplicativo do serviço de aplicativo não usar a filtragem de IP, uma lista de inclusão explícita ou um tratamento especial de tráfego de saída, como roteamento ou firewall, nenhuma ação será necessária.

* Opção 2: se seu aplicativo tiver tratamento especial para os endereços IP de saída (veja os exemplos abaixo), adicione os novos endereços IP de saída sempre que os existentes forem exibidos. Não substitua os endereços IP existentes. Você pode encontrar os novos endereços IP de saída seguindo as instruções na próxima seção.

  Por exemplo, um endereço IP de saída pode ser incluído explicitamente em um firewall fora do seu aplicativo, ou um serviço de pagamento externo pode ter uma lista de permissões que contém o endereço IP de saída para seu aplicativo. Se o endereço de saída estiver configurado em uma lista em qualquer lugar fora do aplicativo, isso precisará ser alterado.

## <a name="find-the-outbound-ip-addresses-in-the-azure-portal"></a>Localize os endereços IP de saída no portal do Azure

Os novos endereços IP de saída são mostrados no portal antes de entrarem em vigor. Quando o Azure começar a usar os novos, os antigos não serão mais usados. Apenas um conjunto de cada vez é usado, de modo que as entradas nas listas de inclusão devem ter endereços IP antigos e novos para evitar uma interrupção quando ocorrer a mudança. 

1.  Abra o [Portal do Azure](https://portal.azure.com).

2.  No menu de navegação à esquerda, selecione **serviços de aplicativos**.

3.  Selecione o aplicativo do serviço de aplicativo na lista.

1.  Se o aplicativo for um aplicativo de funções, consulte [endereços IP de saída do aplicativo de funções](../azure-functions/ip-addresses.md#find-outbound-ip-addresses).

4.  No cabeçalho **configurações** , clique em **Propriedades** no painel de navegação esquerdo e localize a seção denominada **endereços IP de saída**.

5. Copie os endereços IP e adicione-os à sua manipulação especial de tráfego de saída, como um filtro ou uma lista de permissões. Não exclua os endereços IP existentes na lista.

## <a name="next-steps"></a>Passos seguintes

Este artigo explicou como se preparar para uma alteração de endereço IP que foi iniciada pelo Azure. Para obter mais informações sobre endereços IP no serviço Azure App, consulte [endereços IP de entrada e saída no serviço Azure app](overview-inbound-outbound-ips.md).
