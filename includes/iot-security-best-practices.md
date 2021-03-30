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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "72793140"
---
Garantir uma infraestrutura de Internet das Coisas (IoT) requer uma estratégia rigorosa em profundidade em segurança. Esta estratégia requer que você proteja os dados na nuvem, proteja a integridade dos dados durante o transporte através da internet pública e dispositivos de fornecimento de segurança. Cada camada constrói uma maior garantia de segurança na infraestrutura global.

## <a name="secure-an-iot-infrastructure"></a>Garantir uma infraestrutura IoT

Esta estratégia de segurança aprofundada pode ser desenvolvida e executada com a participação ativa de vários intervenientes envolvidos na fabricação, desenvolvimento e implantação de dispositivos e infraestruturas IoT. Segue-se uma descrição de alto nível destes jogadores.

* **Fabricante/integrador de hardware IoT**: Normalmente, estes atores são os fabricantes de hardware IoT que estão a ser implantados, integradores que montam hardware de vários fabricantes, ou fornecedores que fornecem hardware para uma implementação IoT fabricada ou integrada por outros fornecedores.

* Desenvolvedor de **soluções IoT**: O desenvolvimento de uma solução IoT é normalmente feito por um desenvolvedor de soluções. Este desenvolvedor pode fazer parte de uma equipa interna ou de um integrador de sistema (SI) especializado nesta atividade. O desenvolvedor de soluções IoT pode desenvolver vários componentes da solução IoT de raiz, integrar vários componentes off-the-shelf ou open-source, ou adotar aceleradores de solução com pequena adaptação.

* **Desdobrador de solução IoT**: Depois de uma solução IoT ser desenvolvida, tem de ser implantada no campo. Este processo envolve a implementação de hardware, interconexão de dispositivos e implementação de soluções em dispositivos de hardware ou na nuvem.

* **Operador de solução IoT**: Após a implementação da solução IoT, requer operações a longo prazo, monitorização, atualizações e manutenção. Estas tarefas podem ser feitas por uma equipa interna que inclui especialistas em tecnologias da informação, operações de hardware e equipas de manutenção, e especialistas em domínios que monitorizam o comportamento correto da infraestrutura geral de IoT.

As secções que se seguem proporcionam boas práticas para cada um destes intervenientes para ajudar a desenvolver, implementar e operar uma infraestrutura de IoT segura.

## <a name="iot-hardware-manufacturerintegrator"></a>Fabricante/integrador de hardware IoT

Seguem-se as melhores práticas para fabricantes de hardware IoT e integradores de hardware.

* **Hardware de âmbito aos requisitos mínimos**: O design do hardware deve incluir as características mínimas necessárias para o funcionamento do hardware, e nada mais. Um exemplo é incluir portas USB apenas se necessário para o funcionamento do dispositivo. Estas funcionalidades adicionais abrem o dispositivo para vetores de ataque indesejados que devem ser evitados.

* **Faça prova de adulteração** de hardware : Construa em mecanismos para detetar manipulações físicas, tais como a abertura da tampa do dispositivo ou a remoção de uma parte do dispositivo. Estes sinais de adulteração podem fazer parte do fluxo de dados enviado para a nuvem, o que pode alertar os operadores destes eventos.

* **Construa em torno de hardware seguro**: Se o COGS permitir, construa funcionalidades de segurança como armazenamento seguro e encriptado ou funcionalidade de boot com base no Módulo de Plataforma Fidedigna (TPM). Estas funcionalidades tornam os dispositivos mais seguros e ajudam a proteger a infraestrutura IoT global.

* **Torne as atualizações seguras**: As atualizações do Firmware durante o período de vida do dispositivo são inevitáveis. Os dispositivos de construção com caminhos seguros para upgrades e garantia criptográfica das versões do firmware permitirão que o dispositivo seja seguro durante e após as atualizações.

## <a name="iot-solution-developer"></a>Desenvolvedor de soluções IoT

Seguem-se as melhores práticas para os desenvolvedores de soluções IoT:

* **Siga a metodologia segura** de desenvolvimento do software : O desenvolvimento de software seguro requer uma reflexão sobre segurança, desde o início do projeto até à sua implementação, teste e implementação. As escolhas de plataformas, idiomas e ferramentas são todas influenciadas com esta metodologia. O Microsoft Security Development Lifecycle fornece uma abordagem passo a passo para a construção de software seguro.

* **Escolha software de código aberto com cuidado**: O software de código aberto oferece uma oportunidade para desenvolver rapidamente soluções. Ao escolher software de código aberto, considere o nível de atividade da comunidade para cada componente de código aberto. Uma comunidade ativa garante que o software é suportado e que os problemas são descobertos e tratados. Em alternativa, um projeto de software de código aberto obscuro e inativo pode não ser suportado e os problemas não são provavelmente descobertos.

* **Integrar-se com cuidados**: Existem muitas falhas de segurança de software no limite das bibliotecas e APIs. A funcionalidade que pode não ser necessária para a implementação atual ainda pode estar disponível através de uma camada API. Para garantir a segurança geral, certifique-se de verificar todas as interfaces de componentes que estão a ser integrados para falhas de segurança.

## <a name="iot-solution-deployer"></a>Desdobrador de solução IoT

Seguem-se as melhores práticas para os desdobradores de soluções IoT:

* **Implementar hardware de forma segura**: As implementações de IoT podem exigir que o hardware seja implantado em locais inseguros, tais como em espaços públicos ou locais não supervisionados. Nestas situações, certifique-se de que a implementação de hardware é à prova de violação na máxima medida. Se estiverem disponíveis USB ou outras portas no hardware, certifique-se de que estão cobertas de forma segura. Muitos vetores de ataque podem usá-los como pontos de entrada.

* **Mantenha as chaves de autenticação seguras**: Durante a implantação, cada dispositivo necessita de IDs do dispositivo e chaves de autenticação associadas geradas pelo serviço de nuvem. Mantenha estas chaves fisicamente seguras mesmo após a colocação. Qualquer chave comprometida pode ser usada por um dispositivo malicioso para disfarçar-se como um dispositivo existente.

## <a name="iot-solution-operator"></a>Operador de solução IoT

Seguem-se as melhores práticas para os operadores de soluções IoT:

* Mantenha o sistema atualizado : **Certifique-se de** que os sistemas operativos do dispositivo e todos os controladores de dispositivos são atualizados para as versões mais recentes. Se ligar atualizações automáticas no Windows 10 (IoT ou outros SKUs), a Microsoft mantém-na atualizada, fornecendo um sistema operativo seguro para dispositivos IoT. Manter outros sistemas operativos (como o Linux) atualizados ajuda a garantir que também estão protegidos contra ataques maliciosos.

* **Proteger contra atividades maliciosas**: Se o sistema operativo permitir, instale as mais recentes capacidades antivírus e antimalware em cada sistema operativo do dispositivo. Esta prática pode ajudar a mitigar a maioria das ameaças externas. Pode proteger a maioria dos sistemas operativos modernos contra ameaças, tomando as medidas adequadas.

* **Auditoria frequentemente**: Auditar infraestruturas IoT para questões relacionadas com a segurança é fundamental para responder a incidentes de segurança. A maioria dos sistemas operativos fornece registos de eventos incorporados que devem ser revistos frequentemente para garantir que não ocorreu nenhuma falha de segurança. As informações de auditoria podem ser enviadas como um fluxo de telemetria separado para o serviço de nuvem onde podem ser analisadas.

* **Proteger fisicamente a infraestrutura IoT**: Os piores ataques de segurança contra a infraestrutura IoT são lançados usando acesso físico a dispositivos. Uma prática de segurança importante é proteger contra o uso malicioso de portas USB e outros acessos físicos. Uma das chaves para descobrir as falhas que podem ter ocorrido é o registo de acesso físico, como o uso da porta USB. Mais uma vez, o Windows 10 (IoT e outros SKUs) permite o registo detalhado destes eventos.

* **Proteger credenciais de nuvem**: Credenciais de autenticação em nuvem utilizadas para configurar e operar uma implementação IoT são possivelmente a forma mais fácil de aceder e comprometer um sistema IoT. Proteja as credenciais alterando frequentemente a palavra-passe e abstendo-se de utilizar estas credenciais em máquinas públicas.

As capacidades de diferentes dispositivos IoT variam. Alguns dispositivos podem ser computadores que executam sistemas operativos comuns para desktop, e alguns dispositivos podem estar a executar sistemas operativos de peso muito leve. As melhores práticas de segurança descritas anteriormente podem ser aplicáveis a estes dispositivos em graus variados. Se for fornecida, devem ser seguidas as melhores práticas de segurança e de implantação adicionais por parte dos fabricantes destes dispositivos.

Alguns dispositivos legados e constrangidos podem não ter sido concebidos especificamente para a implantação de IoT. Estes dispositivos podem não ter a capacidade de encriptar dados, conectar-se com a Internet ou fornecer auditorias avançadas. Nestes casos, um gateway de campo moderno e seguro pode agregar dados de dispositivos antigos e fornecer a segurança necessária para ligar estes dispositivos através da Internet. Os gateways de campo podem fornecer autenticação segura, negociação de sessões encriptadas, receção de comandos da nuvem e muitas outras funcionalidades de segurança.