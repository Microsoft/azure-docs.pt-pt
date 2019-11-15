---
title: 'Início rápido: criar uma zona DNS e um portal do Azure de registros'
titleSuffix: Azure DNS
description: Use este guia de início rápido passo a passo para saber como criar uma zona DNS do Azure e um registro usando o portal do Azure.
services: dns
author: asudbring
ms.service: dns
ms.topic: quickstart
ms.date: 3/11/2019
ms.author: allensu
ms.openlocfilehash: cb81b0ec2b5283ba242dd7c2dd549c330e230f0a
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082912"
---
# <a name="quickstart-create-an-azure-dns-zone-and-record-using-the-azure-portal"></a>Início rápido: criar uma zona DNS do Azure e um registro usando o portal do Azure

Pode configurar o DNS do Azure para resolver os nomes de anfitrião no seu domínio público. Por exemplo, se você comprou o nome de domínio *contoso. xyz* de um registrador de nome de domínio, você pode configurar o DNS do Azure para hospedar o domínio *contoso. xyz* e resolver *www. contoso. xyz* para o endereço IP do seu servidor Web ou aplicativo Web.

Neste guia de início rápido, você criará um domínio de teste e, em seguida, criará um registro de endereço para resolver *www* para o endereço IP *10.10.10.10*.

>[!IMPORTANT]
>Todos os nomes e endereços IP neste guia de início rápido são exemplos que não representam cenários do mundo real.

<!---
You can also perform these steps using [Azure PowerShell](dns-getstarted-powershell.md) or the cross-platform [Azure CLI](dns-getstarted-cli.md).
--->

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Para todas as etapas do portal, entre no [portal do Azure](https://portal.azure.com).

## <a name="create-a-dns-zone"></a>Criar uma zona DNS

Uma zona DNS contém as entradas DNS para um domínio. Para iniciar a hospedagem de seu domínio no DNS do Azure, você cria uma zona DNS para esse nome de domínio. 

**Para criar a zona DNS:**

1. No canto superior esquerdo, selecione **criar um recurso**, **rede**e, em seguida, **zona DNS**.

1. Na página **criar zona DNS** , digite ou selecione os seguintes valores:

   - **Nome**: digite *contoso. xyz* para este exemplo de início rápido. O nome da zona DNS pode ser qualquer valor que ainda não esteja configurado nos servidores DNS do Azure. Um valor real seria um domínio que comprou de uma entidade de registo de nomes de domínio.
   - **Grupo de recursos**: selecione **criar novo**, insira grupo de *recursos*e selecione **OK**. O nome do grupo de recursos deve ser exclusivo na assinatura do Azure. 

1. Selecione **Criar**.

   ![Zona DNS](./media/dns-getstarted-portal/openzone650.png)

A criação da zona pode demorar alguns minutos.

## <a name="create-a-dns-record"></a>Criar um registo DNS

Você cria registros ou entradas DNS para seu domínio dentro da zona DNS. Crie um novo registro de endereço ou um registro "A" para resolver um nome de host para um endereço IPv4.

**Para criar um registro ' A ':**

1. No portal do Azure, em **todos os recursos**, abra a zona DNS **contoso. xyz** no grupo de recursos **MyResource** Group. Você pode inserir *contoso. xyz* na caixa **Filtrar por nome** para encontrá-la mais facilmente.

1. Na parte superior da página **zona DNS** , selecione **+ conjunto de registros**.

1. Na página **Adicionar conjunto de registros** , digite ou selecione os seguintes valores:

   - **Nome**: digite *www*. O nome do registro é o nome do host que você deseja resolver para o endereço IP especificado.
   - **Tipo**: selecione **um**. Os registros ' a ' são os mais comuns, mas há outros tipos de registro para servidores de email (' MX '), endereços IP V6 (' AAAA ') e assim por diante. 
   - **TTL**: tipo *1*. A *vida* útil da solicitação DNS especifica por quanto tempo os servidores DNS e os clientes podem armazenar em cache uma resposta.
   - **Unidade de TTL**: selecione **horas**. Esta é a unidade de tempo para o valor **TTL** . 
   - **Endereço IP**: para este exemplo de início rápido, digite *10.10.10.10*. Esse valor é o endereço IP ao qual o nome do registro é resolvido. Em um cenário do mundo real, você deve inserir o endereço IP público para seu servidor Web.

Como esse início rápido é apenas para fins de teste rápido, não há necessidade de configurar os servidores de nome DNS do Azure em um registrador de nome de domínio. Com um domínio de produção real, você desejará que qualquer pessoa na Internet resolva o nome do host para se conectar ao seu servidor Web ou aplicativo. Você visitará seu registrador de nome de domínio para substituir os registros de servidor de nomes pelos servidores de nome DNS do Azure. Para obter mais informações, consulte [tutorial: hospedar seu domínio no DNS do Azure](dns-delegate-domain-azure-dns.md#delegate-the-domain).

## <a name="test-the-name-resolution"></a>Testar a resolução de nomes

Agora que você tem uma zona DNS de teste com um registro ' A ' de teste, você pode testar a resolução de nome com uma ferramenta chamada *nslookup*. 

**Para testar a resolução de nomes DNS:**

1. No portal do Azure, em **todos os recursos**, abra a zona DNS **contoso. xyz** no grupo de recursos **MyResource** Group. Você pode inserir *contoso. xyz* na caixa **Filtrar por nome** para encontrá-la mais facilmente.

1. Copie um dos nomes de servidor de nomes da lista de servidores de nomes na página **visão geral** . 

   ![zona](./media/dns-getstarted-portal/viewzonens500.png)

1. Abra um prompt de comando e execute o seguinte comando:

   ```
   nslookup www.contoso.xyz <name server name>
   ```

   Por exemplo:

   ```
   nslookup www.contoso.xyz ns1-08.azure-dns.com.
   ```

   Você deverá ver algo parecido com a tela a seguir:

   ![nslookup](media/dns-getstarted-portal/nslookup.PNG)

O nome de host **www\.contoso. xyz** é resolvido para **10.10.10.10**, assim como você o configurou. Esse resultado verifica se a resolução de nomes está funcionando corretamente. 

## <a name="clean-up-resources"></a>Limpar recursos

Quando você não precisar mais dos recursos criados neste guia de início rápido, remova-os excluindo o grupo de recursos **MyResource** Group. Abra o grupo de recursos **MyResource** Group e selecione **excluir grupo de recursos**.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Criar registos DNS para uma aplicação Web num domínio personalizado](./dns-web-sites-custom-domain.md)