---
title: Atualizar o host OS para cluster de computação e exemplo
titleSuffix: Azure Machine Learning
description: Atualize o oss anfitrião para o cluster de computação e instância computacional de Ubuntu 16.04 LTS para 18.04 LTS.
services: machine-learning
author: nishankgu
ms.author: nigup
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.date: 03/03/2021
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 710a860b1ed87f176b6f42b4963dad17acb323b1
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "104954059"
---
# <a name="upgrade-compute-instance-and-compute-cluster-host-os"></a>Atualizar instância de computação e computação cluster host OS

O __cluster compute e__ __a computação__ Azure Machine Learning são uma infraestrutura de computação gerida. Como um serviço gerido, a Microsoft gere o os SO anfitrião e os pacotes e versões de software que estão instalados.

O hospedeiro SO para o cluster de computação e instância computacional foi Ubuntu 16.04 LTS. No **dia 30 de abril de 2021,** a Ubuntu termina o apoio às 16h04. A partir de __15 de março de 2021,__ a Microsoft atualizará automaticamente o host OS para Ubuntu 18.04 LTS. A atualização para 18.04 garantirá atualizações de segurança contínuas e apoio da comunidade Ubuntu. Esta atualização será lançada em todas as regiões do Azure e estará disponível em todas as regiões até __09 de abril de 2021__. Para obter mais informações sobre o suporte final da Ubuntu para 16.04, consulte o [blog de lançamento da Ubuntu](https://wiki.ubuntu.com/Releases).

> [!TIP]
> * O sistema operativo anfitrião não é a versão SO que pode especificar para um [ambiente](how-to-use-environments.md) quando treina ou implementa um modelo. Os ambientes correm dentro do Docker. Docker corre no hospedeiro os.
> * Se estiver a utilizar atualmente ambientes baseados em Ubuntu 16.04 para treino ou implementação, a Microsoft recomenda que mude a utilizar imagens baseadas em Ubuntu 18.04. Para obter mais informações, consulte [como utilizar ambientes](how-to-use-environments.md) e o [repositório de aprendizagem automática Azure.](https://github.com/Azure/AzureML-Containers/tree/master/base)
> * Ao utilizar um caso de computação Azure Machine Learning baseado em Ubuntu 18.04, a versão padrão python é _Python 3.8_.
## <a name="creating-new-resources"></a>Criação de novos recursos

Compute cluster ou compute instances criados após __09 de abril de 2021__ usam Ubuntu 18.04 LTS como o hospedeiro SO por padrão. Não é possível selecionar um sistema operativo de anfitrião diferente.

## <a name="upgrade-existing-resources"></a>Atualizar os recursos existentes

Se tiver clusters de computação ou instâncias computativas existentes criadas antes __de 15 de março de 2021,__ precisa de tomar medidas para atualizar o ostensos hospedeiros para Ubuntu 18.04. Dependendo da região a que aceda Azure Machine Learning, recomendamos que tome estas ações depois de 09 de abril de __2021__ para garantir que as nossas mudanças foram lançadas para todas as regiões:

* __Cluster de computação Azure Machine Learning:__

    * Se o cluster estiver configurado com __nónóulas min = 0,__ será automaticamente atualizado quando todos os trabalhos estiverem concluídos e reduzir-se a zero nós.
    * Se __os nós min > 0,__ altere temporariamente os nós mínimos para zero e deixe o cluster reduzir para zero nós.

    Para obter mais informações sobre a alteração dos nós mínimos, consulte a [atualização amlcompute](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/update#ext_azure_cli_ml_az_ml_computetarget_update_amlcompute) Azure CLI ou a referência [AmlCompute.update()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#update-min-nodes-none--max-nodes-none--idle-seconds-before-scaledown-none-) SDK.

* __Exemplo de computação Azure Machine Learning__: Crie uma nova instância computacional (que utilizará Ubuntu 18.04) e elimine a antiga instância.

    * Qualquer caderno armazenado na partilha de ficheiros do espaço de trabalho, lojas de dados, de conjuntos de dados estará acessível a partir da nova instância computacional.
    * Se criou ambientes conda personalizados, pode exportar esses ambientes a partir do caso existente e importar no novo caso. Para obter informações sobre a exportação e importação da Conda, consulte [a documentação da Conda](https://docs.conda.io/) na docs.conda.io.

    Para obter mais informações, consulte o [que é a instância compute](concept-compute-instance.md) e Crie e gere [artigos de instância de cálculo Azure Machine Learning](how-to-create-manage-compute-instance.md)

## <a name="check-host-os-version"></a>Verifique a versão DO anfitrião

Para obter informações sobre a versão de host OS, consulte a página wiki da comunidade Ubuntu sobre [a verificação da sua versão Ubuntu](https://help.ubuntu.com/community/CheckingYourUbuntuVersion).

> [!TIP]
> Para utilizar o `lsb_release -a` comando a partir do wiki, pode utilizar uma [sessão terminal numa instância computacional](how-to-access-terminal.md).
## <a name="next-steps"></a>Passos seguintes

Se tiver mais perguntas ou preocupações, contacte-nos em [ubuntu18azureml@service.microsoft.com](mailto:ubuntu18azureml@service.microsoft.com) .
