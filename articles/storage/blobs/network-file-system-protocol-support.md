---
title: Suporte do Sistema de Ficheiros de Rede 3.0 no armazenamento Azure Blob (pré-visualização) Microsoft Docs
description: O armazenamento blob suporta agora o protocolo Sistema de Ficheiros de Rede (NFS) 3.0. Este suporte permite que os clientes Windows e Linux montem um recipiente no armazenamento Blob a partir de uma Máquina Virtual Azure (VM) ou um computador que funciona no local.
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 08/04/2020
ms.author: normesta
ms.reviewer: yzheng
ms.custom: references_regions
ms.openlocfilehash: 41ad20de6b7a800ff1f97a7ff371c8e0012fed27
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88166982"
---
# <a name="network-file-system-nfs-30-protocol-support-in-azure-blob-storage-preview"></a>Suporte ao protocolo do Sistema de Ficheiros de Rede (NFS) 3.0 no armazenamento do Azure Blob (pré-visualização)

O armazenamento blob suporta agora o protocolo Sistema de Ficheiros de Rede (NFS) 3.0. Este suporte permite que os clientes Windows ou Linux montem um recipiente no armazenamento Blob a partir de uma Máquina Virtual Azure (VM) ou um computador no local. 

> [!NOTE]
> O apoio ao protocolo NFS 3.0 no armazenamento de Azure Blob está em pré-visualização pública e está disponível nas seguintes regiões: EUA Leste, EUA Central, Eua West Central, Austrália Sudeste, Europa do Norte, Reino Unido Oeste, Coreia Central, Coreia do Sul e Canadá Central.

## <a name="general-workflow-mounting-a-storage-account-container"></a>Fluxo de trabalho geral: Montagem de um recipiente de conta de armazenamento

Para montar um contentor de conta de armazenamento, terá que fazer estas coisas.

1. Registe a função de protocolo NFS 3.0 com a sua assinatura.

2. Verifique se a funcionalidade está registada.

3. Criar uma Rede Virtual Azure (VNet).

4. Configure a segurança da rede.

5. Crie e configuure a conta de armazenamento que aceita o tráfego apenas a partir do VNet.

6. Criar um recipiente na conta de armazenamento.

7. Monte o contentor.

Para obter orientação passo a passo, consulte [o armazenamento do Mount Blob utilizando o protocolo Network File System (NFS) 3.0 (pré-visualização)](network-file-system-protocol-support-how-to.md).

> [!IMPORTANT]
> É importante completar estas tarefas em ordem. Não é possível montar recipientes que crie antes de ativar o protocolo NFS 3.0 na sua conta. Além disso, depois de ter ativado o protocolo NFS 3.0 na sua conta, não pode desativá-lo.

## <a name="network-security"></a>Segurança da rede

A sua conta de armazenamento deve estar contida num VNet. Um VNet permite que os clientes se conectem de forma segura à sua conta de armazenamento. A única forma de proteger os dados na sua conta é utilizando um VNet e outras definições de segurança de rede. Qualquer outra ferramenta utilizada para proteger dados, incluindo a autorização da chave de conta, a segurança do Azure Ative Directory (AD) e as listas de controlo de acesso (ACLs) ainda não são suportadas em contas que tenham o suporte do protocolo NFS 3.0 ativado nos mesmos. 

Para saber mais, consulte [as recomendações de segurança da Rede para armazenamento blob](security-recommendations.md#networking).

## <a name="supported-network-connections"></a>Ligações de rede suportadas

Um cliente pode ligar-se através de um ponto final público ou [privado,](../common/storage-private-endpoints.md)e pode ligar-se a partir de qualquer um dos seguintes locais de rede:

- O VNet que configura para a sua conta de armazenamento. 

  Para efeitos deste artigo, vamos referir-nos ao VNet como o *VNet primário.* Para saber mais, consulte [o Acesso grant a partir de uma rede virtual.](../common/storage-network-security.md#grant-access-from-a-virtual-network)

- Um VNet espreitado que está na mesma região que o VNet primário.

  Terá de configurar a sua conta de armazenamento para permitir o acesso a este VNet. Para saber mais, consulte [o Acesso grant a partir de uma rede virtual.](../common/storage-network-security.md#grant-access-from-a-virtual-network)

- Uma rede no local que está ligada ao seu VNet primário utilizando [o Gateway VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) ou um [gateway ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-portal-resource-manager). 

  Para saber mais, consulte [configurar o acesso a partir de redes no local.](../common/storage-network-security.md#configuring-access-from-on-premises-networks)

- Uma rede no local que está ligada a uma rede de olhos.

  Isto pode ser feito utilizando [o Gateway VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) ou um [gateway ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-portal-resource-manager) juntamente com [o trânsito gateway.](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/vnet-peering#gateway-transit) 

> [!IMPORTANT]
> Se estiver a ligar-se a partir de uma rede no local, certifique-se de que o seu cliente permite a comunicação de saída através das portas 111 e 2048. O protocolo NFS 3.0 utiliza estas portas.

## <a name="azure-storage-features-not-yet-supported"></a>Funcionalidades de Armazenamento Azure ainda não suportadas

As seguintes funcionalidades de Armazenamento Azure não são suportadas quando ativa o protocolo NFS 3.0 na sua conta. 

- Segurança do Diretório Ativo Azure (AD)

- Listas de controlo de acesso semelhantes a POSIX (ACLs)

- A capacidade de permitir o suporte NFS 3.0 nas contas de armazenamento existentes

- A capacidade de desativar o suporte NFS 3.0 numa conta de armazenamento (depois de o ter ativado)

- Capacidade de escrever para bolhas utilizando APIs ou SDKs rest. 
  
## <a name="nfs-30-features-not-yet-supported"></a>Funcionalidades NFS 3.0 ainda não suportadas

As seguintes funcionalidades NFS 3.0 ainda não são suportadas com a Azure Data Lake Storage Gen2.

- NFS 3.0 sobre UDP. Apenas NFS 3.0 sobre TCP é suportado.

- Bloquear ficheiros com o Network Lock Manager (NLM). Os comandos de montagem devem incluir o `-o nolock` parâmetro.

- Montagem de sub-directórios. Só é possível montar o diretório de raiz (Recipiente).

- Suportes de listagem (por exemplo: utilizando o `showmount -a` comando)

- Listagem de exportações (por exemplo: utilizando o `showmount -e` comando)

- Exportação de um recipiente apenas para leitura

## <a name="pricing"></a>Preços

Durante a pré-visualização, os dados armazenados na sua conta de armazenamento são faturados à mesma taxa de capacidade que os encargos de armazenamento por GB por mês. 

Uma transação não é cobrada durante a pré-visualização. Os preços das transações estão sujeitos a alterações e serão determinados quando estiverem geralmente disponíveis.

## <a name="next-steps"></a>Passos seguintes

Para começar, consulte [o armazenamento do Mount Blob utilizando o protocolo Network File System (NFS) 3.0 (pré-visualização)](network-file-system-protocol-support-how-to.md).





