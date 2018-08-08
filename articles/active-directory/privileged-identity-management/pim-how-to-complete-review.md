---
title: Como concluir uma revisão de acesso | Documentos da Microsoft
description: Depois de iniciado uma revisão de acesso no Azure AD Privileged Identity Management, saiba como concluí-la e ver os resultados
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: pim
ms.date: 06/06/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 3b4135368c2222a08b155c851b384244774ce246
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/08/2018
ms.locfileid: "39622535"
---
# <a name="how-to-complete-an-access-review-in-azure-ad-privileged-identity-management"></a>Como concluir uma revisão de acesso no Azure AD Privileged Identity Management
Os administradores de função com privilégios podem rever acesso privilegiado de uma vez um [revisão de acesso foi iniciada](pim-how-to-start-security-review.md). O Azure AD Privileged Identity Management (PIM) enviará automaticamente um e-mail a pedir aos utilizadores para rever o acesso. Se um utilizador não recebeu uma mensagem de e-mail, pode enviar-lhes as instruções [como realizar uma revisão de acesso](pim-how-to-perform-security-review.md).

Depois do período de revisão de acesso, ou todos os usuários tem concluído a seus Self-rever, siga os passos neste artigo para gerir a revisão e ver os resultados.

## <a name="manage-access-reviews"></a>Gerir as revisões de acesso
1. Vá para o [portal do Azure](https://portal.azure.com/) e selecione o **Azure AD Privileged Identity Management** aplicação no seu dashboard.
2. Selecione o **as revisões de acesso** secção do dashboard.
3. Selecione a revisão de acesso que pretende gerir.

No painel de detalhes a revisão de acesso, há um número opções para gerir essa revisão.

![Botões de revisão de acesso do PIM - captura de ecrã](./media/pim-how-to-complete-review/PIM_review_buttons.png)

### <a name="remind"></a>Lembrar
Se uma revisão de acesso está configurada para que os utilizadores rever si próprios, o **lembrar** botão envia uma notificação. 

### <a name="stop"></a>Parar
Todas as revisões de acesso tem uma data de fim, mas pode utilizar o **parar** botão para terminá-lo no início. Se todos os utilizadores ainda não tiverem sido revistos por desta vez, eles não será capazes de depois de parar a revisão. Não é possível reiniciar uma revisão depois de interrompida.

### <a name="apply"></a>Aplicar
Depois de concluída uma revisão de acesso, ou porque foi atingido a data de fim ou parado-lo manualmente, o **aplicar** botão implementa o resultado da revisão. Se foi negado o acesso de um utilizador da revisão, este é o passo que irá remover a respetiva atribuição de função.  

### <a name="export"></a>Exportar
Se deseja aplicar manualmente os resultados da revisão de acesso, pode exportar a revisão. O **exportar** botão irá começar a transferir um ficheiro CSV. Pode gerir os resultados no Excel ou outros programas que abrem arquivos CSV.

### <a name="delete"></a>Eliminar
Se não estiver interessado em qualquer outra a revisão, eliminá-lo. O **eliminar** botão remove a revisão da aplicação do PIM.

> [!IMPORTANT]
> Não irá obter um aviso antes de ocorre a eliminação, por isso, certifique-se de que pretende eliminar essa revisão. 

## <a name="next-steps"></a>Passos Seguintes
[!INCLUDE [active-directory-privileged-identity-management-toc](../../../includes/active-directory-privileged-identity-management-toc.md)]
