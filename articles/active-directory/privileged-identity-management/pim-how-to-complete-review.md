---
title: Avaliação de acesso ao acabamento das funções da Azure AD na PIM - Azure AD [ Microsoft Docs
description: Saiba como completar uma revisão de acesso das funções da Azure AD na Azure AD Privileged Identity Management (PIM) e veja os resultados
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: fe2d85d605b9ee418a5709ddcdb448c56be1d918
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74022287"
---
# <a name="complete-an-access-review-of-azure-ad-roles-in-privileged-identity-management"></a>Complete uma revisão de acesso das funções da Azure AD na Gestão de Identidade Privilegiada

Os administradores privilegiados podem rever o acesso privilegiado uma vez iniciada uma revisão de [acesso.](pim-how-to-start-security-review.md)  A Privileged Identity Management (PIM) enviará automaticamente um e-mail aos utilizadores da sua organização Azure Ative Directory (Azure AD), levando-os a rever o seu acesso. Se um utilizador não receber um e-mail, pode enviar-lhes as instruções de [como efetuar uma revisão](pim-how-to-perform-security-review.md)de acesso .

Após o fim do período de revisão de acesso, ou todos os utilizadores terminaram a sua auto-revisão, siga os passos deste artigo para gerir a revisão e ver os resultados.

## <a name="manage-access-reviews"></a>Gerir avaliações de acesso

1. Vá ao [portal Azure](https://portal.azure.com/) e selecione o serviço de Gestão de **Identidade Privilegiada Azure AD** no seu painel de instrumentos.
1. Selecione a secção de comentários de **acesso** do painel de instrumentos.
1. Selecione a revisão de acesso que pretende gerir.

Na lâmina de detalhes da revisão de acesso, existem várias opções para gerir essa revisão.

![Botões de revisão de acesso privilegiados à Gestão de Identidade - screenshot](./media/pim-how-to-complete-review/review-buttons.png)

### <a name="remind"></a>Lembre-se

Se for criada uma revisão de acesso para que os utilizadores se revejam, o botão **Remind** envia uma notificação.

### <a name="stop"></a>Parar

Todas as avaliações de acesso têm uma data limite, mas pode usar o botão **Stop** para terminá-lo mais cedo. Se algum utilizador não tiver sido revisto até agora, não poderão fazê-lo depois de parar a revisão. Não pode reiniciar uma revisão depois de ter sido interrompida.

### <a name="apply"></a>Aplicar

Após a conclusão de uma revisão de acesso, quer porque tenha atingido a data final ou a tenha parado manualmente, o botão **Aplicar** implementa o resultado da revisão. Se o acesso de um utilizador foi negado na revisão, este é o passo que irá remover a sua atribuição de funções.  

### <a name="export"></a>Exportar

Se pretender aplicar os resultados da revisão de acesso manualmente, pode exportar o reexame. O botão **Export** começará a descarregar um ficheiro CSV. Pode gerir os resultados em Excel ou outros programas que abrem ficheiros CSV.

### <a name="delete"></a>Eliminar

Se não estiver interessado na revisão, apague-o. O botão **Eliminar** remove a revisão do serviço de Gestão de Identidade Privilegiada.

> [!IMPORTANT]
> Não será obrigado a confirmar esta alteração destrutiva, por isso verifique se pretende eliminar essa revisão.

## <a name="next-steps"></a>Passos seguintes

- [Inicie uma revisão de acesso para funções da Azure AD na Gestão de Identidade Privilegiada](pim-how-to-start-security-review.md)
- [Realizar uma revisão de acesso das minhas funções de AD Azure na Gestão de Identidade Privilegiada](pim-how-to-perform-security-review.md)
