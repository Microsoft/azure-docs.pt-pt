---
title: Configure uma firewall IP para o seu serviço de Pesquisa Cognitiva Azure
titleSuffix: Azure Cognitive Search
description: Configure as políticas de controlo IP para restringir o acesso ao seu serviço de Pesquisa Cognitiva Azure.
manager: nitinme
author: mrcarter8
ms.author: mcarter
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: 98a516e76d5e4f5c225873b934949759d7fcba8d
ms.sourcegitcommit: b513b0becf878eb9a1554c26da53aa48d580bb22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/16/2021
ms.locfileid: "100535336"
---
# <a name="configure-ip-firewall-for-azure-cognitive-search"></a>Configure firewall IP para pesquisa cognitiva Azure

A Azure Cognitive Search suporta regras IP para suporte a firewall de entrada. Este modelo fornece uma camada adicional de segurança para o seu serviço de pesquisa semelhante às regras IP que encontrará num grupo de segurança de rede virtual Azure. Com estas regras de IP, pode configurar o seu serviço de pesquisa para ser acessível apenas a partir de um conjunto aprovado de máquinas e/ou serviços na nuvem. O acesso aos dados armazenados no seu serviço de pesquisa a partir destes conjuntos de máquinas e serviços aprovados ainda exigirá que o chamador apresente um token de autorização válido.

Pode definir as regras de IP no portal Azure, conforme descrito neste artigo. Em alternativa, pode utilizar a [versão API Management REST 2020-03-13](/rest/api/searchmanagement/), [Azure PowerShell](/powershell/module/az.search)ou [Azure CLI](/cli/azure/search).

## <a name="configure-an-ip-firewall-using-the-azure-portal"></a><a id="configure-ip-policy"></a> Configure uma firewall IP utilizando o portal Azure

Para definir a política de controlo de acesso IP no portal Azure, aceda à sua página de serviço de Pesquisa Cognitiva Azure e selecione **Networking** no menu de navegação. A conectividade de rede de pontos finais deve ser **pública.** Se a sua conectividade estiver definida para **Private,** só pode aceder ao seu serviço de pesquisa através de um Ponto Final Privado.

![Screenshot mostrando como configurar a firewall IP no portal Azure](./media/service-configure-firewall/azure-portal-firewall.png)

O portal Azure fornece a capacidade de especificar endereços IP e intervalos de endereços IP no formato CIDR. Um exemplo de notação CIDR é 8.8.8.0/24, que representa os IPs que variam de 8.8.8.0 a 8.8.8.255.

> [!NOTE]
> Depois de ativar a política de controlo de acesso IP para o seu serviço de Pesquisa Cognitiva Azure, todos os pedidos para o plano de dados de máquinas fora da lista permitida de intervalos de endereços IP são rejeitados. Quando as regras ip são configuradas, algumas características do portal Azure são desativadas. Poderá ver e gerir informações de nível de serviço, mas o acesso do portal aos dados de índice e aos vários componentes do serviço, como as definições de índice, indexador e skillset, é restringido por razões de segurança. Como alternativa ao portal, pode utilizar a [Extensão](https://aka.ms/vscode-search) do Código VS para interagir com os vários componentes do serviço.

### <a name="requests-from-your-current-ip"></a>Pedidos do seu IP atual

Para simplificar o desenvolvimento, o portal Azure ajuda-o a identificar e adicionar o IP da sua máquina cliente à lista permitida. As aplicações em execução na sua máquina podem então aceder ao seu serviço de Pesquisa Cognitiva Azure.

O portal deteta automaticamente o endereço IP do seu cliente. Pode ser o endereço IP do cliente da sua máquina ou gateway de rede. Certifique-se de remover este endereço IP antes de levar a sua carga de trabalho para a produção.

Para adicionar o seu IP atual à lista de IPs, consulte **adicionar o endereço IP do seu cliente**. Em seguida, selecione **Guardar**.

![Screenshot mostrando como configurar as definições de firewall IP para permitir o IP atual](./media/service-configure-firewall/enable-current-ip.png)

## <a name="troubleshoot-issues-with-an-ip-access-control-policy"></a><a id="troubleshoot-ip-firewall"></a>Problemas de resolução de problemas com uma política de controlo de acesso ip

Pode resolver problemas com uma política de controlo de acesso IP utilizando as seguintes opções:

### <a name="azure-portal"></a>Portal do Azure

Ativar uma política de controlo de acesso IP para o seu serviço de Pesquisa Cognitiva Azure bloqueia todos os pedidos de máquinas fora da lista permitida de intervalos de endereços IP, incluindo o portal Azure.  Poderá ver e gerir informações de nível de serviço, mas o acesso do portal aos dados de índice e aos vários componentes do serviço, como as definições de índice, indexador e skillset, é restringido por razões de segurança. 

### <a name="sdks"></a>SDKs

Quando acede ao serviço de Pesquisa Cognitiva Azure utilizando o SDK a partir de máquinas que não estão na lista permitida, uma resposta genérica **403 Proibida** é devolvida sem detalhes adicionais. Verifique a lista IP permitida para a sua conta e certifique-se de que a configuração correta é atualizada para o seu serviço de pesquisa.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o acesso ao seu serviço de pesquisa através do Private Link, consulte o seguinte artigo:

* [Criar um ponto final privado para uma ligação segura à Pesquisa Cognitiva Azure](service-create-private-endpoint.md)