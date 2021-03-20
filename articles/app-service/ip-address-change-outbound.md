---
title: Preparar para alteração de endereço IP de saída
description: Se o seu endereço IP de saída for alterado, saiba o que fazer para que a sua aplicação continue a funcionar após a mudança.
ms.topic: article
ms.date: 06/28/2018
ms.custom: seodec18
ms.openlocfilehash: 2be4bc92dde278b054bd04f412f937440027ece7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "74671671"
---
# <a name="how-to-prepare-for-an-outbound-ip-address-change"></a>Como se preparar para uma mudança de endereço IP de saída

Se recebeu a notificação de que os endereços IP de saída da sua aplicação Azure App Service estão a ser alterados, siga as instruções deste artigo.

## <a name="determine-if-you-have-to-do-anything"></a>Determine se tem que fazer alguma coisa

* Opção 1: Se a sua aplicação 'App Service' não utilizar filtragem IP, uma lista de inclusão explícita ou manuseamento especial do tráfego de saída, como encaminhamento ou firewall, não é necessária qualquer ação.

* Opção 2: Se a sua aplicação tiver um manuseamento especial para os endereços IP de saída (ver exemplos abaixo), adicione os novos endereços IP de saída onde quer que os existentes apareçam. Não substitua os endereços IP existentes. Pode encontrar os novos endereços IP de saída seguindo as instruções na secção seguinte.

  Por exemplo, um endereço IP de saída pode ser explicitamente incluído numa firewall fora da sua aplicação, ou um serviço de pagamento externo pode ter uma lista permitida que contenha o endereço IP de saída para a sua aplicação. Se o seu endereço de saída estiver configurado numa lista em qualquer lugar fora da sua aplicação, isso precisa de ser alterado.

## <a name="find-the-outbound-ip-addresses-in-the-azure-portal"></a>Encontre os endereços IP de saída no portal Azure

Os novos endereços IP de saída são apresentados no portal antes de produzirem efeitos. Quando O Azure começar a usar os novos, os antigos deixarão de ser usados. Apenas um conjunto de cada vez é utilizado, pelo que as inscrições nas listas de inclusão devem ter endereços IP antigos e novos para evitar uma paragem quando o interruptor acontece. 

1.  Abra o [portal do Azure](https://portal.azure.com).

2.  No menu de navegação à esquerda, selecione **Serviços de Aplicações**.

3.  Selecione a sua aplicação De Serviço de Aplicações a partir da lista.

1.  Se a aplicação for uma aplicação de função, consulte [endereços IP de saída de aplicações da Função.](../azure-functions/ip-addresses.md#find-outbound-ip-addresses)

4.  No cabeçalho **Definições,** clique em **Propriedades** na navegação à esquerda e encontre os **endereços IP de saída com** a etiqueta .

5. Copie os endereços IP e adicione-os ao seu manuseamento especial do tráfego de saída, como um filtro ou uma lista permitida. Não elimine os endereços IP existentes na lista.

## <a name="next-steps"></a>Passos seguintes

Este artigo explicou como se preparar para uma alteração de endereço IP que foi iniciada pela Azure. Para obter mais informações sobre endereços IP no Azure App Service, consulte [endereços IP de entrada e saída no Serviço de Aplicações Azure.](overview-inbound-outbound-ips.md)
