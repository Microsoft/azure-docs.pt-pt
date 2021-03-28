---
title: Personalize a solução de Fábrica Conectada - Azure | Microsoft Docs
description: Uma descrição de como personalizar o comportamento do acelerador de solução Connected Factory.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.devlang: csharp
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: dobett
ms.openlocfilehash: a092859517ee6ac41e62e55fe95f72b8bfdf87d5
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2021
ms.locfileid: "105646250"
---
# <a name="customize-how-the-connected-factory-solution-displays-data-from-your-opc-ua-servers"></a>Personalize como a solução De Fábrica Conectada exibe dados dos seus servidores OPC UA

> [!IMPORTANT]
> Enquanto atualizamos este artigo, consulte [a Azure Industrial IoT](https://azure.github.io/Industrial-IoT/) para obter o conteúdo mais atualizado.

A solução Connected Factory agrega e exibe dados dos servidores OPC UA ligados à solução. Pode navegar e enviar comandos para os servidores UA OPC na sua solução. Para obter mais informações sobre a OPC UA, consulte as [FAQ de fábrica conectadas.](iot-accelerators-faq-cf.md)

Exemplos de dados agregados na solução incluem a Eficiência Global do Equipamento (OEE) e os Principais Indicadores de Desempenho (KPI's) que pode ver no painel de instrumentos nos níveis de fábrica, linha e estação. A imagem que se segue mostra os valores OEE e KPI para a estação **de montagem,** na **linha de produção 1**, na fábrica **de Munique:**

![Exemplo dos valores OEE e KPI na solução][img-oee-kpi]

A solução permite-lhe visualizar informações detalhadas a partir de itens de dados específicos dos servidores da UA OPC, chamados *estações*. A imagem que se segue mostra parcelas do número de artigos fabricados a partir de uma estação específica:

![Parcelas de número de artigos fabricados][img-manufactured-items]

Se clicar num dos gráficos, pode explorar os dados mais adiante utilizando o Time Series Insights (TSI):

![Explore dados usando insights de séries de tempo][img-tsi]

Este artigo descreve:

- Como os dados são disponibilizados para os vários pontos de vista da solução.
- Como pode personalizar a forma como a solução exibe os dados.

## <a name="data-sources"></a>Origens de dados

A solução Connected Factory exibe dados dos servidores OPC UA ligados à solução. A instalação predefinitiva inclui vários servidores OPC UA executando uma simulação de fábrica. Pode adicionar os seus próprios servidores OPC UA que [conectem através de um gateway][lnk-connect-cf] à sua solução.

Pode navegar nos dados que um servidor OPC UA conectado pode enviar para a sua solução no painel de instrumentos:

1. Escolha **o Navegador** para navegar para a vista do servidor **OPC UA:**

    ![Navegue para a vista do servidor OPC UA][img-select-server]

1. Selecione um servidor e clique em **Connect**. Clique **em Proceder** quando o aviso de segurança aparecer.

    > [!NOTE]
    > Este aviso só aparece uma vez para cada servidor e estabelece uma relação de confiança entre o painel de solução e o servidor.

1. Pode agora navegar nos dados que o servidor pode enviar para a solução. Os itens que estão a ser enviados para a solução têm uma marca de verificação:

    ![Artigos publicados][img-published]

1. Se for *administrador* na solução, pode optar por publicar um item de dados para o disponibilizar na solução De Fábrica Conectada. Como Administrador, também pode alterar o valor dos itens de dados e dos métodos de chamada no servidor OPC UA.

## <a name="map-the-data"></a>Mapear os dados

A solução Connected Factory mapeia e agrega os itens de dados publicados do servidor OPC UA às várias visões da solução. A solução Connected Factory implanta-se na sua conta Azure quando fornece a solução. Um ficheiro JSON na solução Visual Studio Connected Factory armazena esta informação de mapeamento. Pode visualizar e modificar este ficheiro de configuração JSON na solução Connected Factory Visual Studio. Pode recolocar a solução depois de fazer uma alteração.

Pode utilizar o ficheiro de configuração para:

- Editar as fábricas simuladas existentes, linhas de produção e estações.
- Mapeia dados de servidores OPC UA reais que se conectam à solução.

Para obter mais informações sobre o mapeamento e agregação dos dados para satisfazer os seus requisitos específicos, consulte [Como configurar o acelerador de solução De Fábrica Conectada ](iot-accelerators-connected-factory-configure.md).

## <a name="deploy-the-changes"></a>Implementar as alterações

Quando terminar de efetuar alterações **naContosoTopologyDescription.jsno** ficheiro, tem de recolocar a solução De Fábrica Conectada na sua conta Azure.

O repositório **de fábrica ligado a azul-iot** inclui um script **powerShellbuild.ps1** que pode usar para reconstruir e implementar a solução.

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre o acelerador de solução Connected Factory lendo os seguintes artigos:

* [Permissões no site azureiotsolutions.com][lnk-permissions]
* [FAQ de Fábrica Ligada](iot-accelerators-faq-cf.md)
* [FAQ][lnk-faq]


[img-oee-kpi]: ./media/iot-accelerators-connected-factory-customize/oeenadkpi.png
[img-manufactured-items]: ./media/iot-accelerators-connected-factory-customize/manufactured.png
[img-tsi]: ./media/iot-accelerators-connected-factory-customize/tsi.png
[img-select-server]: ./media/iot-accelerators-connected-factory-customize/selectserver.png
[img-published]: ./media/iot-accelerators-connected-factory-customize/published.png


[lnk-permissions]: iot-accelerators-permissions.md
[lnk-faq]: iot-accelerators-faq.md