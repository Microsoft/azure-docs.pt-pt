---
title: Escrever o código de autenticação da aplicação
titleSuffix: Azure Digital Twins
description: Ver como escrever código de autenticação numa aplicação ao cliente
author: baanders
ms.author: baanders
ms.date: 10/7/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 1b7a846ee92da001ea2ac3ddd02efa9a870f72c6
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102501911"
---
# <a name="write-client-app-authentication-code"></a>Escreva código de autenticação de aplicativos de cliente

Depois de [configurar uma instância e autenticação Azure Digital Twins,](how-to-set-up-instance-portal.md)pode criar uma aplicação ao cliente que utilizará para interagir com o caso. Uma vez configurado um projeto de cliente inicial, terá de **escrever código nessa aplicação do cliente para** o autenticar contra a instância Azure Digital Twins.

A Azure Digital Twins realiza a autenticação utilizando [tokens de segurança Azure AD com base em AAUTH 2.0](../active-directory/develop/security-tokens.md#json-web-tokens-and-claims). Para autenticar o seu SDK, terá de obter um token ao portador com as permissões certas para a Azure Digital Twins e passá-lo juntamente com as suas chamadas API. 

Este artigo descreve como obter credenciais usando a biblioteca do `Azure.Identity` cliente. Enquanto este artigo mostra exemplos de código em C#, como o que escreveria para o [.NET (C#) SDK,](/dotnet/api/overview/azure/digitaltwins/client)pode utilizar uma versão `Azure.Identity` de independentemente do que está a usar (para mais informações sobre os SDKs disponíveis para as Gémeas Digitais Azure, ver [*Como:: Use as APIs e SDKs de Gémeos Digitais Azure).*](how-to-use-apis-sdks.md)

## <a name="prerequisites"></a>Pré-requisitos

Em primeiro lugar, complete os passos de configuração em [*Como-a: Configurar uma instância e autenticação*](how-to-set-up-instance-portal.md). Isto irá garantir que possui uma instância Azure Digital Twins e que o seu utilizador tem permissões de acesso. Depois dessa configuração, está pronto para escrever o código de aplicação do cliente.

Para prosseguir, você precisará de um projeto de aplicação de cliente no qual você escreve o seu código. Se ainda não tiver um projeto de aplicação de clientes configurado, crie um projeto básico no seu idioma de eleição para usar com este tutorial.

## <a name="common-authentication-methods-with-azureidentity"></a>Métodos comuns de autenticação com Azure.Identidade

`Azure.Identity` é uma biblioteca de clientes que fornece vários métodos de obtenção de credenciais que você pode usar para obter um token portador e autenticar com o seu SDK. Embora este artigo dê exemplos em C#, você pode ver `Azure.Identity` para várias línguas, incluindo...

* [.NET (C#)](/dotnet/api/azure.identity)
* [Java](/java/api/overview/azure/identity-readme)
* [JavaScript](/javascript/api/overview/azure/identity-readme)
* [Python](/python/api/overview/azure/identity-readme)

Três métodos comuns de obtenção de credenciais `Azure.Identity` são:

* [O DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) fornece um fluxo de autenticação predefinido `TokenCredential` para aplicações que serão implantadas no Azure, e é **a escolha recomendada para o desenvolvimento local.** Pode também ser habilitado a experimentar os outros dois métodos recomendados neste artigo; envolve `ManagedIdentityCredential` e pode aceder com uma `InteractiveBrowserCredential` variável de configuração.
* [ManagedIdentityCredential](/dotnet/api/azure.identity.managedidentitycredential) funciona muito bem nos casos em que precisa de [identidades geridas (MSI)](../active-directory/managed-identities-azure-resources/overview.md)e é um bom candidato para trabalhar com as Funções Azure e implantar para os serviços Azure.
* [InteractiveBrowserCredential destina-se](/dotnet/api/azure.identity.interactivebrowsercredential) a aplicações interativas, e pode ser usado para criar um cliente SDK autenticado

O exemplo a seguir mostra como utilizar cada um destes com o .NET (C#) SDK.

## <a name="authentication-examples-net-c-sdk"></a>Exemplos de autenticação: .NET (C#) SDK

Esta secção mostra um exemplo em C# para utilizar o .NET SDK fornecido para escrever código de autenticação.

Em primeiro lugar, inclua o pacote SDK `Azure.DigitalTwins.Core` e o pacote no seu `Azure.Identity` projeto. Dependendo das suas ferramentas de eleição, pode incluir os pacotes utilizando o gestor de pacotes Visual Studio ou a `dotnet` ferramenta da linha de comando. 

Também terá de adicionar as seguintes declarações ao seu código de projeto:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/authentication.cs" id="Azure_Digital_Twins_dependencies":::

Em seguida, adicione código para obter credenciais usando um dos métodos em `Azure.Identity` .

### <a name="defaultazurecredential-method"></a>Método DefaultAzureCredential

[O DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) fornece um fluxo de autenticação predefinido `TokenCredential` para aplicações que serão implantadas no Azure, e é **a escolha recomendada para o desenvolvimento local.**

Para utilizar as credenciais Azure predefinidas, você precisará do URL da instância Azure Digital Twins[(instruções para encontrar).](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)

Aqui está uma amostra de código para adicionar um `DefaultAzureCredential` ao seu projeto:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/authentication.cs" id="DefaultAzureCredential_full":::

#### <a name="set-up-local-azure-credentials"></a>Configurar credenciais locais do Azure

[!INCLUDE [Azure Digital Twins: local credentials prereq (inner)](../../includes/digital-twins-local-credentials-inner.md)]

### <a name="managedidentitycredential-method"></a>Método ManageIdentityCredential

O método [ManagedIdentityCredential](/dotnet/api/azure.identity.managedidentitycredential) funciona muito bem nos casos em que precisa de [identidades geridas (MSI)](../active-directory/managed-identities-azure-resources/overview.md)— por exemplo, ao trabalhar com as Funções Azure.

Isto significa que pode utilizar `ManagedIdentityCredential` no mesmo projeto que `DefaultAzureCredential` `InteractiveBrowserCredential` ou, para autenticar uma parte diferente do projeto.

Para utilizar as credenciais Azure predefinidas, você precisará do URL da instância Azure Digital Twins[(instruções para encontrar).](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)

Numa função Azure, pode utilizar as credenciais de identidade geridas como esta:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/authentication.cs" id="ManagedIdentityCredential":::

### <a name="interactivebrowsercredential-method"></a>Método InteractiveBrowserCredential

O método [InteractiveBrowserCredential destina-se](/dotnet/api/azure.identity.interactivebrowsercredential) a aplicações interativas e irá criar um navegador web para autenticação. Pode usá-lo em vez de `DefaultAzureCredential` nos casos em que necessita de autenticação interativa.

Para utilizar as credenciais de navegador interativas, você precisará de um **registo de aplicação** que tenha permissões para as APIs de Gémeos Digitais Azure. Para etapas sobre como configurar este registo da aplicação, consulte [*Como fazer: Criar um registo de aplicações.*](how-to-create-app-registration.md) Assim que o registo da aplicação estiver configurado, você precisará...
* ID de *Inscrição da* aplicação (cliente)[(instruções para encontrar)](how-to-create-app-registration.md#collect-client-id-and-tenant-id)
* ID do Diretório *(inquilino)* do registo da aplicação [(instruções para encontrar)](how-to-create-app-registration.md#collect-client-id-and-tenant-id)
* URL da instância Azure Digital Twins[(instruções para encontrar)](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)

Aqui está um exemplo do código para criar um cliente SDK autenticado usando `InteractiveBrowserCredential` .

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/authentication.cs" id="InteractiveBrowserCredential":::

>[!NOTE]
> Embora possa colocar o ID do cliente, o ID do inquilino e o URL de instância diretamente no código, como mostrado acima, é uma boa ideia que o seu código obtenha estes valores a partir de um ficheiro de configuração ou variável ambiental.

#### <a name="other-notes-about-authenticating-azure-functions"></a>Outras notas sobre a autenticação de Funções Azure

Ver [*Como fazer: Configurar uma função Azure para o processamento de dados*](how-to-create-azure-function.md) para um exemplo mais completo que explica algumas das escolhas importantes de configuração no contexto das funções.

Além disso, para utilizar a autenticação em função, lembre-se de:
* [Ativar a identidade gerida](../app-service/overview-managed-identity.md?tabs=dotnet)
* Utilize [variáveis ambientais](/sandbox/functions-recipes/environment-variables?tabs=csharp) conforme apropriado
* Atribua permissões à aplicação de funções que lhe permitem aceder às APIs das Gémeas Digitais. Para obter mais informações sobre os processos de Funções Azure, consulte [*Como fazer: Configurar uma função Azure para o processamento de dados*](how-to-create-azure-function.md).

## <a name="other-credential-methods"></a>Outros métodos de credencial

Se os cenários de autenticação acima referidos não cobrirem as necessidades da sua aplicação, poderá explorar outros tipos de autenticação oferecidas na plataforma de identidade da [**Microsoft.**](../active-directory/develop/v2-overview.md#getting-started) A documentação desta plataforma abrange cenários de autenticação adicional, organizados por tipo de aplicação.

## <a name="next-steps"></a>Passos seguintes

Leia mais sobre como funciona a segurança em Azure Digital Twins:
* [*Conceitos: Segurança para soluções Azure Digital Twins*](concepts-security.md)

Ou, agora que a autenticação está configurada, passe para a criação e gestão de modelos no seu caso:
* [*Como fazer: Gerir os modelos DTDL*](how-to-manage-model.md)