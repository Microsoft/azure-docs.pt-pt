---
title: Criar pontos finais do serviço web
titleSuffix: ML Studio (classic) - Azure
description: Crie pontos finais de serviço web no Azure Machine Learning Studio (clássico). Cada ponto final do serviço web é dirigido de forma independente, estrangulado e gerido.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 02/15/2019
ms.openlocfilehash: 980ed1e54de30ec8a2dc0c1fdac6546d31f48a00
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79218207"
---
# <a name="create-endpoints-for-deployed-azure-machine-learning-studio-classic-web-services"></a>Crie pontos finais para serviços web azure machine learning (clássicos)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

> [!NOTE]
> Este tópico descreve técnicas aplicáveis a um serviço web **Classic** Machine Learning.

Depois de implementar um serviço Web, é criado um ponto final predefinido para esse serviço. O ponto final predefinido pode ser chamado através da respetiva chave de API. Pode adicionar mais pontos finais com as suas próprias chaves do portal de Serviços Web.
Cada ponto final do serviço web é dirigido de forma independente, estrangulado e gerido. Cada ponto final é um URL único com uma chave de autorização que pode distribuir aos seus clientes.

## <a name="add-endpoints-to-a-web-service"></a>Adicione pontos finais a um serviço web

Pode adicionar um ponto final a um serviço web utilizando o portal Azure Machine Learning Web Services. Assim que o ponto final for criado, pode consumi-lo através de APIs síncronas, APIs, do batch e folhas de cálculo do excel.

> [!NOTE]
> Se tiver adicionado pontos finais adicionais ao serviço web, não pode eliminar o ponto final predefinido.

1. No Machine Learning Studio (clássico), na coluna de navegação à esquerda, clique em Web Services.
2. Na parte inferior do painel de instrumentos do serviço web, clique em **Gerir pontos finais**. O portal Azure Machine Learning Web Services abre para a página de pontos finais para o serviço web.
3. Clique em **Novo**.
4. Escreva um nome e descrição para o novo ponto final. Nomes de ponto final tem de ser 24 carateres ou menos de comprimento e devem ser constituídos por letras do alfabeto em minúsculas ou números. Selecione o nível de registo e se os dados de exemplo estão ativados. Para obter mais informações sobre a exploração madeireira, consulte [A exploração de login para serviços web de aprendizagem automática](web-services-logging.md).

## <a id="scaling"></a>Escala um serviço web adicionando pontos finais adicionais

Por padrão, cada serviço web publicado está configurado para suportar 20 pedidos simultâneos e pode ser até 200 pedidos simultâneos. O Azure Machine Learning Studio (clássico) otimiza automaticamente a configuração para proporcionar o melhor desempenho para o seu serviço web e o valor do portal é ignorado.

Se planeia ligar para a API com uma carga superior à de um valor max concurrent calls de 200 irá suportar, você deve criar vários pontos finais no mesmo serviço web. Em seguida, pode distribuir aleatoriamente a sua carga em todos eles.

A escala de um serviço web é uma tarefa comum. Algumas razões para escalar são para suportar mais de 200 pedidos simultâneos, aumentar a disponibilidade através de vários pontos finais, ou fornecer pontos finais separados para o serviço web. Pode aumentar a escala adicionando pontos finais adicionais para o mesmo serviço web através do portal [Azure Machine Learning Web Service.](https://services.azureml.net/)

Tenha em mente que usar uma alta contagem de moedas pode ser prejudicial se não estiver a chamar a API com uma taxa correspondentemente elevada. Pode ver tempo sádicos e/ou picos na latência se colocar uma carga relativamente baixa numa API configurada para alta carga.

As APIs sincronizadas são normalmente usadas em situações em que se deseja uma latência baixa. A latência aqui implica o tempo que a API leva para completar um pedido, e não explica quaisquer atrasos na rede. Digamos que tem uma API com uma latência de 50 ms. Para consumir totalmente a capacidade disponível com o nível de aceleração High and Max Concurrent Calls = 20, você precisa chamar este API 20 * 1000 / 50 = 400 vezes por segundo. Estendendo-o ainda mais, um Max Concurrent Calls de 200 permite-lhe ligar para a API 4000 vezes por segundo, assumindo uma latência de 50 ms.

## <a name="next-steps"></a>Passos seguintes

[Como consumir um serviço web Azure Machine Learning.](consume-web-services.md)
