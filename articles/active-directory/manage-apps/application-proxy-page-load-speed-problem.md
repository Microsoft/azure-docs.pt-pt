---
title: Uma aplicação de procuração de aplicação demora muito tempo a carregar Microsoft Docs
description: Problemas de desempenho da página de resolução de problemas com o Proxy de Aplicação AD Azure
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
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ac1182d719d7c90129115e1fadf94f4f86a28e8
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/26/2020
ms.locfileid: "65782650"
---
# <a name="an-application-proxy-application-takes-too-long-to-load"></a>Uma aplicação de procuração de aplicação demora muito tempo a carregar

Este artigo ajuda-o a entender porque é que uma aplicação de procuração de aplicação ad ad azure pode demorar muito tempo a carregar. Também explica o que pode fazer para resolver esta questão.

## <a name="overview"></a>Descrição geral
Embora as suas candidaturas estejam a funcionar, podem experimentar uma longa latência. Pode haver ajustes de topo de rede que pode fazer para melhorar a velocidade. Para uma avaliação de diferentes topologologias, consulte o [documento de considerações](application-proxy-network-topology.md)de rede .

Além da topologia da rede, não existem atualmente mais recomendações para a sintonização do desempenho. À medida que o serviço de Procuração de Aplicações se expande, pode chegar a um centro de dados fisicamente mais próximo. A proximidade pode ajudar com a latência. Para obter uma lista de centros de dados Azure, consulte a página de teste de [latência](http://www.azurespeed.com/Azure/Latency). 

Os centros de dados com o serviço Proxy de Aplicação podem ser encontrados com a ferramenta de teste de portas de [conector](https://aadap-portcheck.connectorporttest.msappproxy.net/). 

## <a name="feedback-on-application-proxy-data-center-locations"></a>Feedback sobre localizações do centro de dados de proxy de aplicação 
Pode haver centros de dados Azure que ainda não incluem application Proxy, mas levaria a uma grande melhoria de latência para si. Envie a localização aadapfeedback@microsoft.comdo centro de dados para . A Microsoft usa o seu feedback para planos de expansão.

A Microsoft está a trabalhar em capacidades adicionais para melhorar a latência. Assim que estas melhorias estiverem disponíveis, a documentação será atualizada.

## <a name="next-steps"></a>Passos seguintes
[Trabalhar com servidores proxy existentes no local](application-proxy-configure-connectors-with-proxy-servers.md)
