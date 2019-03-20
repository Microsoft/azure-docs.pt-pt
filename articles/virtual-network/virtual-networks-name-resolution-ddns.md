---
title: Utilizar o DNS dinâmico para registar nomes de anfitrião no Azure | Documentos da Microsoft
description: Saiba como configurar o DNS dinâmico para registar nomes de anfitrião em seus próprios servidores DNS.
services: dns
documentationcenter: na
author: subsarma
manager: vitinnan
editor: ''
ms.assetid: c315961a-fa33-45cf-82b9-4551e70d32dd
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/23/2017
ms.author: subsarma
ms.openlocfilehash: c2ef842fd62ef060f06536d66387c3facd0627b5
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57994693"
---
# <a name="use-dynamic-dns-to-register-hostnames-in-your-own-dns-server"></a>Utilizar o DNS dinâmico para registar nomes de anfitriões no seu próprio servidor DNS

[O Azure fornece resolução de nomes](virtual-networks-name-resolution-for-vms-and-role-instances.md) para máquinas virtuais (VM) e instâncias de função. Quando a resolução do nome tem de exceder as capacidades fornecidas por predefinição do Azure DNS, pode fornecer seus próprios servidores DNS. Usar seus próprios servidores DNS dá-lhe a capacidade de adaptar a sua solução DNS para se adequar às suas necessidades específicas. Por exemplo, terá de aceder a recursos no local através do seu controlador de domínio do Active Directory.

Quando os servidores DNS personalizados estão alojados como VMs do Azure, pode reencaminhar consultas de nome de anfitrião para a mesma rede virtual para o Azure para resolver os nomes de anfitrião. Se não pretender utilizar esta opção, pode registrar seus nomes de anfitrião VM no seu servidor DNS com o DNS dinâmicas (DDNS). Azure não tem as credenciais para criar diretamente os registos em seus servidores DNS, para que a alternativas disposições geralmente são necessários. Siga alguns cenários comuns, com alternativas:

## <a name="windows-clients"></a>Clientes do Windows
Clientes do Windows não-associados a um domínio tentarem sem segurança DDNS atualizações quando arrancarem, ou quando o respetivo endereço IP é alterado. O nome DNS é o nome de anfitrião e o sufixo DNS primário. Azure deixa o sufixo DNS primário em branco, mas pode definir o sufixo na VM, através da [interface do usuário](https://technet.microsoft.com/library/cc794784.aspx) ou [PowerShell](/powershell/module/dnsclient/set-dnsclient).

Clientes de Windows associados a um domínio registar os seus endereços IP com o controlador de domínio utilizando o DDNS seguro. O processo de associação a um domínio define o sufixo DNS primário no cliente e cria e mantém a relação de confiança.

## <a name="linux-clients"></a>Clientes Linux
Clientes Linux, geralmente, não registram-se com o servidor DNS na inicialização, eles supõem que o servidor DHCP faz isso. Servidores DHCP do Azure não tem as credenciais para registrar os registros no seu servidor DNS. Pode usar uma ferramenta chamada `nsupdate`, que está incluído no pacote Bind, para enviar DDNS atualizações. Como o protocolo DDNS é padronizado, pode usar `nsupdate` até mesmo quando não estiver a utilizar Bind no servidor DNS.

Pode usar os ganchos que são fornecidos pelo cliente de DHCP para criar e manter a entrada de nome de anfitrião no servidor DNS. Durante o ciclo DHCP, o cliente executa os scripts no */etc/dhcp/dhclient-exit-hooks.d/*. Pode usar os ganchos para registar o novo endereço IP com `nsupdate`. Por exemplo:

```bash
#!/bin/sh
requireddomain=mydomain.local

# only execute on the primary nic
if [ "$interface" != "eth0" ]
then
    return
fi

# When you have a new IP, perform nsupdate
if [ "$reason" = BOUND ] || [ "$reason" = RENEW ] ||
   [ "$reason" = REBIND ] || [ "$reason" = REBOOT ]
then
   host=`hostname`
   nsupdatecmds=/var/tmp/nsupdatecmds
     echo "update delete $host.$requireddomain a" > $nsupdatecmds
     echo "update add $host.$requireddomain 3600 a $new_ip_address" >> $nsupdatecmds
     echo "send" >> $nsupdatecmds

     nsupdate $nsupdatecmds
fi
```

Também pode utilizar o `nsupdate` de atualizações de comando para realizar DDNS seguro. Por exemplo, quando estiver a utilizar um servidor de Bind DNS, um par de chaves públicas-privadas é [gerado](http://linux.yyz.us/nsupdate/). O servidor DNS [configurado](http://linux.yyz.us/dns/ddns-server.html) com a parte pública da chave, por isso que a TI pode verificar a assinatura no pedido. Para fornecer o par de chaves para `nsupdate`, utilize o `-k` opção, para as DDNS pedido sejam assinados de atualização.

Quando estiver a utilizar um servidor DNS do Windows, pode utilizar a autenticação Kerberos com o `-g` parâmetro na `nsupdate`, mas não está disponível na versão do Windows `nsupdate`. Para utilizar o Kerberos, utilize `kinit` para carregar as credenciais. Por exemplo, é possível carregar as credenciais de uma [o keytab ficheiro](https://www.itadmintools.com/2011/07/creating-kerberos-keytab-files.html)), em seguida, `nsupdate -g` seleciona as credenciais, da cache.

Se for necessário, pode adicionar um sufixo de procura DNS às suas VMs. O sufixo DNS especificado no */etc/resolv.conf* ficheiro. A maioria das distribuições de Linux gerir automaticamente o conteúdo deste ficheiro, então, normalmente não é possível editá-lo. No entanto, é possível substituir o sufixo ao utilizar o cliente DHCP `supersede` comando. Para substituir o sufixo, adicione a seguinte linha para o */etc/dhcp/dhclient.conf* ficheiro:

```
supersede domain-name <required-dns-suffix>;
```
