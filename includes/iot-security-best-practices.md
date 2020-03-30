---
title: incluir ficheiro
description: incluir ficheiro
services: iot-fundamentals
author: robinsh
ms.service: iot-fundamentals
ms.topic: include
ms.date: 08/07/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 4fdb891d668d99644d8a9ed9c15d158e65d53ba5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72793140"
---
A garantia de uma infraestrutura de Internet das Coisas (IoT) requer uma estratégia rigorosa de segurança aprofundada. Esta estratégia requer que você garanta dados na nuvem, proteja a integridade dos dados durante o transporte através da internet pública e proteja os dispositivos. Cada camada constrói uma maior garantia de segurança na infraestrutura global.

## <a name="secure-an-iot-infrastructure"></a>Garantir uma infraestrutura ioT

Esta estratégia de segurança em profundidade pode ser desenvolvida e executada com a participação ativa de vários intervenientes envolvidos na fabricação, desenvolvimento e implantação de dispositivos e infraestruturas IoT. Segue-se uma descrição de alto nível destes jogadores.

* **Fabricante/integrador de hardware IoT**: Normalmente, estes jogadores são os fabricantes de hardware IoT que estão a ser implantados, integradores que montam hardware de vários fabricantes, ou fornecedores que fornecem hardware para uma implementação IoT fabricada ou integrada por outros fornecedores.

* **Desenvolvedor de soluções IoT**: O desenvolvimento de uma solução IoT é normalmente feito por um desenvolvedor de soluções. Este desenvolvedor pode fazer parte de uma equipa interna ou de um integrador de sistema (SI) especializado nesta atividade. O desenvolvedor de soluções IoT pode desenvolver vários componentes da solução IoT de raiz, integrar vários componentes off-the-shelf ou open-source, ou adotar aceleradores de soluções com pequena adaptação.

* **Implantador de soluções IoT**: Depois de ser desenvolvida uma solução IoT, tem de ser implantada no terreno. Este processo envolve a implementação de hardware, interligação de dispositivos e implementação de soluções em dispositivos de hardware ou na nuvem.

* **Operador de solução IoT**: Após a implantação da solução IoT, requer operações a longo prazo, monitorização, atualizações e manutenção. Estas tarefas podem ser feitas por uma equipa interna que compreende especialistas em tecnologias da informação, equipas de hardware e manutenção, e especialistas em domínios que monitorizam o comportamento correto da infraestrutura ioT global.

As secções que se seguem fornecem as melhores práticas para cada um destes jogadores ajudar a desenvolver, implementar e operar uma infraestrutura IoT segura.

## <a name="iot-hardware-manufacturerintegrator"></a>Fabricante/integrador de hardware IoT

Seguem-se as melhores práticas para fabricantes de hardware IoT e integradores de hardware.

* **Tipo de hardware para requisitos mínimos**: O design de hardware deve incluir as funcionalidades mínimas necessárias para o funcionamento do hardware, e nada mais. Um exemplo é incluir portas USB apenas se necessário para o funcionamento do dispositivo. Estas funcionalidades adicionais abrem o dispositivo para vetores de ataque indesejados que devem ser evitados.

* **Faça com prova**de adulteração de hardware : Construa mecanismos para detetar adulteração física, como a abertura da tampa do dispositivo ou a remoção de uma parte do dispositivo. Estes sinais de adulteração podem fazer parte do fluxo de dados enviado para a nuvem, o que pode alertar os operadores destes eventos.

* **Construa em torno de hardware seguro**: Se o COGS o permitir, construa funcionalidades de segurança como armazenamento seguro e encriptado, ou funcionalidade de arranque baseada no Módulo de Plataforma Fidedigna (TPM). Estas funcionalidades tornam os dispositivos mais seguros e ajudam a proteger a infraestrutura ioT global.

* **Segurança:** As atualizações de firmware durante o tempo de vida do dispositivo são inevitáveis. Os dispositivos de construção com caminhos seguros para atualizações e garantia criptográfica das versões firmware permitirão que o dispositivo seja seguro durante e após as atualizações.

## <a name="iot-solution-developer"></a>Desenvolvedor de soluções IoT

Seguem-se as melhores práticas para os desenvolvedores de soluções IoT:

* **Siga a metodologia de desenvolvimento de software seguro**: O desenvolvimento de software seguro requer uma reflexão em terra sobre segurança, desde o início do projeto até à sua implementação, teste e implementação. As escolhas de plataformas, línguas e ferramentas são todas influenciadas com esta metodologia. O Microsoft Security Development Lifecycle fornece uma abordagem passo a passo para a construção de software seguro.

* **Escolha software de código aberto com cuidado**: O software de código aberto proporciona uma oportunidade para desenvolver rapidamente soluções. Quando estiver a escolher software de código aberto, considere o nível de atividade da comunidade para cada componente de código aberto. Uma comunidade ativa garante que o software é suportado e que as questões são descobertas e abordadas. Em alternativa, um projeto de software de código aberto obscuro e inativo pode não ser suportado e não são prováveis problemas.

* **Integre com os cuidados**: Existem muitas falhas de segurança de software no limite das bibliotecas e APIs. A funcionalidade que pode não ser necessária para a implementação atual pode ainda estar disponível através de uma camada DePi. Para garantir a segurança geral, certifique-se de verificar todas as interfaces dos componentes que estão a ser integrados para falhas de segurança.

## <a name="iot-solution-deployer"></a>Implantador de soluções IoT

Seguem-se as melhores práticas para os implementadores de soluções IoT:

* **Implementar o hardware de forma segura:** As implementações de IoT podem exigir que o hardware seja implantado em locais não seguros, como em espaços públicos ou locais não supervisionados. Nestas situações, certifique-se de que a implementação do hardware é à prova de adulteração na medida do possível. Se usb ou outras portas estiverem disponíveis no hardware, certifique-se de que estão cobertas de forma segura. Muitos vetores de ataque podem usá-los como pontos de entrada.

* Mantenha as chaves de **autenticação seguras**: Durante a implementação, cada dispositivo necessita de identificações do dispositivo e chaves de autenticação associadas geradas pelo serviço de nuvem. Mantenha estas chaves fisicamente seguras mesmo após a colocação. Qualquer chave comprometida pode ser usada por um dispositivo malicioso para disfarçar-se como um dispositivo existente.

## <a name="iot-solution-operator"></a>Operador de solução IoT

Seguem-se as melhores práticas para os operadores de soluções IoT:

* **Mantenha o sistema atualizado**: Certifique-se de que os sistemas operativos do dispositivo e todos os controladores do dispositivo são atualizados para as versões mais recentes. Se ativar as atualizações automáticas no Windows 10 (IoT ou outros SKUs), a Microsoft mantém-no atualizado, fornecendo um sistema operativo seguro para dispositivos IoT. Manter outros sistemas operativos (como o Linux) atualizados ajuda a garantir que também estão protegidos contra ataques maliciosos.

* **Proteja contra atividades maliciosas**: Se o sistema operativo o permitir, instale as mais recentes capacidades antivírus e antimalware em cada sistema operativo do dispositivo. Esta prática pode ajudar a mitigar a maioria das ameaças externas. Pode proteger a maioria dos sistemas operativos modernos contra ameaças, tomando as medidas adequadas.

* **Auditoria frequentemente**: A auditoria da infraestrutura ioT para questões relacionadas com a segurança é fundamental para responder a incidentes de segurança. A maioria dos sistemas operativos fornece mato de eventos incorporados que devem ser revistos frequentemente para garantir que não ocorreu nenhuma falha de segurança. As informações de auditoria podem ser enviadas como um fluxo de telemetria separado para o serviço de nuvem onde pode ser analisado.

* **Proteja fisicamente a infraestrutura IoT**: Os piores ataques de segurança contra a infraestrutura IoT são lançados utilizando o acesso físico aos dispositivos. Uma prática de segurança importante é proteger contra o uso malicioso de portas USB e outros acessos físicos. Uma das chaves para descobrir as falhas que possam ter ocorrido é o registo de acesso físico, como o uso da porta USB. Mais uma vez, o Windows 10 (IoT e outros SKUs) permite o registo detalhado destes eventos.

* **Proteja as credenciais de nuvem**: As credenciais de autenticação em nuvem utilizadas para configurar e operar uma implementação ioT são possivelmente a forma mais fácil de obter acesso e comprometer um sistema IoT. Proteja as credenciais alterando frequentemente a palavra-passe e abstenha-se de usar estas credenciais em máquinas públicas.

As capacidades de diferentes dispositivos IoT variam. Alguns dispositivos podem ser computadores que executam sistemas operativos comuns para desktop, e alguns dispositivos podem estar a executar sistemas operativos com muito peso leve. As melhores práticas de segurança descritas anteriormente podem ser aplicáveis a estes dispositivos em graus variados. Se for fornecida, devem ser seguidas as melhores práticas de segurança e implementação adicionais por parte dos fabricantes destes dispositivos.

Alguns dispositivos legados e limitados podem não ter sido concebidos especificamente para a implantação de IoT. Estes dispositivos podem não ter a capacidade de encriptar dados, conectar-se com a Internet ou fornecer auditorias avançadas. Nestes casos, um gateway de campo moderno e seguro pode agregar dados de dispositivos legados e fornecer a segurança necessária para ligar estes dispositivos através da Internet. Os gateways de campo podem fornecer autenticação segura, negociação de sessões encriptadas, receção de comandos da nuvem e muitas outras funcionalidades de segurança.