---
title: Ligue os dados do ICDx symantec ao Azure Sentinel. Microsoft Docs
description: Saiba como ligar dados icdx symantec a Azure Sentinel.
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
ms.openlocfilehash: cac63aee5f9ebf3859b138e6444e40b1e2dd30f7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77588098"
---
# <a name="connect-your-symantec-icdx-appliance"></a>Ligue o seu aparelho ICDx Symantec 



O conector ICDx Symantec permite-lhe ligar facilmente todos os seus registos de soluções de segurança Symantec com o seu Azure Sentinel, ver dashboards, criar alertas personalizados e melhorar a investigação. Isto dá-lhe mais informações sobre a rede da sua organização e melhora as suas capacidades de operação de segurança. A integração entre o ICDx symantec e o Azure Sentinel utiliza a API REST.


> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho em que está a executar o Azure Sentinel.

## <a name="configure-and-connect-symantec-icdx"></a>Configure e ligue icDx symantec 

O ICDx symantec pode integrar e exportar registos diretamente para o Azure Sentinel.

1. Abra a consola de gestão ICDx para adicionar os avançados Microsoft Azure Sentinel (Log Analytics).
2. Na barra de navegação ICDx, clique na **Configuração**. 
3. Na parte superior do ecrã de **Configuração,** clique em **Forwarders**.
4. Em **Forwarders**, ao lado do Microsoft Azure Sentinel (Log Analytics), clique em **Adicionar**. 
4. Na janela **Microsoft Azure Sentinel (Log Analytics),** clique no **Show Advanced**. 
5. No topo da janela expandida para microsoft Azure Sentinel (Log Analytics), faça o seguinte:
    -   **Nome**: Digite um nome para o avançado que não tenha mais de 30 caracteres. Escolha um nome único e significativo. Este nome aparece na lista de avançados no ecrã de **Configuração** e nos dashboards no ecrã **do Dashboard.** Por exemplo: Microsoft Azure Log Analytics East. Este campo é obrigatório.
    -   **Descrição**: Digite uma descrição para o reencaminhador. Esta descrição também aparece na lista de avançados no ecrã de **Configuração.** Inclua detalhes como o tipo de evento que está a ser encaminhado e o grupo que precisa de inspecionar os dados.
    -   **Tipo de arranque**: Selecione o método de arranque para a configuração do avançado. As suas opções são manuais e automáticas.<br>O padrão é Automático. 
6. Em **Eventos,** faça o seguinte: 
    - **Fonte**: Selecione um ou mais arquivos a partir dos quais para encaminhar eventos. Pode selecionar arquivos de colecionadores ativos (incluindo o Arquivo Comum), arquivos de colecionadores órfãos (isto é, arquivos para os colecionadores que você eliminou), arquivos recetores ICDx ou o Arquivo do Sistema. <br>O padrão é O Arquivo Comum.
      > [!NOTE]
      > Os arquivos recetores ICDx são listados separadamente, pelo nome. 
 
    - **Filtro**: Adicione um filtro que especifica o subconjunto dos eventos para a frente. Efetue uma das seguintes ações:
        - Para selecionar uma condição de filtro, clique num Tipo, Atributo, Operador e Valor. 
        - No campo Filtro, reveja a sua condição de filtro. Pode editá-lo diretamente no campo ou eliminá-lo conforme necessário.
        - Clique em E ou OU para adicionar ao seu estado de filtro.
        - Também pode clicar em "Guardar Consultas" para aplicar uma consulta guardada.
    - **Atributos Incluídos**: Digite a lista de atributos delimitados da vírlém a incluir nos dados reencaminhados. Os atributos incluídos têm precedência sobre atributos excluídos.
    - **Atributos excluídos**: Digite a lista de atributos delimitados da vírlém para excluir dos dados reencaminhados.
    - **Tamanho do lote**: Selecione o número de eventos a enviar por lote. Suas opções são 10, 50, 100, 500 e 1000.<br>A predefinição é 100. 
    - **Limite de tarifa**: Selecione a taxa a que os eventos são reencaminhados, expressacomo eventos por segundo. As suas opções são Ilimitadas, 500, 1000, 5000, 10000. <br> O padrão é 5000. 
7. No âmbito do **Destino Azure,** faça o seguinte: 
    - **ID do espaço de trabalho**: Colar o ID workspace a partir de baixo. Este campo é obrigatório.
    - **Chave primária**: Colar a chave primária a partir de baixo. Este campo é obrigatório.
    - **Nome**de registo personalizado : Digite o nome de registo personalizado no portal Microsoft Azure Log Analytics espaço de trabalho para o qual vai reencaminhar eventos. O padrão é SymantecICDx. Este campo é obrigatório.
8. Clique em *Guardar* para terminar a configuração do avançado. 
9. Para iniciar o avançado, em **Opções,** clique **em Mais** e **depois Começar**.
10. Para utilizar o esquema relevante no Log Analytics para os eventos icdx symantec, procure **SymantecICDx_CL**.


## <a name="validate-connectivity"></a>Validar a conectividade

Pode demorar mais de 20 minutos até que os seus registos comecem a aparecer no Log Analytics. 



## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a ligar o ICDx symantec a Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Aprenda a [obter visibilidade nos seus dados e ameaças potenciais.](quickstart-get-visibility.md)
- Começar [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
- [Utilize livros](tutorial-monitor-your-data.md) de trabalho para monitorizar os seus dados.


