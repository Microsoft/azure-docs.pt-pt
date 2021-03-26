---
title: SDKs e REST APIs para Serviços de Comunicação Azure
titleSuffix: An Azure Communication Services concept document
description: Saiba mais sobre os SDKs dos Serviços de Comunicação Azure e as APIs REST.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/25/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: b5115355133bdcf33825a05d4baa16408cb3fccd
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105562448"
---
# <a name="sdks-and-rest-apis"></a>SDKs e REST APIs

As capacidades dos Serviços de Comunicação Azure estão conceptualmente organizadas em seis áreas. A maioria das áreas tem bibliotecas de clientes de origem totalmente abertas programadas contra APIs de REST publicadas que você pode usar diretamente através da Internet. A biblioteca de clientes Call utiliza interfaces de rede proprietárias e é atualmente de origem fechada. As amostras e mais detalhes técnicos para os SDKs são publicados no [Azure Communication Services GitHub repo](https://github.com/Azure/communication).

## <a name="rest-apis"></a>APIs REST
As APIs dos Serviços de Comunicação são documentadas juntamente com outras APIs Azure REST em [docs.microsoft.com](/rest/api/azure/). Esta documentação irá dizer-lhe como estruturar as suas mensagens HTTP e oferece orientações para a utilização do Carteiro. Esta documentação também é oferecida em formato Swagger no [GitHub.](https://github.com/Azure/azure-rest-api-specs)


## <a name="sdks"></a>SDKs

| Assemblagem | Espaços de nomes| Protocolos | Capacidades |
|------------------------|-------------------------------------|---------------------------------|--------------------------------------------------------------------------------------------|
| Azure Resource Manager | Azure.ResourceManager.Communication | [REST](https://docs.microsoft.com/rest/api/communication/communicationservice)| Fornecimento e gestão dos recursos dos Serviços de Comunicação|
| Common | Azure.Communication.Common| REST | Fornece tipos de base para outras bibliotecas de clientes |
| Identidade | Azure.Communication.Identity| [REST](https://docs.microsoft.com/rest/api/communication/communicationidentity)| Gerir utilizadores, aceder a fichas|
| Números de telefone _(beta)_| Azure.Communication.PhoneNumbers| [REST](https://docs.microsoft.com/rest/api/communication/phonenumberadministration)| Adquirir e gerir números de telefone |
| Chat | Azure.Communication.Chat| [DESCANSE](https://docs.microsoft.com/rest/api/communication/) COM sinalização proprietária | Adicione o chat baseado em texto em tempo real às suas aplicações |
| SMS| Azure.Communication.SMS | [REST](https://docs.microsoft.com/rest/api/communication/sms)| Enviar e receber mensagens SMS|
| Chamando| Azure.Communication.Calling | Transporte proprietário | Utilize capacidades de comunicação de voz, vídeo, partilha de ecrãs e outras capacidades de comunicação de dados em tempo real |

As bibliotecas de clientes Azure Resource Manager, Identity e SMS estão focadas na integração de serviços e, em muitos casos, surgem problemas de segurança se integrar estas funções em aplicações de utilizador final. As bibliotecas de clientes Common e Chat são adequadas para aplicações de serviço e cliente. A biblioteca de clientes Call foi concebida para aplicações de clientes. Uma biblioteca de clientes focada em cenários de serviço está em desenvolvimento.


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


## <a name="rest-api-throttles"></a>REST API Throttles
Certas APIs de REST e métodos SDK correspondentes têm limites de aceleração que deve estar atento. Ultrapassar estes limites de aceleração irá desencadear uma  `429 - Too Many Requests` resposta de erro. Estes limites podem ser aumentados através [de um pedido de Apoio Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).

| API                                                                                                                          | Limitação            |
|------------------------------------------------------------------------------------------------------------------------------|---------------------|
| [Todas as APIs do Plano de Números de Telefone de Pesquisa](https://docs.microsoft.com/rest/api/communication/phonenumberadministration)         | 4 pedidos/dia      |
| [Plano de Número de Telefone de Compra](https://docs.microsoft.com/rest/api/communication/phonenumberadministration/purchasesearch) | 1 pedido/dia       |
| [Enviar SMS](https://docs.microsoft.com/rest/api/communication/sms/send)                                                       | 200 pedidos/minuto |


## <a name="sdk-platform-support-details"></a>Detalhes de suporte da plataforma SDK

### <a name="ios-and-android"></a>iOS e Android 

- Serviços de Comunicação iOS SDKs visam a versão 13+, e Xcode 11+.
- Android Java SDKs alvo Android API nível 21+ e Android Studio 4.0+

### <a name="net"></a>.NET 

Com exceção dos pacotes de Serviços de Comunicação de Chamada e Comunicação visam .NET Standard 2.0, que suporta as plataformas listadas abaixo.

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

## <a name="api-stability-expectations"></a>Expectativas de estabilidade da API

> [!IMPORTANT]
> Esta secção fornece orientações sobre APIs de REST e SDKs marcados **estáveis**. APIs marcados antes de ser pré-lançamento, pré-visualização ou beta podem ser alterados ou depreciados **sem aviso prévio**.

No futuro, poderemos retirar versões dos SDKs dos Serviços de Comunicação, e podemos introduzir alterações de rutura nas nossas APIs de REST e lançamento de SDKs. Os Serviços de Comunicação da Azure seguirão *geralmente* duas políticas de apoio às versões de serviço de aposentação:

- Será notificado pelo menos três anos antes de ser obrigado a alterar o código devido a uma alteração da interface dos Serviços de Comunicação. Todas as APIs de REST documentadas e APIs SDK geralmente gozam de pelo menos três anos de aviso antes de as interfaces serem desativadas.
- Será notificado pelo menos um ano antes de ter de atualizar os conjuntos SDK para a versão menor mais recente. Estas atualizações necessárias não devem exigir alterações de código porque estão na mesma versão principal. Isto é especialmente verdade para as bibliotecas Call and Chat que têm componentes em tempo real que frequentemente requerem atualizações de segurança e desempenho. Encorajamo-lo a manter os seus SDKs de Serviços de Comunicação atualizados.

### <a name="api-and-sdk-decommissioning-examples"></a>Exemplos de desmantelamento da API e da SDK

**Integrou a versão v24 da API SMS REST na sua aplicação. A Azure Communication lança v25.**

Receberá três anos de aviso antes que estas APIs parem de funcionar e sejam forçados a atualizar para v25. Esta atualização pode requerer uma alteração de código.

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
