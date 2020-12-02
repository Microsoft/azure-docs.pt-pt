---
title: Use o portal Azure para resolver problemas Azure Stack Edge Pro que encomenda problemas Microsoft Docs
description: Descreve como resolver problemas a Azure Stack Edge Pro a encomendar problemas.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 08/11/2020
ms.author: alkohli
ms.openlocfilehash: be7d477d08b025fd450bfa859e8eb677ba680143
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96446377"
---
# <a name="troubleshoot-your-azure-stack-edge-pro-ordering-issues"></a>Resolução de problemas do seu Azure Stack Edge Pro questões

Este artigo descreve como resolver problemas com problemas a Azure Stack Edge Pro a encomendar problemas.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> * Problemas de ordenação de resolução de problemas

## <a name="unsupported-subscription-or-region"></a>Subscrição ou região não suportada

**Descrição do erro:** No portal Azure, se tiver o erro:

*A subscrição ou região selecionada não é suportada. Escolha uma subscrição ou região diferente.*

![Subscrição ou região não suportada](media/azure-stack-edge-troubleshoot-ordering/azure-stack-edge-troubleshoot-ordering-01.png)

**Solução sugerida:**  Certifique-se de que utilizou uma subscrição suportada, como [o Microsoft Enterprise Agreement (EA),](https://azure.microsoft.com/overview/sales-number/)o Cloud Solution Provider [(CSP)](/partner-center/azure-plan-lp)ou [o Microsoft Azure Sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/). As assinaturas pay-as-you-go não são suportadas. Para obter mais informações, consulte [os pré-requisitos de recursos Azure Stack Edge](azure-stack-edge-deploy-prep.md#prerequisites).

Existe a possibilidade de a Microsoft permitir uma atualização do tipo de subscrição caso a caso. Contacte [o suporte da Microsoft](https://azure.microsoft.com/support/options/) para que possam compreender as suas necessidades e ajustar estes limites adequadamente.

## <a name="selected-subscription-type-not-supported"></a>Tipo de assinatura selecionado não suportado

**Erro:** Tem uma subscrição EA, CSP ou patrocinada e obtém o seguinte erro:

*O tipo de subscrição selecionado não é suportado. Certifique-se de que utiliza uma subscrição suportada. [Saiba mais.](azure-stack-edge-deploy-prep.md#prerequisites) Se utilizar um tipo de assinatura suportado, certifique-se de que o `Microsoft.DataBoxEdge` fornecedor está registado. Para obter informações sobre como se registar, consulte [o fornecedor de recursos do Registo.](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers)*

**Solução sugerida:** Siga estes passos para registar o seu fornecedor de recursos Azure Stack Edge:

1. No portal Azure, **Home** aceda a  >  **Assinaturas Domésticas.**

2. Selecione a subscrição que utilizará para encomendar o seu dispositivo.

3. Selecione **fornecedores de recursos** e, em seguida, procure **o Microsoft.DataBoxEdge**.

    ![Registar o fornecedor de recursos](media/azure-stack-edge-troubleshoot-ordering/azure-stack-edge-troubleshoot-ordering-02.png)

Se não tiver acesso ao proprietário ou ao contribuinte para registar o fornecedor de recursos, vê o seguinte erro: *O nome da subscrição de subscrição &lt; não tem &gt; permissões para registar o(s) fornecedor de recursos: Microsoft.DataBoxEdge.*

Para obter mais informações, consulte [os fornecedores de recursos do Registo.](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers)

## <a name="microsoftdataboxedge-not-registered-for-subscription"></a>Microsoft.DataBoxEdge não registado para subscrição

**Erro:** No portal Azure, seleciona uma subscrição para utilizar para Azure Stack Edge Pro ou Data Box Gateway e obtém o seguinte erro:

*Fornecedor de recursos: Microsoft.DataBoxEdge não está registado para &lt; nome de subscrição &gt; e não tem permissões para registar um fornecedor de recursos para &lt; o nome &gt; de assinatura de assinatura.*

**Solução sugerida:** Eleve o acesso à sua subscrição ou encontre alguém com acesso ao proprietário ou colaborador para registar o fornecedor de recursos.

## <a name="resource-disallowed-by-policy"></a>Recurso não permitido pela política

**Erro:** No portal Azure, tenta registar um fornecedor de recursos e obter o seguinte erro:

*O &lt; nome dos recursos foi proibido pela &gt; política. (Código: RequestDisallowedByPolicy). Iniciativa: Negar tipos de recursos geralmente indesejados. Política: Tipos de recursos não permitidos.*

**Solução sugerida:** Este erro ocorre devido a uma política Azure existente que bloqueia a criação de recursos. As políticas Azure são definidas pelo administrador de sistema de uma organização para garantir o cumprimento durante a utilização ou criação de recursos Azure. Se tal política estiver a bloquear a criação de recursos do Azure Stack Edge, contacte o administrador do sistema para editar a sua política Azure.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre como resolver os [seus problemas Azure Stack Edge Pro](azure-stack-edge-troubleshoot.md).