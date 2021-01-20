---
title: Azure AD Connect cloud sync acidental elimina
description: Este tópico descreve como utilizar a função de eliminação acidental para evitar supressões.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/19/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6ef7b6d9495b1431e03808b830671e839b90d436
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/20/2021
ms.locfileid: "98614286"
---
# <a name="accidental-delete-prevention"></a>Prevenção acidental de eliminação

O documento que se segue descreve a função de eliminação acidental para a sincronização da nuvem Azure AD Connect.  A função de eliminação acidental foi concebida para o proteger de alterações de configuração acidentais e alterações no diretório no local que afetariam muitos utilizadores e grupos.  Esta funcionalidade permite-lhe:

- Configure a capacidade de evitar eliminações acidentais automaticamente. 
- Desa ajuste o # de objetos (limiar) para além do qual a configuração irá produzir efeito 
- configurar um endereço de e-mail de notificação para que possam receber uma notificação por e-mail uma vez que o trabalho de sincronização em questão é colocado em quarentena para este cenário 

Para utilizar esta função, estabeleceu o limiar para o número de objetos que, se eliminados, a sincronização deve parar.  Assim, se este número for alcançado, a sincronização irá parar e uma notificação será enviada para o e-mail especificado.  Isto permite-lhe investigar o que se passa.


## <a name="configure-accidental-delete-prevention"></a>Configure a prevenção acidental de eliminação acidental
Para utilizar a nova funcionalidade, siga os passos abaixo.


1.  No portal do Azure, selecione **Azure Active Directory**.
2.  Selecione **Azure Ad Connect**.
3.  **Selecione Gerir a sincronização de nuvens**.
4. Em **Configuração,** selecione a sua configuração.
5. Em Definições preenchem os **seguintes:**
    - **Email de notificação** - e-mail usado para notificações
    - **Prevenir supressões acidentais** - verifique esta caixa para ativar a funcionalidade
    - **Limiar de eliminação acidental** - introduza uma série de objetos para desencadear a paragem de sincronização e notificação

![Eliminações acidentais](media/how-to-accidental-deletes/accident-1.png)

## <a name="next-steps"></a>Passos seguintes 

- [O que é a sincronização de nuvem AZure AD Connect?](what-is-cloud-sync.md)
- [Como instalar sincronização de nuvem Azure AD Connect](how-to-install.md)
 

