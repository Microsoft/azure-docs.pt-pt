---
title: Personalização de Tradução - API de Texto tradutor
titleSuffix: Azure Cognitive Services
description: Utilize o Microsoft Tradutor Hub para construir o seu próprio sistema de tradução automática utilizando a sua terminologia e estilo preferidos.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: be9c06d45da4d83c26f82343c9cb7b19ba19b4ae
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "71257631"
---
# <a name="customize-your-text-translations"></a>Personalize as suas traduções de texto

O Microsoft Custom Tradutor é uma funcionalidade do serviço Microsoft Tradutor, que permite aos utilizadores personalizar a tradução avançada da máquina neural do Microsoft Tradutor ao traduzir texto utilizando o Tradutor Text API (apenas na versão 3).

A funcionalidade também pode ser usada para personalizar a tradução da fala quando usada com discurso de [serviços cognitivos.](https://docs.microsoft.com/azure/cognitive-services/speech-service/)

## <a name="custom-translator"></a>Custom Translator

Com o Custom Tradutor, pode construir sistemas de tradução neural que compreendam a terminologia utilizada no seu próprio negócio e indústria. O sistema de tradução personalizado irá então integrar-se em aplicações, fluxos de trabalho e websites existentes.

### <a name="how-does-it-work"></a>Como funciona?

Utilize os seus documentos previamente traduzidos (folhetos, páginas web, documentação, etc.) para construir um sistema de tradução que reflita a sua terminologia e estilo específicos de domínio, melhor do que um sistema de tradução padrão. Os utilizadores podem carregar documentos TMX, XLIFF, TXT, DOCX e XLSX.  

O sistema também aceita dados paralelos ao nível do documento, mas que ainda não estão alinhados ao nível da frase. Se os utilizadores tiverem acesso a versões do mesmo conteúdo em vários idiomas, mas em documentos separados o Custom Tradutor poderá corresponder automaticamente às frases através de documentos.  O sistema também pode utilizar dados monolingues em ambos os idiomas para complementar os dados de formação paralelos para melhorar as traduções.

O sistema personalizado está então disponível através de uma chamada regular para a API de Texto do Tradutor da Microsoft utilizando o parâmetro da categoria.

Dado o tipo adequado e a quantidade de dados de formação, não é incomum esperar ganhos entre 5 e 10, ou ainda mais pontos BLEU na qualidade de tradução utilizando o Custom Tradutor.

Mais detalhes sobre os vários níveis de personalização com base nos dados disponíveis podem ser encontrados no Guia de [Utilizador tradutor personalizado.](https://aka.ms/CustomTranslatorDocs)


## <a name="microsoft-translator-hub"></a>Hub de tradutor da Microsoft

> [!NOTE]
> O legado Microsoft Tradutor Hub será retirado no dia 17 de maio de 2019. [Ver informações e datas importantes da migração.](https://www.microsoft.com/translator/business/hub/)  

## <a name="custom-translator-versus-hub"></a>Tradutor personalizado versus Hub

|   | **Hub** | **Custom Translator**|
|:-----|:----:|:----:|
|Estatuto de Funcionalidade de Personalização   | Disponibilidade Geral  | Disponibilidade Geral |
| Versão API de texto  | Apenas V2   | Apenas V3 |
| Personalização SMT | Sim   | Não |
| Personalização NMT | Não    | Sim |
| Nova personalização unificada dos serviços de Fala | Não    | Sim |
| [Sem vestígios](https://www.aka.ms/notrace) | Sim  | Sim |

## <a name="collaborative-translations-framework"></a>Quadro de Traduções Colaborativas

> [!NOTE]
> A partir de 1 de fevereiro de 2018, a AddTranslation() e a AddTranslationArray() deixaram de estar disponíveis para utilização com o Texto Tradutor API V2.0. Estes métodos falharão e nada será escrito. O Texto tradutor API V3.0 não suporta estes métodos.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar um sistema de idiomas personalizado usando tradutor personalizado](https://aka.ms/CustomTranslatorDocs)
