---
title: Delegar um subdomínio-DNS do Azure
description: Com este roteiro de aprendizagem, comece a delegar um subdomínio de DNS do Azure.
services: dns
author: asudbring
ms.service: dns
ms.topic: article
ms.date: 2/7/2019
ms.author: allensu
ms.openlocfilehash: 462282e9674e7a253f61c96338b54174c80fb03f
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74212382"
---
# <a name="delegate-an-azure-dns-subdomain"></a>Delegar um subdomínio DNS do Azure

Você pode usar o portal do Azure para delegar um subdomínio DNS. Por exemplo, se você possui o domínio contoso.com, você pode delegar um subdomínio chamado *Engineering* para outro, zona separada que você pode administrar separadamente da zona contoso.com.

Se preferir, você pode delegar um subdomínio usando [Azure PowerShell](delegate-subdomain-ps.md).

## <a name="prerequisites"></a>Pré-requisitos

Para delegar um subdomínio DNS do Azure, primeiro você deve delegar seu domínio público ao DNS do Azure. Consulte [delegar um domínio ao DNS do Azure](./dns-delegate-domain-azure-dns.md) para obter instruções sobre como configurar seus servidores de nomes para delegação. Depois que o domínio for delegado à zona DNS do Azure, você poderá delegar seu subdomínio.

> [!NOTE]
> Contoso.com é usado como um exemplo neste artigo. Substitua o seu nome de domínio por contoso.com.

## <a name="create-a-zone-for-your-subdomain"></a>Criar uma zona para seu subdomínio

Primeiro, crie a zona para o subdomínio de **engenharia** .

1. No portal do Azure, selecione **criar um recurso**.
2. Na caixa de pesquisa, digite **DNS**e selecione **zona DNS**.
3. Selecione **Criar**.
4. No painel **criar zona DNS** , digite **Engineering.contoso.com** na caixa de texto **nome** .
5. Selecione o grupo de recursos para a zona. Talvez você queira usar o mesmo grupo de recursos que a zona pai para manter os recursos semelhantes juntos.
6. Clique em **Criar**.
7. Depois que a implantação for realizada com sucesso, vá para a nova zona.

## <a name="note-the-name-servers"></a>Observe os servidores de nomes

Em seguida, observe os quatro servidores de nomes para o subdomínio de engenharia.

No painel zona de **engenharia** , observe os quatro servidores de nomes da zona. Você usará esses servidores de nomes mais tarde.

## <a name="create-a-test-record"></a>Criar um registro de teste

Crie um **registro a a ser** usado para teste. Por exemplo, crie um registro da **www** a e configure-o com um endereço IP **10.10.10.10** .

## <a name="create-an-ns-record"></a>Criar um registro NS

Em seguida, crie um registro de servidor de nomes (NS) para a zona de **engenharia** .

1. Navegue até a zona do domínio pai.
2. Selecione **+ Conjunto de registos**.
3. No painel **Adicionar conjunto de registros** , digite **engenharia** na caixa de texto **nome** .
4. Para **tipo**, selecione **ns**.
5. Em **servidor de nome**, insira os quatro servidores de nome que você registrou anteriormente na zona de **engenharia** .
6. Clique em **OK**.

## <a name="test-the-delegation"></a>Testar a delegação

Use nslookup para testar a delegação.

1. Abra uma janela do PowerShell.
2. No prompt de comando, digite `nslookup www.engineering.contoso.com.`
3. Você deve receber uma resposta não autoritativa mostrando o endereço **10.10.10.10**.

## <a name="next-steps"></a>Passos seguintes

Saiba como [Configurar o DNS reverso para serviços hospedados no Azure](dns-reverse-dns-for-azure-services.md).