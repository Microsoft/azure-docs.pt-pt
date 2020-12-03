---
title: Habilitar características do espaço de trabalho da Sinaapse
description: Este documento descreve como um utilizador pode ativar as funcionalidades do espaço de trabalho synapse numa piscina SQL dedicada existente (anteriormente SQL DW).
services: synapse-analytics
author: antvgski
manager: igorstan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/25/2020
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: a1fbc6eede6c82020b765185602c672c1162fdf8
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96548193"
---
# <a name="enabling-synapse-workspace-features-for-a-dedicated-sql-pool-formerly-sql-dw"></a>Habilitando recursos de espaço de trabalho synapse para uma piscina SQL dedicada (anteriormente SQL DW)

Todos os utilizadores de armazéns de dados SQL podem agora aceder e usar uma placa de piscina SQL dedicada existente (anteriormente SQL DW) através do Synapse Studio e Workspace. Os utilizadores podem utilizar o Synapse Studio e Workspace sem afetar a automação, as ligações ou a ferramenta. Este artigo explica como um utilizador Azure Synapse Analytics existente pode ativar as funcionalidades do espaço de trabalho Synapse para uma piscina SQL dedicada existente (anteriormente SQL DW). O utilizador pode expandir a sua solução de Analytics existente, tirando partido das novas capacidades ricas em funcionalidades agora disponíveis através do espaço de trabalho synapse e do Studio.   

## <a name="prerequisites"></a>Pré-requisitos
Antes de ativar as funcionalidades do espaço de trabalho Synapse no seu armazém de dados, deve certificar-se de que tem os seguintes
- Direitos para criar e gerir os recursos SQL que estão hospedados no servidor lógico SQL.
- Direitos de criação de recursos Azure Synapse.
- Um administrador do Azure Ative Directory identificado no servidor lógico

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/).

## <a name="enabling-synapse-workspace-features-for-an-existing-dedicated-sql-pool-formerly-sql-dw"></a>Habilitando funcionalidades de espaço de trabalho synapse para uma piscina SQL dedicada existente (anteriormente SQL DW)

As funcionalidades do espaço de trabalho synapse podem ser ativadas em qualquer piscina SQL dedicada existente (anteriormente SQL DW) numa região apoiada. Esta capacidade só está disponível através do portal Azure.

Siga estes passos para criar um espaço de trabalho synapse para o seu armazém de dados existente.
1. Selecione uma piscina SQL dedicada existente (anteriormente SQL DW) e abra a página geral.
2. Selecione **Novo espaço de trabalho da Sinaapse** na barra de menu superior ou na mensagem imediatamente abaixo.
3. Depois de rever a lista de armazéns de dados que são disponibilizados através do novo espaço de trabalho da Sinapse na nova página do **espaço de trabalho Azure Synapse.** **Selecione Continue** a prosseguir.
4. Na página Basics, o pool SQL dedicado existente (a secção de detalhes do **Projeto** deve ser pré-povoado com o mesmo grupo de **Subscrição** e **Recursos** que é implantado sob o servidor lógico. E em **detalhes do Workspace,** o nome **workspace** é pré-povoado com o mesmo nome e região do servidor lógico SQL para garantir que a ligação entre o seu novo espaço de trabalho synapse e o servidor lógico pode ser criado durante o processo de fornecimento. O fornecimento desta ligação deve ser mantido para garantir o acesso continuado aos casos dedicados da piscina SQL (anteriormente SQL DW) através do espaço de trabalho e do Studio.
5. Navegue para selecionar data lake storage Gen 2.
6. Selecione **Criar novo** ou selecione uma Gen2 de **armazenamento de datas** existente antes de selecionar **Seguinte: Networking**.
7. Escolha uma **palavra-passe de administrador SQL** para a sua **instância Serverless**. Alterar esta palavra-passe não atualiza nem altera as credenciais lógicos do SQL do servidor. Se preferir uma palavra-passe definida pelo sistema, deixe estes campos vazios. Esta palavra-passe pode ser alterada a qualquer momento dentro do novo espaço de trabalho. O nome de administração deve ser o mesmo utilizado no SQL Server.
8. Selecione as definições de **Networking preferidas** e selecione **Review + Criar** para iniciar o fornecimento do espaço de trabalho.
9. **Selecione goto para** abrir o seu novo espaço de trabalho.

    > [!NOTE]
    > Todos os casos dedicados de piscina SQL (anteriormente SQL DW) hospedados no servidor lógico estão disponíveis através do novo espaço de trabalho.

## <a name="post-provisioning-steps"></a>Etapas de provisionamento pós-pôr
Os seguintes passos devem ser preenchidos para garantir que as suas instâncias de piscina SQL (anteriormente SQL DW) existentes possam ser acedidas através do Synapse Studio.
1. Na página de visão geral do espaço de trabalho synapse, selecione **Servidor Conectado**. O **servidor Connected** leva-o ao servidor SQL Logical ligado que acolhe os seus armazéns de dados. No menu essencial, selecione **Servidor Connected**.
2. Abra **firewalls e redes virtuais** e certifique-se de que o seu CLIENTE IP ou uma gama IP pré-determinada tem acesso ao servidor lógico.
3. Abra **a administração do Diretório Ativo** e certifique-se de que foi definido um administrador AAD no servidor lógico.
4. Selecione uma das instâncias dedicadas da piscina SQL (anteriormente SQL DW) hospedadas no servidor lógico. Na página geral, selecione **Launch Synapse Studio** ou Vá ao [Sign in para o Synapse Studio](https://web.azuresynapse.net) e inicie sômpa no seu espaço de trabalho.

5. Abra o **centro de dados** e expanda o pool de SQL dedicado no explorador de objetos para garantir que tem acesso e pode consultar o seu armazém de dados.

## <a name="next-steps"></a>Passos seguintes
Começando com [o Synapse Workspace e Studio.](../get-started.md)
