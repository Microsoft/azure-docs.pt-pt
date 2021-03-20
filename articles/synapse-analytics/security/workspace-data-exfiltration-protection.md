---
title: Proteção de exfiltração de dados para espaços de trabalho Azure Synapse Analytics
description: Este artigo explicará a proteção de exfiltração de dados no Azure Synapse Analytics
author: nanditavalsan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 12/01/2020
ms.author: nanditav
ms.reviewer: jrasnick
ms.openlocfilehash: 5fed7649353c0ef6cb222c0b0c91a5203665e9fa
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104598053"
---
# <a name="data-exfiltration-protection-for-azure-synapse-analytics-workspaces"></a>Proteção de exfiltração de dados para espaços de trabalho Azure Synapse Analytics
Este artigo explicará a proteção de exfiltração de dados no Azure Synapse Analytics

## <a name="securing-data-egress-from-synapse-workspaces"></a>Segurança da saída de dados dos espaços de trabalho da Sinaapse
Os espaços de trabalho Azure Synapse Analytics suportam a proteção de exfiltração de dados para espaços de trabalho. Com a proteção de exfiltração, pode proteger-se contra infiltrados maliciosos que acedam aos seus recursos Azure e exfiltrando dados sensíveis para locais fora do âmbito da sua organização. No momento da criação do espaço de trabalho, pode optar por configurar o espaço de trabalho com uma rede virtual gerida e proteção adicional contra a exfiltração de dados. Quando um espaço de trabalho é criado com uma [rede virtual gerida,](./synapse-workspace-managed-vnet.md)a integração de dados e os recursos spark são implantados na rede virtual gerida. As piscinas SQL dedicadas do espaço de trabalho e as piscinas SQL sem servidor têm capacidades multi-arrendatários e, como tal, precisam de existir fora da rede virtual gerida. Para espaços de trabalho com proteção de exfiltração de dados, os recursos dentro da rede virtual gerida comunicam sempre sobre [os pontos finais privados geridos](./synapse-workspace-managed-private-endpoints.md) e os recursos Sinapse SQL só podem ligar-se aos recursos autorizados do Azure (alvos de ligações privadas geridas aprovadas a partir do espaço de trabalho). 

> [!Note]
> Não é possível alterar a configuração do espaço de trabalho para a rede virtual gerida e a proteção contra a exfiltração de dados após a criação do espaço de trabalho.

## <a name="managing-synapse-workspace-data-egress-to-approved-targets"></a>Gerir a erupção de dados do espaço de trabalho da Synapse para metas aprovadas
Após a criação do espaço de trabalho com a proteção de exfiltração de dados ativada, os proprietários do recurso do espaço de trabalho podem gerir a lista de inquilinos aprovados da Azure AD para o espaço de trabalho. Os utilizadores com [as permissões certas](./synapse-workspace-access-control-overview.md) no espaço de trabalho podem usar o Estúdio Synapse para criar pedidos de conexão de ponto final privado geridos para recursos nos inquilinos aprovados do Azure AD aprovados no espaço de trabalho. A criação de ponto final privado gerido será bloqueada se o utilizador tentar criar uma ligação privada de ponto final a um recurso num inquilino não aprovado.

## <a name="sample-workspace-with-data-exfiltration-protection-enabled"></a>Espaço de trabalho de amostra com proteção de exfiltração de dados ativada
Utilizemos um exemplo para ilustrar a proteção contra a exfiltração de dados para os espaços de trabalho da Sinapse. A Contoso tem recursos Azure no Inquilino A e inquilino B e há a necessidade de estes recursos se conectarem de forma segura. Um espaço de trabalho da Sinapse foi criado no Inquilino A com Inquilino B adicionado como um inquilino aprovado da AD AZure. O diagrama mostra ligações privadas de ponto final às contas de Armazenamento A azul no Inquilino A e inquilino B que foram aprovadas pelos proprietários da conta de Armazenamento. O diagrama também mostra a criação de ponto final privado bloqueado. A criação deste ponto final privado foi bloqueada por ter como alvo uma conta de Armazenamento Azure no inquilino da AD Fabrikam Azure, que não é um inquilino aprovado da AD AD para o espaço de trabalho de Contoso.

:::image type="content" source="media/workspace-data-exfiltration-protection/workspace-data-exfiltration-protection-diagram.png" alt-text="Este diagrama mostra como a proteção contra a exfiltração de dados é implementada para espaços de trabalho da Sinapse":::

>[!IMPORTANT]
>
> - Os recursos em inquilinos que não sejam o inquilino do espaço de trabalho não devem ter regras de firewall bloqueadas para que as piscinas SQL se conectem a eles. Os recursos dentro da rede virtual gerida do espaço de trabalho, como os clusters Spark, podem ligar-se sobre ligações privadas geridas a recursos protegidos por firewall.
>
> - A Metastore é desativada em espaços de trabalho da Synapse que têm Rede Virtual Gerida com proteção de exfiltração de dados ativada. Não pode usar o Spark SQL nestes espaços de trabalho.
> >

## <a name="next-steps"></a>Passos Seguintes

Saiba como [criar um espaço de trabalho com proteção de exfiltração de dados ativada](./how-to-create-a-workspace-with-data-exfiltration-protection.md)

Saiba mais sobre [Rede Virtual gerida do espaço de trabalho](./synapse-workspace-managed-vnet.md)

Saiba mais sobre [os pontos finais privados geridos](./synapse-workspace-managed-private-endpoints.md)

[Criar pontos finais privados geridos para as suas fontes de dados](./how-to-create-managed-private-endpoints.md)
