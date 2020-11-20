---
title: Utilize ferramentas de fornecimento de SDKs de serviço de dispositivos Azure IoT
description: Este documento analisa as ferramentas fornecidas no Azure IoT Hub Device Provisioning Service (DPS) SDKs para desenvolvimento
author: wesmc7777
ms.author: wesmc
ms.date: 04/09/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 984d38752df93f233c6d87458e3c9ba713696177
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94967249"
---
# <a name="how-to-use-tools-provided-in-the-sdks-to-simplify-development-for-provisioning"></a>Como utilizar ferramentas fornecidas nos ODS para simplificar o desenvolvimento do provisionamento
O Serviço de Provisionamento de Dispositivos IoT Hub simplifica o processo de fornecimento com [provisão](about-iot-dps.md#provisioning-process) de toque zero, just-in-time de forma segura e escalável.  É necessário um atestado de segurança sob a forma de certificado X.509 ou Módulo de Plataforma Fidedigna (TPM).  A Microsoft também está em parceria com [outros parceiros de hardware](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) de segurança para melhorar a confiança na segurança da implementação de IoT. Compreender o requisito de segurança de hardware pode ser bastante desafiante para os desenvolvedores. Um conjunto de SDKs de serviço de fornecimento de IoT Azure são fornecidos para que os desenvolvedores possam usar uma camada de conveniência para escrever clientes que falem com o serviço de fornecimento. Os SDKs também fornecem amostras para cenários comuns, bem como um conjunto de ferramentas para simplificar o atestado de segurança em desenvolvimento.

## <a name="trusted-platform-module-tpm-simulator"></a>Simulador de módulo de plataforma fidedigno (TPM)
[O TPM](./concepts-service.md#attestation-mechanism) pode consultar uma norma para armazenar de forma segura as chaves para autenticar a plataforma, ou pode referir-se à interface de E/S utilizada para interagir com os módulos que implementam a norma. Os TPMs podem existir como hardware discreto, hardware integrado, baseado em firmware ou baseado em software.  Na produção, o TPM está localizado no dispositivo, seja como hardware discreto, hardware integrado ou baseado em firmware. Na fase de testes, um simulador de TPM baseado em software é fornecido aos desenvolvedores.  Este simulador só se encontra disponível para desenvolvimento na plataforma Windows por enquanto.

Os passos para a utilização do simulador TPM são:
1. [Prepare o ambiente de desenvolvimento](./quick-enroll-device-x509-java.md) e clone o repositório GitHub:
   ```
   git clone https://github.com/Azure/azure-iot-sdk-java.git
   ```
2. Navegue para a pasta do simulador TPM em ```azure-iot-sdk-java/provisioning/provisioning-tool/tpm-simulator/``` .
3. Executar Simulator.exe antes de executar qualquer aplicação do cliente para dispositivo de provisionamento.
4. Deixe o simulador funcionar em segundo plano durante todo o processo de provisionamento para obter iD de registo e Chave de Apoio.  Ambos os valores são válidos apenas para um exemplo da execução.

## <a name="x509-certificate-generator"></a>Gerador de certificado X.509
[Os certificados X.509](./concepts-x509-attestation.md#x509-certificates) podem ser usados como um mecanismo de atestado para escalar a produção e simplificar o fornecimento de dispositivos.  Existem [várias formas](../iot-hub/iot-hub-x509ca-overview.md#how-to-get-an-x509-ca-certificate) de obter um certificado X.509:
* Para o ambiente de produção, recomendamos a compra de um certificado X.509 CA a uma autoridade de certificados de raiz pública.
* Para o ambiente de teste, pode gerar um certificado de raiz X.509 ou uma cadeia de certificados X.509 utilizando:
    * OpenSSL: Pode utilizar scripts para a geração de certificados:
        * [Node.js](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/tools)
        * [PowerShell ou Bash](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)
        
    * Emulador de composição de identidade do dispositivo (DICE): DICE pode ser usado para identidade e atestação de dispositivos criptográficos com base no protocolo TLS e certificados de cliente X.509.  [Saiba](https://www.microsoft.com/research/publication/device-identity-dice-riot-keys-certificates/) mais sobre a identidade do dispositivo com a DICE.

### <a name="using-x509-certificate-generator-with-dice-emulator"></a>Utilizando gerador de certificado X.509 com emulador DICE
Os SDKs fornecem um gerador de certificado X.509 com emulador DICE, localizado no [Java SDK](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-tools/provisioning-x509-cert-generator).  Este gerador funciona numa plataforma transversal.  O certificado gerado pode ser utilizado para o desenvolvimento em outras línguas.

Atualmente, enquanto o DICE Emulator produz um certificado de raiz, um certificado intermédio, um certificado de folha e uma chave privada associada.  No entanto, o certificado de raiz ou o certificado intermédio não podem ser utilizados para assinar um certificado de folha separado.  Se pretende testar o cenário de inscrição em grupo onde um certificado de assinatura é usado para assinar os certificados de folha de vários dispositivos, pode utilizar o OpenSSL para produzir uma cadeia de certificados.

Para gerar certificado X.509 utilizando este gerador:
1. [Prepare o ambiente de desenvolvimento](./quick-enroll-device-x509-java.md) e clone o repositório GitHub:
   ```
   git clone https://github.com/Azure/azure-iot-sdk-java.git
   ```
2. Mude a raiz para azure-iot-sdk-java.
3. Corra ```mvn install -DskipTests=true``` para descarregar todos os pacotes necessários e compilar o SDK
4. Navegue até à raiz do Gerador de Certificados X.509 em ```azure-iot-sdk-java/provisioning/provisioning-tools/provisioning-x509-cert-generator``` .
5. Construir com ```mvn clean install```
6. Execute a ferramenta com os comandos seguintes:
   ```
   cd target
   java -jar ./provisioning-x509-cert-generator-{version}-with-deps.jar
   ```
7. Quando lhe for pedido, pode introduzir opcionalmente um _Nome Comum_ para os certificados.
8. A ferramenta gera localmente um **Cert do Cliente,** a **Chave Privada Cliente Cert,** **o Cert Intermédio** e o **Cert Raiz**.

**O Cliente Cert** é o certificado de folha no dispositivo.  **O Cliente Cert** e a **Chave Privada Cliente Cert** associada são necessários no cliente do dispositivo. Dependendo da língua que escolher, o mecanismo para colocar isso na aplicação do cliente pode ser diferente.  Para obter mais informações, consulte os [Quickstarts](./quick-create-simulated-device-x509.md) na criação de dispositivo simulado utilizando x.509 para obter mais informações.

O certificado de raiz ou intermediário pode ser utilizado para criar um grupo de inscrição ou inscrição individual [programática](./how-to-manage-enrollments-sdks.md) ou utilizando o [portal](./how-to-manage-enrollments.md).

## <a name="next-steps"></a>Passos seguintes
* Desenvolver usando o [Azure IoT SDK]( https://github.com/Azure/azure-iot-sdks) para Azure IoT Hub e Azure IoT Hub Device Provisioning Service