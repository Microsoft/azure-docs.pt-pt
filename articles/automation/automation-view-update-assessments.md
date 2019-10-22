---
title: Exibir avaliações de atualização de Gerenciamento de Atualizações do Azure
description: Este artigo descreve como exibir as avaliações de atualização para implantações de atualização.
services: automation
ms.service: automation
ms.subservice: update-management
author: bobbytreed
ms.author: robreed
ms.date: 05/17/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d4e20b2924504b714dff9f5ba650f9b25c1c26c3
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72690852"
---
# <a name="view-azure-update-management-update-assessments"></a>Exibir avaliações de atualização de Gerenciamento de Atualizações do Azure

Em sua conta de automação do Azure, selecione **Gerenciamento de atualizações** para exibir o status de seus computadores.

Esta exibição fornece informações sobre seus computadores, atualizações ausentes, implantações de atualização e implantações de atualização agendada. Na coluna **conformidade** , você pode ver a última vez em que o computador foi avaliado. Na coluna **prontidão do agente de atualização** , você pode ver a integridade do agente de atualização. Se houver um problema, selecione o link para acessar a documentação de solução de problemas que pode ajudá-lo a corrigir o problema.

Para executar uma pesquisa de logs que retorna informações sobre a máquina, atualização ou implantação, selecione o item correspondente na lista. O painel **pesquisa de log** é aberto com uma consulta para o item selecionado:

![Gerenciamento de Atualizações exibição padrão](media/automation-update-management/update-management-view.png)

## <a name="view-missing-updates"></a>Exibir atualizações ausentes

Selecione **atualizações ausentes** para exibir a lista de atualizações ausentes em seus computadores. Cada atualização é listada e pode ser selecionada. As informações sobre o número de computadores que exigem a atualização, os detalhes do sistema operacional e um link para obter mais informações são mostradas. O painel **pesquisa de logs** também mostra mais detalhes sobre as atualizações.

![Atualizações ausentes](./media/automation-view-update-assessments/automation-view-update-assessments-missing-updates.png)

## <a name="update-classifications"></a>Classificações de atualização

As tabelas a seguir listam as classificações de atualização no Gerenciamento de Atualizações, com uma definição para cada classificação.

### <a name="windows"></a>Windows

|Classificação  |Descrição  |
|---------|---------|
|Atualizações críticas     | Uma atualização para um problema específico que aborda um bug crítico não relacionado à segurança.        |
|Atualizações de segurança     | Uma atualização para um problema específico de um produto e relacionado à segurança.        |
|Update rollups     | Um conjunto cumulativo de hotfixes que são empacotados em conjunto para facilitar a implantação.        |
|Pacotes de funcionalidades     | Novos recursos do produto que são distribuídos fora de uma versão do produto.        |
|Service packs     | Um conjunto cumulativo de hotfixes que são aplicados a um aplicativo.        |
|Atualizações de definições     | Uma atualização para vírus ou outros arquivos de definição.        |
|Ferramentas     | Um utilitário ou recurso que ajuda a concluir uma ou mais tarefas.        |
|Atualizações     | Uma atualização para um aplicativo ou arquivo que está instalado no momento.        |

### <a name="linux-2"></a>Linux

|Classificação  |Descrição  |
|---------|---------|
|Atualizações críticas e de segurança     | Atualizações para um problema específico ou um problema específico do produto, relacionado à segurança.         |
|Outras atualizações     | Todas as outras atualizações que não são críticas por natureza ou que não são atualizações de segurança.        |

Para o Linux, Gerenciamento de Atualizações pode distinguir entre atualizações críticas e atualizações de segurança na nuvem enquanto exibe dados de avaliação. (Essa granularidade é possível devido ao enriquecimento de dados na nuvem.) Para aplicação de patch, Gerenciamento de Atualizações depende dos dados de classificação disponíveis no computador. Ao contrário de outras distribuições, o CentOS não tem essas informações disponíveis nas versões RTM do produto. Se você tiver máquinas CentOS configuradas para retornar dados de segurança para o comando a seguir, Gerenciamento de Atualizações poderá aplicar patch com base nas classificações:

```bash
sudo yum -q --security check-update
```

Atualmente, não há um método com suporte para habilitar a disponibilidade de dados de classificação nativa no CentOS. Neste momento, apenas o suporte de melhor esforço é fornecido aos clientes que habilitaram essa funcionalidade por conta própria.

## <a name="next-steps"></a>Passos seguintes

Depois de exibir as avaliações de atualização, você pode agendar uma implantação de atualização seguindo as etapas em [gerenciar atualizações e patches para suas VMs do Azure](automation-tutorial-update-management.md).
