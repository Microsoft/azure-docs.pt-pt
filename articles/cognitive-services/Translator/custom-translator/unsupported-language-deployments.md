---
title: Implementações de linguagem não suportado - Translator personalizado
titleSuffix: Azure Cognitive Services
description: Como implementar pares de idiomas não suportado no Translator personalizado.
services: cognitive-services
author: rajdeep-in
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 04/24/2019
ms.author: v-pawal
ms.openlocfilehash: 0938ba2e839be603c557cc2a87dd0c5aa1dfe1e3
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66390623"
---
# <a name="unsupported-language-deployments"></a>Implementações de linguagens não suportadas

<!--Custom Translator provides the highest-quality translations possible using the latest techniques in neural machine learning. While Microsoft intends to make neural training available in all languages, there are some limitations that prevent us from being able to offer neural machine translation in all language pairs.-->  

Com a descontinuação em breve do Microsoft Translator Hub, Microsoft irá ser undeploying todos os modelos que atualmente implementados através do Hub. Muitos de vocês têm modelos implementados no Hub cujos pares de idiomas não são suportadas no Translator personalizado.  Não Queremos que os utilizadores nesta situação não tenham nenhum recurso para traduzir o respetivo conteúdo.

Agora, temos um processo que permite-lhe implementar os seus modelos não suportados por meio do Microsoft Translator personalizado.  Este processo permite-lhe continuar a traduzir conteúdo usando a API V3 mais recente.  Esses modelos serão alojados até optar por anular a implementação-los ou o par de idioma torna-se disponíveis no Translator personalizado.  Este artigo explica o processo para implementar modelos com pares de idiomas não suportado.

## <a name="prerequisites"></a>Pré-requisitos

Por ordem para seus modelos para ser candidatos para a implementação, tem de cumprir os seguintes critérios:
* O projeto que contém o modelo tem tiverem sido migrado do Hub para o tradutor personalizada usando a ferramenta de migração.  O processo de migração de projetos e áreas de trabalho pode ser encontrado [aqui](how-to-migrate.md).
* O modelo tem de ser em estado de implementação quando ocorre a migração.  
* O par de linguagem do modelo tem de ser um par de idioma não suportado no Translator personalizado.  Pares de idiomas em que uma linguagem é suportada para ou do inglês, mas o par em si não inclui o inglês, são candidatos para implementações de linguagem não suportada.  Por exemplo, um modelo de Hub para um francês a par de idioma alemão é considerado um par de entanto francês para inglês e em inglês para alemão são de idioma suportado do mesmo par do idioma não suportado.

## <a name="process"></a>Process
Depois de ter migrado os modelos do Hub de que são candidatos para a implementação, pode encontrá-los ao aceder a **configurações** page de sua área de trabalho e de rolagem ao fim da página onde poderá ver um **não suportado Treinamentos do Microsoft Translator Hub** secção.  Esta secção só é apresentada se tiver projetos que cumprem os pré-requisitos mencionados acima.

![Como migrar a partir do Hub](media/unsupported-language-deployments/unsupported-translator-hub-trainings.jpg)

Dentro de **Treinamentos de Hub não suportada do Microsoft Translator** página de seleção, o **Unrequested treinamentos** separador contém modelos que são elegíveis para a implementação.  Selecione os modelos que pretende implementar e submeter um pedido.   Antes do prazo de implementação 30 de Abril, pode selecionar o número de modelos como desejar para implementação.
 
![Como migrar a partir do Hub](media/unsupported-language-deployments/unsupported-translator-hub-trainings-list.jpg)

Depois de submeter, o modelo já não estará disponível na **Unrequested treinamentos** separador e em vez disso, será apresentada no **solicitado treinamentos** separador.  Pode ver o seu pedidos treinamentos em qualquer altura.

![Como migrar a partir do Hub](media/unsupported-language-deployments/request-unsupported-trainings.jpg) 

## <a name="whats-next"></a>Passos seguintes?

Os modelos selecionados para implementação serão guardados depois do Hub é desativado e todos os modelos são não implementados.  Tem até 24 de Maio para submeter pedidos para a implementação de modelos não suportados.  Vamos implementar esses modelos 15 de Junho, altura em que estarão acessíveis através da API do Microsoft Translator V3.  Além disso, vai estar disponíveis por meio da API V2 até 1 de Julho.  

Para obter mais informações sobre datas importantes na remoção da verificação de Hub [aqui](https://www.microsoft.com/translator/business/hub/).
Serão aplicáveis os custos de alojamento de normais, após a implantação.  Ver [preços](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/) para obter detalhes.  

Ao contrário dos modelos de tradutor de personalizada padrão, os modelos de Hub só estará disponíveis numa única região, para que os custos de alojamento de várias regiões não serão aplicada.  Uma vez implantado, poderá anular a implementação de seu modelo de Hub em qualquer altura através do projeto personalizado Translator migrado.

## <a name="next-steps"></a>Passos Seguintes

- [Preparar um modelo](how-to-train-model.md).
- Começar a utilizar o seu modelo de tradução personalizadas implementado via [API de texto do Microsoft Translator V3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl).
