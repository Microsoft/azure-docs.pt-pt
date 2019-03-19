---
title: Utilizar a máquina de Virtual de ciência dados do Geo Inteligência Artificial – Azure | Documentos da Microsoft
description: Saiba como utilizar a máquina de Virtual de ciência de dados de IA geográfica para analisar dados e criar modelos com base em dados geoespaciais.
keywords: IA, ferramentas de ciência de dados, a máquina de virtual de ciência de dados, a análise Geoespacial, aprendizagem aprofundada
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/05/2018
ms.author: gokuma
ms.openlocfilehash: 6e6737e928ece820ea9119d8dfe1d7cf22477646
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57880114"
---
# <a name="using-the-geo-artificial-intelligence-data-science-virtual-machine"></a>Utilizar a máquina de Virtual de ciência de dados de Inteligência Artificial de Geo

Utilize a VM de ciência de dados de IA geográfica para obter dados para análise, executar a preparação de dados e criar modelos para aplicações de IA que consomem a informação geoespacial. Depois de ter aprovisionado sua VM de ciência de dados de IA geográfica e tem sessão iniciada no ArcGIS Pro, com a sua conta do ArcGIS, pode começar a interagir com o ambiente de trabalho do ArcGIS e o ArcGis online. Também pode acessar o ArcGIS de interfaces de Python e uma ponte de linguagem R previamente configurado na VM de ciência de dados Geoespaciais. Para criar aplicações de IA avançadas, combiná-la com a aprendizagem automática e aprendizagem profunda estruturas e outro software de ciência de dados disponível na VM de ciência de dados.  


## <a name="configuration-details"></a>Detalhes de configuração

A biblioteca de Python [arcpy](https://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm), que é utilizado para a interface com o ArcGIS está instalado no ambiente de conda de raiz global da VM de ciência de dados que se encontra em ```c:\anaconda```. 

- Se estiver a executar o Python numa linha de comandos, execute ```activate``` para ativar no ambiente de Python de conda raiz. 
- Se estiver a utilizar um IDE ou Jupyter notebook, pode selecionar o ambiente ou kernel para se certificar de que no ambiente de conda correto. 

A ponte de R para ArcGIS é instalada como uma biblioteca de R com o nome [arcgisbinding](https://github.com/R-ArcGIS/r-bridge) do Microsoft R principal instância autónoma do servidor localizada em ```C:\Program Files\Microsoft\ML Server\R_SERVER```. Visual Studio, o RStudio e Jupyter já está pré-configurado para utilizar este ambiente de R e têm acesso ao ```arcgisbinding``` biblioteca de R. 


## <a name="geo-ai-data-science-vm-samples"></a>Exemplos de VM de ciência de dados de IA geográfica

Além do ML e exemplos com base na estrutura da VM de ciência de dados base de aprendizagem profunda, um conjunto de exemplos de geoespacial também é fornecido como parte da VM de ciência de dados de IA geográfica. Estes exemplos podem ajudar a promover seu desenvolvimento de aplicações de IA com dados Geoespaciais e o software de ArcGIS. 


1. [Introdução declarado com análise Geoespacial com o Python](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/Python%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb): Um exemplo introdutório que mostra como trabalhar com dados Geoespaciais através da interface de Python para o ArcGIS fornecida pelos [arcpy](https://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm) biblioteca. Ela também mostra como pode combinar a aprendizagem automática tradicional com dados geoespaciais e visualizar o resultado num mapa na ArcGIS. 

2. [Introdução declarado com análise Geoespacial com R](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/R%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb): Um exemplo introdutório que mostra como trabalhar com dados Geoespaciais através da interface de R para ArcGIS fornecida pelos [arcgisbinding](https://github.com/R-ArcGIS/r-bridge) biblioteca. 

3. [Nível de pixel terra utilizar a classificação](https://github.com/Azure/pixel_level_land_classification): Um tutorial que ilustra como criar um modelo de rede neural profunda que aceita uma imagem aéreas como entrada e retorna uma etiqueta de capa da terra. Estes são exemplos de etiquetas de capa ' s land "forested" ou "água." O modelo devolve tal uma etiqueta para cada pixel na imagem. O modelo é criado usando o código aberto da Microsoft [Cognitive Toolkit (CNTK)](https://www.microsoft.com/en-us/cognitive-toolkit/) framework de aprendizagem profunda. 


## <a name="next-steps"></a>Passos Seguintes

Exemplos adicionais que utilizam a VM de ciência de dados estão disponíveis aqui:

* [Amostras](dsvm-samples-and-walkthroughs.md)

