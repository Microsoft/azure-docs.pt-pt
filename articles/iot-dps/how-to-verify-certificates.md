---
title: Verifique os certificados X.509 CA com o Serviço de Provisionamento de Dispositivos Hub Azure IoT
description: Como fazer a prova de posse para certificados X.509 CA com o Serviço de Provisionamento de Dispositivos Hub Azure IoT (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 02/26/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: b008c4ebc83200043d51fc8ef367f1983c549949
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74973446"
---
# <a name="how-to-do-proof-of-possession-for-x509-ca-certificates-with-your-device-provisioning-service"></a>Como fazer a prova de posse para certificados X.509 CA com o seu Serviço de Provisionamento de Dispositivos

Um certificado verificado X.509 Certificate Authority (CA) é um certificado CA que foi carregado e registado no seu serviço de provisionamento e passou por uma prova de posse com o serviço. 

A prova de posse envolve os seguintes passos:
1. Obtenha um código de verificação único gerado pelo serviço de provisionamento para o seu certificado X.509 CA. Pode fazê-lo a partir do portal do Azure.
2. Crie um certificado de verificação X.509 com o código de verificação como seu sujeito e assine o certificado com a chave privada associada ao seu certificado X.509 CA.
3. Faça o upload do certificado de verificação assinado para o serviço. O serviço valida o certificado de verificação utilizando a parte pública do certificado CA a verificar, provando assim que está na posse da chave privada do certificado CA.

Os certificados verificados desempenham um papel importante na utilização de grupos de matrícula. A verificação da propriedade do certificado fornece uma camada de segurança adicional, garantindo que o carregador do certificado está na posse da chave privada do certificado. A verificação impede um ator malicioso de cheirar o seu tráfego de extrair um certificado intermédio e usar esse certificado para criar um grupo de inscrições no seu próprio serviço de provisionamento, sequestrando efetivamente os seus dispositivos. Ao provar a propriedade da raiz ou de um certificado intermédio numa cadeia de certificados, está a provar que tem permissão para gerar certificados de folhas para os dispositivos que se registarão como parte desse grupo de matrículas. Por esta razão, o certificado raiz ou intermédio configurado num grupo de matrícula deve ser um certificado verificado ou deve ser enrolado num certificado verificado na cadeia de certificados que um dispositivo apresenta quando autentica com o serviço. Para saber mais sobre os grupos de matrícula, consulte [os certificados X.509](concepts-security.md#x509-certificates) e o acesso do [dispositivo de controlo ao serviço de provisionamento com certificados X.509](concepts-security.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates).

## <a name="register-the-public-part-of-an-x509-certificate-and-get-a-verification-code"></a>Registe a parte pública de um certificado X.509 e obtenha um código de verificação

Para registar um certificado ca com o seu serviço de provisionamento e obter um código de verificação que pode usar durante a prova de posse, siga estes passos. 

1. No portal Azure, navegue para o seu serviço de provisionamento e abra **certificados** do menu à esquerda. 
2. Clique em **Adicionar** para adicionar um novo certificado.
3. Introduza um nome de exibição amigável para o seu certificado. Navegue no ficheiro .cer ou .pem que representa a parte pública do seu certificado X.509. Clique em **Carregar**.
4. Assim que receber uma notificação de que o seu certificado é carregado com sucesso, clique em **Guardar**.

    ![Carregar certificado](./media/how-to-verify-certificates/add-new-cert.png)  

   O seu certificado aparecerá na lista do Explorador de **Certificados.** Note que o **ESTADO** deste certificado não é *verificado*.

5. Clique no certificado que adicionou no passo anterior.

6. Em **Detalhes do Certificado,** clique em **Gerar Código de Verificação**.

7. O serviço de provisionamento cria um Código de **Verificação** que pode utilizar para validar a propriedade do certificado. Copie o código para a sua prancheta. 

   ![Verificar certificado](./media/how-to-verify-certificates/verify-cert.png)  

## <a name="digitally-sign-the-verification-code-to-create-a-verification-certificate"></a>Assine digitalmente o código de verificação para criar um certificado de verificação

Agora, você precisa assinar o Código de *Verificação* com a chave privada associada com o seu certificado De Ca X.509, que gera uma assinatura. Isto é conhecido como [Prova de Posse](https://tools.ietf.org/html/rfc5280#section-3.1) e resulta num certificado de verificação assinado.

A Microsoft fornece ferramentas e amostras que podem ajudá-lo a criar um certificado de verificação assinado: 

- O **Azure IoT Hub C SDK** fornece scripts PowerShell (Windows) e Bash (Linux) para ajudá-lo a criar certificados CA e folhas para desenvolvimento e executar a prova de posse usando um código de verificação. Pode transferir os [ficheiros relevantes](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates) para o seu sistema para uma pasta de trabalho e seguir as instruções nos [certificados de Gestão](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) de CA que se podem ler para executar a prova de posse num certificado CA. 
- O **Azure IoT Hub C# SDK** contém a amostra de verificação de certificados de [grupo,](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/service/GroupCertificateVerificationSample)que pode utilizar para fazer a prova de posse.
 
> [!IMPORTANT]
> Além de realizar provas de posse, os scripts PowerShell e Bash citados anteriormente também permitem criar certificados de raiz, certificados intermédios e certificados de folha que podem ser usados para autenticar e fornecer dispositivos. Estes certificados devem ser utilizados apenas para o desenvolvimento. Nunca devem ser usados num ambiente de produção. 

Os scripts PowerShell e Bash fornecidos na documentação e SDKs dependem da [OpenSSL](https://www.openssl.org/). Também pode utilizar o OpenSSL ou outras ferramentas de terceiros para o ajudar a fazer a prova de posse. Para obter mais informações sobre a ferramenta fornecida com os SDKs, consulte [como utilizar as ferramentas fornecidas nos SDKs](how-to-use-sdk-tools.md). 


## <a name="upload-the-signed-verification-certificate"></a>Faça upload do certificado de verificação assinado

1. Faça upload da assinatura resultante como certificado de verificação para o seu serviço de provisionamento no portal. Em **Dados de Certificado** no portal Azure, utilize o ícone do Explorador de _Ficheiros_ junto ao Certificado de **Verificação .pem ou no campo de ficheiros .cer** para fazer o upload do certificado de verificação assinado do seu sistema.

2. Assim que o certificado for carregado com sucesso, clique em **Verificar**. O **ESTADO** do seu certificado altera-se a **_Verificado_** na lista do Explorador de **Certificados.** Clique em **Refresh** se não atualizar automaticamente.

   ![Verificação de certificado de upload](./media/how-to-verify-certificates/upload-cert-verification.png)  

## <a name="next-steps"></a>Passos seguintes

- Para saber como usar o portal para criar um grupo de inscrições, consulte a Gestão de [inscrições de dispositivos com o portal Azure.](how-to-manage-enrollments.md)
- Para saber como utilizar os SDKs de serviço para criar um grupo de inscrições, consulte a Gestão de [inscrições de dispositivos com SDKs](how-to-manage-enrollments-sdks.md)de serviço .










