---
author: memildin
ms.author: memildin
manager: rkarlin
ms.date: 11/22/2020
ms.topic: include
ms.openlocfilehash: 30de9181fd23a29b28973d01899f0b23fca3ae89
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95530003"
---
## <a name="availability"></a>Disponibilidade

|Aspeto|Detalhes|
|----|:----|
|Estado de libertação:|Geralmente disponível (GA)|
|Preços:|**Azure Defender para registos de contentores** é faturado como mostrado [na página de preços](../articles/security-center/security-center-pricing.md)|
|Registos e imagens suportados:|Imagens linux em registos ACR acessíveis a partir da internet pública com acesso a conchas|
|Registos e imagens não suportados:|Imagens do Windows<br>Registos "privados"<br>Registos com acesso limitado com firewall, ponto final de serviço ou pontos finais privados, tais como Azure Private Link<br>Imagens super minimalistas como imagens [de rascunho de Docker,](https://hub.docker.com/_/scratch/) ou imagens "distroless" que contêm apenas uma aplicação e suas dependências de tempo de execução sem um gestor de pacotes, concha ou SO|
|Funções e permissões necessárias:|**Funções** [e permissões de registo de contentores de segurança e Azure](../articles/container-registry/container-registry-roles.md)|
|Nuvens:|:::image type="icon" source="../articles/security-center/media/icons/yes-icon.png" border="false"::: Nuvens comerciais<br>:::image type="icon" source="../articles/security-center/media/icons/yes-icon.png" border="false"::: US Gov - Apenas a digitalização na função push é suportada atualmente. Saiba mais em [Quando as imagens são digitalizadas?](../articles/security-center/defender-for-container-registries-introduction.md#when-are-images-scanned)<br>:::image type="icon" source="../articles/security-center/media/icons/no-icon.png" border="false"::: China Gov, Outro Gov|
|||