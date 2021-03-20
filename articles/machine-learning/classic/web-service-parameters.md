---
title: 'Estúdio ML (clássico): Parâmetros de serviço web - Azure'
description: Como utilizar os parâmetros de serviço web de aprendizagem de máquinas Azure para modificar o comportamento do seu modelo quando o serviço web é acedido.
services: machine-learning
author: likebupt
ms.author: keli19
ms.assetid: c49187db-b976-4731-89d6-11a0bf653db1
ms.service: machine-learning
ms.subservice: studio-classic
ms.workload: data-services
ms.topic: how-to
ms.date: 01/12/2017
ms.openlocfilehash: 49814d01209b58666c011a6bbd7fe6c328d460f8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100518339"
---
# <a name="use-azure-machine-learning-studio-classic-web-service-parameters"></a>Use parâmetros de serviço web Azure Machine Learning Studio (clássicos)

**APLICA-SE A:** ![ Aplica-se a. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (clássico) ![ Não se aplica a.](../../../includes/media/aml-applies-to-skus/no.png)[ Aprendizagem de Máquinas Azure](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  


Um serviço web Azure Machine Learning é criado através da publicação de uma experiência que contém módulos com parâmetros configuráveis. Em alguns casos, pode querer alterar o comportamento do módulo enquanto o serviço web está em funcionamento. *Os Parâmetros do Serviço Web* permitem-lhe fazer esta tarefa. 

Um exemplo comum é a criação do módulo [de Dados de Importação][reader] para que o utilizador do serviço web publicado possa especificar uma fonte de dados diferente quando o serviço web é acedido. Ou configurar o módulo [de Dados de Exportação][writer] para que um destino diferente possa ser especificado. Alguns outros exemplos incluem a alteração do número de bits para o módulo [de hashing de recurso][feature-hashing] ou o número de funcionalidades desejadas para o módulo de [seleção de recursos baseado em filtros.][filter-based-feature-selection] 

Pode definir parâmetros de serviço web e associá-los a um ou mais parâmetros de módulo na sua experiência, e pode especificar se são necessários ou opcionais. O utilizador do serviço web pode então fornecer valores para estes parâmetros quando eles chamam o serviço web. 



## <a name="how-to-set-and-use-web-service-parameters"></a>Como definir e usar parâmetros de serviço web
Define um Parâmetro de Serviço Web clicando no ícone ao lado do parâmetro para um módulo e selecionando "Definir como parâmetro de serviço web". Isto cria um novo parâmetro de serviço web e liga-o ao parâmetro do módulo. Em seguida, quando o serviço web é acedido, o utilizador pode especificar um valor para o Parâmetro de Serviço Web e é aplicado ao parâmetro do módulo.

Uma vez definido um parâmetro de serviço web, está disponível para qualquer outro parâmetro do módulo na experiência. Se definir um Parâmetro de Serviço Web associado a um parâmetro para um módulo, pode utilizar o mesmo Parâmetro de Serviço Web para qualquer outro módulo, desde que o parâmetro espere o mesmo tipo de valor. Por exemplo, se o Parâmetro de Serviço Web é um valor numérico, então só pode ser usado para parâmetros de módulos que esperam um valor numérico. Quando o utilizador definir um valor para o Parâmetro de Serviço Web, este será aplicado a todos os parâmetros do módulo associados.

Pode decidir se fornece um valor predefinido para o Parâmetro de Serviço Web. Se o fizer, o parâmetro é opcional para o utilizador do serviço web. Se não fornecer um valor predefinido, então o utilizador é obrigado a introduzir um valor quando o serviço web é acedido.

A documentação da API para o serviço web inclui informações para o utilizador do serviço web sobre como especificar o Parâmetro de Serviço Web programáticamente ao aceder ao serviço web.

> [!NOTE]
> A documentação da API para um serviço web clássico é fornecida através do link da página de ajuda da **API** no serviço web **DASHBOARD** em Machine Learning Studio (clássico). A documentação da API para um novo serviço web é fornecida através do portal [Azure Machine Learning Web Services](https://services.azureml.net/Quickstart) nas páginas **API Consume** e **Swagger** para o seu serviço web.
> 
> 

## <a name="example"></a>Exemplo
Como exemplo, vamos supor que temos uma experiência com um módulo [de Dados de Exportação][writer] que envia informações para o armazenamento de blob Azure. Vamos definir um Parâmetro de Serviço Web chamado "Caminho da Bolha" que permite ao utilizador do serviço web alterar o caminho para o armazenamento de bolhas quando o serviço é acedido.

1. No Machine Learning Studio (clássico), clique no módulo [de Dados de Exportação][writer] para selecioná-lo. As suas propriedades são mostradas no painel propriedades à direita da tela experimentadas.
2. Especificar o tipo de armazenamento:
   
   * Em **Please specify data destination**, selecione "Azure Blob Storage".
   * Por **favor, especifique o tipo de autenticação,** selecione "Conta".
   * Introduza as informações de conta para o armazenamento da bolha Azure. 

3. Clique no ícone à direita do **Caminho para blob começando com o parâmetro do recipiente**. Esta secção tem o seguinte aspeto:
   
   ![Ícone do parâmetro do serviço web](./media/web-service-parameters/icon.png)
   
   Selecione "Definir como parâmetro de serviço web".
   
   Uma entrada é adicionada nos **Parâmetros de Serviço Web** na parte inferior do painel propriedades com o nome "Caminho para blob começando com recipiente". Este é o Parâmetro de Serviço Web que está agora associado a este parâmetro do módulo [de dados de exportação.][writer]
4. Para mudar o nome do Parâmetro de Serviço Web, clique no nome, introduza "Caminho da bolha" e prima a tecla **'Inserir'.** 
5. Para fornecer um valor predefinido para o Parâmetro de Serviço Web, clique no ícone à direita do nome, selecione "Fornecer valor predefinido", introduza um valor (por exemplo, "contentor1/output1.csv"), e prima a tecla **'Inserir'.**
   
   ![Parâmetro de serviço web](./media/web-service-parameters/parameter.png)
6. Clique em **Run** (Executar). 
7. Clique **em Implementar o Serviço Web** e selecione Implementar o Serviço Web **[Clássico]** ou implementar o Serviço Web **[Novo]** para implementar o serviço web.

> [!NOTE] 
> Para implementar um Novo serviço web tem de ter permissões suficientes na subscrição à qual implementa o serviço web. Para obter mais informações, [gerencie um serviço Web utilizando o portal Azure Machine Learning Web Services](manage-new-webservice.md). 

O utilizador do serviço web pode agora especificar um novo destino para o módulo [de Dados de Exportação][writer] ao aceder ao serviço web.

## <a name="more-information"></a>Mais informações
Para um exemplo mais detalhado, consulte a entrada [dos Parâmetros](/archive/blogs/machinelearning/azureml-web-service-parameters) de Serviço Web no [Blog de Aprendizagem automática.](/archive/blogs/machinelearning/azureml-web-service-parameters)

Para obter mais informações sobre o acesso a um serviço web machine learning, consulte [Como consumir um serviço Web de Aprendizagem automática Azure.](consume-web-services.md)

<!-- Module References -->
[feature-hashing]: /azure/machine-learning/studio-module-reference/feature-hashing
[filter-based-feature-selection]: /previous-versions/azure/dn905854(v=azure.100)
[reader]: /azure/machine-learning/studio-module-reference/import-data
[writer]: /azure/machine-learning/studio-module-reference/export-data