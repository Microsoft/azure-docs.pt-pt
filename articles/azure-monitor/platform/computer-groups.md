---
title: Grupos de computadores em consultas Azure Monitor log | Microsoft Docs
description: Os grupos de computadores no Azure Monitor permitem que você defina o escopo de consultas de log para um determinado conjunto de computadores.  Este artigo descreve os diferentes métodos que você pode usar para criar grupos de computadores e como usá-los em uma consulta de log.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/05/2019
ms.openlocfilehash: eedf04a2168c67449f97d8e462d4ff82653a22b3
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76513711"
---
# <a name="computer-groups-in-azure-monitor-log-queries"></a>Grupos de computadores em consultas Azure Monitor log
Os grupos de computadores no Azure Monitor permitem que você defina o escopo de [consultas de log](../log-query/log-query-overview.md) para um determinado conjunto de computadores.  Cada grupo é preenchido com computadores por meio de uma consulta por si ou ao importar grupos de diferentes origens.  Quando o grupo é incluído em uma consulta de log, os resultados são limitados aos registros que correspondem aos computadores do grupo.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="creating-a-computer-group"></a>Criar um grupo de computadores
Você pode criar um grupo de computadores em Azure Monitor usando qualquer um dos métodos na tabela a seguir.  Obter detalhes sobre cada método são fornecidos nas secções abaixo. 

| Método | Descrição |
|:--- |:--- |
| Consulta de log |Crie uma consulta de log que retorne uma lista de computadores. |
| API de Pesquisas de Registos |Use a API de pesquisa de log para criar programaticamente um grupo de computadores com base nos resultados de uma consulta de log. |
| Active Directory |Digitalize automaticamente a associação de grupo de qualquer computador agente que seja membro de um Active Directory domínio e crie um grupo no Azure Monitor para cada grupo de segurança. (Somente computadores Windows)|
| Configuration Manager | Importe coleções do Microsoft Endpoint Configuration Manager e crie um grupo no Azure Monitor para cada uma. |
| Windows Server Update Services |Examine automaticamente os servidores ou clientes do WSUS para direcionar grupos e criar um grupo em Azure Monitor para cada um. |

### <a name="log-query"></a>Consulta de log
Os grupos de computadores criados a partir de uma consulta de log contêm todos os computadores retornados por uma consulta que você define.  Esta consulta é executada sempre que o grupo de computadores é utilizado para que quaisquer alterações, uma vez que o grupo foi criado é refletida.  

Pode usar qualquer consulta para um grupo de computadores, mas tem de devolver um conjunto diferente de computadores utilizando `distinct Computer`.  Veja a seguir uma consulta de exemplo típica que você pode usar para o como um grupo de computadores.

    Heartbeat | where Computer contains "srv" | distinct Computer

Utilize o procedimento seguinte para criar um grupo de computadores a partir de uma pesquisa de registos no portal do Azure.

1. Clique em **logs** no menu **Azure monitor** na portal do Azure.
1. Crie e execute uma consulta que retorne os computadores que você deseja no grupo.
1. Clique em **salvar** na parte superior da tela.
1. Altere **salvar como** para **função** e selecione **salvar esta consulta como um grupo de computadores**.
1. Forneça valores para cada propriedade para o grupo de computadores descrito na tabela e clique em **salvar**.

A tabela seguinte descreve as propriedades que definem um grupo de computadores.

| Propriedade | Descrição |
|:---|:---|
| Nome   | Nome da consulta a ser exibida no Portal. |
| Alias de função | Um alias de exclusivo utilizado para identificar o grupo de computadores numa consulta. |
| Categoria       | Categoria para organizar as consultas no Portal. |


### <a name="active-directory"></a>Active Directory
Quando você configura Azure Monitor para importar Active Directory associações de grupo, ele analisa a associação de grupo de qualquer computador ingressado no domínio do Windows com o agente de Log Analytics.  Um grupo de computadores é criado no Azure Monitor para cada grupo de segurança no Active Directory, e cada computador com Windows é adicionado aos grupos de computadores correspondentes aos grupos de segurança dos quais eles são membros.  Esta associação é constantemente atualizada a cada 4 horas.  

> [!NOTE]
> Grupos de Active Directory importados contêm apenas computadores Windows.

Você configura Azure Monitor para importar Active Directory grupos de segurança das **Configurações avançadas** no espaço de trabalho Log Analytics no portal do Azure.  Selecione **grupos de computadores**, **do Active Directory**e, em seguida **associações a grupos de computadores do Active Directory de importação**.  Não há nenhuma configuração adicional.

![Grupos de computadores do Active Directory](media/computer-groups/configure-activedirectory.png)

Quando os grupos foram importados, o menu de lista o número de computadores com a associação a grupos detetados e o número de grupos importados.  Pode clicar em qualquer uma destas ligações para retornar a **grupo de computador** registos com essas informações.

### <a name="windows-server-update-service"></a>Serviço de Atualização do Windows Server
Quando você configura Azure Monitor para importar associações de grupo do WSUS, ele analisa a associação de grupo de destino de todos os computadores com o agente de Log Analytics.  Se você estiver usando o direcionamento do lado do cliente, qualquer computador que esteja conectado a Azure Monitor e fizer parte de qualquer grupo de direcionamento do WSUS terá sua associação de grupos importada para Azure Monitor. Se você estiver usando o direcionamento do lado do servidor, o agente de Log Analytics deverá ser instalado no servidor do WSUS para que as informações de associação de grupo sejam importadas para Azure Monitor.  Esta associação é constantemente atualizada a cada 4 horas. 

Você configura Azure Monitor para importar grupos do WSUS de **Configurações avançadas** em seu espaço de trabalho do Log Analytics no portal do Azure.  Selecione **grupos de computadores**, **WSUS**e, em seguida **associações de grupo do WSUS de importação**.  Não há nenhuma configuração adicional.

![Grupos de computadores do WSUS](media/computer-groups/configure-wsus.png)

Quando os grupos foram importados, o menu de lista o número de computadores com a associação a grupos detetados e o número de grupos importados.  Pode clicar em qualquer uma destas ligações para retornar a **grupo de computador** registos com essas informações.

### <a name="configuration-manager"></a>Configuration Manager
Quando você configura Azure Monitor para importar Configuration Manager associações de coleção, ele cria um grupo de computadores para cada coleção.  As informações de associação de coleção são recuperadas em três horas a manter os grupos de computadores atualizados. 

Antes de importar Configuration Manager coleções, você deve [conectar Configuration Manager ao Azure monitor](collect-sccm.md).  

![Grupos de computadores do SCCM](media/computer-groups/configure-sccm.png)

Quando tem sido importadas coleções, o menu de lista o número de computadores com a associação a grupos detetados e o número de grupos importados.  Pode clicar em qualquer uma destas ligações para retornar a **grupo de computador** registos com essas informações.

## <a name="managing-computer-groups"></a>Gerir grupos de computadores
Você pode exibir grupos de computadores que foram criados a partir de uma consulta de log ou da API de pesquisa de logs nas **Configurações avançadas** em seu espaço de trabalho do Log Analytics na portal do Azure.  Selecione **grupos de computadores** e, em seguida **guardado grupos**.  

Clique nas **x** no **remover** coluna para eliminar o grupo de computadores.  Clique nas **ver membros** ícone para um grupo para executar a pesquisa de registos do grupo, que retorna seus membros.  Não é possível modificar um grupo de computadores, mas em vez disso, deve eliminar e, em seguida, recrie-o com as definições modificadas.

![Grupos de computadores guardados](media/computer-groups/configure-saved.png)


## <a name="using-a-computer-group-in-a-log-query"></a>Usando um grupo de computadores em uma consulta de log
Você usa um grupo de computadores criado a partir de uma consulta de log em uma consulta tratando seu alias como uma função, normalmente com a seguinte sintaxe:

  `Table | where Computer in (ComputerGroup)`

Por exemplo, poderia usar o seguinte para devolver registos UpdateSummary para apenas os computadores num grupo de computadores chamado mycomputergroup.
 
  `UpdateSummary | where Computer in (mycomputergroup)`


Grupos de computadores importados e os respetivos computadores incluídos são armazenadas no **grupo de computador** tabela.  Por exemplo, a seguinte consulta retornaria uma lista de computadores no grupo de computadores de domínio do Active Directory. 

  `ComputerGroup | where GroupSource == "ActiveDirectory" and Group == "Domain Computers" | distinct Computer`

A seguinte consulta retornaria registos UpdateSummary para apenas os computadores em computadores de domínio.

  ```
  let ADComputers = ComputerGroup | where GroupSource == "ActiveDirectory" and Group == "Domain Computers" | distinct Computer;
  UpdateSummary | where Computer in (ADComputers)
  ```




## <a name="computer-group-records"></a>Registos do grupo de computadores
Na área de trabalho do Log Analytics para cada associação de grupo do computador criada a partir do Active Directory ou WSUS, é criado um registo.  Estes registos têm um tipo de **grupo de computador** e ter as propriedades na tabela seguinte.  Os registros não são criados para grupos de computadores com base em consultas de log.

| Propriedade | Descrição |
|:--- |:--- |
| `Type` |*ComputerGroup* |
| `SourceSystem` |*SourceSystem* |
| `Computer` |Nome do computador membro. |
| `Group` |Nome do grupo. |
| `GroupFullName` |Caminho completo para o grupo, incluindo a origem e o nome de origem. |
| `GroupSource` |Esse grupo de origem foi recolhido a partir de. <br><br>ActiveDirectory<br>WSUS<br>WSUSClientTargeting |
| `GroupSourceName` |Nome da origem de que o grupo foi recolhido a partir de.  Para o Active Directory, este é o nome de domínio. |
| `ManagementGroupName` |O nome do grupo de gestão para agentes do SCOM.  Para outros agentes, é AOI -\<ID da área de trabalho\> |
| `TimeGenerated` |Data e hora, o grupo de computadores foi criado ou atualizado. |

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [registar as consultas](../log-query/log-query-overview.md) para analisar os dados recolhidos a partir de origens de dados e soluções.  

