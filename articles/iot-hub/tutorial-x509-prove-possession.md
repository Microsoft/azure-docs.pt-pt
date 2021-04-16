---
title: Tutorial - Prove a propriedade dos certificados ca no Azure IoT Hub | Microsoft Docs
description: Tutorial - Prove que possui um certificado de AC para o Azure IoT Hub
author: v-gpettibone
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 02/26/2021
ms.author: robinsh
ms.custom:
- mvc
- 'Role: Cloud Development'
- 'Role: Data Analytics'
ms.openlocfilehash: b7740fa1f6a54dcfcc1181dddedcdd5fdb50402c
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378232"
---
# <a name="tutorial-proving-possession-of-a-ca-certificate"></a>Tutorial: Comprovativo da posse de um certificado de AC

Depois de enviar o certificado da autoridade de certificação de raiz (CA) ou o certificado de CA subordinado para o seu hub IoT, deve provar que é dono do certificado:

1. No portal Azure, navegue para o seu IoTHub e selecione **Definições > Certificados**.

2. **Selecione Adicionar** para adicionar um novo certificado de CA.

3. Introduza um nome de exibição no campo **Nome de Certificado** e selecione o certificado PEM para adicionar.

4. Selecione **Guardar**. O seu certificado está indicado na lista de certificados com estatuto de **Não verificado.** Este processo de verificação provará que tem a posse do certificado.

5. Selecione o certificado para ver o diálogo Detalhes do **Certificado.**

6. Selecione **Código de Verificação de Geração** no diálogo.

  :::image type="content" source="media/tutorial-x509-prove-possession/certificate-details.png" alt-text="{Diálogo de detalhes do certificado}":::

7. Copie o código de verificação para a área de transferência. Tem de definir o código de verificação como o sujeito do certificado. Por exemplo, se o código de verificação for 75B86466DA34D2B04C0C0C4C4C9557A119687ADAE7D47332BDDB3, adicione-o como objeto do seu certificado como indicado no passo seguinte.

8. Existem três formas de gerar um certificado de verificação:

    * Se estiver a utilizar o script PowerShell fornecido pela Microsoft, corra `New-CACertsVerificationCert "75B86466DA34D2B04C0C4C9557A119687ADAE7D4732BDDB3"` para criar um certificado denominado `VerifyCert4.cer` . Para obter mais informações, consulte [a utilização de Scripts fornecidos pela Microsoft.](tutorial-x509-scripts.md)

    * Se estiver a utilizar o script Bash fornecido pela Microsoft, corra `./certGen.sh create_verification_certificate "75B86466DA34D2B04C0C4C9557A119687ADAE7D4732BDDB3"` para criar um certificado denominado `verification-code.cert.pem` . Para obter mais informações, consulte [a utilização de Scripts fornecidos pela Microsoft.](tutorial-x509-scripts.md)

    * Se estiver a utilizar o OpenSSL para gerar os seus certificados, tem primeiro de gerar uma chave privada e, em seguida, um pedido de assinatura de certificado (CSR):

      ```bash
      $ openssl genpkey -out pop.key -algorithm RSA -pkeyopt rsa_keygen_bits:2048

      $ openssl req -new -key pop.key -out pop.csr

      -----
      Country Name (2 letter code) [XX]:.
      State or Province Name (full name) []:.
      Locality Name (eg, city) [Default City]:.
      Organization Name (eg, company) [Default Company Ltd]:.
      Organizational Unit Name (eg, section) []:.
      Common Name (eg, your name or your server hostname) []:75B86466DA34D2B04C0C4C9557A119687ADAE7D4732BDDB3
      Email Address []:

      Please enter the following 'extra' attributes
      to be sent with your certificate request
      A challenge password []:
      An optional company name []:
 
      ```

      Em seguida, crie um certificado utilizando o ficheiro de configuração raiz CA (mostrado abaixo) ou o ficheiro de configuração de CA subordinado e o CSR.

      ```bash
      openssl ca -config rootca.conf -in pop.csr -out pop.crt -extensions client_ext

      ```

    Para obter mais informações, consulte [utilizar o OpenSSL para criar certificados de teste.](tutorial-x509-openssl.md)

10. Selecione o novo certificado na vista Detalhes do **Certificado.**

11. Depois do upload do certificado, **selecione Verificar**. O estado do certificado de CA deve ser alterado para **Verificado**.
