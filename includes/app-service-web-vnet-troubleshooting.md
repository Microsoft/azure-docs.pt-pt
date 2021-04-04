---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: cec44bbabdb7d528c30a8d3396b819f2eb3c5386
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "95999459"
---
A funcionalidade é fácil de configurar, mas isso não significa que a sua experiência seja livre de problemas. Se encontrar problemas no acesso ao seu ponto final pretendido, existem alguns utilitários que pode utilizar para testar a conectividade a partir da consola da aplicação. Há duas consolas que podes usar. Uma é a consola Kudu, e a outra é a consola no portal Azure. Para chegar à consola Kudu a partir da sua aplicação, vá ao **Tools**  >  **Kudu**. Também pode alcançar a consola Kudo em [sitename].scm.azurewebsites.net. Depois de carregar o site, vá ao separador **de consola Debug.** Para chegar à consola azure a partir da sua aplicação, vá ao **Consola de Ferramentas.**  >  

#### <a name="tools"></a>Ferramentas
Nas aplicações nativas do Windows, as ferramentas **ping**, **nslookup**, e **tracert** não funcionam através da consola devido a restrições de segurança (funcionam em [recipientes windows personalizados).](../articles/app-service/quickstart-custom-container.md) Para preencher o vazio, são adicionadas duas ferramentas separadas. Para testar a funcionalidade DNS, adicionámos uma ferramenta chamada **nameresolver.exe**. A sintaxe é:

```console
nameresolver.exe hostname [optional: DNS Server]
```

Pode utilizar o nomeresolver para verificar os nomes de anfitrião de que a sua aplicação depende. Desta forma pode testar se tem alguma coisa mal configurada com o seu DNS ou talvez não tenha acesso ao seu servidor DNS. Pode ver o servidor DNS que a sua aplicação utiliza na consola, olhando para as variáveis ambientais WEBSITE_DNS_SERVER e WEBSITE_DNS_ALT_SERVER.

> [!NOTE]
> nameresolver.exe atualmente não funciona em recipientes windows personalizados.
>

Pode utilizar a próxima ferramenta para testar a conectividade TCP a uma combinação de hospedeiro e porta. Esta ferramenta **chama-se tcpping** e a sintaxe é:

```console
tcpping.exe hostname [optional: port]
```

O utilitário **de tcpping** diz-lhe se pode chegar a um hospedeiro e porta específicos. Só pode mostrar sucesso se houver uma aplicação a ouvir na combinação de anfitriões e portas, e há acesso à rede da sua app para o anfitrião especificado e porta.

#### <a name="debug-access-to-virtual-network-hosted-resources"></a>Depurg acesso a recursos hospedados em rede virtual
Uma série de coisas pode impedir que a sua app chegue a um anfitrião e porto específicos. A maior parte do tempo é uma destas coisas:

* **Uma firewall está no caminho.** Se tiver uma firewall no caminho, atinja o tempo limite da TCP. O tempo limite de TCP é de 21 segundos neste caso. Utilize a ferramenta **de tcpping** para testar a conectividade. Os intervalos de tempo TCP podem ser causados por muitas coisas além das firewalls, mas começam por aí.
* **O DNS não está acessível.** O tempo limite de DNS é de 3 segundos por servidor DNS. Se tiver dois servidores DNS, o tempo limite é de 6 segundos. Use o nome de consolação para ver se o DNS está a funcionar. Não podes usar o nslookup, porque isso não usa o DNS com o qual a tua rede virtual está configurada. Se for inacessível, pode ter uma firewall ou NSG bloqueando o acesso ao DNS ou pode estar em baixo.

Se esses itens não responderem aos seus problemas, procure primeiro coisas como:

**Integração Regional de VNet**
* O seu destino é um endereço não-RFC1918 e não tem WEBSITE_VNET_ROUTE_ALL definido para 1?
* Existe uma saída de bloqueio da NSG da sua sub-rede de integração?
* Se vai atravessar a Azure ExpressRoute ou uma VPN, o seu portal no local está configurado para encaminhar o tráfego de volta para Azure? Se conseguir chegar aos pontos finais da sua rede virtual, mas não no local, verifique as suas rotas.
* Tem permissões suficientes para definir a delegação na sub-rede de integração? Durante a configuração regional de Integração VNet, a sua sub-rede de integração é delegada no Microsoft.Web/serverFarms. O UI de Integração VNet delega automaticamente a sub-rede para Microsoft.Web/serverFarms. Se a sua conta não tiver permissões de networking suficientes para definir a delegação, precisará de alguém que possa definir atributos na sua sub-rede de integração para delegar a sub-rede. Para delegar manualmente a sub-rede de integração, vá à sub-rede Azure Virtual Network UI e deslote a delegação para Microsoft.Web/serverFarms.

**Integração VNet exigida pelo Gateway**
* É o intervalo de endereços ponto-a-local nas gamas RFC 1918 (10.0.0-10.255.255.255 /172.16.00.0-172.31.255.255 / 192.168.0.0-192.168.255.255)?
* O portal mostra-se como estando no portal? Se o seu portal está em baixo, então traga-o de volta.
* Os certificados mostram estar sincronizados ou suspeita que a configuração da rede foi alterada?  Se os seus certificados estiverem dessincronizados ou suspeitar que foi feita uma alteração na sua configuração de rede virtual que não foi sincronizada com os seus ASPs, selecione **Sync Network**.
* Se vai atravessar uma VPN, o portal no local está configurado para encaminhar o tráfego de volta para Azure? Se conseguir chegar aos pontos finais da sua rede virtual, mas não no local, verifique as suas rotas.
* Está a tentar usar uma porta de entrada de coexistência que suporta tanto o ponto para o site como o ExpressRoute? As portas de coexistência não são suportadas com a Integração VNet.

Depurar problemas de networking é um desafio porque você não pode ver o que está bloqueando o acesso a uma combinação específica de anfitrião:port. Algumas causas incluem:

* Tem uma firewall no seu anfitrião que impede o acesso à porta de aplicação a partir da sua gama IP ponto-a-local. Atravessar sub-redes requer frequentemente acesso público.
* O teu anfitrião-alvo está em baixo.
* A sua candidatura está em baixo.
* Enganou-se no IP ou no nome de anfitrião.
* A sua aplicação está a ouvir numa porta diferente do que esperava. Pode combinar o seu ID de processo com a porta de audição utilizando "netstat -aon" no anfitrião do ponto final.
* Os seus grupos de segurança de rede estão configurados de forma a impedir o acesso ao seu anfitrião de aplicações e à porta a partir da sua gama IP ponto-a-local.

Não sabe que endereço a sua aplicação realmente usa. Pode ser qualquer endereço na sub-rede de integração ou no intervalo de endereços ponto a local, pelo que precisa de permitir o acesso a partir de toda a gama de endereços.

Os passos adicionais de depurg incluem:

* Ligue-se a um VM na sua rede virtual e tente contactar o seu anfitrião de recursos:porta a partir daí. Para testar o acesso à TCP, utilize a **rede de teste de** comando PowerShell . A sintaxe é:

```powershell
test-netconnection hostname [optional: -Port]
```

* Apresentar uma aplicação num VM e testar o acesso a esse anfitrião e porta a partir da consola a partir da sua aplicação, utilizando **tcpping**.

#### <a name="on-premises-resources"></a>Recursos no local ####

Se a sua aplicação não conseguir chegar a um recurso no local, verifique se consegue contactar o recurso a partir da sua rede virtual. Utilize o comando PowerShell **de rede de teste** para verificar se há acesso à TCP. Se o seu VM não conseguir chegar ao seu recurso no local, a sua ligação VPN ou ExpressRoute pode não estar configurada corretamente.

Se o seu VM virtual hospedado em rede pode chegar ao seu sistema no local, mas a sua aplicação não pode, a causa é provavelmente uma das seguintes razões:

* As suas rotas não estão configuradas com os intervalos de endereços da sua sub-rede ou ponto a local no seu gateway no local.
* Os seus grupos de segurança de rede estão a bloquear o acesso para o seu intervalo ip ponto-a-local.
* As suas firewalls no local estão a bloquear o tráfego da sua gama IP ponto-a-local.
* Está a tentar chegar a um endereço não RFC 1918 utilizando a funcionalidade regional de Integração VNet.
