---
title: Serviço de Observação - FAQ
titleSuffix: Azure
description: Serviço de Observação - FAQ
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: reference
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 507e503b881df123ffc6694c53b0e9cc9b6a8872
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "75775475"
---
# <a name="peering-service---faqs"></a>Serviço de Observação - PERGUNTAS Frequentes

Pode rever as informações abaixo para questões gerais.

**Pode uma transportadora utilizar o seu espreitamento direto existente com a Microsoft para apoiar o Serviço de Peering?**

Sim, uma transportadora pode aproveitar o seu PNI existente para apoiar o Serviço de Peering. Um PNI de Serviço de Observação requer diversidade para apoiar o HA. Se o PNI existente já tem diversidade, então não é necessária nenhuma nova infraestrutura. Se o PNI existente precisa de diversidade, então pode ser aumentado.

**Uma transportadora pode utilizar um novo espreitamento direto com a Microsoft para suportar o Serviço de Peering?**

Sim, também é possível. A Microsoft trabalhará com a Carrier para criar um novo espreitamento direto para apoiar o Serviço de Peering.  

**Porque é que o Direct está a espreitar um requisito para apoiar o Serviço de Observação?**

Um dos principais impulsionadores por trás do Peering Service é fornecer conectividade aos serviços online da Microsoft através de um SP bem conectado. Os PNI estão sempre na gama Gbps e, portanto, um bloco de construção fundamental para uma conectividade de alta produção entre a transportadora e a Microsoft.

**Quais são os requisitos de diversidade num olhar direto para apoiar o Serviço de Observação?**

Um PNI deve apoiar o despedimento local e a geo-redundância. A redundância local é definida como dois conjuntos diversos de caminhos em um determinado local de observação. A geo-redundância requer que a Carrier tenha conectividade adicional num site de borda da Microsoft diferente, caso o site principal falhe. Para a curta duração, a transportadora pode encaminhar o tráfego através do site de reserva.

**A transportadora já oferece SLA e Internet de qualidade empresarial, como é que esta oferta é diferente?**

Algumas transportadoras oferecem SLA e Internet de qualidade empresarial na sua parte da rede. No Serviço Peering, a Microsoft oferecerá tráfego de oferta SLA na parte da rede da Microsoft. Ao selecionar o cliente peering Service obterá SLA de ponta a ponta. O SLA do seu site para a borda da Microsoft na rede ISP pode ser coberto pelo ISP. O SLA na Microsoft Global Network da Microsoft edge para a aplicação de utilizadores finais está agora coberto pela Microsoft.

**Se um prestador de serviços já está em pares com a Microsoft a usar O PNI, então que tipo de alterações são necessárias para suportar o Serviço de Peering?**

* Alterações de software para identificar um utilizador do Serviço de Observação e o seu tráfego. Pode exigir alterações na política de encaminhamento para trocar o tráfego de um utilizador na borda mais próxima da Microsoft através da ligação peering Service.
* Assegurar a conectividade tem redundância local e geo-redundância.
