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
ms.openlocfilehash: 1aeab1a7dcdf2b12efc268ed0d47834b24d34b87
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82734982"
---
# <a name="azure-security-center-for-iot-frequently-asked-questions"></a>Perguntas mais frequentes sobre o Centro de Segurança do Azure para IoT

Este artigo fornece uma lista de perguntas e respostas frequentes sobre o Azure Security Center para IoT.

## <a name="does-azure-provide-support-for-iot-security"></a>O Azure fornece apoio à segurança da IoT?

O Azure oferece uma visão integrada para monitorizar e gerir a sua segurança IoT como parte da sua solução de segurança global através do Azure Security Center. Se você é um desenvolvedor de aplicações, você pode usar a vista IoT Hub para gerir a segurança da sua aplicação IoT.

## <a name="what-is-azures-unique-value-proposition-for-iot-security"></a>Qual é a proposta de valor única do Azure para a segurança IoT?

O Azure Security Center for IoT permite às empresas alargar a sua visão de segurança cibernética existente a toda a sua solução IoT. O Azure oferece um fim à visão final da sua solução de negócio, permitindo-lhe tomar ações e decisões relacionadas com o negócio com base na postura de segurança da empresa e dados recolhidos. A segurança combinada utilizando o Azure IoT, Azure IoT Edge e Azure Security Center permitem-lhe criar a solução que pretende com a segurança de que necessita.

## <a name="who-is-azure-security-center-for-iot-made-for"></a>Para quem é o Centro de Segurança Azure para ioT feito?

O Azure Security Center for IoT está integrado no Azure IoT Hub Security e fornece gestão para as operações de segurança da solução de negócio do dia-a-dia. O Azure Security Center for IoT também está integrado nas capacidades do Azure Security Center e fornece uma visão integrada para monitorizar e gerir a sua segurança IoT como parte da sua solução de segurança global.

## <a name="how-does-azure-security-center-for-iot-compare-to-the-competition"></a>Como é que o Azure Security Center para IoT se compara à concorrência?

Enquanto outras soluções fornecem um conjunto de capacidades que permitem aos clientes criar as suas próprias soluções, o Azure Security Center for IoT fornece uma solução de segurança IoT de ponta a ponta que proporciona uma ampla visão sobre a segurança de todos os seus recursos Azure relacionados. O Azure permite uma rápida implantação e integração completa com os gémeos módulos IoT Hub para uma fácil integração com as ferramentas de gestão de dispositivos existentes.

## <a name="do-i-have-to-be-an-azure-security-center-customer-to-use-this-service"></a>Tenho de ser um cliente do Azure Security Center para usar este serviço?

Não, mas é recomendado. Sem o Azure Security Center, o Azure Security Center for IoT recebe dados de recursos conectados limitados e fornece uma análise limitada da sua potencial superfície de ataque, ameaças e potenciais ataques.

## <a name="do-i-have-to-be-an-azure-iot-customer"></a>Tenho de ser cliente do Azure IoT?

Sim. O Centro de Segurança Azure para IoT depende da conectividade e infraestrutura do Azure IoT.

## <a name="do-i-have-to-install-an-agent"></a>Tenho de instalar um agente?

A instalação de agentes nos seus dispositivos IoT não é obrigatória para ativar o Microsoft Azure Security Center para IoT. Pode escolher entre as três opções seguintes, ganhando diferentes níveis de capacidade de monitorização e gestão de segurança de acordo com a sua seleção:

1. Instale o Centro de Segurança Azure para o agente de segurança IoT com ou sem modificações. Esta opção fornece o mais alto nível de informações de segurança melhoradas sobre o comportamento e acesso do dispositivo.

1. Crie o seu próprio agente e implemente o Microsoft Azure Security Center para esquema de mensagens de segurança IoT. Esta opção permite a utilização do Microsoft Azure Security Center para ferramentas de análise IoT em cima do seu agente de segurança do dispositivo.

1. Nenhuma instalação de agente de segurança nos seus dispositivos IoT. Esta opção permite a monitorização da comunicação do IoT Hub, com capacidades reduzidas de monitorização e gestão de segurança.

## <a name="what-does-the-azure-security-center-for-iot-agent-do"></a>O que faz o Centro de Segurança Azure para agente IoT?

O Azure Security Center for IoT agent fornece cobertura de ameaça de nível do dispositivo para configuração, comportamento e acesso do dispositivo (digitalizando a configuração), processo & conectividade. O Centro de Segurança Azure para o agente de segurança IoT não digitaliza dados ou atividades relacionadas com o negócio.

## <a name="where-can-i-get-the-azure-security-center-for-iot-security-agent"></a>Onde posso arranjar o Centro de Segurança Azure para o agente de segurança IoT?

O Azure Security Center for IoT security agent está disponível no GitHub em versões Windows e Linux de 32 bits e 64 bits: https://github.com/Azure/Azure-IoT-Security .

## <a name="where-does-the-azure-security-center-for-iot-agent-get-installed"></a>Onde é que o Centro de Segurança Azure para agente IoT é instalado?

Informações detalhadas de instalação e implantação de agentes podem ser encontradas no GitHub: https://github.com/Azure/Azure-IoT-Security .

## <a name="what-are-the-dependencies-and-prerequisites-of-the-agent"></a>Quais são as dependências e pré-requisitos do agente?

O Azure Security Center for IoT suporta uma grande variedade de plataformas. Consulte [as plataformas do Dispositivo Suportado](how-to-deploy-agent.md) para verificar o suporte aos seus dispositivos específicos.

## <a name="which-data-is-collected-by-the-agent"></a>Que dados são recolhidos pelo agente?

Conectividade, acesso, configuração de firewall, lista de processos & linha de base OS são recolhidos pelo agente.

## <a name="how-much-data-will-the-agent-generate"></a>Quantos dados o agente vai gerar?

A geração de dados do agente é impulsionada pela configuração do dispositivo, aplicação, tipo de conectividade e agente de cliente. Devido à elevada variabilidade entre dispositivos e soluções IoT, recomendamos primeiro a colocação do agente num laboratório ou definição de teste para observar, aprender e definir a configuração específica que se adequa às suas necessidades, ao mesmo tempo que mede a quantidade de dados gerados. Após o início do serviço, o Azure Security Center for IoT agent fornece recomendações operacionais para otimizar o rendimento do agente para ajudá-lo com o processo de configuração e personalização.

## <a name="how-can-i-control-my-billing"></a>Como posso controlar a minha faturação?

O Azure Security Center for IoT fornece digitalizações de agentes configuráveis, tampão de dados e a capacidade de criar alertas personalizados que aumentem ou reduzam a quantidade de dados gerados pelo agente.

## <a name="do-agent-messages-use-up-quota-from-iot-hub"></a>As mensagens de agente usam quota de IoT Hub?

Sim. Os dados transmitidos pelo agente são contados na sua quota IoT Hub.

## <a name="what-next-ive-installed-an-agent-and-dont-see-any-activities-or-logs"></a>O que se segue? Instalei um agente e não vejo atividades ou registos...

1. Verifique se o [tipo de agente se encaixa na plataforma de SO designada do seu dispositivo](how-to-deploy-agent.md)

1. Confirme que o [agente está a trabalhar no aparelho](how-to-agent-configuration.md).

1. Verifique se o [serviço foi ativado com sucesso](quickstart-onboard-iot-hub.md) para a **Segurança** no seu Hub IoT.

1. Verifique se o dispositivo está [configurado no IoT Hub com o Centro de Segurança Azure para o módulo IoT](quickstart-create-security-twin.md).

Se as atividades ou registos ainda não estiverem disponíveis, contacte o seu Centro de Segurança Azure para obter ajuda adicional.

## <a name="what-happens-when-the-internet-connection-stops-working"></a>O que acontece quando a ligação à internet deixa de funcionar?

O agente continua a executar e a armazenar dados enquanto o dispositivo estiver em funcionamento. Os dados são armazenados na cache da mensagem de segurança de acordo com a configuração do tamanho. Quando o dispositivo recupera a conectividade, as mensagens de segurança retomam o envio.

## <a name="if-the-device-is-restarted-will-the-security-agent-self-recover"></a>Se o dispositivo for reiniciado, o agente de segurança irá auto-recuperar??

O agente de segurança foi concebido para voltar a repetir-se automaticamente com o reinício de cada dispositivo.

## <a name="can-the-agent-affect-the-performance-of-the-device-or-other-installed-software"></a>O agente pode afetar o desempenho do dispositivo ou de outro software instalado?

O agente consome recursos de máquina como qualquer outra aplicação/processo e não deve perturbar a atividade normal do dispositivo. O consumo de recursos no dispositivo em que o agente funciona é associado à sua configuração e configuração. Recomendamos testar a configuração do seu agente num ambiente contido, juntamente com a interoperabilidade com as suas outras aplicações e funcionalidades IoT, antes de tentar implantar-se num ambiente de produção.

## <a name="im-making-some-maintenance-on-the-device-can-i-turn-off-the-agent"></a>Estou a fazer alguma manutenção no dispositivo. Posso desligar o agente?

O agente não pode ser desligado.

## <a name="is-there-a-way-to-test-if-the-agent-is-working-correctly"></a>Há alguma maneira de testar se o agente está a funcionar corretamente?

Se o agente deixar de comunicar ou não enviar mensagens de segurança, é gerado um alerta **silencioso.**

## <a name="can-i-create-my-own-alerts"></a>Posso criar os meus próprios alertas?

Sim. Pode definir um alerta personalizado sobre um conjunto pré-determinado de comportamentos como endereço IP e portas abertas. Consulte [Criar alertas personalizados](quickstart-create-custom-alerts.md) para saber mais sobre alertas personalizados e como os fazer.

## <a name="where-can-i-see-logs-can-i-customize-logs"></a>Onde posso ver os troncos? Posso personalizar registos?

- Ver alertas e recomendações utilizando o seu espaço de trabalho log analytics conectado. Configure o tamanho e a duração do armazenamento no espaço de trabalho.

- Os dados brutos do seu agente de segurança também podem ser armazenados na sua conta Log Analytics. Considere o tamanho, duração, requisitos de armazenamento e custos associados antes de alterar a configuração desta opção.

## <a name="why-should-i-add-azure-security-center-for-iot-to-the-module-identity-what-is-it-used-for"></a>Por que devo adicionar o Azure Security Center para IoT à identidade do módulo? Para que é usado?

O Azure Security Center for IoT module é utilizado para a configuração e gestão do agente.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre como começar com o Azure Security Center for IoT, consulte os seguintes artigos:

- Leia o Centro de Segurança Azure para [visão geral do IoT](overview.md)
- Verifique os [pré-requisitos](service-prerequisites.md) do Serviço
- Saiba mais sobre como [começar](getting-started.md)
- Compreenda [o Centro de Segurança Azure para alertas de segurança IoT](concept-security-alerts.md)
