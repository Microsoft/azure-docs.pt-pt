---
title: Gerir zona redundante alta disponibilidade - Portal Azure - Base de Dados Azure para PostgreSQL - Servidor Flexível
description: Este artigo descreve como ativar ou desativar a zona redundante alta disponibilidade na Base de Dados Azure para PostgreSQL - Servidor Flexível através do portal Azure.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: fc1bca1265139a438fad86bfce770026866d9a2f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "90937006"
---
# <a name="manage-zone-redundant-high-availability-in-flexible-server"></a>Gerir zona redundante alta disponibilidade em Servidor Flexível

> [!IMPORTANT]
> Azure Database for PostgreSQL - Flexible Server está em pré-visualização

Este artigo descreve como pode ativar ou desativar a configuração de alta disponibilidade redundante no seu servidor flexível.

As disposições relativas à funcionalidade de alta disponibilidade separam fisicamente a réplica primária e de espera em diferentes zonas. Para mais detalhes, consulte [a documentação de conceitos de alta disponibilidade.](./concepts-high-availability.md) Pode optar por ativar uma elevada disponibilidade no momento da criação flexível do servidor ou após a criação. Esta página fornece diretrizes de como pode ativar ou desativar alta disponibilidade. Esta operação não altera as suas outras definições, incluindo a configuração VNET, as definições de firewall e a retenção de backup. Da mesma forma, permitir e desativar a alta disponibilidade é uma operação online e não afeta a conectividade e operações da sua aplicação.

## <a name="pre-requisites"></a>Pré-requisitos

A zona redundante de alta disponibilidade só está disponível em regiões onde várias zonas são suportadas. 

## <a name="enable-high-availability-during-server-creation"></a>Ativar alta disponibilidade durante a criação do servidor

Esta secção fornece detalhes especificamente para campos relacionados com ha. Pode seguir estes passos para implementar uma alta disponibilidade enquanto cria o seu servidor flexível.

1.  No [portal Azure,](https://portal.azure.com/)escolha o Servidor Flexível e clique em criar.  Para mais detalhes sobre como preencher detalhes como **Subscrição**, **Grupo de Recursos,** **nome do servidor,** **região** e outros campos, consulte a documentação de como preencher o servidor.
   
    :::image type="content" source="./media/how-to-manage-high-availability-portal/subscription-region.png" alt-text="Ver subscrição e região":::

2.  Escolha a sua **zona de disponibilidade.** Isto é útil se quiser transferir a sua aplicação na mesma zona de disponibilidade que a base de dados para reduzir a latência. **Escolha Não Preferência** se quiser que o servidor flexível implemente em qualquer zona de disponibilidade.
    ![Seleção ]() :::image type="content" source="./media/how-to-manage-high-availability-portal/zone-selection.png" alt-text="de zona de disponibilidade de seleção"::: AZ  

3.  Clique na caixa de verificação para **zona redundante alta disponibilidade** na opção Disponibilidade.

    :::image type="content" source="./media/how-to-manage-high-availability-portal/high-availability-checkbox.png" alt-text="Caixa de verificação de alta disponibilidade":::

4.  Se pretender alterar o cálculo e armazenamento predefinidos, clique em  **Configurar servidor**.
 
    :::image type="content" source="./media/how-to-manage-high-availability-portal/configure-server.png" alt-text="servidor de configuração - compute+storage":::  

5.  Se for verificada a opção de alta disponibilidade, o nível de rebentamento não estará disponível para escolher. Pode escolher os níveis de cálculo **geral** ou **otimizado de memória.** Em seguida, pode **selecionar o tamanho** do cálculo para a sua escolha a partir do dropdown.

    :::image type="content" source="./media/how-to-manage-high-availability-portal/select-compute.png" alt-text="Seleção de nível computacional":::  


6.  Selecione o **tamanho do armazenamento** em GiB utilizando a barra de deslizamento e selecione o período de **retenção de backup** entre 7 dias e 35 dias.
   
    :::image type="content" source="./media/how-to-manage-high-availability-portal/storage-backup.png" alt-text="Backup de armazenamento"::: 

7. Clique em **Guardar**. 

## <a name="enable-high-availability-post-server-creation"></a>Ativar a criação de servidor pós-venda de alta disponibilidade

Siga estes passos para permitir uma elevada disponibilidade para o seu servidor flexível existente.

1.  No [portal Azure,](https://portal.azure.com/)selecione o seu servidor flexível PostgreSQL existente.

2.  Na página do servidor flexível, clique em **Alta Disponibilidade** a partir do painel esquerdo para abrir a página de alta disponibilidade.
   
     :::image type="content" source="./media/how-to-manage-high-availability-portal/high-availability-left-panel.png" alt-text="Seleção de painéis esquerdos"::: 

3.  Clique na caixa de verificação **de alta disponibilidade redundante** da zona para **ativar** a opção e clique em **Guardar** para guardar a alteração.

     :::image type="content" source="./media/how-to-manage-high-availability-portal/enable-high-availability.png" alt-text="Ativar a alta disponibilidade"::: 

4.  Um diálogo de confirmação mostrará que, ao permitir uma elevada disponibilidade, o seu custo aumentará devido à implementação adicional do servidor e do armazenamento.

5.  Clique no botão **Ativar HA** para ativar a alta disponibilidade.

6.  Uma notificação aparecerá indicando que a elevada disponibilidade está em curso.

## <a name="disable-high-availability"></a>Desativar a alta disponibilidade

Siga estes passos para desativar a alta disponibilidade para o seu servidor flexível que já está configurado com redundância de zona.

1.  No [portal Azure,](https://portal.azure.com/)selecione a base de dados Azure existente para PostgreSQL - Servidor Flexível.

2.  Na página do servidor flexível, clique em **Alta Disponibilidade** a partir do painel frontal para abrir a página de alta disponibilidade.
   
    :::image type="content" source="./media/how-to-manage-high-availability-portal/high-availability-left-panel.png" alt-text="Seleção de painéis esquerdos"::: 

3.  Clique na caixa de verificação **de alta disponibilidade redundante** para **desativar** a opção. Em seguida, clique em **Guardar** para guardar a alteração.

     :::image type="content" source="./media/how-to-manage-high-availability-portal/disable-high-availability.png" alt-text="Desativar a alta disponibilidade"::: 

4.  Será mostrado um diálogo de confirmação onde pode confirmar a desativação da alta disponibilidade.

5.  Clique no botão **DEsativar** o botão HA para desativar a alta disponibilidade.

6.  Está em curso uma notificação de desmantelamento da elevada disponibilidade.

## <a name="next-steps"></a>Passos seguintes

-   Conheça a [continuidade do negócio](./concepts-business-continuity.md)
-   Saiba mais sobre [zona redundante alta disponibilidade](./concepts-high-availability.md)
