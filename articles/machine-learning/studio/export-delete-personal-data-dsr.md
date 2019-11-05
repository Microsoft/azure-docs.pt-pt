---
title: Exportar e eliminar dados
titleSuffix: Azure Machine Learning Studio (classic)
description: Os dados no produto armazenados por Azure Machine Learning Studio (clássico) estão disponíveis para exportação e exclusão por meio do portal do Azure e também por meio de APIs REST autenticadas. Os dados de telemetria podem ser acessados por meio do portal de privacidade do Azure. Este artigo mostra como.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 05/25/2018
ms.openlocfilehash: a0cc200f69be2362806886aae79ece52c833a43a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73492961"
---
# <a name="export-and-delete-in-product-user-data-from-azure-machine-learning-studio-classic"></a>Exportar e excluir dados de usuário no produto do Azure Machine Learning Studio (clássico)

Você pode excluir ou exportar dados no produto armazenados por Azure Machine Learning Studio (clássico) usando o portal do Azure, a interface do Studio (clássico), o PowerShell e as APIs REST autenticadas. Este artigo explica como. 

Os dados de telemetria podem ser acessados por meio do portal de privacidade do Azure. 

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="what-kinds-of-user-data-does-studio-classic-collect"></a>Que tipos de dados do usuário são coletados pelo estúdio (clássico)?

Para esse serviço, os dados do usuário consistem em informações sobre os usuários autorizados a acessar espaços de trabalho e registros de telemetria de interações do usuário com o serviço.

Há dois tipos de dados de usuário em Machine Learning Studio (clássico):
- **Dados da conta pessoal:** IDs de conta e endereços de email associados a uma conta.
- **Dados do cliente:** Dados que você carregou para analisar.

## <a name="studio-classic-account-types-and-how-data-is-stored"></a>Tipos de conta do Studio (clássico) e como os dados são armazenados

Há três tipos de contas no Machine Learning Studio (clássico). O tipo de conta que você tem determina como os dados são armazenados e como você pode excluí-los ou exportá-los.

- Um **espaço de trabalho convidado** é uma conta anônima e gratuita. Você se inscreve sem fornecer credenciais, como um endereço de email ou senha.
    -  Os dados são limpos após a expiração do espaço de trabalho convidado.
    - Os usuários convidados podem exportar dados do cliente por meio da interface do usuário, APIs REST ou pacote do PowerShell.
- Um **espaço de trabalho gratuito** é uma conta gratuita na qual você entra com credenciais conta Microsoft-um endereço de email e uma senha.
    - Você pode exportar e excluir dados pessoais e do cliente, que estão sujeitos a solicitações de DSR (direitos de entidade de dados).
    - Você pode exportar dados do cliente por meio da interface do usuário, APIs REST ou pacote do PowerShell.
    - Para espaços de trabalho gratuitos que não usam contas do Azure AD, a telemetria pode ser exportada usando o portal de privacidade.
    - Ao excluir o espaço de trabalho, você exclui todos os dados pessoais do cliente.
- Um **espaço de trabalho padrão** é uma conta paga que você acessa com credenciais de entrada.
    - Você pode exportar e excluir dados pessoais e do cliente, que estão sujeitos a solicitações de DSR.
    - Você pode acessar dados por meio do portal de privacidade do Azure
    - Você pode exportar dados pessoais e de clientes por meio da interface do usuário, APIs REST ou pacote do PowerShell
    - Você pode excluir seus dados no portal do Azure.

## <a name="delete"></a>Excluir dados do espaço de trabalho no estúdio (clássico) 

### <a name="delete-individual-assets"></a>Excluir ativos individuais

Os usuários podem excluir ativos em um espaço de trabalho selecionando-os e, em seguida, selecionando o botão excluir.

![Excluir ativos em Machine Learning Studio (clássico)](./media/export-delete-personal-data-dsr/delete-studio-asset.png)

### <a name="delete-an-entire-workspace"></a>Excluir um espaço de trabalho inteiro

Os usuários também podem excluir seu espaço de trabalho inteiro:
- Espaço de trabalho pago: exclua o portal do Azure.
- Espaço de trabalho gratuito: Use o botão excluir no painel **configurações** .

![Excluir um espaço de trabalho gratuito no Machine Learning Studio (clássico)](./media/export-delete-personal-data-dsr/delete-studio-data-workspace.png)
 
## <a name="export-studio-classic-data-with-powershell"></a>Exportar dados do estúdio (clássico) com o PowerShell
Use o PowerShell para exportar todas as suas informações para um formato portátil da versão clássica do Azure Machine Learning Studio usando comandos. Para obter informações, consulte o artigo [módulo do PowerShell para Azure Machine Learning Studio (clássico)](powershell-module.md) .

## <a name="next-steps"></a>Passos seguintes

Para obter a documentação que aborda os serviços Web e a cobrança do plano de compromisso, consulte [Azure Machine Learning Studio (clássico) referência da API REST](https://docs.microsoft.com/rest/api/machinelearning/). 
