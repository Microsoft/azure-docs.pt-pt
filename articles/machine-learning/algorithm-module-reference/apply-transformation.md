---
title: 'Aplicam-se de transformação: Referência do módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como utilizar o módulo de transformação aplicam-se no serviço Azure Machine Learning para modificar um conjunto de dados de entrada com base numa transformação calculada anteriormente.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 810f375642af49814049589cb83ad17fea578b13
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65028714"
---
# <a name="apply-transformation-module"></a>Aplicar o módulo de transformação

Este artigo descreve um módulo da interface visual (pré-visualização) para o serviço Azure Machine Learning.

Utilize este módulo para modificar um conjunto de dados de entrada com base numa transformação calculada anteriormente.  
  
Por exemplo, se tiver utilizado o z-as pontuações para normalizar os dados de treinamento, utilizando o **normalizar dados** módulo, gostaria de utilizar o valor de z pontuação que foi calculado para formação durante a fase de classificação. No Azure Machine Learning, pode salvar o método de normalização como uma transformação e, em seguida, usando **transformação de aplicar** para aplicar a classificação de z para os dados de entrada antes de classificação.
  
O Azure Machine Learning fornece suporte para criar e, em seguida, aplicar muitos tipos diferentes de transformações personalizadas. Por exemplo, pode querer guardar e, em seguida, reutilizar transformações para:  
  
- Remova ou substitua os valores em falta, usando **apagar dados em falta**
- Normalizar dados, usando **normalizar dados**
  

## <a name="how-to-use-apply-transformation"></a>Como utilizar transformação de aplicar  
  
1. Adicionar a **transformação de aplicar** módulo à sua experimentação. Pode encontrar este módulo sob **Machine Learning**, na **pontuação** categoria. 
  
2. Localize uma transformação existente para utilizar como entrada.  Transformações guardadas anteriormente podem ser encontradas no **transforma** grupo no painel de navegação esquerdo.  
  
   
  
3. Ligue-se o conjunto de dados que deseja transformar. O conjunto de dados deve ter exatamente o mesmo esquema (número de colunas, nomes de colunas, tipos de dados) como o conjunto de dados para o qual a transformação primeiro foi criada.  
  
4. Não existem outros parâmetros tem de ser definida porque toda personalização é feito ao definir a transformação.  
  
5. Para aplicar uma transformação para o novo conjunto de dados, execute a experimentação.  

## <a name="next-steps"></a>Passos Seguintes

Consulte a [conjunto de módulos disponíveis](module-reference.md) ao serviço Azure Machine Learning. 