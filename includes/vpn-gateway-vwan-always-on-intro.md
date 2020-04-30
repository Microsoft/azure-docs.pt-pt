---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/12/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4ea97e2dbee87f7ab129c4295276c9024c0212c7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80116862"
---
Uma nova funcionalidade do cliente VPN do Windows 10, Always On, é a capacidade de manter uma ligação VPN. Com o Always On, o perfil VPN ativo pode ligar-se automaticamente e manter-se ligado com base em gatilhos, tais como iniciar sessão do utilizador, alteração do estado da rede ou ecrã do dispositivo ativo.

Pode utilizar gateways com o Windows 10 Always On para estabelecer túneis de utilizador persistentes e túneis de dispositivos para o Azure. Este artigo ajuda-o a configurar um túnel de utilizador Always On VPN.

As ligações VPN sempre em funcionando incluem qualquer um dos dois tipos de túneis:

* **Túnel do dispositivo**: Liga-se a servidores VPN especificados antes de os utilizadores iniciarem o insessão no dispositivo. Cenários de conectividade pré-sinalina e gestão do dispositivo utilizam um túnel de dispositivo.

* **Túnel**do utilizador : Liga-se apenas depois de os utilizadores iniciarem sessão no dispositivo. Ao utilizar túneis de utilizador, pode aceder aos recursos da organização através de servidores VPN.

Os túneis do dispositivo e os túneis de utilizador funcionam independentemente dos seus perfis VPN. Podem ser ligados ao mesmo tempo e podem utilizar diferentes métodos de autenticação e outras configurações de configuração VPN, conforme apropriado.
