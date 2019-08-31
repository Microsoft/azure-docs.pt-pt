---
title: Pools compartilhados
titleSuffix: Azure Data Science Virtual Machine
description: Implantando pools de DSVMs como um recurso compartilhado para uma equipe
keywords: IA, ferramentas de ciência de dados, a máquina de virtual de ciência de dados, a análise geoespacial, o processo de ciência de dados de equipa, aprendizagem aprofundada
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 12/10/2018
ms.openlocfilehash: 929040b5dc8650a757fb9c0da58cb82a76a72b5b
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195625"
---
# <a name="create-a-shared-pool-of-data-science-virtual-machines"></a>Criar um conjunto partilhado de máquinas de virtuais de ciência de dados

Neste artigo, você aprenderá a criar um pool compartilhado de máquinas virtuais de ciência de dados (DSVMs) para uma equipe. Os benefícios de usar um pool compartilhado incluem melhor utilização de recursos, colaboração e compartilhamento mais fáceis e gerenciamento mais eficaz de recursos de DSVM.

Pode usar vários métodos e tecnologias para criar um conjunto de DSVMs. Este artigo se concentra em pools de VMs (máquinas virtuais) interativas. Uma infraestrutura de computação gerenciada alternativa é Azure Machine Learning computação. Para obter mais informações, consulte [Configurar destinos de computação](../service/how-to-set-up-training-targets.md#amlcompute).

## <a name="interactive-vm-pool"></a>Pool de VMS interativa

Um conjunto de VMs interativas que são partilhados pela equipe de ciência de dados/IA toda permite que os utilizadores iniciem sessão a uma instância disponível da DSVM em vez de ter uma instância dedicada para cada conjunto de utilizadores. Essa configuração permite uma melhor disponibilidade e uma utilização mais eficaz dos recursos.

Você usa a tecnologia de conjuntos de dimensionamento de [máquinas virtuais do Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/) para criar um pool de VMs interativo. Pode utilizar os conjuntos de dimensionamento para criar e gerir um grupo de idênticas, com balanceamento de carga e dimensionamento automático VMs.

O utilizador inicia sessão no endereço IP ou o DNS do conjunto principal. O conjunto de dimensionamento automaticamente as rotas a sessão para uma DSVM disponível no conjunto de dimensionamento. Como os usuários desejam um ambiente consistente e familiar, independentemente da VM em que estão fazendo logon, todas as instâncias da VM no conjunto de dimensionamento montam uma unidade de rede compartilhada, como um compartilhamento de arquivos do Azure ou um compartilhamento NFS (sistema de arquivos de rede). Espaço de trabalho compartilhado do usuário é normalmente mantido no arquivo de ficheiros partilhados montado em cada uma das instâncias.

Pode encontrar um modelo do Azure Resource Manager de exemplo que cria um conjunto de dimensionamento com instâncias de DSVM do Ubuntu no [GitHub](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json). Você encontrará um exemplo do [arquivo de parâmetro](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.parameters.json) para o modelo de Azure Resource Manager no mesmo local.

Você pode criar o conjunto de dimensionamento do modelo de Azure Resource Manager especificando valores para o arquivo de parâmetro no CLI do Azure:

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

O modelo anterior permite que o SSH e a porta de JupyterHub do conjunto para o conjunto de back-end do Ubuntu DSVMs de dimensionamento de front-end. Como usuário, você faz logon na VM em um Secure Shell (SSH) ou no JupyterHub da maneira normal. Como as instâncias de VM podem ser aumentadas ou reduzidas dinamicamente, qualquer Estado deve ser salvo no compartilhamento de arquivos do Azure montado. Pode utilizar a mesma abordagem para criar um conjunto de DSVMs do Windows.

O [script que monta a partilha de ficheiros do Azure](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Extensions/General/mountazurefiles.sh) também está disponível no repositório DataScienceVM do Azure no GitHub. O script monta o ponto de montagem especificado no ficheiro de parâmetros a partilha de ficheiros do Azure. O script também cria ligações suaves para a unidade montada no diretório raiz do utilizador inicial. Um diretório de bloco de anotações específico do usuário no compartilhamento de arquivos do Azure é vinculado `$HOME/notebooks/remote` de forma flexível ao diretório para que os usuários possam acessar, executar e salvar seus notebooks Jupyter. Pode utilizar a mesma Convenção quando criar utilizadores adicionais na VM para apontar a área de trabalho de Jupyter de cada utilizador para a partilha de ficheiros do Azure.

Os conjuntos de dimensionamento de máquina virtual suportam o dimensionamento automático. Você pode definir regras sobre quando criar instâncias adicionais e quando reduzir instâncias. Por exemplo, pode reduzir verticalmente para zero instâncias para poupar nos custos de utilização de hardware de cloud quando as VMs não são utilizadas de todo. As páginas de documentação dos conjuntos de dimensionamento de máquina virtual fornecem passos detalhados para [dimensionamento automático](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview).

## <a name="next-steps"></a>Passos Seguintes

* [Configure uma identidade comum](dsvm-common-identity.md)
* [Armazenar em segurança as credenciais para aceder a recursos na cloud](dsvm-secure-access-keys.md)















