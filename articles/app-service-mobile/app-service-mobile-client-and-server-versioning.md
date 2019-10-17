---
title: Controle de versão do SDK do cliente e do servidor em aplicativos móveis e serviços móveis | Microsoft Docs
description: Lista de SDKs de cliente e compatibilidade com versões do SDK do servidor para serviços móveis e aplicativos móveis do Azure
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
ms.openlocfilehash: 5be72a4125b276d85174a7a056cbbc2c23053e89
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388891"
---
# <a name="client-and-server-versioning-in-mobile-apps-and-mobile-services"></a>Controle de versão de cliente e servidor em aplicativos móveis e serviços móveis
> [!NOTE]
> O Visual Studio App Center suporta serviços de ponto a ponto e integrados, fundamentais para o desenvolvimento de aplicações móveis. Os programadores podem utilizar os serviços de **Compilação**, **Teste** e **Distribuição** para configurar o pipeline de Integração e Entrega Contínuas. Após a implementação da aplicação, os programadores podem monitorizar o estado e a utilização da aplicação através dos serviços de **Análise** e de **Diagnóstico** e interagir com os utilizadores através do serviço **Push**. Os programadores também podem tirar partido da **Autenticação** para autenticar os utilizadores e do serviço de **Dados** para manter e sincronizar os dados da aplicação na cloud.
>
> Se você estiver procurando integrar os serviços de nuvem em seu aplicativo móvel, Inscreva-se com o [app Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) hoje mesmo.

A versão mais recente dos serviços móveis do Azure é o recurso de **aplicativos móveis** do serviço de Azure app.

Os SDKs do cliente e do servidor dos aplicativos móveis são originalmente baseados neles nos serviços móveis, mas *não* são compatíveis entre si.
Ou seja, você deve usar um SDK de cliente de *aplicativos móveis* com um SDK de servidor de *aplicativos móveis* e, da mesma forma, para os *serviços móveis*. Este contrato é imposto por meio de um valor de cabeçalho especial usado pelos SDKs do cliente e do servidor, `ZUMO-API-VERSION`.

Observação: sempre que este documento se refere a um back-end *dos serviços móveis* , ele não precisa necessariamente ser hospedado nos serviços móveis. Agora é possível migrar um serviço móvel para ser executado no serviço de aplicativo sem nenhuma alteração de código, mas o serviço ainda estaria usando versões do SDK de *serviços móveis* .

## <a name="header-specification"></a>Especificação de cabeçalho
A chave `ZUMO-API-VERSION` pode ser especificada no cabeçalho HTTP ou na cadeia de caracteres de consulta. O valor é uma cadeia de caracteres de versão no formato **x. y. z**.

Por exemplo:

OBTER https://service.azurewebsites.net/tables/TodoItem

CABEÇALHOS: ZUMO-API-VERSION: 2.0.0

POSTAR https://service.azurewebsites.net/tables/TodoItem?ZUMO-API-VERSION=2.0.0

## <a name="opting-out-of-version-checking"></a>Recusando a verificação de versão
Você pode recusar a verificação de versão definindo um valor de **true** para a configuração do aplicativo **MS_SkipVersionCheck**. Especifique isso em seu Web. config ou na seção Configurações do aplicativo do portal do Azure.

> [!NOTE]
> Há várias alterações de comportamento entre os serviços móveis e os aplicativos móveis, especialmente nas áreas de sincronização offline, autenticação e notificações por push. Você só deve recusar a verificação de versão após o teste completo para garantir que essas alterações comportamentais não interrompam a funcionalidade do aplicativo.

## <a name="2.0.0"></a>Cliente e servidor de aplicativos móveis do Azure
### <a name="MobileAppsClients"></a>SDKs de cliente de *aplicativos* móveis
A verificação de versão foi introduzida a partir das seguintes versões do SDK do cliente para **aplicativos móveis do Azure**:

| Plataforma de cliente | Versão | Valor do cabeçalho da versão |
| --- | --- | --- |
| Cliente gerenciado (Windows, Xamarin) |[2.0.0](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/2.0.0) |2.0.0 |
| iOS |[3.0.0](https://go.microsoft.com/fwlink/?LinkID=529823) |2.0.0 |
| Android |[3.0.0](https://go.microsoft.com/fwlink/?LinkID=717033&clcid=0x409) |3.0.0 |

### <a name="mobile-apps-server-sdks"></a>SDKs do servidor de *aplicativos* móveis
A verificação de versão está incluída nas seguintes versões do SDK do servidor:

| Plataforma de servidor | SDK | Cabeçalho de versão aceito |
| --- | --- | --- |
| .NET |[Microsoft. Azure. Mobile. Server](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) |2.0.0 |
| Node.js |[Azure-Mobile-Apps)](https://www.npmjs.com/package/azure-mobile-apps) |2.0.0 |

### <a name="behavior-of-mobile-apps-backends"></a>Comportamento de back-ends de aplicativos móveis
| ZUMO-VERSÃO DE API | Valor de MS_SkipVersionCheck | Resposta |
| --- | --- | --- |
| x. y. z ou NULL |Verdadeiro |200-OK |
| Null |False/não especificado |400 – Pedido Incorreto |
| 1. x. y |False/não especificado |400 – Pedido Incorreto |
| 2.0.0-2. x. y |False/não especificado |200-OK |
| 3.0.0-3. x. y |False/não especificado |400 – Pedido Incorreto |

[Mobile Services clients]: #MobileServicesClients
[Mobile Apps clients]: #MobileAppsClients
[Mobile App Server SDK]: https://www.nuget.org/packages/microsoft.azure.mobile.server
