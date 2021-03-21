---
title: Verifique os certificados de CA X.509 com o Serviço de Provisionamento de Dispositivos Azure IoT Hub
description: Como fazer comprovativo de posse para certificados DE CA X.509 com Serviço de Provisionamento de Dispositivos Azure IoT Hub (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 02/26/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 0326eef86b42fa8b06c336bbf2b1c6f2f9df0bcf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101730359"
---
# <a name="how-to-do-proof-of-possession-for-x509-ca-certificates-with-your-device-provisioning-service"></a>Como fazer comprovativo de posse para certificados X.509 CA com o seu Serviço de Provisionamento de Dispositivos

Um certificado verificado da X.509 Certificate Authority (CA) é um certificado de CA que foi carregado e registado no seu serviço de fornecimento e passou por prova de posse com o serviço. 

A prova de posse envolve os seguintes passos:
1. Obtenha um código de verificação único gerado pelo serviço de fornecimento do seu certificado X.509 CA. Pode fazê-lo a partir do portal do Azure.
2. Crie um certificado de verificação X.509 com o código de verificação como sujeito e assine o certificado com a chave privada associada ao seu certificado X.509 CA.
3. Faça o upload do certificado de verificação assinado para o serviço. O serviço valida o certificado de verificação utilizando a parte pública do certificado de AC a verificar, provando assim que está na posse da chave privada do certificado de AC.

Os certificados verificados desempenham um papel importante na utilização de grupos de inscrição. A verificação da propriedade do certificado fornece uma camada de segurança adicional, garantindo que o carregador do certificado está na posse da chave privada do certificado. A verificação impede que um ator malicioso cheire o seu tráfego de extrair um certificado intermédio e usar esse certificado para criar um grupo de inscrição no seu próprio serviço de fornecimento, efetivamente sequestrando os seus dispositivos. Ao provar a propriedade da raiz ou um certificado intermédio numa cadeia de certificados, está a provar que tem permissão para gerar certificados de folha para os dispositivos que se registarão como parte desse grupo de inscrição. Por esta razão, o certificado de raiz ou intermédio configurado num grupo de matrículas deve ser um certificado verificado ou deve ser até um certificado verificado na cadeia de certificados que um dispositivo apresenta quando autentica com o serviço. Para saber mais sobre o atestado de certificado X.509, consulte [os certificados X.509](concepts-x509-attestation.md) e [controlando o acesso ao serviço de fornecimento com certificados X.509](concepts-x509-attestation.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates).

## <a name="register-the-public-part-of-an-x509-certificate-and-get-a-verification-code"></a>Registe a parte pública de um certificado X.509 e obtenha um código de verificação

Para registar um certificado de AC com o seu serviço de fornecimento e obter um código de verificação que pode utilizar durante a prova de posse, siga estes passos. 

1. No portal Azure, navegue para o seu serviço de fornecimento e abra **Certificados** a partir do menu da esquerda. 
2. Clique **em Adicionar** para adicionar um novo certificado.
3. Introduza um nome de exibição amigável para o seu certificado. Navegue no ficheiro .cer ou .pem que representa a parte pública do seu certificado X.509. Clique em **Carregar**.
4. Assim que receber a notificação de que o seu certificado é carregado com sucesso, clique em **Guardar**.

    ![Carregar certificado](./media/how-to-verify-certificates/add-new-cert.png)  

   O seu certificado será apresentado na lista do **Certificate Explorer.** Note que o **estado** deste certificado não é *verificado.*

5. Clique no certificado que adicionou no passo anterior.

6. Em **Detalhes do Certificado,** clique em **Código de Verificação de Geração.**

7. O serviço de fornecimento cria um **Código de Verificação** que pode utilizar para validar a propriedade do certificado. Copie o código para a sua área de transferência. 

   ![Verificar certificado](./media/how-to-verify-certificates/verify-cert.png)  

## <a name="digitally-sign-the-verification-code-to-create-a-verification-certificate"></a>Assine digitalmente o código de verificação para criar um certificado de verificação

Agora, tem de assinar o Código de *Verificação* com a chave privada associada ao seu certificado X.509 CA, que gera uma assinatura. Isto é conhecido como [Prova de Posse](https://tools.ietf.org/html/rfc5280#section-3.1) e resulta num certificado de verificação assinado.

A Microsoft fornece ferramentas e amostras que podem ajudá-lo a criar um certificado de verificação assinado: 

- O **Azure IoT Hub C SDK** fornece scripts PowerShell (Windows) e Bash (Linux) para ajudá-lo a criar certificados de ca e folhas para desenvolvimento e executar a prova de posse usando um código de verificação. Pode transferir os [ficheiros relevantes](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates) para o seu sistema para uma pasta de trabalho e seguir as instruções nos [certificados de Gestão de Ca ler-me](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) para efetuar o comprovativo de posse num certificado de CA. 
- O **Azure IoT Hub C# SDK** contém a Amostra de [Verificação](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/service/GroupCertificateVerificationSample)do Certificado de Grupo, que pode utilizar para fazer prova de posse.
 
> [!IMPORTANT]
> Além de realizar a prova de posse, os scripts PowerShell e Bash citados anteriormente também permitem criar certificados de raiz, certificados intermédios e certificados de folha que podem ser usados para autenticar e fornecer dispositivos. Estes certificados devem ser utilizados apenas para o desenvolvimento. Nunca devem ser utilizados num ambiente de produção. 

Os scripts PowerShell e Bash fornecidos na documentação e os SDKs dependem [do OpenSSL](https://www.openssl.org/). Também pode utilizar o OpenSSL ou outras ferramentas de terceiros para ajudá-lo a fazer prova de posse. Por exemplo, utilizando ferramentas fornecidas com os SDKs, consulte [Criar uma cadeia de certificados X.509](tutorial-custom-hsm-enrollment-group-x509.md#create-an-x509-certificate-chain). 


## <a name="upload-the-signed-verification-certificate"></a>Faça o upload do certificado de verificação assinado

1. Faça o upload da assinatura resultante como certificado de verificação para o seu serviço de provisionamento no portal. Em **Dados de Certificado** no portal Azure, utilize o ícone Explorador de  _Ficheiros_ ao lado do **Certificado de Verificação .pem ou .cer** campo de ficheiros para fazer o upload do certificado de verificação assinado do seu sistema.

2. Uma vez que o certificado seja carregado com sucesso, clique em **Verificar**. O **estado** do seu certificado altera-se a **_Verificado_** na lista do **Certificate Explorer.** Clique **em Atualizar** se não atualizar automaticamente.

   ![Verificação de certificado de upload](./media/how-to-verify-certificates/upload-cert-verification.png)  

## <a name="next-steps"></a>Passos seguintes

- Para saber como usar o portal para criar um grupo de [inscrições, consulte gestão de inscrições de dispositivos com portal Azure.](how-to-manage-enrollments.md)
- Para saber como usar os SDKs de serviço para criar um grupo de inscrições, consulte [a Gestão de inscrições de dispositivos com SDKs de serviço.](./quick-enroll-device-x509-java.md)