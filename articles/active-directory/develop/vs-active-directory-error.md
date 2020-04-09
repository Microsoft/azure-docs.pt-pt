---
title: Diagnosticar erros com o serviço ligado à AD Azure (Estúdio Visual)
description: O serviço de ligação ao diretório ativo detetou um tipo de autenticação incompatível
author: ghogen
manager: jillfra
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.openlocfilehash: 4b39aa77ea3895a606ad34a3bc9b70dba924a23f
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886097"
---
# <a name="diagnosing-errors-with-the-azure-active-directory-connected-service"></a>Diagnóstico de erros com o Serviço Conectado ao Diretório Ativo Azure

Ao detetar o código de autenticação anterior, o serviço ligado ao Diretório Ativo Azure detetou um tipo de autenticação incompatível.

Para detetar corretamente o código de autenticação anterior num projeto, o projeto deve ser reconstruído. Se vir este erro e não tiver um código de autenticação anterior no seu projeto, reconstrua e tente novamente.

## <a name="project-types"></a>Tipos de projeto

O serviço conectado verifica o tipo de projeto que está a desenvolver para que possa injetar a lógica de autenticação certa no projeto. Se houver algum controlador que `ApiController` deriva no projeto, o projeto é considerado um projeto WebAPI. Se houver apenas controladores `MVC.Controller` que derivam do projeto, o projeto é considerado um projeto MVC. O serviço conectado não suporta qualquer outro tipo de projeto.

## <a name="compatible-authentication-code"></a>Código de autenticação compatível

O serviço conectado também verifica as definições de autenticação que foram previamente configuradas ou compatíveis com o serviço. Se todas as definições estiverem presentes, é considerado um caso de reentrada, e o serviço conectado abre as definições.  Se apenas algumas das definições estiverem presentes, é considerado um caso de erro.

Num projeto MVC, o serviço conectado verifica qualquer uma das seguintes definições, que resultam da utilização prévia do serviço:

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:AADInstance" value="" />
    <add key="ida:PostLogoutRedirectUri" value="" />

Além disso, o serviço conectado verifica qualquer uma das seguintes definições num projeto Web API, que resulta da utilização prévia do serviço:

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:Audience" value="" />

## <a name="incompatible-authentication-code"></a>Código de autenticação incompatível

Por fim, o serviço conectado tenta detetar versões do código de autenticação que foram configuradas com versões anteriores do Visual Studio. Se recebeu este erro, significa que o seu projeto contém um tipo de autenticação incompatível. O serviço conectado deteta os seguintes tipos de autenticação a partir de versões anteriores do Estúdio Visual:

* Autenticação do Windows
* Contas individuais do utilizador
* Contas Organizacionais

Para detetar a Autenticação do Windows num projeto `authentication` MVC, o dispositivo conectado procura o elemento no seu `web.config` ficheiro.

```xml
<configuration>
    <system.web>
        <authentication mode="Windows" />
    </system.web>
</configuration>
```

Para detetar a Autenticação do Windows num projeto Web `IISExpressWindowsAuthentication` API, o `.csproj` serviço conectado procura o elemento no ficheiro do seu projeto:

```xml
<Project>
    <PropertyGroup>
        <IISExpressWindowsAuthentication>enabled</IISExpressWindowsAuthentication>
    </PropertyGroup>
</Project>
```

Para detetar a autenticação das Contas de Utilizador Individuais, o serviço conectado procura o elemento do pacote no seu `packages.config` ficheiro.

```xml
<packages>
    <package id="Microsoft.AspNet.Identity.EntityFramework" version="2.1.0" targetFramework="net45" />
</packages>
```

Para detetar uma forma antiga de autenticação da Conta Organizacional,`web.config`o serviço conectado procura o seguinte elemento em:

```xml
<configuration>
    <appSettings>
        <add key="ida:Realm" value="***" />
    </appSettings>
</configuration>
```

Para alterar o tipo de autenticação, retire o tipo de autenticação incompatível e tente adicionar novamente o serviço ligado.

Para mais informações, consulte Cenários de [Autenticação para AD Azure](authentication-scenarios.md).
