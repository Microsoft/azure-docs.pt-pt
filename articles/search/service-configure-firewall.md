---
title: Configure uma firewall IP para o seu serviço de Pesquisa Cognitiva Azure
titleSuffix: Azure Cognitive Search
description: Configure as políticas de controlo IP para restringir o acesso ao seu serviço de Pesquisa Cognitiva Azure.
manager: nitinme
author: mrcarter8
ms.author: mcarter
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/11/2020
ms.openlocfilehash: 699715e1188616c2d6bda47016ec1ea7b05cef83
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83125601"
---
# <a name="configure-ip-firewall-for-azure-cognitive-search"></a>Configure firewall IP para pesquisa cognitiva azure

A Pesquisa Cognitiva Azure suporta as regras de IP para suporte à firewall de entrada. Este modelo fornece uma camada adicional de segurança para o seu serviço de pesquisa semelhante às regras IP que encontrará num grupo de segurança de rede virtual Azure. Com estas regras ip, pode configurar o seu serviço de pesquisa para estar acessível apenas a partir de um conjunto aprovado de máquinas e/ou serviços na nuvem. O acesso aos dados armazenados no seu serviço de pesquisa a partir destes conjuntos aprovados de máquinas e serviços ainda exigirá que o chamador apresente um sinal de autorização válido.

> [!Important]
> As regras IP do seu serviço de Pesquisa Cognitiva Azure podem ser configuradas utilizando o portal Azure ou a versão API do Rest de [Gestão 2020-03-13](https://docs.microsoft.com/rest/api/searchmanagement/).

## <a name="configure-an-ip-firewall-using-the-azure-portal"></a><a id="configure-ip-policy"></a>Configure uma firewall IP utilizando o portal Azure

Para definir a política de controlo de acesso IP no portal Azure, aceda à página de serviço de Pesquisa Cognitiva Azure e selecione **Networking** no menu de navegação. A conectividade de rede endpoint deve ser **pública.** Se a sua conectividade estiver definida para **Privado,** só pode aceder ao seu serviço de pesquisa através de um Ponto Final Privado.

![Screenshot mostrando como configurar a firewall IP no portal Azure](./media/service-configure-firewall/azure-portal-firewall.png)

O portal Azure fornece a capacidade de especificar endereços IP e intervalos de endereços IP no formato CIDR. Um exemplo de notação CIDR é 8.8.8.0/24, o que representa os IPs que variam entre 8.8.8.0 e 8.8.8.255.

> [!NOTE]
> Depois de ativar a política de controlo de acesso IP para o seu serviço de Pesquisa Cognitiva Azure, todos os pedidos para o plano de dados de máquinas fora da lista permitida de intervalos de endereços IP são rejeitados. Quando as regras ip são configuradas, algumas características do portal Azure são desativadas. Poderá visualizar e gerir informações de nível de serviço, mas o acesso ao portal aos dados de índice e aos vários componentes do serviço, tais como as definições de indexador, indexador e skillset, é restrito por razões de segurança.

### <a name="requests-from-your-current-ip"></a>Pedidos do seu IP atual

Para simplificar o desenvolvimento, o portal Azure ajuda-o a identificar e adicionar o IP da sua máquina cliente à lista permitida. As aplicações que estão a funcionar na sua máquina podem então aceder ao seu serviço de Pesquisa Cognitiva Azure.

O portal deteta automaticamente o endereço IP do seu cliente. Pode ser o endereço IP do cliente da sua máquina ou gateway de rede. Certifique-se de remover este endereço IP antes de levar a sua carga de trabalho à produção.

Para adicionar o seu IP atual à lista de IPs, consulte **adicionar o endereço IP do seu cliente**. Em seguida, selecione **Guardar**.

![Screenshot mostrando como configurar definições de firewall IP para permitir o IP atual](./media/service-configure-firewall/enable-current-ip.png)

## <a name="troubleshoot-issues-with-an-ip-access-control-policy"></a><a id="troubleshoot-ip-firewall"></a>Problemas de resolução de problemas com uma política de controlo de acesso ip

Pode resolver problemas com uma política de controlo de acesso IP utilizando as seguintes opções:

### <a name="azure-portal"></a>Portal do Azure

Ativar uma política de controlo de acesso IP para o seu serviço de Pesquisa Cognitiva Azure bloqueia todos os pedidos de máquinas fora da lista permitida de intervalos de endereços IP, incluindo o portal Azure.  Poderá visualizar e gerir informações de nível de serviço, mas o acesso ao portal aos dados de índice e aos vários componentes do serviço, tais como as definições de indexador, indexador e skillset, é restrito por razões de segurança. 

### <a name="sdks"></a>SDKs

Quando acede ao serviço de Pesquisa Cognitiva Azure utilizando o SDK a partir de máquinas que não estão na lista permitida, uma resposta genérica **403 Proibida** é devolvida sem detalhes adicionais. Verifique a lista ip permitida para a sua conta e certifique-se de que a configuração correta foi atualizada para o seu serviço de pesquisa.

## <a name="next-steps"></a>Passos seguintes

Para mais informações sobre o acesso ao seu serviço de pesquisa via Private Link, consulte o seguinte artigo:

* [Crie um ponto final privado para uma ligação segura à Pesquisa Cognitiva Azure](service-create-private-endpoint.md)
