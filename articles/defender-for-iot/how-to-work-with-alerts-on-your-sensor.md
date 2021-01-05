---
title: Trabalhe com alertas no seu sensor
description: Trabalhe com alertas para ajudá-lo a melhorar a segurança e o funcionamento da sua rede.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 11/30/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 00207ffb8480ae99c2f1aad74183fca9ea45ee17
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/31/2020
ms.locfileid: "97842848"
---
# <a name="work-with-alerts-on-your-sensor"></a>Trabalhe com alertas no seu sensor

Trabalhe com alertas para ajudá-lo a melhorar a segurança e o funcionamento da sua rede. Os alertas fornecem-lhe informações sobre:

- Desvios da atividade de rede autorizada

- Protocolo e anomalias operacionais

- Suspeita de tráfego de malware

:::image type="content" source="media/how-to-work-with-alerts-sensor/address-scan-detected-screen.png" alt-text="Detetar digitalização de endereços.":::

As opções de gestão de alerta permitem aos utilizadores:

- Instrua os sensores para aprenderem a atividade detetada como tráfego autorizado.

- Reconheça a revisão do alerta.

- Instrua os sensores para silenciar os eventos detetados com dispositivos idênticos e tráfego comparável.

Estão disponíveis ferramentas adicionais que o ajudam a melhorar e a acelerar a investigação de alerta. Por exemplo:

  - Adicione comentários instrutivos para os revisores de alerta.

  - Crie grupos de alerta para visualização em soluções SOC. 

  - Procurar alertas específicos; rever ficheiros PCAP relacionados; ver os dispositivos detetados e outros dispositivos conectados no mapa do dispositivo ou enviar detalhes de alerta para os sistemas parceiros.

  - Alertas avançados para os fornecedores parceiros: sistemas SIEM, sistemas MSSP e muito mais.

## <a name="alerts-and-engines"></a>Alertas e motores

Os alertas são acionados quando os motores dos sensores detetam alterações no tráfego da rede e no comportamento que precisam da sua atenção. Este artigo descreve o tipo de alertas que cada motor dispara.

| Tipo de alerta | Description |
|-|-|
| Alertas de violação de políticas | Acionado quando o motor de Violação de Política deteta um desvio do tráfego previamente aprendido. Por exemplo: <br /> - Um novo dispositivo é detetado.  <br /> - É detetada uma nova configuração num dispositivo. <br /> - Um dispositivo não definido como dispositivo de programação realiza uma alteração de programação. <br /> - Uma versão de firmware mudou. |
| Alertas de violação de protocolo | Acionado quando o motor de Violação de Protocolo deteta estruturas de pacotes ou valores de campo que não cumprem a especificação do protocolo. | 
| Alertas operacionais | Acionado quando o motor Operacional deteta incidentes operacionais da rede ou um mau funcionamento do dispositivo. Por exemplo, um dispositivo de rede foi parado através de um comando Stop PLC, ou uma interface num sensor parou de monitorizar o tráfego. |
| Alertas de malware | Desencadeado quando o motor malware deteta atividade de rede maliciosa. Por exemplo, o motor deteta um ataque conhecido como Conficker. |
| Alertas de anomalia | Acionado quando o motor de anomalia deteta um desvio. Por exemplo, um dispositivo está a realizar análises de rede, mas não é definido como um dispositivo de digitalização. |

As ferramentas estão disponíveis para ativar e desativar os motores sensores. Os alertas não são acionados a partir de motores desativados. Ver [Controlo do tráfego monitorizado](how-to-control-what-traffic-is-monitored.md).

## <a name="alerts-and-sensor-reporting"></a>Alertas e relatórios de sensores

A atividade refletida em alertas pode ser calculada quando está a gerar relatórios de mineração de dados, avaliação de risco e vetores de ataque. Ao gerir estes eventos, o sensor atualiza os relatórios em conformidade.

Por exemplo:

  - A conectividade não autorizada entre um dispositivo numa sub-rede definida e dispositivos localizados fora da sub-rede (público) aparecerá no relatório *de Atividade da Internet* data mining e na secção de *Ligações à Internet* de Avaliação de Riscos. Depois de autorizados estes dispositivos (aprendidos), são calculados na geração destes relatórios.

  - Os eventos de malware detetados em dispositivos de rede são relatados em relatórios de Avaliação de Riscos. Quando os alertas sobre eventos de malware forem *silenciados,* os dispositivos afetados não serão calculados no relatório de Avaliação de Riscos.

## <a name="see-also"></a>Veja também

- [Modos de Aprendizagem e Aprendizagem de TI Inteligentes](how-to-control-what-traffic-is-monitored.md#learning-and-smart-it-learning-modes)
- [Ver informações fornecidas em alertas](how-to-view-information-provided-in-alerts.md)
- [Gerir o evento de alerta](how-to-manage-the-alert-event.md)
- [Acelerar fluxos de trabalho de alerta](how-to-accelerate-alert-incident-response.md)
