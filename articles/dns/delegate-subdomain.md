---
title: Delegado de um subdomínio - Azure DNS
description: Com este caminho de aprendizagem, começa a delegar um subdomínio Azure DNS.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: how-to
ms.date: 2/7/2019
ms.author: rohink
ms.openlocfilehash: add7674771fd19f6029a94c46624006f0cf30f1a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011545"
---
# <a name="delegate-an-azure-dns-subdomain"></a>Delegado um subdomínio Azure DNS

Pode utilizar o portal Azure para delegar um subdomínio DNS. Por exemplo, se possuir o domínio contoso.com, pode delegar um subdomínio chamado *engenharia* para outra zona separada que pode administrar separadamente da zona de contoso.com.

Se preferir, pode delegar um subdomínio utilizando [a Azure PowerShell](delegate-subdomain-ps.md).

## <a name="prerequisites"></a>Pré-requisitos

Para delegar um subdomínio Azure DNS, deve primeiro delegar o seu domínio público no Azure DNS. Consulte [um domínio para Azure DNS](./dns-delegate-domain-azure-dns.md) para obter instruções sobre como configurar os servidores do seu nome para a delegação. Uma vez que o seu domínio é delegado à sua zona Azure DNS, pode delegar o seu subdomínio.

> [!NOTE]
> Contoso.com é usado como exemplo ao longo deste artigo. Substitua o seu nome de domínio por contoso.com.

## <a name="create-a-zone-for-your-subdomain"></a>Crie uma zona para o seu subdomínio

Primeiro, crie a zona para o subdomínio **de engenharia.**

1. A partir do portal Azure, **selecione Criar um recurso**.
2. Na caixa de pesquisa, escreva **DNS** e selecione **a zona DNS**.
3. Selecione **Criar**.
4. No painel **de zona Create DNS,** escreva **engineering.contoso.com** na caixa de texto **'Nome'.**
5. Selecione o grupo de recursos para a sua zona. É melhor usar o mesmo grupo de recursos que a zona-mãe para manter recursos semelhantes juntos.
6. Clique em **Criar**.
7. Depois que a implantação for bem sucedida, vá para a nova zona.

## <a name="note-the-name-servers"></a>Note os servidores de nomes

Em seguida, note os quatro servidores de nome para o subdomínio de engenharia.

No painel da zona **de engenharia,** note os quatro servidores de nome para a zona. Usará estes servidores de nome mais tarde.

## <a name="create-a-test-record"></a>Criar um registo de teste

Crie um registo **A** para ser utilizado para testes. Por exemplo, crie um **disco www** A e configuure-o com um endereço IP **de 10.10.10.10.**

## <a name="create-an-ns-record"></a>Criar um registo NS

Em seguida, crie um registo de servidor de nome (NS) para a zona **de engenharia.**

1. Navegue para a zona para o domínio dos pais.
2. Selecione **+ Conjunto de registos**.
3. No painel **de registo adicionar,** **digite engenharia** na caixa de texto **Name.**
4. Para **tipo**, selecione **NS**.
5. Under **Name server**, insira os quatro servidores de nome que gravou anteriormente a partir da zona de **engenharia.**
6. Clique em **OK**.

## <a name="test-the-delegation"></a>Teste a delegação

Use nslookup para testar a delegação.

1. Abra uma janela do PowerShell.
2. No comando rápido, tipo `nslookup www.engineering.contoso.com.`
3. Deverá receber uma resposta não autorizada que mostre o endereço **10.10.10.10.10**.

## <a name="next-steps"></a>Passos seguintes

Saiba como [configurar DNS invertidos para serviços hospedados no Azure](dns-reverse-dns-for-azure-services.md).