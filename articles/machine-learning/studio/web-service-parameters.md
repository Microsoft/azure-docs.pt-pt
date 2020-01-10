---
title: Parâmetros do serviço Web-Azure Machine Learning Studio (clássico) | Microsoft Docs
description: Como utilizar parâmetros do serviço Web Azure Machine Learning para modificar o comportamento do seu modelo quando o serviço web é acessado.
services: machine-learning
author: xiaoharper
ms.custom: seodec18
ms.author: amlstudiodocs
editor: cgronlun
ms.assetid: c49187db-b976-4731-89d6-11a0bf653db1
ms.service: machine-learning
ms.subservice: studio
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/12/2017
ms.openlocfilehash: 984d2e02ff75df459275fd10e313a4950c8d79c0
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75432186"
---
# <a name="use-azure-machine-learning-studio-classic-web-service-parameters"></a>Usar parâmetros de serviço Web Azure Machine Learning Studio (clássico)
Um serviço web Azure Machine Learning é criado ao publicar uma experimentação que contém módulos com parâmetros configuráveis. Em alguns casos, talvez queira alterar o comportamento de módulo, enquanto o serviço web está em execução. *Parâmetros de serviço da Web* permitem-lhe executar esta tarefa. 

Um exemplo comum é a configuração do módulo [importar dados][reader] para que o usuário do serviço Web publicado possa especificar uma fonte de dados diferente quando o serviço Web é acessado. Ou configurar o módulo [exportar dados][writer] para que um destino diferente possa ser especificado. Alguns outros exemplos incluem a alteração do número de bits para o módulo [hash de recurso][feature-hashing] ou o número de recursos desejados para o módulo [seleção de recursos baseada em filtro][filter-based-feature-selection] . 

Pode definir parâmetros do serviço Web e associá-las com um ou mais parâmetros do módulo na sua experimentação e pode especificar se são necessárias ou opcionais. O utilizador do serviço web, em seguida, pode fornecer valores para estes parâmetros quando eles chamam o serviço web. 



## <a name="how-to-set-and-use-web-service-parameters"></a>Como configurar e utilizar parâmetros do serviço Web
Definir um parâmetro de serviço da Web ao clicar no ícone junto ao parâmetro para um módulo e selecionando "Definir como parâmetro de serviço web". Isso cria um novo parâmetro de serviço da Web e liga-o para esse parâmetro de módulo. Em seguida, quando o serviço da web é acedido, o utilizador pode especificar um valor para o parâmetro de serviço da Web e é aplicada para o parâmetro de módulo.

Depois de definir um parâmetro de serviço da Web, está disponível para qualquer outro parâmetro do módulo na experimentação. Se definir um parâmetro de serviço da Web associado a um parâmetro para um módulo, pode utilizar esse mesmo parâmetro de serviço da Web para qualquer outro módulo, desde que o parâmetro espera que o mesmo tipo de valor. Por exemplo, se o parâmetro de serviço da Web é um valor numérico, em seguida, ele pode apenas ser usado para os parâmetros do módulo que esperam um valor numérico. Quando o utilizador define um valor para o parâmetro de serviço da Web, será aplicada a todos os parâmetros do módulo.

Pode decidir se pretende fornecer um valor predefinido para o parâmetro de serviço da Web. Se o fizer, o parâmetro é opcional para o utilizador do serviço web. Se não fornecer um valor predefinido, em seguida, é pedido ao utilizador para introduzir um valor quando o serviço web é acessado.

A documentação da API para o serviço web inclui informações para o utilizador de serviço da web sobre como especificar o parâmetro de serviço Web por meio de programação, ao acessar o serviço web.

> [!NOTE]
> A documentação da API para um serviço Web clássico é fornecida por meio do link da **página de ajuda da API** no **painel** do serviço Web no Machine Learning Studio (clássico). A documentação da API para um novo serviço web é fornecida através da [serviços da Web do Azure Machine Learning](https://services.azureml.net/Quickstart) portal no **Consume** e **API do Swagger** páginas para a web serviço.
> 
> 

## <a name="example"></a>Exemplo
Por exemplo, vamos supor que tenhamos um experimento com um módulo [exportar dados][writer] que envia informações para o armazenamento de BLOBs do Azure. Vamos definir um parâmetro de serviço da Web com o nome "Caminho do Blob" que permite ao usuário de serviço da web alterar o caminho para o armazenamento de BLOBs, quando o serviço for acedido.

1. Em Machine Learning Studio (clássico), clique no módulo [exportar dados][writer] para selecioná-lo. Suas propriedades são apresentadas no painel de propriedades para a direita da tela de experimentação.
2. Especifique o tipo de armazenamento:
   
   * Sob **especifique o destino dos dados**, selecione "Armazenamento de Blobs do Azure".
   * Sob **especifique o tipo de autenticação**, selecione "Conta".
   * Introduza as informações de conta para o armazenamento de Blobs do Azure. 

3. Clique no ícone à direita dos **caminho para o parâmetro de contentor a partir de BLOBs**. Isso fica assim:
   
   ![Ícone de parâmetro de serviço da Web](./media/web-service-parameters/icon.png)
   
   Selecione "Definir como parâmetro de serviço web".
   
   Uma entrada é adicionada sob **parâmetros do serviço Web** na parte inferior do painel de propriedades com o nome "Caminho para o início com o contentor de BLOBs". Esse é o parâmetro de serviço Web que agora está associado a esse parâmetro de módulo [exportar dados][writer] .
4. Para mudar o nome do parâmetro de serviço da Web, clique no nome, introduza "Caminho do Blob" e prima a **Enter** chave. 
5. Para fornecer um valor predefinido para o parâmetro de serviço da Web, clique no ícone à direita do nome, selecione "Fornecer o valor predefinido", introduza um valor (por exemplo, "container1/output1.csv") e prima a **Enter** chave.
   
   ![Parâmetro de serviço da Web](./media/web-service-parameters/parameter.png)
6. Clique em **Executar**. 
7. Clique em **implementar serviço Web** e selecione **implementar o serviço Web [clássica]** ou **implementar o Web Service [novo]** para implementar o serviço web.

> [!NOTE] 
> Para implementar um novo serviço web tem de ter permissões suficientes na subscrição para a qual estiver a implementar o serviço web. Para obter mais informações, consulte [gerir um serviço Web através do portal do Azure Machine Learning Web Services](manage-new-webservice.md). 

O usuário do serviço Web agora pode especificar um novo destino para o módulo [exportar dados][writer] ao acessar o serviço Web.

## <a name="more-information"></a>Mais informações
Para obter um exemplo mais detalhado, consulte a [parâmetros do serviço Web](https://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx) entrada no [blogue de Machine Learning](https://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx).

Para obter mais informações sobre como acessar um serviço web do Machine Learning, consulte [como consumir um serviço Web do Azure Machine Learning](consume-web-services.md).

<!-- Module References -->
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[writer]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/

