---
title: Web de registo do serviço - Azure Machine Learning Studio | Documentos da Microsoft
description: Saiba como ativar o registo de serviços web Machine Learning Studio. O registo fornece informações adicionais para ajudar a resolver problemas relacionados com as APIs.
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: seodec18
ms.author: amlstudiodocs
editor: cgronlun
ms.assetid: c54d41e1-0300-46ef-bbfc-d6f7dca85086
ms.service: machine-learning
ms.subservice: studio
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/15/2017
ms.openlocfilehash: 23a160ee15547bea7d5ee832cd1032b2e899966b
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56821837"
---
# <a name="enable-logging-for-azure-machine-learning-studio-web-services"></a>Ativar o registo de serviços web Azure Machine Learning Studio
Este documento fornece informações sobre a capacidade de registos de serviços da web de Machine Learning Studio. O registo fornece informações adicionais, além de apenas um número de erro e uma mensagem, que pode ajudá-lo a resolver problemas de suas chamadas para as APIs de Machine Learning Studio.  

## <a name="how-to-enable-logging-for-a-web-service"></a>Como ativar o registo de um serviço Web

Ativar o registo a partir do [serviços Web do Azure Machine Learning Studio](https://services.azureml.net) portal. 

1. Inicie sessão no portal do Azure Machine Learning Studio Web Services em [ https://services.azureml.net ](https://services.azureml.net). Para um serviço web clássico, também pode obter o portal clicando **nova experiência de serviços da Web** na página de serviços da Web do Machine Learning Studio no Machine Learning Studio.

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

      ![Selecione o nível de registo](./media/web-services-logging/create-ml-diagnostics-container.png)

> [!TIP]
>
> Para um serviço web clássico, o Dashboard de serviços da Web no Machine Learning Studio também tem um comutador para ativar o registo. No entanto, uma vez que o registo é agora gerenciado através do portal de serviços da Web, terá de ativar o registo através do portal conforme descrito neste artigo. Se já ativou o registo no Studio, em seguida, no Portal de serviços Web, desativar o registo e ativá-la novamente.


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

* lote  
* pontuação/pedidos  
* pontuação/init  

