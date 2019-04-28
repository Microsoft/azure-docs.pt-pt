---
title: Tutorial – Criar um registo de alias do DNS do Azure para fazer referência a um registo de recursos na zona.
description: Este tutorial mostra como configurar um registo de alias do DNS do Azure para fazer referência a um registo de recursos na zona.
services: dns
author: vhorne
ms.service: dns
ms.topic: tutorial
ms.date: 9/25/2018
ms.author: victorh
ms.openlocfilehash: 3b4ee688d6a5606ab6008b459fcf6331c24afaae
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61429795"
---
# <a name="tutorial-create-an-alias-record-to-refer-to-a-zone-resource-record"></a>Tutorial: Criar um registo de alias para fazer referência a um registo de recurso de zona

Os registos de alias podem fazer referência a outros conjuntos de registos do mesmo tipo. Por exemplo, pode ter um conjunto de registos CNAME do DNS que funciona como alias de outro conjunto de registos CNAME do mesmo tipo. Esta capacidade será útil se quiser ter alguns conjuntos de registos como aliases e alguns como não aliases em termos de comportamento.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um registo de alias para um registo de recursos na zona.
> * Testar o registo de alias.


Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos
Deve ter um nome de domínio disponível que possa alojar no DNS do Azure para testar. Deve ter controlo total sobre este domínio. O controlo total inclui a capacidade de definir os registos do servidor de nomes (NS) do domínio.

Para obter instruções para alojar o seu domínio no DNS do Azure, consulte [Tutorial: Aloje o seu domínio no DNS do Azure](dns-delegate-domain-azure-dns.md).


## <a name="create-an-alias-record"></a>Criar um registo de alias

Crie um registo de alias que esteja associado a um registo de recursos na zona.

### <a name="create-the-target-resource-record"></a>Criar um registo de recursos de destino
1. Selecione na sua zona DNS do Azure para abrir a zona.
2. Selecione **Conjunto de registos**.
3. Na caixa de texto **Nome**, introduza **servidor**.
4. Em **Tipo**, selecione **A**.
5. Na caixa de texto **ENDEREÇO IP**, introduza **10.10.10.10**.
6. Selecione **OK**.

### <a name="create-the-alias-record"></a>Criar o registo de alias
1. Selecione na sua zona DNS do Azure para abrir a zona.
2. Selecione **Conjunto de registos**.
3. Na caixa de texto **Nome**, introduza **teste**.
4. Em **Tipo**, selecione **A**.
5. Selecione **Sim** na caixa de verificação **Conjunto de Registos de Alias**. Em seguida, selecione a opção **Conjunto de registos da zona**.
6. No **Conjunto de registos da zona**, selecione o registo **servidor**.
7. Selecione **OK**.

## <a name="test-the-alias-record"></a>Testar o registo de alias

1. Inicie a sua ferramenta nslookup favorita. Uma das opções consiste em navegar até [https://network-tools.com/nslook](https://network-tools.com/nslook).
2. Defina o tipo de consulta para os registos A e procure **teste.\<o nome de domínio\>**. A resposta é **10.10.10.10**.
3. No portal do Azure, altere o registo A do **servidor** para **10.11.11.11**.
4. Aguarde alguns minutos e, em seguida, utilize novamente nslookup para **testar** o registo. A resposta é **10.11.11.11**.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não precisar dos recursos criados neste tutorial, elimine o **servidor** e **teste** os registos de recursos na sua zona.


## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, criou um registo de alias para fazer referência a um registo de recursos na zona. Para saber mais sobre o DNS do Azure e as aplicações Web, avance para o tutorial de aplicações Web.

> [!div class="nextstepaction"]
> [Criar registos DNS para uma aplicação Web num domínio personalizado](./dns-web-sites-custom-domain.md)
