---
title: Grupos informáticos em consultas de registo do Monitor Azure / Microsoft Docs
description: Os grupos informáticos no Monitor Azure permitem-lhe examinar consultas de registo a um determinado conjunto de computadores.  Este artigo descreve os diferentes métodos que pode usar para criar grupos informáticos e como usá-los numa consulta de registo.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/05/2019
ms.openlocfilehash: a005b6cec811b8a584123dc4c8abab77766961e0
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78373303"
---
# <a name="computer-groups-in-azure-monitor-log-queries"></a>Grupos informáticos em consultas de registo do Monitor Azure
Os grupos informáticos no Monitor Azure permitem-lhe examinar consultas de [registo](../log-query/log-query-overview.md) a um determinado conjunto de computadores.  Cada grupo é preenchido com computadores por meio de uma consulta por si ou ao importar grupos de diferentes origens.  Quando o grupo está incluído numa consulta de registo, os resultados limitam-se a registos que correspondam aos computadores do grupo.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="creating-a-computer-group"></a>Criar um grupo de computadores
Pode criar um grupo informático no Monitor Azure utilizando qualquer um dos métodos na tabela seguinte.  Obter detalhes sobre cada método são fornecidos nas secções abaixo. 

| Método | Descrição |
|:--- |:--- |
| Consulta de log |Crie uma consulta de registo que retorne uma lista de computadores. |
| API de Pesquisas de Registos |Utilize a API de pesquisa de registo para criar programáticamente um grupo de computador com base nos resultados de uma consulta de registo. |
| Active Directory |Digitalize automaticamente a adesão do grupo a quaisquer computadores de agente que sejam membros de um domínio de Diretório Ativo e crie um grupo no Monitor Azure para cada grupo de segurança. (apenas máquinas de janelas)|
| Configuration Manager | Importar coleções do Microsoft Endpoint Configuration Manager e criar um grupo no Monitor Azure para cada um. |
| Windows Server Update Services |Digitalize automaticamente servidores ou clientes wSUS para direcionar grupos e crie um grupo no Monitor Azure para cada um. |

### <a name="log-query"></a>Consulta de log
Os grupos informáticos criados a partir de uma consulta de registo contêm todos os computadores devolvidos por uma consulta que define.  Esta consulta é executada sempre que o grupo de computadores é utilizado para que quaisquer alterações, uma vez que o grupo foi criado é refletida.  

Pode utilizar qualquer consulta para um grupo de computador, mas deve devolver um conjunto distinto de computadores utilizando `distinct Computer`.  Segue-se uma consulta típica de exemplo que pode utilizar como um grupo de computador.

    Heartbeat | where Computer contains "srv" | distinct Computer

Utilize o procedimento seguinte para criar um grupo de computadores a partir de uma pesquisa de registos no portal do Azure.

1. Clique em **Registos** no menu **Do Monitor Azure** no portal Azure.
1. Crie e faça uma consulta que dereque os computadores que deseja no grupo.
1. Clique em **Guardar** na parte superior do ecrã.
1. Altere **Guardar as funções** e selecione Guardar esta consulta como um grupo de **computador**.
1. Forneça valores para cada propriedade para o grupo de computador descrito na tabela e clique em **Guardar**.

A tabela seguinte descreve as propriedades que definem um grupo de computadores.

| Propriedade | Descrição |
|:---|:---|
| Nome   | Nome da consulta para exibir no portal. |
| Alias de função | Um alias de exclusivo utilizado para identificar o grupo de computadores numa consulta. |
| Categoria       | Categoria para organizar as consultas no portal. |


### <a name="active-directory"></a>Active Directory
Quando configura o Monitor Azure para importar membros do grupo Ative Directory, analisa a adesão do grupo a qualquer domínio Windows que se juntou a computadores com o agente Log Analytics.  Um grupo informático é criado no Monitor Azure para cada grupo de segurança no Ative Directory, e cada computador Windows é adicionado aos grupos informáticos correspondentes aos grupos de segurança de que são membros.  Esta associação é constantemente atualizada a cada 4 horas.  

> [!NOTE]
> Os grupos de Diretórios Ativos importados contêm apenas máquinas Windows.

Configura o Azure Monitor para importar grupos de segurança de Diretório Ativo a partir de **configurações avançadas** no seu espaço de trabalho Log Analytics no portal Azure.  Selecione **Grupos informáticos,** **Diretório Ativo,** e, em seguida, **importe membros do grupo De Diretório Ativo a partir de computadores**.  Não há nenhuma configuração adicional.

![Grupos de computadores do Active Directory](media/computer-groups/configure-activedirectory.png)

Quando os grupos foram importados, o menu de lista o número de computadores com a associação a grupos detetados e o número de grupos importados.  Pode clicar em qualquer uma destas ligações para devolver os registos **do ComputerGroup** com esta informação.

### <a name="windows-server-update-service"></a>Serviço de Atualização do Windows Server
Quando configura o Monitor Azure para importar membros do grupo WSUS, analisa a associação de grupos direcionados de quaisquer computadores com o agente Log Analytics.  Se estiver a utilizar o alvo do lado do cliente, qualquer computador ligado ao Monitor Azure e que faça parte de quaisquer grupos de alvo wSUS tem a sua filiação no grupo importada para o Monitor Azure. Se estiver a utilizar o alvo do lado do servidor, o agente Log Analytics deve ser instalado no servidor WSUS para que as informações de membro do grupo sejam importadas para o Monitor Azure.  Esta associação é constantemente atualizada a cada 4 horas. 

Configura o Monitor Azure para importar grupos WSUS a partir de **configurações avançadas** no seu espaço de trabalho Log Analytics no portal Azure.  Selecione **Grupos informáticos,** **WSUS,** e, em seguida, **importe membros do grupo WSUS**.  Não há nenhuma configuração adicional.

![Grupos de computadores do WSUS](media/computer-groups/configure-wsus.png)

Quando os grupos foram importados, o menu de lista o número de computadores com a associação a grupos detetados e o número de grupos importados.  Pode clicar em qualquer uma destas ligações para devolver os registos **do ComputerGroup** com esta informação.

### <a name="configuration-manager"></a>Configuration Manager
Quando configura o Monitor Azure para importar membros de recolha do Gestor de Configuração, cria um grupo informático para cada coleção.  As informações de associação de coleção são recuperadas em três horas a manter os grupos de computadores atualizados. 

Antes de importar coleções do Gestor de Configuração, deve ligar o Gestor de [Configuração ao Monitor Azure](collect-sccm.md).  

![Grupos de computadores do SCCM](media/computer-groups/configure-sccm.png)

Quando tem sido importadas coleções, o menu de lista o número de computadores com a associação a grupos detetados e o número de grupos importados.  Pode clicar em qualquer uma destas ligações para devolver os registos **do ComputerGroup** com esta informação.

## <a name="managing-computer-groups"></a>Gerir grupos de computadores
Pode ver grupos de computador que foram criados a partir de uma consulta de log ou da API de Pesquisa de Registo a partir de **configurações avançadas** no seu espaço de trabalho Log Analytics no portal Azure.  Selecione **Grupos de Computador** e, em seguida, **Grupos Guardados**.  

Clique no **x** na coluna **Remover** para eliminar o grupo de computador.  Clique no ícone dos **membros do View** para um grupo para executar a pesquisa de registo do grupo que devolve os seus membros.  Não é possível modificar um grupo de computadores, mas em vez disso, deve eliminar e, em seguida, recrie-o com as definições modificadas.

![Grupos de computadores guardados](media/computer-groups/configure-saved.png)


## <a name="using-a-computer-group-in-a-log-query"></a>Usando um grupo de computador em uma consulta de log
Você usa um grupo de computador criado a partir de uma consulta de log em uma consulta, tratando o seu pseudónimo como uma função, tipicamente com a seguinte sintaxe:

  `Table | where Computer in (ComputerGroup)`

Por exemplo, poderia usar o seguinte para devolver registos UpdateSummary para apenas os computadores num grupo de computadores chamado mycomputergroup.
 
  `UpdateSummary | where Computer in (mycomputergroup)`


Os grupos informáticos importados e os seus computadores incluídos são armazenados na tabela **ComputerGroup.**  Por exemplo, a seguinte consulta retornaria uma lista de computadores no grupo de computadores de domínio do Active Directory. 

  `ComputerGroup | where GroupSource == "ActiveDirectory" and Group == "Domain Computers" | distinct Computer`

A seguinte consulta retornaria registos UpdateSummary para apenas os computadores em computadores de domínio.

  ```
  let ADComputers = ComputerGroup | where GroupSource == "ActiveDirectory" and Group == "Domain Computers" | distinct Computer;
  UpdateSummary | where Computer in (ADComputers)
  ```




## <a name="computer-group-records"></a>Registos do grupo de computadores
Na área de trabalho do Log Analytics para cada associação de grupo do computador criada a partir do Active Directory ou WSUS, é criado um registo.  Estes registos têm um tipo de **ComputerGroup** e têm as propriedades na tabela seguinte.  Os registos não são criados para grupos informáticos com base em consultas de registo.

| Propriedade | Descrição |
|:--- |:--- |
| `Type` |*Grupo informático* |
| `SourceSystem` |*Sistema fonte* |
| `Computer` |Nome do computador membro. |
| `Group` |Nome do grupo. |
| `GroupFullName` |Caminho completo para o grupo, incluindo a origem e o nome de origem. |
| `GroupSource` |Esse grupo de origem foi recolhido a partir de. <br><br>ActiveDirectory<br>WSUS<br>WSUSClientTargeting |
| `GroupSourceName` |Nome da origem de que o grupo foi recolhido a partir de.  Para o Active Directory, este é o nome de domínio. |
| `ManagementGroupName` |O nome do grupo de gestão para agentes do SCOM.  Para outros agentes, trata-se de\> de identificação do espaço de trabalho AOI-\< |
| `TimeGenerated` |Data e hora, o grupo de computadores foi criado ou atualizado. |

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre consultas de [registo](../log-query/log-query-overview.md) para analisar os dados recolhidos a partir de fontes e soluções de dados.  

