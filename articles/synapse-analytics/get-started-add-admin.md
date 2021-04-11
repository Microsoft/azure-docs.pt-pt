---
title: 'Tutorial: Começar a adicionar um administrador'
description: Neste tutorial, você vai aprender a adicionar outro utilizador administrativo ao seu espaço de trabalho.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 04/02/2021
ms.openlocfilehash: 8b854dfcff7dfb4d03038b542308b6f3ebb6d491
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "106553503"
---
# <a name="add-an-administrator-to-your-synapse-workspace"></a>Adicione um administrador ao seu espaço de trabalho Synapse

Neste tutorial, aprenderá a adicionar um administrador ao seu espaço de trabalho da Synapse. Este utilizador terá controlo total sobre o espaço de trabalho.

## <a name="overview"></a>Descrição Geral

Até agora, no guia de arranque, focamo-nos nas atividades *que faz* no espaço de trabalho. Como criou o espaço de trabalho no STEP 1, é administrador do espaço de trabalho da Sinapse. Agora, faremos de outro utilizador Ryan `ryan@contoso.com` () um administrador. Quando terminarmos, o Ryan poderá fazer tudo o que puder no espaço de trabalho.

## <a name="azure-rbac-owner-role-for-the-workspace"></a>Azure RBAC: Papel do proprietário para o espaço de trabalho

`ryan@contoso.com`Atribua-se ao papel de Proprietário do RBAC **Azure** no espaço de trabalho.

1. Abra o portal Azure e abra-lhe o espaço de trabalho da Sinapse.
1. No lado esquerdo, selecione **Access Control (IAM)**.
1. Adicione `ryan@contoso.com` ao papel **proprietário.** 
1. Clique em **Guardar**.
 
 
## <a name="synapse-rbac-synapse-administrator-role-for-the-workspace"></a>Sinapse RBAC: Função de administrador de sinapse para o espaço de trabalho

`ryan@contoso.com`Atribua-se à **função de Administrador de Sinapse DA SYNAPSE** no espaço de trabalho.

1. Abra o seu espaço de trabalho no Synapse Studio.
1. No lado esquerdo, clique em **Gerir** abrir o hub Manage.
1. Em **Segurança,** clique no **controlo de acesso.**
1. Clique em **Adicionar**.
1. Deixe **o Âmbito** definido para o Espaço de **Trabalho.**
1. Adicione `ryan@contoso.com` à função **de Administrador de Sinapse.** 
1. Em seguida, clique **em Aplicar**.
 
## <a name="azure-rbac-role-assignments-on-the-primary-storage-account"></a>Azure RBAC: Atribuições de funções na conta de armazenamento primário

Atribua `ryan@contoso.com` à função **proprietário** na conta de armazenamento primário do espaço de trabalho.
`ryan@contoso.com`Atribua-se à **função de contribuinte de dados do Blob de Armazenamento Azure** na conta de armazenamento primário do espaço de trabalho.

1. Abra a conta primária de armazenamento do espaço de trabalho no portal Azure.
1. No lado esquerdo, clique em **Controlo de Acesso (IAM)**.
1. Adicione `ryan@contoso.com` ao papel **proprietário.** 
1. Adicione `ryan@contoso.com` à função de contribuinte de dados do **Blob de Armazenamento de Azure**

## <a name="dedicated-sql-pools-db_owner-role"></a>Piscinas SQL dedicadas: db_owner papel

Atribua `ryan@contoso.com` ao **db_owner** em cada piscina SQL dedicada no espaço de trabalho.

```
CREATE USER [ryan@contoso.com] FROM EXTERNAL PROVIDER; 
EXEC sp_addrolemember 'db_owner', 'ryan@contoso.com'
```

## <a name="next-steps"></a>Passos seguintes

* [Introdução ao Azure Synapse Analytics](get-started.md)
* [Criar uma área de trabalho](quickstart-create-workspace.md)
* [Utilizar conjunto de SQL sem servidor](quickstart-sql-on-demand.md)
