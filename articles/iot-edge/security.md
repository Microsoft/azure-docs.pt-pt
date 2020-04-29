---
title: Quadro de segurança - Borda Azure IoT [ Microsoft Docs
description: Conheça os padrões de segurança, autenticação e autorização que foram usados para desenvolver o Azure IoT Edge e deve ser considerado à medida que projeta a sua solução
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/30/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 3212493963805de3c8845ec494d87fc92d72998a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76760118"
---
# <a name="security-standards-for-azure-iot-edge"></a>Normas de segurança para Azure IoT Edge

O Azure IoT Edge aborda os riscos inerentes ao mover os seus dados e análises para a borda inteligente. As normas de segurança IoT Edge equilibram a flexibilidade para diferentes cenários de implementação com a proteção que você espera de todos os serviços Azure.

O IoT Edge funciona em várias criações e modelos de hardware, suporta vários sistemas operativos e aplica-se a diversos cenários de implementação. Em vez de oferecer soluções concretas para cenários específicos, o IoT Edge é um quadro de segurança extensível baseado em princípios bem fundamentados que são concebidos para a escala. O risco de um cenário de implantação depende de muitos fatores, incluindo:

* Propriedade da solução
* Geografia de implantação
* Sensibilidade aos dados
* Privacidade
* Aplicação vertical
* Requisitos regulamentares

Este artigo fornece uma visão geral do quadro de segurança ioT Edge. Para mais informações, consulte [A segurança da vantagem inteligente](https://azure.microsoft.com/blog/securing-the-intelligent-edge/).

## <a name="standards"></a>Normas

As normas promovem a facilidade de escrutínio e a facilidade de implementação, ambas marcas de segurança. Uma solução de segurança deve prestar-se ao escrutínio em avaliação para construir confiança e não deve ser um obstáculo à implantação. A conceção do quadro para assegurar o Azure IoT Edge baseia-se em protocolos de segurança comprovados pela indústria para familiaridade e reutilização.

## <a name="authentication"></a>Autenticação

Quando implementa uma solução IoT, precisa de saber que apenas atores, dispositivos e módulos de confiança têm acesso à sua solução. A autenticação baseada em certificados é o principal mecanismo de autenticação para a plataforma Azure IoT Edge. Este mecanismo deriva de um conjunto de normas que regem a infraestrutura de chaves públicas (PKiX) pela Força-Tarefa de Engenharia da Internet (IETF).

Todos os dispositivos, módulos e atores que interagem com o dispositivo Azure IoT Edge devem ter identidades de certificado únicas. Esta orientação aplica-se quer as interações sejam físicas ou através de uma ligação de rede. Nem todos os cenários ou componentes podem emprestar-se à autenticação baseada em certificados, pelo que a extebilidade do quadro de segurança oferece alternativas seguras.

Para mais informações, consulte a [utilização do certificado Azure IoT Edge](iot-edge-certs.md).

## <a name="authorization"></a>Autorização

O princípio do menor privilégio diz que os utilizadores e componentes de um sistema devem ter acesso apenas ao conjunto mínimo de recursos e dados necessários para desempenhar as suas funções. Dispositivos, módulos e atores devem aceder apenas aos recursos e dados dentro do seu âmbito de permissão, e apenas quando for em termos arquitetónicos permitidos. Algumas permissões são configuráveis com privilégios suficientes e outras são aplicadas arquiteticamente. Por exemplo, alguns módulos podem ser autorizados a ligar-se ao Hub Azure IoT. No entanto, não há razão para que um módulo num dispositivo IoT Edge aceda ao gémeo de um módulo noutro dispositivo IoT Edge.

Outros regimes de autorização incluem direitos de assinatura de certificados e controlo de acesso baseado em funções (RBAC).

## <a name="attestation"></a>Atestação

A Attestation garante a integridade dos bits de software, o que é importante para detetar e prevenir malware. O quadro de segurança Azure IoT Edge classifica o atestado em três categorias principais:

* Atestado estático
* Atestado de tempo de execução
* Atestação de software

### <a name="static-attestation"></a>Atestado estático

Atestado estático verifica a integridade de todo o software num dispositivo durante a alimentação, incluindo o sistema operativo, todos os tempos de execução e informações de configuração. Como o atestado estático ocorre durante a alimentação, é frequentemente referido como bota segura. A estrutura de segurança dos dispositivos IoT Edge estende-se aos fabricantes e incorpora capacidades de hardware seguras que asseguram processos estáticos de atestado. Estes processos incluem uma atualização segura de boot e firmware seguro. Trabalhar em estreita colaboração com fornecedores de silicone elimina camadas de firmware supérfluos, por isso minimiza a superfície de ameaça.

### <a name="runtime-attestation"></a>Atestado de tempo de execução

Uma vez que um sistema tenha concluído um processo de arranque seguro, os sistemas bem concebidos devem detetar tentativas de injetar malware e tomar contramedidas adequadas. Os ataques de malware podem visar as portas e interfaces do sistema. Se os atores mal-intencionados tiverem acesso físico a um dispositivo, podem adulterar o próprio dispositivo ou utilizar ataques de canal lateral para ter acesso. Tal descontentamento, seja alterações de configuração de malware ou não autorizadas, não pode ser detetado por atestado estático porque é injetado após o processo de arranque. Contramedidas oferecidas ou aplicadas pelo hardware do dispositivo ajudam a afastar tais ameaças. O quadro de segurança da IoT Edge exige explicitamente extensões que combatam as ameaças em tempo de execução.  

### <a name="software-attestation"></a>Atestação de software

Todos os sistemas saudáveis, incluindo sistemas inteligentes de borda, precisam de patches e upgrades. A segurança é importante para os processos de atualização, caso contrário podem ser potenciais vetores de ameaça. O quadro de segurança da IoT Edge exige atualizações através de pacotes medidos e assinados para assegurar a integridade e autenticar a origem dos pacotes. Esta norma aplica-se a todos os sistemas operativos e bits de software de aplicação.

## <a name="hardware-root-of-trust"></a>Raiz de confiança de hardware

Para muitos dispositivos de borda inteligente, especialmente dispositivos que podem ser fisicamente acedidos por potenciais atores maliciosos, a segurança do hardware é a última defesa para proteção. Hardware resistente a adulteração é crucial para tais implementações. O Azure IoT Edge encoraja os fornecedores seguros de hardware de silicone a oferecerem diferentes sabores de raiz de confiança de hardware para acomodar vários perfis de risco e cenários de implementação. A confiança no hardware pode vir de padrões comuns de protocolode segurança, como o Módulo de Plataforma Fidedigna (ISO/IEC 11889) e o Motor de Composição de Dispositivos do Grupo de Computação Fidedigno (DICE). Tecnologias seguras do enclave, como TrustZones e Software Guard Extensions (SGX) também fornecem confiança no hardware.

## <a name="certification"></a>Certificação

Para ajudar os clientes a tomar decisões informadas ao adquirir dispositivos Azure IoT Edge para a sua implementação, o quadro IoT Edge inclui requisitos de certificação. Fundacionais a estes requisitos são certificações relativas a sinistros de segurança e certificações relativas à validação da implementação da segurança. Por exemplo, uma certificação de pedido de segurança significa que o dispositivo IoT Edge utiliza hardware seguro conhecido para resistir a ataques de arranque. Uma certificação de validação significa que o hardware seguro foi devidamente implementado para oferecer este valor no dispositivo. De acordo com o princípio da simplicidade, o quadro tenta manter o fardo da certificação mínimo.

## <a name="extensibility"></a>Extensibilidade

Com a tecnologia IoT a impulsionar diferentes tipos de transformações empresariais, a segurança deve evoluir paralelamente para abordar cenários emergentes. O quadro de segurança Azure IoT Edge começa com uma base sólida sobre a qual se constrói em extebilidade em diferentes dimensões para incluir:

* Serviços de segurança de primeira parte como o Serviço de Provisionamento de Dispositivos para o Hub Azure IoT.
* Serviços de terceiros como serviços de segurança geridos para diferentes verticais de aplicações (como industriais ou cuidados de saúde) ou foco tecnológico (como monitorização de segurança em redes de malha, ou serviços de atesta de hardware de silicone) através de uma rica rede de parceiros.
* Sistemas legados para incluir retromontagem com estratégias de segurança alternativas, como usar tecnologia segura que não certificados para autenticação e gestão de identidade.
* Hardware seguro para adoção de tecnologias de hardware seguras emergentes e contribuições de parceiros de silicone.

No final, garantir a vantagem inteligente requer contribuições colaborativas de uma comunidade aberta impulsionada pelo interesse comum em assegurar o IoT. Estas contribuições podem ser sob a forma de tecnologias ou serviços seguros. O quadro de segurança Azure IoT Edge oferece uma base sólida para a segurança que é extensível para a cobertura máxima para oferecer o mesmo nível de confiança e integridade na borda inteligente como com a nuvem Azure.  

## <a name="next-steps"></a>Passos seguintes

Leia mais sobre como o Azure IoT Edge está [a assegurar a vantagem inteligente](https://azure.microsoft.com/blog/securing-the-intelligent-edge/).
