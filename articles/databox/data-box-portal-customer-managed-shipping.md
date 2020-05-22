---
title: Envio autogerido microsoft Azure Data Box [ Microsoft Docs em dados
description: Descreve fluxo de trabalho de envio autogerido para dispositivos Azure Data Box
services: databox
author: priestlg
ms.service: databox
ms.subservice: disk
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: v-grpr
ms.openlocfilehash: 2933eb216b7faed7b28fb53d79fec2f50bc4458d
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83749418"
---
# <a name="use-self-managed-shipping-for-azure-data-box-in-the-azure-portal"></a>Utilize o envio autogerido para a Caixa de Dados Azure no portal Azure

Este artigo descreve tarefas de envio autogeridas para encomendar, recolher e largar um dispositivo Azure Data Box. Pode gerir o dispositivo Data Box utilizando o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

O envio autogerido está disponível como opção quando encomendar caixa de [dados Azure](data-box-deploy-ordered.md). O transporte autogerido só está disponível nas seguintes regiões:

* Governo dos Estados Unidos
* Europa Ocidental
* Japão
* Singapura
* Coreia do Sul

## <a name="use-self-managed-shipping"></a>Use o envio auto-gerido

Ao epor uma encomenda de Caixa de Dados, pode escolher a opção de envio autogerida.

1. Na sua encomenda de Caixa de Dados Azure, sob os **dados do Contacto,** selecione **+ Adicionar endereço de envio**.
   ![Envio autogerido](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-1.png)

2. Ao escolher o tipo de envio, selecione a opção **de envio autogerida.** Esta opção só está disponível se estiver numa região apoiada, conforme descrito nos pré-requisitos.

3. Depois de ter fornecido o seu endereço de envio, terá de validá-lo e completar o seu pedido.
   ![Envio autogerido](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-2.png)

4. Uma vez preparado o dispositivo, pode agendar uma recolha.

   Na sua encomenda da Caixa de Dados Azure, vá ao **Overview e,** em seguida, selecione A recolha de **Horários**.

   ![Encomendar um dispositivo Data Box para recolha](media\data-box-portal-customer-managed-shipping\data-box-portal-schedule-pickup-01.png)

5. Siga as instruções na recolha de **horários para O Azure**.

   Antes de obter o seu código de autorização, tem de enviar um e-mail [adbops@microsoft.com](mailto:adbops@microsoft.com) para agendar a recolha do dispositivo a partir do centro de dados da sua região.

   ![Agendar recolha](media\data-box-portal-customer-managed-shipping\data-box-portal-schedule-pickup-email-01.png)

6. Depois de ter agendado a recolha do seu dispositivo, poderá visualizar o código de autorização do seu dispositivo na recolha de horários para o painel **Azure.**

   ![Visualizando o seu código de autorização](media\data-box-portal-customer-managed-shipping\data-box-portal-auth-01b.png)

   Tome nota deste código de **autorização**. De acordo com os requisitos de segurança, no momento do agendamento de recolha e entrega, é necessário apresentar o nome da pessoa que chegaria para recolha e entrega.

   Também precisa de fornecer detalhes de quem irá ao datacenter para recolha. Você ou o ponto de contacto devem ter um ID fotográfico aprovado pelo Governo que será validado no datacenter.

   Além disso, a pessoa que está a captar o dispositivo também precisa de ter o **código de autorização**. O código de autorização é validado no momento do datacenter de recolha.

7. A sua encomenda desloca-se automaticamente para o estado **pick-up** uma vez que o dispositivo tenha sido recolhido do datacenter.

    ![Recolhido](media\data-box-portal-customer-managed-shipping\data-box-portal-picked-up-boxed-01.png)

8. Depois de o dispositivo ser recolhido, copie os dados para a Caixa de Dados no seu site. Depois de a cópia de dados estar completa, pode preparar-se para enviar a Caixa de Dados. Para mais informações, consulte [Prepare-se para enviar](data-box-deploy-picked-up.md#prepare-to-ship).

   O **Passo Prepare-se para o navio** precisa de ser concluído sem erros críticos, caso contrário terá de voltar a correr este passo depois de efazer as correções necessárias. Depois de a preparação para o navio completar com sucesso, pode visualizar o código de autorização para a entrega na interface de utilizador local do dispositivo.

   > [!NOTE]
   > Não partilhe o código de autorização por e-mail. Isto só deve ser verificado no datacenter durante a entrega.

9. Se recebeu uma consulta para entrega, a encomenda deve estar pronta para receber no estado do Centro de **Dados Azure** no portal Azure. Siga as instruções no âmbito da entrega do **horário** para devolver o dispositivo.

   ![Visualizando o seu código de autorização](media\data-box-portal-customer-managed-shipping\data-box-portal-received-complete-02b.png)

10. Após a verificação do seu código de identificação e autorização e de ter deixado o dispositivo no centro de dados, o estado da encomenda deve ser **recebido**.

    ![Recebido Completo](media\data-box-portal-customer-managed-shipping\data-box-portal-received-complete-01.png)

11. Uma vez recebido o dispositivo, a cópia de dados continuará. Quando a cópia terminar, a encomenda está completa.

## <a name="next-steps"></a>Passos seguintes

* [Introdução ao Azure Data Box](data-box-quickstart-portal.md)
