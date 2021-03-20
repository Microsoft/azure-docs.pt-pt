---
title: Termine a revisão de acesso das funções AD Azure em PIM - Azure AD | Microsoft Docs
description: Saiba como concluir uma revisão de acesso das funções da AD Azure em Azure AD Gestão de Identidade Privilegiada (PIM) e ver os resultados
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: fa7e8089fbbf2ee653100a05383fdbdc877ffda4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "84742221"
---
# <a name="complete-an-access-review-of-azure-ad-roles-in-privileged-identity-management"></a>Complete uma revisão de acesso das funções de Azure AD em Gestão de Identidade Privilegiada

Os administradores privilegiados podem rever o acesso privilegiado uma vez iniciada uma [revisão de acesso.](pim-how-to-start-security-review.md)  A Gestão de Identidade Privilegiada (PIM) enviará automaticamente um e-mail aos utilizadores da sua organização Azure Ative Directory (Azure AD), levando-os a rever o seu acesso. Se um utilizador não tiver um e-mail, pode enviar-lhe as instruções [sobre como realizar uma revisão de acesso](pim-how-to-perform-security-review.md).

Após o fim do período de revisão de acesso, ou todos os utilizadores terminaram a sua auto-revisão, siga os passos deste artigo para gerir a revisão e ver os resultados.

## <a name="manage-access-reviews"></a>Gerir comentários de acesso

1. Vá ao [portal Azure](https://portal.azure.com/) e selecione o serviço **de Gestão de Identidade Privilegiada AZure AD** no seu painel de instrumentos.
1. Selecione a secção **de avaliações** de Acesso do painel de instrumentos.
1. Selecione a revisão de acesso que pretende gerir.

Na lâmina de detalhe da revisão de acesso, existem várias opções para gerir essa revisão.

![Botões privilegiados de revisão de acesso à Gestão de Identidade - screenshot](./media/pim-how-to-complete-review/review-buttons.png)

### <a name="remind"></a>Lembre-se

Se for configurada uma revisão de acesso para que os utilizadores se revejam a si próprios, o botão **Lembrete** envia uma notificação.

### <a name="stop"></a>Parar

Todas as avaliações de acesso têm uma data de fim, mas pode usar o botão **Stop** para terminá-lo mais cedo. Se algum utilizadores não tiver sido revisto por esta altura, não poderá fazê-lo depois de parar a revisão. Não pode reiniciar uma revisão depois de ter sido interrompida.

### <a name="apply"></a>Aplicar

Depois de concluída uma revisão de acesso, quer porque atingiu a data de fim ou a parou manualmente, o botão **Aplicar** implementa o resultado da revisão. Se o acesso de um utilizador foi negado na revisão, este é o passo que removerá a sua atribuição de função.  

### <a name="export"></a>Exportar

Se quiser aplicar manualmente os resultados da revisão de acesso, pode exportar a revisão. O **botão Exportação** começará a descarregar um ficheiro CSV. Pode gerir os resultados no Excel ou noutros programas que abram ficheiros CSV.

### <a name="delete"></a>Eliminar

Se não estiver mais interessado na revisão, apague-a. O botão **Eliminar** remove a revisão do serviço de Gestão de Identidade Privilegiada.

> [!IMPORTANT]
> Não será obrigado a confirmar esta alteração destrutiva, por isso verifique se pretende eliminar essa revisão.

## <a name="next-steps"></a>Passos seguintes

- [Inicie uma revisão de acesso para funções Azure AD em Gestão de Identidade Privilegiada](pim-how-to-start-security-review.md)
- [Realizar uma revisão de acesso das minhas funções de Ad Azure em Gestão de Identidade Privilegiada](pim-how-to-perform-security-review.md)
