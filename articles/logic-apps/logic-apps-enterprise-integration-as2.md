---
title: Nas mensagens AS2 para a integração empresarial de B2B - Azure Logic Apps | Documentos da Microsoft
description: Trocar mensagens AS2 para enterprise integração B2B no Azure Logic Apps Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: c9b7e1a9-4791-474c-855f-988bd7bf4b7f
ms.date: 06/08/2017
ms.openlocfilehash: 3413b235d9202530eb1a3129637e3746bbe6585b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57872582"
---
# <a name="exchange-as2-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Trocar mensagens AS2 para enterprise integração B2B no Azure Logic Apps Enterprise Integration Pack

Antes de pode trocar mensagens AS2 para o Azure Logic Apps, tem de criar um contrato de AS2 e armazenar esse contrato na sua conta de integração. Eis os passos para saber como criar um contrato de AS2.

## <a name="before-you-start"></a>Antes de começar

Eis os itens que precisa:

* Uma [conta de integração](../logic-apps/logic-apps-enterprise-integration-accounts.md) que já definida e associada à sua subscrição do Azure
* Pelo menos dois [parceiros](logic-apps-enterprise-integration-partners.md) que já definidas na sua conta de integração e configurado com o qualificador de AS2 em **identidades de negócio**

> [!NOTE]
> Quando cria um contrato, o conteúdo do ficheiro do contrato tem de corresponder ao tipo de contrato.    

Depois de [criar uma conta de integração](../logic-apps/logic-apps-enterprise-integration-accounts.md) e [adicionar parceiros](logic-apps-enterprise-integration-partners.md), pode criar um contrato de AS2 ao seguir estes passos.

## <a name="create-an-as2-agreement"></a>Crie um contrato de AS2

1.  Inicie sessão no [portal do Azure](https://portal.azure.com "portal do Azure").  

2. No menu principal do Azure, selecione **todos os serviços**. Na caixa de pesquisa, introduza "integração" e, em seguida, selecione **contas de integração**.

   ![Localize a conta de integração](./media/logic-apps-enterprise-integration-as2/overview-1.png)

   > [!TIP]
   > Se não vir **todos os serviços**, poderá ter de expandir o menu pela primeira vez. Na parte superior do menu fechado, selecione **Mostrar etiquetas de texto**.

3. Sob **contas de integração**, selecione a conta de integração em que pretende criar o contrato.

   ![Selecione onde pretende criar o contrato de conta de integração](./media/logic-apps-enterprise-integration-overview/overview-3.png)

4. Escolha o **contratos** mosaico. Se não tiver um mosaico de contratos, adicione primeiro o mosaico.

    ![Escolha o que mosaico "Contratos"](./media/logic-apps-enterprise-integration-as2/agreement-1.png)

5. Sob **contratos**, escolha **Add**.

    ![Escolha "Adicionar"](./media/logic-apps-enterprise-integration-as2/agreement-2.png)

6. Sob **Add**, introduza um **nome** para seu contrato. Para **tipo de contrato**, selecione **AS2**. Selecione o **parceiro do anfitrião**, **identidade do anfitrião**, **parceiro convidado**, e **identidade do convidado** para seu contrato.

    ![Fornecer detalhes do contrato](./media/logic-apps-enterprise-integration-as2/agreement-3.png)  

    | Propriedade | Descrição |
    | --- | --- |
    | Name |Nome do contrato |
    | Tipo de Contrato | Deve ser AS2 |
    | Parceiro do Anfitrião |Tem de um contrato de parceiro de um anfitrião e convidado. O parceiro do anfitrião representa a organização que configura o contrato. |
    | Identidade do Anfitrião |Um identificador para o parceiro do anfitrião |
    | Parceiro Convidado |Tem de um contrato de parceiro de um anfitrião e convidado. O parceiro convidado representa a organização que está negociando com o parceiro do anfitrião. |
    | Identidade do Convidado |Um identificador para o parceiro convidado |
    | Definições de Receção |Estas propriedades aplicam-se a todas as mensagens recebidas por um contrato. |
    | Definições de Envio |Estas propriedades aplicam-se a todas as mensagens enviadas por um contrato. |

## <a name="configure-how-your-agreement-handles-received-messages"></a>Configurar a forma como seus identificadores de contrato receberam mensagens

Agora que definiu as propriedades de contrato, é possível configurar como o presente contrato identifica e processa mensagens de entrada recebidas do seu parceiro por meio do presente contrato.

1.  Sob **Add**, selecione **receber definições**.
Configure estas propriedades com base no seu contrato com o parceiro que troca mensagens com. Para obter descrições de propriedade, consulte a tabela nesta secção.

    ![Configurar "Receber definições"](./media/logic-apps-enterprise-integration-as2/agreement-4.png)

2. Opcionalmente, pode substituir as propriedades de mensagens de entrada, selecionando **substituir propriedades da mensagem**.

3. Para exigir que todas as mensagens recebidas sejam assinados, selecione **deve ser assinada a mensagem**. Partir do **certificado** , selecione um existente [certificado público do parceiro convidado](../logic-apps/logic-apps-enterprise-integration-certificates.md) para validar a assinatura as mensagens. Ou criar o certificado, se não tiver uma.

4.  Para exigir que todas as mensagens recebidas sejam encriptados, selecione **mensagem deve ser encriptada**. Partir do **certificado** , selecione um existente [certificado privado do parceiro anfitrião](../logic-apps/logic-apps-enterprise-integration-certificates.md) para desencriptar mensagens de entrada. Ou criar o certificado, se não tiver uma.

5. Para exigir que as mensagens para ser comprimido, selecione **mensagem deve ser comprimida**.

6. Para enviar uma notificação de disposição de mensagem síncrona (MDN) para as mensagens recebidas, selecione **enviar MDN**.

7. Para enviar MDNs assinados para as mensagens recebidas, selecione **enviar MDN assinado**.

8. Para enviar MDNs assíncronas para as mensagens recebidas, selecione **enviar MDN assíncrono**.

9. Depois de terminar, certifique-se de guardar as definições ao escolher **OK**.

Agora o seu contrato está pronto para lidar com mensagens de entrada que está em conformidade com as definições selecionadas.

| Propriedade | Descrição |
| --- | --- |
| Substituir propriedades da mensagem |Indica que as propriedades nas mensagens recebidas podem ser substituídas. |
| A mensagem deve estar assinada |Requer que as mensagens sejam assinados digitalmente. Configure o certificado público do parceiro convidado para verificação da assinatura.  |
| A mensagem deve ser encriptada |Requer que as mensagens sejam encriptados. Mensagens criptografadas não são rejeitadas. Configure o certificado privado de parceiro de anfitrião para desencriptar as mensagens.  |
| A mensagem deve ser comprimida |Necessita de mensagens para ser comprimido. Mensagens de compressão não são rejeitadas. |
| Texto MDN |A predefinição disposição notificação de mensagem (MDN) a serem enviados para o remetente da mensagem. |
| Enviar MDN |Requer MDNs a serem enviados. |
| Enviar MDN assinado |Requer MDNs sejam assinados. |
| Algoritmo MIC |Selecione o algoritmo a utilizar para assinar mensagens. |
| Enviar MDN assíncrono | Requer mensagens sejam enviadas de forma assíncrona. |
| do IdP | Especifique o URL para onde enviar os MDNs. |

## <a name="configure-how-your-agreement-sends-messages"></a>Configurar a forma como o seu contrato envia mensagens

É possível configurar como o presente contrato identifica e processa mensagens de saída que envia para os seus parceiros através do presente contrato.

1.  Sob **Add**, selecione **enviar definições**.
Configure estas propriedades com base no seu contrato com o parceiro que troca mensagens com. Para obter descrições de propriedade, consulte a tabela nesta secção.

    ![Defina as propriedades de "Enviar definições"](./media/logic-apps-enterprise-integration-as2/agreement-51.png)

2. Para enviar mensagens assinadas para o seu parceiro, selecione **ativar a assinatura da mensagem**. Para além de assinatura de mensagens, o **algoritmo MIC** lista, selecione a *certificado privado de parceiro anfitrião algoritmo MIC*. E, no **certificado** , selecione um existente [certificado privado do parceiro anfitrião](../logic-apps/logic-apps-enterprise-integration-certificates.md).

3. Para enviar mensagens criptografadas para o parceiro, selecione **ativar a encriptação de mensagens**. Para encriptar as mensagens no **algoritmo de encriptação** lista, selecione a *algoritmo de certificado público de parceiro convidado*.
E, no **certificado** , selecione um existente [certificado público do parceiro convidado](../logic-apps/logic-apps-enterprise-integration-certificates.md).

4. Para comprimir a mensagem, selecione **ativar a compressão de mensagem**.

5. Para desdobrar o cabeçalho de tipo de conteúdo HTTP para uma única linha, selecione **cabeçalhos HTTP Desdobrar**.

6. Para receber MDNs síncronos para as mensagens enviadas, selecione **pedir MDN**.

7. Para receber MDNs assinados para as mensagens enviadas, selecione **pedir MDN assinado**.

8. Para receber MDNs assíncronas para as mensagens enviadas, selecione **pedir MDN assíncrono**. Se selecionar esta opção, introduza o URL para onde enviar os MDNs.

9. Para exigir a não rejeição de receção, selecione **ativar NRR**.  

10. Para especificar o formato de algoritmo a utilizar o MIC ou assinatura nos cabeçalhos de saída da mensagem AS2 ou MDN, selecione **formato do algoritmo SHA2**.  

11. Depois de terminar, certifique-se de guardar as definições ao escolher **OK**.

Agora o seu contrato está pronto para lidar com mensagens de saída que estão em conformidade com as definições selecionadas.

| Propriedade | Descrição |
| --- | --- |
| Ativar assinatura de mensagens |Requer que todas as mensagens que são enviadas do contrato sejam assinados. |
| Algoritmo MIC |O algoritmo a utilizar para assinar mensagens. Configura o certificado de privada de parceiro do anfitrião algoritmo MIC para as mensagens de assinatura. |
| Certificado |Selecione o certificado a utilizar para assinar mensagens. Configura o certificado privado de parceiro de anfitrião para as mensagens de assinatura. |
| Ativar encriptação de mensagens |Necessita de encriptação de todas as mensagens que são enviadas do presente contrato. Configura o algoritmo de certificado público de parceiro convidado para encriptar as mensagens. |
| Algoritmo de Encriptação |O algoritmo de encriptação a utilizar para encriptação de mensagens. Configura o certificado público do parceiro convidado para encriptar as mensagens. |
| Certificado |O certificado a utilizar para encriptar mensagens. Configura o certificado de privada do parceiro convidado para encriptar as mensagens. |
| Ativar compressão de mensagens |Requer a compressão de todas as mensagens que são enviadas do presente contrato. |
| Desdobrar cabeçalhos HTTP |Coloca o cabeçalho de tipo de conteúdo HTTP numa única linha. |
| Pedir MDN |Requer um MDN para todas as mensagens que são enviadas do presente contrato. |
| Pedir MDN assinado |Requer MDNs todos os que são enviados para o presente contrato sejam assinados. |
| Pedir MDN assíncrono |Requer MDNs assíncronas sejam enviados para o presente contrato. |
| do IdP |Especifique o URL para onde enviar os MDNs. |
| Ativar NRR |Requer a não rejeição de receção (NRR), um atributo de comunicação que fornece uma evidência que os dados foram recebidos conforme abordado. |
| Formato do Algoritmo SHA2 |Selecione o formato de algoritmo a utilizar o MIC ou assinatura nos cabeçalhos de saída da mensagem AS2 ou MDN |

## <a name="find-your-created-agreement"></a>Encontrar o contrato criado

1. Depois de concluir a configuração de todas as suas propriedades de contrato, no **Add** página, selecione **OK** para concluir a criação de seu contrato e retornar à sua conta de integração.

    Agora seu contrato recentemente adicionado é apresentado no seu **contratos** lista.

2. Também pode ver os contratos de na sua descrição de geral de conta de integração. No seu menu de conta de integração, escolha **descrição geral**, em seguida, selecione a **contratos** mosaico. 

   ![Escolha "Contratos" mosaico para ver todos os contratos](./media/logic-apps-enterprise-integration-as2/agreement-6.png)

## <a name="view-the-swagger"></a>Ver o swagger
Consulte a [detalhes de swagger](/connectors/as2/). 

## <a name="next-steps"></a>Passos Seguintes
* [Saiba mais sobre o Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Saiba mais sobre o Enterprise Integration Pack")  
