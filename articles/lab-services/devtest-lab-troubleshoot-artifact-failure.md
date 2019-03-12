---
title: Diagnosticar falhas de artefactos numa máquina virtual do Azure DevTest Labs | Documentos da Microsoft
description: Saiba como resolver problemas de falhas de artefactos no Azure DevTest Labs.
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
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: d5e03b7645fcacaeba4ccf147d48886cefd33ffb
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57531446"
---
# <a name="diagnose-artifact-failures-in-the-lab"></a>Diagnosticar falhas de artefactos no laboratório 
Depois de ter criado um artefato, pode verificar se foi concluída com êxito ou falha. Registos de artefactos no Azure DevTest Labs fornecem informações que pode usar para diagnosticar uma falha de artefacto. Tem duas opções para visualizar as informações de registo de artefactos para uma VM do Windows:

* No portal do Azure
* Na VM

> [!NOTE]
> Para garantir que as falhas são corretamente identificadas e explicadas, é importante que o artefacto tem a estrutura apropriada. Para obter informações sobre como construir corretamente um artefato, consulte [criar artefactos personalizados](devtest-lab-artifact-author.md). Para ver um exemplo de um artefacto adequadamente estruturado, consulte a [tipos de parâmetro de teste](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-test-paramtypes) artefacto.

## <a name="troubleshoot-artifact-failures-by-using-the-azure-portal"></a>Resolver problemas de falhas de artefactos com o portal do Azure

1. No portal do Azure, na sua lista de recursos, selecione o seu laboratório.
2. Selecione a VM do Windows que inclui o artefacto que pretende investigar.
3. No painel esquerdo, sob **gerais**, selecione **artefactos**. É apresentada uma lista dos artefactos associados a essa VM. O nome do artefacto e o estado de artefactos são indicadas.

   ![Estado de artefacto](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifacts-failure.png)

4. Selecione um artefato que mostra uma **falhada** estado. É aberto o artefacto. É apresentada uma mensagem de extensão que inclui detalhes sobre a falha do artefacto.

   ![Mensagem de erro do artefacto](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error.png)


## <a name="troubleshoot-artifact-failures-from-within-the-virtual-machine"></a>Resolver problemas de falhas de artefactos de dentro da máquina virtual

1. Inicie sessão na VM que contém o artefacto que queira diagnosticar.
2. Aceda a C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\\*1.9*\Status, onde *1.9* é o número de versão de extensão de Script personalizado do Azure.

   ![O ficheiro de estado](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error-vm-status.png)

3. Abra o **estado** ficheiro.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Postagens de blogs relacionados
* [Associar uma VM a um domínio do Active Directory existente, utilizando um modelo do Resource Manager no DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

## <a name="next-steps"></a>Passos Seguintes
* Saiba como [adicionar um repositório de Git a um laboratório](devtest-lab-add-artifact-repo.md).

