---
title: Ver avaliações de atualizações de atualização de automação do Azure
description: Este artigo descreve como visualizar avaliações de atualização para atualizações.
services: automation
ms.subservice: update-management
ms.date: 01/21/2020
ms.topic: conceptual
ms.openlocfilehash: 75762afc9ae69da81e89ce320f454d9764f82914
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81617388"
---
# <a name="view-azure-automation-update-management-update-assessments"></a>Ver avaliações de atualizações de atualização de automação do Azure

Na sua conta Azure Automation, selecione **Update Management** para ver o estado das suas máquinas.

Esta visão fornece informações sobre as suas máquinas, atualizações em falta, implementações de atualizações e implementações de atualizações programadas. Na coluna **COMPLIANCE,** pode ver a última vez que a máquina foi avaliada. Na coluna DE PRONTIDÃO DO AGENTE DE **Atualização,** pode ver a saúde do agente de atualização. Se houver algum problema, selecione o link para ir à documentação de resolução de problemas que pode ajudá-lo a corrigir o problema.

Para executar uma pesquisa de registo que devolve informações sobre a máquina, atualização ou implementação, selecione o item correspondente na lista. O painel de pesquisa de registo abre com uma consulta para o item selecionado.

![Visão padrão de gestão de atualização](media/automation-update-management/update-management-view.png)

## <a name="view-missing-updates"></a>Ver atualizações em falta

Selecione **as atualizações Em falta** para visualizar a lista de atualizações que faltam nas suas máquinas. Cada atualização está listada e pode ser selecionada. São todas mostradas informações sobre o número de máquinas que requerem a atualização, detalhes do sistema operativo e um link para mais informações. O painel de Pesquisa de Registos também mostra mais detalhes sobre as atualizações.

![Atualizações em falta](./media/automation-view-update-assessments/automation-view-update-assessments-missing-updates.png)

## <a name="update-classifications"></a>Classificações de atualizações

As tabelas seguintes listam as classificações de atualização suportadas na Gestão de Atualizações, com uma definição para cada classificação.

### <a name="windows"></a>Windows

|Classificação  |Descrição  |
|---------|---------|
|Atualizações críticas     | Atualizações para problemas específicos que abordam bugs críticos e não relacionados com a segurança.        |
|Atualizações de segurança     | Atualizações para questões específicas do produto e relacionadas com a segurança.        |
|Update rollups     | Conjuntos de fixações que são embalados em conjunto para uma fácil implantação.        |
|Pacotes de funcionalidades     | Novas funcionalidades do produto que são distribuídas fora de uma versão do produto.        |
|Service packs     | Conjuntos de hotfixos que são aplicados a uma aplicação.        |
|Atualizações de definições     | Atualizações para ficheiros de vírus ou outros ficheiros de definição.        |
|Ferramentas     | Utilitários ou funcionalidades que ajudam a completar uma ou mais tarefas.        |
|Atualizações     | Atualizações para aplicações ou ficheiros que estão instalados atualmente.        |

### <a name="linux"></a><a name="linux-2"></a>Linux

|Classificação  |Descrição  |
|---------|---------|
|Atualizações críticas e de segurança     | Atualizações para um problema específico ou um problema específico do produto, relacionado com a segurança.         |
|Outras atualizações     | Todas as outras atualizações que não são críticas por natureza ou que não são atualizações de segurança.        |

Para o Linux, a Atualização de Gestão pode distinguir entre atualizações críticas e atualizações de segurança na nuvem enquanto exibe dados de avaliação. (Esta granularidade é possível devido ao enriquecimento de dados na nuvem.) Para remendar, a Atualização Management baseia-se nos dados de classificação disponíveis na máquina. Ao contrário de outras distribuições, o CentOS não tem esta informação disponível nas versões RTM do produto. Se tiver máquinas CentOS configuradas para devolver os dados de segurança para o seguinte comando, a Atualização de Gestão pode corrigir com base nas classificações:

```bash
sudo yum -q --security check-update
```

Atualmente não existe um método suportado para permitir a disponibilidade de dados de classificação nativa no CentOS. Neste momento, apenas é prestado um apoio de melhor esforço aos clientes que tenham ativado esta funcionalidade por conta própria.

Para classificar as atualizações na versão 6 da Red Hat Enterprise, é necessário instalar o plugin de segurança de yum. No Red Hat Enterprise Linux 7, o plugin já faz parte do próprio yum, não há necessidade de instalar nada. Para mais informações, consulte o seguinte artigo de [conhecimento](https://access.redhat.com/solutions/10021)do Chapéu Vermelho.

## <a name="next-steps"></a>Passos seguintes

Depois de visualizar quaisquer avaliações de atualização, pode agendar uma implementação de atualização seguindo os passos nas atualizações e patches do [Manage para os seus VMs Azure](automation-tutorial-update-management.md).
