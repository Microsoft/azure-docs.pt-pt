---
title: Utilizar ferramentas fornecidas nos SDKs do Azure IoT Hub dispositivo aprovisionamento de serviço para simplificar o desenvolvimento
description: Este documento examina as ferramentas fornecidas no SDKs de serviço de aprovisionamento a dispositivos do Azure IoT Hub para o desenvolvimento
author: yzhong94
ms.author: yizhon
ms.date: 04/09/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: arjmands
ms.openlocfilehash: dc8c29b1c7d4e5056cb6aeee6335e32687fd547f
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58123679"
---
# <a name="how-to-use-tools-provided-in-the-sdks-to-simplify-development-for-provisioning"></a>Como utilizar ferramentas fornecidas nos SDKs para simplificar o desenvolvimento para o aprovisionamento
O serviço de aprovisionamento de dispositivos do IoT Hub simplifica o processo de aprovisionamento sem toque, just-in-time [aprovisionamento automático](concepts-auto-provisioning.md) de forma segura e dimensionável.  É necessário o atestado de segurança sob a forma de certificado X.509 ou Trusted Platform Module (TPM).  Microsoft também é a nossa parceria com a [outros parceiros de hardware de segurança](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) para melhorar a confiança na proteção de implementação de IoT. Noções básicas sobre o requisito de segurança de hardware pode ser um grande desafio para os desenvolvedores. Um conjunto de SDKs de serviço de aprovisionamento do Azure IoT são fornecidos para que os desenvolvedores podem usar uma camada de conveniência para clientes de escrita que comunicar com o serviço de aprovisionamento. Os SDKs também fornecem exemplos para cenários comuns, bem como um conjunto de ferramentas para simplificar o atestado de segurança no desenvolvimento.

## <a name="trusted-platform-module-tpm-simulator"></a>Simulador de Platform Module (TPM) fidedigna
[TPM](https://docs.microsoft.com/azure/iot-dps/concepts-security) pode fazer referência a um padrão para armazenar de forma segura as chaves para autenticar a plataforma, ou ele pode consultar a interface de e/s utilizada para interagir com os módulos implementando o padrão. Os TPMs podem existir como distintos a hardware, hardware integrada, com base em firmware ou baseada em software.  Na produção, TPM está localizado no dispositivo, como distintos a hardware, hardware integrada, ou baseada em firmware. Na fase de teste, um simulador TPM com base em software é fornecido para desenvolvedores.  Este simulador só está disponível para o desenvolvimento na plataforma Windows, por agora.

Passos para utilizar o simulador de TPM são:
1. [Preparar o ambiente de desenvolvimento](https://docs.microsoft.com/azure/iot-dps/quick-enroll-device-x509-java) e clonar o repositório do GitHub:
   ```
   git clone https://github.com/Azure/azure-iot-sdk-java.git
   ```
2. Navegue para a pasta do simulador TPM em ```azure-iot-sdk-java/provisioning/provisioning-tool/tpm-simulator/```.
3. Execute Simulator.exe antes de executar qualquer aplicação de cliente para o aprovisionamento de dispositivo.
4. Permitir que o simulador em execução em segundo plano em todo o processo de aprovisionamento para obter o ID de registo e a chave de endossamento.  Ambos os valores só são válidos para uma instância da execução.

## <a name="x509-certificate-generator"></a>Gerador de certificados X.509
[Certificados X.509](https://docs.microsoft.com/azure/iot-dps/concepts-security#x509-certificates) pode ser utilizado como um mecanismo de atestado para aumentar a produção e simplificar o aprovisionamento de dispositivos.  Existem [várias formas](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview#how-to-get-an-x509-ca-certificate) para obter um certificado X.509:
* Para o ambiente de produção, recomendamos que adquirir um certificado X.509 de AC de uma autoridade de certificação de raiz público.
* Para o ambiente de teste, pode gerar um certificado de raiz X.509 certificado X.509 ou cadeia com:
    * OpenSSL: Pode usar scripts para a geração do certificado:
        * [Node.js](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/tools)
        * [PowerShell ou Bash](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)
        
    * Device Identity Composition Engine (DICE) Emulator: DICE pode ser utilizado para a identidade de dispositivo criptográficos e atestado com base no protocolo TLS e X.509 certificados de cliente.  [Saiba](https://www.microsoft.com/research/publication/device-identity-dice-riot-keys-certificates/) mais sobre a identidade de dispositivo com DICE.

### <a name="using-x509-certificate-generator-with-dice-emulator"></a>Utilizar o gerador de certificados X.509 com o emulador DICE
Os SDKs fornecem um gerador de certificados X.509 com o emulador de DICE, localizado na [SDK de Java](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-tools/provisioning-x509-cert-generator).  Esse gerador funciona em várias plataformas.  O certificado gerado pode ser utilizado para o desenvolvimento em outras linguagens.

Atualmente, enquanto o emulador de REPARTIR produz um certificado de raiz, um certificado intermédio, um certificado de folha e chave privada associada.  No entanto, o certificado de raiz ou intermediária não pode ser utilizado para assinar um certificado de folha separado.  Se pretende testar o cenário de inscrição de grupo em que um certificado de assinatura é utilizado para assinar os certificados de folha de vários dispositivos, pode utilizar OpenSSL para produzir uma cadeia de certificados.

Para gerar certifikát X.509 pomocí esse gerador:
1. [Preparar o ambiente de desenvolvimento](https://docs.microsoft.com/azure/iot-dps/quick-enroll-device-x509-java) e clonar o repositório do GitHub:
   ```
   git clone https://github.com/Azure/azure-iot-sdk-java.git
   ```
2. Altere a raiz para o azure-iot-sdk-java.
3. Executar ```mvn install -DskipTests=true``` para transferir pacotes tudo necessários e compile o SDK
4. Navegue para a raiz para X.509 Certificate Generator no ```azure-iot-sdk-java/provisioning/provisioning-tools/provisioning-x509-cert-generator```.
5. Crie com ```mvn clean install```
6. Execute a ferramenta com os comandos seguintes:
   ```
   cd target
   java -jar ./provisioning-x509-cert-generator-{version}-with-deps.jar
   ```
7. Quando lhe for pedido, pode introduzir opcionalmente um _Nome Comum_ para os certificados.
8. A ferramenta gera localmente um **certificado de cliente**, o **chave privada do certificado de cliente**, **certificado intermédio**e a **certificado de raiz**.

**Certificado de cliente** é o certificado de folha no dispositivo.  **Certificado de cliente** e associada **chave privada do certificado de cliente** são necessárias no cliente do dispositivo. Dependendo do idioma que escolher, o mecanismo de colocá-lo no aplicativo cliente pode ser diferente.  Para obter mais informações, consulte a [inícios Rápidos](https://docs.microsoft.com/azure/iot-dps/quick-create-simulated-device-x509) na criação de dispositivo simulado com o X.509 para obter mais informações.

O certificado de raiz ou intermediário pode ser utilizado para criar um grupo de inscrição ou inscrição individual [programaticamente](https://docs.microsoft.com/azure/iot-dps/how-to-manage-enrollments-sdks) ou utilizando o [portal](https://docs.microsoft.com/azure/iot-dps/how-to-manage-enrollments).

## <a name="next-steps"></a>Passos Seguintes
* Programar com o [SDK do Azure IoT]( https://github.com/Azure/azure-iot-sdks) para o IoT Hub do Azure e o serviço de aprovisionamento de dispositivos do Azure IoT Hub
