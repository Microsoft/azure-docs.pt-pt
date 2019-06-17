---
title: Ligue os dados de ICDX da Symantec para a pré-visualização de sentinela do Azure | Documentos da Microsoft
description: Saiba como ligar a dados da Symantec ICDX a sentinela do Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: d068223f-395e-46d6-bb94-7ca1afd3503c
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2019
ms.author: rkarlin
ms.openlocfilehash: 3b21371d6321b208b19ca8b2524308736c3ceca9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66244348"
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

1. Abra a consola de gestão ICDX para adicionar reencaminhadores do Microsoft Azure sentinela (Log Analytics).
2. Na barra de navegação ICDx, clique em **configuração**. 
3. Na parte superior a **Configuration** ecrã, clique em **reencaminhadores**.
4. Sob **reencaminhadores**, junto ao Microsoft Azure sentinela (Log Analytics), clique em **Add**. 
4. Na **Microsoft Azure sentinela (Log Analytics)** janela, clique em **Mostrar avançadas**. 
5. Na parte superior do expandidos para a janela do Microsoft Azure sentinela (Log Analytics), proceda do seguinte:
    -   **Nome**: Escreva um nome para o reencaminhador com não mais de 30 carateres. Escolha um nome exclusivo e significativo. Este nome é apresentado na lista de reencaminhadores no **Configuration** ecrã e nos dashboards na **Dashboard** ecrã. Por exemplo: Microsoft Azure Log Analytics East. Este campo é obrigatório.
    -   **Descrição**: Escreva uma descrição para o reencaminhador. Esta descrição também aparece na lista de reencaminhadores no **configuração** ecrã. Incluem detalhes como o tipo de evento a ser reencaminhados e o grupo de que precisa para inspecionar os dados.
    -   **Tipo de arranque**: Selecione o método de inicialização para a configuração de reencaminhador. As opções são manuais e automáticas.<br>A predefinição é automático. 
6. Sob **eventos**, efetue o seguinte procedimento: 
    - **origem**: Selecione um ou mais arquivos partir do qual pode reencaminhar os eventos. Pode selecionar os arquivos de recoletor de Active Directory (incluindo o arquivo comum), órfãos arquivos mortos de recoletor (ou seja, nos arquivos de recoletores de que eliminou), arquivos mortos de recetor ICDx ou o arquivo de sistema. <br>A predefinição é comum de arquivo.
      > [!NOTE]
      > Arquivos de recetor ICDx são listados separadamente, por nome. 
 
    - **Filtro**: Adicione um filtro que especifica o subconjunto de eventos para reencaminhar. Efetue uma das seguintes ações:
        - Para selecionar uma condição de filtro, clique num tipo de atributo, operador e valor. 
        - No campo de filtro, reveja a condição de filtro. Pode editá-lo diretamente no campo ou elimine-o conforme necessário.
        - Clique em e ou ou para adicionar a sua condição de filtro.
        - Também pode clicar em guardar consultas para aplicar uma consulta guardada.
    - **Incluído atributos**: Tipo de lista delimitada por vírgulas de atributos a serem incluídos nos dados reencaminhados. Os atributos incluídos têm precedência sobre atributos excluídos.
    - **Excluídos atributos**: Tipo de lista delimitada por vírgulas de atributos para impedir que os dados reencaminhados.
    - **Tamanho do lote**: Selecione o número de eventos para enviar por lote. As opções são 10, 50, 100, 500 e 1000.<br>A predefinição é 100. 
    - **Limite de velocidade**: Selecione a velocidade a que os eventos são reencaminhados, expresso como eventos por segundo. As opções são ilimitado, 500, 1000, 5000, 10000. <br> A predefinição é de 5000. 
7. Sob **do Azure de destino**, efetue o seguinte procedimento: 
    - **ID da área de trabalho**: Cole o ID de área de trabalho de abaixo. Este campo é obrigatório.
    - **Chave primária**: Cole a chave primária de abaixo. Este campo é obrigatório.
    - **Nome do registo personalizado**: Escreva o nome de registo personalizado no Microsoft Azure portal do Log Analytics área de trabalho para o qual pretende reencaminhar eventos. A predefinição é SymantecICDx. Este campo é obrigatório.
8. Clique em *guardar* para concluir a configuração de reencaminhador. 
9. Para iniciar o reencaminhador, em **opções**, clique em **mais** e, em seguida **iniciar**.
10. Para utilizar o esquema relevante no Log Analytics para os eventos de ICDX da Symantec, procure **SymantecICDX_CL**.


## <a name="validate-connectivity"></a>Validar a conectividade

Pode demorar mais de 20 minutos até que os seus registos começam a aparecer no Log Analytics. 



## <a name="next-steps"></a>Passos Seguintes
Neste documento, aprendeu a ligar ICDX da Symantec para sentinela do Azure. Para saber mais sobre sentinela do Azure, veja os artigos seguintes:
- Saiba como [Obtenha visibilidade sobre os seus dados e a potenciais ameaças](quickstart-get-visibility.md).
- Começar a utilizar [deteção de ameaças com Azure sentinela](tutorial-detect-threats.md).

