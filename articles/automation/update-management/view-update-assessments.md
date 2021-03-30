---
title: Ver avaliações de atualização da Azure Automation
description: Este artigo diz como visualizar avaliações de atualização para implementações de Gestão de Atualização.
services: automation
ms.subservice: update-management
ms.date: 09/17/2020
ms.topic: conceptual
ms.openlocfilehash: 39df5888a330a92ae043e34c3043da5b1f566345
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92222524"
---
# <a name="view-update-assessments-in-update-management"></a>Ver avaliações de atualização na Gestão de Atualização

Na Gestão de Atualizações, pode visualizar informações sobre as suas máquinas, atualizações em falta, atualizações e implementações de atualizações programadas. Pode visualizar as informações de avaliação da máquina virtual Azure selecionada, a partir do servidor ativado pelo Arco selecionado, ou da conta Automation em todas as máquinas e servidores configurados.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com)

## <a name="view-update-assessment"></a>Ver avaliação de atualizações

Para ver a avaliação de atualização a partir de um Azure VM, navegue para **Máquinas Virtuais** e selecione a sua máquina virtual da lista. A partir do menu esquerdo, selecione **as atualizações do Anfitrião +** e, em seguida, selecione **Ir para a Gestão** de Atualização na página de atualizações do **anfitrião + convidado.**

Na Gestão de Atualizações, pode visualizar informações sobre a sua máquina, atualizações em falta, atualizações e implementações de atualização programadas.

[![Vista de avaliação da gestão de atualização para Azure VM](./media/view-update-assessments/update-assessment-azure-vm.png)](./media/view-update-assessments/update-assessment-azure-vm-expanded.png#lightbox)

Para visualizar a avaliação da atualização a partir de um servidor ativado pelo Arc, navegue para **Servers - Azure Arc** e selecione o seu servidor a partir da lista. A partir do menu esquerdo, selecione **As atualizações do Convidado e do anfitrião**. Na página **de atualizações do anfitrião + anfitrião,** selecione **Ir para a Gestão de Atualização**.

Na Gestão de Atualizações, pode visualizar informações sobre a sua máquina ativada pelo Arco, atualizações em falta, atualizações e implementações de atualização programadas.

[![Vista de avaliação da gestão de atualização para servidores ativados arc](./media/view-update-assessments/update-assessment-arc-server.png)](./media/view-update-assessments/update-assessment-arc-server-expanded.png#lightbox)

Para visualizar a avaliação da atualização em todas as máquinas, incluindo os servidores ativados pelo Arc a partir da sua conta de Automação, navegue para **contas de Automação** e selecione a sua conta Demôm automação com a Gestão de Atualização ativada na lista. Na sua conta Automation, selecione **'Update management'** a partir do menu esquerdo.

As atualizações para o seu ambiente estão listadas na página **de gestão de Atualização.** Se alguma atualização for identificada como desaparecida, uma lista delas é mostrada no separador **atualizações desaparecidos.**

[![Vista padrão de Gestão de Atualização](./media/overview/update-management-view.png)](./media/overview/update-management-view-expanded.png#lightbox)

Sob a coluna **COMPLIANCE,** pode ver a última vez que a máquina foi avaliada. Na coluna **DE PRONTIDÃO DO AGENTE DE ATUALIZAÇÃO,** pode ver a saúde do agente de atualização. Se houver algum problema, selecione o link para ir para a documentação de resolução de problemas que pode ajudá-lo a corrigir o problema.

No **link Informação**, selecione o link para uma atualização para abrir o artigo de suporte que lhe dá informações importantes sobre a atualização.

[![Ver estado de atualização](./media/view-update-assessments/missing-updates.png)](./media/view-update-assessments/missing-updates-expanded.png#lightbox)

Clique em qualquer outro lugar da atualização para abrir o painel de pesquisa de registo. A consulta para a pesquisa de registos está predefinida para essa atualização específica. Pode modificar esta consulta ou criar a sua própria consulta para visualizar informações detalhadas.

[![Ver resultados de consulta de registo](./media/view-update-assessments/logsearch-results.png)](./media/view-update-assessments/logsearch-results-expanded.png#lightbox)

## <a name="view-missing-updates"></a>Ver atualizações em falta

Selecione **atualizações em falta** para ver a lista de atualizações que faltam nas suas máquinas. Cada atualização está listada e pode ser selecionada. São mostradas informações sobre o número de máquinas que requerem a atualização, detalhes do sistema operativo e um link para mais informações. O painel de Pesquisa de Registos também mostra mais detalhes sobre as atualizações.

![Atualizações em falta](./media/view-update-assessments/automation-view-update-assessments-missing-updates.png)

## <a name="work-with-update-classifications"></a>Trabalhar com classificações de atualização

As tabelas seguintes listam as classificações de atualização suportadas na Gestão de Atualização, com uma definição para cada classificação.

### <a name="windows"></a>Windows

|Classificação  |Descrição  |
|---------|---------|
|Atualizações críticas     | Atualizações para problemas específicos que abordam bugs críticos e não relacionados com a segurança.        |
|Atualizações de segurança     | Atualizações para questões específicas do produto, relacionadas com a segurança.        |
|Update rollups     | Conjuntos de hotfixes que são embalados em conjunto para uma fácil implementação.        |
|Pacotes de funcionalidades     | Novas funcionalidades do produto que são distribuídas fora de uma versão do produto.        |
|Service packs     | Conjuntos de hotfixes que são aplicados a uma aplicação.        |
|Atualizações de definições     | Atualizações para ficheiros de vírus ou outra definição.        |
|Ferramentas     | Utilitários ou funcionalidades que ajudam a completar uma ou mais tarefas.        |
|Atualizações     | Atualizações para aplicações ou ficheiros que estão instalados atualmente.        |

### <a name="linux"></a>Linux

|Classificação  |Descrição  |
|---------|---------|
|Atualizações críticas e de segurança     | Atualizações para um problema específico ou para um problema específico do produto, relacionado com a segurança.         |
|Outras atualizações     | Todas as outras atualizações que não são de natureza crítica ou que não são atualizações de segurança.        |

Para o Linux, a Gestão de Atualizações pode distinguir entre atualizações críticas e atualizações de segurança na nuvem enquanto exibe dados de avaliação. (Esta granularidade é possível devido ao enriquecimento de dados na nuvem.) Para remendar, a Update Management baseia-se nos dados de classificação disponíveis na máquina. Ao contrário de outras distribuições, o CentOS não dispõe desta informação disponível nas versões RTM do produto. Se tiver máquinas CentOS configuradas para devolver dados de segurança para o seguinte comando, a Gestão de Atualização pode corrigir com base nas classificações:

```bash
sudo yum -q --security check-update
```

Não existe atualmente um método suportado para permitir a disponibilidade de dados de classificação nativa no CentOS. Neste momento, apenas é prestado um melhor suporte aos clientes que tenham ativado esta funcionalidade por si só.

Para classificar as atualizações na versão 6 da Red Hat Enterprise, é necessário instalar o plugin de segurança yum. No Red Hat Enterprise Linux 7, o plugin já faz parte do próprio yum, não há necessidade de instalar nada. Para mais informações, consulte o seguinte artigo de [conhecimento](https://access.redhat.com/solutions/10021)da Red Hat .

## <a name="next-steps"></a>Passos seguintes

A próxima fase do processo é [implementar atualizações](deploy-updates.md) em máquinas não conformes e rever os resultados da implementação.
