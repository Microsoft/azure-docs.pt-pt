---
title: Diagnosticar erros com Azure Active Directory serviço conectado
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 26f25daa01288959c38520f9713d35eb975d2df2
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73941387"
---
# <a name="diagnosing-errors-with-the-azure-active-directory-connected-service"></a>Diagnosticando erros com o serviço conectado do Azure Active Directory

Ao detectar o código de autenticação anterior, o servidor de conexão do Azure active Director detectou um tipo de autenticação incompatível.

Para detectar corretamente o código de autenticação anterior em um projeto, o projeto deve ser compilado.  Se você vir esse erro e não tiver um código de autenticação anterior em seu projeto, recompile e tente novamente.

## <a name="project-types"></a>Tipos de projeto

O serviço conectado verifica o tipo de projeto que você está desenvolvendo para que ele possa injetar a lógica de autenticação correta no projeto. Se houver qualquer controlador que derive de `ApiController` no projeto, o projeto será considerado um projeto WebAPI. Se houver apenas controladores que derivam de `MVC.Controller` no projeto, o projeto será considerado um projeto do MVC. O serviço conectado não dá suporte a nenhum outro tipo de projeto.

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
* Contas organizacionais

Para detectar a autenticação do Windows em um projeto MVC, a conexão procura o elemento `authentication` no arquivo `web.config`.

```xml
<configuration>
    <system.web>
        <authentication mode="Windows" />
    </system.web>
</configuration>
```

Para detectar a autenticação do Windows em um projeto de API da Web, o serviço conectado procura o elemento `IISExpressWindowsAuthentication` no arquivo de `.csproj` do seu projeto:

```xml
<Project>
    <PropertyGroup>
        <IISExpressWindowsAuthentication>enabled</IISExpressWindowsAuthentication>
    </PropertyGroup>
</Project>
```

Para detectar a autenticação de contas de usuário individuais, o serviço conectado procura o elemento de pacote em seu arquivo de `packages.config`.

```xml
<packages>
    <package id="Microsoft.AspNet.Identity.EntityFramework" version="2.1.0" targetFramework="net45" />
</packages>
```

Para detectar uma forma antiga de autenticação de conta organizacional, o serviço conectado procura o seguinte elemento no`web.config`:

```xml
<configuration>
    <appSettings>
        <add key="ida:Realm" value="***" />
    </appSettings>
</configuration>
```

Para alterar o tipo de autenticação, remova o tipo de autenticação incompatível e tente adicionar o serviço conectado novamente.

Para obter mais informações, consulte [cenários de autenticação do Azure ad](authentication-scenarios.md).
