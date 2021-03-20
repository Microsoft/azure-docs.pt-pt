---
title: Sobre a integração splunk
titleSuffix: Azure Defender for IoT
description: Para resolver a falta de visibilidade na segurança e resiliência das redes OT, o Defender for IoT desenvolveu o Pedido de Monitorização de Ameaças de IoT, IIoT e ICS para a Splunk, uma integração nativa entre defender para IoT e Splunk que permite uma abordagem unificada à segurança de TI e OT.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/4/2021
ms.topic: article
ms.service: azure
ms.openlocfilehash: 91d877d644b4b5ca7231f5f81f9163a0fd3cbe25
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98558570"
---
# <a name="defender-for-iot-and-ics-threat-monitoring-application-for-splunk"></a>Pedido de monitorização de ameaças de IoT e ICS para Splunk

O Defender para ioT atenua o risco IIoT, ICS e SCADA com motores patenteados de autoaprendizagem conscientes do ICS que fornecem informações imediatas sobre dispositivos, vulnerabilidades e ameaças do ICS em menos de uma hora de imagem e sem depender de agentes, regras ou assinaturas, competências especializadas ou conhecimento prévio do ambiente.

Para resolver a falta de visibilidade na segurança e resiliência das redes OT, o Defender for IoT desenvolveu o Pedido de Monitorização de Ameaças de IoT, IIoT e ICS para a Splunk, uma integração nativa entre defender para IoT e Splunk que permite uma abordagem unificada à segurança de TI e OT.

> [!Note]
> As referências a CyberX referem-se ao Azure Defender para IoT.

## <a name="about-the-splunk-application"></a>Sobre a aplicação Splunk

A aplicação proporciona aos analistas da SOC visibilidade multidimensional nos protocolos especializados de OT e dispositivos IIoT implantados em ambientes industriais, juntamente com análises comportamentais conscientes do ICS para detetar rapidamente comportamentos suspeitos ou anómalos. A aplicação também permite a resposta de incidentes de TI e OT dentro de um SOC corporativo. Esta é uma evolução importante dada a convergência contínua de TI e OT para apoiar novas iniciativas do IIoT, como máquinas inteligentes e inteligência em tempo real.

A aplicação Splunk pode ser instalada localmente ou executada numa nuvem. A integração com o Defender para ioT suporta ambas as implementações.

## <a name="about-the-integration"></a>Sobre a integração

A integração do Defender para IoT e Splunk através da aplicação nativa permite aos utilizadores:

- Reduza o tempo necessário para que as organizações de infraestruturas industriais e críticas detetem, investiguem e atuem sobre ameaças cibernéticas.

- Obtenha informações em tempo real sobre os riscos de OT.

- Correlate Defender para alertas de IoT com repositórios de segurança da empresa Splunk.

- Monitorize e responda a partir de um único painel de vidro.

[:::image type="content" source="media/integration-splunk/splunk-mainpage-v2.png" alt-text="Página principal da ferramenta splunk.":::](media/integration-splunk/splunk-mainpage-v2.png#lightbox)

:::image type="content" source="media/integration-splunk/alerts.png" alt-text="A página de alertas em splunk.":::

A aplicação permite que os administradores da Splunk analisem os alertas de OT que o Defender para ioT envia, e monitorizem toda a implementação de segurança OT, incluindo detalhes como:

- Qual dos cinco motores analíticos detetou o alerta.

- Que protocolo gerou o alerta.

- Que o defender do sensor IoT gerou o alerta.

- O nível de gravidade do alerta.

- A fonte e o destino da comunicação.

## <a name="requirements"></a>Requisitos

### <a name="version-requirements"></a>Requisitos de versão

As seguintes versões são requisitos.

- Defender para a versão IoT 2.4 e superior.

- Splunkbase versão 11 e superior.

- Splunk Enterprise versão 7.2 ou superior.
  
## <a name="download-the-application"></a>Transferir a aplicação

Descarregue o *CyberX ICS Threat Monitoring for Splunk Application* from the [Splunkbase](https://splunkbase.splunk.com/app/4313/).

## <a name="splunk-permission-requirements"></a>Requisitos de permissão splunk

É necessária a seguinte permissão splunk:

- Qualquer utilizador com permissões de função de utilizador *administrativo.*

## <a name="send-defender-for-iot-alerts-to-splunk"></a>Envie o Defender para alertas de IoT para Splunk

Os alertas do Defender for IoT fornecem informações sobre uma vasta gama de eventos de segurança, incluindo:

- Desvios da atividade de rede de base aprendida.

- Deteções de malware.

- Deteções baseadas em mudanças operacionais suspeitas.

- Anomalias na rede.

- O protocolo desvia-se das especificações do protocolo.

:::image type="content" source="media/integration-splunk/address-scan.png" alt-text="O ecrã de deteção.":::

Pode configurar o Defender for IoT para enviar alertas para o servidor Splunk, onde as informações de alerta são apresentadas no painel Splunk Enterprise.

:::image type="content" source="media/integration-splunk/alerts-and-details.png" alt-text="Veja todos os alertas e detalhes.":::

As seguintes informações de alerta são enviadas para o servidor Splunk.

- A data e a hora do alerta.

- O motor Defender for IoT que detetou o evento: Violação de Protocolo, Violação de Políticas, Malware, Anomalia ou motor Operacional.

- O título de alerta.

- A mensagem de alerta.

- A gravidade do alerta: Aviso, Menor, Major ou Crítico.

- O nome do dispositivo de origem.

- O endereço IP do dispositivo de origem.

- O nome do dispositivo de destino.

- O endereço IP do dispositivo de destino.

- O endereço IP da plataforma IoT (Anfitrião).

- O nome do Defender para aparelho de plataforma IoT (tipo de origem).

A produção da amostra é mostrada abaixo:

| Hora | Evento |
|--|--|
| 7/23/15<br />21:28:31.000 | **Defender para ioT plataforma Alerta**: Um dispositivo foi parado por um Comando PLC<br /><br />**Tipo**: Violação Operacional <br /><br />**Severidade**: Major <br /><br />**Nome de** origem : my_device1 <br /><br />**Fonte IP**: 192.168.110.131 <br /><br />**Nome do destino**: my_device2<br /><br /> **Destino IP**: 10.140.33.238 <br /><br />**Mensagem**: Um dispositivo de rede foi interrompido utilizando um comando Stop PLC. Este dispositivo não funcionará até que seja enviado um comando Iniciar. 192.168.110.131 foi interrompido por 10.140.33.238 (um dispositivo Siemens S7), utilizando um comando PLC Stop.<br /><br />**Anfitrião**: 192.168.90.43 <br /><br />**Origem :** Sensor_Agent |

## <a name="define-alert-forwarding-rules"></a>Definir regras de encaminhamento de alerta

Utilize o Defender para *as regras de encaminhamento* IoT para enviar informações de alerta para servidores Splunk.

Estão disponíveis opções para personalizar as regras de alerta com base em:

- Protocolos específicos detetados.

- Gravidade do evento.

- Defender para o motor IoT que deteta eventos.

Para criar uma regra de encaminhamento:

1. A partir do painel esquerdo do sensor ou da consola de gestão no local, selecione **Forwarding.**

    :::image type="content" source="media/integration-splunk/forwarding.png" alt-text="Selecione o botão azul Criar alerta de encaminhamento.":::

1. Selecione **Criar Regras de Encaminhamento**. Na janela **Regra de Encaminhamento criar,** defina os parâmetros de regra.

    :::image type="content" source="media/integration-splunk/forwarding-rule.png" alt-text="Crie as regras para a sua regra de encaminhamento.":::

    | Parâmetro | Descrição |
    |--|--|
    | **Nome** | O nome da regra do encaminhamento. |
    | **Selecione Severidade** | O mínimo incidente de nível de segurança para avançar. Por exemplo, se minor for selecionado, alertas menores e qualquer alerta acima deste nível de gravidade serão reencaminhados. |
    | **Protocolos** | Por predefinição, todos os protocolos são selecionados. Para selecionar um protocolo específico, selecione **Specific** e selecione o protocolo para o qual esta regra é aplicada. |
    | **Motores** | Por defeito, todos os motores de segurança estão envolvidos. Para selecionar um motor de segurança específico para o qual esta regra é aplicada, selecione **Specific** e selecione o motor. |
    | **Notificações do Sistema** | Estado do sensor dianteiro on-line/offline. Esta opção só está disponível se tiver iniciado sessão no Central Manager. |                                            |

1. Para instruir o Defender para ioT para enviar informações de ativos para Splunk, selecione **Ação** e, em seguida, selecione **Enviar para Splunk Server**.

1. Introduza os seguintes parâmetros Splunk.

    :::image type="content" source="media/integration-splunk/parameters.png" alt-text="Os parâmetros Splunk devem introduzir neste ecrã.":::

    | Parâmetro | Descrição |
    |--|--|
    | **Anfitrião** | Endereço do servidor Splunk |
    | **Porta** | 8089 |
    | **Nome de Utilizador** | Nome de utilizador do servidor Splunk |
    | **Palavra-passe** | Senha de servidor splunk |

1. Selecione **Submeter**

## <a name="next-steps"></a>Passos seguintes

Saiba como [encaminhar informações de alerta](how-to-forward-alert-information-to-partners.md).
