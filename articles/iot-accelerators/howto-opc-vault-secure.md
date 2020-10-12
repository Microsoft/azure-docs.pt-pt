---
title: Garantir a comunicação de dispositivos OPC UA com OPC Vault - Azure / Microsoft Docs
description: Como registar aplicações OPC UA e como emitir certificados de inscrição assinados para os seus dispositivos OPC UA com OPC Vault.
author: mregen
ms.author: mregen
ms.date: 8/16/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: e95edf21b7d6dce29b31220533269439fac120e4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91281965"
---
# <a name="use-the-opc-vault-certificate-management-service"></a>Utilize o serviço de gestão de certificados OPC Vault

> [!IMPORTANT]
> Enquanto atualizamos este artigo, consulte [a Azure Industrial IoT](https://azure.github.io/Industrial-IoT/) para obter o conteúdo mais atualizado.

Este artigo explica como registar as aplicações e como emitir certificados de inscrição assinados para os seus dispositivos OPC UA.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="deploy-the-certificate-management-service"></a>Implementar o serviço de gestão de certificados

Primeiro, implante o serviço na nuvem Azure. Para mais informações, consulte [o serviço de gestão de certificados OPC Vault](howto-opc-vault-deploy.md).

### <a name="create-the-issuer-ca-certificate"></a>Criar o certificado emitente CA

Se ainda não o fez, crie o certificado emitente ca. Para mais detalhes, consulte [Criar e gerir o certificado emitente para o Cofre OPC](howto-opc-vault-manage.md).

## <a name="secure-opc-ua-applications"></a>Aplicações Secure OPC UA

### <a name="step-1-register-your-opc-ua-application"></a>Passo 1: Registe a sua aplicação OPC UA 

> [!IMPORTANT]
> O papel de Escritor é necessário para registar uma candidatura.

1. Abra o seu serviço de certificados `https://myResourceGroup-app.azurewebsites.net` em, e inscreva-se.
2. Vá para **registar novo.** Para um registo de inscrição, um utilizador precisa de ter pelo menos a função De Escritor atribuída.
2. O formulário de inscrição segue as convenções de nomeação na UA da OPC. Por exemplo, na seguinte imagem, são mostradas as definições para a amostra do [Servidor de Referência OPC UA](https://github.com/OPCFoundation/UA-.NETStandard/tree/master/Applications/ReferenceServer) na pilha OPC UA .NET Standard:

   ![Screenshot do registo do servidor de referência da UA](media/howto-opc-vault-secure/reference-server-registration.png "Registo do servidor de referência da UA")

5. Selecione **Registar-se** para registar o pedido na base de dados de pedidos de certificado. O fluxo de trabalho orienta diretamente o utilizador para o passo seguinte para solicitar um certificado assinado para o pedido.

### <a name="step-2-secure-your-application-with-a-ca-signed-application-certificate"></a>Passo 2: Proteja o seu pedido com um certificado de inscrição assinado pela AC

Proteja o seu pedido de UA OPC através da emissão de um certificado assinado com base num Pedido de Assinatura de Certificado (CSR). Em alternativa, pode solicitar um novo par de chaves, que inclui uma nova chave privada em formato PFX ou PEM. Para obter informações sobre qual o método suportado para a sua aplicação, consulte a documentação do seu dispositivo OPC UA. Em geral, o método de RSE é recomendado, porque não requer uma chave privada para ser transferido por um fio.

#### <a name="request-a-new-certificate-with-a-new-keypair"></a>Solicite um novo certificado com um novo keypair

1. Ir para **aplicações**.
3. Selecione **Novo Pedido** para uma aplicação listada.

   ![Screenshot do novo certificado de pedido](media/howto-opc-vault-secure/request-new-certificate.png "Pedido novo certificado")

3. Selecione **Solicite novo KeyPair e Certificado** para solicitar uma chave privada e um novo certificado assinado com a chave pública para a sua candidatura.

   ![Screenshot de Gerar um Novo KeyPair e Certificado](media/howto-opc-vault-secure/generate-new-key-pair.png "Gerar novo par de chaves")

4. Preencha o formulário com um assunto e os nomes de domínio. Para a chave privada, escolha PEM ou PFX com senha. **Selecione Gerar Novo KeyPair** para criar o pedido de certificado.

   ![Screenshot de Ver Dados de Pedido de Certificado](media/howto-opc-vault-secure/approve-reject.png "Certificado de aprovação")

5. A aprovação requer um utilizador com a função Approver, e com permissões de assinatura no Cofre da Chave Azure. No fluxo de trabalho típico, as funções de Approver e Requester devem ser atribuídas a diferentes utilizadores. Selecione **Aprovar** ou **Rejeitar** iniciar ou cancelar a criação real do par de chaves e da operação de assinatura. O novo par de chaves é criado e armazenado de forma segura no Cofre da Chave Azure, até ser descarregado pelo solicitador de certificados. O certificado resultante com chave pública é assinado pela AC. Estas operações podem levar alguns segundos para terminar.

   ![Screenshot de Visualização Dados de Pedido de Certificado, com mensagem de aprovação na parte inferior](media/howto-opc-vault-secure/view-key-pair.png "Ver Par de Chaves")

7. A chave privada resultante (PFX ou PEM) e certificado (DER) podem ser descarregados a partir daqui no formato selecionado como descarregamento de ficheiro binário. Uma versão codificada base64 também está disponível, por exemplo, para copiar e colar o certificado a uma linha de comando ou entrada de texto. 
8. Depois de a tecla privada ser descarregada e armazenada de forma segura, pode selecionar **a Chave Privada Delete**. O certificado com a chave pública permanece disponível para utilização futura.
9. Devido à utilização de um certificado assinado pela AC, a Lista de Revogação de Certificados e Certificados (CRL) também deve ser descarregada aqui.

Agora depende do dispositivo OPC UA como aplicar o novo par de chaves. Normalmente, o certificado ca e o CRL são copiados para uma `trusted` pasta, enquanto as chaves públicas e privadas do certificado de pedido são aplicadas a uma `own` pasta na loja de certificados. Alguns dispositivos podem já suportar o impulso do servidor para atualizações de certificados. Consulte a documentação do seu dispositivo OPC UA.

#### <a name="request-a-new-certificate-with-a-csr"></a>Solicito um novo certificado com uma RSE 

1. Ir para **aplicações**.
3. Selecione **Novo Pedido** para uma aplicação listada.

   ![Screenshot do novo certificado de pedido](media/howto-opc-vault-secure/request-new-certificate.png "Pedido novo certificado")

3. Selecione **Solicite novo Certificado com Pedido de Assinatura** para solicitar um novo certificado assinado para o seu pedido.

   ![Screenshot de Gerar um novo Certificado](media/howto-opc-vault-secure/generate-new-certificate.png "Gerar novo certificado")

4. Carrefique a CSR selecionando um ficheiro local ou colando uma RSE codificada de base64 no formulário. **Selecione Gerar Novo Certificado**.

   ![Screenshot de Ver Dados de Pedido de Certificado](media/howto-opc-vault-secure/approve-reject-csr.png "Aprovar CSR")

5. A aprovação requer um utilizador com a função Approver, e com permissões de assinatura no Cofre da Chave Azure. Selecione **Aprovar** ou **Rejeitar** para iniciar ou cancelar a operação de assinatura real. O certificado resultante com chave pública é assinado pela AC. Esta operação pode levar alguns segundos para terminar.

   ![Screenshot de Visualização Dados de Pedido de Certificado, com mensagem de aprovação na parte inferior](media/howto-opc-vault-secure/view-cert-csr.png "Ver Certificado")

6. O certificado resultante (DER) pode ser descarregado a partir daqui como ficheiro binário. Uma versão codificada base64 também está disponível, por exemplo, para copiar e colar o certificado a uma linha de comando ou entrada de texto. 
10. Depois de o certificado ser descarregado e armazenado de forma segura, pode selecionar **o Certificado de Eliminação**.
11. Devido à utilização de um certificado assinado pela AC, o certificado de CA e o CRL também devem ser descarregados aqui.

Agora depende do dispositivo OPC UA como aplicar o novo certificado. Normalmente, o certificado ca e o CRL são copiados para uma `trusted` pasta, enquanto o certificado de inscrição é aplicado a uma `own` pasta na loja de certificados. Alguns dispositivos podem já suportar o impulso do servidor para atualizações de certificados. Consulte a documentação do seu dispositivo OPC UA.

### <a name="step-3-device-secured"></a>Passo 3: Dispositivo seguro

O dispositivo OPC UA está agora pronto para comunicar com outros dispositivos OPC UA protegidos por certificados assinados pela AC, sem configuração adicional.

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a proteger dispositivos OPC UA, pode:

> [!div class="nextstepaction"]
> [Executar um serviço de gestão de certificados seguro](howto-opc-vault-secure-ca.md)
