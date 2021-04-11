---
title: Azure Advisor Windows Virtual Desktop Walkthrough - Azure
description: Como resolver as recomendações do Azure Advisor para o Windows Virtual Desktop.
author: Heidilohr
ms.topic: conceptual
ms.date: 03/31/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 666f882dec6c00b9fe5421485afded1fbd57d961
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448309"
---
# <a name="how-to-resolve-azure-advisor-recommendations"></a>Como resolver as recomendações do Azure Advisor

Este artigo descreve como pode resolver recomendações que aparecem no Azure Advisor para o Windows Virtual Desktop.

## <a name="no-validation-environment-enabled"></a>"Nenhum ambiente de validação habilitado"

>[!div class="mx-imgBorder"]
>![Uma imagem da página Azure Advisor Operational Excellence. A recomendação "sem ambiente de validação habilitado" é realçada a vermelho.](media/no-validation-environment.png)

Esta recomendação surge no âmbito da Excelência Operacional. A recomendação deve também mostrar-lhe uma mensagem de aviso como esta:

"Não tem um ambiente de validação ativado nesta subscrição. Quando fez as suas piscinas de anfitrião, selecionou **o Nº** para "Ambiente de Validação" no separador Propriedades. Para garantir a continuidade do negócio através das implementações do serviço virtual de desktop do Windows, certifique-se de que tem pelo menos um pool de anfitriões com um ambiente de validação onde pode testar potenciais problemas."

Pode fazer desaparecer esta mensagem de aviso, permitindo um ambiente de validação numa das suas piscinas hospedeiras.

Para permitir um ambiente de validação:

1. Vá à página inicial do portal Azure e selecione a piscina de anfitriões que pretende alterar.

2. Em seguida, selecione a piscina hospedeira que pretende mudar de um ambiente de produção para um ambiente de validação.

3. Na sua piscina de anfitriões, selecione **Propriedades** na coluna esquerda. Em seguida, desça até ver "Ambiente de validação". Selecione **Sim** e, em seguida, selecione **Aplicar**.

>[!div class="mx-imgBorder"]
>![Uma imagem do menu Propriedades. "Ambiente de validação" é realçado a vermelho, e a bolha "Sim" é selecionada.](media/validation-yes.png)

Estas alterações não farão o aviso desaparecer imediatamente, mas devem desaparecer eventualmente. Azure Advisor atualiza duas vezes por dia. Até lá, pode adiar ou rejeitar a recomendação manualmente. Recomendamos que deixe a recomendação ir embora por si só. Desta forma, o Azure Advisor pode informá-lo se se deparar com algum problema à medida que as definições mudam.

## <a name="not-enough-production-non-validation-environments-enabled"></a>"Ambientes de produção insuficientes (não validação) habilitados"

Esta recomendação surge no âmbito da Excelência Operacional.

Para esta recomendação, a mensagem de aviso aparece por uma destas razões:

- Você tem muitas piscinas de anfitrião em seu ambiente de validação.
- Você não tem nenhuma piscina de anfitrião de produção.

Recomendamos que os utilizadores tenham menos de metade das suas piscinas hospedeiras num ambiente de validação.

Para resolver este aviso:

1. Vá à página inicial do portal Azure.

2. Selecione as piscinas hospedeiras que deseja alterar de validação para produção.

3. Na sua piscina de anfitrião, selecione o **separador Propriedades** na coluna do lado direito do ecrã. Em seguida, desça até ver "Ambiente de validação". Selecione **Não** e, em seguida, selecione **Aplicar**.

>[!div class="mx-imgBorder"]
>![Uma imagem do menu Propriedades. "Ambiente de validação" é realçado a vermelho, e a bolha "Não" é selecionada.](media/validation-no.png)

Estas alterações não farão o aviso desaparecer imediatamente, mas devem desaparecer eventualmente. Azure Advisor atualiza duas vezes por dia. Até lá, pode adiar ou rejeitar a recomendação manualmente. Recomendamos que deixe a recomendação ir embora por si só. Desta forma, o Azure Advisor pode informá-lo se se deparar com algum problema à medida que as definições mudam.

## <a name="not-enough-links-are-unblocked-to-successfully-implement-your-vm"></a>"Não há ligações suficientes desbloqueadas para implementar com sucesso o seu VM"

Esta recomendação surge no âmbito da Excelência Operacional.

É necessário desbloquear URLs específicos para se certificar de que a sua máquina virtual (VM) funciona corretamente. Pode ver a lista na [lista de URL seguro.](safe-url-list.md) Se os URLs não estiverem desbloqueados, então o seu VM não funcionará corretamente.

Para resolver esta recomendação, certifique-se de desbloquear todos os URLs na [lista de URL seguro](safe-url-list.md). Também pode utilizar tags Service Tag ou FQDN para desbloquear URLs.

## <a name="next-steps"></a>Passos seguintes

Se procura guias mais aprofundados sobre como resolver problemas comuns, consulte a [visão geral da resolução de problemas, o feedback e o suporte para o Windows Virtual Desktop](troubleshoot-set-up-overview.md).
