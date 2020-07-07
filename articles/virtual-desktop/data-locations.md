---
title: Localizações de dados para Windows Virtual Desktop - Azure
description: Uma breve visão geral das localizações onde os dados e metadados do Windows Virtual Desktop são armazenados.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 853f5766c4fd6344eecfd7be1d7911163133a8a5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82611539"
---
# <a name="data-locations-for-windows-virtual-desktop"></a>Localizações de dados para o Windows Virtual Desktop

>[!IMPORTANT]
>Este conteúdo aplica-se à atualização primavera 2020 com objetos de desktop virtual do Windows Manager do Azure. Se estiver a utilizar o desbloqueio virtual do Windows Desktop Fall 2019 sem objetos Azure Resource Manager, consulte [este artigo](./virtual-desktop-fall-2019/data-locations-2019.md).
>
> A atualização Virtual Desktop Spring 2020 do Windows está atualmente em pré-visualização pública. Esta versão de pré-visualização é fornecida sem um acordo de nível de serviço, e não recomendamos a sua utilização para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. 
> Para obter mais informações, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

O Windows Virtual Desktop está atualmente disponível para todas as localizações geográficas. Os administradores podem escolher a localização para armazenar os dados dos utilizadores quando criam as máquinas virtuais do pool anfitrião e os serviços associados, tais como servidores de ficheiros. Saiba mais sobre geografias Azure no mapa do [datacenter Azure.](https://azuredatacentermap.azurewebsites.net/)

>[!NOTE]
>A Microsoft não controla nem limita as regiões onde você ou os seus utilizadores podem aceder aos seus dados específicos do utilizador e da aplicação.

>[!IMPORTANT]
>O Windows Virtual Desktop armazena informações globais de metadados como nomes de inquilinos, nomes de piscinas de anfitriões, nomes de grupos de aplicações e nomes principais de utilizadores num datacenter. Sempre que um cliente cria um objeto de serviço, deve entrar num local para o objeto de serviço. A localização em que entram determina onde os metadados do objeto serão armazenados. O cliente escolherá uma região Azure e os metadados serão armazenados na geografia relacionada. Para obter uma lista de todas as regiões azures e geografias relacionadas, consulte [geografias Azure.](https://azure.microsoft.com/global-infrastructure/geographies/)

Neste momento, só apoiamos o armazenamento de metadados na geografia Azure dos Estados Unidos (EUA). Os metadados armazenados são encriptados em repouso, e os espelhos geo-redundantes são mantidos dentro da geografia. Todos os dados do cliente, tais como configurações de aplicações e dados do utilizador, residem no local que o cliente escolhe e não é gerido pelo serviço. Mais geografias ficarão disponíveis à medida que o serviço cresce.

Os metadados de serviço são replicados dentro da geografia Azure para fins de recuperação de desastres.