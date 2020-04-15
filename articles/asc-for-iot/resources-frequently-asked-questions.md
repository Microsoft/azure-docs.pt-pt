---
title: Perguntas mais frequentes
description: Encontre respostas às perguntas mais frequentes sobre o Azure Security Center para funcionalidades e serviços IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 97fda6c2-1ecb-491f-b48d-41788bd7e0d3
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 02736b63b900600c51eda8666aff0117b9307824
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81310776"
---
# <a name="azure-security-center-for-iot-frequently-asked-questions"></a>Perguntas mais frequentes sobre o Centro de Segurança do Azure para IoT

Este artigo fornece uma lista de perguntas e respostas frequentes sobre o Azure Security Center for IoT.

## <a name="does-azure-provide-support-for-iot-security"></a>O Azure apoia a segurança do IoT?

O Azure oferece uma visão integrada para monitorizar e gerir a sua segurança IoT como parte da sua solução de segurança global através do Azure Security Center. Se for um desenvolvedor de aplicações, pode utilizar a vista IoT Hub para gerir a segurança da sua aplicação IoT.

## <a name="what-is-azures-unique-value-proposition-for-iot-security"></a>Qual é a proposta de valor única do Azure para a segurança ioT?

O Azure Security Center for IoT permite às empresas alargar a sua visão de cibersegurança existente a toda a sua solução IoT. O Azure fornece uma visão final da sua solução de negócio, permitindo-lhe tomar ações e decisões relacionadas com o negócio com base na postura de segurança da empresa e dados recolhidos. A segurança combinada utilizando o Azure IoT, O Edge Azure IoT e o Azure Security Center permitem-lhe criar a solução que deseja com a segurança de que necessita.

## <a name="who-is-azure-security-center-for-iot-made-for"></a>Para quem é feito o Centro de Segurança Azure para ioT?

O Azure Security Center for IoT está integrado no Azure IoT Hub Security e fornece gestão para as operações de segurança de soluções de negócio do dia-a-dia. O Azure Security Center for IoT também está integrado nas capacidades do Azure Security Center e fornece uma visão integrada para monitorizar e gerir a sua segurança IoT como parte da sua solução de segurança global.

## <a name="how-does-azure-security-center-for-iot-compare-to-the-competition"></a>Como é que o Azure Security Center for IoT se compara à concorrência?

Enquanto outras soluções fornecem um conjunto de capacidades que permitem aos clientes criar as suas próprias soluções, o Azure Security Center for IoT fornece uma solução de segurança IoT única de ponta a ponta que proporciona uma ampla visão em toda a segurança de todos os seus recursos Azure relacionados. O Azure permite uma rápida implementação e integração total com os gémeos móduloIO Hub para uma fácil integração com as ferramentas de gestão de dispositivos existentes.

## <a name="do-i-have-to-be-an-azure-security-center-customer-to-use-this-service"></a>Tenho de ser um cliente do Azure Security Center para usar este serviço?

Não, mas é recomendado. Sem o Azure Security Center, o Azure Security Center for IoT recebe dados limitados de recursos conectados e fornece uma análise limitada da sua potencial superfície de ataque, ameaças e potenciais ataques.

## <a name="do-i-have-to-be-an-azure-iot-customer"></a>Tenho de ser cliente azure ioT?

Sim. O Azure Security Center for IoT conta com conectividade e infraestrutura seleções do Azure IoT.

## <a name="do-i-have-to-install-an-agent"></a>Tenho de instalar um agente?

A instalação do agente nos seus dispositivos IoT não é obrigatória de forma a permitir o Microsoft Azure Security Center para IoT. Pode escolher entre as seguintes três opções, obtendo diferentes níveis de monitorização de segurança e capacidades de gestão de acordo com a sua seleção:

1. Instale o Centro de Segurança Azure para agente de segurança IoT com ou sem modificações. Esta opção fornece o mais alto nível de informações de segurança melhoradas sobre o comportamento e acesso do dispositivo.

1. Crie o seu próprio agente e implemente o Microsoft Azure Security Center para o esquema de mensagens de segurança IoT. Esta opção permite a utilização do Microsoft Azure Security Center para ferramentas de análise IoT em cima do seu agente de segurança do dispositivo.

1. Nenhuma instalação de agente de segurança nos seus dispositivos IoT. Esta opção permite a monitorização da comunicação ioT Hub, com capacidades reduzidas de monitorização e gestão de segurança.

## <a name="what-does-the-azure-security-center-for-iot-agent-do"></a>O que faz o Centro de Segurança Azure para agente ioT?

O Azure Security Center para o agente IoT fornece cobertura de ameaça de nível de dispositivo para configuração, comportamento e acesso do dispositivo (através da digitalização da configuração), processo & conectividade. O Centro de Segurança Azure para o agente de segurança IoT não digitaliza dados ou atividades relacionados com o negócio.

## <a name="where-can-i-get-the-azure-security-center-for-iot-security-agent"></a>Onde posso arranjar o Centro de Segurança Azure para agente de segurança IoT?

O Azure Security Center for IoT security agent é de código aberto e está disponível no https://github.com/Azure/Azure-Security-IoTGitHub em versões windows e Linux de 32 bits e 64 bits: .

## <a name="where-does-the-azure-security-center-for-iot-agent-get-installed"></a>Onde é que o Centro de Segurança Azure para agente IoT é instalado?

Informações detalhadas de instalação e implementação de agentes podem ser encontradas no GitHub: https://github.com/Azure/Azure-IoT-Security.

## <a name="what-are-the-dependencies-and-prerequisites-of-the-agent"></a>Quais são as dependências e pré-requisitos do agente?

O Azure Security Center for IoT suporta uma grande variedade de plataformas. Consulte [as plataformas do Dispositivo Suportado](how-to-deploy-agent.md) para verificar o suporte dos seus dispositivos específicos.

## <a name="which-data-is-collected-by-the-agent"></a>Que dados são recolhidos pelo agente?

Conectividade, acesso, configuração de firewall, lista de processos & linha de base do OS são recolhidas pelo agente.

## <a name="how-much-data-will-the-agent-generate"></a>Quantos dados o agente vai gerar?

A geração de dados do agente é impulsionada pela configuração de dispositivo, aplicação, tipo de conectividade e agente do cliente. Devido à elevada variabilidade entre dispositivos e soluções IoT, recomendamos primeiro a implementação do agente num laboratório ou numa configuração de teste para observar, aprender e definir a configuração específica que se adequa às suas necessidades, enquanto mede a quantidade de dados gerados. Após o início do serviço, o Azure Security Center for IoT agent fornece recomendações operacionais para otimizar a entrada do agente para ajudá-lo com o processo de configuração e personalização.

## <a name="how-can-i-control-my-billing"></a>Como posso controlar a minha faturação?

O Azure Security Center for IoT fornece verificações de agentes configuráveis, tampões de dados e a capacidade de criar alertas personalizados que aumentem ou reduzam a quantidade de dados gerados pelo agente.

## <a name="do-agent-messages-use-up-quota-from-iot-hub"></a>As mensagens de agente usam quota do IoT Hub?

Sim. Os dados transmitidos pelo agente são contados na sua quota IoT Hub.

## <a name="what-next-ive-installed-an-agent-and-dont-see-any-activities-or-logs"></a>O que vem a seguir? Instalei um agente e não vejo nenhuma atividade ou registos...

1. Verifique se o tipo de [agente se encaixa na plataforma de OS designado do seu dispositivo](how-to-deploy-agent.md)

1. Confirme que o [agente está a funcionar no dispositivo.](how-to-agent-configuration.md)

1. Verifique se o [serviço foi ativado com sucesso](quickstart-onboard-iot-hub.md) para a **Segurança** no seu Hub IoT.

1. Verifique se o dispositivo está [configurado no IoT Hub com o Centro](quickstart-create-security-twin.md)de Segurança Azure para o módulo IoT .

Se as atividades ou registos ainda estiverem indisponíveis, contacte o seu Centro de Segurança Azure para obter ajuda adicional.

## <a name="what-happens-when-the-internet-connection-stops-working"></a>O que acontece quando a ligação à Internet deixa de funcionar?

O agente continua a executar e a armazenar dados enquanto o dispositivo estiver em funcionamento. Os dados são armazenados na cache da mensagem de segurança de acordo com a configuração do tamanho. Quando o dispositivo recuperar a conectividade, as mensagens de segurança retomam o envio.

## <a name="if-the-device-is-restarted-will-the-security-agent-self-recover"></a>Se o dispositivo for reiniciado, o agente de segurança vai se recuperar?

O agente de segurança foi concebido para ser reexecutado automaticamente com o reinício de cada dispositivo.

## <a name="can-the-agent-affect-the-performance-of-the-device-or-other-installed-software"></a>O agente pode afetar o desempenho do dispositivo ou de outro software instalado?

O agente consome recursos de máquinas como qualquer outra aplicação/processo e não deve perturbar a atividade normal do dispositivo. O consumo de recursos no dispositivo em que o agente funciona é associado à sua configuração e configuração. Recomendamos testar a configuração do seu agente num ambiente contido, juntamente com a interoperabilidade com as suas outras aplicações e funcionalidades IoT, antes de tentar implementar num ambiente de produção.

## <a name="im-making-some-maintenance-on-the-device-can-i-turn-off-the-agent"></a>Estou a fazer manutenção no dispositivo. Posso desligar o agente?

O agente não pode ser desligado.

## <a name="is-there-a-way-to-test-if-the-agent-is-working-correctly"></a>Há alguma maneira de testar se o agente está a trabalhar corretamente?

Se o agente parar de comunicar ou não enviar mensagens de segurança, é gerado um alerta silencioso do **Dispositivo.**

## <a name="can-i-create-my-own-alerts"></a>Posso criar os meus próprios alertas?

Sim. Pode definir um alerta personalizado sobre um conjunto de comportamentos pré-determinados, tais como endereço IP e portas abertas. Consulte [criar alertas personalizados](quickstart-create-custom-alerts.md) para saber mais sobre alertas personalizados e como fazê-los.

## <a name="where-can-i-see-logs-can-i-customize-logs"></a>Onde posso ver troncos? Posso personalizar registos?

- Consulte alertas e recomendações utilizando o seu espaço de trabalho de Log Analytics conectado. Configure o tamanho e duração do armazenamento no espaço de trabalho.

- Os dados brutos do seu agente de segurança também podem ser armazenados na sua conta Log Analytics. Considere o tamanho, duração, requisitos de armazenamento e custos associados antes de alterar a configuração desta opção.

## <a name="why-should-i-add-azure-security-center-for-iot-to-the-module-identity-what-is-it-used-for"></a>Por que devo adicionar o Azure Security Center for IoT à identidade do módulo? Para que é usado?

O Centro de Segurança Azure para módulo IoT é utilizado para configuração e gestão de agentes.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre como começar com o Azure Security Center for IoT, consulte os seguintes artigos:

- Leia o Centro de Segurança Azure para [visão geral](overview.md) do IoT
- Verifique os [pré-requisitos](service-prerequisites.md) do Serviço
- Saiba mais sobre como [começar](getting-started.md)
- Compreender o [Centro de Segurança Azure para alertas de segurança ioT](concept-security-alerts.md)
