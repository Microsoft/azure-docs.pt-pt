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
ms.openlocfilehash: d5cfa16196a8815b711fd5277a80f6eb67d3a388
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76548701"
---
# <a name="azure-iot-edge-security-manager"></a>Gestor de segurança Azure IoT Edge

O gestor de segurança Azure IoT Edge é um núcleo de segurança bem limitado para proteger o dispositivo IoT Edge e todos os seus componentes, abstraindo o hardware de silicone seguro. O gestor de segurança é o ponto focal para o endurecimento da segurança e fornece ponto de integração tecnológica aos fabricantes de equipamentos originais (OEM).

![Gestor de segurança Azure IoT Edge](media/edge-security-manager/iot-edge-security-manager.png)

O gestor de segurança IoT Edge tem como objetivo defender a integridade do dispositivo IoT Edge e todas as operações de software inerentes. O gestor de segurança transita a confiança da raiz de hardware subjacente do hardware trust (se disponível) para arrancar o tempo de execução ioT Edge e monitorizar as operações em curso.  O gestor de segurança IoT Edge é um software que trabalha juntamente com hardware de silicone seguro (quando disponível) para ajudar a fornecer as mais altas garantias de segurança possíveis.  

As responsabilidades do gestor de segurança IoT Edge incluem, mas não se limitam a:

* Correia sinuosa e medida do dispositivo Azure IoT Edge.
* Disposição da identidade do dispositivo e transição de confiança, se aplicável.
* Hospedar e proteger componentes de dispositivos de serviços em nuvem como o Serviço de Provisionamento de Dispositivos.
* Fornecer de forma segura módulos IoT Edge com identidades únicas.
* Gatekeeper para dispositivo raiz de confiança através de serviços notariais.
* Monitorize a integridade das operações da IoT Edge em tempo de execução.

O gestor de segurança IoT Edge inclui três componentes:

* Daemon de segurança IoT Edge.
* Camada de abstração de módulos de segurança de hardware (HSM PAL).
* Raiz opcional mas altamente recomendada de silício de hardware de confiança ou HSM.

## <a name="the-iot-edge-security-daemon"></a>O daemon de segurança IoT Edge

O daemon de segurança IoT Edge é responsável pelas operações lógicas do gestor de segurança IoT Edge. Representa uma parte significativa da base de computação fidedigna do dispositivo IoT Edge.

### <a name="design-principles"></a>Princípios de conceção

O daemon de segurança IoT Edge segue dois princípios fundamentais: maximizar a integridade operacional e minimizar o inchaço e o rebuliço.

#### <a name="maximize-operational-integrity"></a>Maximizar a integridade operacional

O daemon de segurança IoT Edge opera com a maior integridade possível dentro da capacidade de defesa de qualquer raiz de hardware de confiança. Com uma integração adequada, a raiz do hardware de confiança mede e monitoriza o daemon de segurança estática e em tempo de funcionamento para resistir à adulteração.

O acesso físico é sempre uma ameaça para dispositivos IoT. A raiz de confiança do hardware desempenha um papel importante na defesa da integridade do daemon de segurança IoT Edge.  A raiz de confiança do hardware vem em duas variedades:

* elementos seguros para a proteção de informações sensíveis, como segredos e chaves criptográficas.
* enclaves seguros para a proteção de segredos como chaves, e cargas de trabalho sensíveis como a medição e faturação.

Existem dois tipos de ambientes de execução para usar a raiz de confiança do hardware:

* O ambiente de execução padrão ou rico (REE) que se baseia na utilização de elementos seguros para proteger informações sensíveis.
* O ambiente de execução confiável (TEE) que se baseia na utilização de tecnologia de enclave segura para proteger informações sensíveis e oferecer proteção à execução de software.

Para dispositivos que usam enclaves seguros como raiz de hardware de confiança, a lógica sensível dentro do daemon de segurança IoT Edge deve estar dentro do enclave.  Porções não sensíveis do daemon de segurança podem estar fora do TEE.  Em todo o caso, os fabricantes originais de design (ODM) e os fabricantes originais de equipamentos (OEM) devem alargar a confiança do seu HSM para medir e defender a integridade do daemon de segurança IoT Edge no arranque e no tempo de execução.

#### <a name="minimize-bloat-and-churn"></a>Minimizar o inchaço e o churn

Outro princípio fundamental para o daemon de segurança IoT Edge é minimizar o churn.  Para o mais alto nível de confiança, o daemon de segurança IoT Edge pode acoplar-se firmemente com a raiz de hardware do dispositivo de confiança e operar como código nativo.  É comum que este tipo de realizações atualize o software daemon através da raiz de hardware dos caminhos de atualização seguros da trust (ao contrário dos mecanismos de atualização fornecidos pela OS), o que pode ser desafiante em alguns cenários.  Embora a renovação de segurança seja recomendada para dispositivos IoT, requisitos de atualização excessivos ou grandes cargas de atualização podem expandir a superfície de ameaça de muitas maneiras.  Exemplos incluem saltar de atualizações para maximizar a disponibilidade operacional ou raiz de hardware fidedigno demasiado limitado para processar grandes cargas de atualização.  Como tal, o design do daemon de segurança IoT Edge é conciso para manter a pegada e base de computação fidedigna pequena e minimizar os requisitos de atualização.

### <a name="architecture-of-iot-edge-security-daemon"></a>Arquitetura do daemon de segurança IoT Edge

![Daemon de segurança Azure IoT Edge](media/edge-security-manager/iot-edge-security-daemon.png)

O daemon de segurança IoT Edge aproveita qualquer raiz de hardware disponível da tecnologia de confiança para endurecimento de segurança.  Também permite uma operação em mundo dividido entre um ambiente de execução padrão/rico (REE) e um ambiente de execução confiável (TEE) quando as tecnologias de hardware oferecem ambientes de execução fidedignos. As interfaces específicas para o papel permitem que os principais componentes do IoT Edge garantam a integridade do dispositivo IoT Edge e das suas operações.

#### <a name="cloud-interface"></a>Interface de nuvem

A interface da nuvem permite que o daemon de segurança IoT Edge aceda a serviços na nuvem, tais como elogios à segurança do dispositivo, como renovação de segurança.  Por exemplo, o daemon de segurança IoT Edge utiliza atualmente esta interface para aceder ao Serviço de Provisionamento de [Dispositivos](https://docs.microsoft.com/azure/iot-dps/) Hub Azure IoT para gestão do ciclo de vida da identidade do dispositivo.  

#### <a name="management-api"></a>API de Gestão

O daemon de segurança IoT Edge oferece uma API de gestão, que é chamada pelo agente IoT Edge ao criar/iniciar/parar/remover um módulo IoT Edge. O daemon de segurança armazena "registos" para todos os módulos ativos. Estas inscrições mapeiam a identidade de um módulo para algumas propriedades do módulo. Por exemplo, estas propriedades do módulo incluem o identificador de processo (pid) do processo em execução no recipiente e o hash do conteúdo do recipiente de estivador.

Estas propriedades são utilizadas pela Carga de Trabalho API (descrita abaixo) para verificar se o chamador está autorizado para uma ação.

A API de gestão é uma API privilegiada, chamada apenas do agente IoT Edge.  Desde que o daemon de segurança IoT Edge bootstraps e inicia o agente IoT Edge, verifica que o agente IoT Edge não foi adulterado, então pode criar um registo implícito para o agente IoT Edge. O mesmo processo de atestado que a carga de trabalho que a API utiliza também restringe o acesso à API de gestão apenas ao agente IoT Edge.

#### <a name="container-api"></a>API do contentor

O recipiente API interage com o sistema de contentores em uso para a gestão de módulos, como Moby ou Docker.

#### <a name="workload-api"></a>API de carga de trabalho

A API de carga de trabalho é acessível a todos os módulos. Fornece prova de identidade, seja como um símbolo assinado com raízes HSM ou um certificado X509, e o pacote de confiança correspondente a um módulo. O pacote fiduciário contém certificados CA para todos os outros servidores em que os módulos devem confiar.

O daemon de segurança IoT Edge usa um processo de atestado para guardar esta API. Quando um módulo chama a isto API, o daemon de segurança tenta encontrar um registo para a identidade. Se for bem sucedido, utiliza as propriedades do registo para medir o módulo. Se o resultado do processo de medição corresponder ao registo, gera-se uma nova prova de identidade. Os certificados ca correspondentes (pacote fidedigno) são devolvidos ao módulo.  O módulo utiliza este certificado para ligar ao IoT Hub, outros módulos ou iniciar um servidor. Quando o token ou certificado assinado se aproxima da expiração, é da responsabilidade do módulo solicitar um novo certificado.

### <a name="integration-and-maintenance"></a>Integração e manutenção

A Microsoft mantém a base de código principal para o [daemon de segurança IoT Edge no GitHub](https://github.com/Azure/iotedge/tree/master/edgelet).

#### <a name="installation-and-updates"></a>Instalação e atualizações

A instalação e atualizações do daemon de segurança IoT Edge são geridas através do sistema de gestão de pacotes do sistema operativo. Os dispositivos IoT Edge com raiz de confiança de hardware devem fornecer um endurecimento adicional à integridade do daemon, gerindo o seu ciclo de vida através dos sistemas de gestão de boot e atualizações seguros. Os fabricantes de dispositivos devem explorar estas avenidas com base nas respetivas capacidades de dispositivo.

#### <a name="versioning"></a>Controlo de versões

O tempo de execução do IoT Edge rastreia e reporta a versão do daemon de segurança IoT Edge. A versão é reportada como o atributo *runtime.platform.version* do módulo de agente IoT Edge reportado propriedade.

### <a name="hardware-security-module-platform-abstraction-layer-hsm-pal"></a>Camada de abstração de plataforma de módulode segurança de hardware (HSM PAL)

O HSM PAL abstrai toda a raiz do hardware de confiança para isolar o desenvolvedor ou utilizador do IoT Edge das suas complexidades.  Inclui uma combinação de interface de programação de aplicações (API) e procedimentos de comunicação trans-domínio, por exemplo, comunicação entre um ambiente de execução padrão e um enclave seguro.  A implementação real do HSM PAL depende do hardware seguro específico em uso. A sua existência permite a utilização de praticamente qualquer hardware de silício seguro.

## <a name="secure-silicon-root-of-trust-hardware"></a>Raiz segura de silicone do hardware de confiança

O silício seguro é necessário para ancorar a confiança dentro do hardware do dispositivo IoT Edge.  O silício seguro vem na variedade para incluir o Módulo de Plataforma Fidedigna (TPM), elemento seguro incorporado (eSE), ARM TrustZone, Intel SGX e tecnologias personalizadas de silício seguro.  Recomenda-se a utilização de raiz de confiança de silício seguro nos dispositivos, dadas as ameaças associadas à acessibilidade física dos dispositivos IoT.

## <a name="iot-edge-security-manager-integration-and-maintenance"></a>Integração e manutenção do gestor de segurança IoT Edge

O gestor de segurança IoT Edge pretende identificar e isolar os componentes que defendem a segurança e integridade da plataforma Azure IoT Edge para endurecimento personalizado. Terceiros, como fabricantes de dispositivos, devem utilizar funcionalidades de segurança personalizadas disponíveis com o hardware do seu dispositivo.  Consulte a secção de próximos passos para links que demonstrem como endurecer o gestor de segurança Azure IoT com o Módulo de Plataforma Fidedigna (TPM) nas plataformas Linux e Windows. Estes exemplos utilizam software ou TPMs virtuais, mas aplicam-se diretamente à utilização de dispositivos TPM discretos.  

## <a name="next-steps"></a>Passos seguintes

Leia o blog sobre [A fixação da vantagem inteligente](https://azure.microsoft.com/blog/securing-the-intelligent-edge/).

Crie e forre um [dispositivo IoT Edge com um TPM virtual numa máquina virtual Linux](how-to-auto-provision-simulated-device-linux.md).

Crie e forre um [dispositivo IoT Edge com um TPM simulado no Windows](how-to-auto-provision-simulated-device-windows.md).
