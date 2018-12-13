---
title: Personalização de tradução - API de texto do tradutor
titlesuffix: Azure Cognitive Services
description: Utilize o Microsoft Translator Hub para criar seu próprio sistema de tradução automática com a sua terminologia preferencial e estilo.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: article
ms.date: 05/10/2018
ms.author: v-jansko
ms.openlocfilehash: f02c495c7448334529de2d5b42cda02206daea0d
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/04/2018
ms.locfileid: "52877977"
---
# <a name="customize-your-text-translations"></a>Personalize as suas traduções de texto

O personalizado do Microsoft Translator é a funcionalidade do serviço Microsoft Translator, que permite aos utilizadores personalizar a tradução de máquina de neural avançada de Microsoft Translator, ao traduzir texto usando a API de texto do Translator (apenas a versão 3).

O recurso também pode ser usado para personalizar a tradução de voz quando utilizado com [voz de serviços cognitivos](https://docs.microsoft.com/azure/cognitive-services/speech-service/).

## <a name="custom-translator"></a>Custom Translator

Tradutor personalizada, pode criar sistemas de tradução neural que perceber a terminologia utilizada no seu próprio negócio e do setor. O sistema de tradução personalizados, em seguida, integrará em aplicativos existentes, os fluxos de trabalho e Web sites.

### <a name="how-does-it-work"></a>Como funciona?

Utilize os seus documentos traduzidos anteriormente (leaflets, páginas da Web, documentação, etc.) para criar um sistema de tradução que reflete a sua terminologia específicas de domínio e o estilo, melhor do que um sistema de tradução genérico. Os utilizadores podem carregar documentos TMX, XLIFF, TXT, DOCX e XLSX.  

O sistema também aceita dados que seja paralela em nível de documento, mas ainda não estão alinhados ao nível da sentença. Se os utilizadores têm acesso a versões do mesmo conteúdo em vários idiomas, mas em documentos separados Translator personalizada poderá corresponder automaticamente frases entre documentos.  O sistema também pode utilizar dados monolíngües num ou ambos os idiomas para complementar os dados de preparação paralela para melhorar as traduções.

Sistema personalizado, em seguida, está disponível através de uma chamada normal à API de texto do Translator do Microsoft utilizando o parâmetro de categoria.

Fornecido do tipo adequado e a quantidade de dados de treinamento não é incomum esperar ganhos entre 5 e 10 ou BLEU ainda mais pontos na qualidade das traduções com o Translator personalizado.

Podem encontrar mais detalhes sobre os diversos níveis de personalização com base nos dados disponíveis no [Guia do usuário do Microsoft Translator personalizado](https://aka.ms/CustomTranslatorDocs).


## <a name="microsoft-translator-hub"></a>Hub de Microsoft Translator

O Microsoft Translator Hub herdados podem ser usado para converter estatísticos de tradução automática. [Saiba mais](https://www.microsoft.com/en-us/translator/hub.aspx)

## <a name="custom-translator-versus-hub"></a>Tradutor personalizado em comparação com o Hub

|   | **Hub** | **Tradutor personalizado**|
|:-----|:----:|:----:|
|Estado da funcionalidade de personalização   | Disponibilidade Geral  | Disponibilidade Geral |
| Versão de API de texto  | Apenas v2   | Apenas v3 |
| Personalização de SMT | Sim   | Não |
| Personalização de NMT | Não    | Sim |
| Nova personalização de serviços de voz unificada | Não    | Sim |
| [Sem rastreio](https://www.aka.ms/notrace) | Sim  | Sim |

## <a name="collaborative-translations-framework"></a>Framework de traduções de colaboração

> [!NOTE]
> A partir de 1 de Fevereiro de 2018, AddTranslation() e AddTranslationArray() já não estão disponíveis para utilização com a API de texto de tradutor versão 2.0. Esses métodos falhará e nada será gravado. O V3.0 de API de texto do tradutor não suporta estes métodos.

>Uma funcionalidade semelhante está disponível na API de Hub do Translator. Ver [ https://hub.microsofttranslator.com/swagger ](https://hub.microsofttranslator.com/swagger).

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Configurar um sistema de idioma personalizado com o Translator personalizado](https://aka.ms/CustomTranslatorDocs)
