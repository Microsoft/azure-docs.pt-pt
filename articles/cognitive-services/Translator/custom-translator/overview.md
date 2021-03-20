---
title: O que é o Tradutor Personalizado?
titleSuffix: Azure Cognitive Services
description: O Custom Tradutor oferece capacidades semelhantes ao que o Microsoft Tradutor Hub faz para tradução automática estatística (SMT), mas exclusivamente para sistemas de Tradução Automática Neural (NMT).
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 12/09/2019
ms.author: lajanuar
ms.topic: overview
ms.openlocfilehash: 89ab25f45cd35a86d2ff4416ceb851ae6cd5e115
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104657166"
---
# <a name="what-is-custom-translator"></a>O que é o Tradutor Personalizado?

[Custom Tradutor](https://portal.customtranslator.azure.ai) é uma característica do serviço Microsoft Tradutor, que permite às empresas de Tradutor, desenvolvedores de aplicações e prestadores de serviços linguísticos construir sistemas de tradução automática neural (NMT) personalizados. Os sistemas de tradução personalizados integram-se perfeitamente em aplicações, fluxos de trabalho e websites existentes.

Os sistemas de tradução construídos com [Tradutor Personalizado](https://portal.customtranslator.azure.ai) estão disponíveis através do mesmo [texto API V3,](../reference/v3-0-translate.md?tabs=curl)baseado em nuvem, [seguro,](https://cognitive.uservoice.com/knowledgebase/articles/1147537-api-and-customization-confidentiality)de alto desempenho, altamente escalável do Texto do Tradutor da Microsoft, que alimenta milhares de milhões de traduções todos os dias.

O Custom Tradutor suporta mais de três dezenas de idiomas, e mapeia diretamente para as línguas disponíveis para NMT. Para obter uma lista completa, consulte [os idiomas do tradutor da Microsoft.](../language-support.md#customization)

Esta documentação contém os seguintes tipos de artigos:

* [**Os quickstarts**](quickstart-build-deploy-custom-model.md) estão a iniciar instruções para guiá-lo através da realização de pedidos ao serviço.  
* [**Os guias de como**](how-to-create-project.md) fazer contêm instruções para utilizar a funcionalidade de formas mais específicas ou personalizadas.  
* [**Os conceitos**](workspace-and-project.md) fornecem explicações aprofundadas sobre a funcionalidade.  


## <a name="features"></a>Funcionalidades

O Custom Tradutor fornece diferentes funcionalidades para construir um sistema de tradução personalizado e, mais tarde, acede-lo.

|Funcionalidade  |Descrição  |
|---------|---------|
|[Aplicar tecnologia de tradução de máquinas neurais](https://www.microsoft.com/translator/blog/2016/11/15/microsoft-translator-launching-neural-network-based-translations-for-all-its-speech-languages/)     |  Melhore a sua tradução aplicando a tradução de máquinas neurais (NMT) fornecida pelo tradutor personalizado.       |
|[Construa sistemas que conheçam a sua terminologia de negócio](what-are-parallel-documents.md)     |  Personalize e construa sistemas de tradução usando documentos paralelos, que compreendam as terminologias utilizadas no seu próprio negócio e indústria.       |
|[Use um dicionário para construir os seus modelos](what-is-dictionary.md)     |   Se não tiver um conjunto de dados de treino, pode treinar um modelo apenas com dados de dicionário.       |
|[Colaborar com outros](how-to-manage-settings.md#share-your-workspace)     |   Colabore com a sua equipa partilhando o seu trabalho com pessoas diferentes.     |
|[Aceda ao seu modelo de tradução personalizado](../reference/v3-0-translate.md?tabs=curl)     |  O seu modelo de tradução personalizado pode ser acedido a qualquer momento pelas aplicações/programas existentes através do Microsoft Tradutor Text API V3.       |

## <a name="get-better-translations"></a>Obtenha melhores traduções

A Microsoft Tradutor lançou [a Neural Machine Translation (NMT)](https://www.microsoft.com/translator/blog/2016/11/15/microsoft-translator-launching-neural-network-based-translations-for-all-its-speech-languages/) em 2016. A NMT forneceu grandes avanços na qualidade da tradução em relação à tecnologia [de tradução automática (SMT)](https://en.wikipedia.org/wiki/Statistical_machine_translation) padrão da indústria. Como o NMT melhor captura o contexto de frases completas antes de as traduzir, proporciona traduções mais elevadas, mais sonoras humanas e mais fluentes. [O Custom Tradutor](https://portal.customtranslator.azure.ai) fornece NMT para os seus modelos personalizados, resultando numa melhor qualidade de tradução.

Pode utilizar documentos previamente traduzidos para construir um sistema de tradução. Estes documentos incluem terminologia e estilo específicos de domínio, melhor do que um sistema de tradução padrão. Os utilizadores podem carregar documentos ALIGN, PDF, LCL, HTML, HTM, XLF, TMX, XLIFF, TXT, DOCX e XLSX.

O Custom Tradutor também aceita dados paralelos ao nível do documento para tornar a recolha e preparação de dados mais eficazes. Se os utilizadores tiverem acesso a versões do mesmo conteúdo em vários idiomas, mas em documentos separados, o Custom Tradutor poderá combinar automaticamente as frases entre documentos.

Se for fornecido o tipo e a quantidade adequado de dados de treino, não é incomum ver ganhos de [pontuação BLEU](what-is-bleu-score.md) entre 5 e 10 pontos utilizando o Custom Tradutor.

## <a name="be-productive-and-cost-effective"></a>Seja produtivo e rentável

Com [o Custom Tradutor,](https://portal.customtranslator.azure.ai)treinar e implementar um sistema personalizado não requer nenhuma capacidade de programação.

Utilizando o portal [secure Custom Tradutor,](https://portal.customtranslator.azure.ai) os utilizadores podem carregar dados de treino, sistemas de comboio, sistemas de teste e implantá-los num ambiente de produção através de uma interface de utilizador intuitiva. O sistema estará então disponível para ser utilizado à escala dentro de algumas horas (o tempo real depende do tamanho dos dados de treino).

[O Tradutor Personalizado](https://portal.customtranslator.azure.ai) também pode ser acedido programáticamente através de uma [API dedicada](https://custom-api.cognitive.microsofttranslator.com/swagger/) (atualmente em pré-visualização). A API permite que os utilizadores consigam gerir a criação ou atualização da formação através da sua própria app ou webservice.

O custo da utilização de um modelo personalizado para traduzir conteúdo baseia-se no nível de preços da API de texto do utilizador. Consulte a página de preços da [API do Tradutor](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/) de Serviços Cognitivos para obter detalhes do nível de preços.

## <a name="securely-translate-anytime-anywhere-on-all-your-apps-and-services"></a>Traduzir de forma segura a qualquer momento, em qualquer lugar em todas as suas aplicações e serviços

Os sistemas personalizados podem ser acedidos e integrados perfeitamente em qualquer produto ou fluxo de trabalho de negócio, e em qualquer dispositivo, através da API de Texto do Microsoft Tradutor através da tecnologia padrão REST.

## <a name="next-steps"></a>Passos seguintes

- Leia sobre os [detalhes dos preços](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/).

- Com [o Quickstart](quickstart-build-deploy-custom-model.md) aprenda a construir um modelo de tradução em Custom Tradutor.
