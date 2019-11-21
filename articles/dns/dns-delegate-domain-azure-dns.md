---
title: 'Tutorial: Host your domain and subdomain - Azure DNS'
description: In this article, learn how to configure Azure DNS to host your DNS zones.
services: dns
author: asudbring
ms.service: dns
ms.topic: tutorial
ms.date: 3/11/2019
ms.author: allensu
ms.openlocfilehash: 062a5beaec30d510d37af436e00f4d57785245cd
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74212202"
---
# <a name="tutorial-host-your-domain-in-azure-dns"></a>Tutorial: Alojar o seu domínio no DNS do Azure

Pode utilizar o DNS do Azure para alojar o seu domínio DNS e gerir os registos DNS. Ao alojar os seus domínios no Azure, pode gerir os recursos DNS com as mesmas credenciais, APIs, ferramentas e faturação dos seus outros serviços do Azure.

Suponha que compra o domínio contoso.net a partir de uma entidade de registo de domínios e cria uma zona com o nome contoso.net no DNS do Azure. Na qualidade de proprietário do domínio, a sua entidade de registo de domínios oferece-lhe a opção de configurar os registos do servidor de nomes (NS) para o seu domínio. A entidade de registo de domínios armazena os registos NS na zona principal .net. Internet users around the world are then directed to your domain in your Azure DNS zone when they try to resolve DNS records in contoso.net.


Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Create a DNS zone.
> * Retrieve a list of name servers.
> * Delegate the domain.
> * Verify the delegation is working.


Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

You must have a domain name available to test with that you can host in Azure DNS . Deve ter controlo total sobre este domínio. O controlo total inclui a capacidade de definir os registos do servidor de nomes (NS) do domínio.

The example domain used for this tutorial is contoso.net, but use your own domain name.

## <a name="create-a-dns-zone"></a>Criar uma zona DNS

1. Go to the [Azure portal](https://portal.azure.com/) to create a DNS zone. Search for and select **DNS zones**.

   ![Zona DNS](./media/dns-delegate-domain-azure-dns/openzone650.png)

1. Select **Create DNS zone**.
1. Na página **Criar zona DNS**, introduza os valores seguintes e, em seguida, selecione **Criar**:

   | **Definição** | **Valor** | **Detalhes** |
   |---|---|---|
   |**Nome**|[o seu nome de domínio] |O nome de domínio que comprou. Este tutorial utiliza contoso.net como exemplo.|
   |**Subscrição**|[A sua subscrição]|Selecione uma subscrição na que vai criar a zona.|
   |**Grupo de recursos**|**Criar novo:** contosoRG|Crie um grupo de recursos. O nome do grupo de recursos tem de ser exclusivo dentro da subscrição que selecionou.<br>A localização do grupo de recursos não tem qualquer impacto na zona DNS. The DNS zone location is always "global," and isn't shown.|
   |**Localização**|Este dos E.U.A.||

## <a name="retrieve-name-servers"></a>Obter servidores de nomes

Antes de pode delegar a zona DNS ao DNS do Azure, terá de conhecer os servidores de nomes da sua zona. O DNS do Azure aloca servidores de nomes a partir de um conjunto sempre que é criada uma zona.

1. Com a zona DNS criada, no painel **Favoritos** do portal do Azure, selecione **Todos os recursos**. Na página **Todos os recursos**, selecione a zona de DNS. If the subscription that you selected already has several resources in it, you can enter your domain name in the **Filter by name** box to easily access the application gateway. 

1. Obtenha os servidores de nome na página da zona DNS. Neste exemplo, foram atribuídos à zona contoso.net os servidores de nomes *ns1-01.azure-dns.com*, *ns2-01.azure-dns.net*, *ns3-01.azure-dns.org* e *ns4-01.azure-dns.info*:

   ![Lista dos servidores de nomes](./media/dns-delegate-domain-azure-dns/viewzonens500.png)

O DNS do Azure cria automaticamente registos NS autoritativos na sua zona para os servidores de nomes atribuídos.

## <a name="delegate-the-domain"></a>Delegar o domínio

Agora que a zona DNS está criada e que já tem os servidores de nomes, tem de atualizar o domínio principal com os servidores de nomes DNS do Azure. Cada entidade de registo tem as suas próprias ferramentas de gestão de DNS para alterar os registos do servidor de nomes de um domínio. 

1. Na página de gestão do DNS da entidade de registo, edite os registos NS e substitua-os pelos servidores de nomes do DNS do Azure.

1. When you delegate a domain to Azure DNS, you must use the name servers that Azure DNS provides. Use all four name servers, regardless of the name of your domain. Domain delegation doesn't require a name server to use the same top-level domain as your domain.

> [!NOTE]
> Quando copiar cada endereço do servidor de nomes, certifique-se de que copia o ponto à direita no fim do endereço. O ponto à direita indica o fim de um nome de domínio completamente qualificado. Some registrars append the period if the NS name doesn't have it at the end. To be compliant with the DNS RFC, include the trailing period.

Delegations that use name servers in your own zone, sometimes called *vanity name servers*, aren't currently supported in Azure DNS.

## <a name="verify-the-delegation"></a>Verify the delegation

After you complete the delegation, you can verify that it's working by using a tool such as *nslookup* to query the Start of Authority (SOA) record for your zone. O registo SOA é criado automaticamente quando a zona é criada. You might need to wait 10 minutes or more after you complete the delegation, before you can successfully verify that it's working. Pode demorar algum tempo para as alterações serem propagadas em todo o sistema DNS.

You don't have to specify the Azure DNS name servers. Se a delegação estiver configurada corretamente, o processo de resolução DNS normal localiza os servidores de nomes automaticamente.

1. From a command prompt, enter an nslookup command similar to the following example:

   ```
   nslookup -type=SOA contoso.net
   ```

1. Verify that your response looks similar to the following nslookup output:

   ```
   Server: ns1-04.azure-dns.com
   Address: 208.76.47.4

   contoso.net
   primary name server = ns1-04.azure-dns.com
   responsible mail addr = msnhst.microsoft.com
   serial = 1
   refresh = 900 (15 mins)
   retry = 300 (5 mins)
   expire = 604800 (7 days)
   default TTL = 300 (5 mins)
   ```

## <a name="clean-up-resources"></a>Limpar recursos

Pode manter o grupo de recursos **contosoRG** se pretende acompanhar o tutorial seguinte. Caso contrário, elimine o grupo de recursos **contosoRG** para eliminar os recursos criados neste tutorial.

- Select the **contosoRG** resource group, and then select **Delete resource group**. 

## <a name="next-steps"></a>Passos seguintes

In this tutorial, you created a DNS zone for your domain and delegated it to Azure DNS. Para saber mais sobre o DNS do Azure e as aplicações Web, avance para o tutorial de aplicações Web.

> [!div class="nextstepaction"]
> [Criar registos DNS para uma aplicação Web num domínio personalizado](./dns-web-sites-custom-domain.md)