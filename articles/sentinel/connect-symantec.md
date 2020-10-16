---
title: Ligue os dados do ICDx da Symantec ao Azure Sentinel. Microsoft Docs
description: Aprenda a usar o conector Symantec ICDx para ligar facilmente todos os seus registos de soluções de segurança Symantec com o Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: d068223f-395e-46d6-bb94-7ca1afd3503c
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 67fc80b5f34cf3a98fd39ddc352cb2dd9a5e7151
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85564919"
---
# <a name="connect-your-symantec-icdx-appliance"></a>Ligue o seu aparelho Symantec ICDx 



O conector ICDx Symantec permite-lhe ligar facilmente todos os seus registos de soluções de segurança Symantec com o seu Azure Sentinel, para ver dashboards, criar alertas personalizados e melhorar a investigação. Isto dá-lhe mais informações sobre a rede da sua organização e melhora as suas capacidades de operação de segurança. A integração entre o Symantec ICDx e o Azure Sentinel faz uso da REST API.


> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho em que está a executar o Azure Sentinel.

## <a name="configure-and-connect-symantec-icdx"></a>Configure e ligue o ICDx da Symantec 

O ICDx Symantec pode integrar e exportar registos diretamente para o Azure Sentinel.

1. Abra a Consola de Gestão ICDx para adicionar os reencaminhadores Microsoft Azure Sentinel (Log Analytics).
2. Na barra de navegação ICDx, clique em **Configuração**. 
3. No topo do ecrã de **configuração,** clique em **Forwarders**.
4. Em **Forwarders**, ao lado do Microsoft Azure Sentinel (Log Analytics), clique em **Adicionar**. 
4. Na janela **Microsoft Azure Sentinel (Log Analytics),** clique em **Mostrar Avançado**. 
5. No topo da janela expandida para Microsoft Azure Sentinel (Log Analytics), faça o seguinte:
    -   **Nome**: Digite um nome para o reencaminhador que não tenha mais de 30 caracteres. Escolha um nome único e significativo. Este nome aparece na lista de reencaminhadores no ecrã de **configuração** e nos dashboards no ecrã do **Painel de Instrumentos.** Por exemplo: Microsoft Azure Log Analytics East. Este campo é obrigatório.
    -   **Descrição**: Digite uma descrição para o reencaminhador. Esta descrição também aparece na lista de reencaminhadores no ecrã **de configuração.** Inclua detalhes como o tipo de evento que está sendo reencaminhado e o grupo que precisa de inspecionar os dados.
    -   **Tipo de Arranque**: Selecione o método de arranque para a configuração do reencaminhador. As suas opções são manuais e automáticas.<br>O padrão é automático. 
6. Em **Eventos**, faça o seguinte: 
    - **Fonte**: Selecione um ou mais arquivos a partir dos quais para encaminhar eventos. Pode selecionar arquivos de coletor ativos (incluindo o Arquivo Comum), arquivos de coletor órfãos (isto é, arquivos para os colecionadores que apagou), arquivos recetores ICDx ou o Arquivo do Sistema. <br>O padrão é o Arquivo Comum.
      > [!NOTE]
      > Os arquivos recetores ICDx são listados separadamente, pelo nome. 
 
    - **Filtro**: Adicione um filtro que especifique o subconjunto de eventos para encaminhar. Faça um dos seguintes:
        - Para selecionar uma condição de filtro, clique num Tipo, Atributo, Operador e Valor. 
        - No campo Filtro, reveja a sua condição de filtro. Pode editá-lo diretamente no campo ou eliminá-lo conforme necessário.
        - Clique em E ou OU para adicionar à sua condição de filtro.
        - Também pode clicar em Consultas Guardadas para aplicar uma consulta guardada.
    - **Atributos Incluídos**: Digite a lista de atributos delimitados por vírgula para incluir nos dados reencaminhados. Os atributos incluídos têm precedência sobre atributos excluídos.
    - **Atributos excluídos**: Digite a lista de atributos delimitados por vírgula para excluir dos dados reencaminhados.
    - **Tamanho do lote**: Selecione o número de eventos a enviar por lote. Suas opções são 10, 50, 100, 500 e 1000.<br>A predefinição é 100. 
    - **Limite de tarifa**: Selecione a taxa a que os eventos são reencaminhados, expressos como eventos por segundo. As suas opções são Ilimitadas, 500, 1000, 5000, 10000. <br> O padrão é 5000. 
7. No **destino Azure,** faça o seguinte: 
    - **ID do espaço de trabalho**: Cole o ID do espaço de trabalho a partir de baixo. Este campo é obrigatório.
    - **Tecla primária**: Cole a chave primária a partir de baixo. Este campo é obrigatório.
    - **Nome de registo personalizado**: Digite o nome de registo personalizado no espaço de trabalho do portal Microsoft Azure Log Analytics para o qual vai encaminhar eventos. O padrão é SymantecICDx. Este campo é obrigatório.
8. Clique *em Guardar* para terminar a configuração do reencaminhador. 
9. Para iniciar o reencaminhador, em **Opções,** clique em **Mais** e, em seguida, **Iniciar**.
10. Para utilizar o esquema relevante no Log Analytics para os eventos Symantec ICDx, procure **SymantecICDx_CL**.


## <a name="validate-connectivity"></a>Validar conectividade

Pode demorar mais de 20 minutos até que os seus registos comecem a aparecer no Log Analytics. 



## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a ligar o Symantec ICDx ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
- [Utilize livros para](tutorial-monitor-your-data.md) monitorizar os seus dados.


