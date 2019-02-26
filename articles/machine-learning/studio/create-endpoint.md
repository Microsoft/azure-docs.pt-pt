---
title: Criar pontos finais de serviço da web
titleSuffix: Azure Machine Learning Studio
description: Crie pontos finais de serviço da web no Azure Machine Learning Studio. Cada ponto de extremidade no serviço web independente é resolvido, limitado e gerido.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 02/15/2019
ms.openlocfilehash: 477450f341efea832ffa8411cd9f455b94a6ea40
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56822451"
---
# <a name="create-endpoints-for-deployed-azure-machine-learning-studio-web-services"></a>Criar pontos de extremidade dos serviços de web do Azure Machine Learning Studio implementados

> [!NOTE]
> Este tópico descreve técnicas aplicáveis a uma **clássico** serviço web Machine Learning.

Depois de implementar um serviço Web, é criado um ponto final predefinido para esse serviço. O ponto final predefinido pode ser chamado através da respetiva chave de API. Pode adicionar mais pontos finais com suas próprias chaves a partir do portal dos serviços Web.
Cada ponto de extremidade no serviço web independente é resolvido, limitado e gerido. Cada ponto de extremidade é uma URL exclusiva com uma chave de autorização que pode distribuir aos seus clientes.

## <a name="add-endpoints-to-a-web-service"></a>Adicionar pontos de extremidade a um serviço web

Pode adicionar um ponto final a um serviço web através do portal do Azure Machine Learning Web Services. Assim que o ponto final for criado, pode consumi-lo através de APIs síncronas, APIs, do batch e folhas de cálculo do excel.

> [!NOTE]
> Se tiver adicionado pontos finais adicionais para o serviço web, não é possível eliminar o ponto final predefinido.

1. No Machine Learning Studio, na coluna de navegação esquerda, clique em serviços da Web.
2. Na parte inferior do dashboard do serviço web, clique em **gerir pontos finais**. O portal de serviços da Web do Azure Machine Learning abre-se para a página de pontos de extremidade para o serviço web.
3. Clique em **Novo**.
4. Escreva um nome e descrição para o novo ponto final. Nomes de ponto final tem de ser 24 carateres ou menos de comprimento e devem ser constituídos por letras do alfabeto em minúsculas ou números. Selecione o nível de registo e se os dados de exemplo estão ativados. Para obter mais informações sobre o registo, consulte [ativar o registo de serviços web Machine Learning](web-services-logging.md).

## <a id="scaling"></a> Dimensionar um serviço da web ao adicionar pontos finais adicionais

Por predefinição, cada serviço web publicados está configurado para suportar 20 pedidos simultâneos e pode ser tão elevado como 200 pedidos em simultâneo. O Azure Machine Learning Studio automaticamente otimiza a definição para proporcionar o melhor desempenho do web Service e o valor de portal é ignorado.

Se tiver de suportar plano para chamar a API com uma carga maior que um valor de chamadas simultâneas do máximo de 200, deve criar vários pontos de extremidade no mesmo serviço da web. Em seguida, pode distribuir aleatoriamente a carga em todos os.

O dimensionamento de um serviço web é uma tarefa comum. São algumas razões para dimensionar para suportar mais de 200 pedidos em simultâneo, aumentar a disponibilidade por meio de vários pontos de extremidade ou fornecer pontos de extremidade separados para o serviço web. Pode aumentar a escala ao adicionar pontos finais adicionais para o mesmo serviço da web através da [serviço da Web do Azure Machine Learning](https://services.azureml.net/) portal.

Tenha em atenção que pode ser prejudicial se não estiver a chamar a API com uma taxa elevada do mesmo modo com uma contagem alta de simultaneidade. Poderá ver tempos limite esporádica e/ou picos na latência se colocar uma carga relativamente baixa numa API configurada para uma carga elevada.

As APIs síncronas são normalmente utilizadas em situações em que uma baixa latência for o pretendido. Latência aqui indica o tempo necessário para a API concluir um pedido e, não se responsabiliza por qualquer atrasos de rede. Digamos que tem uma API com uma latência de 50 ms. Para consumir totalmente a capacidade disponível com o nível de limitação do alto e máx. de chamadas simultâneas = 20, precisa chamar esta API 20 * 1000 / 50 = 400 horas por segundo. Expandir isso ainda mais, um máximo de chamadas simultâneas de 200 permite que chamar a API 4000 de vezes por segundo, supondo que uma latência de 50 ms.

## <a name="next-steps"></a>Passos Seguintes

[Como consumir um serviço web Azure Machine Learning](consume-web-services.md).
