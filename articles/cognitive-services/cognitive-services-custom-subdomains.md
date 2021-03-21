---
title: Subdomínios personalizados
titleSuffix: Azure Cognitive Services
description: Os nomes de subdomínio personalizados para cada recurso do Serviço Cognitivo são criados através do portal Azure Cloud Shell ou Azure CLI.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 12/04/2020
ms.author: erhopf
ms.openlocfilehash: 4746aad2d7cd62cb309a1823f8c50487e6f7e87c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97032971"
---
# <a name="custom-subdomain-names-for-cognitive-services"></a>Nomes de subdomínio personalizados para Serviços Cognitivos

Os Serviços Cognitivos Azure utilizam nomes de subdomínios personalizados para cada recurso criado através do [portal Azure](https://portal.azure.com) [Cloud Shell,](https://azure.microsoft.com/features/cloud-shell/)ou [Azure CLI](/cli/azure/install-azure-cli). Ao contrário dos pontos finais regionais, que eram comuns para todos os clientes de uma região específica de Azure, os nomes de subdomínios personalizados são exclusivos do recurso. Os nomes de subdomínio personalizados são necessários para ativar funcionalidades como Azure Ative Directory (Azure AD) para a autenticação.

## <a name="how-does-this-impact-existing-resources"></a>Como é que isto afeta os recursos existentes?

Os recursos dos Serviços Cognitivos criados antes de 1 de julho de 2019 utilizarão os pontos finais regionais para o serviço associado. Estes pontos finais funcionarão com recursos existentes e novos.

Se quiser migrar um recurso existente para alavancar nomes de subdomínios personalizados, para que possa ativar funcionalidades como Azure AD, siga estas instruções:

1. Inscreva-se no portal Azure e localize o recurso serviços cognitivos a que gostaria de adicionar um nome de subdomínio personalizado.
2. Na lâmina **de visão geral,** localize e selecione **Gerar Nome de Domínio Personalizado**.
3. Isto abre um painel com instruções para criar um subdomínio personalizado único para o seu recurso.
   > [!WARNING]
   > Depois de criar um nome de subdomínio **personalizado, não pode** ser alterado.

## <a name="do-i-need-to-update-my-existing-resources"></a>Preciso atualizar os meus recursos existentes?

N.º O ponto final regional continuará a trabalhar para novos serviços cognitivos existentes e o nome de subdomínio personalizado é opcional. Mesmo que um nome de subdomínio personalizado seja adicionado, o ponto final regional continuará a funcionar com o recurso.

## <a name="what-if-an-sdk-asks-me-for-the-region-for-a-resource"></a>E se um SDK me pedir um recurso para a região?

> [!WARNING]
> Os Serviços de Fala utilizam subdomínios personalizados **apenas** com [pontos finais privados](Speech-Service/speech-services-private-link.md) . Em todos os outros casos, utilize **pontos finais regionais** com serviços de fala e SDKs associados.

Os pontos finais regionais e os nomes de subdomínio personalizados são suportados e podem ser usados intercambiavelmente. No entanto, é necessário o ponto final completo.

Informações da região estão disponíveis na lâmina **de visão geral** para o seu recurso no [portal Azure](https://portal.azure.com). Para a lista completa dos pontos finais regionais, consulte [a lista de pontos finais regionais?](#is-there-a-list-of-regional-endpoints)

## <a name="are-custom-subdomain-names-regional"></a>Os nomes de subdomínio personalizado são regionais?

Sim. Usar um nome de subdomínio personalizado não altera nenhum dos aspetos regionais do seu recurso de Serviços Cognitivos.

## <a name="what-are-the-requirements-for-a-custom-subdomain-name"></a>Quais são os requisitos para um nome de subdomínio personalizado?

Um nome subdomínio personalizado é exclusivo do seu recurso. O nome só pode incluir caracteres alfanuméricos e o `-` personagem; deve ter entre 2 e 64 caracteres de comprimento e não pode terminar com um `-` .

## <a name="can-i-change-a-custom-domain-name"></a>Posso mudar um nome de domínio personalizado?

N.º Depois de um nome de subdomínio personalizado ser criado e associado a um recurso não pode ser alterado.

## <a name="can-i-reuse-a-custom-domain-name"></a>Posso reutilizar um nome de domínio personalizado?

Cada nome de subdomínio personalizado é único, por isso, para reutilizar um nome de subdomínio personalizado que atribuiu a um recurso de Serviços Cognitivos, terá de eliminar o recurso existente. Depois de o recurso ter sido eliminado, pode reutilizar o nome de subdomínio personalizado.

## <a name="is-there-a-list-of-regional-endpoints"></a>Existe uma lista de pontos finais regionais?

Sim. Esta é uma lista de pontos finais regionais que você pode usar com recursos dos Serviços Cognitivos Azure.

> [!NOTE]
> O serviço de tradutor e as APIs de pesquisa de Bing utilizam pontos finais globais.

| Tipo de ponto final | Region | Ponto final |
|---------------|--------|----------|
| Público | Global (Tradutor & Bing) | `https://api.cognitive.microsoft.com` |
| | Leste da Austrália | `https://australiaeast.api.cognitive.microsoft.com` |
| | Sul do Brasil | `https://brazilsouth.api.cognitive.microsoft.com` |
| | Canadá Central | `https://canadacentral.api.cognitive.microsoft.com` |
| | E.U.A. Central | `https://centralus.api.cognitive.microsoft.com` |
| | Ásia Leste | `https://eastasia.api.cognitive.microsoft.com` |
| | E.U.A. Leste | `https://eastus.api.cognitive.microsoft.com` |
| | E.U.A. Leste 2 | `https://eastus2.api.cognitive.microsoft.com` |
| | França Central | `https://francecentral.api.cognitive.microsoft.com` |
| | Índia Central | `https://centralindia.api.cognitive.microsoft.com` |
| | Leste do Japão | `https://japaneast.api.cognitive.microsoft.com` |
| | Coreia do Sul Central | `https://koreacentral.api.cognitive.microsoft.com` |
| | E.U.A. Centro-Norte | `https://northcentralus.api.cognitive.microsoft.com` |
| | Europa do Norte | `https://northeurope.api.cognitive.microsoft.com` |
| | Norte da África do Sul | `https://southafricanorth.api.cognitive.microsoft.com` |
| | E.U.A. Centro-Sul | `https://southcentralus.api.cognitive.microsoft.com` |
| | Sudeste Asiático | `https://southeastasia.api.cognitive.microsoft.com` |
| | Sul do Reino Unido | `https://uksouth.api.cognitive.microsoft.com` |
| | E.U.A. Centro-Oeste | `https://westcentralus.api.cognitive.microsoft.com` |
| | Europa Ocidental | `https://westeurope.api.cognitive.microsoft.com` |
| | E.U.A. Oeste | `https://westus.api.cognitive.microsoft.com` |
| | E.U.A. Oeste 2 | `https://westus2.api.cognitive.microsoft.com` |
| US Gov | US Gov - Virginia | `https://virginia.api.cognitive.microsoft.us` |
| China | China Leste 2 | `https://chinaeast2.api.cognitive.azure.cn` |
| | Norte da China | `https://chinanorth.api.cognitive.azure.cn` |

## <a name="see-also"></a>Ver também

* [O que são os Serviços Cognitivos?](./what-are-cognitive-services.md)
* [Autenticação](authentication.md)