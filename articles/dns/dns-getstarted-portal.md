---
title: 'Quickstart: Criar uma zona DNS e gravar - Portal Azure'
titleSuffix: Azure DNS
description: Utilize este guia de arranque rápido passo a passo para aprender a criar uma zona DNS Azure e gravar usando o portal Azure.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: quickstart
ms.date: 3/11/2019
ms.author: rohink
ms.openlocfilehash: 26e5386f1c9730f1600e59a002ea7845b82ffe06
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "76937120"
---
# <a name="quickstart-create-an-azure-dns-zone-and-record-using-the-azure-portal"></a>Quickstart: Criar uma zona DNS Azure e gravar usando o portal Azure

Pode configurar o DNS do Azure para resolver os nomes de anfitrião no seu domínio público. Por exemplo, se adquiriu o nome de domínio *contoso.xyz* a partir de um registo de nome de domínio, pode configurar o Azure DNS para acolher o domínio *contoso.xyz* e resolver *www.contoso.xyz* para o endereço IP do seu servidor web ou aplicação web.

Neste arranque rápido, criará um domínio de teste e, em seguida, criará um registo de endereços para resolver *www to* the IP address *10.10.10.10 .*

>[!IMPORTANT]
>Todos os nomes e endereços IP neste quickstart são exemplos que não representam cenários do mundo real.

<!---
You can also perform these steps using [Azure PowerShell](dns-getstarted-powershell.md) or the cross-platform [Azure CLI](dns-getstarted-cli.md).
--->

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Para todos os passos do portal, inscreva-se no [portal Azure.](https://portal.azure.com)

## <a name="create-a-dns-zone"></a>Criar uma zona DNS

Uma zona DNS contém as entradas de DNS para um domínio. Para começar a hospedar o seu domínio em Azure DNS, cria uma zona DNS para esse nome de domínio. 

**Para criar a zona DNS:**

1. Na parte superior esquerda, selecione **Criar um recurso**, em **seguida, Networking**, e, em **seguida, zona DNS**.

1. Na página de **zona Create DNS,** escreva ou selecione os seguintes valores:

   - **Nome**: *Digite contoso.xyz* para este exemplo de arranque rápido. O nome da zona DNS pode ser qualquer valor que ainda não esteja configurado nos servidores DNS Do Azure. Um valor real seria um domínio que comprou de uma entidade de registo de nomes de domínio.
   - **Grupo de recursos**: Selecione **Criar novo,** introduza *o MyResourceGroup,* e selecione **OK**. O nome do grupo de recursos deve ser único dentro da subscrição Azure. 

1. Selecione **Criar**.

   ![Zona DNS](./media/dns-getstarted-portal/openzone650.png)

A criação da zona pode demorar alguns minutos.

## <a name="create-a-dns-record"></a>Criar um registo DNS

Cria entradas de DNS ou registos para o seu domínio dentro da zona DNS. Crie um novo registo de endereços ou 'A' para resolver um nome de anfitrião para um endereço IPv4.

**Para criar um disco 'A':**

1. No portal Azure, sob **todos os recursos,** abra a zona de DNS **contoso.xyz** no grupo de recursos **MyResourceGroup.** Pode introduzir *contoso.xyz* no **Filtro por caixa** de nome para encontrá-lo mais facilmente.

1. No topo da página da **zona DNS,** selecione **+ Conjunto de registos**.

1. Na página **'Adicionar registo',** digite ou selecione os seguintes valores:

   - **Nome**: *Digitar www*. O nome de registo é o nome de anfitrião que pretende resolver para o endereço IP especificado.
   - **Tipo:** Selecione **A**. Os registos 'A' são os mais comuns, mas existem outros tipos de registo para servidores de correio ('MX'), endereços IP v6 ('AAAA'), e assim por diante. 
   - **TTL**: Tipo *1*. *O tempo de vida* do pedido dNS especifica quanto tempo os servidores e clientes DNS podem cache uma resposta.
   - **Unidade TTL**: Selecione **Horas**. Esta é a unidade de tempo para o valor **TTL.** 
   - **Endereço IP**: Para este exemplo de arranque rápido, tipo *10.10.10.10.10*. Este valor é o endereço IP a que o nome do registo se resolve. Num cenário real, introduziria o endereço IP público para o seu servidor web.

Uma vez que este quickstart é apenas para fins de teste rápido, não há necessidade de configurar os servidores de nome DNS Azure em um registrador de nome de domínio. Com um domínio de produção real, você vai querer que qualquer pessoa na Internet resolva o nome do anfitrião para se conectar ao seu servidor ou app web. Visitará o seu registo de nome de domínio para substituir os registos do servidor de nomes por servidores de nome SNS Do Azure. Para mais informações, consulte [Tutorial: Hospedar o seu domínio em DNS Azure](dns-delegate-domain-azure-dns.md#delegate-the-domain).

## <a name="test-the-name-resolution"></a>Testar a resolução de nomes

Agora que tem uma zona de DNS de teste com um registo 'A' de teste, pode testar a resolução de nomecom uma ferramenta chamada *nslookup*. 

**Para testar a resolução de nomes DNS:**

1. No portal Azure, sob **todos os recursos,** abra a zona de DNS **contoso.xyz** no grupo de recursos **MyResourceGroup.** Pode introduzir *contoso.xyz* no **Filtro por caixa** de nome para encontrá-lo mais facilmente.

1. Copie um dos nomes do servidor de nome sintetizador estoiros na página **'Overview'.** 

   ![zona](./media/dns-getstarted-portal/viewzonens500.png)

1. Abra um pedido de comando e executar o seguinte comando:

   ```
   nslookup www.contoso.xyz <name server name>
   ```

   Por exemplo:

   ```
   nslookup www.contoso.xyz ns1-08.azure-dns.com.
   ```

   Devia ver algo como o seguinte ecrã:

   ![nslookup](media/dns-getstarted-portal/nslookup.PNG)

O nome de anfitrião **www\.contoso.xyz** resolve-se em **10.10.10.10.10**, tal como o configura. Este resultado verifica que a resolução de nomes está a funcionar corretamente. 

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não precisar dos recursos que criou neste arranque rápido, remova-os eliminando o grupo de recursos **MyResourceGroup.** Abra o grupo de recursos **MyResourceGroup** e selecione **Eliminar o grupo de recursos**.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar registos DNS para uma aplicação Web num domínio personalizado](./dns-web-sites-custom-domain.md)