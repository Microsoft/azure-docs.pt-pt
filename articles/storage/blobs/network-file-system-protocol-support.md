---
title: Suporte do Sistema de Ficheiros de Rede 3.0 no armazenamento de Azure Blob (pré-visualização) | Microsoft Docs
description: O armazenamento blob suporta agora o protocolo Sistema de Ficheiros de Rede (NFS) 3.0. Este suporte permite que os clientes do Linux montem um recipiente no armazenamento Blob a partir de uma Máquina Virtual Azure (VM) ou um computador que funciona no local.
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 02/19/2021
ms.author: normesta
ms.reviewer: yzheng
ms.custom: references_regions
ms.openlocfilehash: b00956a6fb5a79b09602ca1752cc547595f32db6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103224599"
---
# <a name="network-file-system-nfs-30-protocol-support-in-azure-blob-storage-preview"></a>Suporte ao protocolo do Sistema de Ficheiros de Rede (NFS) 3.0 no armazenamento do Azure Blob (pré-visualização)

O armazenamento blob suporta agora o protocolo Sistema de Ficheiros de Rede (NFS) 3.0. Este suporte proporciona compatibilidade do sistema de ficheiros Linux à escala e preços de armazenamento de objetos e permite que os clientes do Linux montem um recipiente no armazenamento Blob a partir de uma Máquina Virtual Azure (VM) ou um computador no local. 

> [!NOTE]
> O suporte do protocolo NFS 3.0 no armazenamento Azure Blob está em pré-visualização pública. Suporta contas de armazenamento GPV2 com desempenho de nível padrão nas seguintes regiões: Austrália Leste, Coreia Central e South Central EUA. A pré-visualização também suporta blob de bloco com nível de desempenho premium em todas as regiões públicas.

Sempre foi um desafio executar cargas de trabalho antigas em larga escala, como High Performance Computing (HPC) na nuvem. Uma das razões é que as aplicações usam frequentemente protocolos de ficheiros tradicionais, como NFS ou Server Message Block (SMB) para aceder a dados. Além disso, os serviços nativos de armazenamento em nuvem focaram-se no armazenamento de objetos que têm um espaço de nome plano e metadados extensos em vez de sistemas de ficheiros que fornecem um espaço hierárquico de nomes e operações eficientes de metadados. 

Blob Storage agora suporta um espaço hierárquico de nomes, e quando combinado com suporte ao protocolo NFS 3.0, Azure torna muito mais fácil executar aplicações antigas em cima do armazenamento de objetos em nuvem em larga escala. 

## <a name="applications-and-workloads-suited-for-this-feature"></a>Aplicações e cargas de trabalho adequadas para esta funcionalidade

A funcionalidade de protocolo NFS 3.0 é mais adequada para o processamento de alta produção, alta escala, ler cargas de trabalho pesadas como processamento de mídia, simulações de risco e sequenciação genómica. Deve considerar a utilização desta funcionalidade para qualquer outro tipo de carga de trabalho que utilize vários leitores e muitos fios, que requerem alta largura de banda. 

## <a name="nfs-30-and-the-hierarchical-namespace"></a>NFS 3.0 e o espaço hierárquico de nomes

O suporte ao protocolo NFS 3.0 requer que as bolhas sejam organizadas num espaço hierárquico. Pode ativar um espaço hierárquico quando criar uma conta de armazenamento. A capacidade de usar um espaço hierárquico foi introduzida pela Azure Data Lake Storage Gen2. Organiza objetos (ficheiros) numa hierarquia de diretórios e subdiretórios da mesma forma que o sistema de ficheiros no seu computador está organizado.  O espaço hierárquico de nomes escala linearmente e não degrada a capacidade ou desempenho dos dados. Protocolos diferentes estendem-se a partir do espaço hierárquico. O protocolo NFS 3.0 é um destes protocolos disponíveis.   

> [!div class="mx-imgBorder"]
> ![espaço de nome hierárquico](./media/network-protocol-support/hierarchical-namespace-and-nfs-support.png)
  
## <a name="data-stored-as-block-blobs"></a>Dados armazenados como bolhas de bloco

Se ativar o suporte ao protocolo NFS 3.0, todos os dados da sua conta de armazenamento serão armazenados como blobs de bloco. As bolhas de bloco são otimizadas para processar eficientemente grandes quantidades de dados pesados de leitura. As bolhas de bloco são compostas por blocos. Cada bloco é identificado por uma identificação de bloco. Uma bolha de bloco pode incluir até 50.000 blocos. Cada bloco numa bolha de bloco pode ter um tamanho diferente, até ao tamanho máximo permitido para a versão de serviço que a sua conta utiliza.

Quando a sua aplicação faz um pedido utilizando o protocolo NFS 3.0, esse pedido é traduzido em combinação de operações de blob de bloco. Por exemplo, os pedidos de chamada de procedimento remoto (RPC) NFS 3.0 são traduzidos para a operação [Get Blob.](/rest/api/storageservices/get-blob) NFS 3.0 escrever pedidos de RPC são traduzidos numa combinação de [Get Block List](/rest/api/storageservices/get-block-list), Put [Block](/rest/api/storageservices/put-block), e Put [Block List](/rest/api/storageservices/put-block-list).

## <a name="general-workflow-mounting-a-storage-account-container"></a>Fluxo de trabalho geral: Montagem de um recipiente de conta de armazenamento

Os seus clientes Linux podem montar um contentor no armazenamento Blob a partir de uma Máquina Virtual Azure (VM) ou de um computador no local. Para montar um contentor de conta de armazenamento, terá que fazer estas coisas.

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

  Neste artigo, referimo-nos ao VNet como o *VNet primário.* Para saber mais, consulte [o Acesso grant a partir de uma rede virtual.](../common/storage-network-security.md#grant-access-from-a-virtual-network)

- Um VNet espreitado que está na mesma região que o VNet primário.

  Terá de configurar a sua conta de armazenamento para permitir o acesso a este VNet. Para saber mais, consulte [o Acesso grant a partir de uma rede virtual.](../common/storage-network-security.md#grant-access-from-a-virtual-network)

- Uma rede no local que está ligada ao seu VNet primário utilizando [o Gateway VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md) ou um [gateway ExpressRoute](../../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md). 

  Para saber mais, consulte [configurar o acesso a partir de redes no local.](../common/storage-network-security.md#configuring-access-from-on-premises-networks)

- Uma rede no local que está ligada a uma rede de olhos.

  Isto pode ser feito utilizando [o Gateway VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md) ou um [gateway ExpressRoute](../../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md) juntamente com [o trânsito gateway.](/azure/architecture/reference-architectures/hybrid-networking/vnet-peering#gateway-transit) 

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

As seguintes funcionalidades NFS 3.0 ainda não estão suportadas.

- NFS 3.0 sobre UDP. Apenas NFS 3.0 sobre TCP é suportado.

- Bloquear ficheiros com o Network Lock Manager (NLM). Os comandos de montagem devem incluir o `-o nolock` parâmetro.

- Subdiretivas de montagem. Só é possível montar o diretório de raiz (Recipiente).

- Suportes de listagem (por exemplo: utilizando o `showmount -a` comando)

- Listagem de exportações (por exemplo: utilizando o `showmount -e` comando)

- Ligação dura

- Exportação de um recipiente apenas para leitura

## <a name="nfs-30-clients-not-yet-supported"></a>Clientes NFS 3.0 ainda não suportados

Os seguintes clientes NFS 3.0 ainda não estão apoiados.

- Cliente windows para NFS

## <a name="pricing"></a>Preços

Durante a pré-visualização, os dados armazenados na sua conta de armazenamento são faturados à mesma taxa de capacidade que os encargos de armazenamento por GB por mês. 

Uma transação não é cobrada durante a pré-visualização. Os preços das transações estão sujeitos a alterações e serão determinados quando estiverem geralmente disponíveis.

## <a name="next-steps"></a>Passos seguintes

- Para começar, consulte [o armazenamento do Mount Blob utilizando o protocolo Network File System (NFS) 3.0 (pré-visualização)](network-file-system-protocol-support-how-to.md).

- Para otimizar o desempenho, consulte [o Sistema de Ficheiros de Rede (NFS) 3.0 considerações de desempenho no armazenamento Azure Blob (pré-visualização)](network-file-system-protocol-support-performance.md).
