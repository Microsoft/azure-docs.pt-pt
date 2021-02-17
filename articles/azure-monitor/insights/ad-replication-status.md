---
title: MonitorAr estado de replicação do Diretório Ativo
description: O pacote de solução de estado de replicação do diretório ativo monitoriza regularmente o seu ambiente de Diretório Ativo para eventuais falhas de replicação.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/24/2018
ms.openlocfilehash: 01d641801b1b9a0cfaa4fd7ee11e3c55314dc53c
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100577529"
---
# <a name="monitor-active-directory-replication-status-with-azure-monitor"></a>Monitor Ative Directory replication status with Azure Monitor

![Símbolo de estado de replicação de AD](./media/ad-replication-status/ad-replication-status-symbol.png)

O Ative Directory é um componente chave de um ambiente de TI da empresa. Para garantir uma elevada disponibilidade e alto desempenho, cada controlador de domínio tem a sua própria cópia da base de dados Ative Directory. Os controladores de domínio replicam-se entre si para propagar as mudanças em toda a empresa. Falhas neste processo de replicação podem causar uma variedade de problemas em toda a empresa.

A solução de Estado de Replicação de AD monitoriza regularmente o seu ambiente de Diretório Ativo para eventuais falhas de replicação.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand-solution.md)]

## <a name="installing-and-configuring-the-solution"></a>Instalar e configurar a solução
Utilize as seguintes informações para instalar e configurar a solução.

### <a name="prerequisites"></a>Pré-requisitos

* A solução de Estado de Replicação de AD requer uma versão suportada do Quadro .NET 4.6.2 ou superior instalado em cada computador que tenha o agente Log Analytics para windows (também designado por Agente de Monitorização da Microsoft (MMA)) instalado.  O agente é utilizado pelo System Center 2016 - Gestor de Operações, Gestor de Operações 2012 R2 e Monitor Azure.
* A solução suporta controladores de domínio que executam o Windows Server 2008 e 2008 R2, Windows Server 2012 e 2012 R2 e Windows Server 2016.
* Um espaço de trabalho Log Analytics para adicionar a solução Ative Directory Health Check do mercado Azure no portal Azure. Não é necessária nenhuma configuração adicional.


### <a name="install-agents-on-domain-controllers"></a>Instalar agentes em controladores de domínio
Tem de instalar agentes em controladores de domínio que sejam membros do domínio a avaliar. Ou, deve instalar agentes nos servidores dos membros e configurar os agentes para enviar dados de replicação de AD para o Azure Monitor. Para entender como ligar computadores Windows ao Azure Monitor, consulte [os computadores Connect Windows ao Azure Monitor](../agents/agent-windows.md). Se o seu controlador de domínio já faz parte de um ambiente existente do Gestor de Operações do Centro de Sistema que pretende ligar ao Azure Monitor, consulte [o Connect Operations Manager para o Azure Monitor](../agents/om-agents.md).

### <a name="enable-non-domain-controller"></a>Ativar o controlador não de domínio
Se não quiser ligar nenhum dos seus controladores de domínio diretamente ao Azure Monitor, pode utilizar qualquer outro computador do seu domínio ligado ao Azure Monitor para recolher dados para o pacote de solução de estado de replicação de AD e enviar os dados.

1. Verifique se o computador é um membro do domínio que pretende monitorizar utilizando a solução de Estado de Replicação de AD.
2. [Ligue o computador Windows ao Azure Monitor](../agents/om-agents.md) ou [conecte-o utilizando o ambiente do Gestor de Operações existente ao Azure Monitor,](../agents/om-agents.md)se ainda não estiver ligado.
3. Nesse computador, estale a seguinte chave de registo:<br>Chave: **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HealthService\Parameters\Management Groups\<ManagementGroupName> \Soluções\ADReplicação**<br>Valor: **IsTarget**<br>Dados de Valor: **verdadeiros**

   > [!NOTE]
   > Estas alterações não têm efeito até reiniciar o serviço de Monitorização do Microsoft (HealthService.exe).
   > ### <a name="install-solution"></a>Instalar solução
   > Siga o processo descrito na [Instalação de uma solução de monitorização](solutions.md#install-a-monitoring-solution) para adicionar a solução de Estado de Replicação do **Diretório Ativo** ao seu espaço de trabalho Log Analytics. Não há nenhuma configuração adicional.


## <a name="ad-replication-status-data-collection-details"></a>Detalhes da recolha de dados do estado de replicação de AD
A tabela seguinte mostra métodos de recolha de dados e outros detalhes sobre como os dados são recolhidos para o Estado de Replicação de AD.

| plataforma | Agente Direto | Agente SCOM | Storage do Azure | SCOM necessário? | Dados de agente SCOM enviados através do grupo de gestão | frequência de coleção |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |&#8226; |&#8226; |  |  |&#8226; |a cada cinco dias |



## <a name="understanding-replication-errors"></a>Compreender erros de replicação

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

O azulejo do Estado de Replicação AD mostra quantos erros de replicação tem atualmente. **Erros de replicação crítica** são erros que estão em 75% ou acima da [vida útil](/previous-versions/windows/it-pro/windows-server-2003/cc784932(v=ws.10)) da lápide para a sua floresta ative directy.

![Azulejo do estado de replicação da AD](./media/ad-replication-status/oms-ad-replication-tile.png)

Ao clicar no azulejo, pode ver mais informações sobre os erros.
![Painel de estado de replicação de anúncios](./media/ad-replication-status/oms-ad-replication-dash.png)

### <a name="destination-server-status-and-source-server-status"></a>Estado do servidor de destino e estado do servidor de origem
Estas colunas mostram o estado dos servidores de destino e dos servidores de origem que estão a sofrer erros de replicação. O número após cada nome do controlador de domínio indica o número de erros de replicação no controlador de domínio.

Os erros tanto para os servidores de destino como para os servidores de origem são mostrados porque alguns problemas são mais fáceis de resolver a partir da perspetiva do servidor de origem e outros do ponto de vista do servidor de destino.

Neste exemplo, pode ver que muitos servidores de destino têm aproximadamente o mesmo número de erros, mas há um servidor de origem (ADDC35) que tem muitos mais erros do que todos os outros. É provável que haja algum problema no ADDC35 que está a fazer com que não envie dados aos seus parceiros de replicação. Corrigir os problemas no ADDC35 pode resolver muitos dos erros que aparecem na área do servidor de destino.

### <a name="replication-error-types"></a>Tipos de erros de replicação
Esta área dá-lhe informações sobre os tipos de erros detetados em toda a sua empresa. Cada erro tem um código numérico único e uma mensagem que pode ajudá-lo a determinar a causa principal do erro.

O donut no topo dá-lhe uma ideia de quais os erros que aparecem cada vez mais frequentemente no seu ambiente.

Mostra quando vários controladores de domínio experimentam o mesmo erro de replicação. Neste caso, poderá ser capaz de descobrir ou identificar uma solução num controlador de domínio e, em seguida, repeti-la em outros controladores de domínio afetados pelo mesmo erro.

### <a name="tombstone-lifetime"></a>Vida útil de lápide
A duração da lápide determina quanto tempo um objeto apagado, referido como lápide, é mantido na base de dados do Ative Directory. Quando um objeto apagado passa a vida útil da lápide, um processo de recolha de lixo remove-o automaticamente da base de dados Ative Directory.

A vida útil padrão da lápide é de 180 dias para as versões mais recentes do Windows, mas foi de 60 dias em versões mais antigas, e pode ser alterada explicitamente por um administrador do Ative Directory.

É importante saber se está a ter erros de replicação que se aproximam ou que já passaram a vida útil da lápide. Se dois controladores de domínio experimentarem um erro de replicação que persiste após a vida útil da lápide, a replicação é desativada entre esses dois controladores de domínio, mesmo que o erro de replicação subjacente seja corrigido.

A área de Tombstone Lifetime ajuda-o a identificar locais onde a replicação desativada corre o risco de acontecer. Cada erro na categoria **TSL de mais de 100%** representa uma divisória que não se replica entre a sua fonte e o servidor de destino durante pelo menos a vida útil da lápide para a floresta.

Nesta situação, simplesmente corrigir o erro de replicação não será suficiente. No mínimo, é necessário investigar manualmente para identificar e limpar objetos persistentes antes de poder reiniciar a replicação. Pode até precisar de desativar um controlador de domínio.

Além de identificar quaisquer erros de replicação que tenham persistido após a vida útil da lápide, também pretende prestar atenção a quaisquer erros que caiam nas **categorias TSL de 50-75%** ou **75-100% TSL.**

Estes são erros que são claramente persistentes, não transitórios, por isso provavelmente precisam da sua intervenção para resolver. A boa notícia é que ainda não chegaram à lápide. Se corrigir estes problemas rapidamente e *antes* de atingirem a vida útil da lápide, a replicação pode reiniciar com a mínima intervenção manual.

Como já foi notado, o azulejo do dashboard para a solução de Estado de Replicação AD mostra o número de erros *críticos* de replicação no seu ambiente, que é definido como erros que são mais de 75% da vida útil da lápide (incluindo erros que são mais de 100% de TSL). Esforça-te para manter este número em 0.

> [!NOTE]
> Todos os cálculos da percentagem de vida da lápide baseiam-se na vida útil real da lápide para a sua floresta ative directory, para que possa confiar que essas percentagens são precisas, mesmo que tenha um conjunto de valor de vida de lápide personalizado.
>
>

### <a name="ad-replication-status-details"></a>Detalhes do estado da replicação de AD
Quando clica em qualquer item numa das listas, vê detalhes adicionais sobre o mesmo utilizando uma consulta de registo. Os resultados são filtrados para mostrar apenas os erros relacionados com este item. Por exemplo, se clicar no primeiro controlador de domínio listado no **Estado do Servidor de Destino (ADDC02)**, vê os resultados da consulta filtrados para mostrar erros com o controlador de domínio listado como servidor de destino:

![Erros de estado de replicação de AD nos resultados da consulta](./media/ad-replication-status/oms-ad-replication-search-details.png)

A partir daqui, pode filtrar mais, modificar a consulta de registo, e assim por diante. Para obter mais informações sobre a utilização das consultas de registo no Azure Monitor, consulte [os dados de registo de análise no Azure Monitor](../logs/log-query-overview.md).

O campo **HelpLink** mostra o URL de uma página TechNet com detalhes adicionais sobre esse erro específico. Pode copiar e colar este link na janela do seu navegador para ver informações sobre a resolução de problemas e a correção do erro.

Também pode clicar em **Exportar** para exportar os resultados para o Excel. Exportar os dados pode ajudá-lo a visualizar dados de erros de replicação da forma que quiser.

![exportado erros de estado de replicação de AD no Excel](./media/ad-replication-status/oms-ad-replication-export.png)

## <a name="ad-replication-status-faq"></a>Estado de replicação de anúncios FAQ
**P: Com que frequência os dados de estado de replicação da AD são atualizados?**
R: A informação é atualizada a cada cinco dias.

**P: Existe uma forma de configurar com que frequência estes dados são atualizados?**
A: Não neste momento.

**P: Preciso de adicionar todos os meus controladores de domínio ao meu espaço de trabalho Log Analytics para ver o estado de replicação?**
R: Não, apenas um único controlador de domínio deve ser adicionado. Se tiver vários controladores de domínio no seu espaço de trabalho Log Analytics, os dados de todos eles são enviados para o Azure Monitor.

**P: Não quero adicionar controladores de domínio ao meu espaço de trabalho Log Analytics. Ainda posso usar a solução de Estado de Replicação AD?**

R: Sim. Pode definir o valor de uma chave de registo para o ativar. Ver [Ativar o controlador de não domínio](#enable-non-domain-controller).

**P: Qual é o nome do processo que faz a recolha de dados?**
A: AdvisorAssessment.exe

**P: Quanto tempo demora a recolher os dados?**
R: O tempo de recolha de dados depende do tamanho do ambiente ative directory, mas geralmente demora menos de 15 minutos.

**P: Que tipo de dados são recolhidos?**
R: A informação de replicação é recolhida via LDAP.

**P: Existe uma forma de configurar quando os dados são recolhidos?**
A: Não neste momento.

**P: Que permissões preciso para recolher dados?**
R: As permissões normais do utilizador para o Ative Directory são suficientes.

## <a name="troubleshoot-data-collection-problems"></a>Problemas de recolha de dados de resolução de problemas
Para recolher dados, o pacote de solução de estado de replicação AD requer pelo menos um controlador de domínio para ser ligado ao seu espaço de trabalho Log Analytics. Até ligar um controlador de domínio, aparece uma mensagem indicando que **os dados ainda estão a ser recolhidos**.

Se precisar de assistência para ligar um dos seus controladores de domínio, pode ver documentação nos [computadores Connect Windows ao Azure Monitor](../agents/om-agents.md). Em alternativa, se o seu controlador de domínio já estiver ligado a um ambiente existente do Gestor de Operações do Centro de Sistema, pode ver documentação no [Connect System Center Operations Manager ao Azure Monitor](../agents/om-agents.md).

Se não pretender ligar nenhum dos seus controladores de domínio diretamente ao Azure Monitor ou ao Gestor de Operações do Centro de Sistema, consulte [Ativar o controlador de não domínio](#enable-non-domain-controller).

## <a name="next-steps"></a>Passos seguintes
* Utilize [consultas de registo no Azure Monitor](../logs/log-query-overview.md) para ver dados detalhados do estado de replicação do diretório ativo.

