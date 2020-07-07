---
title: O que é o Tradutor Personalizado?
titleSuffix: Azure Cognitive Services
description: O Custom Tradutor oferece capacidades semelhantes ao que o Microsoft Tradutor Hub faz para tradução automática estatística (SMT), mas exclusivamente para sistemas de Tradução Automática Neural (NMT).
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 05/26/2020
ms.author: swmachan
ms.topic: overview
ms.openlocfilehash: d78767474150bc9571b25fe1f26135d6f41d1f20
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2020
ms.locfileid: "85961239"
---
# <a name="what-is-custom-translator"></a>O que é o Tradutor Personalizado?

[O Custom Tradutor](https://portal.customtranslator.azure.ai) é uma característica do serviço Tradutor, que permite às empresas, desenvolvedores de aplicações e prestadores de serviços linguísticos construir sistemas de tradução automática neural (NMT) personalizados. Os sistemas de tradução personalizados integram-se perfeitamente em aplicações, fluxos de trabalho e websites existentes. 

Os sistemas de tradução construídos com [Tradutor Personalizado](https://portal.customtranslator.azure.ai) estão disponíveis através do mesmo Tradutor Azure, baseado na nuvem, [seguro,](https://cognitive.uservoice.com/knowledgebase/articles/1147537-api-and-customization-confidentiality)de alto desempenho, altamente escalável [Azure Cognitive Services Tradutor V3,](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl)que alimenta milhares de milhões de traduções todos os dias.

O Custom Tradutor suporta mais de três dezenas de idiomas, e mapeia diretamente para as línguas disponíveis para NMT. Para obter uma lista completa, consulte [As Línguas Tradutoras.](https://docs.microsoft.com/azure/cognitive-services/translator/language-support#customization)

## <a name="features"></a>Funcionalidades

O Custom Tradutor fornece diferentes funcionalidades para construir um sistema de tradução personalizado e, posteriormente, aceder ao mesmo.

|Funcionalidade  |Description  |
|---------|---------|
|[Alavancar a tecnologia de tradução de máquinas neurais](https://www.microsoft.com/translator/blog/2016/11/15/microsoft-translator-launching-neural-network-based-translations-for-all-its-speech-languages/)     |  Melhore a sua tradução aproveitando a tradução da máquina neural (NMT) fornecida pelo tradutor personalizado.       |
|[Construa sistemas que conheçam a sua terminologia de negócio](what-are-parallel-documents.md)     |  Personalize e construa sistemas de tradução usando documentos paralelos, que compreendam as terminologias utilizadas no seu próprio negócio e indústria.       |
|[Use um dicionário para construir os seus modelos](what-is-dictionary.md)     |   Se não tiver um conjunto de dados de treino, pode treinar um modelo apenas com dados de dicionário.       |
|[Colaborar com outros](how-to-manage-settings.md#share-your-workspace)     |   Colabore com a sua equipa partilhando o seu trabalho com pessoas diferentes.     |
|[Aceda ao seu modelo de tradução personalizado](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl)     |  O seu modelo de tradução personalizado pode ser acedido a qualquer momento pelas aplicações/programas existentes através do Tradutor V3.       |

## <a name="get-better-translations"></a>Obtenha melhores traduções

Tradutor lançou [a Neural Machine Translation (NMT)](https://www.microsoft.com/translator/blog/2016/11/15/microsoft-translator-launching-neural-network-based-translations-for-all-its-speech-languages/) em 2016. A NMT forneceu grandes avanços na qualidade da tradução em relação à tecnologia [de tradução automática (SMT)](https://en.wikipedia.org/wiki/Statistical_machine_translation) padrão da indústria. Como o NMT melhor captura o contexto de frases completas antes de as traduzir, proporciona traduções mais elevadas, mais sonoras humanas e mais fluentes. [O Custom Tradutor](https://portal.customtranslator.azure.ai) fornece NMT para os seus modelos personalizados, resultando numa melhor qualidade de tradução.

Pode utilizar documentos previamente traduzidos para construir um sistema de tradução. Estes documentos incluem terminologia e estilo específicos de domínio, melhor do que um sistema de tradução padrão. Os utilizadores podem carregar documentos ALIGN, PDF, LCL, HTML, HTM, XLF, TMX, XLIFF, TXT, DOCX e XLSX.

O Custom Tradutor também aceita dados paralelos ao nível do documento para tornar a recolha e preparação de dados mais eficazes. Se os utilizadores tiverem acesso a versões do mesmo conteúdo em vários idiomas, mas em documentos separados, o Custom Tradutor poderá combinar automaticamente as frases entre documentos.

Se for fornecido o tipo e a quantidade adequado de dados de treino, não é incomum ver ganhos de [pontuação BLEU](what-is-bleu-score.md) entre 5 e 10 pontos utilizando o Custom Tradutor.

## <a name="be-productive-and-cost-effective"></a>Seja produtivo e rentável

Com [o Custom Tradutor,](https://portal.customtranslator.azure.ai)treinar e implementar um sistema personalizado não requer nenhuma capacidade de programação.

Utilizando o portal [secure Custom Tradutor,](https://portal.customtranslator.azure.ai) os utilizadores podem carregar dados de treino, sistemas de comboio, sistemas de teste e implantá-los num ambiente de produção através de uma interface de utilizador intuitiva. O sistema estará então disponível para ser utilizado à escala dentro de algumas horas (o tempo real depende do tamanho dos dados de treino).

[O Tradutor Personalizado](https://portal.customtranslator.azure.ai) também pode ser acedido programáticamente através de uma [API dedicada](https://custom-api.cognitive.microsofttranslator.com/swagger/) (atualmente em pré-visualização). A API permite que os utilizadores gerem a criação ou atualização regular da formação através da sua própria app ou webservice.

O custo da utilização de um modelo personalizado para traduzir conteúdo baseia-se no nível de preços do Tradutor do utilizador. Consulte a [página de preços](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/) do Tradutor de Serviços Cognitivos para obter detalhes do nível de preços.

## <a name="securely-translate-anytime-anywhere-on-all-your-apps-and-services"></a>Traduzir de forma segura a qualquer momento, em qualquer lugar em todas as suas aplicações e serviços

Os sistemas personalizados podem ser acedidos e integrados perfeitamente em qualquer produto ou fluxo de trabalho de negócio, e em qualquer dispositivo, através da tecnologia padrão REST.

## <a name="next-steps"></a>Passos seguintes

- Leia sobre os [detalhes dos preços](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/).

- Com [o Quickstart](quickstart-build-deploy-custom-model.md) aprenda a construir um modelo de tradução em Custom Tradutor.
