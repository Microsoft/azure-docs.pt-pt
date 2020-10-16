---
title: Localizações de dados para Windows Virtual Desktop - Azure
description: Uma breve visão geral das localizações onde os dados e metadados do Windows Virtual Desktop são armazenados.
author: Heidilohr
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: e626b7e729e394b1012848904f5ce12279c3ef24
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88010077"
---
# <a name="data-and-metadata-locations-for-windows-virtual-desktop"></a>Localizações de dados e metadados para o Windows Virtual Desktop

>[!IMPORTANT]
>Este conteúdo aplica-se ao Windows Virtual Desktop com objetos de ambiente de trabalho virtuais do Windows, gestor de recursos do Azure. Se estiver a utilizar o Windows Virtual Desktop (clássico) sem objetos Azure Resource Manager, consulte [este artigo](./virtual-desktop-fall-2019/data-locations-2019.md).

O Windows Virtual Desktop está atualmente disponível para todas as localizações geográficas. Os administradores podem escolher a localização para armazenar os dados dos utilizadores quando criam as máquinas virtuais do pool anfitrião e os serviços associados, tais como servidores de ficheiros. Saiba mais sobre geografias Azure no mapa do [datacenter Azure.](https://azuredatacentermap.azurewebsites.net/)

>[!NOTE]
>A Microsoft não controla nem limita as regiões onde você ou os seus utilizadores podem aceder aos seus dados específicos do utilizador e da aplicação.

>[!IMPORTANT]
>O Windows Virtual Desktop armazena informações globais de metadados como nomes de inquilinos, nomes de piscinas de anfitriões, nomes de grupos de aplicações e nomes principais de utilizadores num datacenter. Sempre que um cliente cria um objeto de serviço, deve entrar num local para o objeto de serviço. A localização em que entram determina onde os metadados do objeto serão armazenados. O cliente escolherá uma região Azure e os metadados serão armazenados na geografia relacionada. Para obter uma lista de todas as regiões azures e geografias relacionadas, consulte [geografias Azure.](https://azure.microsoft.com/global-infrastructure/geographies/)

Neste momento, só apoiamos o armazenamento de metadados na geografia Azure dos Estados Unidos (EUA). Os metadados armazenados são encriptados em repouso, e os espelhos geo-redundantes são mantidos dentro da geografia. Todos os dados do cliente, tais como configurações de aplicações e dados do utilizador, residem no local que o cliente escolhe e não é gerido pelo serviço. Mais geografias ficarão disponíveis à medida que o serviço cresce.

Os metadados de serviço são replicados dentro da geografia Azure para fins de recuperação de desastres.