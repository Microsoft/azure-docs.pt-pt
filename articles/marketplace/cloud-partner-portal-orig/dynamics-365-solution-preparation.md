---
title: Preparação de solução do Dynamics 365
description: Estrutura para empacotar, instalar e desinstalar componentes
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
manager: Ricarod.Villalobos
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 919feb39c9cd84f8da0ef89827ad3e83e0eb9bc5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64935253"
---
# <a name="dynamics-365-solution-preparation"></a>Preparação de solução do Dynamics 365

O sistema do Dynamics 365 solutioning é uma estrutura de empacotamento, instalar e desinstalar componentes que fornecem funcionalidades de negócios específicas. Soluções são utilizadas por ISVs e outros parceiros do Microsoft Dynamics 365 para distribuir as extensões que criaram.

Se for um existente do Dynamics 365 (xRM) ISV, é mais provável que já tiver criado uma solução gerida e possui um arquivo de solution.zip. Na sua solução, certifique-se de que o "nome a apresentar" e campos de "Descrição" refletem o que deseja que os clientes para ver. Estas serão apresentadas no Centro de administração do CRM Online.

![CRMScreenShot1](media/CRMScreenShot1.png)

_**Nota:** O exemplo de pacote que se segue vamos supor que o nome da solução é "SampleSolution.zip"_

Se for um ISV nova, pode obter mais detalhes sobre como criar uma solução aqui: [https://msdn.microsoft.com/library/gg334530.aspx](https://msdn.microsoft.com/library/gg334530.aspx)

Se a sua solução requer suporte de dados:

* Criar os dados de exemplo no seu ambiente de teste
* Utilize a ferramenta de migração de configuração para criar um esquema com regras de comparação para os seus dados.
* Guarde o seu esquema de configuração com seus arquivos de projeto. Irá precisar mais tarde se atualizar os dados de configuração.
* Exporte os dados de configuração. Certifique-se de que dê um nome significativo para a exportação ao ficheiro de exportação.
