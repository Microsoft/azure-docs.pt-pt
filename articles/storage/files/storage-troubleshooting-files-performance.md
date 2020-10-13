---
title: Guia de resolução de problemas de desempenho dos Ficheiros Azure Files
description: Resolução de problemas conhecidos problemas de desempenho com ações de ficheiros Azure. Descubra potenciais causas e soluções alternativas associadas quando estes problemas são encontrados.
author: gunjanj
ms.service: storage
ms.topic: troubleshooting
ms.date: 09/15/2020
ms.author: gunjanj
ms.subservice: files
ms.openlocfilehash: 7afaa057ecc94cf67d4fd5b041d95210fcf26717
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90707599"
---
# <a name="troubleshoot-azure-files-performance-issues"></a>Problemas de resolução de problemas Problemas de desempenho dos Ficheiros Azure

Este artigo enumera alguns problemas comuns relacionados com as ações de ficheiros Azure. Fornece potenciais causas e soluções quando estes problemas são encontrados.

## <a name="high-latency-low-throughput-and-general-performance-issues"></a>Altas latências, baixo rendimento e problemas gerais de desempenho

### <a name="cause-1-share-was-throttled"></a>Causa 1: A partilha foi acelerada

Os pedidos são acelerados quando o IOPS, ingresss ou limites de saída para uma partilha de ficheiros são atingidos. Para compreender os limites das ações de ficheiros standard e premium, consulte [os alvos de ações de ficheiros e de escala de ficheiros.](https://docs.microsoft.com/azure/storage/files/storage-files-scale-targets#file-share-and-file-scale-targets)

Para confirmar se a sua parte está a ser acelerada, pode aproveitar a Azure Metrics no portal.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Selecione **Todos os serviços** e, em seguida, procure **por Métricas**.

1. Selecione **Métricas**.

1. Selecione a sua conta de armazenamento como o recurso.

1. Selecione **File** como o espaço de nome métrico.

1. Selecione **Transações** como métrica.

1. Adicione um filtro para **o ResponseType** e verifique se algum pedido tem um código de resposta de **SuccessWithThrottling** (para SMB) ou **ClientThrottlingError** (para REST).

![Opções de métricas para partilhas de ficheiros premium](media/storage-troubleshooting-premium-fileshares/metrics.png)

> [!NOTE]
> Para receber um alerta se uma partilha de ficheiros for acelerada, consulte [Como criar um alerta se uma partilha de ficheiros for acelerada](#how-to-create-an-alert-if-a-file-share-is-throttled).

### <a name="solution"></a>Solução

- Se estiver a utilizar uma partilha de ficheiros padrão, ative [grandes ações de ficheiros](https://docs.microsoft.com/azure/storage/files/storage-files-how-to-create-large-file-share?tabs=azure-portal) na sua conta de armazenamento. As grandes ações suportam até 10.000 IOPS por ação.
- Se estiver a utilizar uma parte de ficheiro premium, aumente o tamanho da ação de ficheiros for provisionada para aumentar o limite do IOPS. Para saber mais, consulte a [secção de provisão de Compreensão para ações de ficheiros premium](https://docs.microsoft.com/azure/storage/files/storage-files-planning#understanding-provisioning-for-premium-file-shares) no guia de planeamento dos Ficheiros Azure.

### <a name="cause-2-metadatanamespace-heavy-workload"></a>Causa 2: Metadata/espaço de nome carga de trabalho pesada

Se a maioria dos seus pedidos forem centrados em metadados (tais como criarfile/openfile/closefile/queryinfo/querydirectory) então a latência será pior quando comparada com operações de leitura/escrita.

Para confirmar se a maioria dos seus pedidos são centrados em metadados, pode usar os mesmos passos acima. Exceto que em vez de adicionar um filtro para **ResponseType,** adicione um filtro para **o nome API**.

![Filtro para nome API nas suas métricas](media/storage-troubleshooting-premium-fileshares/MetadataMetrics.png)

### <a name="workaround"></a>Solução

- Verifique se a aplicação pode ser modificada para reduzir o número de operações de metadados.
- Adicione um VHD na partilha de ficheiros e monte VHD sobre SMB do cliente para realizar operações de ficheiros contra os dados. Esta abordagem funciona para cenários individuais de escritores e vários leitores e permite que as operações de metadados sejam locais, oferecendo um desempenho semelhante a um armazenamento direto local.

### <a name="cause-3-single-threaded-application"></a>Causa 3: Aplicação de rosca única

Se a aplicação utilizada pelo cliente for de rosca única, isso pode resultar numa produção IOPS/produção significativamente mais baixa do que o máximo possível com base no tamanho da sua ação aprovisionada.

### <a name="solution"></a>Solução

- Aumente o paralelismo de aplicação aumentando o número de fios.
- Mude para aplicações onde o paralelismo é possível. Por exemplo, para operações de cópia, os clientes poderiam utilizar a AzCopy ou RoboCopy a partir de clientes Windows ou o comando **paralelo** nos clientes Linux.

## <a name="very-high-latency-for-requests"></a>Latência muito alta para pedidos

### <a name="cause"></a>Causa

O VM cliente poderia estar localizado numa região diferente da parte do ficheiro.

### <a name="solution"></a>Solução

- Executar o pedido a partir de um VM que está localizado na mesma região que a partilha de ficheiros.

## <a name="client-unable-to-achieve-maximum-throughput-supported-by-the-network"></a>Cliente incapaz de atingir a produção máxima suportada pela rede

Uma das causas potenciais é a falta de suporte a vários canais SMB. Atualmente, as ações de ficheiroS Azure suportam apenas um canal único, pelo que existe apenas uma ligação do VM do cliente ao servidor. Esta ligação única é marcada para um único núcleo no VM do cliente, de modo que a produção máxima alcançável a partir de um VM é ligada por um único núcleo.

### <a name="workaround"></a>Solução

- Obter um VM com um núcleo maior pode ajudar a melhorar a produção.
- Executar a aplicação do cliente a partir de vários VMs aumentará a produção.

- Utilize APIs DE REPOUSO sempre que possível.

## <a name="throughput-on-linux-clients-is-significantly-lower-when-compared-to-windows-clients"></a>A produção de clientes Linux é significativamente menor quando comparada com os clientes windows.

### <a name="cause"></a>Causa

Esta é uma questão conhecida com a implementação do cliente SMB no Linux.

### <a name="workaround"></a>Solução

- Espalhe a carga por vários VMs.
- No mesmo VM, utilize vários pontos de montagem com opção **nosharesock** e espalhe a carga por estes pontos de montagem.
- Em Linux, tente montar com a opção **nostrictsync** para evitar forçar o SMB a descarregar em cada chamada **fsync.** Para os Ficheiros Azure, esta opção não interfere com a consistência dos dados, mas pode resultar em metadados de ficheiros em atraso na listagem de diretórios (comando**l -l).** Consulta direta de metadados de ficheiro (comando**stat)** retornará os metadados de ficheiro mais atualizados.

## <a name="high-latencies-for-metadata-heavy-workloads-involving-extensive-openclose-operations"></a>Altas latências para metadados pesados cargas de trabalho envolvendo extensas operações abertas/próximas.

### <a name="cause"></a>Causa

Falta de apoio para arrendamentos de diretórios.

### <a name="workaround"></a>Solução

- Se possível, evite uma abertura/manípulo de fecho excessivo no mesmo diretório num curto espaço de tempo.
- Para os VMs Linux, aumente o tempo limite de cache de entrada de diretório especificando **actimeo= \<sec> ** como uma opção de montagem. Por padrão, é um segundo, então um valor maior como três ou cinco pode ajudar.
- Para os VMs Linux, atualize o núcleo para 4,20 ou mais.

## <a name="low-iops-on-centosrhel"></a>IOPS baixos no CentOS/RHEL

### <a name="cause"></a>Causa

A profundidade IO superior a uma não é suportada no CentOS/RHEL.

### <a name="workaround"></a>Solução

- Upgrade para CentOS 8 / RHEL 8.
- Mudar para Ubuntu.

## <a name="slow-file-copying-to-and-from-azure-files-in-linux"></a>Cópia lenta de ficheiros de e para ficheiros Azure em Linux

Se estiver a experimentar cópias lentas de ficheiros de e para ficheiros Azure, dê uma olhada na [cópia do ficheiro Slow de e para ficheiros Azure na](storage-troubleshoot-linux-file-connection-problems.md#slow-file-copying-to-and-from-azure-files-in-linux) secção Linux no guia de resolução de problemas do Linux.

## <a name="jitterysaw-tooth-pattern-for-iops"></a>Padrão nervoso/dente-de-serra para IOPS

### <a name="cause"></a>Causa

A aplicação do cliente excede consistentemente o IOPS de base. Atualmente, não existe um alisamento lateral de serviço da carga de pedido, por isso, se o cliente exceder o IOPS de base, será estrangulado pelo serviço. Esse estrangulamento pode resultar em que o cliente experimente um padrão de IOPS nervoso/dente-serra. Neste caso, o IOPS médio alcançado pelo cliente pode ser inferior ao IOPS de base.

### <a name="workaround"></a>Solução

- Reduza a carga de pedido da aplicação do cliente, para que a parte não seja estrangulada.
- Aumente a quota da parte para que a parte não seja estrangulada.

## <a name="excessive-directoryopendirectoryclose-calls"></a>Chamadas excessivas do DirectórioO/Diretório

### <a name="cause"></a>Causa

Se o número de chamadas Do DirectyOpen/DirectoryClose estiver entre as principais chamadas da API e não espera que o cliente esteja a fazer tantas chamadas, pode ser um problema com o antivírus instalado no VM do cliente Azure.

### <a name="workaround"></a>Solução

- Uma correção para este problema está disponível na [Atualização](https://support.microsoft.com/help/4052623/update-for-windows-defender-antimalware-platform)da Plataforma de abril para o Windows .

## <a name="file-creation-is-slower-than-expected"></a>A criação de ficheiros é mais lenta do que o esperado

### <a name="cause"></a>Causa

Cargas de trabalho que dependem da criação de um grande número de ficheiros não verão uma diferença substancial entre o desempenho das ações de ficheiros premium e as ações de ficheiros padrão.

### <a name="workaround"></a>Solução

- Nenhum.

## <a name="slow-performance-from-windows-81-or-server-2012-r2"></a>Desempenho lento do Windows 8.1 ou Do Servidor 2012 R2

### <a name="cause"></a>Causa

Maior do que o esperado, a latência acede aos Ficheiros Azure para cargas de trabalho intensivas de IO.

### <a name="workaround"></a>Solução

- Instale o [hotfix](https://support.microsoft.com/help/3114025/slow-performance-when-you-access-azure-files-storage-from-windows-8-1)disponível.

## <a name="how-to-create-an-alert-if-a-file-share-is-throttled"></a>Como criar um alerta se uma partilha de ficheiros for acelerada

1. Aceda à sua **conta de armazenamento** no portal **Azure.**
2. Na secção 'Monitorização', clique em **Alertas** e clique em **+ Nova regra de alerta**.
3. Clique **em Editar o recurso,** selecione o **tipo de recurso De ficheiro** para a conta de armazenamento e, em seguida, clique em **Fazer**. Por exemplo, se o nome da conta de armazenamento for contoso, selecione o recurso contoso/ficheiro.
4. Clique **em Selecionar Condição** para adicionar uma condição.
5. Verá uma lista de sinais suportados para a conta de armazenamento, selecione a métrica **de Transações.**
6. Na lâmina lógica de **sinal configurar,** clique no **nome Dimension** drop-down e selecione o tipo **de resposta**.
7. Clique nos valores de **dimensionamento** e selecione **SuccessWithThrottling** (para SMB) ou **ClientThrottlingError** (para REST).

  > [!NOTE]
  > Se o valor da dimensão SuccessWithThrottling ou ClientThrottlingError não estiver listado, isto significa que o recurso não foi estrangulado. Para adicionar o valor de dimensão, clique em **Adicionar valor personalizado** ao lado dos **valores** de Dimension para baixo, **digite SuccessWithThrottling** ou **ClientThrottlingError,** clique em **OK** e repita o passo #7.

8. Clique no **drop-down** do nome Dimension e selecione A partilha **de ficheiros**.
9. Clique nos **valores** de Dimension drop-down e selecione as ações de ficheiros em que pretende alertar.

  > [!NOTE]
  > Se a partilha de ficheiros for uma partilha de ficheiros padrão, selecione **Todos os valores atuais e futuros**. Os valores de dimensão não listam as ações de ficheiros porque as métricas por ação não estão disponíveis para ações de ficheiros padrão. Os alertas de estrangulamento para as ações de ficheiros padrão serão desencadeados se alguma parte do ficheiro dentro da conta de armazenamento for acelerada e o alerta não identificar qual a partilha de ficheiros que foi acelerada. Uma vez que as métricas por ação não estão disponíveis para ações de ficheiros padrão, a recomendação é ter uma ação de ficheiro por conta de armazenamento.

10. Defina os **parâmetros** de alerta (valor limiar, operador, granularidade de agregação e frequência de avaliação) e clique em **Fazer**.

  > [!TIP]
  > Se estiver a utilizar um limiar estático, o gráfico métrico pode ajudar a determinar um valor limiar razoável se a parte do ficheiro estiver atualmente a ser estrangulada. Se estiver a utilizar um limiar dinâmico, o gráfico métrico apresentará os limiares calculados com base em dados recentes.

11. Clique **em Selecionar grupo de ação** para adicionar um grupo de **ação** (e-mail, SMS, etc.) ao alerta, selecionando um grupo de ação existente ou criando um novo grupo de ação.
12. Preencha os **detalhes do Alerta** como o nome da regra de **alerta,** **descrição** e **severidade**.
13. Clique **em Criar regra de alerta** para criar o alerta.

Para saber mais sobre a configuração de alertas no Azure Monitor, consulte [a visão geral dos alertas no Microsoft Azure]( https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview).

## <a name="how-to-create-alerts-if-a-premium-file-share-is-trending-towards-being-throttled"></a>Como criar alertas se uma quota de ficheiro premium está a tendência para ser estrangulada

1. Aceda à sua **conta de armazenamento** no portal **Azure.**
2. Na secção 'Monitorização', clique em **Alertas** e clique em **+ Nova regra de alerta**.
3. Clique **em Editar o recurso,** selecione o **tipo de recurso De ficheiro** para a conta de armazenamento e, em seguida, clique em **Fazer**. Por exemplo, se o nome da conta de armazenamento for contoso, selecione o recurso contoso/ficheiro.
4. Clique **em Selecionar Condição** para adicionar uma condição.
5. Verá uma lista de sinais suportados para a conta de armazenamento, selecione a métrica **Egress.**

  > [!NOTE]
  > Tem de criar 3 alertas separados para ser alertado quando Ingress, Egress ou Transações excedem o limiar que definiu. Isto porque um alerta só é disparado quando todas as condições estão reunidas. Por isso, se colocar todas as condições num só alerta, só será alertado se Ingress, Egress e Transações excederem os seus valores-limite.

6. Rola para baixo. Clique no **drop-down** do nome Dimension e selecione A partilha **de ficheiros**.
7. Clique nos **valores** de Dimension drop-down e selecione as ações de ficheiros em que pretende alertar.
8. Defina os **parâmetros** de alerta (valor limiar, operador, granularidade de agregação e frequência de avaliação) e clique em **Fazer**.

  > [!NOTE]
  > As métricas de Egress, Ingress e Transactions são por minuto, embora sejam saídas, entradas e IOPS por segundo. (falar de granularidade agregação -> por minuto = mais barulhento por isso escolha um difuso) Por isso, por exemplo, se a sua saída a provisionada forctária for de 90 MiB/segundo e pretender que o seu limiar seja de 80% da saída a provisionada, deve selecionar os seguintes parâmetros de alerta: 75497472 para **o valor limiar,** superior ou igual ao **operador**, e uma média para o tipo **de agregação**. Dependendo do quão barulhento quer que o seu alerta seja, pode escolher quais os valores a selecionar para a granularidade agregação e frequência de avaliação. Por exemplo, se eu quiser que o meu alerta olhe para a entrada média durante o período de uma hora e eu quero que a minha regra de alerta seja executada a cada hora, eu escolheria 1 hora para **a granularidade agregação** e 1 hora para **a frequência de avaliação.**

9. Clique **em Selecionar grupo de ação** para adicionar um grupo de **ação** (e-mail, SMS, etc.) ao alerta, selecionando um grupo de ação existente ou criando um novo grupo de ação.
10. Preencha os **detalhes do Alerta** como o nome da regra de **alerta,** **descrição** e **severidade**.
11. Clique **em Criar regra de alerta** para criar o alerta.

  > [!NOTE]
  > Para ser notificado se a sua parte de ficheiro premium estiver perto de ser estrangulada devido a uma entrada prevista, siga os mesmos passos, exceto no passo 5, selecione a métrica **Ingress.**

  > [!NOTE]
  > Para ser notificado se a sua parte de ficheiro premium estiver perto de ser estrangulada devido ao IOPS provisionado, terá de efetos algumas alterações. No passo 5, selecione a métrica **de Transações.** Além disso, para o passo 10, a única opção para o **tipo de agregação** é total. Portanto, o valor limiar dependeria da granularidade de agregação selecionada. Por exemplo, se quisesse que o seu limiar fosse de 80% do IOPS de base a provisionado e selecionasse 1 hora para **a granularidade agregação,** o seu **valor-limiar** seria o seu IOPS de base (in bytes) x 0,8 x 3600. Além destas alterações, siga os mesmos passos acima indicados. 

Para saber mais sobre a configuração de alertas no Azure Monitor, consulte [a visão geral dos alertas no Microsoft Azure]( https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview).

## <a name="see-also"></a>Consulte também
* [Resolução de problemas Ficheiros Azure no Windows](storage-troubleshoot-windows-file-connection-problems.md)
* [Resolução de problemas Ficheiros Azure em Linux](storage-troubleshoot-linux-file-connection-problems.md)
* [Perguntas mais frequentes (FAQ) sobre os Ficheiros do Azure](storage-files-faq.md)
