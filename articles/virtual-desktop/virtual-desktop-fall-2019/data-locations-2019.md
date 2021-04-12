---
title: Localizações de dados para Windows Virtual Desktop (clássico) - Azure
description: Uma breve visão geral das localizações em que os dados e metadados virtuais do Windows Desktop (clássico) são armazenados.
author: Heidilohr
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 7726795582ffa3d85601ec123b58ab705442ef67
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106445010"
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