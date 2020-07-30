---
title: 'Estúdio ML (clássico): Ativar a sessão de serviço web - Azure'
description: Saiba como permitir a gravação de serviços web do Machine Learning Studio (clássico). O registo fornece informações adicionais para ajudar a resolver problemas nas APIs.
services: machine-learning
author: likebupt
ms.author: keli19
editor: cgronlun
ms.assetid: c54d41e1-0300-46ef-bbfc-d6f7dca85086
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
ms.date: 06/15/2017
ms.openlocfilehash: b879fac7482b000e1b92f2fcc3f7586ae7dae321
ms.sourcegitcommit: 0b8320ae0d3455344ec8855b5c2d0ab3faa974a3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/30/2020
ms.locfileid: "87430193"
---
# <a name="enable-logging-for-azure-machine-learning-studio-classic-web-services"></a>Ativar o registo de serviços web do Azure Machine Learning Studio (clássico)

**APLICA-SE A:** ![ sim ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (clássico) no ![ ](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../compare-azure-ml-to-studio-classic.md)  


Este documento fornece informações sobre a capacidade de registo de serviços web do Machine Learning Studio (clássico). O registo fornece informações adicionais, para além de apenas um número de erro e uma mensagem, que podem ajudá-lo a resolver problemas nas suas chamadas para o Machine Learning Studio (clássico) APIs.  

## <a name="how-to-enable-logging-for-a-web-service"></a>Como permitir a sessão de registo de um serviço Web

Você permite iniciar sessão a partir do portal [Azure Machine Learning Studio (clássico) Web Services.](https://services.azureml.net) 

1. Inscreva-se no portal Azure Machine Learning Studio (clássico) dos Serviços Web em [https://services.azureml.net](https://services.azureml.net) . Para um serviço web clássico, você também pode chegar ao portal clicando em **New Web Services Experience** no Machine Learning Studio (clássico) página de Web Services em Studio (clássico).

   ![Nova ligação de experiência de serviços web](./media/web-services-logging/new-web-services-experience-link.png)

2. Na barra de menu superior, clique em **Web Services** para um novo serviço web ou clique em Classic **Web Services** para um serviço web clássico.

   ![Selecione serviços web novos ou clássicos](./media/web-services-logging/select-web-service.png)

3. Para um novo serviço web, clique no nome do serviço web. Para um serviço web clássico, clique no nome do serviço web e, em seguida, na página seguinte clique no ponto final apropriado.

4. Na barra de menu superior, clique em **Configurar**.

5. Desa estade a opção **De Fazer O** *Registo* (para registar apenas erros) ou *Todos* (para registos completos).

   ![Selecione o nível de registo](./media/web-services-logging/enable-logging.png)

6. Clique em **Save** (Guardar).

7. Para serviços web clássicos, crie o recipiente **ml-diagnóstico.**

   Todos os registos de serviço web são mantidos num recipiente de bolhas chamado **ml-diagnósticos** na conta de armazenamento associada ao serviço web. Para novos serviços web, este recipiente é criado na primeira vez que acede ao serviço web. Para serviços web clássicos, você precisa criar o recipiente se ele já não existe. 

   1. No [portal Azure,](https://portal.azure.com)aceda à conta de armazenamento associada ao serviço web.

   2. No **serviço Blob,** clique em **Recipientes**.

   3. Se o recipiente **ml-diagnóstico** não existir, clique **em +Recipiente,** dê ao recipiente o nome "diagnósticos ml", e selecione o **tipo de Acesso** como "Blob". Clique em **OK**.

      ![Crie um novo recipiente para armazenar os seus registos de diagnóstico](./media/web-services-logging/create-ml-diagnostics-container.png)

> [!TIP]
>
> Para um serviço web clássico, o Web Services Dashboard in Machine Learning Studio (clássico) também tem um interruptor para permitir a gravação. No entanto, uma vez que o login é agora gerido através do portal de Serviços Web, é necessário ativar o registo através do portal, conforme descrito neste artigo. Se já ativou o login no Studio (clássico), então no Portal dos Serviços Web, desative o registo e ative-o novamente.


## <a name="the-effects-of-enabling-logging"></a>Os efeitos da ativação do abate
Quando o registo está ativado, os diagnósticos e erros do ponto final do serviço web são registados no recipiente de **bolhas de diagnóstico ml** na Conta de Armazenamento Azure ligada ao espaço de trabalho do utilizador. Este recipiente contém todas as informações de diagnóstico de todos os pontos finais do serviço web para todos os espaços de trabalho associados a esta conta de armazenamento.

Os registos podem ser vistos utilizando qualquer uma das várias ferramentas disponíveis para explorar uma Conta de Armazenamento Azure. O mais fácil pode ser navegar até à conta de armazenamento no portal Azure, clicar em **Recipientes**e, em seguida, clicar nos **diagnósticos ml do**recipiente .  

## <a name="log-blob-detail-information"></a>Informação detalhada do blob de log
Cada bolha no recipiente contém a informação de diagnóstico para uma das seguintes ações:

* Uma execução do método de execução de lote  
* Uma execução do método de resposta de pedido  
* Inicialização de um recipiente de resposta de pedido

O nome de cada bolha tem um prefixo do seguinte formulário: 


`{Workspace Id}-{Web service Id}-{Endpoint Id}/{Log type}`


Quando _o tipo de registo_ é um dos seguintes valores:  

* lote  
* pontuação/pedidos  
* pontuação/init  

