---
title: Descodificar mensagens EDIFACT
description: Validar edi e gerar reconhecimentos com o descodificador de mensagens EDIFACT para Aplicações Lógicas Azure com Pacote de Integração Empresarial
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, divswa, logicappspm
ms.topic: article
ms.date: 01/27/2017
ms.openlocfilehash: 78c0d20c0f32a6d63d134e958b30d38fe11fcc5c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74790662"
---
# <a name="decode-edifact-messages-for-azure-logic-apps-with-the-enterprise-integration-pack"></a>Descodificar mensagens EDIFACT para Aplicações Lógicas Azure com o Pacote de Integração Empresarial

Com o conector de mensagens Decode EDIFACT, pode validar propriedades EDI e específicas de parceiros, dividir intercâmbios em conjuntos de transações ou preservar intercâmbios inteiros, e gerar reconhecimentos para transações processadas. Para utilizar este conector, deve adicionar o conector a um gatilho existente na sua aplicação lógica.

## <a name="before-you-start"></a>Antes de começar

Aqui estão os itens que precisa:

* Uma conta Azure; pode criar uma [conta gratuita](https://azure.microsoft.com/free)
* Uma conta de [integração](logic-apps-enterprise-integration-create-integration-account.md) que já está definida e associada à sua subscrição Azure. Deve ter uma conta de integração para utilizar o conector de mensagens Decode EDIFACT. 
* Pelo menos dois [parceiros](logic-apps-enterprise-integration-partners.md) que já estão definidos na sua conta de integração
* Um [acordo EDIFACT](logic-apps-enterprise-integration-edifact.md) que já está definido na sua conta de integração

## <a name="decode-edifact-messages"></a>Descodificar mensagens EDIFACT

1. [Criar uma aplicação lógica.](quickstart-create-first-logic-app-workflow.md)

2. O conector de mensagens Decode EDIFACT não tem gatilhos, pelo que deve adicionar um gatilho para iniciar a sua aplicação lógica, como um gatilho de Pedido. No Logic App Designer, adicione um gatilho e adicione uma ação à sua aplicação lógica.

3. Na caixa de pesquisa, introduza "EDIFACT" como filtro. Selecione **Decode EDIFACT Message**.
   
    ![pesquisar EDIFACT](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage1.png)

3. Se não criou anteriormente quaisquer ligações à sua conta de integração, é-lhe pedido que crie essa ligação agora. Nomeie a sua ligação e selecione a conta de integração que pretende ligar.
   
    ![criar conta de integração](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage2.png)

    São necessárias propriedades com asterisco.

    | Propriedade | Detalhes |
    | --- | --- |
    | Nome da ligação * |Insira qualquer nome para a sua ligação. |
    | Conta de Integração * |Insira um nome para a sua conta de integração. Certifique-se de que a sua conta de integração e app lógica estão na mesma localização Azure. |

4. Quando terminar para terminar de criar a sua ligação, escolha **Criar**. Os seus dados de ligação devem ser semelhantes a este exemplo:

    ![detalhes da conta de integração](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage3.png)  

5. Após a criação da sua ligação, como mostra este exemplo, selecione a mensagem de ficheiro plano EDIFACT para descodificar.

    ![conexão conta de integração criada](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage4.png)  

    Por exemplo:

    ![Selecione a mensagem de ficheiro plano EDIFACT para descodificação](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage5.png)  

## <a name="edifact-decoder-details"></a>Detalhes do descodificador EDIFACT

O conector Decode EDIFACT executa estas tarefas: 

* Valida o envelope contra o acordo de parceiro comercial.
* Resolve o acordo combinando o identificador de qualificação de remetente & identificador e recetor & identificador.
* Divide um intercâmbio em várias transações quando o intercâmbio tem mais de uma transação com base na configuração de configurações de receção do contrato.
* Desmonta o intercâmbio.
* Valida o EDI e as propriedades específicas do parceiro, incluindo:
  * Validação da estrutura do envelope de intercâmbio
  * Validação do envelope contra o esquema de controlo
  * Validação do esquema dos elementos de dados definidos por transações contra o esquema da mensagem
  * Validação EDI realizada em elementos de dados definidos por transações
* Verifica que os números de controlo de intercâmbio, grupo e conjunto de transações não são duplicados (se configurados) 
  * Verifica o número de controlo de intercâmbio contra as trocas anteriormente recebidas. 
  * Verifica o número de controlo do grupo com outros números de controlo do grupo no intercâmbio. 
  * Verifica o número de controlo definido pela transação contra outros números de controlo definidos por transações nesse grupo.
* Divide o intercâmbio em conjuntos de transações ou preserva todo o intercâmbio:
  * Divisões como conjuntos de transações - suspenda os conjuntos de transações por erro: Divide o intercâmbio em conjuntos de transações e analisa cada conjunto de transações. 
  O X12 Decode produz apenas os conjuntos `badMessages`de transações que falham `goodMessages`na validação e as saídas que as restantes transações se fixam para .
  * Divisões como conjuntos de transações - suspenda o intercâmbio por erro: Divide o intercâmbio em conjuntos de transações e analisa cada conjunto de transações. 
  Se um ou mais conjuntos de transações na validação de falhas de troca, a `badMessages`ação X12 Decode produz todos os conjuntos de transações nesse intercâmbio para .
  * Preserve o Interchange - suspenda os conjuntos de transações por erro: Preservar o intercâmbio e processar todo o intercâmbio em lota. 
  O X12 Decode produz apenas os conjuntos `badMessages`de transações que falham `goodMessages`na validação e as saídas que as restantes transações se fixam para .
  * Preservar o Intercâmbio - suspender o intercâmbio por erro: Preservar o intercâmbio e processar todo o intercâmbio em lota. 
  Se um ou mais conjuntos de transações na validação de falhas de troca, a `badMessages`ação X12 Decode produz todos os conjuntos de transações nesse intercâmbio para .
* Gera um reconhecimento técnico (controlo) e/ou funcional (se configurado).
  * Um Reconhecimento Técnico ou o CONTRL ACK reportam os resultados de uma verificação sintática do intercâmbio recebido completo.
  * Um reconhecimento funcional reconhece aceitar ou rejeitar um intercâmbio recebido ou um grupo

## <a name="view-swagger-file"></a>Ver ficheiro Swagger
Para ver os detalhes da Swagger para o conector EDIFACT, consulte [EDIFACT](/connectors/edifact/).

## <a name="next-steps"></a>Passos seguintes
[Saiba mais sobre o Pacote de Integração Empresarial](logic-apps-enterprise-integration-overview.md "Saiba mais sobre o Pacote de Integração Empresarial") 

