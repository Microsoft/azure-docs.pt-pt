---
title: Quadro de segurança - Azure IoT Edge | Microsoft Docs
description: Conheça os padrões de segurança, autenticação e autorização que foram usados para desenvolver o Azure IoT Edge e deve ser considerado como você projeta a sua solução
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/30/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 9ff154eee6c6174a8f1a3aa7bea37ef62273bb1a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103489507"
---
# <a name="security-standards-for-azure-iot-edge"></a>Normas de segurança para Azure IoT Edge

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

O Azure IoT Edge aborda os riscos inerentes ao mover os seus dados e análises para a borda inteligente. As normas de segurança IoT Edge equilibram a flexibilidade para diferentes cenários de implantação com a proteção que espera de todos os serviços Azure.

O IoT Edge funciona em várias fabricações e modelos de hardware, suporta vários sistemas operativos e aplica-se a diversos cenários de implementação. Em vez de oferecer soluções concretas para cenários específicos, o IoT Edge é um quadro de segurança extensível baseado em princípios bem fundamentados que são concebidos para a escala. O risco de um cenário de implantação depende de muitos fatores, incluindo:

* Propriedade de solução
* Geografia de implantação
* Sensibilidade aos dados
* Privacidade
* Aplicação vertical
* Requisitos regulamentares

Este artigo fornece uma visão geral do quadro de segurança IoT Edge. Para obter mais informações, consulte [a garantia da borda inteligente.](https://azure.microsoft.com/blog/securing-the-intelligent-edge/)

## <a name="standards"></a>Normas

As normas promovem a facilidade de escrutínio e a facilidade de implementação, ambas marcas de segurança. Uma solução de segurança deve prestar-se ao escrutínio sob avaliação para construir a confiança e não deve ser um obstáculo à implantação. O desenho da estrutura para assegurar o Azure IoT Edge baseia-se em protocolos de segurança testados no tempo e na indústria comprovados para familiaridade e reutilização.

## <a name="authentication"></a>Autenticação

Quando implementas uma solução IoT, precisas de saber que apenas atores, dispositivos e módulos de confiança têm acesso à tua solução. A autenticação baseada em certificados é o principal mecanismo de autenticação para a plataforma Azure IoT Edge. Este mecanismo é derivado de um conjunto de normas que regem a Infraestrutura de Chaves Públicas (PKiX) pelo Grupo de Trabalho de Engenharia da Internet (IETF).

Todos os dispositivos, módulos e atores que interagem com o dispositivo Azure IoT Edge devem ter identidades de certificado únicas. Esta orientação aplica-se se as interações são físicas ou através de uma ligação de rede. Nem todos os cenários ou componentes podem prestar-se à autenticação baseada em certificados, pelo que a extensibilidade do quadro de segurança oferece alternativas seguras.

Para mais informações, consulte [a utilização do certificado Azure IoT Edge](iot-edge-certs.md).

## <a name="authorization"></a>Autorização

O princípio do menor privilégio diz que os utilizadores e componentes de um sistema devem ter acesso apenas ao conjunto mínimo de recursos e dados necessários para desempenhar as suas funções. Os dispositivos, módulos e atores devem aceder apenas aos recursos e dados dentro do seu âmbito de permissão, e apenas quando forem autorizados em termos arquitetónicos. Algumas permissões são configuráveis com privilégios suficientes e outras são aplicadas arquiteticamente. Por exemplo, alguns módulos podem ser autorizados a ligar-se ao Azure IoT Hub. No entanto, não há razão para que um módulo num dispositivo IoT Edge aceda ao gémeo de um módulo noutro dispositivo IoT Edge.

Outros regimes de autorização incluem direitos de assinatura de certificados e controlo de acesso baseado em funções (RBAC).

## <a name="attestation"></a>Atestado

O Attestation garante a integridade das bits de software, o que é importante para detetar e prevenir malware. O quadro de segurança Azure IoT Edge classifica o atestado em três categorias principais:

* Atestado estático
* Atestado de tempo de execução
* Atestado de software

### <a name="static-attestation"></a>Atestado estático

O atestado estático verifica a integridade de todo o software num dispositivo durante a alimentação, incluindo o sistema operativo, todos os tempos de funcionamento e informações de configuração. Como o atestado estático ocorre durante a alimentação, é frequentemente referido como bota segura. O quadro de segurança para dispositivos IoT Edge estende-se aos fabricantes e incorpora capacidades de hardware seguras que asseguram processos de atestado estático. Estes processos incluem arranque seguro e atualização segura do firmware. Trabalhar em estreita colaboração com fornecedores de silicone elimina camadas supérfluas de firmware, minimizando assim a superfície de ameaça.

### <a name="runtime-attestation"></a>Atestado de tempo de execução

Uma vez que um sistema tenha concluído um processo de arranque seguro, os sistemas bem concebidos devem detetar tentativas de injetar malware e tomar contramedidas adequadas. Os ataques de malware podem visar as portas e interfaces do sistema. Se os atores mal-intencionados tiverem acesso físico a um dispositivo, podem adulterar o próprio dispositivo ou utilizar ataques de canais laterais para ter acesso. Tal descontentamento, quer o malware quer a configuração não autorizada, não pode ser detetado por atestado estático porque é injetado após o processo de arranque. Contramedidas oferecidas ou aplicadas pela ajuda do dispositivo para afastar tais ameaças. O quadro de segurança da IoT Edge exige explicitamente prorrogações que combatam ameaças de tempo de execução.  

### <a name="software-attestation"></a>Atestado de software

Todos os sistemas saudáveis, incluindo sistemas inteligentes de borda, precisam de patches e upgrades. A segurança é importante para os processos de atualização, caso contrário podem ser potenciais vetores de ameaça. O quadro de segurança da IoT Edge solicita atualizações através de pacotes medidos e assinados para garantir a integridade e autenticação da origem dos pacotes. Esta norma aplica-se a todos os sistemas operativos e bits de software de aplicações.

## <a name="hardware-root-of-trust"></a>Raiz de hardware da confiança

Para muitos dispositivos inteligentes de borda, especialmente dispositivos que podem ser fisicamente acedidos por potenciais atores maliciosos, a segurança do hardware é a última defesa para proteção. O hardware resistente à adulteração é crucial para tais implementações. O Azure IoT Edge encoraja os fornecedores de hardware de silício seguros a oferecerem diferentes sabores de raiz de hardware de confiança para acomodar vários perfis de risco e cenários de implementação. A confiança de hardware pode vir de padrões comuns de protocolo de segurança como o Módulo de Plataforma Fidedigna (ISO/IEC 11889) e o Motor de Composição do Dispositivo do Grupo de Computação Fidedigna (DICE). Tecnologias seguras do enclave como TrustZones e Software Guard Extensions (SGX) também fornecem confiança de hardware.

## <a name="certification"></a>Certificação

Para ajudar os clientes a tomar decisões informadas ao obter dispositivos Azure IoT Edge para a sua implementação, o quadro IoT Edge inclui requisitos de certificação. Fundamentais para estes requisitos são certificações relativas a pedidos de segurança e certificações relativas à validação da implementação da segurança. Por exemplo, uma certificação de alegação de segurança significa que o dispositivo IoT Edge utiliza hardware seguro conhecido por resistir a ataques de arranque. Uma certificação de validação significa que o hardware seguro foi devidamente implementado para oferecer este valor no dispositivo. De acordo com o princípio da simplicidade, o quadro procura manter o ónus da certificação mínimo.

## <a name="extensibility"></a>Extensibilidade

Com a tecnologia IoT a impulsionar diferentes tipos de transformações de negócios, a segurança deve evoluir paralelamente para abordar cenários emergentes. O quadro de segurança Azure IoT Edge começa com uma base sólida sobre a qual se constrói em extensibilidade em diferentes dimensões para incluir:

* Serviços de segurança de primeira parte como o Serviço de Provisionamento de Dispositivos para O Hub IoT Azure.
* Serviços de terceiros como serviços de segurança geridos para diferentes verticais de aplicações (como industriais ou cuidados de saúde) ou foco de tecnologia (como monitorização de segurança em redes de malha ou serviços de atestado de hardware de silício) através de uma rica rede de parceiros.
* Sistemas legados para incluir a retromontagem com estratégias de segurança alternativas, como o uso de tecnologia segura que não certificados para autenticação e gestão de identidade.
* Hardware seguro para adoção de tecnologias de hardware seguras emergentes e contribuições de parceiros de silício.

No final, assegurar a vantagem inteligente requer contribuições colaborativas de uma comunidade aberta impulsionada pelo interesse comum em assegurar a IoT. Estas contribuições podem ser sob a forma de tecnologias ou serviços seguros. O quadro de segurança Azure IoT Edge oferece uma base sólida para a segurança que é extensível para a cobertura máxima para oferecer o mesmo nível de confiança e integridade na borda inteligente como com a nuvem Azure.  

## <a name="next-steps"></a>Passos seguintes

Leia mais sobre como a Azure IoT Edge está [a assegurar a borda inteligente](https://azure.microsoft.com/blog/securing-the-intelligent-edge/).
