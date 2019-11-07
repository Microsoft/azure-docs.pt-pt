---
title: Inteligência contra ameaças & mapa de alertas de segurança – central de segurança do Azure
description: Saiba como usar o mapa de alertas de segurança e a funcionalidade de inteligência contra ameaças na central de segurança do Azure para identificar possíveis ameaças em suas VMs e computadores.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: a771a3a1-2925-46ca-8e27-6f6a0746f58b
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/3/2018
ms.author: rkarlin
ms.openlocfilehash: 97975d72214426907a2ab91f0d3cd98d0ce6734b
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73693491"
---
# <a name="security-alerts-map-and-threat-intelligence"></a>Informações sobre ameaças e mapa de alertas de segurança
Este artigo ajuda você a usar o mapa de alertas de segurança da central de segurança do Azure e o mapa de inteligência contra ameaças com base em eventos de segurança para resolver problemas relacionados à segurança.

> [!NOTE]
> O botão mapa de *eventos* de segurança foi desativado em 31 de julho de 2019. Para obter mais informações e serviços alternativos, consulte [aposentadoria dos recursos da central de segurança (julho de 2019)](security-center-features-retirement-july2019.md#menu_securityeventsmap).


## <a name="how-the-security-alerts-map-works"></a>Como o mapa de alertas de segurança funciona
A central de segurança fornece um mapa que ajuda você a identificar ameaças de segurança em relação ao ambiente. Por exemplo, você pode identificar se um computador específico faz parte de um botnet e de onde a ameaça vem. Os computadores podem se tornar nós em uma botnet quando os invasores instalam de forma ilícita o malware que secretamente interage com o comando e o controle que gerenciam a botnet. 

Para criar esse mapa, a central de segurança usa dados provenientes de várias fontes na Microsoft. A central de segurança usa esses dados para mapear possíveis ameaças em relação ao seu ambiente. 

Um dos passos de um [processo de resposta a incidentes de segurança](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide#incident-response) é identificar a gravidade dos sistemas comprometidos. Nesta fase, deve realizar as tarefas seguintes:

- Determinar a natureza do ataque.
- Determine o ponto de origem do ataque.
- Determinar a intenção do ataque. O ataque foi direcionado à sua organização, para adquirir informações específicas, ou foi aleatório?
- Identificar os sistemas que foram comprometidos.
- Identificar os ficheiros que foram acedidos e determinar a sensibilidade desses ficheiros.

Você pode usar o mapa de alertas de segurança na central de segurança para ajudar com essas tarefas.

## <a name="access-the-security-alerts-map"></a>Acessar o mapa de alertas de segurança
Para visualizar as ameaças atuais em seu ambiente, abra o mapa de alertas de segurança:

1. Abra o dashboard **Centro de Segurança**.
2. No painel esquerdo, em **proteção contra ameaças** , selecione **mapa de alertas de segurança**. O mapa é aberto.
3. Para obter mais informações sobre o alerta e receber as etapas de correção, clique no ponto de alerta no mapa e siga as instruções. 
 
O mapa de alertas de segurança é baseado em alertas. Esses alertas se baseiam em atividades para as quais a comunicação de rede foi associada a um endereço IP que foi resolvido com êxito, se o endereço IP é ou não um endereço IP arriscado conhecido (por exemplo, um cryptominer conhecido) ou um endereço IP que não é reconhecido anteriormente como arriscado. O mapa fornece alertas em todas as assinaturas que você selecionou anteriormente no Azure. 

Os alertas no mapa são exibidos de acordo com a localização geográfica onde são detectados como provenientes de, e são codificados por cor por gravidade. 
    ![informações de inteligência contra ameaças](./media/security-center-threat-intel/security-center-alert-map.png)



## <a name="see-also"></a>Consultar também
Neste artigo, aprendeu a utilizar as informações sobre ameaças no Centro de Segurança para ajudar a identificar atividade suspeita. Para saber mais sobre o Centro de Segurança, veja os artigos seguintes:

* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Saiba como gerir alertas e responder a incidentes de segurança no Centro de Segurança.
* [Monitorização de estado de funcionamento de segurança no Centro de Segurança do Azure](security-center-monitoring.md). Saiba como monitorizar o estado de funcionamento dos recursos do Azure.
* [Compreender os alertas de segurança no Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Saiba mais sobre os diferentes tipos de alertas de segurança.
* [Guia de resolução de problemas do Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Saiba como resolver problemas comuns no Centro de Segurança.
* [Centro de Segurança do Azure FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md). Encontre respostas para as perguntas mais frequentes sobre o serviço.
* [Blogue de segurança do Azure](https://blogs.msdn.com/b/azuresecurity/). Encontre mensagens do blogue acerca da segurança e conformidade do Azure.
