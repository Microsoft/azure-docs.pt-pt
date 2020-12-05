---
title: Monte uma partilha de ficheiros Azure NFS - Ficheiros Azure
description: Saiba como montar uma partilha do Sistema de Ficheiros de Rede.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2020
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 8a993d9c1de35132198de5e3becc4f16d6a2a437
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2020
ms.locfileid: "96621302"
---
# <a name="how-to-mount-an-nfs-file-share"></a>Como montar uma partilha de ficheiros NFS

[Ficheiros do Azure](storage-files-introduction.md) é o sistema de ficheiros na cloud fácil de utilizar da Microsoft. As ações de ficheiros Azure podem ser montadas nas distribuições do Linux utilizando o protocolo do Bloco de Mensagens do Servidor (SMB) ou o protocolo do Sistema de Ficheiros de Rede (NFS). Este artigo está focado na montagem com NFS, para mais detalhes sobre montagem com SMB, ver [Use Azure Files com Linux](storage-how-to-use-files-linux.md). Para mais informações sobre cada um dos protocolos disponíveis, consulte os [protocolos de partilha de ficheiros Azure](storage-files-compare-protocols.md).

## <a name="limitations"></a>Limitações

[!INCLUDE [files-nfs-limitations](../../../includes/files-nfs-limitations.md)]

### <a name="regional-availability"></a>Disponibilidade regional

[!INCLUDE [files-nfs-regional-availability](../../../includes/files-nfs-regional-availability.md)]

## <a name="prerequisites"></a>Pré-requisitos

- [Criar uma participação NFS](storage-files-how-to-create-nfs-shares.md).

    > [!IMPORTANT]
    > As ações da NFS só podem ser acedidas a partir de redes fidedignas. As ligações à sua quota NFS devem ter origem numa das seguintes fontes:

- Utilize uma das seguintes soluções de rede:
    - Ou [cria um ponto final privado](storage-files-networking-endpoints.md#create-a-private-endpoint) (recomendado) ou restringe o acesso ao seu ponto final [público.](storage-files-networking-endpoints.md#restrict-public-endpoint-access)
    - [Configure uma VPN ponto-a-local (P2S) no Linux para utilização com ficheiros Azure](storage-files-configure-p2s-vpn-linux.md).
    - [Configure uma VPN site-to-site para utilização com ficheiros Azure](storage-files-configure-s2s-vpn.md).
    - Configurar [ExpressRoute](../../expressroute/expressroute-introduction.md).

## <a name="disable-secure-transfer"></a>Desativar transferência segura

1. Inscreva-se no portal Azure e aceda à conta de armazenamento que contém a partilha NFS que criou.
1. Selecione **Configuração**.
1. Selecione **Desativado** para **transferência segura necessária**.
1. Selecione **Guardar**.

    :::image type="content" source="media/storage-files-how-to-mount-nfs-shares/storage-account-disable-secure-transfer.png" alt-text="Screenshot do ecrã de configuração da conta de armazenamento com transferência segura desativada.":::

## <a name="mount-an-nfs-share"></a>Monte uma quota NFS

1. Assim que a partilha de ficheiros for criada, selecione a partilha e selecione **Connect from Linux**.
1. Introduza o caminho de montagem que gostaria de usar e, em seguida, copie o script.
1. Ligue-se ao seu cliente e utilize o script de montagem fornecido.

    :::image type="content" source="media/storage-files-how-to-create-mount-nfs-shares/mount-nfs-file-share-script.png" alt-text="Screenshot da lâmina de ligação de partilha de ficheiros":::

Já montou a sua parte da NFS.

### <a name="validate-connectivity"></a>Validar conectividade

Se a montagem falhou, é possível que o seu ponto final privado não tenha sido configurado corretamente ou esteja inacessível. Para obter mais informações sobre a confirmação da conectividade, consulte a secção de [conectividade Verificar](storage-files-networking-endpoints.md#verify-connectivity) o artigo pontos finais de rede.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre a Azure Files com o nosso artigo, [Planejando para uma implementação de Ficheiros Azure](storage-files-planning.md).
- Se tiver algum problema, consulte [as ações de ficheiros Troubleshoot Azure NFS](storage-troubleshooting-files-nfs.md).