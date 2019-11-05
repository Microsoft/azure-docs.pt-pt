---
title: Visão geral da implantação do Azure firewall Manager Preview
description: Conheça as etapas de implantação de alto nível necessárias para a versão prévia do Gerenciador de firewall do Azure
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: overview
ms.date: 10/25/2019
ms.author: victorh
ms.openlocfilehash: df87e652d2969d4ae12e97a2b455648cf39382c3
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73502031"
---
# <a name="azure-firewall-manager-preview-deployment-overview"></a>Visão geral da implantação do Azure firewall Manager Preview

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Há mais de uma maneira de implantar a visualização do Gerenciador de firewall do Azure, mas o processo geral a seguir é recomendado.

## <a name="prerequisites"></a>Pré-requisitos

> [!IMPORTANT]
> A versão prévia do Gerenciador de firewall do Azure deve ser habilitada explicitamente usando o comando `Register-AzProviderFeature` PowerShell.
>Em um prompt de comando do PowerShell, execute os seguintes comandos:
>
>```azure-powershell
>connect-azaccount
>Register-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network
>```
>Demora até 30 minutos para que o registro do recurso seja concluído. Execute o seguinte comando para verificar seu status de registro de >:
>
>`Get-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network`



## <a name="general-deployment-process"></a>Processo de implantação geral

1. Criar sua arquitetura de Hub e spoke

   - Crie um hub virtual protegido usando o Gerenciador de firewall do Azure e adicione conexões de rede virtual.<br>*or*<br>
   - Crie um hub de WAN virtual e adicione conexões de rede virtual.
2. Selecionar provedores de segurança

   - Feito durante a criação de um hub virtual protegido.<br>*or*<br>
   - Converta um hub WAN virtual existente para proteger o Hub virtual.
3. Criar uma política de firewall e associá-la ao seu hub

   - Aplicável somente se estiver usando o Firewall do Azure.
   - As políticas de SECaaS (segurança como serviço) de terceiros são configuradas por meio da experiência de gerenciamento de parceiros.
4. Definir configurações de rota para rotear o tráfego para o Hub protegido

   - Encaminhe facilmente o tráfego para o Hub protegido para filtragem e registro em log sem UDR (rotas definidas pelo usuário) em redes virtuais do spoke usando a página de configuração de rota protegida do Hub virtual.

> [!NOTE]
> - Você não pode ter mais de um hub por Wan virtual por região. Mas você pode adicionar várias WANs virtuais na região para conseguir isso.
> - Não é possível ter espaços IP sobrepostos para hubs em um vWAN.
> - As conexões de VNet do Hub devem estar na mesma região que o Hub.

## <a name="next-steps"></a>Passos seguintes

- [Tutorial: proteger sua rede de nuvem com a versão prévia do Gerenciador de firewall do Azure usando o portal do Azure](secure-cloud-network.md)