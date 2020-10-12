---
title: Diagnosticar erros com serviço ligado a Azure AD (Visual Studio)
description: O serviço de ligação ao diretório ativo detetou um tipo de autenticação incompatível
author: ghogen
manager: jillfra
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: how-to
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.openlocfilehash: 7b437e3117540719c8c0adc5701ac1a5e934340b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88114477"
---
# <a name="diagnosing-errors-with-the-azure-active-directory-connected-service"></a>Diagnóstico de erros com o Serviço De Ligação ao Diretório Ativo Azure

Ao detetar código de autenticação anterior, o serviço Azure Ative Directory detetou um tipo de autenticação incompatível.

Para detetar corretamente o código de autenticação anterior num projeto, o projeto deve ser reconstruído. Se vir este erro e não tiver um código de autenticação anterior no seu projeto, reconstrua e tente novamente.

## <a name="project-types"></a>Tipos de projetos

O serviço conectado verifica o tipo de projeto que está a desenvolver para que possa injetar a lógica de autenticação certa no projeto. Se houver algum controlador que deriva do `ApiController` projeto, o projeto é considerado um projeto WebAPI. Se houver apenas controladores que derivam `MVC.Controller` do projeto, o projeto é considerado um projeto MVC. O serviço conectado não suporta qualquer outro tipo de projeto.

## <a name="compatible-authentication-code"></a>Código de autenticação compatível

O serviço conectado também verifica as definições de autenticação que tenham sido previamente configuradas ou compatíveis com o serviço. Se todas as definições estiverem presentes, é considerado um caso de reentre, e o serviço ligado abre as definições.  Se apenas algumas das definições estiverem presentes, é considerado um caso de erro.

Num projeto MVC, o serviço conectado verifica qualquer uma das seguintes definições, que resultam da utilização prévia do serviço:

```xml
<add key="ida:ClientId" value="" />
<add key="ida:Tenant" value="" />
<add key="ida:AADInstance" value="" />
<add key="ida:PostLogoutRedirectUri" value="" />
```

Além disso, o serviço conectado verifica qualquer uma das seguintes definições num projeto web API, que resulta da utilização prévia do serviço:

```xml
<add key="ida:ClientId" value="" />
<add key="ida:Tenant" value="" />
<add key="ida:Audience" value="" />
```

## <a name="incompatible-authentication-code"></a>Código de autenticação incompatível

Finalmente, o serviço conectado tenta detetar versões de código de autenticação que foram configuradas com versões anteriores do Visual Studio. Se recebeu este erro, significa que o seu projeto contém um tipo de autenticação incompatível. O serviço conectado deteta os seguintes tipos de autenticação a partir de versões anteriores do Visual Studio:

* Autenticação do Windows
* Contas individuais do utilizador
* Contas Organizacionais

Para detetar a autenticação do Windows num projeto MVC, o connected procura o `authentication` elemento no seu `web.config` ficheiro.

```xml
<configuration>
    <system.web>
        <authentication mode="Windows" />
    </system.web>
</configuration>
```

Para detetar a autenticação do Windows num projeto da Web API, o serviço conectado procura o `IISExpressWindowsAuthentication` elemento no ficheiro do seu `.csproj` projeto:

```xml
<Project>
    <PropertyGroup>
        <IISExpressWindowsAuthentication>enabled</IISExpressWindowsAuthentication>
    </PropertyGroup>
</Project>
```

Para detetar a autenticação de Contas de Utilizador Individuais, o serviço conectado procura o elemento pacote no seu `packages.config` ficheiro.

```xml
<packages>
    <package id="Microsoft.AspNet.Identity.EntityFramework" version="2.1.0" targetFramework="net45" />
</packages>
```

Para detetar uma forma antiga de autenticação da Conta Organizacional, o serviço conectado procura o seguinte elemento em `web.config` :

```xml
<configuration>
    <appSettings>
        <add key="ida:Realm" value="***" />
    </appSettings>
</configuration>
```

Para alterar o tipo de autenticação, retire o tipo de autenticação incompatível e tente voltar a adicionar o serviço ligado.

Para mais informações, consulte [Cenários de Autenticação para Azure AD](./authentication-vs-authorization.md).