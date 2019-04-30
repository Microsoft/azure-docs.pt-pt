---
title: Ligue os dados de ICDX da Symantec para a pré-visualização de sentinela do Azure | Documentos da Microsoft
description: Saiba como ligar a dados da Symantec ICDX a sentinela do Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: d068223f-395e-46d6-bb94-7ca1afd3503c
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 1a6843fb1668307aa442011233999c648901d404
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60714551"
---
# <a name="connect-your-symantec-icdx-appliance"></a>Ligar a aplicação Symantec ICDX 

> [!IMPORTANT]
> Sentinel do Azure está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Conector da Symantec ICDX permite-lhe ligar facilmente todos os seus Symantec solução logs de segurança com o Azure sentinela, para ver os dashboards e criar alertas personalizados e a melhorar a investigação. Isso dá-lhe mais informações sobre a rede da sua organização e melhora as capacidades de operação de segurança. Integração entre ICDX da Symantec e o Azure sentinela faz uso da REST API.


> [!NOTE]
> Os dados serão armazenados na localização geográfica da área de trabalho no qual está a executar sentinela do Azure.

## <a name="configure-and-connect-symantec-icdx"></a>Configurar e ligar Symantec ICDX 

Symantec ICDX pode integrar e exportar registos diretamente para o Azure sentinela.

1. Abra a consola de gestão ICDX.
2. No menu de navegação esquerdo, selecione **Configuration** e, em seguida, o **reencaminhadores** separador.
3. Na linha do Microsoft Azure Log Analytics, clique em **mais**, seguido **editar**. 
4. Na **reencaminhador do Microsoft Azure Log Analytics** janela, defina o seguinte:
    - Deixe o nome de registo personalizado como padrão, SymantecICDX.
    - Copie o ID de área de trabalho e cole-a no **identificador de cliente** campo. Copiar o **chave primária** e cole-o no campo de chave partilhada. Pode copiar esses valores a partir do portal do Azure sentinela selecionando **conectores de dados** e, em seguida **Symantec ICDX**.
6. Para utilizar o esquema relevante no Log Analytics para os eventos de ICDX da Symantec, procure **SymantecICDX_CL**.


## <a name="validate-connectivity"></a>Validar a conectividade

Pode demorar mais de 20 minutos até que os seus registos começam a aparecer no Log Analytics. 



## <a name="next-steps"></a>Passos Seguintes
Neste documento, aprendeu a ligar ICDX da Symantec para sentinela do Azure. Para saber mais sobre sentinela do Azure, veja os artigos seguintes:
- Saiba como [Obtenha visibilidade sobre os seus dados e a potenciais ameaças](quickstart-get-visibility.md).
- Começar a utilizar [deteção de ameaças com Azure sentinela](tutorial-detect-threats.md).

