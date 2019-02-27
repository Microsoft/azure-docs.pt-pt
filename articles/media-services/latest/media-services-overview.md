---
title: Descrição geral dos Serviços de Multimédia do Azure v3 | Microsoft Docs
description: Este artigo fornece uma descrição geral de alto nível dos Serviços de Multimédia, além de ligações para artigos para obter mais detalhes.
services: media-services
documentationcenter: na
author: Juliako
manager: femila
editor: ''
tags: ''
keywords: serviços de multimédia do azure, transmissão, difusão, em direto, offline
ms.service: media-services
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 02/07/2019
ms.author: juliako
ms.custom: mvc
ms.openlocfilehash: d6805b3d60e5e30d35acc12c5564498830ddd8fb
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56881737"
---
# <a name="what-is-azure-media-services-v3"></a>O que são os Serviços de Multimédia do Azure v3?

Os Serviços de Multimédia do Azure são uma plataforma baseada na cloud que lhe permite criar soluções que possibilitam a transmissão em vídeo com qualidade de transmissão, melhorar a acessibilidade e distribuição, analisar conteúdo e muito mais. Quer seja um programador de aplicações, um centro de atendimento, um organismo público, uma empresa de entretenimento, os Serviços de Multimédia ajudam-no a criar aplicações que permitem experiências de multimédia de qualidade extraordinária para um grande público nos browsers e nos dispositivos móveis atualmente mais populares. 

## <a name="what-can-i-do-with-media-services"></a>O que posso fazer com os Serviços de Multimédia?

Os Serviços de Multimédia permitem-lhe criar uma variedade de fluxos de trabalho de multimédia na cloud. Veja a seguir alguns exemplos do pode fazer com os Serviços de Multimédia.  

* Fornecer vídeos em diferentes formatos para que possam ser reproduzidos numa ampla variedade de dispositivos e browsers. Para disponibilização de transmissão tanto a pedido como em direto para vários clientes (dispositivos móveis, TV, PC, etc.), o conteúdo de áudio e vídeo tem de ser codificado e empacotado adequadamente. Para ver como fornecer e transmitir esse conteúdo, consulte [início rápido: Codificar e transmitir ficheiros](stream-files-dotnet-quickstart.md).
* Transmita em direto eventos desportivos para uma grande audiência online, tais como jogos de futebol, basebol, eventos desportivos escolares e muito mais. 
* Difunda sessões e eventos públicos como, por exemplo, reuniões municipais, assembleias municipais e órgãos legisladores.
* Analise o conteúdo de áudio ou de vídeos registados. Por exemplo, para alcançar uma maior satisfação do cliente, as organizações podem extrair a conversão de voz em texto e criar índices de pesquisa e dashboards. Em seguida, podem extrair informações relativas a queixas comuns, fontes de queixas e outros dados relevantes.
* Crie um serviço de subscrição de vídeo e transmita conteúdo DRM protegido quando um cliente (por exemplo, um estúdio cinematográfico) necessita de restringir o acesso e a utilização de obras proprietárias protegidas por direitos de autor.
* Disponibilize conteúdo offline para reprodução em aviões, comboios e automóveis. Um cliente poderá ter de transferir o conteúdo para o telemóvel ou tablet para reprodução posterior quando se prevê que esteja desligado da rede.
* Implemente uma plataforma de vídeo de e-learning educativo com os Serviços de Multimédia do Azure e as [APIs Serviços Cognitivos do Azure](https://docs.microsoft.com/azure/#pivot=products&panel=ai) para legendagem de conversão de voz em texto, tradução para vários idiomas, etc. 
* Utilizar serviços de multimédia do Azure em conjunto com [APIs serviços cognitivos do Azure](https://docs.microsoft.com/azure/#pivot=products&panel=ai) para adicionar legendas para vídeos para se adaptar a um público mais amplo (por exemplo, as pessoas portadoras de deficiências auditivas ou pessoas que desejam ler ao longo de forma diferente idioma).
* Ative a CDN do Azure para alcançar a grande dimensionamento para processar melhor cargas elevadas instantâneas (por exemplo, o início de um evento de lançamento do produto). 

## <a name="v3-capabilities"></a>Capacidades da v3

A v3 baseia-se numa superfície da API unificada que expõe uma funcionalidade incorporada de gestão e de operações no Azure Resource Manager. 

Esta versão fornece as capacidades seguintes:  

* **Transformações** para ajudar a definir fluxos de trabalho de tarefas de análise ou de processamento de multimédia simples. A Transformação é uma receita para processar os ficheiros de áudio e de vídeo. Em seguida, pode aplicá-la repetidamente para processar todos os ficheiros na biblioteca de conteúdos, ao submeter as tarefas para Transformação.
* **Tarefas** para processar (codificar ou analisar) os vídeos. Um conteúdo de entrada pode ser especificado numa tarefa com URLs HTTPS, URLs SAS ou caminhos de ficheiros localizados no Armazenamento de blobs do Azure. Atualmente, o AMS v3 não suporta a codificação de transferência segmentada através de URLs HTTPS.
* **Notificações** que monitorizam o progresso ou os estados da tarefa ou eventos de erro ou de início/paragem do Canal em Direto. As notificações estão integradas no sistema de notificação do Azure Event Grid. Pode facilmente subscrever eventos em vários recursos nos Serviços de Multimédia do Azure. 
* Os modelos da **Gestão de Recursos do Azure** podem ser utilizados para criar e implementar as Transformações, os Pontos Finais de Transmissão em Fluxo, os Canais e muito mais.
* O **Controlo de acesso baseado em funções** pode ser definido ao nível do recurso e permite-lhe bloquear o acesso a recursos específicos como Transformações, Canais e muito mais.
* **SDKs do Cliente** em várias linguagens: .NET, .NET core, Python, Go, Java e Node.js.

## <a name="naming-conventions"></a>Convenções de nomenclatura

Os nomes de recursos dos Serviços de Multimédia do Azure v3 (por exemplo, Ativos, Tarefas, Transformações) estão sujeitos às restrições de nomenclatura do Azure Resource Manager. De acordo com o Azure Resource Manager, os nomes de recursos são sempre exclusivos. Desta forma, pode utilizar todas as cadeias de identificador exclusivo (por exemplo, GUIDs) para os nomes de recursos. 

Os nomes de recursos dos Serviços de Multimédia não podem incluir: "<", ">", "%", "&", ":", "&#92;", "?", "/", "*", "+", ".", a plica ou qualquer caráter de controlo. Todos os outros carateres são permitidos. O comprimento máximo de um nome de recurso é superior a 260 carateres. 

Para obter mais informações sobre a nomenclatura do Azure Resource Manager, consulte: [Requisitos de nomes](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource) e [convenções de nomenclatura](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).

## <a name="v3-api-design-principles"></a>princípios de design de API v3

Um dos principais princípios de design da API v3 é tornar a API mais segura. As APIs v3 não devolvem segredos ou credenciais sobre uma operação **Get** ou **List**. As chaves são sempre nulas, vazias ou saneadas da resposta. Tem de chamar um método de ação separado para obter segredos ou credenciais. As ações separadas permitem-lhe definir diferentes permissões de segurança RBAC no caso de algumas APIs obterem/mostrarem segredos e outras não. Para obter informações sobre como gerir o acesso através do RBAC, consulte [Utilizar o RBAC para gerir acesso](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest).

Alguns exemplos incluem 

* não devolver valores ContentKey no Get do StreamingLocator, 
* não devolver as chaves de restrição no get do ContentKeyPolicy, 
* não devolver a parte da cadeia de consulta do URL (para remover a assinatura) dos URLs de Entrada de Tarefas.

Consulte a [obter a política de chave conteúda - .NET](get-content-key-policy-dotnet-howto.md) exemplo.

## <a name="how-can-i-get-started-with-v3"></a>Como posso começar a utilizar a v3?

Para obter informações sobre como começar a desenvolver com a API de v3 de serviços de multimédia com várias ferramentas e SDKs, consulte [começar a desenvolver](developers-guide.md).

## <a name="v3-content-map"></a>v3 content map

Os conteúdos de v3 de serviços de multimédia são organizados de acordo com a seguinte estrutura (também refletida no índice):

|Secções| Descrição|
|---|---|
| Descrição geral | Explica a funcionalidade de serviços de multimédia e o que pode fazer com o serviço.|
| Inícios rápidos | Mostre instruções de 1 dia fundamentais para os novos clientes experimentar rapidamente o serviços de multimédia.|
| Tutoriais | Mostre baseados em cenários de procedimentos para algumas das tarefas principais de serviços de multimédia.|
| Amostras | Ligações para exemplos de código. |
| Conceitos | Conter uma explicação minuciosa dos conceitos de v3 de serviços de multimédia e funcionalidade. Os conceitos fundamentais descritos nos seguintes tópicos devem ser revistos antes de iniciar o desenvolvimento.<br/><br/>* Carregamento e o armazenamento de cloud<br/>* A codificação<br/>* Análise de multimédia<br/>* Empacotamento, entrega, proteção<br/>* Transmissão em direto<br/>* Monitorização contínua<br/>* Os clientes de Player<br/><br/>e muito mais. |
| Guias de procedimentos | Demonstre como concluir uma tarefa.|

## <a name="next-steps"></a>Passos Seguintes

Para ver como é fácil começar a codificar e a transmitir ficheiros de vídeo, veja [Stream files](stream-files-dotnet-quickstart.md) (Transmitir ficheiros). 

