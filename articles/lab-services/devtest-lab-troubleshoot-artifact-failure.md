---
title: Diagnosticar falhas de artefato em uma máquina virtual Azure DevTest Labs
description: Os Laboratórios DevTest fornecem informações que pode usar para diagnosticar uma falha de artefacto. Este artigo mostra-lhe como resolver falhas de artefactos.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 115e0086-3293-4adf-8738-9f639f31f918
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: 7229f1ee4061eb38b7c6da09df21102ab302ab42
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2020
ms.locfileid: "76760322"
---
# <a name="diagnose-artifact-failures-in-the-lab"></a>Diagnosticar falhas de artefato no laboratório 
Depois de criar um artefato, você pode verificar se ele teve êxito ou falhou. Os logs de artefato no Azure DevTest Labs fornecem informações que você pode usar para diagnosticar uma falha de artefato. Você tem algumas opções para exibir as informações de log de artefato para uma VM do Windows:

* No portal Azure
* Na VM

> [!NOTE]
> Para garantir que as falhas sejam identificadas e explicadas corretamente, é importante que o artefato tenha a estrutura adequada. Para obter informações sobre como construir um artefato corretamente, consulte [criar artefatos personalizados](devtest-lab-artifact-author.md). Para ver um exemplo de um artefato corretamente estruturado, confira o artefato de [tipos de parâmetro de teste](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-test-paramtypes) .

## <a name="troubleshoot-artifact-failures-by-using-the-azure-portal"></a>Solucionar problemas de falhas de artefato usando o portal do Azure

1. Na portal do Azure, na lista de recursos, selecione seu laboratório.
2. Escolha a VM do Windows que inclui o artefato que você deseja investigar.
3. No painel esquerdo, em **geral**, selecione **artefatos**. É exibida uma lista de artefatos associados a essa VM. O nome do artefato e o status do artefato são indicados.

   ![Estado do artefacto](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifacts-failure.png)

4. Selecione um artefato que mostre um status de **falha** . O artefato é aberto. Uma mensagem de extensão que inclui detalhes sobre a falha do artefato é exibida.

   ![Mensagem de erro de artefato](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error.png)


## <a name="troubleshoot-artifact-failures-from-within-the-virtual-machine"></a>Solucionar problemas de falhas de artefato de dentro da máquina virtual

1. Entre na VM que contém o artefato que você deseja diagnosticar.
2. Acesse C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\\*1,9*\Status, em que *1,9* é o número de versão da extensão de script personalizado do Azure.

   ![O arquivo de status](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error-vm-status.png)

3. Abra o arquivo de **status** .

Para obter instruções sobre como localizar os arquivos de log em uma VM do **Linux** , consulte o seguinte artigo: [usar a extensão de script personalizado do Azure versão 2 com máquinas virtuais do Linux](../virtual-machines/extensions/custom-script-linux.md#troubleshooting)


## <a name="related-blog-posts"></a>Postagens de blog relacionadas
* [Ingressar uma VM em um domínio Active Directory existente usando um modelo do Resource Manager no DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

## <a name="next-steps"></a>Passos seguintes
* Saiba como [Adicionar um repositório git a um laboratório](devtest-lab-add-artifact-repo.md).

