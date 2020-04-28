---
title: Use o Geo AI
titleSuffix: Azure Data Science Virtual Machine
description: Aprenda a usar a Máquina Virtual geo-AI data Science para analisar dados e construir modelos com base em dados geoespaciais.
keywords: aprendizagem profunda, IA, ferramentas de ciência de dados, máquina virtual de ciência de dados, análise geoespacial
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 03/05/2018
ms.openlocfilehash: 2d7532c51e2963c0dc9f8d02e7a0e32864e80f92
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "70278422"
---
# <a name="using-the-geo-artificial-intelligence-data-science-virtual-machine"></a>Usando a Máquina Virtual geo artificial de dados de inteligência

Utilize o VM geo-AI data science para recolher dados para análise, realizar a discussão de dados e construir modelos para aplicações de IA que consomem informação geoespacial. Depois de ter aprovisionado o seu VM geo-ai data science e ter assinado no ArcGIS Pro através da sua conta ArcGIS, pode começar a interagir com o ambiente de trabalho do ArcGIS e o ArcGIs online. Você também pode aceder ao ArcGIS a partir de interfaces Python e uma ponte linguística R que é reconfigurada no VM geo-data science. Para construir aplicações ricas em IA, combine o VM geo-data science com os quadros de aprendizagem automática e de aprendizagem profunda e outros softwares de ciência de dados que estão disponíveis nele.  


## <a name="configuration-details"></a>Detalhes da configuração

A biblioteca Python, [arcpy](https://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm), que é usada para interagir com o ArcGIS, está instalada no ambiente ```c:\anaconda```global de conda raiz do VM data Science que se encontra em .

- Se estiver a executar python num ```activate``` pedido de comando, corra para ativar o ambiente python raiz da conda.
- Se estiver a usar um portátil IDE ou Jupyter, pode selecionar o ambiente ou o kernel para se certificar de que está no ambiente de conda correto.

A ponte R para arcGIS é instalada como uma biblioteca R chamada [arcgisbinding](https://github.com/R-ArcGIS/r-bridge) na principal ```C:\Program Files\Microsoft\ML Server\R_SERVER```instância autónoma do Microsoft Machine Learning Server que está localizada em . O Visual Studio, o RStudio e o Jupyter já estão pré-configurados para utilizar este ambiente R e terão acesso à biblioteca ```arcgisbinding``` R. 


## <a name="geo-ai-data-science-vm-samples"></a>Amostras vM geo AI Data Science VM

Além das amostras baseadas em machine-learning e deep learning baseadas no quadro da Base Data Science VM, um conjunto de amostras geoespaciais também é fornecido como parte do Geo AI Data Science VM. Estas amostras podem ajudá-lo a iniciar o seu desenvolvimento de aplicações de IA utilizando dados geoespaciais e o software ArcGIS:


1. [Começar com análise geoespacial com Python](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/Python%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb): Uma amostra introdutória que mostra como trabalhar com dados geoespaciais através da interface Python para o ArcGIS é fornecida pela biblioteca [arquipy.](https://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm) Também mostra como combinar a aprendizagem automática tradicional com dados geoespaciais e, em seguida, visualizar o resultado num mapa no ArcGIS.

2. [Começar com análise geoespacial com R:](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/R%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb)Uma amostra introdutória que mostra como trabalhar com dados geoespaciais utilizando a interface R para o ArcGIS que é fornecida pela biblioteca [arcgisbinding.](https://github.com/R-ArcGIS/r-bridge) 

3. [Classificação do uso do terreno ao nível de pixels](https://github.com/Azure/pixel_level_land_classification): Um tutorial que ilustra como criar um modelo de rede neural profunda que aceita uma imagem aérea como entrada e devolve uma etiqueta de capa terrestre. Exemplos de rótulos de cobertura terrestre são *florestados* e *água.* O modelo devolve tal etiqueta para cada pixel na imagem. 


## <a name="next-steps"></a>Passos seguintes

Estão disponíveis amostras adicionais que utilizam o VM de Ciência de Dados aqui:

* [Amostras](dsvm-samples-and-walkthroughs.md)