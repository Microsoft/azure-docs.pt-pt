---
title: Personalização de tradução-API de Tradução de Texto
titleSuffix: Azure Cognitive Services
description: Use o Hub do Microsoft Translator para criar seu próprio sistema de tradução automática usando sua terminologia e estilo preferenciais.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: be9c06d45da4d83c26f82343c9cb7b19ba19b4ae
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71257631"
---
# <a name="customize-your-text-translations"></a>Personalizar suas traduções de texto

O tradutor personalizado da Microsoft é um recurso do serviço Microsoft Translator, que permite aos usuários personalizar a tradução avançada do computador neural do Microsoft Translator ao traduzir texto usando o API de Tradução de Texto (somente versão 3).

O recurso também pode ser usado para personalizar a tradução de fala quando usado com a [fala de serviços cognitivas](https://docs.microsoft.com/azure/cognitive-services/speech-service/).

## <a name="custom-translator"></a>Custom Translator

Com o tradutor personalizado, você pode criar sistemas de tradução neural que compreendam a terminologia usada em seu próprio negócio e setor. O sistema de tradução personalizado será integrado a aplicativos, fluxos de trabalho e sites existentes.

### <a name="how-does-it-work"></a>Como funciona?

Use seus documentos traduzidos anteriormente (leaflets, páginas da Web, documentação, etc.) para criar um sistema de tradução que reflita sua terminologia e estilo específicos de domínio, melhor do que um sistema de tradução padrão. Os usuários podem carregar documentos TMX, XLIFF, TXT, DOCX e XLSX.  

O sistema também aceita dados que são paralelos no nível do documento, mas que ainda não estão alinhados no nível da frase. Se os usuários tiverem acesso a versões do mesmo conteúdo em vários idiomas, mas em documentos separados, o tradutor personalizado será capaz de corresponder automaticamente frases entre documentos.  O sistema também pode usar dados multilíngues em uma ou em ambas as linguagens para complementar os dados de treinamento paralelo para melhorar as traduções.

Em seguida, o sistema personalizado é disponibilizado por meio de uma chamada regular para o Microsoft API de Tradução de Texto usando o parâmetro Category.

Considerando o tipo apropriado e a quantidade de dados de treinamento, não é incomum esperar ganhos entre 5 e 10, ou ainda mais pontos BLEU na qualidade da tradução usando o tradutor personalizado.

Mais detalhes sobre os vários níveis de personalização com base nos dados disponíveis podem ser encontrados no [Guia do usuário do tradutor personalizado](https://aka.ms/CustomTranslatorDocs).


## <a name="microsoft-translator-hub"></a>Hub do Microsoft Translator

> [!NOTE]
> O Hub herdado do Microsoft Translator será desativado em 17 de maio de 2019. [Exiba datas e informações importantes de migração](https://www.microsoft.com/translator/business/hub/).  

## <a name="custom-translator-versus-hub"></a>Tradutor personalizado em comparação com o Hub

|   | **Hub** | **Tradutor personalizado**|
|:-----|:----:|:----:|
|Status do recurso de personalização   | Disponibilidade Geral  | Disponibilidade Geral |
| Versão de API de texto  | Somente v2   | Somente v3 |
| Personalização de SMT | Sim   | Não |
| Personalização de NMT | Não    | Sim |
| Nova personalização de serviços de voz unificada | Não    | Sim |
| [Sem rastreamento](https://www.aka.ms/notrace) | Sim  | Sim |

## <a name="collaborative-translations-framework"></a>Estrutura de traduções colaborativas

> [!NOTE]
> A partir de 1º de fevereiro de 2018, addtranslation () e AddTranslationArray () não estão mais disponíveis para uso com o API de Tradução de Texto V 2.0. Esses métodos falharão e nada será gravado. O API de Tradução de Texto V 3.0 não oferece suporte a esses métodos.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Configurar um sistema de idiomas personalizado usando o tradutor personalizado](https://aka.ms/CustomTranslatorDocs)
