---
title: Configure a preferência de encaminhamento da rede (pré-visualização)
titleSuffix: Azure Storage
description: Configure a preferência de encaminhamento de rede (pré-visualização) para a sua conta de armazenamento Azure para especificar como o tráfego de rede é encaminhado para a sua conta a partir de clientes através da Internet.
services: storage
author: santoshc
ms.service: storage
ms.topic: article
ms.date: 05/12/2020
ms.author: santoshc
ms.reviewer: tamram
ms.subservice: common
ms.openlocfilehash: bdb33ebfb1ca37772a5b0db96acdbddd422578af
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83597822"
---
# <a name="configure-network-routing-preference-for-azure-storage-preview"></a>Configure a preferência de encaminhamento de rede para armazenamento azure (pré-visualização)

Pode configurar a preferência de [encaminhamento](../../virtual-network/routing-preference-overview.md) de rede (pré-visualização) para a sua conta de armazenamento Azure para especificar como o tráfego de rede é encaminhado para a sua conta a partir de clientes através da Internet. Por padrão, o tráfego da Internet é encaminhado para o ponto final público da sua conta de armazenamento através da [rede global](../../networking/microsoft-global-network.md)da Microsoft . O Azure Storage oferece opções adicionais para configurar a forma como o tráfego é encaminhado para a sua conta de armazenamento.

Configurar a preferência de encaminhamento dá-lhe a flexibilidade para otimizar o seu tráfego, quer para desempenho de rede premium, quer para custos. Quando configurar uma preferência de encaminhamento, especifice como o tráfego será direcionado para o ponto final público para a sua conta de armazenamento por padrão. Também pode publicar pontos finais específicos da rota para a sua conta de armazenamento.

## <a name="microsoft-global-network-versus-internet-routing"></a>Rede global da Microsoft versus encaminhamento da Internet

Por padrão, os clientes fora do ambiente Azure acedem à sua conta de armazenamento através da rede global da Microsoft. A rede global da Microsoft está otimizada para a seleção de caminhos de baixa latência para oferecer um desempenho de rede premium com elevada fiabilidade. Tanto o tráfego de entrada como de saída são encaminhados através do ponto de presença (POP) que é mais próximo do cliente. Esta configuração de encaminhamento predefinido garante que o tráfego de e para a sua conta de armazenamento atravessa a rede global da Microsoft para a maior parte do seu percurso, maximizando o desempenho da rede.

Pode alterar a configuração de encaminhamento para a sua conta de armazenamento para que o tráfego de entrada e saída seja encaminhado de e para clientes fora do ambiente Azure através do POP mais próximo da conta de armazenamento. Esta rota minimiza a travessia do seu tráfego através da rede global da Microsoft, entregando-a ao ISP de trânsito o mais rapidamente possível. A utilização desta configuração de encaminhamento reduz os custos de rede.

O diagrama seguinte mostra como o tráfego flui entre o cliente e a conta de armazenamento para cada preferência de encaminhamento:

![Visão geral das opções de encaminhamento para armazenamento azure](media/network-routing-preference/routing-options-diagram.png)

Para obter mais informações sobre a preferência de encaminhamento em Azure, consulte [o que é a preferência de encaminhamento (pré-visualização)?](../../virtual-network/routing-preference-overview.md)

## <a name="routing-configuration"></a>Configuração de encaminhamento

Pode escolher entre a rede global da Microsoft e o encaminhamento da Internet como a preferência padrão de encaminhamento para o ponto final público da sua conta de armazenamento. A preferência por encaminhamento padrão aplica-se a todo o tráfego de clientes fora do Azure e afeta os pontos finais para o Azure Data Lake Storage Gen2, Blob storage, Azure Files e websites estáticos. A configuração da preferência de encaminhamento não é suportada para filas azure ou mesas azure.

Também pode publicar pontos finais específicos da rota para a sua conta de armazenamento. Ao publicar pontos finais específicos da rota, o Azure Storage cria novos pontos finais públicos para a sua conta de armazenamento que encaminham o tráfego sobre o caminho pretendido. Esta flexibilidade permite-lhe direcionar o tráfego para a sua conta de armazenamento numa rota específica sem alterar a sua preferência de encaminhamento predefinido.

Por exemplo, a publicação de um ponto final específico da rota da Internet para o 'StorageAccountA' publicará os seguintes pontos finais para a sua conta de armazenamento:

| Serviço de armazenamento        | Ponto final específico da rota                                  |
| :--------------------- | :------------------------------------------------------- |
| Serviço Blob           | `StorageAccountA-internetrouting.blob.core.windows.net`  |
| Data Lake Storage Gen2 | `StorageAccountA-internetrouting.dfs.core.windows.net`   |
| Serviço de ficheiros           | `StorageAccountA-internetrouting.file.core.windows.net`  |
| Sites estáticos        | `StorageAccountA-internetrouting.web.core.windows.net`   |

Se tiver uma conta de armazenamento geo-redundante de acesso de leitura (RA-GRS) ou uma conta de armazenamento de armazenamento geo-zona-redundante de acesso a leitura (RA-GZRS), a publicação de pontos finais específicos da rota também cria automaticamente os pontos finais correspondentes na região secundária para acesso a leitura.

| Serviço de armazenamento        | Ponto final secundário específico da rota                        |
| :--------------------- | :----------------------------------------------------------------- |
| Serviço Blob           | `StorageAccountA-internetrouting-secondary.blob.core.windows.net`  |
| Data Lake Storage Gen2 | `StorageAccountA-internetrouting-secondary.dfs.core.windows.net`   |
| Serviço de ficheiros           | `StorageAccountA-internetrouting-secondary.file.core.windows.net`  |
| Sites estáticos        | `StorageAccountA-internetrouting-secondary.web.core.windows.net`   |

As cordas de ligação para os pontos finais específicos da rota publicados podem ser copiadas através do [portal Azure](https://portal.azure.com). Estas cordas de ligação podem ser utilizadas para autorização de chave partilhada com todos os SDKs e APIs de Armazenamento Azure existentes.

## <a name="about-the-preview"></a>Sobre a pré-visualização

A preferência de encaminhamento para o Armazenamento Azure está disponível nas seguintes regiões:

- Sul de França
- E.U.A. Centro-Norte
- E.U.A. Centro-Oeste

As seguintes questões conhecidas afetam a pré-visualização da preferência de encaminhamento para o Armazenamento Azure:

- Os pedidos de acesso para o ponto final específico da rota para a rede global da Microsoft falham com erro HTTP 404 ou equivalente. O encaminhamento sobre a rede global da Microsoft funciona como esperado quando é definido como a preferência padrão de encaminhamento para o ponto final público.

## <a name="pricing-and-billing"></a>Preços e faturação

Para obter preços e dados de faturação, consulte a secção **de preços** em Que é a preferência [de encaminhamento (pré-visualização)?](../../virtual-network/routing-preference-overview.md#pricing)

## <a name="next-steps"></a>Passos seguintes

- [O que é a preferência de encaminhamento (pré-visualização)?](../../virtual-network/routing-preference-overview.md)
- [Configure firewalls de armazenamento Azure e redes virtuais](storage-network-security.md)
- [Recomendações de segurança para armazenamento blob](../blobs/security-recommendations.md)
