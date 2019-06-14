---
title: Conjuntos de Máquina Virtual de ciência de dados - Azure | Documentos da Microsoft
description: Implementação de conjuntos de VMs de ciência de dados como um recurso compartilhado numa equipe
keywords: IA, ferramentas de ciência de dados, a máquina de virtual de ciência de dados, a análise geoespacial, o processo de ciência de dados de equipa, aprendizagem aprofundada
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/10/2018
ms.author: gokuma
ms.openlocfilehash: 5cce7f691204a0fd116627fadde1076a4505fcb2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60502287"
---
# <a name="create-a-shared-pool-of-data-science-virtual-machines"></a>Criar um conjunto partilhado de máquinas de virtuais de ciência de dados

Este artigo aborda como criar um conjunto partilhado de dados de máquinas virtuais de ciência (DSVMs) para uma equipe a utilizar. As vantagens de utilizar um conjunto partilhado são melhor utilização de recursos, facilitação de partilha e colaboração e gerenciamento mais eficiente de recursos DSVM. 

Pode usar vários métodos e tecnologias para criar um conjunto de DSVMs. Este artigo se concentra em conjuntos para VMs interativas. Uma infraestrutura de computação gerida alternativo é a computação do Azure Machine Learning. Ver [configurar destinos de computação](../service/how-to-set-up-training-targets.md#amlcompute) para obter mais informações.

## <a name="interactive-vm-pool"></a>Pool de VMS interativa

Um conjunto de VMs interativas que são partilhados pela equipe de ciência de dados/IA toda permite que os utilizadores iniciem sessão a uma instância disponível da DSVM em vez de ter uma instância dedicada para cada conjunto de utilizadores. Esta configuração ajuda a melhor disponibilidade e a utilização mais eficiente de recursos. 

É a tecnologia que utilizar para criar um conjunto VM interativo [conjuntos de dimensionamento de máquina virtual do Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/). Pode utilizar os conjuntos de dimensionamento para criar e gerir um grupo de idênticas, com balanceamento de carga e dimensionamento automático VMs. 

O utilizador inicia sessão no endereço IP ou o DNS do conjunto principal. O conjunto de dimensionamento automaticamente as rotas a sessão para uma DSVM disponível no conjunto de dimensionamento. Uma vez que os usuários desejam um ambiente semelhante, independentemente da VM estão a iniciar sessão, todas as instâncias da VM no conjunto de dimensionamento montagem uma unidade de rede partilhada, como uma partilha de ficheiros do Azure ou uma partilha NFS. Espaço de trabalho compartilhado do usuário é normalmente mantido no arquivo de ficheiros partilhados montado em cada uma das instâncias. 

Pode encontrar um modelo do Azure Resource Manager de exemplo que cria um conjunto de dimensionamento com instâncias de DSVM do Ubuntu no [GitHub](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json). Um exemplo do [ficheiro de parâmetros](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.parameters.json) é de modelo para o Azure Resource Manager na mesma localização. 

É possível criar o conjunto de dimensionamento do modelo do Azure Resource Manager em especificar valores para o ficheiro de parâmetros na CLI do Azure. 

```
az group create --name [[NAME OF RESOURCE GROUP]] --location [[ Data center. For eg: "West US 2"]
az group deployment create --resource-group  [[NAME OF RESOURCE GROUP ABOVE]]  --template-uri https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json --parameters @[[PARAMETER JSON FILE]]
```
Comandos anteriores partem do princípio de que tem:
* Uma cópia do ficheiro de parâmetros com os valores especificados para a sua instância do conjunto de dimensionamento.
* O número de instâncias de VM.
* Partilham a ponteiros para os ficheiros do Azure.
* Credenciais da conta de armazenamento que será montado em cada VM. 

O ficheiro de parâmetros é referenciado localmente nos comandos. Pode também passar parâmetros inline ou linha de comandos para os mesmos no seu script.  

O modelo anterior permite que o SSH e a porta de JupyterHub do conjunto para o conjunto de back-end do Ubuntu DSVMs de dimensionamento de front-end. Como um utilizador, apenas iniciar sessão na VM em SSH ou em JupyterHub da forma normal. Uma vez que as instâncias de VM podem ser aumentadas ou para baixo dinamicamente, qualquer Estado tem de ser guardado o montado em ficheiros do Azure partilham. Pode utilizar a mesma abordagem para criar um conjunto de DSVMs do Windows. 

O [script que monta a partilha de ficheiros do Azure](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Extensions/General/mountazurefiles.sh) também está disponível no repositório DataScienceVM do Azure no GitHub. O script monta o ponto de montagem especificado no ficheiro de parâmetros a partilha de ficheiros do Azure. O script também cria ligações suaves para a unidade montada no diretório raiz do utilizador inicial. Um diretório de bloco de notas específicas do usuário dentro da partilha de ficheiros do Azure é programável ligado para o `$HOME/notebooks/remote` diretório para que os utilizadores podem aceder, executar e guardar os seus blocos de notas do Jupyter. Pode utilizar a mesma Convenção quando criar utilizadores adicionais na VM para apontar a área de trabalho de Jupyter de cada utilizador para a partilha de ficheiros do Azure. 

Os conjuntos de dimensionamento de máquina virtual suportam o dimensionamento automático. Pode definir regras sobre quando criar instâncias adicionais e quando deve reduzir verticalmente instâncias. Por exemplo, pode reduzir verticalmente para zero instâncias para poupar nos custos de utilização de hardware de cloud quando as VMs não são utilizadas de todo. As páginas de documentação dos conjuntos de dimensionamento de máquina virtual fornecem passos detalhados para [dimensionamento automático](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview).

## <a name="next-steps"></a>Passos Seguintes

* [Configure uma identidade comum](dsvm-common-identity.md)
* [Armazenar em segurança as credenciais para aceder a recursos na cloud](dsvm-secure-access-keys.md)















