---
title: Implementações linguísticas não suportadas - Tradutor Personalizado
titleSuffix: Azure Cognitive Services
description: Este artigo mostra-lhe como implementar pares linguísticos não suportados no Azure Cognitive Services Custom Tradutor.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 04/24/2019
ms.author: lajanuar
ms.openlocfilehash: fb31388647fc6022a2e6670baf7b3e73f345c36a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98898075"
---
# <a name="unsupported-language-deployments"></a>Implementações de linguagens não suportadas

<!--Custom Translator provides the highest-quality translations possible using the latest techniques in neural machine learning. While Microsoft intends to make neural training available in all languages, there are some limitations that prevent us from being able to offer neural machine translation in all language pairs.-->  

Com a próxima retirada do Microsoft Tradutor Hub, a Microsoft estará a desaplorar todos os modelos atualmente implantados através do Hub. Muitos de vocês têm modelos implantados no Hub cujos pares linguísticos não são suportados em Custom Tradutor.  Não queremos que os utilizadores nesta situação não tenham qualquer recurso para traduzir o seu conteúdo.

Dispomos agora de um processo que lhe permite implementar os seus modelos não suportados através do Tradutor Personalizado.  Este processo permite-lhe continuar a traduzir conteúdos utilizando a mais recente API V3.  Estes modelos serão hospedados até que você opte por desafetá-los ou o par de idiomas fica disponível em Custom Tradutor.  Este artigo explica o processo de implantação de modelos com pares linguísticos não suportados.

## <a name="prerequisites"></a>Pré-requisitos

Para que os seus modelos sejam candidatos à implantação, devem cumprir os seguintes critérios:
* O projeto que contém o modelo deve ter sido migrado do Hub para o Tradutor Personalizado utilizando a Ferramenta de Migração.  O processo de migração de projetos e espaços de trabalho pode ser consultado [aqui.](how-to-migrate.md)
* O modelo deve estar no estado implantado quando a migração acontecer.  
* O par de linguagem do modelo deve ser um par de linguagem não suportado no Custom Tradutor.  Os pares linguísticos em que uma língua é suportada ou a partir do inglês, mas o par em si não inclui inglês, são candidatos para implementações linguísticas não apoiadas.  Por exemplo, um modelo hub para um par de língua francesa a alemã é considerado um par de línguas não apoiada, embora o francês para o inglês e o inglês para o alemão sejam apoiados em pares de línguas.

## <a name="process"></a>Processo
Uma vez migrados os modelos do Hub que são candidatos à implantação, pode encontrá-los indo para a página **Definições** para o seu espaço de trabalho e percorrendo até ao final da página onde verá uma secção **de Treinos de Hub de Tradutor Não Suportado.**  Esta secção só aparece se tiver projetos que satisfaçam os pré-requisitos acima mencionados.

![Screenshot que destaca a secção de Treinos do Hub de Tradutor Não Suportado.](media/unsupported-language-deployments/unsupported-translator-hub-trainings.jpg)

Dentro da página de seleção de Formação de Centros de Tradutor não **suportados,** o **separador de treinos não solicitado** contém modelos elegíveis para implantação.  Selecione os modelos que pretende implementar e envie um pedido.   Antes do prazo de implementação de 30 de abril, pode selecionar quantos modelos desejar para a implementação.
 
![Screenshot que mostra o separador de treinos não requested.](media/unsupported-language-deployments/unsupported-translator-hub-trainings-list.jpg)

Uma vez apresentado, o modelo deixará de estar disponível no separador **treinos Não Solicitados** e aparecerá no separador **formações solicitados.**  Pode ver os treinos solicitados a qualquer momento.

![Como migrar do Hub](media/unsupported-language-deployments/request-unsupported-trainings.jpg) 

## <a name="whats-next"></a>O que se segue?

Os modelos selecionados para implantação são guardados assim que o Hub for desativado e todos os modelos não serem desprotendo.  Tem até 24 de maio para apresentar pedidos de implementação de modelos não suportados.  Implementaremos estes modelos no dia 15 de junho, altura em que estarão acessíveis através da API tradutora V3.  Além disso, estarão disponíveis através da API V2 até 1 de julho.  

Para mais informações sobre datas importantes na depreciação do Hub consulte [aqui](https://www.microsoft.com/translator/business/hub/).
Uma vez implantados, aplicar-se-ão os encargos normais de hospedagem.  Consulte [os preços](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/) para mais detalhes.  

Ao contrário dos modelos standard de Tradutor Personalizado, os modelos Hub só estarão disponíveis numa única região, pelo que os custos de hospedagem multi-região não serão aplicados.  Uma vez implementado, poderá despreocupar o seu modelo Hub a qualquer momento através do projeto De Tradutor Personalizado migrado.

## <a name="next-steps"></a>Passos seguintes

- [Treine um modelo.](how-to-train-model.md)
- Comece a utilizar o seu modelo de tradução personalizado implementado através do [Microsoft Tradutor Text API V3](../reference/v3-0-translate.md?tabs=curl).