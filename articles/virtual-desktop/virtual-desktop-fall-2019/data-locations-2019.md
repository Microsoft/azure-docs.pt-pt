---
title: Localizações de dados para Windows Virtual Desktop - Azure
description: Uma breve visão geral das localizações que localiza os dados e metadados do Windows Virtual Desktop são armazenados.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: b8cbee74a8f9639fc2c3838f38dabf2a62cf339e
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82614450"
---
# <a name="data-locations-for-windows-virtual-desktop"></a>Localizações de dados para Windows Virtual Desktop

>[!IMPORTANT]
>Este conteúdo aplica-se à versão outono 2019 que não suporta objetos de ambiente de trabalho virtual do Gestor de Recursos Do Azure. Se está a tentar gerir os objetos de ambiente de trabalho virtual do Gestor de Recursos Do Azure Windows introduzidos na atualização da primavera de 2020, consulte [este artigo](../data-locations.md).

O Windows Virtual Desktop está atualmente disponível para todos os locais geográficos. Inicialmente, os metadados de serviço só podem ser armazenados na geografia dos Estados Unidos (EUA). Os administradores podem escolher a localização para armazenar dados dos utilizadores quando criam o conjunto de anfitriões máquinas virtuais e serviços associados, como servidores de ficheiros. Saiba mais sobre geografias Azure no mapa do [datacenter azure.](https://azuredatacentermap.azurewebsites.net/)

>[!NOTE]
>A Microsoft não controla nem limita as regiões onde você ou os seus utilizadores podem aceder aos seus dados específicos de utilizador e aplicações.

>[!IMPORTANT]
>O Windows Virtual Desktop armazena informações globais de metadados como nomes de inquilinos, nomes de piscinas, nomes de grupos de aplicações e nomes principais do utilizador num datacenter localizado nos Estados Unidos. Os metadados armazenados são encriptados em repouso e os espelhos geo-redundantes são mantidos nos Estados Unidos. Todos os dados do cliente, tais como configurações de apps e dados do utilizador, residem no local que o cliente escolhe e não é gerido pelo serviço.

Os metadados de serviço são replicados nos Estados Unidos para fins de recuperação de desastres.