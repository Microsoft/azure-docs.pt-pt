---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: e02c0b86cd542b3ea12914e35a6577cf4e9b43d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67184710"
---
Também pode visualizar as métricas para monitorizar o desempenho do dispositivo e, em alguns casos, para problemas com dispositivos de resolução de problemas.

Dê os seguintes passos no portal Azure para criar um gráfico para métricas de dispositivos selecionadas.

1. Para o seu recurso no portal Azure, vá à **Monitorização > Métricas** e selecione **Adicionar métrica**.

    ![Adicionar métrica](media/data-box-edge-gateway-view-metrics/view-metrics-1.png)

2. O recurso é automaticamente povoado.  

    ![Recurso atual](media/data-box-edge-gateway-view-metrics/view-metrics-2.png)

    Para especificar outro recurso, selecione o recurso. **Selecione uma** lâmina de recurso, selecione a subscrição, o grupo de recursos, o tipo de recurso e o recurso específico para o qual pretende mostrar as métricas e selecionar **Apply**.

    ![Escolha outro recurso](media/data-box-edge-gateway-view-metrics/view-metrics-3.png)

3. A partir da lista de abandono, selecione uma métrica para monitorizar o seu dispositivo. As métricas podem ser **métricas de capacidade** ou métricas de **transação.** As métricas de capacidade estão relacionadas com a capacidade do dispositivo. As métricas de transação estão relacionadas com as operações de leitura e escrita para o Armazenamento Azure.

    |Métricas de capacidade                     |Descrição  |
    |-------------------------------------|-------------|
    |**Capacidade disponível**               | Refere-se ao tamanho dos dados que podem ser escritos ao dispositivo. Por outras palavras, esta é a capacidade que pode ser disponibilizada no dispositivo. <br></br>Pode libertar a capacidade do dispositivo apagando a cópia local dos ficheiros que têm uma cópia tanto no dispositivo como na nuvem.        |
    |**Capacidade total**                   | Refere-se aos bytes totais do dispositivo para escrever dados. Isto também é referido como o tamanho total da cache local. <br></br> Agora pode aumentar a capacidade de um dispositivo virtual existente adicionando um disco de dados. Adicione um disco de dados através da gestão do hipervisor para o VM e, em seguida, reinicie o seu VM. O conjunto de armazenamento local do dispositivo Gateway expandir-se-á para acomodar o disco de dados recém-adicionado. <br></br>Para mais informações, vá adicionar [um disco rígido para a máquina virtual Hyper-V](https://www.youtube.com/watch?v=EWdqUw9tTe4). |
    
    |Métricas de transação              | Descrição         |
    |-------------------------------------|---------|
    |**Bytes de nuvem carregados (dispositivo)**    | Soma de todos os bytes enviados em todas as ações do seu dispositivo        |
    |**Bytes de nuvem carregados (partilhar)**     | Bytes carregadopor ação. Este pode ser: <br></br> Avg, que é a (Soma de todos os bytes carregados por ação/ Número de ações),  <br></br>Max, que é o número máximo de bytes carregados a partir de uma ação <br></br>Min, que é o número mínimo de bytes carregados a partir de uma ação      |
    |**Entrada de download em nuvem (partilha)**| Bytes descarregados por ação. Este pode ser: <br></br> Avg, que é a (Soma de todos os bytes ler ou ser descarregado para uma parte / Número de ações) <br></br> Max, que é o número máximo de bytes descarregados de uma parte<br></br> e Min, que é o número mínimo de bytes descarregados de uma parte  |
    |**Cloud ler a entrada**            | Soma de todos os bytes lidos da nuvem em todas as ações do seu dispositivo     |
    |**Entrada de upload de nuvem**          | Soma de todos os bytes escritos à nuvem em todas as ações do seu dispositivo     |
    |**Cloud upload de entrada (partilha)**  | Soma de todos os bytes escritos à nuvem a partir de uma parte / # de ações é média, máx e min por ação      |
    |**Ler a entrada (rede)**           | Inclui a entrada da rede do sistema para todos os bytes lidos a partir da nuvem. Esta visão pode incluir dados que não se limitam a ações. <br></br>A divisão mostrará o tráfego sobre todos os adaptadores de rede no dispositivo. Isto inclui adaptadores que não estão ligados ou ativados.      |
    |**Escrever a entrada (rede)**       | Inclui a entrada da rede do sistema para todos os bytes escritos na nuvem. Esta visão pode incluir dados que não se limitam a ações. <br></br>A divisão mostrará o tráfego sobre todos os adaptadores de rede no dispositivo. Isto inclui adaptadores que não estão ligados ou ativados.          |
    |**Computação de borda - uso da memória**      | Esta métrica não é aplicável para Data Box Gateway e, portanto, não povoada.          |
    |**Computação de borda - CPU percentual**    | Esta métrica não é aplicável para Data Box Gateway e, portanto, não povoada.         |

4. Quando uma métrica é selecionada a partir da lista de dropdown, a agregação também pode ser definida. A agregação refere-se ao valor real agregado ao longo de um período de tempo especificado. Os valores agregados podem ser médios, mínimos ou máximos. Selecione a Agregação de Avg, Max ou Min.

    ![Ver gráfico](media/data-box-edge-gateway-view-metrics/view-metrics-4.png)

5. Se a métrica selecionada tiver várias instâncias, então a opção de divisão está disponível. Selecione **Aplicar divisão** e, em seguida, selecione o valor pelo qual pretende ver a avaria.

    ![Aplicar divisão](media/data-box-edge-gateway-view-metrics/view-metrics-5.png)

6. Se agora quiser ver a avaria apenas por alguns casos, pode filtrar os dados. Por exemplo, neste caso, se pretender ver a entrada da rede apenas para as duas interfaces de rede conectadas no seu dispositivo, poderá filtrar essas interfaces. **Selecione Adicionar filtro** e especificar o nome da interface de rede para filtragem.

    ![Adicionar filtro](media/data-box-edge-gateway-view-metrics/view-metrics-6.png)

7. Também pode fixar a tabela no painel de instrumentos para facilitar o acesso.

    ![Afixar ao dashboard](media/data-box-edge-gateway-view-metrics/view-metrics-7.png)

8. Para exportar dados de gráficos para uma folha de cálculo do Excel ou obter um link para o gráfico que pode partilhar, selecione a opção de partilha a partir da barra de comando.

    ![Exportar dados](media/data-box-edge-gateway-view-metrics/view-metrics-8.png)