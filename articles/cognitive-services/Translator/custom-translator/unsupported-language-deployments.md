---
title: Implementações linguísticas não suportadas - Tradutor Personalizado
titleSuffix: Azure Cognitive Services
description: Este artigo mostra-lhe como implementar pares de idiomas não suportados no Tradutor Personalizado de Serviços Cognitivos Azure.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 04/24/2019
ms.author: swmachan
ms.openlocfilehash: 252759139de7f53cd7875efeb7f477219092aa0b
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83584705"
---
# <a name="unsupported-language-deployments"></a>Implementações de linguagens não suportadas

<!--Custom Translator provides the highest-quality translations possible using the latest techniques in neural machine learning. While Microsoft intends to make neural training available in all languages, there are some limitations that prevent us from being able to offer neural machine translation in all language pairs.-->  

Com a próxima reforma do Microsoft Tradutor Hub, a Microsoft estará a desimplementar todos os modelos atualmente implantados através do Hub. Muitos de vocês têm modelos implantados no Hub cujos pares de línguas não são suportados em Tradutor Personalizado.  Não queremos que os utilizadores nesta situação não tenham qualquer recurso para traduzir os seus conteúdos.

Temos agora um processo que lhe permite implementar os seus modelos não suportados através do Tradutor Personalizado.  Este processo permite-lhe continuar a traduzir conteúdos utilizando a mais recente API V3.  Estes modelos serão hospedados até que você decida desimplantá-los ou o par de idiomas fica disponível em Tradutor Personalizado.  Este artigo explica o processo de implementação de modelos com pares de línguas não suportados.

## <a name="prerequisites"></a>Pré-requisitos

Para que os seus modelos sejam candidatos à implantação, devem cumprir os seguintes critérios:
* O projeto que contém o modelo deve ter sido migrado do Hub para o Tradutor Personalizado utilizando a Ferramenta de Migração.  O processo de migração de projetos e espaços de trabalho pode ser encontrado [aqui.](how-to-migrate.md)
* O modelo deve estar no estado de implantação quando a migração acontece.  
* O par linguístico do modelo deve ser um par de idiomas não suportado em Tradutor Personalizado.  Os pares linguísticos em que uma língua é suportada para ou a partir do inglês, mas o próprio par não inclui o inglês, são candidatos a implantações linguísticas não apoiadas.  Por exemplo, um modelo Hub para um par de língua francesa para alemão é considerado um par de línguas não suportada, embora o francês para o inglês e o inglês para o alemão sejam um par de línguas suportado.

## <a name="process"></a>Processo
Depois de ter modelos migrados do Hub que são candidatos à implantação, pode encontrá-los indo para a página **Definições** para o seu espaço de trabalho e percorrendo o final da página onde verá uma secção de Formação de **TradutorEs Não Suportados.**  Esta secção só aparece se tiver projetos que satisfaçam os pré-requisitos acima mencionados.

![Como migrar do Hub](media/unsupported-language-deployments/unsupported-translator-hub-trainings.jpg)

Dentro da página de seleção de formações de **tradutornão suportado,** o separador de **formações não solicitadas** contém modelos elegíveis para implementação.  Selecione os modelos que pretende implementar e envie um pedido.   Antes do prazo de implementação de 30 de abril, pode selecionar quantos modelos desejar a sua implementação.
 
![Como migrar do Hub](media/unsupported-language-deployments/unsupported-translator-hub-trainings-list.jpg)

Uma vez submetido, o modelo deixará de estar disponível no separador de **formações não solicitadas** e, em vez disso, aparecerá no separador **de formações solicitadas.**  Pode ver os treinos solicitados a qualquer momento.

![Como migrar do Hub](media/unsupported-language-deployments/request-unsupported-trainings.jpg) 

## <a name="whats-next"></a>Passos seguintes?

Os modelos selecionados para a implantação são guardados assim que o Hub é desativado e todos os modelos não forem implantados.  Tem até 24 de maio para apresentar pedidos de implantação de modelos não suportados.  Vamos implementar estes modelos no dia 15 de junho, altura em que estarão acessíveis através da API tradutora V3.  Além disso, estarão disponíveis através da V2 API até 1 de julho.  

Para mais informações sobre datas importantes na depreciação do Hub consulte [aqui](https://www.microsoft.com/translator/business/hub/).
Uma vez implementados, as taxas normais de hospedagem serão aplicadas.  Consulte [os preços](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/) para mais detalhes.  

Ao contrário dos modelos standard de Tradutor Personalizado, os modelos Hub só estarão disponíveis numa única região, pelo que os encargos de hospedagem multi-regiões não serão aplicados.  Uma vez implementado, poderá desimplantar o seu modelo Hub a qualquer momento através do projeto De tradutor personalizado migrado.

## <a name="next-steps"></a>Passos seguintes

- [Treine um modelo.](how-to-train-model.md)
- Comece a utilizar o seu modelo de tradução personalizada implementado via [Tradutor V3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl).
