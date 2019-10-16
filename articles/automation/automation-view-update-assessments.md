---
title: Exibir avaliações de atualização de Gerenciamento de Atualizações do Azure
description: Este artigo descreve como exibir as avaliações de atualização para implantações de atualização
services: automation
ms.service: automation
ms.subservice: update-management
author: bobbytreed
ms.author: robreed
ms.date: 05/17/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: e88622ede6437086b86a33081d6ec9b9ea50ef65
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72377723"
---
# <a name="view-azure-update-management-update-assessments"></a>Exibir avaliações de atualização de Gerenciamento de Atualizações do Azure

Na sua conta de automação, selecione **Gerenciamento de atualizações** para exibir o status de seus computadores.

Esta exibição fornece informações sobre seus computadores, atualizações ausentes, implantações de atualização e implantações de atualização agendada. Na **coluna conformidade**, você pode ver a última vez em que o computador foi avaliado. Na coluna **prontidão do agente de atualização** , você pode ver se a integridade do agente de atualização. Se houver um problema, selecione o link para acessar a documentação de solução de problemas que pode ajudá-lo a saber quais etapas executar para corrigir o problema.

Para executar uma pesquisa de logs que retorna informações sobre a máquina, atualização ou implantação, selecione o item na lista. O painel **pesquisa de log** é aberto com uma consulta para o item selecionado:

![Gerenciamento de Atualizações exibição padrão](media/automation-update-management/update-management-view.png)

## <a name="view-missing-updates"></a>Exibir atualizações ausentes

Selecione **atualizações ausentes** para exibir a lista de atualizações ausentes em seus computadores. Cada atualização é listada e pode ser selecionada. As informações sobre o número de computadores que exigem a atualização, o sistema operacional e um link para obter mais informações são mostradas. O painel **pesquisa de log** mostra mais detalhes sobre as atualizações.

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
|Outras atualizações     | Todas as outras atualizações que não são críticas por natureza ou não são atualizações de segurança.        |

Para o Linux, Gerenciamento de Atualizações pode distinguir entre atualizações críticas e de segurança na nuvem enquanto exibe dados de avaliação devido ao enriquecimento de dados na nuvem. Para aplicação de patch, Gerenciamento de Atualizações depende dos dados de classificação disponíveis no computador. Ao contrário de outras distribuições, o CentOS não tem essas informações disponíveis prontamente. Se você tiver computadores CentOS configurados de forma a retornar dados de segurança para o comando a seguir, Gerenciamento de Atualizações poderá aplicar patches com base nas classificações.

```bash
sudo yum -q --security check-update
```

Atualmente, não há método com suporte para habilitar a classificação nativa-disponibilidade de dados no CentOS. Neste momento, apenas o suporte de melhor esforço é fornecido aos clientes que podem ter habilitado isso por conta própria.

## <a name="next-steps"></a>Próximos Passos

Depois de exibir as avaliações de atualização, você pode agendar uma implantação de atualização seguindo as etapas em [gerenciar atualizações e patches para suas VMs do Azure](automation-tutorial-update-management.md).