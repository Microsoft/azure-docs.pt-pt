---
title: Personalização de Tradução - Tradutor
titleSuffix: Azure Cognitive Services
description: Utilize o Microsoft Tradutor Hub para construir o seu próprio sistema de tradução automática utilizando a sua terminologia e estilo preferidos.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: swmachan
ms.openlocfilehash: 95cb4aa5827190abf125669f2423c808cf8c92a5
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2020
ms.locfileid: "94368938"
---
# <a name="customize-your-text-translations"></a>Personalize as suas traduções de texto

O Tradutor Personalizado é uma característica do serviço Tradutor, que permite aos utilizadores personalizar a tradução avançada da máquina neural da Microsoft Tradutor ao traduzir texto usando o Tradutor (apenas versão 3).

A funcionalidade também pode ser usada para personalizar a tradução da fala quando usada com [o Discurso dos Serviços Cognitivos.](../speech-service/index.yml)

## <a name="custom-translator"></a>Custom Translator

Com o Custom Tradutor, pode construir sistemas de tradução neural que compreendam a terminologia utilizada no seu próprio negócio e indústria. O sistema de tradução personalizado irá então integrar-se em aplicações, fluxos de trabalho e websites existentes.

### <a name="how-does-it-work"></a>Como funciona?

Utilize os seus documentos previamente traduzidos (folhetos, páginas web, documentação, etc.) para construir um sistema de tradução que reflita a terminologia e o estilo específicos do seu domínio, melhor do que um sistema de tradução padrão. Os utilizadores podem carregar documentos TMX, XLIFF, TXT, DOCX e XLSX.  

O sistema também aceita dados paralelos ao nível do documento, mas que ainda não estão alinhados ao nível da frase. Se os utilizadores tiverem acesso a versões do mesmo conteúdo em vários idiomas, mas em documentos separados o Custom Tradutor poderá corresponder automaticamente as frases entre documentos.  O sistema também pode usar dados monolingues em ambos os idiomas para complementar os dados de formação paralela para melhorar as traduções.

O sistema personalizado está então disponível através de uma chamada regular para Tradutor utilizando o parâmetro da categoria.

Dado o tipo e quantidade adequados de dados de formação, não é incomum esperar ganhos entre 5 e 10, ou ainda mais pontos BLEU sobre a qualidade da tradução utilizando o Tradutor Personalizado.

Mais detalhes sobre os vários níveis de personalização com base nos dados disponíveis podem ser encontrados no Guia do [Utilizador do Tradutor Personalizado.](./custom-translator/overview.md)


## <a name="microsoft-translator-hub"></a>Microsoft Tradutor Hub

> [!NOTE]
> O legado Microsoft Tradutor Hub será retirado no dia 17 de maio de 2019. [Consulte informações e datas de migração importantes.](https://www.microsoft.com/translator/business/hub/)  

## <a name="custom-translator-versus-hub"></a>Tradutor personalizado contra Hub

| Funcionalidade | Hub | Custom Translator |
| ------- | :-: | :---------------: |
|Estado do recurso de personalização    | Disponibilidade Geral    | Disponibilidade Geral |
| Versão API de texto    | Apenas V2    | Apenas V3 |
| Personalização SMT    | Yes    | No |
| Personalização de NMT    | No    | Yes |
| Nova personalização unificada de serviços de fala    | No    | Yes |
| [Sem vestígios](https://www.aka.ms/notrace) | Yes    | Yes |

## <a name="collaborative-translations-framework"></a>Quadro de Traduções Colaborativas

> [!NOTE]
> A partir de 1 de fevereiro de 2018, a AddTranslation() e a AddTranslationArray já não estão disponíveis para utilização com o Tradutor v2.0. Estes métodos falharão e nada será escrito. O tradutor v3.0 não apoia estes métodos.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Crie um sistema de linguagem personalizado utilizando o Tradutor Personalizado](./custom-translator/overview.md)