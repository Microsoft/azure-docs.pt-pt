---
title: Monitorizar o seu dispositivo Edge de caixa de dados do Azure | Documentos da Microsoft
description: Descreve como utilizar o portal do Azure e a IU web local para monitorizar a sua caixa de dados do Azure Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: 4d4cc8a3eaf2bf806d5b265be226482b23804f82
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/15/2019
ms.locfileid: "59580377"
---
# <a name="monitor-your-azure-data-box-edge"></a>Monitorizar o seu limite de caixa de dados do Azure

Este artigo descreve como monitorizar a sua caixa de dados do Azure Edge. Para monitorizar o seu dispositivo, pode utilizar o portal do Azure ou da web local da interface do Usuário. Utilize o portal do Azure para ver eventos de dispositivos, configurar e gerir alertas e ver métricas. Utilize a IU web local no seu dispositivo físico para ver o estado de hardware dos vários componentes do dispositivo.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Ver eventos de dispositivo e os alertas correspondentes
> * Ver o estado de hardware de componentes de dispositivo
> * Ver as métricas de capacidade e a transação para o seu dispositivo
> * Configurar e gerir alertas

## <a name="view-device-events"></a>Ver eventos do dispositivo

Siga os passos seguintes no portal do Azure para ver um evento de dispositivo.

1. No portal do Azure, aceda a extremidade da caixa de dados / recurso do Gateway de caixa de dados e, em seguida, aceda a **monitorização > eventos de dispositivo**.
2. Selecione um evento e ver os detalhes do alerta. Tome as medidas adequadas para resolver a condição do alerta.

    ![Selecione o evento e a vista de detalhes](media/data-box-edge-monitor/view-device-events.png)

## <a name="view-hardware-status"></a>Ver o estado do hardware

Siga os passos seguintes na IU para ver o estado de hardware de seus componentes de dispositivo da web local. Estas informações só estão disponíveis para um dispositivo de limite de caixa de dados.

1. Ligue-se para a web local da interface do Usuário do seu dispositivo.
2. Aceda a **manutenção > Estado do Hardware**. Pode ver o estado de funcionamento dos vários componentes do dispositivo.

    ![Ver o estado do hardware](media/data-box-edge-monitor/view-hardware-status.png)

## <a name="view-metrics"></a>Ver métricas

Também pode ver as métricas para monitorizar o desempenho do dispositivo e, em alguns casos, para resolver problemas de dispositivos.

Siga os passos seguintes no portal do Azure para criar um gráfico de métricas de dispositivo selecionado.

1. Para o seu recurso no portal do Azure, aceda a **monitorização > métricas** e selecione **adicionar métrica**.

    ![Adicionar métrica](media/data-box-edge-monitor/view-metrics-1.png)

2. O recurso é preenchido automaticamente.  

    ![Recurso atual](media/data-box-edge-monitor/view-metrics-2.png)

    Para especificar outro recurso, selecione o recurso. No **selecionar um recurso** painel, selecione a subscrição, grupo de recursos, tipo de recurso e o recurso específico para o qual pretende mostrar as métricas e selecione **aplicar**.

    ![Escolha outro recurso](media/data-box-edge-monitor/view-metrics-3.png)

3. Na lista pendente, selecione uma métrica para monitorizar o seu dispositivo. As métricas podem ser **métricas de capacidade** ou **métricas de transação**. As métricas de capacidade relacionados com a capacidade do dispositivo. As métricas de transação estão relacionadas com a leitura e escrita para armazenamento do Azure.

    |Métricas de capacidade                     |Descrição  |
    |-------------------------------------|-------------|
    |**Capacidade disponível**               | Refere-se ao tamanho dos dados que podem ser escritos para o dispositivo. Em outras palavras, esta é a capacidade de que pode ser disponibilizada no dispositivo. <br></br>Pode libertar a capacidade de dispositivo ao eliminar a cópia local dos ficheiros que têm uma cópia tanto o dispositivo, bem como a cloud.        |
    |**Capacidade total**                   | Refere-se no dispositivo para escrever dados para o total de bytes. Isto é também referido como o tamanho total da local cache. <br></br> Agora pode aumentar a capacidade de um dispositivo virtual existente ao adicionar um disco de dados. Adicionar um disco de dados por meio do gerenciamento de hipervisor para a VM e, em seguida, reinicie a VM. O agrupamento de armazenamento local do dispositivo de Gateway será expandido para acomodar o disco de dados adicionados recentemente. <br></br>Para obter mais informações, aceda a [adicionar um disco rígido para a máquina virtual de Hyper-V](https://www.youtube.com/watch?v=EWdqUw9tTe4). |
    
    |Métricas de transação              | Descrição         |
    |-------------------------------------|---------|
    |**Bytes carregados (dispositivos) da cloud**    | Soma de todos os bytes carregados de todas as partilhas no seu dispositivo        |
    |**Bytes carregados (compartilhamento) da cloud**     | Bytes carregados por partilha. Isto pode ser: <br></br> Média, o que é o (soma de todos os bytes carregados por partilha / número de partilhas),  <br></br>Max, o que é o número máximo de bytes carregados a partir de uma partilha <br></br>Min, o que é o número mínimo de bytes carregados a partir de uma partilha      |
    |**Débito de transferência de cloud (partilha)**| Bytes transferidos por partilha. Isto pode ser: <br></br> Média, o que é o (soma de todos os bytes de leitura ou transferidos para uma partilha / número de partilhas de) <br></br> Max, o que é o número máximo de bytes transferidos a partir de uma partilha<br></br> e Min, o que é o número mínimo de bytes transferidos a partir de uma partilha  |
    |**Débito de leitura de cloud**            | Soma de todos os bytes ler a partir da cloud em todas as partilhas no seu dispositivo     |
    |**Débito de carregamento de cloud**          | Soma de todos os bytes escritos para a cloud em todas as partilhas no seu dispositivo     |
    |**Débito de carregamento de cloud (partilha)**  | Soma de todos os bytes escritos para a cloud a partir de uma partilha / n. º de partilhas é média, máximo e mínimo por partilha      |
    |**Débito de leitura (rede)**           | Inclui o débito de rede do sistema para todos os bytes lidos a partir da cloud. Esta vista pode incluir dados que não está limitados a partilhas. <br></br>A divisão mostrará o tráfego ao longo de todos os adaptadores de rede no dispositivo. Isto inclui placas que não estão ligadas ou ativadas.      |
    |**Escrever o débito (rede)**       | Inclui o débito de rede do sistema para todos os bytes escritos para a cloud. Esta vista pode incluir dados que não está limitados a partilhas. <br></br>A divisão mostrará o tráfego ao longo de todos os adaptadores de rede no dispositivo. Isto inclui placas que não estão ligadas ou ativadas.          |
    |**Computação do Edge - utilização de memória**      | Utilização da memória para o dispositivo do IoT Edge para a extremidade da caixa de dados. Se vir uma elevada utilização e o desempenho do dispositivo é afetado pelas cargas de trabalho atuais que tenha implementado, entre em contato com Support da Microsoft para determinar os passos seguintes. <br></br>Esta métrica não está preenchida para o Gateway de caixa de dados.          |
    |**Computação do Edge - percentagem da CPU**    | Utilização da CPU para o dispositivo do IoT Edge para a extremidade da caixa de dados. Se vir uma elevada utilização e o desempenho do dispositivo é afetado pelas cargas de trabalho atuais que tenha implementado, entre em contato com Support da Microsoft para determinar os passos seguintes. <br></br>Esta métrica não está preenchida para o Gateway de caixa de dados.        |
1. Quando uma métrica é selecionada na lista pendente, agregação também pode ser definida. Agregação refere-se para o real valor agregado ao longo de um intervalo especificado de tempo. Os valores agregados podem ser média, mínimo ou o valor máximo. Selecione a agregação de média, máximo ou mínimo.

    ![Gráfico de modo de exibição](media/data-box-edge-monitor/view-metrics-4.png)

5. Se a métrica que selecionou tem várias instâncias, em seguida, a opção de divisão está disponível. Selecione **aplicam-se de que a divisão** e, em seguida, selecione o valor pelo qual pretende ver a divisão.

    ![Aplicar divisão](media/data-box-edge-monitor/view-metrics-5.png)

6. Se pretender agora ver a divisão apenas para algumas instâncias, pode filtrar os dados. Por exemplo, neste caso, se quiser ver o débito de rede apenas para as duas interfaces de rede ligada no seu dispositivo, poderia filtrar essas interfaces. Selecione **Adicionar filtro** e especifique o nome de interface de rede para filtragem.

    ![Adicionar filtro](media/data-box-edge-monitor/view-metrics-6.png)

7. Também pode afixar o gráfico ao dashboard para facilitar o acesso.

    ![Afixar ao dashboard](media/data-box-edge-monitor/view-metrics-7.png)

8. Para exportar os dados de gráfico numa planilha do Excel ou obter uma ligação para o gráfico que pode partilhar, selecione a opção de partilha na barra de comandos.

    ![Exportar dados](media/data-box-edge-monitor/view-metrics-8.png)

## <a name="manage-alerts"></a>Gerir alertas

Configure regras de alerta para o informar de condições de alerta relacionados com o consumo de recursos no seu dispositivo. Pode configurar regras de alerta para monitorizar o seu dispositivo para condições de alerta. Para obter mais informações sobre alertas, aceda a [criar, ver e gerir alertas de métricas no Azure monitor](../azure-monitor/platform/alerts-metric.md).

## <a name="next-steps"></a>Passos Seguintes 

Saiba como [Gerir a largura de banda](data-box-edge-manage-bandwidth-schedules.md).