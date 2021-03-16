---
title: Gestor de segurança Azure IoT Edge - Azure IoT Edge
description: Gere a posição de segurança do dispositivo IoT Edge e a integridade dos serviços de segurança.
services: iot-edge
keywords: segurança, elemento seguro, enclave, TEE, IoT Edge
author: eustacea
manager: philmea
ms.author: eustacea
ms.date: 08/30/2019
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: 468f1b91d6c6157cd2af6de9599bad7f43c1ad8f
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103492562"
---
# <a name="azure-iot-edge-security-manager"></a>Gestor de segurança Azure IoT Edge

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

O gestor de segurança Azure IoT Edge é um núcleo de segurança bem limitado para proteger o dispositivo IoT Edge e todos os seus componentes, abstraindo o hardware de silício seguro. O gestor de segurança é o ponto focal para o endurecimento da segurança e fornece um ponto de integração tecnológica aos fabricantes de equipamentos originais (OEM).

![Gestor de segurança Azure IoT Edge](media/edge-security-manager/iot-edge-security-manager.png)

O gestor de segurança IoT Edge tem como objetivo defender a integridade do dispositivo IoT Edge e todas as operações de software inerentes. O gestor de segurança transita a confiança da raiz subjacente do hardware de hardware de confiança (se disponível) para arrancar o tempo de execução do IoT Edge e monitorizar as operações em curso.  O gestor de segurança IoT Edge está a trabalhar com um software que está a funcionar juntamente com hardware de silício seguro (quando disponível) para ajudar a fornecer as mais altas garantias de segurança possíveis.  

As responsabilidades do gestor de segurança IoT Edge incluem, mas não se limitam a:

* Sapatilhas seguras e medidas do dispositivo Azure IoT Edge.
* Provisão de identidade do dispositivo e transição de confiança quando aplicável.
* Hospedar e proteger componentes de dispositivos de serviços na nuvem como o Serviço de Provisionamento de Dispositivos.
* Fornecendo de forma segura módulos IoT Edge com identidades únicas.
* Gatekeeper para dispositivo raiz de hardware de confiança através de serviços notários.
* Monitorize a integridade das operações da IoT Edge em tempo de execução.

O gestor de segurança IoT Edge inclui três componentes:

* IoT Edge segurança daemon.
* Camada de abstração da plataforma de módulo de segurança de hardware (HSM PAL).
* Raiz de silício de hardware opcional mas altamente recomendada de confiança ou HSM.

## <a name="the-iot-edge-security-daemon"></a>O daemon de segurança IoT Edge

O daemon de segurança IoT Edge é responsável pelas operações lógicas do gestor de segurança IoT Edge. Representa uma parte significativa da base de computação fidedigna do dispositivo IoT Edge.

### <a name="design-principles"></a>Princípios de conceção

O daemon de segurança IoT Edge segue dois princípios fundamentais: maximizar a integridade operacional e minimizar o inchaço e o churn.

#### <a name="maximize-operational-integrity"></a>Maximizar a integridade operacional

O daemon de segurança IoT Edge opera com a mais alta integridade possível dentro da capacidade de defesa de qualquer raiz de hardware de confiança. Com uma integração adequada, a raiz do hardware de confiança mede e monitoriza o demônio de segurança estático e em tempo de execução para resistir à adulteração.

O acesso físico é sempre uma ameaça para dispositivos IoT. A raiz de hardware da confiança desempenha um papel importante na defesa da integridade do daemon de segurança IoT Edge.  A raiz de hardware da confiança vem em duas variedades:

* elementos seguros para a proteção de informações sensíveis como segredos e chaves criptográficas.
* enclaves seguros para a proteção de segredos como chaves, e cargas de trabalho sensíveis como medição e faturação.

Existem dois tipos de ambientes de execução para usar a raiz de hardware da confiança:

* O ambiente de execução padrão ou rico (REE) que se baseia na utilização de elementos seguros para proteger informações sensíveis.
* O ambiente de execução confiável (TEE) que se baseia no uso de tecnologia segura do enclave para proteger informações sensíveis e oferecer proteção à execução de software.

Para dispositivos que utilizem enclaves seguros como raiz de hardware de confiança, a lógica sensível dentro do daemon de segurança IoT Edge deve estar dentro do enclave.  Partes não sensíveis do daemon de segurança podem estar fora do TEE.  Em todo o caso, os fabricantes de design originais (ODM) e os fabricantes de equipamentos originais (OEM) devem estender a confiança do seu HSM para medir e defender a integridade do daemon de segurança IoT Edge no arranque e no tempo de funcionaamento.

#### <a name="minimize-bloat-and-churn"></a>Minimizar o inchaço e o churn

Outro princípio fundamental para o daemon de segurança IoT Edge é minimizar a agitação.  Para o mais alto nível de confiança, o daemon de segurança IoT Edge pode se aliar firmemente à raiz de hardware do dispositivo de confiança e funcionar como código nativo.  É comum que este tipo de realizações atualizem o software daemon através da raiz de hardware dos caminhos de atualização seguros do Trust (em oposição aos mecanismos de atualização fornecidos pelo SISTEMA), o que pode ser um desafio em alguns cenários.  Embora a renovação de segurança seja recomendada para dispositivos IoT, os requisitos de atualização excessivos ou as cargas de atualização grandes podem expandir a superfície de ameaça de muitas maneiras.  Exemplos incluem saltar de atualizações para maximizar a disponibilidade operacional ou raiz de hardware de confiança demasiado limitado para processar grandes cargas de atualização.  Como tal, o design do daemon de segurança IoT Edge é conciso para manter a pegada e base de computação confiável pequena e para minimizar os requisitos de atualização.

### <a name="architecture-of-iot-edge-security-daemon"></a>Arquitetura do daemon de segurança IoT Edge

![Daemon de segurança Azure IoT Edge](media/edge-security-manager/iot-edge-security-daemon.png)

O daemon de segurança IoT Edge aproveita qualquer raiz de hardware disponível da tecnologia trust para o endurecimento da segurança.  Também permite uma operação dividida entre um ambiente de execução padrão/rico (REE) e um ambiente de execução confiável (TEE) quando as tecnologias de hardware oferecem ambientes de execução fidedignos. As interfaces específicas de função permitem que os principais componentes do IoT Edge garantam a integridade do dispositivo IoT Edge e das suas operações.

#### <a name="cloud-interface"></a>Interface em nuvem

A interface na nuvem permite que o daemon de segurança IoT Edge aceda a serviços na nuvem, como elogios na nuvem à segurança do dispositivo, como renovação de segurança.  Por exemplo, o daemon de segurança IoT Edge utiliza atualmente esta interface para aceder ao [Serviço de Provisionamento de Dispositivos](../iot-dps/index.yml) Azure IoT Hub para gestão do ciclo de vida da identidade do dispositivo.  

#### <a name="management-api"></a>API de Gestão

O daemon de segurança IoT Edge oferece uma API de gestão, que é chamada pelo agente IoT Edge ao criar/iniciar/parar/remover um módulo IoT Edge. O daemon de segurança armazena "registos" para todos os módulos ativos. Estes registos mapeiam a identidade de um módulo em algumas propriedades do módulo. Por exemplo, estas propriedades do módulo incluem o identificador de processo (pid) do processo em execução no recipiente e o haxixe do conteúdo do recipiente do estivador.

Estas propriedades são utilizadas pela API de carga de trabalho (descrita abaixo) para verificar se o chamador está autorizado para uma ação.

A API de gestão é uma API privilegiada, só acessível do agente IoT Edge.  Uma vez que o daemon de segurança IoT Edge é e inicia o agente IoT Edge, verifica-se que o agente IoT Edge não foi adulterado, então pode criar um registo implícito para o agente IoT Edge. O mesmo processo de atestado que a carga de trabalho que a API utiliza também restringe o acesso à API de gestão apenas ao agente IoT Edge.

#### <a name="container-api"></a>API de contentores

A API do contentor interage com o sistema de contentores em uso para a gestão de módulos, como Moby ou Docker.

#### <a name="workload-api"></a>API de carga de trabalho

A API de carga de trabalho é acessível a todos os módulos. Fornece prova de identidade, quer como símbolo assinado com raízes HSM, quer como certificado X509, quer como o correspondente pacote de confiança a um módulo. O pacote de confiança contém certificados de CA para todos os outros servidores em que os módulos devem confiar.

O daemon de segurança IoT Edge usa um processo de atestado para guardar esta API. Quando um módulo chama a esta API, o daemon de segurança tenta encontrar um registo para a identidade. Se for bem sucedido, utiliza as propriedades do registo para medir o módulo. Se o resultado do processo de medição corresponder ao registo, é gerada uma nova prova de identidade. Os certificados ca correspondentes (pacote de confiança) são devolvidos ao módulo.  O módulo utiliza este certificado para ligar ao IoT Hub, outros módulos ou iniciar um servidor. Quando o token ou certificado assinado se aproxima do termo, é da responsabilidade do módulo solicitar um novo certificado.

### <a name="integration-and-maintenance"></a>Integração e manutenção

A Microsoft mantém a base de código principal para o [daemon de segurança IoT Edge no GitHub](https://github.com/Azure/iotedge/tree/master/edgelet).

#### <a name="installation-and-updates"></a>Instalação e atualizações

A instalação e atualizações do daemon de segurança IoT Edge são geridas através do sistema de gestão de pacotes do sistema operativo. Os dispositivos IoT Edge com raiz de hardware de confiança devem fornecer um endurecimento adicional à integridade do daemon, gerindo o seu ciclo de vida através dos sistemas de gestão de arranque e atualizações seguras. Os fabricantes de dispositivos devem explorar estas avenidas com base nas respetivas capacidades do dispositivo.

#### <a name="versioning"></a>Controlo de versões

O tempo de execução IoT Edge rastreia e reporta a versão do daemon de segurança IoT Edge. A versão é reportada como o *atributo runtime.platform.version* do módulo de agente IoT Edge reportado propriedade.

### <a name="hardware-security-module-platform-abstraction-layer-hsm-pal"></a>Camada de abstração da plataforma de módulo de segurança de hardware (HSM PAL)

O HSM PAL abstrata toda a raiz do hardware de confiança para isolar o desenvolvedor ou utilizador de IoT Edge das suas complexidades.  Inclui uma combinação de interface de programação de aplicações (API) e procedimentos de comunicação de trans-domínio, por exemplo comunicação entre um ambiente de execução padrão e um enclave seguro.  A implementação real do HSM PAL depende do hardware seguro específico em uso. A sua existência permite a utilização de praticamente qualquer hardware de silício seguro.

## <a name="secure-silicon-root-of-trust-hardware"></a>Raiz de silício segura do hardware de confiança

O silício seguro é necessário para ancorar a confiança dentro do hardware do dispositivo IoT Edge.  O silício seguro vem em variedade para incluir o Módulo de Plataforma Fidedigna (TPM), elemento secure incorporado (eSE), ARM TrustZone, Intel SGX e tecnologias de silício seguras personalizadas.  Recomenda-se a utilização de raiz de silício segura de confiança nos dispositivos, dadas as ameaças associadas à acessibilidade física dos dispositivos IoT.

## <a name="iot-edge-security-manager-integration-and-maintenance"></a>Integração e manutenção do gestor de segurança IoT Edge

O gestor de segurança IoT Edge tem como objetivo identificar e isolar os componentes que defendem a segurança e integridade da plataforma Azure IoT Edge para o endurecimento personalizado. Terceiros, como fabricantes de dispositivos, devem utilizar funcionalidades de segurança personalizadas disponíveis com o hardware do dispositivo.  Consulte a secção de próximos passos para obter links que demonstrem como endurecer o gestor de segurança Azure IoT com o Módulo de Plataforma Fidedigna (TPM) nas plataformas Linux e Windows. Estes exemplos utilizam software ou TPMs virtuais, mas aplicam-se diretamente à utilização de dispositivos TPM discretos.  

## <a name="next-steps"></a>Passos seguintes

Leia o blog sobre [a garantia da borda inteligente.](https://azure.microsoft.com/blog/securing-the-intelligent-edge/)

Criar e forrar um [dispositivo IoT Edge com um TPM virtual numa máquina virtual Linux.](how-to-auto-provision-simulated-device-linux.md)

Criar e providenciar um [dispositivo IoT Edge com um TPM simulado no Windows](how-to-auto-provision-simulated-device-windows.md).