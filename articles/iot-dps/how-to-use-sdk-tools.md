---
title: Utilize ferramentas sdKs do serviço de fornecimento de dispositivos Azure IoT Hub
description: Este documento analisa as ferramentas fornecidas nos SDKs do Serviço de Fornecimento de Dispositivos Do Hub Azure IoT (DPS) para desenvolvimento
author: robinsh
ms.author: robinsh
ms.date: 04/09/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: b817b3cfe47ed08cae9e7e0b1c2c24363f2ccfed
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79271528"
---
# <a name="how-to-use-tools-provided-in-the-sdks-to-simplify-development-for-provisioning"></a>Como utilizar as ferramentas fornecidas nos SDKs para simplificar o desenvolvimento do provisionamento
O Serviço de Provisionamento de Dispositivos IoT Hub simplifica o processo [](concepts-auto-provisioning.md) de provisionamento com fornecimento automático de toque zero, just-in-time de forma segura e escalável.  É necessário um atestado de segurança sob a forma de certificado X.509 ou Módulo de Plataforma Fidedigna (TPM).  A Microsoft também está em parceria com [outros parceiros](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) de hardware de segurança para melhorar a confiança na segurança da implementação do IoT. Compreender o requisito de segurança do hardware pode ser bastante desafiante para os desenvolvedores. Um conjunto de SDKs de Serviço de Provisionamento Azure IoT são fornecidos para que os desenvolvedores possam usar uma camada de conveniência para escrever clientes que falam com o serviço de provisionamento. Os SDKs também fornecem amostras para cenários comuns, bem como um conjunto de ferramentas para simplificar o atestado de segurança em desenvolvimento.

## <a name="trusted-platform-module-tpm-simulator"></a>Simulador de Módulo de Plataforma Fidedigna (TPM)
[O TPM](https://docs.microsoft.com/azure/iot-dps/concepts-security) pode consultar uma norma para armazenar chaves de forma segura para autenticar a plataforma, ou pode consultar a interface De/S utilizada para interagir com os módulos que implementam a norma. Os TPMs podem existir como hardware discreto, hardware integrado, baseado em firmware ou baseado em software.  Em produção, a TPM está localizada no dispositivo, quer como hardware discreto, hardware integrado ou baseado em firmware. Na fase de teste, um simulador TPM baseado em software é fornecido aos desenvolvedores.  Este simulador só está disponível para desenvolvimento na plataforma Windows por enquanto.

Os passos para a utilização do simulador TPM são:
1. [Preparar o ambiente de desenvolvimento](https://docs.microsoft.com/azure/iot-dps/quick-enroll-device-x509-java) e clonar o repositório GitHub:
   ```
   git clone https://github.com/Azure/azure-iot-sdk-java.git
   ```
2. Navegue para a pasta simulador tPM sob ```azure-iot-sdk-java/provisioning/provisioning-tool/tpm-simulator/```.
3. Executar Simulator.exe antes de executar qualquer pedido de cliente para dispositivo de provisionamento.
4. Deixe o simulador funcionar em segundo plano durante todo o processo de provisionamento para obter identificação de registo e chave de averbamento.  Ambos os valores são válidos apenas para um exemplo da execução.

## <a name="x509-certificate-generator"></a>Gerador de certificadoX.509
[Os certificados X.509](https://docs.microsoft.com/azure/iot-dps/concepts-security#x509-certificates) podem ser utilizados como um mecanismo de atestado para escalar a produção e simplificar o fornecimento de dispositivos.  Existem [várias formas](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview#how-to-get-an-x509-ca-certificate) de obter um certificado X.509:
* Para o ambiente de produção, recomendamos a aquisição de um certificado De A X.509 de uma autoridade pública de certificados de raiz.
* Para o ambiente de teste, pode gerar um certificado de raiz X.509 ou uma cadeia de certificados X.509 utilizando:
    * OpenSSL: Pode utilizar scripts para a geração de certificados:
        * [Node.js](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/tools)
        * [PowerShell ou Bash](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)
        
    * Emulador de composição de identidade do dispositivo (DICE): Dice pode ser usado para identidade e atestado de dispositivocriptográfico com base no protocolo TLS e nos certificados de cliente X.509.  [Saiba](https://www.microsoft.com/research/publication/device-identity-dice-riot-keys-certificates/) mais sobre a identidade do dispositivo com o DICE.

### <a name="using-x509-certificate-generator-with-dice-emulator"></a>Utilização de gerador de certificadoX.509 com emulador DICE
Os SDKs fornecem um gerador de certificadoX.509 com emulador DICE, localizado no [Java SDK](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-tools/provisioning-x509-cert-generator).  Este gerador funciona através da plataforma.  O certificado gerado pode ser utilizado para o desenvolvimento noutras línguas.

Atualmente, enquanto o Emulador DICE produz um certificado de raiz, um certificado intermédio, um certificado de folha e uma chave privada associada.  Todavia, o certificado de raiz ou o certificado intermédio não podem ser utilizados para assinar um certificado de folha separado.  Se pretender testar o cenário de inscrição em grupo onde um certificado de assinatura é usado para assinar os certificados de folha de vários dispositivos, pode utilizar o OpenSSL para produzir uma cadeia de certificados.

Para gerar certificado X.509 utilizando este gerador:
1. [Preparar o ambiente de desenvolvimento](https://docs.microsoft.com/azure/iot-dps/quick-enroll-device-x509-java) e clonar o repositório GitHub:
   ```
   git clone https://github.com/Azure/azure-iot-sdk-java.git
   ```
2. Mude a raiz para azure-iot-sdk-java.
3. Faça ```mvn install -DskipTests=true``` para descarregar todos os pacotes necessários e compilar o SDK
4. Navegue até à raiz do Gerador de Certificados X.509 em ```azure-iot-sdk-java/provisioning/provisioning-tools/provisioning-x509-cert-generator```.
5. Construir com ```mvn clean install```
6. Execute a ferramenta com os comandos seguintes:
   ```
   cd target
   java -jar ./provisioning-x509-cert-generator-{version}-with-deps.jar
   ```
7. Quando lhe for pedido, pode introduzir opcionalmente um _Nome Comum_ para os certificados.
8. A ferramenta localmente gera um **Cliente Cert,** a **Chave Privada Cliente Cert,** **Cert Intermediário,** e o **Cert Raiz.**

**Cliente Cert** é o certificado de folha no dispositivo.  **O cliente Cert** e o cliente associado **Cert Private Key** são necessários no cliente do dispositivo. Dependendo da linguagem que escolher, o mecanismo para colocar isso na aplicação do cliente pode ser diferente.  Para mais informações, consulte os [Quickstarts](https://docs.microsoft.com/azure/iot-dps/quick-create-simulated-device-x509) sobre criar um dispositivo simulado utilizando X.509 para obter mais informações.

O certificado de raiz ou intermédio pode ser utilizado para criar um grupo de inscrições ou inscrição individual [programática ou](https://docs.microsoft.com/azure/iot-dps/how-to-manage-enrollments-sdks) utilizando o [portal](https://docs.microsoft.com/azure/iot-dps/how-to-manage-enrollments).

## <a name="next-steps"></a>Passos seguintes
* Desenvolver a utilização do [Azure IoT SDK]( https://github.com/Azure/azure-iot-sdks) para o Azure IoT Hub e o Serviço de Provisionamento de Dispositivos Azure IoT Hub
