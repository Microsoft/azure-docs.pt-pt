---
title: Tutorial - Alojar o seu domínio e subdomínio no DNS do Azure
description: Este tutorial mostra-lhe como configurar o DNS do Azure para alojar as suas zonas DNS.
services: dns
author: vhorne
ms.service: dns
ms.topic: tutorial
ms.date: 3/11/2019
ms.author: victorh
ms.openlocfilehash: c0c5c5fe899c9b9b898973a88c7dac4256959ee4
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57779781"
---
# <a name="tutorial-host-your-domain-in-azure-dns"></a>Tutorial: Aloje o seu domínio no DNS do Azure

Pode utilizar o DNS do Azure para alojar o seu domínio DNS e gerir os registos DNS. Ao alojar os seus domínios no Azure, pode gerir os recursos DNS com as mesmas credenciais, APIs, ferramentas e faturação dos seus outros serviços do Azure.

Suponha que compra o domínio contoso.net a partir de uma entidade de registo de domínios e cria uma zona com o nome contoso.net no DNS do Azure. Na qualidade de proprietário do domínio, a sua entidade de registo de domínios oferece-lhe a opção de configurar os registos do servidor de nomes (NS) para o seu domínio. A entidade de registo de domínios armazena os registos NS na zona principal .net. Os usuários da Internet em todo o mundo, em seguida, são direcionados para o seu domínio na sua zona DNS do Azure quando tentam resolver os registos DNS em contoso.net.


Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Crie uma zona DNS.
> * Obter uma lista de servidores de nomes.
> * Delegar o domínio.
> * Certifique-se de que a delegação está a funcionar.


Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Tem de ter um nome de domínio disponível para testar com o que pode alojar no DNS do Azure. Deve ter controlo total sobre este domínio. O controlo total inclui a capacidade de definir os registos do servidor de nomes (NS) do domínio.

O domínio de exemplo utilizado para este tutorial é contoso.net, mas utilizar o seu próprio nome de domínio.

## <a name="create-a-dns-zone"></a>Criar uma zona DNS

1. Inicie sessão no Portal do Azure.
1. No canto superior esquerdo, selecione **Criar um recurso** > **Rede** > **Zona DNS**, para abrir a página **Criar zona DNS**.

   ![Zona DNS](./media/dns-delegate-domain-azure-dns/openzone650.png)

1. Na página **Criar zona DNS**, introduza os valores seguintes e, em seguida, selecione **Criar**:

   | **Definição** | **Valor** | **Detalhes** |
   |---|---|---|
   |**Nome**|[o seu nome de domínio] |O nome de domínio que comprou. Este tutorial utiliza contoso.net como exemplo.|
   |**Subscrição**|[A sua subscrição]|Selecione uma subscrição na que vai criar a zona.|
   |**Grupo de recursos**|**Criar novo:** contosoRG|Crie um grupo de recursos. O nome do grupo de recursos tem de ser exclusivo dentro da subscrição que selecionou.<br>A localização do grupo de recursos não tem qualquer impacto na zona DNS. A localização da zona DNS é sempre "global" e não é mostrada.|
   |**Localização**|E.U.A. Leste||

## <a name="retrieve-name-servers"></a>Obter servidores de nomes

Antes de pode delegar a zona DNS ao DNS do Azure, terá de conhecer os servidores de nomes da sua zona. O DNS do Azure aloca servidores de nomes a partir de um conjunto sempre que é criada uma zona.

1. Com a zona DNS criada, no painel **Favoritos** do portal do Azure, selecione **Todos os recursos**. Na página **Todos os recursos**, selecione a zona de DNS. Se a subscrição que selecionou já tiver vários recursos, pode introduzir o nome de domínio a **filtrar por nome** caixa para aceder facilmente o gateway de aplicação. 

1. Obtenha os servidores de nome na página da zona DNS. Neste exemplo, foram atribuídos à zona contoso.net os servidores de nomes *ns1-01.azure-dns.com*, *ns2-01.azure-dns.net*, *ns3-01.azure-dns.org* e *ns4-01.azure-dns.info*:

   ![Lista dos servidores de nomes](./media/dns-delegate-domain-azure-dns/viewzonens500.png)

O DNS do Azure cria automaticamente registos NS autoritativos na sua zona para os servidores de nomes atribuídos.

## <a name="delegate-the-domain"></a>Delegar o domínio

Agora que a zona DNS está criada e que já tem os servidores de nomes, tem de atualizar o domínio principal com os servidores de nomes DNS do Azure. Cada entidade de registo tem as suas próprias ferramentas de gestão de DNS para alterar os registos do servidor de nomes de um domínio. 

1. Na página de gestão do DNS da entidade de registo, edite os registos NS e substitua-os pelos servidores de nomes do DNS do Azure.

1. Quando delegar um domínio ao DNS do Azure, tem de utilizar os servidores de nomes DNS do Azure fornece. Utilize todos os quatro servidores de nomes, independentemente do nome do seu domínio. Delegação de domínio não necessita de um servidor de nomes para utilizar o mesmo domínio de nível superior que o seu domínio.

> [!NOTE]
> Quando copiar cada endereço do servidor de nomes, certifique-se de que copia o ponto à direita no fim do endereço. O ponto à direita indica o fim de um nome de domínio completamente qualificado. Algumas entidades de registo acrescentar o período, se o nome de NS se não tiver isso, no final. Para estar em conformidade com a RFC de DNS, inclua o ponto à direita.

As delegações que utilizam servidores de nomes na sua própria zona, por vezes denominado *servidores de nomes personalizados*, não são atualmente suportadas no DNS do Azure.

## <a name="verify-the-delegation"></a>Certifique-se a delegação

Depois de concluir a delegação, pode verificar se está a funcionar ao utilizar uma ferramenta como *nslookup* para consultar o registo de início de autoridade (SOA) para a sua zona. O registo SOA é criado automaticamente quando a zona é criada. Poderá ter de aguardar 10 minutos ou mais depois de concluir a delegação, antes de poder com êxito Certifique-se de que está a funcionar. Pode demorar algum tempo para as alterações serem propagadas em todo o sistema DNS.

Não precisa especificar os servidores de nomes do DNS do Azure. Se a delegação estiver configurada corretamente, o processo de resolução DNS normal localiza os servidores de nomes automaticamente.

1. Num prompt de comando, introduza um comando nslookup semelhante ao seguinte exemplo:

   ```
   nslookup -type=SOA contoso.net
   ```

1. Certifique-se de que sua resposta é semelhante à seguinte saída nslookup:

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

- Selecione o **contosoRG** recursos de grupo e, em seguida, selecione **eliminar grupo de recursos**. 

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, criou uma zona DNS para o seu domínio e delegado-lo ao DNS do Azure. Para saber mais sobre o DNS do Azure e as aplicações Web, avance para o tutorial de aplicações Web.

> [!div class="nextstepaction"]
> [Criar registos DNS para uma aplicação Web num domínio personalizado](./dns-web-sites-custom-domain.md)