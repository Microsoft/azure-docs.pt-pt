---
title: Personalizar a solução de fábrica ligada - Azure | Documentos da Microsoft
description: Uma descrição de como personalizar o comportamento do acelerador de solução de fábrica ligada.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.devlang: csharp
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: dobett
ms.openlocfilehash: 7d4e95d066e191e1d5b6d083ede65843dbe73f31
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "57010077"
---
# <a name="customize-how-the-connected-factory-solution-displays-data-from-your-opc-ua-servers"></a>Personalizar a forma como a solução de fábrica ligada apresenta dados de seus servidores OPC UA

A solução de fábrica ligada agrega e apresenta os dados dos servidores OPC UA ligados à solução. Pode procurar e enviar comandos para os servidores OPC UA na sua solução. Para obter mais informações sobre OPC UA, veja as [FAQ sobre a Fábrica Ligada](iot-accelerators-faq-cf.md).

Exemplos de dados agregados na solução incluem a eficiência geral de equipamentos (OEE) e indicadores chave de desempenho (KPIs) que pode ver no dashboard de fábrica, linha e níveis de estação. Captura de ecrã seguinte mostra os valores OEE e KPI para a **Assembly** estação, no **linha de produção 1**, no **Munique** factory:

![Exemplo de valores OEE e KPI da solução][img-oee-kpi]

A solução permite-lhe ver informações detalhadas de itens de dados específicos dos servidores OPC UA, chamados *estações*. Captura de ecrã seguinte mostra gráficos do número de itens de fabricante de uma estação de específica:

![Gráficos de número de itens produzidos e][img-manufactured-items]

Se clicar em um dos gráficos, pode explorar os dados ainda mais usando o Time Series Insights (TSI):

![Explorar dados com o Time Series Insights][img-tsi]

Este artigo descreve:

- Como os dados são disponibilizados para as várias vistas na solução.
- Como pode personalizar a forma como a solução apresenta os dados.

## <a name="data-sources"></a>Origens de dados

A solução de fábrica ligada mostra dados dos servidores OPC UA ligados à solução. A instalação predefinida inclui vários servidores OPC UA executando uma simulação de fábrica. Pode adicionar seus próprios servidores OPC UA que [ligar através de um gateway] [ lnk-connect-cf] à sua solução.

Pode procurar os itens de dados que envia um servidor OPC UA ligado à sua solução no dashboard:

1. Escolher **Browser** para navegar para o **selecione um servidor OPC UA** vista:

    ![Navegue para selecionar uma vista de servidor OPC UA][img-select-server]

1. Selecione um servidor e clique em **Connect**. Clique em **continuar** quando aparece o aviso de segurança.

    > [!NOTE]
    > Este aviso só aparece uma vez para cada servidor e estabelece uma relação de confiança entre o dashboard da solução e o servidor.

1. Pode procurar os itens de dados que o servidor pode enviar para a solução. Itens que estão a ser enviados para a solução têm uma marca de verificação:

    ![Itens publicados][img-published]

1. Se for um *administrador* na solução, pode optar por publicar um item de dados para disponibilizá-lo na solução de fábrica ligada. Como administrador, também pode alterar o valor de itens de dados e chamar métodos no servidor OPC UA.

## <a name="map-the-data"></a>Os dados do mapa

A solução de fábrica ligada mapeia e agrega os itens de dados publicados a partir do servidor OPC UA para as várias vistas na solução. Implementa a solução de fábrica ligada à sua conta do Azure, quando Aprovisiona a solução. Um ficheiro JSON da solução do Visual Studio Connected Factory armazena essas informações de mapeamento. Pode ver e modificar este ficheiro de configuração JSON na solução de fábrica de ligado Visual Studio. Pode implementar a solução pode ser novamente depois de efetuar uma alteração.

Pode utilizar o ficheiro de configuração para:

- Edite o fábricas simuladas existentes, as linhas de produção e estações.
- Mapear dados de servidores OPC UA reais que se conectar à solução.

Para obter mais informações sobre o mapeamento e agregar os dados para atender às suas necessidades específicas, consulte [como configurar o acelerador de solução de fábrica ligada ](iot-accelerators-connected-factory-configure.md).

## <a name="deploy-the-changes"></a>Implementar as alterações

Quando terminar de fazer alterações para o **ContosoTopologyDescription.json** ficheiro, tem de Reimplementar a solução de fábrica ligada à sua conta do Azure.

O **azure-iot-connected-factory** repositório inclui um **build.ps1** script de PowerShell que pode utilizar para reconstruir e implementar a solução.

## <a name="next-steps"></a>Próximos Passos

Saiba mais sobre o acelerador de solução de fábrica ligada ao ler os artigos seguintes:

* [Apresentação do acelerador da solução Fábrica Ligada][lnk-rm-walkthrough]
* [Implementar um gateway para a fábrica ligada][lnk-connect-cf]
* [Permissões no azureiotsolutions.com site][lnk-permissions]
* [FAQ de Fábrica Ligada](iot-accelerators-faq-cf.md)
* [FAQ][lnk-faq]


[img-oee-kpi]: ./media/iot-accelerators-connected-factory-customize/oeenadkpi.png
[img-manufactured-items]: ./media/iot-accelerators-connected-factory-customize/manufactured.png
[img-tsi]: ./media/iot-accelerators-connected-factory-customize/tsi.png
[img-select-server]: ./media/iot-accelerators-connected-factory-customize/selectserver.png
[img-published]: ./media/iot-accelerators-connected-factory-customize/published.png


[lnk-rm-walkthrough]:iot-accelerators-connected-factory-sample-walkthrough.md
[lnk-connect-cf]:iot-accelerators-connected-factory-gateway-deployment.md
[lnk-permissions]: iot-accelerators-permissions.md
[lnk-faq]: iot-accelerators-faq.md