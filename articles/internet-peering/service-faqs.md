---
title: Serviço de Peering - FAQ
titleSuffix: Azure
description: Serviço de Peering - FAQ
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: reference
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 507e503b881df123ffc6694c53b0e9cc9b6a8872
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75775475"
---
# <a name="peering-service---faqs"></a>Serviço de Peering - FAQs

Pode rever as informações abaixo para questões gerais.

**Pode uma transportadora que usa o seu direct peering existente com a Microsoft para suportar o Serviço de Peering?**

Sim, uma transportadora pode aproveitar o seu PNI existente para apoiar o Serviço de Peering. Um Serviço de Peering PNI requer diversidade para apoiar ha. Se o PNI existente já tem diversidade, então não é necessária uma nova infraestrutura. Se o PNI existente necessitar de diversidade, então pode ser aumentado.

**Uma transportadora pode usar novos pares diretos com a Microsoft para suportar o Serviço de Peering?**

Sim, também é possível. A Microsoft trabalhará com a Carrier para criar um novo peering direto para suportar o Serviço de Peering.  

**Porque é que o Direct está a espiar um requisito para apoiar o Serviço de Peering?**

Um dos principais impulsionadores por detrás do Peering Service é fornecer conectividade aos serviços online da Microsoft através de um SP bem conectado. O PNI está sempre na gama Gbps e, portanto, um bloco de construção fundamental para uma conectividade de alta capacidade de entrada entre a transportadora e a Microsoft.

**Quais são os requisitos de diversidade num direct peering para apoiar o Serviço de Peering?**

Um PNI deve apoiar a redundância local e a redundância geo-redundância. O despedimento local é definido como dois conjuntos diversificados de caminhos num determinado local de observação. A geo-redundância requer que a Carrier tenha conectividade adicional num site de borda seletiva da Microsoft diferente, caso o site primário falhe. Para a curta duração da avaria, a transportadora pode encaminhar o tráfego através do local de reserva.

**A transportadora já oferece SLA e Internet de nível empresarial, como é que esta oferta é diferente?**

Algumas transportadoras oferecem SLA e Internet de nível empresarial na sua parte da rede. No Peering Service, a Microsoft oferecerá ao SLA oferecer tráfego na Microsoft parte da rede. Ao selecionar o cliente do Peering Service obterá SLA de ponta a ponta. O SLA do seu site para a microsoft edge na rede ISP pode ser coberto pelo ISP. A Aplicação SLA na Microsoft Global Network, da Microsoft Edge para a aplicação de utilizadores finais, está agora coberta pela Microsoft.

**Se um prestador de serviços já faz par com a Microsoft usando o PNI, que tipo de alterações são necessárias para suportar o Serviço de Peering?**

* Alterações de software para identificar um utilizador do Serviço Peering e o seu tráfego. Pode exigir alterações na política de encaminhamento para trocar o tráfego de um utilizador na extremidade mais próxima da Microsoft através da ligação peering Service.
* Certifique-se de que a conectividade tem redundância local e geo-redundância.
