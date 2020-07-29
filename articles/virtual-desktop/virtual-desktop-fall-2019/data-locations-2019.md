---
title: Localizações de dados para Windows Virtual Desktop (clássico) - Azure
description: Uma breve visão geral das localizações em que os dados e metadados virtuais do Windows Desktop (clássico) são armazenados.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 0e12e17914374a64ecd2d17a8e73c6ebf20328d9
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87270044"
---
# <a name="data-locations-for-windows-virtual-desktop-classic"></a>Localizações de dados para Windows Virtual Desktop (clássico)

>[!IMPORTANT]
>Este conteúdo aplica-se ao Windows Virtual Desktop (clássico), que não suporta objetos de desktop virtual do Gestor de Recursos Azure Windows. Se estiver a tentar gerir os objetos de ambiente de trabalho virtuais do Azure Resource Manager Windows, consulte [este artigo](../data-locations.md).

O Windows Virtual Desktop está atualmente disponível para todas as localizações geográficas. Inicialmente, os metadados de serviço só podem ser armazenados na geografia dos Estados Unidos (EUA). Os administradores podem escolher a localização para armazenar os dados dos utilizadores quando criam as máquinas virtuais do pool anfitrião e os serviços associados, tais como servidores de ficheiros. Saiba mais sobre geografias Azure no mapa do [datacenter Azure.](https://azuredatacentermap.azurewebsites.net/)

>[!NOTE]
>A Microsoft não controla nem limita as regiões onde você ou os seus utilizadores podem aceder aos seus dados específicos do utilizador e da aplicação.

>[!IMPORTANT]
>O Windows Virtual Desktop armazena informações globais de metadados como nomes de inquilinos, nomes de piscinas de anfitriões, nomes de grupos de aplicações e nomes principais de utilizadores num datacenter localizado nos Estados Unidos. Os metadados armazenados são encriptados em repouso, e os espelhos geo-redundantes são mantidos nos Estados Unidos. Todos os dados do cliente, tais como configurações de aplicações e dados do utilizador, residem no local que o cliente escolhe e não é gerido pelo serviço.

Os metadados de serviço são replicados nos Estados Unidos para fins de recuperação de desastres.