---
title: Personalize a solução Connected Factory - Azure [ Microsoft Docs
description: Uma descrição de como personalizar o comportamento do acelerador de soluções Connected Factory.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.devlang: csharp
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: dobett
ms.openlocfilehash: 6062f8b3992732e0e0f9bbdae9549e69c393f4ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67080489"
---
# <a name="customize-how-the-connected-factory-solution-displays-data-from-your-opc-ua-servers"></a>Personalize como a solução Connected Factory exibe dados dos seus servidores OPC UA

A solução Connected Factory agrega e exibe dados dos servidores OPC UA ligados à solução. Pode navegar e enviar comandos para os servidores DaUA oPC na sua solução. Para mais informações sobre o OPC UA, consulte as FAQ da [Fábrica Conectada](iot-accelerators-faq-cf.md).

Exemplos de dados agregados na solução incluem a Eficiência Geral do Equipamento (OEE) e indicadores de desempenho chave (KPIIs) que pode ver no painel de instrumentos nos níveis de fábrica, linha e estação. A seguinte imagem mostra os valores de OEE e KPI para a estação de **montagem,** na **linha de produção 1,** na fábrica de **Munique:**

![Exemplo dos valores de OEE e KPI na solução][img-oee-kpi]

A solução permite-lhe visualizar informações detalhadas a partir de itens de dados específicos dos servidores da OPC UA, chamados *estações*. A seguinte imagem mostra parcelas do número de artigos fabricados a partir de uma estação específica:

![Parcelas de número de artigos fabricados][img-manufactured-items]

Se clicar num dos gráficos, pode explorar ainda mais os dados utilizando insights da Série de Tempo (TSI):

![Explore dados usando insights da série temporal][img-tsi]

Este artigo descreve:

- Como os dados são disponibilizados para as várias opiniões da solução.
- Como pode personalizar a forma como a solução exibe os dados.

## <a name="data-sources"></a>Origens de dados

A solução Connected Factory apresenta dados dos servidores OPC UA ligados à solução. A instalação padrão inclui vários servidores OPC UA executando uma simulação de fábrica. Pode adicionar os seus próprios servidores OPC UA que [se ligam através de um gateway][lnk-connect-cf] à sua solução.

Pode navegar nos itens de dados que um servidor OPC UA ligado pode enviar para a sua solução no painel de instrumentos:

1. Escolha o **Browser** para navegar para a vista de **servidor OPC UA Select:**

    ![Navegue para a vista de servidor oPC UA][img-select-server]

1. Selecione um servidor e clique em **Ligar**. Clique em **Proceder** quando aparecer o aviso de segurança.

    > [!NOTE]
    > Este aviso só aparece uma vez para cada servidor e estabelece uma relação de confiança entre o painel de soluções e o servidor.

1. Agora pode navegar nos itens de dados que o servidor pode enviar para a solução. Os itens que estão a ser enviados para a solução têm uma marca de verificação:

    ![Itens publicados][img-published]

1. Se for *administrador* na solução, pode optar por publicar um item de dados para o disponibilizar na solução Connected Factory. Como Administrador, também pode alterar o valor dos itens de dados e dos métodos de chamada no servidor OPC UA.

## <a name="map-the-data"></a>Mapear os dados

A solução Connected Factory mapeia e agrega os itens de dados publicados do servidor OPC UA para as várias vistas da solução. A solução Connected Factory implanta-se na sua conta Azure quando fornecer a solução. Um ficheiro JSON na solução Visual Studio Connected Factory armazena esta informação de mapeamento. Pode visualizar e modificar este ficheiro de configuração JSON na solução Connected Factory Visual Studio. Pode recolocar a solução depois de fazer uma mudança.

Pode utilizar o ficheiro de configuração para:

- Editar as fábricas simuladas existentes, linhas de produção e estações.
- Mapeie dados de servidores UA reais da OPC que se conecta à solução.

Para obter mais informações sobre mapeamento e agregação dos dados para satisfazer os seus requisitos específicos, consulte [como configurar o acelerador ](iot-accelerators-connected-factory-configure.md)de soluções Connected Factory .

## <a name="deploy-the-changes"></a>Implementar as alterações

Quando terminar de fazer alterações no ficheiro **ContosoTopologyDescription.json,** tem de recolocar a solução Connected Factory na sua conta Azure.

O repositório de **fábrica azure-iot-connected-connected** inclui um script **build.ps1** PowerShell que você pode usar para reconstruir e implementar a solução.

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre o acelerador de solução Connected Factory lendo os seguintes artigos:

* [Permissões no site azureiotsolutions.com][lnk-permissions]
* [FAQ de fábrica conectada](iot-accelerators-faq-cf.md)
* [FAQ][lnk-faq]


[img-oee-kpi]: ./media/iot-accelerators-connected-factory-customize/oeenadkpi.png
[img-manufactured-items]: ./media/iot-accelerators-connected-factory-customize/manufactured.png
[img-tsi]: ./media/iot-accelerators-connected-factory-customize/tsi.png
[img-select-server]: ./media/iot-accelerators-connected-factory-customize/selectserver.png
[img-published]: ./media/iot-accelerators-connected-factory-customize/published.png


[lnk-permissions]: iot-accelerators-permissions.md
[lnk-faq]: iot-accelerators-faq.md