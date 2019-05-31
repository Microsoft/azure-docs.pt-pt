---
title: Versão do SDK de cliente e servidor em aplicações móveis e os serviços móveis | Documentos da Microsoft
description: Lista de SDKs do cliente e a compatibilidade com versões do SDK de servidor para serviços móveis e aplicações móveis do Azure
services: app-service\mobile
documentationcenter: ''
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 35b19672-c9d6-49b5-b405-a6dcd1107cd5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: cfa6a363725c35083b32d6de1dd1371777f91907
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66240291"
---
# <a name="client-and-server-versioning-in-mobile-apps-and-mobile-services"></a>Versão do cliente e servidor em aplicações móveis e os serviços móveis
A versão mais recente do serviços móveis do Azure é o **Mobile Apps** recurso do App Service do Azure.

Os SDKs de cliente e servidor de aplicações móveis são originalmente com base nos serviços móveis, mas são *não* compatíveis entre si.
Ou seja, tem de utilizar um *Mobile Apps* SDK de cliente com um *Mobile Apps* SDK do servidor e da mesma forma para *serviços móveis*. Esse contrato é imposto por meio de um valor de cabeçalho especial utilizado pelo cliente e servidor SDKs, `ZUMO-API-VERSION`.

Nota: sempre que este documento refere-se para uma *os serviços móveis* back-end, ele não precisa necessariamente ser hospedados em serviços móveis. Agora, é possível migrar um serviço móvel para ser executada no serviço de aplicações sem alterações de código, mas o serviço ainda estariam usando *os serviços móveis* versões do SDK.

Para saber mais sobre a migração para o serviço de aplicações sem alterações de código, consulte o artigo [migrar um serviço móvel ao serviço de aplicações do Azure].

## <a name="header-specification"></a>Especificação de cabeçalho
A chave `ZUMO-API-VERSION` podem ser especificados o cabeçalho HTTP ou a cadeia de consulta. O valor é uma cadeia de versão no formulário **x.y. z**.

Por exemplo:

GET https://service.azurewebsites.net/tables/TodoItem

CABEÇALHOS: ZUMO-API-VERSION: 2.0.0

POST https://service.azurewebsites.net/tables/TodoItem?ZUMO-API-VERSION=2.0.0

## <a name="opting-out-of-version-checking"></a>Desativar a verificação de versão
Pode optar por versão, a verificação, definindo um valor de **true** para a definição de aplicação **MS_SkipVersionCheck**. Especifique-o na Web. config ou na secção definições da aplicação do portal do Azure.

> [!NOTE]
> Há uma série de alterações de comportamento entre os serviços móveis e aplicações móveis, especialmente nas áreas de sincronização offline, a autenticação e notificações push. Apenas deve desativar a verificação depois de concluir o teste para garantir que estas alterações comportamentais não sofrem funcionalidade da sua aplicação de versão.

## <a name="2.0.0"></a>Cliente de Mobile Apps do Azure e do servidor
### <a name="MobileAppsClients"></a> Mobile *aplicações* SDKs do cliente
A verificar a versão foi introduzida começando com as seguintes versões do SDK de cliente para **Mobile Apps do Azure**:

| Plataforma de cliente | Version | Valor de cabeçalho de versão |
| --- | --- | --- |
| Cliente gerenciado (Windows, Xamarin) |[2.0.0](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/2.0.0) |2.0.0 |
| iOS |[3.0.0](https://go.microsoft.com/fwlink/?LinkID=529823) |2.0.0 |
| Android |[3.0.0](https://go.microsoft.com/fwlink/?LinkID=717033&clcid=0x409) |3.0.0 |

### <a name="mobile-apps-server-sdks"></a>Mobile *aplicações* SDKs do servidor
A verificação de versão está incluída nos seguintes versões do SDK de servidor:

| Plataforma de servidor | SDK | Cabeçalho de versão aceite |
| --- | --- | --- |
| .NET |[Microsoft.Azure.Mobile.Server](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) |2.0.0 |
| Node.js |[azure-mobile-apps)](https://www.npmjs.com/package/azure-mobile-apps) |2.0.0 |

### <a name="behavior-of-mobile-apps-backends"></a>Comportamento de back-ends de aplicações móveis
| ZUMO-API-VERSION | Valor de MS_SkipVersionCheck | Resposta |
| --- | --- | --- |
| x.y. z ou nulo |Verdadeiro |200 - OK |
| Null |FALSE/não especificado |400 - pedido incorreto |
| 1.x.y |FALSE/não especificado |400 - pedido incorreto |
| 2.0.0-2.x.y |FALSE/não especificado |200 - OK |
| 3.0.0-3.x.y |FALSE/não especificado |400 - pedido incorreto |

[Mobile Services clients]: #MobileServicesClients
[Mobile Apps clients]: #MobileAppsClients
[Mobile App Server SDK]: https://www.nuget.org/packages/microsoft.azure.mobile.server
