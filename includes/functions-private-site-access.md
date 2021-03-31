---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/06/2020
ms.author: glenga
ms.openlocfilehash: 3c9679f3d66d58c7a6c847b54c84438c979ecd39
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97936871"
---
O [Ponto Final Privado do Azure](../articles/private-link/private-endpoint-overview.md) é uma interface de rede que o liga em privado e com segurança a um serviço com a tecnologia do Azure Private Link.  O Ponto Final Privado utiliza um endereço IP privado a partir da rede virtual, o que leva de forma eficaz o serviço até à sua rede virtual.

Pode utilizar o Private Endpoint para as suas funções hospedadas nos planos [Premium](../articles/azure-functions/functions-premium-plan.md) e [App Service.](../articles/azure-functions/dedicated-plan.md)

Ao criar uma ligação de ponto final privado de entrada para funções, também necessitará de um registo DNS para resolver o endereço privado.  Por predefinição, será criado um registo de DNS privado para si ao criar um ponto final privado utilizando o portal Azure.

Para saber mais, consulte [a utilização de Pontos Finais Privados para Aplicações Web.](../articles/app-service/networking/private-endpoint.md)