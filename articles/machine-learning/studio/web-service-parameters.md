---
title: Parâmetros de serviço web - Azure Machine Learning Studio (clássico) / Microsoft Docs
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
ms.openlocfilehash: d6ddd9603f22bd3820d18be020b9c620cf06aa42
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79204414"
---
# <a name="use-azure-machine-learning-studio-classic-web-service-parameters"></a>Utilize os parâmetros do serviço web Azure Machine Learning Studio (clássico)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Um serviço web Azure Machine Learning é criado ao publicar uma experimentação que contém módulos com parâmetros configuráveis. Em alguns casos, talvez queira alterar o comportamento de módulo, enquanto o serviço web está em execução. *Os parâmetros* do Serviço Web permitem-lhe fazer esta tarefa. 

Um exemplo comum é a criação do módulo dados de [importação][reader] para que o utilizador do serviço web publicado possa especificar uma fonte de dados diferente quando o serviço web é acedido. Ou configurar o módulo de Dados de [Exportação][writer] para que um destino diferente possa ser especificado. Alguns outros exemplos incluem alterar o número de bits para o módulo [de hashing feature][feature-hashing] ou o número de funcionalidades desejadas para o módulo de [seleção de funcionalidades baseado][filter-based-feature-selection] no filtro. 

Pode definir parâmetros do serviço Web e associá-las com um ou mais parâmetros do módulo na sua experimentação e pode especificar se são necessárias ou opcionais. O utilizador do serviço web, em seguida, pode fornecer valores para estes parâmetros quando eles chamam o serviço web. 



## <a name="how-to-set-and-use-web-service-parameters"></a>Como configurar e utilizar parâmetros do serviço Web
Definir um parâmetro de serviço da Web ao clicar no ícone junto ao parâmetro para um módulo e selecionando "Definir como parâmetro de serviço web". Isso cria um novo parâmetro de serviço da Web e liga-o para esse parâmetro de módulo. Em seguida, quando o serviço da web é acedido, o utilizador pode especificar um valor para o parâmetro de serviço da Web e é aplicada para o parâmetro de módulo.

Depois de definir um parâmetro de serviço da Web, está disponível para qualquer outro parâmetro do módulo na experimentação. Se definir um parâmetro de serviço da Web associado a um parâmetro para um módulo, pode utilizar esse mesmo parâmetro de serviço da Web para qualquer outro módulo, desde que o parâmetro espera que o mesmo tipo de valor. Por exemplo, se o parâmetro de serviço da Web é um valor numérico, em seguida, ele pode apenas ser usado para os parâmetros do módulo que esperam um valor numérico. Quando o utilizador define um valor para o parâmetro de serviço da Web, será aplicada a todos os parâmetros do módulo.

Pode decidir se pretende fornecer um valor predefinido para o parâmetro de serviço da Web. Se o fizer, o parâmetro é opcional para o utilizador do serviço web. Se não fornecer um valor predefinido, em seguida, é pedido ao utilizador para introduzir um valor quando o serviço web é acessado.

A documentação da API para o serviço web inclui informações para o utilizador de serviço da web sobre como especificar o parâmetro de serviço Web por meio de programação, ao acessar o serviço web.

> [!NOTE]
> A documentação da API para um serviço web clássico é fornecida através do link de página de **ajuda da API** no serviço web **DASHBOARD** em Machine Learning Studio (clássico). A documentação da API para um novo serviço web é fornecida através do portal [Azure Machine Learning Web Services](https://services.azureml.net/Quickstart) nas páginas **Consumir** e **Swagger API** para o seu serviço web.
> 
> 

## <a name="example"></a>Exemplo
Como exemplo, vamos supor que temos uma experiência com um módulo de Dados de [Exportação][writer] que envia informações para o armazenamento de blob Azure. Vamos definir um parâmetro de serviço da Web com o nome "Caminho do Blob" que permite ao usuário de serviço da web alterar o caminho para o armazenamento de BLOBs, quando o serviço for acedido.

1. No Machine Learning Studio (clássico), clique no módulo Dados de [Exportação][writer] para selecioná-lo. Suas propriedades são apresentadas no painel de propriedades para a direita da tela de experimentação.
2. Especifique o tipo de armazenamento:
   
   * Em **prevaispor o destino dos dados,** selecione "Armazenamento de Blob Azure".
   * Em **' Especifique o tipo**de autenticação , selecione "Conta".
   * Introduza as informações de conta para o armazenamento de Blobs do Azure. 

3. Clique no ícone à direita do **Caminho para a bolha a partir do parâmetro do recipiente**. Isso fica assim:
   
   ![Ícone de parâmetro de serviço da Web](./media/web-service-parameters/icon.png)
   
   Selecione "Definir como parâmetro de serviço web".
   
   Uma entrada é adicionada nos **parâmetros** do Serviço Web na parte inferior do painel Properties com o nome "Caminho para a bolha começando com o recipiente". Este é o parâmetro do Serviço Web que está agora associado a este parâmetro de módulo de [dados de exportação.][writer]
4. Para mudar o nome do Parâmetro do Serviço Web, clique no nome, introduza "Caminho blob", e prima a tecla **Enter.** 
5. Para fornecer um valor predefinido para o Parâmetro do Serviço Web, clique no ícone à direita do nome, selecione "Fornecer valor predefinido", introduza um valor (por exemplo, "contentor1/output1.csv"), e prima a tecla **Enter.**
   
   ![Parâmetro de serviço da Web](./media/web-service-parameters/parameter.png)
6. Clique em **Executar**. 
7. Clique em **implementar o Serviço Web** e selecione Implementar o Web Service **[Classic]** ou implementar o **Serviço Web [Novo]** para implementar o serviço web.

> [!NOTE] 
> Para implementar um novo serviço web tem de ter permissões suficientes na subscrição para a qual estiver a implementar o serviço web. Para mais informações consulte, [Gerencie um serviço Web utilizando o portal Azure Machine Learning Web Services](manage-new-webservice.md). 

O utilizador do serviço web pode agora especificar um novo destino para o módulo de Dados de [Exportação][writer] ao aceder ao serviço web.

## <a name="more-information"></a>Mais informações
Para um exemplo mais detalhado, consulte a entrada dos [Parâmetros](https://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx) do Serviço Web no Blog de [Aprendizagem automática.](https://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx)

Para obter mais informações sobre o acesso a um serviço web machine learning, consulte [Como consumir um serviço Web de Aprendizagem Automática Azure](consume-web-services.md).

<!-- Module References -->
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[writer]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/

