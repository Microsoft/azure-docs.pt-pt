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
ms.openlocfilehash: 56c5e0582afe55dcd63aa056817898d3d4942419
ms.sourcegitcommit: a8948ddcbaaa22bccbb6f187b20720eba7a17edc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56594071"
---
# <a name="client-and-server-versioning-in-mobile-apps-and-mobile-services"></a>Versão do cliente e servidor em aplicações móveis e os serviços móveis
A versão mais recente do serviços móveis do Azure é o **Mobile Apps** recurso do App Service do Azure.

Os SDKs de cliente e servidor de aplicações móveis são originalmente com base nos serviços móveis, mas são *não* compatíveis entre si.
Ou seja, tem de utilizar um *Mobile Apps* SDK de cliente com um *Mobile Apps* SDK do servidor e da mesma forma para *serviços móveis*. Esse contrato é imposto por meio de um valor de cabeçalho especial utilizado pelo cliente e servidor SDKs, `ZUMO-API-VERSION`.

Nota: sempre que este documento refere-se para uma *os serviços móveis* back-end, ele não precisa necessariamente ser hospedados em serviços móveis. Agora, é possível migrar um serviço móvel para ser executada no serviço de aplicações sem alterações de código, mas o serviço ainda estariam usando *os serviços móveis* versões do SDK.

Para saber mais sobre a migração para o serviço de aplicações sem alterações de código, consulte o artigo [migrar um serviço móvel para o serviço de aplicações do Azure].

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
>
>

## <a name="summary-of-compatibility-for-all-versions"></a>Resumo de compatibilidade para todas as versões
O gráfico abaixo mostra a compatibilidade entre todos os tipos de cliente e servidor. Um back-end é classificado como qualquer um dos Mobile **serviços** ou Mobile **aplicações** com base no servidor do SDK que utiliza.

|  | **Serviços móveis** node. js ou .NET | **Aplicações móveis** node. js ou .NET |
| --- | --- | --- |
| [Clientes de serviços móveis] |Ok |Erro\* |
| [Clientes de aplicações móveis] |Erro\* |Ok |

\*Isto pode ser controlado através da especificação **MS_SkipVersionCheck**.

<!-- IMPORTANT!  The anchors for Mobile Services and Mobile Apps MUST be 1.0.0 and 2.0.0 respectively, since there is an exception error message that uses those anchors. -->

<!-- NOTE: the fwlink to this document is https://go.microsoft.com/fwlink/?LinkID=690568 -->

## <a name="1.0.0"></a>Cliente de serviços móveis e o servidor
Os SDKs do cliente na tabela abaixo são compatíveis com o **os serviços móveis**.

Nota: os serviços móveis SDKs do cliente *isso não é possível* enviar um valor de cabeçalho `ZUMO-API-VERSION`. Se o serviço recebe este cabeçalho ou o valor de cadeia de caracteres de consulta, será devolvido um erro, a menos que explicitamente optou por conforme descrito acima.

### <a name="MobileServicesClients"></a> Mobile *serviços* SDKs do cliente
| Plataforma de cliente | Versão | Valor de cabeçalho de versão |
| --- | --- | --- |
| Cliente gerenciado (Windows, Xamarin) |[1.3.2](https://www.nuget.org/packages/WindowsAzure.MobileServices/1.3.2) |n/d |
| iOS |[2.2.2](https://aka.ms/gc6fex) |n/d |
| Android |[2.0.3](https://go.microsoft.com/fwLink/?LinkID=280126) |n/d |
| HTML |[1.2.7](https://ajax.aspnetcdn.com/ajax/mobileservices/MobileServices.Web-1.2.7.min.js) |n/d |

### <a name="mobile-services-server-sdks"></a>Mobile *serviços* SDKs do servidor
| Plataforma de servidor | Versão | Cabeçalho de versão aceite |
| --- | --- | --- |
| .NET |[WindowsAzure.MobileServices.Backend.* Version 1.0.x](https://www.nuget.org/packages/WindowsAzure.MobileServices.Backend/) |**Nenhum cabeçalho de versão** |
| Node.js |(brevemente) |**Nenhum cabeçalho de versão** |

<!-- TODO: add Node npm version -->

### <a name="behavior-of-mobile-services-backends"></a>Comportamento de back-ends de Mobile Services
| ZUMO-API-VERSION | Valor de MS_SkipVersionCheck | Resposta |
| --- | --- | --- |
| Não especificado |Qualquer |200 - OK |
| Qualquer valor |Verdadeiro |200 - OK |
| Qualquer valor |FALSE/não especificado |400 - pedido incorreto |

## <a name="2.0.0"></a>Cliente de Mobile Apps do Azure e do servidor
### <a name="MobileAppsClients"></a> Mobile *aplicações* SDKs do cliente
A verificar a versão foi introduzida começando com as seguintes versões do SDK de cliente para **Mobile Apps do Azure**:

| Plataforma de cliente | Versão | Valor de cabeçalho de versão |
| --- | --- | --- |
| Cliente gerenciado (Windows, Xamarin) |[2.0.0](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/2.0.0) |2.0.0 |
| iOS |[3.0.0](https://go.microsoft.com/fwlink/?LinkID=529823) |2.0.0 |
| Android |[3.0.0](https://go.microsoft.com/fwlink/?LinkID=717033&clcid=0x409) |3.0.0 |

<!-- TODO: add HTML version when released -->

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

## <a name="next-steps"></a>Próximos Passos
* [Migrar um serviço móvel para o serviço de aplicações do Azure]

[Clientes de serviços móveis]: #MobileServicesClients
[Clientes de aplicações móveis]: #MobileAppsClients


[Mobile App Server SDK]: https://www.nuget.org/packages/microsoft.azure.mobile.server
[Migrar um serviço móvel para o serviço de aplicações do Azure]: app-service-mobile-migrating-from-mobile-services.md
