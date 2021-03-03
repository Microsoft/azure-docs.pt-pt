---
title: Criar um Azure Ative Directory gerido aplicação de identidade a partir do Azure CLI
titleSuffix: An Azure Communication Services quickstart
description: Identidades geridas permitem autorizar o acesso dos Serviços de Comunicação Azure a partir de aplicações em execução em VMs Azure, aplicações de funções e outros recursos.
services: azure-communication-services
author: jbeauregardb
ms.service: azure-communication-services
ms.topic: how-to
ms.date: 02/25/2021
ms.author: jbeauregardb
ms.reviewer: mikben
ms.openlocfilehash: f60c7b0b4c89833d489a435fde85896a8f8bd72f
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101663698"
---
# <a name="authorize-access-with-managed-identity-to-your-communication-resource-in-your-development-environment"></a>Autorizar o acesso com identidade gerida ao seu recurso de comunicação no seu ambiente de desenvolvimento

A biblioteca de clientes Azure Identity fornece suporte de autenticação simbólica Azure Ative (Azure AD) para o Azure SDK. As versões mais recentes das bibliotecas clientes dos Serviços de Comunicação Azure para .NET, Java, Python e JavaScript integram-se na biblioteca de Identidade Azure para fornecer um meio simples e seguro para adquirir um token OAuth 2.0 para autorização dos pedidos dos Serviços de Comunicação Azure.

Uma vantagem da biblioteca de clientes da Identidade Azure é que permite utilizar o mesmo código para autenticar em vários serviços, quer a sua aplicação esteja a decorrer no ambiente de desenvolvimento ou no Azure. A biblioteca de clientes da Identidade Azure autentica um diretor de segurança. Quando o seu código está em execução em Azure, o diretor de segurança é uma identidade gerida para os recursos da Azure. No ambiente de desenvolvimento, a identidade gerida não existe, pelo que a biblioteca do cliente autentica o utilizador ou uma aplicação registada para efeitos de teste.

## <a name="prerequisites"></a>Pré-requisitos

 - CLI do Azure. [Guia de instalação](https://docs.microsoft.com/cli/azure/install-azure-cli)
 - Uma conta Azure com uma subscrição ativa. [Criar uma conta gratuita](https://azure.microsoft.com/free)

## <a name="setting-up"></a>Configuração

As identidades geridas devem ser ativadas sobre os recursos do Azure que está a autorizar. Para aprender como permitir identidades geridas para recursos Azure, consulte um destes artigos:

- [Portal do Azure](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [CLI do Azure](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Modelo Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Bibliotecas de clientes Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)
- [Serviços de aplicações](../../app-service/overview-managed-identity.md)

## <a name="authenticate-a-registered-application-in-the-development-environment"></a>Autenticar uma aplicação registada no ambiente de desenvolvimento

Se o seu ambiente de desenvolvimento não suporta um único login ou login através de um navegador web, então pode usar uma aplicação registada para autenticar a partir do ambiente de desenvolvimento.

### <a name="creating-an-azure-active-directory-registered-application"></a>Criação de um Diretório Ativo Azure

Para criar uma aplicação registada a partir do CLI Azure, tem de ser acedido à conta Azure onde pretende que as operações ocorram. Para isso, pode utilizar o `az login` comando e inserir as suas credenciais no browser. Assim que iniciar sessão na sua conta Azure a partir do CLI, podemos ligar para o `az ad sp create-for-rbac` comando para criar a aplicação registada.

Os exemplos a seguir usam o Azure CLI para criar uma nova aplicação registada

```azurecli
az ad sp create-for-rbac --name <application-name> 
```

O `az ad sp create-for-rbac` comando devolverá uma lista de propriedades principais de serviço no formato JSON. Copie estes valores para que possa usá-los para criar as variáveis ambientais necessárias no passo seguinte.

```json
{
    "appId": "generated-app-ID",
    "displayName": "service-principal-name",
    "name": "http://service-principal-uri",
    "password": "generated-password",
    "tenant": "tenant-ID"
}
```
> [!IMPORTANT]
> As atribuições de funções azure podem demorar alguns minutos a propagar-se.

#### <a name="set-environment-variables"></a>Definir variáveis de ambiente

A biblioteca cliente da Identidade Azure lê valores de três variáveis ambientais em tempo de execução para autenticar a aplicação. A tabela seguinte descreve o valor a definir para cada variável ambiente.

|Variável de ambiente|Valor
|-|-
|`AZURE_CLIENT_ID`|`appId` valor do JSON gerado 
|`AZURE_TENANT_ID`|`tenant` valor do JSON gerado
|`AZURE_CLIENT_SECRET`|`password` valor do JSON gerado

> [!IMPORTANT]
> Depois de definir as variáveis ambientais, feche e reabriu a janela da consola. Se estiver a utilizar o Visual Studio ou outro ambiente de desenvolvimento, poderá ter de reiniciá-lo para que registe as novas variáveis ambientais.


## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre a autenticação](../concepts/authentication.md)

Também pode querer:

- [Saiba mais sobre a Biblioteca de Identidade Azure](/dotnet/api/overview/azure/identity-readme)
