---
title: Preparação de soluções Dinâmica 365
description: Enquadramento para embalagens, instalação e desinstalação de componentes
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: ac1e4fa541e945f20904ced114a36b58d14585ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80278591"
---
# <a name="dynamics-365-solution-preparation"></a>Preparação de soluções Dinâmica 365

O sistema de soluções Dynamics 365 é um quadro para a embalagem, instalação e desinstalação de componentes que fornecem funcionalidades de negócio específicas. As soluções são utilizadas pelos ISVs e outros parceiros da Microsoft Dynamics 365 para distribuir extensões que criam.

Se é um ISV da Dynamics 365 (xRM) existente, provavelmente já criou uma solução gerida e tem um ficheiro solution.zip. Na sua solução, certifique-se de que os campos "Display Name" e "Description" refletem o que pretende que os clientes vejam. Estes são apresentados no Centro de Administração Online CRM.

![CRMScreenShot1](media/CRMScreenShot1.png)

_**Nota:** No exemplo do pacote que se segue vamos assumir que o nome da solução é "SampleSolution.zip"_

Se você é um novo ISV, você pode obter mais detalhes sobre a criação de uma solução aqui:[https://msdn.microsoft.com/library/gg334530.aspx](https://msdn.microsoft.com/library/gg334530.aspx)

Se a sua solução necessitar de dados de suporte:

* Crie os dados da amostra no seu ambiente de teste
* Utilize a Ferramenta de Migração de Configuração para criar um Schema com regras de comparação para os seus dados.
* Guarde o seu esquema de configuração com os seus ficheiros de projeto. Precisará disto mais tarde se atualizar os seus dados de configuração.
* Exporte os seus dados de configuração. Certifique-se de dar ao ficheiro de exportação um nome que seja significativo para a sua exportação.
