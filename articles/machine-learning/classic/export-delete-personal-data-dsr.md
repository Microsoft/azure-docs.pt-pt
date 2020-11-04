---
title: 'ML Studio (clássico): Exportar & apagar os seus dados - Azure'
description: Os dados do produto armazenados pelo Azure Machine Learning Studio (clássico) estão disponíveis para exportação e eliminação através do portal Azure e também através de APIs de REPOUSO autenticados. Os dados de telemetria podem ser acedidos através do Portal de Privacidade Azure. Este artigo mostra-lhe como.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 05/25/2018
ms.openlocfilehash: 550603b9315c1abaa025aac804afc4fe674159d2
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93310020"
---
# <a name="export-and-delete-in-product-user-data-from-azure-machine-learning-studio-classic"></a>Exportar e eliminar dados do utilizador no produto do Azure Machine Learning Studio (clássico)

**APLICA-SE A:** ![ Aplica-se a. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (clássico) ![ Não se aplica a. ](../../../includes/media/aml-applies-to-skus/no.png)[ Aprendizagem de Máquinas Azure](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  




Pode eliminar ou exportar dados de produtos armazenados pelo Azure Machine Learning Studio (clássico) utilizando o portal Azure, a interface Studio (clássico), PowerShell e APIs REST autenticados. Este artigo diz-lhe como. 

Os dados de telemetria podem ser acedidos através do portal Azure Privacy. 

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="what-kinds-of-user-data-does-studio-classic-collect"></a>Que tipo de dados de utilizador o Studio (clássico) recolhe?

Para este serviço, os dados do utilizador consistem em informações sobre utilizadores autorizados a aceder a espaços de trabalho e registos de telemetria das interações dos utilizadores com o serviço.

Existem dois tipos de dados de utilizador no Machine Learning Studio (clássico):
- **Dados de conta pessoal:** IDs de conta e endereços de e-mail associados a uma conta.
- **Dados do cliente:** Dados que fez o upload para analisar.

## <a name="studio-classic-account-types-and-how-data-is-stored"></a>Tipos de conta estúdio (clássicos) e como os dados são armazenados

Existem três tipos de contas no Machine Learning Studio (clássico). O tipo de conta que tem determina como os seus dados são armazenados e como pode eliminá-lo ou exportá-lo.

- Um **espaço de trabalho para hóspedes** é uma conta gratuita e anónima. Você se inscreve sem fornecer credenciais, como um endereço de e-mail ou senha.
    -  Os dados são purgados após o espaço de trabalho dos hóspedes expirar.
    - Os utilizadores convidados podem exportar dados de clientes através do pacote UI, REST APIs ou PowerShell.
- Um **espaço de trabalho gratuito** é uma conta gratuita a que se inscreve com as credenciais de conta da Microsoft - um endereço de e-mail e senha.
    - Pode exportar e apagar dados pessoais e de clientes, que estão sujeitos a pedidos de direitos de dados sujeitos (DSR).
    - Pode exportar dados de clientes através do pacote UI, REST APIs ou PowerShell.
    - Para espaços de trabalho gratuitos que não utilizem contas AZure AD, a telemetria pode ser exportada através do Portal de Privacidade.
    - Quando elimina o espaço de trabalho, apaga todos os dados pessoais do cliente.
- Um **espaço de trabalho padrão** é uma conta paga a que aceda com credenciais de inscrição.
    - Pode exportar e apagar dados pessoais e de clientes, que estão sujeitos a pedidos de DSR.
    - Pode aceder aos dados através do portal Azure Privacy
    - Você pode exportar dados pessoais e de clientes através do pacote UI, REST APIs ou PowerShell
    - Pode eliminar os seus dados no portal Azure.

## <a name="delete-workspace-data-in-studio-classic"></a><a name="delete"></a>Apagar dados do espaço de trabalho no Estúdio (clássico) 

### <a name="delete-individual-assets"></a>Eliminar ativos individuais

Os utilizadores podem eliminar os ativos num espaço de trabalho selecionando-os e, em seguida, selecionando o botão de exclusão.

![Eliminar ativos no Machine Learning Studio (clássico)](./media/export-delete-personal-data-dsr/delete-studio-asset.png)

### <a name="delete-an-entire-workspace"></a>Apagar um espaço de trabalho inteiro

Os utilizadores também podem eliminar todo o seu espaço de trabalho:
- Espaço de trabalho pago: Excluir através do portal Azure.
- Espaço de trabalho gratuito: Utilize o botão de exclusão no painel **definições.**

![Excluir um espaço de trabalho gratuito no Machine Learning Studio (clássico)](./media/export-delete-personal-data-dsr/delete-studio-data-workspace.png)
 
## <a name="export-studio-classic-data-with-powershell"></a>Exportação de dados do Estúdio (clássico) com PowerShell
Utilize o PowerShell para exportar todas as suas informações para um formato portátil do Azure Machine Learning Studio (clássico) utilizando comandos. Para obter informações, consulte o [módulo PowerShell para o artigo do Azure Machine Learning Studio (clássico).](powershell-module.md)

## <a name="next-steps"></a>Passos seguintes

Para documentação que cubra serviços web e faturação de plano de compromisso, consulte [a referência API do API do API do AZure Machine Learning Studio (clássico).](/rest/api/machinelearning/)