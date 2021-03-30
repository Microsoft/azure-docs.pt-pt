---
title: Diagnosticar falhas de artefactos numa máquina virtual Azure DevTest Labs
description: A DevTest Labs fornece informações que pode usar para diagnosticar uma falha de artefactos. Este artigo mostra-lhe como resolver falhas de artefactos.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 440ce6a537ac8d6a21ae8010bfbb3c38a82bf01e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "85480818"
---
# <a name="diagnose-artifact-failures-in-the-lab"></a>Diagnosticar falhas de artefactos no laboratório 
Depois de ter criado um artefacto, pode verificar se conseguiu ou falhou. Os registos de artefactos nos Laboratórios Azure DevTest fornecem informações que pode usar para diagnosticar uma falha de artefacto. Tem algumas opções para visualizar as informações de registo de artefactos para um Windows VM:

* No portal do Azure
* No VM

> [!NOTE]
> Para garantir que as falhas são corretamente identificadas e explicadas, é importante que o artefacto tenha a estrutura adequada. Para obter informações sobre como construir corretamente um artefacto, consulte [Criar artefactos personalizados.](devtest-lab-artifact-author.md) Para ver um exemplo de um artefacto devidamente estruturado, consulte o artefacto [dos parâmetros de teste.](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-test-paramtypes)

## <a name="troubleshoot-artifact-failures-by-using-the-azure-portal"></a>Falhas de artefactos de resolução de problemas utilizando o portal Azure

1. No portal Azure, na sua lista de recursos, selecione o seu laboratório.
2. Escolha o Windows VM que inclui o artefacto que pretende investigar.
3. No painel esquerdo, em **GENERAL,** selecione **Artefactos.** Aparece uma lista de artefactos associados a esse VM. O nome do artefacto e o estado do artefacto são indicados.

   ![Estado do artefacto](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifacts-failure-new.png)

4. Selecione um artefacto que mostre um **estado falhado.** O artefacto abre. É apresentada uma mensagem de extensão que inclui detalhes sobre a falha do artefacto.

   ![Mensagem de erro do artefacto](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error.png)


## <a name="troubleshoot-artifact-failures-from-within-the-virtual-machine"></a>Falhas de artefactos de resolução de problemas dentro da máquina virtual

1. Inscreva-se no VM que contém o artefacto que pretende diagnosticar.
2. Ir para C:\Pacotes\Plugins\Microsoft.Compute.CustomScriptExtension \\ *1.9*\Estado, onde *1.9* é o número da versão da extensão personalizada Azure.

   ![O ficheiro status](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error-vm-status-new.png)

3. Abra o ficheiro **de estado.**

Para obter instruções sobre a descoberta dos ficheiros de registo num **VM Linux,** consulte o seguinte artigo: [Use a versão 2 da extensão de script personalizada Azure com máquinas virtuais Linux](../virtual-machines/extensions/custom-script-linux.md#troubleshooting)


## <a name="related-blog-posts"></a>Publicações de blogs relacionadas
* [Junte um VM a um domínio de Diretório Ativo existente usando um modelo de Gestor de Recursos em Laboratórios DevTest](https://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

## <a name="next-steps"></a>Passos seguintes
* Aprenda a [adicionar um repositório de Git a um laboratório.](devtest-lab-add-artifact-repo.md)

