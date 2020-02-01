---
title: Exportar ou excluir configurações do portal Azure
description: Saiba como você pode exportar ou excluir suas configurações de usuário, painéis particulares e configurações personalizadas no portal do Azure.
services: azure-portal
keywords: ''
author: santhoshsomayajula
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-portal
ms.custom: ''
manager: mtillman
ms.author: mblythe
ms.openlocfilehash: accdfbd939fad73ca7d008450c358d366e7f8b70
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76900739"
---
# <a name="export-or-delete-user-settings"></a>Exportar ou eliminar as definições de utilizador

Você pode usar configurações e recursos no portal do Azure para criar uma experiência personalizada. As informações sobre suas configurações personalizadas são armazenadas no Azure. Você pode exportar ou excluir os seguintes dados de usuário:

* Painéis privados no portal do Azure
* Configurações do usuário, como assinaturas ou diretórios favoritos e o último diretório conectado
* Temas e outras configurações personalizadas do portal

É uma boa ideia exportar e revisar suas configurações antes de excluí-las. Recriar painéis ou refazer configurações personalizadas pode ser demorado.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="export-or-delete-your-portal-settings"></a>Exportar ou excluir as configurações do portal

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).

1. No cabeçalho do portal, selecione ![ícone de definições](media/azure-portal-export-delete-settings/settings-icon.png) **Definições**.

1. Selecione **exportar todas as configurações** ou **excluir todas as configurações e painéis particulares**.

    ![Opções de definições e definições do portal Azure](media/azure-portal-export-delete-settings/azure-portal-settings-with-export-delete.png)

      A tabela a seguir descreve essas ações.

      | Ação | Descrição |
      | --- | --- |
      | **Exportar todas as configurações** | Cria um ficheiro *.json* que contém as definições do utilizador, como o tema da cor, os favoritos e os dashboards privados.|
      | **Excluir todas as configurações e painéis particulares** | Exclui todos os links para painéis privados e outras configurações personalizadas que você fez no Portal. |

> [!NOTE]
> Devido à natureza dinâmica das configurações dos utilizadores e ao risco de corrupção de dados, não é possível importar definições do ficheiro *.json.*
>
>

## <a name="next-steps"></a>Passos seguintes

* [Partilhe dashboards Azure utilizando o Controlo de Acesso baseado em funções](azure-portal-dashboard-share-access.md)
* [Adicione, remova e reorganize os favoritos](azure-portal-add-remove-sort-favorites.md)
