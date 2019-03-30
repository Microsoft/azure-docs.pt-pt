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
ms.date: 03/29/2019
ms.author: juliako
ms.custom: mvc
ms.openlocfilehash: 19e94fc65ddc1719c601397adfe77f8f9445e4fa
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58662130"
---
# <a name="what-is-azure-media-services-v3"></a>O que são os Serviços de Multimédia do Azure v3?

Os Serviços de Multimédia do Azure são uma plataforma baseada na cloud que lhe permite criar soluções que possibilitam a transmissão em vídeo com qualidade de transmissão, melhorar a acessibilidade e distribuição, analisar conteúdo e muito mais. Quer seja um programador de aplicações, um centro de atendimento, um organismo público, uma empresa de entretenimento, os Serviços de Multimédia ajudam-no a criar aplicações que permitem experiências de multimédia de qualidade extraordinária para um grande público nos browsers e nos dispositivos móveis atualmente mais populares. 

> [!NOTE]
> Atualmente, não é possível utilizar o portal do Azure para gerir os recursos de v3. Utilize o [REST API](https://aka.ms/ams-v3-rest-ref), [CLI](https://aka.ms/ams-v3-cli-ref), ou um suportadas [SDKs](developers-guide.md).

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

Aprenda a codificar e compactar conteúdo, transmitir vídeos sob pedido, transmitir em direto, analisar os seus vídeos com serviços de multimédia v3. Tutoriais, referências de API e outra documentação mostram-lhe como fornecer de forma segura transmissões em fluxo de vídeo ou áudio a pedido e live dimensionáveis para milhões de utilizadores.

### <a name="quickstarts"></a>Inícios rápidos  

Os inícios rápidos mostram instruções de 1 dia fundamentais para os novos clientes experimentar rapidamente o serviços de multimédia.

* [Ficheiros de vídeo do Stream - .NET](stream-files-dotnet-quickstart.md)
* [Ficheiros de vídeo do Stream - CLI](stream-files-cli-quickstart.md)
* [Ficheiros de vídeo do Stream - node. js](stream-files-nodejs-quickstart.md)
    
### <a name="tutorials"></a>Tutoriais 

Os tutoriais mostram baseados em cenários de procedimentos para algumas das tarefas principais de serviços de multimédia.

* [Codificar o ficheiro remoto e o fluxo de vídeo – REST](stream-files-tutorial-with-rest.md)
* [Codificar o ficheiro carregado e fluxo de vídeo - .NET](stream-files-tutorial-with-api.md)
* [Stream em direto - .NET](stream-live-tutorial-with-api.md)
* [Analisar o seu vídeo - .NET](analyze-videos-tutorial-with-api.md)
* [Encriptação dinâmica AES-128 - .NET](protect-with-aes128.md)
    
### <a name="how-to-guides"></a>Guias de procedimentos

Artigos contém exemplos de código que demonstrem como concluir uma tarefa. Nesta seção, encontrará muitos exemplos, aqui estão apenas algumas delas:

* [Criar uma conta - CLI](create-account-cli-how-to.md)
* [Aceder a APIs - CLI](access-api-cli-how-to.md)
* [Comece a desenvolver com SDKs](developers-guide.md)
* [Codificar com HTTPS como tarefa de entrada - .NET](job-input-from-http-how-to.md)  
* [Monitor de eventos - Portal](monitor-events-portal-how-to.md)
* [Encriptar dinamicamente com múltipla DRM - .NET](protect-with-drm.md) 
* [Como codificar com uma transformação personalizada com a CLI](custom-preset-cli-howto.md)

## <a name="next-steps"></a>Passos Seguintes

Como posso começar a utilizar a v3? 

> [!div class="nextstepaction"]
> [Saiba mais sobre os conceitos fundamentais](concepts-overview.md)<br/>
> [Desenvolver com a API de v3 de serviços de multimédia através de SDKs](developers-guide.md) 

