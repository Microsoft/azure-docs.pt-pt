---
title: Perguntas mais frequentes sobre o Centro de segurança do Azure para pré-visualização do IoT | Documentos da Microsoft
description: Encontre respostas a mais perguntas freqüentes sobre o Centro de segurança do Azure para recursos de IoT e o serviço.
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
ms.date: 03/25/2019
ms.author: mlottner
ms.openlocfilehash: 4e04e21d55722b8cb90b277b34b5bffab343c575
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/07/2019
ms.locfileid: "67616583"
---
# <a name="azure-security-center-for-iot-frequently-asked-questions"></a>Centro de segurança do Azure para IoT perguntas mais frequentes  

> [!IMPORTANT]
> Centro de segurança do Azure para IoT está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é fornecido sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este artigo fornece uma lista de perguntas mais frequentes e respostas sobre o Centro de segurança do Azure (ASC) para IoT. 

## <a name="does-azure-provide-support-for-iot-security"></a>O Azure fornece suporte para segurança de IoT?

O Azure fornece uma visão integrada para monitorizar e gerir a segurança de IoT como parte da sua solução de segurança geral através do Centro de segurança do Azure. Se for um programador de aplicações, pode utilizar o modo de exibição do IoT Hub para gerir a segurança de aplicações de IoT.

## <a name="what-is-azures-unique-value-proposition-for-iot-security"></a>O que é a proposta de valor único do Azure para segurança de IoT?

ASC para IoT permite às empresas tornar sua visão de segurança informáticas existente para toda a sua solução IoT. O Azure fornece uma vista de ponto a ponto da sua solução de negócios, permitindo-lhe tomar medidas relacionadas com negócios e decisões com base na sua postura de segurança da empresa e dados recolhidos. Combinado com o IoT do Azure, Azure IoT Edge, Azure Sphere, Azure Central de segurança e o Centro de segurança do Azure permitem-lhe criar a solução que pretende com a segurança de que necessita.

## <a name="who-is-asc-for-iot-made-for"></a>Quem é o ASC para IoT feitas? 

ASC para IoT está integrado na segurança do Azure IoT Hub e fornece gerenciamento para a solução de negócios do dia a dia operações de segurança. ASC para IoT também está integrado de recursos do Centro de segurança do Azure e fornecem uma visão integrada para monitorizar e gerir a segurança de IoT como parte da sua solução de segurança geral.

## <a name="how-does-asc-for-iot-compare-to-the-competition"></a>Como o ASC para IoT compara a competição?

Enquanto outras soluções fornecem um conjunto de capacidades que permitem aos clientes criar suas próprias soluções, o ASC para IoT fornece uma solução de segurança IoT exclusiva ponto-a-ponto fornece uma vista alargada entre a segurança de todos os seus recursos do Azure relacionados. O Azure permite a rápida implementação e integração completa com duplos de módulo do IoT Hub para uma integração fácil com as ferramentas de gestão de dispositivos existentes.

## <a name="do-i-have-to-be-an-azure-security-center-asc-customer"></a>É necessário ser um cliente do Centro de segurança do Azure (ASC)?

Não, mas é recomendado. Sem ASC, o ASC para IoT recebe dados de recursos ligados limitado e fornece uma análise limitada de sua superfície de ataque potencial, ameaças e ataques potenciais. 

## <a name="do-i-have-to-be-an-azure-iot-customer"></a>É necessário ser um cliente do Azure IoT?

Sim. ASC para IoT depende de conectividade de IoT do Azure e infraestrutura.

## <a name="do-i-have-to-install-an-agent"></a>É necessário que instalar um agente?

Instalação do agente nos seus dispositivos IoT não é obrigatória para ativar o ASC Microsoft para IoT. Pode escolher entre três opções seguintes, obter capacidades de monitorização e gestão, de acordo com a seleção de diferentes níveis de segurança:

1. Instale o ASC para o agente de segurança de IoT com ou sem modificações. Esta opção fornece o maior nível de informações de segurança melhorada sobre acesso e o comportamento do dispositivo.

2. Criar seu próprio agente e implemente o Microsoft ASC para esquema de mensagens de segurança de IoT. Esta opção permite a utilização do Microsoft ASC para ferramentas de análise de IoT com base no seu agente de segurança do dispositivo.

3. Nenhuma instalação de agente de segurança nos seus dispositivos IoT. Esta opção permite o IoT Hub comunicação monitoramento, com capacidades de monitorização e gestão de segurança reduzidos. 

## <a name="what-does-the-asc-for-iot-agent-do"></a>O que faz o ASC para o agente de IoT?

ASC para o agente de IoT fornece cobertura de nível de ameaça do dispositivo para a configuração do dispositivo, de comportamento e de acesso (ao verificar a configuração), de processo e de conectividade. O ASC para o agente de segurança de IoT não rastreia a atividade ou dados relacionados com a empresa.

## <a name="where-i-can-get-the-asc-for-iot-security-agent"></a>Onde posso obter o ASC para o agente de segurança de IoT?

O ASC para o agente de segurança de IoT é open source e está disponível no GitHub de 32 bits e 64 bits nas versões Windows e Linux: https://github.com/Azure/Azure-Security-IoT-Preview

## <a name="where-does-the-asc-for-iot-agent-get-installed"></a>Onde o ASC para o agente de IoT está obter a ser instalado? 

Pode encontrar informações detalhadas de instalação e o agente da implementação no GitHub: https://github.com/Azure/Azure-Security-IoT-Preview

## <a name="what-are-the-dependencies-and-prerequisites-of-the-agent"></a>Quais são as dependências e a pré-requisitos do agente?

ASC para IoT suporta uma grande variedade de plataformas. Ver [plataformas de dispositivos suportados](how-to-deploy-agent.md) para verificar o suporte para os seus dispositivos específicos. 

## <a name="which-data-is-collected-by-the-agent"></a>Quais são os dados são recolhidos pelo agente?

Conectividade, acesso, a configuração de firewall, lista de processo e linha de base do sistema operacional são recolhidos pelo agente.

## <a name="how-much-data-will-the-agent-generate"></a>A quantidade de dados irá gerar o agente

Geração de dados do agente é orientada por dispositivo, aplicação, tipo de conectividade e configuração do agente do cliente. Devido a uma variabilidade elevada entre dispositivos e soluções de IoT, recomendamos que primeiro, implemente o agente num laboratório ou de teste na definição de observar, aprenda e definir a configuração específica que se adeque às suas necessidades, ao medir a quantidade de dados gerados. Depois de iniciar o serviço, o ASC para o agente de IoT fornece recomendações operacionais para otimizar o débito de agente para ajudá-lo com o processo de configuração e personalização.

## <a name="how-can-i-control-my-billing"></a>Como posso controlar minha faturação?

ASC para IoT fornece agente configurável, análises, buffers de dados e a capacidade de criar alertas personalizados que aumentam ou reduzem a quantidade de dados gerados pelo agente.

## <a name="do-agent-messages-use-up-quota-from-iot-hub"></a>Mensagens de agente utiliza a quota do IoT Hub?

Sim. Os dados transmitido de agente são contabilizados da sua quota de IoT Hub. 

## <a name="what-next-ive-installed-an-agent-and-dont-see-any-activities-or-logs"></a>O que se segue? Eu tiver instalado um agente e não vir quaisquer atividades ou registos

1. Verifique o [a plataforma de SO designada do seu dispositivo se encaixa de tipo de agente](how-to-deploy-agent.md)

1. Confirmar a [agent está em execução no dispositivo](how-to-agent-configuration.md).

2. Verifique a [serviço foi ativado com êxito](quickstart-onboard-iot-hub.md) ao **segurança** do seu IoT Hub. 

3. Verifique se o dispositivo estiver [configurado no IoT Hub com o ASC para o módulo de IoT](quickstart-create-security-twin.md).  

Se as atividades ou registos são ainda indisponíveis, contacte o seu ASC para parceiro de IoT para obter ajuda adicional.

## <a name="what-happens-when-the-internet-connection-stops-working"></a>O que acontece quando a ligação à internet deixa de funcionar?

O agente continua a executar e armazenar dados, desde que o dispositivo está em execução. Dados são armazenados na cache de mensagem de segurança, de acordo com a configuração de tamanho. Quando o dispositivo servidor restabeleça a conectividade, mensagens de segurança retomar a enviar. 

## <a name="if-the-device-is-restarted-will-the-security-agent-self-recover"></a>Se o dispositivo é reiniciado, o agente de segurança personalizada recuperará?

O agente de segurança foi concebido para voltar a executar automaticamente com cada reinício do dispositivo.

## <a name="can-the-agent-affect-the-performance-of-the-device-or-other-installed-software"></a>O agente pode afetar o desempenho do dispositivo ou outro software instalado?

O agente consome recursos da máquina como qualquer outro aplicativo/processo e não deve interromper a atividade de dispositivo normal. Consumo de recursos no dispositivo que executa o agente está integrado no seu programa de configuração e a configuração. Recomendamos que teste a configuração do agente num ambiente controlado, além de interoperabilidade com os outros aplicativos de IoT e funcionalidade, antes de tentar implementar num ambiente de produção.

## <a name="im-making-some-maintenance-on-the-device-can-i-turn-off-the-agent"></a>Estou fazendo algumas manutenção no dispositivo. Pode desativar o agente?

O agente não pode ser desativado.

## <a name="is-there-a-way-to-test-if-the-agent-is-working-correctly"></a>Existe uma forma de testar se o agente está a funcionar corretamente? 

Se o agente interrompe a comunicar ou não conseguir enviar mensagens de segurança, um **dispositivo é a silencioso** alerta é gerado.

## <a name="can-i-create-my-own-alerts"></a>Posso criar meus próprio alertas?

Sim. Pode definir um alerta personalizado no conjunto predeterminado de comportamentos, como o endereço IP e portas abertas. Ver [criar alertas personalizados](quickstart-create-custom-alerts.md) para saber mais sobre alertas personalizados e como criá-los. 

## <a name="where-can-i-see-logs-can-i-customize-logs"></a>Onde posso ver os registos? Pode personalizar os registos?

- Ver alertas e recomendações com a sua área de trabalho do Log Analytics ligada. Configure o tamanho de armazenamento e a duração na área de trabalho.

- Dados não processados a partir de seu agente de segurança também podem ser armazenados na sua conta do Log Analytics. Considere o tamanho, a duração, os requisitos de armazenamento e os custos associados antes de alterar a configuração desta opção. 

## <a name="why-should-i-add-asc-for-iot-to-the-module-identity-what-is-it-used-for"></a>Por que devo adicionar ASC para IoT para a identidade de módulo? O que ela é utilizada?

O ASC para o módulo de IoT é utilizado para a gestão e configuração do agente.


## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre como começar com o ASC para IoT, veja os artigos seguintes:


- Leia o ASC para IoT [descrição geral](overview.md)
- Verifique se o [pré-requisitos de serviço](service-prerequisites.md)
- Saiba mais sobre como [começar a utilizar](getting-started.md)
- Compreender [ASC para alertas de segurança de IoT](concept-security-alerts.md)

