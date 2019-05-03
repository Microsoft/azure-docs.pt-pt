---
title: 'Converta para CSV: Referência do módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como utilizar a converter para o módulo CSV no serviço Azure Machine Learning para converter um conjunto de dados num formato CSV que pode ser transferido, exportado ou partilhado com módulos de script R ou Python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 8b8b6758cc2df7a092ce36e9507f84ac534d0e3d
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028729"
---
# <a name="convert-to-csv-module"></a>Converter para o módulo CSV

Este artigo descreve um módulo da interface visual (pré-visualização) para o serviço Azure Machine Learning.

Utilize este módulo para converter um conjunto de dados num formato CSV que pode ser transferido, exportado ou partilhado com módulos de script R ou Python.

### <a name="more-about-the-csv-format"></a>Mais informações sobre o formato CSV 

O formato CSV, o que significa "valores separados por vírgulas", é um formato de ficheiro utilizado pela máquina externa muitas ferramentas de aprendizagem. CSV é um formato de intercâmbio comuns ao trabalhar com linguagens de código-fonte aberto, como o R ou Python.

Mesmo que faz a maior parte do trabalho no Azure Machine Learning, há momentos em que quando poderá achar útil para converter o conjunto de dados para CSV para usar ferramentas externas. Por exemplo:

+ Transfira o ficheiro CSV para abri-lo com o Excel ou importá-lo para uma base de dados relacional.  
+ Guarde o ficheiro CSV para armazenamento na cloud e ligá-la através do Power BI para criar visualizações.  
+ Utilize o formato CSV para preparar dados para utilização em R e Python. Apenas com o botão direito a saída do módulo para gerar o código necessário para acessar os dados diretamente a partir de Python ou um bloco de notas do Jupyter. 

Quando converte um conjunto de dados para CSV, o ficheiro é guardado na sua área de trabalho do Azure ML. Pode usar um utilitário de armazenamento do Azure para abrir e utilizar o ficheiro diretamente ou pode com o botão direito a saída do módulo e transfira o ficheiro CSV para o computador ou utilizá-lo em código de R e Python.  

## <a name="how-to-configure-convert-to-csv"></a>Como configurar a converter para CSV

1.  Adicionar a [converter para CSV](./convert-to-csv.md) módulo à sua experimentação. Pode encontrar este módulo na **conversões de formato de dados** grupo na interface. 

2. Ligue-o para qualquer módulo que produz um conjunto de dados.   
  
3.  Execute a experimentação.

### <a name="results"></a>Resultados
  

Clique duas vezes a saída de [converter para CSV](./convert-to-csv.md)e selecione uma destas opções.  

 + **Conjunto de dados de resultado -> Download**: Abre-se imediatamente uma cópia dos dados no formato CSV que pode salvar numa pasta local. Se não especificar uma pasta, um nome de ficheiro predefinido é aplicado e o ficheiro CSV é guardado no local **Downloads** biblioteca.


 + **Conjunto de dados de resultado -> Guardar como conjunto de dados**: Guarda o ficheiro CSV para a área de trabalho do Azure ML como um conjunto de dados separado.

 + **Gerar código de acesso a dados**: O Azure ML gera dois conjuntos de código para que possa acessar os dados, com o Python ou com o R. Para acessar os dados, copie o trecho de código na sua aplicação. (*Gerar código de acesso a dados virão em breve.* )

## <a name="next-steps"></a>Passos Seguintes

Consulte a [conjunto de módulos disponíveis](module-reference.md) ao serviço Azure Machine Learning. 