---
title: Descodificar mensagens EDIFACT
description: Validar eDI e gerar reconhecimentos com o descodifa de mensagem EDIFACT para apps lógicas Azure com Pacote de Integração Empresarial
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, divswa, logicappspm
ms.topic: article
ms.date: 04/22/2020
ms.openlocfilehash: b36641677dbf36402c7f578b9b1887c52f441afd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91322483"
---
# <a name="decode-edifact-messages-for-azure-logic-apps-with-the-enterprise-integration-pack"></a>Descodificar mensagens EDIFACT para apps lógicas Azure com o Pacote de Integração Empresarial

Com o conector Descodificar mensagem EDIFACT, pode validar as propriedades de EDI e parceiros específicos, dividir intercâmbios em conjuntos de transações ou preservar intercâmbios inteiros e gerar reconhecimentos para transações processadas. Para utilizar este conector, tem de o adicionar a um acionador existente na aplicação lógica.

## <a name="before-you-start"></a>Antes de começar

Aqui estão os itens que precisa:

* Uma conta Azure; pode criar uma [conta gratuita](https://azure.microsoft.com/free)
* Uma conta de [integração](logic-apps-enterprise-integration-create-integration-account.md) já definida e associada à sua subscrição do Azure. Tem de ter uma conta de integração para utilizar o conector de mensagens EDIFACT descodificar. 
* Pelo menos dois [parceiros](logic-apps-enterprise-integration-partners.md) que já estão definidos na sua conta de integração
* Um [acordo EDIFACT](logic-apps-enterprise-integration-edifact.md) que já está definido na sua conta de integração

## <a name="decode-edifact-messages"></a>Descodificar mensagens EDIFACT

> [!IMPORTANT]
> O conector EDIFACT suporta apenas caracteres UTF-8.
> Se a sua saída contiver caracteres inesperados, verifique se as suas mensagens EDIFACT utilizam o conjunto de caracteres UTF-8. 

1. [Crie uma aplicação lógica.](quickstart-create-first-logic-app-workflow.md)

2. O conector de mensagens EDIFACT descodifica não tem gatilhos, pelo que tem de adicionar um gatilho para iniciar a sua aplicação lógica, como um gatilho Request. No Logic App Designer, adicione um gatilho e, em seguida, adicione uma ação à sua aplicação lógica.

3. Na caixa de pesquisa, introduza "EDIFACT" como filtro. Selecione **Decode EDIFACT Message**.
   
    ![pesquisar EDIFACT](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage1.png)

3. Se não criou previamente quaisquer ligações à sua conta de integração, é solicitado que crie essa ligação agora. Nomeie a sua ligação e selecione a conta de integração que pretende ligar.
   
    ![criar conta de integração](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage2.png)

    São necessárias propriedades com asterisco.

    | Propriedade | Detalhes |
    | --- | --- |
    | Nome de conexão * |Insira qualquer nome para a sua ligação. |
    | Conta de Integração * |Insira um nome para a sua conta de integração. Certifique-se de que a sua conta de integração e aplicação lógica estão na mesma localização Azure. |

4. Quando terminar de criar a sua ligação, escolha **Criar**. Os seus dados de ligação devem ser semelhantes a este exemplo:

    ![detalhes da conta de integração](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage3.png)  

5. Após a criação da sua ligação, como mostrado neste exemplo, selecione a mensagem de ficheiro plano EDIFACT para descodificar.

    ![ligação conta de integração criada](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage4.png)  

    Por exemplo:

    ![Selecione a mensagem de ficheiro plano EDIFACT para descodição](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage5.png)  

## <a name="edifact-decoder-details"></a>Detalhes do descodificador EDIFACT

O conector EDIFACT descodifica estas tarefas: 

* Valida o envelope contra o acordo de parceiro comercial.
* Resolve o acordo combinando o qualificador de remetente & identificador e recetor & identificador.
* Divide um intercâmbio em várias transações quando o intercâmbio tem mais de uma transação com base na configuração de definições de receção do contrato.
* Desmonta o intercâmbio.
* Valida o EDI e propriedades específicas do parceiro, incluindo:
  * Validação da estrutura do envelope de intercâmbio
  * Validação do esquema do envelope contra o esquema de controlo
  * Validação de esquema dos elementos de dados definidos pela transação contra o esquema de mensagem
  * Validação do EDI realizada em elementos de dados definidos por transações
* Verifica se os números de controlo do intercâmbio, do grupo e do conjunto de transações não são duplicados (se configurados) 
  * Verifica o número de controlo de intercâmbio contra as trocas previamente recebidas. 
  * Verifica o número de controlo do grupo contra outros números de controlo do grupo no intercâmbio. 
  * Verifica o número de controlo da transação em número de controlo de outras transações definidas nesse grupo.
* Divide o intercâmbio em conjuntos de transações ou preserva todo o intercâmbio:
  * Split Interchange como conjuntos de transações - suspender conjuntos de transações por erro: Divide o intercâmbio em conjuntos de transações e analisa cada conjunto de transações. 
  O EDIFACT Decode outputs de ação apenas os conjuntos de transações que falham na validação `badMessages` de , e as saídas que as restantes transações definem para `goodMessages` .
  * Split Interchange como conjuntos de transações - suspender o intercâmbio por erro: Divide o intercâmbio em conjuntos de transações e analisa cada conjunto de transações. 
  Se uma ou mais transações se estabelecerem na validação de falhas de câmbio, a ação de descodificar o EDIFACT produz todos os conjuntos de transações nesse intercâmbio para `badMessages` .
  * Preserve Interchange - suspenda os conjuntos de transações por erro: Preserve o intercâmbio e processe todo o intercâmbio em lote. 
  O EDIFACT Decode outputs de ação apenas os conjuntos de transações que falham na validação `badMessages` de , e as saídas que as restantes transações definem para `goodMessages` .
  * Preservar o Intercâmbio - suspender o intercâmbio por erro: Preservar o intercâmbio e processar todo o intercâmbio em lote. 
  Se uma ou mais transações se estabelecerem na validação de falhas de câmbio, a ação de descodificar o EDIFACT produz todos os conjuntos de transações nesse intercâmbio para `badMessages` .
* Gera um reconhecimento técnico (controlo) e/ou funcional (se configurado).
  * Um Reconhecimento Técnico ou a CONTRL ACK reporta os resultados de uma verificação sintática do intercâmbio completo recebido.
  * Um reconhecimento funcional reconhece aceitar ou rejeitar um intercâmbio recebido ou um grupo

## <a name="view-swagger-file"></a>Ver ficheiro Swagger
Para ver os detalhes da Swagger para o conector EDIFACT, consulte [EDIFACT](/connectors/edifact/).

## <a name="next-steps"></a>Passos seguintes
[Saiba mais sobre o Pacote de Integração Empresarial](logic-apps-enterprise-integration-overview.md "Saiba mais sobre o Pacote de Integração Empresarial") 

