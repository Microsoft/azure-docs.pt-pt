---
title: Piscinas partilhadas
titleSuffix: Azure Data Science Virtual Machine
description: Aprenda a criar & implementar um conjunto partilhado de Máquinas Virtuais de Ciência de Dados (DSVMs) como um recurso partilhado para uma equipa.
keywords: aprendizagem profunda, IA, ferramentas de ciência de dados, máquina virtual de ciência de dados, análise geoespacial, processo de ciência de dados de equipa
services: machine-learning
ms.service: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 12/10/2018
ms.openlocfilehash: a118d5a3e716a80bda21ffe82a8cefd1da1202f3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100519716"
---
# <a name="create-a-shared-pool-of-data-science-virtual-machines"></a>Criar um conjunto partilhado de máquinas virtuais de ciência de dados

Neste artigo, você vai aprender a criar um conjunto compartilhado de Data Science Virtual Machines (DSVMs) para uma equipe. Os benefícios da utilização de um pool partilhado incluem uma melhor utilização de recursos, uma partilha e colaboração mais fáceis e uma gestão mais eficaz dos recursos DSVM.

Você pode usar muitos métodos e tecnologias para criar um conjunto de DSVMs. Este artigo centra-se em piscinas para máquinas virtuais interativas (VMs). Uma infraestrutura de computação gerida alternativa é a Azure Machine Learning Compute. Para obter mais informações, consulte [Criar cluster de cálculo.](../how-to-create-attach-compute-cluster.md)

## <a name="interactive-vm-pool"></a>Piscina VM interativa

Um conjunto de VMs interativos que são partilhados por toda a equipa de IA/data science permite que os utilizadores iniciem sessão numa instância disponível do DSVM em vez de ter um exemplo dedicado para cada conjunto de utilizadores. Esta configuração permite uma melhor disponibilidade e uma utilização mais eficaz dos recursos.

Você usa a tecnologia [de conjuntos de escala de máquina virtual Azure](../../virtual-machine-scale-sets/index.yml) para criar uma piscina VM interativa. Pode utilizar conjuntos de escala para criar e gerir um grupo de VMs idênticos, equilibrados em carga e autoscalantes.

O utilizador inicia sessão no endereço IP ou DNS da piscina principal. O conjunto de escalas liga automaticamente a sessão a um DSVM disponível no conjunto de escala. Como os utilizadores querem um ambiente consistente e familiar, independentemente do VM em que estão a iniciar sessão, todas as instâncias do VM na escala montam uma unidade de rede partilhada, como uma partilha de Ficheiros Azure ou uma partilha do Sistema de Ficheiros de Rede (NFS). O espaço de trabalho partilhado do utilizador é normalmente mantido na loja de ficheiros partilhada que é montada em cada uma das instâncias.

Pode encontrar um modelo de gestor de recursos Azure que cria uma escala definida com instâncias Ubuntu DSVM no [GitHub](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json). Encontrará uma amostra do ficheiro de [parâmetros](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.parameters.json) para o modelo do Gestor de Recursos Azure no mesmo local.

Pode criar o conjunto de escala a partir do modelo Azure Resource Manager especificando valores para o ficheiro de parâmetros no CLI Azure:

```azurecli-interactive
az group create --name [[NAME OF RESOURCE GROUP]] --location [[ Data center. For eg: "West US 2"]
az deployment group create --resource-group  [[NAME OF RESOURCE GROUP ABOVE]]  --template-uri https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json --parameters @[[PARAMETER JSON FILE]]
```

Os comandos anteriores assumem que tem:

* Uma cópia do ficheiro de parâmetros com os valores especificados para o seu exemplo do conjunto de escala.
* O número de casos de VM.
* Os ponteiros para a partilha dos Ficheiros Azure.
* Credenciais para a conta de armazenamento que será montada em cada VM.

O ficheiro de parâmetros é referenciado localmente nos comandos. Também pode passar parâmetros inline ou solicitação para eles no seu script.  

O modelo anterior permite o SSH e a porta JupyterHub desde a escala frontal definida para a piscina traseira dos Ubuntu DSVMs. Como utilizador, faça login no VM numa Secure Shell (SSH) ou no JupyterHub da forma normal. Como as instâncias VM podem ser dimensionadas para cima ou para baixo dinamicamente, qualquer estado deve ser guardado na partilha de Ficheiros Azure montados. Pode utilizar a mesma abordagem para criar um conjunto de DSVMs do Windows.

O [script que monta a partilha de Ficheiros Azure](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Extensions/General/mountazurefiles.sh) também está disponível no repositório Azure DataScienceVM no GitHub. O script monta a partilha de Ficheiros Azure no ponto de montagem especificado no ficheiro de parâmetros. O script também cria ligações suaves à unidade montada no diretório inicial do utilizador. Um diretório de portátil específico do utilizador na partilha de Ficheiros Azure está ligado ao diretório para `$HOME/notebooks/remote` que os utilizadores possam aceder, executar e guardar os seus cadernos Jupyter. Pode utilizar a mesma convenção quando criar utilizadores adicionais no VM para apontar o espaço de trabalho jupyter de cada utilizador para a partilha dos Ficheiros Azure.

A balança de máquina virtual define o suporte a autoscaling. Pode definir regras sobre quando criar instâncias adicionais e quando reduzir as instâncias. Por exemplo, pode reduzir para zero casos para economizar nos custos de utilização de hardware na nuvem quando os VMs não são usados de todo. As páginas de documentação dos conjuntos de escala de máquinas virtuais fornecem [passos detalhados para a autoscalagem.](../../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview.md)

## <a name="next-steps"></a>Passos seguintes

* [Criar uma identidade comum](dsvm-common-identity.md)
* [Armazenar credenciais de segurança para aceder a recursos na nuvem](dsvm-secure-access-keys.md)