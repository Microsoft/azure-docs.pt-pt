---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: 2d2a82552a846cedfa5da3bb6ec6df8a40b67732
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78671498"
---
Embora a funcionalidade seja fácil de configurar, isso não significa que a sua experiência será livre de problemas. Caso encontre problemas de acesso ao ponto final pretendido, existem alguns utilitários que pode utilizar para testar a conectividade a partir da consola de aplicações. Há duas consolas que podes usar. Uma é a consola Kudu e a outra é a consola no portal Azure. Para chegar à consola Kudu a partir da sua aplicação, vá a Tools -> Kudu. Você também pode chegar à consola Kudo em [sitename].scm.azurewebsites.net. Assim que o site estiver cheio, vá ao separador de consola Debug. Para chegar ao portal Azure hospedado consola então da sua aplicação vá para Tools -> Console. 

#### <a name="tools"></a>Ferramentas
As ferramentas **ping**, **nslookup**, e **tracert** não funcionarão através da consola devido a restrições de segurança. Para preencher o vazio, duas ferramentas separadas adicionadas. Para testar a funcionalidade DNS, adicionámos uma ferramenta chamada nameresolver.exe. A sintaxe é:

    nameresolver.exe hostname [optional: DNS Server]

Pode utilizar **nameresolver** para verificar os nomes de anfitriões de que a sua aplicação depende. Desta forma, pode testar se tem algo mal configurado com o seu DNS ou talvez não tenha acesso ao seu servidor DNS. Pode ver o servidor DNS que a sua aplicação irá utilizar na consola olhando para as variáveis ambientais WEBSITE_DNS_SERVER e WEBSITE_DNS_ALT_SERVER.

A próxima ferramenta permite-lhe testar a conectividade TCP com uma combinação de hospedeiro e porta. Esta ferramenta chama-se **tcpping** e a sintaxe é:

    tcpping.exe hostname [optional: port]

O utilitário **tcpping** diz-lhe se pode chegar a um hospedeiro específico e porto. Só pode mostrar sucesso se: houver uma aplicação a ouvir a combinação de anfitriões e portas, e existe acesso de rede da sua app ao anfitrião especificado e à porta.

#### <a name="debugging-access-to-vnet-hosted-resources"></a>Depurando o acesso aos recursos hospedados pela VNet
Há uma série de coisas que podem impedir que a sua aplicação chegue a um anfitrião específico e à porta. A maior parte do tempo é uma das três coisas:

* **Uma firewall está no caminho.** Se tiver uma firewall no caminho, vai atingir o intervalo do TCP. O tempo de saída do TCP é de 21 segundos neste caso. Utilize a ferramenta **de tcpping** para testar a conectividade. Os intervalos de tCP podem ser devidos a muitas coisas além das firewalls, mas começam por aí. 
* **DNS não é acessível.** O tempo de paragem do DNS é de três segundos por servidor DNS. Se tiver dois servidores DNS, o tempo é de 6 segundos. Use nameresolver para ver se o DNS está funcionando. Lembre-se que não pode usar o nslookup, uma vez que não utiliza o DNS com o qual o VNet está configurado. Se não for acessível, pode ter uma firewall ou NSG bloqueando o acesso ao DNS ou pode estar em baixo.

Se esses itens não responderem aos seus problemas, procure primeiro coisas como: 

**integração regional vnet**
* o seu destino é um endereço não RFC1918 e não tem WEBSITE_VNET_ROUTE_ALL definido para 1?
* Existe um nSG bloqueando a saída da sua subnet de integração?
* Se atravessar a ExpressRoute ou uma VPN, o seu portal no local está configurado para encaminhar o tráfego de volta para Azure? Se conseguir chegar a pontos finais no seu VNet mas não no local, verifique as suas rotas.
* Tem permissão suficiente para definir delegação na subnet de integração? Durante a configuração regional de Integração VNet, a sua subnet de integração será delegada na Microsoft.Web. O VNet Integration UI delegará a subnet para microsoft.Web automaticamente. Se a sua conta não tiver permissões de networking suficientes para definir a delegação, precisará de alguém que possa definir atributos na sua subnet de integração para delegar a sub-rede. Para delegar manualmente a subnet de integração, vá à subnet UI da rede virtual Azure e detetete a delegação para a Microsoft.Web. 

**gateway necessária Integração VNet**
* é a gama de endereços ponto-a-local nas gamas RFC 1918 (10.0.0.0.0.0.10.255.255.255 / 172.16.0.0-172.31.255.255 / 192.168.0.0-192.168.255.255)?
* O Gateway mostra como estando no portal? Se o seu portal está para baixo, então traga-o de volta para cima.
* Os certificados mostram estar sincronizado ou suspeita que a configuração da rede foi alterada?  Se os seus certificados estiverem dessincronizados ou suspeitar que houve uma alteração na configuração vNet que não foi sincronizada com os seus ASPs, então acerte em "Sync Network".
* se atravessar uma VPN, a porta de entrada no local está configurada para encaminhar o tráfego de volta para Azure? Se conseguir chegar a pontos finais no seu VNet mas não no local, verifique as suas rotas.
* Está a tentar usar um portal de coexistência que suporta tanto o ponto para o site como o ExpressRoute? Os gateways de coexistência não são suportados com a Integração VNet.

Depurar problemas de networking é um desafio porque não se consegue ver o que está a bloquear o acesso a uma combinação específica de hospedeiro:porta. Algumas das causas incluem:

* Tem uma firewall no seu anfitrião impedindo o acesso à porta de aplicação do seu ponto para o intervalo IP do local. Atravessar subredes requer frequentemente acesso público.
* O seu anfitrião alvo está abatido.
* A sua candidatura está em baixo.
* Tinhao IP errado ou nome de anfitrião.
* A sua aplicação está a ouvir num porto diferente do que esperava. Pode comparar o seu ID de processo com a porta de audição utilizando "netstat -aon" no hospedeiro final. 
* Os seus grupos de segurança de rede estão configurados de forma a impedir o acesso ao seu anfitrião de aplicação e porta do seu ponto para o intervalo IP do site.

Lembre-se que não sabe qual o endereço que a sua aplicação irá realmente usar. Pode ser qualquer endereço na subnet de integração ou no intervalo de endereços ponto-a-local, por isso precisa de permitir o acesso a partir de toda a gama de endereços. 

Os passos adicionais de depuração incluem:

* Ligue-se a um VM no seu VNet e tente chegar ao seu anfitrião de recursos:porta a partir daí. Para testar o acesso à TCP, utilize a **ligação**de teste de comando PowerShell . A sintaxe é:

      test-netconnection hostname [optional: -Port]

* apresentar uma aplicação num VM e testar o acesso a esse anfitrião e porta a partir da consola a partir da sua aplicação utilizando **tcpping**

#### <a name="on-premises-resources"></a>Recursos no local ####

Se a sua aplicação não conseguir contactar um recurso no local, verifique se consegue contactar o recurso a partir do seu VNet. Utilize o comando PowerShell de **ligação de rede de teste** para verificar se há acesso tCP. Se o seu VM não conseguir contactar o seu recurso no local, a sua ligação VPN ou ExpressRoute pode não estar configurada corretamente.

Se o vNet hospedado VM pode chegar ao seu sistema no local, mas a sua aplicação não pode, então a causa é provavelmente uma das seguintes razões:

* As suas rotas não estão configuradas com a sua subnet ou apontam para as gamas de endereços do local no seu gateway no local.
* Os seus grupos de segurança de rede estão a bloquear o acesso à sua gama IP ponto-a-local.
* As suas firewalls no local estão a bloquear o tráfego da sua gama IP ponto-a-local.
* Está a tentar alcançar um endereço não RFC 1918 utilizando a funcionalidade regional de Integração VNet.