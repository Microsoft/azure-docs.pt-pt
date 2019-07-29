---
title: O que é o Tradutor Personalizado?
titleSuffix: Azure Cognitive Services
description: O tradutor personalizado oferece recursos semelhantes ao que o Hub do Microsoft Translator faz para a conversão de máquina estatística (SMT), mas exclusivamente para sistemas NMT (conversão de máquina neural).
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: overview
ms.openlocfilehash: cdbbe9006f57c8b29e19fda85eefc0795da95a00
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595500"
---
# <a name="what-is-custom-translator"></a>O que é o Tradutor Personalizado?

O [Tradutor personalizado](https://portal.customtranslator.azure.ai) é um recurso do serviço Microsoft Translator, que permite que as empresas do tradutor, os desenvolvedores de aplicativos e os provedores de serviços de linguagem criem sistemas personalizados de NMT (conversão de máquina neural). Os sistemas de tradução personalizados integram-se perfeitamente a aplicativos, fluxos de trabalho e sites existentes. O [Tradutor personalizado](https://portal.customtranslator.azure.ai/) oferece recursos semelhantes ao que o [Hub do Microsoft Translator](https://hub.microsofttranslator.com/) faz para a conversão de máquina estatística (SMT), mas exclusivamente para sistemas NMT (conversão de máquina neural).

Os sistemas de tradução criados com o [Tradutor personalizado](https://portal.customtranslator.azure.ai) estão disponíveis por meio da mesma [API de texto](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl)do Microsoft Translator baseada em nuvem, [segura](https://cognitive.uservoice.com/knowledgebase/articles/1147537-api-and-customization-confidentiality), de alto desempenho e altamente escalonável, que alimenta bilhões de traduções todos os dias.

O tradutor personalizado dá suporte a mais de três dúzias de idiomas e mapeia diretamente para os idiomas disponíveis para NMT. Para obter uma lista completa, consulte [idiomas do Microsoft Translator](https://docs.microsoft.com/azure/cognitive-services/translator/language-support#customization).

## <a name="features"></a>Funcionalidades

O tradutor personalizado fornece recursos diferentes para criar um sistema de tradução personalizado e, subsequentemente, acessá-lo.

|Funcionalidade  |Descrição  |
|---------|---------|
|[Aproveite a tecnologia de tradução da máquina neural](https://blogs.msdn.microsoft.com/translation/2016/11/15/microsoft-translator-launching-neural-network-based-translations-for-all-its-speech-languages/)     |  Aprimore sua tradução aproveitando a NMT (conversão de máquina neural) fornecida pelo Tradutor personalizado.       |
|[Crie sistemas que conhecem sua terminologia de negócios](what-are-parallel-documents.md)     |  Personalize e crie sistemas de tradução usando documentos paralelos, que compreendem as terminologias usadas em seu próprio negócio e setor.       |
|[Usar um dicionário para criar seus modelos](what-is-dictionary.md)     |   Se você não tiver um conjunto de dados de treinamento, poderá treinar um modelo com apenas os dados do dicionário.       |
|[Colabore com outras pessoas](how-to-manage-settings.md#share-your-workspace)     |   Colabore com sua equipe compartilhando seu trabalho com pessoas diferentes.     |
|[Acessar seu modelo de tradução personalizado](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl)     |  Seu modelo de tradução personalizado pode ser acessado a qualquer momento por seus aplicativos/programas existentes por meio do Microsoft API de Tradução de Texto v3.       |

## <a name="get-better-translations"></a>Obter traduções melhores

O Microsoft Translator liberou a [conversão de máquina neural (NMT)](https://blogs.msdn.microsoft.com/translation/2016/11/15/microsoft-translator-launching-neural-network-based-translations-for-all-its-speech-languages/) em 2016. A NMT forneceu grandes avanços na qualidade da tradução em relação à tecnologia [SMT (conversão de máquina estatística)](https://en.wikipedia.org/wiki/Statistical_machine_translation) padrão do setor. Como o NMT captura melhor o contexto de frases completas antes de translada-las, ele fornece maior qualidade, mais sons humanos e mais traduções fluentes. O [Tradutor personalizado](https://portal.customtranslator.azure.ai) fornece NMT para seus modelos personalizados, resultando em melhor qualidade de tradução.

Você pode usar documentos traduzidos anteriormente para criar um sistema de tradução. Esses documentos incluem terminologia e estilo específicos de domínio, melhor do que um sistema de tradução genérico. Os usuários podem carregar documentos ALIGN, PDF, LCL, HTML, HTM, XLF, TMX, XLIFF, TXT, DOCX e XLSX.

O tradutor personalizado também aceita dados que são paralelos no nível do documento para tornar a coleta e a preparação dos dados mais eficientes. Se os usuários tiverem acesso a versões do mesmo conteúdo em vários idiomas, mas em documentos separados, o tradutor personalizado poderá corresponder automaticamente frases entre documentos.

Se o tipo apropriado e a quantidade de dados de treinamento forem fornecidos, não será incomum ver os ganhos de [Pontuação de Bleu](what-is-bleu-score.md) entre 5 e 10 pontos usando o tradutor personalizado.

## <a name="be-productive-and-cost-effective"></a>Seja produtivo e econômico

Com o [Tradutor personalizado](https://portal.customtranslator.azure.ai), o treinamento e a implantação de um sistema personalizado não exigem habilidades de programação.

Usando o portal do [Tradutor personalizado](https://portal.customtranslator.azure.ai) seguro, os usuários podem carregar dados de treinamento, treinar sistemas, testar sistemas e implantá-los em um ambiente de produção por meio de uma interface do usuário intuitiva. O sistema estará disponível para uso em escala em algumas horas (o tempo real depende do tamanho dos dados de treinamento).

O [Tradutor personalizado](https://portal.customtranslator.azure.ai) também pode ser acessado por meio de programação por meio de uma [API dedicada](https://custom-api.cognitive.microsofttranslator.com/swagger/) (atualmente em versão prévia). A API permite que os usuários gerenciem a criação ou atualização de treinamento regularmente por meio de seu próprio aplicativo ou WebService.

O custo de usar um modelo personalizado para traduzir conteúdo é baseado no tipo de preço API de Tradução de Texto do usuário. Consulte a página da [Web de preços de API de tradução de texto](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/) de serviços cognitivas para obter detalhes do tipo de preço.

## <a name="securely-translate-anytime-anywhere-on-all-your-apps-and-services"></a>Traduza com segurança a qualquer momento, em qualquer lugar em todos os seus aplicativos e serviços

Os sistemas personalizados podem ser acessados e integrados diretamente em qualquer produto ou fluxo de trabalho de negócios, e em qualquer dispositivo, por meio do Microsoft API de Tradução de Texto por meio da tecnologia REST padrão.

## <a name="next-steps"></a>Passos Seguintes

- Leia sobre os [detalhes dos preços](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/).

- Com o [início rápido](quickstart-build-deploy-custom-model.md) , aprenda a criar um modelo de tradução no Tradutor personalizado.
