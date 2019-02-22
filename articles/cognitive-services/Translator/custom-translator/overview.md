---
title: O que é o Tradutor Personalizado?
titleSuffix: Azure Cognitive Services
description: Tradutor personalizado oferece recursos semelhantes para o que o Microsoft Translator Hub faz para estatísticas tradução automática (SMT), mas exclusivamente para sistemas de tradução automática neuronal (NMT).
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: custom-translator
ms.date: 11/13/2018
ms.author: v-rada
ms.topic: overview
ms.openlocfilehash: 852a16f14466d91a0ebfaadb83c674253d0ea15e
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56587757"
---
# <a name="what-is-custom-translator"></a>O que é o Tradutor Personalizado?

[Tradutor personalizado](https://portal.customtranslator.azure.ai) é uma funcionalidade do serviço Microsoft Translator, que habilita as empresas de Microsoft Translator, os desenvolvedores de aplicativos, e personalizados, provedores de serviço de linguagem para criar sistemas de tradução automática neuronal (NMT). Os sistemas de tradução personalizados integrasse perfeitamente a aplicativos existentes, os fluxos de trabalho e Web sites. [Tradutor personalizado](https://portal.customtranslator.azure.ai/) oferece recursos semelhantes ao que [Hub do Microsoft Translator](https://hub.microsofttranslator.com/) faz para estatísticas tradução automática (SMT), mas exclusivamente para sistemas de tradução automática neuronal (NMT).

Sistemas de tradução criados com [personalizado Translator](https://portal.customtranslator.azure.ai) estão disponíveis através da mesma com base na cloud [seguro](https://cognitive.uservoice.com/knowledgebase/articles/1147537-api-and-customization-confidentiality), alto desempenho, altamente dimensionável Microsoft Translator [V3 de API de texto](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl), que capacita a milhares de milhões de traduções todos os dias. 

Tradutor personalizado oferece suporte a mais de três dezenas de linguagens e é mapeado diretamente para os idiomas disponíveis para NMT. Para obter uma lista completa, consulte [idiomas do Microsoft Translator](https://docs.microsoft.com/azure/cognitive-services/translator/language-support#customization).

## <a name="features"></a>Funcionalidades

Tradutor personalizado fornece diferentes funcionalidades para criar o sistema de tradução personalizadas e, em seguida, aceder ao mesmo.

|Funcionalidade  |Descrição  |
|---------|---------|
|[Tirar partido de tecnologia de tradução automática neural](https://blogs.msdn.microsoft.com/translation/2016/11/15/microsoft-translator-launching-neural-network-based-translations-for-all-its-speech-languages/)     |  Aprimorar a tradução ao tirar partido da tradução automática neuronal (NMT) fornecida pelo translator personalizado.       |
|[Criar sistemas que sabe seu terminologia de negócios](what-are-parallel-documents.md)     |  Personalizar e criar sistemas de tradução com documentos paralelos, o que compreender as terminologias utilizadas no seu próprio negócio e do setor.       |
|[Utilizar um dicionário para criar os seus modelos](what-is-dictionary.md)     |   Se não tiver o conjunto de dados de treinamento, pode preparar um modelo com apenas os dados de dicionário.       |
|[Colaborar com outras pessoas](how-to-manage-settings.md#share-your-workspace)     |   Colabore com sua equipe ao partilhar o seu trabalho com diferentes pessoas.     |
|[Aceder ao seu modelo de tradução personalizadas](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl)     |  Seu modelo de tradução personalizadas pode ser acessado por seus aplicativos existentes / programas por meio da API de texto do Microsoft Translator V3.       |

## <a name="get-better-translations"></a>Obter o melhor traduções

Lançamento do Microsoft Translator [tradução automática neuronal (NMT)](https://blogs.msdn.microsoft.com/translation/2016/11/15/microsoft-translator-launching-neural-network-based-translations-for-all-its-speech-languages/) em 2016. NMT fornecido grandes avanços na qualidade das traduções através da norma da indústria [estatísticos de tradução automática (SMT)](https://en.wikipedia.org/wiki/Statistical_machine_translation) tecnologia. Uma vez que NMT melhor captura o contexto de frases completos antes de sua tradução, fornece mais humanos com o som e mais fluentes traduções de melhor qualidade. [Tradutor personalizado](https://portal.customtranslator.azure.ai) fornece NMT para sua personalizado modela resultante melhor qualidade das traduções.

Pode utilizar documentos traduzidos anteriormente para criar um sistema de tradução. Esses documentos incluem terminologia específicas de domínio e de estilo, melhor do que um sistema de tradução genérico. Os utilizadores podem carregar documentos ALIGN, PDF, LCL, HTML, HTM, XLF, TMX, XLIFF, TXT, DOCX e XLSX.

Tradutor personalizado também aceita dados que seja paralelos em nível de documento para tornar a recolha de dados e a preparação mais eficientes. Se os utilizadores têm acesso a versões do mesmo conteúdo em vários idiomas, mas em documentos separados, Translator personalizada poderá corresponder automaticamente frases entre documentos.

Se o tipo apropriado e a quantidade de dados de treinamento for fornecido, não é incomum ver [pontuação BLEU](what-is-bleu-score.md) ganhos entre 5 e 10 pontos com o Translator personalizado.

## <a name="be-productive-and-cost-effective"></a>Ser produtivos e rentável

Com o [Translator personalizado](https://portal.customtranslator.azure.ai), treinamento e implementar um sistema personalizado não requerem habilidades de programação. 

Usando a segura [Translator personalizado](https://portal.customtranslator.azure.ai) portal, os utilizadores podem carregar dados de treinamento, preparar sistemas, testar sistemas e implementá-las num ambiente de produção através de uma interface de utilizador intuitiva. O sistema estará disponível para utilização em escala em algumas horas (tempo em questão depende do tamanho dos dados de treinamento).

[Tradutor personalizado](https://portal.customtranslator.azure.ai) também pode ser acessada por meio de programação através de um [dedicado API](https://custom-api.cognitive.microsofttranslator.com/swagger/) (atualmente em pré-visualização). A API permite aos utilizadores gerir a criar ou atualizar o treinamento em intervalos regulares através de sua própria aplicação ou serviço Web.

O custo de utilização de um modelo personalizado para traduzir conteúdo baseia-se no escalão de preço de API de texto do Translator do utilizador. Ver os serviços cognitivos [API de texto do Translator preços página Web](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/) para detalhes de escalão de preços.

## <a name="securely-translate-anytime-anywhere-on-all-your-apps-and-services"></a>Traduzir com segurança em qualquer altura e em qualquer lugar em todas as suas aplicações e serviços

Sistemas personalizados podem ser facilmente acessados e integrados em qualquer fluxo de trabalho do produto ou de negócios e em qualquer dispositivo, através da API de texto do Microsoft Translator através de uma tecnologia REST padrão.

## <a name="next-steps"></a>Passos Seguintes

- Leia sobre os [detalhes dos preços](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/).

- Com o [guia de introdução](quickstart-build-deploy-custom-model.md) Saiba como criar um modelo de tradução no Translator personalizado.
