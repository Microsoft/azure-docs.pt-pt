---
author: memildin
ms.author: memildin
manager: rkarlin
ms.date: 11/22/2020
ms.topic: include
ms.openlocfilehash: 2112cde42ee00b78a82962ee46f53110068977c0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98186993"
---
## <a name="availability"></a>Disponibilidade

|Aspeto|Detalhes|
|----|:----|
|Estado de libertação:|Geralmente disponível (GA)|
|Preços:|**Azure Defender para registos de contentores** é faturado como mostrado [na página de preços](../articles/security-center/security-center-pricing.md)|
|Registos e imagens suportados:|Imagens linux em registos ACR acessíveis a partir da internet pública com acesso a conchas|
|Registos e imagens não suportados:|Imagens do Windows<br>Registos "privados"<br>Registos com acesso limitado com firewall, ponto final de serviço ou pontos finais privados, tais como Azure Private Link<br>Imagens super minimalistas como imagens [de rascunho de Docker,](https://hub.docker.com/_/scratch/) ou imagens "distroless" que contêm apenas uma aplicação e suas dependências de tempo de execução sem um gestor de pacotes, concha ou SO|
|Funções e permissões necessárias:|**Funções** [e permissões de registo de contentores de segurança e Azure](../articles/container-registry/container-registry-roles.md)|
|Nuvens:|:::image type="icon" source="../articles/security-center/media/icons/yes-icon.png" border="false"::: Nuvens comerciais<br>:::image type="icon" source="../articles/security-center/media/icons/yes-icon.png" border="false"::: EUA Gov e China Gov - Apenas a digitalização na funcionalidade push é atualmente suportada. Saiba mais em [Quando as imagens são digitalizadas?](../articles/security-center/defender-for-container-registries-introduction.md#when-are-images-scanned)|
|||