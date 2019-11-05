---
title: Criar políticas de segurança personalizadas na central de segurança do Azure | Microsoft Docs
description: Definições de política personalizada do Azure monitoradas pela central de segurança do Azure.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: d5432c794c26e350b23fe47aa1574422143ca4b2
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73521959"
---
# <a name="using-custom-security-policies-preview"></a>Usando políticas de segurança personalizadas (versão prévia)

Para ajudar a proteger seus sistemas e ambientes, a central de segurança do Azure gera recomendações de segurança. Essas recomendações são baseadas em práticas recomendadas do setor, que são incorporadas à política de segurança padrão genérica fornecida para todos os clientes. Eles também podem vir do conhecimento da central de segurança de padrões do setor e regulamentadores.

Com esse recurso de visualização, você pode adicionar suas próprias iniciativas *personalizadas* . Em seguida, você receberá recomendações se os computadores não seguirem as políticas que você criar.

## <a name="to-add-a-custom-initiative-to-your-subscription"></a>Para adicionar uma iniciativa personalizada à sua assinatura 

1. Na barra lateral da central de segurança, abra a página **política de segurança** .

1. Selecione uma assinatura ou grupo de gerenciamento ao qual você gostaria de adicionar uma iniciativa personalizada.

    [![selecionar uma assinatura para a qual você criará sua política personalizada](media/custom-security-policies/custom-policy-selecting-a-subscription.png)](media/custom-security-policies/custom-policy-selecting-a-subscription.png#lightbox)

    > [!NOTE]
    > Quando você adiciona um padrão personalizado, ele atribui uma *iniciativa* a esse escopo. Portanto, recomendamos que você selecione o escopo mais largo necessário para essa atribuição. 

1. Na página política de segurança, em suas iniciativas personalizadas (versão prévia), clique em **Adicionar uma iniciativa personalizada**.

    [![clique em * * adicionar uma iniciativa personalizada * *](media/custom-security-policies/custom-policy-add-initiative.png)](media/custom-security-policies/custom-policy-add-initiative.png#lightbox)

    A seguinte página é exibida:

    ![Criar ou adicionar uma política](media/custom-security-policies/create-or-add-custom-policy.png)

1. Na página Adicionar iniciativas personalizadas, examine a lista de políticas personalizadas já criadas em sua organização. Se você vir um que deseja atribuir à sua assinatura, clique em **Adicionar**. Se não houver uma iniciativa na lista que atenda às suas necessidades, pule esta etapa.

1. Para criar uma nova iniciativa personalizada:

    1. Clique em **criar novo**.
    1. Insira o local e o nome da definição.
    1. Selecione as políticas a serem incluídas e clique em **Adicionar**.
    1. Insira os parâmetros desejados.
    1. Clique em **Guardar**.
    1. Na página Adicionar iniciativas personalizadas, clique em atualizar e sua nova iniciativa será mostrada como disponível.
    1. Clique em **Adicionar** e atribua-o à sua assinatura.

    > [!NOTE]
    > A criação de novas iniciativas requer credenciais de proprietário da assinatura. Para obter mais informações sobre as funções do Azure, consulte [permissões na central de segurança do Azure](security-center-permissions.md).

1. Para ver as recomendações resultantes para sua política, clique em **recomendações** na barra lateral para abrir a página recomendações. As recomendações serão exibidas com um rótulo "personalizado" e estarão disponíveis por até 30 minutos.

    [![recomendações personalizadas](media/custom-security-policies/custom-policy-recommendations.png)](media/custom-security-policies/custom-policy-recommendations-in-context.png#lightbox)


## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu a criar políticas de segurança personalizadas. 

Para obter outros materiais relacionados, consulte os seguintes artigos: 

- [A visão geral das políticas de segurança](tutorial-security-policy.md)
- [Uma lista das políticas de segurança internas](security-center-policy-definitions.md)