---
title: Diagnosticar falhas de artefactos numa máquina virtual azure DevTest Labs
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76760322"
---
# <a name="diagnose-artifact-failures-in-the-lab"></a>Diagnosticar falhas de artefactos no laboratório 
Depois de ter criado um artefacto, pode verificar se foi bem sucedido ou falhado. Os registos de artefactos em Azure DevTest Labs fornecem informações que pode usar para diagnosticar uma falha de artefacto. Tem algumas opções para visualizar as informações de registo de artefactos para um VM do Windows:

* No portal do Azure
* No VM

> [!NOTE]
> Para garantir que as falhas são corretamente identificadas e explicadas, é importante que o artefacto tenha a estrutura adequada. Para obter informações sobre como construir corretamente um artefacto, consulte [Criar artefactos personalizados.](devtest-lab-artifact-author.md) Para ver um exemplo de um artefacto devidamente estruturado, confira o artefacto do tipo de parâmetro de [teste.](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-test-paramtypes)

## <a name="troubleshoot-artifact-failures-by-using-the-azure-portal"></a>Falhas de artefactos de resolução de problemas utilizando o portal Azure

1. No portal Azure, na sua lista de recursos, selecione o seu laboratório.
2. Escolha o Windows VM que inclui o artefacto que pretende investigar.
3. No painel esquerdo, em **GERAL,** selecione **Artefactos**. Aparece uma lista de artefactos associados a esse VM. O nome do artefacto e o estado do artefacto estão indicados.

   ![Estado do artefacto](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifacts-failure.png)

4. Selecione um artefacto que mostre um estado **falhado.** O artefacto abre. É apresentada uma mensagem de extensão que inclui detalhes sobre a falha do artefacto.

   ![Mensagem de erro de artefacto](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error.png)


## <a name="troubleshoot-artifact-failures-from-within-the-virtual-machine"></a>Falhas de artefactos de resolução de problemas dentro da máquina virtual

1. Inscreva-se no VM que contém o artefacto que pretende diagnosticar.
2. Vá a C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\\*1.9*\Status, onde *1.9* é o número de extensão de extensão de script personalizado do Azure.

   ![O ficheiro Status](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error-vm-status.png)

3. Abra o ficheiro de **estado.**

Para obter instruções sobre a descoberta dos ficheiros de registo num **VM Linux,** consulte o seguinte artigo: [Utilize a versão 2 da extensão personalizada do script azure com máquinas virtuais Linux](../virtual-machines/extensions/custom-script-linux.md#troubleshooting)


## <a name="related-blog-posts"></a>Posts de blog relacionados
* [Junte-se a um VM a um domínio de Diretório Ativo existente usando um modelo de Gestor de Recursos em Laboratórios DevTest](https://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

## <a name="next-steps"></a>Passos seguintes
* Aprenda a [adicionar um repositório Git a um laboratório.](devtest-lab-add-artifact-repo.md)

