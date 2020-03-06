---
title: Azure Government Web, Mobile e API  Microsoft Docs
description: Isto fornece uma comparação de características e orientações sobre o desenvolvimento de candidaturas para o Governo Azure
services: azure-government
cloud: gov
documentationcenter: ''
author: gsacavdm
manager: pathuff
ms.assetid: a1e173a9-996a-4091-a2e3-6b1e36da9ae1
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 11/22/2019
ms.author: gsacavdm
ms.openlocfilehash: 15c6936b7a8c319c4ddef86eddc47546966782b4
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78396308"
---
# <a name="azure-government-web--mobile"></a>Azure Government Web + Mobile

Este artigo descreve os serviços web e móveis para o ambiente do Governo Azure, descrevendo variações de características que diferem da versão pública, bem como quaisquer considerações específicas do ambiente.

## <a name="azure-cognitive-search"></a>Azure Cognitive Search

[A Pesquisa Cognitiva Azure](https://docs.microsoft.com/azure/search/) está geralmente disponível no Governo Azure. Para uma exploração auto-direcionada da funcionalidade de pesquisa utilizando dados do governo público, visite o site de Pesquisa e Inteligência de [Conteúdo,](https://documentsearch.azurewebsites.net/home/) selecione o conjunto de dados "Us Court of Appeals District 1", e depois escolha uma das opções de demonstração.

As funcionalidades de pesquisa que foram amplamente adotadas em aplicações de pesquisa do governo incluem [habilidades cognitivas,](https://docs.microsoft.com/azure/search/cognitive-search-concept-intro)úteis para extrair estrutura e informações de grandes documentos de texto indiferenciados.

A sintaxe de consulta básica, formulando consultas para pesquisar sobre grandes quantidades de conteúdo, também é relevante para os desenvolvedores de aplicações. A Pesquisa Cognitiva Azure suporta duas sintaxes: [simples](https://docs.microsoft.com/azure/search/query-simple-syntax) e [cheias.](https://docs.microsoft.com/azure/search/query-lucene-syntax) Pode rever [exemplos](https://docs.microsoft.com/azure/search/search-query-simple-examples) de expressão de consulta para uma orientação.

### <a name="variations"></a>Variações
O ponto final dos serviços de pesquisa criados no Governo de Azure é o seguinte:

| Tipo de Serviço | Azure Public | Azure Government |
| ------------ | ------------ | ---------------- |
| Serviço de Pesquisa Cognitiva Azure |\*.search.windows.net |\*.search.windows.us|

Todos os recursos geralmente disponíveis e de pré-visualização na nuvem pública também estão disponíveis no Governo azure.

### <a name="considerations"></a>Considerações

As seguintes informações identificam a fronteira do Governo Azure para a Pesquisa Cognitiva Azure:

| Dados regulados/controlados permitidos | Dados regulados/controlados não permitidos |
| ----------------------------------- | --------------------------------------- |
| Todos os dados armazenados e processados na Pesquisa Cognitiva Azure podem conter dados regulados pelo Governo de Azure. | Os metadados de pesquisa cognitiva Azure não estão autorizados a conter dados controlados pela exportação. Estes metadados incluem todos os dados de configuração introduzidos na criação e manutenção do seu serviço. Não introduza dados regulados/controlados nos seguintes campos: Nome de subscrição, grupos de recursos, nome de serviço, nomes de recursos, etiquetas de recursos, ou qualquer objeto que nomeie ou descreva em Pesquisa Cognitiva (índices, indexadores, fontes de dados, mapas de sinónimos e habilidades). Não inclua dados sensíveis em cabeçalhos HTTP enviados para o REST API pr em cadeias de pesquisa/consulta enviadas como parte da API.|

## <a name="app-services"></a>Serviços de Aplicações
### <a name="variations"></a>Variações
Os Serviços de Aplicações Azure estão geralmente disponíveis no Governo Azure.

O endereço para aplicações azure App Service criadas no Governo Azure é diferente das aplicações criadas na nuvem pública:

| Tipo de Serviço | Azure Public | Azure Government |
| --- | --- | --- |
| Serviço de Aplicações |\*.azurewebsites.net |\*.azurewebsites.us|
| ID principal de serviço| abfa0a7c-a6b6-4736-8310-585508787cd | 6a02c803-dafd-4136-b4c3-5a6f318b4714 |

Algumas funcionalidades do Serviço de Aplicações disponíveis na nuvem pública ainda não estão disponíveis no Governo do Azure:

- Diagnosticar e resolver problemas
- Implementação
    - Opções de Implementação: Apenas o Repositório Local Git e o Repositório Externo estão disponíveis.
    - Centro de Implantação
- Definições
    - Application Insights
- Ferramentas de Desenvolvimento
    - Teste de desempenho
    - Explorador de recursos
    - PHP Debugging
- Suporte e resolução de problemas
    - Consultor de Serviço sinuoso
- Certificados de Serviço de Aplicações


### <a name="considerations"></a>Considerações
As seguintes informações identificam a fronteira do Governo Azure para o Serviço de Aplicações:

| Dados regulados/controlados permitidos | Dados regulados/controlados não permitidos |
| --- | --- |
| Os dados introduzidos, armazenados e tratados no Serviço de Aplicações Azure podem conter dados controlados pela exportação. Binários em execução dentro do Serviço de Aplicações Azure. Autenticadores estáticos, tais como senhas e SMARTCARD PINs para acesso aos componentes da plataforma Azure. Chaves privadas de certificados usados para gerir componentes da plataforma Azure. Cordas de ligação SQL. Outras informações/segredos de segurança, tais como certificados, chaves de encriptação, chaves principais e chaves de armazenamento armazenadas nos serviços Azure. |Os metadados não estão autorizados a conter dados controlados pela exportação. Estes metadados incluem todos os dados de configuração introduzidos na criação e manutenção do seu Serviço de Aplicações Azure. Não introduza dados regulados/controlados nos seguintes campos: Grupos de recursos, nomes de recursos, etiquetas de recursos|

## <a name="api-management"></a>Gestão de API
Para mais informações sobre este serviço e como utilizá-lo, consulte a documentação da [Azure API Management.](../api-management/index.yml)

### <a name="variations"></a>Variações

O serviço de Gestão Da API Azure está geralmente disponível no Governo Azure. As funcionalidades que não estão atualmente disponíveis no serviço de Gestão API para o Governo Azure são:

- Integração Azure AD B2C 
    - Integração com Azure AD B2C não está disponível no Governo azure 

Os URLs para aceder à Gestão da API Azure no Governo Azure são diferentes:

| Tipo de Serviço | Azure Public | Azure Government |
| --- | --- | --- |
|Gateway de gestão da API| \*.azure-api.net| \*.azure-api.us|
|Portal de Gestão API | \*.portal.azure-api.net |\*.portal.azure-api.us| 
|Gestão da API| \*.management.azure-api.net |\*.management.azure-api.us|

### <a name="considerations"></a>Considerações
As seguintes informações identificam a fronteira do Governo Azure para o serviço de gestão da API Azure:

| Dados regulados/controlados permitidos | Dados regulados/controlados não permitidos |
| --- | --- |
|Todos os dados armazenados e tratados no serviço de Gestão API azure podem conter dados regulados pelo Governo do Azure.|Os metadados do serviço azure API Management não estão autorizados a conter dados controlados pela exportação. Não introduza dados regulados/controlados nos seguintes campos: Nome do serviço de gestão aPI, nome de subscrição, grupos de recursos, etiquetas de recursos.|

## <a name="next-steps"></a>Passos Seguintes
Para obter informações e atualizações suplementares, subscreva o [Microsoft Azure Government Blog.](https://blogs.msdn.microsoft.com/azuregov/)

