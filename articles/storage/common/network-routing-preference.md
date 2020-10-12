---
title: Configure a preferência de encaminhamento da rede (pré-visualização)
titleSuffix: Azure Storage
description: Configure a preferência de encaminhamento de rede (pré-visualização) para a sua conta de armazenamento Azure para especificar como o tráfego de rede é encaminhado para a sua conta a partir de clientes através da Internet.
services: storage
author: santoshc
ms.service: storage
ms.topic: conceptual
ms.date: 05/12/2020
ms.author: santoshc
ms.reviewer: tamram
ms.subservice: common
ms.openlocfilehash: 5b4a1b1f27dff059090d78e24a6a0eca0bbbf01f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85514201"
---
# <a name="configure-network-routing-preference-for-azure-storage-preview"></a>Configure a preferência de encaminhamento de rede para armazenamento Azure (pré-visualização)

Pode configurar a preferência de [encaminhamento de](../../virtual-network/routing-preference-overview.md) rede (pré-visualização) para a sua conta de armazenamento Azure para especificar como o tráfego de rede é encaminhado para a sua conta a partir de clientes através da Internet. Por padrão, o tráfego da Internet é encaminhado para o ponto final público da sua conta de armazenamento através da rede global da [Microsoft.](../../networking/microsoft-global-network.md) O Azure Storage fornece opções adicionais para configurar como o tráfego é encaminhado para a sua conta de armazenamento.

Configurar a preferência de encaminhamento dá-lhe a flexibilidade para otimizar o seu tráfego, quer para o desempenho da rede premium, quer para o custo. Ao configurar uma preferência de encaminhamento, especifica como o tráfego será direcionado para o ponto final público para a sua conta de armazenamento por padrão. Também pode publicar pontos finais específicos da rota para a sua conta de armazenamento.

## <a name="microsoft-global-network-versus-internet-routing"></a>Rede global da Microsoft versus encaminhamento de Internet

Por padrão, os clientes fora do ambiente Azure acedem à sua conta de armazenamento através da rede global da Microsoft. A rede global da Microsoft está otimizada para a seleção de caminhos de baixa latência para oferecer um desempenho de rede premium com alta fiabilidade. Tanto o tráfego de entrada como de saída são encaminhados através do ponto de presença (POP) que está mais próximo do cliente. Esta configuração de encaminhamento padrão garante que o tráfego de e para a sua conta de armazenamento atravessa a rede global da Microsoft para a maior parte do seu caminho, maximizando o desempenho da rede.

Pode alterar a configuração de encaminhamento para a sua conta de armazenamento de modo a que tanto o tráfego de entrada como de saída seja encaminhado de e para clientes fora do ambiente Azure através do POP mais próximo da conta de armazenamento. Esta rota minimiza a travessia do seu tráfego através da rede global da Microsoft, entregando-a ao ISP de trânsito o mais rapidamente possível. A utilização desta configuração de encaminhamento reduz os custos de rede.

O seguinte diagrama mostra como o tráfego flui entre o cliente e a conta de armazenamento para cada preferência de encaminhamento:

![Visão geral das opções de encaminhamento para o Armazenamento Azure](media/network-routing-preference/routing-options-diagram.png)

Para obter mais informações sobre a preferência de encaminhamento em Azure, consulte [o que é a preferência de encaminhamento (pré-visualização)?](../../virtual-network/routing-preference-overview.md)

## <a name="routing-configuration"></a>Configuração de encaminhamento

Pode escolher entre a rede global da Microsoft e o encaminhamento da Internet como a preferência por padrão de encaminhamento para o ponto final público da sua conta de armazenamento. A preferência por roteamento padrão aplica-se a todo o tráfego de clientes fora de Azure e afeta os pontos finais para Azure Data Lake Storage Gen2, blob storage, Azure Files e websites estáticos. A preferência de configuração do encaminhamento não é suportada para filas Azure ou tabelas Azure.

Também pode publicar pontos finais específicos da rota para a sua conta de armazenamento. Ao publicar pontos finais específicos da rota, o Azure Storage cria novos pontos finais públicos para a sua conta de armazenamento que encaminha o tráfego através do caminho pretendido. Esta flexibilidade permite-lhe direcionar o tráfego para a sua conta de armazenamento numa rota específica sem alterar a sua preferência de encaminhamento padrão.

Por exemplo, a publicação de um ponto final específico da rota da Internet para o 'StorageAccountA' publicará os seguintes pontos finais para a sua conta de armazenamento:

| Serviço de armazenamento        | Ponto final específico da rota                                  |
| :--------------------- | :------------------------------------------------------- |
| Serviço Blob           | `StorageAccountA-internetrouting.blob.core.windows.net`  |
| Armazenamento do Data Lake Ger2 | `StorageAccountA-internetrouting.dfs.core.windows.net`   |
| Serviço de arquivos           | `StorageAccountA-internetrouting.file.core.windows.net`  |
| Websites estáticos        | `StorageAccountA-internetrouting.web.core.windows.net`   |

Se tiver uma conta de armazenamento geo-redundante de acesso de leitura (RA-GRS) ou uma conta de armazenamento redundante de geo-zona de acesso à leitura (RA-GZRS), a publicação de pontos finais específicos da rota também cria automaticamente os pontos finais correspondentes na região secundária para acesso à leitura.

| Serviço de armazenamento        | Ponto final secundário específico da rota                        |
| :--------------------- | :----------------------------------------------------------------- |
| Serviço Blob           | `StorageAccountA-internetrouting-secondary.blob.core.windows.net`  |
| Armazenamento do Data Lake Ger2 | `StorageAccountA-internetrouting-secondary.dfs.core.windows.net`   |
| Serviço de arquivos           | `StorageAccountA-internetrouting-secondary.file.core.windows.net`  |
| Websites estáticos        | `StorageAccountA-internetrouting-secondary.web.core.windows.net`   |

As cadeias de ligação para os pontos finais específicos da rota publicadas podem ser copiadas através do [portal Azure](https://portal.azure.com). Estas cordas de conexão podem ser usadas para a autorização de Chave Partilhada com todos os SDKs e APIs de armazenamento Azure existentes.

## <a name="about-the-preview"></a>Sobre a pré-visualização

A preferência de encaminhamento para o Armazenamento Azure está disponível nas seguintes regiões:

- Sul de França
- E.U.A. Centro-Norte
- E.U.A. Centro-Oeste

Os seguintes problemas conhecidos afetam a pré-visualização da preferência de encaminhamento para o Armazenamento Azure:

- Os pedidos de acesso para o ponto final específico da rota para a rede global da Microsoft falham com o erro HTTP 404 ou equivalente. O encaminhamento sobre a rede global da Microsoft funciona como esperado quando é definido como a preferência por padrão de encaminhamento para o ponto final público.

## <a name="pricing-and-billing"></a>Preços e faturação

Para obter detalhes sobre preços e faturação, consulte a secção **de preços** em Qual é a preferência [de encaminhamento (pré-visualização)?](../../virtual-network/routing-preference-overview.md#pricing)

## <a name="next-steps"></a>Passos seguintes

- [O que é a preferência de encaminhamento (pré-visualização)?](../../virtual-network/routing-preference-overview.md)
- [Configurar as firewalls e as redes virtuais do Armazenamento do Microsoft Azure](storage-network-security.md)
- [Recomendações de segurança para armazenamento blob](../blobs/security-recommendations.md)
