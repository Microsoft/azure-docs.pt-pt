---
title: APIs de acesso condicional e PowerShell - Diretório Ativo Azure
description: Utilizando as APIs de Acesso Condicional AD Azure e PowerShell para gerir políticas como o código
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 09/10/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: videor, jeevanb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6d4dd6e64053af41d66e3accbb7fc9b21d951fc6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96860218"
---
# <a name="conditional-access-programmatic-access"></a>Acesso Condicional: Acesso programático

Muitas organizações expressaram a sua necessidade de gerir o máximo possível de seus ambientes como o código. Utilizando o Microsoft Graph pode tratar as políticas de Acesso Condicional como qualquer outra peça de código no seu ambiente.

O Microsoft Graph fornece um modelo de programabilidade unificado que as organizações podem usar para interagir com dados no Microsoft 365, Windows 10 e Enterprise Mobility + Security. Para obter mais informações sobre o Microsoft Graph, consulte o artigo, [Visão geral do Microsoft Graph](/graph/overview).

![Uma imagem mostrando os recursos primários e relações que fazem parte do gráfico](./media/howto-conditional-access-apis/microsoft-graph.png)

Os exemplos que se seguem são fornecidos como não tem apoio. Pode utilizar estes exemplos como base para a ferramenta na sua organização. 

Muitos dos exemplos seguintes utilizam ferramentas como [Identidades Geridas,](../managed-identities-azure-resources/overview.md) [Aplicações Lógicas,](../../logic-apps/logic-apps-overview.md) [OneDrive,](https://www.microsoft.com/microsoft-365/onedrive/online-cloud-storage) [Equipas](https://www.microsoft.com/microsoft-365/microsoft-teams/group-chat-software/)e Cofre de [Chaves Azure.](../../key-vault/general/overview.md)

## <a name="configure"></a>Configurar

### <a name="powershell"></a>PowerShell

Para muitos administradores, o PowerShell já é uma ferramenta de scripts compreendida. O exemplo a seguir mostra como utilizar o [módulo Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureAD) para gerir as políticas de Acesso Condicional.

- [Configurar políticas de acesso condicional com comandos Azure AD PowerShell](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/01-configure/powershell)

### <a name="graph-api"></a>Graph API

Este exemplo mostra as opções básicas de Criar, Ler, Atualizar e Eliminar (CRUD) disponíveis nas APIs do Gráfico de Acesso Condicional. O exemplo também inclui alguns modelos JSON que você pode usar para criar algumas políticas de amostra.

- [Configurar políticas de acesso condicional com chamadas API do Microsoft Graph](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/01-configure/graphapi)

### <a name="configure-using-templates"></a>Configurar usando modelos

Utilize APIs de acesso condicional para implementar políticas de acesso condicional no seu ambiente de pré-produção utilizando um modelo.

- [Configurar políticas de acesso condicional com modelos de API do Microsoft Graph](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/01-configure/templates)

## <a name="test"></a>Teste

Este exemplo modela práticas de implementação mais seguras com fluxos de trabalho de aprovação que podem copiar políticas de acesso condicional de um ambiente, como pré-produção, para outro, como o seu ambiente de produção.

- [Promover políticas de acesso condicional a partir de ambientes de teste](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/02-test)

## <a name="deploy"></a>Implementar

Este exemplo fornece um mecanismo para executar uma implementação faseada políticas de acesso condicional gradualmente à população utilizadora, permitindo-lhe gerir precocemente problemas de impacto e spot de suporte.

- [Implementar políticas de acesso condicional a ambientes de produção com fluxos de trabalho de aprovação](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/03-deploy)

## <a name="monitor"></a>Monitor

Este exemplo fornece um mecanismo para monitorizar as mudanças na política de acesso condicional ao longo do tempo e pode desencadear alertas quando as políticas-chave são alteradas.

- [Monitor implementado políticas de acesso condicional para alterações e alertas de desencadeamento](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/04-monitor)

## <a name="manage"></a>Gerir

### <a name="backup-and-restore"></a>Cópia de segurança e restauro

Automatizar o backup e restauro das políticas de Acesso Condicional com aprovações em Equipas usando este exemplo.

- [Gerir o processo de backup e restaurar as políticas de acesso condicional usando chamadas API do Microsoft Graph](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/05-manage/01-backup-restore)

### <a name="emergency-access-accounts"></a>Contas de acesso de emergência

Vários administradores podem criar políticas de Acesso Condicional e podem esquecer-se de adicionar as suas [contas de acesso de emergência](../roles/security-emergency-access.md) como uma exclusão a essas políticas. Este exemplo garante que todas as políticas são atualizadas para incluir as suas contas de acesso de emergência designadas.

- [Gerir a atribuição de contas de acesso de emergência às políticas de acesso condicional utilizando chamadas API do Microsoft Graph](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/05-manage/02-emergency-access)

### <a name="contingency-planning"></a>Planos de contingência

As coisas nem sempre funcionam como queremos, quando isso acontece, precisamos de uma maneira de voltar a um estado onde o trabalho pode continuar. O exemplo a seguir fornece-lhe uma forma de reverter as suas políticas para um plano de contingência conhecido e desativar outras políticas de Acesso Condicional.

- [Gerir a ativação de políticas de contingência de acesso condicional usando chamadas API do Microsoft Graph](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/05-manage/03-contingency)

## <a name="community-contribution"></a>Contribuição comunitária

Estas amostras estão disponíveis no nosso [repositório GitHub.](https://github.com/Azure-Samples/azure-ad-conditional-access-apis) Temos o prazer de apoiar as contribuições comunitárias minuciosas gitHub questões e pedidos de pull.

## <a name="next-steps"></a>Passos seguintes

- [Descrição Geral do Microsoft Graph](/graph/overview)

- [Acesso Condicional API](/graph/api/resources/conditionalaccesspolicy)

- [Localização nomeada API](/graph/api/resources/namedlocation)
