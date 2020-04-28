---
title: Visualizar dados de monitorização remota utilizando o Power BI - Azure [ Microsoft Docs
description: Este tutorial utiliza power BI Desktop e Cosmos DB para integrar dados de uma solução de Monitorização Remota numa visualização personalizada. Desta forma, os utilizadores podem construir os seus próprios dashboards personalizados e partilhá-los com os utilizadores que não estão na solução.
author: dominicbetts
manager: hegate
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 05/01/2018
ms.topic: conceptual
ms.openlocfilehash: 8b0f395936c8628ff7b8dab601168538f97ccdb7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74184248"
---
# <a name="visualize-remote-monitoring-data-using-power-bi"></a>Visualizar dados de monitorização remota utilizando o Power BI

Este tutorial irá acompanhá-lo através de como ligar os dados da sua solução de monitorização remota da CosmosDB ao Power BI. Com esta ligação estabelecida, pode então criar os seus próprios dashboards personalizados e adicioná-los de volta ao seu dashboard de solução de monitorização remota. Este fluxo de trabalho permite a criação de gráficos mais especializados, além dos que saem da caixa. Em seguida, pode utilizar este tutorial para se integrar com outros fluxos de dados ou construir dashboards personalizados para serem consumidos fora da sua Solução de Monitorização Remota. Construir dashboards em Power BI significa que também pode fazer com que cada painel interaja entre si enquanto seleciona peças específicas. Por exemplo, você poderia ter um filtro que lhe mostra apenas informações sobre seus caminhões simulados e cada pedaço do seu painel interagiria para mostrar que apenas simulava informações de caminhão. Se quiser utilizar uma ferramenta diferente do Power BI, também pode estender estes passos para utilizar a sua ferramenta de visualização de eleição e ligar-se à Base de Dados Cosmos, ou à base de dados personalizada, se tiver configurado uma. 

## <a name="prerequisites"></a>Pré-requisitos

- Você deve ter uma solução de monitorização remota atualmente em execução
- Você deve ter acesso ao [portal Azure](https://portal.azure.com) e sua subscrição em que o IoT Hub e Solução estão em execução
- Você deve ter [power BI desktop](https://powerbi.microsoft.com) instalado, qualquer versão vai fazer


## <a name="information-needed-from-azure-portal"></a>Informação Necessária do portal Azure

1. Navegue para [o portal Azure](https://portal.azure.com) e inscreva-se se necessário

2. No painel à esquerda, clique em grupos de recursos

    ![Navegação do Painel Lateral](./media/iot-accelerators-integrate-data-powerbi/side_panel.png)

3. Navegue para o Grupo de Recursos do qual a sua Solução Iot está a funcionar e clique para ser levado para a página de visão geral do Grupo de Recursos. 

4. Nessa página geral clique no item, que tem o tipo "Conta DB Do Cosmos", será depois levado para a página geral do stream Cosmos DB para aquela Solução IoT.

    ![Grupo de Recursos](./media/iot-accelerators-integrate-data-powerbi/resource_groups.png)

5. No painel à esquerda, clique na secção "Teclas" e tome nota dos seguintes valores a utilizar no Power BI:

   - URI
   - Chave Primária

     ![keys](./media/iot-accelerators-integrate-data-powerbi/keys.png)

## <a name="setting-up-the-stream-in-power-bi"></a>Configuração do Fluxo em Power BI
  
1. Abra a aplicação de desktop Power BI e clique em "Obter Dados" a partir do canto superior esquerdo. 

    ![Obter Dados](./media/iot-accelerators-integrate-data-powerbi/get_data.png)

2. Quando lhe for pedido que introduza dados, opte por Procurar por "Azure Cosmos DB" e selecione este conector. Este conector essencialmente retira dados diretamente da base de dados cosmos da sua Solução Azure IoT
  
    ![BD do Cosmos](./media/iot-accelerators-integrate-data-powerbi/cosmos_db.png)
  
3. Introduza a informação, que tenha gravado acima:

    * URI
    * Chave Primária

4. Selecione todas as tabelas a importar para Power BI. Esta ação vai dar início ao carregamento dos dados. Quanto mais tempo a sua solução tiver funcionado, mais tempo demorará a carregar os dados (até algumas horas). 

    ![Tabelas de Importação](./media/iot-accelerators-integrate-data-powerbi/import_tables.png)

5. Uma vez que os dados terminem de carregar, clique em "Editar Consultas" na linha superior do Power BI e expandir todas as tabelas clicando nas setas na barra amarela para cada tabela. Isto irá essencialmente expandir-se para mostrar todas as colunas. Você vai notar como os dados para coisas como humidade, tempo de velocidade, etc. não são do tipo correto.

    ![Nova Coluna](./media/iot-accelerators-integrate-data-powerbi/new_column.png)
  
    Por exemplo, os dados que chegam ao Power BI foram alterados para o tempo UNIX quando entraram através do conector. Para se ajustar a esta conversão, pode criar uma nova coluna e utilizar esta equação para a colocar no formato de data: 

    ```text
    #datetime(1970, 1, 1, 0, 0, 0) + #duration(0, 0, 0, [Document.device.msg.received]/1000)
    ```

    ![Tabela Atualizada](./media/iot-accelerators-integrate-data-powerbi/updated_table.png)
  
    Document.device.msg.received é apenas uma das colunas com formatação UNIX e pode ser substituída por outras que precisam de conversão. 
  
    Outros pontos de dados foram convertidos para o tipo String podem ser convertidos em Duplos ou Int, se for caso disso, utilizando os mesmos passos que acima.

## <a name="creating-a-dashboard"></a>Criação de um dashboard

Uma vez que o fluxo esteja ligado, você está pronto para criar os seus dashboards personalizados! O painel abaixo é um exemplo de tomar a telemetria emitida pelos nossos dispositivos simulados, e mostrar diferentes pivôs à sua volta, tais como: 

* Localização do dispositivo num mapa (à direita)
* Dispositivos com o seu estatuto e gravidade. (superior à esquerda)
* Dispositivos com regras em vigor, e se houver alertas a disparar para eles (inferior esquerdo)

![Visualização PowerBi](./media/iot-accelerators-integrate-data-powerbi/visual_data.png)

## <a name="publishing-the-dashboard-and-refreshing-the-data"></a>Publicar o dashboard e refrescar os dados

Depois de ter criado com sucesso os seus dashboards, recomendamos que [publique os seus dashboards Power BI](https://docs.microsoft.com/power-bi/desktop-upload-desktop-files) para partilhar com outros.

Também vai querer [atualizar os dados](https://docs.microsoft.com/power-bi/refresh-data) no painel de instrumentos publicados para se certificar de que tem o conjunto de dados mais recente.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a visualizar dados de monitorização remota usando o Power BI

Para obter mais informações sobre a personalização da solução de Monitorização Remota, consulte:

* [Personalização da solução de monitorização remota UI](iot-accelerators-remote-monitoring-customize.md)
* [Guia de Referência para Programadores](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Guia de Resolução de Problemas para Programadores](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

