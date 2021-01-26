---
title: Registre o bilhete de suporte para Azure Stack Edge Pro, Azure Data Box Gateway gateway | Microsoft Docs
description: Saiba como registar o pedido de suporte para problemas relacionados com as suas encomendas Azure Stack Edge Pro ou Data Box Gateway.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 01/07/2021
ms.author: alkohli
ms.openlocfilehash: 411757f4ef4e33ccbc8a69ca2b40598b51c6d13b
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98790838"
---
# <a name="open-a-support-ticket-for-azure-stack-edge-pro-and-azure-data-box-gateway"></a>Abra um bilhete de apoio para Azure Stack Edge Pro e Azure Data Box Gateway

Este artigo aplica-se ao Azure Stack Edge Pro e ao Azure Data Box Gateway ambos geridos pelo serviço Azure Stack Edge Pro / Azure Data Box Gateway. Se encontrar algum problema com o seu serviço, pode criar um pedido de assistência técnica. Este artigo acompanha-o:

* Como criar um pedido de apoio.
* Como gerir um ciclo de vida de pedido de suporte a partir do portal.

## <a name="create-a-support-request"></a>Criar um pedido de suporte

Faça as seguintes etapas para criar um pedido de apoio:

1. Aceda ao seu pedido Azure Stack Edge Pro ou Data Box Gateway. Navegue para a secção **de suporte + resolução de problemas** e, em seguida, selecione **Novo pedido de suporte**.

2. No **novo pedido de apoio,** no separador Básico, tome as **seguintes** medidas:

    1. A partir da lista de dropdown **do tipo Demissão,** selecione **Técnico**.
    2. Escolha a sua **Subscrição**.
    3. Em **Serviço,** consulte os **meus serviços.** Na lista de dropdown, selecione **Azure Stack Edge Pro e Data Box Gateway**.
    4. Selecione o seu **Recurso**. Isto corresponde ao nome da sua encomenda.
    5. Faça um breve **resumo** da questão que está a passar. 
    6. Selecione o **seu tipo de problema**.
    7. Com base no tipo de problema selecionado, escolha um **subtipo de problema** correspondente .
    8. Selecione **Seguinte: Soluções >>**.

        ![Informações básicas](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-support-request-1.png)

3. No separador **Detalhes,** tome os seguintes passos:

    1. Forneça a data e hora de início para o problema.
    2. Forneça uma **descrição** para o seu problema.
    3. No **upload do Ficheiro,** selecione o ícone da pasta para navegar em quaisquer outros ficheiros que pretenda carregar.
    4. Verifique **Informações de diagnóstico de partilhar**.
    5. Com base na sua subscrição, um **plano de suporte** é automaticamente povoado.
    6. Na lista de abandono, selecione a **Severidade.**
    7. Especifique um **método de contacto preferido.**
    8. As **horas de Resposta** são selecionadas automaticamente com base no seu plano de subscrição.
    9. Forneça o idioma que prefere para Suporte.
    10. Nas **informações de Contacto,** forneça o seu nome, e-mail, telefone, contacto opcional, país/região. O Microsoft Support utiliza estas informações para lhe chegar a mais informações, diagnósticos e resolução. 
    11. Selecione **Seguinte: Revisão + Criar >>**.

        ![Problema](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-support-request-2.png)

4. No **separador 'Análise + Criar',** revê as informações relacionadas com o bilhete Support. Selecione **Criar**. 

    ![Problema 2](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-support-request-3.png)

    Depois de criar o bilhete De Apoio, um engenheiro de Suporte entrará em contacto consigo o mais rapidamente possível para prosseguir com o seu pedido.

## <a name="get-hardware-support"></a>Obtenha suporte de hardware

Esta informação aplica-se apenas ao dispositivo Azure Stack. O processo de reporte de problemas de hardware é o seguinte:

1. Abra um bilhete de apoio a partir do portal Azure para um problema de hardware. No **tipo Problema**, selecione **Hardware Azure Stack**. Escolha o **subtipo problema** como **falha de hardware**.

    ![Problema de hardware](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-hardware-issue-1.png)

    Depois de ter criado o bilhete De Apoio, um engenheiro de Suporte entrará em contacto consigo o mais rapidamente possível para prosseguir com o seu pedido.

2. Se o Microsoft Support determinar que se trata de um problema de hardware, então ocorre uma das seguintes ações:

    * É enviada uma Unidade de Substituição de Campo (FRU) para a peça de hardware falhada. Atualmente, as unidades de alimentação e as unidades de estado sólido são as únicas FUs suportadas.
    * Apenas as FUs são substituídas no dia útil seguinte, tudo o resto requer uma substituição completa do sistema (FSR) para ser enviada.

3. Se for determinado que uma substituição de FRU é necessária até às 13:00 horas locais (de segunda a sexta-feira), é enviado um técnico no local no dia útil seguinte para a sua localização para realizar uma substituição de FRU. Uma substituição completa do sistema normalmente demorará muito mais tempo porque as peças são enviadas da nossa fábrica e podem estar sujeitas a atrasos de transporte e alfândega.

## <a name="manage-a-support-request"></a>Gerir um pedido de apoio

Depois de criar um pedido de suporte, pode gerir o ciclo de vida do mesmo no portal.

### <a name="to-manage-your-support-requests"></a>Para gerir os seus pedidos de apoio

1. Para chegar à página de ajuda e suporte, navegue para **procurar > suporte Help +**.

    ![Gerir pedidos de apoio](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-manage-support-request-1.png)

2. Uma listagem tabular de **pedidos** de suporte recentes é apresentada no **suporte Help +**.

    <!--[Manage support requests](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-support-request-1.png)--> 

3. Selecione e clique num pedido de suporte. Pode ver o estado e os detalhes deste pedido. Clique **em + Nova mensagem** se quiser acompanhar este pedido.

## <a name="next-steps"></a>Próximos passos

Saiba como [resolver problemas relacionados com o Azure Stack Edge Pro](azure-stack-edge-troubleshoot.md).
Saiba como [resolver problemas relacionados com o Data Box Gateway](../databox-gateway/data-box-gateway-troubleshoot.md).