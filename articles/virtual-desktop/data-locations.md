---
title: Localizações de dados para Windows Virtual Desktop - Azure
description: Uma breve visão geral das localizações onde os dados e metadados do Windows Virtual Desktop são armazenados.
author: Heidilohr
ms.topic: conceptual
ms.custom: references_regions
ms.date: 02/17/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: a4c63cc686b08d179e20e6f3e3a7aa1efa69a5f8
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106447085"
---
# <a name="data-and-metadata-locations-for-windows-virtual-desktop"></a>Localizações de dados e metadados para o Windows Virtual Desktop

>[!IMPORTANT]
>Este conteúdo aplica-se ao Windows Virtual Desktop com objetos de ambiente de trabalho virtuais do Windows, gestor de recursos do Azure. Se estiver a utilizar o Windows Virtual Desktop (clássico) sem objetos Azure Resource Manager, consulte [este artigo](./virtual-desktop-fall-2019/data-locations-2019.md).

O Windows Virtual Desktop está atualmente disponível para todas as localizações geográficas. Os administradores podem escolher a localização para armazenar os dados dos utilizadores quando criam as máquinas virtuais do pool anfitrião e os serviços associados, tais como servidores de ficheiros. Saiba mais sobre geografias Azure no mapa do [datacenter Azure.](https://azuredatacentermap.azurewebsites.net/)

>[!NOTE]
>A Microsoft não controla nem limita as regiões onde você ou os seus utilizadores podem aceder aos seus dados específicos do utilizador e da aplicação.

>[!IMPORTANT]
>O Windows Virtual Desktop armazena informações globais de metadados como nomes de inquilinos, nomes de piscinas de anfitriões, nomes de grupos de aplicações e nomes principais de utilizadores num datacenter. Sempre que um cliente cria um objeto de serviço, deve entrar num local para o objeto de serviço. A localização em que entram determina onde os metadados do objeto serão armazenados. O cliente escolherá uma região Azure e os metadados serão armazenados na geografia relacionada. Para obter uma lista de todas as regiões azures e geografias relacionadas, consulte [geografias Azure.](https://azure.microsoft.com/global-infrastructure/geographies/)

Atualmente apoiamos o armazenamento de metadados nas seguintes geografias:

- Estados Unidos (EUA) (Geralmente disponíveis)
- Europa (UE) (Visualização pública) 

>[!NOTE]
> Ao selecionar uma região para criar objetos de serviço de desktop virtual do Windows, verá regiões sob geografias dos EUA e da UE. Para se certificar de que entende qual a região que funcionaria melhor para a sua implantação, veja o [nosso mapa de infraestruturas globais do Azure.](https://azure.microsoft.com/global-infrastructure/geographies/#geographies)

Os metadados armazenados são encriptados em repouso, e os espelhos geo-redundantes são mantidos dentro da geografia. Todos os dados do cliente, tais como configurações de aplicações e dados do utilizador, residem no local que o cliente escolhe e não é gerido pelo serviço. Mais geografias ficarão disponíveis à medida que o serviço cresce.

Os metadados de serviço são replicados dentro da geografia Azure para fins de recuperação de desastres.