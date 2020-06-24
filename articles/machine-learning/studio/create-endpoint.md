---
title: Criar pontos finais de serviço web
titleSuffix: ML Studio (classic) - Azure
description: Crie pontos finais de serviço web no Azure Machine Learning Studio (clássico). Cada ponto final do serviço web é endereçado de forma independente, estrangulado e gerido.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 02/15/2019
ms.openlocfilehash: e465d739c485009f68d6d5c5ecbb0850d73a6e60
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/11/2020
ms.locfileid: "84696427"
---
# <a name="create-endpoints-for-deployed-azure-machine-learning-studio-classic-web-services"></a>Criar pontos finais para serviços web do Azure Machine Learning Studio (clássicos)

> [!NOTE]
> Este tópico descreve técnicas aplicáveis a um serviço web **Classic** Machine Learning.

Depois de implementar um serviço Web, é criado um ponto final predefinido para esse serviço. O ponto final predefinido pode ser chamado através da respetiva chave de API. Pode adicionar mais pontos finais com as suas próprias chaves a partir do portal Serviços Web.
Cada ponto final do serviço web é endereçado de forma independente, estrangulado e gerido. Cada ponto final é um URL único com uma chave de autorização que pode distribuir aos seus clientes.

## <a name="add-endpoints-to-a-web-service"></a>Adicione pontos finais a um serviço web

Pode adicionar um ponto final a um serviço web utilizando o portal Azure Machine Learning Web Services. Uma vez criado o ponto final, pode consumi-lo através de APIs sincronizados, APIs de lote e folhas de cálculo excel.

> [!NOTE]
> Se tiver adicionado pontos finais adicionais ao serviço web, não poderá eliminar o ponto final predefinido.

1. No Machine Learning Studio (clássico), na coluna de navegação à esquerda, clique nos Serviços Web.
2. Na parte inferior do painel de instrumentos de serviço web, clique **em Gerir pontos finais**. O portal Azure Machine Learning Web Services abre para a página de pontos finais para o serviço web.
3. Clique **em Novo**.
4. Digite um nome e descrição para o novo ponto final. Os nomes do ponto final devem ter 24 caracteres ou menos de comprimento e devem ser compostos por alfabetos ou números inferiores. Selecione o nível de registo e se os dados da amostra estão ativados. Para obter mais informações sobre o registo, consulte [Ativar o registo de serviços web machine learning](web-services-logging.md).

## <a name="scale-a-web-service-by-adding-additional-endpoints"></a><a id="scaling"></a>Dimensione um serviço web adicionando pontos finais adicionais

Por padrão, cada serviço web publicado está configurado para suportar 20 pedidos simultâneos e pode chegar a 200 pedidos simultâneos. O Azure Machine Learning Studio (clássico) otimiza automaticamente a definição para proporcionar o melhor desempenho para o seu serviço web e o valor do portal é ignorado.

Se pretender ligar para a API com uma carga superior à de um valor de Chamadas Max Concurrent de 200 suporte, deverá criar vários pontos finais no mesmo serviço web. Em seguida, pode distribuir aleatoriamente a sua carga por todos eles.

O dimensionamento de um serviço web é uma tarefa comum. Algumas razões para escalar são para suportar mais de 200 pedidos simultâneos, aumentar a disponibilidade através de vários pontos finais, ou fornecer pontos finais separados para o serviço web. Pode aumentar a escala adicionando pontos finais adicionais para o mesmo serviço web através do portal [Azure Machine Learning Web Service.](https://services.azureml.net/)

Tenha em mente que usar uma contagem de alta concordância pode ser prejudicial se não estiver a ligar para a API com uma taxa correspondentemente elevada. Você pode ver intervalos esporádicos e/ou picos na latência se colocar uma carga relativamente baixa em uma API configurada para alta carga.

As APIs sincronizadas são normalmente utilizadas em situações em que se deseja uma baixa latência. A latência aqui implica o tempo que a API leva para completar um pedido, e não explica quaisquer atrasos na rede. Digamos que tem uma API com uma latência de 50 ms. Para consumir totalmente a capacidade disponível com ligações simultâneas de nível de aceleração / 20, é necessário chamar a isto API 20 * 1000 / 50 = 400 vezes por segundo. Alargando ainda mais isto, uma Max Concurrent Calls de 200 permite-lhe ligar para a API 4000 vezes por segundo, assumindo uma latência de 50 ms.

## <a name="next-steps"></a>Passos seguintes

[Como consumir um serviço web Azure Machine Learning](consume-web-services.md).
