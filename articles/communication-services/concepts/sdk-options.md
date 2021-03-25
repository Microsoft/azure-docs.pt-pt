---
title: Bibliotecas de clientes e APIs REST para Serviços de Comunicação Azure
titleSuffix: An Azure Communication Services concept document
description: Saiba mais sobre os SDKs dos Serviços de Comunicação Azure e as APIs REST.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: effd7658bbfe7359e1f99f9452857824c2c45c2f
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2021
ms.locfileid: "105107895"
---
# <a name="client-libraries-and-rest-apis"></a>Bibliotecas de cliente e APIs REST

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]


As capacidades dos Serviços de Comunicação Azure estão conceptualmente organizadas em seis áreas. Algumas áreas têm SDKs de origem totalmente aberta. O Call SDK utiliza interfaces de rede proprietárias e é atualmente de origem fechada, e a biblioteca Chat inclui uma dependência de fonte fechada. As amostras e detalhes técnicos adicionais para os SDKs são publicados no [repo dos Serviços de Comunicação Azure GitHub](https://github.com/Azure/communication).

## <a name="client-libraries"></a>Bibliotecas de cliente

| Assemblagem               | Protocolos             |Aberto vs. Fonte Fechada| Espaços de nomes                          | Capacidades                                                      |
| ---------------------- | --------------------- | ---|-------------------------- | --------------------------------------------------------------------------- |
| Azure Resource Manager | REST | Abrir            | Azure.ResourceManager.Communication | Fornecimento e gestão dos recursos dos Serviços de Comunicação             |
| Common                 | REST | Abrir               | Azure.Communication.Common          | Fornece tipos de base para outros SDKs |
| Identidade         | REST | Abrir               | Azure.Communication.Identity  | Gerir utilizadores, aceder a fichas |
| Números de telefone         | REST | Abrir               | Azure.Communication.PhoneNumbers  | Gestão de números de telefone |
| Chat                   | DESCANSE COM sinalização proprietária | Aberto com pacote de sinalização de fonte fechada    | Azure.Communication.Chat            | Adicione o chat baseado em texto em tempo real às suas aplicações  |
| SMS                    | REST | Abrir              | Azure.Communication.SMS             | Enviar e receber mensagens SMS |
| Chamando                | Transporte proprietário | Fechado |Azure.Communication.Calling         | Alavancar a voz, o vídeo, a partilha de ecrãs e outras capacidades de comunicação de dados em tempo real          |

Note que os SDKs, Identidade e SMS da Azure, estão focados na integração de serviços e, em muitos casos, surgem problemas de segurança se integrar estas funções em aplicações de utilizador final. Os SDKs Common e Chat são adequados para aplicações de serviço e cliente. O Call SDK foi concebido para aplicações de clientes. Está em desenvolvimento um SDK focado em cenários de serviço.

### <a name="languages-and-publishing-locations"></a>Idiomas e locais de publicação

As localizações de publicação de pacotes SDK individuais são detalhadas abaixo.

| Área           | JavaScript | .NET | Python | Java SE | iOS | Android | Outro                          |
| -------------- | ---------- | ---- | ------ | ---- | -------------- | -------------- | ------------------------------ |
| Azure Resource Manager | -         | [NuGet](https://www.nuget.org/packages/Azure.ResourceManager.Communication)    |   [PyPi](https://pypi.org/project/azure-mgmt-communication/)    |  -  | -              | -  | [Vá via GitHub](https://github.com/Azure/azure-sdk-for-go/releases/tag/v46.3.0) |
| Common         | [npm](https://www.npmjs.com/package/@azure/communication-common)         | [NuGet](https://www.nuget.org/packages/Azure.Communication.Common/)    | N/D      | [Maven](https://search.maven.org/search?q=a:azure-communication-common)   | [GitHub](https://github.com/Azure/azure-sdk-for-ios/releases)            | [Maven](https://search.maven.org/artifact/com.azure.android/azure-communication-common)             | -                              |
| Identidade | [npm](https://www.npmjs.com/package/@azure/communication-identity)         | [NuGet](https://www.nuget.org/packages/Azure.Communication.Identity)    | [PyPi](https://pypi.org/project/azure-communication-identity/)      | [Maven](https://search.maven.org/search?q=a:azure-communication-identity)   | -              | -              | -                            |
| Números de telefone | [npm](https://www.npmjs.com/package/@azure/communication-phone-numbers)         | [NuGet](https://www.nuget.org/packages/Azure.Communication.PhoneNumbers)    | [PyPi](https://pypi.org/project/azure-communication-phonenumbers/)      | [Maven](https://search.maven.org/search?q=a:azure-communication-phonenumbers)   | -              | -              | -                            |
| Chat           | [npm](https://www.npmjs.com/package/@azure/communication-chat)        | [NuGet](https://www.nuget.org/packages/Azure.Communication.Chat)     | [PyPi](https://pypi.org/project/azure-communication-chat/)     | [Maven](https://search.maven.org/search?q=a:azure-communication-chat)   | [GitHub](https://github.com/Azure/azure-sdk-for-ios/releases)  | [Maven](https://search.maven.org/search?q=a:azure-communication-chat)   | -                              |
| SMS            | [npm](https://www.npmjs.com/package/@azure/communication-sms)         | [NuGet](https://www.nuget.org/packages/Azure.Communication.Sms)    | [PyPi](https://pypi.org/project/azure-communication-sms/)       | [Maven](https://search.maven.org/artifact/com.azure/azure-communication-sms)   | -              | -              | -                              |
| Chamando        | [npm](https://www.npmjs.com/package/@azure/communication-calling)         | -      | -      | -     | [GitHub](https://github.com/Azure/Communication/releases)     | [Maven](https://search.maven.org/artifact/com.azure.android/azure-communication-calling/)            | -                              |
| Documentação de Referência     | [docs](https://azure.github.io/azure-sdk-for-js/communication.html)         | [docs](https://azure.github.io/azure-sdk-for-net/communication.html)      | -      | [docs](http://azure.github.io/azure-sdk-for-java/communication.html)     | [docs](/objectivec/communication-services/calling/)      | [docs](/java/api/com.azure.communication.calling)            | -                              |

## <a name="rest-apis"></a>APIs REST

As APIs dos Serviços de Comunicação são documentadas juntamente com outras APIs Azure REST em [docs.microsoft.com](/rest/api/azure/). Esta documentação irá dizer-lhe como estruturar as suas mensagens HTTP e oferece orientações para a utilização do Carteiro. Esta documentação também é oferecida em formato Swagger no [GitHub.](https://github.com/Azure/azure-rest-api-specs)

## <a name="additional-support-details"></a>Detalhes adicionais de suporte

### <a name="ios-and-android-support-details"></a>Detalhes de suporte para iOS e Android

- Serviços de Comunicação iOS SDKs visam a versão 13+, e Xcode 11+.
- Android Java SDKs alvo Android API nível 21+ e Android Studio 4.0+

### <a name="net-support-details"></a>Detalhes de suporte .NET

Com exceção da Chamada, os pacotes de serviços de comunicação visam .NET Standard 2.0 que suporta as plataformas listadas abaixo.

Apoio via Quadro .NET 4.6.1
- Windows 10, 8.1, 8 e 7
- Windows Server 2012 R2, 2012 e 2008 R2 SP1

Suporte via .NET Core 2.0:
- Windows 10 (1607+), 7 SP1+, 8.1
- Windows Server 2008 R2 SP1+
- Max OS X 10.12+
- Versões/distribuições linux
- UWP 10.0.16299 (RS3) setembro 2017
- Unidade 2018.1
- Mono 5.4
- Xamarin iOS 10.14
- Xamarin Mac 3.8

## <a name="calling-sdk-timeouts"></a>Chamando os intervalos da SDK

Aplicam-se os seguintes intervalos de tempo aos Serviços de Comunicação que chamam os SDKs:

| Ação           | Tempo limite em segundos |
| -------------- | ---------- |
| Religação/participante da remoção | 120 |
| Adicione ou remova nova modalidade de uma chamada (Iniciar/parar de vídeo ou partilha de ecrã) | 40 |
| Tempo limite de operação de transferência de chamadas | 60 |
| 1:1 chamada de estabelecimento tempo limite | 85 |
| Tempo limite de estabelecimento de chamada de grupo | 85 |
| PSTN chamada tempo limite de estabelecimento | 115 |
| Promover 1:1 chamada para um tempo limite de chamada em grupo | 115 |


## <a name="api-stability-expectations"></a>Expectativas de estabilidade da API

> [!IMPORTANT]
> Esta secção fornece orientações sobre APIs de REST e SDKs marcados **estáveis**. APIs marcados antes de ser pré-lançamento, pré-visualização ou beta podem ser alterados ou depreciados **sem aviso prévio**.

No futuro, poderemos retirar versões dos SDKs dos Serviços de Comunicação, e podemos introduzir alterações de rutura nas nossas APIs de REST e lançamento de SDKs. Os Serviços de Comunicação da Azure seguirão *geralmente* duas políticas de apoio às versões de serviço de aposentação:

- Será notificado pelo menos três anos antes de ser obrigado a alterar o código devido a uma alteração da interface dos Serviços de Comunicação. Todas as APIs de REST documentadas e APIs SDK geralmente gozam de pelo menos três anos de aviso antes de as interfaces serem desativadas.
- Será notificado pelo menos um ano antes de ter de atualizar os conjuntos SDK para a versão menor mais recente. Estas atualizações necessárias não devem exigir alterações de código porque estão na mesma versão principal. Isto é especialmente verdade para as bibliotecas Call and Chat que têm componentes em tempo real que frequentemente requerem atualizações de segurança e desempenho. Encorajamo-lo a manter os seus SDKs de Serviços de Comunicação atualizados.

### <a name="api-and-sdk-decommissioning-examples"></a>Exemplos de desmantelamento da API e da SDK

**Integrou a versão v24 da API SMS REST na sua aplicação. A Azure Communication lança v25.**

Receberá 3 anos de aviso antes que estas APIs parem de funcionar e sejam obrigadas a atualizar para v25. Esta atualização pode requerer uma alteração de código.

**Integrou a versão v2.02 do Call SDK na sua aplicação. A Azure Communication lança v2.05.**

Poderá ser necessário atualizar para a versão v2.05 do Call SDK no prazo de 12 meses após o lançamento do v2.05. Esta deve ser uma simples substituição do artefacto sem exigir uma alteração de código porque o v2.05 está na versão principal v2 e não tem alterações de rutura.

## <a name="next-steps"></a>Passos seguintes

Para mais informações, consulte as seguintes sínteses da SDK:

- [Chamando visão geral do SDK](../concepts/voice-video-calling/calling-sdk-features.md)
- [Visão geral do Chat SDK](../concepts/chat/sdk-features.md)
- [Visão geral do SMS SDK](../concepts/telephony-sms/sdk-features.md)

Para começar com os Serviços de Comunicação Azure:

- [Criar Recursos de Comunicação Azure](../quickstarts/create-communication-resource.md)
- Gerar [fichas de acesso ao utilizador](../quickstarts/access-tokens.md)
