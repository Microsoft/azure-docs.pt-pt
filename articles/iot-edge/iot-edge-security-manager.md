---
title: Azure IoT Edge Security Manager-Azure IoT Edge
description: Gerencia a postura de segurança de dispositivo do IoT Edge e a integridade dos serviços de segurança.
services: iot-edge
keywords: segurança, o elemento seguro, o enclave, TEE, IoT Edge
author: eustacea
manager: philmea
ms.author: eustacea
ms.date: 08/30/2019
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: d5cfa16196a8815b711fd5277a80f6eb67d3a388
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76548701"
---
# <a name="azure-iot-edge-security-manager"></a>Gestor de segurança de IoT Edge do Azure

O Gestor de segurança do Azure IoT Edge é um principal de segurança bem vinculado para proteger o dispositivo IoT Edge e todos os seus componentes ao abstrair o hardware de silicon seguro. O Gerenciador de segurança é o ponto focal para a proteção de segurança e fornece ponto de integração de tecnologia para OEMs (fabricantes originais de equipamento).

![Gestor de segurança de IoT Edge do Azure](media/edge-security-manager/iot-edge-security-manager.png)

Gestor de segurança de IoT Edge tem como objetivo para defender a integridade do dispositivo IoT Edge e todas as operações de software inerente. O Gerenciador de segurança faz a transição de confiança da raiz de hardware subjacente de hardware de confiança (se disponível) para inicializar o tempo de execução do IoT Edge e monitorar operações em andamento.  O IoT Edge Security Manager é um software que trabalha junto com o hardware de silício seguro (quando disponível) para ajudar a fornecer as garantias de segurança mais altas possíveis.  

As responsabilidades do IoT Edge Security Manager incluem, mas não se limitam a:

* Protegidos e medidas de inicialização do dispositivo Azure IoT Edge.
* Provisionamento de identidade de dispositivo e fazer a transição de confiança quando aplicável.
* Alojar e proteger os componentes de dispositivo de serviços cloud como o serviço aprovisionamento de dispositivos.
* Provisione com segurança módulos de IoT Edge com identidades exclusivas.
* Controlador de chamadas para a raiz de hardware do dispositivo de confiança através dos serviços de notary.
* Monitorize a integridade das operações de IoT Edge no tempo de execução.

Gestor de segurança de IoT Edge inclui três componentes:

* Daemon de segurança de IoT Edge.
* Segurança módulo plataforma abstração de hardware (HSM PAL) de camada.
* Raiz de silicon opcional mas recomendado altamente hardware de HSM ou de confiança.

## <a name="the-iot-edge-security-daemon"></a>O daemon de segurança de IoT Edge

O daemon de segurança IoT Edge é responsável pelas operações lógicas do IoT Edge Security Manager. Representa uma parte significativa da base computacional confiável do dispositivo IoT Edge.

### <a name="design-principles"></a>Princípios de conceção

O daemon de segurança IoT Edge segue dois princípios principais: maximizar a integridade operacional e minimizar o inchar e a rotatividade.

#### <a name="maximize-operational-integrity"></a>Maximizar a integridade operacional

O daemon de segurança IoT Edge opera com a integridade mais alta possível dentro do recurso de defesa de qualquer raiz de hardware de confiança. Com a integração adequada, a raiz de hardware de confiança mede e monitoriza o daemon de segurança estaticamente e em tempo de execução para resistir a adulteração.

O acesso físico é sempre uma ameaça aos dispositivos IoT. A raiz de hardware de confiança desempenha um papel importante na defesa da integridade do daemon de segurança do IoT Edge.  A raiz de hardware de confiança é proveniente de duas variedades:

* elementos seguros para a proteção de informações confidenciais, como chaves criptográficas e segredos.
* enclaves seguras para a proteção de segredos, como chaves e cargas de trabalho confidenciais, como a medição e faturação.

Existem dois tipos de ambientes de execução para usar a raiz de hardware de confiança:

* O REE (ambiente de execução avançada) ou padrão que depende do uso de elementos seguros para proteger informações confidenciais.
* O sistema de execução confiável (enclave) que depende do uso da tecnologia de segurança segura para proteger informações confidenciais e oferecer proteção à execução de software.

Para dispositivos que usam o Secure enclaves como raiz de hardware de confiança, a lógica confidencial dentro do IoT Edge daemon de segurança deve estar dentro do enclave.  Partes não confidenciais do daemon de segurança podem estar fora do alto-se.  De qualquer forma, os fabricantes de design original (ODM) e os fabricantes de equipamento original (OEM) devem estender a confiança do seu HSM para medir e defender a integridade do daemon de segurança do IoT Edge na inicialização e no tempo de execução.

#### <a name="minimize-bloat-and-churn"></a>Minimizar o inchaço e alterações a dados

Outro princípio básico do IoT Edge Security daemon é minimizar a rotatividade.  Para o nível mais alto de confiança, o daemon de segurança IoT Edge pode firmemente acoplar com a raiz de hardware do dispositivo de confiança e operar como código nativo.  É comum que esses tipos de realização atualizem o software daemon por meio da raiz de hardware dos caminhos de atualização segura da confiança (em oposição aos mecanismos de atualização fornecidos pelo sistema operacional), o que pode ser desafiador em alguns cenários.  Embora a renovação de segurança seja recomendada para dispositivos IoT, requisitos de atualização excessivos ou grandes cargas de atualização podem expandir a superfície de ameaças de várias maneiras.  Os exemplos incluem a ignorar de atualizações para maximizar a disponibilidade operacional ou raiz de hardware de confiança restrita demasiado para processar cargas de atualizações de grandes dimensões.  Como tal, o design de IoT Edge daemon de segurança é conciso para manter a superfície e a base de computação confiável pequena e minimizar os requisitos de atualização.

### <a name="architecture-of-iot-edge-security-daemon"></a>Arquitetura do daemon de segurança de IoT Edge

![Daemon de segurança do Azure IoT Edge](media/edge-security-manager/iot-edge-security-daemon.png)

O daemon de segurança IoT Edge aproveita qualquer raiz de hardware disponível da tecnologia de confiança para proteger a segurança.  Ele também permite a operação de divisão do mundo entre um REE (ambiente de execução padrão/avançado) e um ambiente de execução confiável (configuração) quando as tecnologias de hardware oferecem ambientes de execução confiáveis. As interfaces específicas de função habilitam os principais componentes do IoT Edge para garantir a integridade do dispositivo IoT Edge e de suas operações.

#### <a name="cloud-interface"></a>Interface na cloud

A interface de nuvem permite que o daemon de segurança do IoT Edge acesse serviços de nuvem, como Complementos de nuvem para segurança de dispositivo, como renovação de segurança.  Por exemplo, o daemon de segurança IoT Edge usa atualmente essa interface para acessar o serviço de [provisionamento de dispositivos](https://docs.microsoft.com/azure/iot-dps/) no Hub IOT do Azure para o gerenciamento do ciclo de vida de identidade do dispositivo.  

#### <a name="management-api"></a>API de Gestão

IoT Edge daemon de segurança oferece uma API de gerenciamento, que é chamada pelo agente de IoT Edge ao criar/iniciar/interromper/remover um módulo IoT Edge. O daemon de segurança armazena "registros" para todos os módulos ativos. Estes registos de mapeiam a identidade de um módulo para algumas propriedades do módulo. Para obter exemplos, essas propriedades de módulo incluem o identificador de processo (PID) do processo em execução no contêiner e o hash do conteúdo do contêiner do Docker.

Essas propriedades são usadas pela API de carga de trabalho (descrita abaixo) para verificar se o chamador está autorizado para uma ação.

A API de gerenciamento é uma API privilegiada, que é possível chamá-la somente do agente de IoT Edge.  Como as inicializações do daemon de segurança IoT Edge e inicia o agente de IoT Edge, ele verifica se o agente de IoT Edge não foi violado, então ele pode criar um registro implícito para o agente de IoT Edge. O mesmo processo de atestado usado pela API de carga de trabalho também restringe o acesso à API de gerenciamento apenas para o agente de IoT Edge.

#### <a name="container-api"></a>Contentor de API

A API do contêiner interage com o sistema de contêiner em uso para o gerenciamento de módulo, como Moby ou Docker.

#### <a name="workload-api"></a>API da carga de trabalho

A API de carga de trabalho é acessível a todos os módulos. Ele fornece uma prova de identidade, seja como um token assinado por raiz do HSM ou um certificado X509, e o grupo de confiança correspondente a um módulo. O grupo de confiança contém certificados de autoridade de certificação para todos os outros servidores nos quais os módulos devem confiar.

O daemon de segurança IoT Edge usa um processo de atestado para proteger essa API. Quando um módulo chama essa API, o daemon de segurança tenta encontrar um registro para a identidade. Se tiver êxito, ele usa as propriedades de registo para medir o módulo. Se o resultado do processo de medição corresponder ao registro, uma nova prova de identidade será gerada. Os certificados de autoridade de certificação correspondentes (grupo de confiança) são retornados para o módulo.  O módulo utiliza este certificado para ligar ao IoT Hub, outros módulos ou iniciar um servidor. Quando o token assinado ou o certificado se aproximar da expiração, é responsabilidade do módulo solicitar um novo certificado.

### <a name="integration-and-maintenance"></a>Integração e manutenção

A Microsoft mantém o código principal base para o [daemon de segurança de IoT Edge no GitHub](https://github.com/Azure/iotedge/tree/master/edgelet).

#### <a name="installation-and-updates"></a>Instalação e atualizações

A instalação e as atualizações do daemon de segurança do IoT Edge são gerenciadas por meio do sistema de gerenciamento de pacotes do sistema operacional. IoT Edge dispositivos com a raiz de hardware de confiança devem fornecer proteção adicional à integridade do daemon gerenciando seu ciclo de vida por meio dos sistemas de gerenciamento de atualizações e inicialização seguras. Os criadores de dispositivos devem explorar esses caminhos com base em seus respectivos recursos de dispositivo.

#### <a name="versioning"></a>Controlo de versões

O runtime do IoT Edge controla e reporta a versão do daemon de segurança de IoT Edge. A versão é relatada como o atributo *Runtime. Platform. Version* da propriedade reportada do módulo do agente de IOT Edge.

### <a name="hardware-security-module-platform-abstraction-layer-hsm-pal"></a>Camada de abstração de plataforma de módulo de segurança do hardware (HSM PAL)

A PAL HSM abstrai todos os raiz de hardware de confiança para isolar o desenvolvedor ou do usuário do IoT Edge de suas complexidades.  Ele inclui uma combinação de procedimentos de comunicação da API (interface de programação de aplicativo) e de transporte de domínio, por exemplo, a comunicação entre um ambiente de execução padrão e um enclave seguro.  A implementação real da HSM PAL depende o hardware de segurança específico em utilização. Sua existência permite o uso de praticamente qualquer hardware de silício seguro.

## <a name="secure-silicon-root-of-trust-hardware"></a>Raiz de silicon segura de hardware de confiança

Silicon segura é necessário âncora de confiança dentro do hardware do dispositivo IoT Edge.  Silicon seguro são fornecidos em várias para incluir o Trusted Platform Module (TPM), elemento seguro incorporado (eSE), ARM TrustZone, Intel SGX e tecnologias de silicon seguro personalizado.  O uso da raiz segura do silício de confiança em dispositivos é recomendado devido às ameaças associadas à acessibilidade física de dispositivos IoT.

## <a name="iot-edge-security-manager-integration-and-maintenance"></a>Integração de Gestor de segurança de IoT Edge e manutenção

O IoT Edge Security Manager tem como objetivo identificar e isolar os componentes que defendem a segurança e a integridade da plataforma Azure IoT Edge para a proteção personalizada. Terceiros, como os criadores de dispositivos, devem fazer uso de recursos de segurança personalizados disponíveis com o hardware do dispositivo.  Consulte a seção próximas etapas para obter links que demonstram como proteger o Gerenciador de segurança do IoT do Azure com o Trusted Platform Module (TPM) em plataformas Linux e Windows. Esses exemplos usam software ou TPMs virtual, mas aplicam-se diretamente ao uso de dispositivos TPM discretos.  

## <a name="next-steps"></a>Passos seguintes

Leia o blog sobre [proteger um edge inteligente](https://azure.microsoft.com/blog/securing-the-intelligent-edge/).

Crie e provisione um [dispositivo IOT Edge com um TPM virtual em uma máquina virtual Linux](how-to-auto-provision-simulated-device-linux.md).

Criar e provisionar um [dispositivo IOT Edge com um TPM simulado no Windows](how-to-auto-provision-simulated-device-windows.md).
