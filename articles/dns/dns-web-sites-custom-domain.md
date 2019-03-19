---
title: Tutorial - criar registos DNS do Azure personalizados para uma aplicação Web
description: Neste tutorial, vai criar registos DNS de domínio personalizado para uma aplicação Web com o DNS do Azure.
services: dns
author: vhorne
ms.service: dns
ms.topic: tutorial
ms.date: 3/11/2019
ms.author: victorh
ms.openlocfilehash: 43df80e060ff698537f7fd65075006e6dfffe6c1
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58117154"
---
# <a name="tutorial-create-dns-records-in-a-custom-domain-for-a-web-app"></a>Tutorial: Criar registos DNS num domínio personalizado para uma aplicação web 

Pode configurar o DNS do Azure para alojar um domínio personalizado para as suas aplicações Web. Por exemplo, pode criar uma aplicação web do Azure e ter o acesso de utilizadores com qualquer um dos www\.contoso.com ou contoso.com como um nome de domínio completamente qualificado (FQDN).

> [!NOTE]
> Contoso.com é utilizado como um exemplo neste tutorial. Substitua o seu nome de domínio por contoso.com.

Para tal, terá de criar três registos:

* Um registo "A" de raiz que aponte para contoso.com
* Um registo "TXT" de raiz para verificação
* Um registo "CNAME" para o nome de www que aponte para o registo A

Tenha em atenção que, se criar um registo para uma aplicação Web no Azure, o registo A tem de ser atualizado manualmente se o endereço IP subjacente à aplicação Web for alterado.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um registo A e um registo TXT para o seu domínio personalizado
> * Criar um registo CNAME para o seu domínio personalizado
> * Testar os novos registos
> * Adicionar nomes de anfitrião personalizados à aplicação Web
> * Testar os nomes de anfitrião personalizados


Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Tem de ter um nome de domínio disponível para testar com o que pode alojar no DNS do Azure. Deve ter controlo total sobre este domínio. O controlo total inclui a capacidade de definir os registos do servidor de nomes (NS) do domínio.
* [Crie uma aplicação do Serviço de Aplicações](../app-service/app-service-web-get-started-html.md) ou utilize uma aplicação que tenha criado para outro tutorial.

* Crie uma zona DNS no DNS do Azure e delegue a zona na sua entidade de registo ao DNS do Azure.

   1. Para criar uma zona DNS, siga os passos em [Criar uma zona DNS](dns-getstarted-create-dnszone.md).
   2. Para delegar a zona ao DNS do Azure, siga os passos em [Delegação de domínio DNS](dns-delegate-domain-azure-dns.md).

Depois de criar uma zona e delegá-la ao DNS do Azure, pode criar registos para o seu domínio personalizado.

## <a name="create-an-a-record-and-txt-record"></a>Criar um registo A e um registo TXT

Um registo A é utilizado para mapear um nome para o respetivo endereço IP. No exemplo seguinte, atribua "\@" como um registo A com o endereço IPv4 da sua aplicação Web. Normalmente, \@ representa o domínio de raiz.

### <a name="get-the-ipv4-address"></a>Obter o endereço IPv4

No painel de navegação esquerdo da página Serviços Aplicacionais no portal do Azure, selecione **Domínios personalizados**. 

![Menu de domínio personalizado](../app-service/./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

Na página **Domínios personalizados**, copie o endereço IPv4 da aplicação:

![Navegação do portal para a aplicação do Azure](../app-service/./media/app-service-web-tutorial-custom-domain/mapping-information.png)

### <a name="create-the-a-record"></a>Criar um registo A

```azurepowershell
New-AzDnsRecordSet -Name "@" -RecordType "A" -ZoneName "contoso.com" `
 -ResourceGroupName "MyAzureResourceGroup" -Ttl 600 `
 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "<your web app IP address>")
```

### <a name="create-the-txt-record"></a>Criar o registo TXT

Os Serviços Aplicacionais utilizam este registo apenas no momento da configuração, para verificar que é o proprietário do domínio personalizado. Pode eliminar este registo TXT após a validação e configuração do domínio personalizado no Serviço de Aplicações.

> [!NOTE]
> Se pretender verificar o nome de domínio, mas não encaminhar o tráfego de produção para a aplicação web, basta especificar o registo TXT para o passo de verificação.  Verificação não necessita de um registo A ou CNAME, além do registo TXT.

```azurepowershell
New-AzDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup `
 -Name "@" -RecordType "txt" -Ttl 600 `
 -DnsRecords (New-AzDnsRecordConfig -Value  "contoso.azurewebsites.net")
```

## <a name="create-the-cname-record"></a>Criar o registo CNAME

Se o seu domínio já for gerido pelo DNS do Azure (veja [Delegação de domínio DNS](dns-domain-delegation.md)), pode utilizar o seguinte exemplo para criar um registo CNAME para contoso.azurewebsites.net.

Abra o Azure PowerShell e crie um novo registo CNAME. Este exemplo cria um tipo de conjunto de registos CNAME com um "TTL" de 600 segundos na zona DNS denominada "contoso.com", com o alias para a aplicação Web contoso.azurewebsites.net.

### <a name="create-the-record"></a>Criar o registo

```azurepowershell
New-AzDnsRecordSet -ZoneName contoso.com -ResourceGroupName "MyAzureResourceGroup" `
 -Name "www" -RecordType "CNAME" -Ttl 600 `
 -DnsRecords (New-AzDnsRecordConfig -cname "contoso.azurewebsites.net")
```

O exemplo seguinte é a resposta:

```
    Name              : www
    ZoneName          : contoso.com
    ResourceGroupName : myresourcegroup
    Ttl               : 600
    Etag              : 8baceeb9-4c2c-4608-a22c-229923ee185
    RecordType        : CNAME
    Records           : {contoso.azurewebsites.net}
    Tags              : {}
```

## <a name="test-the-new-records"></a>Testar os novos registos

Pode confirmar que os registos foram criados corretamente ao consultar o "www.contoso.com" e "contoso.com" com nslookup, conforme mostrado abaixo:

```
PS C:\> nslookup
Default Server:  Default
Address:  192.168.0.1

> www.contoso.com
Server:  default server
Address:  192.168.0.1

Non-authoritative answer:
Name:    <instance of web app service>.cloudapp.net
Address:  <ip of web app service>
Aliases:  www.contoso.com
contoso.azurewebsites.net
<instance of web app service>.vip.azurewebsites.windows.net

> contoso.com
Server:  default server
Address:  192.168.0.1

Non-authoritative answer:
Name:    contoso.com
Address:  <ip of web app service>

> set type=txt
> contoso.com

Server:  default server
Address:  192.168.0.1

Non-authoritative answer:
contoso.com text =

        "contoso.azurewebsites.net"
```
## <a name="add-custom-host-names"></a>Adicionar nomes de anfitrião personalizados

Agora, pode adicionar os nomes de anfitrião personalizados à sua aplicação Web:

```azurepowershell
set-AzWebApp `
 -Name contoso `
 -ResourceGroupName MyAzureResourceGroup `
 -HostNames @("contoso.com","www.contoso.com","contoso.azurewebsites.net")
```
## <a name="test-the-custom-host-names"></a>Testar os nomes de anfitrião personalizados

Abra um browser e navegue até `http://www.<your domainname>` e `http://<you domain name>`.

> [!NOTE]
> Certifique-se de que incluir o prefixo`http://`; caso contrário, o seu browser pode tentar prever um URL por si!

Deverá ver a mesma página para ambos os URLs. Por exemplo:

![Serviço de aplicações da Contoso](media/dns-web-sites-custom-domain/contoso-app-svc.png)


## <a name="clean-up-resources"></a>Limpar recursos

Quando já não precisar dos recursos criados neste tutorial, pode eliminar o grupo de recursos **myresourcegroup**.

## <a name="next-steps"></a>Passos Seguintes

Saiba como criar zonas privadas do DNS do Azure.

> [!div class="nextstepaction"]
> [Introdução às zonas privadas do DNS do Azure com o PowerShell](private-dns-getstarted-powershell.md)
