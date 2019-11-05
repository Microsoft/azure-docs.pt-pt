---
title: Parâmetros do serviço Web-Azure Machine Learning Studio (clássico) | Microsoft Docs
description: Como usar Azure Machine Learning parâmetros de serviço Web para modificar o comportamento do seu modelo quando o serviço Web é acessado.
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
ms.openlocfilehash: 62c6488cfcb30c969c388343c766c482cff7e03b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73466986"
---
# <a name="use-azure-machine-learning-studio-classic-web-service-parameters"></a>Usar parâmetros de serviço Web Azure Machine Learning Studio (clássico)
Um serviço Web Azure Machine Learning é criado por meio da publicação de um experimento que contém módulos com parâmetros configuráveis. Em alguns casos, talvez você queira alterar o comportamento do módulo enquanto o serviço Web está em execução. Os *parâmetros do serviço Web* permitem que você execute essa tarefa. 

Um exemplo comum é a configuração do módulo [importar dados][reader] para que o usuário do serviço Web publicado possa especificar uma fonte de dados diferente quando o serviço Web é acessado. Ou configurar o módulo [exportar dados][writer] para que um destino diferente possa ser especificado. Alguns outros exemplos incluem a alteração do número de bits para o módulo [hash de recurso][feature-hashing] ou o número de recursos desejados para o módulo [seleção de recursos baseada em filtro][filter-based-feature-selection] . 

Você pode definir parâmetros de serviço Web e associá-los a um ou mais parâmetros de módulo em seu experimento, e pode especificar se eles são obrigatórios ou opcionais. O usuário do serviço Web pode fornecer valores para esses parâmetros ao chamar o serviço Web. 



## <a name="how-to-set-and-use-web-service-parameters"></a>Como definir e usar parâmetros de serviço Web
Você define um parâmetro de serviço Web clicando no ícone ao lado do parâmetro de um módulo e selecionando "definir como parâmetro de serviço Web". Isso cria um novo parâmetro de serviço Web e o conecta a esse parâmetro de módulo. Em seguida, quando o serviço Web é acessado, o usuário pode especificar um valor para o parâmetro do serviço Web e é aplicado ao parâmetro do módulo.

Depois de definir um parâmetro de serviço Web, ele estará disponível para qualquer outro parâmetro de módulo no experimento. Se você definir um parâmetro de serviço Web associado a um parâmetro para um módulo, poderá usar esse mesmo parâmetro de serviço Web para qualquer outro módulo, contanto que o parâmetro Espere o mesmo tipo de valor. Por exemplo, se o parâmetro de serviço Web for um valor numérico, ele só poderá ser usado para parâmetros de módulo que esperam um valor numérico. Quando o usuário define um valor para o parâmetro de serviço Web, ele será aplicado a todos os parâmetros de módulo associados.

Você pode decidir se deseja fornecer um valor padrão para o parâmetro de serviço Web. Se você fizer isso, o parâmetro será opcional para o usuário do serviço Web. Se você não fornecer um valor padrão, o usuário será solicitado a inserir um valor quando o serviço Web for acessado.

A documentação da API para o serviço Web inclui informações para o usuário do serviço Web sobre como especificar o parâmetro do serviço Web programaticamente ao acessar o serviço Web.

> [!NOTE]
> A documentação da API para um serviço Web clássico é fornecida por meio do link da **página de ajuda da API** no **painel** do serviço Web no Machine Learning Studio (clássico). A documentação da API para um novo serviço Web é fornecida por meio do portal de [serviços da web Azure Machine Learning](https://services.azureml.net/Quickstart) nas páginas de API **consume** e **Swagger** para seu serviço Web.
> 
> 

## <a name="example"></a>Exemplo
Por exemplo, vamos supor que tenhamos um experimento com um módulo [exportar dados][writer] que envia informações para o armazenamento de BLOBs do Azure. Vamos definir um parâmetro de serviço Web chamado "caminho do blob" que permite ao usuário do serviço Web alterar o caminho para o armazenamento de BLOBs quando o serviço é acessado.

1. Na versão clássica do Machine Learning Studio, clique no módulo [exportar dados][writer] para selecioná-lo. Suas propriedades são mostradas no painel Propriedades à direita da tela do experimento.
2. Especifique o tipo de armazenamento:
   
   * Em **especificar destino de dados**, selecione "armazenamento de BLOBs do Azure".
   * Em **especificar tipo de autenticação**, selecione "conta".
   * Insira as informações da conta para o armazenamento de BLOBs do Azure. 

3. Clique no ícone à direita do **caminho para o blob que começa com o parâmetro de contêiner**. Ele tem a seguinte aparência:
   
   ![Ícone de parâmetro de serviço Web](./media/web-service-parameters/icon.png)
   
   Selecione "definir como parâmetro de serviço Web".
   
   Uma entrada é adicionada em **parâmetros de serviço Web** na parte inferior do painel Propriedades com o nome "caminho para o blob começando com o contêiner". Esse é o parâmetro de serviço Web que agora está associado a esse parâmetro de módulo [exportar dados][writer] .
4. Para renomear o parâmetro do serviço Web, clique no nome, insira "caminho do blob" e pressione a tecla **Enter** . 
5. Para fornecer um valor padrão para o parâmetro de serviço Web, clique no ícone à direita do nome, selecione "fornecer valor padrão", insira um valor (por exemplo, "Container1/Saída1. csv") e pressione a tecla **Enter** .
   
   ![Parâmetro de serviço Web](./media/web-service-parameters/parameter.png)
6. Clique em **Executar**. 
7. Clique em **implantar serviço Web** e selecione **implantar serviço Web [clássico]** ou **implantar serviço Web [novo]** para implantar o serviço Web.

> [!NOTE] 
> Para implantar um novo serviço Web, você deve ter permissões suficientes na assinatura na qual você está implantando o serviço Web. Para obter mais informações, consulte [gerenciar um serviço Web usando o Azure Machine Learning Portal de serviços Web](manage-new-webservice.md). 

O usuário do serviço Web agora pode especificar um novo destino para o módulo [exportar dados][writer] ao acessar o serviço Web.

## <a name="more-information"></a>Mais informações
Para obter um exemplo mais detalhado, consulte a entrada [parâmetros de serviço Web](https://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx) no [blog Machine Learning](https://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx).

Para obter mais informações sobre como acessar um serviço Web Machine Learning, consulte [como consumir um serviço web Azure Machine Learning](consume-web-services.md).

<!-- Module References -->
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[writer]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/

