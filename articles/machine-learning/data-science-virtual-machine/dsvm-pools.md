---
title: Pools compartilhados
titleSuffix: Azure Data Science Virtual Machine
description: Saiba como criar & implantar um pool compartilhado de máquinas virtuais de ciência de dados (DSVMs) como um recurso compartilhado para uma equipe.
keywords: aprendizado profundo, ia, ferramentas de ciência de dados, máquina virtual de ciência de dados, análise geoespacial, processo de ciência de dados de equipe
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 12/10/2018
ms.openlocfilehash: 2a74df3aaf70270353b10f757cf3a61e8f479d74
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73053000"
---
# <a name="create-a-shared-pool-of-data-science-virtual-machines"></a>Criar um pool compartilhado de máquinas virtuais de ciência de dados

Neste artigo, você aprenderá a criar um pool compartilhado de máquinas virtuais de ciência de dados (DSVMs) para uma equipe. Os benefícios de usar um pool compartilhado incluem melhor utilização de recursos, colaboração e compartilhamento mais fáceis e gerenciamento mais eficaz de recursos de DSVM.

Você pode usar muitos métodos e tecnologias para criar um pool de DSVMs. Este artigo se concentra em pools de VMs (máquinas virtuais) interativas. Uma infraestrutura de computação gerenciada alternativa é Azure Machine Learning computação. Para obter mais informações, consulte [Configurar destinos de computação](../service/how-to-set-up-training-targets.md#amlcompute).

## <a name="interactive-vm-pool"></a>Pool de VMs interativa

Um pool de VMs interativas que são compartilhadas por toda a equipe de ia/ciência de dados permite que os usuários façam logon em uma instância disponível do DSVM em vez de ter uma instância dedicada para cada conjunto de usuários. Essa configuração permite uma melhor disponibilidade e uma utilização mais eficaz dos recursos.

Você usa a tecnologia de [conjuntos de dimensionamento de máquinas virtuais do Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/) para criar um pool de VMs interativo. Você pode usar conjuntos de dimensionamento para criar e gerenciar um grupo de VMs idênticas de carga balanceada e de dimensionamento automático.

O usuário faz logon no endereço IP ou DNS do pool principal. O conjunto de dimensionamento roteia automaticamente a sessão para um DSVM disponível no conjunto de dimensionamento. Como os usuários desejam um ambiente consistente e familiar, independentemente da VM em que estão fazendo logon, todas as instâncias da VM no conjunto de dimensionamento montam uma unidade de rede compartilhada, como um compartilhamento de arquivos do Azure ou um compartilhamento NFS (sistema de arquivos de rede). O espaço de trabalho compartilhado do usuário normalmente é mantido no armazenamento de arquivos compartilhado que é montado em cada uma das instâncias.

Você pode encontrar um modelo de Azure Resource Manager de exemplo que cria um conjunto de dimensionamento com instâncias do Ubuntu DSVM no [GitHub](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json). Você encontrará um exemplo do [arquivo de parâmetro](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.parameters.json) para o modelo de Azure Resource Manager no mesmo local.

Você pode criar o conjunto de dimensionamento do modelo de Azure Resource Manager especificando valores para o arquivo de parâmetro no CLI do Azure:

```
az group create --name [[NAME OF RESOURCE GROUP]] --location [[ Data center. For eg: "West US 2"]
az group deployment create --resource-group  [[NAME OF RESOURCE GROUP ABOVE]]  --template-uri https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json --parameters @[[PARAMETER JSON FILE]]
```
Os comandos anteriores pressupõem que você tenha:
* Uma cópia do arquivo de parâmetro com os valores especificados para sua instância do conjunto de dimensionamento.
* O número de instâncias de VM.
* Ponteiros para o compartilhamento de arquivos do Azure.
* Credenciais para a conta de armazenamento que será montada em cada VM.

O arquivo de parâmetro é referenciado localmente nos comandos. Você também pode passar parâmetros embutidos ou solicitá-los em seu script.  

O modelo anterior habilita o SSH e a porta JupyterHub do conjunto de dimensionamento de front-end para o pool de back-end do Ubuntu DSVMs. Como usuário, você faz logon na VM em um Secure Shell (SSH) ou no JupyterHub da maneira normal. Como as instâncias de VM podem ser aumentadas ou reduzidas dinamicamente, qualquer Estado deve ser salvo no compartilhamento de arquivos do Azure montado. Você pode usar a mesma abordagem para criar um pool de DSVMs do Windows.

O [script que monta o compartilhamento de arquivos do Azure](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Extensions/General/mountazurefiles.sh) também está disponível no repositório DataScienceVM do Azure no github. O script monta o compartilhamento de arquivos do Azure no ponto de montagem especificado no arquivo de parâmetro. O script também cria links suaves para a unidade montada no diretório base do usuário inicial. Um diretório de notebook específico do usuário no compartilhamento de arquivos do Azure é vinculado de forma flexível ao diretório `$HOME/notebooks/remote` para que os usuários possam acessar, executar e salvar seus notebooks Jupyter. Você pode usar a mesma convenção ao criar usuários adicionais na VM para apontar o espaço de trabalho Jupyter de cada usuário para o compartilhamento de arquivos do Azure.

Os conjuntos de dimensionamento de máquinas virtuais dão suporte ao dimensionamento automático. Você pode definir regras sobre quando criar instâncias adicionais e quando reduzir instâncias. Por exemplo, você pode reduzir verticalmente até zero instâncias para economizar em custos de uso de hardware na nuvem quando as VMs não são usadas. As páginas de documentação dos conjuntos de dimensionamento de máquinas virtuais fornecem etapas detalhadas para o [dimensionamento](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview)automático.

## <a name="next-steps"></a>Passos seguintes

* [Configurar uma identidade comum](dsvm-common-identity.md)
* [Armazenar credenciais com segurança para acessar recursos de nuvem](dsvm-secure-access-keys.md)















