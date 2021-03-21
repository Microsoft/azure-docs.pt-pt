---
title: Microsoft Azure Data Box Disk auto-gerido | Microsoft Docs em dados
description: Descreve fluxo de trabalho de envio auto-gerido para dispositivos Azure Data Box Disk
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: how-to
ms.date: 02/02/2021
ms.author: alkohli
ms.openlocfilehash: f512b4415f4a83e779a8f9bf790ba2806e3b05c5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99526335"
---
# <a name="use-self-managed-shipping-for-azure-data-box-disk-in-the-azure-portal"></a>Utilize o envio auto-gerido para o disco Azure Data Box no portal Azure

Este artigo descreve tarefas de envio auto-geridas para encomendar, recolher e largar o Disco de Caixa de Dados Azure. Pode gerir o Disco de Caixa de Dados utilizando o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

O envio auto-gerido está disponível como opção quando [encomenda o disco de caixa de dados Azure](data-box-disk-deploy-ordered.md). O transporte auto-gerido só está disponível nas seguintes regiões:

* Governo dos Estados Unidos
* Reino Unido
* Europa Ocidental
* Austrália
* Japão
* Singapura
* Coreia do Sul
* África do Sul
* Índia (Pré-visualização)

## <a name="use-self-managed-shipping"></a>Utilizar o envio gerido automaticamente

Quando escamoteia uma encomenda de disco de caixa de dados, pode escolher a opção de envio auto-gerida.

1. Na sua encomenda de disco Azure Data Box, nos **dados** de Contacto , selecione **+ Adicionar Endereço de Envio**.

   ![Screenshot do assistente de Encomenda mostrando o passo dos dados de contacto com a opção 'Endereço de Envio' "Adicionar" chamada.](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-1.png)

2. Ao escolher o tipo de envio, selecione a opção **de envio auto-gerida.** Esta opção só está disponível se estiver numa região apoiada, conforme descrito nos pré-requisitos.

3. Depois de fornecer o seu endereço de envio, terá de o validar e completar o seu pedido.

   ![Screenshot da caixa de diálogo do endereço de envio de envio com o Navio usando opções e a opção de endereço de envio Adicionar chamada.](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-2.png)

4. Uma vez preparado o dispositivo e recebeu uma notificação por e-mail, pode agendar uma recolha. Na sua encomenda de disco Azure Data Box, vá ao **Overview** e, em seguida, **selecione Agendar pick-up**.

   ![Encomendar um dispositivo Data Box para recolha](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-user-pickup-01b.png)

5. Siga as instruções na recolha da **Agenda para Azure**. Antes de obter o seu código de autorização, tem de enviar um e-mail [adbops@microsoft.com](mailto:adbops@microsoft.com) para agendar a recolha do dispositivo no centro de dados da sua região.

   ![Agendar recolha](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-user-pickup-02c.png)

6. Depois de ter agendado a recolha do seu dispositivo, pode ver o seu código de autorização na **recolha agenda para Azure**.

   ![Screenshot da lista de levantamento para caixa de diálogo Azure com o código de autorização para caixa de texto pickup chamado.](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-authcode-01b.png)

   Tome nota deste código de autorização.

   De acordo com os requisitos de segurança, no momento da recolha de horários, é necessário apresentar o nome da pessoa que vai chegar para a recolha.

   Também precisa de fornecer detalhes de quem irá ao datacenter para a recolha. Você ou o ponto de contacto devem ter um ID de foto aprovado pelo governo que será validado no datacenter.

   A pessoa que está a apanhar o dispositivo também precisa de ter o código de autorização. O código de autorização é único para uma recolha ou entrega e é validado no datacenter.

7. A sua encomenda desloca-se automaticamente para o estado **de recolha** depois de o dispositivo ser recolhido no centro de dados.

   ![Recolhido](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-ready-disk-01b.png)

8. Depois de o dispositivo ser recolhido, poderá copiar dados para o disco(s) data box(s) no seu site. Depois da cópia de dados estar completa, pode preparar-se para enviar o Disco caixa de dados.

   Depois de terminar a cópia de dados, contacte as operações para agendar uma marcação para a entrega. Terá de partilhar os detalhes da pessoa que vem ao datacenter para deixar os discos. O datacenter também terá de verificar o código de autorização no momento da entrega. Encontrará o código de autorização para entrega no portal Azure ao abrigo da **entrega do Programa.**

   > [!NOTE]
   > Não partilhe o código de autorização por e-mail. Isto só deve ser verificado no datacenter durante a entrega.

9. Depois de receber uma marcação para entrega, a encomenda deve estar no Ready para receber no estado do **datacenter Azure** no portal Azure.

   ![Screenshot da caixa de diálogo do Endereço de Envio de Adicionar com o Navio utilizando opções para fora e a opção de endereço de envio Adicionar chamada.](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-authcode-dropoff-02b.png)

10. Depois de o seu ID e código de autorização terem sido verificados, e ter deixado o dispositivo no centro de dados, o estado da encomenda deve ser **recebido**.

    ![Recebido Completo](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-received-01a.png)

11. Uma vez que o dispositivo é recebido, a cópia de dados continuará. Quando a cópia terminar, a encomenda está completa.

## <a name="next-steps"></a>Passos seguintes

* [Início Rápido: Implementar o Azure Data Box com o portal do Azure](data-box-disk-quickstart-portal.md)
