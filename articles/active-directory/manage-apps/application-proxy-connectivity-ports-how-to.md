---
title: Como abrir portas de firewall necessárias para uma aplicação Proxy de aplicação
description: Saiba quais as portas a abrir para que o Azure AD Application Proxy funcione corretamente
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/21/2018
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 192e721e25612019649fed86b44135fb6bfef89c
ms.sourcegitcommit: bc943dc048d9ab98caf4706b022eb5c6421ec459
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/14/2020
ms.locfileid: "84764711"
---
# <a name="how-to-open-the-firewall-ports-required-for-an-application-proxy-application"></a>Como abrir as portas de firewall necessárias para uma aplicação de Procuração de Aplicação

Para ver uma lista completa das portas necessárias e a função de cada porta, consulte a secção de pré-requisitos da [documentação do Application Proxy](application-proxy-add-on-premises-application.md). note que o Application Proxy só utiliza portas de saída.

Também pode verificar se tem todas as portas necessárias abertas abrindo a Ferramenta de Teste de Portas do [Conector](https://aadap-portcheck.connectorporttest.msappproxy.net/) a partir da sua rede de instalações. Mais marcas verdes significam maior resiliência. 

## <a name="app-proxy-regions"></a>Regiões de Procuração de Aplicativos

Estamos a trabalhar numa forma de vos informar qual destas regiões tem de ser verde para si. Por enquanto, certifique-se de que todos estão. Os EUA centrais também são necessários independentemente da região em que se encontra.

Para se certificar de que a ferramenta lhe dá os resultados certos, certifique-se de:

-   Abra a ferramenta num browser a partir do servidor onde instalou o Conector.

-   Certifique-se de que quaisquer proxies ou firewalls aplicáveis ao seu Conector também são aplicados nesta página. Isto pode ser feito no Internet Explorer indo para **Configurações**  - &gt; **Opções de Internet**  - &gt; **Configurações**  - &gt; **Configurações LAN .** Nesta página, vê o campo "Use um Servidor Proxy para o seu LAN". Selecione esta caixa e coloque o endereço de procuração no campo "Endereço".

## <a name="next-steps"></a>Passos seguintes
[Compreenda os conectores Proxy de aplicação AD Azure](application-proxy-connectors.md)
