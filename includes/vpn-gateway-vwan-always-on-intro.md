---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/12/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4ea97e2dbee87f7ab129c4295276c9024c0212c7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80116862"
---
Uma nova funcionalidade do cliente VPN VPN do Windows 10, Always On, é a capacidade de manter uma ligação VPN. Com Always On, o perfil VPN ativo pode ligar-se automaticamente e permanecer ligado com base em gatilhos, tais como o sessão de sessão do utilizador, a alteração do estado da rede ou o ecrã do dispositivo ativo.

Pode utilizar gateways com o Windows 10 Always On para estabelecer túneis de utilizador persistentes e túneis de dispositivos para Azure. Este artigo ajuda-o a configurar um túnel de utilizador Always On VPN.

As ligações sempre VPN incluem um dos dois tipos de túneis:

* **Túnel do dispositivo**: Liga-se aos servidores VPN especificados antes que os utilizadores entrem no dispositivo. Os cenários de conectividade pré-in e a gestão do dispositivo utilizam um túnel do dispositivo.

* **Túnel do utilizador**: Só se conecta depois de os utilizadores iniciarem a sômposições no aparelho. Ao utilizar túneis de utilizador, pode aceder aos recursos da organização através de servidores VPN.

Os túneis do dispositivo e os túneis dos utilizadores funcionam independentemente dos seus perfis VPN. Podem ser conectados ao mesmo tempo e podem utilizar diferentes métodos de autenticação e outras definições de configuração VPN, conforme apropriado.
