---
title: O que é o Tradutor Personalizado?
titleSuffix: Azure Cognitive Services
description: O Custom Tradutor oferece capacidades semelhantes ao que o Microsoft Tradutor Hub faz para a Tradução Automática Estatística (SMT), mas exclusivamente para sistemas de Tradução de Máquinas Neurais (NMT).
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 12/09/2019
ms.author: swmachan
ms.topic: overview
ms.openlocfilehash: d84a0190e13d7bb7664e8792e0047338fe4bf5e2
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75982694"
---
# <a name="what-is-custom-translator"></a>O que é o Tradutor Personalizado?

[Custom Tradutor](https://portal.customtranslator.azure.ai) é uma funcionalidade do serviço Microsoft Tradutor, que permite às empresas tradutoras, desenvolvedores de aplicações e fornecedores de serviços linguísticos construir sistemas de tradução de máquinas neurais personalizadas (NMT). Os sistemas de tradução personalizados integram-se perfeitamente em aplicações, fluxos de trabalho e websites existentes. [O Custom Tradutor](https://portal.customtranslator.azure.ai/) oferece capacidades semelhantes ao que o [Microsoft Tradutor Hub](https://hub.microsofttranslator.com/) faz para a Tradução Automática Estatística (SMT), mas exclusivamente para sistemas de Tradução de Máquinas Neurais (NMT).

Os sistemas de tradução construídos com [tradutor personalizado](https://portal.customtranslator.azure.ai) estão disponíveis através do mesmo cloud baseado, [seguro,](https://cognitive.uservoice.com/knowledgebase/articles/1147537-api-and-customization-confidentiality)alto desempenho, altamente escalável Microsoft Tradutor [Text API V3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl), que alimenta milhares de milhões de traduções todos os dias.

O Custom Tradutor suporta mais de três dezenas de idiomas, e mapeia diretamente para os idiomas disponíveis para NMT. Para obter uma lista completa, consulte [os idiomas do Microsoft Tradutor](https://docs.microsoft.com/azure/cognitive-services/translator/language-support#customization).

## <a name="features"></a>Funcionalidades

O Custom Tradutor fornece diferentes funcionalidades para construir um sistema de tradução personalizado e, posteriormente, acessá-lo.

|Funcionalidade  |Descrição  |
|---------|---------|
|[Alavancar a tecnologia de tradução da máquina neural](https://www.microsoft.com/translator/blog/2016/11/15/microsoft-translator-launching-neural-network-based-translations-for-all-its-speech-languages/)     |  Melhore a sua tradução alavancando a tradução da máquina neural (NMT) fornecida pelo tradutor personalizado.       |
|[Construa sistemas que conheçam a sua terminologia de negócio](what-are-parallel-documents.md)     |  Personalize e construa sistemas de tradução utilizando documentos paralelos, que compreendam as terminologias utilizadas no seu próprio negócio e indústria.       |
|[Use um dicionário para construir os seus modelos](what-is-dictionary.md)     |   Se não tiver um conjunto de dados de treino, pode treinar um modelo apenas com dados do dicionário.       |
|[Colaborar com os outros](how-to-manage-settings.md#share-your-workspace)     |   Colabore com a sua equipa partilhando o seu trabalho com pessoas diferentes.     |
|[Aceda ao seu modelo de tradução personalizada](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl)     |  O seu modelo de tradução personalizado pode ser acedido a qualquer momento pelas suas aplicações/programas existentes através do Microsoft Tradutor Text API V3.       |

## <a name="get-better-translations"></a>Obtenha melhores traduções

Microsoft Tradutor lançou [Tradução da Máquina Neural (NMT)](https://www.microsoft.com/translator/blog/2016/11/15/microsoft-translator-launching-neural-network-based-translations-for-all-its-speech-languages/) em 2016. A NMT forneceu grandes avanços na qualidade da tradução em relação à tecnologia de [tradução automática estatística (SMT)](https://en.wikipedia.org/wiki/Statistical_machine_translation) padrão da indústria. Como o NMT captura melhor o contexto das frases completas antes de as traduzir, proporciona traduções mais de qualidade, mais sonoras e mais fluentes. [O Custom Tradutor](https://portal.customtranslator.azure.ai) fornece NMT para os seus modelos personalizados, resultando numa melhor qualidade de tradução.

Pode utilizar documentos previamente traduzidos para construir um sistema de tradução. Estes documentos incluem terminologia e estilo específicos de domínio, melhor do que um sistema de tradução padrão. Os utilizadores podem carregar os documentos ALIGN, PDF, LCL, HTML, HTM, XLF, TMX, XLIFF, TXT, DOCX e XLSX.

O Custom Tradutor também aceita dados paralelos ao nível do documento para tornar a recolha e preparação de dados mais eficazes. Se os utilizadores tiverem acesso a versões do mesmo conteúdo em vários idiomas, mas em documentos separados, o Custom Tradutor poderá corresponder automaticamente às frases através de documentos.

Se o tipo apropriado e a quantidade de dados de treino forem fornecidos, não é incomum ver ganhos de [pontuação bleu](what-is-bleu-score.md) entre 5 e 10 pontos usando o Tradutor Personalizado.

## <a name="be-productive-and-cost-effective"></a>Seja produtivo e rentável

Com [o Custom Tradutor,](https://portal.customtranslator.azure.ai)treinar e implementar um sistema personalizado não requer nenhuma habilidade de programação.

Utilizando o portal tradutor [personalizado](https://portal.customtranslator.azure.ai) seguro, os utilizadores podem carregar dados de treino, sistemas de comboio, sistemas de teste e implantá-los para um ambiente de produção através de uma interface intuitiva do utilizador. O sistema estará então disponível para utilização em escala dentro de algumas horas (o tempo real depende do tamanho dos dados de treino).

[O Tradutor Personalizado](https://portal.customtranslator.azure.ai) também pode ser acedido programáticamente através de uma [API dedicada](https://custom-api.cognitive.microsofttranslator.com/swagger/) (atualmente em pré-visualização). A API permite que os utilizadores gerem a criação ou atualização da formação regularmente através da sua própria app ou serviço web.

O custo de utilização de um modelo personalizado para traduzir conteúdo baseia-se no nível de preços da API de Texto tradutor do utilizador. Consulte a página web de preços da [API](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/) do Tradutor de Serviços Cognitivos para obter detalhes do nível de preços.

## <a name="securely-translate-anytime-anywhere-on-all-your-apps-and-services"></a>Traduza de forma segura a qualquer momento, em qualquer lugar em todas as suas aplicações e serviços

Os sistemas personalizados podem ser perfeitamente acedidos e integrados em qualquer produto ou fluxo de trabalho empresarial, e em qualquer dispositivo, através da API de Texto do Tradutor da Microsoft através da tecnologia PADRÃO REST.

## <a name="next-steps"></a>Passos seguintes

- Leia sobre os [detalhes dos preços](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/).

- Com [quickstart](quickstart-build-deploy-custom-model.md) aprender a construir um modelo de tradução em Tradutor Personalizado.
