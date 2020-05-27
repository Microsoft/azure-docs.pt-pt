---
title: Trabalhar com configurações de âmbito para a Gestão de Atualização de Automação Azure
description: Este artigo diz como trabalhar com configurações de âmbito quando está a usar a Atualização.
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 23ec49f2d68cf376ef0beb118d8bf69ada7bc0de
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83832032"
---
# <a name="work-with-scope-configurations-for-update-management"></a>Trabalhar com configurações de âmbito para Gestão de Atualizações

Este artigo descreve como pode trabalhar com configurações de âmbito ao utilizar a funcionalidade [De Gestão](automation-update-management.md) de Atualizações em VMs. 

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="check-the-scope-configuration"></a><a name="scope-configuration"></a>Verifique a configuração do âmbito

A Atualização A Gestão utiliza uma configuração de âmbito dentro do espaço de trabalho do Log Analytics para direcionar os computadores para ativar a funcionalidade. A configuração de âmbito é um grupo de uma ou mais pesquisas guardadas que são usadas para limitar o âmbito da funcionalidade a computadores específicos. Para aceder às configurações de âmbito:

1. Na sua conta de Automação sob **recursos Relacionados,** selecione **Workspace**. 

2. Escolha o espaço de trabalho sob fontes de dados do **Espaço de Trabalho,** e selecione **Configurações**de Âmbito .

3. Se o espaço de trabalho selecionado ainda não tiver a funcionalidade De gestão de atualizações ativada, cria a configuração de `MicrosoftDefaultScopeConfig-Updates` âmbito. 

4. Se o espaço de trabalho selecionado já tiver a funcionalidade ativada, não é reimplantada e a configuração de âmbito não é adicionada à funcionalidade. 

5. Selecione a elipse em qualquer uma das configurações de âmbito e, em seguida, clique em **Editar**. 

6. No painel de edição, selecione **Select Computer Groups**. O painel de Grupos de Computadores mostra as pesquisas guardadas que são usadas para criar a configuração de âmbito.

## <a name="view-a-saved-search"></a>Ver uma pesquisa guardada

Quando um computador é adicionado à Atualização management, também é adicionado a uma pesquisa guardada no seu espaço de trabalho. A pesquisa guardada é uma consulta que contém os computadores direcionados.

1. Navegue para o seu espaço de trabalho Log Analytics e selecione **pesquisas guardadas** sob **o General**. A pesquisa guardada utilizada pela Atualização gestão é:

|Name     |Categoria  |Alias  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     | Atualizações        | Updates__MicrosoftDefaultComputerGroup         |

2. Selecione a pesquisa guardada para visualizar a consulta usada para povoar o grupo. A imagem seguinte mostra a consulta e os seus resultados:

    ![Pesquisas guardadas](media/automation-scope-configurations-update-management/logsearch.png)

## <a name="next-steps"></a>Passos seguintes

* Para trabalhar com a funcionalidade, consulte ['Gerir atualizações e patches' para os seus VMs Azure](automation-tutorial-update-management.md).
* Para resolver erros de funcionalidades, consulte problemas de Gestão de Atualização de Resolução de [Problemas](troubleshoot/update-management.md).
* Para resolver os erros do agente de atualização do Windows, consulte problemas de agente de [atualização do Windows](troubleshoot/update-agent-issues.md).
* Para resolver os erros do agente de atualização do Linux, consulte problemas de agente de [atualização Da Troubleshoot Linux](troubleshoot/update-agent-issues-linux.md).