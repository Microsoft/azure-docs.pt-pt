---
title: Versão SDK do cliente e do servidor
description: Lista de SDKs de clientes e compatibilidade com versões SDK do servidor para Serviços Móveis e Aplicações Móveis Azure.
ms.assetid: 35b19672-c9d6-49b5-b405-a6dcd1107cd5
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.openlocfilehash: f24ae0a48b835785a2e000210f3609b82d42d0f6
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77461560"
---
# <a name="client-and-server-versioning-in-mobile-apps-and-mobile-services"></a>Versão de cliente e servidor em Aplicações Móveis e Serviços Móveis

A versão mais recente do Azure Mobile Services é a funcionalidade **de Aplicações Móveis** do Azure App Service.

Os SDKs de cliente e servidor de Aplicações Móveis são originalmente baseados nos dos Serviços Móveis, mas *não* são compatíveis uns com os outros.
Ou seja, deve utilizar um SDK cliente de *Aplicações Móveis* com um servidor de *aplicações móveis* SDK e similarmente para *Serviços Móveis*. Este contrato é aplicado através de um valor especial de cabeçalho usado pelos SDKs do cliente e servidor, `ZUMO-API-VERSION`.

Nota: sempre que este documento se refere a um backend *dos Serviços Móveis,* não precisa necessariamente de ser hospedado em Serviços Móveis. Agora é possível migrar um serviço móvel para funcionar no Serviço de Aplicações sem alterações de código, mas o serviço ainda estaria a utilizar versões SDK de *Serviços Móveis.*

## <a name="header-specification"></a>Especificação do cabeçalho
A `ZUMO-API-VERSION` chave pode ser especificada no cabeçalho HTTP ou na corda de consulta. O valor é uma cadeia de versão na forma **x.y.z**.

Por exemplo:

OBTER https://service.azurewebsites.net/tables/TodoItem

CABEÇADÓS: ZUMO-API-VERSÃO: 2.0.0

POST https://service.azurewebsites.net/tables/TodoItem?ZUMO-API-VERSION=2.0.0

## <a name="opting-out-of-version-checking"></a>Optando por não verificar versão
Pode optar por não verificar a versão, definindo um valor **verdadeiro** para a definição de aplicações **MS_SkipVersionCheck**. Especifique isto no seu web.config ou na secção Definições de Aplicação do portal Azure.

> [!NOTE]
> Há uma série de mudanças de comportamento entre Serviços Móveis e Aplicações Móveis, particularmente nas áreas de sincronização offline, autenticação e notificações push. Só deve optar por não verificar a versão após testes completos para garantir que estas alterações comportamentais não quebram a funcionalidade da sua aplicação.

## <a name="2.0.0"></a>Cliente e servidor de Aplicativos Móveis Azure
### <a name="MobileAppsClients"></a>SDKs clientes de *aplicativos* móveis
A verificação da versão foi introduzida a partir das seguintes versões do cliente SDK para **Aplicações Móveis Azure:**

| Plataforma de clientes | Versão | Valor do cabeçalho da versão |
| --- | --- | --- |
| Cliente gerido (Windows, Xamarin) |[2.0.0](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/2.0.0) |2.0.0 |
| iOS |[3.0.0](https://go.microsoft.com/fwlink/?LinkID=529823) |2.0.0 |
| Android |[3.0.0](https://go.microsoft.com/fwlink/?LinkID=717033&clcid=0x409) |3.0.0 |

### <a name="mobile-apps-server-sdks"></a>SDKs do servidor de *aplicativos* móveis
A verificação da versão está incluída nas seguintes versões SDK do servidor:

| Plataforma do servidor | SDK | Cabeçalho de versão aceite |
| --- | --- | --- |
| .NET |[Microsoft.Azure.Mobile.Server](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) |2.0.0 |
| Node.js |[azure-mobile-apps)](https://www.npmjs.com/package/azure-mobile-apps) |2.0.0 |

### <a name="behavior-of-mobile-apps-backends"></a>Comportamento das aplicações móveis backends
| ZUMO-API-VERSÃO | Valor da MS_SkipVersionCheck | Resposta |
| --- | --- | --- |
| x.y.z ou Nulo |Verdadeiro |200 - OK |
| Null |Falso/não especificado |400 – Pedido Incorreto |
| 1.x.y |Falso/não especificado |400 – Pedido Incorreto |
| 2.0.0-2.x.y |Falso/não especificado |200 - OK |
| 3.0.0-3.x.y |Falso/não especificado |400 – Pedido Incorreto |

[Mobile Services clients]: #MobileServicesClients
[Mobile Apps clients]: #MobileAppsClients
[Mobile App Server SDK]: https://www.nuget.org/packages/microsoft.azure.mobile.server
