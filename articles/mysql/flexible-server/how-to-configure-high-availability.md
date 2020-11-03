---
title: Gerir zona redundante alta disponibilidade - Portal Azure - Base de Dados Azure para MySQL Flexible Server
description: Este artigo descreve como ativar ou desativar a zona redundante alta disponibilidade na Base de Dados Azure para o MySQL flexible Server através do portal Azure.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.custom: references_regions
ms.openlocfilehash: d65b074385311e74444929ef74901e402e29ec03
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93241740"
---
# <a name="manage-zone-redundant-high-availability-in-azure-database-for-mysql-flexible-server-preview"></a>Gerir zona redundante alta disponibilidade em Azure Database para MySQL Flexible Server (Preview)

Este artigo descreve como pode ativar ou desativar a configuração de alta disponibilidade redundante no seu servidor flexível.

As disposições relativas à funcionalidade de alta disponibilidade separam fisicamente a réplica primária e de espera em diferentes zonas. Para mais detalhes, consulte [a documentação de conceitos de alta disponibilidade.](./concepts/../concepts-high-availability.md) 

> [!IMPORTANT]
> Só é possível ativar uma zona redundante de alta disponibilidade durante a criação flexível do servidor.

Esta página fornece diretrizes de como pode ativar ou desativar alta disponibilidade. Esta operação não altera as suas outras definições, incluindo a configuração VNET, as definições de firewall e a retenção de backup. Da mesma forma, a desativação de alta disponibilidade é uma operação online e não afeta a conectividade e operações da sua aplicação.

> [!IMPORTANT]
> Zona redundante alta disponibilidade está disponível em conjunto limitado de regiões : Sudeste Asiático, WestUS 2, Europa Ocidental e Leste dos EUA.  

## <a name="enable-high-availability-during-server-creation"></a>Ativar alta disponibilidade durante a criação do servidor

Esta secção fornece detalhes especificamente para campos relacionados com ha. Pode seguir estes passos para implementar uma alta disponibilidade enquanto cria o seu servidor flexível.

1.  No [portal Azure,](https://portal.azure.com/)escolha o Servidor Flexível e clique em **Criar** .  Para mais detalhes sobre como preencher detalhes como **Subscrição** , **Grupo de Recursos,** **Nome do Servidor,** **Região** e outros campos, consulte a documentação de como preencher o servidor.

2.  Clique na caixa de verificação para **zona redundante alta disponibilidade** na opção Disponibilidade.

3.  Se pretender alterar o cálculo e armazenamento predefinidos, clique em  **Configurar servidor** .

4.  Se for verificada a opção de alta disponibilidade, o nível de rebentamento não estará disponível para escolher. Pode escolher os níveis de cálculo **geral** ou **otimizado de memória.**

    > [!IMPORTANT]
    > Só apoiamos zona redundante alta disponibilidade para o *_* finalidade geral_* _ e nível de preços _*_otimizado de memória._*_

5.  Selecione o tamanho _ *Compute* * para a sua escolha a partir do dropdown.

6.  Selecione **o tamanho do armazenamento** em GiB utilizando a barra de deslizamento e selecione o período de **retenção de backup** entre 7 dias e 35 dias.   

## <a name="disable-high-availability"></a>Desativar a alta disponibilidade

Siga estes passos para desativar a alta disponibilidade para o seu servidor flexível que já está configurado com redundância de zona.

1.  No [portal Azure,](https://portal.azure.com/)selecione a base de dados Azure existente para o servidor flexível MySQL.

2.  Na página do servidor flexível, clique em **Alta Disponibilidade** a partir do painel frontal para abrir a página de alta disponibilidade.

3.  Clique na caixa de verificação **de alta disponibilidade redundante** para desativar a opção e clique em **Guardar** para guardar a alteração.

4.  Será mostrado um diálogo de confirmação onde pode confirmar a desativação do HA.

5.  Clique no botão **DEsativar** o botão HA para desativar a alta disponibilidade.

6.  Está em curso uma notificação de desmantelamento da elevada disponibilidade.

## <a name="next-steps"></a>Passos seguintes

-   Conheça a [continuidade do negócio](./concepts-business-continuity.md)
-   Saiba mais sobre [zona redundante alta disponibilidade](./concepts-high-availability.md)
