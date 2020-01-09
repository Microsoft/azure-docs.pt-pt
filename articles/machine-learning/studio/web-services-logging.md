---
title: Log do serviço Web-Azure Machine Learning Studio (clássico) | Microsoft Docs
description: Saiba como habilitar o registro em log para serviços da Web Machine Learning Studio (clássico). O registo fornece informações adicionais para ajudar a resolver problemas relacionados com as APIs.
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
ms.openlocfilehash: 0b0dfeb6a19e2f6f24568de0b4712758d2b7ad4a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75427399"
---
# <a name="enable-logging-for-azure-machine-learning-studio-classic-web-services"></a>Habilitar o registro em log para serviços Web Azure Machine Learning Studio (clássico)
Este documento fornece informações sobre o recurso de log dos serviços Web Machine Learning Studio (clássico). O registro em log fornece informações adicionais, além de apenas um número de erro e uma mensagem, que podem ajudá-lo a solucionar suas chamadas para as APIs de Machine Learning Studio (clássicas).  

## <a name="how-to-enable-logging-for-a-web-service"></a>Como ativar o registo de um serviço Web

Você habilita o registro em log no portal de [Serviços Web Azure Machine Learning Studio (clássico)](https://services.azureml.net) . 

1. Entre no portal de serviços Web Azure Machine Learning Studio (clássico) em [https://services.azureml.net](https://services.azureml.net). Para um serviço Web clássico, você também pode acessar o portal clicando em **nova experiência de serviços Web** na página de serviços Web Machine Learning Studio (clássico) no Studio (clássico).

   ![Nova ligação de experiência de serviços da Web](./media/web-services-logging/new-web-services-experience-link.png)

2. Na barra de menus superior, clique em **serviços Web** para um novo serviço web ou clique em **serviços Web clássicos** para um clássico de serviço web.

   ![Selecione o novo ou clássico serviços da web](./media/web-services-logging/select-web-service.png)

3. Para um novo serviço web, clique no nome de serviço da web. Para um serviço web clássico, clique no nome de serviço da web e, em seguida, na página seguinte, clique o ponto final adequado.

4. Na barra de menus superior, clique em **configurar**.

5. Definir o **Enable Logging** a opção de *erro* (para iniciar sessão apenas erros) ou *todos os* (para o registo completo).

   ![Selecione o nível de registo](./media/web-services-logging/enable-logging.png)

6. Clique em **Guardar**.

7. Para serviços da web clássico, criar os **ml-diagnostics** contentor.

   Todos os registos de serviço da web são mantidos num contentor de Blobs com o nome **ml-diagnostics** na conta de armazenamento associada ao serviço web. Para novos serviços web, este contentor é criado na primeira vez que aceder ao serviço web. Para serviços da web clássico, terá de criar o contentor se ainda não exista. 

   1. Na [portal do Azure](https://portal.azure.com), vá para a conta de armazenamento associada ao serviço web.

   2. Em **Serviço Blob**, clique em **Contentores**.

   3. Se o contentor **ml-diagnostics** não existir, clique em **+ contentor**, dê o contentor a. o nome "ml-diagnostics" e selecione o **acessar tipo** como "Blob". Clique em **OK**.

      ![Criar um novo contêiner para armazenar seus logs de diagnóstico](./media/web-services-logging/create-ml-diagnostics-container.png)

> [!TIP]
>
> Para um serviço Web clássico, o painel de serviços Web no Machine Learning Studio (clássico) também tem uma opção para habilitar o registro em log. No entanto, uma vez que o registo é agora gerenciado através do portal de serviços da Web, terá de ativar o registo através do portal conforme descrito neste artigo. Se você já tiver habilitado o log no Studio (clássico), no portal de serviços Web, desabilite o registro em log e habilite-o novamente.


## <a name="the-effects-of-enabling-logging"></a>Os efeitos de ativar o registo
Quando o registo está ativado, os diagnósticos e erros do ponto de final de serviço da web estiver conectados a **ml-diagnostics** contentor de BLOBs na conta de armazenamento do Azure que está associado ao espaço de trabalho do utilizador. Este contentor armazena todas as informações de diagnóstico para todos os web pontos finais de serviço para todas as áreas de trabalho associados a esta conta de armazenamento.

Os registos podem ser exibidos em qualquer uma das várias ferramentas disponíveis para explorar uma conta de armazenamento do Azure. O mais fácil pode ser navegar para a conta de armazenamento no portal do Azure, clique em **contentores**e, em seguida, clique em contentor **ml-diagnostics**.  

## <a name="log-blob-detail-information"></a>Informações de detalhe de blob de registo
Cada blob no contentor contém as informações de diagnóstico do exatamente uma das seguintes ações:

* Uma execução do método de execução de lotes  
* Uma execução do método de solicitação-resposta  
* Inicialização de um contentor de solicitação-resposta

O nome de cada blob tem um prefixo da seguinte forma: 


`{Workspace Id}-{Web service Id}-{Endpoint Id}/{Log type}`


Em que _tipo de registo_ é um dos seguintes valores:  

* batch  
* pontuação/pedidos  
* pontuação/init  

