---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: e02c0b86cd542b3ea12914e35a6577cf4e9b43d8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66161323"
---
Também pode ver as métricas para monitorizar o desempenho do dispositivo e, em alguns casos, para resolver problemas de dispositivos.

Siga os passos seguintes no portal do Azure para criar um gráfico de métricas de dispositivo selecionado.

1. Para o seu recurso no portal do Azure, aceda a **monitorização > métricas** e selecione **adicionar métrica**.

    ![Adicionar métrica](media/data-box-edge-gateway-view-metrics/view-metrics-1.png)

2. O recurso é preenchido automaticamente.  

    ![Recurso atual](media/data-box-edge-gateway-view-metrics/view-metrics-2.png)

    Para especificar outro recurso, selecione o recurso. No **selecionar um recurso** painel, selecione a subscrição, grupo de recursos, tipo de recurso e o recurso específico para o qual pretende mostrar as métricas e selecione **aplicar**.

    ![Escolha outro recurso](media/data-box-edge-gateway-view-metrics/view-metrics-3.png)

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
    |**Computação do Edge - utilização de memória**      | Esta métrica não é aplicável para o Gateway de caixa de dados e, por conseguinte, não é preenchida.          |
    |**Computação do Edge - percentagem da CPU**    | Esta métrica não é aplicável para o Gateway de caixa de dados e, por conseguinte, não é preenchida.         |

4. Quando uma métrica é selecionada na lista pendente, agregação também pode ser definida. Agregação refere-se para o real valor agregado ao longo de um intervalo especificado de tempo. Os valores agregados podem ser média, mínimo ou o valor máximo. Selecione a agregação de média, máximo ou mínimo.

    ![Gráfico de modo de exibição](media/data-box-edge-gateway-view-metrics/view-metrics-4.png)

5. Se a métrica que selecionou tem várias instâncias, em seguida, a opção de divisão está disponível. Selecione **aplicam-se de que a divisão** e, em seguida, selecione o valor pelo qual pretende ver a divisão.

    ![Aplicam-se de que a divisão](media/data-box-edge-gateway-view-metrics/view-metrics-5.png)

6. Se pretender agora ver a divisão apenas para algumas instâncias, pode filtrar os dados. Por exemplo, neste caso, se quiser ver o débito de rede apenas para as duas interfaces de rede ligada no seu dispositivo, poderia filtrar essas interfaces. Selecione **Adicionar filtro** e especifique o nome de interface de rede para filtragem.

    ![Adicionar filtro](media/data-box-edge-gateway-view-metrics/view-metrics-6.png)

7. Também pode afixar o gráfico ao dashboard para facilitar o acesso.

    ![Afixar ao dashboard](media/data-box-edge-gateway-view-metrics/view-metrics-7.png)

8. Para exportar os dados de gráfico numa planilha do Excel ou obter uma ligação para o gráfico que pode partilhar, selecione a opção de partilha na barra de comandos.

    ![Exportar dados](media/data-box-edge-gateway-view-metrics/view-metrics-8.png)