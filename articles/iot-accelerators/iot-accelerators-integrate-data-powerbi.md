---
title: Visualizar dados de monitorização remota com o Power BI - Azure | Documentos da Microsoft
description: Este tutorial utiliza o ambiente de trabalho do Power BI e o Cosmos DB para integerate dados de uma solução de monitorização remota numa visualização personalizada. Esta forma como os utilizadores podem criar seus próprios dashboards personalizados e compartilhá-los com os utilizadores não na solução.
author: asdonald
manager: hegate
ms.author: asdonald
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 05/01/2018
ms.topic: conceptual
ms.openlocfilehash: 3398c6d318e0e3c51d3f6cfe8af651a6e3f55c9c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58094825"
---
# <a name="visualize-remote-monitoring-data-using-power-bi"></a>Visualizar dados de monitorização remota com o Power BI

Este tutorial explica como conectar seus dados de solução de monitorização remota do cosmos DB para o Power BI. Com esta ligação estabelecida, em seguida, pode criar seus próprios dashboards personalizados e adicioná-los para o seu dashboard da solução de monitorização remota. Este workstream possibilita gráficos mais especializados a ser criado, para além daquelas prontas a utilizar. Em seguida, pode utilizar este tutorial para integrar com outros fluxos de dados ou criar dashboards personalizados para ser consumida fora da sua solução de monitorização remota. A criar dashboards no Power BI significa que pode também verificar cada painel interagir entre si, à medida que seleciona partes específicas. Por exemplo, poderia ter um filtro que mostra-lhe informações apenas sobre sua trucks simulados e cada parte do dashboard para mostrar a que simulated apenas informações de camião interagir. Se desejar usar uma ferramenta que não seja o Power BI, também pode expandir estes passos para utilizar a sua ferramenta de visualização de escolha e conectar-se na base de dados do Cosmos ou banco de dados personalizado se o ter configurado um. 

## <a name="prerequisites"></a>Pré-requisitos

- Tem de ter uma solução de monitorização remota atualmente em execução
- Tem de ter acesso ao [Portal do Azure](https://portal.azure.com) e a sua subscrição no qual o IoT Hub e a solução estão a executar
- Tem de ter [Power BI desktop](https://powerbi.microsoft.com) instalado, será feito qualquer versão


## <a name="information-needed-from-azure-portal"></a>Informações necessárias a partir do Portal do Azure

1. Navegue para [Portal do Azure](https://portal.azure.com) e início de sessão se for necessário

2. No painel à esquerda, clique em grupos de recursos

    ![Barra de navegação do lado painel](./media/iot-accelerators-integrate-data-powerbi/side_panel.png)

3. Navegue para o grupo de recursos de que a sua solução de Iot está em execução no e clique para ir para página de descrição geral do grupo de recursos. 

4. Nessa página de descrição geral, clique no item, que tem o tipo "Conta do Azure Cosmos DB", será encaminhado para a página de descrição geral da transmissão em fluxo do Cosmos DB para essa solução de IoT.

    ![Grupo de Recursos](./media/iot-accelerators-integrate-data-powerbi/resource_groups.png)

5. No painel à esquerda, clique a secção "Chaves" e tome nota dos seguintes valores a ser utilizado no Power BI:

   - URI
   - Chave Primária

     ![keys](./media/iot-accelerators-integrate-data-powerbi/keys.png)

## <a name="setting-up-the-stream-in-power-bi"></a>Como configurar o Stream no Power BI
  
1. Abrir a aplicação de ambiente de trabalho do Power BI e clique em "Get Data" no canto superior esquerdo. 

    ![Obter dados](./media/iot-accelerators-integrate-data-powerbi/get_data.png)

2. Quando solicitado a inserir dados, escolha a pesquisa para "Do Azure Cosmos DB" e selecione este conector. Este conector, essencialmente, extrai dados diretamente a partir de uma base de dados do cosmos da sua solução de IoT do Azure
  
    ![BD do Cosmos](./media/iot-accelerators-integrate-data-powerbi/cosmos_db.png)
  
3. Introduza as informações que foram registradas acima:

    * URI
    * Chave Primária

4. Selecione todas as tabelas a serem importados para o Power BI. Esta ação irá iniciar o carregamento dos dados. Mais tempo a solução tem funcionado, quanto mais tempo pode demorar para que a data e carregar (até algumas horas). 

    ![Importar tabelas](./media/iot-accelerators-integrate-data-powerbi/import_tables.png)

5. Quando tem terminado dos dados de carregamento, clique em "Editar consultas" na linha superior do Power BI e expanda todas as tabelas ao clicar nas setas na barra amarela para cada tabela. Essencialmente, isto irá expandir para mostrar todas as colunas. Verá como os dados para tarefas como a humidade, acelerar o tempo, etc. não são do tipo correto.

    ![Nova coluna](./media/iot-accelerators-integrate-data-powerbi/new_column.png)
  
    Por exemplo, os dados para o Power BI foi alterados em tempo de UNIX quando chegou através do conector. Para ajustar para esta conversão, daqui em diante pode criar uma nova coluna e, utilize esta equação para inseri-los no formato de hora de data: 

    ```text
    #datetime(1970, 1, 1, 0, 0, 0) + #duration(0, 0, 0, [Document.device.msg.received]/1000)
    ```

    ![Tabela atualizada](./media/iot-accelerators-integrate-data-powerbi/updated_table.png)
  
    Document.Device.msg.Received é apenas uma das colunas com a formatação de UNIX e podem ser substituídas com outras pessoas que necessitam de conversão. 
  
    Outros pontos de dados foram convertidos para o tipo de que cadeia de caracteres pode ser alterada em duplicatas ou Int onde apropriado usar os mesmos passos como anteriormente.

## <a name="creating-a-dashboard"></a>Criar um dashboard

Assim que o fluxo foi ligado, está pronto para criar dashboards personalizados! O dashboard abaixo é um exemplo de colocar a telemetria que está a ser emitida pelo nosso dispositivos simulados e tabelas dinâmicas de diferentes que mostra em torno dele, tais como: 

* Localização do dispositivo num mapa (direita)
* Dispositivos com o respetivo estado e gravidade. (canto superior esquerdo)
* Dispositivos com regras no local e se existem quaisquer alertas vai para os mesmos (canto inferior esquerdo)

![Visualização do Power BI](./media/iot-accelerators-integrate-data-powerbi/visual_data.png)

## <a name="publishing-the-dashboard-and-refreshing-the-data"></a>Publicar o dashboard e atualizar os dados

Depois de ter criado com êxito os seus dashboards, recomendamos que [publicar dashboards do Power BI](https://docs.microsoft.com/power-bi/desktop-upload-desktop-files) para partilhar com outras pessoas.

Também vai querer [atualizar os dados](https://docs.microsoft.com/power-bi/refresh-data) no dashboard publicado para se certificar de que tem o conjunto de dados mais recentes.

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, aprendeu sobre como visualizar dados de monitorização remotos com o Power BI

Para obter mais informações sobre como personalizar a solução de monitorização remota, consulte:

* [Personalizar a solução de monitorização remota da interface do Usuário](iot-accelerators-remote-monitoring-customize.md)
* [Guia de Referência para Programadores](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Guia de Resolução de Problemas de Programadores](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

