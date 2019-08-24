---
title: Proteger a comunicação de dispositivos OPC UA com o cofre OPC – Azure | Microsoft Docs
description: Como registrar aplicativos OPC UA e como emitir certificados de aplicativo assinados para seus dispositivos OPC UA com o cofre OPC.
author: mregen
ms.author: mregen
ms.date: 8/16/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: ae10bb3488c21b6637163e333231e95a18c652bf
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/23/2019
ms.locfileid: "69996024"
---
# <a name="how-to-use-the-opc-vault-certificate-management-service"></a>Como usar o serviço de gerenciamento de certificados do compartimento OPC

Este artigo explica como registrar aplicativos e como emitir certificados de aplicativo assinados para seus dispositivos OPC UA.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="deploy-the-certificate-management-service"></a>Implantar o serviço de gerenciamento de certificados

Em primeiro lugar, o serviço precisa ser implantado na nuvem do Azure.
Encontre um artigo que descreve como [implantar o serviço de gerenciamento de certificados do compartimento OPC](howto-opc-vault-deploy.md).

### <a name="create-the-issuer-ca-certificate"></a>Criar o certificado de autoridade de certificação do emissor

Se você ainda não tiver feito isso, crie o certificado de autoridade de certificação do emissor.

Encontre um artigo que descreve como [criar e gerenciar o certificado do emissor para o cofre OPC](howto-opc-vault-manage.md).

## <a name="secure-opc-ua-applications"></a>Proteger aplicativos OPC UA

### <a name="step-1-register-your-opc-ua-application"></a>Passo 1: Registrar seu aplicativo OPC UA 

> [!IMPORTANT]
> A função ' Writer ' é necessária para registrar um aplicativo.

1. Abra seu serviço de certificado `https://myResourceGroup-app.azurewebsites.net` em e entre.
2. Navegue até a `Register New` página.
1. Para um registro de aplicativo, um usuário precisa ter pelo menos a função ' Writer ' atribuída.
2. O formulário de entrada segue as convenções de nomenclatura no mundo do OPC UA. Por exemplo, na imagem abaixo das configurações para o exemplo de [servidor de referência do OPC UA](https://github.com/OPCFoundation/UA-.NETStandard/tree/master/SampleApplications/Workshop/Reference) no OPC UA. A pilha netstandard é mostrada:

   ![Registro do servidor de referência do UA](media/howto-opc-vault-secure/reference-server-registration.png "Registro do servidor de referência do UA")

5. Pressione o `Register` botão para registrar o aplicativo no banco de dados do aplicativo do serviço de certificado. O fluxo de trabalho orienta diretamente o usuário para a próxima etapa para solicitar um certificado assinado para o aplicativo.

### <a name="step-2-secure-your-application-with-a-ca-signed-application-certificate"></a>Passo 2: Proteger seu aplicativo com um certificado de aplicativo assinado por AC

Um aplicativo OPC UA pode ser protegido emitindo um certificado assinado com base em um CSR (solicitação de assinatura de certificado) ou solicitando um novo par de chaves, que inclui também uma nova chave privada no formato PFX ou PEM. Siga a documentação do seu dispositivo OPC UA em qual método tem suporte para seu aplicativo. Em geral, o método CSR é recomendado, pois ele não requer que uma chave privada seja transferida por uma transmissão.

- Se o dispositivo precisar emitir um novo par de chaves, siga [Step3a](##step-3a-request-a-new-certificate-with-a-new-keypair).
- Se o dispositivo der suporte para emitir uma solicitação de assinatura de certificado (CSR), siga [Step3b](#step-3b-request-a-new-certificate-with-a-csr).

### <a name="step-3a-request-a-new-certificate-with-a-new-keypair"></a>Etapa 3a: Solicitar um novo certificado com um novo par de chaves

1. Navegue até a `Applications` página.
3. Inicie o fluxo de trabalho de solicitação `New Request` de certificado escolhendo um aplicativo listado.

   ![Solicitar novo certificado](media/howto-opc-vault-secure/request-new-certificate.png "Solicitar novo certificado")

3. Pressione ' solicitar novo par de chaves e certificado ' para solicitar uma chave privada e um novo certificado assinado com a chave pública para seu aplicativo.

   ![Gerar novo par de chaves](media/howto-opc-vault-secure/generate-new-key-pair.png "Gerar novo par de chaves")

4. Preencha o formulário com um assunto, os nomes de domínio e escolha PEM ou PFX com senha para a chave privada. Pressione o `Generate New Certificate` botão para criar a solicitação de certificado.

   ![Aprovar certificado](media/howto-opc-vault-secure/approve-reject.png "Aprovar certificado")

5. A etapa de aprovação requer um usuário com a função ' aprovador ' e com permissões de assinatura no Azure Key Vault. No fluxo de trabalho típico, a função Aprovador e solicitante deve ser atribuída a usuários diferentes.
6. Aprove ou rejeite a solicitação de certificado para iniciar ou cancelar a criação real do par de chaves e a operação de assinatura. O novo par de chaves é criado e armazenado com segurança no Azure Key Vault até ser baixado pelo solicitante do certificado. O certificado resultante com a chave pública é assinado pela autoridade de certificação. Essas operações podem levar alguns segundos para serem concluídas.

   ![Exibir par de chaves](media/howto-opc-vault-secure/view-key-pair.png "Exibir par de chaves")

7. A chave privada resultante (PFX ou PEM) e o certificado (DER) podem ser baixados aqui no formato selecionado como download de arquivo binário. Uma versão codificada em base64 também está disponível, por exemplo, para copiar, Cole o certificado em uma linha de comando ou entrada de texto. 
8. Depois que a chave privada é baixada e armazenada com segurança, ela pode ser excluída do `Delete Private Key` serviço com o botão. O certificado com a chave pública permanece disponível para uso futuro.
9. Devido ao uso de um certificado assinado por uma autoridade de certificação, o certificado de CA e a CRL devem ser baixados aqui também.
10. Agora, ele depende do dispositivo OPC UA como aplicar o novo par de chaves. Normalmente, o certificado de autoridade de certificação e a CRL `trusted` são copiados para uma pasta, enquanto a chave pública e privada do certificado de `own` aplicativo é aplicada a uma pasta no repositório de certificados. Alguns dispositivos já podem oferecer suporte a ' push de servidor ' para atualizações de certificado. Consulte a documentação do seu dispositivo OPC UA.

### <a name="step-3b-request-a-new-certificate-with-a-csr"></a>Etapa 3B: Solicitar um novo certificado com um CSR 

1. Navegue até a `Applications` página.
3. Inicie o fluxo de trabalho de solicitação `New Request` de certificado escolhendo um aplicativo listado.

   ![Solicitar novo certificado](media/howto-opc-vault-secure/request-new-certificate.png "Solicitar novo certificado")

3. Pressione ' solicitar novo certificado com a solicitação de assinatura ' para solicitar um novo certificado assinado para seu aplicativo.

   ![Gerar novo certificado](media/howto-opc-vault-secure/generate-new-certificate.png "Gerar novo certificado")

4. Carregue o CSR selecionando um arquivo local ou colando um CSR codificado em base64 no formulário. Pressione o `Generate New Certificate` botão para criar a solicitação de certificado.

   ![Aprovar CSR](media/howto-opc-vault-secure/approve-reject-csr.png "Aprovar CSR")

5. A etapa de aprovação requer um usuário com a função ' aprovador ' e com permissões de assinatura no Azure Key Vault. Aprove ou rejeite a solicitação de certificado para iniciar ou cancelar a operação de assinatura real. O certificado resultante com a chave pública é assinado pela autoridade de certificação. Esta operação pode levar alguns segundos para ser concluída.

   ![Exibir certificado](media/howto-opc-vault-secure/view-cert-csr.png "Exibir certificado")

6. O certificado resultante (DER) pode ser baixado aqui como arquivo binário. Uma versão codificada em base64 também está disponível, por exemplo, para copiar, Cole o certificado em uma linha de comando ou entrada de texto. 
10. Depois que o certificado for baixado e armazenado com segurança, ele poderá ser excluído do serviço com o `Delete Certificate` botão.
11. Devido ao uso de um certificado assinado por uma autoridade de certificação, o certificado de CA e a CRL devem ser baixados aqui também.
12. Agora, isso depende do dispositivo OPC UA como aplicar o novo certificado. Normalmente, o certificado de autoridade de certificação e a CRL `trusted` são copiados para uma pasta, ao passo que `own` ele é aplicado a uma pasta no repositório de certificados. Alguns dispositivos já podem oferecer suporte a ' push de servidor ' para atualizações de certificado. Consulte a documentação do seu dispositivo OPC UA.

### <a name="step-4-device-secured"></a>Passo 4: Dispositivo protegido

O dispositivo OPC UA agora está pronto para se comunicar com outros dispositivos OPC UA protegidos por certificados assinados por CA sem configuração adicional. Divirta-se!


## <a name="next-steps"></a>Passos Seguintes

Agora que você aprendeu a proteger os dispositivos OPC UA, aqui está a próxima etapa sugerida:

> [!div class="nextstepaction"]
> [Executar um serviço de gerenciamento de certificados seguro](howto-opc-vault-secure-ca.md)