---
title: Envio auto-gerido da Microsoft Azure Data Box Microsoft Docs em dados
description: Descreve fluxo de trabalho de envio auto-gerido para dispositivos Azure Data Box
services: databox
author: priestlg
ms.service: databox
ms.subservice: disk
ms.topic: how-to
ms.date: 05/20/2020
ms.author: v-grpr
ms.openlocfilehash: 99c7021744467484656c52cf72378c6f553c3913
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84608592"
---
# <a name="use-self-managed-shipping-for-azure-data-box-in-the-azure-portal"></a>Utilize o envio auto-gerido para a Caixa de Dados Azure no portal Azure

Este artigo descreve tarefas de envio auto-geridas para encomendar, recolher e largar um dispositivo Azure Data Box. Pode gerir o dispositivo Data Box utilizando o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

O envio auto-gerido está disponível como opção quando [encomenda a Caixa de Dados Azure](data-box-deploy-ordered.md). O transporte auto-gerido só está disponível nas seguintes regiões:

* Governo dos Estados Unidos
* Europa Ocidental
* Japão
* Singapura
* Coreia do Sul

## <a name="use-self-managed-shipping"></a>Utilizar o envio gerido automaticamente

Quando escamada uma encomenda de Caixa de Dados, pode escolher a opção de envio auto-gerida.

1. Na sua encomenda Azure Data Box, nos **dados**de Contacto , selecione **+ Adicionar Endereço de Envio**.
   ![Envio auto-gerido](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-1.png)

2. Ao escolher o tipo de envio, selecione a opção **de envio auto-gerida.** Esta opção só está disponível se estiver numa região apoiada, conforme descrito nos pré-requisitos.

3. Depois de ter fornecido o seu endereço de envio, terá de o validar e completar a sua encomenda.
   ![Envio auto-gerido](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-2.png)

4. Uma vez preparado o dispositivo e receber uma notificação por e-mail, pode agendar uma recolha.

   Na sua encomenda Azure Data Box, vá ao **Overview** e, em seguida, **selecione Agendar pick-up**.

   ![Encomendar um dispositivo Data Box para recolha](media\data-box-portal-customer-managed-shipping\data-box-portal-schedule-pickup-01.png)

5. Siga as instruções na recolha da **Agenda para Azure**.

   Antes de obter o seu código de autorização, tem de enviar um e-mail [adbops@microsoft.com](mailto:adbops@microsoft.com) para agendar a recolha do dispositivo no centro de dados da sua região.

   ![Agendar recolha](media\data-box-portal-customer-managed-shipping\data-box-portal-schedule-pickup-email-01.png)

6. Depois de ter programado a recolha do seu dispositivo, poderá ver o código de autorização do seu dispositivo na **pick-up Schedule para painel Azure.**

   ![Visualização do seu código de autorização](media\data-box-portal-customer-managed-shipping\data-box-portal-auth-01b.png)

   Tome nota deste código de **Autorização.** De acordo com os requisitos de segurança, no momento da recolha de horários, é necessário apresentar o nome da pessoa que chegaria para recolha.

   Também precisa de fornecer detalhes de quem irá ao datacenter para recolha. Você ou o ponto de contacto devem levar um Documento de Identificação fotográfica aprovado pelo Governo que será validado no datacenter.

   Além disso, a pessoa que está a captar o dispositivo também precisa de ter o **código de Autorização**. O código de autorização é validado no momento do datacenter da recolha.

7. A sua encomenda desloca-se automaticamente para o estado **de recolha** uma vez que o dispositivo tenha sido recolhido no centro de dados.

    ![Recolhido](media\data-box-portal-customer-managed-shipping\data-box-portal-picked-up-boxed-01.png)

8. Depois de o dispositivo ser recolhido, copie os dados para a Caixa de Dados no seu site. Depois da cópia de dados estar completa, pode preparar-se para enviar a Caixa de Dados. Para mais informações, consulte [Prepare-se para enviar.](data-box-deploy-picked-up.md#prepare-to-ship)

   O **passo de preparação para o envio** tem de ser concluído sem erros críticos, caso contrário terá de voltar a correr este passo depois de fazer as correções necessárias. Depois de a preparação para o envio ter concluído com sucesso, pode ver o código de autorização para a entrega na interface de utilizador local do dispositivo.

   > [!NOTE]
   > Não partilhe o código de autorização por e-mail. Isto só deve ser verificado no datacenter durante a entrega.

9. Se recebeu uma marcação para entrega, a encomenda deve estar pronta para receber no estado **do datacenter Azure** no portal Azure. Siga as instruções previstas na **entrega do horário** para devolver o aparelho.

   ![Visualização do seu código de autorização](media\data-box-portal-customer-managed-shipping\data-box-portal-received-complete-02b.png)

10. Após a verificação do seu ID e código de autorização e de ter deixado o dispositivo no centro de dados, o estado da encomenda deve ser **recebido**.

    ![Recebido Completo](media\data-box-portal-customer-managed-shipping\data-box-portal-received-complete-01.png)

11. Uma vez que o dispositivo é recebido, a cópia de dados continuará. Quando a cópia terminar, a encomenda está completa.

## <a name="next-steps"></a>Próximos passos

* [Introdução ao Azure Data Box](data-box-quickstart-portal.md)
