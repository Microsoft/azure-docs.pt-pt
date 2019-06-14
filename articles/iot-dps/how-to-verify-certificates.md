---
title: Como fazer uma prova de posse para certificados de AC X.509 no serviço de aprovisionamento de dispositivos do Azure IoT Hub | Documentos da Microsoft
description: Como verificar os certificados X.509 de AC com o seu serviço de aprovisionamento de dispositivos
author: wesmc7777
ms.author: wesmc
ms.date: 02/26/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: afa4b3861e9fb7f91fd9f5d540353c5fad23efe0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "54913619"
---
# <a name="how-to-do-proof-of-possession-for-x509-ca-certificates-with-your-device-provisioning-service"></a>Como fazer uma prova de posse para os certificados X.509 de AC com o serviço de aprovisionamento de dispositivos

Um verificado X.509 certificado de autoridade (CA) certificado é um certificado de AC que foi carregado e registrado para o aprovisionamento de serviço e passou por uma prova de posse com o serviço. 

Prova de posse envolve os seguintes passos:
1. Obtenha um código de verificação exclusivo gerado pelo serviço de aprovisionamento para o seu certificado X.509 de AC. Pode fazê-lo a partir do portal do Azure.
2. Criar um certificado de verificação de X.509 com o código de verificação como o assunto e assinar os certificados com a chave privada associada com seu certificado X.509 de AC.
3. Carregue o certificado de verificação assinado para o serviço. O serviço valida o certificado de verificação usando a parte pública do certificado da AC para ser verificadas, assim a provar que está na posse da chave privada do certificado de AC.

Verificado certificados desempenham um papel importante ao utilizar grupos de inscrição. Verificar a propriedade do certificado fornece uma camada de segurança adicional, garantindo que o carregador do certificado estiver na posse da chave privada do certificado. Verificação impede que um ator malicioso seu tráfego de extrair um certificado intermédio e utilizar esse certificado para criar um grupo de inscrição no seu próprio serviço de aprovisionamento, a detecção de hijacking com eficiência de seus dispositivos. Por a provar a propriedade de raiz ou um certificado intermédio numa cadeia de certificados, está a provar que tem permissão para gerar certificados de folha para os dispositivos que irão registar como parte desse grupo de inscrição. Para este motivo, o certificado raiz ou intermediário configurado num grupo de inscrição tem de ser um certificado verificado ou tem o roll até um certificado verificado na cadeia de certificados de um dispositivo apresenta quando autentica com o serviço. Para saber mais sobre os grupos de inscrição, veja [certificados X.509](concepts-security.md#x509-certificates) e [controlar o acesso de dispositivo ao serviço de aprovisionamento com certificados X.509](concepts-security.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates).

## <a name="register-the-public-part-of-an-x509-certificate-and-get-a-verification-code"></a>Registre-se a parte pública de um certificado X.509 e obter um código de verificação

Para registrar um certificado de AC com o serviço de aprovisionamento e obter um código de verificação que podem ser usados durante a prova de posse, siga estes passos. 

1. No portal do Azure, navegue para o serviço de aprovisionamento e abra **certificados** no menu esquerdo. 
2. Clique em **adicionar** para adicionar um novo certificado.
3. Introduza um nome a apresentar amigável para o seu certificado. Procure o ficheiro. cer ou. pem que representa a parte pública do certificado X.509. Clique em **Carregar**.
4. Depois de obter uma notificação que o certificado é carregado com êxito, clique em **guardar**.

    ![Carregar certificado](./media/how-to-verify-certificates/add-new-cert.png)  

   O certificado irá mostrar no **Explorador de certificados** lista. Tenha em atenção que o **STATUS** deste certificado é *verificado*.

5. Clique no certificado que adicionou no passo anterior.

6. Na **detalhes do certificado**, clique em **gerar código de verificação**.

7. O serviço de aprovisionamento cria um **código de verificação** que pode utilizar para validar a propriedade do certificado. Copie o código para a área de transferência. 

   ![Verificar o certificado](./media/how-to-verify-certificates/verify-cert.png)  

## <a name="digitally-sign-the-verification-code-to-create-a-verification-certificate"></a>Assinar digitalmente o código de verificação para criar um certificado de verificação

Agora, tem de iniciar sessão a *código de verificação* com a chave privada associada com seu certificado X.509 de AC, que gera uma assinatura. Isso é conhecido como [prova de posse](https://tools.ietf.org/html/rfc5280#section-3.1) e resulta num certificado de verificação assinado.

A Microsoft fornece ferramentas e exemplos que podem ajudá-lo a criar um certificado de verificação assinado: 

- O **SDK C do Azure IoT Hub** fornece PowerShell (Windows) e scripts de Bash (Linux) para ajudar a criar certificados de AC e folha para desenvolvimento e para realizar uma prova de posse com um código de verificação. Pode transferir o [arquivos](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates) relevantes para o sistema para uma pasta de trabalho e siga as instruções no [Leiame de certificados de AC Managing](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) para efetuar uma prova de posse num certificado de AC. 
- O **do Azure IoT Hub c# SDK** contém o [exemplo de verificação de certificado do grupo](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/service/GroupCertificateVerificationSample), que pode utilizar para fazer uma prova de posse.
 
> [!IMPORTANT]
> Além de efetuar uma prova de posse, os scripts do PowerShell e o Bash citados anteriormente também permitem que criar certificados de raiz e certificados de folha que podem ser utilizados para autenticar e aprovisionar dispositivos certificados intermédios. Estes certificados devem ser usados apenas para programação. Nunca deve ser utilizadas num ambiente de produção. 

Contam com os scripts do PowerShell e o Bash fornecidos na documentação e SDKs [OpenSSL](https://www.openssl.org/). Também pode usar OpenSSL ou outras ferramentas de terceiros para o ajudar a fazer uma prova de posse. Para obter mais informações sobre as ferramentas fornecidas com os SDKs, consulte [como utilizar ferramentas fornecidas nos SDKs](how-to-use-sdk-tools.md). 


## <a name="upload-the-signed-verification-certificate"></a>Carregue o certificado de verificação assinado

1. Carregar a assinatura resultante como um certificado de verificação para o serviço de aprovisionamento no portal. Na **detalhes do certificado** no portal do Azure, utilize o _Explorador de ficheiros_ ícone junto aos **ficheiro. pem ou. cer do certificado de verificação** campo para carregar com sessão iniciada certificado de verificação do sistema.

2. Assim que o certificado é carregado com êxito, clique em **Verifique se**. O **STATUS** das suas modificações de certificado **_verificado_** no **Explorador de certificados** lista. Clique em **atualizar** se não atualizar automaticamente.

   ![Carregar a verificação de certificado](./media/how-to-verify-certificates/upload-cert-verification.png)  

## <a name="next-steps"></a>Passos Seguintes

- Para saber mais sobre como utilizar o portal para criar um grupo de inscrição, veja [gestão de inscrições de dispositivos com o portal do Azure](how-to-manage-enrollments.md).
- Para saber mais sobre como utilizar os SDKs de serviço para criar um grupo de inscrição, veja [gestão de inscrições de dispositivos com SDKs de serviço](how-to-manage-enrollments-sdks.md).










