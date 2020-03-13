---
title: Exploração madeireira de serviço web - Azure Machine Learning Studio (clássico) Microsoft Docs
description: Saiba como ativar o loglogging para serviços web machine learning studio (clássicos). O registo fornece informações adicionais para ajudar a resolver problemas relacionados com as APIs.
services: machine-learning
author: xiaoharper
ms.custom: seodec18
ms.author: amlstudiodocs
editor: cgronlun
ms.assetid: c54d41e1-0300-46ef-bbfc-d6f7dca85086
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
ms.date: 06/15/2017
ms.openlocfilehash: 90e7692fe0e254074d8176d719d0ca9abad54a9b
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79217852"
---
# <a name="enable-logging-for-azure-machine-learning-studio-classic-web-services"></a>Ativar a exploração de serviços web Azure Machine Learning Studio (clássico)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Este documento fornece informações sobre a capacidade de registo de serviços web do Machine Learning Studio (clássico). O registo fornece informações adicionais, além de apenas um número de erro e uma mensagem, que podem ajudá-lo a resolver as suas chamadas para o Machine Learning Studio (clássico) APIs.  

## <a name="how-to-enable-logging-for-a-web-service"></a>Como ativar o registo de um serviço Web

Permite a exploração de registo sessão a partir do portal [de Serviços Web Azure Machine Learning Studio (clássico).](https://services.azureml.net) 

1. Inscreva-se no portal azure machine learning studio (clássico) web services no [https://services.azureml.net](https://services.azureml.net). Para um serviço web Clássico, você também pode chegar ao portal clicando em **New Web Services Experience** na página de Serviços Web Machine Learning Studio (clássico) em Studio (clássico).

   ![Nova ligação de experiência de serviços da Web](./media/web-services-logging/new-web-services-experience-link.png)

2. Na barra de menu superior, clique em **Web Services** para um novo serviço web ou clique em **Serviços Web Clássicos** para um serviço web clássico.

   ![Selecione o novo ou clássico serviços da web](./media/web-services-logging/select-web-service.png)

3. Para um novo serviço web, clique no nome de serviço da web. Para um serviço web clássico, clique no nome de serviço da web e, em seguida, na página seguinte, clique o ponto final adequado.

4. Na barra de menu superior, clique **em Configurar**.

5. Detete a opção **Ativar** o registo ao *Erro* (para registar apenas erros) ou *Tudo* (para o registo completo).

   ![Selecione o nível de registo](./media/web-services-logging/enable-logging.png)

6. Clique em **Guardar**.

7. Para os serviços web clássicos, crie o recipiente **de diagnóstico ml.**

   Todos os registos de serviço web são guardados num recipiente de blob denominado **ml-diagnósticos** na conta de armazenamento associada ao serviço web. Para novos serviços web, este contentor é criado na primeira vez que aceder ao serviço web. Para serviços da web clássico, terá de criar o contentor se ainda não exista. 

   1. No [portal Azure,](https://portal.azure.com)vá à conta de armazenamento associada ao serviço web.

   2. Em **Serviço Blob**, clique em **Contentores**.

   3. Se o recipiente **ml-diagnóstico** não existir, clique em **+Contentor,** dê ao recipiente o nome "ml-diagnósticos", e selecione o **tipo de Acesso** como "Blob". Clique em **OK**.

      ![Crie um novo recipiente para armazenar os seus registos de diagnóstico](./media/web-services-logging/create-ml-diagnostics-container.png)

> [!TIP]
>
> Para um serviço web Clássico, o Painel de Serviços Web no Machine Learning Studio (clássico) também tem um interruptor para ativar a exploração madeireira. No entanto, uma vez que o registo é agora gerenciado através do portal de serviços da Web, terá de ativar o registo através do portal conforme descrito neste artigo. Se já permitiu a exploração madeireira no Studio (clássico), então no Portal dos Serviços Web, desative o registo e ative-o novamente.


## <a name="the-effects-of-enabling-logging"></a>Os efeitos de ativar o registo
Quando o registo está ativado, os diagnósticos e erros do ponto final do serviço web são registados no recipiente blob **ml-diagnóstico na** Conta de Armazenamento Azure ligada ao espaço de trabalho do utilizador. Este contentor armazena todas as informações de diagnóstico para todos os web pontos finais de serviço para todas as áreas de trabalho associados a esta conta de armazenamento.

Os registos podem ser exibidos em qualquer uma das várias ferramentas disponíveis para explorar uma conta de armazenamento do Azure. O mais fácil pode ser navegar para a conta de armazenamento no portal Azure, clicar em **Contentores,** e, em seguida, clicar no recipiente **ml-diagnósticos**.  

## <a name="log-blob-detail-information"></a>Informações de detalhe de blob de registo
Cada blob no contentor contém as informações de diagnóstico do exatamente uma das seguintes ações:

* Uma execução do método de execução de lotes  
* Uma execução do método de solicitação-resposta  
* Inicialização de um contentor de solicitação-resposta

O nome de cada blob tem um prefixo da seguinte forma: 


`{Workspace Id}-{Web service Id}-{Endpoint Id}/{Log type}`


Quando o _tipo de Registo_ é um dos seguintes valores:  

* lote  
* pontuação/pedidos  
* pontuação/init  

