---
title: Preparação da solução do Dynamics 365
description: Estrutura para empacotamento, instalação e desinstalação de componentes
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
manager: Ricarod.Villalobos
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: bcb3bb63f305aeb98efda3baf0f6661bd7f67a7c
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824236"
---
# <a name="dynamics-365-solution-preparation"></a>Preparação da solução do Dynamics 365

O sistema de soluções do Dynamics 365 é uma estrutura para empacotar, instalar e desinstalar componentes que fornecem funcionalidade comercial específica. As soluções são usadas por ISVs e outros parceiros do Microsoft Dynamics 365 para distribuir extensões que criam.

Se você for um ISV do Dynamics 365 (xRM) existente, provavelmente já terá criado uma solução gerenciada e ter um arquivo. zip de solução. Em sua solução, certifique-se de que os campos "nome de exibição" e "Descrição" reflitam o que você deseja que os clientes vejam. Eles são exibidos no centro de administração do CRM Online.

![CRMScreenShot1](media/CRMScreenShot1.png)

_**Observação:** No exemplo de pacote a seguir, vamos supor que o nome da solução é "SampleSolution. zip"_

Se você for um novo ISV, poderá obter mais detalhes sobre como criar uma solução aqui: [https://msdn.microsoft.com/library/gg334530.aspx](https://msdn.microsoft.com/library/gg334530.aspx)

Se sua solução exigir dados de suporte:

* Criar os dados de exemplo em seu ambiente de teste
* Use a ferramenta de migração de configuração para criar um esquema com regras de comparação para seus dados.
* Salve o esquema de configuração com seus arquivos de projeto. Você precisará disso mais tarde se atualizar os dados de configuração.
* Exporte seus dados de configuração. Certifique-se de dar um nome ao arquivo de exportação que seja significativo para a sua exportação.
