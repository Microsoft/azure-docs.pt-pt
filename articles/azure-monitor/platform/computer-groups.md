---
title: Consultas de registo de grupos de computadores no Azure Monitor | Documentos da Microsoft
description: Grupos de computadores no Azure Monitor permitem consultas de âmbito de registo para um determinado conjunto de computadores.  Este artigo descreve os diferentes métodos que pode utilizar para criar grupos de computadores e como utilizá-los numa consulta de registo.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: a28b9e8a-6761-4ead-aa61-c8451ca90125
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/05/2019
ms.author: bwren
ms.openlocfilehash: 0a29e453c723ecc9ac378ee337365525587aaef2
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57444128"
---
# <a name="computer-groups-in-azure-monitor-log-queries"></a>Grupos de computadores em consultas de registo do Azure Monitor
Grupos de computador no Monitor do Azure permitem-lhe âmbito [registar as consultas](../log-query/log-query-overview.md) para um determinado conjunto de computadores.  Cada grupo é preenchido com computadores por meio de uma consulta por si ou ao importar grupos de diferentes origens.  Quando o grupo está incluído numa consulta de registo, os resultados estão limitados aos registos que correspondem a computadores no grupo.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="creating-a-computer-group"></a>Criar um grupo de computadores
Pode criar um grupo de computadores no Azure Monitor, utilizando qualquer um dos métodos a tabela seguinte.  Obter detalhes sobre cada método são fornecidos nas secções abaixo. 

| Método | Descrição |
|:--- |:--- |
| Consulta de registo |Crie uma consulta de registo que retorna uma lista de computadores. |
| API de Pesquisas de Registos |Utilize a API de pesquisa de registo para criar programaticamente um grupo de computadores com base nos resultados de uma consulta de registo. |
| Active Directory |Analise automaticamente a associação do grupo de todos os computadores agente que são membros de um domínio do Active Directory e criar um grupo no Azure Monitor para cada grupo de segurança. (Apenas para máquinas do Windows)|
| Configuration Manager | Importar coleções a partir do System Center Configuration Manager e crie um grupo no Azure Monitor para cada um. |
| Windows Server Update Services |Procurar o filtragem de grupos de servidores WSUS ou de clientes e criar um grupo no Azure Monitor para cada automaticamente. |

### <a name="log-query"></a>Consulta de registo
Grupos de computadores criados a partir de uma consulta de registo contém todos os computadores devolvidos por uma consulta que definir.  Esta consulta é executada sempre que o grupo de computadores é utilizado para que quaisquer alterações, uma vez que o grupo foi criado é refletida.  

Pode usar qualquer consulta para um grupo de computadores, mas tem de devolver um conjunto diferente de computadores utilizando `distinct Computer`.  Segue-se uma consulta de exemplo típico que poderia usar para como um grupo de computadores.

    Heartbeat | where Computer contains "srv" | distinct Computer

Utilize o procedimento seguinte para criar um grupo de computadores a partir de uma pesquisa de registos no portal do Azure.

1. Clique em **Logs** no **Azure Monitor** menu no portal do Azure.
1. Criar e executar uma consulta que devolve os computadores que pretende no grupo.
1. Clique em **guardar** na parte superior do ecrã.
1. Alteração **guardar como** ao **função** e selecione **guardar esta consulta como grupo de computadores**.
1. Fornecer valores para cada propriedade para o grupo de computadores descrito na tabela e clique em **guardar**.

A tabela seguinte descreve as propriedades que definem um grupo de computadores.

| Propriedade | Descrição |
|:---|:---|
| Name   | Nome da consulta para apresentar no portal. |
| Alias de função | Um alias de exclusivo utilizado para identificar o grupo de computadores numa consulta. |
| Categoria       | Categoria para organizar consultas no portal. |


### <a name="active-directory"></a>Active Directory
Quando configurar o Azure Monitor para importar associações a grupos do Active Directory, analisa a associação de grupo dos computadores associados a um domínio Windows com o agente do Log Analytics.  Um grupo de computadores é criado no Azure Monitor para cada grupo de segurança no Active Directory, e cada computador Windows é adicionado para os grupos de computadores correspondentes aos grupos de segurança que são membros.  Esta associação é constantemente atualizada a cada 4 horas.  

> [!NOTE]
> Grupos importados do Active Directory contêm apenas as máquinas do Windows.

Configurar o Azure Monitor para importar grupos de segurança do Active Directory da **definições avançadas** na sua área de trabalho do Log Analytics no portal do Azure.  Selecione **grupos de computadores**, **do Active Directory**e, em seguida **associações a grupos de computadores do Active Directory de importação**.  Não há nenhuma configuração adicional.

![Grupos de computadores do Active Directory](media/computer-groups/configure-activedirectory.png)

Quando os grupos foram importados, o menu de lista o número de computadores com a associação a grupos detetados e o número de grupos importados.  Pode clicar em qualquer uma destas ligações para retornar a **grupo de computador** registos com essas informações.

### <a name="windows-server-update-service"></a>Serviço de Atualização do Windows Server
Quando configurar o Azure Monitor para importar associações a grupos WSUS, analisa a associação ao grupo de direcionamento de todos os computadores com o agente do Log Analytics.  Se estiver a utilizar o lado do cliente como o objetivo, qualquer computador que está ligada ao Azure Monitor e faz parte de qualquer WSUS filtragem de grupos tem respetiva associação a grupos importada para o Azure Monitor. Se estiver a utilizar o servidor como o objetivo, o Log Analytics agent deve ser instalado no servidor WSUS para que as informações de associação de grupo ser importado para o Azure Monitor.  Esta associação é constantemente atualizada a cada 4 horas. 

Configurar o Azure Monitor para importar grupos de WSUS a partir **definições avançadas** na sua área de trabalho do Log Analytics no portal do Azure.  Selecione **grupos de computadores**, **WSUS**e, em seguida **associações de grupo do WSUS de importação**.  Não há nenhuma configuração adicional.

![Grupos de computadores do WSUS](media/computer-groups/configure-wsus.png)

Quando os grupos foram importados, o menu de lista o número de computadores com a associação a grupos detetados e o número de grupos importados.  Pode clicar em qualquer uma destas ligações para retornar a **grupo de computador** registos com essas informações.

### <a name="system-center-configuration-manager"></a>System Center Configuration Manager
Quando configurar o Azure Monitor para importar subscrições da coleção do Configuration Manager, ele cria um grupo de computadores para cada coleção.  As informações de associação de coleção são recuperadas em três horas a manter os grupos de computadores atualizados. 

Antes de pode importar coleções do Configuration Manager, tem [ligar o Configuration Manager para o Azure Monitor](collect-sccm.md).  Em seguida, pode configurar a importação de **definições avançadas** na sua área de trabalho do Log Analytics no portal do Azure.  Selecione **grupos de computadores**, **SCCM**e, em seguida **associações de coleção do Gestor de configuração da importação**.  Não há nenhuma configuração adicional.

![Grupos de computadores do SCCM](media/computer-groups/configure-sccm.png)

Quando tem sido importadas coleções, o menu de lista o número de computadores com a associação a grupos detetados e o número de grupos importados.  Pode clicar em qualquer uma destas ligações para retornar a **grupo de computador** registos com essas informações.

## <a name="managing-computer-groups"></a>Gerir grupos de computadores
Pode ver grupos de computadores que foram criados a partir de uma consulta de registo ou a API de pesquisa de registo do **definições avançadas** na sua área de trabalho do Log Analytics no portal do Azure.  Selecione **grupos de computadores** e, em seguida **guardado grupos**.  

Clique nas **x** no **remover** coluna para eliminar o grupo de computadores.  Clique nas **ver membros** ícone para um grupo para executar a pesquisa de registos do grupo, que retorna seus membros.  Não é possível modificar um grupo de computadores, mas em vez disso, deve eliminar e, em seguida, recrie-o com as definições modificadas.

![Grupos de computadores guardados](media/computer-groups/configure-saved.png)


## <a name="using-a-computer-group-in-a-log-query"></a>Utilizar um grupo de computadores numa consulta de registo
Utilize um grupo de computadores criado a partir de uma consulta de log numa consulta ao tratar o respetivo alias como uma função, normalmente, com a seguinte sintaxe:

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
Na área de trabalho do Log Analytics para cada associação de grupo do computador criada a partir do Active Directory ou WSUS, é criado um registo.  Estes registos têm um tipo de **grupo de computador** e ter as propriedades na tabela seguinte.  Registos não são criados para grupos de computadores com base em consultas de registo.

| Propriedade | Descrição |
|:--- |:--- |
| Tipo |*ComputerGroup* |
| SourceSystem |*SourceSystem* |
| Computador |Nome do computador membro. |
| Grupo |Nome do grupo. |
| GroupFullName |Caminho completo para o grupo, incluindo a origem e o nome de origem. |
| GroupSource |Esse grupo de origem foi recolhido a partir de. <br><br>ActiveDirectory<br>WSUS<br>WSUSClientTargeting |
| GroupSourceName |Nome da origem de que o grupo foi recolhido a partir de.  Para o Active Directory, este é o nome de domínio. |
| ManagementGroupName |O nome do grupo de gestão para agentes do SCOM.  Para outros agentes, é AOI -\<ID da área de trabalho\> |
| TimeGenerated |Data e hora, o grupo de computadores foi criado ou atualizado. |

## <a name="next-steps"></a>Passos Seguintes
* Saiba mais sobre [registar as consultas](../log-query/log-query-overview.md) para analisar os dados recolhidos a partir de origens de dados e soluções.  

