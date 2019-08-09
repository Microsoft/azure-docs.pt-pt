---
title: Use as ferramentas fornecidas nos SDKs do serviço de provisionamento de dispositivos no Hub IoT do Azure para simplificar o desenvolvimento
description: Este documento revisa as ferramentas fornecidas nos SDKs do serviço de provisionamento de dispositivos no Hub IoT do Azure para desenvolvimento
author: robinsh
ms.author: robinsh
ms.date: 04/09/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 4fd4913776a7d21405f62a28d452bd50cd22f046
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68883012"
---
# <a name="how-to-use-tools-provided-in-the-sdks-to-simplify-development-for-provisioning"></a>Como usar as ferramentas fornecidas nos SDKs para simplificar o desenvolvimento para provisionamento
O serviço de provisionamento de dispositivos no Hub IoT simplifica o processo de provisionamento com [provisionamento automático](concepts-auto-provisioning.md) de toque zero e Just-in-time de maneira segura e escalonável.  O atestado de segurança na forma de certificado X. 509 ou de Trusted Platform Module (TPM) é necessário.  A Microsoft também está fazendo parceria com [outros parceiros de hardware de segurança](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) para melhorar a confiança na proteção da implantação de IOT. Entender o requisito de segurança de hardware pode ser bastante desafiador para os desenvolvedores. Um conjunto de SDKs do serviço de provisionamento do Azure IoT é fornecido para que os desenvolvedores possam usar uma camada de conveniência para escrever clientes que se comunicam com o serviço de provisionamento. Os SDKs também fornecem exemplos para cenários comuns, bem como um conjunto de ferramentas para simplificar o atestado de segurança no desenvolvimento.

## <a name="trusted-platform-module-tpm-simulator"></a>Simulador de Trusted Platform Module (TPM)
O [TPM](https://docs.microsoft.com/azure/iot-dps/concepts-security) pode se referir a um padrão para armazenar chaves com segurança para autenticar a plataforma ou pode consultar a interface de e/s usada para interagir com os módulos que implementam o padrão. O TPMs pode existir como hardware discreto, hardware integrado, baseado em firmware ou baseado em software.  Em produção, o TPM está localizado no dispositivo, como hardware discreto, hardware integrado ou baseado em firmware. Na fase de teste, um simulador de TPM baseado em software é fornecido aos desenvolvedores.  Este simulador só está disponível para o desenvolvimento na plataforma Windows por enquanto.

As etapas para usar o simulador TPM são:
1. [Prepare o ambiente de desenvolvimento](https://docs.microsoft.com/azure/iot-dps/quick-enroll-device-x509-java) e clone o repositório github:
   ```
   git clone https://github.com/Azure/azure-iot-sdk-java.git
   ```
2. Navegue até a pasta simulador de ```azure-iot-sdk-java/provisioning/provisioning-tool/tpm-simulator/```TPM em.
3. Execute o Simulator. exe antes de executar qualquer aplicativo cliente para o dispositivo de provisionamento.
4. Permita que o simulador seja executado em segundo plano em todo o processo de provisionamento para obter a ID de registro e a chave de endosso.  Os dois valores só são válidos para uma instância da execução.

## <a name="x509-certificate-generator"></a>Gerador de certificado X. 509
Os [certificados X. 509](https://docs.microsoft.com/azure/iot-dps/concepts-security#x509-certificates) podem ser usados como um mecanismo de atestado para dimensionar a produção e simplificar o provisionamento de dispositivos.  Há [várias maneiras](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview#how-to-get-an-x509-ca-certificate) de obter um certificado X. 509:
* Para o ambiente de produção, é recomendável adquirir um certificado de autoridade de certificação X. 509 de uma autoridade de certificação raiz pública.
* Para o ambiente de teste, você pode gerar um certificado raiz X. 509 ou uma cadeia de certificados X. 509 usando:
    * OpenSSL Você pode usar scripts para a geração de certificado:
        * [Node.js](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/tools)
        * [PowerShell ou bash](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)
        
    * Emulador do mecanismo de composição de identidade do dispositivo (os): Os databases podem ser usados para identidade do dispositivo de criptografia e atestado baseados em protocolo TLS e certificados de cliente X. 509.  [Saiba](https://www.microsoft.com/research/publication/device-identity-dice-riot-keys-certificates/) mais sobre a identidade do dispositivo com os Data.

### <a name="using-x509-certificate-generator-with-dice-emulator"></a>Usando o gerador de certificado X. 509 com o emulador de os
Os SDKs fornecem um gerador de certificado X. 509 com o emulador de, localizado no [SDK do Java](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-tools/provisioning-x509-cert-generator).  Esse gerador funciona entre plataformas.  O certificado gerado pode ser usado para desenvolvimento em outras linguagens.

Atualmente, enquanto o emulador de datagera um certificado raiz, um certificado intermediário, um certificado de folha e uma chave privada associada.  No entanto, o certificado raiz ou o certificado intermediário não podem ser usados para assinar um certificado secundário separado.  Se você pretende testar o cenário de registro de grupo em que um certificado de assinatura é usado para assinar os certificados de folha de vários dispositivos, você pode usar o OpenSSL para produzir uma cadeia de certificados.

Para gerar o certificado X. 509 usando este gerador:
1. [Prepare o ambiente de desenvolvimento](https://docs.microsoft.com/azure/iot-dps/quick-enroll-device-x509-java) e clone o repositório github:
   ```
   git clone https://github.com/Azure/azure-iot-sdk-java.git
   ```
2. Altere a raiz para Azure-IOT-SDK-Java.
3. Executar ```mvn install -DskipTests=true``` para baixar todos os pacotes necessários e compilar o SDK
4. Navegue até a raiz do gerador de certificado X. 509 ```azure-iot-sdk-java/provisioning/provisioning-tools/provisioning-x509-cert-generator```no.
5. Compilar com```mvn clean install```
6. Execute a ferramenta com os comandos seguintes:
   ```
   cd target
   java -jar ./provisioning-x509-cert-generator-{version}-with-deps.jar
   ```
7. Quando lhe for pedido, pode introduzir opcionalmente um _Nome Comum_ para os certificados.
8. A ferramenta gera localmente um **certificado de cliente**, **a chave privada de certificado de cliente**, o **certificado intermediário**e o **certificado raiz**.

**CERT do cliente** é o certificado de folha no dispositivo.  O **certificado do cliente** e a **chave privada do certificado do cliente** associado são necessários no cliente do dispositivo. Dependendo de qual idioma você escolher, o mecanismo para colocá-lo no aplicativo cliente pode ser diferente.  Para obter mais informações, consulte os [guias de início rápido](https://docs.microsoft.com/azure/iot-dps/quick-create-simulated-device-x509) em criar dispositivo simulado usando X. 509 para obter mais informações.

O certificado raiz ou intermediário pode ser usado para criar um grupo de registro ou registro individual de [forma programática](https://docs.microsoft.com/azure/iot-dps/how-to-manage-enrollments-sdks) ou usando o [portal](https://docs.microsoft.com/azure/iot-dps/how-to-manage-enrollments).

## <a name="next-steps"></a>Passos Seguintes
* Desenvolver usando o [SDK do Azure IOT]( https://github.com/Azure/azure-iot-sdks) para o Hub IOT do Azure e o serviço de provisionamento de dispositivos no Hub IOT do Azure
