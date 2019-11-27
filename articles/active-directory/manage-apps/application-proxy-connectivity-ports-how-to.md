---
title: Como abrir portas de firewall necessárias para um aplicativo de proxy de aplicativo
description: Descubra o que portas abrir para o Proxy de aplicação do Azure AD funcione corretamente
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
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74275554"
---
# <a name="how-to-open-the-firewall-ports-required-for-an-application-proxy-application"></a>Como abrir as portas de firewall necessárias para uma aplicação de Proxy de aplicações

Para ver uma lista completa das portas necessárias e da função de cada porta, consulte a seção pré-requisitos da [documentação do proxy de aplicativo](application-proxy-add-on-premises-application.md). tenha em atenção que o Proxy de aplicações utiliza apenas a portas de saída.

Você também pode verificar se você tem todas as portas necessárias abertas abrindo a [ferramenta de teste de portas de conector](https://aadap-portcheck.connectorporttest.msappproxy.net/) de sua rede local. Mais marcas de verificação verde significa maior resiliência. 

## <a name="app-proxy-regions"></a>Regiões de Proxy de aplicações

Estamos a trabalhar numa forma de informá-lo de que estas regiões tem de ser verde para. Por enquanto, certifique-se de que todos eles são. E.U.A. central também é necessário, independentemente de qual é a região.

Para certificar-se de que a ferramenta dá-lhe os resultados certos, certifique-se de que para:

-   Abra a ferramenta num browser a partir do servidor onde instalou o conector.

-   Certifique-se de que quaisquer proxies ou firewalls aplicáveis ao seu conector também são aplicadas a esta página. Isso pode ser feito no Internet Explorer acessando **configurações** -&gt; **opções da Internet** -&gt; **conexões** -&gt; **configurações de LAN**. Nesta página, verá o campo "Utilização de Proxy de servidor para sua LAN". Selecione esta caixa e colocar o endereço de proxy no campo "Address".

## <a name="next-steps"></a>Passos seguintes
[Entender os conectores de Proxy de Aplicativo do AD do Azure](application-proxy-connectors.md)
