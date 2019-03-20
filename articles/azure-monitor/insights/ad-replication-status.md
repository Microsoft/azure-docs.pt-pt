---
title: Monitorizar o estado de replicação do Active Directory com o Azure Monitor | Documentos da Microsoft
description: O pacote de solução de estado de replicação do Active Directory monitoriza regularmente o ambiente do Active Directory para qualquer falhas de replicação.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 1b988972-8e01-4f83-a7f4-87f62778f91d
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/24/2018
ms.author: magoedte
ms.openlocfilehash: 100d33bbd888d00ed33a38680df5a777e12fd63e
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58120810"
---
# <a name="monitor-active-directory-replication-status-with-azure-monitor"></a>Monitorizar o estado de replicação do Active Directory com o Azure Monitor

![Símbolo de estado de replicação do AD](./media/ad-replication-status/ad-replication-status-symbol.png)

Active Directory é um componente fundamental de um ambiente de TI empresarial. Para garantir elevada disponibilidade e elevado desempenho, cada controlador de domínio tem sua própria cópia do banco de dados do Active Directory. Controladores de domínio, replicar entre si, para poder propagar alterações em toda a empresa. Falhas nesse processo de replicação podem causar uma variedade de problemas em toda a empresa.

O pacote de solução de estado de replicação do AD monitora regularmente o ambiente do Active Directory para qualquer falhas de replicação.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand-solution.md)]

## <a name="installing-and-configuring-the-solution"></a>Instalar e configurar a solução
Utilize as seguintes informações para instalar e configurar a solução.

### <a name="install-agents-on-domain-controllers"></a>Instalar agentes nos controladores de domínio
Tem de instalar agentes nos controladores de domínio que são membros do domínio a ser avaliada. Em alternativa, tem de instalar agentes nos servidores de membro e configurar os agentes para enviar dados de replicação do AD para o Azure Monitor. Para compreender como ligar computadores Windows para o Azure Monitor, veja [computadores Windows ligar para o Azure Monitor](../../azure-monitor/platform/agent-windows.md). Se o seu controlador de domínio já faz parte de um ambiente existente do System Center Operations Manager que pretende ligar-se para o Azure Monitor, consulte [ligar o Operations Manager para o Azure Monitor](../../azure-monitor/platform/om-agents.md).

### <a name="enable-non-domain-controller"></a>Ativar o controlador de domínio
Se não pretender ligar-se a qualquer um dos seus controladores de domínio diretamente para o Azure Monitor, pode utilizar qualquer outro computador no seu domínio ligado para o Azure Monitor para recolher dados para o pacote de solução de estado de replicação do AD e fazê-lo a enviar os dados.

1. Certifique-se de que o computador é membro do domínio que pretende monitorizar a utilização da solução de estado de replicação do AD.
2. [Ligar o computador do Windows para o Azure Monitor](../../azure-monitor/platform/om-agents.md) ou [ligá-la com o seu ambiente existente do Operations Manager para o Azure Monitor](../../azure-monitor/platform/om-agents.md), se já não está ligado.
3. Nesse computador, defina a seguinte chave de registo:<br>Chave: **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HealthService\Parameters\Management Groups\<ManagementGroupName>\Solutions\ADReplication**<br>Valor: **IsTarget**<br>Dados do valor: **verdadeiro**

   > [!NOTE]
   > Estas alterações não entrem em vigor até a reiniciar o serviço Microsoft Monitoring Agent (HealthService.exe).
   > ### <a name="install-solution"></a>Instalar a solução
   > Siga o processo descrito em [instalar uma solução de monitorização](solutions.md#install-a-monitoring-solution) para adicionar o **estado de replicação do Active Directory** solução para a área de trabalho do Log Analytics. Não há nenhuma configuração adicional.


## <a name="ad-replication-status-data-collection-details"></a>Detalhes de recolha de dados de estado de replicação do AD
A tabela seguinte mostra os métodos de recolha de dados e outros detalhes sobre como os dados são recolhidos para o estado de replicação do AD.

| Plataforma | Agente Direto | Agente do SCOM | Storage do Azure | SCOM necessário? | Dados de agente do SCOM enviados através do grupo de gestão | Frequência de recolha |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |&#8226; |&#8226; |  |  |&#8226; |cada cinco dias |



## <a name="understanding-replication-errors"></a>Compreender erros de replicação

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

O mosaico de estado de replicação do AD apresenta o número de erros de replicação já existe. **Erros críticos de replicação** erros que são igual ou superior a 75% do valor de [duração do tombstone](https://technet.microsoft.com/library/cc784932%28v=ws.10%29.aspx) para sua floresta do Active Directory.

![Mosaico de estado de replicação do AD](./media/ad-replication-status/oms-ad-replication-tile.png)

Quando clica no mosaico, pode ver mais informações sobre os erros.
![Dashboard de estado de replicação do AD](./media/ad-replication-status/oms-ad-replication-dash.png)

### <a name="destination-server-status-and-source-server-status"></a>Estado do servidor de destino e o estado do servidor de origem
Estas colunas mostram o estado dos servidores de destino e servidores de origem que ocorrerem erros de replicação. O número após cada nome de controlador de domínio indica o número de erros de replicação nesse controlador de domínio.

Os erros para servidores de destino e servidores de origem são mostrados porque alguns problemas são mais fáceis de solucionar problemas a partir da perspectiva do servidor de origem e de outras pessoas da perspectiva do servidor de destino.

Neste exemplo, pode ver que muitos servidores de destino têm praticamente o mesmo número de erros, mas há um servidor de origem (ADDC35) que tenha muitos erros mais do que todos os outros. É provável que existe um problema no ADDC35 que está a causar que a falha ocorra enviar dados para seus parceiros de replicação. Corrigir os problemas em ADDC35 poderá resolver muitos dos erros que aparecem na área de servidor de destino.

### <a name="replication-error-types"></a>Tipos de erros de replicação
Esta área fornece informações sobre os tipos de erros detetados em toda a empresa. Cada erro tem um código numérico exclusivo e uma mensagem que pode ajudar a determinar a causa do erro.

Anel na parte superior dá uma idéia de quais erros aparecem mais e com menos frequência no seu ambiente.

Ela mostra quando vários controladores de domínio experiência o mesmo erro de replicação. Neste caso, poderá ser capaz de detetar ou identificar uma solução num controlador de domínio, em seguida, repeti-lo em outros controladores de domínio afetados pelo mesmo erro.

### <a name="tombstone-lifetime"></a>Duração do tombstone
A duração do tombstone determina o tempo que um objeto eliminado, conhecido como uma marca para exclusão, são retidas na base de dados do Active Directory. Quando um objeto excluído transmite o tempo de vida da marca para exclusão, um processo de coleta de lixo remove-lo automaticamente da base de dados do Active Directory.

A duração do tombstone predefinido é de 180 dias para versões mais recentes do Windows, mas era 60 dias em versões mais antigas e pode ser alterada explicitamente por um administrador do Active Directory.

É importante saber se estiver a ter erros de replicação que estão prestes a atingir ou passado o tempo de vida da marca para exclusão. Se dois controladores de domínio tiver um erro de replicação que persiste após o tempo de vida da marca para exclusão, é desativar a replicação entre os controladores de duas domínio, mesmo se o erro de replicação subjacente é fixo.

A área de duração do Tombstone ajuda a identificar os locais onde os replicação desativada está em perigo de acontecer. Cada erro no **mais de 100% TSL** categoria representa uma partição que não foi replicado entre o servidor de origem e de destino para, pelo menos, o tempo de vida da marca para exclusão para a floresta.

Nesta situação, simplesmente corrigir o erro de replicação não será suficiente. No mínimo, precisa investigar manualmente para identificar e limpar objetos remanescentes, antes de reiniciar a replicação. Pode até mesmo terá de desativar um controlador de domínio.

Além de identificar quaisquer erros de replicação que têm mantidos após o tempo de vida da marca para exclusão, também deve prestar atenção para quaisquer erros cair a **50 75% TSL** ou **75 a 100% TSL** categorias.

Esses são erros que estão claramente remanescentes, não é transitório, pelo que provavelmente precisam, de facto, a intervenção do utilizador para resolver. A boa notícia é que eles têm ainda não atingiu o tempo de vida da marca para exclusão. Se corrigir esses problemas imediatamente e *antes de* atingirem o tempo de vida da marca para exclusão, a replicação pode reiniciar com intervenção manual mínima.

Como observado anteriormente, o mosaico do dashboard, para a solução de estado de replicação do AD mostra o número de *críticos* erros de replicação no seu ambiente, o que é definido como erros que são mais de 75% da duração do tombstone (incluindo erros que são mais 100% de TSL). Se esforça manter este número em 0.

> [!NOTE]
> Todos os marca para exclusão duração percentagem cálculos baseiam-se a ser a duração do tombstone real para a floresta do Active Directory, por isso, pode que essas porcentagens são precisas, mesmo que tenha um valor de tempo de vida da marca para exclusão personalizado definido.
>
>

### <a name="ad-replication-status-details"></a>Detalhes de estado de replicação do AD
Quando clicar em qualquer item em uma das listas, verá detalhes adicionais sobre o mesmo usando uma consulta de registo. Os resultados são filtrados para mostrar apenas os erros relacionados com esse item. Por exemplo, se clicar no primeiro controlador de domínio listados na **estado do servidor de destino (ADDC02)**, verá os resultados da consulta filtrados para mostrar erros com esse controlador de domínio listado como o servidor de destino:

![Erros de estado de replicação de AD nos resultados de consulta](./media/ad-replication-status/oms-ad-replication-search-details.png)

A partir daqui, pode filtrar ainda mais, modifique a consulta de registo e assim por diante. Para obter mais informações sobre como utilizar as consultas de registo no Azure Monitor, consulte [analisar dados de registo no Azure Monitor](../../azure-monitor/log-query/log-query-overview.md).

O **HelpLink** campo mostra o URL de uma página do TechNet com detalhes adicionais sobre esse erro específico. Pode copiar e colar esta ligação para a janela do browser para ver informações sobre resolução de problemas e corrigir o erro.

Também pode clicar **exportar** para exportar os resultados para o Excel. Exportar os dados pode ajudá-lo a visualizar dados de erro de replicação de qualquer forma que quiser.

![exportado erros de estado de replicação de AD no Excel](./media/ad-replication-status/oms-ad-replication-export.png)

## <a name="ad-replication-status-faq"></a>FAQ de estado de replicação do AD
**P: São a frequência com que dados de estado de replicação de AD atualizados?**
R: As informações são atualizadas a cada cinco dias.

**P: Existe uma forma de configurar a frequência com que estes dados são atualizados?**
R: Neste momento, não.

**P: É necessário adicionar todos meus controladores de domínio para a minha área de trabalho do Log Analytics para ver o estado de replicação?**
R: Não, apenas um único controlador de domínio tem de ser adicionado. Se tiver vários controladores de domínio na sua área de trabalho do Log Analytics, os dados de todos eles são enviados para o Azure Monitor.

**P: Não quero adicionar quaisquer controladores de domínio a minha área de trabalho do Log Analytics. Posso continuar a utilizar a solução de estado de replicação do AD?**

R: Sim. Pode definir o valor de uma chave de registo para ativá-la. Ver [controlador de domínio não ativar](#enable-non-domain-controller).

**P: O que é o nome do processo que faz a recolha de dados?**
R: AdvisorAssessment.exe

**P: Quanto tempo demora para recolher dados?**
R: Hora de recolha de dados depende do tamanho do ambiente do Active Directory, mas, normalmente, demora menos de 15 minutos.

**P: Que tipo de dados é recolhido?**
R: Informações de replicação são recolhidas através de LDAP.

**P: Existe uma forma de configurar quando os dados são recolhidos?**
R: Neste momento, não.

**P: Que permissões preciso recolher dados?**
R: Permissões de usuário normal para o Active Directory são suficientes.

## <a name="troubleshoot-data-collection-problems"></a>Resolução de problemas de recolha de dados
Para recolher dados, o pacote de solução de estado de replicação do AD requer pelo menos um controlador de domínio a ser ligado à sua área de trabalho do Log Analytics. Até que se liga um controlador de domínio, é apresentada uma mensagem indicando que **ainda estão a ser recolhidos dados**.

Se precisar de assistência a ligar um dos seus controladores de domínio, pode ver a documentação em [computadores Windows ligar para o Azure Monitor](../../azure-monitor/platform/om-agents.md). Em alternativa, se o seu controlador de domínio já está ligado a um ambiente existente do System Center Operations Manager, pode ver documentação em [ligar o System Center Operations Manager para o Azure Monitor](../../azure-monitor/platform/om-agents.md).

Se não pretender ligar qualquer um dos seus controladores de domínio diretamente para o Azure Monitor ou para o System Center Operations Manager, veja [controlador de domínio não ativar](#enable-non-domain-controller).

## <a name="next-steps"></a>Passos Seguintes
* Uso [registar as consultas no Azure Monitor](../../azure-monitor/log-query/log-query-overview.md) para ver os dados detalhados de estado de replicação do Active Directory.
