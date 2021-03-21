---
title: Visão geral do quadro dos serviços de comunicação do Azure
titleSuffix: An Azure Communication Services conceptual document
description: Conheça o Quadro de Serviços de Comunicação Azure
author: ddematheu2
ms.author: dademath
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 47a32815ded5809edfde856a38c69ec7c6fd6fdf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103493361"
---
# <a name="azure-communication-services-ui-framework"></a>Quadro de UI dos Serviços de Comunicação Azure

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

:::image type="content" source="../media/ui-framework/pre-and-custom-composite.png" alt-text="Comparação entre compósitos pré-construídos e personalizados":::

A Azure Communication Services UI Framework facilita-lhe a construção de experiências modernas de utilizador de comunicações. Dá-lhe uma biblioteca de componentes de UI prontos para a produção que pode deixar cair nas suas aplicações:

- **Componentes Compostos** - Estes componentes são soluções chave-na-curva que implementam cenários comuns de comunicação. Pode adicionar rapidamente experiências de chamada de vídeo ou chat às suas aplicações. Os compostos são componentes de código aberto construídos com componentes base.
- **Componentes base** - Estes componentes são blocos de construção de código aberto que permitem construir experiência de comunicação personalizada. Os componentes são oferecidos tanto para capacidades de chamada como de chat que podem ser combinadas para construir experiências. 

Estas bibliotecas de clientes da UI utilizam todas as linguagens e ativos [fluentes da Microsoft.](https://developer.microsoft.com/fluentui/) A UI fluente fornece uma camada fundamental para o UI Framework que foi testada em todos os produtos da Microsoft.

## <a name="differentiating-components-and-composites"></a>**Componentes diferenciadores e compósitos**

**Os Componentes Base** são construídos em cima das bibliotecas de clientes do Core Azure Communication Services e implementam ações básicas como a inicialização das bibliotecas principais do cliente, a renderização de vídeo e o fornecimento de controlos do utilizador para silenciamento, vídeo ligado/desligado, etc. Você pode usar estes Componentes Base para construir suas **próprias experiências** de layout personalizado usando componentes de comunicação pré-construídos e prontos de produção.

:::image type="content" source="../media/ui-framework/component-overview.png" alt-text="Visão geral do componente para o quadro de UI":::

**Os componentes compósitos** combinam **vários componentes base** para criar experiências de comunicação mais completas. Estes componentes de nível superior podem ser facilmente integrados numa app existente para deixar cair uma experiência de comunicação completa sem a tarefa de construí-la de raiz. Os desenvolvedores podem concentrar-se na construção da experiência e fluxo circundante desejados nas suas apps e deixar a complexidade das comunicações para componentes compósitos.

:::image type="content" source="../media/ui-framework/composite-overview.png" alt-text="Visão geral do composto para o quadro de UI":::

## <a name="what-ui-framework-is-best-for-my-project"></a>Qual é o melhor para o meu projeto?

Compreender estes requisitos irá ajudá-lo a escolher a biblioteca de clientes certa:

- **Qual a personalização que deseja?** As bibliotecas principais de clientes da Azure Communication não têm um UX e são projetadas para que possa construir o UX que quiser. Os componentes do UI Framework fornecem ativos de UI à custa da menor personalização.
- **Precisa de funcionalidades de reunião?** O sistema de Encontros tem várias capacidades únicas não disponíveis atualmente nas principais bibliotecas de clientes dos Serviços de Comunicação Azure, tais como fundo desfocado e mão levantada.
- **Quais as plataformas que está a apontar?** Plataformas diferentes têm capacidades diferentes.

Detalhes sobre a disponibilidade de recursos nos [variados UI SDKs estão disponíveis aqui](ui-sdk-features.md), mas as principais trocas são resumidas abaixo.

|Biblioteca de clientes / SDK|Complexidade de Implementação|    Capacidade de personalização|  Chamando| Chat| [Equipas Interop](./../teams-interop.md)
|---|---|---|---|---|---|---|
|Componentes Compostos|Baixo|Baixo|✔|✔|✕
|Componentes base|Médio|Médio|✔|✔|✕
|Bibliotecas de clientes centrais|Alto|Alto|✔|✔ |✔

## <a name="cost"></a>Custo

A utilização de Quadros Azure UI não tem qualquer custo extra de Azure ou medição. Você só paga pelo uso do serviço subjacente, usando os mesmos contadores De Chamada, Chat e PSTN.

## <a name="supported-use-cases"></a>Casos de utilização apoiados

Chamando:

- Junte-se a Azure Communication Services call com o ID do Grupo

Chat:

- Junte-se ao chat dos Serviços de Comunicação Azure com o Thread ID

## <a name="supported-identities"></a>Identidades apoiadas:

É necessária uma identidade dos Serviços de Comunicação Azure para inicializar o Quadro de UI e autenticar o serviço. Para obter mais informações sobre autenticação, consulte [Autenticação](../authentication.md) e [Tokens de Acesso](../../quickstarts/access-tokens.md)


## <a name="recommended-architecture"></a>Arquitetura recomendada 

:::image type="content" source="../media/ui-framework/framework-architecture.png" alt-text="UI Framework recomendou arquitetura com arquitetura de servidor de clientes ":::

Os componentes compósitos e base são inicializados utilizando um token de acesso dos Serviços de Comunicação Azure. Os tokens de acesso devem ser adquiridos a partir dos Serviços de Comunicação Azure através de um serviço de confiança que gere. Consulte [Quickstart: Crie Tokens de acesso](../../quickstarts/access-tokens.md) e tutorial de serviço [fidedigno](../../tutorials/trusted-service-tutorial.md) para obter mais informações.

Estas bibliotecas de clientes também requerem o contexto para a chamada ou chat a que se juntarão. Semelhante aos tokens de acesso ao utilizador, este contexto deve ser divulgado aos clientes através do seu próprio serviço de confiança. A lista abaixo resume as funções de inicialização e gestão de recursos que precisa para operacionalizar.

| Responsabilidades de Contoso                                 | Responsabilidades do Quadro da UI                         |
|----------------------------------------------------------|-----------------------------------------------------------------|
| Fornecer ficha de acesso a partir de Azure                    | Passar através de dado token de acesso para inicializar componentes        |
| Fornecer função de atualização                                 | Atualizar o token de acesso usando a função fornecida pelo desenvolvedor          |
| Recuperar/Passar juntar informações para chamada ou chat          | Passar informações de chamada e chat para inicializar componentes |
| Recuperar/Passar informações do utilizador para qualquer modelo de dados personalizado | Passe através de modelo de dados personalizado para componentes para renderizar          |
