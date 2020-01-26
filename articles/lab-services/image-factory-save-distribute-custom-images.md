---
title: Guardar e distribuir imagens em Azure DevTest Labs [ Laboratórios DevTest] Microsoft Docs
description: Este artigo dá-lhe os passos para guardar imagens personalizadas das máquinas virtuais já criadas (VMs) em Azure DevTest Labs.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: e5bc8e5041bfe6d95e3ff1a93bb3338ccead5bb4
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759436"
---
# <a name="save-custom-images-and-distribute-to-multiple-labs"></a>Guardar imagens personalizadas e distribuir para vários laboratórios
Este artigo dá-lhe os passos para guardar imagens personalizadas das máquinas virtuais já criadas (VMs). Também abrange como distribuir estas imagens personalizadas a outros Laboratórios DevTest na organização.

## <a name="prerequisites"></a>Pré-requisitos
Os itens a seguir já devem estar em vigor:

- Um laboratório para a Fábrica de Imagem em Azure DevTest Labs.
- Um Projeto Azure DevOps que é usado para automatizar a fábrica de imagem.
- Localização do código fonte contendo os scripts e configuração (no nosso exemplo, no mesmo Projeto DevOps mencionado na etapa anterior).
- Construa definição para orquestrar as tarefas Azure Powershell.

Se necessário, siga os passos na [Run uma fábrica de imagem da Azure DevOps](image-factory-set-up-devops-lab.md) para criar ou configurar estes itens. 

## <a name="save-vms-as-generalized-vhds"></a>Salvar VMs como VHDs generalizados
Guarde os VMs existentes como VHDs generalizados.  Há um script powerShell de amostra para salvar os VMs existentes como VHDs generalizados. Para usá-lo, em primeiro lugar, adicione outra tarefa **Azure Powershell** à definição de construção, como mostrado na seguinte imagem:

![Adicione passo Azure PowerShell](./media/save-distribute-custom-images/powershell-step.png)

Assim que tiver a nova tarefa na lista, selecione o item para que possamos preencher todos os detalhes, conforme mostrado na seguinte imagem: 

![Definições PowerShell](./media/save-distribute-custom-images/powershell-settings.png)


## <a name="generalized-vs-specialized-custom-images"></a>Imagens personalizadas generalizadas vs. especializadas
No [portal Azure,](https://portal.azure.com)ao criar uma imagem personalizada a partir de uma máquina virtual, pode optar por ter uma imagem personalizada ou especializada.

- **Imagem personalizada especializada:** Sysprep/Deprovision não foi executado na máquina. Significa que a imagem é uma cópia exata do Disco OS na máquina virtual existente (um instantâneo).  Os mesmos ficheiros, aplicações, contas de utilizador, nome do computador, e assim por diante, estão todos presentes quando criamos uma nova máquina a partir desta imagem personalizada.
- **Imagem personalizada generalizada:** A sysprep/Deprovision foi executada na máquina.  Quando este processo decorre, remove as contas do utilizador, remove o nome do computador, tira as colmeias do registo do utilizador, etc., com o objetivo de generalizar a imagem para que possa ser personalizada na criação de outra máquina virtual.  Ao generalizar uma máquina virtual (através da sysprep), o processo destrói a máquina virtual atual – deixará de estar funcional.

O script para tirar imagens personalizadas na Image Factory irá guardar VHDs para quaisquer máquinas virtuais criadas no passo anterior (identificada seletiva com base numa etiqueta no recurso em Azure).

## <a name="update-configuration-for-distributing-images"></a>Configuração de atualização para distribuição de imagens
O próximo passo no processo é empurrar as imagens personalizadas do laboratório da fábrica de imagem para qualquer outro laboratório que precise delas. A parte central deste processo é o ficheiro de configuração **labs.json.** Pode encontrar este ficheiro na pasta **De Configuração** incluída na fábrica de imagem.

Há duas coisas-chave listadas no ficheiro de configuração labs.json:

- Identificando de forma única um laboratório de destino específico usando o ID de subscrição e o nome do laboratório.
- O conjunto específico de imagens que devem ser empurradas para o laboratório como caminhos relativos para a raiz de configuração. Também pode especificar toda a pasta (para obter todas as imagens dessa pasta).

Aqui está um exemplo labs.json arquivo com dois laboratórios listados. Neste caso, está a distribuir imagens para dois laboratórios diferentes.

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
Utilizando os mesmos passos que viu anteriormente neste artigo, adicione uma tarefa adicional de construção **de Azure Powershell** a você construir definição. Preencha os detalhes conforme mostrado na seguinte imagem: 

![Construir tarefa para distribuir imagens](./media/save-distribute-custom-images/second-build-task-powershell.png)

Os parâmetros são: `-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -SubscriptionId $(SubscriptionId) -DevTestLabName $(DevTestLabName) -maxConcurrentJobs 20`

Esta tarefa requer quaisquer imagens personalizadas presentes na fábrica de imagem e empurra-as para qualquer laboratório definido no ficheiro Labs.json.

## <a name="queue-the-build"></a>Enfileirar a compilação
Uma vez concluída a tarefa de construção de distribuição, faça fila para uma nova construção para garantir que tudo está funcionando. Após a construção concluída com sucesso, as novas imagens personalizadas aparecerão no laboratório de destino que foi introduzido no ficheiro de configuração Labs.json.

## <a name="next-steps"></a>Passos seguintes
No próximo artigo da série, atualiza a fábrica de imagem com uma política de retenção e etapas de limpeza: Definir a política de [retenção e executar scripts](image-factory-set-retention-policy-cleanup.md)de limpeza .
