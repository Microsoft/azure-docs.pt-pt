---
title: Como fazer a prova de posse para certificados de AC X. 509 com o serviço de provisionamento de dispositivos no Hub IoT do Azure
description: Como fazer a prova de posse para certificados de AC X. 509 com o serviço de provisionamento de dispositivos no Hub IoT do Azure
author: wesmc7777
ms.author: wesmc
ms.date: 02/26/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: e762a1ab307bdc5ca9369c3f2e424cf6fd35f163
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73890634"
---
# <a name="how-to-do-proof-of-possession-for-x509-ca-certificates-with-your-device-provisioning-service"></a>Como fazer a prova de posse para certificados de AC X. 509 com o serviço de provisionamento de dispositivos

Um certificado de autoridade de certificação (CA) do X. 509 verificado é um certificado de autoridade de certificação que foi carregado e registrado no serviço de provisionamento e passou pela prova de posse com o serviço. 

A prova de posse envolve as seguintes etapas:
1. Obtenha um código de verificação exclusivo gerado pelo serviço de provisionamento para seu certificado de autoridade de certificação X. 509. Você pode fazer isso na portal do Azure.
2. Crie um certificado de verificação X. 509 com o código de verificação como seu assunto e assine o certificado com a chave privada associada ao seu certificado de autoridade de certificação X. 509.
3. Carregue o certificado de verificação assinado para o serviço. O serviço valida o certificado de verificação usando a parte pública do certificado de autoridade de certificação a ser verificado, comprovando que você está em posse da chave privada do certificado de autoridade de certificação.

Os certificados verificados desempenham uma função importante ao usar grupos de registro. Verificar a propriedade do certificado fornece uma camada de segurança adicional, garantindo que o carregador do certificado esteja em posse da chave privada do certificado. A verificação impede que um ator mal-intencionado farejando o tráfego de extrair um certificado intermediário e usar esse certificado para criar um grupo de registro em seu próprio serviço de provisionamento, seqüestrando efetivamente seus dispositivos. Ao provar a propriedade da raiz ou um certificado intermediário em uma cadeia de certificados, você está provando que tem permissão para gerar certificados de folha para os dispositivos que serão registrados como parte desse grupo de registro. Por esse motivo, o certificado raiz ou intermediário configurado em um grupo de registro deve ser um certificado verificado ou deve ser acumulado em um certificado verificado na cadeia de certificados que um dispositivo apresenta quando é autenticado com o serviço. Para saber mais sobre grupos de registro, consulte [certificados x. 509](concepts-security.md#x509-certificates) e [controlando o acesso do dispositivo ao serviço de provisionamento com certificados x. 509](concepts-security.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates).

## <a name="register-the-public-part-of-an-x509-certificate-and-get-a-verification-code"></a>Registrar a parte pública de um certificado X. 509 e obter um código de verificação

Para registrar um certificado de autoridade de certificação no serviço de provisionamento e obter um código de verificação que você pode usar durante a prova de posse, siga estas etapas. 

1. No portal do Azure, navegue até o serviço de provisionamento e abra **certificados** no menu à esquerda. 
2. Clique em **Adicionar** para adicionar um novo certificado.
3. Insira um nome de exibição amigável para seu certificado. Navegue até o arquivo. cer ou. pem que representa a parte pública do seu certificado X. 509. Clique em **Carregar**.
4. Depois de receber uma notificação de que o certificado foi carregado com êxito, clique em **salvar**.

    ![Carregar certificado](./media/how-to-verify-certificates/add-new-cert.png)  

   Seu certificado será exibido na lista **Gerenciador de certificados** . Observe que o **status** desse certificado não é *verificado*.

5. Clique no certificado que você adicionou na etapa anterior.

6. Em **detalhes do certificado**, clique em **gerar código de verificação**.

7. O serviço de provisionamento cria um **código de verificação** que você pode usar para validar a propriedade do certificado. Copie o código para a área de transferência. 

   ![Verificar certificado](./media/how-to-verify-certificates/verify-cert.png)  

## <a name="digitally-sign-the-verification-code-to-create-a-verification-certificate"></a>Assinar digitalmente o código de verificação para criar um certificado de verificação

Agora, você precisa assinar o *código de verificação* com a chave privada associada ao seu certificado de autoridade de certificação X. 509, que gera uma assinatura. Isso é conhecido como [prova de posse](https://tools.ietf.org/html/rfc5280#section-3.1) e resulta em um certificado de verificação assinado.

A Microsoft fornece ferramentas e exemplos que podem ajudá-lo a criar um certificado de verificação assinado: 

- O **SDK C do Hub IOT do Azure** fornece scripts do PowerShell (Windows) e bash (Linux) para ajudá-lo a criar certificados de autoridade de certificação e folha para desenvolvimento e para executar a prova de posse usando um código de verificação. Você pode baixar os [arquivos](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates) relevantes para o seu sistema para uma pasta de trabalho e seguir as instruções no [Leiame de gerenciamento de certificados de autoridade de certificação](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) para executar a prova de posse em um certificado de autoridade de certificação. 
- O **SDK do Hub C# IOT do Azure** contém o [exemplo de verificação de certificado de grupo](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/service/GroupCertificateVerificationSample), que você pode usar para fazer uma prova de posse.
 
> [!IMPORTANT]
> Além de executar a prova de posse, os scripts do PowerShell e bash citados anteriormente também permitem que você crie certificados raiz, certificados intermediários e certificados folha que podem ser usados para autenticar e provisionar dispositivos. Esses certificados devem ser usados somente para desenvolvimento. Eles nunca devem ser usados em um ambiente de produção. 

Os scripts do PowerShell e bash fornecidos na documentação e nos SDKs dependem do [OpenSSL](https://www.openssl.org/). Você também pode usar o OpenSSL ou outras ferramentas de terceiros para ajudá-lo a fazer uma prova de posse. Para obter mais informações sobre as ferramentas fornecidas com os SDKs, consulte [como usar as ferramentas fornecidas nos SDKs](how-to-use-sdk-tools.md). 


## <a name="upload-the-signed-verification-certificate"></a>Carregar o certificado de verificação assinado

1. Carregue a assinatura resultante como um certificado de verificação para o serviço de provisionamento no Portal. Em **detalhes do certificado** no portal do Azure, use o ícone _Explorador de arquivos_ ao lado do campo arquivo. **PEM ou. cer do certificado de verificação** para carregar o certificado de verificação assinado do seu sistema.

2. Depois que o certificado for carregado com êxito, clique em **verificar**. O **status** do seu certificado é alterado para **_verificado_** na lista do **Gerenciador de certificados** . Clique em **Atualizar** se ele não for atualizado automaticamente.

   ![Carregar verificação de certificado](./media/how-to-verify-certificates/upload-cert-verification.png)  

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre como usar o portal para criar um grupo de registro, consulte [Gerenciando registros de dispositivo com o portal do Azure](how-to-manage-enrollments.md).
- Para saber mais sobre como usar os SDKs de serviço para criar um grupo de registro, consulte [Gerenciando registros de dispositivo com SDKs de serviço](how-to-manage-enrollments-sdks.md).










