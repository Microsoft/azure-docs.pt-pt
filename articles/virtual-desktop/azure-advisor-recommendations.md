---
title: Azure Advisor Windows Virtual Desktop Walkthrough - Azure
description: Como resolver as recomendações do Azure Advisor para o Windows Virtual Desktop.
author: Heidilohr
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 760e0212d2d863e6b869c23c2e523a0e056a28ed
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90033035"
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

3. Na sua piscina de anfitriões, selecione **Propriedades** na coluna esquerda. Em seguida, desça até ver "Ambiente de validação". Selecione **Sim**e, em seguida, selecione **Aplicar**.

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

3. Na sua piscina de anfitrião, selecione o **separador Propriedades** na coluna do lado direito do ecrã. Em seguida, desça até ver "Ambiente de validação". Selecione **Não**e, em seguida, selecione **Aplicar**.

>[!div class="mx-imgBorder"]
>![Uma imagem do menu Propriedades. "Ambiente de validação" é realçado a vermelho, e a bolha "Não" é selecionada.](media/validation-no.png)

Estas alterações não farão o aviso desaparecer imediatamente, mas devem desaparecer eventualmente. Azure Advisor atualiza duas vezes por dia. Até lá, pode adiar ou rejeitar a recomendação manualmente. Recomendamos que deixe a recomendação ir embora por si só. Desta forma, o Azure Advisor pode informá-lo se se deparar com algum problema à medida que as definições mudam.

## <a name="not-enough-links-are-unblocked-to-successfully-implement-your-vm"></a>"Não há ligações suficientes desbloqueadas para implementar com sucesso o seu VM"

Esta recomendação surge no âmbito da Excelência Operacional.

É necessário desbloquear URLs específicos para se certificar de que a sua máquina virtual (VM) funciona corretamente. Pode ver a lista na [lista de URL seguro.](safe-url-list.md) Se os URLs não estiverem desbloqueados, então o seu VM não funcionará corretamente.

Para resolver esta recomendação, certifique-se de desbloquear todos os URLs na [lista de URL seguro](safe-url-list.md). Também pode utilizar tags Service Tag ou FQDN para desbloquear URLs.

## <a name="propose-new-recommendations"></a>Propor novas recomendações

Pode ajudar-nos a melhorar o Azure Advisor, apresentando ideias para recomendações. A sua recomendação pode ajudar outro utilizador a sair de uma situação difícil. Para submeter uma sugestão, vá ao [nosso fórum UserVoice](https://windowsvirtualdesktop.uservoice.com/forums/930847-azure-advisor-recommendations) e preencha o formulário de submissão. Quando preencher o formulário, certifique-se de nos dar o máximo de detalhes possível.

## <a name="next-steps"></a>Passos seguintes

Se procura guias mais aprofundados sobre como resolver problemas comuns, consulte a [visão geral da resolução de problemas, o feedback e o suporte para o Windows Virtual Desktop](troubleshoot-set-up-overview.md).
