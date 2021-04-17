---
title: Desativar a sua conta Azure Automation gerida (pré-visualização)
description: Este artigo explica como desativar e remover uma identidade gerida para uma conta Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 04/14/2021
ms.topic: conceptual
ms.openlocfilehash: e17e1afda50d9a0263067a77bf26435f53b4f237
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519277"
---
# <a name="disable-your-azure-automation-account-managed-identity-preview"></a>Desativar a sua conta Azure Automation gerida (pré-visualização)

Existem duas formas de desativar uma identidade atribuída ao sistema na Azure Automation. Pode completar esta tarefa a partir do portal Azure ou utilizando um modelo Azure Resource Manager (ARM).

## <a name="disable-managed-identity-in-the-azure-portal"></a>Desativar identidade gerida no portal Azure

Pode desativar a identidade gerida a partir do portal Azure, independentemente da forma como a identidade gerida foi originalmente criada.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Navegue na sua conta de Automação e selecione **identidade** em **Definições de Conta.**

1. Desa estada a opção **do Sistema** para **desligar** e prima **Guardar**. Quando for solicitado a confirmar, prima **Sim.**

A identidade gerida é removida e já não tem acesso ao recurso-alvo.

## <a name="disable-using-azure-resource-manager-template"></a>Desativar usando o modelo de Gestor de Recursos Azure

Se criou a identidade gerida para a sua conta Dem automação utilizando um modelo de Gestor de Recursos Azure, pode desativar a identidade gerida reutilizando esse modelo e modificando as suas definições. Desajei o tipo de propriedade do objeto de identidade a **Nenhum,** como mostrado no exemplo seguinte, e, em seguida, re-executar o modelo.

```json
"identity": { 
   "type": "None" 
} 
```

A remoção de uma identidade atribuída ao sistema utilizando este método também a elimina do Azure AD. As identidades atribuídas ao sistema também são automaticamente removidas do Azure AD quando o recurso da aplicação a que são atribuídos é eliminado.

## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações sobre como ativar a identidade gerida na Azure Automation, consulte [Ativar e utilizar identidade gerida para Automação (pré-visualização)](enable-managed-identity-for-automation.md).

- Para obter uma visão geral da segurança da conta de [Automação, consulte a visão geral da autenticação da conta de Automação.](automation-security-overview.md)
