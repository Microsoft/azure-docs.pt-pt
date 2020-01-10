---
title: Criar pontos de extremidade de serviço Web
titleSuffix: ML Studio (classic) - Azure
description: Crie pontos de extremidade de serviço Web no Azure Machine Learning Studio (clássico). Cada ponto de extremidade no serviço Web é endereçado, limitado e gerenciado de forma independente.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 02/15/2019
ms.openlocfilehash: 57bc5739d8e6b1fa30482de69285c8895294cfa8
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75454765"
---
# <a name="create-endpoints-for-deployed-azure-machine-learning-studio-classic-web-services"></a>Criar pontos de extremidade para serviços Web Azure Machine Learning Studio (clássico) implantados

> [!NOTE]
> Este tópico descreve as técnicas aplicáveis a um serviço Web **clássico** Machine Learning.

Depois de implementar um serviço Web, é criado um ponto final predefinido para esse serviço. O ponto final predefinido pode ser chamado através da respetiva chave de API. Você pode adicionar mais pontos de extremidade com suas próprias chaves no portal de serviços Web.
Cada ponto de extremidade no serviço Web é endereçado, limitado e gerenciado de forma independente. Cada ponto de extremidade é uma URL exclusiva com uma chave de autorização que você pode distribuir para seus clientes.

## <a name="add-endpoints-to-a-web-service"></a>Adicionar pontos de extremidade a um serviço Web

Você pode adicionar um ponto de extremidade a um serviço Web usando o Azure Machine Learning Portal de serviços Web. Assim que o ponto final for criado, pode consumi-lo através de APIs síncronas, APIs, do batch e folhas de cálculo do excel.

> [!NOTE]
> Se você tiver adicionado pontos de extremidade adicionais ao serviço Web, não será possível excluir o ponto de extremidades padrão.

1. Em Machine Learning Studio (clássico), na coluna de navegação à esquerda, clique em serviços Web.
2. Na parte inferior do painel do serviço Web, clique em **gerenciar pontos de extremidade**. O portal de serviços Web do Azure Machine Learning é aberto na página pontos de extremidade do serviço Web.
3. Clique em **Novo**.
4. Escreva um nome e descrição para o novo ponto final. Nomes de ponto final tem de ser 24 carateres ou menos de comprimento e devem ser constituídos por letras do alfabeto em minúsculas ou números. Selecione o nível de registo e se os dados de exemplo estão ativados. Para obter mais informações sobre registro em log, consulte [habilitar o log para serviços web Machine Learning](web-services-logging.md).

## <a id="scaling"></a>Dimensionar um serviço Web adicionando pontos de extremidade adicionais

Por padrão, cada serviço Web publicado é configurado para dar suporte a 20 solicitações simultâneas e pode ser tão alto quanto 200 solicitações simultâneas. Azure Machine Learning Studio (clássico) otimiza automaticamente a configuração para fornecer o melhor desempenho para o serviço Web e o valor do portal é ignorado.

Se você planeja chamar a API com uma carga maior do que um valor máximo de chamadas simultâneas de 200 dará suporte a, você deve criar vários pontos de extremidade no mesmo serviço Web. Em seguida, você pode distribuir aleatoriamente a carga entre todas elas.

O dimensionamento de um serviço Web é uma tarefa comum. Alguns motivos para dimensionar são dar suporte a mais de 200 solicitações simultâneas, aumentar a disponibilidade por meio de vários pontos de extremidade ou fornecer pontos de extremidade separados para o serviço Web. Você pode aumentar a escala adicionando pontos de extremidade adicionais para o mesmo serviço Web por meio do portal do [serviço web Azure Machine Learning](https://services.azureml.net/) .

Tenha em mente que o uso de uma contagem de simultaneidade alta pode ser prejudicial se você não estiver chamando a API com uma taxa de alta correspondente. Você pode ver tempos limite esporádicos e/ou picos na latência se você colocar uma carga relativamente baixa em uma API configurada para alta carga.

As APIs síncronas são normalmente usadas em situações em que uma baixa latência é desejada. A latência aqui implica o tempo necessário para a API concluir uma solicitação e não leva em conta os atrasos de rede. Digamos que você tenha uma API com uma latência de 50 ms. Para consumir totalmente a capacidade disponível com chamadas simultâneas alta e máxima do nível de limitação = 20, você precisa chamar essa API 20 * 1000/50 = 400 vezes por segundo. Estendendo isso ainda mais, um máximo de chamadas simultâneas de 200 permite chamar a API 4000 vezes por segundo, supondo uma latência de 50-ms.

## <a name="next-steps"></a>Passos seguintes

[Como consumir um serviço web Azure Machine Learning](consume-web-services.md).
