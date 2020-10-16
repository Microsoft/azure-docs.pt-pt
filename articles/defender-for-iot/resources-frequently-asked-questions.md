---
title: Defender da IoT frequentemente fez perguntas
description: Encontre respostas às perguntas mais frequentes sobre o Azure Defender para funcionalidades e serviços IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/07/2020
ms.author: rkarlin
ms.openlocfilehash: 5e7eabd44ea8c56fbb102f9e48812745a31de62a
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92089201"
---
# <a name="azure-defender-for-iot-frequently-asked-questions"></a>Azure Defender para IoT frequentemente fez perguntas

Este artigo fornece uma lista de perguntas e respostas frequentes sobre o Defender para ioT.

## <a name="what-is-azures-unique-value-proposition-for-iot-security"></a>Qual é a proposta de valor única do Azure para a segurança IoT?

O Defender for IoT permite às empresas alargar a sua visão de segurança cibernética existente a toda a sua solução IoT. O Azure oferece um fim à visão final da sua solução de negócio, permitindo-lhe tomar ações e decisões relacionadas com o negócio com base na postura de segurança da empresa e dados recolhidos. A segurança combinada utilizando o Azure IoT, Azure IoT Edge e Azure Security Center permitem-lhe criar a solução que pretende com a segurança de que necessita.

## <a name="our-organization-uses-proprietary-non-standard-industrial-protocols-are-they-supported"></a>A nossa organização usa protocolos industriais não padrão proprietários. São suportados? 

O Azure Defender for IoT fornece um suporte abrangente ao protocolo. Além do suporte ao protocolo incorporado, pode garantir dispositivos IoT e OT que executam protocolos proprietários e personalizados, ou protocolos que se desviam de qualquer padrão. Utilizando o SDK horizonte Open Development Environment (ODE), os desenvolvedores podem criar plugins de dissector que descodificam o tráfego da rede com base em protocolos definidos. O tráfego é analisado por serviços para fornecer monitorização completa, alerta e reporte. Use horizonte para:
- Expandir a visibilidade e o controlo sem a necessidade de atualizar para novas versões.
- Proteja a informação proprietária desenvolvendo no local como um plugin externo. 
- Localize texto para alertas, eventos e parâmetros de protocolo.

Esta solução única para desenvolver protocolos como plugins, não requer equipas de desenvolvimento dedicadas ou lançamentos de versão para suportar um novo protocolo. Os desenvolvedores, parceiros e clientes podem desenvolver protocolos de forma segura e partilhar insights e conhecimentos usando a Horizon. 

## <a name="do-i-have-to-purchase-hardware-appliances-from-microsoft-partners"></a>Tenho de comprar aparelhos de hardware a parceiros da Microsoft?
O Azure Defender para sensor IoT funciona em especificações específicas de hardware, conforme descrito no Guia de Especificações de [Hardware,](https://aka.ms/AzureDefenderforIoTBareMetalAppliance)os clientes podem adquirir hardware certificado junto dos parceiros da Microsoft ou usar a fatura de materiais fornecida (BOM) e comprá-lo por conta própria. 

Hardware certificado foi testado nos nossos laboratórios para estabilidade do condutor, gotas de pacote e dimensionamento de rede.


## <a name="regulation-does-not-allow-us-to-connect-our-system-to-the-internet-can-we-still-utilize-defender-for-iot"></a>A regulamentação não nos permite ligar o nosso sistema à Internet. Ainda podemos utilizar o Defender para o IoT?

Sim, tu podes! A solução de plataforma Azure Defender for IoT no local é implantada como um aparelho sensor físico ou virtual que ingere passivamente o tráfego da rede (via SPAN, RSPAN ou TAP) para analisar, descobrir e monitorizar continuamente as redes de TI, OT e IoT. Para empresas maiores, vários sensores podem agregar os seus dados a uma consola de gestão no local.

## <a name="where-in-the-network-should-i-connect-monitoring-ports"></a>Onde é que eu devo ligar as portas de monitorização?

O sensor Azure Defender for IoT liga-se a uma porta SPAN ou à rede TAP e começa imediatamente a recolher tráfego de rede ICS através de uma monitorização passiva (sem agente). Não tem impacto nas redes OT, uma vez que não é colocado na via dos dados e não digitaliza ativamente os dispositivos OT.

Por exemplo:
- Um único aparelho (virtual de físico) pode estar na camada DMZ do chão da loja, tendo todo o tráfego de células shop floor encaminhado para esta camada.
- Em alternativa, localize pequenos mini-sensores em cada célula do Chão da Loja com uma gestão em nuvem ou local que resida na camada DMZ do chão da loja. Outro aparelho (virtual ou físico) pode monitorizar o tráfego na camada DMZ do chão da loja (para SCADA, Historiador ou MES).

## <a name="how-does-defender-for-iot-compare-to-the-competition"></a>Como é que o Defender para ioT se compara à competição?

Enquanto outras soluções fornecem um conjunto de capacidades que permitem aos clientes criar as suas próprias soluções, o Defender for IoT fornece uma solução de segurança IoT de ponta a ponta que proporciona uma ampla visão em toda a segurança de todos os seus recursos Azure relacionados. O Azure permite uma rápida implantação e integração completa com os gémeos módulos IoT Hub para uma fácil integração com as ferramentas de gestão de dispositivos existentes.


## <a name="do-i-have-to-be-an-azure-iot-customer"></a>Tenho de ser cliente do Azure IoT?

Sim. Para implementações ligadas à nuvem, o Azure Defender para IoT depende da conectividade e infraestrutura Azure IoT.
## <a name="can-i-create-my-own-alerts"></a>Posso criar os meus próprios alertas?

Sim. Pode definir um alerta personalizado sobre um conjunto pré-determinado de comportamentos como endereço IP e portas abertas. Consulte [Criar alertas personalizados](quickstart-create-custom-alerts.md) para saber mais sobre alertas personalizados e como os fazer.

## <a name="where-can-i-see-logs-can-i-customize-logs"></a>Onde posso ver os troncos? Posso personalizar registos?

- Ver alertas e recomendações utilizando o seu espaço de trabalho log analytics conectado. Configure o tamanho e a duração do armazenamento no espaço de trabalho.

- Os dados brutos do seu agente de segurança também podem ser armazenados na sua conta Log Analytics. Considere o tamanho, duração, requisitos de armazenamento e custos associados antes de alterar a configuração desta opção.



## <a name="what-happens-when-the-internet-connection-stops-working"></a>O que acontece quando a ligação à internet deixa de funcionar?

Os sensores e agentes continuam a executar e armazenar dados enquanto o dispositivo estiver em funcionamento. Os dados são armazenados na cache da mensagem de segurança de acordo com a configuração do tamanho. Quando o dispositivo recupera a conectividade, as mensagens de segurança retomam o envio.





## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre como começar com o Defender para ioT, consulte os seguintes artigos:

- Leia o Defender para a [visão geral do IoT](overview.md)
- Verifique os [pré-requisitos](service-prerequisites.md) do Serviço
- Saiba mais sobre como [começar](getting-started.md)
- Compreenda [os alertas de segurança do Defender para ioT](concept-security-alerts.md)
