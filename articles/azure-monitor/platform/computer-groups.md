---
title: Grupos de computadores em consultas Azure Monitor log | Microsoft Docs
description: Os grupos de computadores no Azure Monitor permitem que você defina o escopo de consultas de log para um determinado conjunto de computadores.  Este artigo descreve os diferentes métodos que você pode usar para criar grupos de computadores e como usá-los em uma consulta de log.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/05/2019
ms.openlocfilehash: 9ef0f2810252b73921fc0a72f2e523262c760bab
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932663"
---
# <a name="computer-groups-in-azure-monitor-log-queries"></a>Grupos de computadores em consultas Azure Monitor log
Os grupos de computadores no Azure Monitor permitem que você defina o escopo de [consultas de log](../log-query/log-query-overview.md) para um determinado conjunto de computadores.  Cada grupo é preenchido com computadores usando uma consulta que você define ou importando grupos de fontes diferentes.  Quando o grupo é incluído em uma consulta de log, os resultados são limitados aos registros que correspondem aos computadores do grupo.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="creating-a-computer-group"></a>Criando um grupo de computadores
Você pode criar um grupo de computadores em Azure Monitor usando qualquer um dos métodos na tabela a seguir.  Detalhes sobre cada método são fornecidos nas seções a seguir. 

| Método | Descrição |
|:--- |:--- |
| Consulta de log |Crie uma consulta de log que retorne uma lista de computadores. |
| API de Pesquisas de Registos |Use a API de pesquisa de log para criar programaticamente um grupo de computadores com base nos resultados de uma consulta de log. |
| Active Directory |Digitalize automaticamente a associação de grupo de qualquer computador agente que seja membro de um Active Directory domínio e crie um grupo no Azure Monitor para cada grupo de segurança. (Somente computadores Windows)|
| Configuration Manager | Importe coleções de System Center Configuration Manager e crie um grupo no Azure Monitor para cada uma. |
| Windows Server Update Services |Examine automaticamente os servidores ou clientes do WSUS para direcionar grupos e criar um grupo em Azure Monitor para cada um. |

### <a name="log-query"></a>Consulta de log
Os grupos de computadores criados a partir de uma consulta de log contêm todos os computadores retornados por uma consulta que você define.  Essa consulta é executada sempre que o grupo de computadores é usado para que as alterações feitas desde a criação do grupo sejam refletidas.  

Você pode usar qualquer consulta para um grupo de computadores, mas ele deve retornar um conjunto distinto de computadores usando `distinct Computer`.  Veja a seguir uma consulta de exemplo típica que você pode usar para o como um grupo de computadores.

    Heartbeat | where Computer contains "srv" | distinct Computer

Use o procedimento a seguir para criar um grupo de computadores de uma pesquisa de logs no portal do Azure.

1. Clique em **logs** no menu **Azure monitor** na portal do Azure.
1. Crie e execute uma consulta que retorne os computadores que você deseja no grupo.
1. Clique em **salvar** na parte superior da tela.
1. Altere **salvar como** para **função** e selecione **salvar esta consulta como um grupo de computadores**.
1. Forneça valores para cada propriedade para o grupo de computadores descrito na tabela e clique em **salvar**.

A tabela a seguir descreve as propriedades que definem um grupo de computadores.

| Propriedade | Descrição |
|:---|:---|
| Nome   | Nome da consulta a ser exibida no Portal. |
| Alias da função | Um alias exclusivo usado para identificar o grupo de computadores em uma consulta. |
| Categoria       | Categoria para organizar as consultas no Portal. |


### <a name="active-directory"></a>Active Directory
Quando você configura Azure Monitor para importar Active Directory associações de grupo, ele analisa a associação de grupo de qualquer computador ingressado no domínio do Windows com o agente de Log Analytics.  Um grupo de computadores é criado no Azure Monitor para cada grupo de segurança no Active Directory, e cada computador com Windows é adicionado aos grupos de computadores correspondentes aos grupos de segurança dos quais eles são membros.  Essa associação é continuamente atualizada a cada 4 horas.  

> [!NOTE]
> Grupos de Active Directory importados contêm apenas computadores Windows.

Você configura Azure Monitor para importar Active Directory grupos de segurança das **Configurações avançadas** no espaço de trabalho Log Analytics no portal do Azure.  Selecione **grupos de computadores**, **Active Directory**e, em seguida, **importe Active Directory associações de grupo de computadores**.  Não há nenhuma configuração adicional.

![Grupos de computadores de Active Directory](media/computer-groups/configure-activedirectory.png)

Quando os grupos foram importados, o menu lista o número de computadores com a associação de grupo detectada e o número de grupos importados.  Você pode clicar em um desses links para retornar os registros do **computador** com essas informações.

### <a name="windows-server-update-service"></a>Serviço de atualização do Windows Server
Quando você configura Azure Monitor para importar associações de grupo do WSUS, ele analisa a associação de grupo de destino de todos os computadores com o agente de Log Analytics.  Se você estiver usando o direcionamento do lado do cliente, qualquer computador que esteja conectado a Azure Monitor e fizer parte de qualquer grupo de direcionamento do WSUS terá sua associação de grupos importada para Azure Monitor. Se você estiver usando o direcionamento do lado do servidor, o agente de Log Analytics deverá ser instalado no servidor do WSUS para que as informações de associação de grupo sejam importadas para Azure Monitor.  Essa associação é continuamente atualizada a cada 4 horas. 

Você configura Azure Monitor para importar grupos do WSUS de **Configurações avançadas** em seu espaço de trabalho do Log Analytics no portal do Azure.  Selecione **grupos de computadores**, **WSUS**e, em seguida, **importar associações de grupo do WSUS**.  Não há nenhuma configuração adicional.

![Grupos de computadores do WSUS](media/computer-groups/configure-wsus.png)

Quando os grupos foram importados, o menu lista o número de computadores com a associação de grupo detectada e o número de grupos importados.  Você pode clicar em um desses links para retornar os registros do **computador** com essas informações.

### <a name="system-center-configuration-manager"></a>System Center Configuration Manager
Quando você configura Azure Monitor para importar Configuration Manager associações de coleção, ele cria um grupo de computadores para cada coleção.  As informações de associação da coleção são recuperadas a cada três horas para manter os grupos de computadores atualizados. 

Antes de importar Configuration Manager coleções, você deve [conectar Configuration Manager ao Azure monitor](collect-sccm.md).  

![Grupos de computadores do SCCM](media/computer-groups/configure-sccm.png)

Quando as coleções foram importadas, o menu lista o número de computadores com a associação de grupo detectada e o número de grupos importados.  Você pode clicar em um desses links para retornar os registros do **computador** com essas informações.

## <a name="managing-computer-groups"></a>Gerenciando grupos de computadores
Você pode exibir grupos de computadores que foram criados a partir de uma consulta de log ou da API de pesquisa de logs nas **Configurações avançadas** em seu espaço de trabalho do Log Analytics na portal do Azure.  Selecione **grupos de computadores** e **grupos salvos**.  

Clique no **x** na coluna **remover** para excluir o grupo de computadores.  Clique no ícone **Exibir Membros** de um grupo para executar a pesquisa de log do grupo que retorna seus membros.  Você não pode modificar um grupo de computadores, mas deve excluí-lo e recriá-lo com as configurações modificadas.

![Grupos de computadores salvos](media/computer-groups/configure-saved.png)


## <a name="using-a-computer-group-in-a-log-query"></a>Usando um grupo de computadores em uma consulta de log
Você usa um grupo de computadores criado a partir de uma consulta de log em uma consulta tratando seu alias como uma função, normalmente com a seguinte sintaxe:

  `Table | where Computer in (ComputerGroup)`

Por exemplo, você pode usar o seguinte para retornar registros UpdateSummary somente para computadores em um grupo de computadores chamado MyComputer.
 
  `UpdateSummary | where Computer in (mycomputergroup)`


Os grupos de computadores importados e seus computadores incluídos são armazenados na tabela de grupo de **computadores** .  Por exemplo, a consulta a seguir retornaria uma lista de computadores no grupo de computadores do domínio a partir de Active Directory. 

  `ComputerGroup | where GroupSource == "ActiveDirectory" and Group == "Domain Computers" | distinct Computer`

A consulta a seguir retornará registros UpdateSummary somente para computadores em computadores de domínio.

  ```
  let ADComputers = ComputerGroup | where GroupSource == "ActiveDirectory" and Group == "Domain Computers" | distinct Computer;
  UpdateSummary | where Computer in (ADComputers)
  ```




## <a name="computer-group-records"></a>Registros de grupo de computadores
Um registro é criado no espaço de trabalho Log Analytics para cada associação de grupo de computadores criada no Active Directory ou no WSUS.  Esses registros têm um tipo de **computador** e têm as propriedades na tabela a seguir.  Os registros não são criados para grupos de computadores com base em consultas de log.

| Propriedade | Descrição |
|:--- |:--- |
| `Type` |*ComputerGroup* |
| `SourceSystem` |*SourceSystem* |
| `Computer` |Nome do computador membro. |
| `Group` |Nome do grupo. |
| `GroupFullName` |Caminho completo para o grupo, incluindo a origem e o nome da origem. |
| `GroupSource` |Fonte da qual o grupo foi coletado. <br><br>Active<br>WSUS<br>WSUSClientTargeting |
| `GroupSourceName` |Nome da origem da qual o grupo foi coletado.  Por Active Directory, esse é o nome de domínio. |
| `ManagementGroupName` |O nome do grupo de gestão para agentes do SCOM.  Para outros agentes, isso é AOI-\<ID do espaço de trabalho\> |
| `TimeGenerated` |Data e hora em que o grupo de computadores foi criado ou atualizado. |

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [consultas de log](../log-query/log-query-overview.md) para analisar os dados coletados de fontes de dados e soluções.  

