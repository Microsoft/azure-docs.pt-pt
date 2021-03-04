---
title: Criar um espaço de trabalho com proteção de exfiltração de dados ativada
description: Este artigo irá explicar como criar um espaço de trabalho com proteção de exfiltração de dados em Azure Synapse Analytics
author: NanditaV
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 12/01/2020
ms.author: NanditaV
ms.reviewer: jrasnick
ms.openlocfilehash: a3dc0b9e83af5a29172189f75e5a2174dc4d393d
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102096127"
---
# <a name="create-a-workspace-with-data-exfiltration-protection-enabled"></a>Criar um espaço de trabalho com proteção de exfiltração de dados ativada
Este artigo descreve como criar um espaço de trabalho com proteção de exfiltração de dados habilitado e como gerir os inquilinos aprovados da AZure AD para este espaço de trabalho.

>[!Note]
>Não é possível alterar a configuração do espaço de trabalho para a rede virtual gerida e a proteção contra a exfiltração de dados após a criação do espaço de trabalho.

## <a name="prerequisites"></a>Pré-requisitos
- Permissões para criar um recurso de espaço de trabalho em Azure.
- Sinaapse permissões de espaço de trabalho para criar pontos finais privados geridos.
- Assinaturas registadas para o fornecedor de recursos de rede. [Saiba mais.](../../azure-resource-manager/management/resource-providers-and-types.md)

Siga os passos listados no [Quickstart: Crie um espaço de trabalho synapse](../quickstart-create-workspace.md) para começar a criar o seu espaço de trabalho. Antes de criar o seu espaço de trabalho, utilize as informações abaixo para adicionar proteção de exfiltração de dados ao seu espaço de trabalho.

## <a name="add-data-exfiltration-protection-when-creating-your-workspace"></a>Adicione proteção de exfiltração de dados ao criar o seu espaço de trabalho
1. No separador 'Rede', selecione a caixa de verificação "Enable managed virtual network".
1. Selecione "Sim" para a opção "Permitir o tráfego de dados de saída apenas para alvos aprovados".
1. Escolha os inquilinos AD Azure aprovados para este espaço de trabalho.
1. Reveja a configuração e crie o espaço de trabalho.
:::image type="content" source="./media/how-to-create-a-workspace-with-data-exfiltration-protection/workspace-creation-data-exfiltration-protection.png" alt-text="Screenshot que mostra um espaço de trabalho Create Synapse com 'Enable manage virtual network' selecionado.":::

## <a name="manage-approved-azure-active-directory-tenants-for-the-workspace"></a>Gerir inquilinos aprovados do Azure Ative Directory para o espaço de trabalho
1. Do portal Azure do espaço de trabalho, navegue até "Inquilinos aprovados da AD Azure". A lista de inquilinos aprovados da AD AD para o espaço de trabalho será listada aqui. O inquilino do espaço de trabalho está incluído por defeito e não está listado.
1. Utilize "+Add" para incluir novos inquilinos na lista aprovada.
1. Para remover um inquilino AZure AD da lista aprovada, selecione o inquilino e selecione em "Delete" e, em seguida, "Save".
:::image type="content" source="./media/how-to-create-a-workspace-with-data-exfiltration-protection/workspace-manage-aad-tenant-list.png" alt-text="Criar um espaço de trabalho com proteção de exfiltração de dados":::


## <a name="connecting-to-azure-resources-in-approved-azure-ad-tenants"></a>Ligação aos recursos da Azure em inquilinos aprovados da AD Azure

Você pode criar pontos finais privados geridos para conectar aos recursos Azure que residem em inquilinos AD Azure, que são aprovados para um espaço de trabalho. Siga os passos indicados no guia para [a criação de pontos finais privados geridos.](./how-to-create-managed-private-endpoints.md)

>[!IMPORTANT]
>Os recursos em inquilinos que não sejam o inquilino do espaço de trabalho não devem ter regras de firewall bloqueadas para que as piscinas SQL se conectem a eles. Os recursos dentro da rede virtual gerida do espaço de trabalho, como os clusters Spark, podem ligar-se sobre ligações privadas geridas a recursos protegidos por firewall.

## <a name="known-limitations"></a>Limitações conhecidas
Os utilizadores podem fornecer um ficheiro de configuração ambiental para instalar pacotes Python a partir de repositórios públicos como o PyPI. Nos espaços de trabalho protegidos pela exfiltração de dados, as ligações aos repositórios de saída estão bloqueadas. Como resultado, a biblioteca Python instalada a partir de repositórios públicos como pyPI não são suportados. 

Como alternativa, os utilizadores podem carregar pacotes de espaço de trabalho ou criar um canal privado dentro da sua conta primária de Armazenamento de Data Lake. Para mais informações, visite [a gestão de pacotes em Azure Synapse Analytics](./spark/../../spark/apache-spark-azure-portal-add-libraries.md) 
  
## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [a proteção de exfiltração de dados nos espaços de trabalho da Sinapse](./workspace-data-exfiltration-protection.md)

Saiba mais sobre [Rede Virtual gerida do espaço de trabalho](./synapse-workspace-managed-vnet.md)

Saiba mais sobre [os pontos finais privados geridos](./synapse-workspace-managed-private-endpoints.md)

[Criar pontos finais privados geridos para as suas fontes de dados](./how-to-create-managed-private-endpoints.md)
