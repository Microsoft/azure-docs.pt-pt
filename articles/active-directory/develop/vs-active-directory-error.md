---
title: Diagnosticar erros com o serviço conectado do Azure AD (Visual Studio)
description: O serviço conectado do Active Directory detectou um tipo de autenticação incompatível
author: ghogen
manager: jillfra
ms.assetid: dd89ea63-4e45-4da1-9642-645b9309670a
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.openlocfilehash: a6ec31f0d60c7f6e3737dc4042b05a6d8bf3dd5e
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76699976"
---
# <a name="diagnosing-errors-with-the-azure-active-directory-connected-service"></a>Diagnosticando erros com o serviço conectado do Azure Active Directory

Ao detectar o código de autenticação anterior, o serviço conectado do Azure Active Directory detectou um tipo de autenticação incompatível.

Para detectar corretamente o código de autenticação anterior em um projeto, o projeto deve ser recriado. Se você vir esse erro e não tiver um código de autenticação anterior em seu projeto, recompile e tente novamente.

## <a name="project-types"></a>Tipos de projeto

O serviço conectado verifica o tipo de projeto que você está desenvolvendo para que ele possa injetar a lógica de autenticação correta no projeto. Se houver algum controlador que deriva de `ApiController` no projeto, o projeto é considerado um projeto WebAPI. Se houver apenas controladores que derivam de `MVC.Controller` no projeto, o projeto é considerado um projeto MVC. O serviço conectado não dá suporte a nenhum outro tipo de projeto.

## <a name="compatible-authentication-code"></a>Código de autenticação compatível

O serviço conectado também verifica as configurações de autenticação que foram configuradas anteriormente ou que são compatíveis com o serviço. Se todas as configurações estiverem presentes, será considerado um caso reentrante e o serviço conectado será aberto para exibir as configurações.  Se apenas algumas das configurações estiverem presentes, ele será considerado um caso de erro.

Em um projeto MVC, o serviço conectado verifica qualquer uma das seguintes configurações, que resultam do uso anterior do serviço:

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:AADInstance" value="" />
    <add key="ida:PostLogoutRedirectUri" value="" />

Além disso, o serviço conectado verifica qualquer uma das seguintes configurações em um projeto de API da Web, que resulta do uso anterior do serviço:

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:Audience" value="" />

## <a name="incompatible-authentication-code"></a>Código de autenticação incompatível

Por fim, o serviço conectado tenta detectar versões de código de autenticação que foram configuradas com versões anteriores do Visual Studio. Se você recebeu esse erro, significa que seu projeto contém um tipo de autenticação incompatível. O serviço conectado detecta os seguintes tipos de autenticação de versões anteriores do Visual Studio:

* Autenticação do Windows
* Contas de usuário individuais
* Contas institucionais

Para detetar a Autenticação do Windows num projeto MVC, o dispositivo conectado procura o elemento `authentication` no seu ficheiro `web.config`.

```xml
<configuration>
    <system.web>
        <authentication mode="Windows" />
    </system.web>
</configuration>
```

Para detetar a Autenticação do Windows num projeto Web API, o serviço conectado procura o elemento `IISExpressWindowsAuthentication` no ficheiro `.csproj` do seu projeto:

```xml
<Project>
    <PropertyGroup>
        <IISExpressWindowsAuthentication>enabled</IISExpressWindowsAuthentication>
    </PropertyGroup>
</Project>
```

Para detetar a autenticação das Contas de Utilizador Individuais, o serviço conectado procura o elemento do pacote no seu ficheiro `packages.config`.

```xml
<packages>
    <package id="Microsoft.AspNet.Identity.EntityFramework" version="2.1.0" targetFramework="net45" />
</packages>
```

Para detetar uma antiga forma de autenticação da Conta Organizacional, o serviço conectado procura o seguinte elemento em`web.config`:

```xml
<configuration>
    <appSettings>
        <add key="ida:Realm" value="***" />
    </appSettings>
</configuration>
```

Para alterar o tipo de autenticação, remova o tipo de autenticação incompatível e tente adicionar o serviço conectado novamente.

Para mais informações, consulte Cenários de [Autenticação para AD Azure](authentication-scenarios.md).
