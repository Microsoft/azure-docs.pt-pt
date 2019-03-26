---
title: Guardar e distribuir imagens no Azure DevTest Labs | Documentos da Microsoft
description: Saiba como criar uma fábrica de imagem personalizada no Azure DevTest Labs.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: spelluru
ms.openlocfilehash: feabd055833e5f0d850138af528cce1da82cae49
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58440102"
---
# <a name="save-custom-images-and-distribute-to-multiple-labs"></a>Guardar imagens personalizadas e distribuir a vários laboratórios
Este artigo aborda dá-lhe os passos para guardar imagens personalizadas do já criadas máquinas virtuais (VMs). Também aborda como distribuir estas imagens personalizadas para outros laboratórios Dev/Test na organização.

## <a name="prerequisites"></a>Pré-requisitos
Os itens seguintes já deverá estar no local:

- Um laboratório para a fábrica de imagem no Azure DevTest Labs.
- Um projeto de DevOps do Azure, que é utilizada para automatizar a fábrica de imagem.
- Localização do código de origem que contém os scripts e a configuração (no nosso exemplo, no mesmo projeto de DevOps mencionado no passo anterior).
- Crie definição para orquestrar as tarefas do Azure Powershell.

Se for necessário, siga os passos no [execute uma fábrica de imagem a partir do Azure DevOps](image-factory-set-up-devops-lab.md) para criar ou configurar esses itens. 

## <a name="save-vms-as-generalized-vhds"></a>Guardar VMs como VHD generalizado
Guarde as VMs existentes como VHDs generalizadas.  Existe um script do PowerShell de exemplo para guardar as VMs existentes como VHDs generalizadas. Para usá-lo, primeiro, adicione outro **do Azure Powershell** tarefas para a definição de compilação, conforme mostrado na imagem seguinte:

![Adicionar passo de Azure PowerShell](./media/save-distribute-custom-images/powershell-step.png)

Assim que tiver a nova tarefa na lista, selecione o item, de modo que pode preencher todos os detalhes conforme mostrado na imagem seguinte: 

![Definições do PowerShell](./media/save-distribute-custom-images/powershell-settings.png)


## <a name="generalized-vs-specialized-custom-images"></a>Generalizado versus especializadas imagens personalizadas
Na [portal do Azure](https://portal.azure.com), ao criar uma imagem personalizada a partir de uma máquina virtual, pode optar por ter um generalizado ou uma imagem personalizada especializada.

- **Especializadas imagem personalizada:** Sysprep/desaprovisionamento não foi executado na máquina. Isso significa que a imagem é uma cópia exata do disco do SO na máquina virtual existente (um instantâneo).  O mesmo ficheiros, aplicações, as contas de utilizador, nome do computador e assim por diante, são todos presentes quando criamos uma nova máquina a partir desta imagem personalizada.
- **Generalizado a imagem personalizada:** Sysprep/desaprovisionamento foi executado na máquina.  Quando esse processo é executado, ele remove as contas de utilizador, remove o nome do computador, separe os hives do Registro de usuário, etc., com o objetivo de generalizar a imagem para que ele pode ser personalizado durante a criação de outra máquina virtual.  Ao generalizar uma máquina virtual (ao executar o sysprep), o processo destrói a máquina virtual atual – já não estará funcional.

O script para ajuste imagens personalizadas na fábrica de imagem será guardada VHDs para as máquinas virtuais criadas no passo anterior (identificado com base numa marca do recurso no Azure).

## <a name="update-configuration-for-distributing-images"></a>Atualizar a configuração para a distribuição de imagens
A próxima etapa do processo é enviar imagens personalizadas do laboratório de fábrica imagem horizontalmente para outros laboratórios que precisam deles. A parte principal desse processo é o **labs.json** ficheiro de configuração. Pode encontrar este ficheiro na **configuração** pasta incluída na fábrica de imagem.

Existem duas coisas principais listadas no arquivo de configuração de labs.json:

- Identificar de forma exclusiva um laboratório de destino específico usando o ID de subscrição e o nome de laboratório.
- O conjunto específico de imagens que devem ser enviadas para o laboratório como caminhos relativos para a raiz de configuração. Pode especificar uma pasta inteira (para obter todas as imagens nessa pasta) também.

Eis um exemplo de arquivo labs.json com dois laboratórios listados. Neste caso, está a distribuir imagens para dois laboratórios diferentes.

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
Com os mesmos passos que viu anteriormente no artigo, adicione um adicionais **do Azure Powershell** definição de compilação de tarefa de compilação para. Preencha os detalhes, conforme mostrado na imagem seguinte: 

![Criar tarefa de distribuir imagens](./media/save-distribute-custom-images/second-build-task-powershell.png)

Os parâmetros são: `-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -SubscriptionId $(SubscriptionId) -DevTestLabName $(DevTestLabName) -maxConcurrentJobs 20`

Esta tarefa demora quaisquer imagens personalizadas presentes na fábrica de imagem e envia-os a qualquer laboratórios definidos no arquivo Labs.json.

## <a name="queue-the-build"></a>A compilação de fila
Depois de concluída a tarefa de compilação de distribuição, enfileirar uma nova compilação para se certificar de que tudo está funcionando. Após a compilação for concluída com êxito, as novas imagens personalizadas serão apresentados no laboratório de destino que foi introduzido no ficheiro de configuração Labs.json.

## <a name="next-steps"></a>Passos Seguintes
No próximo artigo da série, atualize a fábrica de imagem com passos de política e a limpeza de retenção: [Definir política de retenção e executar scripts de limpeza](image-factory-set-retention-policy-cleanup.md).
