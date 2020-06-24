---
title: Uma aplicação Proxy de aplicação demora muito tempo a carregar Microsoft Docs
description: Problemas de desempenho da carga de página de resolução de problemas com o Proxy de aplicação AD AD Azure
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
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: bff9873742d5d660f563eebcb58ff9bcca940d24
ms.sourcegitcommit: bc943dc048d9ab98caf4706b022eb5c6421ec459
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/14/2020
ms.locfileid: "84760185"
---
# <a name="an-application-proxy-application-takes-too-long-to-load"></a>Uma aplicação de procuração de aplicação leva muito tempo a carregar

Este artigo ajuda-o a entender por que uma aplicação de procuração de aplicação AD Azure pode demorar muito tempo a carregar. Também explica o que pode fazer para resolver esta questão.

## <a name="overview"></a>Descrição geral
Embora as suas aplicações estejam a funcionar, podem experimentar uma longa latência. Pode haver ajustes de topologia de rede que pode fazer para melhorar a velocidade. Para uma avaliação de diferentes topologias, consulte o [documento de considerações de rede.](application-proxy-network-topology.md)

Além da topologia da rede, não existem atualmente mais recomendações para a afinação de desempenho. À medida que o serviço Application Proxy se expande, pode chegar a um centro de dados que está fisicamente mais próximo. A proximidade mais próxima pode ajudar com a latência. Para obter uma lista de centros de dados Azure, consulte a página de [teste de latência](http://www.azurespeed.com/Azure/Latency). 

Os centros de dados com o serviço Application Proxy podem ser encontrados com a Ferramenta de Teste de Portas do [Conector.](https://aadap-portcheck.connectorporttest.msappproxy.net/) 

## <a name="feedback-on-application-proxy-data-center-locations"></a>Feedback sobre as localizações do centro de dados do Proxy de aplicação 
Pode haver centros de dados Azure que ainda não incluem Application Proxy, mas que levariam a uma grande melhoria de latência para si. Envie a localização do centro de dados para aadapfeedback@microsoft.com . A Microsoft usa o seu feedback para planos de expansão.

A Microsoft está a trabalhar em capacidades adicionais para melhorar a latência. Assim que estas melhorias estiverem disponíveis, a documentação será atualizada.

## <a name="next-steps"></a>Passos seguintes
[Trabalhar com servidores proxy existentes no local](application-proxy-configure-connectors-with-proxy-servers.md)
