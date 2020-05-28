---
title: Ativar o registo de serviçoweb
titleSuffix: ML Studio (classic) - Azure
description: Saiba como ativar o loglogging para serviços web machine learning studio (clássicos). O registo de registo sintetiza informações adicionais para ajudar a resolver problemas com as APIs.
services: machine-learning
author: likebupt
ms.author: keli19
editor: cgronlun
ms.assetid: c54d41e1-0300-46ef-bbfc-d6f7dca85086
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
ms.date: 06/15/2017
ms.openlocfilehash: 45acac646fece808065d12081bdc28eff92a1b79
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84119337"
---
# <a name="enable-logging-for-azure-machine-learning-studio-classic-web-services"></a>Ativar a exploração de serviços web Azure Machine Learning Studio (clássico)

Este documento fornece informações sobre a capacidade de registo de serviços web do Machine Learning Studio (clássico). O registo fornece informações adicionais, além de apenas um número de erro e uma mensagem, que podem ajudá-lo a resolver as suas chamadas para o Machine Learning Studio (clássico) APIs.  

## <a name="how-to-enable-logging-for-a-web-service"></a>Como ativar o registo de um serviço Web

Permite a exploração de registo sessão a partir do portal [de Serviços Web Azure Machine Learning Studio (clássico).](https://services.azureml.net) 

1. Inscreva-se no portal azure machine learning studio (clássico) Web Services em [https://services.azureml.net](https://services.azureml.net) . Para um serviço web Clássico, você também pode chegar ao portal clicando em **New Web Services Experience** na página de Serviços Web Machine Learning Studio (clássico) em Studio (clássico).

   ![Novo link de experiência de serviços web](./media/web-services-logging/new-web-services-experience-link.png)

2. Na barra de menu superior, clique em **Web Services** para um novo serviço web ou clique em **Serviços Web Clássicos** para um serviço web clássico.

   ![Selecione serviços web novos ou clássicos](./media/web-services-logging/select-web-service.png)

3. Para um novo serviço web, clique no nome do serviço web. Para um serviço web Clássico, clique no nome do serviço web e, em seguida, na página seguinte clique no ponto final apropriado.

4. Na barra de menu superior, clique **em Configurar**.

5. Detete a opção **Ativar** o registo ao *Erro* (para registar apenas erros) ou *Tudo* (para o registo completo).

   ![Selecione o nível de exploração madeir](./media/web-services-logging/enable-logging.png)

6. Clique em **Guardar**.

7. Para os serviços web clássicos, crie o recipiente **de diagnóstico ml.**

   Todos os registos de serviço web são guardados num recipiente de blob denominado **ml-diagnósticos** na conta de armazenamento associada ao serviço web. Para novos serviços web, este recipiente é criado na primeira vez que acede ao serviço web. Para os serviços web Clássicos, você precisa criar o recipiente se ele já não existir. 

   1. No [portal Azure,](https://portal.azure.com)vá à conta de armazenamento associada ao serviço web.

   2. Em **Serviço Blob**, clique em **Contentores**.

   3. Se o recipiente **ml-diagnóstico** não existir, clique em **+Contentor,** dê ao recipiente o nome "ml-diagnósticos", e selecione o **tipo de Acesso** como "Blob". Clique em **OK**.

      ![Crie um novo recipiente para armazenar os seus registos de diagnóstico](./media/web-services-logging/create-ml-diagnostics-container.png)

> [!TIP]
>
> Para um serviço web Clássico, o Painel de Serviços Web no Machine Learning Studio (clássico) também tem um interruptor para ativar a exploração madeireira. No entanto, como a exploração madeireira é agora gerida através do portal dos Serviços Web, é necessário permitir o login através do portal, tal como descrito neste artigo. Se já permitiu a exploração madeireira no Studio (clássico), então no Portal dos Serviços Web, desative o registo e ative-o novamente.


## <a name="the-effects-of-enabling-logging"></a>Os efeitos da exploração madeireira
Quando o registo está ativado, os diagnósticos e erros do ponto final do serviço web são registados no recipiente blob **ml-diagnóstico na** Conta de Armazenamento Azure ligada ao espaço de trabalho do utilizador. Este recipiente contém todas as informações de diagnóstico para todos os pontos finais do serviço web para todos os espaços de trabalho associados a esta conta de armazenamento.

Os registos podem ser vistos utilizando qualquer uma das várias ferramentas disponíveis para explorar uma Conta de Armazenamento Azure. O mais fácil pode ser navegar para a conta de armazenamento no portal Azure, clicar em **Contentores,** e, em seguida, clicar no recipiente **ml-diagnósticos**.  

## <a name="log-blob-detail-information"></a>Informação de detalhes blob de log
Cada bolha no recipiente contém as informações de diagnóstico para exatamente uma das seguintes ações:

* Uma execução do método de execução de lotes  
* Uma execução do método de resposta ao pedido  
* Inicialização de um recipiente de resposta a pedido

O nome de cada bolha tem um prefixo do seguinte formulário: 


`{Workspace Id}-{Web service Id}-{Endpoint Id}/{Log type}`


Quando o _tipo de Registo_ é um dos seguintes valores:  

* lote  
* pontuação/pedidos  
* pontuação/init  

