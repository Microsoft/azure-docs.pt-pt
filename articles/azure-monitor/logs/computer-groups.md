---
title: Grupos de computador em Azure Monitor consultas de registo | Microsoft Docs
description: Os grupos informáticos no Azure Monitor permitem-lhe fazer consultas de registo a um determinado conjunto de computadores.  Este artigo descreve os diferentes métodos que pode usar para criar grupos de computador e como usá-los numa consulta de registo.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/05/2019
ms.openlocfilehash: 92603165ac399415ec4fb6daeea1641065671a83
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100618432"
---
# <a name="computer-groups-in-azure-monitor-log-queries"></a>Grupos de computador em consultas de registo do Monitor Azure
Os grupos informáticos no Azure Monitor permitem-lhe fazer [consultas de registo](../log-query/log-query-overview.md) a um determinado conjunto de computadores.  Cada grupo é preenchido com computadores, quer utilizando uma consulta que define ou importando grupos de diferentes fontes.  Quando o grupo é incluído numa consulta de registo, os resultados limitam-se a registos que correspondem aos computadores do grupo.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="creating-a-computer-group"></a>Criar um grupo de computadores
Pode criar um grupo de computador no Azure Monitor utilizando qualquer um dos métodos na tabela seguinte.  Os detalhes de cada método são fornecidos nas secções abaixo. 

| Método | Descrição |
|:--- |:--- |
| Consulta de registo |Crie uma consulta de registo que devolva uma lista de computadores. |
| API de Pesquisas de Registos |Utilize a API de Pesquisa de Registo para criar programaticamente um grupo de computador com base nos resultados de uma consulta de registo. |
| Active Directory |Digitalize automaticamente a filiação do grupo de quaisquer computadores de agente que sejam membros de um domínio ative directory e crie um grupo no Azure Monitor para cada grupo de segurança. (Apenas máquinas windows)|
| Configuration Manager | Coleções de importação do Microsoft Endpoint Configuration Manager e criar um grupo no Azure Monitor para cada um. |
| Windows Server Update Services |Digitalize automaticamente servidores ou clientes WSUS para direcionar grupos e criar um grupo no Azure Monitor para cada um. |

### <a name="log-query"></a>Consulta de registo
Os grupos informáticos criados a partir de uma consulta de registo contêm todos os computadores devolvidos por uma consulta que define.  Esta consulta é executada sempre que o grupo de computador é utilizado para que quaisquer alterações desde que o grupo foi criado seja refletida.  

Pode utilizar qualquer consulta para um grupo de computadores, mas deve devolver um conjunto distinto de computadores utilizando `distinct Computer` .  Segue-se uma consulta de exemplo típica que pode ser utilizada como um grupo de computador.

```kusto
Heartbeat | where Computer contains "srv" | distinct Computer
```

Utilize o seguinte procedimento para criar um grupo de computador a partir de uma pesquisa de registo no portal Azure.

1. Clique em **Registos** no menu **Azure Monitor** no portal Azure.
1. Crie e execute uma consulta que devolva os computadores que deseja no grupo.
1. Clique em **Guardar** na parte superior do ecrã.
1. Alterar **Guardar quanto** à **Função** e selecionar **Guarde esta consulta como um grupo de computador**.
1. Forneça valores para cada propriedade para o grupo de computador descrito na tabela e clique em **Guardar**.

A tabela seguinte descreve as propriedades que definem um grupo de computador.

| Propriedade | Descrição |
|:---|:---|
| Nome   | Nome da consulta a exibir no portal. |
| Pseudónimo de função | Um pseudónimo único usado para identificar o grupo de computador numa consulta. |
| Categoria       | Categoria para organizar as consultas no portal. |


### <a name="active-directory"></a>Active Directory
Ao configurar o Azure Monitor para importar membros do grupo Ative Directory, analisa a adesão do grupo de qualquer domínio Windows a computadores ligados ao agente Log Analytics.  Um grupo de computador é criado no Azure Monitor para cada grupo de segurança no Ative Directory, e cada computador Windows é adicionado aos grupos informáticos correspondentes aos grupos de segurança de que são membros.  Esta adesão é continuamente atualizada a cada 4 horas.  

> [!NOTE]
> Os grupos de Diretório Ativo Importados contêm apenas máquinas Windows.

Configura o Azure Monitor para importar grupos de segurança ative directory a partir de **configurações avançadas** no seu espaço de trabalho Log Analytics no portal Azure.  Selecione **grupos informáticos,** **Ative Directory** e, em seguida, **Import Ative Directory memberships from computers**.  Não há nenhuma configuração adicional.

![Grupos informáticos do Ative Directory](media/computer-groups/configure-activedirectory.png)

Quando os grupos foram importados, o menu lista o número de computadores com membros do grupo detetados e o número de grupos importados.  Pode clicar em qualquer um destes links para devolver os registos do **ComputerGroup** com esta informação.

### <a name="windows-server-update-service"></a>Serviço de atualização do servidor do Windows
Ao configurar o Azure Monitor para importar membros do grupo WSUS, ele analisa a adesão ao grupo de alvos de qualquer computador com o agente Log Analytics.  Se estiver a utilizar o alvo do lado do cliente, qualquer computador que esteja ligado ao Azure Monitor e faça parte de qualquer grupo de alvos da WSUS tem a sua filiação do grupo importada para o Azure Monitor. Se estiver a utilizar o alvo do lado do servidor, o agente Log Analytics deve ser instalado no servidor WSUS para que as informações de membro do grupo sejam importadas para o Azure Monitor.  Esta adesão é continuamente atualizada a cada 4 horas. 

Configura o Azure Monitor para importar grupos WSUS de **configurações avançadas** no seu espaço de trabalho Log Analytics no portal Azure.  Selecione **grupos informáticos,** **WSUS,** e, em seguida, **importe membros do grupo WSUS**.  Não há nenhuma configuração adicional.

![Grupos informáticos da WSUS](media/computer-groups/configure-wsus.png)

Quando os grupos foram importados, o menu lista o número de computadores com membros do grupo detetados e o número de grupos importados.  Pode clicar em qualquer um destes links para devolver os registos do **ComputerGroup** com esta informação.

### <a name="configuration-manager"></a>Configuration Manager
Ao configurar o Azure Monitor para importar membros de recolha do Gestor de Configuração, cria um grupo de computadores para cada coleção.  A informação de membro da recolha é recuperada a cada 3 horas para manter os grupos de computador em funcionamento. 

Antes de poder importar coleções de Gestor de Configuração, tem de [ligar o Gestor de Configurações ao Azure Monitor.](collect-sccm.md)  

![Grupos informáticos da SCCM](media/computer-groups/configure-sccm.png)

Quando as coleções são importadas, o menu lista o número de computadores com membros do grupo detetados e o número de grupos importados.  Pode clicar em qualquer um destes links para devolver os registos do **ComputerGroup** com esta informação.

## <a name="managing-computer-groups"></a>Gestão de grupos informáticos
Pode ver grupos de computador que foram criados a partir de uma consulta de registo ou da API de Pesquisa de Registo a partir de **definições Avançadas** no seu espaço de trabalho Log Analytics no portal Azure.  Selecione **grupos de computador** e, em seguida, **grupos guardados**.  

Clique no **x** na coluna **Remover** para eliminar o grupo de computador.  Clique no ícone **'Ver' para** um grupo para executar a pesquisa de registo do grupo que devolve os seus membros.  Não é possível modificar um grupo de computador, mas deve, em vez disso, eliminá-lo e recriá-lo com as definições modificadas.

![Grupos de computadores salvos](media/computer-groups/configure-saved.png)


## <a name="using-a-computer-group-in-a-log-query"></a>Usando um grupo de computador em uma consulta de log
Utiliza um grupo de Computador criado a partir de uma consulta de log numa consulta, tratando o seu pseudónimo como uma função, tipicamente com a seguinte sintaxe:

```kusto
Table | where Computer in (ComputerGroup)
```

Por exemplo, pode utilizar o seguinte para devolver registos UpdateSummary apenas para computadores num grupo de computador chamado mycomputatergroup.

```kusto
UpdateSummary | where Computer in (mycomputergroup)
```

Os grupos informáticos importados e os seus computadores incluídos são armazenados na tabela **Do Grupo de Computadores.**  Por exemplo, a seguinte consulta devolveria uma lista de computadores no grupo De computadores de domínio do Ative Directory. 

```kusto
ComputerGroup | where GroupSource == "ActiveDirectory" and Group == "Domain Computers" | distinct Computer
```

A seguinte consulta devolveria registos UpdateSummary apenas para computadores em Computadores de Domínio.

```kusto
let ADComputers = ComputerGroup | where GroupSource == "ActiveDirectory" and Group == "Domain Computers" | distinct Computer;
  UpdateSummary | where Computer in (ADComputers)
```

## <a name="computer-group-records"></a>Registos de grupos informáticos
Um registo é criado no espaço de trabalho Log Analytics para cada membro do grupo de computador criado a partir do Ative Directory ou WSUS.  Estes registos têm um tipo de Grupo de **Computador** e têm as propriedades na tabela seguinte.  Os registos não são criados para grupos de computador com base em consultas de registo.

| Propriedade | Descrição |
|:--- |:--- |
| `Type` |*Grupo de Computadores* |
| `SourceSystem` |*SourceSystem* |
| `Computer` |Nome do computador dos membros. |
| `Group` |Nome do grupo. |
| `GroupFullName` |Caminho completo para o grupo, incluindo a fonte e o nome de origem. |
| `GroupSource` |Fonte de que o grupo foi recolhido. <br><br>ActiveDirectory<br>WSUS<br>WSUSClientTargeting |
| `GroupSourceName` |O nome da fonte de onde o grupo foi recolhido.  Para o Ative Directory, este é o nome de domínio. |
| `ManagementGroupName` |O nome do grupo de gestão para agentes do SCOM.  Para outros agentes, este é o AOI...\<workspace ID\> |
| `TimeGenerated` |Data e hora em que o grupo informático foi criado ou atualizado. |

## <a name="next-steps"></a>Passos seguintes
* Saiba mais [sobre consultas de registo](../log-query/log-query-overview.md) para analisar os dados recolhidos a partir de fontes de dados e soluções.  

