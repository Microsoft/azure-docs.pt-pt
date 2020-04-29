---
title: Localizações de dados para Windows Virtual Desktop - Azure
description: Uma breve visão geral das localizações que localiza os dados e metadados do Windows Virtual Desktop são armazenados.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 5f48586f38c310c77c61f470d3e2fbc819c33f71
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79128051"
---
# <a name="data-locations-for-windows-virtual-desktop"></a>Localizações de dados para Windows Virtual Desktop

O Windows Virtual Desktop está atualmente disponível para todos os locais geográficos. Inicialmente, os metadados de serviço só podem ser armazenados na geografia dos Estados Unidos (EUA). Os administradores podem escolher a localização para armazenar dados dos utilizadores quando criam o conjunto de anfitriões máquinas virtuais e serviços associados, como servidores de ficheiros. Saiba mais sobre geografias Azure no mapa do [datacenter azure.](https://azuredatacentermap.azurewebsites.net/)

>[!NOTE]
>A Microsoft não controla nem limita as regiões onde você ou os seus utilizadores podem aceder aos seus dados específicos de utilizador e aplicações.

>[!IMPORTANT]
>O Windows Virtual Desktop armazena informações globais de metadados como nomes de inquilinos, nomes de piscinas, nomes de grupos de aplicações e nomes principais do utilizador num datacenter localizado nos Estados Unidos. Os metadados armazenados são encriptados em repouso e os espelhos geo-redundantes são mantidos nos Estados Unidos. Todos os dados do cliente, tais como configurações de apps e dados do utilizador, residem no local que o cliente escolhe e não é gerido pelo serviço.

Os metadados de serviço são replicados nos Estados Unidos para fins de recuperação de desastres.