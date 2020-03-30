---
title: Proteja a comunicação de dispositivos OPC UA com OPC Vault - Azure / Microsoft Docs
description: Como registar as aplicações da OPC UA e como emitir certificados de candidatura assinados para os seus dispositivos OPC UA com OPC Vault.
author: mregen
ms.author: mregen
ms.date: 8/16/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: af5e511cbf273bc4e4fa0a08d089a955426fe75c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75454196"
---
# <a name="use-the-opc-vault-certificate-management-service"></a>Utilize o serviço de gestão de certificados OPC Vault

Este artigo explica como registar aplicações e como emitir certificados de candidatura assinados para os seus dispositivos OPC UA.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="deploy-the-certificate-management-service"></a>Implementar o serviço de gestão de certificados

Primeiro, desloque o serviço para a nuvem Azure. Para mais detalhes, consulte Implementar o serviço de [gestão de certificados OPC Vault](howto-opc-vault-deploy.md).

### <a name="create-the-issuer-ca-certificate"></a>Criar o certificado CA emitente

Se ainda não o fez, crie o certificado DE EMitente CA. Para mais detalhes, consulte [Criar e gerir o certificado emitente para o Cofre OPC](howto-opc-vault-manage.md).

## <a name="secure-opc-ua-applications"></a>Aplicações Seguras da OPC UA

### <a name="step-1-register-your-opc-ua-application"></a>Passo 1: Registe a sua aplicação OPC UA 

> [!IMPORTANT]
> O papel de Escritor é obrigado a registar uma candidatura.

1. Abra o seu `https://myResourceGroup-app.azurewebsites.net`serviço de certificados em, e inscreva-se.
2. Ir para **registar novo**. Para um registo de candidatura, um utilizador precisa de ter pelo menos o papel de Escritor atribuído.
2. O formulário de entrada segue as convenções de nomeação na OPC UA. Por exemplo, na seguinte imagem, as definições para a amostra do Servidor de [Referência OPC UA](https://github.com/OPCFoundation/UA-.NETStandard/tree/master/SampleApplications/Workshop/Reference) na pilha padrão OPC UA .NET são mostradas:

   ![Screenshot do registo do servidor de referência ua](media/howto-opc-vault-secure/reference-server-registration.png "Registo do Servidor de Referência ua")

5. Selecione **Registar** para registar a aplicação na base de dados de aplicação de serviço de certificado. O fluxo de trabalho orienta diretamente o utilizador para o passo seguinte para solicitar um certificado assinado para a aplicação.

### <a name="step-2-secure-your-application-with-a-ca-signed-application-certificate"></a>Passo 2: Proteja o seu pedido com um certificado de candidatura assinado pela AC

Proteja o seu pedido de UA OPC através da emissão de um certificado assinado com base num Pedido de Assinatura de Certificado (CSR). Em alternativa, pode solicitar um novo par de chaves, que inclui uma nova chave privada em formato PFX ou PEM. Para obter informações sobre qual o método suportado para a sua aplicação, consulte a documentação do seu dispositivo OPC UA. Em geral, recomenda-se o método CSR, porque não é necessária uma chave privada para ser transferida por um fio.

#### <a name="request-a-new-certificate-with-a-new-keypair"></a>Solicite um novo certificado com um novo par de chaves

1. Ir a **Candidaturas.**
3. Selecione **Novo Pedido** para uma candidatura listada.

   ![Screenshot do pedido novo certificado](media/howto-opc-vault-secure/request-new-certificate.png "Solicitar Novo Certificado")

3. Selecione **Solicitar novos KeyPair e Certificado** para solicitar uma chave privada e um novo certificado assinado com a chave pública para a sua aplicação.

   ![Screenshot de Gerar um novo KeyPair e certificado](media/howto-opc-vault-secure/generate-new-key-pair.png "Gerar novo par de chaves")

4. Preencha o formulário com um sujeito e os nomes de domínio. Para a chave privada, escolha PEM ou PFX com senha. Selecione **Generate New KeyPair** para criar o pedido de certificado.

   ![Screenshot de ver detalhes do pedido de certificado](media/howto-opc-vault-secure/approve-reject.png "Aprovar Certificado")

5. A aprovação requer um utilizador com a função De aprovação, e com permissões de assinatura no Cofre chave Azure. No fluxo de trabalho típico, as funções de Aprovador e Requester devem ser atribuídas a diferentes utilizadores. Selecione **Aprovar** ou **Rejeitar** para iniciar ou cancelar a criação real do par chave e a operação de assinatura. O novo par chave é criado e armazenado de forma segura no Cofre de Chaves Azure, até ser descarregado pelo solicitor de certificado. O certificado resultante com chave pública é assinado pela AC. Estas operações podem levar alguns segundos para terminar.

   ![Screenshot de Ver Dados de Pedido de Certificado, com mensagem de aprovação na parte inferior](media/howto-opc-vault-secure/view-key-pair.png "Ver par de chaves")

7. A chave privada resultante (PFX ou PEM) e o certificado (DER) podem ser descarregados daqui no formato selecionado como download de ficheiros binários. Está também disponível uma versão codificada base64, por exemplo, para copiar e colar o certificado a uma linha de comando ou entrada de texto. 
8. Depois de a chave privada ser descarregada e armazenada de forma segura, pode selecionar **apagar a tecla privada**. O certificado com a chave pública permanece disponível para utilização futura.
9. Devido à utilização de um certificado assinado pela AC, a Lista de Revogação de Certificados ca e certificado (CRL) também deve ser descarregada aqui.

Agora depende do dispositivo OPC UA como aplicar o novo par de chaves. Normalmente, o CA cert e o `trusted` CRL são copiados para uma pasta, `own` enquanto as chaves públicas e privadas do certificado de inscrição são aplicadas a uma pasta na loja de certificados. Alguns dispositivos podem já suportar o impulso do servidor para atualizações de certificados. Consulte a documentação do seu dispositivo OPC UA.

#### <a name="request-a-new-certificate-with-a-csr"></a>Solicite um novo certificado com uma RSE 

1. Ir a **Candidaturas.**
3. Selecione **Novo Pedido** para uma candidatura listada.

   ![Screenshot do pedido novo certificado](media/howto-opc-vault-secure/request-new-certificate.png "Solicitar Novo Certificado")

3. Selecione **Solicitar novo Certificado com Pedido** de Assinatura para solicitar um novo certificado assinado para a sua candidatura.

   ![Screenshot de Gerar um novo Certificado](media/howto-opc-vault-secure/generate-new-certificate.png "Gerar Novo Certificado")

4. Faça upload de CSR selecionando um ficheiro local ou colando um CSR codificado base64 no formulário. **Selecione Gerar novo certificado**.

   ![Screenshot de ver detalhes do pedido de certificado](media/howto-opc-vault-secure/approve-reject-csr.png "Aprovar a RSE")

5. A aprovação requer um utilizador com a função De aprovação, e com permissões de assinatura no Cofre chave Azure. Selecione **Aprovar** ou **Rejeitar** para iniciar ou cancelar a operação de assinatura real. O certificado resultante com chave pública é assinado pela AC. Esta operação pode levar alguns segundos para terminar.

   ![Screenshot de Ver Dados de Pedido de Certificado, com mensagem de aprovação na parte inferior](media/howto-opc-vault-secure/view-cert-csr.png "Ver Certificado")

6. O certificado resultante (DER) pode ser descarregado daqui como ficheiro binário. Está também disponível uma versão codificada base64, por exemplo, para copiar e colar o certificado a uma linha de comando ou entrada de texto. 
10. Depois de o certificado ser descarregado e armazenado de forma segura, pode selecionar **o Certificado de Exclusão**.
11. Devido à utilização de um certificado assinado pela AC, o CA cert e o CRL também devem ser descarregados aqui.

Agora depende do dispositivo OPC UA como aplicar o novo certificado. Normalmente, o CA cert e o `trusted` CRL são copiados para `own` uma pasta, enquanto o certificado de inscrição é aplicado a uma pasta na loja de certificados. Alguns dispositivos podem já suportar o impulso do servidor para atualizações de certificados. Consulte a documentação do seu dispositivo OPC UA.

### <a name="step-3-device-secured"></a>Passo 3: Dispositivo seguro

O dispositivo OPC UA está agora pronto para comunicar com outros dispositivos OPC UA protegidos por certificados assinados pela CA, sem configuração adicional.

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a proteger dispositivos OPC UA, pode:

> [!div class="nextstepaction"]
> [Executar um serviço de gestão de certificados seguro](howto-opc-vault-secure-ca.md)
