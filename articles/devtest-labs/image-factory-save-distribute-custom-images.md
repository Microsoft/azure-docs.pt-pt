---
title: Guardar e distribuir imagens em Azure DevTest Labs | Microsoft Docs
description: Este artigo dá-lhe os passos para guardar imagens personalizadas das máquinas virtuais já criadas (VMs) em Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: a5278626f8cdd4299912f3c952786422436fe916
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "85476245"
---
# <a name="save-custom-images-and-distribute-to-multiple-labs"></a>Guardar imagens personalizadas e distribuir para vários laboratórios
Este artigo dá-lhe os passos para guardar imagens personalizadas das máquinas virtuais já criadas (VMs). Também cobre como distribuir estas imagens personalizadas para outros Laboratórios DevTest na organização.

## <a name="prerequisites"></a>Pré-requisitos
Os seguintes itens já devem estar no lugar:

- Um laboratório para a Fábrica de Imagem em Azure DevTest Labs.
- Um Projeto Azure DevOps que é usado para automatizar a fábrica de imagens.
- Localização do código fonte que contém os scripts e configuração (no nosso exemplo, no mesmo Projeto DevOps mencionado no passo anterior).
- Construa definição para orquestrar as tarefas de Azure Powershell.

Se necessário, siga os passos na [Run uma fábrica de imagens da Azure DevOps](image-factory-set-up-devops-lab.md) para criar ou configurar estes itens. 

## <a name="save-vms-as-generalized-vhds"></a>Salvar VMs como VHDs generalizados
Guarde os VMs existentes como VHDs generalizados.  Há uma amostra do guião PowerShell para salvar os VMs existentes como VHDs generalizados. Para usá-lo, em primeiro lugar, adicione outra tarefa **de Azure Powershell** à definição de construção, como mostrado na imagem seguinte:

![Adicione passo Azure PowerShell](./media/save-distribute-custom-images/powershell-step.png)

Assim que tiver a nova tarefa na lista, selecione o item para que possamos preencher todos os detalhes mostrados na seguinte imagem: 

![Definições PowerShell](./media/save-distribute-custom-images/powershell-settings.png)


## <a name="generalized-vs-specialized-custom-images"></a>Generalizada vs. imagens personalizadas especializadas
No [portal Azure,](https://portal.azure.com)ao criar uma imagem personalizada a partir de uma máquina virtual, pode optar por ter uma imagem personalizada generalizada ou especializada.

- **Imagem personalizada especializada:** A Sysprep/Deprovision NÃO foi executada na máquina. Significa que a imagem é uma cópia exata do disco DE na máquina virtual existente (um instantâneo).  Os mesmos ficheiros, aplicações, contas de utilizador, nome de computador, e assim por diante, estão todos presentes quando criamos uma nova máquina a partir desta imagem personalizada.
- **Imagem personalizada generalizada:** Sysprep/Deprovision foi executado na máquina.  Quando este processo é executado, remove as contas do utilizador, remove o nome do computador, retira as colmeias de registo do utilizador, etc., com o objetivo de generalizar a imagem para que possa ser personalizada ao criar outra máquina virtual.  Quando generaliza uma máquina virtual (executando sysprep), o processo destrói a atual máquina virtual – deixará de estar funcional.

O script para captar imagens personalizadas na Fábrica de Imagem salvará VHDs para quaisquer máquinas virtuais criadas no passo anterior (identificados com base numa etiqueta no recurso em Azure).

## <a name="update-configuration-for-distributing-images"></a>Configuração de atualização para distribuir imagens
O próximo passo no processo é empurrar as imagens personalizadas do laboratório de fábrica de imagens para outros laboratórios que precisem delas. A parte central deste processo é a **labs.jsno** ficheiro de configuração. Pode encontrar este ficheiro na pasta **configuração** incluída na fábrica de imagens.

Existem duas coisas-chave listadas no labs.jsno ficheiro de configuração:

- Identificando exclusivamente um laboratório de destino específico usando o ID de assinatura e o nome do laboratório.
- O conjunto específico de imagens que devem ser empurradas para o laboratório como caminhos relativos para a raiz de configuração. Também é possível especificar pasta inteira (para obter todas as imagens nessa pasta).

Aqui está um exemplo labs.jsficheiro com dois laboratórios listados. Neste caso, está a distribuir imagens em dois laboratórios diferentes.

```json
{
   "Labs": [
      {
         "SubscriptionId": "<subscription ID that contains the lab>",
         "LabName": "<Name of the DevTest Lab>",
         "ImagePaths": [
               "Win2012R2",
               "Win2016/Datacenter.json"
         ]
      },
      {
         "SubscriptionId": "<subscription ID that contains the lab>",
         "LabName": "<Name of the DevTest Lab>",
         "ImagePaths": [
               "Win2016/Datacenter.json"
         ]
      }
   ]
}
```

## <a name="create-a-build-task"></a>Criar uma tarefa de compilação
Utilizando os mesmos passos que viu anteriormente neste artigo, adicione uma tarefa adicional de construção **de Azure Powershell** para construir definição. Preencha os detalhes como mostrado na seguinte imagem: 

![Construir tarefa para distribuir imagens](./media/save-distribute-custom-images/second-build-task-powershell.png)

Os parâmetros são: `-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -SubscriptionId $(SubscriptionId) -DevTestLabName $(DevTestLabName) -maxConcurrentJobs 20`

Esta tarefa retira quaisquer imagens personalizadas presentes na fábrica de imagens e empurra-as para quaisquer laboratórios definidos no Labs.jsem arquivo.

## <a name="queue-the-build"></a>Fila da construção
Uma vez concluída a tarefa de construção de distribuição, faça fila de uma nova construção para garantir que tudo está funcionando. Após a construção concluída com sucesso, as novas imagens personalizadas aparecerão no laboratório de destino que foi introduzido no Labs.jsno ficheiro de configuração.

## <a name="next-steps"></a>Passos seguintes
No artigo seguinte da série, atualiza a fábrica de imagens com uma política de retenção e etapas de limpeza: [Desemote a política de retenção e execute scripts de limpeza](image-factory-set-retention-policy-cleanup.md).
