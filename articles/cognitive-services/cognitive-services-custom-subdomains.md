---
title: Subdomínios personalizados
titleSuffix: Azure Cognitive Services
description: Os nomes personalizados de subdomínio para cada recurso do Serviço Cognitivo são criados através do portal Azure, Azure Cloud Shell ou Azure CLI.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 07/24/2019
ms.author: erhopf
ms.openlocfilehash: 108e5ebfa34db711af4ff33cbf4fbba897b8947d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "73647680"
---
# <a name="custom-subdomain-names-for-cognitive-services"></a>Nomes personalizados de subdomínio para Serviços Cognitivos

Os Serviços Cognitivos Azure utilizam nomes de subdomínio personalizados para cada recurso criado através do [portal Azure,](https://portal.azure.com) [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/), ou [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). Ao contrário dos pontos finais regionais, que eram comuns para todos os clientes numa região específica do Azure, os nomes de subdomínio personalizados são exclusivos do recurso. Os nomes de subdomínio personalizados são necessários para ativar funcionalidades como o Azure Ative Directory (Azure AD) para autenticação.

## <a name="how-does-this-impact-existing-resources"></a>Como é que isto afeta os recursos existentes?

Os recursos dos Serviços Cognitivos criados antes de 1 de julho de 2019 utilizarão os pontos finais regionais para o serviço associado. Estes pontos finais funcionarão com recursos existentes e novos.

Se quiser migrar um recurso existente para alavancar nomes de subdomínio personalizados, para que possa ativar funcionalidades como o Azure AD, siga estas instruções:

1. Inscreva-se no portal Azure e localize o recurso dos Serviços Cognitivos a que gostaria de adicionar um nome de subdomínio personalizado.
2. Na lâmina **de visão geral,** localize e selecione **Generate Custom Domain Name**.
3. Isto abre um painel com instruções para criar um subdomínio personalizado único para o seu recurso.
   > [!WARNING]
   > Depois de ter criado um nome de subdomínio personalizado, **não pode** ser alterado.

## <a name="do-i-need-to-update-my-existing-resources"></a>Preciso atualizar os meus recursos existentes?

Não. O ponto final regional continuará a trabalhar para novos e existentes Serviços Cognitivos e o nome de subdomínio personalizado é opcional. Mesmo que um nome de subdomínio personalizado seja adicionado, o ponto final regional continuará a trabalhar com o recurso.

## <a name="what-if-an-sdk-asks-me-for-the-region-for-a-resource"></a>E se um SDK me pedir um recurso para a região?

> [!WARNING]
> Os Serviços de Fala **não** suportam subdomínios personalizados neste momento. Utilize os pontos finais regionais ao utilizar os Serviços de Fala e os SDKs associados.

Os pontos finais regionais e os nomes de subdomínio personalizados são suportados e podem ser utilizados alternadamente. No entanto, é necessário o ponto final completo.

A informação da região está disponível na lâmina **de visão geral** para o seu recurso no portal [Azure](https://portal.azure.com). Para a lista completa de pontos finais regionais, veja-se [uma lista de pontos finais regionais?](#is-there-a-list-of-regional-endpoints)

## <a name="are-custom-subdomain-names-regional"></a>Os nomes de subdomínio personalizado saem regionais?

Sim. Usar um nome de subdomínio personalizado não altera nenhum dos aspetos regionais do seu recurso de Serviços Cognitivos.

## <a name="what-are-the-requirements-for-a-custom-subdomain-name"></a>Quais são os requisitos para um nome de subdomínio personalizado?

Um nome de subdomínio personalizado é exclusivo do seu recurso. O nome só pode incluir caracteres `-` alfanuméricos e o personagem; deve ter entre 2 e 64 caracteres de `-`comprimento e não pode terminar com um .

## <a name="can-i-change-a-custom-domain-name"></a>Posso mudar um nome de domínio personalizado?

Não. Depois de um nome de subdomínio personalizado ser criado e associado a um recurso, não pode ser alterado.

## <a name="can-i-reuse-a-custom-domain-name"></a>Posso reutilizar um nome de domínio personalizado?

Cada nome de subdomínio personalizado é único, por isso, para reutilizar um nome de subdomínio personalizado que atribuiu a um recurso dos Serviços Cognitivos, terá de eliminar o recurso existente. Depois de o recurso ter sido eliminado, pode reutilizar o nome de subdomínio personalizado.

## <a name="is-there-a-list-of-regional-endpoints"></a>Existe uma lista de pontos finais regionais?

Sim. Esta é uma lista de pontos finais regionais que você pode usar com recursos dos Serviços Cognitivos Azure.

> [!NOTE]
> A API de Texto tradutor e as APIs de pesquisa de Bing usam pontos finais globais.

| Tipo endpoint | Região | Ponto Final |
|---------------|--------|----------|
| Público | Global (Texto tradutor & Bing) | `https://api.cognitive.microsoft.com` |
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
| | África do Sul Norte | `https://southafricanorth.api.cognitive.microsoft.com` |
| | E.U.A. Centro-Sul | `https://southcentralus.api.cognitive.microsoft.com` |
| | Ásia Sudeste | `https://southeastasia.api.cognitive.microsoft.com` |
| | Sul do Reino Unido | `https://uksouth.api.cognitive.microsoft.com` |
| | E.U.A. Centro-Oeste | `https://westcentralus.api.cognitive.microsoft.com` |
| | Europa ocidental | `https://westeurope.api.cognitive.microsoft.com` |
| | E.U.A. Oeste | `https://westus.api.cognitive.microsoft.com` |
| | E.U.A.Oeste 2 | `https://westus2.api.cognitive.microsoft.com` |
| US Gov | US Gov - Virginia | `https://virginia.api.cognitive.microsoft.us` |
| China | China Leste 2 | `https://chinaeast2.api.cognitive.azure.cn` |
| | Norte da China | `https://chinanorth.api.cognitive.azure.cn` |

## <a name="see-also"></a>Consulte também

* [O que são os Serviços Cognitivos?](Welcome.md)
* [Autenticação](authentication.md)
