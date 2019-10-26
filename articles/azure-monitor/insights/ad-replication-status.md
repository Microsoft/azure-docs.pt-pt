---
title: Monitorar Active Directory status de replicação com Azure Monitor | Microsoft Docs
description: O pacote de solução Status de Replicação do Active Directory monitora regularmente seu ambiente de Active Directory para quaisquer falhas de replicação.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: MGoedtel
ms.author: magoedte
ms.date: 01/24/2018
ms.openlocfilehash: 04112042c871f5268c64bda374f040f1bba92969
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72931343"
---
# <a name="monitor-active-directory-replication-status-with-azure-monitor"></a>Monitorar Active Directory status de replicação com Azure Monitor

![Símbolo de Status de Replicação do AD](./media/ad-replication-status/ad-replication-status-symbol.png)

Active Directory é um componente-chave de um ambiente de ti empresarial. Para garantir a alta disponibilidade e o alto desempenho, cada controlador de domínio tem sua própria cópia do banco de dados Active Directory. Os controladores de domínio são replicados entre si para propagar as alterações em toda a empresa. As falhas nesse processo de replicação podem causar uma variedade de problemas em toda a empresa.

O pacote de solução Status de Replicação do AD monitora regularmente seu ambiente de Active Directory para quaisquer falhas de replicação.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand-solution.md)]

## <a name="installing-and-configuring-the-solution"></a>Instalando e configurando a solução
Utilize as seguintes informações para instalar e configurar a solução.

### <a name="install-agents-on-domain-controllers"></a>Instalar agentes em controladores de domínio
Você deve instalar agentes em controladores de domínio que são membros do domínio a ser avaliado. Ou, você deve instalar agentes em servidores membros e configurar os agentes para enviar dados de replicação do AD para Azure Monitor. Para entender como conectar computadores Windows ao Azure Monitor, consulte [conectar computadores Windows ao Azure monitor](../../azure-monitor/platform/agent-windows.md). Se o controlador de domínio já fizer parte de um ambiente de System Center Operations Manager existente ao qual você deseja se conectar Azure Monitor, consulte [conectar Operations Manager ao Azure monitor](../../azure-monitor/platform/om-agents.md).

### <a name="enable-non-domain-controller"></a>Habilitar controlador que não é de domínio
Se você não quiser conectar um de seus controladores de domínio diretamente ao Azure Monitor, poderá usar qualquer outro computador em seu domínio conectado a Azure Monitor para coletar dados para o Status de Replicação do AD Solution Pack e fazê-lo enviar os dados.

1. Verifique se o computador é membro do domínio que você deseja monitorar usando a solução Status de Replicação do AD.
2. [Conecte o computador Windows ao Azure monitor](../../azure-monitor/platform/om-agents.md) ou [Conecte-o usando seu ambiente de Operations Manager existente para Azure monitor](../../azure-monitor/platform/om-agents.md), se ele ainda não estiver conectado.
3. Nesse computador, defina a seguinte chave do registro:<br>Chave: **grupos de HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HealthService\Parameters\Management\<ManagementGroupName > \Solutions\ADReplication**<br>Valor: **Istarget**<br>Dados do valor: **true**

   > [!NOTE]
   > Essas alterações não entram em vigor até que você reinicie o serviço de Microsoft Monitoring Agent (HealthService. exe).
   > ### <a name="install-solution"></a>Instalar solução
   > Siga o processo descrito em [instalar uma solução de monitoramento](solutions.md#install-a-monitoring-solution) para adicionar a solução de **status de replicação do Active Directory** ao seu espaço de trabalho do log Analytics. Não há nenhuma configuração adicional.


## <a name="ad-replication-status-data-collection-details"></a>Detalhes da coleta de dados de Status de Replicação do AD
A tabela a seguir mostra os métodos de coleta de dados e outros detalhes sobre como os dados são coletados para Status de Replicação do AD.

| Plataforma | Agente direto | Agente do SCOM | Armazenamento do Azure | O SCOM é necessário? | Dados do agente do SCOM enviados por meio do grupo de gerenciamento | frequência de coleta |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |&#8226; |&#8226; |  |  |&#8226; |a cada cinco dias |



## <a name="understanding-replication-errors"></a>Noções básicas sobre erros de replicação

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

O bloco Status de Replicação do AD exibe quantos erros de replicação você tem atualmente. **Erros de replicação críticos** são erros que estão em ou acima de 75% do [tempo de vida da marca de exclusão](https://technet.microsoft.com/library/cc784932%28v=ws.10%29.aspx) para sua floresta Active Directory.

![Bloco Status de Replicação do AD](./media/ad-replication-status/oms-ad-replication-tile.png)

Ao clicar no bloco, você pode exibir mais informações sobre os erros.
![Status de Replicação do AD painel](./media/ad-replication-status/oms-ad-replication-dash.png)

### <a name="destination-server-status-and-source-server-status"></a>Status do servidor de destino e do servidor de origem
Essas colunas mostram o status dos servidores de destino e dos servidores de origem que estão apresentando erros de replicação. O número após cada nome de controlador de domínio indica o número de erros de replicação nesse controlador de domínio.

Os erros para servidores de destino e servidores de origem são mostrados porque alguns problemas são mais fáceis de solucionar a perspectiva do servidor de origem e outros da perspectiva do servidor de destino.

Neste exemplo, você pode ver que muitos servidores de destino têm aproximadamente o mesmo número de erros, mas há um servidor de origem (ADDC35) que tem muito mais erros do que todos os outros. É provável que haja algum problema em ADDC35 que esteja fazendo com que ele falhe ao enviar dados para seus parceiros de replicação. Corrigir os problemas no ADDC35 pode resolver muitos dos erros que aparecem na área do servidor de destino.

### <a name="replication-error-types"></a>Tipos de erro de replicação
Essa área fornece informações sobre os tipos de erros detectados em toda a sua empresa. Cada erro tem um código numérico exclusivo e uma mensagem que pode ajudá-lo a determinar a causa raiz do erro.

A rosca na parte superior dá a você uma ideia de quais erros aparecem com mais e menos frequência em seu ambiente.

Ele mostra quando vários controladores de domínio experimentam o mesmo erro de replicação. Nesse caso, você poderá descobrir ou identificar uma solução em um controlador de domínio e, em seguida, repeti-la em outros controladores de domínio afetados pelo mesmo erro.

### <a name="tombstone-lifetime"></a>Tempo de vida da marca de exclusão
O tempo de vida da marca para exclusão determina por quanto tempo um objeto excluído, chamado de marca para exclusão, é mantido no banco de dados Active Directory. Quando um objeto excluído passa o tempo de vida da marca para exclusão, um processo de coleta de lixo o remove automaticamente do banco de dados Active Directory.

O tempo de vida padrão de marca de exclusão é de 180 dias para as versões mais recentes do Windows, mas ele foi de 60 dias em versões mais antigas e pode ser alterado explicitamente por um administrador de Active Directory.

É importante saber se você está tendo erros de replicação que estão se aproximando ou estão além do tempo de vida da marca para exclusão. Se dois controladores de domínio tiverem um erro de replicação que persiste após o tempo de vida da marca de exclusão, a replicação será desabilitada entre esses dois controladores de domínio, mesmo que o erro de replicação subjacente seja corrigido.

A área de vida útil da marca para exclusão ajuda a identificar locais onde a replicação desabilitada está em perigo de ocorrer. Cada erro na categoria **acima de 100% TSL** representa uma partição que não foi replicada entre seu servidor de origem e de destino por pelo menos o tempo de vida da marca para exclusão para a floresta.

Nessa situação, simplesmente corrigir o erro de replicação não será suficiente. No mínimo, você precisa investigar manualmente para identificar e limpar os objetos remanescentes antes de poder reiniciar a replicação. Você pode até mesmo precisar encerrar um controlador de domínio.

Além de identificar quaisquer erros de replicação que persistiram após o tempo de vida da marca de exclusão, você também deseja prestar atenção a todos os erros que se enquadram nas categorias de **50-75% TSL** ou **75-100% TSL** .

Esses são erros que são claramente persistentes, não transitórios, portanto, eles provavelmente precisam de sua intervenção para resolver. A boa notícia é que eles ainda não atingiram o tempo de vida da marca de exclusão. Se você corrigir esses problemas imediatamente e *antes* que eles atinjam o tempo de vida da marca de exclusão, a replicação poderá ser reiniciada com intervenção manual mínima.

Conforme observado anteriormente, o bloco do painel para a solução Status de Replicação do AD mostra o número de erros de replicação *críticos* em seu ambiente, que é definido como erros que estão acima de 75% do tempo de vida da marca de exclusão (incluindo erros que têm mais de 100% de TSL). Busque para manter esse número em 0.

> [!NOTE]
> Todos os cálculos de porcentagem de tempo de vida da marca de exclusão são baseados no tempo de vida real da marca de exclusão para sua Active Directory floresta, para que você possa confiar que esses percentuais são precisos, mesmo que você tenha um conjunto de valores de tempo de vida de marca de exclusão personalizado definido.
>
>

### <a name="ad-replication-status-details"></a>Detalhes do status de replicação do AD
Ao clicar em qualquer item em uma das listas, você verá detalhes adicionais sobre ele usando uma consulta de log. Os resultados são filtrados para mostrar apenas os erros relacionados a esse item. Por exemplo, se você clicar no primeiro controlador de domínio listado em **status do servidor de destino (ADDC02)** , verá os resultados da consulta filtrados para mostrar erros com esse controlador de domínio listado como o servidor de destino:

![Erros de status de replicação do AD nos resultados da consulta](./media/ad-replication-status/oms-ad-replication-search-details.png)

A partir daqui, você pode filtrar mais detalhadamente, modificar a consulta de log e assim por diante. Para obter mais informações sobre como usar as consultas de log no Azure Monitor, consulte [analisar dados de log em Azure monitor](../../azure-monitor/log-query/log-query-overview.md).

O campo **HelpLink** mostra a URL de uma página do TechNet com detalhes adicionais sobre esse erro específico. Você pode copiar e colar esse link na janela do navegador para ver informações sobre como solucionar problemas e corrigir o erro.

Você também pode clicar em **Exportar** para exportar os resultados para o Excel. A exportação dos dados pode ajudá-lo a Visualizar dados de erro de replicação de qualquer forma que desejar.

![erros de status de replicação do AD exportados no Excel](./media/ad-replication-status/oms-ad-replication-export.png)

## <a name="ad-replication-status-faq"></a>Perguntas frequentes Status de Replicação do AD
**P: com que frequência os dados de status de replicação do AD são atualizados?**
R: as informações são atualizadas a cada cinco dias.

**P: há uma maneira de configurar a frequência com que esses dados são atualizados?**
R: não no momento.

**P: preciso adicionar todos os meus controladores de domínio ao meu Log Analytics espaço de trabalho para ver o status de replicação?**
R: não, apenas um único controlador de domínio deve ser adicionado. Se você tiver vários controladores de domínio em seu espaço de trabalho Log Analytics, os dados de todos eles serão enviados para Azure Monitor.

**P: não desejo adicionar nenhum controlador de domínio ao meu espaço de trabalho do Log Analytics. Ainda posso usar a solução Status de Replicação do AD?**

R: Sim. Você pode definir o valor de uma chave do registro para habilitá-la. Consulte [habilitar o controlador que não é de domínio](#enable-non-domain-controller).

**P: Qual é o nome do processo que faz a coleta de dados?**
R: AdvisorAssessment. exe

**P: quanto tempo leva para que os dados sejam coletados?**
R: o tempo de coleta de dados depende do tamanho do ambiente de Active Directory, mas geralmente leva menos de 15 minutos.

**P: que tipo de dados é coletado?**
R: as informações de replicação são coletadas via LDAP.

**P: há uma maneira de configurar quando os dados são coletados?**
R: não no momento.

**P: quais permissões preciso para coletar dados?**
R: as permissões de usuário normais para Active Directory são suficientes.

## <a name="troubleshoot-data-collection-problems"></a>Solucionar problemas de coleta de dados
Para coletar dados, o pacote de solução Status de Replicação do AD requer que pelo menos um controlador de domínio seja conectado ao seu espaço de trabalho Log Analytics. Até que você conecte um controlador de domínio, será exibida uma mensagem indicando que **os dados ainda estão sendo coletados**.

Se precisar de assistência para conectar um de seus controladores de domínio, você poderá exibir [a documentação em conectar computadores Windows ao Azure monitor](../../azure-monitor/platform/om-agents.md). Como alternativa, se o controlador de domínio já estiver conectado a um ambiente de System Center Operations Manager existente, você poderá exibir [a documentação em conectar System Center Operations Manager para Azure monitor](../../azure-monitor/platform/om-agents.md).

Se você não quiser conectar os controladores de domínio diretamente ao Azure Monitor ou ao System Center Operations Manager, consulte Habilitar o [controlador que não](#enable-non-domain-controller)é de domínio.

## <a name="next-steps"></a>Passos seguintes
* Use [consultas de log em Azure monitor](../../azure-monitor/log-query/log-query-overview.md) para exibir dados detalhados de status de replicação Active Directory.
