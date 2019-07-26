---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: f188f2c7bea511f1109d37ef49563e0f745a770e
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385753"
---
Com Azure Resource Manager, você pode definir parâmetros para os valores a serem usados ao implantar o modelo. O modelo inclui uma `parameters` seção que contém todos os valores de parâmetro. Cada valor de parâmetro é usado pelo modelo para definir os recursos que você deseja implantar.

> [!NOTE]
> Não defina parâmetros para valores que permanecem sempre iguais. Defina parâmetros somente para valores que variam de acordo com o projeto que você está implantando ou com base no ambiente em que você está implantando.

Quando você define parâmetros:

* Para especificar os valores permitidos que um usuário pode fornecer durante a implantação, use o campo **allowedValues** .

* Para atribuir valores padrão ao parâmetro quando nenhum valor for fornecido durante a implantação, use o campo **DefaultValue** . 
