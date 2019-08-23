---
title: Referência do SDK do iOS do leitor de imersão
titleSuffix: Azure Cognitive Services
description: Referência para o SDK do iOS do leitor de imersão
services: cognitive-services
author: metanMSFT
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 08/01/2019
ms.author: metan
ms.openlocfilehash: 615c09dd8a7287918bb009ce11854278b21554c1
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69899419"
---
# <a name="immersive-reader-sdk-reference"></a>Referência do SDK do leitor de imersão

O SDK do iOS do leitor de imersão é um CocoaPod Swift que permite que você integre o leitor de imersão em seu aplicativo iOS.

## <a name="functions"></a>Functions

O SDK expõe uma única função, `launchImmersiveReader(navController, token, subdomain, content, options, onSuccess, onFailure)`.

### <a name="launchimmersivereader"></a>launchImmersiveReader

Inicia o leitor de imersão iniciando um controlador de exibição em seu aplicativo iOS.

```swift
public func launchImmersiveReader(navController: UINavigationController, token: String, subdomain: String, content: Content, options: Options?, onSuccess: @escaping () -> Void, onFailure: @escaping (_ error: Error) -> Void)
```

#### <a name="parameters"></a>Parâmetros

| Name | Tipo | Descrição |
| ---- | ---- |------------ |
| `navController` | UINavigationController | O controlador de navegação para o aplicativo iOS do qual a função está sendo chamada. |
| `token` | Cadeia | O token de autenticação do Azure AD. Consulte o [How-to de autenticação do Azure ad](./azure-active-directory-authentication.md). |
| `subdomain` | Cadeia | O subdomínio personalizado do seu recurso de leitor de imersão no Azure. Consulte o [How-to de autenticação do Azure ad](./azure-active-directory-authentication.md). |
| `content` | [Conteúdo](#content) | Um objeto que contém o conteúdo a ser mostrado no leitor de imersão. |
| `options` | [Opções](#options) | Opções para configurar determinados comportamentos do leitor de imersão. Opcional. |
| `onSuccess` | ()-> void | Um fechamento que é invocado quando o leitor de imersão é iniciado com êxito. |
| `onFailure` | (_ erro: [Erro](#error)) -> void | Um fechamento que é invocado quando o leitor de imersão não é carregado. Esse fechamento retorna um [`Error`](#error) objeto que representa um código de erro e uma mensagem de erro associada à falha. Para obter mais informações, consulte os [códigos de erro](#error-codes). |

## <a name="types"></a>Tipos

### <a name="content"></a>Conteúdo

Contém o conteúdo a ser mostrado no leitor de imersão.

```swift
struct Content: Encodable {
    var title: String
    var chunks: [Chunk]
}
```

#### <a name="supported-mime-types"></a>Tipos MIME com suporte

| Tipo MIME | Descrição |
| --------- | ----------- |
| texto/sem formatação | Texto sem formatação. |
| aplicativo/MathML + XML | MathML (matematica Markup Language). [Saiba mais](https://developer.mozilla.org/en-US/docs/Web/MathML).

### <a name="options"></a>Opções

Contém propriedades que configuram determinados comportamentos do leitor de imersão.

```swift
struct Options {
    var uiLang: String?
    var timeout: TimeInterval?
}
```

### <a name="error"></a>Erro

Contém informações sobre o erro.

```swift
struct Error {
    var code: String
    var message: String
}
```

#### <a name="error-codes"></a>Códigos de erro

| Código | Descrição |
| ---- | ----------- |
| BadArgument | O argumento fornecido é inválido, `message` consulte para obter detalhes. |
| Tempo limite | Falha ao carregar o leitor de imersão no tempo limite especificado. |
| TokenExpired | O token fornecido expirou. |
| Limitado | O limite de taxa de chamada foi excedido. |
| InternalError | Ocorreu um erro interno no controlador de exibição do leitor de imersão. Consulte `message` para obter detalhes.|

## <a name="os-version-support"></a>Suporte à versão do so

O SDK do iOS do leitor de imersão tem suporte para iOS 9,0 ou superior, no iPad e no iPhone.

## <a name="next-steps"></a>Passos Seguintes

* Explore o [SDK do IOS do leitor de imersão no GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS)
* [Quickstart: Criar um aplicativo iOS que inicie o leitor de imersão (Swift)](./ios-quickstart.md)