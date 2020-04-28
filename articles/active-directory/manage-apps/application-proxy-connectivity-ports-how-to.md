---
title: Como abrir portas de firewall necessárias para uma aplicação proxy de aplicação
description: Descubra quais os portos a abrir para que o Procurador de Aplicação AD Azure funcione corretamente
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2b676508c73ff0233526f19b865bb9e4f7b80f12
ms.sourcegitcommit: b1e25a8a442656e98343463aca706f4fde629867
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "74275554"
---
# <a name="how-to-open-the-firewall-ports-required-for-an-application-proxy-application"></a>Como abrir as portas de firewall necessárias para uma aplicação proxy de aplicação

Para ver uma lista completa das portas necessárias e a função de cada porta, consulte a secção de pré-requisitos da documentação do [Procuração](application-proxy-add-on-premises-application.md)de Pedidos . note que o Application Proxy utiliza apenas portas de saída.

Também pode verificar se tem todas as portas necessárias abertas abrindo a ferramenta de teste de portas de [conector](https://aadap-portcheck.connectorporttest.msappproxy.net/) a partir da sua rede de instalações. Mais marcas de verificação verdes significa maior resiliência. 

## <a name="app-proxy-regions"></a>Regiões de Procuração de Aplicações

Estamos a trabalhar numa forma de vos informar qual destas regiões tem de ser verde para si. Por enquanto, certifique-se de que todos são. Os EUA centrais também são necessários independentemente da região em que se encontra.

Para se certificar de que a ferramenta lhe dá os resultados certos, certifique-se de:

-   Abra a ferramenta num browser a partir do servidor onde instalou o Conector.

-   Certifique-se de que quaisquer proxies ou firewalls aplicáveis ao seu Conector também são aplicados a esta página. Isto pode ser feito no Internet Explorer indo para **Definições**  - &gt; **De Ligações**  - &gt; de Internet **Ligações LAN** **Definições**  - &gt; . Nesta página, vê o campo "Use um Servidor proxy para o seu LAN". Selecione esta caixa e coloque o endereço proxy no campo "Endereço".

## <a name="next-steps"></a>Passos seguintes
[Compreender os conectores de procuração de aplicação da AD Azure](application-proxy-connectors.md)
