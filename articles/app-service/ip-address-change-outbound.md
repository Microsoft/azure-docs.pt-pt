---
title: Preparar a alteração de endereço IP de saída - serviço de aplicações do Azure
description: Se o seu endereço IP de saída vai ser alterado, saiba o que fazer para que a sua aplicação continua a funcionar após a alteração.
services: app-service\web
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.topic: article
ms.date: 06/28/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: ac62217af096653d61a79ff29ae352c8e950f8af
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61269788"
---
# <a name="how-to-prepare-for-an-outbound-ip-address-change"></a>Como se preparar para uma alteração de endereço IP saída

Se tiver recebido uma notificação que estão mudando os endereços IP de saída da sua aplicação de serviço de aplicações do Azure, siga as instruções neste artigo.

## <a name="determine-if-you-have-to-do-anything"></a>Determinar se tem de fazer nada

* Opção 1: Se a sua aplicação de serviço de aplicações não utilizar a filtragem de IP, uma lista de inclusão explícita ou tratamento especial de tráfego de saída como encaminhamento ou firewall, é necessária nenhuma ação.

* Opção 2: Se a sua aplicação tiver um tratamento especial para os endereços IP de saída (veja exemplos abaixo), adicione os novos endereços IP de saída, onde quer que os existentes são apresentados. Não substitua os endereços IP existentes. Pode encontrar os novos endereços IP de saída ao seguir as instruções na secção seguinte.

  Por exemplo, um endereço IP de saída pode ser explicitamente incluído numa firewall fora da sua aplicação ou um serviço de pagamento externo pode ter uma lista de permitidos que contém o endereço IP de saída para a sua aplicação. Se o seu endereço de saída está configurado numa lista em qualquer lugar fora da sua aplicação, isso precisa mudar.

## <a name="find-the-outbound-ip-addresses-in-the-azure-portal"></a>Localizar os endereços IP de saída no portal do Azure

Os novos endereços IP de saída são apresentados no portal do antes que elas entrem em vigor. Quando o Azure começa a utilizar as novas, os antigos já não serão utilizados. Apenas um conjunto cada vez, é utilizado pelo entradas nas listas de inclusão tem de ter antigos e novos endereços IP para evitar um período de indisponibilidade quando ocorre o comutador. 

1.  Abra o [Portal do Azure](https://portal.azure.com).

2.  No menu de navegação esquerdo, selecione **dos serviços de aplicações**.

3.  Selecione a sua aplicação de serviço de aplicações na lista.

1.  Se a aplicação for uma aplicação de funções, veja [endereços IP de saída de aplicação de função](../azure-functions/ip-addresses.md#find-outbound-ip-addresses).

4.  Sob o **configurações** cabeçalho, clique em **propriedades** na navegação à esquerda e localize a seção rotulada **endereços IP de saída**.

5. Copie os endereços IP e adicioná-los para sua manipulação especial de tráfego de saída, como um filtro ou lista de permitidos. Não elimine os endereços IP existentes na lista.

## <a name="next-steps"></a>Passos Seguintes

Este artigo explicou como se preparar para uma alteração de endereço IP que foi iniciada pelo Azure. Para obter mais informações sobre os endereços IP no serviço de aplicações do Azure, consulte [de entrada e endereços IP de saída no serviço de aplicações do Azure](overview-inbound-outbound-ips.md).
