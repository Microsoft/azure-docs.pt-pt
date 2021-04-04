---
title: Visão geral da gestão de dispositivos com a Azure IoT Hub | Microsoft Docs
description: Visão geral da gestão de dispositivos em Azure IoT Hu -- ciclo de vida de dispositivos empresariais e padrões de gestão de dispositivos como, reboot, reset de fábrica, atualização de firmware, configuração, gémeos de dispositivo, consultas, empregos.
author: bzurcher
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/24/2017
ms.author: briz
ms.custom:
- 'Role: Cloud Development'
- 'Role: IoT Device'
- 'Role: System Architecture'
ms.openlocfilehash: 9ff8ebeaf7c4a219bc403c81dfea62f4657a1fd8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92148344"
---
# <a name="overview-of-device-management-with-iot-hub"></a>Descrição geral da gestão de dispositivos com o Hub IoT

O Hub IoT do Azure fornece as funcionalidades e um modelo de extensibilidade que permitem ao dispositivo e aos programadores de back-end criarem soluções de gestão de dispositivos robustas. Os dispositivos variam desde sensores restritos e microcontroladores com um objetivo único, a gateways poderosos que encaminham as comunicações para grupos de dispositivos.  Além disso, os casos de utilização e os requisitos para operadores de IoT variam significativamente entre os setores.  Apesar desta variação, a gestão de dispositivos com o Hub IoT fornece as capacidades, os padrões e as bibliotecas de código para atender um conjunto diverso de dispositivos e utilizadores finais.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Uma parte fundamental da criação de uma solução de IoT empresarial bem-sucedida é fornecer uma estratégia para a forma como os operadores vão processar a gestão contínua da coleção de dispositivos. Os operadores de IoT requerem ferramentas simples e fiáveis e aplicações e que lhes permitam concentrar-se em aspetos mais estratégicos das funções deles. Este artigo fornece:

* Uma breve descrição geral da abordagem do Hub IoT perante a gestão de dispositivos.
* Uma descrição dos princípios de gestão de dispositivos comuns.
* Uma descrição do ciclo de vida do dispositivo.
* Uma descrição geral dos padrões de gestão de dispositivos comuns.

## <a name="device-management-principles"></a>Princípios da gestão de dispositivos

O IoT inclui uma série de desafios de gestão de dispositivos únicos e qualquer solução de classe empresarial tem de abordar os seguintes princípios:

![Gráfico dos princípios da gestão de dispositivos](media/iot-hub-device-management-overview/image4.png)

* **Escala e automatização**: as soluções IoT requerem ferramentas simples que consigam automatizar tarefas de rotina e permitam que uma equipa de operações relativamente pequena faça a gestão de milhões de dispositivos. Todos os dias, os operadores esperam processar operações com dispositivos remotamente, em massa, e serem alertados apenas quando ocorrem problemas que precisam da atenção direta deles.

* **Acessibilidade e compatibilidade**: o ecossistema de dispositivos é extremamente diversificado. As ferramentas de gestão têm de ser adaptadas, de modo a acomodarem uma multitude de classes de dispositivos, plataformas e protocolos. Os operadores têm de conseguir suportar muitos tipos de dispositivos, desde os mais restritos chips de processamento único incorporados a computadores poderosos e totalmente funcionais.

* **Perceção do contexto**: os ambientes de IoT são dinâmicos e estão em constante mudança. A fiabilidade do serviço é crucial. As operações de gestão de dispositivos devem ter em consideração os seguintes fatores para assegurar que esse período de indisponibilidade devido a manutenção não afeta operações empresariais críticas nem cria condições perigosas:

    * Janelas de manutenção de SLA
    * Estados de rede e de energia
    * Condições em utilização
    * Geolocalização de dispositivos

* **Dar assistência a muitas funções**: o suporte para os fluxos de trabalho e os processos exclusivos das funções de operações de IoT é crucial. A equipa de operações tem de trabalhar em harmonia com as restrições dadas de departamentos de TI internos.  Ela também tem de encontrar formas sustentáveis de fornecer informações de operações de dispositivos em tempo real a supervisores e outras funções de gestão empresarial.

## <a name="device-lifecycle"></a>Ciclo de vida dos dispositivos
Existe um conjunto de fases de gestão de dispositivos geral que são comuns a todos os projetos de IoT empresarial. No Azure IoT, existem cinco fases do ciclo de vida dos dispositivos:

![As cinco fases do ciclo de vida de dispositivos do Azure IoT: planear, aprovisionar, configurar, monitorizar, extinguir](./media/iot-hub-device-management-overview/image5.png)

Dentro de cada uma destas cinco fases, existem vários requisitos de operador de dispositivo que devem ser cumpridos para disponibilizar uma solução completa:

* **Planear**: permitir aos operadores criarem um esquema de metadados de dispositivo que lhes possibilite, de forma fácil e precisa, consultarem e segmentarem um grupo de dispositivos para operações de gestão em massa. Pode utilizar os twins do dispositivo para armazenar metadados deste dispositivo sob a forma de etiquetas e propriedades.
  
    *Continuar a ler*: 
    * [Introdução aos dispositivos duplos](iot-hub-node-node-twin-getstarted.md)
    * [Compreender os dispositivos duplos](iot-hub-devguide-device-twins.md)
    * [Como usar propriedades gémeas do dispositivo](tutorial-device-twins.md)
    * [Melhores práticas para a configuração do dispositivo dentro de uma solução IoT](iot-hub-configuration-best-practices.md)

* **Aprovisionar**: aprovisionar em segurança dispositivos novos no Hub IoT e permitir aos operadores descobrir imediatamente as capacidades dos dispositivos.  Utilize o registo de dispositivos do Hub para criar identidades e credenciais de dispositivos flexíveis e efetuar esta operação em massa através de uma tarefa. Crie dispositivos para comunicar as respetivas capacidades e condições através de propriedades dos dispositivos no twin do dispositivo.
  
    *Continuar a ler*: 
    * [Gerir identidades do dispositivo](iot-hub-devguide-identity-registry.md)
    * [Gestão a granel das identidades dos dispositivos](iot-hub-bulk-identity-mgmt.md)
    * [Como usar propriedades gémeas do dispositivo](tutorial-device-twins.md)
    * [Melhores práticas para a configuração do dispositivo dentro de uma solução IoT](iot-hub-configuration-best-practices.md)
    * [Serviço Aprovisionamento de Dispositivos no Hub IoT do Azure](https://azure.microsoft.com/documentation/services/iot-dps)

* **Configurar**: facilitar alterações de configuração e atualizações de firmware em massa nos dispositivos, preservando o estado de funcionamento e as segurança. Efetue estas operações de gestão de dispositivos em massa com as propriedades pretendidas ou com métodos diretos e tarefas de difusão.
  
    *Continuar a ler*:
    * [Como usar propriedades gémeas do dispositivo](tutorial-device-twins.md)
    * [Configurar e monitorizar dispositivos IoT à escala](./iot-hub-automatic-device-management.md)
    * [Melhores práticas para a configuração do dispositivo dentro de uma solução IoT](iot-hub-configuration-best-practices.md)

* **Monitorizar**: monitorizar o estado de funcionamento geral da coleção de dispositivos, o estado de operações contínuas e alertar os operadores relativamente a problemas que possam precisar da atenção deles.  Aplique o twin do dispositivo para permitir aos dispositivos comunicarem as condições de funcionamento em tempo real e o estado das operações de atualização. Crie relatórios de dashboards eficientes que emitem os problemas mais imediatos com consultas twin do dispositivo.
  
    *Continuar a ler*: 
    * [Como usar propriedades gémeas do dispositivo](tutorial-device-twins.md)
    * [IoT Hub linguagem de consulta para gémeos de dispositivo, empregos e encaminhamento de mensagens](iot-hub-devguide-query-language.md)
    * [Configurar e monitorizar dispositivos IoT à escala](./iot-hub-automatic-device-management.md)
    * [Melhores práticas para a configuração do dispositivo dentro de uma solução IoT](iot-hub-configuration-best-practices.md)

* **Aposentar-se**: Substitua ou desative os dispositivos após uma avaria, ciclo de atualização ou no final do tempo de vida útil do serviço.  Utilize o twin do dispositivo para manter informações do dispositivo se o dispositivo físico estiver a ser substituído, ou arquivar se estiver a ser retirado. Utilize o registo de identidades do Hub IoT para revogar de forma segura identidades e credenciais de dispositivos.
  
    *Continuar a ler*: 
    * [Como usar propriedades gémeas do dispositivo](tutorial-device-twins.md)
    * [Gerir identidades do dispositivo](iot-hub-devguide-identity-registry.md)

## <a name="device-management-patterns"></a>Padrões da gestão de dispositivos

O Hub IoT permite o conjunto de padrões de gestão de dispositivos seguinte. Os [tutoriais da gestão de dispositivos](iot-hub-node-node-device-management-get-started.md) mostram-lhe mais detalhadamente como pode expandir estes padrões para se ajustarem ao seu cenário exato e como desenhar novos padrões com base nestes modelos de núcleo.

* **Reboot**: A aplicação back-end informa o dispositivo através de um método direto que iniciou um reboot.  O dispositivo utiliza as propriedades reportadas no para atualizar o respetivo estado de reinício.
  
    ![Gráfico do padrão de reinício de gestão de dispositivos](./media/iot-hub-device-management-overview/reboot-pattern.png)

* **Reposição de Fábrica**: A aplicação back-end informa o dispositivo através de um método direto que iniciou um reset de fábrica. O dispositivo utiliza as propriedades reportadas para atualizar o respetivo estado da reposição de fábrica.
  
    ![Gráfico do padrão de reposição de fábrica de gestão de dispositivos](./media/iot-hub-device-management-overview/facreset-pattern.png)

* **Configuração**: A aplicação back-end utiliza as propriedades desejadas para configurar o software em execução no dispositivo. O dispositivo utiliza as propriedades reportadas para atualizar o respetivo estado de configuração.
  
    ![Gráfico do padrão de configuração de gestão de dispositivos](./media/iot-hub-device-management-overview/configuration-pattern.png)

* **Atualização do Firmware**: A aplicação back-end utiliza uma configuração automática de gestão de dispositivos para selecionar os dispositivos para receber a atualização, para dizer aos dispositivos onde encontrar a atualização e monitorizar o processo de atualização. O dispositivo inicia um processo de várias etapas para descarregar, verificar e aplicar a imagem do firmware e, em seguida, reiniciar o dispositivo antes de voltar a ligar-se ao serviço IoT Hub. Ao longo do processo de vários passos, o dispositivo utiliza as propriedades reportadas para atualizar o respetivo progresso e estado.
  
    ![Gráfico do padrão de atualização de firmware de gestão de dispositivos](media/iot-hub-device-management-overview/fwupdate-pattern.png)

* **Reportando o progresso e o estado**: A solução traseira executa consultas gémeas do dispositivo, através de um conjunto de dispositivos, para informar sobre o estado e o progresso das ações em execução nos dispositivos.
  
    ![Gráfico do padrão de estado e progresso dos relatórios de gestão de dispositivos](./media/iot-hub-device-management-overview/report-progress-pattern.png)

## <a name="next-steps"></a>Passos Seguintes

As capacidades, padrões e bibliotecas de código que o Hub IoT fornece para gestão de dispositivos permitem criar aplicações IoT que satisfazem os requisitos do operador de IoT empresarial em cada fase do ciclo de vida do dispositivo.

Para continuar a saber mais sobre as funcionalidades de gestão de dispositivos no Hub IoT, veja o tutorial [Get started with device management (Introdução à gestão de dispositivos)](iot-hub-node-node-device-management-get-started.md).