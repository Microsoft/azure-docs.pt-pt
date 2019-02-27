---
title: Estrutura de segurança - Azure IoT Edge | Documentos da Microsoft
description: Saiba mais sobre a segurança, a autenticação e padrões de autorização que foram utilizadas para desenvolver o Azure IoT Edge e devem ser considerados à medida que concebe a sua solução
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/25/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 8aadddbc9ae13a87f89db4d7e7189ea7aa8aeef5
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56883509"
---
# <a name="security-standards-for-azure-iot-edge"></a>Normas de segurança do Azure IoT Edge

O Azure IoT Edge foi concebido para abordar os cenários de risco inerentes ao mover seus dados e análises para um edge inteligente. As normas de segurança de IoT Edge fornecem flexibilidade para cenários de implementação e de perfis de risco diferentes, permitindo ainda oferecer a proteção que espera de todos os serviços do Azure. 

O Azure IoT Edge é executado em vários modelos e torna o hardware, suporta vários sistemas operativos e aplica-se a cenários de implementação diferentes. Avaliar o risco de um cenário de implementação depende muitas considerações, incluindo a propriedade de solução, área geográfica de implementação, confidencialidade de dados, privacidade, requisitos regulamentares e vertical do aplicativo. Em vez de oferecer soluções concretas para cenários específicos, o IoT Edge é uma estrutura de segurança extensível baseada nos princípios mantermos bem projetadas para dimensionamento. 
 
Este artigo fornece uma descrição geral da estrutura de segurança de IoT Edge. Para obter mais informações, consulte [proteger um edge inteligente](https://azure.microsoft.com/blog/securing-the-intelligent-edge/).

## <a name="standards"></a>Padrões

Padrões de promoção da Facilidade de exame detalhado e facilidade de implementação, sendo que ambas são hallmarks de segurança. Uma solução de segurança deve é adaptado para exame detalhado em avaliação para criar a confiança e não deve ser um obstáculo para a implementação. O design da estrutura para proteger o Azure IoT Edge baseia-se no comprovada e protocolos de segurança comprovada pelo setor para familiaridade e reutilização. 

## <a name="authentication"></a>Authentication

Quando implementa uma solução de IoT, precisa saber que apenas atores fidedignos, dispositivos e módulos têm acesso à sua solução. Esses dados de conhecimento oferece a responsabilidade de segura de participantes. O Azure IoT Edge atinge esse conhecimento através da autenticação. Autenticação baseada em certificado é o mecanismo primário para a autenticação para a plataforma do Azure IoT Edge. Esse mecanismo é derivada de um conjunto de normas que regem a infraestrutura de chave pública (PKiX) pelo IETF Internet Engineering Task Force ().     

Todos os dispositivos, módulos e atores interagir com o dispositivo Azure IoT Edge, quer fisicamente ou através de uma ligação de rede, devem ter identidades de certificado exclusivo. No entanto, nem todo componente ou cenário poderá é adaptado para autenticação baseada em certificados. Nesses cenários, a extensibilidade da estrutura de segurança oferece alternativas seguras. 

## <a name="authorization"></a>Autorização

O princípio do menor privilégio diz que os utilizadores e os componentes de um sistema devem ter acesso apenas para o conjunto mínimo de recursos e os dados necessários para executar suas funções. Dispositivos, módulos e atores devem acessar apenas os recursos e os dados dentro do respetivo âmbito de permissão e apenas quando é aceitável em termos de arquitetura. Algumas permissões são configuráveis com privilégios suficientes e outras pessoas em termos de arquitetura são impostas.  Por exemplo, um módulo pode ser autorizado por meio da configuração com privilégios para iniciar uma ligação para o IoT Hub do Azure. No entanto, não existe nenhuma razão por que um módulo num dispositivo Azure IoT Edge deve acessar o duplo do módulo no outro dispositivo Azure IoT Edge.

Outros esquemas de autorização incluem direitos de assinatura de certificado e o controlo de acesso baseado em funções (RBAC). 

## <a name="attestation"></a>Atestado

Atestado assegura a integridade dos bits de software.  É importante para a deteção e prevenção de software maligno.  A estrutura de segurança do Azure IoT Edge classifica atestado em três categorias principais:

* Atestado estático
* Atestado de tempo de execução
* Atestado de software

### <a name="static-attestation"></a>Atestado estático

Atestado estático verifica a integridade de todos os softwares num dispositivo, incluindo o sistema operativo, todos os tempos de execução e informações de configuração no dispositivo. o power-up. Ele é frequentemente referido como o arranque seguro. A estrutura de segurança para dispositivos do Azure IoT Edge expande para fabricantes e incorpore os recursos de hardware de segurança que impeçam a processos de atestado estático. Esses processos incluem a inicialização segura e firmware seguro atualizar.  Trabalhar em colaboração fechar com fornecedores de silício elimina as camadas de firmware supérfluo, então, minimiza a superfície de ameaças. 

### <a name="runtime-attestation"></a>Atestado de tempo de execução

Depois de um sistema concluiu um processo de arranque seguro e está em execução, sistemas bem projetados seriam detetar tentativas para injetar o software maligno e tomar as contramedidas adequadas. Ataques de malware podem direcionar as portas e interfaces para acessar o sistema do sistema. Em alternativa, se atores maliciosos tem acesso físico a um dispositivo que podem violar o próprio dispositivo ou utilizar o ataques de canal de lado para obter acesso. Tal malcontent, que pode ser na forma de software maligno ou alterações de configuração não autorizado, não é possível detetar por atestado estático porque ele é injetado após o processo de inicialização. Medidas preventivas oferecidos ou impostas pela ajuda de hardware do dispositivo para ward desativar essas ameaças.  A estrutura de segurança do Azure IoT Edge chama explicitamente para extensões que combater as ameaças de tempo de execução.  

### <a name="software-attestation"></a>Atestado de software

Todos os sistemas de bom estado de funcionamento, incluindo sistemas de um edge inteligente, tem de aceitar patches e atualizações.  A segurança é importante para processos de atualização caso contrário, que eles podem ser potenciais vetores de ameaças.  A estrutura de segurança do Azure IoT Edge chamadas por atualizações medido e assinado pacotes para garantir a integridade dos e autenticar-se a origem dos pacotes.  Esta Norma aplica-se a todos os sistemas operativos e bits de software de aplicação. 

## <a name="hardware-root-of-trust"></a>Raiz de hardware de confiança

Para muitos dispositivos de um edge inteligente, especialmente em dispositivos em locais onde o potenciais atores maliciosos tem acesso físico ao dispositivo, a segurança de hardware do dispositivo é o último defesa para proteção. Hardware resistente a adulterações é crucial para implementações deste tipo. O Azure IoT Edge incentiva os fornecedores de hardware de silicon seguro para oferecer diferentes tipos de raiz de hardware de confiança para acomodar vários perfis de risco e cenários de implementação. Confiança de hardware pode ter normas de protocolo de segurança comuns, como o Trusted Platform Module (ISO/IEC 11889) e Device identificador Composition Engine (DICE da Trusted Computing Group). Proteja as tecnologias de enclave como TrustZones e extensões de proteção de Software (SGX) também fornecem a confiança de hardware. 

## <a name="certification"></a>Certificação

Para ajudar a tomar decisões informadas ao adquirir dispositivos Azure IoT Edge para a implantação de clientes, a estrutura de Azure IoT Edge inclui os requisitos de certificação.  Certificações relacionadas à segurança afirmações e certificações relativas a validação da implementação de segurança, são fundamentais para esses requisitos.  Por exemplo, uma certificação de afirmação da segurança informa que o dispositivo do IoT Edge usa hardware de segurança conhecido para resistir a ataques de arranque. Uma certificação de validação informa que o hardware de segurança foi implementado corretamente para oferecer esse valor no dispositivo.  Para manter o princípio de simplicidade, o framework tenta manter a carga de certificação mínima.   

## <a name="extensibility"></a>Extensibilidade

Com a tecnologia de IoT impulsionar diferentes tipos de transformações de negócios, é razoável dizer que a segurança deve evoluir em paralelo para o endereço emergentes de cenários.  A estrutura de segurança do Azure IoT Edge é iniciado com uma base sólida na qual ele se baseia na extensibilidade em dimensões diferentes para incluir: 

* Primeira serviços de segurança de terceiros como o serviço de aprovisionamento de dispositivos para o IoT Hub do Azure.
* Serviços de terceiros, como serviços de segurança gerida para mercados verticais de aplicação diferente (como industriais ou cuidados de saúde) e do foco de tecnologia (como a segurança de monitorização na malha redes ou serviços de atestado de hardware de silicon) através de uma rede avançada de parceiros.
* Sistemas herdados para incluir retroajustar com estratégias de segurança alternativas, gosto de usar tecnologia segura que não sejam certificados para gestão de identidades e autenticação.
* Proteger o hardware para a adoção de tecnologias emergentes de hardware de segurança e silicon contribuições de parceiro.

No final, o maior sucesso na proteção de um edge inteligente resulta da colaboração contribuições de uma Comunidade de open orientado pelo interesse comuns na proteção de IoT.  Nessas contribuições poderão ser na forma de tecnologias seguras ou serviços.  A estrutura de segurança do Azure IoT Edge oferece uma base sólida de segurança que é extensível para a máxima cobertura oferecer o mesmo nível de confiança e a integridade de um edge inteligente como com a cloud do Azure.  

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre como é o Azure IoT Edge [proteger um edge inteligente](https://azure.microsoft.com/blog/securing-the-intelligent-edge/).
