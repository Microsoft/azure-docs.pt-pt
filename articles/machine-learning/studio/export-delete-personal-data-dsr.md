---
title: Exportar e eliminar os seus dados
titleSuffix: ML Studio (classic) - Azure
description: Os dados in-product armazenados pelo Azure Machine Learning Studio (clássico) estão disponíveis para exportação e eliminação através do portal Azure e também através de APIs REST autenticados. Os dados de telemetria podem ser acedidos através do Portal de Privacidade Azure. Este artigo mostra-lhe como.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 05/25/2018
ms.openlocfilehash: c380d10d0c68794ec3810cea25341d68bb41400d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251690"
---
# <a name="export-and-delete-in-product-user-data-from-azure-machine-learning-studio-classic"></a>Exportar e eliminar dados de utilizadores no produto do Azure Machine Learning Studio (clássico)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Pode eliminar ou exportar dados no produto armazenados pelo Azure Machine Learning Studio (clássico) utilizando o portal Azure, a interface Studio (clássica), powerShell e APIs REST autenticados. Este artigo diz-lhe como. 

Os dados de telemetria podem ser acedidos através do portal de privacidade Azure. 

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="what-kinds-of-user-data-does-studio-classic-collect"></a>Que tipo de dados de utilizador o Studio (clássico) recolhe?

Para este serviço, os dados dos utilizadores consistem em informações sobre utilizadores autorizados a aceder a espaços de trabalho e registos de telemetria das interações dos utilizadores com o serviço.

Existem dois tipos de dados de utilizadores no Machine Learning Studio (clássico):
- **Dados da conta pessoal:** IDs de conta e endereços de e-mail associados a uma conta.
- **Dados do cliente:** Dados que fez upload para analisar.

## <a name="studio-classic-account-types-and-how-data-is-stored"></a>Tipos de conta estúdio (clássicos) e como os dados são armazenados

Existem três tipos de contas no Machine Learning Studio (clássico). O tipo de conta que tem determina como os seus dados são armazenados e como pode eliminá-lo ou exportá-lo.

- Um espaço de **trabalho para hóspedes** é uma conta anónima e gratuita. Inscreva-se sem fornecer credenciais, como um endereço de e-mail ou senha.
    -  Os dados são purgados após o espaço de trabalho dos hóspedes expirar.
    - Os utilizadores convidados podem exportar dados dos clientes através do pacote UI, REST APIs ou PowerShell.
- Um **espaço de trabalho gratuito** é uma conta gratuita a que se insere com credenciais de conta microsoft - um endereço de e-mail e senha.
    - Pode exportar e eliminar dados pessoais e de clientes, que estão sujeitos a pedidos de direitos de titular ização (DSR).
    - Pode exportar dados dos clientes através do pacote UI, REST APIs ou PowerShell.
    - Para espaços de trabalho gratuitos que não utilizem contas Azure AD, a telemetria pode ser exportada através do Portal da Privacidade.
    - Ao eliminar o espaço de trabalho, elimina todos os dados pessoais dos clientes.
- Um **espaço de trabalho padrão** é uma conta paga a que acede com credenciais de inscrição.
    - Pode exportar e eliminar dados pessoais e de clientes, que estão sujeitos a pedidos de DSR.
    - Pode aceder a dados através do portal De Privacidade Azure
    - Pode exportar dados pessoais e de clientes através do pacote UI, REST APIs ou PowerShell
    - Pode eliminar os seus dados no portal Azure.

## <a name="delete-workspace-data-in-studio-classic"></a><a name="delete"></a>Eliminar dados do espaço de trabalho em Studio (clássico) 

### <a name="delete-individual-assets"></a>Eliminar ativos individuais

Os utilizadores podem eliminar os ativos num espaço de trabalho selecionando-os e, em seguida, selecionando o botão de eliminar.

![Eliminar ativos no Machine Learning Studio (clássico)](./media/export-delete-personal-data-dsr/delete-studio-asset.png)

### <a name="delete-an-entire-workspace"></a>Eliminar todo um espaço de trabalho

Os utilizadores também podem eliminar todo o seu espaço de trabalho:
- Espaço de trabalho pago: Apague através do portal Azure.
- Espaço de trabalho gratuito: Utilize o botão de eliminar no painel **Definições.**

![Eliminar um espaço de trabalho gratuito no Machine Learning Studio (clássico)](./media/export-delete-personal-data-dsr/delete-studio-data-workspace.png)
 
## <a name="export-studio-classic-data-with-powershell"></a>Export Studio (clássico) dados com PowerShell
Utilize a PowerShell para exportar todas as suas informações para um formato portátil do Azure Machine Learning Studio (clássico) utilizando comandos. Para obter informações, consulte o módulo PowerShell para o artigo [do Azure Machine Learning Studio (clássico).](powershell-module.md)

## <a name="next-steps"></a>Passos seguintes

Para documentação que cubra serviços web e faturação de plano de compromisso, consulte [Azure Machine Learning Studio (clássico) referência REST API](https://docs.microsoft.com/rest/api/machinelearning/). 
