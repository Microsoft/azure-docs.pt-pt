---
title: Bibliotecas de clientes e APIs REST para Serviços de Comunicação Azure
titleSuffix: An Azure Communication Services concept document
description: Saiba mais sobre as bibliotecas de clientes dos Serviços de Comunicação Azure e as APIs rest.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/18/2020
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 2d5637be547b28c231ef757b5dd4355692dab88f
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91318828"
---
# <a name="client-libraries-and-rest-apis"></a>Bibliotecas de cliente e APIs REST

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

As capacidades dos Serviços de Comunicação Azure estão conceptualmente organizadas em seis áreas. Algumas áreas têm bibliotecas de clientes totalmente abertas. A biblioteca de clientes Call utiliza interfaces de rede proprietárias e é atualmente de origem fechada, e a biblioteca Chat inclui uma dependência de fonte fechada. As ligações a todos os SDKs e amostras são mantidas no [repo dos Serviços de Comunicação Azure GitHub](https://github.com/Azure/communication).

## <a name="client-libraries"></a>Bibliotecas de cliente

| Assemblagem               | Protocolos             |Aberto vs. Fonte Fechada| Espaços de nomes                          | Capacidades                                                      |
| ---------------------- | --------------------- | ---|-------------------------- | --------------------------------------------------------------------------- |
| Azure Resource Manager | REST | Abrir            | Azure.ResourceManager.Communication | Fornecimento e gestão dos recursos dos Serviços de Comunicação             |
| Common                 | REST | Abrir               | Azure.Communication.Common          | Fornece tipos de base para outras bibliotecas de clientes |
| Administração         | REST | Abrir               | Azure.Communication.Administration  | Gerir utilizadores, aceder a fichas e números de telefone, alocar servidores STUN e TURN compatíveis com padrões |
| Chat                   | DESCANSE COM sinalização proprietária | Aberto com pacote de sinalização de fonte fechada    | Azure.Communication.Chat            | Adicione o chat baseado em texto em tempo real às suas aplicações  |
| SMS                    | REST | Abrir              | Azure.Communication.SMS             | Enviar e receber mensagens SMS |
| Chamando                | Transporte proprietário | Fechada |Azure.Communication.Calling         | Alavancar a voz, o vídeo, a partilha de ecrãs e outras capacidades de comunicação de dados em tempo real          |

### <a name="client-library-language-support"></a>Suporte à linguagem da biblioteca do cliente

Abaixo estão detalhadas as orientações e os prazos de disponibilidade para pacotes individuais de biblioteca de clientes. O [roteiro Azure](https://azure.microsoft.com/updates/) fornece informações adicionais sobre as próximas funcionalidades.

| Área           | JavaScript | .NET | Python | Java | Swift ou Obj-C | Java (Android) | Outro                          |
| -------------- | ---------- | ---- | ------ | ---- | -------------- | -------------- | ------------------------------ |
| Azure Resource Manager | ✔️         | ✔️    | ✔️      | -    | -              | *Ainda não suportado*  | GO e Azure CLI *Ainda não apoiados* |
| Common         | ✔️         | ✔️    | -      | ✔️   | ✔️            | ✔️             | -                              |
| Administração | ✔️         | ✔️    | ✔️      | ✔️   | -              | -              | CLI                            |
| Chat           | ✔️         | ✔️    | ✔️      | ✔️   | *Ainda não suportado*  | *Ainda não suportado*  | -                              |
| SMS            | ✔️         | ✔️    | ✔️      | ✔️   | -              | -              | -                              |
| Chamando        | ✔️         | -      | -      | -     | (Obj-C) ✔️     | ✔️            | -                              |

### <a name="client-library-public-repository-support"></a>Apoio ao repositório público da biblioteca do cliente

Serviços de Comunicação publica bibliotecas construídas em vários repositórios públicos.

| Linguagem       | Otimizado para...                       | Empacotamento |
| -------------- | ------------------------------------ | --------- |
| .NET           | Plataforma transversal                       | NuGet     |
| Python         | Servidores Linux windows &              | Pypi      |
| Java (J2EE)    | JVM em Servidores Windows ou Linux      | Maven     |
| Java (Android) | Aplicações para clientes Android          | Maven     |
| JavaScript     | Aplicações de clientes do navegador e nó | Npm       |

## <a name="rest-apis"></a>APIs REST

As APIs dos Serviços de Comunicação são documentadas juntamente com outras APIs Azure REST em [docs.microsoft.com](https://docs.microsoft.com/rest/api/azure/). Esta documentação irá dizer-lhe como estruturar as suas mensagens HTTP e oferece orientações para a utilização do Carteiro. Esta documentação também é oferecida em formato Swagger no [GitHub.](https://github.com/Azure/azure-rest-api-specs)

## <a name="additional-support-details"></a>Detalhes adicionais de suporte

### <a name="ios-and-android-support-details"></a>Detalhes de suporte para iOS e Android

- As bibliotecas de clientes iOS dos Serviços de Comunicação iOS têm como alvo a versão 13+, e o Xcode 11+.
- Bibliotecas de clientes Android Java têm como alvo Android API nível 21+ e Android Studio 4.0+

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

## <a name="api-stability-expectations"></a>Expectativas de estabilidade da API 

> [!IMPORTANT]
> Esta secção fornece orientações sobre ASP ESE REST e bibliotecas de clientes marcadas **como estáveis.** APIs marcados antes de ser pré-lançamento, pré-visualização ou beta podem ser alterados ou depreciados **sem aviso prévio**. Atualmente, os Serviços de Comunicação Azure estão em **pré-visualização pública,** e as APIs estão marcadas como tal.

No futuro, poderemos retirar versões das bibliotecas de clientes dos Serviços de Comunicação, e podemos introduzir alterações de rutura nas nossas APIs rest e bibliotecas de clientes lançadas. Os Serviços de Comunicação da Azure seguirão *geralmente* duas políticas de apoio às versões de serviço de aposentação:

- Será notificado pelo menos três anos antes de ser obrigado a alterar o código devido a uma alteração da interface dos Serviços de Comunicação. Todas as APIs de REST documentadas e apIs da biblioteca de clientes geralmente gozam de pelo menos três anos de aviso antes de as interfaces serem desativadas.
- Será notificado pelo menos um ano antes de ter de atualizar as assembleias de biblioteca de clientes para a versão menor mais recente. Estas atualizações necessárias não devem exigir alterações de código porque estão na mesma versão principal. Isto é especialmente verdade para as bibliotecas Call and Chat que têm componentes em tempo real que frequentemente requerem atualizações de segurança e desempenho. Encorajamo-lo a manter as suas bibliotecas de clientes dos Serviços de Comunicação atualizadas.

### <a name="api-and-client-library-decommissioning-examples"></a>Exemplos de desmantelamento de bibliotecas de clientes e API

**Integrou a versão v24 da API SMS REST na sua aplicação. A Azure Communication lança v25.**

Receberá 3 anos de aviso antes que estas APIs parem de funcionar e sejam obrigadas a atualizar para v25. Esta atualização pode requerer uma alteração de código.

**Integrou a versão v2.02 da biblioteca do cliente Call na sua aplicação. A Azure Communication lança v2.05.**

Poderá ser necessário atualizar para a versão v2.05 da biblioteca do cliente Call no prazo de 12 meses após o lançamento do v2.05. Esta deve ser uma simples substituição do artefacto sem exigir uma alteração de código porque o v2.05 está na versão principal v2 e não tem alterações de rutura.

## <a name="next-steps"></a>Passos seguintes

Para mais informações, consulte as seguintes sínteses da biblioteca do cliente:

- [Chamar a biblioteca de clientes visão geral](../concepts/voice-video-calling/calling-sdk-features.md)
- [Visão geral da biblioteca do cliente do chat](../concepts/chat/sdk-features.md)
- [Visão geral da biblioteca do cliente POR SMS](../concepts/telephony-sms/sdk-features.md)

Para começar com os Serviços de Comunicação Azure:

- [Criar Recursos de Comunicação Azure](../quickstarts/create-communication-resource.md)
- Gerar [fichas de acesso ao utilizador](../quickstarts/access-tokens.md)
