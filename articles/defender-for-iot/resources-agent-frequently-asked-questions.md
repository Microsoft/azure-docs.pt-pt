---
title: Azure Defender para agente IoT frequentemente fez perguntas
description: Encontre respostas às perguntas mais frequentes sobre o Azure Defender para agente IoT.
ms.topic: conceptual
ms.date: 10/07/2020
ms.openlocfilehash: 4bd7d3c9b76dfb37e53cc51e5e16b660545cb7f0
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104778666"
---
# <a name="azure-defender-for-iot-agent-frequently-asked-questions"></a>Azure Defender para agente IoT frequentemente fez perguntas

Este artigo fornece uma lista de perguntas e respostas frequentes sobre o Defensor para agente IoT.

## <a name="do-i-have-to-install-an-embedded-security-agent"></a>Tenho de instalar um agente de segurança incorporado?

A instalação do agente nos seus dispositivos IoT não é obrigatória para ativar o Defender para IoT. Pode escolher entre as três opções seguintes, ganhando diferentes níveis de capacidade de monitorização e gestão de segurança de acordo com a sua seleção:

- Implantação passiva, não invasiva (sem agente) utilizando sensores NTA (Network Traffic Analysis) para monitorizar e fornecer uma visibilidade profunda para o risco IoT/OT com impacto de desempenho zero na rede e dispositivos
- Instale o Defender para o agente de segurança incorporado IoT com ou sem modificações. Esta opção fornece o mais alto nível de informações de segurança melhoradas sobre o comportamento e acesso do dispositivo.

- Crie o seu próprio agente e implemente o Defender para esquema de mensagens de segurança IoT. Esta opção permite a utilização de ferramentas de análise de IoT do Defender para além do seu agente de segurança do dispositivo.

- Nenhuma instalação de agente de segurança nos seus dispositivos IoT. Esta opção permite a monitorização da comunicação do IoT Hub, com capacidades reduzidas de monitorização e gestão de segurança.

## <a name="what-does-the-defender-for-iot-agent-do"></a>O que faz o Defensor do agente IoT?

O defensor do agente IoT fornece uma cobertura de ameaça de nível do dispositivo para configuração, comportamento e acesso do dispositivo (digitalizando a configuração), processo & conectividade. O Defensor do agente de segurança IoT não digitaliza dados ou atividades relacionadas com o negócio.

O agente de segurança Defender for IoT é de código aberto e está disponível no GitHub em versões Windows e Linux de 32 bits e 64 bits: https://github.com/Azure/Azure-IoT-Security .

## <a name="what-are-the-dependencies-and-prerequisites-of-the-agent"></a>Quais são as dependências e pré-requisitos do agente?

O Defender for IoT suporta uma grande variedade de plataformas. Consulte [as plataformas do Dispositivo Suportado](how-to-deploy-agent.md) para verificar o suporte aos seus dispositivos específicos.

## <a name="which-data-is-collected-by-the-agent"></a>Que dados são recolhidos pelo agente?

Conectividade, acesso, configuração de firewall, lista de processos & linha de base OS são recolhidos pelo agente.

## <a name="how-much-data-will-the-agent-generate"></a>Quantos dados o agente vai gerar?

A geração de dados do agente é impulsionada pela configuração do dispositivo, aplicação, tipo de conectividade e agente de cliente. Devido à elevada variabilidade entre dispositivos e soluções IoT, recomendamos primeiro a colocação do agente num laboratório ou definição de teste para observar, aprender e definir a configuração específica que se adequa às suas necessidades, ao mesmo tempo que mede a quantidade de dados gerados. Após o início do serviço, o agente Defender for IoT fornece recomendações operacionais para otimizar a produção do agente para o ajudar com o processo de configuração e personalização.

## <a name="do-agent-messages-use-up-quota-from-iot-hub"></a>As mensagens de agente usam quota de IoT Hub?

Sim. Os dados transmitidos pelo agente são contados na sua quota IoT Hub.

## <a name="what-next-ive-installed-an-agent-and-dont-see-any-activities-or-logs"></a>O que se segue? Instalei um agente e não vejo atividades ou registos...

1. Verifique se o [tipo de agente se encaixa na plataforma de SO designada do seu dispositivo](how-to-deploy-agent.md)

1. Confirme que o [agente está a trabalhar no aparelho](how-to-agent-configuration.md).

1. Verifique se o [serviço foi ativado com sucesso](quickstart-onboard-iot-hub.md) para a **Segurança** no seu Hub IoT.

1. Verifique se o dispositivo está [configurado no Hub IoT com o módulo Defender para IoT](quickstart-create-security-twin.md).

Se as atividades ou registos ainda não estiverem disponíveis, contacte o seu parceiro Defender para obter ajuda adicional.

## <a name="what-happens-when-the-internet-connection-stops-working"></a>O que acontece quando a ligação à internet deixa de funcionar?

Os sensores e agentes continuam a executar e armazenar dados enquanto o dispositivo estiver em funcionamento. Os dados são armazenados na cache da mensagem de segurança de acordo com a configuração do tamanho. Quando o dispositivo recupera a conectividade, as mensagens de segurança retomam o envio.

## <a name="can-the-agent-affect-the-performance-of-the-device-or-other-installed-software"></a>O agente pode afetar o desempenho do dispositivo ou de outro software instalado?

O agente consome recursos de máquina como qualquer outra aplicação/processo e não deve perturbar a atividade normal do dispositivo. O consumo de recursos no dispositivo em que o agente funciona é associado à sua configuração e configuração. Recomendamos testar a configuração do seu agente num ambiente contido, juntamente com a interoperabilidade com as suas outras aplicações e funcionalidades IoT, antes de tentar implantar-se num ambiente de produção.

## <a name="im-making-some-maintenance-on-the-device-can-i-turn-off-the-agent"></a>Estou a fazer alguma manutenção no dispositivo. Posso desligar o agente?

O agente não pode ser desligado.

## <a name="is-there-a-way-to-test-if-the-agent-is-working-correctly"></a>Há alguma maneira de testar se o agente está a funcionar corretamente?

Se o agente deixar de comunicar ou não enviar mensagens de segurança, é gerado um alerta **silencioso.**

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre como começar com o Defender para ioT, consulte os seguintes artigos:

- Leia o Defender para a [visão geral do IoT](overview.md)
- Verifique os [pré-requisitos do Sistema](quickstart-system-prerequisites.md)
- Saiba mais sobre como [começar com o Defender para ioT](getting-started.md)
- Compreenda [os alertas de segurança do Defender para ioT](concept-security-alerts.md)
