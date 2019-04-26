---
title: Serviço de ligado como diagnosticar erros com o Azure Active Directory
description: O serviço ligado do Active Directory detetou um tipo de autenticação incompatível
services: active-directory
author: ghogen
manager: douge
ms.assetid: dd89ea63-4e45-4da1-9642-645b9309670a
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.collection: M365-identity-device-management
ms.openlocfilehash: a6f151251d76965cf1bc86216eac15a08f1adbc6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60296797"
---
# <a name="diagnosing-errors-with-the-azure-active-directory-connected-service"></a>Diagnosticar erros com o Active Directory serviço ligado do Azure

Ao detetar o código de autenticação anterior, o Azure Active Director ligar servidor detetou um tipo de autenticação incompatível.

Para detetar corretamente o código de autenticação anterior num projeto, deve ser criado o projeto.  Se vir este erro e não tiver um código de autenticação anterior no seu projeto, reconstruir e tente novamente.

## <a name="project-types"></a>Tipos de projeto

O serviço ligado verifica o tipo de projeto que esteja a desenvolver para que ele pode injetar a lógica de autenticação direito no projeto. Se houver qualquer controlador que deriva de `ApiController` no projeto, o projeto é considerado um projeto WebAPI. Se existirem apenas os controladores que derivam de `MVC.Controller` no projeto, o projeto é considerado um projeto MVC. O serviço ligado não suporta qualquer tipo de projeto.

## <a name="compatible-authentication-code"></a>Código de autenticação compatíveis

O serviço ligado também verifica a existência de definições de autenticação que foram anteriormente configuradas ou que são compatíveis com o serviço. Se todas as definições estiverem presentes, é considerada um caso reentrante e abre o serviço ligado visualize as definições.  Se apenas algumas das definições estiverem presentes, é considerada um caso de erro.

Num projeto MVC, o serviço ligado verifica a existência de qualquer uma das seguintes definições, que resultam da utilização anterior do serviço:

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:AADInstance" value="" />
    <add key="ida:PostLogoutRedirectUri" value="" />

Além disso, o serviço ligado verifica a existência de qualquer uma das seguintes definições num projeto Web API, que resultam da utilização anterior do serviço:

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:Audience" value="" />

## <a name="incompatible-authentication-code"></a>Código de autenticação incompatível

Por fim, o serviço ligado tenta detetar versões do código de autenticação que foram configuradas com as versões anteriores do Visual Studio. Se tiver recebido este erro, significa que seu projeto contém um tipo de autenticação incompatível. O serviço ligado Deteta os seguintes tipos de autenticação a partir de versões anteriores do Visual Studio:

* Autenticação do Windows
* Contas de utilizador individuais
* Contas institucionais

Para detetar a autenticação do Windows num projeto MVC, o ligados procura a `authentication` elemento no seu `web.config` ficheiro.

```xml
<configuration>
    <system.web>
        <authentication mode="Windows" />
    </system.web>
</configuration>
```

Para detetar a autenticação do Windows num projeto Web API, o serviço ligado procura a `IISExpressWindowsAuthentication` elemento no seu projeto `.csproj` ficheiro:

```xml
<Project>
    <PropertyGroup>
        <IISExpressWindowsAuthentication>enabled</IISExpressWindowsAuthentication>
    </PropertyGroup>
</Project>
```

Para detetar a autenticação de contas de utilizador individuais, o serviço ligado procura o elemento de pacote no seu `packages.config` ficheiro.

```xml
<packages>
    <package id="Microsoft.AspNet.Identity.EntityFramework" version="2.1.0" targetFramework="net45" />
</packages>
```

Para detectar uma forma antiga de autenticação de conta da organização, o serviço ligado procura o seguinte elemento no`web.config`:

```xml
<configuration>
    <appSettings>
        <add key="ida:Realm" value="***" />
    </appSettings>
</configuration>
```

Para alterar o tipo de autenticação, remova o tipo de autenticação incompatíveis e tente adicionar o serviço ligado novamente.

Para obter mais informações, consulte [cenários de autenticação do Azure AD](authentication-scenarios.md).
