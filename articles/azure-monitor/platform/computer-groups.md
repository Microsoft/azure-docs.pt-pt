---
title: Grupos informáticos em consultas de registo do Monitor Azure / Microsoft Docs
description: Os grupos informáticos no Monitor Azure permitem-lhe examinar consultas de registo a um determinado conjunto de computadores.  Este artigo descreve os diferentes métodos que pode usar para criar grupos informáticos e como usá-los numa consulta de registo.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/05/2019
ms.openlocfilehash: a005b6cec811b8a584123dc4c8abab77766961e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274778"
---
# <a name="computer-groups-in-azure-monitor-log-queries"></a>Grupos informáticos em consultas de registo do Monitor Azure
Os grupos informáticos no Monitor Azure permitem-lhe examinar consultas de [registo](../log-query/log-query-overview.md) a um determinado conjunto de computadores.  Cada grupo é povoado com computadores usando uma consulta que você define ou importando grupos de diferentes fontes.  Quando o grupo está incluído numa consulta de registo, os resultados limitam-se a registos que correspondam aos computadores do grupo.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="creating-a-computer-group"></a>Criação de um grupo informático
Pode criar um grupo informático no Monitor Azure utilizando qualquer um dos métodos na tabela seguinte.  Os detalhes de cada método são fornecidos nas secções abaixo. 

| Método | Descrição |
|:--- |:--- |
| Consulta de log |Crie uma consulta de registo que retorne uma lista de computadores. |
| API de Pesquisas de Registos |Utilize a API de pesquisa de registo para criar programáticamente um grupo de computador com base nos resultados de uma consulta de registo. |
| Active Directory |Digitalize automaticamente a adesão do grupo a quaisquer computadores de agente que sejam membros de um domínio de Diretório Ativo e crie um grupo no Monitor Azure para cada grupo de segurança. (apenas máquinas de janelas)|
| Configuration Manager | Importar coleções do Microsoft Endpoint Configuration Manager e criar um grupo no Monitor Azure para cada um. |
| Windows Server Update Services |Digitalize automaticamente servidores ou clientes wSUS para direcionar grupos e crie um grupo no Monitor Azure para cada um. |

### <a name="log-query"></a>Consulta de log
Os grupos informáticos criados a partir de uma consulta de registo contêm todos os computadores devolvidos por uma consulta que define.  Esta consulta é executada sempre que o grupo de computador é usado para que quaisquer alterações desde que o grupo foi criado se reflitam.  

Pode utilizar qualquer consulta para um grupo de computador, mas deve `distinct Computer`devolver um conjunto distinto de computadores utilizando .  Segue-se uma consulta típica de exemplo que pode utilizar como um grupo de computador.

    Heartbeat | where Computer contains "srv" | distinct Computer

Utilize o seguinte procedimento para criar um grupo informático a partir de uma pesquisa de registo no portal Azure.

1. Clique em **Registos** no menu **Do Monitor Azure** no portal Azure.
1. Crie e faça uma consulta que dereque os computadores que deseja no grupo.
1. Clique em **Guardar** na parte superior do ecrã.
1. Altere **Guardar as funções** e selecione Guardar esta consulta como um grupo de **computador**. **Function**
1. Forneça valores para cada propriedade para o grupo de computador descrito na tabela e clique em **Guardar**.

A tabela seguinte descreve as propriedades que definem um grupo de computador.

| Propriedade | Descrição |
|:---|:---|
| Nome   | Nome da consulta para exibir no portal. |
| Pseudónimo de função | Um pseudónimo único usado para identificar o grupo de computador numa consulta. |
| Categoria       | Categoria para organizar as consultas no portal. |


### <a name="active-directory"></a>Active Directory
Quando configura o Monitor Azure para importar membros do grupo Ative Directory, analisa a adesão do grupo a qualquer domínio Windows que se juntou a computadores com o agente Log Analytics.  Um grupo informático é criado no Monitor Azure para cada grupo de segurança no Ative Directory, e cada computador Windows é adicionado aos grupos informáticos correspondentes aos grupos de segurança de que são membros.  Esta adesão é continuamente atualizada a cada 4 horas.  

> [!NOTE]
> Os grupos de Diretórios Ativos importados contêm apenas máquinas Windows.

Configura o Azure Monitor para importar grupos de segurança de Diretório Ativo a partir de **configurações avançadas** no seu espaço de trabalho Log Analytics no portal Azure.  Selecione **Grupos informáticos,** **Diretório Ativo,** e, em seguida, **importe membros do grupo De Diretório Ativo a partir de computadores**.  Não há nenhuma configuração adicional.

![Grupos informáticos do Ative Directory](media/computer-groups/configure-activedirectory.png)

Quando os grupos foram importados, o menu enumera o número de computadores com membros do grupo detetados e o número de grupos importados.  Pode clicar em qualquer uma destas ligações para devolver os registos **do ComputerGroup** com esta informação.

### <a name="windows-server-update-service"></a>Serviço de atualização do servidor do Windows
Quando configura o Monitor Azure para importar membros do grupo WSUS, analisa a associação de grupos direcionados de quaisquer computadores com o agente Log Analytics.  Se estiver a utilizar o alvo do lado do cliente, qualquer computador ligado ao Monitor Azure e que faça parte de quaisquer grupos de alvo wSUS tem a sua filiação no grupo importada para o Monitor Azure. Se estiver a utilizar o alvo do lado do servidor, o agente Log Analytics deve ser instalado no servidor WSUS para que as informações de membro do grupo sejam importadas para o Monitor Azure.  Esta adesão é continuamente atualizada a cada 4 horas. 

Configura o Monitor Azure para importar grupos WSUS a partir de **configurações avançadas** no seu espaço de trabalho Log Analytics no portal Azure.  Selecione **Grupos informáticos,** **WSUS,** e, em seguida, **importe membros do grupo WSUS**.  Não há nenhuma configuração adicional.

![Grupos informáticos da WSUS](media/computer-groups/configure-wsus.png)

Quando os grupos foram importados, o menu enumera o número de computadores com membros do grupo detetados e o número de grupos importados.  Pode clicar em qualquer uma destas ligações para devolver os registos **do ComputerGroup** com esta informação.

### <a name="configuration-manager"></a>Configuration Manager
Quando configura o Monitor Azure para importar membros de recolha do Gestor de Configuração, cria um grupo informático para cada coleção.  A informação sobre a adesão à coleção é recuperada a cada 3 horas para manter os grupos informáticos atuais. 

Antes de importar coleções do Gestor de Configuração, deve ligar o Gestor de [Configuração ao Monitor Azure](collect-sccm.md).  

![Grupos informáticos do SCCM](media/computer-groups/configure-sccm.png)

Quando as coleções foram importadas, o menu enumera o número de computadores com membros do grupo detetados e o número de grupos importados.  Pode clicar em qualquer uma destas ligações para devolver os registos **do ComputerGroup** com esta informação.

## <a name="managing-computer-groups"></a>Gestão de grupos informáticos
Pode ver grupos de computador que foram criados a partir de uma consulta de log ou da API de Pesquisa de Registo a partir de **configurações avançadas** no seu espaço de trabalho Log Analytics no portal Azure.  Selecione **Grupos de Computador** e, em seguida, **Grupos Guardados**.  

Clique no **x** na coluna **Remover** para eliminar o grupo de computador.  Clique no ícone dos **membros do View** para um grupo para executar a pesquisa de registo do grupo que devolve os seus membros.  Não pode modificar um grupo de computador, mas deve eliminá-lo e recriá-lo com as definições modificadas.

![Grupos de computador guardados](media/computer-groups/configure-saved.png)


## <a name="using-a-computer-group-in-a-log-query"></a>Usando um grupo de computador em uma consulta de log
Você usa um grupo de computador criado a partir de uma consulta de log em uma consulta, tratando o seu pseudónimo como uma função, tipicamente com a seguinte sintaxe:

  `Table | where Computer in (ComputerGroup)`

Por exemplo, pode utilizar o seguinte para devolver registos de Atualizações Sumárias apenas para computadores de um grupo de computador chamado mycomputergroup.
 
  `UpdateSummary | where Computer in (mycomputergroup)`


Os grupos informáticos importados e os seus computadores incluídos são armazenados na tabela **ComputerGroup.**  Por exemplo, a seguinte consulta devolveria uma lista de computadores no grupo DeDomain Computers do Ative Directory. 

  `ComputerGroup | where GroupSource == "ActiveDirectory" and Group == "Domain Computers" | distinct Computer`

A seguinte consulta devolveria os registos de Atualizações Sumárias apenas para computadores em Computadores de Domínio.

  ```
  let ADComputers = ComputerGroup | where GroupSource == "ActiveDirectory" and Group == "Domain Computers" | distinct Computer;
  UpdateSummary | where Computer in (ADComputers)
  ```




## <a name="computer-group-records"></a>Registos de grupos informáticos
É criado um disco no espaço de trabalho do Log Analytics para cada membro do grupo de computador criado a partir de Ative Directory ou WSUS.  Estes registos têm um tipo de **ComputerGroup** e têm as propriedades na tabela seguinte.  Os registos não são criados para grupos informáticos com base em consultas de registo.

| Propriedade | Descrição |
|:--- |:--- |
| `Type` |*Grupo informático* |
| `SourceSystem` |*SourceSystem* |
| `Computer` |Nome do computador membro. |
| `Group` |Nome do grupo. |
| `GroupFullName` |Caminho completo para o grupo, incluindo a fonte e o nome de origem. |
| `GroupSource` |Fonte de que o grupo foi recolhido. <br><br>ActiveDirectory<br>WSUS<br>WSUSClientTargeting |
| `GroupSourceName` |Nome da fonte de que o grupo foi recolhido.  Para o Diretório Ativo, este é o nome de domínio. |
| `ManagementGroupName` |O nome do grupo de gestão para agentes do SCOM.  Para outros agentes, este\<é O II- espaço de trabalho\> |
| `TimeGenerated` |Data e hora o grupo de computador foi criado ou atualizado. |

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre consultas de [registo](../log-query/log-query-overview.md) para analisar os dados recolhidos a partir de fontes e soluções de dados.  

