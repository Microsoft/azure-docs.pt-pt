---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 05/06/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: ea8ed75bf91850abb95ebe983923989375c0fcf5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "76759858"
---
Para criar uma conta de armazenamento para fins gerais v2 no portal do Azure, siga estes passos:

1. No menu do portal do Azure, selecione **Todos os serviços**. Na lista de recursos, escreva **Contas de Armazenamento**. À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione **Contas de Armazenamento**.
2. Na janela **Contas de Armazenamento** que é apresentada, escolha **Adicionar**.
3. Selecione a subscrição na qua pretende criar a conta de armazenamento.
4. No campo **Grupo de recursos**, selecione **Criar novo**. Introduza um nome para o novo grupo de recursos, conforme exemplificado na imagem seguinte.

    ![Captura de ecrã que mostra como criar um grupo de recursos no portal](./media/storage-create-account-portal-include/create-resource-group-for-storage.png)

5. A seguir, introduza um nome para a sua conta de armazenamento. O nome que escolher tem de ser exclusivo em todo o Azure. O nome também tem de ter entre 3 e 24 carateres de comprimento e apenas pode incluir números e letras minúsculas.
6. Selecione uma localização para a sua conta de armazenamento ou utilize a localização predefinida.
7. Deixe os outros campos definidos para os respetivos valores predefinidos:

   |Campo  |Valor  |
   |---------|---------|
   |Modelo de implementação     |Resource Manager         |
   |Desempenho     |Standard         |
   |Tipo de conta     |StorageV2 (general-purpose v2)         |
   |Replicação     |Armazenamento georredundante com acesso de leitura (RA-GRS)         |
   |Escalão de acesso     |Acesso Frequente         |

8. Se planeia utilizar [o Azure Data Lake Storage,](https://azure.microsoft.com/services/storage/data-lake-storage/)escolha o separador **Avançado** e, em seguida, desempate o espaço de **nome hierárquico** para **Ativado**.
9. Selecione **Rever + Criar** para rever as definições de conta de armazenamento e criar a conta.
10. Selecione **Criar**.

Para obter mais informações sobre tipos de contas de armazenamento e outras definições de conta de armazenamento, consulte [Descrição geral da conta de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-account-overview). Para obter mais informações sobre grupos de recursos, consulte [a visão geral do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). 
