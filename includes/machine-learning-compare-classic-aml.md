---
author: peterclu
ms.service: machine-learning
ms.topic: include
ms.date: 03/08/2021
ms.author: peterlu
ms.openlocfilehash: ff64a0948402ff152e45bd4702d986f51b9547aa
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103563206"
---
A tabela seguinte resume as principais diferenças entre ml Studio (clássico) e Azure Machine Learning.

| Funcionalidade | Estúdio ML (clássico) | Azure Machine Learning |
|---| --- | --- |
| Interface arrastar e largar | Experiência clássica | Experiência atualizada - [Azure Machine Learning designer](../articles/machine-learning/concept-designer.md)| 
| SDKs de código | Não suportado | Totalmente integrado com [Azure Machine Learning Python](/python/api/overview/azure/ml/) e [R](https://github.com/Azure/azureml-sdk-for-r) SDKs |
| Experimentação | Escalável (limite de dados de formação de 10 GB) | Escala com alvo de computação |
| Metas de computação de formação | Alvo de computação proprietária, suporte de CPU apenas | Ampla gama de objetivos de [computação de formação](../articles/machine-learning/concept-compute-target.md#train)personalizáveis. Inclui suporte de GPU e CPU | 
| Alvos de computação de implantação | Formato de serviço web proprietário, não personalizável | Ampla gama de alvos de [computação](../articles/machine-learning/concept-compute-target.md#deploy)personalizável. Inclui suporte de GPU e CPU |
| Gasoduto ML | Não suportado | Construir gasodutos flexíveis e [modulares](../articles/machine-learning/concept-ml-pipelines.md) para automatizar fluxos de trabalho |
| MLOps | Gestão e implantação de modelos básicos; Apenas implantações do CPU | Versões de entidades (modelo, dados, fluxos de trabalho), automatização de fluxos de trabalho, integração com ferramentas CICD, implementações de CPU e GPU [e muito mais](../articles/machine-learning/concept-model-management-and-deployment.md) |
| Formato de modelo | Formato proprietário, Estúdio (clássico) apenas | Múltiplos formatos suportados dependendo do tipo de trabalho de formação |
| Treinamento de modelo automatizado e afinação de hiperparímetro |  Não suportado | [Suportado.](../articles/machine-learning/concept-automated-ml.md) Código primeiro e sem código. | 
| Deteção de deriva de dados | Não suportado | [Suportado](../articles/machine-learning/how-to-monitor-datasets.md) |
| Projetos de rotulagem de dados | Não suportado | [Suportado](../articles/machine-learning/how-to-create-labeling-projects.md) |
| Controlo de Acesso Baseado em Funções (RBAC) | Apenas papel de contribuinte e proprietário | [Definição flexível de função e controlo do RBAC](../articles/machine-learning/how-to-assign-roles.md) |
| Galeria AI | Suportado ( [https://gallery.azure.ai/](https://gallery.azure.ai/) ) | Não suportado <br><br> Aprenda com [a amostra de cadernos Python SDK.](https://github.com/Azure/MachineLearningNotebooks) |