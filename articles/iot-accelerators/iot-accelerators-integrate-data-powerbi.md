---
title: Visualizar dados de monitorização remota utilizando o Power BI - Azure / Microsoft Docs
description: Este tutorial utiliza power BI Desktop e Cosmos DB para integrar dados de uma solução de Monitorização Remota numa visualização personalizada. Desta forma, os utilizadores podem construir os seus próprios dashboards personalizados e partilhá-los para os utilizadores que não estão na solução.
author: dominicbetts
manager: hegate
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 05/01/2018
ms.topic: conceptual
ms.openlocfilehash: ba669c86c406959a3f92949e5f806fa011527d47
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92070916"
---
# <a name="visualize-remote-monitoring-data-using-power-bi"></a>Visualizar dados de monitorização remota utilizando o Power BI

Este tutorial irá acompanhá-lo como ligar os dados da sua solução de monitorização remota da CosmosDB ao Power BI. Com esta ligação estabelecida, pode então criar os seus próprios dashboards personalizados e adicioná-los de volta ao painel de instrumentos de monitorização remota. Este fluxo de trabalho permite a criação de gráficos mais especializados, além dos fora da caixa. Em seguida, pode utilizar este tutorial para se integrar com outros fluxos de dados ou construir dashboards personalizados para serem consumidos fora da sua Solução de Monitorização Remota. Construir painéis de instrumentos no Power BI significa que também pode fazer com que cada painel interaja entre si à medida que seleciona peças específicas. Por exemplo, pode ter um filtro que mostra apenas informações sobre os seus camiões simulados e cada pedaço do seu painel interagiria para mostrar que apenas simulava informações sobre o camião. Se quiser utilizar uma ferramenta que não seja o Power BI, também pode estender estes passos para utilizar a sua ferramenta de visualização de eleição e ligar-se à Base de Dados cosmos, ou base de dados personalizada se tiver configurado uma. 

## <a name="prerequisites"></a>Pré-requisitos

- Deve ter uma solução de monitorização remota em execução
- Você deve ter acesso ao [portal Azure](https://portal.azure.com) e à sua subscrição em que o IoT Hub e solução estão em execução
- Tem de ter [o ambiente de trabalho Power BI](https://powerbi.microsoft.com) instalado, qualquer versão serve


## <a name="information-needed-from-azure-portal"></a>Informação Necessária do portal Azure

1. Navegue [para o portal Azure](https://portal.azure.com) e inscreva-se se necessário

2. No painel da esquerda, clique em grupos de recursos

    ![Nav painel lateral](./media/iot-accelerators-integrate-data-powerbi/side_panel.png)

3. Navegue para o Grupo de Recursos em que a sua Solução Iot está em execução e clique para ser levado para a página geral do Grupo de Recursos. 

4. Nessa página geral clique no item, que tem o tipo "Conta DB Azure Cosmos", será depois levado para a página geral do fluxo DB cosmos para essa Solução IoT.

    ![Grupo de Recursos](./media/iot-accelerators-integrate-data-powerbi/resource_groups.png)

5. No painel à esquerda, clique na secção "Chaves" e tome nota dos seguintes valores a utilizar no Power BI:

   - URI
   - Chave Primária

     ![A screenshot mostra uma conta Azure Cosmos D B com o item Keys selecionado.](./media/iot-accelerators-integrate-data-powerbi/keys.png)

## <a name="setting-up-the-stream-in-power-bi"></a>Configuração do Stream in Power BI
  
1. Abra a aplicação de ambiente de trabalho Power BI e clique em "Obter Dados" a partir do canto superior esquerdo. 

    ![Obter Dados](./media/iot-accelerators-integrate-data-powerbi/get_data.png)

2. Quando solicitado para introduzir dados, opte por procurar "Azure Cosmos DB" e selecione este conector. Este conector essencialmente retira dados diretamente da base de dados cosmos da sua Solução Azure IoT
  
    ![Cosmos DB](./media/iot-accelerators-integrate-data-powerbi/cosmos_db.png)
  
3. Introduza as informações, que já registou acima:

    * URI
    * Chave Primária

4. Selecione todas as tabelas a serem importadas para o Power BI. Esta ação irá dar início ao carregamento dos dados. Quanto mais tempo a sua solução estiver em funcionamento, mais tempo poderá demorar para os dados carregarem (até algumas horas). 

    ![Tabelas de Importação](./media/iot-accelerators-integrate-data-powerbi/import_tables.png)

5. Uma vez terminado o carregamento dos dados, clique em "Editar Consultas" na primeira linha do Power BI e expanda todas as tabelas clicando nas setas na barra amarela para cada mesa. Isto irá essencialmente expandir-se para mostrar todas as colunas. Você vai notar como os dados para coisas como humidade, tempo de velocidade, etc. não são do tipo correto.

    ![Nova Coluna](./media/iot-accelerators-integrate-data-powerbi/new_column.png)
  
    Por exemplo, os dados que entram no Power BI foram alterados para o tempo UNIX quando entrou através do conector. Para se ajustar a esta conversão, pode criar uma nova coluna e usar esta equação para a colocar no formato de hora de data: 

    ```text
    #datetime(1970, 1, 1, 0, 0, 0) + #duration(0, 0, 0, [Document.device.msg.received]/1000)
    ```

    ![Tabela atualizada](./media/iot-accelerators-integrate-data-powerbi/updated_table.png)
  
    Document.device.msg.received é apenas uma das colunas com formatação UNIX e pode ser substituído por outras que precisam de conversão. 
  
    Outros pontos de dados foram convertidos para tipo String podem ser alterados em Doubles ou Int, se for caso disso, utilizando os mesmos passos acima.

## <a name="creating-a-dashboard"></a>Criação de um dashboard

Uma vez ligado o fluxo, está pronto para criar os seus dashboards personalizados! O painel abaixo é um exemplo de tomar a telemetria emitida pelos nossos dispositivos simulados, e mostrar diferentes pivôs em torno dele, tais como: 

* Localização do dispositivo num mapa (à direita)
* Dispositivos com o seu estado e gravidade. (em cima à esquerda)
* Dispositivos com regras em vigor, e se houver alertas a disparar para eles (inferior à esquerda)

![Visualização powerbi](./media/iot-accelerators-integrate-data-powerbi/visual_data.png)

## <a name="publishing-the-dashboard-and-refreshing-the-data"></a>Publicar o painel de instrumentos e refrescar os dados

Depois de ter criado com sucesso os seus dashboards, recomendamos que [publique os seus dashboards Power BI](/power-bi/desktop-upload-desktop-files) para partilhar com outros.

Também deverá atualizar [os dados](/power-bi/refresh-data) do painel de instrumentos publicado para se certificar de que tem os dados mais recentes.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu sobre como visualizar dados de monitorização remota usando o Power BI

Para obter mais informações sobre a personalização da solução de Monitorização Remota, consulte:

* [Personalizar a Solução de Monitorização Remota UI](iot-accelerators-remote-monitoring-customize.md)
* [Guia de Referência para Programadores](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Guia de Resolução de Problemas para Programadores](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)