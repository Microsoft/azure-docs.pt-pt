---
title: Início rápido - criar uma zona DNS do Azure e registar com o portal do Azure
description: Utilize este guia de início rápido passo a passo para saber como criar uma zona DNS do Azure e registar com o portal do Azure.
services: dns
author: vhorne
ms.service: dns
ms.topic: quickstart
ms.date: 3/11/2019
ms.author: victorh
ms.openlocfilehash: c09bb25fcdeead2a982ab235efbdff8932f5308f
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57775979"
---
# <a name="quickstart-create-an-azure-dns-zone-and-record-using-the-azure-portal"></a>Início rápido: Criar uma zona DNS do Azure e o registo no portal do Azure

Pode configurar o DNS do Azure para resolver os nomes de anfitrião no seu domínio público. Por exemplo, se tiver adquirido o *contoso.xyz* nome de domínio de uma entidade de registo de nome de domínio, pode configurar o DNS do Azure para alojar o *contoso.xyz* domínio e resolve *www.contoso.xyz* para o endereço IP do seu servidor web ou aplicação web.

Neste início rápido, irá criar um domínio de teste e, em seguida, crie um registo de endereço para resolver *www* para o endereço IP *10.10.10.10*.

>[!IMPORTANT]
>Todos os nomes e endereços IP neste início rápido são exemplos que não representam cenários do mundo real.

<!---
You can also perform these steps using [Azure PowerShell](dns-getstarted-powershell.md) or the cross-platform [Azure CLI](dns-getstarted-cli.md).
--->

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Para todos os passos do portal, inicie sessão para o [portal do Azure](https://portal.azure.com).

## <a name="create-a-dns-zone"></a>Criar uma zona DNS

Uma zona DNS contém as entradas de DNS para um domínio. Para começar a alojar o seu domínio no DNS do Azure, crie uma zona DNS para esse nome de domínio. 

**Para criar a zona DNS:**

1. No canto superior esquerdo, selecione **criar um recurso**, em seguida, **redes**e, em seguida **zona DNS**.

1. Sobre o **criar zona DNS** página, escreva ou selecione os seguintes valores:

   - **Nome**: Tipo *contoso.xyz* para este exemplo de início rápido. O nome da zona DNS pode ser qualquer valor que já não está configurado nos servidores de DNS do Azure. Um valor real seria um domínio que comprou de uma entidade de registo de nomes de domínio.
   - **Grupo de recursos**: Selecione **criar novo**, introduza *MyResourceGroup*e selecione **OK**. O nome do grupo de recursos tem de ser exclusivo dentro da subscrição do Azure. 

1. Selecione **Criar**.

   ![Zona DNS](./media/dns-getstarted-portal/openzone650.png)

A criação da zona pode demorar alguns minutos.

## <a name="create-a-dns-record"></a>Criar um registo DNS

Criar entradas DNS ou registos para o seu domínio dentro da zona DNS. Crie um novo registo de endereço ou de registo "A" para resolver um nome de anfitrião para um endereço IPv4.

**Para criar um registo "A":**

1. No portal do Azure, em **todos os recursos**, abra o **contoso.xyz** zona DNS no **MyResourceGroup** grupo de recursos. Pode introduzir *contoso.xyz* no **filtrar por nome** caixa para encontrá-lo mais facilmente.

1. Na parte superior a **zona DNS** página, selecione **+ conjunto de registos**.

1. Sobre o **adicionar conjunto de registos** página, escreva ou selecione os seguintes valores:

   - **Nome**: Tipo *www*. O nome do registo é o nome de anfitrião que pretende resolver para o endereço IP especificado.
   - **Tipo de**: Selecione **A**. "A" registos são as mais comuns, mas existem outros tipos de registo para servidores de email ("MX"), endereços IP v6 ('AAAA') e assim por diante. 
   - **TTL**: Tipo *1*. *Time-to-live* de DNS pedido especifica o tempo que os clientes e servidores DNS podem colocar em cache uma resposta.
   - **Unidade de TTL**: Selecione **horas**. Esta é a unidade de tempo para o **TTL** valor. 
   - **Endereço IP**: Para este exemplo de início rápido, digite *10.10.10.10*. Este valor é que o nome do registo de endereço IP é resolvido para o. Num cenário do mundo real, deve introduzir o endereço IP público para o servidor web.

Uma vez que este guia de introdução é apenas para fins de testes rápidas, não é necessário para configurar os servidores de nomes do DNS do Azure numa entidade de registo de nome de domínio. Com um domínio de produção real, desejará qualquer pessoa na Internet para resolver o nome de anfitrião para ligar ao servidor web ou aplicação. Vai visitar a sua entidade de registo de nome de domínio para substituir os registos do servidor de nome com os servidores de nomes do DNS do Azure. Para obter mais informações, consulte [Tutorial: Aloje o seu domínio no DNS do Azure](dns-delegate-domain-azure-dns.md#delegate-the-domain).

## <a name="test-the-name-resolution"></a>Testar a resolução de nomes

Agora que tem uma zona DNS de teste com um registo de teste "A", pode testar a resolução de nomes com uma ferramenta chamada *nslookup*. 

**Para testar a resolução de nome DNS:**

1. No portal do Azure, em **todos os recursos**, abra o **contoso.xyz** zona DNS no **MyResourceGroup** grupo de recursos. Pode introduzir *contoso.xyz* no **filtrar por nome** caixa para encontrá-lo mais facilmente.

1. Copiar um dos nomes de servidor na lista de servidores de nome no **descrição geral** página. 

   ![zona](./media/dns-getstarted-portal/viewzonens500.png)

1. Abra uma linha de comandos e execute o seguinte comando:

   ```
   nslookup www.contoso.xyz <name server name>
   ```

   Por exemplo:

   ```
   nslookup www.contoso.xyz ns1-08.azure-dns.com.
   ```

   Deverá ver algo semelhante ao seguinte ecrã:

   ![nslookup](media/dns-getstarted-portal/nslookup.PNG)

O nome do anfitrião **www.contoso.xyz** é resolvida para **10.10.10.10**, tal como tiver configurado. Este resultado verifica se a resolução de nomes está a funcionar corretamente. 

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não precisar dos recursos criados neste início rápido, removê-los ao eliminar a **MyResourceGroup** grupo de recursos. Abra o **MyResourceGroup** recursos de grupo e selecione **eliminar grupo de recursos**.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Criar registos DNS para uma aplicação Web num domínio personalizado](./dns-web-sites-custom-domain.md)