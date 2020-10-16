---
title: Microsoft Azure Data Box Disk auto-gerido Envio / Microsoft Docs em dados
description: Descreve fluxo de trabalho de envio auto-gerido para dispositivos Azure Data Box Disk
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: how-to
ms.date: 05/20/2020
ms.author: alkohli
ms.openlocfilehash: c4b3479e5728c32e66bc40f950bc948bf61dce42
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91575168"
---
# <a name="use-self-managed-shipping-for-azure-data-box-disk-in-the-azure-portal"></a>Utilize o envio auto-gerido para o disco Azure Data Box no portal Azure

Este artigo descreve tarefas de envio auto-geridas para encomendar, recolher e largar o Disco de Caixa de Dados Azure. Pode gerir o Disco de Caixa de Dados utilizando o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

O envio auto-gerido está disponível como opção quando [encomenda o disco de caixa de dados Azure](data-box-disk-deploy-ordered.md). O transporte auto-gerido só está disponível nas seguintes regiões:

* Governo dos Estados Unidos
* Europa Ocidental
* Japão
* Singapura
* Coreia do Sul
* África do Sul
* Índia (Pré-visualização)

## <a name="use-self-managed-shipping"></a>Utilizar o envio gerido automaticamente

Quando escamoteia uma encomenda de disco de caixa de dados, pode escolher a opção de envio auto-gerida.

1. Na sua encomenda de disco Azure Data Box, nos **dados**de Contacto , selecione **+ Adicionar Endereço de Envio**.

   ![Screenshot do assistente de Encomenda mostrando o passo dos dados de contacto com a opção 'Endereço de Envio' "Adicionar" chamada.](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-1.png)

2. Ao escolher o tipo de envio, selecione a opção **de envio auto-gerida.** Esta opção só está disponível se estiver numa região apoiada, conforme descrito nos pré-requisitos.

3. Depois de ter fornecido o seu endereço de envio, terá de o validar e completar a sua encomenda.

   ![Screenshot da caixa de diálogo do endereço de envio de envio com o Navio usando opções e a opção de endereço de envio Adicionar chamada.](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-2.png)

4. Uma vez preparado o dispositivo e recebeu uma notificação por e-mail, pode agendar uma recolha. Na sua encomenda de disco Azure Data Box, vá ao **Overview** e, em seguida, **selecione Agendar pick-up**.

   ![Encomendar um dispositivo Data Box para recolha](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-user-pickup-01b.png)

5. Siga as instruções na recolha da **Agenda para Azure**. Antes de obter o seu código de autorização, tem de enviar um e-mail [adbops@microsoft.com](mailto:adbops@microsoft.com) para agendar a recolha do dispositivo no centro de dados da sua região.

   ![Agendar recolha](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-user-pickup-02c.png)

6. Depois de ter programado a recolha do seu dispositivo, poderá ver o seu código de autorização na  **recolha do Agenda para Azure**.

   ![Screenshot da lista de levantamento para caixa de diálogo Azure com o código de autorização para caixa de texto pickup chamado.](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-authcode-01b.png)

   Tome nota deste código de **Autorização.**

   De acordo com os requisitos de segurança, no momento da recolha de horários, é necessário apresentar o nome da pessoa que chegará para a recolha.

   Também precisa de fornecer detalhes de quem irá ao datacenter para recolha. Você ou o ponto de contacto devem levar um Documento de Identificação fotográfica aprovado pelo Governo que será validado no datacenter.

   Além disso, a pessoa que está a captar o dispositivo também precisa de ter o **código de Autorização**. O código de autorização é único para uma recolha ou uma entrega e é validado no datacenter.

7. A sua encomenda desloca-se automaticamente para o estado **de recolha** uma vez que o dispositivo tenha sido recolhido no centro de dados.

   ![Recolhido](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-ready-disk-01b.png)

8. Depois de o dispositivo ser recolhido, poderá copiar dados para o disco(s) data box(s) no seu site. Depois da cópia de dados estar completa, pode preparar-se para enviar o Disco caixa de dados.

   Uma vez terminada a cópia de dados, terá de contactar as operações para agendar uma marcação para a entrega. Terá de partilhar os detalhes da pessoa que vem ao datacenter para deixar os discos. O datacenter também terá de verificar o código de autorização no momento da entrega. O código de autorização para entrega estará disponível no portal Azure ao abrigo **da entrega do Programa**.

   > [!NOTE]
   > Não partilhe o código de autorização por e-mail. Isto só deve ser verificado no datacenter durante a entrega.

9. Se recebeu uma marcação para entrega, a encomenda deve estar agora no Ready para receber no estado do **datacenter Azure** no portal Azure.

   ![Screenshot da caixa de diálogo do Endereço de Envio de Adicionar com o Navio utilizando opções para fora e a opção de endereço de envio Adicionar chamada.](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-authcode-dropoff-02b.png)

10. Depois de verificado o seu ID e código de autorização e ter deixado o dispositivo no centro de dados, o estado da encomenda deve ser **recebido**.

    ![Recebido Completo](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-received-01a.png)

11. Uma vez que o dispositivo é recebido, a cópia de dados continuará. Quando a cópia terminar, a encomenda está completa.

## <a name="next-steps"></a>Passos seguintes

* [Quickstart: Implementar disco de caixa de dados Azure utilizando o portal Azure](data-box-disk-quickstart-portal.md)
