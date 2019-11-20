---
title: Visualizar dados de monitoramento remoto usando o Power BI-Azure | Microsoft Docs
description: Este tutorial usa Power BI Desktop e Cosmos DB para integrar dados de uma solução de monitoramento remoto em uma visualização personalizada. Dessa forma, os usuários podem criar seus próprios painéis personalizados e compartilhá-los para os usuários que não estão na solução.
author: dominicbetts
manager: hegate
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 05/01/2018
ms.topic: conceptual
ms.openlocfilehash: 8b0f395936c8628ff7b8dab601168538f97ccdb7
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184248"
---
# <a name="visualize-remote-monitoring-data-using-power-bi"></a>Visualizar dados de monitoramento remotos usando o Power BI

Este tutorial explicará como conectar seus dados de solução de monitoramento remoto do CosmosDB ao Power BI. Com essa conexão estabelecida, você pode criar seus próprios painéis personalizados e adicioná-los de volta no painel da solução de monitoramento remoto. Esse workstream permite que gráficos mais especializados sejam criados, além daqueles prontos para uso. Você pode usar este tutorial para integrar com outros fluxos de dados ou criar painéis personalizados para serem consumidos fora da sua solução de monitoramento remoto. A criação de painéis no Power BI significa que você também pode fazer com que cada painel interaja um com o outro à medida que você seleciona partes específicas. Por exemplo, você pode ter um filtro que mostra apenas informações sobre o caminhões simulado e cada parte do seu painel interagiria para mostrar apenas informações simuladas do caminhão. Se você quiser usar uma ferramenta diferente de Power BI, também poderá estender essas etapas para usar a ferramenta de visualização de sua escolha e conectar-se ao banco de dados Cosmos ou ao banco de dados personalizado se tiver configurado um. 

## <a name="prerequisites"></a>Pré-requisitos

- Você deve ter uma solução de monitoramento remoto em execução no momento
- Você deve ter acesso ao [portal do Azure](https://portal.azure.com) e à sua assinatura na qual o Hub IOT e a solução estão em execução
- Você deve ter o [Power bi desktop](https://powerbi.microsoft.com) instalado, qualquer versão do fará


## <a name="information-needed-from-azure-portal"></a>Informações necessárias do portal do Azure

1. Navegue até [portal do Azure](https://portal.azure.com) e entre, se necessário

2. No painel esquerdo, clique em grupos de recursos

    ![Navegação do painel lateral](./media/iot-accelerators-integrate-data-powerbi/side_panel.png)

3. Navegue até o grupo de recursos do qual sua solução de IOT está em execução e clique para ser levado para a página de visão geral desse grupo de recursos. 

4. Nessa página de visão geral, clique no item, que tem o tipo "conta de Azure Cosmos DB", você será levado para a página de visão geral do fluxo de Cosmos DB para essa solução de IoT.

    ![Grupo de Recursos](./media/iot-accelerators-integrate-data-powerbi/resource_groups.png)

5. No painel à esquerda, clique na seção "chaves" e anote os seguintes valores a serem usados no Power BI:

   - URI
   - Chave primária

     ![keys](./media/iot-accelerators-integrate-data-powerbi/keys.png)

## <a name="setting-up-the-stream-in-power-bi"></a>Configurando o fluxo no Power BI
  
1. Abra o aplicativo de área de trabalho Power BI e clique em "obter dados" no canto superior esquerdo. 

    ![Obter dados](./media/iot-accelerators-integrate-data-powerbi/get_data.png)

2. Quando solicitado a inserir dados, escolha procurar "Azure Cosmos DB" e selecione este conector. Essencialmente, esse conector extrai dados diretamente do banco de Cosmos de sua solução de IoT do Azure
  
    ![BD do Cosmos](./media/iot-accelerators-integrate-data-powerbi/cosmos_db.png)
  
3. Insira as informações que você gravou acima:

    * URI
    * Chave primária

4. Selecione todas as tabelas a serem importadas para Power BI. Esta ação iniciará o carregamento dos dados. Quanto mais longa sua solução estiver em execução, mais tempo poderá levar para que os dados sejam carregados (até algumas horas). 

    ![Importar tabelas](./media/iot-accelerators-integrate-data-powerbi/import_tables.png)

5. Depois que os dados tiverem terminado o carregamento, clique em "editar consultas" na linha superior de Power BI e expanda todas as tabelas clicando nas setas na barra amarela para cada tabela. Essencialmente, isso será expandido para mostrar todas as colunas. Você notará como os dados para coisas como umidade, tempo de velocidade, etc. não são do tipo correto.

    ![Nova coluna](./media/iot-accelerators-integrate-data-powerbi/new_column.png)
  
    Por exemplo, os dados que entram em Power BI foram alterados para o tempo de UNIX quando ele chegou por meio do conector. Para ajustar essa conversão, você pode criar uma nova coluna e usar esta equação para colocá-la em formato de data/hora: 

    ```text
    #datetime(1970, 1, 1, 0, 0, 0) + #duration(0, 0, 0, [Document.device.msg.received]/1000)
    ```

    ![Tabela atualizada](./media/iot-accelerators-integrate-data-powerbi/updated_table.png)
  
    Document. Device. msg. Received é apenas uma das colunas com a formatação UNIX e pode ser substituída por outras que precisam de conversão. 
  
    Outros pontos de dados foram convertidos no tipo cadeia de caracteres podem ser alterados em duplos ou int, quando apropriado, usando as mesmas etapas acima.

## <a name="creating-a-dashboard"></a>Criando um painel

Depois que o fluxo estiver conectado, você estará pronto para criar seus painéis personalizados! O painel abaixo é um exemplo de como fazer a telemetria ser emitida por nossos dispositivos simulados e mostrar diferentes dinamizações em relação a ele, como: 

* Local do dispositivo em um mapa (à direita)
* Dispositivos com seu status e severidade. (superior à esquerda)
* Dispositivos com regras em vigor e se houver alertas sendo desativados para eles (parte inferior esquerda)

![Visualização do PowerBi](./media/iot-accelerators-integrate-data-powerbi/visual_data.png)

## <a name="publishing-the-dashboard-and-refreshing-the-data"></a>Publicando o painel e atualizando os dados

Depois de criar seus painéis com êxito, recomendamos que você [publique seus painéis de Power bi](https://docs.microsoft.com/power-bi/desktop-upload-desktop-files) para compartilhar com outras pessoas.

Você também desejará [atualizar os dados](https://docs.microsoft.com/power-bi/refresh-data) no painel publicado para ter certeza de que tem o conjunto de dados mais recente.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu sobre como Visualizar dados de monitoramento remoto usando Power BI

Para obter mais informações sobre como personalizar a solução de monitoramento remoto, consulte:

* [Personalizando a interface do usuário da solução de monitoramento remoto](iot-accelerators-remote-monitoring-customize.md)
* [Guia de Referência para Programadores](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Guia de Resolução de Problemas de Programadores](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

