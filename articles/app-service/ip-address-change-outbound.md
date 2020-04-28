---
title: Preparar para a alteração do endereço IP de saída
description: Se o seu endereço IP de saída for alterado, saiba o que fazer para que a sua aplicação continue a funcionar após a mudança.
ms.topic: article
ms.date: 06/28/2018
ms.custom: seodec18
ms.openlocfilehash: 2be4bc92dde278b054bd04f412f937440027ece7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74671671"
---
# <a name="how-to-prepare-for-an-outbound-ip-address-change"></a>Como preparar-se para uma mudança de endereço IP de saída

Se recebeu uma notificação de que os endereços IP de saída da sua aplicação Azure App Service estão a mudar, siga as instruções deste artigo.

## <a name="determine-if-you-have-to-do-anything"></a>Determine se tem que fazer alguma coisa.

* Opção 1: Se a sua aplicação app Service não utilizar a filtragem IP, uma lista de inclusão explícita ou um manuseamento especial do tráfego de saída, como encaminhamento ou firewall, não é necessária qualquer ação.

* Opção 2: Se a sua aplicação tiver um manuseamento especial para os endereços IP de saída (ver exemplos abaixo), adicione os novos endereços IP de saída onde quer que os existentes apareçam. Não substitua os endereços IP existentes. Pode encontrar os novos endereços IP de saída seguindo as instruções na secção seguinte.

  Por exemplo, um endereço IP de saída pode ser explicitamente incluído numa firewall fora da sua aplicação, ou um serviço de pagamento externo pode ter uma lista permitida que contenha o endereço IP de saída para a sua aplicação. Se o seu endereço de saída estiver configurado numa lista fora da sua aplicação, isso tem de ser alterado.

## <a name="find-the-outbound-ip-addresses-in-the-azure-portal"></a>Encontre os endereços IP de saída no portal Azure

Os novos endereços IP de saída são mostrados no portal antes de produzirem efeito. Quando o Azure começar a usar os novos, os antigos deixarão de ser utilizados. Apenas um conjunto de cada vez é utilizado, pelo que as inscrições nas listas de inclusão devem ter endereços IP antigos e novos para evitar uma interrupção quando o interruptor acontece. 

1.  Abra o [portal Azure.](https://portal.azure.com)

2.  No menu de navegação à esquerda, selecione Serviços de **Aplicações**.

3.  Selecione a sua aplicação App Service da lista.

1.  Se a aplicação for uma aplicação de função, consulte os [endereços IP de saída](../azure-functions/ip-addresses.md#find-outbound-ip-addresses)da aplicação Function .

4.  No cabeçalho **Definições,** clique em **Propriedades** na navegação à esquerda e encontre a secção etiquetada **endereços IP outbound**.

5. Copie os endereços IP e adicione-os ao seu manuseamento especial de tráfego de saída, como um filtro ou lista permitida. Não elimine os endereços IP existentes na lista.

## <a name="next-steps"></a>Passos seguintes

Este artigo explicou como se preparar para uma mudança de endereço IP que foi iniciada pelo Azure. Para obter mais informações sobre endereços IP no Serviço de Aplicações Azure, consulte endereços IP de entrada e saída no Serviço de [Aplicações Azure](overview-inbound-outbound-ips.md).
