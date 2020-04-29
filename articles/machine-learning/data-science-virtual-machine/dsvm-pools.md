---
title: Piscinas partilhadas
titleSuffix: Azure Data Science Virtual Machine
description: Aprenda a criar & implementar um conjunto partilhado de Máquinas Virtuais de Ciência de Dados (DSVMs) como um recurso partilhado para uma equipa.
keywords: aprendizagem profunda, IA, ferramentas de ciência de dados, máquina virtual de ciência de dados, análise geoespacial, processo de ciência de dados de equipa
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 12/10/2018
ms.openlocfilehash: cc0efc0a076ddc3fc9425999f1e38b4a32dec7a3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79477345"
---
# <a name="create-a-shared-pool-of-data-science-virtual-machines"></a>Criar um conjunto partilhado de Máquinas Virtuais de Ciência de Dados

Neste artigo, você vai aprender a criar um conjunto partilhado de Máquinas Virtuais de Ciência de Dados (DSVMs) para uma equipa. Os benefícios da utilização de um pool partilhado incluem uma melhor utilização de recursos, uma melhor partilha e colaboração e uma gestão mais eficaz dos recursos da DSVM.

Você pode usar muitos métodos e tecnologias para criar um conjunto de DSVMs. Este artigo centra-se em piscinas para máquinas virtuais interativas (VMs). Uma infraestrutura de computação gerida alternativa é a Azure Machine Learning Compute. Para obter mais informações, consulte [Configurar os alvos do cálculo](../how-to-set-up-training-targets.md#amlcompute).

## <a name="interactive-vm-pool"></a>Piscina VM interativa

Um conjunto de VMs interativos que são partilhados por toda a equipa de Ciência da IA/dados permite que os utilizadores iniciem sessão numa instância disponível do DSVM em vez de terem uma instância dedicada para cada conjunto de utilizadores. Esta configuração permite uma melhor disponibilidade e uma utilização mais eficaz dos recursos.

Você usa a tecnologia de conjuntos de conjuntos de [máquinas virtuais Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/) para criar uma piscina vm interativa. Pode utilizar conjuntos de escala para criar e gerir um grupo de VMs idênticos, equilibrados e autoscalcificadores.

O utilizador faz login no endereço IP ou DNS da piscina principal. O conjunto de escala supõe automaticamente a sessão para um DSVM disponível no conjunto de escala. Como os utilizadores querem um ambiente consistente e familiar, independentemente do VM a que estão a fazer login, todos os casos do VM na escala montam uma unidade de rede partilhada, como uma partilha de Ficheiros Azure ou uma partilha do Sistema de Ficheiros de Rede (NFS). O espaço de trabalho partilhado do utilizador é normalmente mantido na loja de ficheiros partilhada que é montada em cada uma das instâncias.

Você pode encontrar um modelo de Gestor de Recursos Azure que cria um conjunto de escala com instâncias Ubuntu DSVM no [GitHub](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json). Você encontrará uma amostra do ficheiro de [parâmetro](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.parameters.json) para o modelo do Gestor de Recursos Azure no mesmo local.

Pode criar a escala definida a partir do modelo do Gestor de Recursos Azure, especificando valores para o ficheiro parâmetro no ClI Azure:

```azurecli-interactive
az group create --name [[NAME OF RESOURCE GROUP]] --location [[ Data center. For eg: "West US 2"]
az group deployment create --resource-group  [[NAME OF RESOURCE GROUP ABOVE]]  --template-uri https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json --parameters @[[PARAMETER JSON FILE]]
```

Os comandos anteriores assumem que tem:

* Uma cópia do ficheiro parâmetro com os valores especificados para a sua parte do conjunto de escala.
* O número de casos vm.
* Os ponteiros para os Ficheiros Azure partilham.
* Credenciais para a conta de armazenamento que será montada em cada VM.

O ficheiro do parâmetro é referenciado localmente nos comandos. Também pode passar parâmetros inline ou prompt para eles no seu script.  

O modelo anterior permite o SSH e a porta JupyterHub desde a balança frontal definida até à piscina traseira dos DSVMs ubuntu. Como utilizador, inicia sessão no VM numa Concha Segura (SSH) ou no JupyterHub da forma normal. Como as instâncias vm podem ser dimensionadas para cima ou para baixo dinamicamente, qualquer estado deve ser guardado na partilha de Ficheiros Azure montados. Pode utilizar a mesma abordagem para criar um conjunto de DSVMs windows.

O [script que monta a partilha de Ficheiros Azure](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Extensions/General/mountazurefiles.sh) também está disponível no repositório Azure DataScienceVM em GitHub. O script monta a partilha dos Ficheiros Azure no ponto de montagem especificado no ficheiro parâmetro. O script também cria ligações suaves à unidade montada no diretório inicial do utilizador. Um diretório de caderno específico do utilizador na partilha `$HOME/notebooks/remote` de Ficheiros Azure está ligado ao diretório para que os utilizadores possam aceder, executar e guardar os seus cadernos Jupyter. Pode utilizar a mesma convenção quando criar utilizadores adicionais no VM para indicar o espaço de trabalho jupyter de cada utilizador para a partilha de Ficheiros Azure.

Os conjuntos de escala de máquinas virtuais suportam a autoscalcificação. Pode definir regras sobre quando criar instâncias adicionais e quando reduzir as instâncias. Por exemplo, pode reduzir para zero instâncias para economizar nos custos de utilização do hardware em nuvem quando os VMs não são usados de todo. As páginas de documentação dos conjuntos de escala de máquinas virtuais fornecem [passos detalhados para autodimensionamento.](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview)

## <a name="next-steps"></a>Passos seguintes

* [Criar uma identidade comum](dsvm-common-identity.md)
* [Armazenar seguramente credenciais para aceder a recursos em nuvem](dsvm-secure-access-keys.md)
