---
title: Use o portal Azure para resolver problemas Azure Stack Edge Microsoft Docs
description: Saiba como resolver problemas no seu Azure Stack Edge. Pode executar diagnósticos, recolher informações para suporte e usar registos para resolver problemas.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 03/15/2019
ms.author: alkohli
ms.openlocfilehash: b80b47a1be67a9771d138c43178d50156a6f1a95
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/07/2020
ms.locfileid: "87926438"
---
# <a name="troubleshoot-your-azure-stack-edge-issues"></a>Resolução de problemas dos seus problemas com a Azure Stack Edge

Este artigo descreve como resolver problemas no seu Azure Stack Edge. 

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Executar diagnósticos
> * Recolher pacote de Suporte
> * Utilizar registos para resolução de problemas


## <a name="run-diagnostics"></a>Executar diagnósticos

Para diagnosticar e resolver quaisquer erros do dispositivo, pode executar os testes de diagnóstico. Efetue os seguintes passos na IU da Web local no dispositivo para executar testes de diagnóstico.

1. Na IU da Web local, aceda a **Resolução de problemas > Testes de diagnóstico**. Selecione o teste que pretende executar e clique em **Executar teste**. Esta ação executa os testes para diagnosticar possíveis problemas nas definições de rede, dispositivo, proxy Web, hora ou cloud. Será notificado de que o dispositivo está a executar testes.

    ![Selecione testes](media/azure-stack-edge-troubleshoot/run-diag-1.png)
 
2. Depois de os testes estarem concluídos, os resultados são apresentados. 

    ![Rever os resultados dos testes](media/azure-stack-edge-troubleshoot/run-diag-2.png)

    Se um teste falhar, é apresentado um URL para a ação recomendada. Pode clicar no URL para ver a ação recomendada.
 
    ![Avisos de revisão para testes falhados](media/azure-stack-edge-troubleshoot/run-diag-3.png)


## <a name="collect-support-package"></a>Recolher pacote de Suporte

Um pacote de registos é composto por todos os registos relevantes que possam ajudar o Suporte da Microsoft a resolver quaisquer problemas do dispositivo. Pode gerar um pacote de registos através da IU da Web local.

Efetue os seguintes passos para recolher um pacote de Suporte. 

1. Na IU da Web local, aceda a **Resolução de problemas > Suporte**. Clique em **Criar pacote de suporte**. O sistema começa a recolher o pacote de suporte. A recolha do pacote pode demorar vários minutos.

    ![Clicar em adicionar utilizador](media/azure-stack-edge-troubleshoot/collect-logs-1.png)
 
2. Depois de o pacote de Suporte estar criado, clique em **Transferir pacote de Suporte**. Um pacote zipado é transferido no caminho que escolher. Pode deszipar o pacote e ver os ficheiros de registo do sistema.

    ![Clicar em adicionar utilizador](media/azure-stack-edge-troubleshoot/collect-logs-2.png)

## <a name="use-logs-to-troubleshoot"></a>Utilizar registos para resolução de problemas

Quaisquer erros ocorridos durante os processos de carregamento e atualização são incluídos nos ficheiros de erro correspondente.

1. Para ver os ficheiros de erro, aceda à partilha e clique na partilha para ver o conteúdo. 

      ![Ligar e visualizar conteúdos de partilha](media/azure-stack-edge-troubleshoot/troubleshoot-logs-1.png)

2. Clique na _pasta Microsoft Azure Stack Edge_. Esta pasta tem duas subpastas:

    - A pasta de carregamentos, que inclui os ficheiros de registos com erros de carregamento.
    - A pasta de atualizações para os erros durante a atualização.

    Eis um ficheiro de registo de exemplo para atualização.

    ```
    <root container="test1" machine="VM15BS020663" timestamp="03/18/2019 00:11:10" />
    <file item="test.txt" local="False" remote="True" error="16001" />
    <summary runtime="00:00:00.0945320" errors="1" creates="2" deletes="0" insync="3" replaces="0" pending="9" />
    ``` 

3. Quando vir um erro neste ficheiro (realçado no exemplo), preste atenção ao código de erro, neste caso é 16001. Procure a descrição deste código de erro em relação à seguinte referência de erro.

    [!INCLUDE [data-box-edge-edge-upload-error-reference](../../includes/data-box-edge-gateway-upload-error-reference.md)]


## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre os [problemas conhecidos nesta versão](data-box-gateway-release-notes.md).
