---
title: Utilizar identidades geridas nos Serviços de Comunicação (.NET)
titleSuffix: An Azure Communication Services quickstart
description: Identidades geridas permitem autorizar o acesso dos Serviços de Comunicação Azure a partir de aplicações em execução em VMs Azure, aplicações de funções e outros recursos.
services: azure-communication-services
author: stefang931
ms.service: azure-communication-services
ms.topic: how-to
ms.date: 12/04/2020
ms.author: gistefan
ms.reviewer: mikben
ms.openlocfilehash: ee691d4809a68a0ba60f60a2240b76a1e53104bc
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102171578"
---
# <a name="use-managed-identities-net"></a>Utilizar identidades geridas (.NET)

Inicie-se com os Serviços de Comunicação Azure utilizando identidades geridas em .NET. A Administração de Serviços de Comunicação e as bibliotecas de clientes SMS suportam a autenticação do Azure Ative Directory (Azure AD) com [identidades geridas para recursos Azure.](../../active-directory/managed-identities-azure-resources/overview.md)

Este quickstart mostra-lhe como autorizar o acesso às bibliotecas de clientes da Administração e SMS a partir de um ambiente Azure que suporta identidades geridas. Também descreve como testar o seu código num ambiente de desenvolvimento.

## <a name="prerequisites"></a>Pré-requisitos

 - Uma conta Azure com uma subscrição ativa. [Criar uma conta gratuita](https://azure.microsoft.com/free)
 - Um recurso ativo dos Serviços de Comunicação e cadeia de ligação. [Criar um recurso de Serviços de Comunicação.](./create-communication-resource.md?pivots=platform-azp&tabs=windows)

## <a name="setting-up"></a>Configuração

### <a name="enable-managed-identities-on-a-virtual-machine-or-app-service"></a>Ativar identidades geridas numa máquina virtual ou serviço de Aplicação

As identidades geridas devem ser ativadas sobre os recursos do Azure que está a autorizar. Para aprender como permitir identidades geridas para recursos Azure, consulte um destes artigos:

- [Portal do Azure](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [CLI do Azure](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Modelo Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Bibliotecas de clientes Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)
- [Serviços de aplicações](../../app-service/overview-managed-identity.md)

#### <a name="assign-azure-roles-with-the-azure-portal"></a>Atribuir funções a Azure com o portal Azure

1. Navegue para o portal do Azure.
1. Navegue para o recurso do Serviço de Comunicação Azure.
1. Navegue para o menu Controlo de Acesso (IAM) -> + Adicionar -> Adicionar função.
1. Selecione a função "Contribuinte" (esta é a única função suportada).
1. Selecione "Identidade gerida atribuída pelo utilizador" (ou um "Sistema atribuído identidade gerida") e, em seguida, selecione a identidade desejada. Salve a sua seleção.

![Papel de identidade gerido](media/managed-identity-assign-role.png)

#### <a name="assign-azure-roles-with-powershell"></a>Atribuir funções a Azure com PowerShell

Para atribuir funções e permissões utilizando o PowerShell, consulte [adicionar ou remover atribuições de funções Azure utilizando a Azure PowerShell](../../../articles/role-based-access-control/role-assignments-powershell.md)

## <a name="add-managed-identity-to-your-communication-services-solution"></a>Adicione identidade gerida à sua solução de Serviços de Comunicação

### <a name="install-the-client-library-packages"></a>Instale os pacotes da biblioteca do cliente

```console
dotnet add package Azure.Communication.Identity
dotnet add package Azure.Communication.Configuration
dotnet add package Azure.Communication.Sms
dotnet add package Azure.Identity
```

### <a name="use-the-client-library-packages"></a>Use os pacotes da biblioteca do cliente

Adicione as `using` seguintes diretivas ao seu código para utilizar as bibliotecas de clientes Azure Identity e Azure Storage.

```csharp
using Azure.Identity;
using Azure.Communication.Identity;
using Azure.Communication.Configuration;
using Azure.Communication.Sms;
```

Os exemplos abaixo estão a utilizar o [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential). Esta credencial é adequada para ambientes de produção e desenvolvimento.

### <a name="create-an-identity-and-issue-a-token"></a>Criar uma identidade e emitir um símbolo

O exemplo de código que se segue mostra como criar um objeto de cliente de serviço com fichas do Azure Ative Directory e, em seguida, usar o cliente para emitir um símbolo para um novo utilizador:

```csharp
     public async Task<Response<CommunicationUserToken>> CreateIdentityAndIssueTokenAsync(Uri resourceEndpoint) 
     {
          TokenCredential credential = new DefaultAzureCredential();
     
          var client = new CommunicationIdentityClient(resourceEndpoint, credential);
          var identityResponse = await client.CreateUserAsync();
     
          var tokenResponse = await client.IssueTokenAsync(identity, scopes: new [] { CommunicationTokenScope.VoIP });

          return tokenResponse;
     }
```

### <a name="send-an-sms-with-azure-active-directory-tokens"></a>Envie um SMS com fichas de diretório Azure Ative

O exemplo de código que se segue mostra como criar um objeto de cliente de serviço com fichas de Diretório Azure Ative e, em seguida, usar o cliente para enviar uma mensagem SMS:

```csharp

     public async Task SendSmsAsync(Uri resourceEndpoint, PhoneNumber from, PhoneNumber to, string message)
     {
          TokenCredential credential = new DefaultAzureCredential();
     
          SmsClient smsClient = new SmsClient(resourceEndpoint, credential);
          smsClient.Send(
               from: from,
               to: to,
               message: message,
               new SendSmsOptions { EnableDeliveryReport = true } // optional
          );
     }
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre a autenticação](../concepts/authentication.md)

Também pode querer:

- [Saiba mais sobre o controlo de acesso baseado em funções da Azure](../../../articles/role-based-access-control/index.yml)
- [Saiba mais sobre a biblioteca de identidades Azure para .NET](/dotnet/api/overview/azure/identity-readme)
- [Criação de fichas de acesso ao utilizador](../quickstarts/access-tokens.md)
- [Enviar uma mensagem SMS](../quickstarts/telephony-sms/send.md)
- [Saiba mais sobre SMS](../concepts/telephony-sms/concepts.md)