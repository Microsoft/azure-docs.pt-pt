---
title: Criar políticas de segurança personalizadas no Centro de Segurança Azure [ Microsoft Docs
description: Definições de política personalizadas azure monitorizadas pelo Azure Security Center.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: memildin
ms.openlocfilehash: c709890ae6c57a001c6a0e9df4e973bd3bd24602
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80258265"
---
# <a name="using-custom-security-policies"></a>Usando políticas de segurança personalizadas

Para ajudar a proteger os seus sistemas e ambiente, o Azure Security Center gera recomendações de segurança. Estas recomendações baseiam-se nas melhores práticas da indústria, incorporadas na política genérica de segurança por defeito fornecida a todos os clientes. Também podem vir do conhecimento do Security Center sobre a indústria e normas regulamentares.

Com esta funcionalidade, pode adicionar as suas próprias iniciativas *personalizadas.* Em seguida, receberá recomendações se o seu ambiente não seguir as políticas que cria. Quaisquer iniciativas personalizadas que crie aparecerão ao lado das iniciativas incorporadas no painel de conformidade regulamentar descrito no tutorial Melhorar a [sua conformidade regulamentar.](security-center-compliance-dashboard.md)

Conforme discutido [aqui](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#definition-location) na documentação da Política Azure, quando especifica um local para a sua iniciativa personalizada, deve ser um grupo de gestão ou uma subscrição. 

## <a name="to-add-a-custom-initiative-to-your-subscription"></a>Para adicionar uma iniciativa personalizada à sua subscrição 

1. Da barra lateral do Centro de Segurança, abra a página da política de **segurança.**

1. Selecione uma subscrição ou Grupo de Gestão ao qual gostaria de adicionar uma iniciativa personalizada.

    [![Selecionando uma subscrição para a qual irá criar a sua política personalizada](media/custom-security-policies/custom-policy-selecting-a-subscription.png)](media/custom-security-policies/custom-policy-selecting-a-subscription.png#lightbox)

    > [!NOTE]
    > Deve adicionar padrões personalizados ao nível de subscrição (ou superior) para que sejam avaliados e exibidos no Centro de Segurança. 
    >
    > Quando se adiciona um padrão personalizado, atribui uma *iniciativa* a esse âmbito. Por isso, recomendamos que selecione o âmbito mais amplo necessário para essa atribuição.

1. Na página de política de Segurança, sob as suas iniciativas personalizadas, clique em **Adicionar uma iniciativa personalizada**.

    [![Clique em **Adicione uma iniciativa personalizada**](media/custom-security-policies/custom-policy-add-initiative.png)](media/custom-security-policies/custom-policy-add-initiative.png#lightbox)

    Aparece a seguinte página:

    ![Criar ou adicionar uma política](media/custom-security-policies/create-or-add-custom-policy.png)

1. Na página de iniciativas personalizadas Add, reveja a lista de políticas personalizadas já criadas na sua organização. Se vir um que queira atribuir à sua subscrição, clique em **Adicionar**. Se não houver uma iniciativa na lista que responda às suas necessidades, ignore este passo.

1. Para criar uma nova iniciativa personalizada:

    1. Clique **em Criar novo**.
    1. Insira a localização e o nome da definição.
    1. Selecione as políticas para incluir e clique em **Adicionar**.
    1. Introduza os parâmetros desejados.
    1. Clique em **Guardar**.
    1. Na página de iniciativas personalizadas Add, clique em atualizar e a sua nova iniciativa será mostrada como disponível.
    1. Clique em **Adicionar** e atribua-o à sua subscrição.

    > [!NOTE]
    > A criação de novas iniciativas requer credenciais de proprietários de subscrições. Para mais informações sobre as funções do Azure, consulte [Permissões no Centro de Segurança Azure.](security-center-permissions.md)

    A sua nova iniciativa entra em vigor e pode ver o impacto nas seguintes duas formas:

    * A partir da barra lateral do Centro de Segurança, ao abrigo da Política & Compliance, **selecione conformidade regulamentar.** O painel de instrumentos de conformidade abre para mostrar a sua nova iniciativa personalizada ao lado das iniciativas incorporadas.
    
    * Começará a receber recomendações se o seu ambiente não seguir as políticas que definiu.

1. Para ver as recomendações resultantes para a sua política, clique em **Recomendações** da barra lateral para abrir a página de recomendações. As recomendações aparecerão com uma etiqueta "Personalizada" e estarão disponíveis dentro de aproximadamente uma hora.

    [![Recomendações personalizadas](media/custom-security-policies/custom-policy-recommendations.png)](media/custom-security-policies/custom-policy-recommendations-in-context.png#lightbox)


## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a criar políticas de segurança personalizadas. 

Para outros materiais relacionados, consulte os seguintes artigos: 

- [A visão geral das políticas de segurança](tutorial-security-policy.md)
- [Uma lista das políticas de segurança incorporadas](security-center-policy-definitions.md)