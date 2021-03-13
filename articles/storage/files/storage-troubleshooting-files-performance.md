---
title: Azure file partilha guia de resolução de problemas de desempenho
description: Resolução de problemas conhecidos problemas de desempenho com ações de ficheiros Azure. Descubra potenciais causas e soluções alternativas associadas quando estes problemas são encontrados.
author: gunjanj
ms.service: storage
ms.topic: troubleshooting
ms.date: 11/16/2020
ms.author: gunjanj
ms.subservice: files
ms.openlocfilehash: 9f858549f36d196c6412aec549d0ab2e2d864145
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2021
ms.locfileid: "103417676"
---
# <a name="troubleshoot-azure-file-shares-performance-issues"></a>Resolução de problemas Azure partilha problemas de desempenho

Este artigo enumera alguns problemas comuns relacionados com as ações de ficheiros Azure. Fornece potenciais causas e soluções alternativas para quando se depara com estes problemas.

## <a name="high-latency-low-throughput-and-general-performance-issues"></a>Altas latências, baixo rendimento e problemas gerais de desempenho

### <a name="cause-1-share-was-throttled"></a>Causa 1: A partilha foi acelerada

Os pedidos são acelerados quando as operações de E/S por segundo (IOPS), ingresss ou limites de saída para uma partilha de ficheiros são atingidos. Para compreender os limites das ações de ficheiros standard e premium, consulte [os alvos de ações de ficheiros e de escala de ficheiros.](./storage-files-scale-targets.md#azure-file-share-scale-targets)

Para confirmar se a sua parte está a ser acelerada, pode aceder e utilizar métricas Azure no portal.

1. No portal Azure, vá à sua conta de armazenamento.

1. No painel esquerdo, em **Monitorização,** selecione **Métricas**.

1. Selecione **File** como o espaço de nome métrico para o seu âmbito de conta de armazenamento.

1. Selecione **Transações** como métrica.

1. Adicione um filtro para **o tipo de resposta** e, em seguida, verifique se os pedidos foram estrangulados. 

    Para as ações de ficheiros padrão, os seguintes tipos de resposta são registados se um pedido for acelerado:

    - SucessoWithThrottling
    - SucessoComPartiIopsThrottling
    - ClientShareIopsThrottlingError

    Para ações de ficheiros premium, os seguintes tipos de resposta são registados se um pedido for acelerado:

    - SucessoComParteEgressThrottling
    - SucessoWithShareIngressThrottling
    - SucessoComPartiIopsThrottling
    - ClientShareEgressThrottlingError
    - ClientShareIngressThrottlingError
    - ClientShareIopsThrottlingError

    Para saber mais sobre cada tipo de resposta, consulte [as dimensões métricas.](./storage-files-monitoring-reference.md#metrics-dimensions)

    ![Screenshot das opções de métricas para ações de ficheiros premium, mostrando um filtro de propriedade "Tipo resposta".](media/storage-troubleshooting-premium-fileshares/metrics.png)

    > [!NOTE]
    > Para receber um alerta, consulte a secção ["Como criar um alerta se uma partilha de ficheiros for estrangulada"](#how-to-create-an-alert-if-a-file-share-is-throttled) mais tarde neste artigo.

### <a name="solution"></a>Solução

- Se estiver a utilizar uma parte de ficheiro padrão, ative [grandes ações de ficheiros](./storage-files-how-to-create-large-file-share.md?tabs=azure-portal) na sua conta de armazenamento. As grandes ações suportam até 10.000 IOPS por ação.
- Se estiver a utilizar uma parte de ficheiro premium, aumente o tamanho da ação de ficheiros for provisionada para aumentar o limite do IOPS. Para saber mais, consulte o [Provisioning Understanding para ações de ficheiros premium.](./understanding-billing.md#provisioned-model)

### <a name="cause-2-metadata-or-namespace-heavy-workload"></a>Causa 2: Metadados ou carga de trabalho pesada do espaço de nome

Se a maioria dos seus pedidos forem centrados em metadados (tais como o ficheiro de criação, o ficheiro aberto, o ficheiro de encerramento, a consulta ou a consulta), a latência será pior do que a das operações de leitura/escrita.

Para determinar se a maioria dos seus pedidos são centrados em metadados, comece por seguir os passos 1-4 como previamente descrito na Causa 1. Para o passo 5, em vez de adicionar um filtro para **o tipo resposta,** adicione um filtro de propriedade para **o nome API**.

![Screenshot das opções de métricas para ações de ficheiros premium, mostrando um filtro de propriedade "Nome API".](media/storage-troubleshooting-premium-fileshares/MetadataMetrics.png)

### <a name="workaround"></a>Solução

- Verifique se a aplicação pode ser modificada para reduzir o número de operações de metadados.
- Adicione um disco rígido virtual (VHD) na partilha de ficheiros e monte o VHD sobre SMB do cliente para realizar operações de ficheiros contra os dados. Esta abordagem funciona para cenários de escritores individuais e múltiplos leitores e permite que as operações de metadados sejam locais. A configuração oferece um desempenho semelhante ao de um armazenamento local ligado diretamente.

### <a name="cause-3-single-threaded-application"></a>Causa 3: Aplicação de rosca única

Se a aplicação que está a utilizar for de rosca única, esta configuração pode resultar numa produção IOPS significativamente mais baixa do que a produção máxima possível, dependendo do tamanho da sua ação aprovisionada.

### <a name="solution"></a>Solução

- Aumente o paralelismo de aplicação aumentando o número de fios.
- Mude para aplicações onde o paralelismo é possível. Por exemplo, para operações de cópia, pode utilizar a AzCopy ou RoboCopy a partir de clientes Windows ou o comando **paralelo** dos clientes Linux.

## <a name="very-high-latency-for-requests"></a>Latência muito alta para pedidos

### <a name="cause"></a>Causa

A máquina virtual do cliente (VM) poderia estar localizada numa região diferente da partilha de ficheiros. Outra razão para a alta latência pode ser devido à latência causada pelo cliente ou pela rede.

### <a name="solution"></a>Solução

- Executar o pedido a partir de um VM que está localizado na mesma região que a partilha de ficheiros.
- Para a sua conta de armazenamento, reveja as métricas de transação **SuccessE2ELatency** e  **SuccessServerLatency** via **Azure Monitor** no portal Azure. Uma grande diferença entre os valores de métricas SuccessE2ELatency e SuccessServerLatency é uma indicação de latência que é provavelmente causada pela rede ou pelo cliente. Consulte [as métricas de transação](storage-files-monitoring-reference.md#transaction-metrics) em Azure Files Monitoring data reference.

## <a name="client-unable-to-achieve-maximum-throughput-supported-by-the-network"></a>Cliente incapaz de atingir a produção máxima suportada pela rede

### <a name="cause"></a>Causa
Uma das causas potenciais é a falta de suporte smb multicanal para ações de ficheiros padrão. Atualmente, o Azure Files suporta apenas um único canal, pelo que existe apenas uma ligação do VM do cliente ao servidor. Esta ligação única é marcada para um único núcleo no VM do cliente, de modo que a produção máxima alcançável a partir de um VM é ligada por um único núcleo.

### <a name="workaround"></a>Solução

- Para ações de ficheiros premium, [Ative o SMB Multichannel numa conta FileStorage](storage-files-enable-smb-multichannel.md).
- Obter um VM com um núcleo maior pode ajudar a melhorar a produção.
- Executar a aplicação do cliente a partir de vários VMs aumentará a produção.
- Utilize APIs DE REPOUSO sempre que possível.

## <a name="throughput-on-linux-clients-is-significantly-lower-than-that-of-windows-clients"></a>A produção de clientes Linux é significativamente inferior à dos clientes windows

### <a name="cause"></a>Causa

Esta é uma questão conhecida com a implementação do cliente SMB no Linux.

### <a name="workaround"></a>Solução

- Espalhe a carga por vários VMs.
- No mesmo VM, utilize vários pontos de montagem com uma opção **de não partilha** e espalhe a carga por estes pontos de montagem.
- Em Linux, tente montar com uma opção **de nostrictsync** para evitar forçar um flush SMB em cada chamada **fsync.** Para os Ficheiros Azure, esta opção não interfere com a consistência dos dados, mas pode resultar em metadados de ficheiros antigos nas listas de diretórios (comando **ls -l).** Consultando diretamente metadados de ficheiros utilizando o comando **stat** irá retornar os metadados de ficheiros mais atualizados.

## <a name="high-latencies-for-metadata-heavy-workloads-involving-extensive-openclose-operations"></a>Altas latências para cargas de trabalho pesadas de metadados que envolvam extensas operações abertas/próximas

### <a name="cause"></a>Causa

Falta de apoio para arrendamentos de diretórios.

### <a name="workaround"></a>Solução

- Se possível, evite utilizar uma pega de abertura/fecho excessiva no mesmo diretório num curto espaço de tempo.
- Para os VMs Linux, aumente o tempo limite de cache de entrada de diretório especificando **actimeo= \<sec>** como uma opção de montagem. Por predefinição, o tempo limite é de 1 segundo, pelo que um valor maior, como 3 ou 5 segundos, pode ajudar.
- Para VMs CentOS Linux ou Red Hat Enterprise Linux (RHEL), atualize o sistema para CentOS Linux 8.2 ou RHEL 8.2. Para outros VMs Linux, atualize o núcleo para 5.0 ou mais tarde.

## <a name="low-iops-on-centos-linux-or-rhel"></a>IOPS baixos no CentOS Linux ou RHEL

### <a name="cause"></a>Causa

Uma profundidade de I/O superior a 1 não é suportada no CentOS Linux ou RHEL.

### <a name="workaround"></a>Solução

- Upgrade para CentOS Linux 8 ou RHEL 8.
- Mudar para Ubuntu.

## <a name="slow-file-copying-to-and-from-azure-file-shares-in-linux"></a>Cópia lenta de ficheiros de e para ações de ficheiros Azure no Linux

Se estiver a experimentar uma cópia lenta de ficheiros, consulte a secção "Cópia lenta de ficheiros de e para a Azure na secção Linux" no [guia de resolução de problemas](storage-troubleshoot-linux-file-connection-problems.md#slow-file-copying-to-and-from-azure-files-in-linux)do Linux .

## <a name="jittery-or-sawtooth-pattern-for-iops"></a>Padrão nervoso ou dente de serra para IOPS

### <a name="cause"></a>Causa

A aplicação do cliente excede consistentemente o IOPS de base. Atualmente, não há um alisamento do lado do serviço da carga de pedido. Se o cliente exceder o IOPS de base, será estrangulado pelo serviço. O estrangulamento pode resultar em que o cliente experimente um padrão de IOPS nervoso ou dente de serra. Neste caso, o IOPS médio alcançado pelo cliente pode ser inferior ao IOPS de base.

### <a name="workaround"></a>Solução
- Reduza a carga de pedido da aplicação do cliente, para que a parte não seja estrangulada.
- Aumente a quota da parte para que a parte não seja estrangulada.

## <a name="excessive-directoryopendirectoryclose-calls"></a>Chamadas excessivas do DirectórioO/Diretório

### <a name="cause"></a>Causa

Se o número de chamadas **DirectoryOpen/DirectoryClose** estiver entre as principais chamadas da API e não esperar que o cliente faça tantas chamadas, o problema pode ser causado pelo software antivírus instalado no VM do cliente Azure.

### <a name="workaround"></a>Solução

- Uma correção para este problema está disponível na [Atualização](https://support.microsoft.com/help/4052623/update-for-windows-defender-antimalware-platform)da Plataforma de abril para o Windows .

## <a name="file-creation-is-slower-than-expected"></a>A criação de ficheiros é mais lenta do que o esperado

### <a name="cause"></a>Causa

Cargas de trabalho que dependem da criação de um grande número de ficheiros não verão uma diferença substancial no desempenho entre ações de ficheiros premium e ações de ficheiros padrão.

### <a name="workaround"></a>Solução

- Nenhum.

## <a name="slow-performance-from-windows-81-or-server-2012-r2"></a>Desempenho lento do Windows 8.1 ou Do Servidor 2012 R2

### <a name="cause"></a>Causa

Maior do que o esperado, a latência acede às ações de ficheiros Azure para cargas de trabalho intensivas de I/O.

### <a name="workaround"></a>Solução

- Instale o [hotfix](https://support.microsoft.com/help/3114025/slow-performance-when-you-access-azure-files-storage-from-windows-8-1)disponível.

## <a name="smb-multichannel-option-not-visible-under-file-share-settings"></a>Opção SMB Multicanal não é visível nas definições de partilha de ficheiros. 

### <a name="cause"></a>Causa

Ou a subscrição não está registada para a funcionalidade, ou a região e o tipo de conta não são suportados.

### <a name="solution"></a>Solução

Certifique-se de que a sua subscrição está registada para a funcionalidade SMB Multichannel. Ver [Começar](storage-files-enable-smb-multichannel.md#getting-started) Certifique-se de que o tipo de conta é FileStorage (conta de ficheiro premium) na página geral da conta. 

## <a name="smb-multichannel-is-not-being-triggered"></a>O SMB Multicanal não está a ser acionado.

### <a name="cause"></a>Causa

Alterações recentes nas definições de configuração de config multicanais SMB sem remontagem.

### <a name="solution"></a>Solução
 
-   Depois de quaisquer alterações nas definições de configuração multicanal do Cliente do Windows SMB ou conta SMB, tem de desmontar a partilha, esperar 60 segundos e voltar a montar a ação para ativar o multicanal.
-   Para o cliente do Windows OS, gere a carga IO com alta profundidade de fila, por exemplo QD=8, por exemplo, copiando um ficheiro para ativar o SMB Multichannel.  Para o servidor OS, o SMB Multichannel é ativado com QD=1, o que significa que assim que iniciar qualquer IO na partilha.

## <a name="high-latency-on-web-sites-hosted-on-file-shares"></a>Alta latência em sites hospedados em ações de ficheiros 

### <a name="cause"></a>Causa  

A notificação de alteração de ficheiros de um número elevado nas ações de ficheiros pode resultar em elevadas latências significativas. Isto ocorre tipicamente com sites hospedados em partilhas de ficheiros com estrutura de diretório aninhado profundo. Um cenário típico é a aplicação web hospedada do IIS onde a notificação de alteração de ficheiros é configurada para cada diretório na configuração predefinida. Cada alteração[(ReadDirectoryChangesW)](/windows/win32/api/winbase/nf-winbase-readdirectorychangesw)sobre a ação que o cliente SMB está registado para empurrar uma notificação de alteração do serviço de ficheiros para o cliente, que requer recursos do sistema, e emite piora com o número de alterações. Isto pode causar estrangulamento de partilha e, assim, resultar em maior latência do lado do cliente. 

Para confirmar, pode utilizar a Azure Metrics no portal - 

1. No portal Azure, vá à sua conta de armazenamento. 
1. No menu esquerdo, em Monitorização, selecione Métricas. 
1. Selecione File como o espaço de nome métrico para o seu âmbito de conta de armazenamento. 
1. Selecione Transações como métrica. 
1. Adicione um filtro para o ResponseType e verifique se algum pedido tem um código de resposta de SuccessWithThrottling (para SMB) ou ClientThrottlingError (para REST).

### <a name="solution"></a>Solução 

- Se a notificação de alteração de ficheiro não for utilizada, desative a notificação de alteração de ficheiros (preferida).
    - [Desativar a notificação](https://support.microsoft.com/help/911272/fix-asp-net-2-0-connected-applications-on-a-web-site-may-appear-to-sto) de alteração de ficheiros atualizando a FCNMode. 
    - Atualize o intervalo de votação do IIS Worker Process (W3WP) para 0, definindo `HKEY_LOCAL_MACHINE\System\CurrentControlSet\Services\W3SVC\Parameters\ConfigPollMilliSeconds ` o seu registo e reinicie o processo W3WP. Para saber mais sobre esta definição, consulte [as teclas de registo comuns que são utilizadas por muitas partes do IIS](/troubleshoot/iis/use-registry-keys#registry-keys-that-apply-to-iis-worker-process-w3wp).
- Aumente a frequência do intervalo de votação da notificação de alteração de ficheiros para reduzir o volume.
    - Atualize o intervalo de votação do processo do trabalhador W3WP para um valor mais elevado (por exemplo, 10 mins ou 30mins) com base na sua exigência. Desace no `HKEY_LOCAL_MACHINE\System\CurrentControlSet\Services\W3SVC\Parameters\ConfigPollMilliSeconds ` [seu registo](/troubleshoot/iis/use-registry-keys#registry-keys-that-apply-to-iis-worker-process-w3wp) e reinicie o processo W3WP.
- Se o diretório físico mapeado do seu site tiver uma estrutura de diretório aninhada, pode tentar limitar o âmbito da notificação de alteração de ficheiros para reduzir o volume de notificação. Por padrão, o IIS utiliza a configuração a partir de ficheiros Web.config no diretório físico para o qual o diretório virtual está mapeado, bem como em quaisquer diretórios infantis nesse diretório físico. Se não pretender utilizar ficheiros Web.config em diretórios infantis, especifique falso para o atributo allowSubDirConfig no diretório virtual. Mais detalhes podem ser encontrados [aqui.](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) 
    - Definir o diretório virtual do IIS "permitir que o SuubDirConfig" se ajuste em Web.Config *falso* para excluir do âmbito de aplicação os diretórios físicos de crianças mapeados.  

## <a name="how-to-create-an-alert-if-a-file-share-is-throttled"></a>Como criar um alerta se uma partilha de ficheiros for acelerada

1. Aceda à sua **conta de armazenamento** no portal **Azure.**
2. Na secção **de Monitorização,** clique em **Alertas** e, em seguida, clique **em + Nova regra de alerta**.
3. Clique **em Editar o recurso,** selecione o **tipo de recurso De ficheiro** para a conta de armazenamento e, em seguida, clique em **Fazer**. Por exemplo, se o nome da conta de armazenamento `contoso` for, selecione o `contoso/file` recurso.
4. Clique **em Adicionar condição** para adicionar uma condição.
5. Verá uma lista de sinais suportados para a conta de armazenamento, selecione a métrica **de Transações.**
6. Na lâmina lógica de **sinal configurar,** clique no **nome Dimension** drop-down e selecione o tipo **de resposta**.
7. Clique nos **valores** de Dimension drop-down e selecione os tipos de resposta adequados para a sua partilha de ficheiros.

    Para ações de ficheiros padrão, selecione os seguintes tipos de resposta:

    - SucessoWithThrottling
    - SucessoComPartiIopsThrottling
    - ClientShareIopsThrottlingError

    Para ações de ficheiros premium, selecione os seguintes tipos de resposta:

    - SucessoComParteEgressThrottling
    - SucessoWithShareIngressThrottling
    - SucessoComPartiIopsThrottling
    - ClientShareEgressThrottlingError
    - ClientShareIngressThrottlingError
    - ClientShareIopsThrottlingError

   > [!NOTE]
   > Se os tipos de resposta não estiverem listados na redução dos valores de **dimensão,** isto significa que o recurso não foi estrangulado. Para adicionar os valores de dimensão, junto à lista de valores de **Dimensão,** selecione **Adicionar valor personalizado,** introduzir o tipo de respone (por exemplo, **SuccessWithThrottling),** selecionar **OK**, e, em seguida, repetir estes passos para adicionar todos os tipos de resposta aplicáveis para a sua partilha de ficheiros.

8. Para **ações de ficheiros premium,** clique no **nome Dimension** drop-down e selecione File **Share**. Para **ações de ficheiros padrão**, salte para **o passo #10**. .

   > [!NOTE]
   > Se a partilha de ficheiros for uma partilha de ficheiros padrão, a dimensão de Partilha de **Ficheiros** não listará a(s) partilha de ficheiros porque as métricas por partilha não estão disponíveis para ações de ficheiros padrão. Os alertas de estrangulamento para as ações de ficheiros padrão serão desencadeados se alguma parte do ficheiro dentro da conta de armazenamento for acelerada e o alerta não identificar qual a partilha de ficheiros que foi acelerada. Uma vez que as métricas por ação não estão disponíveis para ações de ficheiros padrão, a recomendação é ter uma ação de ficheiro por conta de armazenamento.

9. Clique nos **valores** de Dimension drop-down e selecione as ações de ficheiros em que pretende alertar.
10. Defina os **parâmetros** de alerta (valor limiar, operador, granularidade de agregação e frequência de avaliação) e clique em **Fazer**.

    > [!TIP]
    > Se estiver a utilizar um limiar estático, o gráfico métrico pode ajudar a determinar um valor limiar razoável se a parte do ficheiro estiver atualmente a ser estrangulada. Se estiver a utilizar um limiar dinâmico, o gráfico métrico apresentará os limiares calculados com base em dados recentes.

11. Clique **em Adicionar grupos de ação** para adicionar um grupo de **ação** (e-mail, SMS, etc.) ao alerta, selecionando um grupo de ação existente ou criando um novo grupo de ação.
12. Preencha os **detalhes do Alerta** como o nome da regra de **alerta,** **descrição** e **severidade**.
13. Clique **em Criar regra de alerta** para criar o alerta.

Para saber mais sobre a configuração de alertas no Azure Monitor, consulte [a visão geral dos alertas no Microsoft Azure]( https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview).

## <a name="how-to-create-alerts-if-a-premium-file-share-is-trending-toward-being-throttled"></a>Como criar alertas se uma quota de ficheiro premium está a tendência para ser estrangulada

1. No portal Azure, vá à sua conta de armazenamento.
2. Na secção **de Monitorização,** selecione **Alertas** e, em seguida, selecione **Nova regra de alerta**.
3. **Selecione o recurso Editar,** selecione o **tipo de recurso De ficheiro** para a conta de armazenamento e, em seguida, selecione **'Fazer'** Por exemplo, se o nome da conta de armazenamento for *contoso,* selecione o recurso contoso/ficheiro.
4. **Selecione Selecione Condição** para adicionar uma condição.
5. Na lista de sinais suportados para a conta de armazenamento, selecione a métrica **Egress.**

   > [!NOTE]
   > Tem de criar três alertas separados para ser alertado quando a entrada, a saída ou os valores de transação excederem os limiares definidos. Isto porque um alerta só é desencadeado quando todas as condições são satisfeitas. Por exemplo, se colocar todas as condições num só alerta, só será alertado se entrar, entrar e transações excederem os seus valores-limite.

6. Rola para baixo. Na lista de drop-down de **nome Dimension,** selecione **'Partilhar ficheiros'.**
7. Na lista de valores de **Dimensão,** selecione a partilha de ficheiros ou as ações que pretende alertar.
8. Defina os parâmetros de alerta selecionando valores no **Operador**, **Valor limiar,** **granularidade agregação** e frequência das listas **de redução de avaliação** e, em seguida, selecione **Fazer**.

   As métricas de egress, entradas e transações são expressas por minuto, embora sejam agrestes, entradas e I/O por segundo. Portanto, por exemplo, se a sua saída a provisionada forisso de 90 &nbsp; mebibytes por segundo (MiB/s) e pretender que o seu limiar seja de 80% &nbsp; das saídas a provisionadas, selecione os seguintes parâmetros de alerta: 
   - Por **valor limiar:** *75497472* 
   - Para **o operador:** *maior ou igual a*
   - Para **o tipo de agregação:** *média*
   
   Dependendo do quão barulhento quer que o seu alerta seja, também pode selecionar valores para **granularidade agregação** e **frequência de avaliação.** Por exemplo, se quiser que o seu alerta olhe para a entrada média durante o período de 1 hora, e deseja que a sua regra de alerta seja executada a cada hora, selecione o seguinte:
   - Para **granularidade agregação**: *1 hora*
   - Para **frequência de avaliação:** *1 hora*

9. **Selecione Adicionar grupos de ação**, e, em seguida, adicionar um grupo de ação (por exemplo, e-mail ou SMS) ao alerta, quer selecionando um grupo de ação existente, quer criando um novo.
10. Introduza os detalhes do alerta, tais como **o nome da regra de alerta,** **descrição** e **severidade**.
11. Selecione **Criar a regra de alerta** para criar o alerta.

    > [!NOTE]
    > - Para ser notificado de que a sua parte de ficheiro premium está perto de ser estrangulada devido a *ingresss,* siga as instruções anteriores, mas com a seguinte alteração:
    >    - No passo 5, selecione a métrica **Ingress** em vez de **Egress**.
    >
    > - Para ser notificado de que a sua parte de ficheiro premium está perto de ser estrangulada devido ao *IOPS provisionado,* siga as instruções anteriores, mas com as seguintes alterações:
    >    - No passo 5, selecione a métrica **de Transações** em vez de **Egress**.
    >    - No passo 10, a única opção para **o tipo de agregação** é *total.* Portanto, o valor limiar depende da granularidade de agregação selecionada. Por exemplo, se quiser que o seu limiar seja de 80% &nbsp; do IOPS de base a provisionado e selecione *1 hora* para **granularidade agregação,** o seu **valor threshold** seria o seu IOPS de base (in bytes) &times; &nbsp; 0,8 &times; &nbsp; 3600. 

Para saber mais sobre a configuração de alertas no Azure Monitor, consulte [a visão geral dos alertas no Microsoft Azure]( https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview).

## <a name="see-also"></a>Ver também
- [Resolução de problemas Ficheiros Azure no Windows](storage-troubleshoot-windows-file-connection-problems.md)  
- [Resolução de problemas Ficheiros Azure em Linux](storage-troubleshoot-linux-file-connection-problems.md)  
- [FAQ sobre Ficheiros do Azure](storage-files-faq.md)
