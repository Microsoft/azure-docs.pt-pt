---
title: Envio autogerido do Microsoft Azure Data Box Disk [ Microsoft Docs em dados
description: Descreve fluxo de trabalho de envio autogerido para dispositivos de disco de caixa de dados Azure
services: databox
author: priestlg
ms.service: databox
ms.subservice: disk
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: v-grpr
ms.openlocfilehash: 74828bfd3a110739afc211a530c11c74387b70ed
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83749313"
---
# <a name="use-self-managed-shipping-for-azure-data-box-disk-in-the-azure-portal"></a>Utilize o envio autogerido para o Disco de Caixa de Dados Azure no portal Azure

Este artigo descreve tarefas de envio autogeridas para encomendar, recolha e entrega do Disco de Caixa de Dados Azure. Pode gerir o Disco de Caixa de Dados utilizando o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

O envio autogerido está disponível como opção quando encomendar disco de caixa de [dados Azure](data-box-disk-deploy-ordered.md). O transporte autogerido só está disponível nas seguintes regiões:

* Governo dos Estados Unidos
* Europa Ocidental
* Japão
* Singapura
* Coreia do Sul

## <a name="use-self-managed-shipping"></a>Use o envio auto-gerido

Ao epor uma encomenda de Disco de Caixa de Dados, pode escolher a opção de envio autogerida.

1. Na sua encomenda disco de disco de caixa de dados Azure, sob os **dados do Contacto,** selecione **+ Adicionar endereço de envio**.

   ![Envio autogerido](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-1.png)

2. Ao escolher o tipo de envio, selecione a opção **de envio autogerida.** Esta opção só está disponível se estiver numa região apoiada, conforme descrito nos pré-requisitos.

3. Depois de ter fornecido o seu endereço de envio, terá de validá-lo e completar o seu pedido.

   ![Envio autogerido](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-2.png)

4. Uma vez preparado o dispositivo, pode agendar uma recolha. Na sua encomenda de Disco de Caixa de Dados Azure, vá ao **Overview e,** em seguida, selecione **A recolha**de Agenda .

   ![Encomendar um dispositivo Data Box para recolha](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-user-pickup-01b.png)

5. Siga as instruções na recolha de **horários para O Azure**. Antes de obter o seu código de autorização, tem de enviar um e-mail [adbops@microsoft.com](mailto:adbops@microsoft.com) para agendar a recolha do dispositivo a partir do centro de dados da sua região.

   ![Agendar recolha](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-user-pickup-02c.png)

6. Depois de ter agendado a recolha do seu dispositivo, poderá visualizar o seu código de autorização na recolha de **horários para o Azure**.

   ![Visualizando o seu código de autorização](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-authcode-01b.png)

   Tome nota deste código de **autorização**.

   De acordo com os requisitos de segurança, no momento do agendamento de recolha e entrega, é necessário apresentar o nome da pessoa que chegaria para recolha e entrega.

   Também precisa de fornecer detalhes de quem irá ao datacenter para recolha. Você ou o ponto de contacto devem ter um ID fotográfico aprovado pelo Governo que será validado no datacenter.

   Além disso, a pessoa que está a captar o dispositivo também precisa de ter o **código de autorização**. O código de autorização é validado no momento do datacenter de recolha.

7. A sua encomenda desloca-se automaticamente para o estado **pick-up** uma vez que o dispositivo tenha sido recolhido do datacenter.

   ![Recolhido](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-ready-disk-01b.png)

8. Depois de o dispositivo ser recolhido, poderá copiar dados para o Disco da Caixa de Dados no seu site. Depois de a cópia de dados estar completa, pode preparar-se para enviar o Disco da Caixa de Dados.

   Uma vez concluída a cópia de dados, terá de contactar as operações para agendar uma marcação para a entrega. Terá de partilhar os detalhes da pessoa que vem ao centro de dados para deixar os discos. O datacenter também terá de verificar o código de autorização no momento da entrega. O código de autorização para entrega estará disponível no portal Azure no âmbito **da entrega do Horário.**

   > [!NOTE]
   > Não partilhe o código de autorização por e-mail. Isto só deve ser verificado no datacenter durante a entrega.

9. Se recebeu uma marcação para a entrega da encomenda deve agora estar no Ready a receber no estado do Centro de **Dados Azure** no portal Azure.

   ![Visualizando o seu código de autorização](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-authcode-dropoff-02b.png)

10. Depois de verificado o seu código de identificação e autorização e de ter deixado o dispositivo no centro de dados, o estado da encomenda deve ser **recebido**.

    ![Recebido Completo](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-received-01a.png)

11. Uma vez recebido o dispositivo, a cópia de dados continuará. Quando a cópia terminar, a encomenda está completa.

## <a name="next-steps"></a>Passos seguintes

* [Quickstart: Implementar disco de caixa de dados Azure utilizando o portal Azure](data-box-disk-quickstart-portal.md)
