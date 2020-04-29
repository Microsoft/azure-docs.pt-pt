---
title: Delegar um subdomínio - Azure DNS
description: Com este caminho de aprendizagem, começar a delegar um subdomínio Azure DNS.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 2/7/2019
ms.author: rohink
ms.openlocfilehash: cd1443a9ca8ccf7172072078734f21d789d36194
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76937438"
---
# <a name="delegate-an-azure-dns-subdomain"></a>Delegar um subdomínio Azure DNS

Pode utilizar o portal Azure para delegar um subdomínio DNS. Por exemplo, se possuir o domínio contoso.com, pode delegar um subdomínio chamado *engenharia* para outra zona separada que pode administrar separadamente da zona contoso.com.

Se preferir, pode delegar um subdomínio utilizando [o Azure PowerShell](delegate-subdomain-ps.md).

## <a name="prerequisites"></a>Pré-requisitos

Para delegar um subdomínio Azure DNS, deve primeiro delegar o seu domínio público no Azure DNS. Consulte [o Delegado de Um domínio para o DNS Azure](./dns-delegate-domain-azure-dns.md) para obter instruções sobre como configurar os seus servidores de nome para delegação. Assim que o seu domínio for delegado à sua zona DeNS Azure, pode delegar o seu subdomínio.

> [!NOTE]
> Contoso.com é usado como exemplo ao longo deste artigo. Substitua o seu nome de domínio por contoso.com.

## <a name="create-a-zone-for-your-subdomain"></a>Crie uma zona para o seu subdomínio

Primeiro, crie a zona para o subdomínio de **engenharia.**

1. A partir do portal Azure, selecione **Criar um recurso**.
2. Na caixa de pesquisa, digite **DNS,** e selecione **zona DNS**.
3. Selecione **Criar**.
4. No painel de **zona Create DNS,** digite **engineering.contoso.com** na caixa de texto **Nome.**
5. Selecione o grupo de recursos para a sua zona. Talvez queira usar o mesmo grupo de recursos que a zona-mãe para manter recursos semelhantes.
6. Clique em **Criar**.
7. Depois do lançamento ter sucesso, vá para a nova zona.

## <a name="note-the-name-servers"></a>Note os servidores de nomes

Em seguida, note os quatro servidores de nome para o subdomínio de engenharia.

No painel da zona **de engenharia,** note os quatro servidores de nome para a zona. Utilizará estes servidores de nomes mais tarde.

## <a name="create-a-test-record"></a>Criar um registo de teste

Crie um registo **A** para ser usado para testes. Por exemplo, crie um **registo www** Um e configure-o com um endereço IP **10.10.10.10.10.**

## <a name="create-an-ns-record"></a>Criar um registo ns

Em seguida, crie um registo de servidor de nome (NS) para a zona **de engenharia.**

1. Navegue para a zona para o domínio dos pais.
2. Selecione **+ Conjunto de registos**.
3. No painel **de conjunto de registos Adicionar,** **digite a engenharia** na caixa de texto **Name.**
4. Para **Tipo,** selecione **NS**.
5. No **servidor Nome,** introduza os servidores de quatro nomes que gravou anteriormente a partir da zona **de engenharia.**
6. Clique em **OK**.

## <a name="test-the-delegation"></a>Testar a delegação

Use nslookup para testar a delegação.

1. Abra uma janela do PowerShell.
2. No pedido de comando, tipo`nslookup www.engineering.contoso.com.`
3. Deve receber uma resposta não autorizada que mostre o endereço **10.10.10.10 .**

## <a name="next-steps"></a>Passos seguintes

Saiba como [configurar dNS invertidos para serviços hospedados em Azure](dns-reverse-dns-for-azure-services.md).