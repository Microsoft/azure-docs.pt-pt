---
title: Trabalhar com configurações de âmbito para rastreio e inventário de alterações de automatização Azure
description: Este artigo diz como trabalhar com configurações de âmbito quando está a usar o Change Tracking e o Inventário.
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 4fac94cc2f8f378b7e9d8e9485baed6a0ffa838b
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83832168"
---
# <a name="work-with-scope-configurations-for-change-tracking-and-inventory"></a>Trabalhar com configurações de âmbito para rastreio e inventário de alterações

Este artigo descreve como pode trabalhar com configurações de âmbito ao ativar a funcionalidade de Gestão de [Atualizações](automation-update-management.md) em VMs. 

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="check-the-scope-configuration"></a><a name="scope-configuration"></a>Verifique a configuração do âmbito

A Atualização Management utiliza uma configuração de âmbito dentro do espaço de trabalho do Log Analytics para direcionar os computadores para ativar a Gestão de Atualizações. A configuração de âmbito é um grupo de uma ou mais pesquisas guardadas que são usadas para limitar o âmbito da funcionalidade a computadores específicos. Para aceder às configurações de âmbito:

1. Na sua conta de Automação sob **recursos Relacionados,** selecione **Workspace**. 

2. Escolha o espaço de trabalho sob fontes de dados do **Espaço de Trabalho,** e selecione **Configurações**de Âmbito .

3. Se o espaço de trabalho selecionado ainda não tiver a funcionalidade De gestão de atualizações ativada, cria a configuração de `MicrosoftDefaultScopeConfig-ChangeTracking` âmbito. 

4. Se o espaço de trabalho selecionado já tiver a funcionalidade ativada, não é reimplantada e a configuração de âmbito não é adicionada à funcionalidade. 

5. Selecione a elipse em qualquer uma das configurações de âmbito e, em seguida, clique em **Editar**. 

6. No painel de edição, selecione **Select Computer Groups**. O painel de Grupos de Computadores mostra as pesquisas guardadas que são usadas para criar a configuração de âmbito.

## <a name="view-a-saved-search"></a>Ver uma pesquisa guardada

Quando um computador é adicionado ao Change Tracking and Inventory, também é adicionado a uma pesquisa guardada no seu espaço de trabalho. A pesquisa guardada é uma consulta que contém os computadores direcionados.

1. Navegue para o seu espaço de trabalho Log Analytics e selecione **pesquisas guardadas** sob **o General**. A pesquisa guardada utilizada pela Atualização gestão é:

    |Name     |Categoria  |Alias  |
    |---------|---------|---------|
    |MicrosoftDefaultComputerGroup     |  ChangeTracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |

2. Selecione a pesquisa guardada para visualizar a consulta usada para povoar o grupo. A imagem seguinte mostra a consulta e os seus resultados:

    ![Pesquisas guardadas](media/automation-scope-configurations-change-tracking/logsearch.png)

## <a name="next-steps"></a>Passos seguintes

* Para trabalhar com o Change Tracking e Inventário, consulte Gerir o Rastreio e O Inventário de [Alterações](change-tracking-file-contents.md).
* Para resolver problemas de funcionalidades gerais, consulte problemas de rastreio e inventário de mudança de resolução de [problemas.](troubleshoot/change-tracking.md)