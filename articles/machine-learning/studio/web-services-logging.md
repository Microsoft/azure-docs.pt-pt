---
title: Log do serviço Web-Azure Machine Learning Studio (clássico) | Microsoft Docs
description: Saiba como habilitar o registro em log para serviços da Web Machine Learning Studio (clássico). O registro em log fornece informações adicionais para ajudar a solucionar problemas das APIs.
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
ms.openlocfilehash: 90ee036275e13b209d9f6c37aba6d16ed564cf5d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73466955"
---
# <a name="enable-logging-for-azure-machine-learning-studio-classic-web-services"></a>Habilitar o registro em log para serviços Web Azure Machine Learning Studio (clássico)
Este documento fornece informações sobre o recurso de log dos serviços Web Machine Learning Studio (clássico). O registro em log fornece informações adicionais, além de apenas um número de erro e uma mensagem, que podem ajudá-lo a solucionar suas chamadas para as APIs de Machine Learning Studio (clássicas).  

## <a name="how-to-enable-logging-for-a-web-service"></a>Como habilitar o registro em log para um serviço Web

Você habilita o registro em log no portal de [Serviços Web Azure Machine Learning Studio (clássico)](https://services.azureml.net) . 

1. Entre no portal de serviços Web Azure Machine Learning Studio (clássico) em [https://services.azureml.net](https://services.azureml.net). Para um serviço Web clássico, você também pode acessar o portal clicando em **nova experiência de serviços Web** na página de serviços Web Machine Learning Studio (clássico) na versão clássica do Machine Learning Studio.

   ![Novo link de experiência de serviços Web](./media/web-services-logging/new-web-services-experience-link.png)

2. Na barra de menus superior, clique em **Serviços Web** para um novo serviço Web ou clique em **Serviços Web clássicos** para um serviço Web clássico.

   ![Selecionar Serviços Web novos ou clássicos](./media/web-services-logging/select-web-service.png)

3. Para um novo serviço Web, clique no nome do serviço Web. Para um serviço Web clássico, clique no nome do serviço Web e, na próxima página, clique no ponto de extremidade apropriado.

4. Na barra de menus superior, clique em **Configurar**.

5. Defina a opção **habilitar registro em log** como *erro* (para registrar apenas erros) ou *todos* (para log completo).

   ![Selecionar nível de log](./media/web-services-logging/enable-logging.png)

6. Clique em **Guardar**.

7. Para serviços Web clássicos, crie o contêiner **ml-Diagnostics** .

   Todos os logs de serviço Web são mantidos em um contêiner de blob chamado **ml-Diagnostics** na conta de armazenamento associada ao serviço Web. Para novos serviços Web, esse contêiner é criado na primeira vez que você acessa o serviço Web. Para serviços Web clássicos, você precisa criar o contêiner se ele ainda não existir. 

   1. Na [portal do Azure](https://portal.azure.com), vá para a conta de armazenamento associada ao serviço Web.

   2. Em **Serviço Blob**, clique em **Contentores**.

   3. Se o contêiner **ml-Diagnostics** não existir, clique em **+ contêiner**, dê ao contêiner o nome "ml-Diagnostics" e selecione o **tipo de acesso** como "blob". Clique em **OK**.

      ![Criar um novo contêiner para armazenar seus logs de diagnóstico](./media/web-services-logging/create-ml-diagnostics-container.png)

> [!TIP]
>
> Para um serviço Web clássico, o painel de serviços Web na versão clássica do Machine Learning Studio também tem uma opção para habilitar o registro em log. No entanto, como o log agora é gerenciado por meio do portal de serviços Web, você precisa habilitar o registro em log por meio do portal, conforme descrito neste artigo. Se você já tiver habilitado o log no Studio (clássico), no portal de serviços Web, desabilite o registro em log e habilite-o novamente.


## <a name="the-effects-of-enabling-logging"></a>Os efeitos de habilitar o registro em log
Quando o registro em log está habilitado, o diagnóstico e os erros do ponto de extremidade do serviço Web são registrados no contêiner de blob do **ml-Diagnostics** na conta de armazenamento do Azure vinculada ao espaço de trabalho do usuário. Esse contêiner contém todas as informações de diagnóstico para todos os pontos de extremidade de serviço Web para todos os espaços de trabalho associados a essa conta de armazenamento.

Os logs podem ser exibidos usando qualquer uma das várias ferramentas disponíveis para explorar uma conta de armazenamento do Azure. A mais fácil pode ser navegar para a conta de armazenamento no portal do Azure, clicar em **contêineres**e clicar no contêiner **ml-Diagnostics**.  

## <a name="log-blob-detail-information"></a>Informações detalhadas do blob de log
Cada blob no contêiner contém as informações de diagnóstico para exatamente uma das seguintes ações:

* Uma execução do método de execução em lotes  
* Uma execução do método de solicitação-resposta  
* Inicialização de um contêiner de solicitação-resposta

O nome de cada blob tem um prefixo do seguinte formato: 


`{Workspace Id}-{Web service Id}-{Endpoint Id}/{Log type}`


Em que _tipo de log_ é um dos seguintes valores:  

* lote  
* Pontuação/solicitações  
* Pontuação/inicialização  

